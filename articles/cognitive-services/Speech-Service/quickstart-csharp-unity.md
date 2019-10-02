---
title: 'Rychlý Start: rozpoznávání řeči, služba Unity – Speech'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto průvodce můžete vytvořit aplikaci pro převod řeči na text s Unity a sadou Speech SDK for Unity. Po dokončení můžete použít mikrofon vašeho počítače a v reálném čase přepisovat řeč na text.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/17/2019
ms.author: jhakulin
ms.openlocfilehash: a7ac6831fd21fcc0dc425b57f5d73d8c328f9350
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803469"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-unity"></a>Rychlý Start: rozpoznávání řeči pomocí sady Speech SDK for Unity

K dispozici jsou také rychlé starty pro [Převod textu na řeč](quickstart-text-to-speech-csharp-unity.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Pomocí tohoto průvodce můžete vytvořit aplikaci pro převod řeči na text pomocí [Unity](https://unity3d.com/) a sady Speech SDK for Unity.
Až budete hotovi, můžete mluvit do svého zařízení a přepisovat řeč na text v reálném čase.
Pokud s Unity začínáte, doporučujeme, abyste před vývojem aplikace prostudovali [příručku pro uživatele Unity](https://docs.unity3d.com/Manual/UnityManual.html) .

> [!NOTE]
> Sada Speech SDK for Unity podporuje Desktop Windows (x86 a x64) nebo Univerzální platforma Windows (x86, x64, ARM/ARM64), Android (x86, ARM32/64) a iOS (simulátor x64, ARM32 a ARM64).

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto projektu budete potřebovat:

- [Unity 2018,3 nebo novější](https://store.unity.com/) s [Unity 2019,1 Přidání podpory pro UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal)
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Také je přijatelné verze 15,9 nebo vyšší ze sady Visual Studio 2017.
- Pro podporu Windows ARM64 nainstalujte [volitelné nástroje sestavení pro ARM64 a sadu Windows 10 SDK pro ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).
- Klíč předplatného pro službu rozpoznávání řeči [Získejte je zdarma](get-started.md).
- Přístup k mikrofonu vašeho počítače.

## <a name="create-a-unity-project"></a>Vytvoření projektu Unity

1. Otevřete Unity. Pokud používáte Unity poprvé, zobrazí se okno **centrum Unity** *<version number>* . (K tomuto oknu se dostanete také tak, že můžete otevřít centrum Unity přímo.)

   [@no__t – okno centra 1Unity](media/sdk/qs-csharp-unity-hub.png)](media/sdk/qs-csharp-unity-hub.png#lightbox)
1. Vyberte **Nový**. Zobrazí se okno **vytvořit nový projekt s** *@no__t Unity – 2* .

   [@no__t – 1Create nový projekt v centru Unity](media/sdk/qs-csharp-unity-create-a-new-project.png)](media/sdk/qs-csharp-unity-create-a-new-project.png#lightbox)
1. Do **název projektu**zadejte **CSharp-Unity**.
1. Pokud se v **šablonách**ještě není vybraná možnost **3D** , vyberte ji.
1. V části **umístění**vyberte nebo vytvořte složku, do které chcete projekt uložit.
1. Vyberte **Create** (Vytvořit).

Po uplynutí časového intervalu se zobrazí okno Editor Unity.

## <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Pokud chcete sadu Speech SDK for Unity nainstalovat, postupujte takto:

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

1. Stáhněte a otevřete [sadu Speech SDK for Unity](https://aka.ms/csspeech/unitypackage), která je zabalená jako balíček assetů Unity (. unitypackage). Po otevření balíčku assetu se zobrazí dialogové okno **importovat balíček Unity** .

   [dialogová okna balíčku Unity @no__t 1Import v editoru Unity](media/sdk/qs-csharp-unity-01-import.png)](media/sdk/qs-csharp-unity-01-import.png#lightbox)
1. Zajistěte, aby byly vybrány všechny soubory, a vyberte **importovat**. Po chvíli se balíček assetů Unity naimportuje do vašeho projektu.

Další informace o importu balíčků assetů do Unity najdete v [dokumentaci k Unity](https://docs.unity3d.com/Manual/AssetPackages.html).

## <a name="add-ui"></a>Přidat uživatelské rozhraní

Nyní přidáme k naší scéně minimální uživatelské rozhraní. Toto uživatelské rozhraní se skládá z tlačítka pro aktivaci rozpoznávání řeči a textového pole, které zobrazí výsledek. V okně [ **hierarchie** ](https://docs.unity3d.com/Manual/Hierarchy.html)se zobrazí ukázková scéna, kterou Unity vytvořil s novým projektem.

1. V horní části okna **hierarchie** vyberte**tlačítko** **vytvořit** > **uživatelské rozhraní** > .

   Tato akce vytvoří tři objekty hry, které lze zobrazit v okně **hierarchie** : objekt **tlačítka** , objekt **plátna** obsahující tlačítko a objekt **EventSystem** .

   [@no__t – prostředí editoru 1Unity](media/sdk/qs-csharp-unity-editor-window.png)](media/sdk/qs-csharp-unity-editor-window.png#lightbox)

1. [Přejděte do zobrazení **scény** ](https://docs.unity3d.com/Manual/SceneViewNavigation.html) , abyste měli dobré zobrazení plátna a tlačítka v [zobrazení **scény** ](https://docs.unity3d.com/Manual/UsingTheSceneView.html).

1. V okně [ **inspektora** ](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standardně napravo) nastavte vlastnosti **POS X** a **POS Y** na **hodnotu 0**, aby bylo tlačítko zarovnané na střed plátna.

1. V okně **hierarchie** vyberte **vytvořit**text  > **uživatelské rozhraní**@no__t **-4.** tím vytvoříte **textový** objekt.

1. V okně **inspektora** nastavte vlastnosti **POS X** a **pos Y** na **0** a **120**a nastavte vlastnosti **Width** a **Height** na **240** a **120**. Tyto hodnoty zajišťují, aby se textové pole a tlačítko nepřekrývaly.

Až skončíte, zobrazení **scény** by mělo vypadat podobně jako na tomto snímku obrazovky:

[zobrazení @no__t – 1Scene v editoru Unity](media/sdk/qs-csharp-unity-02-ui-inline.png)](media/sdk/qs-csharp-unity-02-ui-inline.png#lightbox)

## <a name="add-the-sample-code"></a>Přidání vzorového kódu

Chcete-li přidat ukázkový kód skriptu pro projekt Unity, postupujte takto:

1. V [okně projekt](https://docs.unity3d.com/Manual/ProjectView.html)vyberte **vytvořit** **C# skript**  >  pro přidání nového C# skriptu.

   [okno @no__t – 1Project v editoru Unity](media/sdk/qs-csharp-unity-project-window.png)](media/sdk/qs-csharp-unity-project-window.png#lightbox)
1. Pojmenujte skript `HelloWorld`.

1. Dvojím kliknutím na `HelloWorld` upravíte nově vytvořený skript.

   > [!NOTE]
   > Chcete-li nakonfigurovat Editor kódu, který má aplikace Unity použít pro úpravy, vyberte možnost **upravit** > **Předvolby**a pak přejít na předvolby **externích nástrojů** . Další informace najdete v příručce pro [uživatele Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Existující skript nahraďte následujícím kódem:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Vyhledejte a nahraďte řetězec `YourSubscriptionKey` pomocí klíče předplatného služby Speech Services.

1. Vyhledejte a nahraďte řetězec `YourServiceRegion` s [oblastí](regions.md) přidruženou k vašemu předplatnému. Pokud například používáte bezplatnou zkušební verzi, oblast je `westus`.

1. Uložte změny do skriptu.

Nyní se vraťte do editoru Unity a přidejte tento skript jako součást do jednoho z vašich herních objektů:

1. V okně **hierarchie** vyberte objekt **plátno** .

1. V okně **inspektora** vyberte tlačítko **Přidat komponentu** .

   [okno @no__t – 1Inspector v editoru Unity](media/sdk/qs-csharp-unity-inspector-window.png)](media/sdk/qs-csharp-unity-inspector-window.png#lightbox)

1. V rozevíracím seznamu vyhledejte skript `HelloWorld`, který jsme vytvořili výše, a přidejte ho. V okně **inspektoru** se zobrazí oddíl **Hello World (skript)** , který obsahuje seznam dvou neinicializovaných vlastností, **výstupního textu** a **tlačítka Start &** . Vlastnosti komponenty Unity odpovídají veřejným vlastnostem třídy `HelloWorld`.

1. Vyberte objekt pro výběr objektu vlastnosti **Start &** (ikona malé kružnice napravo od vlastnosti) a zvolte objekt **tlačítka** , který jste vytvořili dříve.

1. Vyberte výběr objektu vlastnosti **výstupního textu** a zvolte objekt **text** , který jste vytvořili dříve.

   > [!NOTE]
   > Tlačítko má také vnořený textový objekt. Ujistěte se, že je neúmyslně nevybírejte textový výstup (nebo přejmenujte jeden z textových objektů pomocí pole **název** v okně **inspektora** tak, aby nedocházelo k záměně).

## <a name="run-the-application-in-the-unity-editor"></a>Spuštění aplikace v editoru Unity

Teď jste připraveni spustit aplikaci v editoru Unity.

1. Na panelu nástrojů editoru Unity (pod řádkem nabídek) vyberte tlačítko **Přehrát** (trojúhelník směřující vpravo).

1. Přejděte na [ **herní** zobrazení](https://docs.unity3d.com/Manual/GameView.html)a počkejte, než se v **textovém** objektu zobrazí **tlačítko pro rozpoznávání řeči**. (Zobrazí **nový text** , když se aplikace nespustí nebo není připravená na odpověď.)

1. Vyberte tlačítko a vymluvte si anglickou frázi nebo větu do mikrofonu vašeho počítače. Váš hlas se přenáší do služby Speech Services a přepisu na text, který se zobrazí v zobrazení **hry** .

   [zobrazení @no__t – 1Game v editoru Unity](media/sdk/qs-csharp-unity-03-output-inline.png)](media/sdk/qs-csharp-unity-03-output-inline.png#lightbox)

1. V okně [ **konzoly** ](https://docs.unity3d.com/Manual/Console.html) vyhledejte zprávy ladění. Pokud se okno **konzoly** nezobrazí, přejděte na panel nabídek a vyberte **okno** > **Obecná** **Konzola**  > .

1. Až budete s rozpoznáváním řeči hotovi, vyberte na panelu nástrojů editoru Unity tlačítko **Přehrát** a zastavte aplikaci.

## <a name="additional-options-to-run-this-application"></a>Další možnosti spuštění této aplikace

Tato aplikace se dá nasadit taky do aplikace pro Android, samostatné aplikace Windows nebo aplikace UWP.
Další informace najdete v našem [ukázkovém úložišti](https://aka.ms/csspeech/samples). Složka `quickstart/csharp-unity` popisuje konfiguraci pro tyto další cíle.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte C# ukázky na GitHubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Další informace najdete v tématech

- [Výuka modelu pro Custom Speech](how-to-custom-speech-train-model.md)
