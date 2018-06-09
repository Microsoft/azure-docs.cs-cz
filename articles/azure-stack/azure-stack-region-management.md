---
title: Oblast správy v zásobníku Azure | Microsoft Docs
description: Přehled správy oblast v zásobníku Azure.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: brenduns
ms.reviewer: efemmano
ms.openlocfilehash: 0286ed9c7b3fe320b936d33fe3beaddccd6ac0fa
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247530"
---
# <a name="region-management-in-azure-stack"></a>Oblast správy v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Azure zásobníku používá koncept oblastí, které se skládá z hardwarové prostředky, které tvoří infrastruktury Azure zásobníku logických entit. V oblasti správy můžete najít všechny prostředky, které jsou nutné pro úspěšný provoz infrastruktury Azure zásobníku.

Jedna integrovaná nasazení systému (označované jako *zásobník Azure cloud*) tvoří jedné oblasti. Každý Azure zásobníku Development Kit má jedné oblasti, s názvem **místní**. Pokud nasadíte druhý zásobník Azure integrovaný systém nebo jinou instanci development kit na samostatných nastavíte, je tento cloud Azure zásobníku v jiné oblasti.

## <a name="information-available-through-the-region-management-tile"></a>Informace, které jsou k dispozici prostřednictvím dlaždici správy oblast

Obsahuje sadu oblast možnosti správy, které jsou dostupné v Azure zásobníku **oblast správy** dlaždici. Tato dlaždice není k dispozici pro operátor zásobník Azure na výchozí řídicí panel portálu správce. Prostřednictvím této dlaždice můžete monitorovat a aktualizovat vaši oblast Azure zásobníku a jeho komponenty, které jsou specifické pro oblast.

 ![Na dlaždici správy oblast](media/azure-stack-manage-region/image1.png)

 Pokud kliknete na oblast na dlaždici správy oblast, se můžete dostat následující informace:

  ![Popis podokna v okně správy oblast](media/azure-stack-manage-region/image2.png)

1. **V nabídce prostředků**. Zde můžete přístup k oblasti správy konkrétní infrastruktury a zobrazit a spravovat prostředky uživatele, například účty úložiště a virtuální sítě.

2. **Výstrahy**. Toto jsou uvedeny systémové výstrahy a poskytuje podrobné informace o každé z těchto výstrah.

3. **Aktualizace**. Sem můžete zobrazit aktuální verzi vaší infrastruktury Azure zásobníku, dostupné aktualizace a aktualizace historie. Můžete také aktualizovat integrovaný systém.

4. **Zprostředkovatelé prostředků**. Zprostředkovatelé prostředků je místo, ke správě uživatele funkce nabízené komponenty potřebné ke spuštění zásobník Azure. Každý poskytovatel prostředků se dodává s správu prostředí. Toto prostředí může zahrnovat výstrahy pro konkrétního poskytovatele, metriky a dalším funkcím správy specifické pro poskytovatele prostředků.

5. **Role infrastruktury**. Role infrastruktury jsou komponenty potřebné ke spuštění zásobník Azure. Jsou uvedeny pouze role infrastruktury, které sestavy výstrahy. Pokud vyberete roli, můžete zobrazit výstrahy spojené se role a instance rolí, kde je spuštěna tato role.

## <a name="next-steps"></a>Další postup

- [Sledování stavu a výstrahy v Azure zásobníku](azure-stack-monitor-health.md)
- [Správa aktualizací v Azure zásobníku](azure-stack-updates.md)
