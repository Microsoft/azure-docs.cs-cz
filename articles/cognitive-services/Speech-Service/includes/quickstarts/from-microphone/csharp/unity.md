---
title: 'Rychlý Start: rozpoznávání řeči z mikrofonu C# , (Unity) – služba pro rozpoznávání řeči'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: erhopf
ms.openlocfilehash: 149ec84732171cd57b3f8a9590d5340107536eac
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/13/2020
ms.locfileid: "75927955"
---
> [!NOTE]
> Sada Speech SDK for Unity podporuje Desktop Windows (x86 a x64) nebo Univerzální platforma Windows (x86, x64, ARM/ARM64), Android (x86, ARM32/64) a iOS (simulátor x64, ARM32 a ARM64).

## <a name="prerequisites"></a>Požadavky

Než začnete:

> [!div class="checklist"]
> * [Vytvoření prostředku Azure Speech](../../../../get-started.md)
> * [Nastavení vývojového prostředí](../../../../quickstarts/setup-platform.md?tabs=unity)
> * [Vytvořit prázdný ukázkový projekt](../../../../quickstarts/create-project.md?tabs=unity)
> * Ujistěte se, že máte přístup k mikrofonu pro záznam zvuku

Pokud jste to již provedli, Skvělé. Pojďme pokračovat.

## <a name="create-a-unity-project"></a>Vytvoření projektu Unity

