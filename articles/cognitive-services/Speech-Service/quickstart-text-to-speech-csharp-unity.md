---
title: 'Rychlý Start: syntetizace řeči, Unity – Speech Service'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto průvodce můžete vytvořit aplikaci pro převod textu na řeč s Unity a sadou Speech SDK for Unity. Po dokončení můžete řeč od textu v reálném čase až po mluvčí zařízení.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 9/19/2019
ms.author: yinhew
ms.openlocfilehash: be5f07b8ea58d0d62c70e0e9dc8ab187ce4a0f63
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803190"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-unity"></a>Rychlý Start: syntetizace řeči pomocí sady Speech SDK for Unity

K dispozici jsou také rychlé starty pro [rozpoznávání řeči](quickstart-csharp-unity.md).

Pomocí tohoto průvodce můžete vytvořit aplikaci pro převod textu na řeč pomocí [Unity](https://unity3d.com/) a sady Speech SDK for Unity.
Po dokončení můžete řeč od textu v reálném čase až po mluvčí zařízení.
Pokud nejste obeznámeni s Unity, doporučujeme před zahájením vývoje aplikace prozkoumat [příručku pro uživatele Unity](https://docs.unity3d.com/Manual/UnityManual.html) .

> [!NOTE]
> Podporuje Desktop Windows (x86 a x64) nebo Univerzální platforma Windows (x86, x64, ARM/ARM64), Android (x86, ARM32/64) a iOS (simulátor x64, ARM32 a ARM64).

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto projektu budete potřebovat:

* [Unity 2018,3 nebo novější](https://store.unity.com/) s [Unity 2019,1 Přidání podpory pro UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Také je přijatelné verze 15,9 nebo vyšší ze sady Visual Studio 2017.
* Pro podporu Windows ARM64 nainstalujte [volitelné nástroje sestavení pro ARM64 a sadu Windows 10 SDK pro ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/) 
* Klíč předplatného pro službu rozpoznávání řeči [Získejte je zdarma](get-started.md).

## <a name="create-a-unity-project"></a>Vytvoření projektu Unity

* Spusťte Unity a na kartě **projekty** vyberte **Nový**.
* Zadejte **název projektu** jako **CSharp-Unity**, **template** jako **3D** a vyberte umístění.
  Pak vyberte **vytvořit projekt**.
* Po určitém časovém intervalu by se měl okno editoru Unity automaticky zobrazit.

## <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

* Sada Speech SDK for Unity (beta) je zabalená jako balíček assetů Unity (. unitypackage).
  Stáhněte [si ho odsud.](https://aka.ms/csspeech/unitypackage)
* Importujte sadu Speech SDK tak, že vyberete **asset** > **importovat** **vlastní balíček** > .
  Podrobnosti najdete v [dokumentaci k Unity](https://docs.unity3d.com/Manual/AssetPackages.html) .
* V nástroji pro výběr souborů vyberte soubor sady Speech SDK. unitypackage, který jste si stáhli výše.
* Zajistěte, aby byly vybrány všechny soubory, a klikněte na tlačítko **importovat**:

  ![Snímek obrazovky s editorem Unity při importu balíčku assetu Unity sady Speech SDK](media/sdk/qs-csharp-unity-01-import.png)

## <a name="add-ui"></a>Přidat uživatelské rozhraní

Do naší scény přidáváme minimální uživatelské rozhraní, které obsahuje vstupní pole pro zadání textu pro shrnutí, tlačítko pro aktivaci syntézy řeči a textové pole pro zobrazení výsledku.

* V [okně hierarchie](https://docs.unity3d.com/Manual/Hierarchy.html) (ve výchozím nastavení vlevo) se zobrazí ukázková scéna, kterou Unity vytvořil s novým projektem.
* Klikněte na tlačítko **vytvořit** v horní části okna hierarchie a vyberte**vstupní pole** **uživatelské rozhraní** > .
* Tím se vytvoří tři objekty hry, které vidíte v okně hierarchie: objekt **vstupního pole** vnořený v rámci objektu **plátna** a objekt **EventSystem** .
* [Přejděte do zobrazení scény](https://docs.unity3d.com/Manual/SceneViewNavigation.html) , abyste měli dobrý pohled na plátno a vstupní pole v [zobrazení scény](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Kliknutím na objekt **vstupního pole** v okně hierarchie zobrazíte jeho nastavení v [okně inspektora](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standardně napravo).
* Nastavte vlastnosti **POS X** a **POS Y** na **0**, aby se vstupní pole nacentroval na střed plátna.
* Znovu klikněte na tlačítko **vytvořit** v horní části okna hierarchie a pak vyberte**tlačítko** **uživatelské rozhraní** >  a vytvořte tlačítko.
* Kliknutím na objekt **tlačítka** v okně hierarchie zobrazíte jeho nastavení v [okně inspektora](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standardně napravo).
* Nastavte vlastnosti **POS X** a **POS Y** na **0** a **-48**a nastavte vlastnosti **Width** a **Height** na **160** a **30** , aby se tlačítko a vstupní pole nepřekrývaly.
* Znovu klikněte na tlačítko **vytvořit** v horní části okna hierarchie a vyberte **uživatelské rozhraní**@no__t **-2 a vytvořte textové pole** .
* Kliknutím na objekt **text** v okně hierarchie zobrazíte jeho nastavení v [okně inspektora](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standardně napravo).
* Nastavte vlastnosti **POS X** a **POS Y** na **0** a **80**a nastavte vlastnosti **Width** a **Height** na **320** a **80** , aby se textové pole a vstupní pole nepřekrývaly.
* Znovu klikněte na tlačítko **vytvořit** v horní části okna hierarchie a vyberte **zvuk** > **zdroj zvuku** a vytvořte zdroj zvuku.

Až budete hotovi, uživatelské rozhraní by mělo vypadat podobně jako na tomto snímku obrazovky:

[@no__t – 1Screenshot uživatelského rozhraní pro rychlé zprovoznění v editoru Unity](media/sdk/qs-tts-csharp-unity-ui-inline.png)](media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>Přidání vzorového kódu

1. V [okně projektu](https://docs.unity3d.com/Manual/ProjectView.html) (ve výchozím nastavení v levém dolním rohu) klikněte na tlačítko **vytvořit** a pak vyberte  **C# skript**. Pojmenujte skript `HelloWorld`.

1. Upravte skript tak, že na něj dvakrát kliknete.

   > [!NOTE]
   > Můžete nakonfigurovat, který Editor kódu se spustí v části **Upravit** **Předvolby** > , přečtěte si [příručku pro uživatele Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Nahraďte veškerý kód následujícím kódem:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Vyhledejte řetězec a nahraďte ho `YourSubscriptionKey` pomocí klíče předplatného služby Speech Services.

1. Vyhledejte řetězec `YourServiceRegion` a nahraďte ho [oblastí](regions.md) přidruženou k vašemu předplatnému. Pokud například používáte bezplatnou zkušební verzi, oblast je `westus`.

1. Uložte změny do skriptu.

1. Zpátky v editoru Unity se skript musí přidat jako součást jednoho z vašich herních objektů.

   * V okně hierarchie klikněte na objekt **plátno** . Tím se otevře nastavení v [okně inspektora](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standardně napravo).
   * V okně inspektoru klikněte na tlačítko **Přidat komponentu** a pak vyhledejte skript HelloWorld, který jsme vytvořili výše, a přidejte ho.
   * Všimněte si, že komponenta Hello World obsahuje čtyři neinicializované vlastnosti, **Výstupní text**, **vstupní pole**, **tlačítko Speak** a **zdroj zvuku**, které odpovídají veřejným vlastnostem třídy `HelloWorld`.
     Chcete-li je napravit, klikněte na tlačítko pro výběr objektu (ikona malé kružnice napravo od vlastnosti) a vyberte objekty textu a tlačítka, které jste vytvořili dříve.

     > [!NOTE]
     > Vstupní pole a tlačítko mají také vnořený textový objekt. Ujistěte se, že je neúmyslně nevybírejte textový výstup (nebo přejmenujte textové objekty pomocí pole název v okně inspektora, aby nedocházelo k záměně).

## <a name="run-the-application-in-the-unity-editor"></a>Spuštění aplikace v editoru Unity

* Na panelu nástrojů editoru Unity (pod řádkem nabídek) stiskněte tlačítko **Přehrát** .

* Po spuštění aplikace zadejte do vstupního pole nějaký text a klikněte na tlačítko. Váš text se přenáší do služeb řeči a syntetizuje se na řeč, který hraje na reproduktoru.

  [@no__t – 1Screenshot běžícího rychlého startu v okně hry Unity](media/sdk/qs-tts-csharp-unity-output-inline.png)](media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* V [okně konzoly](https://docs.unity3d.com/Manual/Console.html) vyhledejte zprávy ladění.

* Až budete s řečí hotovi, klikněte na tlačítko **Přehrát** na panelu nástrojů editoru Unity a zastavte aplikaci.

## <a name="additional-options-to-run-this-application"></a>Další možnosti spuštění této aplikace

Tuto aplikaci je také možné nasadit do systému Android, jako samostatnou aplikaci pro Windows nebo do aplikace UWP.
Podívejte se na naše [ukázkové úložiště](https://aka.ms/csspeech/samples) ve složce Start/CSharp-Unity, která popisuje konfiguraci pro tyto další cíle.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte C# ukázky na GitHubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Další informace najdete v tématech

- [Přizpůsobení hlasových písem](how-to-customize-voice-font.md)
- [Záznam ukázek hlasu](record-custom-voice-samples.md)
