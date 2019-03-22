---
title: Rychlý start Akustika projektu pomocí Unity
titlesuffix: Azure Cognitive Services
description: Použijete ukázkový obsah, Experimentujte s projektu Akustika ovládací prvky návrhu v Unity a nasazení pro Windows Desktop.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 468c5584d21c226d6ffce55ff3981e629d872c56
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317183"
---
# <a name="project-acoustics-unity-quickstart"></a>Rychlý start Unity Akustika projektu
Použití projektu Akustika ukázkový obsah pro Unity a experimentovat s ovládacími prvky návrhu zálohovanou simulace.

Požadavky na software:
* [Unity 2018.2 +](http://unity3d.com) pro Windows
* [Projekt Akustika ukázkového balíčku obsahu](https://www.microsoft.com/download/details.aspx?id=57346)

Co je součástí ukázkového balíčku?
* Unity scény s geometrie, zvukové zdroji a hraní her ovládacích prvků
* Modul plug-in Akustika projektu 
* Dokončené Akustika prostředky scény.

## <a name="import-the-sample-package"></a>Importovat ukázkový balíček
Importujte ukázkový balíček do nového projektu Unity. 
* V Unity, přejděte na **prostředky > Importovat balíček > vlastní balíček...**

    ![Snímek obrazovky s Unity importovat balíček možnosti](media/import-package.png)  

* Zvolte **ProjectAcoustics.unitypackage**

Pokud importujete balíček do existujícího projektu, přečtěte si téma [integrace Unity](unity-integration.md) pro další kroky a poznámky.

## <a name="restart-unity"></a>Restartujte Unity
Která má označení vytvoření část toolkit Akustika vyžaduje verzi skriptovací modul runtime .NET 4.x. Import balíčku se aktualizovat nastavení Unity Playeru. Restartujte Unity pro toto nastavení se projeví.

Toto nastavení vstoupily v platnost otevřením můžete ověřit **nastavení přehrávače**:

![Panel nastavení snímku obrazovky z Unity Playeru](media/player-settings.png)

![Snímek obrazovky s Unity Playeru nastavení panelu s vybrané rozhraní .NET 4.5](media/net45.png)

## <a name="experiment-with-design-controls"></a>Experimentujte s ovládacími prvky návrhu
Otevřete ukázkový scény v **ProjectAcousticsSample** složky a klikněte na tlačítko Přehrát v Unity editoru. Použití W, A S, D a myš uspořádat jinak. Chcete-li porovnat, jak na scéně podle názvu dalo čekat a nemusíte Akustika, stiskněte **R** dokud překrytí text zčervená a uvádí, že tlačítko "Akustika: Zakázáno." Pokud zobrazit klávesové zkratky dalších možností ovládání, stiskněte klávesu **F1**. Ovládací prvky jsou také nedodržíte kliknutím pravým tlačítkem myši a vyberte akci chcete provést, left kliknutím provést akci.

Skript **AcousticsAdjust** je připojen k zdrojů zvuku ve scéně ukázka umožňuje návrh parametry-source. 

![Snímek obrazovky s Unity AcousticsAdjust skriptu](media/acoustics-adjust.png)

Následující zkoumá některé efekty, které je možné vytvořit pomocí zadané ovládacích prvků. Podrobné informace o každý ovládací prvek, najdete v článku [projektu Akustika Unity návrhu kurzu](unreal-workflow.md).

### <a name="modify-distance-based-attenuation"></a>Upravit na základě vzdálenosti zeslabení
Zvuk poskytované DSP **projektu Akustika** modulu plug-in spatializer Unity respektuje integrované do editoru Unity na základě vzdálenosti zeslabení-source. Ovládací prvky pro na základě vzdálenosti zeslabení **zdroje zvuku** komponenta nalezena v **inspektoru** panelu zvuk v oblasti zdrojů **3D nastavení zvuku**:

![Panel možností zeslabení vzdálenost snímek obrazovky s Unity](media/distance-attenuation.png)

Projekt Akustika provádí výpočet v poli "simulace oblasti" zaměřená na umístění přehrávače. Protože Akustika prostředky v ukázkového balíčku byly vloženými s velikostí oblasti simulace 45m okolní hráč, by se měly zvukové zeslabení navrhovat tak, aby 0 přibližně 45 m.

### <a name="modify-occlusion-and-transmission"></a>Upravit uzavření a přenosu
* Pokud **uzavření** multiplikátor je větší než 1 (výchozí hodnota je 1), bude možné exaggerated uzavření. Nastavení je méně než 1 je vhodné projeví složitější.

* Chcete-li povolit přenos přes wall, přesuňte **přenosu (databáze)** posuvník vypnuto jeho nejnižší úroveň. 

### <a name="modify-wetness-for-a-source"></a>Upravit wetness zdroje
* Chcete-li změnit, jak rychle mění wetness vzdálenost, použijte **Percepční Warp vzdálenost**. **Projekt Akustika** výpočetní prostředí wet úrovně v rámci místo z simulace, které hladce lišit podle vzdálenosti a poskytují Percepční vzdálenost pomůcky. Podle zvyšující se vzdáleností související vlhkou úroveň zvětšení warp vzdálenost zvýrazní tohoto efektu. Pokřivení hodnoty menší než 1 provést na základě vzdálenosti reverberation změnit složitější. Také je možné upravit tento efekt podrobně citlivější úpravou **Wetness (databáze)**.

* Prodloužit dobu decay v celém prostoru úpravou **Decay časové měřítko**. Pokud je čas decay 1.5s, simulace výsledek pro pár příslušného naslouchacího procesu zdrojového umístění a **Decay časové měřítko** je nastavena na 2, čas decay použití ke zdroji je 3s.

## <a name="next-steps"></a>Další postup
* Přečtěte si úplné podrobnosti o [vytvořených v Unity projektu Akustika návrh ovládacích prvků](unity-workflow.md)
* Další Seznamte se s koncepty za [návrhu procesu](design-process.md)
* [Vytvoření účtu Azure](create-azure-account.md) předběžné která má označení vytvoření a vytvoření procesů

