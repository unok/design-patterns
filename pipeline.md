# Pipeline (パイプライン)

## 目的

一連の処理ステップを順次実行し、各ステップの出力が次のステップの入力となるような処理フローを構築します。これにより、複雑な処理を小さな独立したステップに分割し、柔軟な処理の組み合わせを実現します。

## 価値・解決する問題

- 複雑な処理フローの分割と整理
- 処理ステップの再利用性向上
- 処理フローの柔軟な組み替え
- 各ステップの独立性確保
- データ変換処理の段階的な実行
- 処理の並列化と最適化
- エラーハンドリングの局所化
- 処理の進捗監視と制御
- テスト容易性の向上（各ステップを独立してテスト可能）
- パフォーマンスのボトルネック特定が容易

## 概要・特徴

### 概要

Pipelineパターンは、複雑なデータ処理フローを一連の独立したステップに分割し、それらを順次実行する設計パターンです。各ステップは前のステップの出力を入力として受け取り、処理結果を次のステップに渡します。このパターンは、特にデータ変換、ETL処理、ビルドプロセスなどで広く使用されています。

### 特徴

#### 単方向処理
Pipelineパターンの基本的な特性は、データが一方向に流れることです。各処理ステップは前のステップから出力を受け取り、処理した結果を次のステップに渡します。この単方向の流れにより、処理の依存関係が明確になり、システムの理解と保守が容易になります。例えば、画像処理パイプラインでは、画像の読み込み→サイズ変更→フィルタ適用→圧縮→保存という一連の流れで処理が進みます。この単方向の流れは、デバッグや問題追跡も容易にします。ある時点でのデータの状態を確認することで、どのステップで問題が発生したかを特定しやすくなります。また、単方向の流れは処理の予測可能性を高め、副作用の範囲を限定することにも役立ちます。データ変換や集計処理など、入力から出力への明確な変換が必要なアプリケーションでは、この特性が特に有効です。

#### 入出力の明確化
Pipelineパターンでは、各ステップの入力と出力が明確に定義されます。これにより、ステップ間のインターフェースが標準化され、ステップの交換や再構成が容易になります。入出力の明確化は、強力な型システムを持つ言語（TypeScriptなど）では特に効果的で、コンパイル時にステップ間の互換性を検証できます。例えば、テキスト処理パイプラインでは、テキスト分割ステップの出力が単語配列であると明確に定義され、次の単語フィルタリングステップはその配列を入力として期待します。この明確な契約により、開発者は各ステップの実装詳細を知らなくても、パイプライン全体を理解し操作できます。また、この特性はインクリメンタルな開発を促進し、パイプラインを段階的に拡張できるようになります。さらに、明確な入出力定義はテストを容易にし、各ステップを独立してユニットテストできるようになります。

#### 独立性
Pipelineパターンでは、各処理ステップが互いに独立して実装されます。各ステップは自己完結した機能単位であり、他のステップの内部実装に依存しません。この独立性により、異なる開発者やチームが並行して異なるステップを開発できるようになり、開発効率が向上します。例えば、データ分析パイプラインでは、データ収集、前処理、分析、可視化といった各ステップを異なる専門家が担当できます。ステップの独立性は、メンテナンスと進化の観点からも有利です。特定のステップのロジックを変更しても、入出力インターフェースを維持する限り、他のステップへの影響は最小限に抑えられます。また、新しい要件や技術の変化に応じて、パイプラインの一部だけを更新することも容易になります。ステップの独立性はコードの分離と責任の明確化をもたらし、結果として保守性の高いシステム設計につながります。

#### 再利用性
Pipelineパターンの大きな利点の一つは、処理ステップの再利用性が高いことです。標準化された入出力インターフェースを持つステップは、異なるパイプラインで容易に再利用できます。これにより、コードの重複が減少し、開発効率が向上します。例えば、ログ処理のためのパース、フィルタリング、集計などのステップは、さまざまなログ分析パイプラインで再利用できます。ステップのライブラリやカタログを構築することで、新しいパイプラインの迅速な構築が可能になります。再利用性の高いステップは、時間をかけて洗練され、堅牢になる傾向があり、信頼性の向上にも寄与します。また、再利用可能なステップの作成は、ベストプラクティスの標準化と知識の共有を促進します。多様なユースケースに対応できる汎用的なステップを設計することで、組織全体の開発効率と一貫性を高めることができます。

