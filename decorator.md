# Decorator（デコレータ）パターン

## 目的

オブジェクトに動的に新しい責務を追加することを可能にするパターンです。

## 価値・解決する問題

- 機能を動的に追加できます
- 単一責任の原則を守れます
- 継承の代替手段を提供します
- 柔軟な機能拡張が可能です
- 既存のコードを変更せずに機能を追加できます

## 概要・特徴

### 概要

Decoratorパターンは、オブジェクトに新しい振る舞いを動的に追加することを可能にし、サブクラス化の代替となる柔軟な設計を提供する設計パターンです。このパターンでは、元のオブジェクトを修正せずに、それをラップするデコレータオブジェクトを使って機能を拡張します。デコレータは元のオブジェクトと同じインターフェースを実装し、受け取ったリクエストを元のオブジェクトに転送した後、追加の振る舞いを実行します。複数のデコレータを重ねることで、柔軟に機能を組み合わせることができます。これにより、クラス爆発を避けつつ、実行時に機能を追加したり取り除いたりすることが可能になります。

Decoratorパターンは、GOF（Gang of Four）の構造パターンの一つとして定義されました。このパターンは、「静的な継承」ではなく「動的な委譲」を通じて機能拡張を実現する手法として確立されています。パターンの名前が示すように、既存のオブジェクトを「装飾」することで、元の構造や目的を変えることなく新しい機能を追加できます。この概念は、オブジェクト指向設計における「合成（Composition）優先、継承は限定的に」という原則と密接に関連しています。

Decoratorパターンは、特に次のような状況で効果を発揮します：

1. **動的な機能追加が必要な場合**: 実行時に機能を追加または削除する必要があるシステム
2. **機能の組み合わせが多様な場合**: 継承で全ての組み合わせを表現すると膨大なクラス数になる場合
3. **既存コードに影響を与えずに拡張したい場合**: 特に変更が難しいコアクラスや外部ライブラリのクラスを拡張する場合
4. **横断的関心事を分離したい場合**: ログ記録、トランザクション管理、キャッシュなど
5. **単一責任の原則を維持したい場合**: 一つのクラスに多くの責務を持たせたくない場合

このパターンは、多くの現代的なフレームワークやライブラリで採用されています。例えば、Javaのストリーム処理、TypeScriptとPythonのデコレータ機能、ReactのHigh Order Components（HOC）など、様々な形で応用されています。機能拡張の柔軟性と既存コードの保護という二つの重要な原則を両立させるための強力な手段として広く認識されています。

### 特徴

#### 動的な機能追加

実行時にオブジェクトに新しい振る舞いや責務を動的に追加できます。これにより、静的な継承では難しい柔軟な機能拡張が可能になります。例えば、テキスト処理システムでは、基本的なテキストコンポーネントに対して、書式設定、暗号化、圧縮などの機能をユーザーの必要に応じて動的に追加できます。このような動的な性質により、アプリケーションの実行中でも機能構成を変更できる柔軟性が生まれます。

動的な機能追加の具体的なメリットと応用方法：

- **実行時の柔軟性**: 設定やユーザー操作に基づいて機能セットを変更できます
```typescript
// ユーザー設定に基づいて動的にデコレータを適用する例
const createUserConfiguredDocument = (userId: string, document: Document): Document => {
  let configuredDoc = document
  const userSettings = getUserSettings(userId)
  
  if (userSettings.enableEncryption) {
    configuredDoc = withEncryption(configuredDoc, userSettings.encryptionKey)
  }
  
  if (userSettings.enableCompression) {
    configuredDoc = withCompression(configuredDoc, userSettings.compressionLevel)
  }
  
  if (userSettings.enableVersioning) {
    configuredDoc = withVersioning(configuredDoc)
  }
  
  return configuredDoc
}
```

- **プラグインアーキテクチャ**: サードパーティによる機能拡張が容易になります
- **A/Bテスト**: 異なる機能セットをテストするために、特定のデコレータを一部のユーザーにのみ適用できます
- **ライセンスベースの機能制限**: ライセンスレベルに応じて利用可能な機能を制御できます
- **環境依存の振る舞い変更**: 開発、テスト、本番環境など、実行環境に応じて異なるデコレータを適用できます

