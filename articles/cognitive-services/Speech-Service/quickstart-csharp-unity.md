---
title: 'Rychlý start: Rozpoznávání řeči, Unity – Speech Service'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto průvodce můžete vytvořit aplikaci pro převod řeči na text s Unity a sadou Speech SDK for Unity (beta). Po dokončení můžete použít mikrofon vašeho počítače a v reálném čase přepisovat řeč na text.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/24/2019
ms.author: jhakulin
ms.openlocfilehash: 1b6e60edd86cff2d657b562f05351e20571c0909
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815394"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-unity-beta"></a>Rychlý start: Rozpoznávání řeči pomocí sady Speech SDK for Unity (beta verze)

K dispozici jsou také rychlé starty pro [Převod textu na řeč](quickstart-text-to-speech-csharp-unity.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Pomocí tohoto průvodce můžete vytvořit aplikaci pro převod řeči na text pomocí [Unity](https://unity3d.com/) a sady Speech SDK for Unity (beta).
Až budete hotovi, můžete mluvit do svého zařízení a přepisovat řeč na text v reálném čase.
Pokud s Unity začínáte, doporučujeme, abyste před vývojem aplikace prostudovali příručku pro [uživatele Unity](https://docs.unity3d.com/Manual/UnityManual.html) .

> [!NOTE]
> Sada Speech SDK for Unity je momentálně ve verzi beta.
> Podporuje desktopy Windows (x86 a x64) nebo Univerzální platforma Windows (x86, x64, ARM/ARM64) a Android (x86, ARM32/64).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto projektu budete potřebovat:

- [Unity 2018,3 nebo novější](https://store.unity.com/) s [Unity 2019,1 Přidání podpory pro UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal)
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Také je přijatelné verze 15,9 nebo vyšší ze sady Visual Studio 2017.
  - Pro podporu ARM64 nainstalujte [volitelné nástroje Build pro ARM64 a sadu Windows 10 SDK pro ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).
- Klíč předplatného pro službu rozpoznávání řeči [Získejte je zdarma](get-started.md).
- Přístup k mikrofonu vašeho počítače.

## <a name="create-a-unity-project"></a>Vytvoření projektu Unity

1. Otevřete Unity. Pokud používáte Unity poprvé, zobrazí se okno **centrum** *<version number>* Unity. (K tomuto oknu se dostanete také tak, že můžete otevřít centrum Unity přímo.)

   [![Okno centra Unity](media/sdk/qs-csharp-unity-hub.png)](media/sdk/qs-csharp-unity-hub.png#lightbox)
1. Vyberte **Nový**. Zobrazí se okno **vytvořit nový projekt s Unity** *<version number>* .

   [![Vytvořit nový projekt v centru Unity](media/sdk/qs-csharp-unity-create-a-new-project.png)](media/sdk/qs-csharp-unity-create-a-new-project.png#lightbox)
1. Do **název projektu**zadejte **CSharp-Unity**.
1. Pokudse v šablonách ještě není vybraná možnost **3D** , vyberte ji.
1. V části **umístění**vyberte nebo vytvořte složku, do které chcete projekt uložit.
1. Vyberte **Vytvořit**.

Po uplynutí časového intervalu se zobrazí okno Editor Unity.

## <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Pokud chcete sadu Speech SDK for Unity nainstalovat, postupujte takto:

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

1. Stáhněte a otevřete [sadu Speech SDK for Unity (beta)](https://aka.ms/csspeech/unitypackage), která je zabalená jako balíček assetů Unity (. unitypackage). Po otevření balíčku assetu se zobrazí dialogové okno **importovat balíček Unity** .

   [![Dialogové okno Import balíčku Unity v editoru Unity](media/sdk/qs-csharp-unity-01-import.png)](media/sdk/qs-csharp-unity-01-import.png#lightbox)
1. Zajistěte, aby byly vybrány všechny soubory, a vyberte **importovat**. Po chvíli se balíček assetů Unity naimportuje do vašeho projektu.

Další informace o importu balíčků assetů do Unity najdete v [dokumentaci k Unity](https://docs.unity3d.com/Manual/AssetPackages.html).

## <a name="add-ui"></a>Přidat uživatelské rozhraní

Nyní přidáme k naší scéně minimální uživatelské rozhraní. Toto uživatelské rozhraní se skládá z tlačítka pro aktivaci rozpoznávání řeči a textového pole, které zobrazí výsledek. V okně [ **hierarchie** ](https://docs.unity3d.com/Manual/Hierarchy.html)se zobrazí ukázková scéna, kterou Unity vytvořil s novým projektem.

1. V horní části okna **hierarchie** vyberte**tlačítko** **vytvořit** > **uživatelské rozhraní** > .

   Tato akce vytvoří tři objekty hry, které lze zobrazit v okně **hierarchie** : objekt **tlačítka** , objekt plátna obsahující tlačítko a objekt **EventSystem** .

   [![Prostředí editoru Unity](media/sdk/qs-csharp-unity-editor-window.png)](media/sdk/qs-csharp-unity-editor-window.png#lightbox)

1. [Přejděte do zobrazení **scény** ](https://docs.unity3d.com/Manual/SceneViewNavigation.html) , abyste měli dobré zobrazení plátna a tlačítka v [zobrazení **scény** ](https://docs.unity3d.com/Manual/UsingTheSceneView.html).

1. V [ okně inspektora](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standardně napravo) nastavte vlastnosti **POS X** a **POS Y** na **hodnotu 0**, aby bylo tlačítko zarovnané na střed plátna.

1. V okně **hierarchie** vyberte **vytvořit** > **text** **uživatelského rozhraní** > a vytvořte **textový** objekt.

1. V okně **inspektora** nastavte vlastnosti **POS X** a **pos Y** na **0** a **120**a nastavte vlastnosti **Width** a **Height** na **240** a **120**. Tyto hodnoty zajišťují, aby se textové pole a tlačítko nepřekrývaly.

Až skončíte, zobrazení **scény** by mělo vypadat podobně jako na tomto snímku obrazovky:

[![Zobrazení scény v editoru Unity](media/sdk/qs-csharp-unity-02-ui-inline.png)](media/sdk/qs-csharp-unity-02-ui-inline.png#lightbox)

## <a name="add-the-sample-code"></a>Přidání vzorového kódu

Chcete-li přidat ukázkový kód skriptu pro projekt Unity, postupujte takto:

1. V [okně projekt](https://docs.unity3d.com/Manual/ProjectView.html)vyberte **vytvořit**  >   **C# skript** pro přidání nového C# skriptu.

   [![Okno projektu v editoru Unity](media/sdk/qs-csharp-unity-project-window.png)](media/sdk/qs-csharp-unity-project-window.png#lightbox)
1. Pojmenujte `HelloWorld`skript.

1. Dvakrát klikněte `HelloWorld` pro úpravu nově vytvořeného skriptu.

   > [!NOTE]
   > Pokud chcete nakonfigurovat Editor kódu, který bude používat Unity pro úpravy, vyberte **Upravit** > **Předvolby**a pak přejít na předvolby **externích nástrojů** . Další informace najdete v příručce pro [uživatele Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Existující skript nahraďte následujícím kódem:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Vyhledejte a nahraďte řetězec `YourSubscriptionKey` pomocí klíče předplatného služby Speech Services.

1. Vyhledejte řetězec `YourServiceRegion` a nahraďte ho [oblastí](regions.md) , která je přidružená k vašemu předplatnému. Pokud například používáte bezplatnou zkušební verzi, oblast je `westus`.

1. Uložte změny do skriptu.

Nyní se vraťte do editoru Unity a přidejte tento skript jako součást do jednoho z vašich herních objektů:

1. V okně **hierarchie** vyberte objekt plátno .

1. V okně **inspektora** vyberte tlačítko **Přidat komponentu** .

   [![Okno inspektora v editoru Unity](media/sdk/qs-csharp-unity-inspector-window.png)](media/sdk/qs-csharp-unity-inspector-window.png#lightbox)

1. V rozevíracím seznamu vyhledejte skript, který `HelloWorld` jsme vytvořili výše, a přidejte ho. V okně inspektoru se zobrazí oddíl **Hello World (skript)** , který obsahuje seznam dvou neinicializovaných vlastností, **výstupního textu** a **tlačítka Start &** . Tyto vlastnosti komponenty Unity odpovídají veřejným vlastnostem `HelloWorld` třídy.

1. Vyberte objekt pro výběr objektu vlastnosti **Start &** (ikona malé kružnice napravo od vlastnosti) a zvolte objekt **tlačítka** , který jste vytvořili dříve.

1. Vyberte výběr objektu vlastnosti **výstupního textu** a zvolte objekt **text** , který jste vytvořili dříve.

   > [!NOTE]
   > Tlačítko má také vnořený textový objekt. Ujistěte se, že je neúmyslně nevybírejte textový výstup (nebo přejmenujte jeden z textových objektů pomocí pole **název** v okně inspektora tak, aby nedocházelo k záměně).

## <a name="run-the-application-in-the-unity-editor"></a>Spuštění aplikace v editoru Unity

Teď jste připraveni spustit aplikaci v editoru Unity.

1. Na panelu nástrojů editoru Unity (pod řádkem nabídek) vyberte tlačítko **Přehrát** (trojúhelník směřující vpravo).

1. Přejděte na [ **herní** zobrazení](https://docs.unity3d.com/Manual/GameView.html)a počkejte, než se v **textovém** objektu zobrazí **tlačítko pro rozpoznávání řeči**. (Zobrazí **nový text** , když se aplikace nespustí nebo není připravená na odpověď.)

1. Vyberte tlačítko a vymluvte si anglickou frázi nebo větu do mikrofonu vašeho počítače. Váš hlas se přenáší do služby Speech Services a přepisu na text, který se zobrazí v zobrazení **hry** .

   [![Herní zobrazení v editoru Unity](media/sdk/qs-csharp-unity-03-output-inline.png)](media/sdk/qs-csharp-unity-03-output-inline.png#lightbox)

1. V okně [ **konzoly** ](https://docs.unity3d.com/Manual/Console.html) vyhledejte zprávy ladění. Pokud se okno **konzoly** nezobrazuje, přejděte na panel nabídek a vyberte **okno** > **Obecná** > **Konzola** a zobrazte ho.

1. Až budete s rozpoznáváním řeči hotovi, vyberte na panelu nástrojů editoru Unity tlačítko **Přehrát** a zastavte aplikaci.

## <a name="additional-options-to-run-this-application"></a>Další možnosti spuštění této aplikace

Tato aplikace se dá nasadit taky do aplikace pro Android, samostatné aplikace Windows nebo aplikace UWP.
Další informace najdete v našem [ukázkovém úložišti](https://aka.ms/csspeech/samples). Tato `quickstart/csharp-unity` složka popisuje konfiguraci pro tyto další cíle.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Prozkoumejte C# ukázky na GitHubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Viz také:

- [Výuka modelu pro Custom Speech](how-to-custom-speech-train-model.md)