#### 柔軟性
Pipelineパターンは、構成の柔軟性という大きな利点を提供します。ステップの追加、削除、並べ替え、置換などを容易に行うことができ、パイプラインの動作を変更できます。この柔軟性により、変化する要件に対して迅速に対応できるようになります。例えば、データ処理パイプラインに新しい検証ステップを追加したり、既存の変換ステップをより効率的な実装に置き換えたりすることが容易です。ランタイム構成をサポートする実装では、設定ファイルやUIを通じてパイプラインの構造を動的に変更することも可能です。これにより、コードの再コンパイルや再デプロイなしにシステムの振る舞いを調整できます。この柔軟性は、特に要件が頻繁に変化するプロジェクトや、異なるユースケースに対して類似した処理を提供する必要があるシステムで価値を発揮します。また、A/Bテストやプロトタイピングなど、異なる処理アプローチを実験する際にも役立ちます。

#### 並列処理
Pipelineパターンは、自然な並列処理の機会を提供します。独立したステップは、データ依存関係がない限り、並列に実行できます。また、ストリーミングパイプラインでは、異なるデータチャンクが異なるステップで同時に処理される「パイプライン並列性」を実現できます。この並列処理能力により、マルチコアプロセッサや分散システムでのパフォーマンスを最大化できます。例えば、画像処理パイプラインでは、複数の画像を並列に処理したり、大きな画像を分割して並列処理したりすることで、処理時間を大幅に短縮できます。現代の並列処理フレームワークやアクターモデルを採用したシステムでは、この特性を活用した高スケーラブルなパイプライン実装が可能です。並列処理は特に大量データ処理や計算集約型のアプリケーションで重要であり、Pipelineパターンはそのような要件に自然に対応できます。ただし、並列処理の導入には同期や競合状態の管理など、追加の複雑さが伴うこともあるため、適切な設計が重要です。

#### エラー処理
Pipelineパターンでは、各ステップでエラーが発生する可能性があり、それらを適切に処理するメカニズムが重要です。エラー処理の方法としては、エラーが発生したアイテムをスキップして処理を継続する、エラーログを記録して後で分析する、リトライメカニズムを導入する、あるいはエラーが発生した場合に処理全体を中止するなど、様々なアプローチがあります。例えば、バッチ処理パイプラインでは、個々のレコード処理エラーをログに記録し、成功したレコードの処理を継続することで、部分的な成功を実現できます。また、トランザクション的な性質が必要な場合は、ロールバックメカニズムを導入して、パイプライン全体の一貫性を保つことも可能です。エラー通知や監視との統合も重要な側面であり、重大なエラーが発生した際に管理者に通知することで、問題の早期発見と対応が可能になります。適切なエラー処理戦略は、パイプラインの堅牢性と信頼性を大きく向上させ、特に長時間実行される処理や重要なビジネスプロセスでは不可欠です。

#### 進捗管理
Pipelineパターンは、処理の進捗状況を監視し報告するためのフレームワークを自然に提供します。各ステップでの処理状況や全体の進行状況を追跡することで、長時間実行される処理の透明性が向上します。例えば、大規模データ移行パイプラインでは、処理済みレコード数、現在のステップ、推定残り時間などの情報を提供できます。進捗情報はログ記録、ダッシュボード表示、またはイベント通知などの形で提供され、運用チームや利害関係者に重要な可視性をもたらします。また、進捗管理は問題の早期発見にも役立ちます。特定のステップで予想よりも時間がかかっている場合、それはパフォーマンス問題の兆候かもしれません。高度な実装では、進捗データを収集・分析して、将来のパイプライン実行の見積もりを改善したり、リソース割り当てを最適化したりすることも可能です。定期的な進捗更新は、特にユーザーが結果を待っているインタラクティブなシステムでは、優れたユーザーエクスペリエンスを提供するためにも重要です。

