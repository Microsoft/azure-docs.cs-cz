---
title: 'Kurz: Rozpoznávání a orámování tváří na obrázku pomocí sady Android SDK'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu vytvoříte jednoduchou aplikaci pro Android, která k detekci a orámování plošek v imagi používá službu obličeje.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 8d5bef141f83eedaa996bb63c1fb814aeb6af197
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2020
ms.locfileid: "76165964"
---
# <a name="tutorial-create-an-android-app-to-detect-and-frame-faces-in-an-image"></a>Kurz: Vytvoření aplikace pro Android k rozpoznání a orámování tváří na obrázku

V tomto kurzu vytvoříte aplikaci pro Android, která využívá službu Azure Face prostřednictvím sady Java SDK k detekci lidských plošek v obraze. Aplikace zobrazí vybrané bitové kopie a nakreslí rámeček kolek každou zjištěnou plochu.

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> - Vytvoření aplikace pro Android
> - Instalace klientské knihovny pro tvář
> - Použití klientské knihovny k rozpoznání tváří v obrázku
> - Zakreslení rámečku kolem každé rozpoznané tváře

![Snímek fotografie s tvářemi orámovanými červeným obdélníkem v Androidu](../Images/android_getstarted2.1.PNG)

Kompletní vzorový kód je k dispozici v úložišti [Cognitive Services obličeje pro Android](https://github.com/Azure-Samples/cognitive-services-face-android-sample) na GitHubu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete. 

## <a name="prerequisites"></a>Požadavky

- Klíč předplatného pro vytvoření obličeje. Můžete získat bezplatné předplatné zkušební verze klíče z [zkuste služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Případně postupujte podle pokynů v části [Vytvoření účtu Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pro přihlášení k odběru služby obličeje a Získejte svůj klíč. Pak [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro řetězec klíčového a koncového bodu služby s názvem `FACE_SUBSCRIPTION_KEY` a `FACE_ENDPOINT`v uvedeném pořadí.
- Libovolná edice sady [Visual Studio 2015 nebo 2017](https://www.visualstudio.com/downloads/).
- [Android Studio](https://developer.android.com/studio/) s rozhraním API úrovně 22 nebo novějším (vyžaduje Klientská knihovna pro obličej).

## <a name="create-the-android-studio-project"></a>Vytvoření projektu Android Studio

Pomocí těchto kroků vytvořte nový projekt aplikace pro Android.

1. V Android Studio vyberte **Spustit nový projekt Android Studio**.
1. Na obrazovce **Create Android Project** (Vytvořit projekt pro Android) v případě potřeby upravte výchozí pole a potom klikněte na **Next** (Další).
1. Na obrazovce **cílová zařízení s Androidem** použijte selektor rozevíracího seznamu a zvolte **rozhraní API 22** nebo novější a pak klikněte na **Další**.
1. Vyberte **Empty Activity** (Prázdná aktivita) a potom klikněte na **Next** (Další).
1. Zrušte zaškrtnutí políčka **Backwards Compatibility** (Zpětná kompatibilita) a potom klikněte na **Finish** (Dokončit).

## <a name="add-the-initial-code"></a>Přidání počátečního kódu

### <a name="create-the-ui"></a>Vytvoření uživatelského rozhraní

Otevřete *soubor activity_main. XML*. V editoru rozložení vyberte kartu **text** a pak obsah nahraďte následujícím kódem.

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/res/layout/activity_main.xml?name=snippet_activitymain)]

### <a name="create-the-main-class"></a>Vytvoření hlavní třídy

Otevřete *MainActivity. Java* a nahraďte existující příkazy `import` následujícím kódem.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_imports)]

Pak nahraďte obsah třídy **MainActivity** následujícím kódem. Tím se vytvoří obslužná rutina události na **tlačítku** , které spustí novou aktivitu, aby uživatel mohl vybrat obrázek. Zobrazí se obrázek v **ImageView**.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_mainactivity_methods)]

### <a name="try-the-app"></a>Zkuste aplikaci

