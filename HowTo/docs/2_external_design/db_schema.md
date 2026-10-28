# データベース定義（DB Schema）

## 1. テーブル一覧

| テーブル名 | 説明 | PK | FK |
|-----------|------|-----|----|
| `users` | ユーザー情報 | user_id | - |
| `user_profiles` | ユーザープロフィール | profile_id | user_id |
| `payment_transactions` | 決済取引履歴 | transaction_id | user_id, product_id |
| `notifications` | 通知メッセージ | notification_id | user_id |
| `products` | 商品情報 | product_id | - |

## 2. テーブル定義詳細

### users（ユーザー情報）
```sql
CREATE TABLE users (
    user_id VARCHAR(36) PRIMARY KEY,
    email VARCHAR(255) NOT NULL UNIQUE,
    password_hash VARCHAR(255) NOT NULL,
    first_name VARCHAR(100),
    last_name VARCHAR(100),
    status ENUM('active', 'inactive', 'pending') DEFAULT 'pending',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    INDEX idx_email (email),
    INDEX idx_status (status)
);
```

### user_profiles（ユーザープロフィール）
```sql
CREATE TABLE user_profiles (
    profile_id INT AUTO_INCREMENT PRIMARY KEY,
    user_id VARCHAR(36) NOT NULL,
    avatar_url VARCHAR(500),
    bio TEXT,
    preferences JSON,
    
    FOREIGN KEY (user_id) REFERENCES users(user_id) 
        ON DELETE CASCADE 
        ON UPDATE CASCADE,
    
    INDEX idx_user_id (user_id)
);
```

### payment_transactions（決済取引履歴）
```sql
CREATE TABLE payment_transactions (
    transaction_id VARCHAR(36) PRIMARY KEY,
    user_id VARCHAR(36) NOT NULL,
    product_id INT NOT NULL,
    amount DECIMAL(10, 2) NOT NULL,
    currency VARCHAR(3) DEFAULT 'JPY',
    status ENUM('pending', 'completed', 'failed', 'refunded') DEFAULT 'pending',
    payment_method VARCHAR(50),
    transaction_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (user_id) REFERENCES users(user_id) 
        ON DELETE CASCADE,
    FOREIGN KEY (product_id) REFERENCES products(product_id) 
        ON DELETE RESTRICT,
    
    INDEX idx_user_transaction (user_id, transaction_date),
    INDEX idx_status (status)
);
```

### notifications（通知メッセージ）
```sql
CREATE TABLE notifications (
    notification_id INT AUTO_INCREMENT PRIMARY KEY,
    user_id VARCHAR(36) NOT NULL,
    message_type ENUM('email', 'push', 'sms') NOT NULL,
    subject VARCHAR(255),
    body TEXT,
    is_read BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (user_id) REFERENCES users(user_id) 
        ON DELETE CASCADE,
    
    INDEX idx_user_notifications (user_id, created_at),
    INDEX idx_is_read (is_read)
);
```

### products（商品情報）
```sql
CREATE TABLE products (
    product_id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    price DECIMAL(10, 2) NOT NULL,
    currency VARCHAR(3) DEFAULT 'JPY',
    stock_quantity INT DEFAULT 0,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_price (price),
    INDEX idx_is_active (is_active)
);
```

## 3. システム共通制約

### 3.1 文字コード
- **全テーブル**: UTF-8
- **コラレーション**: `utf8mb4_unicode_ci`

### 3.2 外鍵制約ポリシー
- カスケード削除：子テーブルで `ON DELETE CASCADE`
- セット NULL: オプションフラグで `ON SET NULL`

### 3.3 インデックス戦略
- 検索頻度の高いカラムにインデックスを配置
- 複合インデックスは WHERE 条件の順序に注意

## 4. データ移行・初期化スクリプト

```sql
-- 初期データ挿入例
INSERT INTO products (name, description, price, stock_quantity) VALUES
('サンプル商品 A', 'テスト用商品', 1000, 100),
('サンプル商品 B', 'テスト用商品', 2000, 50);

-- ユーザー初期データ（パスワードはハッシュ済み）
INSERT INTO users (user_id, email, password_hash, first_name, last_name) VALUES
('usr-001', 'admin@example.com', '$2b$12$...', '管理者', 'テスト');
```