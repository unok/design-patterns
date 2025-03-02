# Bridge（ブリッジ）パターン

## 目的

抽象化を実装から分離し、両者を独立して変更できるようにします。

## 価値・解決する問題

- 抽象化と実装の分離を実現します
- 実装の詳細を隠蔽します
- インターフェースと実装の独立した拡張が可能です
- 実行時の実装の切り替えが可能です
- 単一継承の制限を克服します

## 概要・特徴

### 概要

ブリッジパターンは、抽象化と実装を分離し、それぞれを独立して変更できるようにする設計パターンです。このパターンでは、抽象化（インターフェースや抽象クラス）と実装（具体的な実装クラス）を別々のクラス階層として設計し、抽象化が実装へのリファレンスを保持することで両者をつなぎます。これにより、クライアントコードは抽象化されたインターフェースのみを使用し、具体的な実装の詳細から切り離されます。

ブリッジパターンは、GOF（Gang of Four）の構造パターンの一つで、「抽象と実装の分離」という設計原則を実現するための重要な手段です。この概念は、ソフトウェア工学において「依存関係逆転の原則」（Dependency Inversion Principle, DIP）とも密接に関連しています。物理的な橋が二つの場所を結びつけるように、このパターンは抽象化と実装という二つの側面を結びつけます。

ブリッジパターンは特に次のような状況で有効です：

1. **複数の実装バリエーションがある場合**: 異なるプラットフォーム、データベース、グラフィックシステムなど
2. **抽象化に複数の変形がある場合**: 基本機能に加えて拡張機能が必要な場合
3. **実装の詳細を隠蔽したい場合**: クライアントに実装の詳細を見せたくない場合
4. **継承による階層の爆発を避けたい場合**: 複数の軸で変化する要素がある場合

多くの現代的なフレームワークやライブラリでは、この原則を応用した設計が見られます。例えば、ORMフレームワークは、データアクセスの抽象化と具体的なデータベース実装を分離しています。また、クロスプラットフォームUIフレームワークは、共通のUIコンポーネント（抽象化）と各プラットフォーム固有の描画機能（実装）を分離しています。

### 特徴

#### 抽象化と実装の分離

ブリッジパターンの中核となる特徴は、抽象化と実装を明確に分離することです。これにより、それぞれが独立して変更や拡張が可能になります。例えば、グラフィックシステムにおいて、「形状」の抽象化（円、四角形、三角形など）と「描画方法」の実装（ベクターレンダリング、ラスターレンダリングなど）を分離することで、新しい形状の追加や描画方法の変更が互いに影響せずに行えます。この分離により、システムの柔軟性が向上し、変更に対する耐性が高まります。

抽象化と実装の分離には、以下のようなメカニズムが使用されます：

- **インターフェース定義**: 明確な契約を通じて抽象化と実装の間の関係を定義
- **委譲（Delegation）**: 抽象化が実装に処理を委譲することで機能を実現
- **間接参照**: 抽象化は実装のインターフェースのみを参照し、具体的な実装クラスは知らない
- **依存性注入**: 実装インスタンスを抽象化に外部から注入することで結合度を下げる

この分離は、マイクロサービスアーキテクチャや分散システムにおいても、サービス境界の設計原則として応用されています。

#### 実装の隠蔽

クライアントコードは抽象化されたインターフェースのみを使用し、具体的な実装の詳細を知る必要がありません。実装の詳細はブリッジを通じて隠蔽されます。例えば、データベース接続において、アプリケーションは抽象化されたデータベース操作インターフェースのみを使用し、実際のデータベース（MySQL、PostgreSQL、MongoDBなど）の違いを意識する必要がありません。これにより、クライアントコードはシンプルになり、実装の変更や切り替えの影響を受けにくくなります。

実装の隠蔽によるメリット：

