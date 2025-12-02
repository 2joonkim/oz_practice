# 사용자 중심 ERD (PostgreSQL)

```mermaid
erDiagram
    users ||--o{ orders : "주문"
    users ||--o{ addresses : "배송지"
    users ||--o{ reviews : "리뷰"
    users ||--o{ cart_items : "장바구니"
    users ||--|| user_profiles : "프로필"
    users ||--o{ user_roles : "역할"

    orders ||--|{ order_items : "주문상품"
    orders ||--|| payments : "결제"
    orders }o--|| addresses : "배송"

    products ||--o{ order_items : "포함"
    products ||--o{ cart_items : "담김"
    products ||--o{ reviews : "평가"
    products }o--|| categories : "분류"

    roles ||--o{ user_roles : "할당"

    users {
        serial id PK
        varchar email UK
        varchar password_hash
        varchar username
        boolean is_active
        timestamp created_at
        timestamp updated_at
    }

    user_profiles {
        serial id PK
        int user_id FK
        varchar full_name
        varchar phone
        date birth_date
        varchar profile_image_url
    }

    addresses {
        serial id PK
        int user_id FK
        varchar address_name
        varchar recipient_name
        varchar phone
        varchar zip_code
        varchar address1
        varchar address2
        boolean is_default
    }

    roles {
        serial id PK
        varchar role_name UK
        varchar description
    }

    user_roles {
        serial id PK
        int user_id FK
        int role_id FK
        timestamp assigned_at
    }

    orders {
        serial id PK
        int user_id FK
        int shipping_address_id FK
        varchar order_number UK
        varchar status
        decimal total_amount
        timestamp ordered_at
    }

    order_items {
        serial id PK
        int order_id FK
        int product_id FK
        int quantity
        decimal unit_price
        decimal subtotal
    }

    payments {
        serial id PK
        int order_id FK
        varchar payment_method
        varchar status
        decimal amount
        timestamp paid_at
    }

    products {
        serial id PK
        int category_id FK
        varchar name
        text description
        decimal price
        int stock_quantity
        boolean is_available
    }

    categories {
        serial id PK
        varchar name
        int parent_id
    }

    cart_items {
        serial id PK
        int user_id FK
        int product_id FK
        int quantity
        timestamp added_at
    }

    reviews {
        serial id PK
        int user_id FK
        int product_id FK
        int rating
        text content
        timestamp created_at
    }
```

## 테이블 설명

| 테이블 | 설명 |
|--------|------|
| **users** | 사용자 기본 정보 (로그인 계정) |
| **user_profiles** | 사용자 상세 프로필 (1:1 관계) |
| **addresses** | 사용자 배송지 목록 |
| **roles** | 권한 역할 정의 (admin, user 등) |
| **user_roles** | 사용자-역할 매핑 (다대다) |
| **orders** | 주문 정보 |
| **order_items** | 주문 상세 품목 |
| **payments** | 결제 정보 |
| **products** | 상품 정보 |
| **categories** | 상품 카테고리 (셀프조인으로 계층구조) |
| **cart_items** | 장바구니 |
| **reviews** | 상품 리뷰 |

## 주요 관계

- `users` → `user_profiles` : 1:1 (사용자당 하나의 프로필)
- `users` → `addresses` : 1:N (여러 배송지 등록 가능)
- `users` → `orders` : 1:N (여러 주문 가능)
- `users` ↔ `roles` : N:M (user_roles 중간 테이블)
- `orders` → `order_items` : 1:N (주문당 여러 상품)
- `products` → `categories` : N:1 (카테고리별 상품 분류)
