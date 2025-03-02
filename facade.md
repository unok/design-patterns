# Facade（ファサード）パターン

## 目的

複雑なサブシステムに対してシンプルなインターフェースを提供し、クライアントの利用を容易にするパターンです。

## 価値・解決する問題

- 複雑なサブシステムの利用を簡単にします
- サブシステムの詳細を隠蔽します
- 依存関係を最小限に抑えます
- システムの結合度を下げます
- 既存システムのリファクタリングを容易にします

## 概要・特徴

### 概要

Facadeパターンは、複雑なサブシステムに対してシンプルな統一インターフェースを提供することで、クライアントがサブシステムを容易に利用できるようにする設計パターンです。このパターンでは、複数のクラスやサブシステムの複雑な相互作用をファサードクラスによって抽象化し、クライアントから見えるインターフェースをシンプルにします。これにより、クライアントはサブシステムの内部構造や実装の詳細を知る必要がなくなり、使いやすくなります。特に、レガシーコードの改善、複雑なサードパーティライブラリのラッピング、システムの各レイヤー間のインターフェース設計などの場面で効果を発揮します。

Facadeパターンは、GoF（Gang of Four）による「Design Patterns: Elements of Reusable Object-Oriented Software」で定義された構造パターンの一つです。名前の「Facade（ファサード）」は建築用語で「建物の正面」を意味し、複雑な内部構造を隠蔽して外部に対してシンプルな「正面」だけを見せるという概念を表しています。このパターンは、複雑さを管理するための「最小知識の原則」（Law of Demeter）や「情報隠蔽の原則」といったオブジェクト指向設計の基本原則と密接に関連しています。

ソフトウェア開発の歴史の中で、システムの複雑さが増大するにつれてFacadeパターンの重要性は高まってきました。特に、マイクロサービスアーキテクチャやクラウドコンピューティングの普及により、異なるサービスやAPIを統合する必要性が生じた現代のソフトウェア開発において、Facadeパターンは以下のような状況で特に有用です：

1. **レガシーシステムの段階的なリファクタリング**: 古いコードベースを徐々に改善する際のインターフェースとして
2. **複雑なライブラリやサードパーティAPIの統合**: 外部サービスの複雑な操作を隠蔽するための層として
3. **マイクロサービス間の通信の簡素化**: 複数のマイクロサービスとの相互作用を単一のインターフェースに集約
4. **クロスカッティング関心事の管理**: ログ記録、エラー処理、認証など横断的な関心事を一元管理
5. **複雑なドメインロジックの抽象化**: ビジネスロジックの複雑な処理を単純なAPIで提供

Facadeパターンは単なる設計パターンを超えて、複雑なシステムを管理し、関心事を適切に分離するための重要な設計原則としての役割も果たしています。

### 特徴

#### シンプルなインターフェース提供

複雑なサブシステムに対して、単純化された統一的なインターフェースを提供します。これにより、クライアントは複数のサブシステムとの複雑な相互作用を意識せずに、単一のインターフェースを通じて必要な機能を利用できます。例えば、決済処理やメディア変換など、複数のステップや依存関係を持つ処理を、単一のメソッド呼び出しで実行できるようにします。

シンプルなインターフェースを設計する際の具体的なアプローチと利点：

- **ユースケース指向の設計**: クライアントの具体的な利用シナリオに基づいてインターフェースを設計し、不要な柔軟性より実際のニーズに焦点を当てます
```typescript
// 複雑な決済処理をシンプルなインターフェースで提供する例
class PaymentFacade {
  // 内部的には複数のサービスと連携
  private paymentProcessor: PaymentProcessor
  private fraudDetector: FraudDetector
  private notificationService: NotificationService
  private ledgerService: LedgerService
  
  constructor(/* 依存サービスの注入 */) {
    // サービスの初期化
  }
  
  // シンプルな単一メソッドで複雑な処理を隠蔽
  async processPayment(userId: string, amount: number, paymentMethod: PaymentMethod): Promise<PaymentResult> {
    try {
      // 1. 不正検知
      const riskAssessment = await this.fraudDetector.assessRisk(userId, amount, paymentMethod)
      if (riskAssessment.riskLevel > RiskLevel.ACCEPTABLE) {
        throw new Error('Payment rejected due to fraud risk')
      }
      
      // 2. 実際の決済処理
      const paymentResult = await this.paymentProcessor.charge(userId, amount, paymentMethod)
      
      // 3. 台帳への記録
      await this.ledgerService.recordTransaction(userId, amount, paymentResult.transactionId)
      
      // 4. 通知送信
      await this.notificationService.notifyPaymentComplete(userId, amount)
      
      return paymentResult
    } catch (error) {
      // エラー処理を一元管理
      await this.notificationService.notifyPaymentFailed(userId, amount, error)
      throw error
    }
  }
}

// クライアントからの利用例 - 複雑さが隠蔽されている
const payment = await paymentFacade.processPayment('user123', 99.99, { type: 'CREDIT_CARD', ... })
```

