---
title: 'Rychlý Start: Klientská knihovna pro rozpoznávání formulářů pro Java'
description: Použijte klientskou knihovnu pro rozpoznávání formulářů pro Java k vytvoření aplikace pro zpracování formulářů, která extrahuje páry klíč/hodnota a tabulková data z vlastních dokumentů.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 09/21/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: 93552d203508fb893bd2e85d27a3a991fc539472
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98132271"
---
> [!IMPORTANT]
> Kód v tomto článku používá synchronní metody a nezabezpečené úložiště přihlašovacích údajů z důvodů jednoduchosti.

[Referenční dokumentace](/java/api/overview/azure/ai-formrecognizer-readme?view=azure-java-preview)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src)  |  [Balíček (Maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer)  |  [Ukázky](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* Aktuální verze sady [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Nástroj Gradle Build](https://gradle.org/install/)nebo jiný správce závislostí.
* Jakmile budete mít předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" vytvořte prostředek pro rozpoznávání formulářů "  target="_blank"> vytvořením prostředku pro rozpoznávání formulářů <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.
    * K připojení aplikace k rozhraní API pro rozpoznávání formulářů budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.
* Objekt blob Azure Storage, který obsahuje sadu školicích dat. Tipy a možnosti pro sestavení sady školicích dat najdete v tématu [Vytvoření školicích dat sady pro vlastní model](../../build-training-data-set.md) . Pro účely tohoto rychlého startu můžete použít soubory ve složce **výuka** [ukázkové sady dat](https://go.microsoft.com/fwlink/?linkid=2090451) (stažení a extrakce *sample_data.zip*).


## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-gradle-project"></a>Vytvořit nový projekt Gradle

V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj. 

```console
mkdir myapp && cd myapp
```

Spusťte `gradle init` příkaz z pracovního adresáře. Tento příkaz vytvoří základní soubory sestavení pro Gradle, včetně *Build. Gradle. kts* , který se používá za běhu k vytvoření a konfiguraci vaší aplikace.

```console
gradle init --type basic
```

Po zobrazení výzvy k výběru **DSL** vyberte **Kotlin**.


### <a name="install-the-client-library"></a>Instalace klientské knihovny

V tomto rychlém startu se používá správce závislostí Gradle. Knihovnu klienta a informace pro jiné správce závislostí najdete v [centrálním úložišti Maven](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer).

V souboru *Build. Gradle. kts* projektu přidejte klientskou knihovnu jako `implementation` příkaz spolu s požadovanými moduly plug-in a nastavením.

#### <a name="version-20"></a>[verze 2,0](#tab/ga)
```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.0.0")
}
```

> [!NOTE]
> 3.0.0 SDK pro rozpoznávání formulářů odráží rozhraní API verze 2,0.

#### <a name="version-31-preview"></a>[verze 3,1 Preview](#tab/preview)
```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.1.0-beta.1")
}
```

> [!NOTE]
> 3.1.0 SDK pro rozpoznávání formulářů zobrazuje rozhraní API verze 2,1 Preview

---

### <a name="create-a-java-file"></a>Vytvoření souboru Java


V pracovním adresáři spusťte následující příkaz:

```console
mkdir -p src/main/java
```

Přejděte do nové složky a vytvořte soubor s názvem *FormRecognizer. Java*. Otevřete ho v preferovaném editoru nebo integrovaném vývojovém prostředí a přidejte následující `import` příkazy:

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_imports)]

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/FormRecognizer/FormRecognizer.java), který obsahuje příklady kódu v tomto rychlém startu.


V třídě **FormRecognizer** aplikace vytvořte proměnné pro klíč a koncový bod prostředku.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_creds)]

> [!IMPORTANT]
> Přejděte na Azure Portal. Pokud prostředek [název produktu], který jste vytvořili v části **předpoklady** , se úspěšně nasadil, klikněte v části **Další kroky** na tlačítko **Přejít k prostředku** . Klíč a koncový bod můžete najít na stránce **klíč a koncový bod** prostředku v části **Správa prostředků**. 
>
> Nezapomeňte odebrat klíč z kódu, až budete hotovi, a nikdy ho zveřejnit. V případě produkčního prostředí zvažte použití zabezpečeného způsobu ukládání a přístupu k vašim přihlašovacím údajům. Další informace najdete v článku o [zabezpečení](../../../cognitive-services-security.md) Cognitive Services.

