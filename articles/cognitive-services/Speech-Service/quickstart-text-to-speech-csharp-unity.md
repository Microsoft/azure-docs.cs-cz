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
ms.openlocfilehash: b4d329d9d3c2a259fb90b0278c54ba8590590995
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675490"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-unity"></a>Rychlý Start: syntetizace řeči pomocí sady Speech SDK for Unity

K dispozici jsou také rychlé starty pro [rozpoznávání řeči](quickstart-csharp-unity.md).

Pomocí tohoto průvodce můžete vytvořit aplikaci pro převod textu na řeč pomocí [Unity](https://unity3d.com/) a sady Azure Cognitive Services Speech SDK for Unity.
Až budete hotovi, můžete řeč od textu v reálném čase až po mluvčí zařízení.
Pokud nejste obeznámeni s Unity, prostudujte si [příručku pro uživatele Unity](https://docs.unity3d.com/Manual/UnityManual.html) předtím, než začnete s vývojem vaší aplikace.

> [!NOTE]
> Unity podporuje Desktop Windows (x86 a x64) nebo Univerzální platforma Windows (x86, x64, ARM/ARM64), Android (x86, ARM32/64) a iOS (simulátor x64, ARM32 a ARM64).

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto projektu budete potřebovat:

* [Unity 2018,3 nebo novější](https://store.unity.com/) s [Unity 2019,1 Přidání podpory pro UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Také je přijatelné verze 15,9 nebo vyšší ze sady Visual Studio 2017.
* Pro podporu Windows ARM64 nainstalujte [volitelné nástroje Build pro ARM64 a sadu Windows 10 SDK pro ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).
* Klíč předplatného pro službu rozpoznávání řeči [Získejte je zdarma](get-started.md).

## <a name="create-a-unity-project"></a>Vytvoření projektu Unity

* Spusťte Unity a na kartě **projekty** vyberte **Nový**.
* Zadejte **název projektu** jako **CSharp-Unity** a **template** jako **3D**a vyberte umístění.
  Pak vyberte **vytvořit projekt**.
* Po určitém časovém intervalu by se měl okno editoru Unity automaticky zobrazit.

## <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

* Sada Speech SDK for Unity (beta) je zabalená jako balíček assetů Unity (. unitypackage). Stáhněte si ho z [tohoto webu](https://aka.ms/csspeech/unitypackage).
* Importujte sadu Speech SDK tak, že vyberete **assets**  > **importovat balíček**  > **vlastní balíček**. Další informace najdete v [dokumentaci k Unity](https://docs.unity3d.com/Manual/AssetPackages.html).
* V nástroji pro výběr souborů vyberte soubor sady Speech SDK. unitypackage, který jste stáhli.
* Zajistěte, aby byly vybrány všechny soubory, a vyberte **importovat**.

  ![Snímek obrazovky s editorem Unity při importu balíčku assetu Unity sady Speech SDK](media/sdk/qs-csharp-unity-01-import.png)

## <a name="add-a-ui"></a>Přidání uživatelského rozhraní

Do naší scény přidáváme minimální uživatelské rozhraní, které obsahuje vstupní pole pro zadání textu pro shrnutí, tlačítko pro aktivaci syntézy řeči a textové pole pro zobrazení výsledku.

* V [okně hierarchie](https://docs.unity3d.com/Manual/Hierarchy.html) (ve výchozím nastavení vlevo) se zobrazí ukázková scéna, kterou Unity vytvořil s novým projektem.
* V horní části okna **hierarchie** vyberte tlačítko **vytvořit** a vyberte **uživatelské rozhraní**  > **vstupní pole**.
* Tato možnost vytvoří tři herní objekty, které lze zobrazit v okně **hierarchie** : objekt **vstupního pole** vnořený v rámci objektu **plátna** a objekt **EventSystem** .
* [Přejděte do zobrazení scény](https://docs.unity3d.com/Manual/SceneViewNavigation.html) , abyste měli dobrý pohled na plátno a vstupní pole v [zobrazení scény](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Vyberte objekt **vstupního pole** v okně **hierarchie** a zobrazte jeho nastavení v [okně inspektora](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standardně napravo).
* Nastavte vlastnosti **POS X** a **POS Y** na **0** , aby se vstupní pole centroval uprostřed plátna.
* Znovu vyberte tlačítko **vytvořit** v horní části okna **hierarchie** . Kliknutím na**tlačítko**  >  **uživatelského rozhraní** vytvoříte tlačítko.
* Vyberte objekt **tlačítka** v okně **hierarchie** a zobrazte jeho nastavení v [okně inspektora](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standardně napravo).
* Nastavte vlastnosti **POS X** a **POS Y** na **0** a **-48**. Nastavte vlastnosti **Width** a **Height** na **160** a **30** , abyste zajistili, že se tlačítko a vstupní pole nepřekrývají.
* Znovu vyberte tlačítko **vytvořit** v horní části okna **hierarchie** . Vyberte **uživatelské rozhraní**  > **text** pro vytvoření textového pole.
* Vyberte objekt **text** v okně **hierarchie** a zobrazte jeho nastavení v [okně inspektora](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standardně napravo).
* Nastavte vlastnosti **POS X** a **POS Y** na **0** a **80**. Nastavte vlastnosti **Width** a **Height** na **320** a **80** , aby se textové pole a vstupní pole nepřekrývaly.
* Znovu vyberte tlačítko **vytvořit** v horní části okna **hierarchie** . Vyberte **zvuk**  > **zdroj** zvuku a vytvořte zdroj zvuku.

Až budete hotovi, uživatelské rozhraní by mělo vypadat podobně jako na tomto snímku obrazovky:

[![Screenshot uživatelského rozhraní pro rychlé zprovoznění v editoru Unity](media/sdk/qs-tts-csharp-unity-ui-inline.png)](media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>Přidání vzorového kódu

1. V [okně projektu](https://docs.unity3d.com/Manual/ProjectView.html) (ve výchozím nastavení v levém dolním rohu) vyberte tlačítko **vytvořit** a pak vyberte  **C# skript**. Pojmenujte skript `HelloWorld`.

1. Upravte skript tak, že na něj dvakrát kliknete.

   > [!NOTE]
   > Můžete nakonfigurovat, který Editor kódu se spustí, výběrem možnosti **upravit**  > **Předvolby**. Další informace najdete v příručce pro [uživatele Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Nahraďte veškerý kód následujícím kódem:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Vyhledejte a nahraďte řetězec `YourSubscriptionKey` klíčem předplatného služby Speech Services.

1. Vyhledejte řetězec `YourServiceRegion` a nahraďte ho [oblastí](regions.md) přidruženou k vašemu předplatnému. Oblast je například `westus`, pokud používáte bezplatnou zkušební verzi.

1. Uložte změny do skriptu.

1. Zpátky v editoru Unity přidejte skript jako komponentu do jednoho z herních objektů.

   * Vyberte objekt **plátno** v okně **hierarchie** a otevřete nastavení v [okně Inspektor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standardně napravo).
   * V okně **inspektora** vyberte tlačítko **Přidat komponentu** . Pak vyhledejte `HelloWorld` skript, který jsme předtím vytvořili, a přidejte ho.
   * Komponenta HelloWorld má čtyři neinicializované vlastnosti, **Výstupní text**, **vstupní pole**, **tlačítko Speak** a **zdroj zvuku**, které odpovídají veřejným vlastnostem třídy `HelloWorld`.
     Chcete-li je napravit, vyberte výběr objektu (ikona malé kružnice napravo od vlastnosti). Vyberte objekty textu a tlačítka, které jste vytvořili dříve.

     > [!NOTE]
     > Vstupní pole a tlačítko mají také vnořený textový objekt. Ujistěte se, že je neúmyslně nevybírejte jako textový výstup. Nebo můžete přejmenovat textové objekty, které používají pole **název** v okně **inspektora** , aby nedocházelo k záměně.

## <a name="run-the-application-in-the-unity-editor"></a>Spuštění aplikace v editoru Unity

* Vyberte tlačítko **Přehrát** na panelu nástrojů editoru Unity, který je pod řádkem nabídek.
* Po spuštění aplikace zadejte do vstupního pole nějaký text a vyberte tlačítko. Váš text se přenáší do služby Speech a syntetizuje se přes řeč, který hraje na svém mluvčím.

  [![Screenshot běžícího rychlého startu v okně hry Unity](media/sdk/qs-tts-csharp-unity-output-inline.png)](media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* V [okně konzoly](https://docs.unity3d.com/Manual/Console.html) vyhledejte zprávy ladění.
* Až budete s řečí hotovi, vyberte na panelu nástrojů editoru Unity tlačítko **Přehrát** a zastavte aplikaci.

## <a name="additional-options-to-run-this-application"></a>Další možnosti spuštění této aplikace

Tuto aplikaci je taky možné nasadit do Androidu jako samostatnou aplikaci pro Windows nebo aplikaci UWP.
Podívejte se na [ukázkové úložiště](https://aka.ms/csspeech/samples) ve složce rychlého startu/CSharp-Unity, která popisuje konfiguraci pro tyto další cíle.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte C# ukázky na GitHubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Další informace najdete v tématech

- [Přizpůsobení hlasových písem](how-to-customize-voice-font.md)
- [Záznam ukázek hlasu](record-custom-voice-samples.md)