- **段階的な複雑性**: 基本的な操作はシンプルに提供しつつ、高度な操作のための拡張インターフェースもオプションで提供する「シンプルファサード」と「高度ファサード」の2層構造
- **コンテキスト特化**: 異なるクライアントやユースケースごとに特化したファサードを提供し、特定の利用文脈に最適化する
- **ドメイン言語の活用**: 技術的な詳細ではなく、ビジネスドメインの用語や概念に基づいたインターフェース設計

シンプルなインターフェースの提供は、システムの学習曲線を緩やかにし、利用の障壁を下げる効果があります。特に、異なるチーム間や組織間の連携が必要な場面や、APIの公開範囲を制限したい場合に効果的です。

#### 複雑さの隠蔽

サブシステムの内部構造や実装の詳細をクライアントから隠蔽します。これにより、クライアントはサブシステムの複雑な仕組みを理解する必要がなく、シンプルなインターフェースだけを知っていれば良くなります。例えば、データベース操作やネットワーク通信などの複雑な処理を抽象化し、クライアントが直接それらの低レベルAPIを扱う必要をなくします。

複雑さの隠蔽がもたらす具体的なメリットと実装テクニック：

- **実装の詳細からの解放**: クライアントはサブシステムの実装技術や内部構造を知る必要がなくなる
```typescript
// 複雑なレガシーシステムの隠蔽例
class LegacySystemFacade {
  // 内部的に複雑なレガシーAPIを使用
  private legacySystem: LegacySystem
  
  constructor() {
    // 複雑な初期化処理
    this.legacySystem = new LegacySystem()
    this.legacySystem.initialize({
      // 複雑な設定パラメータ
      timeout: 30000,
      retryCount: 3,
      encoding: 'LATIN1',
      bufferSize: 8192,
      // ...他多数のパラメータ
    })
  }
  
  // シンプルで現代的なAPIを提供
  async getData(id: string): Promise<Data> {
    // 内部的には複雑なレガシー呼び出しを実行
    const rawData = await new Promise<any>((resolve, reject) => {
      this.legacySystem.queryData(
        id,
        (result, errorCode) => {
          if (errorCode) {
            reject(new Error(`Legacy system error: ${errorCode}`))
          } else {
            resolve(result)
          }
        },
        { format: 'BINARY', verify: true }
      )
    })
    
    // 複雑な変換処理
    return this.convertLegacyDataToModernFormat(rawData)
  }
  
  private convertLegacyDataToModernFormat(rawData: any): Data {
    // 複雑な変換ロジック
    // ...
  }
}

// クライアントは単純なAPIだけを知っていれば良い
const data = await legacySystemFacade.getData('document-123')
```

- **エラー処理の統合**: 複数のサブシステムからの様々な形式のエラーを統一的に扱い、一貫性のあるエラーモデルを提供
- **技術的な複雑さの抽象化**: スレッド管理、接続プーリング、再試行ロジックなどの技術的な複雑さをファサードの内部に閉じ込める
- **バージョニングの管理**: サブシステムのバージョン変更や互換性の問題をファサード層で吸収し、クライアントを保護
- **条件付きロジックの隠蔽**: 複雑な条件分岐や判断ロジックをファサード内で処理し、クライアントコードをシンプルに保つ

複雑さの隠蔽は、特に以下のような状況で効果を発揮します：

1. **レガシーシステムのモダナイゼーション**: 古いシステムを段階的に刷新する際の移行層として
2. **複数の外部APIの統合**: 異なるベンダーや形式のAPIを単一のインターフェースにまとめる際
3. **技術スタックの変更**: 基盤技術の変更をクライアントに影響させずに行う際の保護層として
4. **マルチプラットフォーム対応**: 異なるプラットフォーム固有の実装を隠蔽し、共通インターフェースを提供

#### 依存関係の管理

クライアントとサブシステム間の依存関係を管理・減少させます。クライアントはファサードのみに依存し、サブシステムの個々のコンポーネントに直接依存することがなくなります。これにより、サブシステムの変更がクライアントに与える影響を最小限に抑えることができます。例えば、サブシステムのコンポーネントが変更されても、ファサードのインターフェースを維持することで、クライアントコードを変更せずに済みます。

依存関係管理の具体的な方法と利点：

