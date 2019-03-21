---
title: 'Rychlý start: Rozpoznávání řeči, Unity – hlasové služby'
titleSuffix: Azure Cognitive Services
description: Použijte tuto příručku k vytvoření aplikace řeči na text pomocí Unity a sadou SDK pro řeč pro Unity (beta verze). Po dokončení můžete použít mikrofon vašeho počítače a v reálném čase přepisovat řeč na text.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 2/20/2019
ms.author: wolfma
ms.openlocfilehash: cdde9f0ec69bec48ae0fb747db0cc49e81920817
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "57872554"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-unity-beta"></a>Rychlý start: Rozpoznávání řeči se sadou SDK pro řeč pro Unity (beta verze)

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Použijte tato příručka k vytvoření aplikace řeči na text pomocí [Unity](https://unity3d.com/) a sadou SDK pro řeč pro Unity (beta verze).
Po dokončení můžete použít mikrofon vašeho počítače a v reálném čase přepisovat řeč na text.
Pokud nejste obeznámeni s Unity, se doporučuje pro zkoumání [uživatelská příručka pro Unity](https://docs.unity3d.com/Manual/UnityManual.html) před zahájením vývoje aplikací.

> [!NOTE]
> Sadou SDK pro řeč pro Unity je aktuálně ve verzi beta.
> Podporuje x86 a x64 (samostatné aplikace klasické pracovní plochy nebo univerzální platformu Windows), Windows a Android (ARM32/64, x86).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto projektu budete potřebovat:

* [Unity 2018.3 nebo novější](https://store.unity.com/)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Klíč předplatného pro službu rozpoznávání řeči. [Získat zdarma](get-started.md).
* Přístup k mikrofonu v počítači.

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

Přidáme minimálního uživatelského rozhraní do našich scény skládající se z tlačítka pro aktivaci rozpoznávání řeči a textové pole k zobrazení výsledku.

* V [okno hierarchie](https://docs.unity3d.com/Manual/Hierarchy.html) (ve výchozím nastavení na levé straně), se ukázková Scéna zobrazuje, že Unity vytvořené pomocí nového projektu.
* Klikněte na tlačítko **vytvořit** tlačítko v horní části okna hierarchii a vyberte **uživatelského rozhraní** > **tlačítko**.
* Tím se vytvoří tři herním objektům, které se zobrazí v okně hierarchie: **tlačítko** vnořené objektu **plátna** objektu a **EventSystem** objektu.
* [Přejděte scény](https://docs.unity3d.com/Manual/SceneViewNavigation.html) proto měly dobrý výhled na plátno a tlačítko v [scény](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Klikněte na tlačítko **tlačítko** objektu v okně hierarchie k jeho nastavení v zobrazení [okně Inspektor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (ve výchozím nastavení na pravé straně).
* Nastavte **Pos X** a **Pos Y** vlastností **0**, takže je zarovnaný na střed na tlačítko uprostřed na plátno.
* Klikněte na tlačítko **vytvořit** tlačítko v horní části okna hierarchii znovu a vyberte **uživatelského rozhraní** > **Text** vytvoření textového pole.
* Klikněte na tlačítko **Text** objektu v okně hierarchie k jeho nastavení v zobrazení [okně Inspektor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (ve výchozím nastavení na pravé straně).
* Nastavte **Pos X** a **Pos Y** vlastností **0** a **120**a nastavte **šířka** a **Výška** vlastností **240** a **120** zajistit, že do textového pole a tlačítko nepřekrývají.

Až skončíte, uživatelské rozhraní by měl vypadat zhruba takto:

[![Snímek obrazovky uživatelského rozhraní pro rychlý start v Unity editoru](media/sdk/qs-csharp-unity-02-ui-inline.png)](media/sdk/qs-csharp-unity-02-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>Přidání vzorového kódu

1. V [okno projektu](https://docs.unity3d.com/Manual/ProjectView.html) (ve výchozím nastavení v levé dolní části), klikněte na tlačítko **vytvořit** tlačítko a pak vyberte  **C# skript**. Název skriptu `HelloWorld`.

1. Upravte skript, že na něj kliknuli dvakrát.

   > [!NOTE]
   > Můžete nakonfigurovat, které editor kódu bude spuštěna v rámci **upravit** > **Předvolby**, najdete v článku [uživatelská příručka pro Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Veškerý kód nahraďte následujícím kódem:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Najít a nahradit řetězec `YourSubscriptionKey` s klíči předplatného hlasové služby.

1. Vyhledejte řetězec `YourServiceRegion` a nahraďte ho [oblastí](regions.md) přidruženou k vašemu předplatnému. Pokud například používáte bezplatnou zkušební verzi, oblast je `westus`.

1. Uložte změny do skriptu.

1. Zpět v Unity editoru skriptu musí být přidán jako součást na jeden z vašich her objektů.

   * Klikněte na **plátna** objektu v okně hierarchie. Otevře nastavení [okně Inspektor](https://docs.unity3d.com/Manual/UsingTheInspector.html) (ve výchozím nastavení na pravé straně).
   * Klikněte na tlačítko **přidat součást** tlačítka v okně Inspektor a pak vyhledejte skript HelloWorld vytvoříme výše a přidejte ji.
   * Všimněte si, že komponenta Hello World má dvě neinicializovaných vlastností **textový výstup** a **tlačítko Start obnovení**, které se shodují s veřejné vlastnosti `HelloWorld` třídy.
     Je nastavit, klikněte na výběr objektů (ikona malý kruh napravo od vlastnost) a zvolte text a tlačítko objekty, které jste vytvořili dříve.

     > [!NOTE]
     > Tlačítko má také vnořeného textu objektu. Ujistěte se, že že není vyberete omylem ho pro textový výstup (nebo přejmenujte jednu textových objektů, aby nedošlo ke zmatkům pomocí pole názvu v okně Inspektor).

## <a name="run-the-application-in-the-unity-editor"></a>Spusťte aplikaci v Unity editoru

* Stisknutím klávesy **Přehrát** tlačítko v panelu nástrojů editoru Unity (pod nabídek).

* Po spuštění aplikace, klikněte na tlačítko a Mluvte anglickou fráze nebo větu do počítače mikrofon. Vaše řeči se přenášejí do služeb řeči a převede na text, který se zobrazí v okně.

  [![Snímek obrazovky spuštěné rychlý start v okně hry Unity](media/sdk/qs-csharp-unity-03-output-inline.png)](media/sdk/qs-csharp-unity-03-output-expanded.png#lightbox)

* Zkontrolujte, [okna konzoly](https://docs.unity3d.com/Manual/Console.html) pro zprávy ladění.

* Až to budete mít rozpoznávání řeči, klikněte na tlačítko **Přehrát** tlačítko v panelu nástrojů editoru Unity a zastavte tak aplikace.

## <a name="additional-options-to-run-this-application"></a>Další možnosti ke spuštění této aplikace

Tato aplikace je také nasadit do systému Android, jako samostatné aplikace Windows nebo aplikaci pro UPW.
Odkazovat na naše [ukázkové úložiště](https://aka.ms/csspeech/samples) ve složce Rychlý start/csharp-unity, který popisuje konfiguraci pro tyto další cíle.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Prozkoumejte C# ukázky na Githubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Další informace najdete v tématech

- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)
