# Value Object（値オブジェクト）パターン

## 目的

値の不変性と等価性を保証し、ドメインの概念を明確に表現するためのパターンです。

## 価値・解決する問題

- 値の不変性を保証します
- 等価性の比較を適切に行えます
- ドメインの概念を明確に表現できます
- バリデーションを集約できます
- 型安全性を向上させます

## 概要・特徴

### 概要

Value Objectパターンは、値の不変性と等価性に焦点を当てた設計パターンです。オブジェクトの同一性ではなく、値の等価性に基づいて比較を行います。この設計パターンを使用することで、ドメインの概念をより明確に表現し、バリデーションを一箇所に集約し、型安全性を向上させることができます。

### 特徴

#### 不変性の保証

Value Objectは一度作成されると変更できない不変（イミュータブル）なオブジェクトとして設計されます。これにより、オブジェクトが予期せず変更されることを防ぎ、副作用のない安全なコードを実現できます。例えば、金額を表す`Money`クラスでは、一度作成された金額オブジェクトは変更できず、演算操作（加算や乗算など）を行う場合は常に新しいオブジェクトを返します。この特性により、並行処理環境でも安全に値を共有でき、デバッグが容易になります。

#### 等価性の比較

Value Objectは参照ではなく、その内容（値）によって等価性が判断されます。同じ値を持つ2つのValue Objectは、異なるメモリ上の参照であっても等価とみなされます。例えば、`10 USD`という金額を表す2つの異なるMoneyオブジェクトは、参照は異なるものの等価と判断されます。この特性は、ドメインの概念をより自然に表現するのに役立ち、ビジネスルールの実装を明確にします。等価性の比較は通常、すべての属性値を比較する`equals`メソッドを実装することで実現されます。

#### 自己完結性

Value Objectは自身の状態と振る舞いを完全に内包しており、外部の状態に依存しません。これにより、Value Objectを使用するコードはシンプルになり、予測可能性が向上します。例えば、`EmailAddress`クラスは電子メールアドレスの検証ロジックと操作（ドメイン部分の抽出など）をすべて内包し、使用する側はその詳細を知る必要がありません。この自己完結性により、Value Objectは再利用可能なコンポーネントとなり、システム全体の一貫性が向上します。

#### バリデーションの集約

Value Objectは作成時にデータの妥当性を検証し、無効な状態のオブジェクトが存在しないことを保証します。これにより、バリデーションロジックが一箇所に集約され、コードの重複が防止されます。例えば、`PostalCode`クラスは郵便番号の形式検証を内部で行い、無効な形式の郵便番号を持つオブジェクトが作成されることを防ぎます。バリデーションが集約されているため、ビジネスルールの変更があった場合も一箇所の修正で対応でき、保守性が向上します。

#### 型安全性の向上

プリミティブ型の代わりにValue Objectを使用することで、型安全性が向上します。例えば、単なる文字列ではなく`EmailAddress`型を使用することで、コンパイル時に型チェックが行われ、誤った使用方法を早期に検出できます。また、IDやコードなど似たような文字列型を区別することもできます。例えば、`CustomerId`と`ProductId`を別々のValue Objectとして定義することで、誤って混同することを防ぎます。型安全性の向上により、バグの発生リスクが低減し、コードの信頼性が向上します。

## 類似パターンとの比較

- [Entity (エンティティ)](entity.md): Value Object は値の等価性に焦点を当て、これに対して Entity は同一性に焦点を当てます。
- [DTO (データ転送オブジェクト)](dto.md): Value Object はドメインの概念を表現し、これに対して DTO はデータの転送に特化します。
- [Immutable (イミュータブル)](immutable.md): Value Object は値の等価性と不変性を組み合わせ、これに対して Immutable は不変性のみに焦点を当てます。

## 利用されているライブラリ／フレームワークの事例