#### パフォーマンス最適化
Pipelineパターンの分割された構造は、パフォーマンス最適化の機会を提供します。各ステップを個別にプロファイリングし、ボトルネックを特定して改善することができます。さらに、ステップ間のデータ転送やバッファリング戦略を最適化することも可能です。例えば、メモリ使用量が多いステップを識別し、ストリーミング処理や効率的なデータ構造の使用などで改善できます。また、一部のステップのみを高性能ハードウェア（GPUなど）で実行したり、計算コストの高いステップを分散処理システムに移行したりといった選択的な最適化も可能です。パイプラインの構造を変更することでもパフォーマンスを向上できます。例えば、頻繁に失敗するバリデーションステップを早い段階に移動させることで、後続の高コスト処理を回避できます。パフォーマンスのボトルネックが変化しても、該当するステップのみを最適化または置換できるため、システム全体を再設計する必要がありません。この特性により、継続的なパフォーマンス改善が容易になり、システムの長期的な進化をサポートします。

## 類似パターンとの比較

- [Chain of Responsibility (責任の連鎖)](chain-of-responsibility.md): Pipelineは単方向のデータ変換に注力し、これに対してChain of Responsibilityは条件付きの処理委譲に焦点を当てています。
- [Middleware (ミドルウェア)](middleware.md): Pipelineは単方向のデータ変換に注力し、これに対してMiddlewareは双方向処理とコンテキスト共有に焦点を当てています。
- [Builder (ビルダー)](builder.md): Pipelineはデータ変換の順次実行に注力し、これに対してBuilderはオブジェクトの段階的な構築に焦点を当てています。
- [Decorator (デコレーター)](decorator.md): Pipelineはデータ変換の連鎖に注力し、これに対してDecoratorは機能の動的な追加に焦点を当てています。

## 利用されているライブラリ／フレームワークの事例

