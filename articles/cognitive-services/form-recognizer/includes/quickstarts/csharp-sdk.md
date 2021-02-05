---
title: 'Rychlý Start: Klientská knihovna pro rozpoznávání formulářů pro .NET'
description: Použijte klientskou knihovnu pro rozpoznávání formulářů pro .NET k vytvoření aplikace pro zpracování formulářů, která extrahuje páry klíč/hodnota a tabulková data z vlastních dokumentů.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 10/06/2020
ms.author: pafarley
ms.openlocfilehash: e85a6ad4619897a6c655874b43e6a6b1a7723d3a
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584597"
---
> [!IMPORTANT]
> Kód v tomto článku používá synchronní metody a nezabezpečené úložiště přihlašovacích údajů z důvodů jednoduchosti.

[Referenční dokumentace](/dotnet/api/overview/azure/ai.formrecognizer-readme)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src)  |  [Balíček (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer)  |  [Ukázky](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* [Integrované vývojové prostředí (IDE) sady Visual Studio](https://visualstudio.microsoft.com/vs/) nebo aktuální verze [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Objekt blob Azure Storage, který obsahuje sadu školicích dat. Tipy a možnosti pro sestavení sady školicích dat najdete v tématu [Vytvoření školicích dat sady pro vlastní model](../../build-training-data-set.md) . Pro účely tohoto rychlého startu můžete použít soubory ve složce **výuka** [ukázkové sady dat](https://go.microsoft.com/fwlink/?linkid=2090451) (stažení a extrakce *sample_data.zip*).
* Jakmile budete mít předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" vytvořte prostředek pro rozpoznávání formulářů "  target="_blank"> vytvořením prostředku pro rozpoznávání formulářů <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.
    * K připojení aplikace k rozhraní API pro rozpoznávání formulářů budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

V okně konzoly (například cmd, PowerShell nebo bash) použijte `dotnet new` příkaz k vytvoření nové aplikace konzoly s názvem `formrecognizer-quickstart` . Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: *program.cs*. 

```console
dotnet new console -n formrecognizer-quickstart
```

Změňte adresář na nově vytvořenou složku aplikace. Aplikaci můžete vytvořit pomocí:

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

### <a name="install-the-client-library"></a>Instalace klientské knihovny 

V adresáři aplikace nainstalujte klientskou knihovnu pro rozpoznávání formulářů pro rozhraní .NET pomocí následujícího příkazu:

#### <a name="version-20"></a>[verze 2,0](#tab/ga)

```console
dotnet add package Azure.AI.FormRecognizer --version 3.0.0
```

> [!NOTE]
> 3.0.0 SDK pro rozpoznávání formulářů odráží rozhraní API verze 2,0.

#### <a name="version-21-preview"></a>[verze 2,1 Preview](#tab/preview)

```console
dotnet add package Azure.AI.FormRecognizer --version 3.1.0-beta.1
```

> [!NOTE]
> 3.1.0 SDK pro rozpoznávání formulářů zobrazuje rozhraní API verze 2,1 Preview

---

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít na [GitHubu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md), který obsahuje příklady kódu v tomto rychlém startu.

V adresáři projektu otevřete soubor *program.cs* v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE). Přidejte následující `using` direktivy:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_using)]

Do třídy **programu** aplikace vytvořte proměnné pro klíč a koncový bod prostředku.

> [!IMPORTANT]
> Přejděte na Azure Portal. Pokud se prostředek pro rozpoznávání formulářů, který jste vytvořili v části **předpoklady** , nasadil úspěšně, klikněte na tlačítko **Přejít k prostředku** v části **Další kroky**. Klíč a koncový bod můžete najít na stránce **klíč a koncový bod** prostředku v části **Správa prostředků**. 
>
> Nezapomeňte odebrat klíč z kódu, až budete hotovi, a nikdy ho zveřejnit. V případě produkčního prostředí zvažte použití zabezpečeného způsobu ukládání a přístupu k vašim přihlašovacím údajům. Další informace najdete v článku o [zabezpečení](../../../cognitive-services-security.md) Cognitive Services.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_creds)]