- **依存性逆転の適用**: ファサードとサブシステムの間に抽象インターフェースを導入し、具体的な実装への依存を減らす
```typescript
// 依存関係逆転を用いたファサード実装例
// 抽象インターフェース
interface Logger {
  log(message: string, level: LogLevel): void
}

interface Storage {
  save(key: string, data: any): Promise<void>
  load(key: string): Promise<any>
}

interface Network {
  request(url: string, method: string, data?: any): Promise<Response>
}

// ファサードは抽象インターフェースに依存
class DataSyncFacade {
  constructor(
    private logger: Logger,
    private storage: Storage,
    private network: Network
  ) {}
  
  async synchronizeData(userId: string): Promise<SyncResult> {
    this.logger.log(`Starting data sync for user ${userId}`, LogLevel.INFO)
    
    try {
      // ローカルデータ取得
      const localData = await this.storage.load(`user_${userId}`)
      
      // サーバーとの同期
      const response = await this.network.request(
        '/api/sync',
        'POST',
        { userId, localData }
      )
      
      // 同期結果の保存
      await this.storage.save(`user_${userId}`, response.data)
      
      this.logger.log(`Sync completed for user ${userId}`, LogLevel.INFO)
      return { success: true, timestamp: new Date() }
    } catch (error) {
      this.logger.log(`Sync failed: ${error.message}`, LogLevel.ERROR)
      throw new SyncError('Data synchronization failed', error)
    }
  }
}

// 実際の依存関係は構成時に注入
const syncFacade = new DataSyncFacade(
  new ConsoleLogger(),  // Logger実装
  new IndexedDBStorage(),  // Storage実装
  new FetchNetworkClient()  // Network実装
)
```

- **サブシステム間の調整**: サブシステム同士の相互作用や依存関係をファサード内で管理し、クライアントはその複雑さから解放される
- **変更のカプセル化**: サブシステムの変更をファサード内に局所化することで、クライアントコードへの影響を最小限に抑える
- **プラグイン可能なアーキテクチャ**: ファサードを通じてサブシステムを交換可能な形で構成し、システム全体の柔軟性を高める
- **選択的依存性**: クライアントの種類に応じて必要なサブシステムだけを選択的に組み合わせたファサードを提供

依存関係管理の効果は特に以下のような状況で顕著になります：

1. **大規模なリファクタリング**: システムの内部構造を大幅に変更する際の緩衝材として
2. **チーム間の協業**: 異なるチームが開発するコンポーネント間のインターフェース役として
3. **テスト容易性の向上**: モックやスタブを用いたテストを容易にするための抽象化層として
4. **サードパーティコンポーネントの交換**: 外部ライブラリを別の実装に置き換える際の変更を局所化

#### 結合度の低減

システム全体の結合度を低減します。ファサードを介してサブシステムとやり取りすることで、クライアントとサブシステム間の直接的な結びつきが少なくなります。これにより、システムの柔軟性や保守性が向上し、変更に強い設計が実現します。特に大規模なシステムでは、モジュール間の結合度を下げることで、システム全体の安定性が高まります。

結合度低減のための設計アプローチと具体的な利点：

- **契約による設計**: ファサードは明確に定義された契約（インターフェース）を通じてサービスを提供し、実装詳細を隠蔽
```typescript
// 契約による設計を用いたファサード例
// 明確に定義された契約（インターフェース）
interface ReportingFacade {
  generateReport(type: ReportType, parameters: ReportParameters): Promise<Report>
  scheduleReport(type: ReportType, parameters: ReportParameters, schedule: Schedule): Promise<string>
  getReportStatus(reportId: string): Promise<ReportStatus>
}

// 実装クラス
class EnterpriseReportingFacade implements ReportingFacade {
  // 内部的に複数のサービスを利用
  constructor(
    private dataWarehouse: DataWarehouse,
    private reportRenderer: ReportRenderer,
    private schedulingService: SchedulingService,
    private notificationService: NotificationService
  ) {}
  
  async generateReport(type: ReportType, parameters: ReportParameters): Promise<Report> {
    // 実装の詳細はクライアントから隠蔽されている
    const data = await this.dataWarehouse.queryData(this.createQuery(type, parameters))
    return this.reportRenderer.render(type, data)
  }
  
  async scheduleReport(type: ReportType, parameters: ReportParameters, schedule: Schedule): Promise<string> {
    const reportJob = {
      type,
      parameters,
      schedule,
      onComplete: async (report: Report) => {
        await this.notificationService.notifyReportComplete(report)
      }
    }
    
    return this.schedulingService.scheduleJob(reportJob)
  }
  
  async getReportStatus(reportId: string): Promise<ReportStatus> {
    return this.schedulingService.getJobStatus(reportId)
  }
  
  private createQuery(type: ReportType, parameters: ReportParameters): DataQuery {
    // クエリ生成ロジック
    // ...
  }
}

// クライアントは契約だけを知っており、実装詳細には依存しない
function useReporting(reporting: ReportingFacade) {
  // ReportingFacadeインターフェースを通じて操作
}
```

