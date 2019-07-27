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
ms.date: 07/23/2019
ms.author: jhakulin
ms.openlocfilehash: 06831fa933c04827c966e8f6e12aa817f5008b88
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68554150"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-unity-beta"></a>Rychlý start: Rozpoznávání řeči pomocí sady Speech SDK for Unity (beta verze)

K dispozici jsou také rychlé starty pro [Převod textu na řeč](quickstart-text-to-speech-csharp-unity.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Pomocí tohoto průvodce můžete vytvořit aplikaci pro převod řeči na text pomocí [Unity](https://unity3d.com/) a sady Speech SDK for Unity (beta).
Po dokončení můžete použít mikrofon vašeho počítače a v reálném čase přepisovat řeč na text.
Pokud nejste obeznámeni s Unity, doporučujeme před zahájením vývoje aplikace prozkoumat příručku pro [uživatele Unity](https://docs.unity3d.com/Manual/UnityManual.html) .

> [!NOTE]
> Sada Speech SDK for Unity je momentálně ve verzi beta.
> Podporuje desktopy Windows (x86 a x64) nebo Univerzální platforma Windows (x86, x64, ARM/ARM64) a Android (x86, ARM32/64).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto projektu budete potřebovat:

* [Unity 2018,3 nebo novější](https://store.unity.com/) s [Unity 2019,1 Přidání podpory pro UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
     * Pro podporu ARM64 nainstalujte [volitelné nástroje Build pro ARM64 a sadu Windows 10 SDK pro ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/)
* Klíč předplatného pro službu rozpoznávání řeči [Získejte je zdarma](get-started.md).
* Přístup k mikrofonu vašeho počítače.

## <a name="create-a-unity-project"></a>Vytvoření projektu Unity

* Spusťte Unity a na kartě **projekty** vyberte **Nový**.
* Zadejte **název projektu** jako **CSharp-Unity**, **template** jako **3D** a vyberte umístění.
  Pak vyberte **vytvořit projekt**.
* Po určitém časovém intervalu by se měl okno editoru Unity automaticky zobrazit.

## <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

* Sada Speech SDK for Unity (beta) je zabalená jako balíček assetů Unity (. unitypackage).
  Stáhněte si ji z [tady](https://aka.ms/csspeech/unitypackage).
* Importujte sadu Speech SDK tak,  > že vyberete assety**importovat balíček** > **vlastní balíček**.
  Podrobnosti najdete v [dokumentaci k Unity](https://docs.unity3d.com/Manual/AssetPackages.html) .
* V nástroji pro výběr souborů vyberte soubor sady Speech SDK. unitypackage, který jste si stáhli výše.
* Zajistěte, aby byly vybrány všechny soubory, a klikněte na tlačítko **importovat**:

  ![Snímek obrazovky s editorem Unity při importu balíčku assetu Unity sady Speech SDK](media/sdk/qs-csharp-unity-01-import.png)

## <a name="add-ui"></a>Přidat uživatelské rozhraní

Do naší scény přidáme minimální uživatelské rozhraní, které se skládá z tlačítka pro aktivaci rozpoznávání řeči a textového pole, které zobrazí výsledek.

* V [okně hierarchie](https://docs.unity3d.com/Manual/Hierarchy.html) (ve výchozím nastavení vlevo) se zobrazí ukázková scéna, kterou Unity vytvořil s novým projektem.
* Klikněte na tlačítko **vytvořit** v horní části okna hierarchie a vyberte**tlačítko** **uživatelské rozhraní** > .
* Tím dojde k vytvoření tří herních objektů, které lze zobrazit v okně hierarchie: objekt **Button** je vnořen  do objektu plátna a objekt **EventSystem** .
* [Přejděte do zobrazení scény](https://docs.unity3d.com/Manual/SceneViewNavigation.html) , abyste měli dobré zobrazení plátna a tlačítka v [zobrazení scény](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Kliknutím na objekt **tlačítka** v okně hierarchie zobrazíte jeho nastavení v [okně inspektora](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standardně napravo).
* Nastavte vlastnosti **POS X** a **POS Y** na **0**, aby bylo tlačítko zarovnané na střed plátna.
* Znovu klikněte na tlačítko **vytvořit** v horní části okna hierarchie a vyberte**text** **uživatelského rozhraní** > a vytvořte textové pole.
* Kliknutím na objekt **text** v okně hierarchie zobrazíte jeho nastavení v [okně inspektora](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standardně napravo).
* Nastavte vlastnosti **POS X** a **POS Y** na **0** a **120**a nastavte vlastnosti **Width** a **Height** na **240** a **120** , aby se textové pole a tlačítko nepřekrývaly.

Až budete hotovi, uživatelské rozhraní by mělo vypadat podobně jako na tomto snímku obrazovky:

[![Snímek obrazovky uživatelského rozhraní pro rychlé zprovoznění v editoru Unity](media/sdk/qs-csharp-unity-02-ui-inline.png)](media/sdk/qs-csharp-unity-02-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>Přidání vzorového kódu

1. V [okně projektu](https://docs.unity3d.com/Manual/ProjectView.html) (ve výchozím nastavení v levém dolním rohu) klikněte na tlačítko **vytvořit** a pak vyberte  **C# skript**. Pojmenujte `HelloWorld`skript.

1. Upravte skript tak, že na něj dvakrát kliknete.

   > [!NOTE]
   > Můžete nakonfigurovat, který Editor kódu se spustí v části **Upravit** > **Předvolby**, v [příručce pro uživatele Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Nahraďte veškerý kód následujícím kódem:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Vyhledejte řetězec `YourSubscriptionKey` a nahraďte ho klíčovým předplatným služby Speech Services.

1. Vyhledejte řetězec `YourServiceRegion` a nahraďte ho [oblastí](regions.md) přidruženou k vašemu předplatnému. Pokud například používáte bezplatnou zkušební verzi, oblast je `westus`.

1. Uložte změny do skriptu.

1. Zpátky v editoru Unity se skript musí přidat jako součást jednoho z vašich herních objektů.

   * V okně hierarchie  klikněte na objekt plátno. Tím se otevře nastavení v [okně inspektora](https://docs.unity3d.com/Manual/UsingTheInspector.html) (standardně napravo).
   * V okně inspektoru klikněte na tlačítko **Přidat komponentu** a pak vyhledejte skript HelloWorld, který jsme vytvořili výše, a přidejte ho.
   * Všimněte si, že komponenta Hello World obsahuje dvě neinicializované vlastnosti, **Výstupní text** a **tlačítko Start &** , které odpovídají veřejným `HelloWorld` vlastnostem třídy.
     Chcete-li je napravit, klikněte na tlačítko pro výběr objektu (ikona malé kružnice napravo od vlastnosti) a vyberte objekty textu a tlačítka, které jste vytvořili dříve.

     > [!NOTE]
     > Tlačítko má také vnořený textový objekt. Ujistěte se, že je neúmyslně nevybírejte textový výstup (nebo přejmenujte jeden z textových objektů pomocí pole název v okně inspektora, aby nedocházelo k záměně).

## <a name="run-the-application-in-the-unity-editor"></a>Spuštění aplikace v editoru Unity

* Na panelu nástrojů editoru Unity (pod řádkem nabídek) stiskněte tlačítko **Přehrát** .

* Po spuštění aplikace klikněte na toto tlačítko a mluvte do mikrofonu počítače anglickou frázi nebo větu. Váš hlas se přenáší do služby Speech Services a přepisu na text, který se zobrazí v okně.

  [![Snímek obrazovky běžícího rychlého startu v okně hry Unity](media/sdk/qs-csharp-unity-03-output-inline.png)](media/sdk/qs-csharp-unity-03-output-expanded.png#lightbox)

* V [okně konzoly](https://docs.unity3d.com/Manual/Console.html) vyhledejte zprávy ladění.

* Až budete hotovi s rozpoznáváním řeči, ukončete aplikaci kliknutím na tlačítko **Přehrát** na panelu nástrojů editoru Unity.

## <a name="additional-options-to-run-this-application"></a>Další možnosti spuštění této aplikace

Tuto aplikaci je také možné nasadit do systému Android, jako samostatnou aplikaci pro Windows nebo do aplikace UWP.
Podívejte se na naše [ukázkové úložiště](https://aka.ms/csspeech/samples) ve složce Start/CSharp-Unity, která popisuje konfiguraci pro tyto další cíle.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Prozkoumejte C# ukázky na GitHubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Viz také:

- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)
