---
title: Správa oblastí ve službě Azure Stack | Dokumentace Microsoftu
description: Přehled oblastí správy ve službě Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2018
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 11/27/2018
ms.openlocfilehash: c28fce9c6c9d7b0ddeba54e1ba643817436adb4e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55244959"
---
# <a name="region-management-in-azure-stack"></a>Správa oblastí ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Azure Stack používá koncept *oblastech*, které jsou logické entity sestává z hardwarové prostředky, které tvoří infrastruktura Azure stacku. V oblasti správy můžete najít všechny prostředky, které jsou nutné pro úspěšný provoz infrastruktury Azure stacku.

Jedna integrovaná nasazení systému (označované jako *cloudu služby Azure Stack*) tvoří jedné oblasti. Každá Azure Stack Development Kit má jedné oblasti s názvem **místní**. Pokud nasazujete druhý systém Azure Stack integrované nebo nastavit jiná instance sada na samostatné hardwaru, je tento cloud Azure Stack v jiné oblasti.

## <a name="information-available-through-the-region-management-tile"></a>Informace, které jsou k dispozici prostřednictvím dlaždice oblasti správy

Obsahuje sadu možností správy oblasti k dispozici v Azure Stack **Správa oblastí** dlaždici. Tato dlaždice není k dispozici pro operátory Azure stacku na výchozí řídicí panel portálu správce. Prostřednictvím této dlaždice můžete monitorovat a aktualizovat vaši oblast Azure Stack a jeho komponenty, které jsou specifické pro oblast.

![Dlaždice oblasti správy](media/azure-stack-region-management/image1.png)

Pokud kliknete na oblasti v **Správa oblastí** dlaždici můžete získat následující informace:

[ ![Popis podoken v okně správy oblasti](media/azure-stack-region-management/regionssm.png "okno správy oblasti") ](media/azure-stack-region-management/regions.png#lightbox)

1. **V nabídce prostředků**. Přístup k oblasti konkrétní infrastrukturu správy a zobrazovat a spravovat prostředky uživatele, jako jsou virtuální síť a účet úložiště.

2. **Výstrahy**. Uvádí systémová oznámení a poskytuje podrobné informace o každé z těchto upozornění.

3. **Aktualizace**. Zobrazte aktuální verzi vaší infrastruktury Azure stacku, dostupné aktualizace a aktualizace historie. Můžete aktualizovat také integrovaného systému.

4. **Poskytovatelé prostředků**. Spravujte uživatele funkce nabízené komponenty potřebné ke spuštění služby Azure Stack. Každý poskytovatel prostředků obsahuje prostředí pro správu. Tyto možnosti mohou zahrnovat upozornění pro konkrétního zprostředkovatele, metrik a jiné funkce Správa specifické pro poskytovatele prostředků.

5. **Infrastrukturu role**. Komponenty potřebné ke spuštění služby Azure Stack. Jsou uvedeny pouze infrastrukturu role, které podléhají výstrahy. Pokud vyberete roli, můžete zobrazit výstrahy spojené s rolí a instancí rolí, ve kterém je spuštěna tato role.

6. **Vlastnosti**. Stav registrace a podrobnosti vašeho prostředí v okně správy oblasti. Stav může být **registrované** nebo **Neregistrovaný**. Pokud je zaregistrován, také ukazuje, ID předplatného Azure, který jste použili k registraci služby Azure Stack, spolu s registrace skupiny prostředků a názvem.

## <a name="next-steps"></a>Další postup

- [Monitorování stavu a výstrah ve službě Azure Stack](azure-stack-monitor-health.md)
- [Správa aktualizací ve službě Azure Stack](azure-stack-updates.md)