- **クライアントコードの単純化**: 実装の複雑さがクライアントに漏れない
- **変更の局所化**: 実装の変更が抽象化レイヤーに閉じ込められる
- **セキュリティの向上**: 内部実装の詳細が外部から見えない
- **API設計の改善**: 抽象化レイヤーで一貫性のあるAPIを提供できる
- **チーム分業の促進**: 抽象化チームと実装チームが独立して作業できる

これは情報隠蔽（Information Hiding）原則の実践でもあり、モジュール間の結合度を下げる効果があります。

#### 柔軟な拡張性

抽象化と実装の両方を独立して拡張できるため、システムの柔軟性が高まります。新しい抽象化クラスや実装クラスを追加する際に、既存のコードを変更する必要がなく、開放/閉鎖原則（OCP）に従った設計が可能です。例えば、メディアプレーヤーアプリケーションでは、新しいメディアタイプ（抽象化）や再生デバイス（実装）を、互いに独立して追加できます。これにより、システムの機能を拡張する際の複雑さが軽減され、保守性が向上します。

拡張のシナリオと利点：

- **水平拡張**: 新しい実装を追加（例：新しいデータベースエンジンのサポート）
- **垂直拡張**: 抽象化に新機能を追加（例：基本的なレポート生成から高度な分析機能への拡張）
- **互換性の維持**: 既存の抽象化と新しい実装の互換性確保
- **段階的な進化**: システム全体を変更せずに部分的な機能拡張が可能
- **特化したバリエーション**: 特定のユースケースに最適化した実装の追加

この特性は特に長期にわたって進化する大規模システムで価値を発揮します。システムの寿命を通じて新しい要件や技術が追加されても、アーキテクチャの健全性を維持できます。

#### 実行時の切り替え

実行時に異なる実装を動的に切り替えることができます。抽象化は実装へのリファレンスを持ち、このリファレンスは実行時に変更可能です。例えば、グラフィカルユーザーインターフェース（GUI）において、実行時にテーマやスタイル（実装）を切り替えることができます。ユーザーは同じUI要素（抽象化）を操作しながら、ダークモードやライトモードなど異なる表示スタイルを選択できます。この動的な切り替え機能により、システムの適応性が向上し、ユーザー体験を向上させることができます。

実行時切り替えの応用例：

- **ユーザー設定によるカスタマイズ**: ユーザー設定に基づいて適切な実装を選択
- **環境適応**: 実行環境や利用可能なリソースに応じた実装の選択
- **A/Bテスト**: 異なる実装を同時に提供し、ユーザー反応を測定
- **フォールバックメカニズム**: 主要実装が利用できない場合の代替実装への切り替え
- **段階的なマイグレーション**: 古い実装から新しい実装への漸進的な移行

この特性は特に、高可用性が求められるシステムや、様々な環境で動作する必要があるアプリケーションに有用です。

#### 疎結合の実現

ブリッジパターンは、抽象化と実装の間に疎結合を実現します。抽象化は実装のインターフェースに依存するだけで、具体的な実装クラスに直接依存することはありません。これにより、一方の変更が他方に与える影響を最小限に抑えることができます。例えば、クロスプラットフォームアプリケーションでは、ビジネスロジック（抽象化）とプラットフォーム固有のAPI（実装）の間にブリッジを設けることで、プラットフォームの実装が変更されても、ビジネスロジックへの影響を最小限に抑えることができます。また、テストの観点からも疎結合は有利に働き、実装をモックオブジェクトに置き換えることで、抽象化部分を単体でテストすることが容易になります。この疎結合の特性により、大規模システムにおける変更管理が効率化され、長期的なメンテナンスコストの削減につながります。

疎結合がもたらす具体的なメリット：

- **変更の影響範囲の限定**: 一方の変更がもう一方に影響しにくい
- **並行開発の促進**: 抽象化チームと実装チームが独立して開発可能
- **テスト容易性の向上**: モックやスタブを使用した孤立テストが容易
- **バグの局所化**: 問題の原因特定が容易になる
- **コンポーネントの再利用性向上**: 低結合なコンポーネントは他の文脈でも使いやすい