V metodě **Main** aplikace přidejte volání metod používaných v rámci tohoto rychlého startu. Později je budete definovat. Také budete muset přidat odkazy na adresy URL pro školení a testování dat.

* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]
  
   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="Načítání adresy URL SAS":::
* Chcete-li získat adresu URL formuláře k otestování, můžete použít výše uvedené kroky a získat adresu URL SAS jednotlivého dokumentu v úložišti objektů BLOB. Nebo si Převezměte adresu URL dokumentu, který se nachází jinde.
* K získání adresy URL obrázku účtenky použijte výše uvedenou metodu.

#### <a name="version-20"></a>[verze 2,0](#tab/ga)
[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_mainvars)]

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_maincalls)]
#### <a name="version-21-preview"></a>[verze 2,1 Preview](#tab/preview)
[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_mainvars)]

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_maincalls)]

---



## <a name="object-model"></a>Objektový model 

Pomocí nástroje pro rozpoznávání formulářů můžete vytvořit dva různé typy klientů. První `FormRecognizerClient` slouží k dotazování služby na rozpoznaná pole a obsah formuláře. Druhá možnost slouží `FormTrainingClient` k vytváření a správě vlastních modelů, které můžete použít ke zlepšení rozpoznávání. 

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` poskytuje operace pro:

- Rozpoznávání polí formuláře a obsahu pomocí vlastních modelů, které jsou vyškolené k rozpoznávání vlastních formulářů.  Tyto hodnoty jsou vráceny v kolekci `RecognizedForm` objektů. Viz příklad [analýzy vlastních formulářů](#analyze-forms-with-a-custom-model).
- Rozpoznávání obsahu formuláře, včetně tabulek, řádků a slov, bez nutnosti vyškolit model.  Obsah formuláře se vrátí v kolekci `FormPage` objektů. Viz příklad [analýzy rozložení](#analyze-layout).
- Rozpoznávání společných polí z příjmů spojených s námi pomocí předem připraveného modelu příjmu ve službě pro rozpoznávání formulářů.  Tato pole a meta data jsou vrácena v kolekci `RecognizedForm` objektů. Viz příklad [Analýza účtenek](#analyze-receipts).

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient` poskytuje operace pro:

- Školením vlastních modelů můžete rozpoznat všechna pole a hodnoty nalezené ve vlastních formulářích.  `CustomFormModel`Je vrácena indikace typu formuláře, který model rozpozná, a pole, která se budou extrahovat pro každý typ formuláře.
- Školením vlastních modelů můžete rozpoznat konkrétní pole a hodnoty, které určíte tak, že označíte vlastní formuláře.  `CustomFormModel`Vrátí se typ označující pole, která model vyextrahuje, a také odhadovanou přesnost pro každé pole.
- Správa modelů vytvořených ve vašem účtu.
- Zkopírování vlastního modelu z jednoho prostředku na rozpoznávání formulářů do jiného.

> [!NOTE]
> Modely je také možné vyškolet pomocí grafického uživatelského rozhraní, jako je například [Nástroj pro rozpoznávání popisů formulářů](../../quickstarts/label-tool.md).

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí klientské knihovny pro rozpoznávání formulářů pro Java:

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

V horní části metody **Main** přidejte následující kód. Tady ověříte dva klientské objekty pomocí proměnných předplatného, které jste definovali výše. Použijete objekt **AzureKeyCredential** , takže v případě potřeby můžete aktualizovat klíč rozhraní API bez vytváření nových objektů klienta.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_auth)]

## <a name="analyze-layout"></a>Analyzovat rozložení

Nástroj pro rozpoznávání formulářů můžete použít k rozpoznávání tabulek, řádků a slov v dokumentech, aniž byste museli proškolit model.

Pro rozpoznání obsahu souboru v dané adrese URL použijte metodu **beginRecognizeContentFromUrl** .

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_getcontent_call)]

> [!TIP]
> Můžete také získat obsah z místního souboru. Podívejte se na metody [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient?view=azure-java-stable) , jako je například **beginRecognizeContent**. Nebo si přečtěte ukázkový kód na [GitHubu](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) , kde najdete scénáře týkající se místních imagí.

Vrácená hodnota je kolekce objektů **FormPage** : jedna pro každou stránku v odeslaném dokumentu. Následující kód projde tyto objekty a vytiskne extrahované páry klíč/hodnota a data tabulky.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_getcontent_print)]
### <a name="output"></a>Výstup