V metodě **Main** aplikace přidejte volání asynchronních úloh používaných v rámci tohoto rychlého startu. Později je budete implementovat.

#### <a name="version-20"></a>[verze 2,0](#tab/ga)
[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_main)]
#### <a name="version-21-preview"></a>[verze 2,1 Preview](#tab/preview)
[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_main)]

---


## <a name="object-model"></a>Objektový model 

Pomocí nástroje pro rozpoznávání formulářů můžete vytvořit dva různé typy klientů. První `FormRecognizerClient` slouží k dotazování služby na rozpoznaná pole a obsah formuláře. Druhá možnost slouží `FormTrainingClient` k vytváření a správě vlastních modelů, které můžete použít ke zlepšení rozpoznávání. 

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` poskytuje operace pro:

 - Rozpoznávání polí formuláře a obsahu pomocí vlastních modelů, které jsou vyškolené k analýze vlastních formulářů.  Tyto hodnoty jsou vráceny v kolekci `RecognizedForm` objektů. Viz příklad [analýzy vlastních formulářů](#analyze-forms-with-a-custom-model).
 - Rozpoznávání obsahu formuláře, včetně tabulek, řádků a slov, bez nutnosti vyškolit model.  Obsah formuláře se vrátí v kolekci `FormPage` objektů. Viz příklad [analýzy rozložení](#analyze-layout).
 - Rozpoznávání společných polí z příjmů spojených s námi pomocí předem připraveného modelu příjmu ve službě pro rozpoznávání formulářů. Tato pole a meta data jsou vrácena v kolekci `RecognizedForm` objektů. Viz příklad [Analýza účtenek](#analyze-receipts).

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient` poskytuje operace pro:

- Školením vlastních modelů můžete analyzovat všechna pole a hodnoty nalezené ve vlastních formulářích.  `CustomFormModel`Je vrácena zpráva oznamující typy formulářů, které bude model analyzovat, a pole, která se budou extrahovat pro každý typ formuláře.
- Školením vlastních modelů můžete analyzovat konkrétní pole a hodnoty, které určíte tak, že označíte vlastní formuláře.  `CustomFormModel`Vrátí se typ označující pole, která model vyextrahuje, a také odhadovanou přesnost pro každé pole.
- Správa modelů vytvořených ve vašem účtu.
- Zkopírování vlastního modelu z jednoho prostředku na rozpoznávání formulářů do jiného.