1. Otevřete Unity. Pokud používáte Unity poprvé, zobrazí se okno **centrum Unity** *<version number>* . (K tomuto oknu se dostanete také tak, že můžete otevřít centrum Unity přímo.)

   [![okno centra Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png#lightbox)
1. Vyberte **Nový**. Zobrazí se okno **vytvořit nový projekt s** *<version number>* Unity.

   [Vytvoření nového projektu v centru Unity ![](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png#lightbox)
1. Do **název projektu**zadejte **CSharp-Unity**.
1. Pokud se v **šablonách**ještě není vybraná možnost **3D** , vyberte ji.
1. V části **umístění**vyberte nebo vytvořte složku, do které chcete projekt uložit.
1. Vyberte **Vytvořit**.

Po uplynutí časového intervalu se zobrazí okno Editor Unity.



## <a name="add-ui"></a>Přidat uživatelské rozhraní

Nyní přidáme k naší scéně minimální uživatelské rozhraní. Toto uživatelské rozhraní se skládá z tlačítka pro aktivaci rozpoznávání řeči a textového pole, které zobrazí výsledek. V okně [ **hierarchie** ](https://docs.unity3d.com/Manual/Hierarchy.html)se zobrazí ukázková scéna, kterou Unity vytvořil s novým projektem.

1. V horní části okna **hierarchie** vyberte **vytvořit** > **uživatelské rozhraní** > **tlačítko**.

   Tato akce vytvoří tři objekty hry, které lze zobrazit v okně **hierarchie** : objekt **tlačítka** , objekt **plátna** obsahující tlačítko a objekt **EventSystem** .

   [![prostředí editoru Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png#lightbox)

1. [Přejděte do zobrazení **scény** ](https://docs.unity3d.com/Manual/SceneViewNavigation.html) , abyste měli dobré zobrazení plátna a tlačítka v [zobrazení **scény** ](https://docs.unity3d.com/Manual/UsingTheSceneView.html).

1. V okně [ **inspektora** ](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standardně napravo) nastavte vlastnosti **POS X** a **POS Y** na **hodnotu 0**, aby bylo tlačítko zarovnané na střed plátna.

1. V okně **hierarchie** vyberte **vytvořit** > **uživatelského rozhraní** > **text** pro vytvoření objektu **text** .

1. V okně **inspektora** nastavte vlastnosti **POS X** a **pos Y** na **0** a **120**a nastavte vlastnosti **Width** a **Height** na **240** a **120**. Tyto hodnoty zajišťují, aby se textové pole a tlačítko nepřekrývaly.

Až skončíte, zobrazení **scény** by mělo vypadat podobně jako na tomto snímku obrazovky:

[![zobrazení scény v editoru Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png#lightbox)

## <a name="add-the-sample-code"></a>Přidání vzorového kódu

Chcete-li přidat ukázkový kód skriptu pro projekt Unity, postupujte takto:

1. V [okně projekt](https://docs.unity3d.com/Manual/ProjectView.html)vyberte **vytvořit** >  **C# skript** pro přidání nového C# skriptu.

   [![okno projektu v editoru Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png#lightbox)
1. Pojmenujte skript `HelloWorld`.

1. Dvojím kliknutím na `HelloWorld` upravíte nově vytvořený skript.

   > [!NOTE]
   > Pokud chcete nakonfigurovat Editor kódu, který má použít Unity pro úpravy, vyberte **upravit** > **Předvolby**a pak přejít na předvolby **externích nástrojů** . Další informace najdete v příručce pro [uživatele Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Existující skript nahraďte následujícím kódem:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/from-microphone/Assets/Scripts/HelloWorld.cs#code)]

1. Vyhledejte a nahraďte řetězec `YourSubscriptionKey` klíčem k předplatnému služby Speech.

1. Vyhledejte řetězec `YourServiceRegion` a nahraďte ho [oblastí](~/articles/cognitive-services/Speech-Service/regions.md) , která je přidružená k vašemu předplatnému. Pokud například používáte bezplatnou zkušební verzi, oblast je `westus`.

1. Uložte změny do skriptu.

Nyní se vraťte do editoru Unity a přidejte tento skript jako součást do jednoho z vašich herních objektů:

1. V okně **hierarchie** vyberte objekt **plátno** .

1. V okně **inspektora** vyberte tlačítko **Přidat komponentu** .

   [okno kontroly ![v editoru Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png#lightbox)

1. V rozevíracím seznamu vyhledejte skript `HelloWorld`, který jsme vytvořili výše, a přidejte ho. V okně **inspektoru** se zobrazí oddíl **Hello World (skript)** , který obsahuje seznam dvou neinicializovaných vlastností, **výstupního textu** a **tlačítka Start &** . Vlastnosti komponenty Unity odpovídají veřejným vlastnostem třídy `HelloWorld`.

1. Vyberte objekt pro výběr objektu vlastnosti **Start &** (ikona malé kružnice napravo od vlastnosti) a zvolte objekt **tlačítka** , který jste vytvořili dříve.

1. Vyberte výběr objektu vlastnosti **výstupního textu** a zvolte objekt **text** , který jste vytvořili dříve.

   > [!NOTE]
   > Tlačítko má také vnořený textový objekt. Ujistěte se, že je neúmyslně nevybírejte textový výstup (nebo přejmenujte jeden z textových objektů pomocí pole **název** v okně **inspektora** tak, aby nedocházelo k záměně).

## <a name="run-the-application-in-the-unity-editor"></a>Spuštění aplikace v editoru Unity

Teď jste připraveni spustit aplikaci v editoru Unity.

1. Na panelu nástrojů editoru Unity (pod řádkem nabídek) vyberte tlačítko **Přehrát** (trojúhelník směřující vpravo).

1. Přejděte na [ **herní** zobrazení](https://docs.unity3d.com/Manual/GameView.html)a počkejte, než se v **textovém** objektu zobrazí **tlačítko pro rozpoznávání řeči**. (Zobrazí **nový text** , když se aplikace nespustí nebo není připravená na odpověď.)

1. Vyberte tlačítko a vymluvte si anglickou frázi nebo větu do mikrofonu vašeho počítače. Váš hlas se přenáší do služby Speech a přepisu na text, který se zobrazí v **herním** zobrazení.

   [zobrazení herního ![v editoru Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png#lightbox)

1. V okně [ **konzoly** ](https://docs.unity3d.com/Manual/Console.html) vyhledejte zprávy ladění. Pokud se okno **konzoly** nezobrazí, přejděte na panel nabídek a vyberte **okno** > **Obecné** > **konzoly** .

1. Až budete s rozpoznáváním řeči hotovi, vyberte na panelu nástrojů editoru Unity tlačítko **Přehrát** a zastavte aplikaci.

## <a name="additional-options-to-run-this-application"></a>Další možnosti spuštění této aplikace

Tato aplikace se dá nasadit taky do aplikace pro Android, samostatné aplikace Windows nebo aplikace UWP.
Další informace najdete v našem [ukázkovém úložišti](https://aka.ms/csspeech/samples). `quickstart/csharp-unity` složka popisuje konfiguraci pro tyto další cíle.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]