```console
Get form content...
----Recognizing content ----
Has width: 8.500000 and height: 11.000000, measured with unit: inch.
Table has 2 rows and 6 columns.
Cell has text Invoice Number.
Cell has text Invoice Date.
Cell has text Invoice Due Date.
Cell has text Charges.
Cell has text VAT ID.
Cell has text 458176.
Cell has text 3/28/2018.
Cell has text 4/16/2018.
Cell has text $89,024.34.
Cell has text ET.
```

## <a name="analyze-receipts"></a>Analyzovat účtenky

V této části se dozvíte, jak rozpoznat a extrahovat společná pole z příjmů z USA pomocí předem připraveného modelu příjemů.

Chcete-li rozpoznat potvrzení z identifikátoru URI, použijte metodu **beginRecognizeReceiptsFromUrl** . 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_call)]

> [!TIP]
> Můžete také rozpoznat místní obrázky pro příjem. Podívejte se na metody [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient?view=azure-java-stable) , jako je například **beginRecognizeReceipts**. Nebo si přečtěte ukázkový kód na [GitHubu](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) , kde najdete scénáře týkající se místních imagí.

Vrácená hodnota je kolekce objektů **RecognizedReceipt** : jedna pro každou stránku v odeslaném dokumentu. Další blok kódu prochází potvrzením a tiskne jejich podrobnosti do konzoly.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_print)]

Další blok kódu prochází jednotlivé položky zjištěné na účtence a tiskne jejich podrobnosti do konzoly.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_print_items)]

### <a name="output"></a>Výstup 

```console
Analyze receipt...
----------- Recognized Receipt page 0 -----------
Merchant Name: Contoso Contoso, confidence: 0.62
Merchant Address: 123 Main Street Redmond, WA 98052, confidence: 0.99
Transaction Date: 2020-06-10, confidence: 0.90
Receipt Items:
Name: Cappuccino, confidence: 0.96s
Quantity: null, confidence: 0.957s]
Total Price: 2.200000, confidence: 0.95
Name: BACON & EGGS, confidence: 0.94s
Quantity: null, confidence: 0.927s]
Total Price: null, confidence: 0.93
```

## <a name="analyze-business-cards"></a>Analýza vizitek

#### <a name="version-20"></a>[verze 2,0](#tab/ga)

> [!IMPORTANT]
> Tato funkce není ve vybrané verzi rozhraní API k dispozici.

#### <a name="version-21-preview"></a>[verze 2,1 Preview](#tab/preview)

V této části se dozvíte, jak rozpoznat a extrahovat společná pole z anglických vizitek pomocí předem připraveného modelu.

K rozpoznání vizitek z adresy URL použijte `beginRecognizeBusinessCardsFromUrl` metodu. 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_bc_call)]

> [!TIP]
> Můžete také rozpoznat obrázky místních obchodních karet. Podívejte se na metody [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient?view=azure-java-stable) , jako je například **beginRecognizeBusinessCards**. Nebo si přečtěte ukázkový kód na [GitHubu](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) , kde najdete scénáře týkající se místních imagí.

Vrácená hodnota je kolekce objektů **RecognizedForm** : jedna pro každou kartu v dokumentu. Následující kód zpracuje vizitku na daném identifikátoru URI a vytiskne hlavní pole a hodnoty do konzoly.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_bc_print)]

---

## <a name="analyze-invoices"></a>Analyzovat faktury

#### <a name="version-20"></a>[verze 2,0](#tab/ga)

> [!IMPORTANT]
> Tato funkce není ve vybrané verzi rozhraní API k dispozici.

#### <a name="version-21-preview"></a>[verze 2,1 Preview](#tab/preview)

V této části se dozvíte, jak rozpoznat a extrahovat společná pole z prodejních faktur pomocí předem připraveného modelu.

K rozpoznání vizitek z adresy URL použijte `beginRecognizeInvoicesFromUrl` metodu. 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_invoice_call)]

> [!TIP]
> Můžete také rozpoznat místní faktury. Podívejte se na metody [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient?view=azure-java-stable) , jako je například **beginRecognizeInvoices**. Nebo si přečtěte ukázkový kód na [GitHubu](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) , kde najdete scénáře týkající se místních imagí.

Vrácená hodnota je kolekce objektů **RecognizedForm** : jedna pro každou fakturu v dokumentu. Následující kód zpracuje vizitku na daném identifikátoru URI a vytiskne hlavní pole a hodnoty do konzoly.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_invoice_print)]

---

## <a name="train-a-custom-model"></a>Trénování vlastního modelu

