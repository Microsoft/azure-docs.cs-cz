---
title: 'Kurz: Rozpoznávání a orámování tváří na obrázku pomocí sady Android SDK'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu vytvoříte jednoduchou aplikaci pro Android, která používá službu Face k detekci a vytváření rámů v obraze.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 8d5bef141f83eedaa996bb63c1fb814aeb6af197
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76165964"
---
# <a name="tutorial-create-an-android-app-to-detect-and-frame-faces-in-an-image"></a>Kurz: Vytvoření aplikace pro Android k rozpoznání a orámování tváří na obrázku

V tomto kurzu vytvoříte aplikaci pro Android, která používá službu Azure Face prostřednictvím sady Java SDK k detekci lidských tváří v bitové kopii. Aplikace zobrazí vybraný obrázek a nakreslí rámeček kolem každé zjištěné plochy.

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> - Vytvoření aplikace pro Android
> - Instalace klientské knihovny Face
> - Použití klientské knihovny k rozpoznání tváří v obrázku
> - Zakreslení rámečku kolem každé rozpoznané tváře

![Snímek fotografie s tvářemi orámovanými červeným obdélníkem v Androidu](../Images/android_getstarted2.1.PNG)

Kompletní ukázkový kód je k dispozici v úložišti [Android Cognitive Services Face](https://github.com/Azure-Samples/cognitive-services-face-android-sample) na GitHubu.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete. 

## <a name="prerequisites"></a>Požadavky

- Klíč předplatného Face. Můžete získat bezplatný klíč zkušebního předplatného od [společnosti Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Nebo postupujte podle pokynů v [tématu Vytvoření účtu služeb Cognitive Services,](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) abyste se přihlásili ke službě Face a získali klíč. Potom [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro řetězec koncového `FACE_SUBSCRIPTION_KEY` klíče `FACE_ENDPOINT`a služby s názvem a , resp.
- Libovolné vydání [Visual Studia 2015 nebo 2017](https://www.visualstudio.com/downloads/).
- [Android Studio](https://developer.android.com/studio/) s úrovní rozhraní API 22 nebo novější (vyžadováno klientskou knihovnou Face).

## <a name="create-the-android-studio-project"></a>Vytvoření projektu Android Studio

Podle těchto kroků vytvořte nový projekt aplikace pro Android.

1. V Android Studiu vyberte **Spustit nový projekt Android Studio**.
1. Na obrazovce **Create Android Project** (Vytvořit projekt pro Android) v případě potřeby upravte výchozí pole a potom klikněte na **Next** (Další).
1. Na obrazovce **Cílová zařízení androida** zvolte pomocí rozbalovacího voliče **rozhraní API 22** nebo novější a pak klepněte na tlačítko **Další**.
1. Vyberte **Empty Activity** (Prázdná aktivita) a potom klikněte na **Next** (Další).
1. Zrušte zaškrtnutí políčka **Backwards Compatibility** (Zpětná kompatibilita) a potom klikněte na **Finish** (Dokončit).

## <a name="add-the-initial-code"></a>Přidání počátečního kódu

### <a name="create-the-ui"></a>Vytvoření nového ui

Otevřete *soubor activity_main.xml*. V Editoru rozložení vyberte kartu **Text** a nahraďte obsah následujícím kódem.

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/res/layout/activity_main.xml?name=snippet_activitymain)]

### <a name="create-the-main-class"></a>Vytvoření hlavní třídy

Otevřete *MainActivity.java* a `import` nahraďte existující příkazy následujícím kódem.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_imports)]

Potom nahraďte obsah **MainActivity** třídy s následujícím kódem. Tím se vytvoří obslužná rutina události na **button,** který spustí novou aktivitu, aby uživatel i vybrat obrázek. Zobrazí obrázek v **ImageView**.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_mainactivity_methods)]

### <a name="try-the-app"></a>Vyzkoušejte aplikaci

Komentář volání **detectAndFrame** v **onActivityResult** metoda. Pak stiskněte **spustit** v nabídce otestovat svou aplikaci. Když se aplikace otevře, v emulátoru nebo připojeném zařízení, klikněte na **procházet** v dolní části. Měl by se zobrazit dialogové okno pro výběr souboru zařízení. Zvolte obrázek a ověřte, zda se zobrazí v okně. Potom aplikaci zavřete a překroucete k dalšímu kroku.

![Snímek fotografie s tvářemi v Androidu](../Images/android_getstarted1.1.PNG)

## <a name="add-the-face-sdk"></a>Přidání sady Face SDK

### <a name="add-the-gradle-dependency"></a>Přidání závislosti Gradle

V podokně **Project** (Projekt) použijte selektor rozevíracího seznamu a vyberte **Android**. Rozbalte **Gradle Scripts** (Skripty Gradle) a potom otevřete *build.gradle (Module: app)*. Přidejte závislost pro klientskou knihovnu služby Rozpoznávání tváře `com.microsoft.projectoxford:face:1.4.3`, jak ukazuje následující snímek obrazovky, a potom klikněte na **Sync Now** (Synchronizovat).

![Snímek souboru build.gradle aplikace v sadě Android Studio](../Images/face-tut-java-gradle.png)

### <a name="add-the-face-related-project-code"></a>Přidání kódu projektu souvisejícího s tváří

Vraťte se na **MainActivity.java** a přidejte následující `import` příkazy:

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_face_imports)]

Potom vložte následující kód do **MainActivity** třídy, nad **onCreate** metoda:

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_mainactivity_fields)]

V podokně **Project** rozbalte **app** a potom **manifests** a otevřete *AndroidManifest.xml*. Vložte následující element jako přímý podřízený elementu `manifest`:

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/AndroidManifest.xml?name=snippet_manifest_entry)]

## <a name="upload-image-and-detect-faces"></a>Nahrání obrázku a detekce tváří

Vaše aplikace detekuje tváře voláním **faceClient.Face.DetectWithStreamAsync** metoda, která zabalí [rozhraní PAMĚŤ](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) ROZHRANÍ API A vrátí seznam **face** instance.

Každá vrácená **plocha** obsahuje obdélník označující jeho umístění v kombinaci s řadou volitelných atributů plochy. V tomto příkladu jsou požadovány pouze obdélníky plochy.

Vložte následující dvě metody do **MainActivity** třídy. Po dokončení detekce obličeje aplikace volá **metodu drawFaceRectanglesOnBitmap** k úpravě **ImageView**. Tuto metodu definujete dále.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_detection_methods)]

## <a name="draw-face-rectangles"></a>Kreslení obdélníků ploch

Vložte následující pomocnou metodu do **mainactivity** třídy. Tato metoda nakreslí obdélník kolem každé zjištěné plochy pomocí obdélníkové souřadnice každé instance **Face.**

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_drawrectangles)]

Nakonec odkomentujte volání metody **detectAndFrame** v **onActivityResult**.

## <a name="run-the-app"></a>Spuštění aplikace

Spusťte aplikaci a vyhledejte obrázek, který obsahuje nějakou tvář. Počkejte několik sekund, než služba Rozpoznávání tváře zareaguje. Na každé z ploch v obraze byste měli vidět červený obdélník.

![Android screenshot z tváří s červenými obdélníky nakreslené kolem nich](../Images/android_getstarted2.1.PNG)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili základní proces pro použití sady Face Java SDK a vytvořili aplikaci pro detekci a vytváření rámečků v obraze. Dále se dozvíte více o podrobnostech detekce obličeje.

> [!div class="nextstepaction"]
> [Postup při rozpoznávání tváří v obrázku](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
