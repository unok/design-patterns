# Ambassador Pattern（アンバサダーパターン）

## 目的

クライアントアプリケーションから複雑なネットワーク通信やサービス間通信の詳細を隠蔽し、共通の機能（ロギング、モニタリング、リトライ、セキュリティなど）を提供する補助サービスを実装します。

## 価値・解決する問題

- クライアントコードの単純化
- 共通機能の一元管理
- プラットフォーム固有の詳細の抽象化
- クロスカッティングコンサーンの分離
- サービスの回復力と信頼性の向上
- 監視とデバッグの容易化

## 概要・特徴

### 概要

Ambassadorパターンは、クライアントとリモートサービス間に配置される「大使」のような補助サービスを提供します。このパターンは、サービス間の通信に関連する共通の課題（再試行、認証、ロギング、監視など）をクライアントコードから分離し、専用のコンポーネントに委譲します。マイクロサービスアーキテクチャやクラウドネイティブ環境で特に有効です。

### 特徴

#### プロキシ機能
Ambassadorパターンの基本的な特徴は、クライアントアプリケーションの代わりにリモートサービスとの通信を仲介するプロキシ機能です。クライアントは直接リモートサービスにアクセスするのではなく、ローカルのAmbassadorコンポーネントと通信します。このコンポーネントが実際のネットワーク通信、プロトコル処理、エラーハンドリングなどの詳細を担当します。この間接的な通信モデルにより、クライアントコードは通信の複雑さから解放され、ビジネスロジックに集中できます。例えば、マイクロサービスアーキテクチャにおいて、あるサービスが他の複数のサービスと通信する必要がある場合、各通信先ごとにAmbassadorを配置することで、サービス間通信の複雑さを管理可能なレベルに抑えることができます。プロキシ機能はまた、サービスの場所や実装の詳細をクライアントから隠蔽することで、システムの柔軟性と拡張性を高めます。サービスの移行や再構築が必要になった場合でも、Ambassadorの内部実装を変更するだけで、クライアントコードに影響を与えることなく対応できます。

#### 横断的関心事の管理
Ambassadorパターンは、複数のサービスやコンポーネントにまたがる横断的関心事（クロスカッティングコンサーン）を一元管理する強力な手段を提供します。これには、認証・認可、ロギング、メトリクス収集、トレーシング、監査などが含まれます。これらの関心事をクライアントコードから分離し、Ambassadorに集約することで、コードの重複を減らし、一貫した実装を確保できます。例えば、すべてのサービス呼び出しに対して一貫した認証トークンの処理、標準化されたログフォーマット、統合されたトレーシングIDの伝播などを実装できます。これは特に大規模な分散システムやマイクロサービスアーキテクチャにおいて、システム全体の一貫性と保守性を高める重要な役割を果たします。また、横断的関心事の要件が変更された場合（例えば、新しい認証方式の導入やログ形式の変更）、Ambassadorのみを更新すれば良いため、変更の影響範囲を最小限に抑えることができます。これにより、システム全体の進化と保守が容易になり、新しいセキュリティ要件や監視要件にも迅速に対応できるようになります。

#### 再試行と回復力
分散システムにおける一時的な障害や通信エラーに対処するため、Ambassadorパターンは洗練された再試行メカニズムと回復力戦略を提供します。ネットワークの一時的な問題、サービスの一時的な過負荷、データセンターの切り替えなど、様々な障害シナリオに対して、適切な再試行ポリシーを実装できます。これには、指数バックオフ（exponential backoff）アルゴリズム、ジッタ（jitter）の追加、最大再試行回数の設定、タイムアウト管理などの高度な技術が含まれます。例えば、HTTPリクエストが失敗した場合、Ambassadorは自動的に設定された回数まで再試行し、その間にバックオフ時間を指数関数的に増加させることで、サービスの回復に時間を与えることができます。また、異なるタイプのエラーに対して異なる再試行戦略を適用することも可能です（一時的なエラーは再試行するが、認証エラーは即座に失敗させるなど）。これらの回復力機能はクライアントコードから完全に隠蔽されるため、アプリケーション開発者は障害処理の複雑な詳細に悩まされることなく、ビジネスロジックの実装に集中できます。結果として、システム全体の安定性と信頼性が向上し、一時的な障害がエンドユーザーに影響を与える可能性が低減されます。

