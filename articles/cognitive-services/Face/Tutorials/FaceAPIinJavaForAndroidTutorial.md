---
title: 'Kurz: Rozpoznávání a orámování tváří na obrázku pomocí sady Android SDK'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu vytvoříte jednoduchou aplikaci pro Android, která používá rozhraní API pro rozpoznávání tváře ke zjišťování a snímků tváří v obrázku.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 11/12/2018
ms.author: pafarley
ms.openlocfilehash: 92344943d2ec19f6730387221974a001e6a2045a
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2018
ms.locfileid: "51852890"
---
# <a name="tutorial-create-an-android-app-to-detect-and-frame-faces-in-an-image"></a>Kurz: Vytvoření aplikace pro Android k rozpoznání a orámování tváří na obrázku

V tomto kurzu vytvoříte jednoduchou aplikaci s Androidem, která používá rozhraní API pro rozpoznávání tváře Azure pomocí sady Java SDK, k detekci lidských tváří v obrázku. Aplikace zobrazí vybrané bitové kopie a nakreslí rámeček kolek každou zjištěnou plochu.

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> - Vytvoření aplikace pro Android
> - Nainstalujte klientské knihovny rozhraní API pro rozpoznávání tváře
> - Použití klientské knihovny k rozpoznání tváří v obrázku
> - Zakreslení rámečku kolem každé rozpoznané tváře

![Snímek fotografie s tvářemi orámovanými červeným obdélníkem v Androidu](../Images/android_getstarted2.1.PNG)

Úplný ukázkový kód je k dispozici v [Cognitive Services Face Android](https://github.com/Azure-Samples/cognitive-services-face-android-sample) úložišti na Githubu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete. 

## <a name="prerequisites"></a>Požadavky

- Klíč rozhraní API pro rozpoznávání tváře předplatného. Můžete získat bezplatné předplatné zkušební verze klíče z [zkuste služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Nebo, postupujte podle pokynů v [vytvoření účtu služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) k odběru služby API pro rozpoznávání tváře a získejte klíč.
- [Android Studio](https://developer.android.com/studio/) s úrovní rozhraní API 22 nebo novější (Klientská knihovna pro rozpoznávání tváře vyžadují).

## <a name="create-the-android-studio-project"></a>Vytvoření projektu Android Studio

Postupujte podle těchto kroků a vytvořte nový projekt aplikace pro Android.

1. V nástroji Android Studio vyberte **spusťte nový projekt Android Studio**.
1. Na obrazovce **Create Android Project** (Vytvořit projekt pro Android) v případě potřeby upravte výchozí pole a potom klikněte na **Next** (Další).
1. Na **cílové zařízení s Androidem** obrazovky, pomocí selektoru rozevíracího seznamu vyberte **rozhraní API 22** nebo později, pak klikněte na tlačítko **Další**.
1. Vyberte **Empty Activity** (Prázdná aktivita) a potom klikněte na **Next** (Další).
1. Zrušte zaškrtnutí políčka **Backwards Compatibility** (Zpětná kompatibilita) a potom klikněte na **Finish** (Dokončit).

## <a name="add-the-initial-code"></a>Přidání počátečního kódu

### <a name="create-the-ui"></a>Vytvoření uživatelského rozhraní

Otevřít *activity_main.xml*. V editoru rozložení vyberte **Text** kartu a potom nahraďte obsah následujícím kódem.

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/res/layout/activity_main.xml?range=1-18)]

### <a name="create-the-main-class"></a>Vytvořit hlavní třída

Otevřít *MainActivity.java* a nahraďte existující `import` příkazy následujícím kódem.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=3-11)]

Potom nahraďte obsah **MainActivity** třídy následujícím kódem. Tím se vytvoří obslužnou rutinu události na **tlačítko** , který spouští novou aktivitu, aby uživatel mohl vybrat obrázek. Zobrazí obrázek v **ImageView**.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=29-68)]

### <a name="try-the-app"></a>Zkuste aplikaci

Odkomentujte volání **detectAndFrame** v **onActivityResult** metody. Stiskněte klávesu **spustit** v nabídce testovat svou aplikaci. Při spuštění aplikace, buď v emulátoru nebo připojeného zařízení, klikněte na tlačítko **Procházet** v dolní části. By se zobrazit dialogové okno pro výběr souboru zařízení. Zvolit bitovou kopii a ověřte, zda se zobrazí v okně. Zavřete aplikaci a pokračujte k dalšímu kroku.

