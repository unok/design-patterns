# Composite（コンポジット）パターン

## 目的

個々のオブジェクトと複合オブジェクトを同一視し、ツリー構造を表現するパターンです。

## 価値・解決する問題

- 個々のオブジェクトと複合オブジェクトを統一的に扱えます
- 階層構造を効果的に表現できます
- クライアントコードを単純化できます
- 再帰的な処理を容易に実装できます
- 柔軟な構造変更が可能です

## 概要・特徴

### 概要

Compositeパターンは、個々のオブジェクトと複合オブジェクトを同一のインターフェースで扱い、ツリー構造を表現する設計パターンです。このパターンでは、単一のオブジェクト（リーフ）と複数のオブジェクトからなるグループ（コンポジット）を同じ方法で操作できるようにします。これにより、複雑な階層構造を持つシステムでも、クライアントは対象が単一オブジェクトか複合オブジェクトかを区別せずに操作できるようになります。ファイルシステム、グラフィックシステム、メニュー構造など、階層的なデータ構造を扱う場面で特に有効です。

Compositeパターンは、GOF（Gang of Four）の構造に関するパターンの一つとして定義されました。このパターンは「全体-部分関係」（whole-part relationships）を表現するための一般的な手法として、オブジェクト指向設計において重要な位置を占めています。自然界や人工物に広く見られる「部分が集まって全体を構成する」という構造を、プログラムで表現するための効果的な手段です。

特にオブジェクト指向設計の基本原則の一つである「Open/Closed Principle（開放/閉鎖原則）」を実現する手段としても重要です。Compositeパターンを使用することで、新しい種類のコンポーネントを追加しても、既存のコードを変更する必要がなくなります。これは、システムの拡張性を高め、保守性を向上させる重要な特性です。

Compositeパターンが特に有効な状況：

1. **階層的な情報の表現**: 文書構造、組織図、メニュー構造など
2. **部分-全体の構造化**: 部品と組立品、地域と国、細胞と組織など
3. **再帰的な処理の実装**: 集計、検索、変換など階層全体に適用する操作
4. **統一的なクライアントインターフェース**: クライアントコードを単純化したい場合
5. **動的に変化する構造**: 実行時に構造が変更される可能性がある場合

Compositeパターンは、多くの現代的なUI設計、ゲームエンジン設計、ドキュメント処理システムなどで広く採用されています。React、Vue、Angularなどのコンポーネントベースのフロントエンドフレームワークや、XMLやHTMLなどの階層的なマークアップ言語の処理システムにも、この概念が応用されています。

### 特徴

#### 統一的なインターフェース

個々のオブジェクト（リーフ）と複合オブジェクト（コンポジット）が同じインターフェースを実装するため、クライアントはどちらを扱っているのかを意識する必要がありません。この共通インターフェースにより、処理の一貫性が保たれ、コードの複雑さが軽減されます。例えば、描画操作やコスト計算など、階層全体に適用される操作を統一的に定義できます。

統一的なインターフェースは、以下のような重要な利点をもたらします：

- **ポリモーフィズムの活用**: リーフとコンポジットが同じ型として扱えるため、ポリモーフィズムを効果的に活用できます
- **依存関係の軽減**: クライアントは具体的な実装クラスではなく、抽象インターフェースに依存します
- **拡張性の向上**: 新しい種類のコンポーネントを追加しても、クライアントコードの変更は不要です
- **コードの再利用**: 共通の振る舞いをインターフェースや抽象クラスで一度実装すれば、全てのコンポーネントで利用できます
- **テストの容易性**: モックやスタブを使用したテストが容易になります

例えば、グラフィカルユーザーインターフェースのライブラリでは、ボタン、テキストフィールド、パネルなど様々なコンポーネントが `Component` インターフェースを実装します。これにより、単一のコンポーネント（ボタンなど）もコンポーネントのグループ（パネルなど）も同じメソッド（`render()`, `resize()`, `handleEvent()` など）で操作できるようになります。