#### プラットフォーム抽象化
Ambassadorパターンの重要な利点の一つは、異なるプラットフォーム、プロトコル、APIバージョン間の違いを抽象化する能力です。クライアントアプリケーションは単一の一貫したインターフェースを使用して通信でき、Ambassador内部で必要な変換や適応が行われます。これにより、クライアントは通信先のサービスが使用するプロトコルや技術的詳細を意識する必要がなくなります。例えば、一部のサービスがRESTを使用し、他のサービスがgRPCやGraphQLを使用している場合でも、Ambassadorはこれらの違いを隠蔽し、クライアントに統一されたインターフェースを提供できます。また、クラウドプロバイダー固有のAPIやサービスを使用する場合も、Ambassadorがこれらの詳細を抽象化することで、クライアントコードのポータビリティと再利用性を高めることができます。さらに、APIのバージョン管理や互換性の問題もAmbassador層で処理できるため、サービスのAPIが進化しても、クライアントコードへの影響を最小限に抑えることができます。この抽象化は、特にマルチクラウド環境やハイブリッドクラウド環境で価値を発揮し、基盤となるインフラストラクチャやサービスの実装詳細からアプリケーションロジックを効果的に分離します。

#### サーキットブレーカー
Ambassadorパターンの高度な機能として、サーキットブレーカーパターンの実装があります。これは電気回路のブレーカーと同様に、障害が発生しているサービスへの呼び出しを一時的に停止し、システム全体の安定性を保つ役割を果たします。サービスが正常に応答しない場合（タイムアウトや高エラー率など）、サーキットブレーカーは「オープン」状態になり、そのサービスへの後続の呼び出しを即座に失敗させます。これにより、失敗が予測されるリクエストを試みることによる無駄なリソース消費を防ぎ、障害のあるサービスにさらなる負荷をかけることを避けます。一定時間経過後、サーキットブレーカーは「半オープン」状態に移行し、限られた数のリクエストを試験的に許可してサービスの回復状態を確認します。サービスが正常に応答するようになれば、サーキットブレーカーは「クローズド」状態に戻り、通常の操作を再開します。この機能は特に、複数のサービスが相互依存する複雑な分散システムにおいて、障害の連鎖的な拡大（カスケード障害）を防止する重要な役割を果たします。サーキットブレーカーのしきい値やポリシーはサービスの特性に合わせて調整可能であり、障害検出のメトリクス（エラー率、レイテンシなど）や回復に関するパラメータをきめ細かく設定できます。

#### キャッシング
パフォーマンスを向上させ、リモートサービスへの負荷を軽減するため、Ambassadorはレスポンスキャッシング機能を提供することができます。頻繁にアクセスされるが比較的変更されないデータに対して、Ambassadorはレスポンスをローカルにキャッシュし、同じリクエストが再度行われた場合にリモートサービスへの呼び出しをバイパスします。これにより、レイテンシが大幅に低減され、スループットが向上し、バックエンドサービスの負荷が軽減されます。キャッシュの有効期間、キャッシュの無効化条件、キャッシュサイズの制限などのキャッシュポリシーは、データの特性や一貫性要件に基づいて設定できます。例えば、参照データや頻繁に読み取られるが稀に更新されるデータに対しては、長めのキャッシュ有効期間を設定し、動的または頻繁に変更されるデータに対しては短い有効期間や条件付き無効化を設定できます。また、キャッシュヒット率や有効期限切れ率などのメトリクスを収集して、キャッシュの効果を監視し、必要に応じて調整することも重要です。適切に実装されたキャッシング戦略は、特にネットワークレイテンシが高い環境や、リソース制約のあるバックエンドシステムを利用している場合に、アプリケーションの応答性とスケーラビリティを大幅に向上させることができます。

#### 負荷分散
複数のサービスインスタンスがある場合、Ambassadorは負荷分散機能を提供し、リクエストを適切に分散させることができます。これにより、システムの可用性とスケーラビリティが向上します。負荷分散の戦略には、ラウンドロビン、最小接続数、応答時間ベース、ハッシュベースの一貫性のある分散など、様々なアルゴリズムが利用できます。例えば、各リクエストを順番に異なるサービスインスタンスに振り分けるシンプルなラウンドロビン方式や、最も応答が速いインスタンスを優先する応答時間ベースの方式などです。また、Ambassadorは各サービスインスタンスの健全性を監視し、異常を検出した場合には自動的にそのインスタンスをロテーションから除外することができます。これにより、クライアントが障害のあるインスタンスにリクエストを送信するリスクが軽減されます。さらに、地理的に分散したサービスインスタンスがある場合、クライアントの位置に基づいて最も近いインスタンスを選択するなど、地理的な考慮も負荷分散戦略に組み込むことができます。負荷分散は特に高トラフィックのアプリケーションや、高可用性が要求されるミッションクリティカルなシステムにおいて、システム全体の耐障害性とパフォーマンスを向上させる重要な役割を果たします。

