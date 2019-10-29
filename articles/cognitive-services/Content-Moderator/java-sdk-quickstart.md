---
title: 'Rychlý Start: Content Moderator klientskou knihovnu pro Java | Microsoft Docs'
description: Začněte s knihovnou klienta Content Moderator pro Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 10/25/2019
ms.author: pafarley
ms.openlocfilehash: 9f31d54314c6d6bd7a7f9ae2d134c1281bfe9294
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044093"
---
# <a name="quickstart-content-moderator-client-library-for-java"></a>Rychlý Start: Content Moderator Klientská knihovna pro Java

Začněte s knihovnou klienta Content Moderator pro Java. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy. Content Moderator je služba rozpoznávání, která kontroluje text, obrázky a obsah videa pro materiál, který je potenciálně urážlivý, rizikové nebo jinak nežádoucí. Pokud se takový materiál najde, použije služba pro obsah odpovídající popisky (příznaky). Vaše aplikace pak může obsah označený příznakem zpracovat a zajistit tak dodržování předpisů nebo zachování zamýšleného prostředí pro uživatele.

Pomocí klientské knihovny Content Moderator pro jazyk Java:

* Střední obrázky pro dospělé nebo pikantní obsah, text nebo lidské obličeje.

[Referenční dokumentace](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-java-stable) | [artefaktem (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator) | [ukázek](https://docs.microsoft.com/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* Aktuální verze sady [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Nástroj Gradle Build](https://gradle.org/install/)nebo jiný správce závislostí.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-content-moderator-azure-resource"></a>Vytvoření prostředku Azure Content Moderator

Azure Cognitive Services jsou představovány prostředky Azure, ke kterým jste se přihlásili. Vytvořte prostředek pro Content Moderator pomocí [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) nebo rozhraní příkazového [řádku Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na vašem místním počítači. Můžete také:

* Získejte [zkušební klíč](https://azure.microsoft.com/try/cognitive-services/#decision) platný po dobu sedmi dnů zdarma. Po registraci bude k dispozici na [webu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Prohlédněte si prostředek na [Azure Portal](https://portal.azure.com/).

Po získání klíče ze zkušebního předplatného nebo prostředku [vytvořte pro klíč proměnnou prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) s názvem `AZURE_CONTENTMODERATOR_KEY`.

### <a name="create-a-new-gradle-project"></a>Vytvořit nový projekt Gradle

V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj. 

```console
mkdir myapp && cd myapp
```
Spusťte `gradle init`. Tento příkaz vytvoří základní soubory sestavení pro Gradle, včetně *Build. Gradle. kts*, který se používá za běhu k vytvoření a konfiguraci vaší aplikace. Spusťte tento příkaz z pracovního adresáře:

```console
gradle init --type basic
```

Po zobrazení výzvy k výběru skriptu sestavení DSL vyberte **Kotlin**.

Vyhledejte *Build. Gradle. kts* a otevřete ho pomocí vašeho preferovaného integrovaného vývojového prostředí (IDE) nebo textového editoru. Pak zkopírujte do následující konfigurace sestavení. Tato konfigurace definuje projekt jako aplikaci Java, jejíž vstupní bod je třídou **ContentModeratorQuickstart**. Naimportuje sadu Content Moderator SDK i sadu gson SDK pro serializaci JSON.

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

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

## <a name="object-model"></a>Objektový model

Následující třídy zpracovávají některé hlavní funkce Content Moderator Java SDK.

|Name (Název)|Popis|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable)|Tato třída je potřebná pro všechny funkce Content Moderator. Vytvoří se jeho instance s informacemi o předplatném a Vy ho použijete k vytvoření instancí jiných tříd.|
|[ImageModeration](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations?view=azure-java-stable)|Tato třída poskytuje funkce pro analýzu obrázků pro obsah pro dospělé, osobní údaje nebo lidské obličeje.|
|[TextModerations](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations?view=azure-java-stable)|Tato třída poskytuje funkce pro analýzu textu pro jazyk, vulgární výrazy, chyby a osobní údaje.|
|[Revize](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews?view=azure-java-stable)|Tato třída poskytuje funkce v rozhraních API pro revize, včetně metod vytváření úloh, vlastních pracovních postupů a lidských recenzí.|


## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí Content Moderator klientské knihovny pro Java:

* [Ověření klienta](#authenticate-the-client)
* [Střední obrázky](#moderate-images)

## <a name="authenticate-the-client"></a>Ověření klienta

> [!NOTE]
> Tento krok předpokládá, že jste pro svůj Content Moderator klíč [vytvořili proměnnou prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) s názvem `AZURE_CONTENTMODERATOR_KEY`.

V metodě `main` aplikace vytvořte objekt [ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable) pomocí hodnoty koncového bodu předplatného a proměnné prostředí klíče předplatného. 

> [!NOTE]
> Pokud jste po spuštění aplikace vytvořili proměnnou prostředí, budete muset zavřít a znovu otevřít Editor, rozhraní IDE nebo prostředí, na kterém je spuštěný, abyste měli přístup k této proměnné.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]

## <a name="moderate-images"></a>Střední obrázky

### <a name="get-images"></a>Získat obrázky

V části **Src/Main/** složka projektu vytvořte složku **Resources (prostředky** ) a přejděte na ni. Pak vytvořte nový textový soubor *ImageFiles. txt*. V tomto souboru přidáte adresy URL obrázků k analýze&mdash;jednu adresu URL na každém řádku. Můžete použít následující ukázkové obrázky:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>Definovat pomocnou třídu

Potom v souboru *ContentModeratorQuickstart. Java* přidejte do třídy **ContentModeratorQuickstart** následující definici třídy. Tato vnitřní třída bude použita později v procesu moderování obrázku.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]

### <a name="iterate-through-images"></a>Iterovat prostřednictvím imagí

Dále přidejte následující kód do dolní části metody `main`. Nebo ho můžete přidat k samostatné metodě, která je volána z `main`. Tento kód provede jednotlivé řádky souboru _ImageFiles. txt_ .

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Kontrolovat obsah pro dospělé/pikantní
Tento řádek kódu kontroluje obrázek na dané adrese URL pro dospělé nebo pikantní obsah. Informace o těchto pojmech najdete v koncepční příručce pro moderování obrázků.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>Kontrolovat text
Tento řádek kódu kontroluje obrázek pro viditelný text.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Kontrolovat obličeje
Tento řádek kódu kontroluje obrázek pro lidské obličeje.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

Nakonec uložte vrácené informace do seznamu `EvaluationData`.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>Vytisknout výsledky

Po `while` smyčce přidejte následující kód, který vytiskne výsledky do konzoly a do výstupního souboru, *Src/Main/Resources/ModerationOutput. JSON*.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

Zavřete příkaz `try` a přidejte příkaz `catch` k dokončení metody.

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="run-the-application"></a>Spuštění aplikace

Aplikaci můžete vytvořit pomocí:

```console
gradle build
```

Spusťte aplikaci pomocí příkazu `gradle run`:

```console
gradle run
```

Pak přejděte do souboru *Src/Main/Resources/ModerationOutput. JSON* a podívejte se na výsledky Moderování obsahu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak používat knihovnu Content Moderator Java k provádění úloh moderování. V dalším kroku se dozvíte další informace o moderování imagí nebo jiných médií. Přečtěte si koncepční průvodce.

> [!div class="nextstepaction"]
>[Koncepty moderování obrázků](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Co je Azure Content Moderator?](./overview.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/blob/master/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java).