#### 階層構造の表現

ツリー構造を自然に表現できるため、複雑な階層関係を持つデータや構造を効果的にモデル化できます。ファイルシステム（ファイルとディレクトリ）、組織図（従業員と部署）、GUIコンポーネント（ボタンとパネル）など、現実世界の多くの階層的構造をそのままコードに落とし込むことができます。

階層構造を表現する際の具体的な利点：

- **直感的なモデリング**: 現実世界の階層構造をそのままコードで表現できます
- **複雑性の管理**: 複雑な構造を再帰的に分解して管理できます
- **深さの柔軟性**: 任意の深さの階層を統一的な方法で表現できます
- **構造の透明性**: 階層の各レベルで同じインターフェースを使用するため、構造が理解しやすくなります
- **拡張の容易さ**: 階層のどの部分でも新しい要素を追加することが容易です

階層構造の代表的な実装例：

1. **ファイルシステム**: ファイル（リーフ）とディレクトリ（コンポジット）が共通の「ファイルシステムエントリ」インターフェースを実装
2. **XMLドキュメント**: 要素ノード（コンポジット）とテキストノード（リーフ）を統一的に扱う
3. **組織構造**: 従業員（リーフ）と部署（コンポジット）を共通の「組織単位」として扱う
4. **メニューシステム**: メニュー項目（リーフ）とサブメニュー（コンポジット）を同一視
5. **グラフィックシステム**: 基本図形（リーフ）と図形グループ（コンポジット）を同じ「描画可能オブジェクト」として扱う

#### 再帰的な処理

ツリー構造全体に対する操作を再帰的に適用できます。例えば、深さ優先探索や幅優先探索などのアルゴリズムを使って、ツリー全体を簡潔に処理できます。これにより、複雑なデータ構造に対する操作を、短くて読みやすいコードで実装できます。特に、合計値の計算や階層的な検索などの処理に適しています。

再帰的な処理のパワーと応用：

- **集計操作の実装**: 階層内の全要素の合計、平均、最大値などを簡潔に計算できます（例：ファイルシステムの総容量計算）
- **検索アルゴリズムの統一**: 深さ優先探索や幅優先探索を用いた検索を容易に実装できます
- **ビジターパターンとの組み合わせ**: 階層構造に対して様々な操作を追加できます
- **変換操作の実装**: 階層全体に対する変換（例：XML→HTML、通貨換算など）を再帰的に適用できます
- **双方向操作の実現**: 上位から下位へのメッセージ伝播と下位から上位への結果集約を自然に実装できます

再帰的処理の具体例：

```typescript
// 代表的な再帰的処理の例：ファイルシステムの総サイズ計算
interface FileSystemElement {
  getName(): string
  getSize(): number
}

class File implements FileSystemElement {
  constructor(private name: string, private size: number) {}
  
  getName(): string {
    return this.name
  }
  
  getSize(): number {
    return this.size
  }
}

class Directory implements FileSystemElement {
  private children: FileSystemElement[] = []
  
  constructor(private name: string) {}
  
  getName(): string {
    return this.name
  }
  
  add(element: FileSystemElement): void {
    this.children.push(element)
  }
  
  // 再帰的な処理の例
  getSize(): number {
    // 全ての子要素のサイズを合計
    return this.children.reduce((sum, element) => sum + element.getSize(), 0)
  }
}
```

この例では、`getSize()` メソッドが再帰的に呼び出され、ディレクトリ内の全てのファイルとサブディレクトリのサイズを合計しています。クライアントコードは、対象が単一のファイルなのか、複雑な階層構造を持つディレクトリなのかを区別せずに、同じ `getSize()` メソッドを呼び出すだけで総サイズを取得できます。

#### 柔軟な構造変更

実行時に階層構造を動的に変更できるため、システムの柔軟性が向上します。新しい要素（リーフやコンポジット）の追加や削除が容易で、構造の再編成も簡単に行えます。これにより、変化する要件に対応しやすくなり、システムの拡張性が高まります。

