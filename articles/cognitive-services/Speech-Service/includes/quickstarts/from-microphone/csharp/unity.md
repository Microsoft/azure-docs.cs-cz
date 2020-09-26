---
title: 'Rychlý Start: rozpoznávání řeči z mikrofonu, C# (Unity) – Speech Service'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.custom: devx-track-csharp
ms.topic: include
ms.date: 04/02/2020
ms.author: erhopf
ms.openlocfilehash: 974f147492877f481616b43d67146a03814ada16
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377138"
---
> [!NOTE]
> Sada Speech SDK for Unity podporuje Desktop Windows (x86 a x64) nebo Univerzální platforma Windows (x86, x64, ARM/ARM64), Android (x86, ARM32/64) a iOS (simulátor x64, ARM32 a ARM64).

## <a name="prerequisites"></a>Požadavky

Než začnete:

> [!div class="checklist"]
> * [Vytvoření prostředku Azure Speech](../../../../overview.md#try-the-speech-service-for-free)
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?tabs=unity&pivots=programming-language-csharp)
> * Ujistěte se, že máte přístup k mikrofonu pro záznam zvuku

Pokud jste to již provedli, Skvělé. Pojďme pokračovat.

## <a name="create-a-unity-project"></a>Vytvoření projektu Unity

1. Otevřete Unity. Pokud používáte Unity poprvé, zobrazí se okno **centrum Unity** *<version number>* . (K tomuto oknu se dostanete také tak, že můžete otevřít centrum Unity přímo.)

   [![Okno centra Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png#lightbox)
1. Vyberte **Nové**. Zobrazí se okno **vytvořit nový projekt s Unity** *<version number>* .

   [![Vytvořit nový projekt v centru Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png#lightbox)
1. Do **název projektu**zadejte **CSharp-Unity**.
1. Pokud se v **šablonách**ještě není vybraná možnost **3D** , vyberte ji.
1. V části **umístění**vyberte nebo vytvořte složku, do které chcete projekt uložit.
1. Vyberte **Vytvořit**.

Po uplynutí časového intervalu se zobrazí okno Editor Unity.



## <a name="add-ui"></a>Přidat uživatelské rozhraní

Nyní přidáme k naší scéně minimální uživatelské rozhraní. Toto uživatelské rozhraní se skládá z tlačítka pro aktivaci rozpoznávání řeči a textového pole, které zobrazí výsledek. V okně [ **hierarchie** ](https://docs.unity3d.com/Manual/Hierarchy.html)se zobrazí ukázková scéna, kterou Unity vytvořil s novým projektem.

1. V horní části okna **hierarchie** vyberte tlačítko **vytvořit**  >  **uživatelské rozhraní**  >  **Button**.

   Tato akce vytvoří tři objekty hry, které lze zobrazit v okně **hierarchie** : objekt **tlačítka** , objekt **plátna** obsahující tlačítko a objekt **EventSystem** .

   [![Prostředí editoru Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png#lightbox)

1. [Přejděte do zobrazení **scény** ](https://docs.unity3d.com/Manual/SceneViewNavigation.html) , abyste měli dobré zobrazení plátna a tlačítka v [zobrazení **scény** ](https://docs.unity3d.com/Manual/UsingTheSceneView.html).

1. V okně [ **inspektora** ](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standardně napravo) nastavte vlastnosti **POS X** a **POS Y** na **hodnotu 0**, aby bylo tlačítko zarovnané na střed plátna.

1. V okně **hierarchie** vyberte **vytvořit**  >  text**uživatelského rozhraní**  >  **Text** a vytvořte **textový** objekt.

1. V okně **inspektora** nastavte vlastnosti **POS X** a **pos Y** na **0** a **120**a nastavte vlastnosti **Width** a **Height** na **240** a **120**. Tyto hodnoty zajišťují, aby se textové pole a tlačítko nepřekrývaly.

Až skončíte, zobrazení **scény** by mělo vypadat podobně jako na tomto snímku obrazovky:

[![Zobrazení scény v editoru Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png#lightbox)

## <a name="add-the-sample-code"></a>Přidání vzorového kódu

Chcete-li přidat ukázkový kód skriptu pro projekt Unity, postupujte takto:

1. V [okně projekt](https://docs.unity3d.com/Manual/ProjectView.html)vyberte možnost **vytvořit**  >  **skript v jazyce c#** a přidejte nový skript jazyka c#.

   [![Okno projektu v editoru Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png#lightbox)
1. Pojmenujte skript `HelloWorld` .

1. Dvakrát klikněte `HelloWorld` pro úpravu nově vytvořeného skriptu.

   > [!NOTE]
   > Pokud chcete nakonfigurovat Editor kódu, který bude používat Unity pro úpravy, vyberte **Upravit**  >  **Předvolby**a pak přejít na předvolby **externích nástrojů** . Další informace najdete v příručce pro [uživatele Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Existující skript nahraďte následujícím kódem:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/from-microphone/Assets/Scripts/HelloWorld.cs#code)]

1. Vyhledejte řetězec a nahraďte `YourSubscriptionKey` ho klíčovým předplatným služby Speech.

1. Vyhledejte a nahraďte řetězec `YourServiceRegion` **identifikátorem oblasti** z [oblasti](https://aka.ms/speech/sdkregion) přidružené k vašemu předplatnému.

1. Uložte změny do skriptu.

Nyní se vraťte do editoru Unity a přidejte tento skript jako součást do jednoho z vašich herních objektů:

1. V okně **hierarchie** vyberte objekt **plátno** .

1. V okně **inspektora** vyberte tlačítko **Přidat komponentu** .

   [![Okno inspektora v editoru Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png#lightbox)

1. V rozevíracím seznamu vyhledejte skript, který `HelloWorld` jsme vytvořili výše, a přidejte ho. V okně **inspektoru** se zobrazí oddíl **Hello World (skript)** , který obsahuje seznam dvou neinicializovaných vlastností, **výstupního textu** a **tlačítka Start &**. Tyto vlastnosti komponenty Unity odpovídají veřejným vlastnostem `HelloWorld` třídy.

1. Vyberte objekt pro výběr objektu vlastnosti **Start &** (ikona malé kružnice napravo od vlastnosti) a zvolte objekt **tlačítka** , který jste vytvořili dříve.

1. Vyberte výběr objektu vlastnosti **výstupního textu** a zvolte objekt **text** , který jste vytvořili dříve.

   > [!NOTE]
   > Tlačítko má také vnořený textový objekt. Ujistěte se, že je neúmyslně nevybírejte textový výstup (nebo přejmenujte jeden z textových objektů pomocí pole **název** v okně **inspektora** tak, aby nedocházelo k záměně).

## <a name="run-the-application-in-the-unity-editor"></a>Spuštění aplikace v editoru Unity

Teď jste připraveni spustit aplikaci v editoru Unity.

1. Na panelu nástrojů editoru Unity (pod řádkem nabídek) vyberte tlačítko **Přehrát** (trojúhelník směřující vpravo).

1. Přejděte na [ **herní** zobrazení](https://docs.unity3d.com/Manual/GameView.html)a počkejte, než se v **textovém** objektu zobrazí **tlačítko pro rozpoznávání řeči**. (Zobrazí **nový text** , když se aplikace nespustí nebo není připravená na odpověď.)

1. Vyberte tlačítko a vymluvte si anglickou frázi nebo větu do mikrofonu vašeho počítače. Váš hlas se přenáší do služby Speech a přepisu na text, který se zobrazí v **herním** zobrazení.

   [![Herní zobrazení v editoru Unity](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png#lightbox)

1. V okně [ **konzoly** ](https://docs.unity3d.com/Manual/Console.html) vyhledejte zprávy ladění. Pokud se okno **konzoly** nezobrazuje, přejděte na panel nabídek a vyberte **okno**  >  **Obecná**  >  **Konzola** a zobrazte ho.

1. Až budete s rozpoznáváním řeči hotovi, vyberte na panelu nástrojů editoru Unity tlačítko **Přehrát** a zastavte aplikaci.

## <a name="additional-options-to-run-this-application"></a>Další možnosti spuštění této aplikace

Tato aplikace se dá nasadit taky do aplikace pro Android, samostatné aplikace Windows nebo aplikace UWP.
Další informace najdete v našem [ukázkovém úložišti](https://aka.ms/csspeech/samples). Tato `quickstart/csharp-unity` Složka popisuje konfiguraci pro tyto další cíle.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