![Snímek fotografie s tvářemi v Androidu](../Images/android_getstarted1.1.PNG)

## <a name="add-the-face-sdk"></a>Přidat sadu SDK pro rozpoznávání tváře

### <a name="add-the-gradle-dependency"></a>Přidat závislost Gradle

V podokně **Project** (Projekt) použijte selektor rozevíracího seznamu a vyberte **Android**. Rozbalte **Gradle Scripts** (Skripty Gradle) a potom otevřete *build.gradle (Module: app)*. Přidejte závislost pro klientskou knihovnu služby Rozpoznávání tváře `com.microsoft.projectoxford:face:1.4.3`, jak ukazuje následující snímek obrazovky, a potom klikněte na **Sync Now** (Synchronizovat).

![Snímek souboru build.gradle aplikace v sadě Android Studio](../Images/face-tut-java-gradle.png)

### <a name="add-the-face-related-project-code"></a>Přidejte kód pro rozpoznávání tváře související projektu

Přejděte zpět na **MainActivity.java** a přidejte následující `import` příkazy:

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=13-14)]

Vložte následující kód **MainActivity** třídy výše **onCreate** metody:

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=17-27)]

Budete muset nahradit `<Subscription Key>` s klíči předplatného. Také nahraďte `<API endpoint>` s váš koncový bod rozhraní API pro rozpoznávání tváře pomocí identifikátoru příslušnou oblast pro váš klíč (najdete v článku [dokumenty k rozhraní API pro rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) seznam všechny koncové body oblasti). Bezplatné předplatné zkušební verze klíče jsou generovány v **westus** oblasti.

V podokně **Project** rozbalte **app** a potom **manifests** a otevřete *AndroidManifest.xml*. Vložte následující element jako přímý podřízený elementu `manifest`:

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/AndroidManifest.xml?range=5)]

## <a name="upload-image-and-detect-faces"></a>Nahrajte obrázek a rozpoznávání tváří

Vaše aplikace bude voláním rozpoznávání tváří **FaceServiceClient.detect** metoda, která zabalí [rozpoznat](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) rozhraní REST API a vrátí seznam hodnot **pro rozpoznávání tváře** instancí.

Každá vrácená **pro rozpoznávání tváře** zahrnuje obdélník a určit jeho umístění v kombinaci s řadou volitelné obličejových atributů. V tomto příkladu jsou požadovány pouze obdélníky pro rozpoznávání tváře.

Vložit do následujících dvou metod **MainActivity** třídy. Všimněte si, že po dokončení rozpoznávání tváří v aplikaci označuje jako **drawFaceRectanglesOnBitmap** metoda změnit **ImageView**. Tato metoda dále definujete.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=70-150)]

## <a name="draw-face-rectangles"></a>Kreslení obdélníků pro rozpoznávání tváře

Vložte následující metodu do pomocné rutiny **MainActivity** třídy. Tato metoda Nakreslí obdélník kolem každé zjištěné obličej pomocí souřadnice obdélník jednotlivých **pro rozpoznávání tváře** instance.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?range=152-173)]

A konečně, zrušte komentář u volání **detectAndFrame** metoda **onActivityResult**.

## <a name="run-the-app"></a>Spuštění aplikace

Spusťte aplikaci a vyhledejte obrázek, který obsahuje nějakou tvář. Počkejte několik sekund, než služba Rozpoznávání tváře zareaguje. Měli byste vidět červeným rámečkem na všech tváří na obrázku.

![Android – snímek obrazovky ploch s červenou obdélníky vykreslen kolem sebe](../Images/android_getstarted2.1.PNG)

## <a name="next-steps"></a>Další postup

V tomto kurzu se naučili základní postup pro použití sady Java SDK API pro rozpoznávání tváře a vytvořili aplikaci ke zjišťování a snímků tváří v obrázku. Další informace o podrobnosti o rozpoznávání tváří v dalším kroku.

> [!div class="nextstepaction"]
> [Postup při rozpoznávání tváří v obrázku](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)