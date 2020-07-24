---
title: Protokoly – Citus (škálování) – Azure Database for PostgreSQL
description: Přístup k protokolům databáze pro Azure Database for PostgreSQL – Citus (škálování)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: 4ca64a7793d229e7910fb122fb33996b1ac0c4fe
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097270"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Protokoly v Azure Database for PostgreSQL – Citus (škálování)

Protokoly PostgreSQL jsou k dispozici na všech uzlech skupiny serverů Citus (). Protokoly můžete dodávat do serveru úložiště nebo do služby Analytics Service. Protokoly je možné použít k identifikaci, odstraňování potíží a opravě chyb konfigurace a k zajištění optimálního výkonu.

## <a name="accessing-logs"></a>Přístup k protokolům

Chcete-li získat přístup k protokolům PostgreSQL pro koordinátor Citus () nebo pracovní uzel, otevřete uzel v Azure Portal:

![seznam uzlů](media/howto-hyperscale-logging/choose-node.png)

Pro vybraný uzel otevřete **nastavení diagnostiky**a klikněte na **+ Přidat nastavení diagnostiky**.

![Tlačítko Přidat nastavení diagnostiky](media/howto-hyperscale-logging/diagnostic-settings.png)

Vyberte název nového nastavení diagnostiky a zaškrtněte políčko **PostgreSQLLogs** .  Vyberte cílové umístění, do kterých mají být protokoly přijímány.

![Zvolit protokoly PostgreSQL](media/howto-hyperscale-logging/diagnostic-create-setting.png)

## <a name="next-steps"></a>Další kroky

- [Začínáme s dotazy Log Analytics](/azure/azure-monitor/log-query/get-started-portal)
- Další informace o [službě Azure Event Center](/azure/event-hubs/event-hubs-about)