Tato část ukazuje, jak vytvořit model s vlastními daty. Vycvičený model může výstupovat strukturovaná data, která zahrnují vztahy klíč/hodnota v původním dokumentu formuláře. Po proškolování modelu můžete otestovat a znovu ho využít a nakonec ho použít k spolehlivé extrakci dat z dalších formulářů podle vašich potřeb.

> [!NOTE]
> Modely můžete také naučit pomocí grafického uživatelského rozhraní, jako je například [Nástroj pro rozpoznávání ukázkových popisků ve formě](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Výuka modelu bez popisků

Výukové vlastní modely vám poznají všechna pole a hodnoty nalezené ve vlastních formulářích bez ručního označení školicích dokumentů.

Následující metoda naplňuje model v dané sadě dokumentů a vytiskne stav modelu do konzoly. 


[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_call)]

Vrácený objekt **CustomFormModel** obsahuje informace o typech formulářů, které může model rozpoznat, a o polích, která může extrahovat z každého typu formuláře. Následující blok kódu vytiskne tyto informace do konzoly nástroje.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_print)]

Nakonec tato metoda vrátí jedinečné ID modelu.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_return)]


### <a name="output"></a>Výstup

```console
Train Model with training data...
Model Id: 20c3544d-97b4-49d9-b39b-dc32d85f1358
Model Status: ready
Training started on: 2020-08-31T16:52:09Z
Training completed on: 2020-08-31T16:52:23Z

Recognized Fields:
The subModel has form type form-0
The model found field 'field-0' with label: Address:
The model found field 'field-1' with label: Charges
The model found field 'field-2' with label: Invoice Date
The model found field 'field-3' with label: Invoice Due Date
The model found field 'field-4' with label: Invoice For:
The model found field 'field-5' with label: Invoice Number
The model found field 'field-6' with label: VAT ID
```

### <a name="train-a-model-with-labels"></a>Výuka modelu s popisky

Vlastní modely můžete také vyškolit ručním popiskem školicích dokumentů. Školení s popisky vede k lepšímu výkonu v některých scénářích. Pro výuku s popisky musíte mít v kontejneru úložiště objektů BLOB vedle školicích dokumentů speciální soubory s informacemi o popisku (*\<filename\>.pdf.labels.json*). [Nástroj pro rozpoznávání popisů vzorků pro rozpoznávání formulářů](../../quickstarts/label-tool.md) poskytuje uživatelské rozhraní, které vám pomůžou vytvořit tyto soubory popisků. Jakmile je máte, můžete zavolat metodu **beginTraining** s parametrem *useTrainingLabels* nastaveným na `true` .

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_trainlabels_call)]


Vrácený **CustomFormModel** označuje pole, která model může extrahovat, spolu s odhadovanou přesností v každém poli. Následující blok kódu vytiskne tyto informace do konzoly nástroje.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_trainlabels_print)]


### <a name="output"></a>Výstup

```console
Train Model with training data...
Model Id: 20c3544d-97b4-49d9-b39b-dc32d85f1358
Model Status: ready
Training started on: 2020-08-31T16:52:09Z
Training completed on: 2020-08-31T16:52:23Z

Recognized Fields:
The subModel has form type form-0
The model found field 'field-0' with label: Address:
The model found field 'field-1' with label: Charges
The model found field 'field-2' with label: Invoice Date
The model found field 'field-3' with label: Invoice Due Date
The model found field 'field-4' with label: Invoice For:
The model found field 'field-5' with label: Invoice Number
The model found field 'field-6' with label: VAT ID
```

## <a name="analyze-forms-with-a-custom-model"></a>Analýza formulářů pomocí vlastního modelu

V této části se dozvíte, jak extrahovat informace o klíčích a hodnotách a dalším obsahu z vlastních typů formulářů pomocí modelů, které jste vyškole s vlastními formuláři.

> [!IMPORTANT]
> Aby bylo možné tento scénář implementovat, je nutné již vyškolet model, abyste mohli předat jeho ID do níže uvedené metody. Viz část [výuka modelu](#train-a-model-without-labels) .

Použijete metodu **beginRecognizeCustomFormsFromUrl** . 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_analyze_call)]

> [!TIP]
> Můžete také analyzovat místní soubor. Podívejte se na metody [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient?view=azure-java-stable) , jako je například **beginRecognizeCustomForms**. Nebo si přečtěte ukázkový kód na [GitHubu](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) , kde najdete scénáře týkající se místních imagí.