動的な構造変更の利点と応用：

- **実行時の構造適応**: ユーザー操作や外部条件に応じて構造を動的に変更できます
- **段階的な構築**: 大規模な階層構造を段階的に構築できます
- **構造の最適化**: 使用パターンに基づいて構造を最適化できます
- **構造の保存と復元**: 構造全体をシリアライズ/デシリアライズして永続化できます
- **構造の変更履歴管理**: 変更をトラッキングし、undo/redo機能を実装できます

構造変更の具体的なシナリオ：

1. **グラフィカルエディタ**: ユーザーが図形を追加、削除、グループ化、グループ解除する操作
2. **動的メニューシステム**: ユーザー権限や状況に応じてメニュー項目が変化するシステム
3. **プラグイン管理**: プラグインの追加・削除によって機能階層が動的に変化するシステム
4. **カスタマイズ可能なダッシュボード**: ユーザーがウィジェットを追加、削除、配置変更できるインターフェース
5. **動的な文書構造**: ユーザーが編集する文書の構造（見出し、段落、リストなど）を表現

#### クライアントコードの単純化

Compositeパターンは、クライアントコードを大幅に単純化します。クライアントは個々のオブジェクト（リーフ）と複合オブジェクト（コンポジット）を区別する必要がなく、同じインターフェースを通じて統一的に扱うことができます。例えば、グラフィックエディタでは、単一の図形（円や四角形）とグループ化された図形に対して、同じ「描画」「移動」「サイズ変更」などの操作を一貫して適用できます。これにより、クライアントコードの条件分岐が減少し、「オブジェクトの種類に応じて異なる処理を行う」という複雑さが解消されます。また、新しい種類のコンポーネントが追加されても、クライアントコードを変更する必要がないため、システムの保守性が向上します。Webアプリケーションのコンポーネントシステムや、文書処理システムのドキュメント構造など、様々な階層的なシステムにおいて、この単純化の効果は大きな利点となります。さらに、テストの観点からも、クライアントコードのテストが簡素化され、より堅牢なシステムの構築につながります。

クライアントコード単純化の具体的なメリットと例：

- **条件分岐の削減**: 「オブジェクトのタイプに応じた処理」のための条件分岐が不要になります
```typescript
// Before Composite pattern:
if (object instanceof Leaf) {
  // Leaf用の処理
} else if (object instanceof Composite) {
  // Composite用の処理（子要素への処理委譲など）
}

// After Composite pattern:
object.operation() // リーフでもコンポジットでも同じメソッドを呼び出せる
```

- **コードの一貫性**: 同じインターフェースを通じて全てのオブジェクトを操作するため、コードが一貫します
- **拡張に対する堅牢性**: 新しい種類のコンポーネントが追加されても、クライアントコードは変更不要です
- **抽象化レベルの統一**: クライアントコードは抽象レベルで操作するため、実装詳細から保護されます
- **責任の明確な分離**: クライアントは「何をするか」に集中し、コンポーネントは「どのように行うか」を担当します

実際の適用例：

1. **UIフレームワーク**: 複合コンポーネント（パネル、フォーム）と単純コンポーネント（ボタン、ラベル）を統一的に扱う
2. **3Dグラフィックスエンジン**: 単一のメッシュとシーングラフ（複数のメッシュやライトなど）を同じように描画操作の対象にする
3. **コマンドライン処理**: 単一コマンドと複合コマンド（スクリプト）を統一的に実行できるようにする
4. **REST API処理**: 単一リソースと複合リソース（関連リソースを含む）の処理を統一する
5. **レポートエンジン**: 単純なレポート項目と複合レポートセクションを同じ方法で処理する

#### 型安全性との兼ね合い

