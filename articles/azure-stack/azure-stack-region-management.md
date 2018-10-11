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
ms.date: 06/05/2018
ms.author: sethm
ms.reviewer: efemmano
ms.openlocfilehash: 401b81ceb7ab71528a4ad11bc7d8944b4d732933
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078850"
---
# <a name="region-management-in-azure-stack"></a>Správa oblastí ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Azure Stack používá koncept oblastí, které jsou logické entity sestává z hardwarové prostředky, které tvoří infrastruktura Azure stacku. V oblasti správy můžete najít všechny prostředky, které jsou nutné pro úspěšný provoz infrastruktury Azure stacku.

Jedna integrovaná nasazení systému (označované jako *cloudu služby Azure Stack*) tvoří jedné oblasti. Každá Azure Stack Development Kit má jedné oblasti s názvem **místní**. Pokud nasazujete druhý systém Azure Stack integrované nebo nastavit jiná instance sada na samostatné hardwaru, je tento cloud Azure Stack v jiné oblasti.

## <a name="information-available-through-the-region-management-tile"></a>Informace, které jsou k dispozici prostřednictvím dlaždice oblasti správy

Obsahuje sadu možností správy oblasti k dispozici v Azure Stack **Správa oblastí** dlaždici. Tato dlaždice není k dispozici pro operátory Azure stacku na výchozí řídicí panel portálu správce. Prostřednictvím této dlaždice můžete monitorovat a aktualizovat vaši oblast Azure Stack a jeho komponenty, které jsou specifické pro oblast.

 ![Dlaždice oblasti správy](media/azure-stack-manage-region/image1.png)

 Pokud kliknete na oblast v oblasti správy dlaždici, můžete získat následující informace:

  ![Popis podoken v okně správy oblasti](media/azure-stack-manage-region/image2.png)

1. **V nabídce prostředků**. Tady, přístup k oblasti konkrétní infrastrukturu správy a umožňuje zobrazit a spravovat prostředky uživatele, jako jsou virtuální síť a účet úložiště.

2. **Výstrahy**. Toto jsou uvedeny systémová oznámení a poskytuje podrobné informace o každé z těchto upozornění.

3. **Aktualizace**. Zde můžete zobrazit aktuální verzi vaší infrastruktury Azure stacku, dostupné aktualizace a aktualizace historie. Můžete aktualizovat také integrovaného systému.

4. **Poskytovatelé prostředků**. Poskytovatelé prostředků je místem, kde můžete spravovat uživatelské funkce nabízené komponenty potřebné ke spuštění služby Azure Stack. Každý poskytovatel prostředků obsahuje prostředí pro správu. Tyto možnosti mohou zahrnovat upozornění pro konkrétního zprostředkovatele, metrik a jiné funkce Správa specifické pro poskytovatele prostředků.

5. **Infrastrukturu role**. Infrastrukturu role jsou komponenty potřebné ke spuštění služby Azure Stack. Jsou uvedeny pouze infrastrukturu role, které podléhají výstrahy. Pokud vyberete roli, můžete zobrazit výstrahy spojené s rolí a instancí rolí, ve kterém je spuštěna tato role.

## <a name="next-steps"></a>Další postup

- [Monitorování stavu a výstrah ve službě Azure Stack](azure-stack-monitor-health.md)
- [Správa aktualizací ve službě Azure Stack](azure-stack-updates.md)
