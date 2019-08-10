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
ms.openlocfilehash: 0ea020ca76381a4ae5d6b6e480c94e63f9aa2dab
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933127"
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
Importujte vzorový balíček do nového projektu Unity. 
* V Unity jděte na **assets > Import balíčku > vlastní balíček...**

    ![Snímek obrazovky s možnostmi balíčku pro import Unity](media/import-package.png)  

* Vyberte **ProjectAcoustics. unitypackage**

* Kliknutím na tlačítko **Import** integrujete balíček Unity do svého projektu.  
  
    ![Snímek obrazovky dialogového okna pro Import balíčku Unity](media/import-dialog.png)  

Pokud balíček importujete do existujícího projektu, přečtěte si téma [integrace Unity](unity-integration.md) pro další kroky a poznámky.

>[!NOTE] 
>Po dokončení importu se v protokolu konzoly zobrazí několik chyb.  Pokračujte dalším krokem a restartujte Unity.

## <a name="restart-unity"></a>Restartovat Unity
Zanesli část akustického množství Toolkit vyžaduje verzi modulu runtime skriptování .NET 4. x. Při importu balíčku se aktualizují nastavení přehrávače Unity. Restartujte Unity, aby se toto nastavení projevilo.

Toto nastavení můžete ověřit tak, že otevřete **nastavení přehrávače**:

![Snímek obrazovky s panelem nastavení přehrávače Unity](media/player-settings.png)  

![Snímek obrazovky s panelem nastavení přehrávače Unity s vybraným .NET 4,5](media/net45.png)  

>[!NOTE]
>Snímek obrazovky je pořízen z Unity 2018. x. Novější verze Unity se mohou lišit.

## <a name="open-the-project-acoustics-bake-window"></a>Otevřete okno akustické zanesli projektu.
V nabídce Unity vyberte **okno > akustické** :

![Snímek obrazovky s vybranými možnostmi nabídky okna s akustickými obrazovkami v editoru Unity](media/window-acoustics.png)

Zobrazí se nové plovoucí okno s názvem akustické.  V tomto okně jsou nastaveny vlastnosti akustické simulace.

![Snímek obrazovky editoru Unity s otevřeným oknem akustického množství](media/unity-editor-plugin-window.png)  

## <a name="experiment-with-design-controls"></a>Experiment s ovládacími prvky návrhu
Otevřete ukázkovou scénu ve složce **ProjectAcousticsSample** a klikněte na tlačítko Přehrát v editoru Unity. Pro pohyb použijte W, A, S, D a myš. Chcete-li porovnat způsob, jakým je scéna zvuk, a to bez akustického navýšení, stiskněte tlačítko **R** , dokud se překryvný text neprojeví červeně a říká se Zakázáno. " Pokud zobrazit klávesové zkratky dalších možností ovládání, stiskněte klávesu **F1**. Ovládací prvky se také podporují kliknutím pravým tlačítkem a výběrem akce, která se má provést, a následným kliknutím na provést akci.

Skript **AcousticsAdjust** je připojen ke zdrojům zvuku v ukázkové scéně, která umožňuje parametry návrhu na zdroj. 

![Snímek obrazovky s AcousticsAdjust skriptem Unity](media/acoustics-adjust.png)

Následující seznam popisuje některé efekty, které lze vyrobit s poskytnutými ovládacími prvky. Podrobné informace o jednotlivých ovládacích prvkůch najdete v [kurzu návrhu Unity pro projekt](unity-workflow.md).

### <a name="modify-distance-based-attenuation"></a>Úprava zeslabení na základě vzdálenosti
Zvukový procesor, který je poskytováný modulem plug-in Unity spatializer, dodržuje odkládání na dálku, které jsou součástí editoru Unity. Ovládací prvky pro odkládání na dálku jsou součástí **zdrojové zvukové** komponenty, která se nachází na panelu inspektora zvukových zdrojů, v části **Nastavení 3D zvuku**:

![Snímek obrazovky s panelem možností pro zeslabení vzdálenosti Unity](media/distance-attenuation.png)

Akustické navýšení projektu provádí výpočet v políčku simulace oblasti uprostřed umístění přehrávače. Vzhledem k tomu, že akustické prostředky v ukázkovém balíčku byly vloženými s velikostí oblasti simulace 45m okolním přehrávačem, musí se zpomalení zvuku navrhnout na 0 v přibližně 45 m.

### <a name="modify-occlusion-and-transmission"></a>Upravit překrytí a přenos
* Pokud je násobitel **překrytí** větší než 1 (výchozí hodnota je 1), překrytí bude exaggerated. Nastavení menší než 1 způsobí, že překrytí efekt bude jemnější.

* Pokud chcete povolit přenos přes zeď, přesuňte posuvník **přenos (DB)** od nejnižší úrovně. 

### <a name="modify-wetness-for-a-source"></a>Upravit wetness pro zdroj
* Chcete-li změnit, jak rychle wetness změny pomocí vzdálenosti, použijte **Perceptuální**. **Akustické a projektové** prostředky počítají vlhké úrovně v celém prostoru z simulace, která se škáluje plynule se vzdáleností a poskytují neperceptuální upozornění na dálku. Zvýšením úrovně vlhkého nárůstu vzdálenosti exaggerates tím, že se zvýší úroveň mokrého vztahu na dálku. Rozkládání hodnot menší než 1 zjednodušuje reverberationou změnu na dálku. Tento efekt se dá také upravit v přesnější podrobnostech úpravou **Wetness (DB)** .

* Zvyšte Decay dobu v celém prostoru úpravou **časového měřítka Decay**. Pokud výsledek simulace pro konkrétní dvojici umístění zdrojového naslouchacího procesu je decayý čas 1,5 s a **časové období Decay** je nastavené na hodnotu 2, Decay čas aplikovaný na zdroj je 3 s.

## <a name="next-steps"></a>Další postup
* Přečtěte si úplné podrobnosti o [ovládacích prvcích návrhu v projektech založených na Unity](unity-workflow.md) .
* Další zkoumání konceptů za [procesem návrhu](design-process.md)
* [Vytvoření účtu Azure](create-azure-account.md) pro zkoumání zanesli a zanesli procesů