Compositeパターンを実装する際の課題の一つは、型安全性とインターフェースの統一のバランスを取ることです。完全に統一されたインターフェースを提供するために、リーフノードには意味をなさない操作（例えば「子要素の追加」）も含める必要がある場合があります。例えば、ファイルシステムの実装では、ファイル（リーフ）に対する「子要素の追加」操作は本来意味を持ちませんが、インターフェースの統一のためにこうした操作が定義されることがあります。この問題に対処するアプローチとしては、①すべての操作をComponent基底クラスに定義し、リーフでは例外をスローする方法、②コンポジット専用の操作を別インターフェースに分離する方法、③型チェックと型キャストを使用する方法などがあります。例えば、Javaの`Swing`ライブラリでは、`Component`クラスと`Container`クラスを分離することで、この問題に対処しています。ただし、この分離はクライアントコードの複雑化を招く可能性があるため、アプリケーションの具体的な要件に応じて適切なバランスを見つける必要があります。また、最近の言語機能（ジェネリクスやオプショナル型など）を活用することで、この問題を軽減する手法も考案されています。

型安全性の課題に対する具体的なアプローチと比較：

1. **透過的アプローチ（Transparent Approach）**
   - 全てのメソッドを共通インターフェースに定義
   - リーフでは意味のない操作に対して例外をスロー
   - **利点**: 完全な統一インターフェース、クライアントコードの単純化
   - **欠点**: 型安全性の低下、実行時エラーの可能性

2. **安全なアプローチ（Safe Approach）**
   - 共通操作のみを基本インターフェースに定義
   - 子管理操作は別のインターフェースまたはコンポジットクラスに定義
   - **利点**: 型安全性の向上、コンパイル時のエラー検出
   - **欠点**: クライアントコードで型チェックや型キャストが必要になる可能性

3. **折衷アプローチ（Hybrid Approach）**
   - 基本操作は共通インターフェースに定義
   - 子管理操作も共通インターフェースに定義するが、デフォルト実装を提供
   - **利点**: インターフェースの統一と型安全性のバランス
   - **欠点**: インターフェース設計の複雑化

4. **ジェネリックアプローチ（Generic Approach）**
   - ジェネリクスを使用して型パラメータでコンポーネントの種類を表現
   - コンパイル時に型安全性を確保
   - **利点**: 高い型安全性、コンパイル時のエラーチェック
   - **欠点**: 型パラメータの複雑化、言語のサポートに依存

最新のプログラミングパラダイムでの対応：

- **オプショナル型の活用**: 子要素操作が返す結果を Optional<T> でラップして安全に扱う
- **代数的データ型の利用**: パターンマッチングを活用して型安全に異なる種類のコンポーネントを処理
- **関数型プログラミングアプローチ**: 不変データ構造と高階関数を使用した階層構造の処理
- **スマートコンパイラの活用**: 静的解析ツールを使って型安全性の問題を早期に検出

#### 実装のバリエーションと応用

Compositeパターンには、様々な実装バリエーションと応用方法があります：

**1. 親リファレンスの管理**
- 子から親への参照を維持するか否かの選択
- **双方向リンク**: 子から親へのナビゲーションが容易になるが、メモリ使用量と複雑性が増加
- **単方向リンク**: シンプルで効率的だが、親へのアクセスには追加の仕組みが必要

**2. 順序の管理**
- 子要素の順序を管理するか否かの選択
- **順序あり**: 子要素の順序が重要な場合（HTMLドキュメント、メニュー項目など）
- **順序なし**: 子要素の順序が重要でない場合（ファイルシステム、組織構造など）

**3. キャッシュと最適化**
- パフォーマンス向上のためのキャッシュ戦略
- 集計結果のキャッシュ（例：ファイルシステムでのサイズ計算）
- レンダリング結果のキャッシュ（例：GUIコンポーネントの描画）

**4. ビジターパターンとの組み合わせ**
- 階層構造に対して様々な操作を追加する方法
- 構造と操作を分離することで、新しい操作を容易に追加できる
- 例：XMLドキュメントに対して、検証、変換、シリアライズなど様々な操作を追加