この特性はマイクロサービスアーキテクチャなど、現代のシステム設計においても中心的な原則となっています。

#### 階層の爆発の防止

ブリッジパターンは、継承による「クラス階層の爆発」問題を効果的に解決します。2つの独立した変化の軸（例：形状と色）がある場合、単純な継承では全ての組み合わせに対してクラスを作成する必要があります（赤い円、青い円、赤い四角形、青い四角形など）。ブリッジパターンでは、抽象化（形状）と実装（色）を分離することで、n個の抽象化とm個の実装から、n×mの組み合わせをn+m個のクラスで表現できます。例えば、UIコンポーネントライブラリでは、コンポーネントの種類（ボタン、リスト、カード）とスタイル（マテリアルデザイン、フラットデザイン、カスタムテーマ）を分離することで、クラス数が大幅に削減されます。この効率的なクラス設計により、コードの重複が減少し、開発・保守の両面でコスト削減を実現できます。また、新しい抽象化や実装を追加する際のコスト増加が線形的になるため、システムの拡張性も向上します。

階層爆発防止の数学的利点：

- **クラス数の削減**: n×m個のクラスが必要な場合、n+m個のクラスで対応可能
- **コード重複の排除**: 共通機能がそれぞれの層で一度だけ実装される
- **変更波及の抑制**: 一つの変更がn個またはm個のクラスにのみ影響
- **線形的な拡張コスト**: 新要素追加時のコストが組み合わせ数に比例しない
- **メンテナンス容易性**: より少ないクラスで同じ機能を実現できるため保守が容易

この特性は特に、製品ライン開発やフレームワーク設計において重要な利点をもたらします。

#### 実装方法のバリエーション

ブリッジパターンには、状況に応じて適用できる複数の実装バリエーションがあります：

**1. 標準的なブリッジ実装**
- **メカニズム**: 抽象クラスが実装インターフェースへの参照を保持
- **適用場面**: 基本的なブリッジパターンの適用シナリオほとんど全て
- **特徴**: GOFが定義した典型的な構造に従う実装

**2. イベント駆動型ブリッジ**
- **メカニズム**: イベントやコールバックを通じて抽象化と実装が通信
- **適用場面**: 非同期処理が必要な場合や、実装が独立したライフサイクルを持つ場合
- **特徴**: 疎結合度がさらに高まるが、デバッグが複雑になる可能性がある

**3. 設定駆動型ブリッジ**
- **メカニズム**: 設定ファイルや動的パラメータに基づいて実装を選択
- **適用場面**: 実行環境に応じて異なる実装を使用する必要がある場合
- **特徴**: 実装の選択をコードから設定に移行することで柔軟性が向上

**4. 階層的ブリッジ**
- **メカニズム**: 複数のブリッジを階層的に組み合わせる
- **適用場面**: 複数の独立した変化の軸が存在する複雑なシステム
- **特徴**: 複雑な依存関係を整理できるが、設計の複雑性が増す

#### 使用時の注意点と課題

ブリッジパターンは強力ですが、適切に設計・実装する必要があります：

- **初期設計の複雑性**: ブリッジパターンは単純な継承より複雑な初期設計が必要
- **インターフェース設計の重要性**: 抽象化と実装のインターフェースが適切に設計されていないと、柔軟性が損なわれる
- **オーバーヘッド**: 間接レイヤーによるパフォーマンスへの若干の影響
- **デバッグの難しさ**: 複数のクラス間の相互作用を理解する必要がある
- **過剰設計のリスク**: 変化の軸が少ない単純なシステムでは過度な複雑性をもたらす可能性

これらの課題に対処するためには：

- システムの要件と進化の可能性を慎重に分析
- 抽象化と実装のインターフェースを慎重に設計
- 抽象化と実装の間の依存関係を明確に文書化
- 単体テストと統合テストを通じて設計の健全性を確認
- パターンの適用が本当に必要かどうかを評価（YAGNI原則）