Podívejte se na příklady [výukového modelu](#train-a-custom-model) a [spravujte vlastní modely](#manage-custom-models).

> [!NOTE]
> Modely je také možné vyškolet pomocí grafického uživatelského rozhraní, jako je například [Nástroj pro rozpoznávání popisů formulářů](../../quickstarts/label-tool.md).

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí klientské knihovny pro rozpoznávání formulářů pro .NET:

#### <a name="version-20"></a>[verze 2,0](#tab/ga)

* [Ověření klienta](#authenticate-the-client)
* [Analyzovat rozložení](#analyze-layout)
* [Analyzovat účtenky](#analyze-receipts)
* [Trénování vlastního modelu](#train-a-custom-model)
* [Analýza formulářů pomocí vlastního modelu](#analyze-forms-with-a-custom-model)
* [Správa vlastních modelů](#manage-your-custom-models)

#### <a name="version-21-preview"></a>[verze 2,1 Preview](#tab/preview)

* [Ověření klienta](#authenticate-the-client)
* [Analyzovat rozložení](#analyze-layout)
* [Analyzovat účtenky](#analyze-receipts)
* [Analýza vizitek](#analyze-business-cards)
* [Analyzovat faktury](#analyze-invoices)
* [Trénování vlastního modelu](#train-a-custom-model)
* [Analýza formulářů pomocí vlastního modelu](#analyze-forms-with-a-custom-model)
* [Správa vlastních modelů](#manage-your-custom-models)

---

## <a name="authenticate-the-client"></a>Ověření klienta

Pod **Hlavní** vytvořte novou metodu s názvem `AuthenticateClient` . Tento postup použijete v jiných úkolech k ověření požadavků na službu rozpoznávání formulářů. Tato metoda používá `AzureKeyCredential` objekt, takže v případě potřeby můžete aktualizovat klíč rozhraní API bez vytváření nových objektů klienta.

> [!IMPORTANT]
> Získejte klíč a koncový bod z Azure Portal. Pokud se prostředek pro rozpoznávání formulářů, který jste vytvořili v části **předpoklady** , nasadil úspěšně, klikněte na tlačítko **Přejít k prostředku** v části **Další kroky**. Klíč a koncový bod můžete najít na stránce **klíč a koncový bod** prostředku v části **Správa prostředků**. 
>
> Nezapomeňte odebrat klíč z kódu, až budete hotovi, a nikdy ho zveřejnit. V případě produkčního prostředí zvažte použití zabezpečeného způsobu ukládání a přístupu k vašim přihlašovacím údajům. Například [Trezor klíčů Azure](../../../../key-vault/general/overview.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_auth)]

Opakujte výše uvedené kroky pro novou metodu, která ověřuje školicího klienta.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_auth_training)]

## <a name="get-assets-for-testing"></a>Získat prostředky pro testování 

Také budete muset přidat odkazy na adresy URL pro školení a testování dat. Přidejte je do kořenové složky vaší třídy **programu** .

* [!INCLUDE [get SAS URL](../sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="Načítání adresy URL SAS":::
* Potom opakujte výše uvedené kroky a získejte adresu URL SAS jednotlivého dokumentu v kontejneru úložiště objektů BLOB. Uložte je taky do dočasného umístění.
* Nakonec uložte adresu URL ukázkových imagí, které jsou uvedené níže (k dispozici také na [GitHubu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms)). 

#### <a name="version-20"></a>[verze 2,0](#tab/ga)
[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_urls)]
#### <a name="version-21-preview"></a>[verze 2,1 Preview](#tab/preview)
[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_urls)]

---


## <a name="analyze-layout"></a>Analyzovat rozložení

Nástroj pro rozpoznávání formulářů můžete použít k analýze tabulek, řádků a slov v dokumentech, aniž byste museli proškolit model. Vrácená hodnota je kolekce objektů **FormPage** : jedna pro každou stránku v odeslaném dokumentu. Další informace o extrakci rozložení najdete v [koncepční příručce pro rozložení](../../concept-layout.md).

Chcete-li analyzovat obsah souboru v dané adrese URL, použijte `StartRecognizeContentFromUri` metodu.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_getcontent_call)]

> [!TIP]
> Můžete také získat obsah z místního souboru. Podívejte se na metody [FormRecognizerClient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) , jako je například **StartRecognizeContent**. Nebo si přečtěte ukázkový kód na [GitHubu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) , kde najdete scénáře týkající se místních imagí.

Zbývající část tohoto úkolu vytiskne informace o obsahu do konzoly.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_getcontent_print)]

### <a name="output"></a>Výstup

```console
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    Line 5 has 3 words, and text: '6000 Redmond, WA'.
    Line 6 has 3 words, and text: 'Sunnayvale, CA 87659'.
    Line 7 has 1 word, and text: '99243'.
    Line 8 has 2 words, and text: 'Invoice Number'.
    Line 9 has 2 words, and text: 'Invoice Date'.
    Line 10 has 3 words, and text: 'Invoice Due Date'.
    Line 11 has 1 word, and text: 'Charges'.
    Line 12 has 2 words, and text: 'VAT ID'.
    Line 13 has 1 word, and text: '34278587'.
    Line 14 has 1 word, and text: '6/18/2017'.
    Line 15 has 1 word, and text: '6/24/2017'.
    Line 16 has 1 word, and text: '$56,651.49'.
    Line 17 has 1 word, and text: 'PT'.
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    Cell (0, 3) contains text: 'Charges'.
    Cell (0, 5) contains text: 'VAT ID'.
    Cell (1, 0) contains text: '34278587'.
    Cell (1, 1) contains text: '6/18/2017'.
    Cell (1, 2) contains text: '6/24/2017'.
    Cell (1, 3) contains text: '$56,651.49'.
    Cell (1, 5) contains text: 'PT'.
```


## <a name="analyze-invoices"></a>Analyzovat faktury

#### <a name="version-20"></a>[verze 2,0](#tab/ga)

> [!IMPORTANT]
> Tato funkce není ve vybrané verzi rozhraní API k dispozici.

#### <a name="version-21-preview"></a>[verze 2,1 Preview](#tab/preview)

V této části se dozvíte, jak pomocí předem připraveného modelu analyzovat a extrahovat běžná pole z prodejních faktur. Další informace o analýze faktur najdete v [koncepční příručce pro fakturaci](../../concept-invoices.md).

Chcete-li analyzovat faktury z adresy URL, použijte `StartRecognizeInvoicesFromUriAsync` metodu. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_invoice_call)]

> [!TIP]
> Můžete také analyzovat místní obrázky faktury. Podívejte se na metody [FormRecognizerClient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient?view=azure-dotnet) , jako je například **StartRecognizeInvoices**. Nebo si přečtěte ukázkový kód na [GitHubu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) , kde najdete scénáře týkající se místních imagí.

Vrácená hodnota je kolekce `RecognizedForm` objektů: jedna pro každou fakturu v odeslaném dokumentu. Následující kód zpracuje fakturu na daném identifikátoru URI a vytiskne hlavní pole a hodnoty do konzoly.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_invoice_print)]

---


## <a name="train-a-custom-model"></a>Trénování vlastního modelu

Tato část ukazuje, jak vytvořit model s vlastními daty. Vycvičený model může výstupovat strukturovaná data, která zahrnují vztahy klíč/hodnota v původním dokumentu formuláře. Po proškolování modelu můžete otestovat a znovu ho využít a nakonec ho použít k spolehlivé extrakci dat z dalších formulářů podle vašich potřeb.

> [!NOTE]
> Modely můžete také naučit pomocí grafického uživatelského rozhraní, jako je například [Nástroj pro rozpoznávání ukázkových popisků ve formě](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Výuka modelu bez popisků

Výukové vlastní modely vám umožní analyzovat všechna pole a hodnoty nalezené ve vlastních formulářích bez ručního označení školicích dokumentů. Následující metoda naplňuje model v dané sadě dokumentů a vytiskne stav modelu do konzoly. 


[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train)]


Vrácený `CustomFormModel` objekt obsahuje informace o typech formulářů, které může model analyzovat, a pole, která může extrahovat z každého typu formuláře. Následující blok kódu vytiskne tyto informace do konzoly nástroje.


[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train_response)]

Nakonec vraťte ID trained model pro použití v pozdějších krocích.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train_return)]

### <a name="output"></a>Výstup

Tato odpověď byla zkrácena z důvodu čitelnosti.

```console
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    ...
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    Cell (0, 3) contains text: 'Charges'.
    ... 
Custom Model Info:
    Model Id: 95035721-f19d-40eb-8820-0c806b42798b
    Model Status: Ready
    Training model started on: 8/24/2020 6:36:44 PM +00:00
    Training model completed on: 8/24/2020 6:36:50 PM +00:00
Submodel Form Type: form-95035721-f19d-40eb-8820-0c806b42798b
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    ... 
Custom Model Info:
    Model Id: e7a1181b-1fb7-40be-bfbe-1ee154183633
    Model Status: Ready
    Training model started on: 8/24/2020 6:36:44 PM +00:00
    Training model completed on: 8/24/2020 6:36:52 PM +00:00
Submodel Form Type: form-0
    FieldName: field-0, FieldLabel: Additional Notes:
    FieldName: field-1, FieldLabel: Address:
    FieldName: field-2, FieldLabel: Company Name:
    FieldName: field-3, FieldLabel: Company Phone:
    FieldName: field-4, FieldLabel: Dated As:
    FieldName: field-5, FieldLabel: Details
    FieldName: field-6, FieldLabel: Email:
    FieldName: field-7, FieldLabel: Hero Limited
    FieldName: field-8, FieldLabel: Name:
    FieldName: field-9, FieldLabel: Phone:
    ...
```

### <a name="train-a-model-with-labels"></a>Výuka modelu s popisky

Vlastní modely můžete také vyškolit ručním popiskem školicích dokumentů. Školení s popisky vede k lepšímu výkonu v některých scénářích. Pro výuku pomocí popisků musíte mít `\<filename\>.pdf.labels.json` v kontejneru úložiště objektů BLOB společně s školicími dokumenty speciální soubory s informacemi o popisku (). [Nástroj pro rozpoznávání popisů vzorků pro rozpoznávání formulářů](../../quickstarts/label-tool.md) poskytuje uživatelské rozhraní, které vám pomůžou vytvořit tyto soubory popisků. Jakmile je máte, můžete zavolat `StartTrainingAsync` metodu s `uselabels` parametrem nastaveným na `true` . 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_trainlabels)]