- **メディエーターとしての役割**: ファサードは複数のコンポーネント間の調整を担当し、コンポーネント同士の直接的な依存関係を減らす
- **イベント駆動設計との統合**: ファサードを通じてイベントベースの疎結合アーキテクチャを実現し、コンポーネント間の直接的な呼び出しを減らす
- **レイヤー間バウンダリの明確化**: ファサードをアプリケーション層の境界として利用し、ドメイン層とプレゼンテーション層の分離を強化
- **モジュールレベルのカプセル化**: モジュールの内部実装を完全に隠蔽し、公開APIのみを通じた相互作用を強制

結合度の低減は以下のような場面で特に重要になります：

1. **マイクロサービスアーキテクチャ**: 異なるサービス間の相互作用を適切に抽象化する
2. **大規模なモノリシックアプリケーション**: 内部モジュールを論理的に分離し、変更の影響範囲を制限する
3. **チーム間のコラボレーション**: 異なるチームが担当するコンポーネント間のインターフェースを明確に定義する
4. **将来の拡張性を考慮した設計**: システムの一部を将来的に置き換えることを見越した柔軟な構造を実現する

#### 再利用性の向上

ファサードを通じて機能を提供することで、サブシステムの再利用性が向上します。ファサードはサブシステムの利用パターンを標準化し、様々なクライアントが容易に利用できるようにします。さらに、サブシステムの詳細や依存関係を隠蔽することで、サブシステム自体の再利用も容易になります。例えば、複数のプロジェクトで共通のライブラリを利用する際に、プロジェクト固有のファサードを作成することで、ライブラリの利用方法を標準化できます。

再利用性向上のための設計パターンと具体的な利点：

- **コンテキスト固有のファサード**: 異なる利用コンテキストごとに最適化されたファサードを提供し、適切な抽象化レベルでの再利用を促進
```typescript
// 異なるコンテキスト向けの複数のファサード例
// 基本的なメディア処理機能を提供するサブシステム
class MediaProcessingSubsystem {
  // 複雑なメディア処理機能の実装
  // ...
}

// 画像編集アプリケーション向けファサード
class ImageEditorFacade {
  constructor(private mediaSystem: MediaProcessingSubsystem) {}
  
  // 画像編集向けに最適化されたインターフェース
  applyFilter(image: Image, filterType: FilterType): Image {
    // mediaSystemの複数の機能を組み合わせて実装
    // ...
  }
  
  adjustColors(image: Image, settings: ColorSettings): Image {
    // 色調整に特化した処理
    // ...
  }
  
  resize(image: Image, dimensions: Dimensions): Image {
    // リサイズ処理
    // ...
  }
}

// 動画処理アプリケーション向けファサード
class VideoProcessingFacade {
  constructor(private mediaSystem: MediaProcessingSubsystem) {}
  
  // 動画処理向けに最適化されたインターフェース
  applyTransition(video: Video, transition: TransitionType): Video {
    // トランジション処理
    // ...
  }
  
  extractAudio(video: Video): AudioTrack {
    // 音声抽出処理
    // ...
  }
  
  compressForStreaming(video: Video, quality: StreamingQuality): CompressedVideo {
    // ストリーミング向け圧縮処理
    // ...
  }
}

// 両方のファサードは同じサブシステムを再利用しているが、
// 異なるコンテキストに最適化されたインターフェースを提供している
```

- **ユーティリティファサード**: 複雑なAPIの頻繁に使用されるパターンをシンプルなユーティリティメソッドとして提供
- **アダプターとしてのファサード**: 異なるシステム間の互換性を提供し、既存コードの再利用を可能にする
- **コンポジションベースの設計**: 小さな機能単位を組み合わせてより高次の機能を構築し、基本要素の再利用性を高める
- **テンプレートメソッドとの組み合わせ**: 共通の処理フローをテンプレート化し、具体的な実装のみを変更可能にする

再利用性向上が特に効果的な場面：

1. **エンタープライズアプリケーションフレームワーク**: 複数のプロジェクトで共通の機能を提供する
2. **ユーティリティライブラリの設計**: 複雑な処理を簡単に利用できるラッパーを提供する
3. **共通インフラストラクチャの抽象化**: データベースやキャッシュなどの基盤サービスの利用を標準化する
4. **クロスプラットフォーム開発**: 異なるプラットフォーム固有の実装を共通インターフェースの背後に隠蔽する

