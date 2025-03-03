# Rate Limiter（レートリミッター）パターン

## 目的

リソースへのアクセス頻度を制限し、システムの安定性とパフォーマンスを維持するパターンです。

## 価値・解決する問題

- リソースの過剰消費を防ぎます
- システムの安定性を向上させます
- サービスの品質を維持します
- DoS攻撃を防止します
- 公平なリソース分配を実現します

## 概要・特徴

### 概要

Rate Limiterパターンは、リソースへのアクセス頻度を制限する設計パターンです。これにより、システムの安定性とパフォーマンスを維持し、公平なリソース分配を実現します。

### 特徴

#### アクセス制限
Rate Limiterパターンの中核機能は、特定のリソースやAPIに対するアクセス頻度を制限する能力です。この制限は、単一ユーザー、IPアドレス、APIキー、またはその他の識別子ごとに適用できます。例えば、1ユーザーあたり1分間に最大100リクエストといった形で定義されます。これにより、少数のクライアントが過剰なリクエストを送信することでシステム全体のパフォーマンスが低下するというシナリオを防止できます。アクセス制限は、サービス拒否（DoS）攻撃やクライアントの不具合によって引き起こされる意図しない高負荷からシステムを保護する有効な手段です。また、特定のリソースへのアクセスを意図的に抑制することで、システム全体の負荷分散や優先度の高いトラフィックのための容量確保にも役立ちます。このようなアクセス制限メカニズムは、マイクロサービスアーキテクチャや分散システムにおいて特に重要であり、個々のサービスのキャパシティを超えるトラフィックから保護する役割を果たします。

#### トークンバケット方式
トークンバケットアルゴリズムは、Rate Limiterの実装で広く使用されている手法です。このアルゴリズムでは、一定のレートでトークンが生成され、バケットに蓄積されます。リクエストが処理されるたびに、バケットからトークンが消費されます。バケットが空の場合、新しいリクエストはブロックまたは遅延されます。このアプローチの大きな利点は、一時的なトラフィックバーストに対応できることです。バケットにトークンが蓄積されていれば、短期間の高負荷でもスムーズに処理できます。例えば、1秒あたり10トークンが生成され、バケットの容量が100トークンの場合、クライアントは一時的に高頻度でリクエストを送信できますが、長期的には1秒あたり10リクエストの制限が維持されます。このアルゴリズムは、ネットワークトラフィック制御、APIレート制限、データベース接続管理など、様々なシナリオで効果的です。実装も比較的シンプルで効率的であり、メモリ消費も抑えられるため、高スループットが求められるシステムに適しています。多くのクラウドサービスやAPIゲートウェイでは、このトークンバケットアルゴリズムをベースにしたレート制限機能を提供しています。

#### 時間窓方式
時間窓（Time Window）アルゴリズムは、固定または移動時間枠内でのリクエスト数をカウントし、制限する方法です。固定時間窓では、例えば「1分間に最大60リクエスト」という形で制限が定義され、1分ごとにカウンターがリセットされます。移動時間窓（Sliding Window）では、現在時刻から過去一定時間（例：過去60秒間）のリクエスト数を常に計算し、上限を超えないように制御します。固定時間窓の実装はシンプルですが、時間枠の境界でトラフィックスパイクが発生する可能性があります（例：分の変わり目に集中的にリクエストが行われる）。対して移動時間窓は、より滑らかなレート制限を提供しますが、実装はやや複雑で、過去のリクエスト履歴を保持する必要があります。タイムスタンプカウンター、ビットマップ、またはレッドブラックツリーなど、様々なデータ構造がこのアルゴリズムの実装に使用されます。特に高頻度APIや分散システムでは、レッドブラックツリーのような効率的なデータ構造を使用することで、メモリ使用量を抑えつつ高速な判定が可能になります。時間窓方式は、特に一定時間内のリクエスト総数を厳格に制限したい場合や、時間ベースの課金モデルを持つサービスに適しています。

#### 優先順位付け
高度なRate Limiterシステムでは、リクエストの優先順位付けをサポートしています。すべてのリクエストを同等に扱うのではなく、重要度や緊急性に基づいて異なる制限ポリシーを適用します。例えば、通常のユーザーリクエストよりも管理タスクや決済処理などのクリティカルな操作に高い優先順位を割り当てることができます。また、有料サービスでは、サブスクリプションレベルに応じた階層的なレート制限を設定することも一般的です（プレミアムユーザーは標準ユーザーより高いリクエスト上限を持つなど）。この優先順位付けは、複数のトークンバケットや時間窓を並行して使用したり、キューイングシステムと組み合わせたりすることで実装できます。特に負荷の高い状況では、低優先度のリクエストを遅延させたり拒否したりすることで、重要なオペレーションのパフォーマンスと可用性を確保します。この機能は、サービスレベル契約（SLA）を維持し、ビジネスクリティカルな機能を保護するために重要です。また、システム負荷に応じて動的に優先順位ポリシーを調整する適応型レート制限も、より洗練されたシステムでは採用されています。

