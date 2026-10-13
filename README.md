import streamlit as st
import pandas as pd
import matplotlib.pyplot as plt

# ==================== Dữ liệu ====================
du_lieu = {
    "Ho_ten": ["Nguyen Van An", "Tran Thi Binh", "Le Van Chi", "Pham Thi Dung", 
               "Hoang Van Ha", "Vu Thi Lan", "Do Van Minh", "Bui Thi Nam", 
               "Ngo Van Phuc", "Dang Thi Trang"],
    "Chuyen_can": [8.5, 7.0, 9.0, 6.5, 8.0, 7.5, 5.0, 9.5, 6.0, 8.5],
    "Giua_ky":    [7.5, 8.0, 6.5, 9.0, 7.0, 8.5, 4.5, 9.0, 5.5, 7.5],
    "Cuoi_ky":    [8.0, 7.5, 8.5, 8.0, 9.0, 6.5, 5.0, 9.5, 6.0, 8.0]
}

df = pd.DataFrame(du_lieu)

# Tính điểm tổng kết
df["Tong_ket"] = (0.2 * df["Chuyen_can"] + 0.3 * df["Giua_ky"] + 0.5 * df["Cuoi_ky"]).round(2)

# Xếp loại
def xep_loai(diem):
    if diem >= 8.5:
        return "Giỏi"
    elif diem >= 7.0:
        return "Khá"
    elif diem >= 5.0:
        return "Trung bình"
    else:
        return "Yếu"

df["Xep_loai"] = df["Tong_ket"].apply(xep_loai)

# ==================== Giao diện Streamlit ====================
st.title("QUẢN LÝ ĐIỂM SINH VIÊN")

# Hiển thị bảng điểm
st.subheader("Bảng điểm sinh viên")
st.dataframe(df, use_container_width=True)

# Thống kê
diem_tb = df["Tong_ket"].mean()
sv_cao_nhat = df.loc[df["Tong_ket"].idxmax()]
sv_thap_nhat = df.loc[df["Tong_ket"].idxmin()]
so_sv_dat = (df["Tong_ket"] >= 5).sum()

st.subheader("Thống kê lớp")
col1, col2, col3 = st.columns(3)
col1.metric("Điểm TB lớp", f"{round(diem_tb, 2)}")
col2.metric("Sinh viên cao nhất", f"{sv_cao_nhat['Ho_ten']}", f"{sv_cao_nhat['Tong_ket']}")
col3.metric("Sinh viên thấp nhất", f"{sv_thap_nhat['Ho_ten']}", f"{sv_thap_nhat['Tong_ket']}")

st.write(f"**Số sinh viên đạt (≥ 5):** {so_sv_dat}")

# Selectbox chọn sinh viên
st.subheader("Tra cứu điểm sinh viên")
ten_sv = st.selectbox("Chọn sinh viên:", df["Ho_ten"].tolist())

sv = df[df["Ho_ten"] == ten_sv].iloc[0]
st.write(f"**Họ tên:** {sv['Ho_ten']}")
st.write(f"**Chuyên cần:** {sv['Chuyen_can']}")
st.write(f"**Giữa kỳ:** {sv['Giua_ky']}")
st.write(f"**Cuối kỳ:** {sv['Cuoi_ky']}")
st.write(f"**Tổng kết:** {sv['Tong_ket']}")
st.write(f"**Xếp loại:** {sv['Xep_loai']}")

# Biểu đồ
st.subheader("Biểu đồ điểm tổng kết")
fig, ax = plt.subplots(figsize=(10, 5))
ax.bar(df["Ho_ten"], df["Tong_ket"], color="skyblue")
ax.set_xlabel("Sinh viên")
ax.set_ylabel("Điểm tổng kết")
ax.set_title("Điểm tổng kết của 10 sinh viên")
ax.set_ylim(0, 10)
plt.xticks(rotation=45, ha="right")
plt.tight_layout()
st.pyplot(fig)

# Thông tin người tạo
st.markdown("---")
st.markdown("<p style='font-size:12px; color:gray;'>Họ tên: Nguyễn Xuân Hoàng Hiệp | MSSV: [037208003830]</p>", unsafe_allow_html=True)