- [Node.js Stream](https://nodejs.org/api/stream.html): ストリーム処理のパイプライン
- [Gulp](https://gulpjs.com/): ビルドプロセスのパイプライン
- [Apache Kafka](https://kafka.apache.org/): データストリーム処理
- [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/): CI/CDパイプライン
- [scikit-learn Pipeline](https://scikit-learn.org/stable/modules/generated/sklearn.pipeline.Pipeline.html): 機械学習の処理パイプライン

## 解説ページリンク

- [Martin Fowler - Pipes and Filters](https://martinfowler.com/articles/collection-pipeline/)
- [Microsoft - Pipeline Pattern](https://docs.microsoft.com/azure/architecture/patterns/pipes-and-filters)
- [Baeldung - Pipeline Pattern in Java](https://www.baeldung.com/pipeline-pattern-java)
- [Design Patterns - Pipeline Pattern](https://www.oreilly.com/library/view/design-patterns-in/9781785888038/ch07.html)

## コード例

### Before:

単一の関数で全ての処理を行う実装

```typescript
interface ImageData {
  width: number;
  height: number;
  format: string;
  data: Buffer;
}

class ImageProcessor {
  async processImage(inputImage: ImageData): Promise<ImageData> {
    try {
      console.log("画像処理開始");

      // 画像のバリデーション
      if (!inputImage.data || inputImage.data.length === 0) {
        throw new Error("無効な画像データです");
      }
      if (inputImage.width <= 0 || inputImage.height <= 0) {
        throw new Error("無効な画像サイズです");
      }

      // フォーマット変換
      let processedData = inputImage.data;
      if (inputImage.format !== "jpeg") {
        processedData = await this.convertFormat(processedData, "jpeg");
      }

      // リサイズ
      if (inputImage.width > 1920 || inputImage.height > 1080) {
        processedData = await this.resize(processedData, 1920, 1080);
      }

      // 画質最適化
      processedData = await this.optimize(processedData);

      // メタデータ追加
      processedData = await this.addMetadata(processedData, {
        processed: new Date().toISOString()
      });

      console.log("画像処理完了");

      return {
        width: 1920,
        height: 1080,
        format: "jpeg",
        data: processedData
      };
    } catch (error) {
      console.error("画像処理エラー:", error);
      throw error;
    }
  }

  private async convertFormat(data: Buffer, format: string): Promise<Buffer> {
    console.log(`フォーマットを${format}に変換`);
    return data; // 実際の変換処理
  }

  private async resize(data: Buffer, width: number, height: number): Promise<Buffer> {
    console.log(`サイズを${width}x${height}に変更`);
    return data; // 実際のリサイズ処理
  }

  private async optimize(data: Buffer): Promise<Buffer> {
    console.log("画質を最適化");
    return data; // 実際の最適化処理
  }

  private async addMetadata(data: Buffer, metadata: Record<string, string>): Promise<Buffer> {
    console.log("メタデータを追加:", metadata);
    return data; // 実際のメタデータ追加処理
  }
}

// 使用例
async function example() {
  const processor = new ImageProcessor();
  const inputImage: ImageData = {
    width: 3840,
    height: 2160,
    format: "png",
    data: Buffer.from("dummy image data")
  };

  try {
    const result = await processor.processImage(inputImage);
    console.log("処理結果:", result);
  } catch (error) {
    console.error("エラー:", error);
  }
}

example();
```

### After:

Pipeline パターンを利用した実装

```typescript
interface ImageData {
  width: number;
  height: number;
  format: string;
  data: Buffer;
  metadata?: Record<string, string>;
  processingTime?: number;
  error?: Error;
}

interface PipelineStep<T> {
  execute(input: T): Promise<T>;
  name: string;
}

class Pipeline<T> {
  private steps: PipelineStep<T>[] = [];
  private errorHandlers: Array<(error: Error, data: T) => Promise<T>> = [];
  private progressHandlers: Array<(step: string, progress: number) => void> = [];

  addStep(step: PipelineStep<T>): Pipeline<T> {
    this.steps.push(step);
    return this;
  }

  onError(handler: (error: Error, data: T) => Promise<T>): Pipeline<T> {
    this.errorHandlers.push(handler);
    return this;
  }

  onProgress(handler: (step: string, progress: number) => void): Pipeline<T> {
    this.progressHandlers.push(handler);
    return this;
  }

  async execute(input: T): Promise<T> {
    let current = input;
    const startTime = Date.now();

    for (let i = 0; i < this.steps.length; i++) {
      const step = this.steps[i];
      try {
        console.log(`ステップ実行: ${step.name}`);
        this.notifyProgress(step.name, (i / this.steps.length) * 100);
        
        const stepStartTime = Date.now();
        current = await step.execute(current);
        const stepTime = Date.now() - stepStartTime;
        
        console.log(`ステップ完了: ${step.name} (${stepTime}ms)`);
      } catch (error) {
        console.error(`ステップでエラー発生: ${step.name}`, error);
        
        // エラーハンドラを実行
        for (const handler of this.errorHandlers) {
          try {
            current = await handler(error as Error, current);
          } catch (handlerError) {
            console.error("エラーハンドラでエラー発生:", handlerError);
          }
        }

        if (current.error) {
          break; // エラーが設定された場合は処理を中断
        }
      }
    }

    const totalTime = Date.now() - startTime;
    console.log(`パイプライン完了 (${totalTime}ms)`);
    this.notifyProgress("完了", 100);

    return {
      ...current,
      processingTime: totalTime
    };
  }

  private notifyProgress(step: string, progress: number): void {
    for (const handler of this.progressHandlers) {
      try {
        handler(step, progress);
      } catch (error) {
        console.error("進捗通知でエラー発生:", error);
      }
    }
  }
}

// バリデーションステップ
class ValidationStep implements PipelineStep<ImageData> {
  name = "バリデーション";

  async execute(input: ImageData): Promise<ImageData> {
    if (!input.data || input.data.length === 0) {
      throw new Error("無効な画像データです");
    }
    if (input.width <= 0 || input.height <= 0) {
      throw new Error("無効な画像サイズです");
    }
    return input;
  }
}

// フォーマット変換ステップ
class FormatConversionStep implements PipelineStep<ImageData> {
  name = "フォーマット変換";
  private targetFormat: string;

  constructor(targetFormat: string) {
    this.targetFormat = targetFormat;
  }

  async execute(input: ImageData): Promise<ImageData> {
    if (input.format === this.targetFormat) {
      return input;
    }

    return {
      ...input,
      format: this.targetFormat,
      data: await this.convertFormat(input.data, this.targetFormat)
    };
  }

  private async convertFormat(data: Buffer, format: string): Promise<Buffer> {
    console.log(`フォーマットを${format}に変換`);
    return data; // 実際の変換処理
  }
}

// リサイズステップ
class ResizeStep implements PipelineStep<ImageData> {
  name = "リサイズ";
  private maxWidth: number;
  private maxHeight: number;

  constructor(maxWidth: number, maxHeight: number) {
    this.maxWidth = maxWidth;
    this.maxHeight = maxHeight;
  }

  async execute(input: ImageData): Promise<ImageData> {
    if (input.width <= this.maxWidth && input.height <= this.maxHeight) {
      return input;
    }

    // アスペクト比を維持したリサイズ
    const ratio = Math.min(
      this.maxWidth / input.width,
      this.maxHeight / input.height
    );
    const newWidth = Math.round(input.width * ratio);
    const newHeight = Math.round(input.height * ratio);

    return {
      ...input,
      width: newWidth,
      height: newHeight,
      data: await this.resize(input.data, newWidth, newHeight)
    };
  }

  private async resize(data: Buffer, width: number, height: number): Promise<Buffer> {
    console.log(`サイズを${width}x${height}に変更`);
    return data; // 実際のリサイズ処理
  }
}

// 最適化ステップ
class OptimizationStep implements PipelineStep<ImageData> {
  name = "最適化";

  async execute(input: ImageData): Promise<ImageData> {
    return {
      ...input,
      data: await this.optimize(input.data)
    };
  }

  private async optimize(data: Buffer): Promise<Buffer> {
    console.log("画質を最適化");
    return data; // 実際の最適化処理
  }
}

// メタデータ追加ステップ
class MetadataStep implements PipelineStep<ImageData> {
  name = "メタデータ追加";

  async execute(input: ImageData): Promise<ImageData> {
    const metadata = {
      ...(input.metadata || {}),
      processed: new Date().toISOString(),
      processor: "ImagePipeline v1.0"
    };

    return {
      ...input,
      metadata,
      data: await this.addMetadata(input.data, metadata)
    };
  }

  private async addMetadata(data: Buffer, metadata: Record<string, string>): Promise<Buffer> {
    console.log("メタデータを追加:", metadata);
    return data; // 実際のメタデータ追加処理
  }
}

// 使用例
async function example() {
  // パイプラインの構築
  const pipeline = new Pipeline<ImageData>()
    .addStep(new ValidationStep())
    .addStep(new FormatConversionStep("jpeg"))
    .addStep(new ResizeStep(1920, 1080))
    .addStep(new OptimizationStep())
    .addStep(new MetadataStep());

  // エラーハンドラの追加
  pipeline.onError(async (error, data) => {
    console.error("エラーを検知:", error);
    return {
      ...data,
      error,
      metadata: {
        ...(data.metadata || {}),
        error: error.message,
        errorTime: new Date().toISOString()
      }
    };
  });

  // 進捗ハンドラの追加
  pipeline.onProgress((step, progress) => {
    console.log(`進捗: ${step} (${Math.round(progress)}%)`);
  });

  // パイプラインの実行
  const inputImage: ImageData = {
    width: 3840,
    height: 2160,
    format: "png",
    data: Buffer.from("dummy image data")
  };

  try {
    const result = await pipeline.execute(inputImage);
    console.log("処理結果:", {
      width: result.width,
      height: result.height,
      format: result.format,
      metadata: result.metadata,
      processingTime: result.processingTime,
      error: result.error
    });
  } catch (error) {
    console.error("パイプライン実行エラー:", error);
  }
}

example();
```