#### メトリクス収集
効果的なRate Limiterシステムには、包括的なメトリクス収集と監視機能が不可欠です。これには、リクエスト数、拒否率、バケットの満杯状態、応答時間などの統計情報が含まれます。これらのメトリクスは、レート制限の効果を評価し、必要に応じて調整するための重要なデータとなります。例えば、特定のエンドポイントで頻繁にレート制限に達している場合、そのリソースの割り当てを増やすか、クライアント側の実装を最適化する必要があるかもしれません。また、突然のトラフィックパターンの変化は、システムの異常や潜在的なセキュリティ問題を示している可能性もあります。メトリクス収集は、リアルタイムのダッシュボード表示だけでなく、長期的なトレンド分析やキャパシティプランニングにも活用されます。多くの場合、PrometheusやGrafanaなどのツールと統合し、アラートシステムと連携して、しきい値を超えた場合に自動通知を行うように構成されます。これにより、運用チームはシステムのパフォーマンスと健全性を継続的に監視し、問題が深刻化する前に対応することができます。

### 概要図

```mermaid
classDiagram
    class Client {
        +sendRequest()
    }
    note for Client "クライアント
• リクエストの送信
• レスポンスの受信"

    class RateLimiter {
        +execute()
        -acquirePermit()
        -cleanup()
        -getMetrics()
    }
    note for RateLimiter "レートリミッター
• リクエストの制限
• 時間窓管理
• メトリクス収集"

    class TokenBucket {
        +getToken()
        +refillTokens()
        +getAvailableTokens()
    }
    note for TokenBucket "トークンバケット
• トークン管理
• レート制御
• バケットサイズ管理"

    class Resource {
        +processRequest()
        +getStatus()
    }
    note for Resource "リソース
• リクエストの処理
• 状態管理
• 負荷監視"

    class RateLimiterMetrics {
        +totalRequests
        +acceptedRequests
        +rejectedRequests
        +currentRequests
    }
    note for RateLimiterMetrics "メトリクス
• リクエスト統計
• 制限状態監視
• パフォーマンス分析"

    Client --> RateLimiter: リクエスト送信
    RateLimiter --> TokenBucket: トークン要求
    RateLimiter --> Resource: 制限付きアクセス
    RateLimiter --> RateLimiterMetrics: メトリクス収集

    %% スタイル定義
    style Client fill:#1a237e,stroke:#7986cb,stroke-width:2px,color:#ffffff
    style RateLimiter fill:#1b5e20,stroke:#81c784,stroke-width:2px,color:#ffffff
    style TokenBucket fill:#b71c1c,stroke:#ef5350,stroke-width:2px,color:#ffffff
    style Resource fill:#f57f17,stroke:#ffb74d,stroke-width:2px,color:#ffffff
    style RateLimiterMetrics fill:#6a1b9a,stroke:#ba68c8,stroke-width:2px,color:#ffffff
```

## 類似パターンとの比較

- [Circuit Breaker (サーキットブレーカー)](circuit-breaker.md): Rate Limiter はリクエストの制限を行い、これに対して Circuit Breaker は障害検知による遮断を行います。
- [Bulkhead (バルクヘッド)](bulkhead.md): Rate Limiter はリクエストの制限を行い、これに対して Bulkhead は分離による障害の局所化を行います。
- [Throttling (スロットリング)](throttling.md): Rate Limiter は制限を超えたリクエストを拒否し、これに対して Throttling は制限を超えたリクエストを遅延させます。

## 利用されているライブラリ／フレームワークの事例

