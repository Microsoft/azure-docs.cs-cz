---
title: Vytvoření předpovědi s modelem ONNX AutoML v .NET
description: Naučte se, jak vytvořit předpovědi pomocí modelu AutoML ONNX v .NET s ML.NET
titleSuffix: Azure Machine Learning
author: luisquintanilla
ms.author: luquinta
ms.date: 10/30/2020
ms.topic: conceptual
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.custom: how-to
ms.openlocfilehash: eb1a3c1f306b6941987d0981967cf5d096a68735
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93133568"
---
# <a name="make-predictions-with-an-automl-onnx-model-in-net"></a>Vytvoření předpovědi s modelem ONNX AutoML v .NET

V tomto článku se dozvíte, jak pomocí automatizovaného modelu AutoML (neuronové Network Exchange) vytvořit předpovědi v konzolové aplikaci .NET Core v C# s ML.NET.

[Ml.NET](https://docs.microsoft.com/dotnet/machine-learning/) je open source architektura pro strojové učení pro ekosystém .NET, která umožňuje vytvářet vlastní modely strojového učení pomocí přístupu k prvnímu kódu v jazyce C# nebo F # a také prostřednictvím nástrojů s nízkým kódem, jako je [tvůrce modelů](https://docs.microsoft.com/dotnet/machine-learning/automate-training-with-model-builder) a [ml.NET CLI](https://docs.microsoft.com/dotnet/machine-learning/automate-training-with-cli). Rozhraní je také rozšiřitelné a umožňuje využít další oblíbené architektury strojového učení, jako je TensorFlow a ONNX.

ONNX je open source formát pro modely AI. ONNX podporuje interoperabilitu mezi platformami. To znamená, že můžete model vytvořit v jedné z mnoha oblíbených rozhraní pro strojové učení, jako je PyTorch, převést ho do formátu ONNX a spotřebovat model ONNX v jiném rozhraní jako ML.NET. Další informace najdete na [webu ONNX](https://onnx.ai/).

## <a name="prerequisites"></a>Předpoklady

- [.NET Core SDK 3,1 nebo vyšší](https://dotnet.microsoft.com/download)
- Textový editor nebo integrované vývojové prostředí (např. [Visual Studio](https://visualstudio.microsoft.com/vs/) nebo [Visual Studio Code](https://code.visualstudio.com/Download))
- ONNX model. Další informace o tom, jak se naučit model AutoML ONNX, najdete v následujícím [poznámkovém bloku klasifikace marketingu v bankách](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb).
- [Netron](https://github.com/lutzroeder/netron) (volitelné)

## <a name="create-a-c-console-application"></a>Vytvoření konzolové aplikace v jazyce C#

V této ukázce použijete .NET Core CLI k sestavení aplikace, ale můžete provádět stejné úlohy pomocí sady Visual Studio. Přečtěte si další informace o [.NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/).

1. Otevřete terminál a vytvořte novou konzolovou aplikaci C# .NET Core. V tomto příkladu je název aplikace `AutoMLONNXConsoleApp` . Vytvoří se adresář se stejným názvem a obsahem vaší aplikace.

    ```dotnetcli
    dotnet new console -o AutoMLONNXConsoleApp
    ```

1. V terminálu přejděte do adresáře *AutoMLONNXConsoleApp* .

    ```bash
    cd AutoMLONNXConsoleApp
    ```

## <a name="add-software-packages"></a>Přidat softwarové balíčky

1. Pomocí .NET Core CLI nainstalujte balíčky NuGet **Microsoft.ml** , **Microsoft. ml. OnnxRuntime** a **Microsoft. ml. OnnxTransformer** .

    ```dotnetcli
    dotnet add package Microsoft.ML
    dotnet add package Microsoft.ML.OnnxRuntime
    dotnet add package Microsoft.ML.OnnxTransformer
    ```

    Tyto balíčky obsahují závislosti potřebné k použití modelu ONNX v aplikaci .NET. ML.NET poskytuje rozhraní API, které používá [modul runtime ONNX](https://github.com/Microsoft/onnxruntime) pro předpovědi.

1. Otevřete soubor *program.cs* a přidejte následující `using` příkazy v horní části, abyste odkazovali na příslušné balíčky.

    ```csharp
    using System.Linq;
    using Microsoft.ML;
    using Microsoft.ML.Data;
    using Microsoft.ML.Transforms.Onnx;
    ```

## <a name="add-a-reference-to-the-onnx-model"></a>Přidat odkaz na model ONNX

Způsob, jak Konzolová aplikace přistupovat k ONNX modelu, je přidat do výstupního adresáře sestavení.  Další informace o běžných položkách MSBuild najdete v [Průvodci MSBuild](https://docs.microsoft.com/visualstudio/msbuild/common-msbuild-project-items).

Do vaší aplikace přidejte odkaz na soubor modelu ONNX.

1. Zkopírujte model ONNX do kořenového adresáře *AutoMLONNXConsoleApp* vaší aplikace.
1. Otevřete soubor *AutoMLONNXConsoleApp. csproj* a přidejte do uzlu následující obsah `Project` .

    ```xml
    <ItemGroup>
        <None Include="automl-model.onnx">
            <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
        </None>
    </ItemGroup>
    ```

    V tomto případě je název souboru modelu ONNX *automl-model. ONNX* .

1. Otevřete soubor *program.cs* a přidejte do třídy následující řádek `Program` .

    ```csharp
    static string ONNX_MODEL_PATH = "automl-model.onnx";
    ```

## <a name="initialize-mlcontext"></a>Inicializovat MLContext

V rámci `Main` metody vaší `Program` třídy vytvořte novou instanci [`MLContext`](xref:Microsoft.ML.MLContext) .

```csharp
MLContext mlContext = new MLContext();
```

[`MLContext`](xref:Microsoft.ML.MLContext)Třída je výchozím bodem pro všechny operace ml.NET a inicializace `mlContext` vytvoří nové prostředí ml.NET, které se dá sdílet napříč životním cyklem modelu. Je podobný, koncepčně, aby DbContext v Entity Framework.

## <a name="define-the-model-data-schema"></a>Definování schématu dat modelu

Váš model očekává vaše vstupní a výstupní data v konkrétním formátu. ML.NET umožňuje definovat formát dat prostřednictvím tříd. Někdy může být možné, že už víte, jaký formát vypadá. V případech, kdy neznáte formát dat, můžete ke kontrole modelu ONNX použít nástroje, jako je Netron.

Model použitý v tomto příkladu používá data z datové sady NYC TLC taxislužby Trip. Ukázka dat se může zobrazit níže:

|vendor_id|rate_code|passenger_count|trip_time_in_secs|trip_distance|payment_type|fare_amount|
|---|---|---|---|---|---|---|
|VTS|1|1|1140|3,75|CRD|15,5|
|VTS|1|1|480|2,72|CRD|10,0|
|VTS|1|1|1680|7,8|CSH|26,5|

### <a name="inspect-the-onnx-model-optional"></a>Kontrola modelu ONNX (volitelné)

Pomocí nástroje, jako je Netron, zkontrolujte vstupy a výstupy modelu.

1. Otevřete Netron.
1. V horním řádku nabídek vyberte **soubor > otevřít** a pomocí prohlížeče souborů vyberte svůj model.
1. Otevře se váš model. Například struktura modelu *automl-model. Onnx* vypadá takto:

    :::image type="content" source="media/how-to-use-automl-onnx-model-dotnet/netron-automl-onnx-model.png" alt-text="Netron AutoML – model ONNX":::

1. Vyberte poslední uzel v dolní části grafu ( `variable_out1` v tomto případě), chcete-li zobrazit metadata modelu. Vstupy a výstupy na bočním panelu ukazují, jak se v modelu očekávají vstupy, výstupy a datové typy. Tyto informace slouží k definování schématu vstupu a výstupu modelu.

### <a name="define-model-input-schema"></a>Definovat schéma vstupu modelu

V souboru Program.cs vytvořte novou třídu `OnnxInput` s názvem s následujícími vlastnostmi *Program.cs* .

```csharp
public class OnnxInput
{
    [ColumnName("vendor_id")]
    public string VendorId { get; set; }

    [ColumnName("rate_code"),OnnxMapType(typeof(Int64),typeof(Single))]
    public Int64 RateCode { get; set; }

    [ColumnName("passenger_count"), OnnxMapType(typeof(Int64), typeof(Single))]
    public Int64 PassengerCount { get; set; }

    [ColumnName("trip_time_in_secs"), OnnxMapType(typeof(Int64), typeof(Single))]
    public Int64 TripTimeInSecs { get; set; }

    [ColumnName("trip_distance")]
    public float TripDistance { get; set; }

    [ColumnName("payment_type")]
    public string PaymentType { get; set; }
}
```

Každá z vlastností je mapována na sloupec v datové sadě. Vlastnosti jsou dále opatřeny poznámkami s atributy.

[`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute)Atribut umožňuje určit, jak by měl ml.NET odkazovat na sloupec při práci na datech. Například Přestože `TripDistance` vlastnost dodržuje standardní konvence pro pojmenování rozhraní .NET, model zná pouze sloupec nebo funkci známou jako `trip_distance` . Pro vyřešení tohoto rozdílu v pojmenování [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) atribut mapuje `TripDistance` vlastnost na sloupec nebo funkci podle názvu `trip_distance` .
  
Pro číselné hodnoty ML.NET funguje pouze na [`Single`](xref:System.Single) hodnotových typech. Původní datový typ některých sloupců je však celých čísel. [`OnnxMapType`](xref:Microsoft.ML.Transforms.Onnx.OnnxMapTypeAttribute)Atribut mapuje typy mezi ONNX a ml.NET.

Další informace o atributech dat naleznete v [příručce ml.NET Load data Guide](https://docs.microsoft.com/dotnet/machine-learning/how-to-guides/load-data-ml-net).

### <a name="define-model-output-schema"></a>Definování schématu výstupu modelu

Po zpracování dat se vytvoří výstup určitého formátu. Definujte schéma výstup dat. V souboru Program.cs vytvořte novou třídu `OnnxOutput` s názvem s následujícími vlastnostmi *Program.cs* .

```csharp
public class OnnxOutput
{
    [ColumnName("variable_out1")]
    public float[] PredictedFare { get; set; }
}
```

Podobně jako u `OnnxInput` použijte [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) atribut k namapování `variable_out1` výstupu na výstižnější název `PredictedFare` .

## <a name="define-a-prediction-pipeline"></a>Definování kanálu předpovědi

Kanál v ML.NET je obvykle série zřetězených transformací, které pracují se vstupními daty, aby vznikl výstup. Další informace o transformaci dat najdete v [příručce k transformaci dat ml.NET](https://docs.microsoft.com/dotnet/machine-learning/resources/transforms).

1. Vytvoří novou metodu volanou `GetPredictionPipeline` uvnitř `Program` třídy.

    ```csharp
    static ITransformer GetPredictionPipeline(MLContext mlContext)
    {

    }
    ```

1. Zadejte název vstupních a výstupních sloupců. Do metody přidejte následující kód `GetPredictionPipeline` .

    ```csharp
    var inputColumns = new string []
    {
        "vendor_id", "rate_code", "passenger_count", "trip_time_in_secs", "trip_distance", "payment_type"
    };

    var outputColumns = new string [] { "variable_out1" };
    ```

1. Definujte svůj kanál. [`IEstimator`](xref:Microsoft.ML.IEstimator%601)Poskytuje podrobný plán operací, vstupních a výstupních schémat vašeho kanálu.

    ```csharp
    var onnxPredictionPipeline =
        mlContext
            .Transforms
            .ApplyOnnxModel(
                outputColumnNames: outputColumns,
                inputColumnNames: inputColumns,
                ONNX_MODEL_PATH);
    ```

    V tomto případě [`ApplyOnnxModel`](xref:Microsoft.ML.OnnxCatalog.ApplyOnnxModel%2A) je jediná transformace v kanálu, která přebírá názvy vstupních a výstupních sloupců a také cestu k souboru modelu ONNX.

1. [`IEstimator`](xref:Microsoft.ML.IEstimator%601)Definuje jenom sadu operací, které se mají použít na vaše data. Co funguje s Vašimi daty, se říká [`ITransformer`](xref:Microsoft.ML.ITransformer) . Pomocí `Fit` metody ji vytvořte z vaší `onnxPredictionPipeline` .

    ```csharp
    var emptyDv = mlContext.Data.LoadFromEnumerable(new OnnxInput[] {});

    return onnxPredictionPipeline.Fit(emptyDv);
    ```

    [`Fit`](xref:Microsoft.ML.IEstimator%601.Fit%2A)Metoda očekává [`IDataView`](xref:Microsoft.ML.IDataView) jako vstup k provedení operací. [`IDataView`](xref:Microsoft.ML.IDataView)Je způsob, jak znázornit data v ml.NET pomocí tabulkového formátu. Vzhledem k tomu, že se v tomto případě kanál používá jenom pro předpovědi, můžete zadat prázdné a [`IDataView`](xref:Microsoft.ML.IDataView) poskytnout tak [`ITransformer`](xref:Microsoft.ML.ITransformer) potřebné informace o schématu vstupu a výstupu. [`ITransformer`](xref:Microsoft.ML.ITransformer)Pak se vrátí k dalšímu použití ve vaší aplikaci.

    > [!TIP]
    > V této ukázce je kanál definován a používán ve stejné aplikaci. Doporučuje se ale použít samostatné aplikace k definování a použití kanálu k vytvoření předpovědi. V ML.NET mohou být vaše kanály serializovány a uloženy pro další použití v jiných aplikacích pro koncové uživatele .NET. ML.NET podporuje různé cíle nasazení, jako jsou desktopové aplikace, webové služby, aplikace WebAssembly * a spousta dalších. Další informace o ukládání kanálů najdete v [průvodci ml.NET Uložit a načíst vyškolené modely](https://docs.microsoft.com/dotnet/machine-learning/how-to-guides/save-load-machine-learning-models-ml-net).
    >
    > * WebAssembly se podporuje jenom v .NET Core 5 nebo novějších.

1. Uvnitř `Main` metody zavolejte `GetPredictionPipeline` metodu s požadovanými parametry.

    ```csharp
    var onnxPredictionPipeline = GetPredictionPipeline(mlContext);
    ```

## <a name="use-the-model-to-make-predictions"></a>Použití modelu k vytvoření předpovědi

Teď, když máte kanál, je čas ho použít k vytvoření předpovědi. ML.NET poskytuje praktické rozhraní API pro vytváření předpovědiů v jedné instanci dat s názvem [`PredictionEngine`](xref:Microsoft.ML.PredictionEngine%602) .

1. Uvnitř `Main` metody vytvořte [`PredictionEngine`](xref:Microsoft.ML.PredictionEngine%602) pomocí [`CreatePredictionEngine`](xref:Microsoft.ML.ModelOperationsCatalog.CreatePredictionEngine%2A) metody.

    ```csharp
    var onnxPredictionEngine = mlContext.Model.CreatePredictionEngine<OnnxInput, OnnxOutput>(onnxPredictionPipeline);
    ```

1. Vytvořte vstup testovacích dat.

    ```csharp
    var testInput = new OnnxInput
    {
        VendorId = "CMT",
        RateCode = 1,
        PassengerCount = 1,
        TripTimeInSecs = 1271,
        TripDistance = 3.8f,
        PaymentType = "CRD"
    };
    ```

1. Použijte `predictionEngine` k vytvoření předpovědi v závislosti na nových `testInput` datech pomocí [`Predict`](xref:Microsoft.ML.PredictionEngineBase%602.Predict%2A) metody.

    ```csharp
    var prediction = onnxPredictionEngine.Predict(testInput);
    ```

1. Výstup výsledku předpovědi do konzoly.

    ```csharp
    Console.WriteLine($"Predicted Fare: {prediction.PredictedFare.First()}");
    ```

1. Použijte .NET Core CLI ke spuštění aplikace.

    ```dotnetcli
    dotnet run
    ```

    Výsledek by měl vypadat podobně jako následující výstup:

    ```text
    Predicted Fare: 15.621523
    ```

Další informace o tom, jak vytvářet předpovědi v ML.NET, najdete v tématu [použití modelu k vytvoření příručky předpovědi](https://docs.microsoft.com/dotnet/machine-learning/how-to-guides/machine-learning-model-predictions-ml-net).

## <a name="next-steps"></a>Další kroky

- [Nasazení modelu jako ASP.NET Core webového rozhraní API](https://docs.microsoft.com/dotnet/machine-learning/how-to-guides/serve-model-web-api-ml-net)
- [Nasazení modelu jako bez serveru Azure Functions .NET](https://docs.microsoft.com/dotnet/machine-learning/how-to-guides/serve-model-serverless-azure-functions-ml-net)