Vrácená `CustomFormModel` položka znamená, že pole, která model může extrahovat, spolu s odhadovanou přesností v každém poli. Následující blok kódu vytiskne tyto informace do konzoly nástroje.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_trainlabels_response)]


### <a name="output"></a>Výstup

Tato odpověď byla zkrácena z důvodu čitelnosti.

```console
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    Line 5 has 3 words, and text: '6000 Redmond, WA'.
    ...
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    ... 
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
Custom Model Info:
    Model Id: 63c013e3-1cab-43eb-84b0-f4b20cb9214c
    Model Status: Ready
    Training model started on: 8/24/2020 6:42:54 PM +00:00
    Training model completed on: 8/24/2020 6:43:01 PM +00:00
Submodel Form Type: form-63c013e3-1cab-43eb-84b0-f4b20cb9214c
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    FieldName: DatedAs
    FieldName: Email
    FieldName: Merchant
    ... 
```

## <a name="analyze-forms-with-a-custom-model"></a>Analýza formulářů pomocí vlastního modelu

V této části se dozvíte, jak extrahovat informace o klíčích a hodnotách a dalším obsahu z vlastních typů formulářů pomocí modelů, které jste vyškole s vlastními formuláři.

> [!IMPORTANT]
> Aby bylo možné tento scénář implementovat, je nutné již vyškolet model, abyste mohli předat jeho ID do níže uvedené metody.

