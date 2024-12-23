# Internal Messaging App

Ứng dụng nhắn tin nội bộ với các tính năng:

- Đăng nhập/Đăng ký tài khoản
- Chat 1-1 realtime qua WebSocket
- Quản lý người dùng (Admin)
- Hiển thị trạng thái online/offline
- Đánh dấu tin nhắn đã đọc
- Upload file/hình ảnh

## Cấu trúc thư mục

```
src/
  ├── server/
  │   ├── controllers/     # Xử lý logic nghiệp vụ
  │   ├── middleware/      # Middleware (auth, error handling)
  │   ├── routes/         # Định tuyến API
  │   ├── websocket/      # Xử lý WebSocket
  │   └── index.ts        # Entry point
  ├── client/
  │   ├── components/     # React components
  │   ├── pages/         # Page components
  │   └── stores/        # State management (Zustand)
  ├── config/            # Cấu hình (database, env)
  └── types/             # TypeScript type definitions
```

## Cài đặt

1. Clone repository
2. Cài đặt dependencies:

```bash
npm install
```

3. Tạo file .env với nội dung:

```properties
# Server
PORT=3000
HOST=localhost

# Database
DB_PATH=./database.sqlite

# JWT
JWT_SECRET=your_jwt_secret_key_here
JWT_EXPIRES_IN=24h

# Admin
ADMIN_SETUP_KEY=your_admin_setup_key_here

# WebSocket
WS_HEARTBEAT_INTERVAL=30000
```

4. Khởi chạy ứng dụng:

```bash
# Development
npm run dev

# Production
npm run build
npm start
```

## API Endpoints

### Authentication
- POST /api/users/register - Đăng ký
- POST /api/users/login - Đăng nhập
- GET /api/users/profile - Lấy thông tin user

### Messages
- GET /api/messages/:receiverId - Lấy tin nhắn
- POST /api/messages - Gửi tin nhắn
- PUT /api/messages/:messageId - Sửa tin nhắn
- DELETE /api/messages/:messageId - Xóa tin nhắn
- PUT /api/messages/:receiverId/mark-read - Đánh dấu đã đọc

### Admin
- POST /api/admin/setup-admin - Tạo admin đầu tiên
- GET /api/admin/info - Lấy thông tin admin

## WebSocket

WebSocket kết nối qua URL:
```
ws://localhost:3000/ws?token={jwt_token}
```

### Message Format

```typescript
interface WebSocketMessage {
  type: string;
  content?: string;
  receiverId?: string;
  message?: {
    id: string;
    content: string;
    sender_id: string;
    receiver_id: string;
    created_at: string;
    is_read: boolean;
  };
}
```

## Database Schema

```sql
CREATE TABLE users (
  id TEXT PRIMARY KEY,
  username TEXT UNIQUE NOT NULL,
  password TEXT NOT NULL,
  full_name TEXT NOT NULL,
  is_admin INTEGER DEFAULT 0,
  avatar_url TEXT,
  last_online DATETIME DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE messages (
  id TEXT PRIMARY KEY,
  content TEXT NOT NULL,
  sender_id TEXT NOT NULL,
  receiver_id TEXT NOT NULL,
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  is_read INTEGER DEFAULT 0,
  FOREIGN KEY (sender_id) REFERENCES users(id),
  FOREIGN KEY (receiver_id) REFERENCES users(id)
);
```

## License

MIT