機能追加が継承ではなく委譲（デコレータ）で行われるため、クラスの爆発的増加を防ぎながら、多様な機能組み合わせを実現できます。例えば、3つの独立した機能拡張があった場合、継承で全ての組み合わせを表現するには2³=8個のサブクラスが必要ですが、デコレータパターンでは3つのデコレータクラスだけで全ての組み合わせをカバーできます。

#### 単一責任の原則

各デコレータクラスは特定の機能追加に集中するため、単一責任の原則に従った設計が可能になります。例えば、ログ記録、キャッシュ、権限チェックなど、それぞれの横断的関心事を別々のデコレータとして実装できます。これにより、コードの保守性が向上し、特定の機能だけを修正する場合も影響範囲が限定されます。また、機能ごとに分離されたコードはテストも容易になります。

単一責任の原則の適用による具体的な利点：

- **コードの分離**: 各機能が独立したクラスに分離されるため、1つの機能を変更しても他に影響しません
```typescript
// 単一責任の原則を適用したデコレータ例
// 認証デコレータ - 認証のみに責任を持つ
const withAuthentication = (service: UserService): UserService => ({
  ...service,
  getUser: async (userId: string) => {
    // 認証関連のロジックのみを処理
    if (!isAuthenticated()) {
      throw new Error('認証が必要です')
    }
    return service.getUser(userId)
  }
})

// ロギングデコレータ - ログ記録のみに責任を持つ
const withLogging = (service: UserService): UserService => ({
  ...service,
  getUser: async (userId: string) => {
    console.log(`ユーザー情報取得: ${userId}`)
    try {
      const result = await service.getUser(userId)
      console.log(`ユーザー情報取得完了: ${userId}`)
      return result
    } catch (error) {
      console.error(`ユーザー情報取得エラー: ${userId}`, error)
      throw error
    }
  }
})

// キャッシュデコレータ - キャッシュのみに責任を持つ
const withCaching = (service: UserService): UserService => {
  const cache = new Map<string, User>()
  
  return {
    ...service,
    getUser: async (userId: string) => {
      if (cache.has(userId)) {
        return cache.get(userId)!
      }
      
      const user = await service.getUser(userId)
      cache.set(userId, user)
      return user
    }
  }
}
```

- **テスト容易性**: 各デコレータを個別にテストできます
- **変更の局所化**: 特定の機能に関する変更が他の機能に影響を与えません
- **機能の再利用**: 独立したデコレータは他のコンポーネントでも再利用できます
- **関心事の分離**: クロスカッティングコンサーン（横断的関心事）を明確に分離できます

この原則は特にメンテナンス性の高いシステムを設計する際に重要です。例えば、エンタープライズアプリケーションでは、トランザクション管理、セキュリティ、ロギング、パフォーマンスモニタリングなど、多くの横断的関心事が存在します。これらをデコレータパターンで分離することで、コアビジネスロジックを単純に保ちながら、これらの関心事を適切に管理できます。

#### 柔軟な拡張性

デコレータを組み合わせることで、様々な機能の組み合わせを実現できます。これにより、事前に全ての組み合わせを想定したクラスを用意する必要がなく、クラス爆発を防ぐことができます。例えば、データストリームに対して圧縮、暗号化、バッファリングなどの機能を任意の順序で組み合わせることができます。この柔軟性により、将来の要件変更にも対応しやすいシステム設計が可能になります。

柔軟な拡張性の具体的な利点と実装方法：

- **選択的な機能適用**: 必要な機能だけを選択して適用できます
- **機能適用順序の制御**: デコレータの適用順序を変更することで、同じデコレータセットでも異なる振る舞いを実現できます
```typescript
// デコレータの適用順序による異なる結果の例
// 圧縮してから暗号化する場合
const compressedAndEncrypted = withEncryption(withCompression(data))

// 暗号化してから圧縮する場合（効率が低下する可能性がある）
const encryptedAndCompressed = withCompression(withEncryption(data))
```

- **新機能の段階的導入**: 新しいデコレータを1つずつ追加して機能を段階的に拡張できます
- **条件付き拡張**: 特定の条件下でのみ適用されるデコレータを実装できます
- **層別の拡張**: アプリケーションの異なる層（データアクセス、ビジネスロジック、プレゼンテーション）で異なるデコレータを適用できます

拡張性向上のための実装パターン：