Použijete `StartRecognizeCustomFormsFromUri` metodu. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_analyze)]

> [!TIP]
> Můžete také analyzovat místní soubor. Podívejte se na metody [FormRecognizerClient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) , jako je například **StartRecognizeCustomForms**. Nebo si přečtěte ukázkový kód na [GitHubu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) , kde najdete scénáře týkající se místních imagí.

Vrácená hodnota je kolekce `RecognizedForm` objektů: jedna pro každou stránku v odeslaném dokumentu. Následující kód vytiskne výsledky analýzy do konzoly. Vytiskne všechna rozpoznaná pole a odpovídající hodnotu spolu s hodnocením spolehlivosti.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_analyze_response)]


### <a name="output"></a>Výstup

Tato odpověď byla zkrácena z důvodu čitelnosti.

```console
Custom Model Info:
    Model Id: 9b0108ee-65c8-450e-b527-bb309d054fc4
    Model Status: Ready
    Training model started on: 8/24/2020 7:00:31 PM +00:00
    Training model completed on: 8/24/2020 7:00:32 PM +00:00
Submodel Form Type: form-9b0108ee-65c8-450e-b527-bb309d054fc4
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    ...
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    ...

Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    ...
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
Custom Model Info:
    Model Id: dc115156-ce0e-4202-bbe4-7426e7bee756
    Model Status: Ready
    Training model started on: 8/24/2020 7:00:31 PM +00:00
    Training model completed on: 8/24/2020 7:00:41 PM +00:00
Submodel Form Type: form-0
    FieldName: field-0, FieldLabel: Additional Notes:
    FieldName: field-1, FieldLabel: Address:
    FieldName: field-2, FieldLabel: Company Name:
    FieldName: field-3, FieldLabel: Company Phone:
    FieldName: field-4, FieldLabel: Dated As:
    ... 
Form of type: custom:form
Field 'Azure.AI.FormRecognizer.Models.FieldValue:
    Value: '$56,651.49
    Confidence: '0.249
Field 'Azure.AI.FormRecognizer.Models.FieldValue:
    Value: 'PT
    Confidence: '0.245
Field 'Azure.AI.FormRecognizer.Models.FieldValue:
    Value: '99243
    Confidence: '0.114
   ...
```

