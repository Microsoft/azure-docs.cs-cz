---
title: Rychlý Start klientské knihovny s Content Moderator Java
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte, jak začít s klientskou knihovnou Azure Content Moderator pro Java. Sestavujte software pro filtrování obsahu do vaší aplikace, abyste vyhověli předpisům nebo zachovali zamýšlené prostředí pro vaše uživatele.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 10/16/2020
ms.custom: devx-track-java, cog-serv-seo-aug-2020
ms.author: pafarley
ms.openlocfilehash: 17acd11b6ebb651be170135ab9789f788d4d8077
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444169"
---
Začněte s klientskou knihovnou Azure Content Moderator pro Java. Pomocí těchto kroků nainstalujete balíček Maven a vyzkoušíte ukázkový kód pro základní úlohy. 

Content Moderator je služba AI, která umožňuje zpracovávat obsah, který je potenciálně urážlivý, rizikové nebo jinak nežádoucí. Použijte službu pro moderování obsahu s AI ke skenování textu, obrázku a videí a automatické použití příznaků obsahu. Sestavujte software pro filtrování obsahu do vaší aplikace, abyste vyhověli předpisům nebo zachovali zamýšlené prostředí pro vaše uživatele.

Pomocí klientské knihovny Content Moderator pro jazyk Java:

* Střední text
* Střední obrázky

[Referenční dokumentace](/java/api/overview/azure/cognitiveservices/client/contentmoderator)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-contentmoderator)  | [Artefakt (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator)  |  [Ukázky](/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [můžete ho vytvořit zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Aktuální verze sady [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Nástroj Gradle Build](https://gradle.org/install/)nebo jiný správce závislostí.
* Jakmile budete mít předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title=" vytvořte prostředek Content moderator vytvoření prostředku "  target="_blank"> Content Moderator </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Počkejte na nasazení a klikněte na tlačítko **Přejít k prostředku** .
    * K připojení aplikace k Content Moderator budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-gradle-project"></a>Vytvořit nový projekt Gradle

V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj. 

```console
mkdir myapp && cd myapp
```

Spusťte `gradle init` příkaz z pracovního adresáře. Tento příkaz vytvoří základní soubory sestavení pro Gradle, včetně *Build. Gradle. kts*, který se používá za běhu k vytvoření a konfiguraci vaší aplikace.

```console
gradle init --type basic
```

Po zobrazení výzvy k výběru **DSL** vyberte **Kotlin**.

## <a name="install-the-client-library"></a>Instalace klientské knihovny

Vyhledejte *Build. Gradle. kts* a otevřete ho pomocí vašeho preferovaného integrovaného vývojového prostředí (IDE) nebo textového editoru. Pak zkopírujte do následující konfigurace sestavení. Tato konfigurace definuje projekt jako aplikaci Java, jejíž vstupní bod je třídou **ContentModeratorQuickstart**. Importuje knihovnu klienta Content Moderator a také sadu SDK GSON pro serializaci JSON.

```kotlin
plugins {
    java
    application
}

application{ 
    mainClassName = "ContentModeratorQuickstart"
}

repositories{
    mavenCentral()
}

dependencies{
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-contentmoderator", version = "1.0.2-beta")
    compile(group = "com.google.code.gson", name = "gson", version = "2.8.5")
}
```

### <a name="create-a-java-file"></a>Vytvoření souboru Java


V pracovním adresáři spusťte následující příkaz, který vytvoří zdrojovou složku projektu:

```console
mkdir -p src/main/java
```

Přejděte do nové složky a vytvořte soubor s názvem *ContentModeratorQuickstart. Java*. Otevřete ho v preferovaném editoru nebo integrovaném vývojovém prostředí a přidejte následující `import` příkazy:

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

> [!TIP]
> Chcete zobrazit celý soubor kódu pro rychlý Start najednou? Můžete ji najít na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java), který obsahuje příklady kódu v tomto rychlém startu.

V třídě **ContentModeratorQuickstart** aplikace vytvořte proměnné pro klíč a koncový bod prostředku.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_creds)]

> [!IMPORTANT]
> Přejděte na Azure Portal. Pokud se prostředek Content Moderator, který jste vytvořili v části **předpoklady** , se úspěšně nasadil, klikněte v části **Další kroky** na tlačítko **Přejít k prostředku** . Klíč a koncový bod můžete najít na stránce **klíč a koncový bod** prostředku v části **Správa prostředků**. 
>
> Nezapomeňte odebrat klíč z kódu, až budete hotovi, a nikdy ho zveřejnit. V případě produkčního prostředí zvažte použití zabezpečeného způsobu ukládání a přístupu k vašim přihlašovacím údajům. Další informace najdete v článku o [zabezpečení](../../../cognitive-services-security.md) Cognitive Services.

V metodě **Main** aplikace přidejte volání metod používaných v rámci tohoto rychlého startu. Později je budete definovat.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_maincalls)]