#### 接続プール
ネットワーク接続の確立とティアダウンには時間とリソースがかかるため、Ambassadorは接続プール機能を実装して効率を高めることができます。接続プールは、再利用可能なネットワーク接続のセットを維持し、新しいリクエストがある度に接続を確立するのではなく、既存の接続を再利用できるようにします。これにより、接続のオーバーヘッドが削減され、レイテンシが低減し、スループットが向上します。接続プールは、プール内の接続数、接続の最大アイドル時間、接続のヘルスチェック、プールの拡張と縮小ポリシーなどのパラメータを設定できます。例えば、トラフィックが増加した場合に自動的にプールサイズを増やし、トラフィックが減少した場合に余分な接続を閉じるなど、動的に調整することが可能です。また、長時間使用されていない接続や異常が検出された接続を自動的に終了し、新しい健全な接続に置き換えることで、接続プールの健全性を維持することができます。接続プールは特に、多数の短期リクエストを処理する必要があるアプリケーションや、データベース接続、キャッシュサーバー接続、マイクロサービス間の通信など、接続のオーバーヘッドが顕著なシナリオで大きな効果を発揮します。適切に構成された接続プールにより、システムのリソース使用率が最適化され、高負荷時でも安定したパフォーマンスを維持することができます。

#### 非同期通信
Ambassadorパターンの高度な実装では、非同期通信モデルをサポートし、クライアントアプリケーションがブロッキング操作を避けられるようにします。同期的なリクエスト・レスポンスモデルではなく、コールバック、Promise/Future、イベント駆動型メカニズム、リアクティブストリーミングなどの非同期パターンを採用することができます。これにより、クライアントはリモートサービスからの応答を待つ間もその他の処理を継続でき、システム全体のレスポンシブネスとスループットが向上します。例えば、複数のサービスからデータを並行して取得する必要がある場合、非同期コミュニケーションにより、これらのリクエストを同時に発行し、応答が届き次第処理することができます。これは、シーケンシャルに各サービスを呼び出す同期アプローチと比較して、大幅なパフォーマンス向上をもたらします。また、長時間実行される処理やストリーミングデータの取得などのシナリオでも、非同期通信は特に有効です。Ambassadorは、基盤となる非同期通信の複雑さ（エラーハンドリング、タイムアウト管理、バックプレッシャー制御など）を抽象化し、クライアントに使いやすいAPIを提供します。さらに、複数のサービス呼び出しの調整や、条件付き実行、結果の集約など、高度な非同期パターンもAmbassador内で実装できます。これにより、クライアントコードは複雑な非同期プログラミングの詳細から解放され、よりシンプルで保守しやすいものになります。

### 概要図

```mermaid
classDiagram
    class Client {
        +executeOperation()
    }
    note for Client "クライアント
• ビジネスロジックに集中
• 通信の詳細を意識しない"

    class Ambassador {
        -retryPolicy
        -circuitBreaker
        -metrics
        -cache
        -authManager
        +request(serviceCall)
    }
    note for Ambassador "アンバサダー
• 通信の詳細を処理
• 横断的関心事を管理
• クライアントをサービスから分離"

    class RemoteService {
        +processRequest(request)
    }
    note for RemoteService "リモートサービス
• 実際の機能を提供
• クライアントから直接アクセスされない"

    class RetryPolicy {
        +execute(operation)
    }
    note for RetryPolicy "リトライポリシー
• 一時的な障害を処理
• バックオフ戦略を実装"

    class CircuitBreaker {
        +isAllowed()
        +recordSuccess()
        +recordFailure()
    }
    note for CircuitBreaker "サーキットブレーカー
• 連続障害を検出
• 障害時のフェイルファスト"

    class MetricsCollector {
        +startTimer()
        +recordSuccess()
        +recordError()
    }
    note for MetricsCollector "メトリクス収集
• 性能測定
• 監視データ収集"

    class Cache {
        +get(key)
        +set(key, value)
    }
    note for Cache "キャッシュ
• レスポンスを保存
• パフォーマンス向上"

    class AuthManager {
        +getToken()
    }
    note for AuthManager "認証管理
• 認証情報の管理
• トークンの更新"

    Client --> Ambassador: 使用
    Ambassador --> RemoteService: 通信
    Ambassador --> RetryPolicy: 使用
    Ambassador --> CircuitBreaker: 使用
    Ambassador --> MetricsCollector: 使用
    Ambassador --> Cache: 使用
    Ambassador --> AuthManager: 使用

    %% スタイル定義
    style Client fill:#1a237e,stroke:#7986cb,stroke-width:2px,color:#ffffff
    style Ambassador fill:#1b5e20,stroke:#81c784,stroke-width:2px,color:#ffffff
    style RemoteService fill:#b71c1c,stroke:#ef5350,stroke-width:2px,color:#ffffff
    style RetryPolicy fill:#f57f17,stroke:#ffb74d,stroke-width:2px,color:#ffffff
    style CircuitBreaker fill:#f57f17,stroke:#ffb74d,stroke-width:2px,color:#ffffff
    style MetricsCollector fill:#f57f17,stroke:#ffb74d,stroke-width:2px,color:#ffffff
    style Cache fill:#f57f17,stroke:#ffb74d,stroke-width:2px,color:#ffffff
    style AuthManager fill:#f57f17,stroke:#ffb74d,stroke-width:2px,color:#ffffff
```