Vrácená hodnota je kolekce objektů **RecognizedForm** : jedna pro každou stránku v odeslaném dokumentu. Následující kód vytiskne výsledky analýzy do konzoly. Vytiskne všechna rozpoznaná pole a odpovídající hodnotu spolu s hodnocením spolehlivosti.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_analyze_print)]


### <a name="output"></a>Výstup

```console
Analyze PDF form...
----------- Recognized custom form info for page 0 -----------
Form type: form-0
Field 'field-0' has label 'Address:' with a confidence score of 0.91.
Field 'field-1' has label 'Invoice For:' with a confidence score of 1.00.
Field 'field-2' has label 'Invoice Number' with a confidence score of 1.00.
Field 'field-3' has label 'Invoice Date' with a confidence score of 1.00.
Field 'field-4' has label 'Invoice Due Date' with a confidence score of 1.00.
Field 'field-5' has label 'Charges' with a confidence score of 1.00.
Field 'field-6' has label 'VAT ID' with a confidence score of 1.00.
```


## <a name="manage-custom-models"></a>Správa vlastních modelů

Tato část ukazuje, jak spravovat vlastní modely uložené ve vašem účtu. Následující kód provádí všechny úlohy správy modelů v jediné metodě jako příklad. Začněte tím, že níže zkopírujete signaturu metody:

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage)]


### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Ověřte počet modelů v účtu FormRecognizer prostředků.

Následující blok kódu kontroluje, kolik modelů jste uložili v účtu pro rozpoznávání formulářů a porovnává je s limitem účtu.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_count)]


#### <a name="output"></a>Výstup 

```console
The account has 12 custom models, and we can have at most 250 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Vypíše modely, které jsou aktuálně uložené v účtu prostředků.

Následující blok kódu uvádí aktuální modely v účtu a tiskne jejich podrobnosti do konzoly.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_list)]


#### <a name="output"></a>Výstup 

Tato odpověď byla zkrácena z důvodu čitelnosti.

```console
We have following models in the account:
Model Id: 0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Model Id: 0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Model Status: ready
Training started on: 2020-06-04T18:33:08Z
Training completed on: 2020-06-04T18:33:10Z
Custom Model Form type: form-0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Custom Model Accuracy: 1.00
Field Text: invoice date
Field Accuracy: 1.00
Field Text: invoice number
Field Accuracy: 1.00
...
```

### <a name="delete-a-model-from-the-resource-account"></a>Odstranění modelu z účtu zdroje

Z vašeho účtu můžete také odstranit model odkazem na jeho ID.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_delete)]



## <a name="run-the-application"></a>Spuštění aplikace

Přejděte zpátky do hlavního adresáře projektu. Pak aplikaci Sestavte pomocí následujícího příkazu:

```console
gradle build
```

Spusťte aplikaci s `run` cílem:

```console
gradle run
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Řešení potíží

Od klientů pro rozpoznávání vyvolat `ErrorResponseException` výjimky. Pokud se například pokusíte zadat neplatnou adresu URL zdroje souboru, bude `ErrorResponseException` vyvolána chyba, která signalizuje chybu. V následujícím fragmentu kódu je chyba zpracována řádným zachycením výjimky a zobrazením dalších informací o chybě.

```java Snippet:FormRecognizerBadRequest
try {
    formRecognizerClient.beginRecognizeContentFromUrl("invalidSourceUrl");
} catch (ErrorResponseException e) {
    System.out.println(e.getMessage());
}
```

### <a name="enable-client-logging"></a>Povolit protokolování klienta

Sady Azure SDK pro jazyk Java nabízejí jednotný scénář protokolování, který pomáhá pomoci při řešení chyb aplikací a urychlení jejich řešení. Vytvořené protokoly zachytí tok aplikace před tím, než se dokončí stav terminálu, a pomůže tak najít hlavní problém. V části [protokolování na wikiwebu](https://github.com/Azure/azure-sdk-for-java/wiki/Logging-with-Azure-SDK) najdete pokyny k povolení protokolování.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili klientskou knihovnu Java pro rozpoznávání formulářů k učení modelů a analýze formulářů různými způsoby. V dalším kroku se dozvíte tipy, jak vytvořit lepší školicí sadu dat a vytvářet přesnější modely.

> [!div class="nextstepaction"]
> [Vytvoření trénovací sady dat](../../build-training-data-set.md)

* [Co je služba Rozpoznávání formulářů?](../../overview.md)
* Vzorový kód z této příručky (a další) najdete na [GitHubu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples).