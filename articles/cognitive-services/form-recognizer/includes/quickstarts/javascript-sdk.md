---
title: 'Rychlý Start: Klientská knihovna pro rozpoznávání formulářů pro JavaScript'
description: Použijte klientskou knihovnu pro rozpoznávání formulářů pro JavaScript k vytvoření aplikace pro zpracování formulářů, která extrahuje páry klíč/hodnota a tabulková data z vlastních dokumentů.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 04/14/2021
ms.author: lajanuar
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 7098cfbc2fbe2236687eb7d621a0e587497fcebc
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516395"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!IMPORTANT]
>
> * Kód v tomto článku používá synchronní metody a nezabezpečené úložiště přihlašovacích údajů z důvodů jednoduchosti. Další informace najdete v referenční dokumentaci níže.

[Referenční dokumentace](../../index.yml)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/formrecognizer/ai-form-recognizer/)  |  [Balíček (npm)](https://www.npmjs.com/package/@azure/ai-form-recognizer)  |  [Ukázky](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* Aktuální verze [Node.js](https://nodejs.org/)
* Objekt blob Azure Storage, který obsahuje sadu školicích dat. Tipy a možnosti pro sestavení sady školicích dat najdete v tématu [Vytvoření školicích dat sady pro vlastní model](../../build-training-data-set.md) . Pro účely tohoto rychlého startu můžete použít soubory ve složce **výuka** [ukázkové sady dat](https://go.microsoft.com/fwlink/?linkid=2090451) (stažení a extrakce *sample_data.zip*).
* Jakmile budete mít předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" vytvořte prostředek pro rozpoznávání formulářů "  target="_blank"> vytvořením prostředku pro rozpoznávání formulářů </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Po nasazení vyberte **Přejít k prostředku**.
  * K připojení aplikace k rozhraní API pro rozpoznávání formulářů budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
  * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj.

```console
mkdir myapp && cd myapp
```

Spuštěním `npm init` příkazu vytvořte aplikaci uzlu se `package.json` souborem.

```console
npm init
```

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Nainstalujte `ai-form-recognizer` balíček npm:

```console
npm install @azure/ai-form-recognizer
```

Soubor vaší aplikace `package.json` bude aktualizován pomocí závislostí.

Vytvořte soubor s názvem `index.js` , otevřete ho a importujte následující knihovny:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_imports)]

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/FormRecognizerQuickstart.js), který obsahuje příklady kódu v tomto rychlém startu.

Vytvořte proměnné pro koncový bod a klíč Azure prostředku.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_creds)]

> [!IMPORTANT]
> Přejděte na Azure Portal. Pokud se prostředek pro rozpoznávání formulářů, který jste vytvořili v části **předpoklady** , nasadil úspěšně, klikněte na tlačítko **Přejít k prostředku** v části **Další kroky**. Klíč a koncový bod můžete najít na stránce **klíč a koncový bod** prostředku v části **Správa prostředků**.
>
> Nezapomeňte odebrat klíč z kódu, až budete hotovi, a nikdy ho zveřejnit. V případě produkčního prostředí zvažte použití zabezpečeného způsobu ukládání a přístupu k vašim přihlašovacím údajům. Další informace najdete v článku o [zabezpečení](../../../cognitive-services-security.md) Cognitive Services.

## <a name="object-model"></a>Objektový model

Pomocí nástroje pro rozpoznávání formulářů můžete vytvořit dva různé typy klientů. První `FormRecognizerClient` slouží k dotazování služby na rozpoznaná pole a obsah formuláře. Druhý `FormTrainingClient` slouží k vytváření a správě vlastních modelů, které můžete použít ke zlepšení rozpoznávání.

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` poskytuje operace pro:

* Rozpoznávání polí formuláře a obsahu pomocí vlastních modelů, které jsou vyškolené k analýze vlastních formulářů. Tyto hodnoty jsou vráceny v kolekci `RecognizedForm` objektů.
* Rozpoznávání obsahu formuláře, včetně tabulek, řádků a slov, bez nutnosti vyškolit model. Obsah formuláře se vrátí v kolekci `FormPage` objektů.
* Rozpoznávání společných polí z podnikových příjmů, vizitek, faktur a dokumentů identity s využitím předučeného modelu ve službě pro rozpoznávání formulářů.

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient` poskytuje operace pro:

* Školením vlastních modelů můžete analyzovat všechna pole a hodnoty nalezené ve vlastních formulářích. `CustomFormModel`Je vrácena zpráva oznamující typy formulářů, které bude model analyzovat, a pole, která se budou extrahovat pro každý typ formuláře. Další podrobnosti _najdete_ v [dokumentaci ke službě pro školení k neoznačenému modelu](#train-a-model-without-labels) .
* Školením vlastních modelů můžete analyzovat konkrétní pole a hodnoty, které určíte tak, že označíte vlastní formuláře. `CustomFormModel`Vrátí se typ označující pole, která model vyextrahuje, a také odhadovanou přesnost pro každé pole. Podrobnější vysvětlení použití popisků pro školicí sadu dat najdete v [dokumentaci ke službě s popsaným školením modelu](#train-a-model-with-labels) .
* Správa modelů vytvořených ve vašem účtu.
* Zkopírování vlastního modelu z jednoho prostředku na rozpoznávání formulářů do jiného.

> [!NOTE]
> Modely je také možné vyškolet pomocí grafického uživatelského rozhraní, jako je například [Nástroj pro rozpoznávání popisů formulářů](../../quickstarts/label-tool.md).

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí klientské knihovny pro rozpoznávání formulářů pro JavaScript:

* [Ověření klienta](#authenticate-the-client)
* [Analyzovat rozložení](#analyze-layout)
* [Analyzovat účtenky](#analyze-receipts)
* [Analýza vizitek](#analyze-business-cards)
* [Analyzovat faktury](#analyze-invoices)
* [Analýza dokumentů identity](#analyze-identity-documents)
* [Trénování vlastního modelu](#train-a-custom-model)
* [Analýza formulářů pomocí vlastního modelu](#analyze-forms-with-a-custom-model)
* [Správa vlastních modelů](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>Ověření klienta

Ověřte objekt klienta pomocí definovaných proměnných předplatného. Použijete `AzureKeyCredential` objekt, takže v případě potřeby můžete aktualizovat klíč rozhraní API bez vytváření nových objektů klienta. Vytvoří se také objekt školení klienta.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_auth)]

## <a name="get-assets-for-testing"></a>Získat prostředky pro testování

Také budete muset přidat odkazy na adresy URL pro školení a testování dat.

* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="Načítání adresy URL SAS":::
* Použijte ukázku z a příjem fotografií, které jsou uvedené v následujících ukázkách (k dispozici na [GitHubu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/test-assets)), nebo můžete pomocí výše uvedeného postupu získat adresu URL SAS jednotlivého dokumentu v úložišti objektů BLOB.

## <a name="analyze-layout"></a>Analyzovat rozložení

Nástroj pro rozpoznávání formulářů můžete použít k analýze tabulek, řádků a slov v dokumentech, aniž byste museli proškolit model. Další informace o extrakci rozložení naleznete v [koncepční příručce rozložení](../../concept-layout.md). Chcete-li analyzovat obsah souboru v daném identifikátoru URI, použijte `beginRecognizeContentFromUrl` metodu.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_getcontent)]

> [!TIP]
> Můžete také získat obsah z místního souboru. Podívejte se na metody [FormRecognizerClient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) , jako je například **beginRecognizeContent**. Nebo si přečtěte ukázkový kód na [GitHubu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) , kde najdete scénáře týkající se místních imagí.

### <a name="output"></a>Výstup

```console
Page 1: width 8.5 and height 11 with unit inch
cell [0,0] has text Invoice Number
cell [0,1] has text Invoice Date
cell [0,2] has text Invoice Due Date
cell [0,3] has text Charges
cell [0,5] has text VAT ID
cell [1,0] has text 34278587
cell [1,1] has text 6/18/2017
cell [1,2] has text 6/24/2017
cell [1,3] has text $56,651.49
cell [1,5] has text PT
```

## <a name="analyze-receipts"></a>Analyzovat účtenky

V této části se dozvíte, jak pomocí předem připraveného příjmového modelu analyzovat a extrahovat běžná pole z příjmů spojených s námi. Další informace o analýze příjmů najdete v [koncepční příručce pro příjem](../../concept-receipts.md).

K analýze potvrzení z identifikátoru URI použijte `beginRecognizeReceiptsFromUrl` metodu. Následující kód zpracuje příjem na daném identifikátoru URI a vytiskne hlavní pole a hodnoty do konzoly.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_receipts)]

> [!TIP]
> Můžete také analyzovat místní obrázky pro příjem. Podívejte se na metody [FormRecognizerClient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) , jako je například **beginRecognizeReceipts**. Nebo si přečtěte ukázkový kód na [GitHubu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) , kde najdete scénáře týkající se místních imagí.

### <a name="output"></a>Výstup

```console
status: notStarted
status: running
status: succeeded
First receipt:
  Receipt Type: 'Itemized', with confidence of 0.659
  Merchant Name: 'Contoso Contoso', with confidence of 0.516
  Transaction Date: 'Sun Jun 09 2019 17:00:00 GMT-0700 (Pacific Daylight Time)', with confidence of 0.985
    Item Name: '8GB RAM (Black)', with confidence of 0.916
    Item Name: 'SurfacePen', with confidence of 0.858
  Total: '1203.39', with confidence of 0.774
```

## <a name="analyze-business-cards"></a>Analýza vizitek

V této části se dozvíte, jak pomocí předem připraveného modelu analyzovat a extrahovat běžná pole z jazykových vizitek v angličtině. Další informace o analýze vizitky najdete v tématu [koncepční příručka pro obchodní karty](../../concept-business-cards.md).

K analýze obchodních karet z adresy URL použijte `beginRecognizeBusinessCardsFromURL` metodu.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js" id="snippet_bc":::

> [!TIP]
> Můžete také analyzovat image místních obchodních karet. Podívejte se na metody [FormRecognizerClient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) , jako je například **beginRecognizeBusinessCards**. Nebo si přečtěte ukázkový kód na [GitHubu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) , kde najdete scénáře týkající se místních imagí.

## <a name="analyze-invoices"></a>Analyzovat faktury

V této části se dozvíte, jak pomocí předem připraveného modelu analyzovat a extrahovat běžná pole z prodejních faktur. Další informace o analýze faktur najdete v [koncepční příručce pro fakturaci](../../concept-invoices.md).

Chcete-li analyzovat faktury z adresy URL, použijte `beginRecognizeInvoicesFromUrl` metodu.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js" id="snippet_invoice":::

> [!TIP]
> Můžete také analyzovat image místních obchodních karet. Podívejte se na metody [FormRecognizerClient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) , jako je například **beginRecognizeInvoices**. Nebo si přečtěte ukázkový kód na [GitHubu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) , kde najdete scénáře týkající se místních imagí.

## <a name="analyze-identity-documents"></a>Analýza dokumentů identity

V této části se dozvíte, jak analyzovat a extrahovat informace o klíčích z identifikačních dokumentů vydaných vládou – celosvětově Passports a licence k ovladači USA – pomocí předdefinovaného modelu ID pro rozpoznávání formulářů. Další informace o analýze faktur najdete v našem [průvodci předem sestaveným identifikačním modelem](../../concept-identification-cards.md).

K analýze dokumentů identity z adresy URL použijte `beginRecognizeIdDocumentsFromUrl` metodu.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js" id="snippet_id":::

## <a name="train-a-custom-model"></a>Trénování vlastního modelu

Tato část ukazuje, jak vytvořit model s vlastními daty. Vycvičený model může výstupovat strukturovaná data, která zahrnují vztahy klíč/hodnota v původním dokumentu formuláře. Po proškolování modelu můžete otestovat a znovu ho využít a nakonec ho použít k spolehlivé extrakci dat z dalších formulářů podle vašich potřeb.

> [!NOTE]
> Modely můžete také naučit pomocí grafického uživatelského rozhraní, jako je například [Nástroj pro rozpoznávání ukázkových popisků ve formě](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Výuka modelu bez popisků

Výukové vlastní modely vám umožní analyzovat všechna pole a hodnoty nalezené ve vlastních formulářích bez ručního označení školicích dokumentů.

Následující funkce naplňuje model v dané sadě dokumentů a vytiskne stav modelu do konzoly.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_train)]

### <a name="output"></a>Výstup

Toto je výstup pro model vyškolený pomocí školicích dat dostupných v [sadě JavaScript SDK](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer). Tento ukázkový výstup byl zkrácen z důvodu čitelnosti.

```console
training status: creating
training status: ready
Model ID: 9d893595-1690-4cf2-a4b1-fbac0fb11909
Status: ready
Training started on: Thu Aug 20 2020 20:27:26 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:27:37 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'field-0'
The model found field 'field-1'
The model found field 'field-2'
The model found field 'field-3'
The model found field 'field-4'
The model found field 'field-5'
The model found field 'field-6'
The model found field 'field-7'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors:
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors:
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors:
...
```

### <a name="train-a-model-with-labels"></a>Výuka modelu s popisky

Vlastní modely můžete také vyškolit ručním popiskem školicích dokumentů. Školení s popisky vede k lepšímu výkonu v některých scénářích. Pro výuku pomocí popisků musíte mít `\<filename\>.pdf.labels.json` v kontejneru úložiště objektů BLOB společně s školicími dokumenty speciální soubory s informacemi o popisku (). [Nástroj pro rozpoznávání popisů vzorků pro rozpoznávání formulářů](../../quickstarts/label-tool.md) poskytuje uživatelské rozhraní, které vám pomůžou vytvořit tyto soubory popisků. Jakmile je máte, můžete zavolat `beginTraining` metodu s `uselabels` parametrem nastaveným na `true` .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_trainlabels)]

### <a name="output"></a>Výstup

Toto je výstup pro model vyškolený pomocí školicích dat dostupných v [sadě JavaScript SDK](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples). Tento ukázkový výstup byl zkrácen z důvodu čitelnosti.

```console
training status: creating
training status: ready
Model ID: 789b1b37-4cc3-4e36-8665-9dde68618072
Status: ready
Training started on: Thu Aug 20 2020 20:30:37 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:30:43 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'CompanyAddress'
The model found field 'CompanyName'
The model found field 'CompanyPhoneNumber'
The model found field 'DatedAs'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
...
```

## <a name="analyze-forms-with-a-custom-model"></a>Analýza formulářů pomocí vlastního modelu

V této části se dozvíte, jak extrahovat informace o klíčích a hodnotách a dalším obsahu z vlastních typů formulářů pomocí modelů, které jste vyškole s vlastními formuláři.

> [!IMPORTANT]
> Aby bylo možné tento scénář implementovat, je nutné již vyškolet model, abyste mohli předat jeho ID do níže uvedené metody. Viz část [výuka modelu](#train-a-model-without-labels) .

Použijete `beginRecognizeCustomFormsFromUrl` metodu. Vrácená hodnota je kolekce `RecognizedForm` objektů: jedna pro každou stránku v odeslaném dokumentu.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_analyze)]

> [!TIP]
> Můžete také analyzovat místní soubory. Podívejte se na metody [FormRecognizerClient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) , jako je například **beginRecognizeCustomForms**. Nebo si přečtěte ukázkový kód na [GitHubu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) , kde najdete scénáře týkající se místních imagí.

### <a name="output"></a>Výstup

```console
status: notStarted
status: succeeded
Forms:
custom:form, page range: [object Object]
Pages:
Page number: 1
Tables
cell (0,0) Invoice Number
cell (0,1) Invoice Date
cell (0,2) Invoice Due Date
cell (0,3) Charges
cell (0,5) VAT ID
cell (1,0) 34278587
cell (1,1) 6/18/2017
cell (1,2) 6/24/2017
cell (1,3) $56,651.49
cell (1,5) PT
Fields:
Field Merchant has value 'Invoice For:' with a confidence score of 0.116
Field CompanyPhoneNumber has value '$56,651.49' with a confidence score of 0.249
Field VendorName has value 'Charges' with a confidence score of 0.145
Field CompanyAddress has value '1 Redmond way Suite 6000 Redmond, WA' with a confidence score of 0.258
Field CompanyName has value 'PT' with a confidence score of 0.245
Field Website has value '99243' with a confidence score of 0.114
Field DatedAs has value 'undefined' with a confidence score of undefined
Field Email has value 'undefined' with a confidence score of undefined
Field PhoneNumber has value 'undefined' with a confidence score of undefined
Field PurchaseOrderNumber has value 'undefined' with a confidence score of undefined
Field Quantity has value 'undefined' with a confidence score of undefined
Field Signature has value 'undefined' with a confidence score of undefined
Field Subtotal has value 'undefined' with a confidence score of undefined
Field Tax has value 'undefined' with a confidence score of undefined
Field Total has value 'undefined' with a confidence score of undefined
```

## <a name="manage-your-custom-models"></a>Správa vlastních modelů

Tato část ukazuje, jak spravovat vlastní modely uložené ve vašem účtu. Následující kód provádí všechny úlohy správy modelů v jediné funkci jako příklad.

### <a name="get-number-of-models"></a>Získat počet modelů

Následující blok kódu získá počet modelů, které jsou aktuálně ve vašem účtu.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_count)]

