# SPX Linehaul Truck Planner

Python script tối ưu truck plan linehaul theo các ràng buộc vận hành:

- Có cả First-mile (Hub -> SOC) và Last-mile (SOC -> Hub).
- Không vượt capacity xe.
- Mỗi route tối đa 3 Hub.
- Không đi xuyên tỉnh trong cùng route.
- Có phạt chạy rỗng 50% chi phí một chiều.

## File chính

- `linehaul_truck_planner.py`

## Input Excel

Script đọc 3 sheet:

1. `Distance_Matrix_Km`: ma trận vuông Node x Node (km)
2. `Time_Matrix_Mins`: ma trận vuông Node x Node (phút)
3. `Node_Metadata`: metadata node, bắt buộc các cột:
   - `Node`
   - `NodeType` (`SOC` hoặc `HUB`)
   - `Province`
   - `FirstMile_Orders`
   - `LastMile_Orders`

## Chạy chương trình

```bash
python linehaul_truck_planner.py \
  --excel "D:/Đại học/Kì 2025.2/Quản lý vận tải/SPX Case Study_Routing_Matrix_81_Nodes.xlsx" \
  --output ./output \
  --allow-two-soc false \
  --max-hubs-per-trip 3
```

## Output

- `output/truck_plan_detail.csv`: chi tiết route-hub assignment.
- `output/truck_plan_summary.csv`: tổng hợp cost từng route.
- `output/qa_report.json`: QA + kiểm tra rule.

## Ghi chú mô hình

- Dùng MILP (PuLP/CBC) để gán Hub về SOC + loại xe theo chi phí thấp nhất.
- Ràng buộc một SOC mặc định (`--allow-two-soc false`), có thể nới ra 2 SOC.
- Chi phí route hiện tại tính gần đúng theo tổng vòng `SOC<->Hub` của các hub trong route pack.
- Khi một route chỉ có 1 chiều hàng (FM=0 hoặc LM=0) thì cộng thêm 50% chi phí chiều tương ứng.
