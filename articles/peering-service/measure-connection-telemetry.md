---
title: 'Azure peering Service: postup měření telemetrie připojení '
description: V tomto kurzu se dozvíte, jak změřit telemetrii připojení.
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 6bd00b314c94e1edf094599bdb43c70376a18550
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065374"
---
# <a name="tutorial-measure-peering-service-connection-telemetry"></a>Kurz: měření telemetrie připojení ke službě peering Service

 V tomto kurzu se dozvíte, jak změřit telemetrii pro připojení ke službě peering Service.
 
 Telemetrie připojení poskytuje přehledy shromažďované pro připojení mezi umístěním zákazníka a sítí Microsoftu. V tomto článku se dozvíte, jak zobrazit zprávu o latenci pro konkrétní připojení služby Azure peering Service. 

K měření telemetrie připojení ke službě peering Service musíte v Azure Portal zaregistrovat připojení ke službě peering Service. Informace o tom, jak zaregistrovat připojení, najdete v tématu [registrace připojení ke službě peering Service – Azure Portal](azure-portal.md).


## <a name="view-a-latency-report"></a>Zobrazit sestavu latence

Pokud chcete zobrazit sestavu latence pro konkrétní připojení ke službě partnerského vztahu, postupujte podle těchto kroků.

1. V levém podokně vyberte **všechny prostředky** a vyberte připojení služby partnerského vztahu. Pak vyberte **otevřít** pod **předponami**. 

   ![Vyberte připojení služby peering Service.](./media/peering-service-measure/peering-service-measure-menu.png)

2. Zobrazí se stránka s oznámením latence pro všechny předpony přidružené k danému připojení služby partnerského vztahu. 

      ![Stránka sestavy latence](./media/peering-service-measure/peering-service-latency-report.png)

3. Ve výchozím nastavení se sestava aktualizuje na každou hodinu, která se zobrazuje na této stránce. Chcete-li zobrazit sestavu pro různé časové osy, vyberte příslušnou možnost z možnosti **Zobrazit data za poslední**. 

4. Chcete-li zobrazit události pro konkrétní předponu, vyberte název předpony a v levém podokně vyberte **události předpony** . Zobrazí se události, které jsou zachyceny.


   ![Události předpony](./media/peering-service-measure/peering-service-prefix-event.png)

 Zde jsou uvedeny některé možné události, které jsou zachyceny v seznamu **událostí předpony** .

| **Události předpony** | **Typ události**|**Reasoning**|
|-----------|---------|---------|
| PrefixAnnouncementEvent |Informace|Bylo přijato oznámení předpony.|
| PrefixWithdrawalEvent|Upozornění| Byla přijata odnětí předpony. |
| PrefixBackupRouteAnnouncementEvent |Informace|Bylo přijato oznámení o trase zálohování předpony. |
| PrefixBackupRouteWithdrawalEvent|Upozornění|Byla přijata odnětí trasy zálohy předpony. |
| PrefixActivePath |Informace| Aktuální předpona aktivní trasy   |
| PrefixBackupPath | Informace|Aktuální předpona záložního postupu   |
| PrefixOriginAsChangeEvent|Kritické| Přesná předpona byla přijata s odlišným číslem autonomního systému (pro aktivní trasu).| 
| PrefixBackupRouteOriginAsChangeEvent  | Chyba|Předpona přijata s odlišným zdrojem autonomního systému (pro postup zálohování)  |

## <a name="next-steps"></a>Další kroky

- Další informace o připojení ke službě peering Service najdete v tématu [připojení ke službě peering Service](connection.md).
- Další informace o telemetrie připojení ke službě peering Service najdete v tématu [telemetrie připojení ke službě peering Service](connection-telemetry.md).