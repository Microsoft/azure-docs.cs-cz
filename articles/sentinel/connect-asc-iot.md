---
title: Připojit Azure Defender pro IoT do Azure Sentinel | Microsoft Docs
description: Naučte se, jak propojit Azure Defender (dřív Azure Security Center) pro data IoT do Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: 67bc104434dc0db30f5973bec0979afb7480fe4c
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621375"
---
# <a name="connect-your-data-from-azure-defender-formerly-azure-security-center-for-iot-to-azure-sentinel"></a>Připojení dat z Azure Defenderu (dříve Azure Security Center) pro IoT do Azure Sentinel 

Použijte Defender pro IoT Connector ke streamování všech vašich Defenderů pro události IoT do Azure Sentinel. 

Tato integrace umožňuje organizacím rychle detekovat útoky s více fázemi, které jsou často mezi sebou a hranicemi. Kromě toho Defender pro integraci IoT s funkcemi orchestrace, automatizace a odezva zabezpečení Azure Sentinel umožňuje automatizovanou reakci a prevenci pomocí integrované playbooky optimalizované pro. 
## <a name="prerequisites"></a>Požadavky

- Oprávnění **ke čtení** a **zápisu** v pracovním prostoru, na kterém je nasazená Azure Sentinel
- V příslušném IoT Hub musí být **povolený** **Defender pro IoT** .
- Musíte mít oprávnění **přispěvatele** u **předplatného** , ke kterému se chcete připojit.

## <a name="connect-to-defender-for-iot"></a>Připojení k programu Defender pro IoT

1. V Azure Sentinel vyberte **datové konektory** a pak v galerii vyberte **Defender for IoT** (Azure Security Center for IoT).

1. V dolní části pravého podokna klikněte na tlačítko **otevřít stránku konektoru**. 

1. Klikněte na **připojit** a vedle každého předplatného IoT Hub, jejichž výstrahy a výstrahy zařízení chcete streamovat do Azure Sentinel. 
    - Pokud není povolený Defender pro IoT aspoň na jednom IoT Hub v rámci předplatného, zobrazí se chybová zpráva. Pokud chcete chybu odebrat, povolte v rámci IoT Hub Defender pro IoT.

1. Můžete rozhodnout, jestli chcete, aby upozornění z programu Defender pro IoT automaticky generovala incidenty v Azure Sentinel. V části **vytvořit incidenty** vyberte **Povolit** , pokud chcete výchozímu pravidlu analýzy povolit automatické vytváření incidentů z generovaných výstrah. Toto pravidlo se dá změnit nebo upravit v části **Analýza**  >  **aktivní pravidla**.

> [!NOTE]
> Může trvat 10 sekund nebo déle, než se seznam **předplatných** aktualizuje po provedení změn připojení. 

## <a name="log-analytics-alert-view"></a>Zobrazení výstrah Log Analytics

Chcete-li použít příslušné schéma v Log Analytics k zobrazení výstrah programu Defender pro IoT:

1. Otevřete **protokoly**  >  **SecurityInsights**  >  **SecurityAlert**, nebo vyhledejte **SecurityAlert**. 

2. Filtr pro zobrazení upozornění vygenerovaných v rámci IoT pomocí následujícího filtru KQL:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>Poznámky ke službě

Po připojení **předplatného** jsou data centra dostupná v Azure Sentinel přibližně 15 minut později.


## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit Defender pro IoT k Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.