Odkomentujte volání **detectAndFrame** v metodě **onActivityResult** . Potom stisknutím klávesy **Run** v nabídce otestujte svoji aplikaci. Po otevření aplikace buď v emulátoru nebo připojeném zařízení, klikněte na **Procházet** dole. Zobrazí se dialogové okno pro výběr souboru zařízení. Vyberte obrázek a ověřte, zda se zobrazí v okně. Pak aplikaci zavřete a přejděte k dalšímu kroku.

![Snímek fotografie s tvářemi v Androidu](../Images/android_getstarted1.1.PNG)

## <a name="add-the-face-sdk"></a>Přidat sadu SDK pro obličej

### <a name="add-the-gradle-dependency"></a>Přidat závislost Gradle

V podokně **Project** (Projekt) použijte selektor rozevíracího seznamu a vyberte **Android**. Rozbalte **Gradle Scripts** (Skripty Gradle) a potom otevřete *build.gradle (Module: app)* . Přidejte závislost pro klientskou knihovnu služby Rozpoznávání tváře `com.microsoft.projectoxford:face:1.4.3`, jak ukazuje následující snímek obrazovky, a potom klikněte na **Sync Now** (Synchronizovat).

![Snímek souboru build.gradle aplikace v sadě Android Studio](../Images/face-tut-java-gradle.png)

### <a name="add-the-face-related-project-code"></a>Přidat kód projektu související s obličejem

Vraťte se do **MainActivity. Java** a přidejte následující příkazy `import`:

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_face_imports)]

Pak vložte následující kód do třídy **MainActivity** nad metodu **Create** :

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_mainactivity_fields)]

V podokně **Project** rozbalte **app** a potom **manifests** a otevřete *AndroidManifest.xml*. Vložte následující element jako přímý podřízený elementu `manifest`:

[!code-xml[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/AndroidManifest.xml?name=snippet_manifest_entry)]

## <a name="upload-image-and-detect-faces"></a>Nahrát obrázek a detekovat plošky

Vaše aplikace detekuje obličeje voláním metody **faceClient. Face. DetectWithStreamAsync** , která zabalí REST API [detekce](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) a vrátí seznam instancí **obličeje** .

Každý vrácený **obličej** obsahuje obdélník, který označuje jeho umístění v kombinaci s řadou volitelných atributů Facet. V tomto příkladu jsou požadovány pouze obdélníky obličeje.

Do třídy **MainActivity** vložte následující dvě metody. Když se rozpoznávání obličeje dokončí, aplikace zavolá metodu **drawFaceRectanglesOnBitmap** pro úpravu **ImageView**. Tuto metodu budete definovat jako další.

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_detection_methods)]

## <a name="draw-face-rectangles"></a>Kreslení obdélníků pro rozpoznávání tváře

Do třídy **MainActivity** vložte následující pomocnou metodu. Tato metoda nakreslí obdélník kolem každé zjištěné plochy pomocí souřadnic obdélníku každé instance **obličeje** .

[!code-java[](~/cognitive-services-face-android-detect/FaceTutorial/app/src/main/java/com/contoso/facetutorial/MainActivity.java?name=snippet_drawrectangles)]

Nakonec Odkomentujte volání metody **detectAndFrame** v **onActivityResult**.

## <a name="run-the-app"></a>Spuštění aplikace

Spusťte aplikaci a vyhledejte obrázek, který obsahuje nějakou tvář. Počkejte několik sekund, než služba Rozpoznávání tváře zareaguje. Na všech plochách v obrázku byste měli vidět červený obdélník.

![Snímek obrazovky se systémem Android s červenými obdélníky nakreslenými kolem nich](../Images/android_getstarted2.1.PNG)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili základní proces používání sady Face Java SDK a vytvořili jste aplikaci pro detekci a orámování plošek v obraze. V dalším kroku se dozvíte více o podrobnostech detekce obličeje.

> [!div class="nextstepaction"]
> [Postup při rozpoznávání tváří v obrázku](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
