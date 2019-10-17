---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 5/23/2019
ms.author: travisw
ms.openlocfilehash: e1f9b561efb1fd88fe2dd00f8c175f1ebae848d8
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391589"
---
1. Spusťte Android Studio a v uvítacím okně zvolte **Start a new Android Studio projekt** (Začít nový projekt Android Studia).

    ![Snímek obrazovky s uvítacím oknem Android Studia](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. Zobrazí se průvodce **Vybrat projekt** , v poli Výběr aktivity vyberte možnost **telefon a tablet** a **prázdná aktivita** . Vyberte **Další**.

   ![Snímek obrazovky s průvodcem pro výběr projektu](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. Na obrazovce **Konfigurovat projekt** zadejte jako **název** **balíčku**možnost **rychlý Start** , **Samples.Speech.cognitiveservices.Microsoft.com** a vyberte adresář projektu. Pro **minimální úroveň rozhraní API** vyberte **rozhraní API 23: Android 6,0 (Marshmallow)** , ponechte všechna ostatní políčka nezaškrtnutá a vyberte **Dokončit**.

   ![Snímek obrazovky Průvodce konfigurací projektu](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

Android Studiu chvíli trvá, než nový projekt pro Android připraví. V dalším kroku nakonfigurujete v tomto projektu sadu Speech SDK a jazyk Java 8.

[!INCLUDE [License Notice](cognitive-services-speech-service-license-notice.md)]

Aktuální verze sady Cognitive Services Speech SDK je `1.7.0`.

Sada Speech SDK pro Android je zabalená jako [Knihovna AAR (Android Library)](https://developer.android.com/studio/projects/android-library), která zahrnuje nezbytné knihovny a požadovaná oprávnění pro Android.
Hostuje se v úložišti Maven na https: \//csspeechstorage. blob. Core. Windows. NET/Maven/.

Nastavte projekt tak, aby používal sadu Speech SDK. Výběrem příkazu **File (Soubor)**  > **Project Structure (Struktura projektu)** na řádku nabídek Android Studia otevřete okno se strukturou projektu. V okně se strukturou projektu udělejte následující změny:

1. V seznamu na levé straně okna vyberte **Project** (Projekt). Upravte nastavení **Default Library Repository** (Výchozí úložiště knihoven) připojením čárky a adresy URL úložiště Maven v jednoduchých uvozovkách. https: \//csspeechstorage. blob. Core. Windows. NET/Maven/

   ![Snímek obrazovky okna se strukturou projektu](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. Na stejné obrazovce vyberte na levé straně **app** (Aplikace). Pak vyberte kartu **Dependencies** (Závislosti) v horní části okna. Vyberte zelené znaménko plus (+) a v rozevírací nabídce zvolte **Library dependency** (Závislost na knihovně).

   ![Snímek obrazovky okna se strukturou projektu](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. V okně, které se zobrazí, zadejte název a verzi naší sady Speech SDK pro Android – `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0`. Pak vyberte **OK**.
   Sada Speech SDK by teď měla být přidaná do seznamu závislostí, jak je znázorněno níže:

   ![Snímek obrazovky okna se strukturou projektu](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Vyberte kartu **vlastnosti** . V případě kompatibility **zdrojového kódu** i **cíle**vyberte **1,8**.

   ![](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. Výběrem tlačítka **OK** zavřete okno se strukturou projektu a uplatněte na projekt provedené změny.