### <a name="get-list-of-models-in-account"></a>Získat seznam modelů v účtu

Následující blok kódu poskytuje úplný seznam dostupných modelů v účtu, včetně informací o tom, kdy byl model vytvořen a jeho aktuální stav.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_list)]

### <a name="output"></a>Výstup

```console
model 0:
{
  modelId: '453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e',
  status: 'invalid',
  trainingStartedOn: 2020-08-20T22:28:52.000Z,
  trainingCompletedOn: 2020-08-20T22:28:53.000Z
}
model 1:
{
  modelId: '628739de-779c-473d-8214-d35c72d3d4f7',
  status: 'ready',
  trainingStartedOn: 2020-08-20T23:16:51.000Z,
  trainingCompletedOn: 2020-08-20T23:16:59.000Z
}
model 2:
{
  modelId: '789b1b37-4cc3-4e36-8665-9dde68618072',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:30:37.000Z,
  trainingCompletedOn: 2020-08-21T03:30:43.000Z
}
model 3:
{
  modelId: '9d893595-1690-4cf2-a4b1-fbac0fb11909',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:27:26.000Z,
  trainingCompletedOn: 2020-08-21T03:27:37.000Z
}
```

### <a name="get-list-of-model-ids-by-page"></a>Získá seznam ID modelů podle stránky.

