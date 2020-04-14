---
title: 'Úvodní příručka: Syntetizovat řeč, C# (Jednota) - Služba řeči'
titleSuffix: Azure Cognitive Services
description: Tento průvodce slouží k vytvoření aplikace pro převod textu na řeč s unity a sady SDK řeči pro jednotu. Po dokončení můžete syntetizovat řeč z textu v reálném čase do reproduktoru zařízení.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yinhew
ms.openlocfilehash: 0934738c557ac6d26867546783797cf5d2b75056
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274780"
---
> [!NOTE]
> Unity podporuje Windows Desktop (x86 a x64) nebo univerzální platformu Windows (x86, x64, ARM/ARM64), Android (x86, ARM32/64) a iOS (simulátor x64, ARM32 a ARM64).

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

> [!div class="checklist"]
> * [Vytvoření řečového prostředku Azure](../../../../get-started.md)
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?tabs=unity&pivots=programming-language-csharp)

## <a name="add-a-ui"></a>Přidání ui

Do naší scény přidáme minimální uzpole, které se skládá ze vstupního pole pro zadání textu pro syntézu, tlačítka pro spuštění syntézy řeči a textového pole pro zobrazení výsledku.

* V [okně Hierarchie](https://docs.unity3d.com/Manual/Hierarchy.html) (ve výchozím nastavení vlevo) se zobrazí ukázková scéna, kterou unity vytvořila s novým projektem.
* Vyberte tlačítko **Vytvořit** v horní části okna **Hierarchie** a vyberte**vstupní pole** **ui** > .
* Tato možnost vytvoří tři herní objekty, které můžete vidět v okně **Hierarchie:** objekt **vstupního pole** vnořený do objektu **Canvas** a objekt **EventSystem.**
* [Procházejte zobrazení scény](https://docs.unity3d.com/Manual/SceneViewNavigation.html) tak, abyste měli dobrý přehled o plátně a vstupním poli v [zobrazení Scéna](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Vyberte objekt **Vstupní pole** v okně **Hierarchie,** chcete-li zobrazit jeho nastavení v [okně Inspektor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (ve výchozím nastavení vpravo).
* Nastavte vlastnosti **Pos X** a **Pos Y** na **0** tak, aby vstupní pole bylo vystředěno uprostřed plátna.
* Vhorní části okna **Hierarchie** znovu vyberte tlačítko **Vytvořit.** Chcete-li vytvořit **tlačítko,** > vyberte**tlačítko pro tlačítko.**
* Vyberte objekt **Button** v okně **Hierarchie,** chcete-li zobrazit jeho nastavení v [okně Inspektor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (ve výchozím nastavení vpravo).
* Nastavte vlastnosti **Pos X** a **Pos Y** na **0** a **-48**. Nastavte vlastnosti **Šířka** a **Výška** na **160** a **30,** abyste zajistili, že se tlačítko a vstupní pole nepřekrývají.
* Vhorní části okna **Hierarchie** znovu vyberte tlačítko **Vytvořit.** Vyberte**Text** **ui,** > chcete-li vytvořit textové pole.
* Vyberte **textový** objekt v okně **Hierarchie,** chcete-li zobrazit jeho nastavení v [okně Inspektor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (ve výchozím nastavení vpravo).
* Nastavte vlastnosti **Pos X** a **Pos Y** na **0** a **80**. Nastavte vlastnosti **Šířka** a **Výška** na **320** a **80,** aby se textové pole a vstupní pole nepřekrývaly.
* Vhorní části okna **Hierarchie** znovu vyberte tlačítko **Vytvořit.** Vyberte **Zdroj** > **zvuku,** chcete-li vytvořit zdroj zvuku.

Po dokončení by mělo ui vypadat podobně jako tento snímek obrazovky:

[![Snímek obrazovky s uživatelským rozhraním rychlého startu v Editoru unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>Přidání vzorového kódu

1. V [okně Projekt](https://docs.unity3d.com/Manual/ProjectView.html) (ve výchozím nastavení v levém dolním rohu) vyberte tlačítko **Vytvořit** a pak vyberte **skript C#**. Pojmenujte `HelloWorld`skript .

1. Skript můžete upravit poklepáním.

   > [!NOTE]
   > Který editor kódu se spustí, můžete nakonfigurovat výběrem **možnosti Upravit** > **předvolby**. Další informace naleznete v [uživatelské příručce Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Nahraďte veškerý kód následujícím:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/text-to-speech/Assets/Scripts/HelloWorld.cs#code)]

1. Vyhledejte a `YourSubscriptionKey` nahraďte řetězec klíčem předplatného služby Speech.

1. Vyhledejte řetězec `YourServiceRegion` a nahraďte ho [oblastí](~/articles/cognitive-services/Speech-Service/regions.md) přidruženou k vašemu předplatnému. Oblast je `westus` například, pokud používáte bezplatnou zkušební verzi.

1. Uložte změny do skriptu.

1. Zpět v Unity Editor, přidejte skript jako součást jednoho z vašich herních objektů.

   * Vyberte objekt **Plátno** v okně **Hierarchie,** chcete-li otevřít nastavení v [okně Inspektor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (ve výchozím nastavení vpravo).
   * V okně **Inspektor** vyberte tlačítko **Přidat komponentu.** Pak vyhledejte `HelloWorld` skript, který jsme dříve vytvořili, a přidejte jej.
   * Komponenta HelloWorld má čtyři neinicializované vlastnosti, **výstupní text**, **vstupní pole**, tlačítko **Speak** a **zdroj zvuku**, které odpovídají veřejným vlastnostem `HelloWorld` třídy.
     Chcete-li je napojit, vyberte výběr objektů (ikona malého kruhu napravo od vlastnosti). Vyberte text a objekty tlačítek, které jste vytvořili dříve.

     > [!NOTE]
     > Vstupní pole a tlačítko mají také vnořený textový objekt. Ujistěte se, že jste ji omylem nevybrali pro textový výstup. Nebo můžete přejmenovat textové objekty, které používají pole **Název** v okně **Inspektor,** abyste se vyhnuli nejasnostem.

## <a name="run-the-application-in-the-unity-editor"></a>Spuštění aplikace v Editoru jednoty

* Vyberte tlačítko **Přehrát** na panelu nástrojů Editor unity, který je pod panelem nabídek.
* Po spuštění aplikace zadejte nějaký text do vstupního pole a vyberte tlačítko. Text je přenášen do služby Řeč a syntetizován do řeči, která se přehrává na reproduktoru.

  [![Snímek obrazovky s rychlým startem běhu v okně Unity Game](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-inline.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* Zkontrolujte, zda [v okně konzoly](https://docs.unity3d.com/Manual/Console.html) není možné ladit zprávy.

## <a name="additional-options-to-run-this-application"></a>Další možnosti spuštění této aplikace

Tato aplikace může být také nasazena do systému Android jako samostatná aplikace pro Windows nebo aplikace UPW.
Podívejte se na [ukázkové úložiště](https://aka.ms/csspeech/samples) ve složce quickstart/csharp-unity, která popisuje konfiguraci pro tyto další cíle.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Viz také

- [Vytvoření vlastního hlasu](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Záznam vlastních hlasových ukázek](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