#### モジュール化と層化設計の促進

Facadeパターンはシステムのモジュール化と層化設計を促進します。ファサードは異なるレイヤーやモジュール間の明確な境界として機能し、責任の分離を実現します。これにより、システムの各部分が独立して発展できるようになり、全体のアーキテクチャが整理されます。例えば、プレゼンテーション層、ビジネスロジック層、データアクセス層の間にファサードを設置することで、各層の独立性を高めることができます。

モジュール化と層化設計を促進するアプローチと利点：

- **レイヤーファサード**: アプリケーションの異なる層の間に設置され、層間の通信を抽象化
```typescript
// 層化アーキテクチャにおけるファサードの使用例
// データアクセス層
class DataAccessLayer {
  // データベース操作の詳細な実装
  // ...
}

// ビジネスロジック層へのファサード
class BusinessServiceFacade {
  constructor(private dataAccess: DataAccessLayer) {}
  
  // ビジネスサービスを提供するメソッド
  async createOrder(userId: string, items: OrderItem[]): Promise<Order> {
    // 入力の検証
    if (!userId || items.length === 0) {
      throw new ValidationError('Invalid order data')
    }
    
    // トランザクション管理
    const transaction = await this.dataAccess.beginTransaction()
    
    try {
      // 複数のデータアクセス操作を調整
      const user = await this.dataAccess.getUserById(userId, transaction)
      if (!user) {
        throw new NotFoundError('User not found')
      }
      
      // 在庫確認
      for (const item of items) {
        const stock = await this.dataAccess.checkInventory(item.productId, transaction)
        if (stock < item.quantity) {
          throw new BusinessError('Insufficient inventory')
        }
      }
      
      // 注文作成
      const order = await this.dataAccess.createOrder({
        userId,
        items,
        totalAmount: this.calculateTotal(items),
        status: 'PENDING'
      }, transaction)
      
      // 在庫更新
      for (const item of items) {
        await this.dataAccess.updateInventory(item.productId, -item.quantity, transaction)
      }
      
      // トランザクション確定
      await this.dataAccess.commitTransaction(transaction)
      return order
    } catch (error) {
      // エラー時のロールバック
      await this.dataAccess.rollbackTransaction(transaction)
      throw error
    }
  }
  
  private calculateTotal(items: OrderItem[]): number {
    // 合計金額計算のロジック
    // ...
  }
}

// プレゼンテーション層（APIコントローラーなど）
class OrderController {
  constructor(private businessService: BusinessServiceFacade) {}
  
  // シンプルなAPIエンドポイント
  async createOrder(req: Request, res: Response) {
    try {
      const { userId, items } = req.body
      const order = await this.businessService.createOrder(userId, items)
      res.status(201).json(order)
    } catch (error) {
      // エラーハンドリング
      // ...
    }
  }
}
```

- **マイクロサービス間のファサード**: 複数のマイクロサービスを統合し、クライアントに一貫したAPIを提供
- **機能モジュールのファサード**: 特定の機能領域をカプセル化し、モジュール間の明確な境界を形成
- **クロスカッティング関心事の抽象化**: ログ記録、セキュリティ、トランザクション管理などの横断的関心事を各層で適切に抽象化
- **APIゲートウェイパターン**: 外部からのリクエストを内部サービスに分配するゲートウェイとしてファサードを活用

モジュール化と層化設計が特に効果的な場面：

1. **大規模なエンタープライズアプリケーション**: 責任領域を明確に分離し、保守性を高める
2. **マイクロサービスアーキテクチャ**: サービス間の連携を抽象化し、クライアントの利用を簡素化する
3. **フロントエンドとバックエンドの分離**: API層をファサードとして設計し、フロントエンドの変更からバックエンドを保護する
4. **レガシーシステムのモダナイゼーション**: 既存システムを段階的にモジュール化するための戦略として活用

#### 実装バリエーションと応用パターン

Facadeパターンには様々な実装バリエーションがあり、異なるコンテキストや要件に応じて適用できます：

**1. シンプルファサードとフルファサード**
- **シンプルファサード**: 最も基本的な機能のみを提供するミニマルなインターフェース
- **フルファサード**: 高度な機能やカスタマイズオプションも含む包括的なインターフェース
- **階層的ファサード**: 基本機能を提供するシンプルファサードと、それを拡張したより高度なファサードの組み合わせ