1. **クラスベースのデコレータチェーン**: 従来のGOFパターン実装
2. **高階関数ベースのデコレータ**: 関数型プログラミングアプローチ
3. **言語レベルのデコレータ**: TypeScript、Python、Javaなどのデコレータ構文
4. **ミックスインベースの拡張**: 複数の振る舞いを混ぜ合わせる手法
5. **アスペクト指向プログラミング**: 横断的関心事の分離に特化した拡張

これらの手法を適切に組み合わせることで、メンテナンス性と拡張性に優れたシステムを構築できます。特に、将来の要件変更が予想されるシステムでは、デコレータパターンによる柔軟な拡張性が大きな価値を持ちます。

#### 既存コードの保護

オリジナルのオブジェクトを修正せずに機能を拡張できるため、既存コードを保護し、オープン・クローズドの原則に従った設計を実現できます。これは特に、変更できないライブラリの機能拡張や、チーム間で共有されるコアコンポーネントの拡張に役立ちます。元のコードに影響を与えずに新機能を追加できるため、リグレッションのリスクが減少し、安全な拡張が可能になります。

既存コード保護の具体的なメリットと応用例：

- **ライブラリの安全な拡張**: 外部ライブラリのクラスを修正せずに機能拡張できます
```typescript
// サードパーティライブラリの拡張例
import { ThirdPartyDataSource } from 'external-library'

// 元のライブラリコードを変更せずに機能を追加
const withRetry = <T extends ThirdPartyDataSource>(
  dataSource: T,
  maxRetries: number = 3,
  delay: number = 1000
): T => {
  const originalFetch = dataSource.fetchData.bind(dataSource)
  
  // プロトタイプを保持しながら拡張
  return Object.assign(Object.create(Object.getPrototypeOf(dataSource)), {
    ...dataSource,
    fetchData: async (...args: Parameters<typeof dataSource.fetchData>) => {
      let lastError: Error | null = null
      
      for (let attempt = 1; attempt <= maxRetries; attempt++) {
        try {
          return await originalFetch(...args)
        } catch (error) {
          lastError = error as Error
          console.log(`Retry ${attempt}/${maxRetries} after error: ${error.message}`)
          await new Promise(resolve => setTimeout(resolve, delay))
        }
      }
      
      throw lastError || new Error('リトライ失敗')
    }
  })
}

// 使用例
const enhancedDataSource = withRetry(new ThirdPartyDataSource())
```

- **レガシーコードの段階的リファクタリング**: レガシーコードをデコレータでラップして徐々に改善できます
- **バージョン間の互換性確保**: 既存APIを保持しながら内部実装を拡張できます
- **テスト可能性の向上**: 本番コードを変更せずにテスト用のラッパーを追加できます
- **APIバージョニングの管理**: 古いAPIをデコレータで拡張して新しい機能を追加できます

これはオープン・クローズド原則（OCP: Open-Closed Principle）を実践する具体的な方法でもあります。OCPは「ソフトウェアエンティティ（クラス、モジュール、関数など）は拡張に対して開いていて、修正に対して閉じているべき」という原則です。デコレータパターンはこの原則を実現するための効果的な手段として機能します。

#### 機能の組み合わせ

デコレータパターンでは、複数のデコレータを重ねることで、機能を自由に組み合わせることができます。これにより、特定のユースケースや要件に合わせたカスタマイズが容易になります。例えば、Webアプリケーションのリクエスト処理において、認証チェック、ロギング、キャッシュ、レート制限などの機能を独立したデコレータとして実装し、それらを必要に応じて組み合わせることができます。APIエンドポイントによって異なるセキュリティ要件がある場合、一部のエンドポイントには認証デコレータを適用し、他のエンドポイントにはそれを省略することが可能です。また、高負荷が予想されるエンドポイントには、キャッシュデコレータとレート制限デコレータを組み合わせて適用することができます。この柔軟な組み合わせ能力により、アプリケーション全体で一貫したコンポーネントを維持しながらも、各部分に最適な機能セットを提供することができます。

機能組み合わせの効果的な実装戦略：

- **デコレータの合成関数**: 複数のデコレータを合成する関数を提供すると使いやすくなります
```typescript
// デコレータの合成を容易にするユーティリティ関数
const composeDecorators = <T>(...decorators: Array<(obj: T) => T>) => (obj: T): T =>
  decorators.reduceRight((decorated, decorator) => decorator(decorated), obj)

// 使用例
const enhancedService = composeDecorators(
  withAuthentication,
  withLogging,
  withCaching,
  withRetry
)(baseService)
```