**5. イテレータパターンとの組み合わせ**
- 階層構造を効率的に走査するための方法
- 深さ優先、幅優先、フィルタリングなど様々な走査戦略を提供
- 例：ファイルシステムの走査、UIコンポーネントのフォーカス移動

**6. オブザーバーパターンとの組み合わせ**
- 階層構造の変更通知を効率的に処理する方法
- 構造変更イベントの伝播と集約
- 例：GUIフレームワークでのイベント伝播、ドキュメント構造の変更通知

**7. プロトタイプパターンとの組み合わせ**
- 複雑な階層構造のコピーを効率的に作成する方法
- 深いコピーと浅いコピーの選択
- 例：クリップボード操作での複合オブジェクトのコピー

これらのバリエーションは、具体的なアプリケーションの要件や制約に応じて選択されます。適切なバリエーションを選ぶことで、Compositeパターンの利点を最大限に活かしつつ、パフォーマンスや保守性の課題に対処することができます。

### 概要図

```mermaid
classDiagram
    class Component {
        <<abstract>>
        +operation(): void
        +add(component: Component): void
        +remove(component: Component): void
        +getChild(index: int): Component
    }
    
    class Leaf {
        +operation(): void
    }
    
    class Composite {
        -children: List<Component>
        +operation(): void
        +add(component: Component): void
        +remove(component: Component): void
        +getChild(index: int): Component
    }
    
    class Client {
        +manipulate(component: Component): void
    }
    
    Component <|-- Leaf
    Component <|-- Composite
    Composite o-- Component : contains
    Client --> Component
    
    note for Component "• リーフとコンポジットの共通インターフェース\n• 共通操作の宣言\n• 子コンポーネント管理のメソッド定義"
    note for Leaf "• 個別オブジェクトの実装\n• 子を持たない単純な要素\n• 共通操作の具体的な実装\n• 追加/削除メソッドは空実装または例外"
    note for Composite "• 複合オブジェクトの実装\n• 子コンポーネントのコレクションを管理\n• 操作を子コンポーネントに委譲\n• 子の追加/削除/取得を実装"
    note for Client "• コンポーネントを操作するクライアント\n• 個別または複合かを意識せず扱う\n• 統一インターフェースを利用"
```

## 類似パターンとの比較

- [Decorator (デコレータ)](decorator.md): Composite は階層構造を扱い、これに対して Decorator は機能を動的に追加します。
- [Iterator (イテレータ)](iterator.md): Composite は階層構造を扱い、これに対して Iterator は要素へのアクセス方法を提供します。
- [Chain of Responsibility (責任連鎖)](chain-of-responsibility.md): Composite は階層構造を扱い、これに対して Chain of Responsibility は処理の連鎖を扱います。

## 利用されているライブラリ／フレームワークの事例