**2. コンテキスト特化ファサード**
- **ドメイン特化ファサード**: 特定のビジネスドメインに特化したインターフェースを提供
- **ユースケース駆動ファサード**: 特定のユースケースやワークフローに最適化されたインターフェース
- **デバイス・環境適応ファサード**: デバイスや実行環境に応じて最適化されたインターフェース

**3. 動的・適応型ファサード**
- **設定駆動ファサード**: 実行時の設定に基づいて振る舞いを変化させるファサード
- **機能検出ファサード**: 利用可能なサブシステムの機能を検出し、適切に機能提供する適応型ファサード
- **ロールベースファサード**: ユーザーの権限やロールに基づいて異なるインターフェースを提供

**4. アーキテクチャパターンとしての応用**
- **APIゲートウェイ**: マイクロサービスへのアクセスを統合するゲートウェイとしての応用
- **サービスファサード**: SOAにおけるサービスレイヤーの抽象化としての応用
- **フロントコントローラ**: Webアプリケーションにおける入口として全リクエストの処理を統括

**5. 現代的なフレームワークでの実装**
- **依存性注入フレームワークとの統合**: SpringやAngularなどのDIフレームワークを活用したファサード実装
- **リアクティブプログラミングとの組み合わせ**: RxJSやReactive Streamsを使った非同期・リアクティブなファサード
- **関数型プログラミングアプローチ**: 純粋関数とコンポジションに基づくファサードの設計

#### 注意点と落とし穴

Facadeパターンの適用には以下のような注意点や潜在的な問題があります：

1. **ファサードの肥大化**: 時間とともにファサードが多すぎる責任を持ち、「ゴッドオブジェクト」になるリスク
   - **対策**: ファサードの責任範囲を明確に定義し、必要に応じて複数の特化したファサードに分割

2. **過度の抽象化**: 必要以上に抽象化することで、却って理解や使用が難しくなる可能性
   - **対策**: 実際のユースケースに基づいてインターフェースを設計し、過度な汎用性を避ける

3. **パフォーマンスのオーバーヘッド**: 追加のレイヤーによる処理のオーバーヘッドが生じる可能性
   - **対策**: 性能クリティカルな部分では直接アクセスの選択肢を残すか、最適化されたファサードを設計

4. **サブシステムの不適切な隠蔽**: 本来必要な詳細な制御を失ってしまう恐れ
   - **対策**: 高度な制御が必要なケースのために、低レベルのアクセス手段も提供する

5. **変更の伝播**: サブシステムの変更がファサードの変更を要求し、クライアントの安定性が損なわれる
   - **対策**: 適切な抽象化レベルを選び、安定したインターフェースを設計する

### 概要図

```mermaid
classDiagram
    class Client {
        +useSubsystem()
    }
    note for Client "• ファサードを通じてサブシステムを利用\n• 複雑さから保護される"

    class Facade {
        +operation1()
        +operation2()
    }
    note for Facade "• サブシステムへの単純化されたインターフェースを提供\n• 複雑な処理の調整を担当\n• クライアントとサブシステムの間の橋渡し役"

    class SubsystemA {
        +operationA1()
        +operationA2()
    }
    note for SubsystemA "• 特定の機能を担当するコンポーネント\n• 他のサブシステムとの連携が必要なこともある"

    class SubsystemB {
        +operationB1()
        +operationB2()
    }
    note for SubsystemB "• 別の機能領域を担当\n• 詳細な実装はクライアントから隠蔽される"

    class SubsystemC {
        +operationC1()
        +operationC2()
    }
    note for SubsystemC "• サブシステムは相互に依存することもある\n• 複雑な関係性をファサードが管理"

    Client --> Facade
    Facade --> SubsystemA
    Facade --> SubsystemB
    Facade --> SubsystemC
    
    %% スタイル定義
    style Client fill:#4527A0,stroke:#7E57C2,stroke-width:2px,color:#ffffff
    style Facade fill:#1565C0,stroke:#64B5F6,stroke-width:2px,color:#ffffff
    style SubsystemA fill:#2E7D32,stroke:#81C784,stroke-width:2px,color:#ffffff
    style SubsystemB fill:#2E7D32,stroke:#81C784,stroke-width:2px,color:#ffffff
    style SubsystemC fill:#2E7D32,stroke:#81C784,stroke-width:2px,color:#ffffff
```

## 類似パターンとの比較

- [Adapter (アダプター)](adapter.md): Facade は複雑なインターフェースをシンプルにし、これに対して Adapter は互換性のないインターフェースを変換します。
- [Mediator (メディエーター)](mediator.md): Facade はサブシステムへの一方向の通信を提供し、これに対して Mediator はオブジェクト間の双方向の通信を調整します。
- [Proxy (プロキシ)](proxy.md): Facade はインターフェースを簡素化し、これに対して Proxy はアクセス制御を提供します。