- **デコレータファクトリー**: パラメータ化されたデコレータを作成するファクトリー関数を実装する
- **デコレータレジストリ**: 名前付きのデコレータを登録し、文字列ベースで適用できるようにする
- **宣言的な構成**: 設定ファイルや注釈（アノテーション）を通じてデコレータを適用する
- **動的デコレータチェーン**: 実行時の条件に基づいてデコレータチェーンを動的に構築する

デコレータの組み合わせパターンは様々な領域で応用されています：

1. **Webフレームワーク**: ルートハンドラに対する認証、認可、バリデーション、キャッシュなどの適用
2. **データベースアクセス**: トランザクション管理、接続プール、クエリキャッシュ、監査ログなどの追加
3. **UIコンポーネント**: テーマ適用、イベント処理、アニメーション、アクセシビリティ機能の追加
4. **メッセージング**: 圧縮、暗号化、シリアライゼーション、再試行ロジックの追加
5. **テスト**: モック、スタブ、パフォーマンス計測、カバレッジ分析の追加

これらのアプローチにより、単一の責務を持つデコレータの組み合わせから、複雑な振る舞いを構築することが可能になります。これは「小さなパーツから複雑なシステムを構築する」という設計原則に合致しています。

#### 透過的な使用

デコレータは装飾対象と同じインターフェースを実装するため、クライアントコードは装飾されたオブジェクトを元のオブジェクトと同じように扱うことができます。これにより、クライアントコードを変更することなく、システムの振る舞いを拡張できます。例えば、データソースコンポーネントに対して、パフォーマンス測定デコレータを追加しても、そのデータソースを使用するクライアントコードは変更する必要がありません。この透過性は、レガシーシステムの拡張や、複雑なシステムの段階的な改善において特に価値があります。また、テスト環境では実際のサービスをモックデコレータでラップすることで、本番環境のコードを変更せずにテスト可能なシステムを構築できます。この特性により、デコレータパターンは依存性注入フレームワークやAOPツールとの相性が良く、横断的関心事の管理に効果的なアプローチを提供します。

透過的な使用がもたらす具体的なメリット：

- **クライアントコードの安定性**: インターフェースが同じなのでクライアントは変更不要です
- **交換可能性**: 実行時に異なるデコレータに交換しても、クライアントコードには影響しません
- **段階的な拡張**: システムを稼働させながら徐々に機能を追加できます
- **責任の分離**: クライアントはビジネスロジックに集中し、横断的関心事はデコレータが処理します
- **テスト用モック**: 本番コードを変更せずにテスト用のモックデコレータを使用できます

透過的な使用の注意点：

1. **インターフェースの肥大化**: 全てのデコレータが同じインターフェースを共有するため、インターフェースが大きくなりがち
2. **パフォーマンスオーバーヘッド**: 多層のデコレータは追加の委譲コストを発生させる
3. **デバッグの難しさ**: 多層のデコレータチェーンは追跡が難しくなる場合がある
4. **実装の漏れ**: 全メソッドを正しく委譲する必要があり、一部を忘れると問題が発生する

これらの課題は、適切な設計と実装プラクティスによって軽減できます。例えば、インターフェースを適切に分割したり、デコレータの構築を自動化したり、透明性を保ちながらもデバッグ情報を提供するメカニズムを導入したりすることが考えられます。

#### 実装のバリエーションと応用

Decoratorパターンには様々な実装バリエーションがあり、言語の特性や用途に応じて異なるアプローチが取られます：

**1. オブジェクト指向アプローチ**
- **クラスベースのデコレータ**: 伝統的なGOFパターン実装
- **インターフェースベースのデコレータ**: 明示的なインターフェースを使用
- **抽象クラスベースのデコレータ**: 共通機能を抽象クラスで提供
- **ミックスインベースのデコレータ**: 複数の振る舞いを混合

**2. 関数型アプローチ**
- **高階関数デコレータ**: 関数を受け取り拡張された関数を返す
- **合成関数パターン**: 複数のデコレータ関数を合成する
- **部分適用とカリー化**: デコレータの柔軟な構成を可能にする
- **パイプラインパターン**: デコレータの適用を明示的なパイプラインとして表現

