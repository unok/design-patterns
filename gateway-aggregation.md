# Gateway Aggregation（ゲートウェイ集約）パターン

## 目的

複数のマイクロサービスからのデータを集約し、クライアントに単一のレスポンスとして提供することで、ネットワーク通信を最適化し、クライアントの実装を簡素化します。

## 価値・解決する問題

- ネットワーク通信の削減
- クライアント側の実装の簡素化
- バックエンドサービスの複雑性の隠蔽
- レイテンシの削減
- バックエンドサービスの独立性維持
- クライアントとサービス間の結合度の低減

## 概要・特徴

### 概要

Gateway Aggregationパターンは、複数のマイクロサービスからのレスポンスを集約し、単一のレスポンスとしてクライアントに返す設計パターンです。これにより、クライアントのネットワーク呼び出し回数を減らし、レイテンシを削減しながらバックエンドサービスの分離を維持します。

### 特徴

#### 集約レイヤー
Gateway Aggregationパターンでは、API Gatewayが集約レイヤーとして機能し、複数のマイクロサービスからのデータを一つのレスポンスにまとめます。これにより、クライアントは複数のサービスと個別に通信する必要がなくなります。例えば、Eコマースアプリケーションでは、商品詳細ページを表示するために必要な商品情報、在庫状況、価格、レビュー、関連商品などの情報を、それぞれ専門のマイクロサービスから取得し、単一のレスポンスにまとめることができます。この集約レイヤーにより、クライアントの実装が単純化され、フロントエンドとバックエンドの分離が促進されます。

#### 並列リクエスト
集約ゲートウェイは、複数のマイクロサービスに対するリクエストを並列に実行することで、全体的なレスポンス時間を短縮します。各サービスへのリクエストを順次処理するのではなく、同時に発行し、すべての結果が揃ったところで集約して返すことができます。例えば、ダッシュボード表示に必要な複数のデータソースからの情報を、並列に取得して表示することができます。これにより、ユーザー体験が向上し、アプリケーションの応答性が改善されます。また、非同期リクエスト処理技術と組み合わせることで、さらに効率的な実装が可能になります。

#### フォーマット変換
集約ゲートウェイは、マイクロサービスから返される様々な形式のデータをクライアントに最適な形式に変換する役割も担います。異なるサービスが異なるデータ形式（JSON、XML、独自形式など）を使用している場合でも、ゲートウェイはこれらを統一された一貫性のあるフォーマットに変換できます。例えば、レガシーシステムからのXMLレスポンスと新しいサービスからのJSONレスポンスを、クライアントに適したJSONスキーマに統合することができます。これにより、クライアント側の処理が簡素化され、バックエンドの技術的な詳細からクライアントを保護することができます。

#### キャッシュ機能
集約ゲートウェイにキャッシュ機能を実装することで、頻繁に要求される集約データを一時的に保存し、パフォーマンスを向上させることができます。すべてのリクエストに対して毎回マイクロサービスを呼び出す代わりに、キャッシュされた結果を返すことで、レスポンス時間を短縮し、バックエンドサービスの負荷を軽減できます。例えば、多くのユーザーがアクセスする製品カタログ情報は、数分間キャッシュすることで効率的に提供できます。キャッシュの有効期限、更新戦略、整合性管理などを適切に設計することで、パフォーマンスと最新性のバランスを取ることが重要です。

#### 部分的な障害の処理
集約ゲートウェイは、一部のマイクロサービスが利用できない場合や応答遅延が発生した場合でも、部分的な結果を返すことができます。すべてのサービスからの応答を待つのではなく、タイムアウト設定やサーキットブレーカーパターンを実装することで、システム全体の耐障害性を向上させることができます。例えば、商品ページで関連商品の情報が取得できない場合でも、主要な商品情報と在庫状況は表示し、関連商品部分のみ「現在利用できません」と表示することができます。この方法により、一部のサービスの障害が全体のシステムダウンにつながることを防ぎ、ユーザー体験を最大限維持できます。

