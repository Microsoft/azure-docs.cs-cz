---
title: Typy připojení spravované instance
description: Informace o typech připojení spravovaných instancí
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.openlocfilehash: 46223d1701b930d93de7c49c1e216a41045dda16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73819462"
---
# <a name="azure-sql-database-managed-instance-connection-types"></a>Typy připojení spravovaných instancí Azure SQL Database

Tento článek vysvětluje, jak se klienti připojují ke spravované instanci Azure SQL Database v závislosti na typu připojení. Ukázky skriptů pro změnu typů připojení jsou uvedeny níže spolu s aspekty souvisejícími se změnou výchozího nastavení připojení.

## <a name="connection-types"></a>Typy připojení

Spravovaná instance Azure SQL Database podporuje následující dva typy připojení:

- **Přesměrování (doporučeno):** Klienti navázat připojení přímo k uzlu hostování databáze. Chcete-li povolit připojení pomocí přesměrování, je nutné otevřít brány firewall a skupiny zabezpečení sítě (NSG), aby byl povolen přístup na portech 1433 a 11000-11999. Pakety přejdou přímo do databáze, a proto existují vylepšení latence a výkonu propustnosti pomocí přesměrování přes proxy server.
- **Proxy server (výchozí):** V tomto režimu všechna připojení používají součást brány proxy. Chcete-li povolit připojení, je třeba otevřít pouze port 1433 pro privátní sítě a port 3342 pro veřejné připojení. Výběr tohoto režimu může mít za následek vyšší latenci a nižší propustnost, v závislosti na povaze úlohy. Důrazně doporučujeme zásady přesměrování připojení přes zásady připojení proxy pro nejnižší latenci a nejvyšší propustnost.

## <a name="redirect-connection-type"></a>Typ přesměrování připojení

Typ připojení přesměrování znamená, že po vytvoření relace TCP pro modul SQL získá relace klienta cílovou virtuální IP adresu uzlu virtuálního clusteru z nástroje pro vyrovnávání zatížení. Následné pakety toku přímo do uzlu virtuálního clusteru, obcházet bránu. Následující diagram znázorňuje tento tok provozu.

![redirect.png](media/sql-database-managed-instance-connection-types/redirect.png)

> [!IMPORTANT]
> Typ připojení přesměrování aktuálně funguje pouze pro soukromý koncový bod. Bez ohledu na nastavení typu připojení by připojení přicházející přes veřejný koncový bod byla prostřednictvím proxy serveru.

## <a name="proxy-connection-type"></a>Typ připojení proxy

Typ připojení proxy znamená, že relace TCP je vytvořena pomocí brány a všechny následující pakety ji procházejí. Následující diagram znázorňuje tento tok provozu.

![proxy.png](media/sql-database-managed-instance-connection-types/proxy.png)

## <a name="script-to-change-connection-type-settings-using-powershell"></a>Skript pro změnu nastavení typu připojení pomocí PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Následující skript prostředí PowerShell ukazuje, jak změnit typ připojení spravované instance na Přesměrovat.

```powershell
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
# Get your SubscriptionId from the Get-AzSubscription command
Get-AzSubscription
# Use your SubscriptionId in place of {subscription-id} below
Select-AzSubscription -SubscriptionId {subscription-id}
# Replace {rg-name} with the resource group for your managed instance, and replace {mi-name} with the name of your managed instance
$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}
$mi = $mi | Set-AzSqlInstance -ProxyOverride "Redirect" -force
```

## <a name="next-steps"></a>Další kroky

- [Obnovení databáze do spravované instance](sql-database-managed-instance-get-started-restore.md)
- Zjistěte, jak [nakonfigurovat veřejný koncový bod ve spravované instanci](sql-database-managed-instance-public-endpoint-configure.md)
- Informace o [architektuře připojení spravovaných instancí](sql-database-managed-instance-connectivity-architecture.md)