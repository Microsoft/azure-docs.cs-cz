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
ms.date: 09/15/2020
ms.custom: devx-track-java, cog-serv-seo-aug-2020
ms.author: pafarley
ms.openlocfilehash: 1e32cd924c8e0f713ebe7cedfca0466a1e07c3bf
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91332548"
---
Začněte s klientskou knihovnou Azure Content Moderator pro Java. Pomocí těchto kroků nainstalujete balíček Maven a vyzkoušíte ukázkový kód pro základní úlohy. 

Content Moderator je služba AI, která umožňuje zpracovávat obsah, který je potenciálně urážlivý, rizikové nebo jinak nežádoucí. Použijte službu pro moderování obsahu s AI ke skenování textu, obrázku a videí a automatické použití příznaků obsahu. Sestavujte software pro filtrování obsahu do vaší aplikace, abyste vyhověli předpisům nebo zachovali zamýšlené prostředí pro vaše uživatele.

Pomocí klientské knihovny Content Moderator pro jazyk Java:

* Střední obrázky pro dospělé nebo pikantní obsah, text nebo lidské obličeje.

[Referenční dokumentace](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-java-stable)  |  [Artefakt (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator)  |  [Ukázky](https://docs.microsoft.com/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Aktuální verze sady [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Nástroj Gradle Build](https://gradle.org/install/)nebo jiný správce závislostí.

## <a name="create-a-content-moderator-resource"></a>Vytvoření prostředku Content Moderator

Azure Cognitive Services jsou představovány prostředky Azure, ke kterým jste se přihlásili. Vytvořte prostředek pro Content Moderator pomocí [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) nebo rozhraní příkazového [řádku Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na vašem místním počítači. Můžete také:

* Prohlédněte si prostředek na [Azure Portal](https://portal.azure.com/).

Po získání klíče z prostředku [Vytvořte proměnnou prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro klíč s názvem `AZURE_CONTENTMODERATOR_KEY` .

## <a name="create-a-new-gradle-project"></a>Vytvořit nový projekt Gradle

V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj. 

```console
mkdir myapp && cd myapp
```
Spusťte `gradle init`. Tento příkaz vytvoří základní soubory sestavení pro Gradle, včetně *Build. Gradle. kts*, který se používá za běhu k vytvoření a konfiguraci vaší aplikace. Spusťte tento příkaz z pracovního adresáře:

```console
gradle init --type basic
```

Po zobrazení výzvy k výběru skriptu sestavení DSL vyberte **Kotlin**.

## <a name="install-the-client-library"></a>Instalace klientské knihovny

Vyhledejte *Build. Gradle. kts* a otevřete ho pomocí vašeho preferovaného integrovaného vývojového prostředí (IDE) nebo textového editoru. Pak zkopírujte do následující konfigurace sestavení. Tato konfigurace definuje projekt jako aplikaci Java, jejíž vstupní bod je třídou **ContentModeratorQuickstart**. Importuje knihovnu klienta Content Moderator a také sadu SDK gson pro serializaci JSON.

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

V pracovním adresáři spusťte následující příkaz, který vytvoří zdrojovou složku projektu.

```console
mkdir -p src/main/java
```

Pak v nové složce vytvořte soubor s názvem *ContentModeratorQuickstart. Java* . Otevřete soubor v preferovaném editoru nebo IDE a naimportujte následující knihovny v horní části:

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

## <a name="object-model"></a>Objektový model

Následující třídy zpracovávají některé hlavní funkce klientské knihovny Content Moderator Java.

|Název|Popis|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable)|Tato třída je potřebná pro všechny funkce Content Moderator. Vytvoří se jeho instance s informacemi o předplatném a Vy ho použijete k vytvoření instancí jiných tříd.|
|[ImageModeration](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations?view=azure-java-stable)|Tato třída poskytuje funkce pro analýzu obrázků pro obsah pro dospělé, osobní údaje nebo lidské obličeje.|
|[TextModerations](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations?view=azure-java-stable)|Tato třída poskytuje funkce pro analýzu textu pro jazyk, vulgární výrazy, chyby a osobní údaje.|
|[Recenze](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews?view=azure-java-stable)|Tato třída poskytuje funkce v rozhraních API pro revize, včetně metod vytváření úloh, vlastních pracovních postupů a lidských recenzí.|


## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí Content Moderator klientské knihovny pro Java:

* [Ověření klienta](#authenticate-the-client)
* [Střední obrázky](#moderate-images)

## <a name="authenticate-the-client"></a>Ověření klienta

> [!NOTE]
> Tento krok předpokládá, že jste [vytvořili proměnnou prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro Content moderator klíč s názvem `AZURE_CONTENTMODERATOR_KEY` .

V `main` metodě aplikace vytvořte objekt [ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable) pomocí hodnoty koncového bodu předplatného a proměnné prostředí klíče předplatného. 

> [!NOTE]
> Pokud jste po spuštění aplikace vytvořili proměnnou prostředí, budete muset zavřít a znovu otevřít Editor, rozhraní IDE nebo prostředí, na kterém je spuštěný, abyste měli přístup k této proměnné.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]

## <a name="moderate-images"></a>Střední obrázky

### <a name="get-sample-images"></a>Získat ukázkové obrázky

V části **Src/Main/** složka projektu vytvořte složku **Resources (prostředky** ) a přejděte na ni. Pak vytvořte nový textový soubor *ImageFiles.txt*. V tomto souboru přidáte adresy URL obrázků k analýze &mdash; jedné adresy URL na každém řádku. Můžete použít následující ukázkové obrázky:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>Definovat pomocnou třídu

Potom v souboru *ContentModeratorQuickstart. Java* přidejte do třídy **ContentModeratorQuickstart** následující definici třídy. Tato vnitřní třída bude použita později v procesu moderování obrázku.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]

### <a name="iterate-through-images"></a>Iterovat prostřednictvím imagí

Dále do dolní části metody přidejte následující kód `main` . Nebo ho můžete přidat k samostatné metodě, která je volána z `main` . Tento kód prochází každým řádkem _ImageFiles.txt_ souboru.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_iterate)]

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
> [Koncepty moderování obrázků](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Co je Azure Content Moderator?](../../overview.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java).