## <a name="object-model"></a>Objektový model

Následující třídy zpracovávají některé hlavní funkce klientské knihovny Content Moderator Java.

|Název|Popis|
|---|---|
|[ContentModeratorClient](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient)|Tato třída je potřebná pro všechny funkce Content Moderator. Vytvoří se jeho instance s informacemi o předplatném a Vy ho použijete k vytvoření instancí jiných tříd.|
|[ImageModeration](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations)|Tato třída poskytuje funkce pro analýzu obrázků pro obsah pro dospělé, osobní údaje nebo lidské obličeje.|
|[TextModerations](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations)|Tato třída poskytuje funkce pro analýzu textu pro jazyk, vulgární výrazy, chyby a osobní údaje.|
|[Recenze](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews)|Tato třída poskytuje funkce v rozhraních API pro revize, včetně metod vytváření úloh, vlastních pracovních postupů a lidských recenzí.|


## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí Content Moderator klientské knihovny pro Java:

* [Ověření klienta](#authenticate-the-client)
* [Střední text](#moderate-text)
* [Střední obrázky](#moderate-images)


## <a name="authenticate-the-client"></a>Ověření klienta

V `main` metodě aplikace vytvořte objekt [ContentModeratorClient](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient) pomocí hodnoty koncového bodu předplatného a klíče předplatného.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]



## <a name="moderate-text"></a>Střední text

### <a name="set-up-sample-text"></a>Nastavení ukázkového textu

V horní části třídy **ContentModeratorQuickstart** definujte odkaz na místní textový soubor. Přidejte soubor. txt do adresáře projektu a zadejte text, který se má analyzovat.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_var)]

### <a name="analyze-text"></a>Analyzovat text

Vytvořte novou metodu, která přečte soubor. txt a zavolá metodu **screenText** na každém řádku.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod)]

### <a name="print-text-moderation-results"></a>Vytiskněte výsledky moderování textu.

Přidejte následující kód pro vytištění výsledků moderování do souboru. JSON v adresáři projektu.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_print)]

`try` `catch` Pro dokončení metody zavřete příkaz a.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_catch)]


## <a name="moderate-images"></a>Střední obrázky

### <a name="set-up-sample-image"></a>Nastavit ukázkovou bitovou kopii

V nové metodě Sestavte objekt **[BodyModelModel](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.models.bodymodelmodel)** s daným řetězcem URL, který odkazuje na obrázek.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod)]


### <a name="define-helper-class"></a>Definovat pomocnou třídu

Potom v souboru *ContentModeratorQuickstart. Java* přidejte do třídy **ContentModeratorQuickstart** následující definici třídy. Tato vnitřní třída se používá v procesu moderování imagí.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]


### <a name="analyze-content"></a>Analýza obsahu
Tento řádek kódu kontroluje obrázek na dané adrese URL pro dospělé nebo pikantní obsah. Informace o těchto pojmech najdete v koncepční příručce pro moderování obrázků.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>Kontrolovat text
Tento řádek kódu kontroluje obrázek pro viditelný text.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Kontrolovat obličeje
Tento řádek kódu kontroluje obrázek pro lidské obličeje.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

Nakonec uložte vrácené informace v `EvaluationData` seznamu.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>Vytisknout výsledky

Po `while` smyčce přidejte následující kód, který vytiskne výsledky do konzoly a do výstupního souboru, *Src/Main/Resources/ModerationOutput.jsv*.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

Zavřete `try` příkaz a přidejte `catch` příkaz pro dokončení metody.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="run-the-application"></a>Spuštění aplikace

Aplikaci můžete vytvořit pomocí:

```console
gradle build
```

Spusťte aplikaci pomocí `gradle run` příkazu:

```console
gradle run
```

Pak přejděte k souboru *Src/Main/Resources/ModerationOutput.jsna* soubor a podívejte se na výsledky Moderování obsahu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak používat knihovnu Content Moderator Java k provádění úloh moderování. V dalším kroku se dozvíte další informace o moderování imagí nebo jiných médií. Přečtěte si koncepční průvodce.

> [!div class="nextstepaction"]
> [Koncepty moderování obrázků](../../image-moderation-api.md)

* [Co je Azure Content Moderator?](../../overview.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java).
