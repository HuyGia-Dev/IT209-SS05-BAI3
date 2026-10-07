# Báo Cáo Bài 3: Xử Lý Xung Đột Phức Tạp Trong Quá Trình Rebase

## 1. Chuẩn bị môi trường và các commit ban đầu

### Thao tác trên nhánh `main`:
1. Tạo tệp `config.json`:
   ```json
   {
     "port": 8080,
     "debug": false
   }
   ```
2. Commit ban đầu:
   ```bash
   git add config.json
   git commit -m "init config"
   ```

### Thao tác trên nhánh `feature-api`:
1. Tạo và chuyển sang nhánh `feature-api`:
   ```bash
   git checkout -b feature-api
   ```
2. Chỉnh sửa `config.json` lần 1 (`"port": 9000`) và commit:
   ```bash
   git add config.json
   git commit -m "feat: change port"
   ```
3. Chỉnh sửa `config.json` lần 2 (`"debug": true`) và commit:
   ```bash
   git add config.json
   git commit -m "feat: enable debug"
   ```

### Nhánh `main` có thêm cập nhật từ thành viên khác:
1. Quay lại nhánh `main`:
   ```bash
   git checkout main
   ```
2. Chỉnh sửa `"port": 8081` và commit:
   ```bash
   git add config.json
   git commit -m "update port on main"
   ```
3. Thêm cấu hình `"env": "production"` và commit:
   ```bash
   git add config.json
   git commit -m "add env config"
   ```

---

## 2. Tiến hành Rebase và Giải quyết xung đột (Conflict Resolution)

Chuyển về nhánh `feature-api` và bắt đầu rebase lên `main`:
```bash
git checkout feature-api
git rebase main
```

---

### Chặng 1: Xử lý conflict khi áp dụng commit `feat: change port`

**Thông báo lỗi từ Git:**
```text
Auto-merging config.json
CONFLICT (content): Merge conflict in config.json
error: Could not apply 3a1b2c3... feat: change port
```

**Nội dung tệp `config.json` bị xung đột:**
```json
{
<<<<<<< HEAD
  "port": 8081,
  "debug": false,
  "env": "production"
=======
  "port": 9000,
  "debug": false
>>>>>>> feat: change port
}
```

**Cách giải quyết:**
Giữ lại thay đổi port mới (`9000`) của nhánh `feature-api` đồng thời giữ cấu hình `"env": "production"` mới được thêm từ nhánh `main`.

Chỉnh sửa tệp `config.json` thành:
```json
{
  "port": 9000,
  "debug": false,
  "env": "production"
}
```

Lưu tệp, đánh dấu đã giải quyết và tiếp tục rebase:
```bash
git add config.json
git rebase --continue
```

---

### Chặng 2: Áp dụng commit `feat: enable debug`

Git tiếp tục áp dụng commit thứ 2 (`feat: enable debug`). Nếu phát sinh xung đột giữa `"debug": false` từ `main` và `"debug": true` từ `feature-api`:

**Nội dung tệp `config.json` bị xung đột:**
```json
{
  "port": 9000,
<<<<<<< HEAD
  "debug": false,
  "env": "production"
=======
  "debug": true
>>>>>>> feat: enable debug
}
```

**Cách giải quyết:**
Cập nhật thuộc tính `"debug": true` theo đúng tính năng của nhánh `feature-api` và giữ lại `"env": "production"`.

Chỉnh sửa tệp `config.json` thành:
```json
{
  "port": 9000,
  "debug": true,
  "env": "production"
}
```

Lưu tệp, đánh dấu đã giải quyết và hoàn tất rebase:
```bash
git add config.json
git rebase --continue
```

**Kết quả thông báo:**
```text
Successfully rebased and updated refs/heads/feature-api.
```

---

## 3. Kiểm tra trạng thái và lịch sử Git

### Kiểm tra trạng thái Rebase:
```bash
git status
```
*Kết quả:*
```text
On branch feature-api
nothing to commit, working tree clean
```

### Kiểm tra lịch sử Git (Lịch sử thẳng tắp, không có merge commit phụ):
```bash
git log --graph --oneline
```
*Kết quả:*
```text
* e4f5g6h (HEAD -> feature-api) feat: enable debug
* d3e4f5g feat: change port
* c2d3e4f (main) add env config
* b1c2d3e update port on main
* a0b1c2d init config
```

---

## 4. Kết luận
- Quá trình Rebase đã đưa các commit của nhánh `feature-api` lên trên cùng của nhánh `main` theo một tuyến thẳng.
- Đã giải quyết triệt để xung đột qua từng bước (`step-by-step conflict resolution`) mà không làm hỏng dữ liệu hay tạo ra bất kỳ Merge Commit phụ nào.