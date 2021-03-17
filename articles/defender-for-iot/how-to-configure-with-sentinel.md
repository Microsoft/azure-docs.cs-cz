---
title: Konfigurace Azure Sentinel pro Defender pro IoT
description: Vysvětluje, jak nakonfigurovat Azure Sentinel pro příjem dat z vašeho programu Defender pro řešení IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/28/2020
ms.author: shhazam
ms.openlocfilehash: 2d82aaadf158e45cb8faaeee0b9b4e0fc80a3420
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247331"
---
# <a name="connect-your-data-from-defender-for-iot-to-azure-sentinel"></a>Připojení dat z programu Defender pro IoT do Azure Sentinel 

Použijte Defender pro IoT Connector ke streamování všech vašich Defenderů pro události IoT do Azure Sentinel. 

Tato integrace umožňuje organizacím rychle detekovat útoky s více fázemi, které jsou často mezi sebou a hranicemi. Kromě toho Defender pro integraci IoT s funkcemi orchestrace, automatizace a odezva zabezpečení Azure Sentinel umožňuje automatizovanou reakci a prevenci pomocí integrované playbooky optimalizované pro. 

## <a name="prerequisites"></a>Předpoklady

- Oprávnění **ke čtení** a **zápisu** v pracovním prostoru, na kterém je nasazená Azure Sentinel
- V příslušném IoT Hub musí být **povolený** **Defender pro IoT** .
- Musíte mít oprávnění **přispěvatele** u **předplatného** , ke kterému se chcete připojit.

## <a name="connect-to-defender-for-iot"></a>Připojení k programu Defender pro IoT

1. V Azure Sentinel vyberte **datové konektory** a pak v galerii vyberte **Defender pro IoT** (Azure Security Center pro IoT).

1. V dolní části pravého podokna klikněte na tlačítko **otevřít stránku konektoru**.

1. Klikněte na **připojit** a vedle každého předplatného IoT Hub, jejichž výstrahy a výstrahy zařízení chcete streamovat do Azure Sentinel.
    - Pokud není povolený Defender pro IoT aspoň na jednom IoT Hub v rámci předplatného, zobrazí se chybová zpráva. Pokud chcete chybu odebrat, povolte v rámci IoT Hub Defender pro IoT.

1. Můžete rozhodnout, jestli chcete, aby upozornění z programu Defender pro IoT automaticky generovala incidenty v Azure Sentinel. V části **vytvořit incidenty** vyberte **Povolit** , pokud chcete výchozímu pravidlu analýzy povolit automatické vytváření incidentů z generovaných výstrah. Toto pravidlo se dá změnit nebo upravit v části **Analýza**  >  **aktivní pravidla**.

> [!NOTE]
> Může trvat 10 sekund nebo déle, než se seznam **předplatných** aktualizuje po provedení změn připojení. 

## <a name="log-analytics-alert-view"></a>Zobrazení výstrah Log Analytics

Chcete-li použít příslušné schéma v Log Analytics k zobrazení výstrah programu Defender pro IoT:

1. Otevřete **protokoly**  >  **SecurityInsights**  >  **SecurityAlert**, nebo vyhledejte **SecurityAlert**.

1. Filtr pro zobrazení upozornění vygenerovaných v rámci IoT pomocí následujícího filtru KQL:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
```

### <a name="service-notes"></a>Poznámky ke službě

Po připojení **předplatného** jsou data centra dostupná v Azure Sentinel přibližně 15 minut později.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit Defender pro IoT k Azure Sentinel. Další informace o detekci hrozeb a přístupu k datům zabezpečení najdete v následujících článcích:

- Naučte se, jak pomocí funkce Azure Sentinel [získat přehled o vašich datech a potenciálních hrozbách](../sentinel/quickstart-get-visibility.md).
- Informace o [přístupu k datům zabezpečení IoT](how-to-security-data-access.md)