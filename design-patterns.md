# デザインパターン一覧

## 基本パターン（GoF）

### 1. Creational パターン

- [Abstract Factory (抽象ファクトリ)](abstract-factory.md)
- [Builder (ビルダー)](builder.md)
- [Factory Method (ファクトリーメソッド)](factory-method.md)
- [Prototype (プロトタイプ)](prototype.md)
- [Singleton (シングルトン)](singleton.md)

### 2. Structural パターン

- [Adapter (アダプター)](adapter.md)
- [Bridge (ブリッジ)](bridge.md)
- [Composite (コンポジット)](composite.md)
- [Decorator (デコレーター)](decorator.md)
- [Facade (ファサード)](facade.md)
- [Flyweight (フライウェイト)](flyweight.md)
- [Proxy (プロキシ)](proxy.md)

### 3. Behavioral パターン

- [Chain of Responsibility (責任の連鎖)](chain-of-responsibility.md)
- [Command (コマンド)](command.md)
- [Interpreter (インタプリタ)](interpreter.md)
- [Iterator (イテレーター)](iterator.md)
- [Mediator (メディエーター)](mediator.md)
- [Memento (メメント)](memento.md)
- [Observer (オブザーバー)](observer.md)
- [State (ステート)](state.md)
- [Strategy (ストラテジー)](strategy.md)
- [Template Method (テンプレートメソッド)](template-method.md)
- [Visitor (ビジター)](visitor.md)

## 追加パターン

### 1. オブジェクト生成パターン

- [Multiton (マルチトン)](multiton.md)
- [Object Pool (オブジェクトプール)](object-pool.md)

### 2. インターセプトパターン

- [Interceptor (インターセプター)](interceptor.md)

## システムパターン

### 1. アーキテクチャパターン

#### レイヤー型
- [Layered Architecture (レイヤードアーキテクチャ)](layered-architecture.md)
- [Clean Architecture (クリーンアーキテクチャ)](clean-architecture.md)
- [Hexagonal Architecture (ヘキサゴナルアーキテクチャ)](hexagonal-architecture.md)
- [Onion Architecture (オニオンアーキテクチャ)](onion-architecture.md)

#### 分散型
- [Microservices (マイクロサービス)](microservices.md)
- [Service-Oriented Architecture (SOA)](soa.md)
- [Monolithic (モノリシック)](monolithic.md)
- [Monolithic Architecture (モノリシックアーキテクチャ)](monolithic-architecture.md)

#### プレゼンテーション
- [MVC (Model-View-Controller)](mvc.md)
- [MVP (Model-View-Presenter)](mvp.md)
- [MVVM (Model-View-ViewModel)](mvvm.md)
- [Client-Side MVC (クライアントサイドMVC)](client-side-mvc.md)
- [Server-Side MVC (サーバーサイドMVC)](server-side-mvc.md)
- [PAC (Presentation-Abstraction-Control)](pac.md)
- [Presentation Model (プレゼンテーションモデル)](presentation-model.md)

### 2. 統合パターン

#### ゲートウェイ
- [API Gateway (APIゲートウェイ)](api-gateway.md)
- [Backend for Frontend (BFF)](backend-for-frontend.md)
- [Backends for Frontends (BFF)](bff.md)
- [Anti-Corruption Layer (腐敗防止層)](anti-corruption-layer.md)

#### マイクロサービス連携
- [Ambassador (アンバサダー)](ambassador.md)
- [Sidecar (サイドカー)](sidecar.md)
- [Gateway Aggregation (ゲートウェイ集約)](gateway-aggregation.md)
- [Gateway Routing (ゲートウェイルーティング)](gateway-routing.md)
- [Gateway Offloading (ゲートウェイオフロード)](gateway-offloading.md)
- [Strangler Fig (ストラングラーフィグ)](strangler-fig.md)

### 3. 耐障害性パターン

- [Circuit Breaker (サーキットブレーカー)](circuit-breaker.md)
- [Retry (リトライ)](retry.md)
- [Fallback (フォールバック)](fallback.md)
- [Bulkhead (バルクヘッド)](bulkhead.md)
- [Rate Limiter (レートリミッター)](rate-limiter.md)
- [Throttling (スロットリング)](throttling.md)

### 4. キャッシュパターン

- [Cache-Aside (キャッシュアサイド)](cache-aside.md)
- [Read-Through (リードスルーキャッシュ)](read-through.md)
- [Write-Through (ライトスルーキャッシュ)](write-through.md)
- [Write-Behind (ライトビハインドキャッシュ)](write-behind.md)
- [Write-Around (ライトアラウンド)](write-around.md)
- [Refresh-Ahead (リフレッシュアヘッド)](refresh-ahead-cache.md)

### 5. データアクセスパターン

- [Active Record (アクティブレコード)](active-record.md)
- [Repository (リポジトリ)](repository.md)
- [Data Access Object (DAO)](dao.md)
- [Unit of Work (ユニットオブワーク)](unit-of-work.md)
- [Identity Map (アイデンティティマップ)](identity-map.md)
- [Domain Model (ドメインモデル)](domain-model.md)
- [Table Module (テーブルモジュール)](table-module.md)
- [Transaction Script (トランザクションスクリプト)](transaction-script.md)
- [Registry (レジストリ)](registry.md)

### 6. メッセージングパターン

- [Publisher-Subscriber (パブリッシャー・サブスクライバー)](pub-sub.md)
- [Event-Driven Architecture (イベント駆動アーキテクチャ)](event-driven-architecture.md)
- [CQRS (コマンドクエリ責務分離)](cqrs.md)
- [Event Sourcing (イベントソーシング)](event-sourcing.md)
- [Saga (サガ)](saga.md)

### 7. 並行処理パターン

- [Actor Model (アクターモデル)](actor-model.md)
- [Reactor (リアクター)](reactor.md)
- [Reactive Extensions (リアクティブ拡張)](rx.md)
- [Pipeline (パイプライン)](pipeline.md)
- [Middleware (ミドルウェア)](middleware.md)

### 8. データ管理パターン

- [Optional (オプショナル)](optional.md)
- [Value Object (値オブジェクト)](value-object.md)
- [Maybe (メイビー)](maybe.md)
- [Either (イーザー)](either.md)
- [Result (リザルト)](result.md)
- [Special Case (特殊ケース)](special-case.md)
- [Service Locator (サービスロケーター)](service-locator.md)
- [Null Object (ヌルオブジェクト)](null-object.md)
- [Entity (エンティティ)](entity.md)
- [Specification (仕様)](specification.md)
- [Dependency Injection (依存性注入)](di.md)