- [React](https://reactjs.org/): コンポーネントの構成
- [Java Swing](https://docs.oracle.com/javase/tutorial/uiswing/): UI要素の構成
- [DOM](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model): 文書構造の表現

## 解説ページリンク

- [Refactoring Guru - Composite](https://refactoring.guru/design-patterns/composite)
- [Microsoft - Composite Pattern](https://docs.microsoft.com/en-us/previous-versions/msp-n-p/ee658117(v=pandp.10))
- [SourceMaking - Composite](https://sourcemaking.com/design_patterns/composite)

## コード例

### Before:

階層構造を個別に処理する実装

```typescript
// ファイルクラス
class File {
  constructor(
    private name: string,
    private size: number
  ) {}

  getName(): string {
    return this.name;
  }

  getSize(): number {
    return this.size;
  }
}

// ディレクトリクラス
class Directory {
  private files: File[] = [];
  private subdirectories: Directory[] = [];

  constructor(private name: string) {}

  addFile(file: File): void {
    this.files.push(file);
  }

  addSubdirectory(directory: Directory): void {
    this.subdirectories.push(directory);
  }

  getName(): string {
    return this.name;
  }

  getFiles(): File[] {
    return this.files;
  }

  getSubdirectories(): Directory[] {
    return this.subdirectories;
  }
}

// 使用例
function example() {
  // ルートディレクトリの作成
  const root = new Directory("root");

  // ファイルの追加
  root.addFile(new File("file1.txt", 100));
  root.addFile(new File("file2.txt", 200));

  // サブディレクトリの作成と追加
  const subdir = new Directory("subdir");
  subdir.addFile(new File("file3.txt", 300));
  root.addSubdirectory(subdir);

  // ディレクトリ構造の表示（再帰的な処理が必要）
  function printDirectory(directory: Directory, indent: string = "") {
    console.log(`${indent}[${directory.getName()}]`);
    
    // ファイルの表示
    directory.getFiles().forEach(file => {
      console.log(`${indent}  ${file.getName()} (${file.getSize()}バイト)`);
    });
    
    // サブディレクトリの表示
    directory.getSubdirectories().forEach(subdir => {
      printDirectory(subdir, indent + "  ");
    });
  }

  // 合計サイズの計算（再帰的な処理が必要）
  function calculateTotalSize(directory: Directory): number {
    let totalSize = 0;
    
    // ファイルサイズの合計
    directory.getFiles().forEach(file => {
      totalSize += file.getSize();
    });
    
    // サブディレクトリのサイズを加算
    directory.getSubdirectories().forEach(subdir => {
      totalSize += calculateTotalSize(subdir);
    });
    
    return totalSize;
  }

  // 結果の表示
  console.log("=== ディレクトリ構造 ===");
  printDirectory(root);
  
  console.log("\n=== 合計サイズ ===");
  console.log(`${calculateTotalSize(root)}バイト`);
}

example();
```

### After:

Compositeパターンを関数型プログラミングスタイルで適用した実装

```typescript
// ファイルシステム要素の型定義
type FileSystemElementType = 'file' | 'directory'

// 共通プロパティの型定義
type FileSystemElementBase = Readonly<{
  name: string
  createdAt: Date
  modifiedAt: Date
  parent: string | null // 親ディレクトリのパス
}>

// ファイル型の定義
type FileElement = FileSystemElementBase & Readonly<{
  type: 'file'
  size: number
  content: string
}>

// ディレクトリ型の定義
type DirectoryElement = FileSystemElementBase & Readonly<{
  type: 'directory'
  children: ReadonlyArray<string> // 子要素のパス
}>

// ファイルシステム要素の共用型
type FileSystemElement = FileElement | DirectoryElement

// 全体のファイルシステムの状態を表す型
type FileSystem = Readonly<{
  // パスをキーとしたファイルシステム要素のマップ
  elements: Readonly<Record<string, FileSystemElement>>
}>

// 初期状態のファイルシステム
const createInitialFileSystem = (): FileSystem => ({
  elements: {}
})

// パスを構築するユーティリティ関数
const buildPath = (parent: string | null, name: string): string => 
  parent ? `${parent}/${name}` : name

// ファイルを作成する純粋関数
const createFile = (
  fs: FileSystem,
  parentPath: string | null,
  name: string,
  size: number,
  content: string = ''
): FileSystem => {
  const now = new Date()
  const path = buildPath(parentPath, name)
  
  // 新しいファイル要素を作成
  const file: FileElement = {
    type: 'file',
    name,
    size,
    content,
    createdAt: now,
    modifiedAt: now,
    parent: parentPath
  }
  
  // 親ディレクトリがある場合は親も更新する
  let updatedFs = {
    elements: {
      ...fs.elements,
      [path]: file
    }
  }
  
  if (parentPath && fs.elements[parentPath]) {
    const parent = fs.elements[parentPath] as DirectoryElement
    updatedFs = {
      elements: {
        ...updatedFs.elements,
        [parentPath]: {
          ...parent,
          modifiedAt: now,
          children: [...parent.children, path]
        }
      }
    }
  }
  
  return updatedFs
}

// ディレクトリを作成する純粋関数
const createDirectory = (
  fs: FileSystem,
  parentPath: string | null,
  name: string
): FileSystem => {
  const now = new Date()
  const path = buildPath(parentPath, name)
  
  // 新しいディレクトリ要素を作成
  const directory: DirectoryElement = {
    type: 'directory',
    name,
    createdAt: now,
    modifiedAt: now,
    parent: parentPath,
    children: []
  }
  
  // 親ディレクトリがある場合は親も更新する
  let updatedFs = {
    elements: {
      ...fs.elements,
      [path]: directory
    }
  }
  
  if (parentPath && fs.elements[parentPath]) {
    const parent = fs.elements[parentPath] as DirectoryElement
    updatedFs = {
      elements: {
        ...updatedFs.elements,
        [parentPath]: {
          ...parent,
          modifiedAt: now,
          children: [...parent.children, path]
        }
      }
    }
  }
  
  return updatedFs
}

// ファイルシステム要素のサイズを計算する純粋関数（再帰）
const calculateSize = (fs: FileSystem, path: string): number => {
  const element = fs.elements[path]
  if (!element) return 0
  
  if (element.type === 'file') {
    return element.size
  } else {
    // ディレクトリの場合は子要素のサイズを合計
    return element.children.reduce((total, childPath) => 
      total + calculateSize(fs, childPath), 0)
  }
}

// ファイルシステム要素を表示する関数
const printElement = (
  fs: FileSystem, 
  path: string, 
  indent: string = ''
): void => {
  const element = fs.elements[path]
  if (!element) return
  
  if (element.type === 'file') {
    console.log(
      `${indent}📄 ${element.name} ` +
      `(${element.size}バイト, ` +
      `更新: ${element.modifiedAt.toLocaleString()})`
    )
  } else {
    console.log(
      `${indent}📁 ${element.name} ` +
      `(${calculateSize(fs, path)}バイト, ` +
      `更新: ${element.modifiedAt.toLocaleString()})`
    )
    
    // 子要素を表示
    element.children.forEach(childPath => {
      printElement(fs, childPath, indent + '  ')
    })
  }
}

// 検索を行う純粋関数
const findElements = (
  fs: FileSystem,
  searchTerm: string,
  path: string | null = null
): ReadonlyArray<string> => {
  // パスが指定されていない場合はすべての要素から検索
  const elementsToSearch = path 
    ? [path] 
    : Object.keys(fs.elements)
  
  const results: string[] = []
  
  elementsToSearch.forEach(currentPath => {
    const element = fs.elements[currentPath]
    if (!element) return
    
    // 名前に検索語を含む場合は結果に追加
    if (element.name.includes(searchTerm)) {
      results.push(currentPath)
    }
    
    // ファイルの場合はコンテンツも検索
    if (element.type === 'file' && (element as FileElement).content.includes(searchTerm)) {
      if (!results.includes(currentPath)) {
        results.push(currentPath)
      }
    }
    
    // ディレクトリの場合は子要素も検索
    if (element.type === 'directory') {
      const childResults = (element as DirectoryElement).children.flatMap(childPath => 
        findElements(fs, searchTerm, childPath)
      )
      
      results.push(...childResults)
    }
  })
  
  return [...new Set(results)] // 重複を削除
}

// ファイルシステム統計を計算する純粋関数
const calculateStatistics = (
  fs: FileSystem,
  rootPath: string
): Readonly<{
  fileCount: number
  directoryCount: number
  totalSize: number
  largestFile: string | null
  deepestDirectory: string | null
  deepestLevel: number
}> => {
  let fileCount = 0
  let directoryCount = 0
  let totalSize = 0
  let largestFile: string | null = null
  let largestFileSize = 0
  let deepestDirectory: string | null = null
  let deepestLevel = 0
  
  // ファイルシステムをトラバースする再帰関数
  const traverse = (path: string): void => {
    const element = fs.elements[path]
    if (!element) return
    
    const level = path.split('/').length
    
    if (element.type === 'file') {
      fileCount++
      totalSize += element.size
      
      if (element.size > largestFileSize) {
        largestFileSize = element.size
        largestFile = path
      }
    } else {
      directoryCount++
      
      if (level > deepestLevel) {
        deepestLevel = level
        deepestDirectory = path
      }
      
      // 子要素を処理
      (element as DirectoryElement).children.forEach(traverse)
    }
  }
  
  // 処理開始
  traverse(rootPath)
  
  return {
    fileCount,
    directoryCount,
    totalSize,
    largestFile,
    deepestDirectory,
    deepestLevel
  }
}

// 使用例
const example = (): void => {
  // ファイルシステムの構築
  let fs = createInitialFileSystem()
  
  // ルートディレクトリの作成
  fs = createDirectory(fs, null, 'root')
  
  // documents ディレクトリ
  fs = createDirectory(fs, 'root', 'documents')
  fs = createFile(fs, 'root/documents', 'resume.txt', 1024, '職務経歴書の内容')
  fs = createFile(fs, 'root/documents', 'memo.txt', 512, 'メモの内容')
  
  // images ディレクトリ
  fs = createDirectory(fs, 'root', 'images')
  fs = createFile(fs, 'root/images', 'photo1.jpg', 2048)
  fs = createFile(fs, 'root/images', 'photo2.jpg', 3072)
  
  // projects ディレクトリ
  fs = createDirectory(fs, 'root', 'projects')
  fs = createDirectory(fs, 'root/projects', 'projectA')
  fs = createFile(fs, 'root/projects/projectA', 'readme.md', 256, 'プロジェクトAの説明')
  fs = createFile(fs, 'root/projects/projectA', 'main.ts', 1024, 'ソースコード')
  
  // ディレクトリ構造の表示
  console.log('=== ディレクトリ構造 ===')
  printElement(fs, 'root')
  
  // 統計情報の収集
  console.log('\n=== ファイルシステムの統計 ===')
  const stats = calculateStatistics(fs, 'root')
  
  console.log(`ファイル数: ${stats.fileCount}`)
  console.log(`ディレクトリ数: ${stats.directoryCount}`)
  console.log(`合計サイズ: ${stats.totalSize}バイト`)
  
  if (stats.largestFile) {
    const largestFile = fs.elements[stats.largestFile] as FileElement
    console.log(
      `最大のファイル: ${largestFile.name} ` +
      `(${largestFile.size}バイト)`
    )
  }
  
  if (stats.deepestDirectory) {
    const deepestDir = fs.elements[stats.deepestDirectory] as DirectoryElement
    console.log(
      `最も深いディレクトリ: ${stats.deepestDirectory} ` +
      `(レベル ${stats.deepestLevel})`
    )
  }
  
  // ファイルの検索
  console.log('\n=== ファイルの検索 ===')
  const searchResults = findElements(fs, 'project')
  
  console.log('検索結果 ("project" を含む要素):')
  searchResults.forEach(path => {
    const element = fs.elements[path]
    console.log(
      `- [${element.type}] ${path} ` +
      `(${element.type === 'file' ? 
        (element as FileElement).size : 
        calculateSize(fs, path)}バイト)`
    )
  })
  
  // 名前による要素の検索
  console.log('\n=== 名前による検索 ===')
  const nameResults = findElements(fs, 'main')
  nameResults.forEach(path => {
    const element = fs.elements[path]
    console.log(
      `${path} ` +
      `(${element.type}, ${element.type === 'file' ? 
        (element as FileElement).size : 
        calculateSize(fs, path)}バイト)`
    )
  })
}

// 実行
example()
```

