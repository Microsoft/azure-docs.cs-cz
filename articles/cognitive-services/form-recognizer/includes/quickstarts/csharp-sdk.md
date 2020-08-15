---
title: 'Rychlý Start: Klientská knihovna pro rozpoznávání formulářů pro .NET'
description: V tomto rychlém startu začněte s klientskou knihovnou pro rozpoznávání formulářů pro .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/06/2020
ms.author: pafarley
ms.openlocfilehash: b56c3dfc755f1b0368ef93d21cec18e7fc14a64b
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2020
ms.locfileid: "88246520"
---
[Referenční dokumentace](https://docs.microsoft.com/dotnet/api/overview/azure/ai.formrecognizer-readme-pre)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src)  |  [Balíček (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer)  |  [Ukázky](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services).
* Objekt blob Azure Storage, který obsahuje sadu školicích dat. Tipy a možnosti pro sestavení sady školicích dat najdete v tématu [Vytvoření školicích dat sady pro vlastní model](../../build-training-data-set.md) . Pro účely tohoto rychlého startu můžete použít soubory ve složce **výuka** [ukázkové sady dat](https://go.microsoft.com/fwlink/?linkid=2090451).
* Aktuální verze [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Nastavení

### <a name="create-a-form-recognizer-azure-resource"></a>Vytvoření prostředku Azure pro rozpoznávání formulářů

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>Vytvoření proměnných prostředí

[!INCLUDE [environment-variables](../environment-variables.md)]

### <a name="create-a-new-c-application"></a>Vytvoření nové aplikace v C#

V okně konzoly (například cmd, PowerShell nebo bash) použijte `dotnet new` příkaz k vytvoření nové aplikace konzoly s názvem `formrecognizer-quickstart` . Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: _program.cs_. 

```console
dotnet new console -n formrecognizer-quickstart
```

Změňte adresář na nově vytvořenou složku aplikace. Pak sestavte aplikaci pomocí:

```console
dotnet build
```

Výstup sestavení by neměl obsahovat žádná upozornění ani chyby. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

V adresáři projektu otevřete soubor *program.cs* v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE). Přidejte následující `using` direktivy:

```csharp
using Azure.AI.FormRecognizer;
using Azure.AI.FormRecognizer.Models;
using Azure.AI.FormRecognizer.Training;

using System;
using System.IO;
using System.Threading.Tasks;
```

Pak do metody **Main** aplikace přidejte následující kód. Tuto asynchronní úlohu budete definovat později. 

```csharp
static void Main(string[] args)
{
    var t1 = RunFormRecognizerClient();

    Task.WaitAll(t1);
}
```

### <a name="install-the-client-library"></a>Instalace klientské knihovny

V adresáři aplikace nainstalujte klientskou knihovnu pro rozpoznávání formulářů pro rozhraní .NET pomocí následujícího příkazu:

```console
dotnet add package Azure.AI.FormRecognizer --version 1.0.0-preview.3
```

Pokud používáte integrované vývojové prostředí (IDE) sady Visual Studio, je knihovna klienta k dispozici jako balíček NuGet ke stažení.


<!-- Objet model TBD -->



## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí klientské knihovny pro rozpoznávání formulářů pro .NET:

* [Ověření klienta](#authenticate-the-client)
* [Rozpoznávání obsahu formuláře](#recognize-form-content)
* [Rozpoznávání příjmů](#recognize-receipts)
* [Trénování vlastního modelu](#train-a-custom-model)
* [Analýza formulářů pomocí vlastního modelu](#analyze-forms-with-a-custom-model)
* [Správa vlastních modelů](#manage-your-custom-models)


## <a name="authenticate-the-client"></a>Ověření klienta

Pod `Main` metodou definujte úkol, na který je odkazováno v `Main` . Tady ověříte dva klientské objekty pomocí proměnných předplatného, které jste definovali výše. Použijete objekt **AzureKeyCredential** , takže v případě potřeby můžete aktualizovat klíč rozhraní API bez vytváření nových objektů klienta.

```csharp
static async Task RunFormRecognizerClient()
{ 
    string endpoint = Environment.GetEnvironmentVariable(
        "FORM_RECOGNIZER_ENDPOINT");
    string apiKey = Environment.GetEnvironmentVariable(
        "FORM_RECOGNIZER_KEY");
    var credential = new AzureKeyCredential(apiKey);
    
    var trainingClient = new FormTrainingClient(new Uri(endpoint), credential);
    var recognizerClient = new FormRecognizerClient(new Uri(endpoint), credential);
```

### <a name="call-client-specific-methods"></a>Volání metod specifických pro klienta

Další blok kódu používá klientské objekty pro volání metod pro každou hlavní úlohu v sadě SDK pro rozpoznávání formulářů. Tyto metody budete definovat později.

Také budete muset přidat odkazy na adresy URL pro školení a testování dat. 
* Pokud chcete načíst adresu URL SAS pro vlastní model data školení, otevřete Průzkumník služby Microsoft Azure Storage, klikněte pravým tlačítkem na svůj kontejner a vyberte **získat sdílený přístupový podpis**. Ujistěte se, že jsou zaškrtnutá oprávnění **číst** a **Zobrazit seznam** , a klikněte na **vytvořit**. Pak zkopírujte hodnotu v části **Adresa URL** . Měla by mít tvar: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` .
* Chcete-li získat adresu URL formuláře k otestování, můžete použít výše uvedené kroky a získat adresu URL SAS jednotlivého dokumentu v úložišti objektů BLOB. Nebo si Převezměte adresu URL dokumentu, který se nachází jinde.
* Použijte výše uvedenou metodu k získání adresy URL obrázku účtenky nebo použijte poskytnutou adresu URL ukázkového obrázku.

> [!NOTE]
> Fragmenty kódu v této příručce používají vzdálené formuláře, ke kterým přistupovali pomocí adres URL. Chcete-li místo toho zpracovat dokumenty v místním formuláři, přečtěte si související metody v [referenční dokumentaci](https://docs.microsoft.com/dotnet/api/overview/azure/ai.formrecognizer-readme-pre).

```csharp
    string trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>";
    string formUrl = "<SAS-URL-of-a-form-in-blob-storage>";
    string receiptUrl = "https://docs.microsoft.com/azure/cognitive-services/form-recognizer/media"
    + "/contoso-allinone.jpg";

    // Call Form Recognizer scenarios:
    Console.WriteLine("Get form content...");
    await GetContent(recognizerClient, formUrl);

    Console.WriteLine("Analyze receipt...");
    await AnalyzeReceipt(recognizerClient, receiptUrl);

    Console.WriteLine("Train Model with training data...");
    Guid modelId = await TrainModel(trainingClient, trainingDataUrl);

    Console.WriteLine("Analyze PDF form...");
    await AnalyzePdfForm(recognizerClient, modelId, formUrl);

    Console.WriteLine("Manage models...");
    await ManageModels(trainingClient, trainingDataUrl) ;
}
```

## <a name="recognize-form-content"></a>Rozpoznávání obsahu formuláře

Nástroj pro rozpoznávání formulářů můžete použít k rozpoznávání tabulek, řádků a slov v dokumentech, aniž byste museli proškolit model.

Pro rozpoznání obsahu souboru v daném identifikátoru URI použijte metodu **StartRecognizeContentFromUri** .

```csharp
private static async Task GetContent(
    FormRecognizerClient recognizerClient, string invoiceUri)
{
    Response<FormPageCollection> formPages = await recognizerClient
        .StartRecognizeContentFromUri(new Uri(invoiceUri))
        .WaitForCompletionAsync();
```

Vrácená hodnota je kolekce objektů **FormPage** : jedna pro každou stránku v odeslaném dokumentu. Následující kód projde tyto objekty a vytiskne extrahované páry klíč/hodnota a data tabulky.

```csharp
    foreach (FormPage page in formPages.Value)
    {
        Console.WriteLine($"Form Page {page.PageNumber} has {page.Lines.Count}" + 
            $" lines.");
    
        for (int i = 0; i < page.Lines.Count; i++)
        {
            FormLine line = page.Lines[i];
            Console.WriteLine($"    Line {i} has {line.Words.Count}" + 
                $" word{(line.Words.Count > 1 ? "s" : "")}," +
                $" and text: '{line.Text}'.");
        }
    
        for (int i = 0; i < page.Tables.Count; i++)
        {
            FormTable table = page.Tables[i];
            Console.WriteLine($"Table {i} has {table.RowCount} rows and" +
                $" {table.ColumnCount} columns.");
            foreach (FormTableCell cell in table.Cells)
            {
                Console.WriteLine($"    Cell ({cell.RowIndex}, {cell.ColumnIndex})" +
                    $" contains text: '{cell.Text}'.");
            }
        }
    }
}
```

## <a name="recognize-receipts"></a>Rozpoznávání příjmů

V této části se dozvíte, jak rozpoznat a extrahovat společná pole z příjmů z USA pomocí předem připraveného modelu příjemů.

Chcete-li rozpoznat potvrzení z identifikátoru URI, použijte metodu **StartRecognizeReceiptsFromUri** . Vrácená hodnota je kolekce objektů **RecognizedReceipt** : jedna pro každou stránku v odeslaném dokumentu. Následující kód zpracuje příjem na daném identifikátoru URI a vytiskne hlavní pole a hodnoty do konzoly.

```csharp
private static async Task AnalyzeReceipt(
    FormRecognizerClient recognizerClient, string receiptUri)
{
    RecognizedReceiptCollection receipts = await recognizerClient.StartRecognizeReceiptsFromUri(new Uri(receiptUri))
    .WaitForCompletionAsync();

    foreach (RecognizedReceipt receipt in receipts)
    {
    FormField merchantNameField;
    if (receipt.RecognizedForm.Fields.TryGetValue("MerchantName", out merchantNameField))
    {
        if (merchantNameField.Value.Type == FieldValueType.String)
        {
            string merchantName = merchantNameField.Value.AsString();

            Console.WriteLine($"Merchant Name: '{merchantName}', with confidence {merchantNameField.Confidence}");
        }
    }

    FormField transactionDateField;
    if (receipt.RecognizedForm.Fields.TryGetValue("TransactionDate", out transactionDateField))
    {
        if (transactionDateField.Value.Type == FieldValueType.Date)
        {
            DateTime transactionDate = transactionDateField.Value.AsDate();

            Console.WriteLine($"Transaction Date: '{transactionDate}', with confidence {transactionDateField.Confidence}");
        }
    }
```

Další blok kódu prochází jednotlivé položky zjištěné na účtence a tiskne jejich podrobnosti do konzoly.

```csharp
    FormField itemsField;
    if (receipt.RecognizedForm.Fields.TryGetValue("Items", out itemsField))
    {
        if (itemsField.Value.Type == FieldValueType.List)
        {
            foreach (FormField itemField in itemsField.Value.AsList())
            {
                Console.WriteLine("Item:");

                if (itemField.Value.Type == FieldValueType.Dictionary)
                {
                    IReadOnlyDictionary<string, FormField> itemFields = itemField.Value.AsDictionary();

                    FormField itemNameField;
                    if (itemFields.TryGetValue("Name", out itemNameField))
                    {
                        if (itemNameField.Value.Type == FieldValueType.String)
                        {
                            string itemName = itemNameField.Value.AsString();

                            Console.WriteLine($"    Name: '{itemName}', with confidence {itemNameField.Confidence}");
                        }
                    }

                    FormField itemTotalPriceField;
                    if (itemFields.TryGetValue("TotalPrice", out itemTotalPriceField))
                    {
                        if (itemTotalPriceField.Value.Type == FieldValueType.Float)
                        {
                            float itemTotalPrice = itemTotalPriceField.Value.AsFloat();

                            Console.WriteLine($"    Total Price: '{itemTotalPrice}', with confidence {itemTotalPriceField.Confidence}");
                        }
                    }
                }
            }
        }
    }
```

Nakonec poslední blok kódu vytiskne celkovou hodnotu pro příjem.

```csharp
    FormField totalField;
    if (receipt.RecognizedForm.Fields.TryGetValue("Total", out totalField))
    {
        if (totalField.Value.Type == FieldValueType.Float)
        {
            float total = totalField.Value.AsFloat();

            Console.WriteLine($"Total: '{total}', with confidence '{totalField.Confidence}'");
        }
    }
}
```

## <a name="train-a-custom-model"></a>Trénování vlastního modelu

Tato část ukazuje, jak vytvořit model s vlastními daty. Vycvičený model může výstupovat strukturovaná data, která zahrnují vztahy klíč/hodnota v původním dokumentu formuláře. Po proškolování modelu můžete otestovat a znovu ho využít a nakonec ho použít k spolehlivé extrakci dat z dalších formulářů podle vašich potřeb.

> [!NOTE]
> Modely můžete také naučit pomocí grafického uživatelského rozhraní, jako je například [Nástroj pro rozpoznávání ukázkových popisků ve formě](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Výuka modelu bez popisků

Výukové vlastní modely vám umožní rozpoznat všechna pole a hodnoty nalezené ve vlastních formulářích bez ručního označení školicích dokumentů.

Následující metoda naplňuje model v dané sadě dokumentů a vytiskne stav modelu do konzoly. 

```csharp
private static async Task<Guid> TrainModel(
    FormRecognizerClient trainingClient, string trainingDataUrl)
{
    CustomFormModel model = await trainingClient
        .StartTrainingAsync(new Uri(trainingFileUrl), useTrainingLabels: false).WaitForCompletionAsync();
    
    Console.WriteLine($"Custom Model Info:");
    Console.WriteLine($"    Model Id: {model.ModelId}");
    Console.WriteLine($"    Model Status: {model.Status}");
    Console.WriteLine($"    Requested on: {model.RequestedOn}");
    Console.WriteLine($"    Completed on: {model.CompletedOn}");
```

Vrácený objekt **CustomFormModel** obsahuje informace o typech formulářů, které může model rozpoznat, a o polích, která může extrahovat z každého typu formuláře. Následující blok kódu vytiskne tyto informace do konzoly nástroje.

```csharp
    foreach (CustomFormSubmodel submodel in model.Submodels)
    {
        Console.WriteLine($"Submodel Form Type: {submodel.FormType}");
        foreach (CustomFormModelField field in submodel.Fields.Values)
        {
            Console.Write($"    FieldName: {field.Name}");
            if (field.Label != null)
            {
                Console.Write($", FieldLabel: {field.Label}");
            }
            Console.WriteLine("");
        }
    }
```

Nakonec tato metoda vrátí jedinečné ID modelu.

```csharp
    return model.ModelId;
}
```

### <a name="train-a-model-with-labels"></a>Výuka modelu s popisky

Vlastní modely můžete také vyškolit ručním popiskem školicích dokumentů. Školení s popisky vede k lepšímu výkonu v některých scénářích. Pro výuku s popisky musíte mít v kontejneru úložiště objektů BLOB vedle školicích dokumentů speciální soubory s informacemi o popisku (* \<filename\>.pdf.labels.json*). [Nástroj pro rozpoznávání popisů vzorků pro rozpoznávání formulářů](../../quickstarts/label-tool.md) poskytuje uživatelské rozhraní, které vám pomůžou vytvořit tyto soubory popisků. Jakmile je máte, můžete zavolat metodu **StartTrainingAsync** s parametrem *uselabels* nastaveným na `true` .

```csharp
private static async Task<Guid> TrainModelWithLabelsAsync(
    FormRecognizerClient trainingClient, string trainingDataUrl)
{
    CustomFormModel model = await trainingClient
    .StartTrainingAsync(new Uri(trainingFileUrl), useTrainingLabels: true).WaitForCompletionAsync();
    
    Console.WriteLine($"Custom Model Info:");
    Console.WriteLine($"    Model Id: {model.ModelId}");
    Console.WriteLine($"    Model Status: {model.Status}");
    Console.WriteLine($"    Requested on: {model.RequestedOn}");
    Console.WriteLine($"    Completed on: {model.CompletedOn}");
```

Vrácený **CustomFormModel** označuje pole, která model může extrahovat, spolu s odhadovanou přesností v každém poli. Následující blok kódu vytiskne tyto informace do konzoly nástroje.

```csharp
    foreach (CustomFormSubmodel submodel in model.Submodels)
    {
        Console.WriteLine($"Submodel Form Type: {submodel.FormType}");
        foreach (CustomFormModelField field in submodel.Fields.Values)
        {
            Console.Write($"    FieldName: {field.Name}");
            if (field.Accuracy != null)
            {
                Console.Write($", Accuracy: {field.Accuracy}");
            }
            Console.WriteLine("");
        }
    }
    return model.ModelId;
}
```

## <a name="analyze-forms-with-a-custom-model"></a>Analýza formulářů pomocí vlastního modelu

V této části se dozvíte, jak extrahovat informace o klíčích a hodnotách a dalším obsahu z vlastních typů formulářů pomocí modelů, které jste vyškole s vlastními formuláři.

> [!IMPORTANT]
> Aby bylo možné tento scénář implementovat, je nutné již vyškolet model, abyste mohli předat jeho ID do níže uvedené metody. Viz část [výuka modelu](#train-a-model-without-labels) .

Použijete metodu **StartRecognizeCustomFormsFromUri** . Vrácená hodnota je kolekce objektů **RecognizedForm** : jedna pro každou stránku v odeslaném dokumentu.

```csharp
// Analyze PDF form data
private static async Task AnalyzePdfForm(
    FormRecognizerClient recognizerClient, Guid modelId, string formUrl)
{    
    Response<IReadOnlyList<RecognizedForm>> forms = await recognizerClient
        .StartRecognizeCustomFormsFromUri(modelId.ToString(), new Uri(formUrl))
        .WaitForCompletionAsync();
```

Následující kód vytiskne výsledky analýzy do konzoly. Vytiskne všechna rozpoznaná pole a odpovídající hodnotu spolu s hodnocením spolehlivosti.

```csharp
    foreach (RecognizedForm form in forms.Value)
    {
        Console.WriteLine($"Form of type: {form.FormType}");
        foreach (FormField field in form.Fields.Values)
        {
            Console.WriteLine($"Field '{field.Name}: ");
    
            if (field.LabelText != null)
            {
                Console.WriteLine($"    Label: '{field.LabelText.Text}");
            }
    
            Console.WriteLine($"    Value: '{field.ValueText.Text}");
            Console.WriteLine($"    Confidence: '{field.Confidence}");
        }
    }
}
```

## <a name="manage-your-custom-models"></a>Správa vlastních modelů

Tato část ukazuje, jak spravovat vlastní modely uložené ve vašem účtu. Následující kód provádí všechny úlohy správy modelů v jediné metodě jako příklad. Začněte tím, že níže zkopírujete signaturu metody:

```csharp
private static async Task ManageModels(
    FormRecognizerClient trainingClient, string trainingFileUrl)
{
```

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Ověřte počet modelů v účtu FormRecognizer prostředků.

Následující blok kódu kontroluje, kolik modelů jste uložili v účtu pro rozpoznávání formulářů a porovnává je s limitem účtu.

```csharp
    // Check number of models in the FormRecognizer account, 
    // and the maximum number of models that can be stored.
    AccountProperties accountProperties = trainingClient.GetAccountProperties();
    Console.WriteLine($"Account has {accountProperties.CustomModelCount} models.");
    Console.WriteLine($"It can have at most {accountProperties.CustomModelLimit}" +
        $" models.");
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Vypíše modely, které jsou aktuálně uložené v účtu prostředků.

Následující blok kódu uvádí aktuální modely v účtu a tiskne jejich podrobnosti do konzoly.

```csharp
    // List the first ten or fewer models currently stored in the account.
    Pageable<CustomFormModelInfo> models = trainingClient.GetModelInfos();
    
    foreach (CustomFormModelInfo modelInfo in models.Take(10))
    {
        Console.WriteLine($"Custom Model Info:");
        Console.WriteLine($"    Model Id: {modelInfo.ModelId}");
        Console.WriteLine($"    Model Status: {modelInfo.Status}");
        Console.WriteLine($"    Created On: {modelInfo.CreatedOn}");
        Console.WriteLine($"    Last Modified: {modelInfo.LastModified}");
    }
```

### <a name="get-a-specific-model-using-the-models-id"></a>Získat konkrétní model pomocí ID modelu

Následující blok kódu naplní nový model (stejně jako v části [výuka modelu a](#train-a-model-without-labels) ) a potom načte druhý odkaz na něj pomocí jeho ID.

```csharp
    // Create a new model to store in the account
    CustomFormModel model = await trainingClient.StartTrainingAsync(
        new Uri(trainingFileUrl)).WaitForCompletionAsync();
    
    // Get the model that was just created
    CustomFormModel modelCopy = trainingClient.GetCustomModel(model.ModelId);
    
    Console.WriteLine($"Custom Model {modelCopy.ModelId} recognizes the following" +
        " form types:");
    
    foreach (CustomFormSubModel subModel in modelCopy.Models)
    {
        Console.WriteLine($"SubModel Form Type: {subModel.FormType}");
        foreach (CustomFormModelField field in subModel.Fields.Values)
        {
            Console.Write($"    FieldName: {field.Name}");
            if (field.Label != null)
            {
                Console.Write($", FieldLabel: {field.Label}");
            }
            Console.WriteLine("");
        }
    }
```

### <a name="delete-a-model-from-the-resource-account"></a>Odstranění modelu z účtu zdroje

Z vašeho účtu můžete také odstranit model odkazem na jeho ID.

```csharp
    // Delete the model from the account.
    trainingClient.DeleteModel(model.ModelId);
}
```

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci z adresáře aplikace pomocí `dotnet run` příkazu.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Řešení potíží

Když pracujete s klientskou knihovnou nástroje pro rozpoznávání formulářů Cognitive Services pomocí sady .NET SDK, chyby vrácené službou budou mít za následek `RequestFailedException` . Budou zahrnovat stejný stavový kód protokolu HTTP, který by byl vrácen požadavkem REST API.

Pokud například odešlete příjemku s neplatným identifikátorem URI, `400` vrátí se chyba, která označuje "Chybný požadavek".

```csharp Snippet:FormRecognizerBadRequest
try
{
    RecognizedReceiptCollection receipts = await client.StartRecognizeReceiptsFromUri(new Uri(receiptUri)).WaitForCompletionAsync();

}
catch (RequestFailedException e)
{
    Console.WriteLine(e.ToString());
}
```

Všimnete si, že se protokolují Další informace, třeba ID žádosti klienta o operaci.

```
Message:
    Azure.RequestFailedException: Service request failed.
    Status: 400 (Bad Request)

Content:
    {"error":{"code":"FailedToDownloadImage","innerError":
    {"requestId":"8ca04feb-86db-4552-857c-fde903251518"},
    "message":"Failed to download image from input URL."}}

Headers:
    Transfer-Encoding: chunked
    x-envoy-upstream-service-time: REDACTED
    apim-request-id: REDACTED
    Strict-Transport-Security: REDACTED
    X-Content-Type-Options: REDACTED
    Date: Mon, 20 Apr 2020 22:48:35 GMT
    Content-Type: application/json; charset=utf-8
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili klientskou knihovnu rozhraní .NET pro rozpoznávání formulářů k učení modelů a analýze formulářů různými způsoby. V dalším kroku se dozvíte tipy, jak vytvořit lepší školicí sadu dat a vytvářet přesnější modely.

> [!div class="nextstepaction"]
> [Vytvoření trénovací sady dat](../../build-training-data-set.md)

* [Co je služba Rozpoznávání formulářů?](../../overview.md)
* Vzorový kód z této příručky (a další) najdete na [GitHubu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md).
