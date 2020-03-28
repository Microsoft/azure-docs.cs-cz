---
title: 'Úvodní příručka: Rozpoznávání řeči z mikrofonu, C# (Unity)- Služba řeči'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: erhopf
ms.openlocfilehash: be8497cb07bd3c761c59bfc55e773b5dcc937c50
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925621"
---
> [!NOTE]
> Sada Speech SDK for Unity podporuje plochu Systému Windows (x86 a x64) nebo univerzální platformu Windows (x86, x64, ARM/ARM64), Android (x86, ARM32/64) a iOS (simulátor x64, ARM32 a ARM64)

## <a name="prerequisites"></a>Požadavky

Než začnete:

> [!div class="checklist"]
> * [Vytvoření řečového prostředku Azure](../../../../get-started.md)
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?tabs=unity)
> * Ujistěte se, že máte přístup k mikrofonu pro snímání zvuku

Pokud jste to už udělali, skvělé. Pojďme dál.

## <a name="create-a-unity-project"></a>Vytvoření projektu Unity

1. Otevřít jednotu. Pokud používáte Unity poprvé, zobrazí se okno **Unity Hub.** *<version number>* (Můžete také otevřít Unity Hub přímo se dostanete do tohoto okna.)

   [![Okno Hub Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png#lightbox)
1. Vyberte **nový**. Zobrazí se okno **Vytvořit nový projekt s unity.** *<version number>*

   [![Vytvoření nového projektu v unity hubu](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png#lightbox)
1. Do **pole Název projektu**zadejte **csharp-unity**.
1. Pokud **v části Šablony**ještě není vybraná aplikace **3D** , vyberte ji.
1. V **části Umístění**vyberte nebo vytvořte složku, do které chcete projekt uložit.
1. Vyberte **Vytvořit**.

Po chvíli se zobrazí okno Editor unity.



## <a name="add-ui"></a>Přidat ui

Nyní přidáme minimální ui do naší scény. Toto ui se skládá z tlačítka pro aktivaci rozpoznávání řeči a textové pole pro zobrazení výsledku. V [okně **Hierarchie** ](https://docs.unity3d.com/Manual/Hierarchy.html)se zobrazí ukázková scéna, kterou unity vytvořila s novým projektem.

1. V horní části okna **Hierarchie** vyberte **Vytvořit** > **tlačítko****ui** > .

   Tato akce vytvoří tři herní objekty, které můžete vidět v okně **Hierarchie:** Objekt **Button,** objekt **Canvas** obsahující tlačítko a objekt **EventSystem.**

   [![Prostředí Editoru unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png#lightbox)

1. [Procházejte zobrazení **scény,** ](https://docs.unity3d.com/Manual/SceneViewNavigation.html) abyste měli dobrý přehled o plátně a tlačítku v zobrazení [ **Scéna** ](https://docs.unity3d.com/Manual/UsingTheSceneView.html).

1. V [okně **Inspektor** ](https://docs.unity3d.com/Manual/UsingTheInspector.html) (ve výchozím nastavení vpravo) nastavte vlastnosti **Pos X** a **Pos Y** na **0**, aby bylo tlačítko vystředěno uprostřed plátna.

1. V okně **Hierarchie** vyberte **Vytvořit** > text **Text** **ui,** > **Text** chcete-li vytvořit textový objekt.

1. V okně **Inspektor** nastavte vlastnosti **Pos X** a **Pos Y** na **0** a **120**a nastavte vlastnosti **Šířka** a **Výška** na **240** a **120**. Tyto hodnoty zajišťují, že se textové pole a tlačítko nepřekrývají.

Po dokončení by mělo zobrazení **Scéna** vypadat podobně jako tento snímek obrazovky:

[![Zobrazení scény v Editoru jednoty](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png#lightbox)

## <a name="add-the-sample-code"></a>Přidání vzorového kódu

Chcete-li přidat ukázkový kód skriptu pro projekt Unity, postupujte takto:

1. V [okně Projekt](https://docs.unity3d.com/Manual/ProjectView.html)vyberte **Vytvořit** > **skript C#,** abyste přidali nový skript C#.

   [![Okno projektu v Editoru jednoty](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png#lightbox)
1. Pojmenujte `HelloWorld`skript .

1. `HelloWorld` Poklepáním upravte nově vytvořený skript.

   > [!NOTE]
   > Chcete-li nakonfigurovat editor kódu tak, aby jej používala unity pro**úpravy,** vyberte **Upravit** > předvolby a pak přejděte do předvoleb Externí **nástroje.** Další informace naleznete v [uživatelské příručce Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Nahraďte existující skript následujícím kódem:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/from-microphone/Assets/Scripts/HelloWorld.cs#code)]

1. Najděte a `YourSubscriptionKey` nahraďte řetězec klíčem předplatného služby Speech.

1. Najděte a `YourServiceRegion` nahraďte řetězec **identifikátorem oblasti** z [oblasti](https://aka.ms/speech/sdkregion) přidružené k vašemu předplatnému. Pokud například používáte bezplatnou zkušební verzi, oblast je `westus`.

1. Uložte změny do skriptu.

Nyní se vraťte do Editoru jednoty a přidejte skript jako součást jednoho z vašich herních objektů:

1. V okně **Hierarchie** vyberte objekt **Plátno.**

1. V okně **Inspektor** vyberte tlačítko **Přidat komponentu.**

   [![Okno Inspektor v Editoru jednoty](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png#lightbox)

1. V rozevíracím seznamu vyhledejte `HelloWorld` skript, který jsme vytvořili výše, a přidejte jej. V okně **Inspektor** se zobrazí sekce **Hello World (Script),** která obsahuje dvě neinicializované vlastnosti, **Výstupní text** a Tlačítko **Start Reco**. Tyto vlastnosti komponenty Unity `HelloWorld` odpovídají veřejným vlastnostem třídy.

1. Vyberte výběr objektů vlastnosti **Start Reco Button** (ikona malého kruhu napravo od vlastnosti) a zvolte Objekt **Button,** který jste vytvořili dříve.

1. Vyberte výběr objektů vlastnosti **Output Text** a zvolte objekt **Text,** který jste vytvořili dříve.

   > [!NOTE]
   > Tlačítko má také vnořený textový objekt. Ujistěte se, že jste jej omylem nevybrali pro textový výstup (nebo přejmenujte jeden z textových objektů pomocí pole **Název** v okně **Inspektor,** abyste předešli nejasnostem).

## <a name="run-the-application-in-the-unity-editor"></a>Spuštění aplikace v Editoru jednoty

Nyní jste připraveni ke spuštění aplikace v editoru Unity.

1. Na panelu nástrojů Editor unity (pod panelem nabídek) vyberte tlačítko **Přehrát** (trojúhelník směřující doprava).

1. Přejděte do [ **zobrazení hry** ](https://docs.unity3d.com/Manual/GameView.html)a počkejte, až se objekt **Text** zobrazí **Tlačítko Klikněte pro rozpoznání řeči**. (Zobrazí **nový text,** když se aplikace nespustila nebo není připravena odpovědět.)

1. Vyberte tlačítko a mluvte anglickou frází nebo větou do mikrofonu počítače. Vaše řeč je přenesena do služby Řeč a přepsána na text, který se zobrazí v zobrazení **Hry.**

   [![Zobrazení hry v Editoru unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png#lightbox)

1. Zkontrolujte, zda v okně [ **konzoly** ](https://docs.unity3d.com/Manual/Console.html) není možné ladit zprávy. Pokud se okno **Konzola** nezobrazuje, přejděte na řádek nabídek a vyberte okno**General** > **Console,** aby se zobrazilo. **Window** > 

1. Po dokončení rozpoznávání řeči vyberte tlačítko **Přehrát** na panelu nástrojů Editor unity, abyste aplikaci zastavili.

## <a name="additional-options-to-run-this-application"></a>Další možnosti spuštění této aplikace

Do této aplikace lze také nasadit jako aplikaci pro Android, samostatnou aplikaci pro Windows nebo aplikaci UPW.
Další informace naleznete v našem [ukázkovém úložišti](https://aka.ms/csspeech/samples). Složka `quickstart/csharp-unity` popisuje konfiguraci pro tyto další cíle.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]
