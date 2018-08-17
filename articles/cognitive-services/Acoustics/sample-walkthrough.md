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
ms.openlocfilehash: b738cc2fc7db6987b8f4ad54a2c53cc9e69989b3
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/17/2018
ms.locfileid: "40181470"
---
# <a name="unity-sample-walkthrough"></a>Ukázkový názorný postup Unity
Jedná se názorného Průvodce ukázkou Akustika projektu. Další informace o jaké Akustika projektu je, přečtěte si [Úvod do projektu Akustika](what-is-acoustics.md). Pomoc při přidání balíčku projektu Akustika do již existující Unity projektu, použijte [příručce Začínáme](getting-started.md).

## <a name="requirements-for-running-the-sample-project"></a>Požadavky na spuštění ukázkového projektu
* Unity 2018.2 + pomocí skriptování verze modulu runtime .NET 4.x
* Windows 64-bit Unity editoru
* Ukázka podporuje Windows desktop, UPW a cílech Android, včetně hlavu zobrazí (HMDs)
* Předplatné Azure Batch potřeba která má označení vytvoření procesu

## <a name="sample-project-setup"></a>Ukázkový projekt instalace
Stáhnout a naimportovat **MicrosoftAcoustics.Sample.unitypackage**. Při importu nastavení včetně projektu **Spatializer** a **skriptování verze modulu Runtime** se aktualizují, aby modul plug-in požadavkům. Po jejím dokončení, zobrazí se vám Chyba v Unity konzole z **AcousticsGeometry.cs** o změně verze modulu Runtime skriptování pro **.NET 4.x ekvivalent**. Změna tohoto nastavení se provádí jako součást importu balíčku naimportovány, ale vyžaduje restartování Unity se projeví. Restartujte Unity.

## <a name="running-the-sample"></a>Spuštění ukázky
Ukázka zahrnuje Scéna ukázka **Assets/AcousticsDemo/ProjectAcousticsDemo.unity**. Tato scéna má jeden spatialized zvuku zdroj přehrávání z datové krychle s plovoucí desetinnou čárkou (s názvem **AudioHolder** v **hierarchie**). Abyste se mohli navigace obecného skriptu, fotoaparátu/kamery hlavní je podřízený objekt CameraHolder. 

![Hierarchické zobrazení](media/SampleHierarchyView.png)

Scény již byla vloženými a soubor s příponou ACE přidružené **MicrosoftAcoustics** prefab v **hierarchie**. 

Naslouchání jak scéně podle názvu dalo čekat po kliknutí na tlačítko Přehrát v Unity editoru. Použití W, A S, D a myš uspořádat jinak. Chcete-li porovnat, jak scéně podle názvu dalo čekat a nemusíte Akustika, klepněte na tlačítko primární kontroler nebo levé tlačítko myši. Budete cyklicky procházet různé zdroje zvuku, klikněte pravým tlačítkem myši nebo na tlačítko Zpět na vašem řadiči.

## <a name="targeting-other-platforms"></a>Cílení na jiných platformách
Ukázka obsahuje nastavení pro spuštění na ploše Windows, UPW, smíšené Reality ve Windows, Android a Oculus Go. Projekt je ve výchozím nastavení nakonfigurován for Windows Desktop. Pro platformu VR, přejděte na nastavení player (**Upravit > Nastavení projektu > Player**), najdete **XR nastavení**a zkontrolujte **podporované virtuální realitu** zaškrtávací políčko.

![Povolit VR](media/VRSupport.png)  

Sluchátka s mikrofonem VR připojí k vašemu počítači. Přejděte na **soubor > Nastavení sestavení**a klikněte na tlačítko **sestavíte a spustíte** nasazení ukázky do vaší sluchátka s mikrofonem VR. Procházet scény pomocí řadiče pohybu pro vaše sluchátka s mikrofonem, případně zkuste použít S W, A, D na klávesnici.    
Chcete-li cílit na zařízení s Androidem a Oculus Go, zvolte Android z **nastavení sestavení** nabídky. Klikněte na tlačítko **přepněte cílový**, pak **sestavíte a spustíte**. Ukázková Scéna to bude nasadit do připojených zařízení s Androidem. Informace o vývoji pro Unity pro Android, naleznete v tématu [dokumentace k Unity](https://docs.unity3d.com/Manual/android-GettingStarted.html).

![Cíl Android](media/TargetAndroid.png)  

## <a name="next-steps"></a>Další postup
* [Vytvoření účtu Azure](create-azure-account.md) pro vlastní vytváří
* Prozkoumejte [návrhu procesu](design-process.md)