適切な状況で適用された場合、ブリッジパターンはシステムの柔軟性と保守性を大幅に向上させる強力なツールです。

### 概要図

```mermaid
classDiagram
    class Abstraction {
        -implementor: Implementor
        +operation()
    }
    
    class RefinedAbstraction {
        +operation()
        +extraOperation()
    }
    
    class Implementor {
        <<interface>>
        +operationImpl()
    }
    
    class ConcreteImplementorA {
        +operationImpl()
    }
    
    class ConcreteImplementorB {
        +operationImpl()
    }
    
    Abstraction o-- Implementor : ブリッジ
    Abstraction <|-- RefinedAbstraction
    Implementor <|.. ConcreteImplementorA
    Implementor <|.. ConcreteImplementorB
    
    note for Abstraction "• 抽象化の高レベルインターフェース\n• 実装クラスへの参照を保持\n• クライアントはこのインターフェースを使用"
    note for RefinedAbstraction "• 抽象化の拡張\n• 追加機能の実装\n• 基本操作は実装者に委譲"
    note for Implementor "• 実装のインターフェース\n• 抽象化から独立した操作を定義\n• 具象実装者の共通インターフェース"
    note for ConcreteImplementorA "• 実装Aの具体的な実装\n• プラットフォームやAPIに特化した実装を提供"
    note for ConcreteImplementorB "• 実装Bの具体的な実装\n• 別の方法で機能を実現"
```

## 類似パターンとの比較

- [Adapter (アダプター)](adapter.md): Bridge は実装を抽象化から分離し、これに対して Adapter は既存のインターフェースを変換します。
- [Strategy (ストラテジー)](strategy.md): Bridge は実装を抽象化から分離し、これに対して Strategy はアルゴリズムを切り替えます。
- [Abstract Factory (アブストラクトファクトリー)](abstract-factory.md): Bridge は実装を抽象化から分離し、これに対して Abstract Factory は関連オブジェクトの生成を抽象化します。

## 利用されているライブラリ／フレームワークの事例

