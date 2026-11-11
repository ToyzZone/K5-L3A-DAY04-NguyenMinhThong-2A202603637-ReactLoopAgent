# Ghi chú phân tích · Day 04

Họ và tên: Nguyễn Minh Thông
MSSV: 2A202603637

## 1. Case được chọn

- ID case:
- Câu trả lời của agent:
- Điểm ở lần chạy 1:

## 2. Bằng chứng trong trace

Dán đoạn trace chứng minh lỗi và nêu nguyên nhân có thể xảy ra.

```text

```

## 3. Thay đổi đã thực hiện

- Sửa gì, ở đâu:
- Vì sao chọn thay đổi này:

## 4. So sánh hai lần chạy

| Chỉ số | `eval_before.json` | `eval_after.json` |
|---|---|---|
| Điểm tổng | 92.0 (22/22 case được chấm) |  |
| Điểm case đã chọn |  |  |
| Số model calls | 61 (agent, tổng 22 case) |  |
| Token, chi phí nếu có | Agent 69.739 (prompt 55.812, completion 13.927); judge 55.029 |  |

- Lỗi đã giải quyết:
- Lỗi còn lại hoặc mới xuất hiện:

## 5. Giới hạn

Nêu giới hạn của điểm judge, của snapshot và của việc chỉ chạy một lần mỗi phiên bản.

- **Endpoint giới hạn việc chọn model.** `providers.py` gọi `/v1/chat/completions`. Lần chạy eval đầu với `MODEL=gpt-6-luna` hỏng cả 22 case ngay ở step 1: trace rỗng, `HTTP 400`. OpenAI báo lỗi: `Function tools with reasoning_effort are not supported for gpt-6-luna in /v1/chat/completions`. `gpt-6-sol` và `gpt-5.6-luna` gặp đúng lỗi này. Cùng payload đó, khi thêm `reasoning_effort: 'none'` thì `gpt-6-luna` gọi được `get_status`, nhưng `reasoning_tokens = 0`. Như vậy với adapter hiện tại, agent không dùng được model reasoning kèm tools. Cả hai lần eval phải dùng model không reasoning (xem khối `config`), và điểm số không cho biết gì về model reasoning trên cùng bộ case.
  - Giải thích có thể (suy luận, chưa kiểm chứng): Chat Completions không giữ trạng thái và không trả lại phần suy luận, nên model reasoning sẽ mất mạch suy nghĩ giữa các lần gọi tool. Responses API (`/v1/responses`) thì mang phần suy luận đã mã hóa sang bước sau. Giả thuyết "chống distillation" không giải thích được lỗi này, vì cả hai endpoint đều không trả nguyên văn phần suy luận.