## <a name="analyze-receipts"></a>Analyzovat účtenky

V této části se dozvíte, jak pomocí předem připraveného příjmového modelu analyzovat a extrahovat běžná pole z příjmů spojených s námi. Další informace o analýze příjmů najdete v [koncepční příručce pro příjem](../../concept-receipts.md).

K analýze potvrzení z adresy URL použijte `StartRecognizeReceiptsFromUri` metodu. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_receipt_call)]

> [!TIP]
> Můžete také analyzovat místní obrázky pro příjem. Podívejte se na metody [FormRecognizerClient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient?view=azure-dotnet) , jako je například **StartRecognizeReceipts**. Nebo si přečtěte ukázkový kód na [GitHubu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) , kde najdete scénáře týkající se místních imagí.

Vrácená hodnota je kolekce `RecognizedReceipt` objektů: jedna pro každou stránku v odeslaném dokumentu. Následující kód zpracuje příjem na daném identifikátoru URI a vytiskne hlavní pole a hodnoty do konzoly.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_receipt_print)]

### <a name="output"></a>Výstup 

```console
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    Line 5 has 3 words, and text: '6000 Redmond, WA'.
    Line 6 has 3 words, and text: 'Sunnayvale, CA 87659'.
    Line 7 has 1 word, and text: '99243'.
    Line 8 has 2 words, and text: 'Invoice Number'.
    Line 9 has 2 words, and text: 'Invoice Date'.
    Line 10 has 3 words, and text: 'Invoice Due Date'.
    Line 11 has 1 word, and text: 'Charges'.
    Line 12 has 2 words, and text: 'VAT ID'.
    Line 13 has 1 word, and text: '34278587'.
    Line 14 has 1 word, and text: '6/18/2017'.
    Line 15 has 1 word, and text: '6/24/2017'.
    Line 16 has 1 word, and text: '$56,651.49'.
    Line 17 has 1 word, and text: 'PT'.
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    Cell (0, 3) contains text: 'Charges'.
    Cell (0, 5) contains text: 'VAT ID'.
    Cell (1, 0) contains text: '34278587'.
    Cell (1, 1) contains text: '6/18/2017'.
    Cell (1, 2) contains text: '6/24/2017'.
    Cell (1, 3) contains text: '$56,651.49'.
    Cell (1, 5) contains text: 'PT'.
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
```

## <a name="analyze-business-cards"></a>Analýza vizitek

#### <a name="version-20"></a>[verze 2,0](#tab/ga)

> [!IMPORTANT]
> Tato funkce není ve vybrané verzi rozhraní API k dispozici.

#### <a name="version-21-preview"></a>[verze 2,1 Preview](#tab/preview)


Tato část ukazuje, jak pomocí předem připraveného modelu analyzovat a extrahovat běžná pole z anglických vizitek. Další informace o analýze vizitky najdete v tématu [koncepční příručka pro obchodní karty](../../concept-business-cards.md).