#### クロスカッティングコンサーン
集約ゲートウェイは、認証、認可、ロギング、モニタリングなどのクロスカッティングコンサーンを一元的に処理する場所としても機能します。各マイクロサービスでこれらの機能を個別に実装する代わりに、ゲートウェイレベルで共通の処理を行うことで、重複を避け、一貫性のある実装を提供できます。例えば、すべてのサービス呼び出しに対して統一された認証チェックを適用したり、リクエスト/レスポンスのロギングを一箇所で行ったりすることができます。これにより、各サービスの実装がシンプルになり、セキュリティポリシーの管理も容易になります。

### 概要図

```mermaid
flowchart TB
    Client["クライアント
    • モバイル/Webアプリ
    • 外部システム"]
    
    subgraph Gateway["API Gateway"]
        Aggregator["集約コンポーネント
        • 複数レスポンスの統合
        • データ変換
        • キャッシュ
        • 障害処理"]
    end
    
    subgraph Microservices["マイクロサービス"]
        ServiceA["サービスA
        • 商品情報"]
        ServiceB["サービスB
        • 在庫状態"]
        ServiceC["サービスC
        • 価格情報"]
        ServiceD["サービスD
        • レビュー"]
    end
    
    Client -- "1つのリクエスト" --> Gateway
    Gateway -- "複数の並列リクエスト" --> Microservices
    Aggregator --> ServiceA
    Aggregator --> ServiceB
    Aggregator --> ServiceC
    Aggregator --> ServiceD
    Gateway -- "集約された1つのレスポンス" --> Client
    
    style Client fill:#1a237e,stroke:#7986cb,stroke-width:2px,color:#ffffff
    style Gateway fill:#b71c1c,stroke:#ef5350,stroke-width:2px,color:#ffffff
    style Aggregator fill:#006064,stroke:#00bcd4,stroke-width:2px,color:#ffffff
    style Microservices fill:#1b5e20,stroke:#81c784,stroke-width:2px,color:#ffffff
    style ServiceA fill:#f57f17,stroke:#ffb74d,stroke-width:2px,color:#ffffff
    style ServiceB fill:#f57f17,stroke:#ffb74d,stroke-width:2px,color:#ffffff
    style ServiceC fill:#f57f17,stroke:#ffb74d,stroke-width:2px,color:#ffffff
    style ServiceD fill:#f57f17,stroke:#ffb74d,stroke-width:2px,color:#ffffff
```

## コード例

### Before:

```typescript
// クライアントが複数のサービスと直接通信
class ProductDetailsPage {
  async loadProductDetails(productId: string) {
    try {
      // 製品基本情報の取得
      const productResponse = await fetch(
        `http://product-service/products/${productId}`
      );
      const product = await productResponse.json();

      // 在庫情報の取得
      const inventoryResponse = await fetch(
        `http://inventory-service/stock/${productId}`
      );
      const inventory = await inventoryResponse.json();

      // レビュー情報の取得
      const reviewsResponse = await fetch(
        `http://review-service/reviews?productId=${productId}`
      );
      const reviews = await reviewsResponse.json();

      // 価格情報の取得
      const priceResponse = await fetch(
        `http://pricing-service/prices/${productId}`
      );
      const pricing = await priceResponse.json();

      // 推奨商品の取得
      const recommendationsResponse = await fetch(
        `http://recommendation-service/recommendations/${productId}`
      );
      const recommendations = await recommendationsResponse.json();

      // クライアント側でデータを組み合わせて表示
      this.displayProduct({
        ...product,
        stock: inventory.quantity,
        reviews: reviews.items,
        rating: this.calculateAverageRating(reviews.items),
        price: pricing.currentPrice,
        discount: pricing.discount,
        recommendations: recommendations.items
      });
    } catch (error) {
      console.error('Failed to load product details:', error);
      this.showError('製品情報の取得に失敗しました');
    }
  }
}
```

### After:

```typescript
// APIゲートウェイでの集約実装
@Controller('products')
class ProductAggregationController {
  constructor(
    private readonly productClient: ProductServiceClient,
    private readonly inventoryClient: InventoryServiceClient,
    private readonly reviewClient: ReviewServiceClient,
    private readonly pricingClient: PricingServiceClient,
    private readonly recommendationClient: RecommendationServiceClient,
    private readonly cacheManager: CacheManager
  ) {}

