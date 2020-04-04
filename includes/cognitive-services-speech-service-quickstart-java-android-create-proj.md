---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: travisw
ms.openlocfilehash: 8b187e058299f8aa8b762231c0ed1e708e5ad9d1
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80659129"
---
1. Spusťte Android Studio a v uvítacím **okně** vyberte **Spustit nový projekt Android Studio.**

    ![Snímek obrazovky s uvítacím oknem Android Studia](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. Zobrazí se Průvodce **výběrem projektu.** Ve výběrovém poli aktivity vyberte **Telefon a Tablet** a **Prázdná aktivita.** Vyberte **další**.

   ![Snímek obrazovky s průvodcem výběrem projektu](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. Na obrazovce **Konfigurace projektu** zadejte *úvodní obrazovku* jako **název** a zadejte *samples.speech.cognitiveservices.microsoft.com* jako **název balíčku**. Pak vyberte adresář projektu. Pro **minimální úroveň rozhraní API**vyberte rozhraní API **23: Android 6.0 (Marshmallow)**. Všechna ostatní zaškrtávací políčka ponechejte vyškrtli a vyberte **Dokončit**.

   ![Snímek obrazovky s průvodcem Konfigurace projektu](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

Android Studiu chvíli trvá, než nový projekt pro Android připraví. Dále nakonfigurujte projekt tak, aby věděl o sadě Azure Cognitive Services Speech SDK a používal java 8.

[!INCLUDE [License notice](cognitive-services-speech-service-license-notice.md)]

Aktuální verze sady SDK pro rozpoznávání řeči služeb Cognitive Services je 1.11.0.

Sada Speech SDK pro Android je zabalena jako [AAR (Knihovna Android)](https://developer.android.com/studio/projects/android-library), která obsahuje potřebné knihovny a požadovaná oprávnění androida.
Hostuje se v úložišti Maven\/na adrese https: /csspeechstorage.blob.core.windows.net/maven/.

Nastavte projekt tak, aby používal sadu Speech SDK. Otevřete okno **Struktura projektu** výběrem**struktury projektu** **souboru** > z panelu nabídek Android Studio. V okně **Struktura projektu** proveďte následující změny:

1. V seznamu na levé straně okna vyberte **Project** (Projekt). Upravte výchozí nastavení **úložiště knihovny** připojením čárky a adresy URL úložiště Maven uzavřené\/v jednoduchých uvozovkách: 'https: /csspeechstorage.blob.core.windows.net/maven/'

   ![Snímek obrazovky okna se strukturou projektu](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. Na stejné obrazovce na levé straně vyberte **aplikaci**. Pak vyberte kartu **Dependencies** (Závislosti) v horní části okna. V rozevírací**+** nabídce vyberte zelené znaménko plus ( ) a **vyberte závislost knihovny.**

   ![Snímek obrazovky se závislostí knihovny](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. Do okna, které se zobrazí, zadejte název a verzi sady Speech SDK pro Android, *com.microsoft.cognitiveservices.speech:client-sdk:1.11.0*. Pak vyberte **OK**.
   Sada Speech SDK by měla být nyní přidána do seznamu závislostí, jak je znázorněno:

   ![Snímek obrazovky sady Speech SDK v seznamu závislostí](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Vyberte kartu **Vlastnosti.** Pro **kompatibilitu zdroje** i **cílovou kompatibilitu**vyberte **1.9**.

   ![Snímek obrazovky s kompatibilitou zdroje a cílovou kompatibilitou](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. Výběrem **možnosti OK** zavřete okno **Struktura projektu** a aplikujte změny na projekt.
