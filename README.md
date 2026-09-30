# Immediate Generator (ImmGen) - RISC-V RV32I

[![Language](https://img.shields.io/badge/Language-SystemVerilog-blue.svg)](https://en.wikipedia.org/wiki/SystemVerilog)
[![Standard](https://img.shields.io/badge/Standard-IEEE%201800--2012%2F2017-brightgreen.svg)]()
[![Target ISA](https://img.shields.io/badge/ISA-RISC--V%20RV32I-red.svg)](https://riscv.org/)
[![Tool](https://img.shields.io/badge/Verified%20with-Vivado%202022.2-orange.svg)]()
[![License](https://img.shields.io/badge/License-MIT-green.svg)]()

Khối **Immediate Generator (`imm_gen`)** phụ trách việc trích xuất và mở rộng dấu (sign-extension) các hằng số tức thời (immediate) từ lệnh 32-bit thành giá trị 32-bit hoàn chỉnh cho cả 5 định dạng lệnh chuẩn của kiến trúc RISC-V: **I-type, S-type, B-type, U-type, và J-type**.

---

## 📌 Đặc tả 5 Định dạng Tức thời (Immediate Formats)

```
                    +----------------------+
  instr [31:7] ---->|       imm_gen        |-----> imm [31:0]
  ImmSrc [2:0] ---->|    (Combinational)   |
                    +----------------------+
```

### 📋 Bảng Mã hóa `ImmSrc` và Quy tắc Ghép Bit

| `ImmSrc[2:0]` | Định dạng | Nhóm lệnh tiêu biểu | Quy tắc mở rộng & ghép bit SystemVerilog | Độ rộng gốc |
| :---: | :---: | :--- | :--- | :---: |
| `3'b000` | **I-type** | `ADDI`, `LW`, `JALR` | `{{20{instr[31]}}, instr[31:20]}` | 12 bit |
| `3'b001` | **S-type** | `SW`, `SH`, `SB` | `{{20{instr[31]}}, instr[31:25], instr[11:7]}` | 12 bit |
| `3'b010` | **B-type** | `BEQ`, `BNE`, `BLT` | `{{19{instr[31]}}, instr[31], instr[7], instr[30:25], instr[11:8], 1'b0}` | 13 bit |
| `3'b011` | **U-type** | `LUI`, `AUIPC` | `{instr[31:12], 12'b0}` | 20 bit |
| `3'b100` | **J-type** | `JAL` | `{{11{instr[31]}}, instr[31], instr[19:12], instr[20], instr[30:21], 1'b0}` | 21 bit |

> **Lưu ý kỹ thuật:**
> - Trong định dạng **B-type** và **J-type**, bit thấp nhất (`bit 0`) luôn bằng `0` vì địa chỉ nhảy/rẽ nhánh trong RISC-V luôn được căn lề bội số của 2 byte (halfword-aligned).
> - Bit `instr[31]` luôn là bit dấu trong tất cả các định dạng, giúp tối ưu diện tích mạch giải mã trên FPGA/ASIC.

---

## 🔌 Đặc tả Cổng Giao tiếp

| Tên cổng | Hướng (Direction) | Độ rộng bit | Ý nghĩa |
| :--- | :---: | :---: | :--- |
| `instr` | Input | `[31:7]` | Các trường bit mang immediate của lệnh 32-bit |
| `ImmSrc` | Input | `[2:0]` | Tín hiệu chọn kiểu giải mã tức thời từ Main Decoder |
| `imm` | Output | `[31:0]` | Giá trị tức thời 32-bit sau khi mở rộng dấu |

---

## 🧪 Kiểm chứng & Mô phỏng (Verification)

Testbench `testbench/tb_imm_gen.sv` kiểm tra tự động tất cả các trường hợp giá trị dương lớn nhất, giá trị âm (mở rộng dấu bit 1), các bit hoán vị ngẫu nhiên theo đúng quy chuẩn RISC-V.

### Lệnh chạy mô phỏng:

```bash
xvlog -sv rtl/imm_gen.sv testbench/tb_imm_gen.sv
xelab Imm_gen_tb -s imm_sim
xsim imm_sim -R
```

---

## 📂 Cấu trúc Thư mục Repo

```
.
├── rtl/
│   └── imm_gen.sv         # RTL Immediate Generator
├── testbench/
│   └── tb_imm_gen.sv      # Self-checking testbench
├── .gitignore
└── README.md
```

---

## 👨‍💻 Thông tin Tác giả & Đồ án

- **Sinh viên thực hiện:** Nguyễn Thành Trung
- **Học phần:** Đồ án Môn học 2 (Capstone Project II) – Ngành Kỹ thuật Máy tính
- **Tên đề tài:** Thiết kế, kiểm chứng và triển khai FPGA lõi vi xử lý RISC-V RV32I 32-bit pipeline 5 tầng ở mức RTL bằng SystemVerilog
- **GitHub cá nhân:** [@thanhchun2005-blip](https://github.com/thanhchun2005-blip)
- **Email:** thanhchun2005@gmail.com