- [JDBC](https://docs.oracle.com/javase/8/docs/technotes/guides/jdbc/): データベースドライバーの抽象化
- [Java AWT](https://docs.oracle.com/javase/8/docs/api/java/awt/package-summary.html): プラットフォーム依存のGUI実装
- [Logging Frameworks](https://logging.apache.org/log4j/2.x/): ロギング実装の抽象化

## 解説ページリンク

- [Refactoring Guru - Bridge Pattern](https://refactoring.guru/design-patterns/bridge)
- [SourceMaking - Bridge Pattern](https://sourcemaking.com/design_patterns/bridge)
- [Design Patterns - Bridge Pattern](https://www.oodesign.com/bridge-pattern.html)

## コード例

### Before:

継承を使用した実装の例

```typescript
abstract class Shape {
  abstract draw(): void;
}

class RedCircle extends Shape {
  draw(): void {
    console.log("赤い円を描画");
  }
}

class BlueCircle extends Shape {
  draw(): void {
    console.log("青い円を描画");
  }
}

class RedSquare extends Shape {
  draw(): void {
    console.log("赤い四角を描画");
  }
}

class BlueSquare extends Shape {
  draw(): void {
    console.log("青い四角を描画");
  }
}

// 使用例
const shapes: Shape[] = [
  new RedCircle(),
  new BlueCircle(),
  new RedSquare(),
  new BlueSquare()
];

shapes.forEach(shape => shape.draw());
```

### After:

ブリッジパターンを適用した実装

```typescript
// 実装のインターフェース
interface DrawingAPI {
  drawCircle(x: number, y: number, radius: number): void;
  drawSquare(x: number, y: number, side: number): void;
  setColor(color: string): void;
  getColor(): string;
  setOpacity(opacity: number): void;
  getOpacity(): number;
  clear(): void;
  save(): void;
  restore(): void;
}

// 具象実装クラス: Canvas API
class CanvasAPI implements DrawingAPI {
  private color: string = "#000000";
  private opacity: number = 1.0;
  private stateStack: { color: string; opacity: number }[] = [];

  drawCircle(x: number, y: number, radius: number): void {
    console.log(`Canvas: 円を描画 (x: ${x}, y: ${y}, radius: ${radius}, color: ${this.color}, opacity: ${this.opacity})`);
  }

  drawSquare(x: number, y: number, side: number): void {
    console.log(`Canvas: 四角を描画 (x: ${x}, y: ${y}, side: ${side}, color: ${this.color}, opacity: ${this.opacity})`);
  }

  setColor(color: string): void {
    this.color = color;
  }

  getColor(): string {
    return this.color;
  }

  setOpacity(opacity: number): void {
    this.opacity = Math.max(0, Math.min(1, opacity));
  }

  getOpacity(): number {
    return this.opacity;
  }

  clear(): void {
    console.log("Canvas: 描画をクリア");
  }

  save(): void {
    this.stateStack.push({ color: this.color, opacity: this.opacity });
    console.log("Canvas: 状態を保存");
  }

  restore(): void {
    const state = this.stateStack.pop();
    if (state) {
      this.color = state.color;
      this.opacity = state.opacity;
      console.log("Canvas: 状態を復元");
    }
  }
}

// 具象実装クラス: SVG API
class SVGAPI implements DrawingAPI {
  private color: string = "#000000";
  private opacity: number = 1.0;
  private stateStack: { color: string; opacity: number }[] = [];

  drawCircle(x: number, y: number, radius: number): void {
    console.log(`SVG: <circle cx="${x}" cy="${y}" r="${radius}" fill="${this.color}" opacity="${this.opacity}" />`);
  }

  drawSquare(x: number, y: number, side: number): void {
    console.log(`SVG: <rect x="${x}" y="${y}" width="${side}" height="${side}" fill="${this.color}" opacity="${this.opacity}" />`);
  }

  setColor(color: string): void {
    this.color = color;
  }

  getColor(): string {
    return this.color;
  }

  setOpacity(opacity: number): void {
    this.opacity = Math.max(0, Math.min(1, opacity));
  }

  getOpacity(): number {
    return this.opacity;
  }

  clear(): void {
    console.log("SVG: 描画をクリア");
  }

  save(): void {
    this.stateStack.push({ color: this.color, opacity: this.opacity });
    console.log("SVG: 状態を保存");
  }

  restore(): void {
    const state = this.stateStack.pop();
    if (state) {
      this.color = state.color;
      this.opacity = state.opacity;
      console.log("SVG: 状態を復元");
    }
  }
}

// 抽象化クラス
abstract class Shape {
  constructor(protected api: DrawingAPI) {}

  abstract draw(): void;
  abstract move(x: number, y: number): void;
  abstract getArea(): number;

  setColor(color: string): void {
    this.api.setColor(color);
  }

  setOpacity(opacity: number): void {
    this.api.setOpacity(opacity);
  }
}

// 精緻化された抽象クラス: 円
class Circle extends Shape {
  constructor(
    api: DrawingAPI,
    private x: number,
    private y: number,
    private radius: number
  ) {
    super(api);
  }

  draw(): void {
    this.api.drawCircle(this.x, this.y, this.radius);
  }

  move(x: number, y: number): void {
    this.x = x;
    this.y = y;
  }

  getArea(): number {
    return Math.PI * this.radius * this.radius;
  }
}

// 精緻化された抽象クラス: 四角形
class Square extends Shape {
  constructor(
    api: DrawingAPI,
    private x: number,
    private y: number,
    private side: number
  ) {
    super(api);
  }

  draw(): void {
    this.api.drawSquare(this.x, this.y, this.side);
  }

  move(x: number, y: number): void {
    this.x = x;
    this.y = y;
  }

  getArea(): number {
    return this.side * this.side;
  }
}

// 図形グループ（Composite パターンとの組み合わせ）
class ShapeGroup extends Shape {
  private shapes: Shape[] = [];

  constructor(api: DrawingAPI) {
    super(api);
  }

  addShape(shape: Shape): void {
    this.shapes.push(shape);
  }

  draw(): void {
    this.api.save();
    this.shapes.forEach(shape => shape.draw());
    this.api.restore();
  }

  move(x: number, y: number): void {
    this.shapes.forEach(shape => shape.move(x, y));
  }

  getArea(): number {
    return this.shapes.reduce((total, shape) => total + shape.getArea(), 0);
  }

  setColor(color: string): void {
    this.shapes.forEach(shape => shape.setColor(color));
  }

  setOpacity(opacity: number): void {
    this.shapes.forEach(shape => shape.setOpacity(opacity));
  }
}

// 使用例
function example() {
  // 描画APIの作成
  const canvasAPI = new CanvasAPI();
  const svgAPI = new SVGAPI();

  console.log("=== Canvas APIを使用した描画 ===");
  
  // 個別の図形の描画
  const circle1 = new Circle(canvasAPI, 100, 100, 50);
  circle1.setColor("#FF0000");
  circle1.setOpacity(0.8);
  circle1.draw();

  const square1 = new Square(canvasAPI, 200, 200, 100);
  square1.setColor("#0000FF");
  square1.setOpacity(0.6);
  square1.draw();

  console.log("\n=== SVG APIを使用した描画 ===");

  // 図形グループの作成と描画
  const group = new ShapeGroup(svgAPI);
  
  const circle2 = new Circle(svgAPI, 150, 150, 75);
  circle2.setColor("#00FF00");
  
  const square2 = new Square(svgAPI, 300, 300, 150);
  square2.setColor("#FF00FF");
  
  group.addShape(circle2);
  group.addShape(square2);
  
  // グループ全体の透明度を設定
  group.setOpacity(0.7);
  group.draw();

  // 図形の移動
  console.log("\n=== 図形の移動 ===");
  circle1.move(120, 120);
  circle1.draw();

  // 面積の計算
  console.log("\n=== 面積の計算 ===");
  console.log(`Circle1の面積: ${circle1.getArea()}`);
  console.log(`Square1の面積: ${square1.getArea()}`);
  console.log(`グループ全体の面積: ${group.getArea()}`);
}

// 実行
example();
```

### 関数型プログラミングスタイルでのBridge実装:

```typescript
// 関数型プログラミングアプローチでのBridgeパターン

// =========== 実装部分（Implementation） ===========

// DrawingAPIの型定義
type DrawingAPI = {
  // 基本描画関数
  drawCircle: (x: number, y: number, radius: number, style: DrawingStyle) => void
  drawSquare: (x: number, y: number, side: number, style: DrawingStyle) => void
  
  // ユーティリティ関数
  clear: () => void
  getApiName: () => string
}

// 描画スタイルの型定義
type DrawingStyle = Readonly<{
  color: string
  opacity: number
}>

// 標準のスタイル
const defaultStyle: DrawingStyle = Object.freeze({
  color: '#000000',
  opacity: 1.0
})

// スタイルを作成する関数
const createStyle = (
  options: Partial<DrawingStyle> = {}
): DrawingStyle => Object.freeze({
  ...defaultStyle,
  ...options
})

// Canvas API の実装
const createCanvasAPI = (): DrawingAPI => {
  return {
    drawCircle: (x, y, radius, style) => {
      console.log(`Canvas: 円を描画 (x: ${x}, y: ${y}, radius: ${radius}, color: ${style.color}, opacity: ${style.opacity})`)
    },
    
    drawSquare: (x, y, side, style) => {
      console.log(`Canvas: 四角を描画 (x: ${x}, y: ${y}, side: ${side}, color: ${style.color}, opacity: ${style.opacity})`)
    },
    
    clear: () => {
      console.log('Canvas: 描画をクリア')
    },
    
    getApiName: () => 'Canvas API'
  }
}

// SVG API の実装
const createSvgAPI = (): DrawingAPI => {
  return {
    drawCircle: (x, y, radius, style) => {
      console.log(`SVG: <circle cx="${x}" cy="${y}" r="${radius}" fill="${style.color}" opacity="${style.opacity}" />`)
    },
    
    drawSquare: (x, y, side, style) => {
      console.log(`SVG: <rect x="${x}" y="${y}" width="${side}" height="${side}" fill="${style.color}" opacity="${style.opacity}" />`)
    },
    
    clear: () => {
      console.log('SVG: 描画をクリア')
    },
    
    getApiName: () => 'SVG API'
  }
}

// =========== 抽象化部分（Abstraction） ===========

// Shape（図形）の型定義
type Shape = {
  // 基本プロパティ
  readonly type: string
  position: { x: number, y: number }
  style: DrawingStyle
  
  // 操作
  draw: (api: DrawingAPI) => void
  move: (x: number, y: number) => Shape
  withStyle: (newStyle: Partial<DrawingStyle>) => Shape
  getArea: () => number
}

// 円を作成する関数
const createCircle = (
  x: number,
  y: number,
  radius: number,
  style: DrawingStyle = defaultStyle
): Shape => {
  // イミュータブルなオブジェクトとプロパティ
  const position = { x, y }
  
  // インスタンス
  const circle: Shape = {
    type: 'circle',
    position,
    style,
    
    draw: (api) => {
      api.drawCircle(position.x, position.y, radius, style)
    },
    
    move: (newX, newY) => {
      return createCircle(newX, newY, radius, style)
    },
    
    withStyle: (newStyleProps) => {
      const newStyle = createStyle({ ...style, ...newStyleProps })
      return createCircle(position.x, position.y, radius, newStyle)
    },
    
    getArea: () => Math.PI * radius * radius
  }
  
  return circle
}

// 四角形を作成する関数
const createSquare = (
  x: number,
  y: number,
  side: number,
  style: DrawingStyle = defaultStyle
): Shape => {
  // イミュータブルなオブジェクトとプロパティ
  const position = { x, y }
  
  // インスタンス
  const square: Shape = {
    type: 'square',
    position,
    style,
    
    draw: (api) => {
      api.drawSquare(position.x, position.y, side, style)
    },
    
    move: (newX, newY) => {
      return createSquare(newX, newY, side, style)
    },
    
    withStyle: (newStyleProps) => {
      const newStyle = createStyle({ ...style, ...newStyleProps })
      return createSquare(position.x, position.y, side, newStyle)
    },
    
    getArea: () => side * side
  }
  
  return square
}

// 図形グループを作成する関数
const createShapeGroup = (
  shapes: Shape[] = [],
  style: DrawingStyle = defaultStyle
): Shape & { addShape: (shape: Shape) => Shape & { addShape: any } } => {
  // インスタンス
  const group = {
    type: 'group',
    position: { x: 0, y: 0 }, // グループの位置は内部の図形に委譲
    style,
    
    draw: (api) => {
      console.log(`${api.getApiName()}: グループの描画を開始 (要素数: ${shapes.length})`)
      shapes.forEach(shape => shape.draw(api))
      console.log(`${api.getApiName()}: グループの描画を終了`)
    },
    
    move: (newX, newY) => {
      // 各図形の相対位置を維持したまま移動
      const offsetX = newX - group.position.x
      const offsetY = newY - group.position.y
      
      const movedShapes = shapes.map(shape => 
        shape.move(shape.position.x + offsetX, shape.position.y + offsetY)
      )
      
      return createShapeGroup(movedShapes, style)
    },
    
    withStyle: (newStyleProps) => {
      const newStyle = createStyle({ ...style, ...newStyleProps })
      
      // グループ内の全ての図形にスタイルを適用
      const styledShapes = shapes.map(shape => 
        shape.withStyle(newStyleProps)
      )
      
      return createShapeGroup(styledShapes, newStyle)
    },
    
    getArea: () => {
      return shapes.reduce((total, shape) => total + shape.getArea(), 0)
    },
    
    // グループ固有の操作
    addShape: (shape) => {
      return createShapeGroup([...shapes, shape], style)
    }
  }
  
  return group
}

// =========== 操作関数（Operations） ===========

// 図形の描画をラップする関数
const drawShape = (api: DrawingAPI) => (shape: Shape): void => {
  shape.draw(api)
}

// スタイル適用をラップする関数
const withColor = (color: string) => (shape: Shape): Shape => {
  return shape.withStyle({ color })
}

const withOpacity = (opacity: number) => (shape: Shape): Shape => {
  return shape.withStyle({ opacity })
}

// 関数合成ユーティリティ
const pipe = <T>(...fns: Array<(arg: T) => T>) => 
  (value: T): T => fns.reduce((acc, fn) => fn(acc), value)

// =========== クライアントコード（Client） ===========

// 関数型プログラミングスタイルでのサンプル
const functionalExample = (): void => {
  console.log('\n=== 関数型プログラミングスタイルでのBridgeパターン ===\n')
  
  // APIの作成
  const canvasAPI = createCanvasAPI()
  const svgAPI = createSvgAPI()
  
  // 図形の作成
  const redCircle = pipe(
    withColor('#FF0000'),
    withOpacity(0.8)
  )(createCircle(100, 100, 50))
  
  const blueSquare = pipe(
    withColor('#0000FF'),
    withOpacity(0.6)
  )(createSquare(200, 200, 100))

  console.log('=== Canvas APIを使用した描画（関数型） ===')
  
  // 描画関数の作成
  const drawOnCanvas = drawShape(canvasAPI)
  
  // 個別の図形の描画
  drawOnCanvas(redCircle)
  drawOnCanvas(blueSquare)
  
  console.log('\n=== SVG APIを使用した描画（関数型） ===')
  
  // 描画関数の作成
  const drawOnSVG = drawShape(svgAPI)
  
  // 図形グループの作成
  const greenCircle = pipe(
    withColor('#00FF00')
  )(createCircle(150, 150, 75))
  
  const purpleSquare = pipe(
    withColor('#FF00FF')
  )(createSquare(300, 300, 150))
  
  // イミュータブルな方法でグループを構築
  const group = pipe(
    group => group.addShape(greenCircle),
    group => group.addShape(purpleSquare),
    withOpacity(0.7)
  )(createShapeGroup())
  
  // グループの描画
  drawOnSVG(group)
  
  // 図形の移動 (イミュータブル)
  console.log('\n=== 図形の移動（関数型） ===')
  const movedCircle = redCircle.move(120, 120)
  drawOnCanvas(movedCircle)
  
  // 面積の計算
  console.log('\n=== 面積の計算（関数型） ===')
  console.log(`元の円の面積: ${redCircle.getArea()}`)
  console.log(`青い四角形の面積: ${blueSquare.getArea()}`)
  console.log(`グループ全体の面積: ${group.getArea()}`)
  
  // 関数型アプローチを活用した操作
  console.log('\n=== 高度な操作（関数型） ===')
  
  // マップ操作: 全ての図形の面積を計算
  const shapes = [redCircle, blueSquare, greenCircle, purpleSquare]
  const areas = shapes.map(shape => shape.getArea())
  console.log('全ての図形の面積:', areas)
  
  // フィルター操作: 特定の条件の図形だけを抽出
  const largeShapes = shapes.filter(shape => shape.getArea() > 10000)
  console.log(`大きな図形の数: ${largeShapes.length}`)
  
  // リデュース操作: 全ての図形の面積の合計
  const totalArea = shapes.reduce((sum, shape) => sum + shape.getArea(), 0)
  console.log(`全ての図形の面積の合計: ${totalArea}`)
}

// 実行
functionalExample()
```