- [Resilience4j](https://resilience4j.readme.io/): Java用の障害耐性ライブラリ
- [Polly](https://github.com/App-vNext/Polly): .NET用の回復力パターンライブラリ
- [Express Rate Limit](https://github.com/nfriedly/express-rate-limit): Express.jsのレートリミッター

## 解説ページリンク

- [Microsoft Cloud Design Patterns: Throttling](https://docs.microsoft.com/en-us/azure/architecture/patterns/throttling)
- [Rate Limiting Strategies and Techniques](https://cloud.google.com/architecture/rate-limiting-strategies-techniques)
- [Understanding Rate Limiting](https://blog.cloudflare.com/understanding-rate-limiting/)

## コード例

### Before:

レートリミッター機能のない実装

```typescript
class UserService {
  private db: Map<string, any>;

  constructor() {
    this.db = new Map([
      ["1", { id: "1", name: "John Doe", email: "john@example.com" }],
      ["2", { id: "2", name: "Jane Smith", email: "jane@example.com" }]
    ]);
  }

  async getUser(id: string): Promise<any> {
    // データベースアクセスを遅延させる
    await new Promise(resolve => setTimeout(resolve, 100));

    const user = this.db.get(id);
    if (!user) {
      throw new Error("User not found");
    }
    return user;
  }

  async updateUser(id: string, data: any): Promise<any> {
    // データベースアクセスを遅延させる
    await new Promise(resolve => setTimeout(resolve, 100));

    if (!this.db.has(id)) {
      throw new Error("User not found");
    }
    const updatedUser = { ...this.db.get(id), ...data };
    this.db.set(id, updatedUser);
    return updatedUser;
  }
}

// 使用例
async function example() {
  const service = new UserService();

  try {
    // 大量のリクエストを同時に実行
    const promises = Array.from({ length: 50 }, (_, i) => 
      service.getUser(String(i % 2 + 1))
    );

    const results = await Promise.all(promises);
    console.log("結果:", results.length);
  } catch (error) {
    console.error("エラー:", error);
  }
}

example();
```

### After:

Rate Limiterパターンを適用した実装

```typescript
// レートリミッターの設定
interface RateLimiterConfig {
  maxRequests: number;     // 最大リクエスト数
  windowMs: number;        // 時間窓（ミリ秒）
  maxWaitingTime: number;  // 最大待機時間（ミリ秒）
}

// レートリミッターのメトリクス
interface RateLimiterMetrics {
  totalRequests: number;   // 総リクエスト数
  acceptedRequests: number; // 受け入れられたリクエスト数
  rejectedRequests: number; // 拒否されたリクエスト数
  currentRequests: number;  // 現在のリクエスト数
  waitingRequests: number;  // 待機中のリクエスト数
}

// レートリミッターの実装
class RateLimiter {
  private requests: Array<number> = [];
  private waitingQueue: Array<{
    resolve: () => void;
    reject: (error: Error) => void;
    timeout: NodeJS.Timeout;
  }> = [];
  private metrics: RateLimiterMetrics = {
    totalRequests: 0,
    acceptedRequests: 0,
    rejectedRequests: 0,
    currentRequests: 0,
    waitingRequests: 0
  };

  constructor(private config: RateLimiterConfig) {
    // 定期的なクリーンアップを開始
    setInterval(() => this.cleanup(), 1000);
  }

  // リクエストを実行
  async execute<T>(operation: () => Promise<T>): Promise<T> {
    this.metrics.totalRequests++;
    this.metrics.currentRequests++;

    try {
      // リクエストを許可するかチェック
      await this.acquirePermit();

      // 操作を実行
      const result = await operation();

      this.metrics.acceptedRequests++;
      return result;
    } catch (error) {
      this.metrics.rejectedRequests++;
      throw error;
    } finally {
      this.metrics.currentRequests--;
    }
  }

  // リクエストの許可を取得
  private async acquirePermit(): Promise<void> {
    const now = Date.now();
    this.cleanup();

    // 現在のウィンドウ内のリクエスト数をチェック
    if (this.requests.length < this.config.maxRequests) {
      this.requests.push(now);
      return;
    }

    // 待機キューが許容範囲内かチェック
    if (this.waitingQueue.length >= this.config.maxRequests) {
      throw new Error("Rate limit exceeded");
    }

    // 待機キューに追加
    return new Promise<void>((resolve, reject) => {
      const timeout = setTimeout(() => {
        const index = this.waitingQueue.findIndex(item => item.timeout === timeout);
        if (index !== -1) {
          this.waitingQueue.splice(index, 1);
          this.metrics.waitingRequests--;
          reject(new Error("Request timeout"));
        }
      }, this.config.maxWaitingTime);

      this.waitingQueue.push({ resolve, reject, timeout });
      this.metrics.waitingRequests++;
    });
  }

  // 期限切れのリクエストをクリーンアップ
  private cleanup(): void {
    const now = Date.now();
    const windowStart = now - this.config.windowMs;

    // 期限切れのリクエストを削除
    while (this.requests.length > 0 && this.requests[0] <= windowStart) {
      this.requests.shift();

      // 待機中のリクエストがあれば実行
      if (this.waitingQueue.length > 0) {
        const { resolve, timeout } = this.waitingQueue.shift()!;
        clearTimeout(timeout);
        this.metrics.waitingRequests--;
        this.requests.push(now);
        resolve();
      }
    }
  }

  // メトリクスを取得
  getMetrics(): RateLimiterMetrics {
    return { ...this.metrics };
  }
}

// ユーザーの型
interface User {
  id: string;
  name: string;
  email: string;
  updatedAt: number;
}

// データベースの実装
class Database {
  private db: Map<string, User>;

  constructor() {
    this.db = new Map([
      ["1", {
        id: "1",
        name: "John Doe",
        email: "john@example.com",
        updatedAt: Date.now()
      }],
      ["2", {
        id: "2",
        name: "Jane Smith",
        email: "jane@example.com",
        updatedAt: Date.now()
      }]
    ]);
  }

  async read(id: string): Promise<User> {
    // データベースアクセスを遅延させる
    await new Promise(resolve => setTimeout(resolve, 100));

    const user = this.db.get(id);
    if (!user) {
      throw new Error("User not found");
    }
    return user;
  }

  async write(id: string, user: User): Promise<void> {
    // データベースアクセスを遅延させる
    await new Promise(resolve => setTimeout(resolve, 100));

    this.db.set(id, {
      ...user,
      updatedAt: Date.now()
    });
  }
}

// レートリミッター機能を持つユーザーサービス
class RateLimitedUserService {
  private readLimiter: RateLimiter;
  private writeLimiter: RateLimiter;
  private db: Database;

  constructor(
    readConfig?: Partial<RateLimiterConfig>,
    writeConfig?: Partial<RateLimiterConfig>
  ) {
    this.readLimiter = new RateLimiter({
      maxRequests: 10,    // 10リクエスト
      windowMs: 1000,     // 1秒間
      maxWaitingTime: 5000, // 最大5秒待機
      ...readConfig
    });

    this.writeLimiter = new RateLimiter({
      maxRequests: 5,     // 5リクエスト
      windowMs: 1000,     // 1秒間
      maxWaitingTime: 10000, // 最大10秒待機
      ...writeConfig
    });

    this.db = new Database();
  }

  // ユーザーを取得
  async getUser(id: string): Promise<User> {
    return await this.readLimiter.execute(async () => {
      return await this.db.read(id);
    });
  }

  // ユーザーを更新
  async updateUser(id: string, data: Partial<User>): Promise<User> {
    return await this.writeLimiter.execute(async () => {
      const currentUser = await this.db.read(id);
      const updatedUser: User = {
        ...currentUser,
        ...data,
        id, // IDは変更不可
        updatedAt: Date.now()
      };
      await this.db.write(id, updatedUser);
      return updatedUser;
    });
  }

  // メトリクスを取得
  getMetrics(): {
    read: RateLimiterMetrics;
    write: RateLimiterMetrics;
  } {
    return {
      read: this.readLimiter.getMetrics(),
      write: this.writeLimiter.getMetrics()
    };
  }
}

// 使用例
async function example() {
  const service = new RateLimitedUserService({
    maxRequests: 5,      // 5リクエスト
    windowMs: 1000,      // 1秒間
    maxWaitingTime: 5000 // 最大5秒待機
  });

  try {
    console.log("=== 大量のリクエストを同時に実行 ===");
    const startTime = Date.now();

    // 20個のリクエストを同時に実行
    const promises = Array.from({ length: 20 }, (_, i) => {
      const id = String(i % 2 + 1);
      return service.getUser(id)
        .then(user => ({ status: "fulfilled", value: user }))
        .catch(error => ({ status: "rejected", reason: error.message }));
    });

    const results = await Promise.all(promises);
    const endTime = Date.now();

    console.log("\n=== 実行結果 ===");
    console.log("成功:", results.filter(r => r.status === "fulfilled").length);
    console.log("失敗:", results.filter(r => r.status === "rejected").length);
    console.log("実行時間:", endTime - startTime, "ms");
    console.log("メトリクス:", service.getMetrics());

    console.log("\n=== 1秒待機後に再度実行 ===");
    await new Promise(resolve => setTimeout(resolve, 1000));

    const user = await service.getUser("1");
    console.log("ユーザー取得成功:", user);
    console.log("メトリクス:", service.getMetrics());

  } catch (error) {
    console.error("エラー:", error);
  }
}

// 実行
example();