---
title: 'Rychlý Start: Klientská knihovna pro rozpoznávání formulářů pro JavaScript'
description: Použijte klientskou knihovnu pro rozpoznávání formulářů pro JavaScript k vytvoření aplikace pro zpracování formulářů, která extrahuje páry klíč/hodnota a tabulková data z vlastních dokumentů.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 09/21/2020
ms.author: pafarley
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 5e5d7c48508cc13d2ad36906df7d31c8926c75f1
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91963035"
---
> [!IMPORTANT]
> * Sada SDK pro rozpoznávání formulářů je aktuálně cílena v 2.0 ze služby pro rozpoznávání.
> * Kód v tomto článku používá synchronní metody a nezabezpečené úložiště přihlašovacích údajů z důvodů jednoduchosti. Další informace najdete v referenční dokumentaci níže. 

[Referenční dokumentace](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/formrecognizer/ai-form-recognizer/)  |  [Balíček (npm)](https://www.npmjs.com/package/@azure/ai-form-recognizer)  |  [Ukázky](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* Objekt blob Azure Storage, který obsahuje sadu školicích dat. Tipy a možnosti pro sestavení sady školicích dat najdete v tématu [Vytvoření školicích dat sady pro vlastní model](../../build-training-data-set.md) . Pro účely tohoto rychlého startu můžete použít soubory ve složce **výuka** [ukázkové sady dat](https://go.microsoft.com/fwlink/?linkid=2090451) (stažení a extrakce *sample_data.zip*).
* Aktuální verze [Node.js](https://nodejs.org/)
* Jakmile budete mít předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" vytvořte prostředek pro rozpoznávání formulářů "  target="_blank"> vytvořením prostředku pro rozpoznávání formulářů <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.
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

Vytvořte soubor s názvem `index.js` , otevřete ho a importujte následující knihovny:

```javascript
const { FormRecognizerClient, FormTrainingClient, AzureKeyCredential } = require("@azure/ai-form-recognizer");
const fs = require("fs");
```

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Nainstalujte `ai-form-recognizer` balíček npm:

```console
npm install @azure/ai-form-recognizer
```

Soubor vaší aplikace `package.json` bude aktualizován pomocí závislostí.

## <a name="object-model"></a>Objektový model 

Pomocí nástroje pro rozpoznávání formulářů můžete vytvořit dva různé typy klientů. První `FormRecognizerClient` slouží k dotazování služby na rozpoznaná pole a obsah formuláře. Druhá možnost slouží `FormTrainingClient` k vytváření a správě vlastních modelů, které můžete použít ke zlepšení rozpoznávání. 

### <a name="formrecognizerclient"></a>FormRecognizerClient
`FormRecognizerClient` poskytuje operace pro:

 * Rozpoznávání polí formuláře a obsahu pomocí vlastních modelů vyškolených pro rozpoznávání vlastních formulářů. Tyto hodnoty jsou vráceny v kolekci `RecognizedForm` objektů.
 * Rozpoznávání obsahu formuláře, včetně tabulek, řádků a slov, bez nutnosti vyškolit model. Obsah formuláře se vrátí v kolekci `FormPage` objektů.
 * Rozpoznávání společných polí z příjmů pomocí předem připraveného modelu příjmu ve službě rozpoznávání formulářů. Tato pole a meta data jsou vrácena v kolekci `RecognizedReceipt` .

### <a name="formtrainingclient"></a>FormTrainingClient
`FormTrainingClient` poskytuje operace pro:

* Školením vlastních modelů můžete rozpoznat všechna pole a hodnoty nalezené ve vlastních formulářích. `CustomFormModel`Je vrácena indikace typu formuláře, který model rozpozná, a pole, která se budou extrahovat pro každý typ formuláře. Podrobnější vysvětlení vytváření školicích dat najdete v [dokumentaci ke službě školení k neoznačenému modelu](#train-a-model-without-labels) .
* Školením vlastních modelů můžete rozpoznat konkrétní pole a hodnoty, které určíte tak, že označíte vlastní formuláře. `CustomFormModel`Vrátí se typ označující pole, která model vyextrahuje, a také odhadovanou přesnost pro každé pole. Podrobnější vysvětlení použití popisků pro školicí sadu dat najdete v [dokumentaci ke službě s popsaným školením modelu](#train-a-model-with-labels) .
* Správa modelů vytvořených ve vašem účtu.
* Zkopírování vlastního modelu z jednoho prostředku na rozpoznávání formulářů do jiného.

> [!NOTE]
> Modely je také možné vyškolet pomocí grafického uživatelského rozhraní, jako je například [Nástroj pro rozpoznávání popisů formulářů](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/label-tool).


## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí klientské knihovny pro rozpoznávání formulářů pro JavaScript:

* [Ověření klienta](#authenticate-the-client)
* [Rozpoznávání obsahu formuláře](#recognize-form-content)
* [Rozpoznávání příjmů](#recognize-receipts)
* [Trénování vlastního modelu](#train-a-custom-model)
* [Analýza formulářů pomocí vlastního modelu](#analyze-forms-with-a-custom-model)
* [Správa vlastních modelů](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>Ověření klienta

V aplikaci vytvořte proměnné pro koncový bod a klíč Azure prostředku. 

```javascript
// You will need to set these environment variables or edit the following values
const endpoint = "<paste-your-form-recognizer-endpoint-here>";
const apiKey = "<paste-your-form-recognizer-key-here>";
```

Pak ověřte objekt klienta pomocí definovaných proměnných předplatného. Použijete `AzureKeyCredential` objekt, takže v případě potřeby můžete aktualizovat klíč rozhraní API bez vytváření nových objektů klienta. Vytvoří se také objekt školení klienta.

```javascript
const trainingClient = new FormTrainingClient(endpoint, new AzureKeyCredential(apiKey));
const client = new FormRecognizerClient(endpoint, new AzureKeyCredential(apiKey));
```

## <a name="get-assets-for-testing"></a>Získat prostředky pro testování

Fragmenty kódu v této příručce používají vzdálené formuláře, ke kterým přistupovali pomocí adres URL. Pokud místo toho chcete zpracovat dokumenty v místním formuláři, přečtěte si související metody v [referenční dokumentaci](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer) a [ukázkách](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples).

Také budete muset přidat odkazy na adresy URL pro školení a testování dat.
* Pokud chcete načíst adresu URL SAS pro vlastní model data školení, otevřete Průzkumník služby Microsoft Azure Storage, klikněte pravým tlačítkem na svůj kontejner a vyberte **získat sdílený přístupový podpis**. Ujistěte se, že jsou zaškrtnutá oprávnění **číst** a **Zobrazit seznam** , a klikněte na **vytvořit**. Pak zkopírujte hodnotu v části **Adresa URL** . Měla by mít tento formát: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
* Použijte ukázku z obrázků a příjemů obsažených v následujících ukázkách (k dispozici také na [GitHubu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms) nebo můžete použít výše uvedené kroky a získat adresu URL SAS jednotlivého dokumentu v úložišti objektů BLOB). 

> [!NOTE]
> Fragmenty kódu v této příručce používají vzdálené formuláře, ke kterým přistupovali pomocí adres URL. Chcete-li místo toho zpracovat dokumenty v místním formuláři, přečtěte si související metody v [referenční dokumentaci](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).

## <a name="recognize-form-content"></a>Rozpoznávání obsahu formuláře

Nástroj pro rozpoznávání formulářů můžete použít k rozpoznávání tabulek, řádků a slov v dokumentech, aniž byste museli proškolit model. Pro rozpoznání obsahu souboru v daném identifikátoru URI použijte `beginRecognizeContentFromUrl` metodu.

```javascript
async function recognizeContent() {
    const formUrl = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/forms/Invoice_1.pdf";
    const poller = await client.beginRecognizeContentFromUrl(formUrl);
    const pages = await poller.pollUntilDone();

    if (!pages || pages.length === 0) {
        throw new Error("Expecting non-empty list of pages!");
    }

    for (const page of pages) {
        console.log(
            `Page ${page.pageNumber}: width ${page.width} and height ${page.height} with unit ${page.unit}`
        );
        for (const table of page.tables) {
            for (const cell of table.cells) {
                console.log(`cell [${cell.rowIndex},${cell.columnIndex}] has text ${cell.text}`);
            }
        }
    }
}

recognizeContent().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

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

## <a name="recognize-receipts"></a>Rozpoznávání příjmů

V této části se dozvíte, jak rozpoznat a extrahovat společná pole z příjmů z USA pomocí předem připraveného modelu příjemů.

Chcete-li rozpoznat potvrzení z identifikátoru URI, použijte `beginRecognizeReceiptsFromUrl` metodu. Následující kód zpracuje příjem na daném identifikátoru URI a vytiskne hlavní pole a hodnoty do konzoly.

```javascript
async function recognizeReceipt() {
    receiptUrl = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/receipt/contoso-receipt.png";
    const client = new FormRecognizerClient(endpoint, new AzureKeyCredential(apiKey));
    const poller = await client.beginRecognizeReceiptsFromUrl(receiptUrl, {
        onProgress: (state) => { console.log(`status: ${state.status}`); }
    });

    const receipts = await poller.pollUntilDone();

    if (!receipts || receipts.length <= 0) {
        throw new Error("Expecting at lease one receipt in analysis result");
    }

    const receipt = receipts[0];
    console.log("First receipt:");
    const receiptTypeField = receipt.fields["ReceiptType"];
    if (receiptTypeField.valueType === "string") {
        console.log(`  Receipt Type: '${receiptTypeField.value || "<missing>"}', with confidence of ${receiptTypeField.confidence}`);
    }
    const merchantNameField = receipt.fields["MerchantName"];
    if (merchantNameField.valueType === "string") {
        console.log(`  Merchant Name: '${merchantNameField.value || "<missing>"}', with confidence of ${merchantNameField.confidence}`);
    }
    const transactionDate = receipt.fields["TransactionDate"];
    if (transactionDate.valueType === "date") {
        console.log(`  Transaction Date: '${transactionDate.value || "<missing>"}', with confidence of ${transactionDate.confidence}`);
    }
    const itemsField = receipt.fields["Items"];
    if (itemsField.valueType === "array") {
        for (const itemField of itemsField.value || []) {
            if (itemField.valueType === "object") {
                const itemNameField = itemField.value["Name"];
                if (itemNameField.valueType === "string") {
                    console.log(`    Item Name: '${itemNameField.value || "<missing>"}', with confidence of ${itemNameField.confidence}`);
                }
            }
        }
    }
    const totalField = receipt.fields["Total"];
    if (totalField.valueType === "number") {
        console.log(`  Total: '${totalField.value || "<missing>"}', with confidence of ${totalField.confidence}`);
    }
}

recognizeReceipt().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

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

## <a name="train-a-custom-model"></a>Trénování vlastního modelu

Tato část ukazuje, jak vytvořit model s vlastními daty. Vycvičený model může výstupovat strukturovaná data, která zahrnují vztahy klíč/hodnota v původním dokumentu formuláře. Po proškolování modelu můžete otestovat a znovu ho využít a nakonec ho použít k spolehlivé extrakci dat z dalších formulářů podle vašich potřeb.

> [!NOTE]
> Modely můžete také naučit pomocí grafického uživatelského rozhraní, jako je například [Nástroj pro rozpoznávání ukázkových popisků ve formě](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Výuka modelu bez popisků

Výukové vlastní modely vám umožní rozpoznat všechna pole a hodnoty nalezené ve vlastních formulářích bez ručního označení školicích dokumentů.

Následující funkce naplňuje model v dané sadě dokumentů a vytiskne stav modelu do konzoly. 

```javascript
async function trainModel() {

    const containerSasUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>";
    const trainingClient = new FormTrainingClient(endpoint, new AzureKeyCredential(apiKey));

    const poller = await trainingClient.beginTraining(containerSasUrl, false, {
        onProgress: (state) => { console.log(`training status: ${state.status}`); }
    });
    const model = await poller.pollUntilDone();

    if (!model) {
        throw new Error("Expecting valid training result!");
    }

    console.log(`Model ID: ${model.modelId}`);
    console.log(`Status: ${model.status}`);
    console.log(`Training started on: ${model.trainingStartedOn}`);
    console.log(`Training completed on: ${model.trainingCompletedOn}`);

    if (model.submodels) {
        for (const submodel of model.submodels) {
            // since the training data is unlabeled, we are unable to return the accuracy of this model
            console.log("We have recognized the following fields");
            for (const key in submodel.fields) {
                const field = submodel.fields[key];
                console.log(`The model found field '${field.name}'`);
            }
        }
    }
    // Training document information
    if (model.trainingDocuments) {
        for (const doc of model.trainingDocuments) {
            console.log(`Document name: ${doc.name}`);
            console.log(`Document status: ${doc.status}`);
            console.log(`Document page count: ${doc.pageCount}`);
            console.log(`Document errors: ${doc.errors}`);
        }
    }
}

trainModel().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

### <a name="output"></a>Výstup

Toto je výstup pro model vyškolený pomocí školicích dat dostupných v [sadě Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training). Tento ukázkový výstup byl zkrácen z důvodu čitelnosti.

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

```javascript
async function trainModelLabels() {

    const containerSasUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>";
    const trainingClient = new FormTrainingClient(endpoint, new AzureKeyCredential(apiKey));

    const poller = await trainingClient.beginTraining(containerSasUrl, true, {
        onProgress: (state) => { console.log(`training status: ${state.status}`); }
    });
    const model = await poller.pollUntilDone();

    if (!model) {
        throw new Error("Expecting valid training result!");
    }

    console.log(`Model ID: ${model.modelId}`);
    console.log(`Status: ${model.status}`);
    console.log(`Training started on: ${model.trainingStartedOn}`);
    console.log(`Training completed on: ${model.trainingCompletedOn}`);

    if (model.submodels) {
        for (const submodel of model.submodels) {
            // since the training data is unlabeled, we are unable to return the accuracy of this model
            console.log("We have recognized the following fields");
            for (const key in submodel.fields) {
                const field = submodel.fields[key];
                console.log(`The model found field '${field.name}'`);
            }
        }
    }
    // Training document information
    if (model.trainingDocuments) {
        for (const doc of model.trainingDocuments) {
            console.log(`Document name: ${doc.name}`);
            console.log(`Document status: ${doc.status}`);
            console.log(`Document page count: ${doc.pageCount}`);
            console.log(`Document errors: ${doc.errors}`);
        }
    }
}

trainModelLabels().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

### <a name="output"></a>Výstup 

Toto je výstup pro model vyškolený pomocí školicích dat dostupných v [sadě Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training). Tento ukázkový výstup byl zkrácen z důvodu čitelnosti.

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

```javascript
async function recognizeCustom() {
    // Model ID from when you trained your model.
    const modelId = "<modelId>";
    const formUrl = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/forms/Invoice_1.pdf";

    const client = new FormRecognizerClient(endpoint, new AzureKeyCredential(apiKey));
    const poller = await client.beginRecognizeCustomForms(modelId, formUrl, {
        onProgress: (state) => { console.log(`status: ${state.status}`); }
    });
    const forms = await poller.pollUntilDone();

    console.log("Forms:");
    for (const form of forms || []) {
        console.log(`${form.formType}, page range: ${form.pageRange}`);
        console.log("Pages:");
        for (const page of form.pages || []) {
            console.log(`Page number: ${page.pageNumber}`);
            console.log("Tables");
            for (const table of page.tables || []) {
                for (const cell of table.cells) {
                    console.log(`cell (${cell.rowIndex},${cell.columnIndex}) ${cell.text}`);
                }
            }
        }

        console.log("Fields:");
        for (const fieldName in form.fields) {
            // each field is of type FormField
            const field = form.fields[fieldName];
            console.log(
                `Field ${fieldName} has value '${field.value}' with a confidence score of ${field.confidence}`
            );
        }
    }
}

recognizeCustom().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

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

### <a name="get-list-of-models-in-account"></a>Získat seznam modelů v účtu

Následující blok kódu poskytuje úplný seznam dostupných modelů v účtu, včetně informací o tom, kdy byl model vytvořen, a jeho aktuálním stavu.

```javascript
async function listModels() {
    const client = new FormTrainingClient(endpoint, new AzureKeyCredential(apiKey));

    // returns an async iteratable iterator that supports paging
    const result = client.listCustomModels();
    let i = 0;
    for await (const modelInfo of result) {
        console.log(`model ${i++}:`);
        console.log(modelInfo);
    }
}

listModels().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

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

### <a name="get-list-of-model-ids"></a>Získat seznam ID modelů

Tento blok kódu poskytuje seznam modelů a ID modelů.

```javascript
async function listModelIds(){
    const client = new FormTrainingClient(endpoint, new AzureKeyCredential(apiKey));
    // using `iter.next()`
    i = 1;
    let iter = client.listCustomModels();
    let modelItem = await iter.next();
    while (!modelItem.done) {
        console.log(`model ${i++}: ${modelItem.value.modelId}`);
        modelItem = await iter.next();
    }
}
```

### <a name="output"></a>Výstup

```console
model 1: 453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
model 2: 628739de-779c-473d-8214-d35c72d3d4f7
model 3: 789b1b37-4cc3-4e36-8665-9dde68618072
```

### <a name="get-list-of-model-ids-by-page"></a>Získá seznam ID modelů podle stránky.

Tento blok kódu poskytuje stránkované seznam modelů a ID modelů.

```javascript
async function listModelsByPage(){
    const client = new FormTrainingClient(endpoint, new AzureKeyCredential(apiKey));
    // using `byPage()`
    i = 1;
    for await (const response of client.listCustomModels().byPage()) {
        for (const modelInfo of response.modelList) {
            console.log(`model ${i++}: ${modelInfo.modelId}`);
        }
    }
}

listModelsByPage().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

### <a name="output"></a>Výstup

```console
model 1: 453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
model 2: 628739de-779c-473d-8214-d35c72d3d4f7
model 3: 789b1b37-4cc3-4e36-8665-9dde68618072
```

### <a name="delete-a-model-from-the-resource-account"></a>Odstranění modelu z účtu zdroje

Z vašeho účtu můžete také odstranit model odkazem na jeho ID. Tento kód odstraní model použitý v předchozí části.

```javascript
    await client.deleteModel(firstModel.modelId);
    try {
        const deleted = await trainingClient.deleteModel(firstModel.modelId);
        console.log(deleted);
    } catch (err) {
        // Expected
        console.log(`Model with id ${firstModel.modelId} has been deleted`);
    }
}
```

### <a name="output"></a>Výstup

```console
Model with id 789b1b37-4cc3-4e36-8665-9dde68618072 has been deleted
```

## <a name="run-the-application"></a>Spuštění aplikace

Aplikaci můžete kdykoli spustit s libovolným počtem funkcí, o které jste si v tomto rychlém startu seznámili pomocí tohoto příkazu:

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

```
export DEBUG=azure*
```

Podrobnější pokyny k povolení protokolů najdete v [ @azure/logger dokumentaci k balíčku](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger).

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili klientskou knihovnu pro rozpoznávání formulářů Pythonu k učení modelů a analýze formulářů různými způsoby. V dalším kroku se dozvíte tipy, jak vytvořit lepší školicí sadu dat a vytvářet přesnější modely.

> [!div class="nextstepaction"]
> [Vytvoření trénovací sady dat](../../build-training-data-set.md)

## <a name="see-also"></a>Viz také

* [Co je služba Rozpoznávání formulářů?](../../overview.md)
