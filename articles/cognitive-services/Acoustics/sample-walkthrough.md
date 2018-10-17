---
title: 'Příklad: Project Acoustics'
titlesuffix: Azure Cognitive Services
description: Tento názorný postup popisuje ukázkovou scénu Unity pro Project Acoustics, včetně nasazení na desktopu a platformě VR.
services: cognitive-services
author: kegodin
manager: cgronlun
ms.service: cognitive-services
ms.component: acoustics
ms.topic: sample
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: f5ea565e68579dfad601d1037daeb4113e3daa43
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901154"
---
# <a name="unity-sample-walkthrough"></a>Unity – ukázkový názorný postup
Toto je názorný postup ukázkou z Project Acoustics. Další informace o tom co Project Acoustics je, najdete v části [Úvod do Project Acoustics](what-is-acoustics.md). Pokud potřebujete pomoc s přidáním balíčku Project Acoustics do již existujícího projektu Unity, nahlédněte do [příručky Začínáme](getting-started.md).

## <a name="requirements-for-running-the-sample-project"></a>Požadavky na spuštění ukázkového projektu
* Unity 2018.2+, s použitím verze .NET 4.x skriptovacího modulu runtime
* 64bitová verze editoru Unity pro Windows
* Ukázka podporuje počítač s Windows, UPW a cíle Android, včetně displeje připevněného k hlavě (HMD)
* Předplatné Azure Batch, které proces vypalování požaduje

## <a name="sample-project-setup"></a>Nastavení ukázkového projektu
Stáhněte a naimportujte **MicrosoftAcoustics.Sample.unitypackage**. Při importu se aktualizují nastavení projektu včetně **Spatializéru** a **verze skriptovacího modulu runtime**, aby vyhověly požadavkům pluginu. Když je toto hotové, zobrazí se v konzole projektu Unity chyba z **AcousticsGeometry.cs**, která se týká změny verze skriptovacího modulu runtime na **ekvivalent .NET 4.x**. Tato změna nastavení se provádí jako součást importu balíčku, ale vyžaduje restartování projektu Unity, aby se projevila. Restartujte Unity.

## <a name="running-the-sample"></a>Spuštění ukázky
Ukázka zahrnuje ukázkovou scénu **Assets/AcousticsDemo/ProjectAcousticsDemo.unity**. Tato scéna má tři zvukové zdroje. Ve výchozím nastavení se přehrává jenom jeden zdroj zvuku a další dva jsou pozastavené. Jsou umístěné v možnosti **Hierarchie** pod položkou **Zdroje zvuku**. Hlavní kamera je podřízeným prvkem objektu CameraHolder, aby vám usnadnila vytvoření obecného skriptu pro navigaci. 

![Zobrazení hierarchie](media/SampleHierarchyView.png)

Scény již byla vypálena a má soubor ACE přidružený k přednastavené položce **MicrosoftAcoustics** v **Hierarchii**. 

Poslechněte si, jak scéna zní. Stačí kliknout na tlačítko přehrávání v editoru Unity. Na počítači použijte k pohybu klávesy W, A, S a D a myš. Pokud chcete zvuk scény porovnat s akustikou a bez ní, stiskněte tlačítko **R**, dokud překryvný text nezčervená a neobjeví se „Akustika: Zakázána“. Pokud zobrazit klávesové zkratky dalších možností ovládání, stiskněte klávesu **F1**. Všechny ovládací prvky jsou také použitelné po kliknutí pravého tlačítka a výběru požadované akce. Poté stačí kliknout levým tlačítkem a akci provést.

## <a name="targeting-other-platforms"></a>Cílení na jiné platformy
Ukázka obsahuje nastavení pro spuštění na počítači s Windows, UPW, Windows Mixed Reality, Androidu a Oculus Go. Projekt je ve výchozím nastavení nakonfigurován pro počítač s Windows. Pokud chcete cílit na platformu VR, přejděte na nastavení přehrávače (**Upravit > Nastavení projektu > Přehrávač**), najděte **Nastavení XR**a zaškrtněte políčko **Virtuální realita podporována**.

![Povolení VR](media/VRSupport.png)  

Připojte náhlavní soupravu VR k počítači. Přejděte na **Soubor > Nastavení sestavení**a klikněte na **Sestavit a spustit**. Tím ukázku nasadíte do náhlavní soupravy VR. Scénu můžete procházet pomocí ovladačů pohybu na náhlavní soupravě, popřípadě zkuste klávesy W,A,S a D na klávesnici.    
Pokud chcete cílit na zařízení s Androidem a Oculus Go, zvolte v nabídce **Nastavení sestavení** systém Android. Klikněte na **Přepnout cíl** a pak na **Sestavit a spustit**. Tím ukázkovou scénu nasadíte do připojeného zařízení s Androidem. Informace o vývoji pro Unity pro Android najdete v tématu [Dokumentace k Unity](https://docs.unity3d.com/Manual/android-GettingStarted.html).

![Cílení na Android](media/TargetAndroid.png)  

## <a name="next-steps"></a>Další kroky
* [Vytvoření účtu Azure](create-azure-account.md) pro vlastní vypalování
* Prozkoumejte [proces návrhu](design-process.md)

