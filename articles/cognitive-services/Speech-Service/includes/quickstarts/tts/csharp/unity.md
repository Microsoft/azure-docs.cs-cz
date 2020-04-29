---
title: 'Rychlý Start: syntetizace řeči, C# (Unity) – služba pro rozpoznávání řeči'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto průvodce můžete vytvořit aplikaci pro převod textu na řeč s Unity a sadou Speech SDK for Unity. Po dokončení můžete řeč od textu v reálném čase až po mluvčí zařízení.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yinhew
ms.openlocfilehash: 0934738c557ac6d26867546783797cf5d2b75056
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81274780"
---
> [!NOTE]
> Unity podporuje Desktop Windows (x86 a x64) nebo Univerzální platforma Windows (x86, x64, ARM/ARM64), Android (x86, ARM32/64) a iOS (simulátor x64, ARM32 a ARM64).

## <a name="prerequisites"></a>Požadavky

Než začnete, nezapomeňte:

> [!div class="checklist"]
> * [Vytvoření prostředku Azure Speech](../../../../get-started.md)
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?tabs=unity&pivots=programming-language-csharp)

## <a name="add-a-ui"></a>Přidání uživatelského rozhraní

Do naší scény přidáváme minimální uživatelské rozhraní, které obsahuje vstupní pole pro zadání textu pro shrnutí, tlačítko pro aktivaci syntézy řeči a textové pole pro zobrazení výsledku.

* V [okně hierarchie](https://docs.unity3d.com/Manual/Hierarchy.html) (ve výchozím nastavení vlevo) se zobrazí ukázková scéna, kterou Unity vytvořil s novým projektem.
* V horní části okna **hierarchie** vyberte tlačítko **vytvořit** a vyberte**vstupní pole** **uživatelského rozhraní** > .
* Tato možnost vytvoří tři herní objekty, které lze zobrazit v okně **hierarchie** : objekt **vstupního pole** vnořený v rámci objektu **plátna** a objekt **EventSystem** .
* [Přejděte do zobrazení scény](https://docs.unity3d.com/Manual/SceneViewNavigation.html) , abyste měli dobrý pohled na plátno a vstupní pole v [zobrazení scény](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Vyberte objekt **vstupního pole** v okně **hierarchie** a zobrazte jeho nastavení v [okně inspektora](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standardně napravo).
* Nastavte vlastnosti **POS X** a **POS Y** na **0** , aby se vstupní pole centroval uprostřed plátna.
* Znovu vyberte tlačítko **vytvořit** v horní části okna **hierarchie** . Kliknutím na**tlačítko** **uživatelské rozhraní** > vytvoříte tlačítko.
* Vyberte objekt **tlačítka** v okně **hierarchie** a zobrazte jeho nastavení v [okně inspektora](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standardně napravo).
* Nastavte vlastnosti **POS X** a **POS Y** na **0** a **-48**. Nastavte vlastnosti **Width** a **Height** na **160** a **30** , abyste zajistili, že se tlačítko a vstupní pole nepřekrývají.
* Znovu vyberte tlačítko **vytvořit** v horní části okna **hierarchie** . Vyberte**text** **uživatelského rozhraní** > a vytvořte textové pole.
* Vyberte objekt **text** v okně **hierarchie** a zobrazte jeho nastavení v [okně inspektora](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standardně napravo).
* Nastavte vlastnosti **POS X** a **POS Y** na **0** a **80**. Nastavte vlastnosti **Width** a **Height** na **320** a **80** , aby se textové pole a vstupní pole nepřekrývaly.
* Znovu vyberte tlačítko **vytvořit** v horní části okna **hierarchie** . Vyberte zdroj zvukové**zvukové nahrávky** a vytvořte zdroj zvuku. **Audio** > 

Až budete hotovi, uživatelské rozhraní by mělo vypadat podobně jako na tomto snímku obrazovky:

[![Snímek obrazovky uživatelského rozhraní pro rychlé zprovoznění v editoru Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>Přidání vzorového kódu

1. V [okně projektu](https://docs.unity3d.com/Manual/ProjectView.html) (ve výchozím nastavení v levém dolním rohu) vyberte tlačítko **vytvořit** a pak vyberte **skript jazyka C#**. Pojmenujte `HelloWorld`skript.

1. Upravte skript tak, že na něj dvakrát kliknete.

   > [!NOTE]
   > Můžete nakonfigurovat, který Editor kódu se spustí, výběrem možnosti **Upravit** > **Předvolby**. Další informace najdete v příručce pro [uživatele Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Nahraďte veškerý kód následujícím kódem:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/text-to-speech/Assets/Scripts/HelloWorld.cs#code)]

1. Vyhledejte řetězec `YourSubscriptionKey` a nahraďte ho klíčovým předplatným služby Speech.

1. Vyhledejte řetězec `YourServiceRegion` a nahraďte ho [oblastí](~/articles/cognitive-services/Speech-Service/regions.md) přidruženou k vašemu předplatnému. Například oblast je `westus` , pokud používáte bezplatnou zkušební verzi.

1. Uložte změny do skriptu.

1. Zpátky v editoru Unity přidejte skript jako komponentu do jednoho z herních objektů.

   * Vyberte objekt **plátno** v okně **hierarchie** a otevřete nastavení v [okně Inspektor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standardně napravo).
   * V okně **inspektora** vyberte tlačítko **Přidat komponentu** . Pak vyhledejte skript, `HelloWorld` který jsme předtím vytvořili, a přidejte ho.
   * Komponenta HelloWorld má čtyři neinicializované vlastnosti, **Výstupní text**, **vstupní pole**, **tlačítko Speak** a **zdroj zvuku**, které odpovídají veřejným vlastnostem `HelloWorld` třídy.
     Chcete-li je napravit, vyberte výběr objektu (ikona malé kružnice napravo od vlastnosti). Vyberte objekty textu a tlačítka, které jste vytvořili dříve.

     > [!NOTE]
     > Vstupní pole a tlačítko mají také vnořený textový objekt. Ujistěte se, že je neúmyslně nevybírejte jako textový výstup. Nebo můžete přejmenovat textové objekty, které používají pole **název** v okně **inspektora** , aby nedocházelo k záměně.

## <a name="run-the-application-in-the-unity-editor"></a>Spuštění aplikace v editoru Unity

* Vyberte tlačítko **Přehrát** na panelu nástrojů editoru Unity, který je pod řádkem nabídek.
* Po spuštění aplikace zadejte do vstupního pole nějaký text a vyberte tlačítko. Váš text se přenáší do služby pro rozpoznávání řeči a syntetizuje se na řeč, který hraje na svém mluvčím.

  [![Snímek obrazovky běžícího rychlého startu v okně hry Unity](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-inline.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* V [okně konzoly](https://docs.unity3d.com/Manual/Console.html) vyhledejte zprávy ladění.

## <a name="additional-options-to-run-this-application"></a>Další možnosti spuštění této aplikace

Tuto aplikaci je taky možné nasadit do Androidu jako samostatnou aplikaci pro Windows nebo aplikaci UWP.
Podívejte se na [ukázkové úložiště](https://aka.ms/csspeech/samples) ve složce rychlého startu/CSharp-Unity, která popisuje konfiguraci pro tyto další cíle.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Viz také

- [Vytvoření vlastního hlasu](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Zaznamenat vlastní ukázky hlasu](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