  @Get(':id/details')
  async getProductDetails(@Param('id') productId: string) {
    // キャッシュチェック
    const cacheKey = `product-details:${productId}`;
    const cachedData = await this.cacheManager.get(cacheKey);
    if (cachedData) {
      return cachedData;
    }

    try {
      // 並列でサービスを呼び出し
      const [product, inventory, reviews, pricing, recommendations] = await Promise.all([
        this.productClient.getProduct(productId),
        this.inventoryClient.getStock(productId),
        this.reviewClient.getReviews(productId),
        this.pricingClient.getPrice(productId),
        this.recommendationClient.getRecommendations(productId)
      ]);

      // レスポンスの組み立て
      const response = {
        id: product.id,
        name: product.name,
        description: product.description,
        category: product.category,
        brand: product.brand,
        stock: {
          available: inventory.quantity > 0,
          quantity: inventory.quantity,
          nextRestockDate: inventory.nextRestockDate
        },
        reviews: {
          items: reviews.items.map(review => ({
            rating: review.rating,
            comment: review.comment,
            author: review.userName,
            date: review.createdAt
          })),
          averageRating: this.calculateAverageRating(reviews.items),
          totalCount: reviews.totalCount
        },
        pricing: {
          currentPrice: pricing.currentPrice,
          originalPrice: pricing.originalPrice,
          discount: pricing.discount,
          discountPercentage: this.calculateDiscountPercentage(
            pricing.originalPrice,
            pricing.currentPrice
          ),
          currency: pricing.currency
        },
        recommendations: recommendations.items.map(item => ({
          id: item.id,
          name: item.name,
          thumbnail: item.imageUrl,
          price: item.price
        }))
      };

      // キャッシュの更新
      await this.cacheManager.set(cacheKey, response, {ttl: 300}); // 5分間キャッシュ

      return response;
    } catch (error) {
      // エラーハンドリングと部分的な応答
      return this.handleAggregationError(error, productId);
    }
  }

  private calculateAverageRating(reviews: Review[]): number {
    if (reviews.length === 0) return 0;
    const sum = reviews.reduce((acc, review) => acc + review.rating, 0);
    return Math.round((sum / reviews.length) * 10) / 10;
  }

  private calculateDiscountPercentage(original: number, current: number): number {
    if (original <= 0) return 0;
    return Math.round(((original - current) / original) * 100);
  }

  private async handleAggregationError(error: any, productId: string) {
    // エラーログの記録
    this.logger.error('Product aggregation failed', {
      productId,
      error: error.message,
      stack: error.stack
    });

    // サービスごとのフォールバック処理
    try {
      // 最低限必要な製品情報のみ取得
      const product = await this.productClient.getProduct(productId);
      
      return {
        id: product.id,
        name: product.name,
        description: product.description,
        error: {
          message: '一部の製品情報を取得できませんでした',
          details: this.getErrorDetails(error)
        }
      };
    } catch (criticalError) {
      throw new ServiceUnavailableException('製品情報を取得できません');
    }
  }

  private getErrorDetails(error: any): string[] {
    const details = [];
    if (error.services) {
      for (const [service, status] of Object.entries(error.services)) {
        if (status === 'failed') {
          details.push(`${service}の情報を取得できませんでした`);
        }
      }
    }
    return details;
  }
}