**3. 言語特有の機能を活用したアプローチ**
- **アノテーションベースのデコレータ**: Java、Python、TypeScriptなどの言語機能
- **プロキシオブジェクト**: JavaScript Proxyなどを使用
- **メタプログラミング**: ランタイムでの動的な振る舞いの変更
- **AOP（アスペクト指向プログラミング）**: 横断的関心事の分離に特化

**4. 特殊なユースケースのアプローチ**
- **レイヤー化されたデコレータ**: アプリケーションの異なる層で異なるデコレータを適用
- **適応型デコレータ**: 実行時の状況に応じて動的に振る舞いを変更
- **キャッシュを考慮したデコレータ**: パフォーマンスのためにキャッシュ戦略を組み込む
- **並列処理用デコレータ**: 非同期操作や並列処理に特化

#### 性能への影響と最適化

Decoratorパターンは柔軟性と拡張性を提供する一方で、いくつかの性能への影響も考慮する必要があります：

**1. 委譲のオーバーヘッド**
- 多層のデコレータチェーンは追加の関数呼び出しや参照解決を発生させます
- 処理の大部分が単純な委譲である場合、相対的なオーバーヘッドが大きくなる可能性があります

**2. メモリ使用量**
- 各デコレータインスタンスは追加のメモリを消費します
- 大量のオブジェクトに対して個別にデコレータを適用すると、メモリ使用量が増加します

**3. 最適化戦略**
- **レイジーデコレーション**: 必要になった時点でデコレータを適用する
- **デコレータのプーリング**: 同じ設定のデコレータを再利用する
- **デコレータの結合**: 複数の単純なデコレータを1つの最適化されたデコレータに統合する
- **キャッシュ戦略**: 頻繁に使用される操作の結果をキャッシュする
- **軽量デコレータ**: 最小限のメモリフットプリントを持つデコレータを設計する

**4. 注意点と対策**
- 性能クリティカルなコードパスでは、デコレータの層数を最小限に抑える
- 大量のデータを処理する場合は、バッチ処理やストリーミング処理と組み合わせる
- プロファイリングを行い、ボトルネックとなっているデコレータを特定して最適化する

適切に設計・実装されたデコレータパターンは、柔軟性と性能のバランスを取ることができます。多くの場合、デコレータによるわずかな性能オーバーヘッドは、得られる設計上の利点によって相殺されます。

### 概要図

```mermaid
classDiagram
    class Component {
        <<interface>>
        +operation()
    }
    note for Component "• コンポーネントの基本インターフェース\n• すべてのオブジェクトに共通の操作を定義"

    class ConcreteComponent {
        +operation()
    }
    note for ConcreteComponent "• 基本的な機能を実装したコンポーネント\n• デコレータで拡張される対象"

    class Decorator {
        <<abstract>>
        -component: Component
        +operation()
    }
    note for Decorator "• コンポーネントを参照として保持\n• コンポーネントと同じインターフェースを実装\n• 操作の前後で追加処理を実行可能"

    class ConcreteDecoratorA {
        -addedState
        +operation()
        +addedBehavior()
    }
    note for ConcreteDecoratorA "• 特定の機能Aを追加するデコレータ\n• 基本コンポーネントに新しい状態や振る舞いを追加"

    class ConcreteDecoratorB {
        +operation()
        +addedBehavior()
    }
    note for ConcreteDecoratorB "• 特定の機能Bを追加するデコレータ\n• 複数のデコレータを組み合わせることが可能"

    Component <|.. ConcreteComponent
    Component <|.. Decorator
    Decorator o-- Component
    Decorator <|-- ConcreteDecoratorA
    Decorator <|-- ConcreteDecoratorB
    
    %% スタイル定義
    style Component fill:#1a237e,stroke:#7986cb,stroke-width:2px,color:#ffffff
    style ConcreteComponent fill:#1b5e20,stroke:#81c784,stroke-width:2px,color:#ffffff
    style Decorator fill:#1a237e,stroke:#7986cb,stroke-width:2px,color:#ffffff
    style ConcreteDecoratorA fill:#b71c1c,stroke:#ef5350,stroke-width:2px,color:#ffffff
    style ConcreteDecoratorB fill:#b71c1c,stroke:#ef5350,stroke-width:2px,color:#ffffff
```

## 類似パターンとの比較

