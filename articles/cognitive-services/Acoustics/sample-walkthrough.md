---
title: Projekt Akustika ukázkový názorný postup – Cognitive Services
description: Tento návod popisuje ukázková Scéna Unity pro Akustika projektu, včetně nasazení na ploše a VR.
services: cognitive-services
author: kegodin
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: eaf7ff9f7f791fd6d04e6b76d256b4987c50cd13
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434088"
---
# <a name="unity-sample-walkthrough"></a>Ukázkový názorný postup Unity
Jedná se názorného Průvodce ukázkou Akustika projektu. Další informace o jaké Akustika projektu je, přečtěte si [Úvod do projektu Akustika](what-is-acoustics.md). Pomoc při přidání balíčku projektu Akustika do již existující Unity projektu, použijte [příručce Začínáme](getting-started.md).

## <a name="requirements-for-running-the-sample-project"></a>Požadavky na spuštění ukázkového projektu
* Unity 2018.2 + pomocí skriptování verze modulu runtime .NET 4.x
* 64bitová verze editoru Unity pro Windows
* Ukázka podporuje Windows desktop, UPW a cílech Android, včetně hlavu zobrazí (HMDs)
* Předplatné Azure Batch potřeba která má označení vytvoření procesu

## <a name="sample-project-setup"></a>Ukázkový projekt instalace
Stáhnout a naimportovat **MicrosoftAcoustics.Sample.unitypackage**. Při importu nastavení včetně projektu **Spatializer** a **skriptování verze modulu Runtime** se aktualizují, aby modul plug-in požadavkům. Po jejím dokončení, zobrazí se vám Chyba v Unity konzole z **AcousticsGeometry.cs** o změně verze modulu Runtime skriptování pro **.NET 4.x ekvivalent**. Změna tohoto nastavení se provádí jako součást importu balíčku naimportovány, ale vyžaduje restartování Unity se projeví. Restartujte Unity.

## <a name="running-the-sample"></a>Spuštění ukázky
Ukázka zahrnuje Scéna ukázka **Assets/AcousticsDemo/ProjectAcousticsDemo.unity**. Tato scéna má tři zvukové zdroje. Ve výchozím nastavení přehrávání zvuku jenom jeden zdroj a další dvě jako jsou pozastavena. Tyto jsou umístěné v části **zdroje zvuku** v **hierarchie**. Abyste se mohli navigace obecného skriptu, fotoaparátu/kamery hlavní je podřízený objekt CameraHolder. 

![Hierarchické zobrazení](media/SampleHierarchyView.png)

Scény již byla vloženými a soubor s příponou ACE přidružené **MicrosoftAcoustics** prefab v **hierarchie**. 

Naslouchání jak scéně podle názvu dalo čekat po kliknutí na tlačítko Přehrát v Unity editoru. Ve stolním počítači, použijte W, A, S, D a myší uspořádat jinak. Chcete-li porovnat, jak na scéně podle názvu dalo čekat a nemusíte Akustika, stiskněte **R** dokud překrytí text zčervená a uvádí, že tlačítko "Akustika: zakázáno." Chcete-li zobrazit klávesové zkratky pro větší počet kontrolních mechanismů, stiskněte **F1**. Všechny ovládací prvky jsou také nedodržíte kliknutím pravým tlačítkem a vyberte akci chcete provést, pak levým na zbývající k provedení akce.

## <a name="targeting-other-platforms"></a>Cílení na jiných platformách
Ukázka obsahuje nastavení pro spuštění na ploše Windows, UPW, smíšené Reality ve Windows, Android a Oculus Go. Projekt je ve výchozím nastavení nakonfigurován for Windows Desktop. Pro platformu VR, přejděte na nastavení player (**Upravit > Nastavení projektu > Player**), najdete **XR nastavení**a zkontrolujte **podporované virtuální realitu** zaškrtávací políčko.

![Povolit VR](media/VRSupport.png)  

Sluchátka s mikrofonem VR připojí k vašemu počítači. Přejděte na **soubor > Nastavení sestavení**a klikněte na tlačítko **sestavíte a spustíte** nasazení ukázky do vaší sluchátka s mikrofonem VR. Procházet scény pomocí řadiče pohybu pro vaše sluchátka s mikrofonem, případně zkuste použít S W, A, D na klávesnici.    
Chcete-li cílit na zařízení s Androidem a Oculus Go, zvolte Android z **nastavení sestavení** nabídky. Klikněte na tlačítko **přepněte cílový**, pak **sestavíte a spustíte**. Ukázková Scéna to bude nasadit do připojených zařízení s Androidem. Informace o vývoji pro Unity pro Android najdete v tématu [dokumentace k Unity](https://docs.unity3d.com/Manual/android-GettingStarted.html).

![Cíl Android](media/TargetAndroid.png)  

## <a name="next-steps"></a>Další postup
* [Vytvoření účtu Azure](create-azure-account.md) pro vlastní vytváří
* Prozkoumejte [návrhu procesu](design-process.md)