// サービスクライアントの実装例
class ProductServiceClient {
  constructor(
    private readonly httpClient: HttpClient,
    private readonly circuitBreaker: CircuitBreaker
  ) {}

  async getProduct(id: string): Promise<Product> {
    return this.circuitBreaker.execute(() =>
      this.httpClient.get(`http://product-service/products/${id}`)
    );
  }
}

// クライアントでの使用例
class ProductDetailsPage {
  async loadProductDetails(productId: string) {
    try {
      // 単一のAPIコールで全データを取得
      const response = await fetch(`/api/products/${productId}/details`);
      const data = await response.json();

      // 集約されたデータを直接使用
      this.displayProduct(data);
    } catch (error) {
      console.error('Failed to load product details:', error);
      this.showError(error.message || '製品情報の取得に失敗しました');
    }
  }
}

// モバイルクライアントでの使用例
class ProductDetailsScreen {
  async loadProductDetails(productId: string) {
    try {
      // 同じエンドポイントを使用
      const response = await fetch(`/api/products/${productId}/details`);
      const data = await response.json();

      // デバイスに応じた表示の最適化
      this.displayProduct({
        name: data.name,
        description: data.description,
        price: this.formatPrice(data.pricing),
        stock: this.formatStockStatus(data.stock),
        rating: data.reviews.averageRating,
        recommendations: data.recommendations.slice(0, 3) // 上位3件のみ表示
      });
    } catch (error) {
      this.showError('製品情報を読み込めません');
    }
  }
}

## 類似パターンとの比較

- [API Gateway（APIゲートウェイ）](gateway.md): Gateway Aggregationはデータの集約に特化し、API Gatewayはルーティングや認証など、より広範な機能を提供します。
- [Backends for Frontends（BFF）](bff.md): Gateway Aggregationは汎用的なデータ集約を行い、BFFは特定のクライアントに最適化されたAPIを提供します。
- [Facade（ファサード）](facade.md): Gateway Aggregationはマイクロサービス間のデータ集約に焦点を当て、Facadeはサブシステムの複雑性を隠蔽します。
- [Gateway Offloading（ゲートウェイオフローディング）](gateway-offloading.md): Gateway Aggregationはデータの集約に焦点を当て、Gateway Offloadingは共通機能の実装を担います。
- [Gateway Routing（ゲートウェイルーティング）](gateway-routing.md): Gateway Aggregationはデータの集約に焦点を当て、Gateway Routingはリクエストの転送に特化します。

## 利用されているライブラリ／フレームワークの事例

- [Apollo Federation](https://github.com/apollographql/federation): GraphQLを使用したサービス集約の実装
- [Spring Cloud Gateway](https://github.com/spring-cloud/spring-cloud-gateway): ゲートウェイ集約機能を提供
- [Kong Gateway](https://github.com/Kong/kong): プラグインを通じたAPIの集約機能を提供 

## 解説ページリンク

- [Microsoft - Gateway Aggregation pattern](https://learn.microsoft.com/ja-jp/azure/architecture/patterns/gateway-aggregation)  
  - クラウドアプリケーションにおけるゲートウェイ集約パターンの実装方法と、具体的なユースケースを詳しく解説しています。

- [Microservices.io - API Gateway pattern](https://microservices.io/patterns/apigateway.html)  
  - マイクロサービスアーキテクチャにおけるゲートウェイ集約の重要性と、実装のベストプラクティスを提供しています。

- [Kong - API Gateway Patterns](https://konghq.com/learning-center/api-gateway/patterns)  
  - API Gateway製品での実装例を交えながら、ゲートウェイ集約パターンの実践的な適用方法を解説しています。

- [AWS - API Gateway](https://aws.amazon.com/jp/api-gateway/patterns/)  
  - クラウド環境でのゲートウェイ集約パターンの実装例と、AWSサービスを使用した具体的な実現方法を提供しています。 