---
title: Rychlá odezva Azure DDoS
description: Naučte se zapojit odborníky na DDoS během aktivního útoku na specializovanou podporu.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: yitoh
ms.openlocfilehash: 8de95a56f3da928997a97ff216b13d522c82afca
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814189"
---
# <a name="azure-ddos-rapid-response"></a>Rychlá odezva Azure DDoS

Během aktivního přístupu mají zákazníci Azure DDoS Protection Standard přístup ke týmu DDoS Rapid Response (DRR), který může pomáhat s vyšetřováním útoků během útoku a analýz po útoku.

## <a name="prerequisites"></a>Požadavky

- Než budete moct dokončit kroky v tomto kurzu, musíte nejdřív vytvořit [Plán Standard Protection pro Azure DDoS](manage-ddos-protection.md).

## <a name="when-to-engage-drr"></a>Kdy se zapojit do DRR

DRR byste měli zapojit jenom v těchto případech: 

- Při útoku DDoS zjistíte, že výkon chráněného prostředku je vážně snížený, nebo že prostředek není k dispozici. Přečtěte si krok 2 výše týkající se konfigurace monitorování, aby bylo možné zjistit dostupnost prostředků a problémy s výkonem.
- Myslíte si, že je prostředek pod útokem DDoS, ale služba DDoS Protection neomezuje útok efektivně.
- Plánujete virovou událost, která významně zvýší síťový provoz.
- Pro útoky, které mají zásadní dopad na chod firmy.

## <a name="engage-drr-during-an-active-attack"></a>Zapojit DRR během aktivního útoku

1. Z Azure Portal při vytváření nové žádosti o podporu vyberte **typ problému** jako technický.
2. Jako **DDoS Protection** vyberte **Služba** .
3. V rozevírací nabídce prostředek vyberte prostředek. _Musíte vybrat plán DDoS, který je propojený s virtuální sítí chráněnou nástrojem DDoS Protection standard pro zapojení DRR._

    ![Zvolit prostředek](./media/ddos-rapid-response/choose-resource.png)

4. Na stránce další **problém** vyberte závažnost jako **závažná** a **problémový typ** jako v části útok.

    ![PSeverity a typ problému](./media/ddos-rapid-response/severity-and-problem-type.png)

5. Dokončete další podrobnosti a odešlete žádost o podporu.

DRR se řídí modelem podpory Azure Rapid Response. Další informace o rychlé odezvě najdete v tématu [Podpora oboru a odezvy](https://azure.microsoft.com/en-us/support/plans/response/) .

Pokud se chcete dozvědět víc, přečtěte si dokumentaci ke službě [DDoS Protection Standard](./ddos-protection-overview.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [testovat pomocí simulací](test-through-simulations.md).
- Přečtěte si, jak [Zobrazit a nakonfigurovat telemetrii DDoS Protection](telemetry.md).
- Přečtěte si [, jak zobrazit a nakonfigurovat protokolování diagnostiky DDoS](diagnostic-logging.md).