## 解説ページリンク

- [Martin Fowler - Ambassador Pattern](https://martinfowler.com/articles/patterns-of-distributed-systems/ambassador.html)  
  - 分散システムにおけるAmbassadorパターンの実装方法と利点について、詳細な解説と実例を提供しています。

- [Microsoft - Ambassador pattern](https://learn.microsoft.com/ja-jp/azure/architecture/patterns/ambassador)  
  - クラウドアプリケーションにおけるAmbassadorパターンの実装ガイドと、Azure上での具体的な実装例を紹介しています。

- [Kubernetes - Ambassador pattern](https://kubernetes.io/docs/concepts/extend-kubernetes/service-catalog/)  
  - Kubernetesにおけるサービスメッシュとの統合方法や、コンテナ環境での実践的な使用例を説明しています。

- [NGINX Blog - Ambassador Pattern](https://www.nginx.com/blog/microservices-reference-architecture-nginx-ambassador-pattern/)  
  - マイクロサービスアーキテクチャにおけるAmbassadorパターンの実装方法と、NGINXを使用した具体的な設定例を提供しています。

## コード例

### Before:

```typescript
// クライアントが直接リモートサービスと通信する実装
class RemoteServiceClient {
  private retryCount = 3;
  private timeout = 5000;

  async callRemoteService(data: any) {
    let lastError: Error;

    for (let i = 0; i < this.retryCount; i++) {
      try {
        // タイムアウト設定
        const controller = new AbortController();
        const timeoutId = setTimeout(() => controller.abort(), this.timeout);

        // メトリクスの記録開始
        const startTime = Date.now();

        // リモートサービスの呼び出し
        const response = await fetch('http://remote-service/api', {
          method: 'POST',
          body: JSON.stringify(data),
          headers: {
            'Content-Type': 'application/json',
            'Authorization': `Bearer ${this.getAuthToken()}`,
          },
          signal: controller.signal
        });

        // メトリクスの記録
        const duration = Date.now() - startTime;
        console.log(`Remote service call took ${duration}ms`);

        clearTimeout(timeoutId);

        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }

        // レスポンスのパース
        const result = await response.json();

        // キャッシュの更新
        this.updateCache(result);

        return result;
      } catch (error) {
        lastError = error;
        console.error(`Attempt ${i + 1} failed:`, error);
        await this.delay(Math.pow(2, i) * 1000); // 指数バックオフ
      }
    }

    throw lastError;
  }

  private getAuthToken(): string {
    // 認証トークンの取得ロジック
    return 'some-auth-token';
  }

  private updateCache(data: any): void {
    // キャッシュ更新ロジック
  }

  private delay(ms: number): Promise<void> {
    return new Promise(resolve => setTimeout(resolve, ms));
  }
}
```

### After:

```typescript
// Ambassador（共通機能を提供する補助サービス）
class ServiceAmbassador {
  constructor(
    private retryPolicy: RetryPolicy,
    private circuitBreaker: CircuitBreaker,
    private metrics: MetricsCollector,
    private cache: Cache,
    private authManager: AuthenticationManager
  ) {}

  async request<T>(serviceCall: ServiceCall<T>): Promise<T> {
    // サーキットブレーカーのチェック
    if (!this.circuitBreaker.isAllowed()) {
      throw new Error('Circuit breaker is open');
    }

    // キャッシュのチェック
    const cachedResult = await this.cache.get(serviceCall.getCacheKey());
    if (cachedResult) {
      return cachedResult;
    }

    return this.retryPolicy.execute(async () => {
      try {
        // メトリクス収集の開始
        const timer = this.metrics.startTimer();

        // 認証トークンの取得
        const token = await this.authManager.getToken();

        // サービスコールの実行
        const result = await serviceCall.execute({
          headers: {
            'Authorization': `Bearer ${token}`,
            'Content-Type': 'application/json'
          }
        });

        // メトリクスの記録
        this.metrics.recordSuccess(timer.stop());

        // キャッシュの更新
        await this.cache.set(serviceCall.getCacheKey(), result);

        // サーキットブレーカーの成功を記録
        this.circuitBreaker.recordSuccess();

        return result;
      } catch (error) {
        // メトリクスの記録
        this.metrics.recordError();

        // サーキットブレーカーの失敗を記録
        this.circuitBreaker.recordFailure();

        throw error;
      }
    });
  }
}

// リトライポリシーの実装
class RetryPolicy {
  constructor(
    private maxAttempts: number = 3,
    private backoffMultiplier: number = 2
  ) {}

  async execute<T>(operation: () => Promise<T>): Promise<T> {
    let lastError: Error;

    for (let attempt = 1; attempt <= this.maxAttempts; attempt++) {
      try {
        return await operation();
      } catch (error) {
        lastError = error;
        if (attempt === this.maxAttempts) {
          throw error;
        }
        await this.delay(Math.pow(this.backoffMultiplier, attempt - 1) * 1000);
      }
    }

    throw lastError;
  }

  private delay(ms: number): Promise<void> {
    return new Promise(resolve => setTimeout(resolve, ms));
  }
}

// サーキットブレーカーの実装
class CircuitBreaker {
  private failures = 0;
  private lastFailureTime?: number;
  private readonly threshold = 5;
  private readonly resetTimeout = 60000; // 1分

  isAllowed(): boolean {
    if (this.failures >= this.threshold) {
      const now = Date.now();
      if (this.lastFailureTime && (now - this.lastFailureTime) > this.resetTimeout) {
        this.reset();
        return true;
      }
      return false;
    }
    return true;
  }

  recordSuccess(): void {
    this.reset();
  }

  recordFailure(): void {
    this.failures++;
    this.lastFailureTime = Date.now();
  }

  private reset(): void {
    this.failures = 0;
    this.lastFailureTime = undefined;
  }
}

// クライアントコード
class RemoteServiceClient {
  constructor(private ambassador: ServiceAmbassador) {}

  async callRemoteService(data: any) {
    const serviceCall = new RemoteServiceCall(data);
    return this.ambassador.request(serviceCall);
  }
}

// サービスコールの実装
class RemoteServiceCall implements ServiceCall<any> {
  constructor(private data: any) {}

  getCacheKey(): string {
    return `remote-service:${JSON.stringify(this.data)}`;
  }

  async execute(options: { headers: Record<string, string> }): Promise<any> {
    const response = await fetch('http://remote-service/api', {
      method: 'POST',
      body: JSON.stringify(this.data),
      headers: options.headers
    });

    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }

    return response.json();
  }
}

// 使用例
const ambassador = new ServiceAmbassador(
  new RetryPolicy(),
  new CircuitBreaker(),
  new MetricsCollector(),
  new Cache(),
  new AuthenticationManager()
);

const client = new RemoteServiceClient(ambassador);
const result = await client.callRemoteService({ some: 'data' });
```

## 類似パターンとの比較

- [Sidecar（サイドカー）](sidecar.md): Ambassador は主にクライアントサイドでの共通機能の提供に焦点を当て、Sidecar はアプリケーションコンテナに付随する補助機能を提供します。
- [Proxy（プロキシ）](proxy.md): Ambassador は共通機能の提供に重点を置き、Proxy はアクセス制御や要求の前処理に焦点を当てます。
- [Gateway（ゲートウェイ）](gateway.md): Ambassador は単一のサービスに対する共通機能を提供し、Gateway は複数のサービスへのアクセスを制御します。

## 利用されているライブラリ／フレームワークの事例

- [Linkerd](https://github.com/linkerd/linkerd2): サービスメッシュの実装でAmbassadorパターンを活用
- [Envoy](https://github.com/envoyproxy/envoy): エッジプロキシとサービスプロキシの機能を提供
- [Istio](https://github.com/istio/istio): サービスメッシュでのサイドカープロキシとしてEnvoyを使用 