Tento blok kódu poskytuje stránkované seznam modelů a ID modelů.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_listpages)]

### <a name="output"></a>Výstup

```console
model 1: 453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
model 2: 628739de-779c-473d-8214-d35c72d3d4f7
model 3: 789b1b37-4cc3-4e36-8665-9dde68618072
```

### <a name="get-model-by-id"></a>Získat model podle ID

Následující funkce přijímá ID modelu a získá model odpovídajícího objektu. Tato funkce není ve výchozím nastavení volána.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_getmodel)]

### <a name="delete-a-model-from-the-resource-account"></a>Odstranění modelu z účtu zdroje

Z vašeho účtu můžete také odstranit model odkazem na jeho ID. Tato funkce odstraní model s daným ID. Tato funkce není ve výchozím nastavení volána.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_delete)]

### <a name="output"></a>Výstup

```console
Model with id 789b1b37-4cc3-4e36-8665-9dde68618072 has been deleted
```

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí `node` příkazu v souboru rychlého startu.

```console
node index.js
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Řešení potíží

### <a name="enable-logs"></a>Povolení protokolů

Můžete nastavit následující proměnnou prostředí pro zobrazení protokolů ladění při použití této knihovny.

```console
export DEBUG=azure*
```

Podrobnější pokyny k povolení protokolů najdete v [ @azure/logger dokumentaci k balíčku](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger).

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili klientskou knihovnu pro rozpoznávání formulářů JavaScriptu k učení modelů a analýze formulářů různými způsoby. V dalším kroku se dozvíte tipy, jak vytvořit lepší školicí sadu dat a vytvářet přesnější modely.

> [!div class="nextstepaction"]
> [Vytvoření trénovací sady dat](../../build-training-data-set.md)

## <a name="see-also"></a>Viz také

* [Co je služba Rozpoznávání formulářů?](../../overview.md)
* Vzorový kód z této příručky najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/FormRecognizerQuickstart.js).