## 利用されているライブラリ／フレームワークの事例

- [jQuery](https://github.com/jquery/jquery): DOM操作の簡素化
- [Express.js](https://github.com/expressjs/express): HTTPサーバーの簡素化
- [Spring Framework](https://github.com/spring-projects/spring-framework): Javaエンタープライズ開発の簡素化

## 解説ページリンク

- [Refactoring Guru - Facade](https://refactoring.guru/design-patterns/facade)
- [Microsoft - Facade Pattern](https://docs.microsoft.com/en-us/previous-versions/msp-n-p/ee658117(v=pandp.10))
- [SourceMaking - Facade](https://sourcemaking.com/design_patterns/facade)

## コード例

### Before:

複雑なサブシステムを直接利用する実装

```typescript
// 複雑なサブシステムのクラス群
class VideoFile {
  private file: string;

  constructor(filename: string) {
    this.file = filename;
  }

  read(): string {
    return `動画ファイル ${this.file} を読み込みました`;
  }

  save(data: string): void {
    console.log(`動画ファイル ${this.file} を保存しました: ${data}`);
  }
}

class AudioMixer {
  private volume: number;

  constructor() {
    this.volume = 1.0;
  }

  setVolume(volume: number): void {
    this.volume = Math.max(0, Math.min(1, volume));
  }

  mix(audioData: string): string {
    return `音声をミックスしました (volume: ${this.volume}): ${audioData}`;
  }
}

class VideoEncoder {
  private quality: number;
  private format: string;

  constructor() {
    this.quality = 720;
    this.format = "mp4";
  }

  setQuality(quality: number): void {
    this.quality = quality;
  }

  setFormat(format: string): void {
    this.format = format;
  }

  encode(videoData: string): string {
    return `動画をエンコードしました (quality: ${this.quality}p, format: ${this.format}): ${videoData}`;
  }
}

class BitrateConverter {
  private bitrate: number;

  constructor() {
    this.bitrate = 1000;
  }

  setBitrate(bitrate: number): void {
    this.bitrate = bitrate;
  }

  convert(data: string): string {
    return `ビットレートを変換しました (${this.bitrate}kbps): ${data}`;
  }
}

class CodecFactory {
  extract(file: string): string {
    return `コーデックを抽出しました: ${file}`;
  }
}

// クライアントコード
function convertVideo(filename: string, format: string) {
  const file = new VideoFile(filename);
  const sourceVideo = file.read();

  const codecFactory = new CodecFactory();
  const sourceCodec = codecFactory.extract(filename);

  const bitrateConverter = new BitrateConverter();
  bitrateConverter.setBitrate(1500);
  const convertedVideo = bitrateConverter.convert(sourceCodec);

  const audioMixer = new AudioMixer();
  audioMixer.setVolume(0.8);
  const mixedAudio = audioMixer.mix(convertedVideo);

  const videoEncoder = new VideoEncoder();
  videoEncoder.setQuality(1080);
  videoEncoder.setFormat(format);
  const encodedVideo = videoEncoder.encode(mixedAudio);

  file.save(encodedVideo);
}

// 使用例
function example() {
  console.log("=== 動画変換の実行 ===");
  convertVideo("funny_cat_video.mp4", "avi");
}

example();
```

### After:

Facadeパターンを関数型プログラミングスタイルで適用した実装

```typescript
// 各サブシステムのインターフェース型定義
type VideoFileData = Readonly<{
  filename: string
  content?: string
}>

type AudioMixerOptions = Readonly<{
  volume: number
}>

type VideoEncoderOptions = Readonly<{
  quality: number
  format: string
}>

type BitrateOptions = Readonly<{
  bitrate: number
}>

// サブシステムの純粋関数群
const videoFileOps = {
  read: (data: VideoFileData): string => 
    `動画ファイル ${data.filename} を読み込みました`,
  
  save: (data: VideoFileData, content: string): void => {
    console.log(`動画ファイル ${data.filename} を保存しました: ${content}`)
  }
}

const audioMixerOps = {
  mix: (options: AudioMixerOptions, audioData: string): string => {
    const safeVolume = Math.max(0, Math.min(1, options.volume))
    return `音声をミックスしました (volume: ${safeVolume}): ${audioData}`
  }
}

const videoEncoderOps = {
  encode: (options: VideoEncoderOptions, videoData: string): string => 
    `動画をエンコードしました (quality: ${options.quality}p, format: ${options.format}): ${videoData}`
}

const bitrateOps = {
  convert: (options: BitrateOptions, data: string): string => 
    `ビットレートを変換しました (${options.bitrate}kbps): ${data}`
}

const codecOps = {
  extract: (filename: string): string => 
    `コーデックを抽出しました: ${filename}`
}

// Facadeの型定義
type VideoConversionOptions = Readonly<{
  format: string
  quality: number
  bitrate: number
  volume: number
}>

// デフォルトのオプション
const DEFAULT_OPTIONS: VideoConversionOptions = Object.freeze({
  format: 'mp4',
  quality: 1080,
  bitrate: 1500,
  volume: 0.8
})

// プリセット設定
const PRESETS: Readonly<Record<string, VideoConversionOptions>> = Object.freeze({
  web: Object.freeze({
    format: 'mp4',
    quality: 720,
    bitrate: 1000,
    volume: 1.0
  }),
  mobile: Object.freeze({
    format: 'mp4',
    quality: 480,
    bitrate: 800,
    volume: 1.0
  }),
  hd: Object.freeze({
    format: 'mp4',
    quality: 1080,
    bitrate: 2000,
    volume: 1.0
  }),
  '4k': Object.freeze({
    format: 'mp4',
    quality: 2160,
    bitrate: 4000,
    volume: 1.0
  })
})

// ファサード関数群
const videoConverterFacade = {
  // 動画変換の実行
  convert: (
    filename: string, 
    customOptions: Partial<VideoConversionOptions> = {}
  ): void => {
    // オプションのマージ
    const options: VideoConversionOptions = Object.freeze({
      ...DEFAULT_OPTIONS,
      ...customOptions
    })

    console.log('=== 動画変換の開始 ===')
    console.log(`入力ファイル: ${filename}`)
    console.log(`出力形式: ${options.format}`)

    try {
      // ファイルの読み込み
      const file: VideoFileData = { filename }
      const sourceVideo = videoFileOps.read(file)
      console.log(sourceVideo)

      // コーデックの抽出
      const sourceCodec = codecOps.extract(filename)
      console.log(sourceCodec)

      // ビットレートの変換
      const convertedVideo = bitrateOps.convert(
        { bitrate: options.bitrate }, 
        sourceCodec
      )
      console.log(convertedVideo)

      // 音声のミキシング
      const mixedAudio = audioMixerOps.mix(
        { volume: options.volume }, 
        convertedVideo
      )
      console.log(mixedAudio)

      // 動画のエンコード
      const encodedVideo = videoEncoderOps.encode(
        { 
          quality: options.quality, 
          format: options.format 
        }, 
        mixedAudio
      )
      console.log(encodedVideo)

      // 結果の保存
      videoFileOps.save(file, encodedVideo)

      console.log('=== 動画変換の完了 ===')
    } catch (error) {
      console.error('動画変換中にエラーが発生しました:', error)
      throw error
    }
  },

  // バッチ変換の実行
  convertBatch: (
    files: ReadonlyArray<string>, 
    customOptions: Partial<VideoConversionOptions> = {}
  ): void => {
    console.log(`=== バッチ変換の開始 (${files.length}ファイル) ===`)
    
    files.forEach((file, index) => {
      console.log(`\n[${index + 1}/${files.length}] ${file} の変換を開始`)
      try {
        videoConverterFacade.convert(file, customOptions)
      } catch (error) {
        console.error(`${file} の変換に失敗しました:`, error)
      }
    })

    console.log('\n=== バッチ変換の完了 ===')
  },

  // プリセットを使用した変換
  convertWithPreset: (
    filename: string, 
    preset: 'web' | 'mobile' | 'hd' | '4k'
  ): void => {
    console.log(`プリセット '${preset}' を使用して変換を開始`)
    videoConverterFacade.convert(filename, PRESETS[preset])
  }
}

// 使用例
const example = (): void => {
  // 基本的な変換
  console.log('\n1. 基本的な変換')
  videoConverterFacade.convert('funny_cat_video.mp4', { format: 'avi' })

  // カスタムオプションを使用した変換
  console.log('\n2. カスタムオプションを使用した変換')
  videoConverterFacade.convert('vacation_video.mov', {
    format: 'mp4',
    quality: 1440,
    bitrate: 2000,
    volume: 0.9
  })

  // プリセットを使用した変換
  console.log('\n3. プリセットを使用した変換')
  videoConverterFacade.convertWithPreset('conference_recording.webm', 'web')

  // バッチ変換
  console.log('\n4. バッチ変換')
  videoConverterFacade.convertBatch(
    [
      'video1.mp4',
      'video2.avi',
      'video3.mov'
    ],
    {
      format: 'mp4',
      quality: 1080
    }
  )
}

// 実行
example()