- [Composite (コンポジット)](composite.md): Decorator は機能を動的に追加し、これに対して Composite は階層構造を扱います。
- [Strategy (ストラテジー)](strategy.md): Decorator は機能を動的に追加し、これに対して Strategy はアルゴリズムの切り替えを提供します。
- [Chain of Responsibility (責任連鎖)](chain-of-responsibility.md): Decorator は機能を動的に追加し、これに対して Chain of Responsibility は処理の連鎖を扱います。

## 利用されているライブラリ／フレームワークの事例

- [Java I/O Streams](https://docs.oracle.com/javase/8/docs/api/java/io/package-summary.html): 入出力処理
- [TypeScript Decorators](https://www.typescriptlang.org/docs/handbook/decorators.html): クラス拡張
- [Python Decorators](https://docs.python.org/3/glossary.html#term-decorator): 関数拡張

## 解説ページリンク

- [Refactoring Guru - Decorator](https://refactoring.guru/design-patterns/decorator)
- [Microsoft - Decorator Pattern](https://docs.microsoft.com/en-us/previous-versions/msp-n-p/ee658117(v=pandp.10))
- [SourceMaking - Decorator](https://sourcemaking.com/design_patterns/decorator)

## コード例

### Before:

継承を使用した機能追加の実装

```typescript
// 基本的なコーヒークラス
class Coffee {
  getDescription(): string {
    return "コーヒー";
  }

  getCost(): number {
    return 300;
  }
}

// ミルク入りコーヒー
class CoffeeWithMilk extends Coffee {
  getDescription(): string {
    return "ミルク入りコーヒー";
  }

  getCost(): number {
    return super.getCost() + 50;
  }
}

// シロップ入りコーヒー
class CoffeeWithSyrup extends Coffee {
  getDescription(): string {
    return "シロップ入りコーヒー";
  }

  getCost(): number {
    return super.getCost() + 100;
  }
}

// ミルクとシロップ入りコーヒー
class CoffeeWithMilkAndSyrup extends CoffeeWithMilk {
  getDescription(): string {
    return "ミルクとシロップ入りコーヒー";
  }

  getCost(): number {
    return super.getCost() + 100;
  }
}

// 使用例
function example() {
  const coffee = new Coffee();
  console.log(`${coffee.getDescription()}: ${coffee.getCost()}円`);

  const coffeeWithMilk = new CoffeeWithMilk();
  console.log(`${coffeeWithMilk.getDescription()}: ${coffeeWithMilk.getCost()}円`);

  const coffeeWithSyrup = new CoffeeWithSyrup();
  console.log(`${coffeeWithSyrup.getDescription()}: ${coffeeWithSyrup.getCost()}円`);

  const coffeeWithMilkAndSyrup = new CoffeeWithMilkAndSyrup();
  console.log(`${coffeeWithMilkAndSyrup.getDescription()}: ${coffeeWithMilkAndSyrup.getCost()}円`);
}

example();
```

### After:

Decoratorパターンを関数型プログラミングスタイルで適用した実装

```typescript
// 飲み物の型定義
type Beverage = Readonly<{
  description: string
  cost: number
  calories: number
  allergens: ReadonlyArray<string>
  preparationTime: number
}>

// 基本的なコーヒーを作成する関数
const createSimpleCoffee = (): Beverage => ({
  description: 'コーヒー',
  cost: 300,
  calories: 0,
  allergens: [],
  preparationTime: 180 // 3分
})

// デコレータ関数の型 - 既存の飲み物を受け取り新しい飲み物を返す
type BeverageDecorator = (beverage: Beverage) => Beverage

// ミルクデコレータ関数
const withMilk: BeverageDecorator = beverage => ({
  ...beverage,
  description: `${beverage.description}、ミルク追加`,
  cost: beverage.cost + 50,
  calories: beverage.calories + 50,
  allergens: [...beverage.allergens, '乳'],
  preparationTime: beverage.preparationTime + 30
})

// シロップデコレータを作成する関数
const withSyrup = (flavor: string): BeverageDecorator => beverage => ({
  ...beverage,
  description: `${beverage.description}、${flavor}シロップ追加`,
  cost: beverage.cost + 100,
  calories: beverage.calories + 100,
  preparationTime: beverage.preparationTime + 20
})

// ホイップクリームデコレータ関数
const withWhippedCream: BeverageDecorator = beverage => ({
  ...beverage,
  description: `${beverage.description}、ホイップクリーム追加`,
  cost: beverage.cost + 100,
  calories: beverage.calories + 120,
  allergens: [...beverage.allergens, '乳'],
  preparationTime: beverage.preparationTime + 60
})

// キャラメルソースデコレータ関数
const withCaramelSauce: BeverageDecorator = beverage => ({
  ...beverage,
  description: `${beverage.description}、キャラメルソース追加`,
  cost: beverage.cost + 80,
  calories: beverage.calories + 90,
  preparationTime: beverage.preparationTime + 25
})

// エクストラショットデコレータ関数
const withExtraShot: BeverageDecorator = beverage => ({
  ...beverage,
  description: `${beverage.description}、エクストラショット追加`,
  cost: beverage.cost + 150,
  calories: beverage.calories + 5,
  preparationTime: beverage.preparationTime + 90
})

// アイスデコレータ関数
const withIce: BeverageDecorator = beverage => ({
  ...beverage,
  description: `アイス${beverage.description}`,
  // 追加料金なし
  preparationTime: beverage.preparationTime + 15
})

// サイズデコレータを作成する関数
const withSize = (size: 'Small' | 'Regular' | 'Large'): BeverageDecorator => beverage => {
  const sizeFactors = {
    Small: { costAdjust: -50, caloriesFactor: 0.8, timeFactor: 1 },
    Regular: { costAdjust: 0, caloriesFactor: 1, timeFactor: 1 },
    Large: { costAdjust: 100, caloriesFactor: 1.3, timeFactor: 1.2 }
  }
  
  const { costAdjust, caloriesFactor, timeFactor } = sizeFactors[size]
  
  return {
    ...beverage,
    description: `${size} ${beverage.description}`,
    cost: beverage.cost + costAdjust,
    calories: Math.floor(beverage.calories * caloriesFactor),
    preparationTime: Math.floor(beverage.preparationTime * timeFactor)
  }
}

// 複数のデコレータを適用するユーティリティ関数
const pipe = <T>(...fns: Array<(arg: T) => T>) => (value: T): T =>
  fns.reduce((acc, fn) => fn(acc), value)

// 飲み物の情報を表示するユーティリティ関数
const displayBeverageInfo = (beverage: Beverage): void => {
  console.log('=== 飲み物の情報 ===')
  console.log(`商品名: ${beverage.description}`)
  console.log(`価格: ${beverage.cost}円`)
  console.log(`カロリー: ${beverage.calories}kcal`)
  
  if (beverage.allergens.length > 0) {
    console.log(`アレルギー物質: ${beverage.allergens.join(', ')}`)
  }
  
  const minutes = Math.floor(beverage.preparationTime / 60)
  const seconds = beverage.preparationTime % 60
  console.log(`準備時間: ${minutes}分${seconds}秒`)
  console.log()
}

// 使用例
const example = (): void => {
  // 基本的なコーヒー
  console.log('1. 基本的なコーヒー')
  const coffee = createSimpleCoffee()
  displayBeverageInfo(coffee)

  // カフェラテ（コーヒー + ミルク）
  console.log('2. カフェラテ')
  const latte = withMilk(createSimpleCoffee())
  displayBeverageInfo(latte)

  // キャラメルマキアート
  // （コーヒー + ミルク + キャラメルソース + ホイップクリーム）
  console.log('3. キャラメルマキアート')
  const caramelMacchiato = pipe(
    withMilk,
    withCaramelSauce,
    withWhippedCream
  )(createSimpleCoffee())
  displayBeverageInfo(caramelMacchiato)

  // アイスバニララテ
  // （コーヒー + ミルク + バニラシロップ + 氷）
  console.log('4. アイスバニララテ')
  const icedVanillaLatte = pipe(
    withMilk,
    withSyrup('バニラ'),
    withIce
  )(createSimpleCoffee())
  displayBeverageInfo(icedVanillaLatte)

  // ダブルショットキャラメルフラペチーノ
  // （コーヒー + ミルク + キャラメルソース + ホイップクリーム + エクストラショット + 氷）
  console.log('5. ダブルショットキャラメルフラペチーノ')
  const doubleCaramelFrappuccino = pipe(
    withMilk,
    withExtraShot,
    withExtraShot,
    withCaramelSauce,
    withWhippedCream,
    withIce,
    withSize('Large')
  )(createSimpleCoffee())
  displayBeverageInfo(doubleCaramelFrappuccino)
}

// 実行
example()
```
