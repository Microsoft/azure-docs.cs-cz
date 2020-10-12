---
title: Protokoly-Azure Database for PostgreSQL-flexibilní Server
description: Popisuje konfiguraci protokolování, úložiště a analýzu v Azure Database for PostgreSQL flexibilním serveru.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 852bce26e348c817b609d5ff837641741afe4461
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90936601"
---
# <a name="logs-in-azure-database-for-postgresql---flexible-server"></a>Protokoly v Azure Database for PostgreSQL – flexibilní Server

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.

Azure Database for PostgreSQL umožňuje konfiguraci a přístup ke standardním protokolům Postgres. Protokoly je možné použít k identifikaci, odstraňování potíží a opravě chyb konfigurace a k zajištění optimálního výkonu. Protokolovací informace, které můžete konfigurovat, a přístup zahrnují chyby, informace o dotazech, autovaku záznamů, připojení a kontrolní body. (Přístup k protokolům transakcí není k dispozici).

Protokolování auditu je zpřístupněno prostřednictvím rozšíření Postgres `pgaudit` . Další informace najdete v článku věnovaném [koncepcím auditu](concepts-audit.md) .

## <a name="configure-logging"></a>Konfigurovat protokolování

Na serveru můžete nakonfigurovat standardní protokolování Postgres pomocí parametrů protokolovacího serveru. Další informace o parametrech protokolu Postgres najdete v části dokumentace k [protokolování](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) a [co do protokolu](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT) v dokumentaci k Postgres. Většina, ale ne všechny parametry protokolování Postgres jsou k dispozici pro konfiguraci v Azure Database for PostgreSQL.

Informace o tom, jak nakonfigurovat parametry v Azure Database for PostgreSQL, najdete v [dokumentaci k portálu](howto-configure-server-parameters-using-portal.md) nebo v dokumentaci k rozhraní příkazového [řádku](howto-configure-server-parameters-using-cli.md).

> [!NOTE]
> Konfigurace velkého objemu protokolů, například protokolování příkazů, může výrazně zvýšit nároky na výkon. 

## <a name="accessing-logs"></a>Přístup k protokolům

Azure Database for PostgreSQL je integrován s nastavením diagnostiky Azure Monitor. Nastavení diagnostiky umožňuje odeslat protokoly Postgres ve formátu JSON, abyste Azure Monitor protokoly pro analýzy a upozorňování, Event Hubs pro streamování a Azure Storage k archivaci. 

### <a name="log-format"></a>Formát protokolu

V následující tabulce jsou popsána pole pro typ **PostgreSQLLogs** . V závislosti na zvoleném výstupním koncovém bodu se můžou pole zahrnutá a pořadí, ve kterém se zobrazují, lišit. 

|**Pole** | **Popis** |
|---|---|
| TenantId | ID tenanta |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Časové razítko, kdy se protokol zaznamenal v UTC |
| Typ | Typ protokolu Stál `AzureDiagnostics` |
| SubscriptionId | Identifikátor GUID předplatného, ke kterému server patří |
| ResourceGroup | Název skupiny prostředků, do které server patří |
| ResourceProvider | Název poskytovatele prostředků Stál `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | Identifikátor URI prostředku |
| Prostředek | Název serveru |
| Kategorie | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | Úroveň protokolování, příklad: protokol, chyba, upozornění |
| Zpráva | Zpráva primárního protokolu | 
| Doména | Verze serveru, například: Postgres-10 |
| Podrobnosti | Sekundární zpráva protokolu (Pokud je k dispozici) |
| ColumnName | Název sloupce (Pokud je k dispozici) |
| SchemaName | Název schématu (Pokud je k dispozici) |
| Typ DataType | Název datového typu (Pokud je k dispozici) |
| LogicalServerName | Název serveru | 
| _ResourceId | Identifikátor URI prostředku |
| Předpona | Předpona řádku protokolu |


## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o tom, jak [nakonfigurovat protokoly a přistupovat k nim](howto-configure-and-access-logs.md).
- Přečtěte si další informace o [cenách Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).
- Další informace o [protokolech auditu](concepts-audit.md)
