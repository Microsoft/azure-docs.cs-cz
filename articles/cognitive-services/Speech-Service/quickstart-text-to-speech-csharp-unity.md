---
title: 'Rychlý start: Syntetizace řeči, Unity – hlasové služby'
titleSuffix: Azure Cognitive Services
description: Použijte tuto příručku k vytvoření aplikace převod textu na řeč pomocí Unity a sadou SDK pro řeč pro Unity (beta verze). Až budete hotovi, můžete syntetizace řeč z textu v reálném čase na vaše zařízení mluvčího.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 6/26/2019
ms.author: yinhew
ms.openlocfilehash: 5240ea45097ce3c0ae7ccbc15a7f99b2f5990832
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67467485"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-unity-beta"></a>Rychlý start: Syntetizace řeči se sadou SDK pro řeč pro Unity (beta verze)

Rychlí průvodci jsou také k dispozici pro [rozpoznávání řeči](quickstart-csharp-unity.md).

Použijte tato příručka k vytvoření aplikace pro převod textu na řeč pomocí [Unity](https://unity3d.com/) a sadou SDK pro řeč pro Unity (beta verze).
Až budete hotovi, můžete syntetizace řeč z textu v reálném čase na vaše zařízení mluvčího.
Pokud nejste obeznámeni s Unity, se doporučuje pro zkoumání [uživatelská příručka pro Unity](https://docs.unity3d.com/Manual/UnityManual.html) před zahájením vývoje aplikací.

> [!NOTE]
> Sadou SDK pro řeč pro Unity je aktuálně ve verzi beta.
> Podporuje Windows Desktop (x86 a x64) nebo univerzální platformu Windows (x86, x64, ARM a ARM64) a Android (x86 ARM32/64).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto projektu budete potřebovat:

* [Unity 2018.3 nebo novější](https://store.unity.com/) s [Unity 2019.1 Přidání podpory pro UPW ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
     * Pro podporu ARM64, nainstalujte [volitelné sestavení nástroje pro ARM64 a Windows 10 SDK pro ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/) 
* Klíč předplatného pro službu rozpoznávání řeči. [Získat zdarma](get-started.md).

## <a name="create-a-unity-project"></a>Vytváření projektů Unity

* Spustit Unity a v části **projekty** kartě vyberte **nový**.
* Zadejte **název projektu** jako **csharp unity**, **šablony** jako **3D** a vyberte umístění.
  Potom vyberte **vytvořit projekt**.
* V okně editoru Unity by po si trochu času to vyskočit.

## <a name="install-the-speech-sdk"></a>Instalace sady SDK pro řeč

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

* Sadou SDK pro řeč pro Unity (beta verze) je zabalený jako balíček asset Unity (.unitypackage).
  Stáhněte si ji z [tady](https://aka.ms/csspeech/unitypackage).
* Importovat sadou SDK pro řeč tak, že vyberete **prostředky** > **importovat balíček** > **vlastní balíček**.
  Podívejte se [dokumentace k Unity](https://docs.unity3d.com/Manual/AssetPackages.html) podrobnosti.
* V výběr souborů vyberte soubor .unitypackage sadou SDK pro řeč, který jste stáhli výše.
* Ujistěte se, že jsou vybrány všechny soubory a klikněte na tlačítko **Import**:

  ![Snímek obrazovky z Unity editoru při importu balíčku asset řeči SDK Unity](media/sdk/qs-csharp-unity-01-import.png)

## <a name="add-ui"></a>Přidání uživatelského rozhraní

Přidáme minimálního uživatelského rozhraní do našich scény skládající se z vstupního pole zadejte text pro syntézu, tlačítko pro syntézu řeči aktivační události a textové pole k zobrazení výsledku.

* V [okno hierarchie](https://docs.unity3d.com/Manual/Hierarchy.html) (ve výchozím nastavení na levé straně), se ukázková Scéna zobrazuje, že Unity vytvořené pomocí nového projektu.
* Klikněte na tlačítko **vytvořit** tlačítko v horní části okna hierarchii a vyberte **uživatelského rozhraní** > **vstupní pole**.
* Tím se vytvoří tři herním objektům, které se zobrazí v okně hierarchie: **vstupní pole** vnořené objektu **plátna** objektu a **EventSystem** objektu.
* [Přejděte scény](https://docs.unity3d.com/Manual/SceneViewNavigation.html) proto měly dobrý výhled na plátno a vstupních polí v [scény](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Klikněte na tlačítko **vstupní pole** objektu v okně hierarchie k jeho nastavení v zobrazení [okně Inspektor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (ve výchozím nastavení na pravé straně).
* Nastavte **Pos X** a **Pos Y** vlastností **0**, takže vstupní pole je zarovnaný na střed uprostřed na plátno.
* Klikněte na tlačítko **vytvořit** tlačítko v horní části okna hierarchii znovu a vyberte **uživatelského rozhraní** > **tlačítko** vytvoření tlačítka.
* Klikněte na tlačítko **tlačítko** objektu v okně hierarchie k jeho nastavení v zobrazení [okně Inspektor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (ve výchozím nastavení na pravé straně).
* Nastavte **Pos X** a **Pos Y** vlastností **0** a **-48**a nastavte **šířka** a **Výška** vlastností **160** a **30** zajistit, že tlačítko a vstupní pole nemusí být stejné.
* Klikněte na tlačítko **vytvořit** tlačítko v horní části okna hierarchii znovu a vyberte **uživatelského rozhraní** > **Text** vytvoření textového pole.
* Klikněte na tlačítko **Text** objektu v okně hierarchie k jeho nastavení v zobrazení [okně Inspektor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (ve výchozím nastavení na pravé straně).
* Nastavte **Pos X** a **Pos Y** vlastností **0** a **80**a nastavte **šířka** a  **Výška** vlastností **320** a **80** zajistit, že do textového pole a vstupní pole nemusí být stejné.
* Klikněte na tlačítko **vytvořit** tlačítko v horní části okna hierarchii znovu a vyberte **zvuku** > **zdroje zvuku** k vytvoření zdroje zvuku.

Až skončíte, uživatelské rozhraní by měl vypadat zhruba takto:

[![Snímek obrazovky uživatelského rozhraní pro rychlý start v Unity editoru](media/sdk/qs-tts-csharp-unity-ui-inline.png)](media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>Přidání vzorového kódu

1. V [okno projektu](https://docs.unity3d.com/Manual/ProjectView.html) (ve výchozím nastavení v levé dolní části), klikněte na tlačítko **vytvořit** tlačítko a pak vyberte  **C# skript**. Název skriptu `HelloWorld`.

1. Upravte skript, že na něj kliknuli dvakrát.

   > [!NOTE]
   > Můžete nakonfigurovat, které editor kódu bude spuštěna v rámci **upravit** > **Předvolby**, najdete v článku [uživatelská příručka pro Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Veškerý kód nahraďte následujícím kódem:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Najít a nahradit řetězec `YourSubscriptionKey` s klíči předplatného hlasové služby.

1. Vyhledejte řetězec `YourServiceRegion` a nahraďte ho [oblastí](regions.md) přidruženou k vašemu předplatnému. Pokud například používáte bezplatnou zkušební verzi, oblast je `westus`.

1. Uložte změny do skriptu.

1. Zpět v Unity editoru skriptu musí být přidán jako součást na jeden z vašich her objektů.

   * Klikněte na **plátna** objektu v okně hierarchie. Otevře nastavení [okně Inspektor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (ve výchozím nastavení na pravé straně).
   * Klikněte na tlačítko **přidat součást** tlačítka v okně Inspektor a pak vyhledejte skript HelloWorld vytvoříme výše a přidejte ji.
   * Všimněte si, že komponenta Hello World má čtyři neinicializovaných vlastností **textový výstup**, **vstupní pole**, **mluvit tlačítko** a **zdroje zvuku**, které se shodují s veřejné vlastnosti `HelloWorld` třídy.
     Je nastavit, klikněte na výběr objektů (ikona malý kruh napravo od vlastnost) a zvolte text a tlačítko objekty, které jste vytvořili dříve.

     > [!NOTE]
     > Vstupní pole a tlačítko mají také vnořeného textu objektu. Ujistěte se, že nejsou omylem vyberte pro textový výstup (nebo přejmenovat textové objekty, aby nedošlo ke zmatkům pomocí pole názvu v okně Inspektor).

## <a name="run-the-application-in-the-unity-editor"></a>Spusťte aplikaci v Unity editoru

* Stisknutím klávesy **Přehrát** tlačítko v panelu nástrojů editoru Unity (pod nabídek).

* Po spuštění aplikace, zadejte nějaký text do vstupního pole a klikněte na tlačítko. Text se přenášejí do služeb řeči a syntetizovat do mluvené řeči, který přehraje na reproduktoru.

  [![Snímek obrazovky spuštěné rychlý start v okně hry Unity](media/sdk/qs-tts-csharp-unity-output-inline.png)](media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* Zkontrolujte, [okna konzoly](https://docs.unity3d.com/Manual/Console.html) pro zprávy ladění.

* Až to budete mít syntéz řeči, klikněte na tlačítko **Přehrát** tlačítko v panelu nástrojů editoru Unity a zastavte tak aplikace.

## <a name="additional-options-to-run-this-application"></a>Další možnosti ke spuštění této aplikace

Tato aplikace je také nasadit do systému Android, jako samostatné aplikace Windows nebo aplikaci pro UPW.
Odkazovat na naše [ukázkové úložiště](https://aka.ms/csspeech/samples) ve složce Rychlý start/csharp-unity, který popisuje konfiguraci pro tyto další cíle.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Prozkoumejte C# ukázky na Githubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Další informace najdete v tématech

- [Přizpůsobení hlasová písma](how-to-customize-voice-font.md)
- [Záznam hlasu ukázky](record-custom-voice-samples.md)
