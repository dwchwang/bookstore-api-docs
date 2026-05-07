# Bookstore API

Tai lieu mo ta he thong Bookstore API va dac ta OpenAPI cho ung dung quan ly nha sach online.

## Tong quan he thong

Bookstore API la REST API demo cho mot he thong ban sach online. API ho tro cac chuc nang chinh:

- Dang ky, dang nhap va xac thuc nguoi dung bang JWT.
- Quan ly thong tin tai khoan ca nhan va anh dai dien.
- Xem, tim kiem, loc va phan trang danh sach sach.
- Tao don hang, xem danh sach don hang, xem chi tiet don hang va huy don hang.

Dac ta API duoc viet theo chuan OpenAPI `3.0.3` trong file [bookstore-api.yaml](./bookstore-api.yaml). Giao dien tai lieu API duoc hien thi bang Swagger UI trong file [index.html](./index.html).

## Cau truc thu muc

```text
.
├── bookstore-api.yaml   # Dac ta OpenAPI chinh
├── index.html           # Trang Swagger UI de xem va thu API
└── README.md            # Tai lieu he thong va huong dan su dung
```

## Moi truong server

OpenAPI dang khai bao 3 moi truong:

| Moi truong | Base URL |
| --- | --- |
| Production | `https://api.bookstore.com/v1` |
| Staging | `https://staging.bookstore.com/v1` |
| Local Development | `http://localhost:3000/v1` |



## Xac thuc

He thong su dung Bearer Token JWT cho cac endpoint can bao ve.

Header can gui:

```http
Authorization: Bearer <access_token>
```

Lay token bang endpoint:

```http
POST /auth/login
```

Mot so endpoint public khong can token:

- `POST /auth/register`
- `POST /auth/login`
- `GET /books`
- `GET /books/{bookId}`

## Nhom API

### Auth

| Method | Endpoint | Mo ta | Can token |
| --- | --- | --- | --- |
| `POST` | `/auth/register` | Dang ky tai khoan moi | Khong |
| `POST` | `/auth/login` | Dang nhap va lay access token, refresh token | Khong |

### Users

| Method | Endpoint | Mo ta | Can token |
| --- | --- | --- | --- |
| `GET` | `/users/me` | Lay thong tin profile cua nguoi dung dang dang nhap | Co |
| `PATCH` | `/users/me` | Cap nhat mot phan thong tin profile | Co |
| `POST` | `/users/me/avatar` | Upload anh dai dien JPEG/PNG toi da 5MB | Co |

### Books

| Method | Endpoint | Mo ta | Can token |
| --- | --- | --- | --- |
| `GET` | `/books` | Lay danh sach sach, co phan trang va bo loc | Khong |
| `GET` | `/books/{bookId}` | Lay chi tiet mot cuon sach | Khong |

Tham so loc cho `GET /books`:

| Tham so | Vi tri | Mo ta |
| --- | --- | --- |
| `page` | query | So trang, bat dau tu `1`, mac dinh `1` |
| `limit` | query | So item moi trang, mac dinh `10`, toi da `100` |
| `search` | query | Tim theo ten sach hoac tac gia |
| `category` | query | Loc theo the loai sach |
| `minPrice` | query | Gia toi thieu |
| `maxPrice` | query | Gia toi da |

Gia tri `category` hop le:

```text
programming, business, science, history, fiction, self-help
```

### Orders

| Method | Endpoint | Mo ta | Can token |
| --- | --- | --- | --- |
| `GET` | `/orders` | Lay danh sach don hang cua nguoi dung hien tai | Co |
| `POST` | `/orders` | Tao don hang moi | Co |
| `GET` | `/orders/{orderId}` | Lay chi tiet don hang | Co |
| `POST` | `/orders/{orderId}/cancel` | Huy don hang | Co |

Trang thai don hang:

| Trang thai | Y nghia |
| --- | --- |
| `pending` | Cho xac nhan |
| `confirmed` | Da xac nhan |
| `processing` | Dang dong goi/xu ly |
| `shipped` | Da giao cho don vi van chuyen |
| `delivered` | Khach da nhan hang |
| `cancelled` | Don hang da bi huy |

Chi co the huy don hang khi don hang dang o trang thai `pending` hoac `confirmed`.

## Vi du request

### Dang ky tai khoan

```http
POST /auth/register
Content-Type: application/json
```

```json
{
  "name": "Nguyen Van A",
  "email": "a@gmail.com",
  "password": "Secret@123"
}
```

### Dang nhap

```http
POST /auth/login
Content-Type: application/json
```

```json
{
  "email": "a@gmail.com",
  "password": "Secret@123"
}
```

Response thanh cong:

```json
{
  "accessToken": "eyJhbGciOiJIUzI1NiJ9...",
  "refreshToken": "eyJhbGciOiJIUzI1NiJ9...",
  "expiresIn": 86400
}
```

### Lay danh sach sach

```http
GET /books?page=1&limit=10&category=programming&search=Clean%20Code
```

### Tao don hang

```http
POST /orders
Authorization: Bearer <access_token>
Content-Type: application/json
```

```json
{
  "items": [
    {
      "bookId": 101,
      "quantity": 2
    },
    {
      "bookId": 102,
      "quantity": 1
    }
  ],
  "shippingAddress": {
    "street": "123 Nguyen Hue",
    "district": "Quan 1",
    "city": "Ho Chi Minh City"
  }
}
```

## Cac schema chinh

| Schema | Mo ta |
| --- | --- |
| `RegisterRequest` | Du lieu dang ky tai khoan |
| `LoginRequest` | Du lieu dang nhap |
| `AuthTokens` | Access token, refresh token va thoi gian het han |
| `User` | Thong tin nguoi dung |
| `UpdateProfileRequest` | Du lieu cap nhat profile |
| `Book` | Thong tin sach |
| `Order` | Thong tin don hang |
| `OrderItem` | Mot dong san pham trong don hang |
| `CreateOrderRequest` | Du lieu tao don hang |
| `Address` | Dia chi giao hang |
| `PaginationMeta` | Thong tin phan trang |
| `ErrorResponse` | Dinh dang loi dung chung |

## Dinh dang loi

API su dung response loi dang gan voi RFC 7807:

```json
{
  "status": 404,
  "title": "Not Found",
  "detail": "Resource does not exist"
}
```

Mot so ma loi thuong gap:

| HTTP status | Y nghia |
| --- | --- |
| `400` | Du lieu gui len khong hop le |
| `401` | Chua xac thuc hoac token het han |
| `403` | Khong co quyen truy cap tai nguyen |
| `404` | Khong tim thay tai nguyen |
| `409` | Email da ton tai |
| `429` | Vuot gioi han so luong request |

## Rate limiting

Theo mo ta trong OpenAPI, API gioi han toi da:

```text
100 requests/phut/IP
```

Khi vuot qua gioi han, server tra ve:

```http
429 Too Many Requests
```


## Thong tin lien he

- Tac gia: Duc Hoang
- Email: `baohoangbh@gmail.com`
- Facebook: `https://www.facebook.com/uchoang.237342/?locale=vi_VN`
