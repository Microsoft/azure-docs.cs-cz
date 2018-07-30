---
title: 'Rychlý start: Rozpoznávat řeč v jazyce Java v Androidu pomocí Cognitive Services SDK řeči'
titleSuffix: Microsoft Cognitive Services
description: Zjistěte, jak rozpoznávat řeč v jazyce Java v Androidu pomocí Cognitive Services SDK řeči
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: fmegen
ms.openlocfilehash: ad1bf15f198529487dbed92d8f8639704b0c5fc7
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325121"
---
# <a name="quickstart-recognize-speech-in-java-on-android-using-the-speech-sdk"></a>Rychlý start: Rozpoznávat řeč v jazyce Java v Androidu pomocí sadou SDK pro řeč

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku se dozvíte, jak vytvořit aplikaci v jazyce Java pro Android pomocí Cognitive Services SDK řeči přepisy převod řeči na text.
Aplikace je založena na Microsoft Cognitive Services řeči Maven balíček SDK, verze si verzi 0.5.0 a Android Studio 3.1.

> [!NOTE]
> Sadou SDK pro řeč zařízení a Roobo zařízení, najdete [sadou SDK pro řeč zařízení](speech-devices-sdk.md) stránky.

## <a name="prerequisites"></a>Požadavky

* Klíč předplatného pro službu rozpoznávání řeči. Zobrazit [službu řeči si můžete vyzkoušet zdarma](get-started.md).
* Počítač (Windows, Linux, Mac) umožňující spustit Android Studio.
* Verze 3.1 [Android Studio](https://developer.android.com/studio/).
* Zařízení s Androidem založené na ARM (API 23: Android 6.0 Marshmallow nebo vyšší) [aktivované pro vývoj](https://developer.android.com/studio/debug/dev-options) s pracovní mikrofon.

## <a name="create-an-android-studio-project"></a>Vytvoření projektu Android Studio

Spustit Android Studio a vyberte **spusťte nový projekt Android Studio**.

![](media/sdk/qs-java-android-01-start-new-android-studio-project.png)

V **vytvořit nový projekt** průvodce, který obsahuje až, vyberte následující možnosti:

1. V **vytvořte projekt pro Android** obrazovky, zadejte **rychlý Start** jako **název_aplikace**, **samples.speech.cognitiveservices.microsoft.com** jako **doménu společnosti**a zvolte umístění projektu. Nechte nezaškrtnuté zaškrtávací políčka a klikněte na tlačítko **Další**.

   ![](media/sdk/qs-java-android-02-create-android-project.png)

1. V **cílové zařízení s Androidem** obrazovky, zkontrolujte **telefony a tablety** vybrat pouze možnost **rozhraní API 23: Android 6.0 (Marshmallow)** z rozevíracího seznamu v části a klikněte na **Další**.

   ![](media/sdk/qs-java-android-03-target-android-devices.png)

1. V **přidat aktivitu do Mobile** obrazovky, vyberte **prázdná aktivita** a klikněte na tlačítko **Další**.

   ![](media/sdk/qs-java-android-04-add-an-activity-to-mobile.png)

1. V **konfigurace aktivity** obrazovka, použijte **MainActivity** jako název aktivity a **aktivity\_hlavní** jako název rozložení. Zkontrolujte obou políček a klikněte na tlačítko **Dokončit**.

   ![](media/sdk/qs-java-android-05-configure-activity.png)

Po spuštění nějakou dobu, by měl vyvolají nově vytvořený projekt Android Studio.

## <a name="configure-your-project-for-the-speech-sdk"></a>Konfigurace projektu pro sadou SDK pro řeč

[!include[License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Aktuální verze sady SDK pro řeč Cognitive Services je `0.5.0`.

Sadou SDK pro řeč pro Android je zabalena jako [AAR (knihovna pro Android)](https://developer.android.com/studio/projects/android-library), který obsahuje potřebné knihovny, stejně jako požadovaná Android oprávnění k jeho používání.
Je hostován v úložiště Maven v https://csspeechstorage.blob.core.windows.net/maven/.

Popisujeme níže nastavení projektu pro použití sadou SDK pro řeč.

Otevřete okno strukturu projektu v rámci **souboru** \> **strukturu projektu**.
V okně, které se zobrazí zkontrolujte následující změny (klikněte na tlačítko **OK** až po dokončení všech kroků):

1. Vyberte **projektu**a upravit **výchozí knihovny úložiště** nastavení přidáním čárku a naší adresy URL úložiště Maven uzavřena do jednoduchých uvozovek `'https://csspeechstorage.blob.core.windows.net/maven/'`:

  ![](media/sdk/qs-java-android-06-add-maven-repository.png)

1. Stále na stejné obrazovce na levé straně vyberte **aplikace** modulu a v horní části **závislosti** kartu. Potom klikněte na zelenou znaménko Plus v pravém horním rohu a vyberte **knihovny závislost**.

  ![](media/sdk/qs-java-android-07-add-module-dependency.png)

1. V okně, které se zobrazí, zadejte název a verzi naší sadou SDK pro řeč pro Android, `com.microsoft.cognitiveservices.speech:client-sdk:0.5.0`, pak klikněte na tlačítko **OK**.
   Sadou SDK pro řeč přidaly do seznamu závislostí, jak je znázorněno níže:

  ![](media/sdk/qs-java-android-08-dependency-added.png)

1. V horní části, vyberte **vlastnosti** kartu. Vyberte **1.8** obě **zdroje kompatibility** a **cílová Kompatibilita**.

  ![](media/sdk/qs-java-android-09-dependency-added.png)

1. Nakonec klikněte na tlačítko **OK** zavřete **strukturu projektu** windows a nainstalovat všechny aktualizace.

## <a name="create-a-minimal-ui"></a>Vytvořte minimálního uživatelského rozhraní

Upravit rozložení pro hlavní aktivitu, `activity_main.xml`.
Ve výchozím nastavení by měl mít záhlaví s názvem vaší aplikace a TextView s textem "Hello World!".

* Klikněte na TextView. Změnit atribut ID v pravém horním rohu `hello`.

* Z palety v levém horním rohu vašeho `activity_main.xml` okno, přetáhněte tlačítko na prázdné místo nad text.

* V atributech tlačítka na pravé straně v hodnotě `onClick` atribut, zadejte `onSpeechButtonClicked`, která bude název naší tlačítko obslužné rutiny.
  Změnit atribut ID v pravém horním rohu `button`.

* Pokud chcete k odvození omezení rozložení pro vás, použijte ikonu magic hůlka v horní části návrháře.

  ![](media/sdk/qs-java-android-10-infer-layout-constraints.png)

Textové a grafické uživatelské rozhraní verze by teď měl vypadat nějak takto:

<table>
<tr>
<td valign="top">
![](media/sdk/qs-java-android-11-gui.png)
</td>
<td valign="top">
[! code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/res/layout/activity_main.xml)]
</td>
</tr>
</table>

## <a name="add-the-sample-code"></a>Přidejte ukázkový kód

1. Upravit `MainActivity.java` zdrojového souboru a jeho kódu nahraďte následujícím (nižší než váš výpis z balíčku):

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * `onCreate` Metoda obsahuje kód, který požaduje oprávnění Internet a mikrofon, stejně jako inicializuje vazby nativní platformy. Konfigurace vazeb pro nativní platformy je jen potřeba jednou, to znamená, je třeba jej provést již v rané fázi během inicializace aplikace.
   
   * Metoda `onSpeechButtonClicked` byl dříve připraveno, protože obslužná rutina kliknutí na tlačítko. Stiskněte tlačítko aktivuje skutečné řeči.

1. Nahraďte řetězec `YourSubscriptionKey` s klíči předplatného.

1. Nahraďte řetězec `YourServiceRegion` s [oblasti](regions.md) přidružených k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

* K sestavení, stiskněte klávesy Ctrl + F9 nebo vyberte **sestavení** \> **vytvořit projekt**.

* Připojte zařízení s Androidem k počítači vývoje. Ujistěte se, že máte [režimu pro vývoj a ladění USB povolené](https://developer.android.com/studio/debug/dev-options).

* Aplikaci spustit, stiskněte klávesy Shift + F10 nebo vyberte **spustit** \> **spuštění "aplikace"**.

* V nasazení použít jako cíl windows, které se zobrazí vyberte zařízení s Androidem.

  ![Spusťte aplikaci do ladění](media/sdk/qs-java-android-12-deploy.png)

* Aplikace by měla spustit ve vašem zařízení.
  Po stisknutí tlačítka, dalších 15 sekund se bude používat a v uživatelském rozhraní (byste také měli vidět odpovědi v okně logcat v nástroji Android Studio):

  ![Uživatelské rozhraní po úspěšné rozpoznávání](media/sdk/qs-java-android-13-gui-on-device.png)

Tento snímek obrazovky dojde k závěru Android tohoto rychlého startu. Úplný projekt vzorový kód můžete stáhnout z úložiště ukázek.

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Hledat v této ukázce `quickstart/java-android` složky.

## <a name="next-steps"></a>Další postup

* [Získat naše ukázky](speech-sdk.md#get-the-samples)
