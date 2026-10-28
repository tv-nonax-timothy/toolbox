# 共通 UI コンポーネント仕様

## 1. デザインシステム概要

### 1.1 カラーパレット

#### プライマリーカラー
- **Primary**: `#007AFF` (Apple Blue)
- **Primary Dark**: `#0056CC`
- **Primary Light**: `#5AC8FF`

#### セカンダリカラー
- **Secondary**: `#5856D6` (Purple)
- **Secondary Dark**: `#4341C2`

#### アクセントカラー
- **Accent Green**: `#34C759`
- **Accent Orange**: `#FF9500`
- **Accent Red**: `#FF3B30`

### 1.2 タイポグラフィ

#### フォントスタック
```css
font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 
             'Helvetica Neue', Arial, sans-serif;
```

#### ヘッディングサイズ
- **H1**: 32px / 24px (レスポンシブ)
- **H2**: 28px / 20px
- **H3**: 24px / 18px
- **H4**: 20px / 16px

#### ボディテキスト
- **Large**: 18px / line-height: 1.5
- **Normal**: 16px / line-height: 1.5
- **Small**: 14px / line-height: 1.4

## 2. コンポーネントライブラリ

### 2.1 ボタンコンポーネント

#### プライマリーボタン
```css
background-color: #007AFF;
color: white;
padding: 12px 24px;
border-radius: 8px;
font-weight: 500;
cursor: pointer;
transition: all 0.2s ease;

:hover { background-color: #0056CC; }
:active { transform: scale(0.98); }
```

#### セカンダリーボタン（アウトライナー）
```css
background-color: transparent;
border: 1px solid #007AFF;
color: #007AFF;
padding: 12px 24px;
border-radius: 8px;
font-weight: 500;

:hover { background-color: rgba(0, 122, 255, 0.1); }
```

#### テキストボタン
```css
background-color: transparent;
color: #007AFF;
padding: 8px 16px;
cursor: pointer;

:hover { text-decoration: underline; }
```

### 2.2 フォームコンポーネント

#### インプットフィールド
```css
border: 1px solid #D2D2D7;
border-radius: 8px;
padding: 10px 12px;
font-size: 16px;
transition: border-color 0.2s ease;

:focus { 
    border-color: #007AFF;
    outline: none;
    box-shadow: 0 0 0 3px rgba(0, 122, 255, 0.1);
}

:error { 
    border-color: #FF3B30;
}
```

#### チェックボックス
```css
width: 20px;
height: 20px;
border: 2px solid #D2D2D7;
border-radius: 4px;
cursor: pointer;

:checked { 
    background-color: #007AFF;
    border-color: #007AFF;
}
```

#### レーディオボタン
```css
width: 20px;
height: 20px;
border: 2px solid #D2D2D7;
border-radius: 50%;
cursor: pointer;
display: inline-flex;
align-items: center;
justify-content: center;

:checked { 
    background-color: #007AFF;
    border-color: #007AFF;
}
```

### 2.3 カードコンポーネント

#### ベースカード
```css
background-color: white;
border-radius: 12px;
box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
padding: 24px;
transition: box-shadow 0.2s ease;

:hover { 
    box-shadow: 0 4px 16px rgba(0, 0, 0, 0.15);
}
```

#### カードヘッダー
```css
font-size: 18px;
font-weight: 600;
margin-bottom: 12px;
color: #1D1D1F;
```

### 2.4 アラートコンポーネント

#### 成功アラート（グリーン）
```css
background-color: rgba(52, 199, 89, 0.1);
border-left: 4px solid #34C759;
color: #1D1D1F;
padding: 16px;
border-radius: 8px;
```

#### エラーアラート（レッド）
```css
background-color: rgba(255, 59, 48, 0.1);
border-left: 4px solid #FF3B30;
color: #1D1D1F;
padding: 16px;
border-radius: 8px;
```

#### ワーニングアラート（オレンジ）
```css
background-color: rgba(255, 149, 0, 0.1);
border-left: 4px solid #FF9500;
color: #1D1D1F;
padding: 16px;
border-radius: 8px;
```

## 3. レイアウトグリッド

### 3.1 コンテナ（最大幅）
- **Desktop**: max-width: 1200px, margin: 0 auto
- **Tablet**: max-width: 768px
- **Mobile**: 100% width

### 3.2 グリッドシステム
```css
/* Desktop */
grid-template-columns: repeat(3, 1fr);
gap: 24px;

/* Tablet */
@media (max-width: 768px) {
    grid-template-columns: repeat(2, 1fr);
}

/* Mobile */
@media (max-width: 480px) {
    grid-template-columns: 1fr;
}
```

## 4. アニメーション

### 4.1 フェードイン
```css
@keyframes fadeIn {
    from { opacity: 0; transform: translateY(-10px); }
    to { opacity: 1; transform: translateY(0); }
}

.fade-in {
    animation: fadeIn 0.3s ease-out;
}
```

### 4.2 スライドイン（サイドバー）
```css
@keyframes slideInRight {
    from { 
        opacity: 0; 
        transform: translateX(100%); 
    }
    to { 
        opacity: 1; 
        transform: translateX(0); 
    }
}

.slide-in-right {
    animation: slideInRight 0.3s ease-out;
}
```

## 5. アクセシビリティ要件

### 5.1 コントラスト比
- テキストと背景：最小 4.5:1 (WCAG AA)
- フォーカスインジケーター：明確な視認性確保

### 5.2 キーボードナビゲーション
- Tab キーで要素間移動可能
- Enter/Space でアクティブ要素操作可能

### 5.3 スクリーンリーダー対応
- ARIA ラベルの適切な設定
- フォーカス順序の論理的な配置