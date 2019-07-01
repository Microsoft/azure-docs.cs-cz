---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 5/23/2019
ms.author: travisw
ms.openlocfilehash: a8118d80e85d562fa4137ed1f1844e6bf9f1793e
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485034"
---
1. Spusťte Android Studio a v uvítacím okně zvolte **Start a new Android Studio projekt** (Začít nový projekt Android Studia).

    ![Snímek obrazovky s uvítacím oknem Android Studia](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. **Zvolte projekt** průvodce se zobrazí, vyberte **telefony a tablety** a **prázdná aktivita** ve výběru aktivit. Vyberte **Další**.

   ![Snímek obrazovky zvolte projektu průvodce](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. V **nakonfigurujete svůj projekt** obrazovky, zadejte **rychlý Start** jako **název**, **samples.speech.cognitiveservices.microsoft.com** jako **Název balíčku**a vyberte adresář projektu. Pro **úroveň rozhraní API minimální** vyberte **rozhraní API 23: Android 6.0 (Marshmallow)** , ponechte všechna ostatní zaškrtávací políčka není zaškrtnuto a vyberte **Dokončit**.

   ![Snímek obrazovky Konfigurace Průvodce projektu](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

Android Studiu chvíli trvá, než nový projekt pro Android připraví. V dalším kroku nakonfigurujete v tomto projektu sadu Speech SDK a jazyk Java 8.

[!INCLUDE [License Notice](cognitive-services-speech-service-license-notice.md)]

Aktuální verze sady Cognitive Services Speech SDK je `1.6.0`.

Sadou SDK pro řeč pro Android je zabalena jako [AAR (knihovna pro Android)](https://developer.android.com/studio/projects/android-library), který obsahuje potřebné knihovny a požadovaná oprávnění pro Android.
Je hostován v úložiště Maven v protokolu https:\//csspeechstorage.blob.core.windows.net/maven/.

Nastavte projekt tak, aby používal sadu Speech SDK. Výběrem příkazu **File (Soubor)**  > **Project Structure (Struktura projektu)** na řádku nabídek Android Studia otevřete okno se strukturou projektu. V okně se strukturou projektu udělejte následující změny:

1. V seznamu na levé straně okna vyberte **Project** (Projekt). Upravte nastavení **Default Library Repository** (Výchozí úložiště knihoven) připojením čárky a adresy URL úložiště Maven v jednoduchých uvozovkách. 'https:\//csspeechstorage.blob.core.windows.net/maven/'

   ![Snímek obrazovky okna se strukturou projektu](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. Na stejné obrazovce vyberte na levé straně **app** (Aplikace). Pak vyberte kartu **Dependencies** (Závislosti) v horní části okna. Vyberte zelené znaménko plus (+) a v rozevírací nabídce zvolte **Library dependency** (Závislost na knihovně).

   ![Snímek obrazovky okna se strukturou projektu](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. V okně, které se zobrazí, zadejte název a verzi naší sady Speech SDK pro Android – `com.microsoft.cognitiveservices.speech:client-sdk:1.6.0`. Pak vyberte **OK**.
   Sada Speech SDK by teď měla být přidaná do seznamu závislostí, jak je znázorněno níže:

   ![Snímek obrazovky okna se strukturou projektu](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Vyberte kartu **Properties** (Vlastnosti). U polí **Source Compatibility** (Kompatibilita zdroje) a **Target Compatibility** (Kompatibilita cíle) vyberte **1.8**.

   ![](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. Výběrem tlačítka **OK** zavřete okno se strukturou projektu a uplatněte na projekt provedené změny.
