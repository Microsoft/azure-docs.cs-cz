---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: travisw
ms.openlocfilehash: 2efe17bdf597b7e3ba7d0277f06d4b24e9b51db3
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096874"
---
1. Spusťte Android Studio a v **uvítacím** okně vyberte **spustit nový projekt Android Studio** .

    ![Snímek obrazovky s uvítacím oknem Android Studia](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. Zobrazí se průvodce **zvolit projekt** . V poli pro výběr aktivity vyberte možnost **telefon a tablet** a **prázdná aktivita** . Vyberte **Další**.

   ![Snímek obrazovky s průvodcem pro výběr projektu](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. Na obrazovce **konfigurace projektu** zadejte jako **název** do pole *rychlý Start* a jako **název balíčku**zadejte *Samples.Speech.cognitiveservices.Microsoft.com* . Pak vyberte adresář projektu. V případě **minimální úrovně rozhraní API**vyberte **rozhraní API 23: Android 6,0 (Marshmallow)**. Zrušte zaškrtnutí všech ostatních políček a vyberte **Dokončit**.

   ![Snímek obrazovky Průvodce konfigurací projektu](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

Android Studiu chvíli trvá, než nový projekt pro Android připraví. Dále nakonfigurujte projekt tak, aby věděli o sadě Azure Cognitive Services Speech SDK a používání Java 8.

[!INCLUDE [License notice](cognitive-services-speech-service-license-notice.md)]

Aktuální verze sady Cognitive Services Speech SDK je 1.13.0.

Sada Speech SDK pro Android je zabalená jako [Knihovna AAR (Android Library)](https://developer.android.com/studio/projects/android-library), která zahrnuje nezbytné knihovny a požadovaná oprávnění pro Android.
Hostuje se v úložišti Maven na adrese https: \/ /csspeechstorage.blob.Core.Windows.NET/Maven/.

Nastavte projekt tak, aby používal sadu Speech SDK. Otevřete okno **struktury projektu** výběrem **soubor**  >  **struktura projektu** z panelu nabídek Android Studio. V okně **struktura projektu** proveďte následující změny:

1. V seznamu na levé straně okna vyberte **Project** (Projekt). Upravte **výchozí nastavení úložiště knihovny** tak, že připojíte čárku a adresu URL úložiště Maven v jednoduchých uvozovkách: https: \/ /csspeechstorage.blob.Core.Windows.NET/Maven/.

   ![Snímek obrazovky okna se strukturou projektu](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. Na stejné obrazovce na levé straně vyberte **aplikace**. Pak vyberte kartu **Dependencies** (Závislosti) v horní části okna. Vyberte zelený symbol plus ( **+** ) a v rozevírací nabídce vyberte **závislost knihovny** .

   ![Snímek obrazovky se závislostí knihovny](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. V zobrazeném okně zadejte název a verzi sady Speech SDK pro Android, *com. Microsoft. cognitiveservices Account. Speech: Client-SDK: 1.13.0*. Pak vyberte **OK**.
   Sada Speech SDK by teď měla být přidána do seznamu závislostí, jak je znázorněno níže:

   ![Snímek obrazovky sady Speech SDK v seznamu závislostí](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Vyberte kartu **vlastnosti** . V případě kompatibility **zdrojového kódu** i **cíle**vyberte **1,9**.

   ![Snímek obrazovky s kompatibilitou zdrojů a cíli](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. Výběrem **OK** zavřete okno **struktury projektu** a změny se projeví v projektu.