- [Java BigDecimal](https://docs.oracle.com/javase/8/docs/api/java/math/BigDecimal.html): 不変な数値表現
- [UUID](https://developer.mozilla.org/en-US/docs/Web/API/Crypto/randomUUID): 一意識別子
- [Moment.js](https://momentjs.com/): 日時操作

## 解説ページリンク

- [Martin Fowler - Value Object](https://martinfowler.com/bliki/ValueObject.html)
- [DDD - Value Objects](https://deviq.com/domain-driven-design/value-object)
- [Implementing Value Objects](https://docs.microsoft.com/en-us/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns/implement-value-objects)

## コード例

### Before:

プリミティブ型を直接使用する実装

```typescript
class Product {
  constructor(
    public name: string,
    public price: number,
    public currency: string
  ) {}

  getPrice(): string {
    return `${this.price} ${this.currency}`;
  }

  updatePrice(newPrice: number): void {
    this.price = newPrice;
  }
}

// 使用例
const product = new Product("Book", 1000, "JPY");
product.updatePrice(1200);
console.log(product.getPrice());
```

### After:

Value Objectパターンを適用した実装

```typescript
// 金額を表すValue Object
class Money {
  private constructor(
    private readonly amount: number,
    private readonly currency: string
  ) {
    this.validateAmount(amount);
    this.validateCurrency(currency);
  }

  static of(amount: number, currency: string): Money {
    return new Money(amount, currency);
  }

  static yen(amount: number): Money {
    return new Money(amount, "JPY");
  }

  static dollar(amount: number): Money {
    return new Money(amount, "USD");
  }

  static euro(amount: number): Money {
    return new Money(amount, "EUR");
  }

  private validateAmount(amount: number): void {
    if (!Number.isFinite(amount)) {
      throw new Error("金額は有限の数値である必要があります");
    }
    if (amount < 0) {
      throw new Error("金額は0以上である必要があります");
    }
  }

  private validateCurrency(currency: string): void {
    const validCurrencies = ["JPY", "USD", "EUR"];
    if (!validCurrencies.includes(currency)) {
      throw new Error(`無効な通貨コードです: ${currency}`);
    }
  }

  equals(other: Money): boolean {
    return this.amount === other.amount && this.currency === other.currency;
  }

  add(other: Money): Money {
    if (this.currency !== other.currency) {
      throw new Error("異なる通貨同士の加算はできません");
    }
    return new Money(this.amount + other.amount, this.currency);
  }

  subtract(other: Money): Money {
    if (this.currency !== other.currency) {
      throw new Error("異なる通貨同士の減算はできません");
    }
    return new Money(this.amount - other.amount, this.currency);
  }

  multiply(multiplier: number): Money {
    return new Money(this.amount * multiplier, this.currency);
  }

  toString(): string {
    return `${this.amount} ${this.currency}`;
  }

  getAmount(): number {
    return this.amount;
  }

  getCurrency(): string {
    return this.currency;
  }
}

// メールアドレスを表すValue Object
class EmailAddress {
  private constructor(private readonly value: string) {
    this.validate(value);
  }

  static of(value: string): EmailAddress {
    return new EmailAddress(value);
  }

  private validate(value: string): void {
    if (!value.includes("@")) {
      throw new Error("無効なメールアドレスです");
    }
  }

  equals(other: EmailAddress): boolean {
    return this.value === other.value;
  }

  toString(): string {
    return this.value;
  }

  getDomain(): string {
    return this.value.split("@")[1];
  }
}

// 住所を表すValue Object
class Address {
  private constructor(
    private readonly postalCode: string,
    private readonly prefecture: string,
    private readonly city: string,
    private readonly street: string,
    private readonly building?: string
  ) {
    this.validatePostalCode(postalCode);
    this.validatePrefecture(prefecture);
  }

  static of(
    postalCode: string,
    prefecture: string,
    city: string,
    street: string,
    building?: string
  ): Address {
    return new Address(postalCode, prefecture, city, street, building);
  }

  private validatePostalCode(postalCode: string): void {
    if (!/^\d{3}-\d{4}$/.test(postalCode)) {
      throw new Error("無効な郵便番号です");
    }
  }

  private validatePrefecture(prefecture: string): void {
    const validPrefectures = [
      "北海道", "青森県", "岩手県", // ... 他の都道府県
    ];
    if (!validPrefectures.includes(prefecture)) {
      throw new Error("無効な都道府県です");
    }
  }

  equals(other: Address): boolean {
    return (
      this.postalCode === other.postalCode &&
      this.prefecture === other.prefecture &&
      this.city === other.city &&
      this.street === other.street &&
      this.building === other.building
    );
  }

  toString(): string {
    const parts = [
      this.postalCode,
      this.prefecture,
      this.city,
      this.street,
      this.building
    ].filter(part => part !== undefined);
    return parts.join(" ");
  }
}

// 商品を表すエンティティ（Value Objectを使用）
class Product {
  constructor(
    private readonly id: string,
    private readonly name: string,
    private price: Money
  ) {}

  getPrice(): Money {
    return this.price;
  }

  updatePrice(newPrice: Money): Product {
    return new Product(this.id, this.name, newPrice);
  }

  equals(other: Product): boolean {
    return this.id === other.id;
  }
}

// 注文を表すエンティティ（Value Objectを使用）
class Order {
  private constructor(
    private readonly id: string,
    private readonly items: OrderItem[],
    private readonly shippingAddress: Address,
    private readonly customerEmail: EmailAddress
  ) {}

  static create(
    id: string,
    items: OrderItem[],
    shippingAddress: Address,
    customerEmail: EmailAddress
  ): Order {
    if (items.length === 0) {
      throw new Error("注文には少なくとも1つの商品が必要です");
    }
    return new Order(id, items, shippingAddress, customerEmail);
  }

  getTotalAmount(): Money {
    return this.items.reduce(
      (total, item) => total.add(item.getSubtotal()),
      Money.yen(0)
    );
  }

  getItems(): OrderItem[] {
    return [...this.items];
  }

  getShippingAddress(): Address {
    return this.shippingAddress;
  }

  getCustomerEmail(): EmailAddress {
    return this.customerEmail;
  }
}

// 注文項目を表すValue Object
class OrderItem {
  private constructor(
    private readonly product: Product,
    private readonly quantity: number
  ) {
    this.validateQuantity(quantity);
  }

  static of(product: Product, quantity: number): OrderItem {
    return new OrderItem(product, quantity);
  }

  private validateQuantity(quantity: number): void {
    if (!Number.isInteger(quantity) || quantity <= 0) {
      throw new Error("数量は正の整数である必要があります");
    }
  }

  getSubtotal(): Money {
    return this.product.getPrice().multiply(this.quantity);
  }

  equals(other: OrderItem): boolean {
    return (
      this.product.equals(other.product) &&
      this.quantity === other.quantity
    );
  }
}

// 使用例
function example() {
  try {
    // 商品の作成
    const book = new Product(
      "1",
      "プログラミングの基礎",
      Money.yen(2800)
    );

    const pen = new Product(
      "2",
      "ボールペン",
      Money.yen(100)
    );

    // 注文項目の作成
    const orderItems = [
      OrderItem.of(book, 2),
      OrderItem.of(pen, 3)
    ];

    // 注文の作成
    const order = Order.create(
      "order-1",
      orderItems,
      Address.of(
        "123-4567",
        "東京都",
        "渋谷区",
        "代々木1-1-1",
        "渋谷ビル101"
      ),
      EmailAddress.of("customer@example.com")
    );

    // 注文内容の表示
    console.log("=== 注文内容 ===");
    console.log("配送先:", order.getShippingAddress().toString());
    console.log("連絡先:", order.getCustomerEmail().toString());
    console.log("合計金額:", order.getTotalAmount().toString());

    // Value Objectの等価性テスト
    console.log("\n=== 等価性テスト ===");
    const price1 = Money.yen(1000);
    const price2 = Money.yen(1000);
    const price3 = Money.dollar(1000);

    console.log("同じ金額の比較:", price1.equals(price2));
    console.log("異なる通貨の比較:", price1.equals(price3));

    // 金額計算
    console.log("\n=== 金額計算 ===");
    const sum = price1.add(Money.yen(500));
    const product = price1.multiply(2);
    console.log("加算結果:", sum.toString());
    console.log("乗算結果:", product.toString());

  } catch (error) {
    console.error("エラー:", error.message);
  }
}

example();

### 概要図

```mermaid
classDiagram
    class ValueObject {
        -properties: any
        +equals(other: ValueObject)
        +clone()
        +validate()
    }
    note for ValueObject "値オブジェクト
• 不変性を保証
• 等価性で比較
• 自己完結的な検証"

    class Money {
        -amount: number
        -currency: string
        +equals(other: Money)
        +add(other: Money)
        +subtract(other: Money)
        +multiply(factor: number)
    }
    note for Money "金額を表す値オブジェクト
• 不変な金額と通貨
• 演算操作を提供
• 通貨の一致を検証"

    class EmailAddress {
        -address: string
        +equals(other: EmailAddress)
        +getDomain()
        +validate()
    }
    note for EmailAddress "メールアドレスを表す値オブジェクト
• アドレスの妥当性を検証
• ドメイン部分の抽出
• 等価性の比較"

    class Address {
        -postalCode: string
        -prefecture: string
        -city: string
        -street: string
        +equals(other: Address)
        +validate()
        +toString()
    }
    note for Address "住所を表す値オブジェクト
• 住所要素を不変に保持
• 文字列表現を提供
• 妥当性の検証"

    class Entity {
        -id: string
        -valueObjects: ValueObject[]
        +equals(other: Entity)
    }
    note for Entity "エンティティ
• 値オブジェクトを利用
• IDによる同一性
• ライフサイクル管理"