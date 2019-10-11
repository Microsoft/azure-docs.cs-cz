---
title: Rychlý Start akustického projektu pomocí Unity
titlesuffix: Azure Cognitive Services
description: Pomocí ukázkového obsahu můžete experimentovat s ovládacími prvky návrhu pro návrh projektů v Unity a nasazovat je do desktopu Windows.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fabdd221ef99414eae0156babbd76dedb1f0745d
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243026"
---
# <a name="project-acoustics-unity-quickstart"></a>Projekty s akustickým rychlým startem Unity
Použijte k experimentování s ovládacími prvky návrhu pro simulaci s vydanými simulací ukázkový obsah projektového obsahu pro Unity.

Požadavky na software:
* [Unity 2018.2 +](https://unity3d.com) pro Windows
* [Ukázkový balíček obsahu projekt akustického obsahu](https://www.microsoft.com/download/details.aspx?id=57346)

Co obsahuje ukázkový balíček?
* Scéna Unity s geometrií, zdroji zvuku a ovládacími prvky pro hraní her
* Modul plug-in akustického projektu
* Vloženými akustické prostředky pro scénu

## <a name="import-the-sample-package"></a>Import ukázkového balíčku
Importujte ukázkový balíček do nového projektu Unity.
1. V Unity jděte do **assets** > **Import balíčku** > **vlastní balíček**.

    ![Možnosti balíčku importu Unity](media/import-package.png)  

1. Vyberte **ProjectAcoustics. unitypackage**.

1. Vyberte tlačítko **importovat** a integrujte balíček Unity do svého projektu.  
  
    ![Dialogové okno pro Import balíčku Unity](media/import-dialog.png)  

Pokud chcete balíček importovat do existujícího projektu, přečtěte si téma [integrace Unity](unity-integration.md) pro další kroky a poznámky.

>[!NOTE]
>Po dokončení importu se v protokolu konzoly zobrazí několik chybových zpráv. Pokračujte dalším krokem a restartujte Unity.

## <a name="restart-unity"></a>Restartovat Unity
Zanesli část akustického navýšení sady nástrojů vyžaduje rozhraní .NET 4. verze modulu runtime skriptování *x* Import balíčku aktualizuje nastavení přehrávače Unity. Restartujte Unity, aby se toto nastavení projevilo. Pokud chcete ověřit, že nastavení vstoupilo v platnost, otevřete nastavení **přehrávače** :

![Nabídka nastavení projektu Unity](media/player-settings.png)  

![Vybraný panel nastavení přehrávače Unity s rozhraním .NET 4. x](media/net45.png)  

>[!NOTE]
>Tento snímek obrazovky byl získán z Unity 2018. *x*. Bitová kopie se může v novějších verzích Unity lišit.

## <a name="open-the-project-acoustics-bake-window"></a>Otevřete okno akustické zanesli projektu.
V části Unity vyberte v nabídce **okna** možnost **akustické** .

![Editor Unity s možností zvukové možnosti, která se v nabídce okna zvýrazní](media/window-acoustics.png)

Otevře se plovoucí okno **akustického** navýšení. V tomto okně můžete nastavit vlastnosti akustické simulace.

![Editor Unity s otevřeným oknem akustického navýšení](media/unity-editor-plugin-window.png)  

## <a name="experiment-with-the-design-controls"></a>Experimentování s ovládacími prvky návrhu
Otevřete ukázkovou scénu ve složce *ProjectAcousticsSample* a v editoru Unity vyberte tlačítko Přehrát. Pomocí kláves W, A, S a D a myši můžete pohybovat. Chcete-li porovnat způsob, jakým se scéna nahraje a bez akustického množství, vyberte klíč R, dokud se překryvný text nevypne červeně a zobrazí "akustické: zakázané". Chcete-li zobrazit klávesové zkratky pro další ovládací prvky, vyberte F1. Můžete také kliknout pravým tlačítkem a vybrat akci a potom kliknutím levým tlačítkem akci provést.

Skript *AcousticsAdjust* je připojen ke zdrojům zvuku v ukázce scény. Povoluje parametry návrhu na zdroj.

![Skript Unity AcousticsAdjust](media/acoustics-adjust.png)

Následující části Prozkoumejte některé efekty, které můžete vytvořit pomocí dostupných ovládacích prvků. Podrobné informace o jednotlivých ovládacích prvkůch najdete v [kurzu návrhu Unity pro projekt](unity-workflow.md).

### <a name="modify-distance-based-attenuation"></a>Úprava zeslabení na základě vzdálenosti
Zpracování zvukového digitálního signálu v **projektu** : modul plug-in Unity spatializer má za následek odkládání na dálku, které je integrováno do editoru Unity. Ovládací prvky pro odkládání na dálku jsou součástí **zdrojové komponenty zvuku** , která je na panelu **inspektora** zdroje zvuku v **Nastavení 3D zvuku**:

![Panel možností pro zeslabení vzdálenosti Unity](media/distance-attenuation.png)

V poli simulace oblasti, která se nachází na střed umístění přehrávače, se vypočítají akustické projekty. Akustické prostředky v ukázkovém balíčku se vloženými na velikost oblasti simulace 45 měřičů kolem přehrávače. Proto by se mělo zeslabení zvukového zpomalit na hodnotu 0 v přibližně 45 m.

### <a name="modify-occlusion-and-transmission"></a>Upravit překrytí a přenos
* Pokud je násobitel **překrytí** větší než 1 (výchozí hodnota je 1), překrytí je exaggerated. Chcete-li zvýšit jemný efekt překrytí, nastavte jej na hodnotu menší než 1.

* Pokud chcete povolit přenos přes zeď, přesuňte posuvník **přenos (DB)** směrem od nejnižšího nastavení.

### <a name="modify-wetness-for-a-source"></a>Upravit wetness pro zdroj
* Pokud chcete změnit, jak rychle wetness změny pomocí vzdálenosti, použijte **Perceptuální na dálkovou osnovu**. Díky simulaci budou akustické množství projektů počítat mokré úrovně, které poskytují Perceptuální a hladce se škálují pomocí vzdálenosti. Zvýšením úrovně vlhkého nárůstu vzdálenosti exaggerates tím, že se zvýší úroveň mokrého vztahu na dálku. Rozkládání hodnot menší než 1 vede ke změně reverberation na dálku.

   Pro zajištění jemnějších úprav tohoto efektu změňte nastavení **Wetness (DB)** .

* Pokud chcete zvýšit Decay dobu v celém prostoru, upravte **časový rozsah Decay**. Pokud výsledek simulace pro konkrétní dvojici umístění zdrojového naslouchacího procesu je Decay čas 1,5 sekund a **časové měřítko Decay** je nastavené na 2, decayý čas, který se použije na zdroj, je 3 sekundy.

## <a name="next-steps"></a>Další kroky
* Přečtěte si podrobnosti o [ovládacích prvcích návrhu v projektech založených na Unity](unity-workflow.md).
* Dále si Prozkoumejte koncepty [procesu návrhu](design-process.md).
* [Vytvořte účet Azure](create-azure-account.md) , který vám umožní prozkoumat procesy zanesli a zanesli.