K analýze obchodních karet z adresy URL použijte `StartRecognizeBusinessCardsFromUriAsync` metodu. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_bc_call)]

> [!TIP]
> Můžete také analyzovat místní obrázky pro příjem. Podívejte se na metody [FormRecognizerClient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient?view=azure-dotnet) , jako je například **StartRecognizeBusinessCards**. Nebo si přečtěte ukázkový kód na [GitHubu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) , kde najdete scénáře týkající se místních imagí.

Vrácená hodnota je kolekce `RecognizedForm` objektů: jedna pro každou kartu v dokumentu. Následující kód zpracuje vizitku na daném identifikátoru URI a vytiskne hlavní pole a hodnoty do konzoly.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_bc_print)]

---

## <a name="manage-custom-models"></a>Správa vlastních modelů

Tato část ukazuje, jak spravovat vlastní modely uložené ve vašem účtu. V rámci tohoto postupu provedete více operací:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage)]


### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Ověřte počet modelů v účtu FormRecognizer prostředků.

Následující blok kódu kontroluje, kolik modelů jste uložili v účtu pro rozpoznávání formulářů a porovnává je s limitem účtu.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_count)]

### <a name="output"></a>Výstup 

```console
Account has 20 models.
It can have at most 5000 models.
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Vypíše modely, které jsou aktuálně uložené v účtu prostředků.

Následující blok kódu uvádí aktuální modely v účtu a tiskne jejich podrobnosti do konzoly.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_list)]


### <a name="output"></a>Výstup 

Tato odpověď byla zkrácena z důvodu čitelnosti.

```console
Custom Model Info:
    Model Id: 05932d5a-a2f8-4030-a2ef-4e5ed7112515
    Model Status: Creating
    Training model started on: 8/24/2020 7:35:02 PM +00:00
    Training model completed on: 8/24/2020 7:35:02 PM +00:00
Custom Model Info:
    Model Id: 150828c4-2eb2-487e-a728-60d5d504bd16
    Model Status: Ready
    Training model started on: 8/24/2020 7:33:25 PM +00:00
    Training model completed on: 8/24/2020 7:33:27 PM +00:00
Custom Model Info:
    Model Id: 3303e9de-6cec-4dfb-9e68-36510a6ecbb2
    Model Status: Ready
    Training model started on: 8/24/2020 7:29:27 PM +00:00
    Training model completed on: 8/24/2020 7:29:36 PM +00:00
```

### <a name="get-a-specific-model-using-the-models-id"></a>Získat konkrétní model pomocí ID modelu

Následující blok kódu naplní nový model (stejně jako v části [výuka modelu a](#train-a-model-without-labels) ) a potom načte druhý odkaz na něj pomocí jeho ID.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_get)]

### <a name="output"></a>Výstup 

Tato odpověď byla zkrácena z důvodu čitelnosti.

```console
Custom Model Info:
    Model Id: 150828c4-2eb2-487e-a728-60d5d504bd16
    Model Status: Ready
    Training model started on: 8/24/2020 7:33:25 PM +00:00
    Training model completed on: 8/24/2020 7:33:27 PM +00:00
Submodel Form Type: form-150828c4-2eb2-487e-a728-60d5d504bd16
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    FieldName: DatedAs
    FieldName: Email
    FieldName: Merchant
    FieldName: PhoneNumber
    FieldName: PurchaseOrderNumber
    FieldName: Quantity
    FieldName: Signature
    FieldName: Subtotal
    FieldName: Tax
    FieldName: Total
    FieldName: VendorName
    FieldName: Website
...
```

### <a name="delete-a-model-from-the-resource-account"></a>Odstranění modelu z účtu zdroje

Z vašeho účtu můžete také odstranit model odkazem na jeho ID. Tento krok také uzavře metodu.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_delete)]


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

```csharp
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
