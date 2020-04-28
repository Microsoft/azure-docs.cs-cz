---
title: Typy připojení spravované instance
description: Další informace o typech připojení spravované instance
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.openlocfilehash: 46223d1701b930d93de7c49c1e216a41045dda16
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "73819462"
---
# <a name="azure-sql-database-managed-instance-connection-types"></a>Azure SQL Database typy připojení spravované instance

Tento článek vysvětluje, jak se klienti připojují k Azure SQL Database spravované instanci v závislosti na typu připojení. Ukázky skriptů pro změnu typů připojení jsou uvedené níže a s důležitými informacemi týkajícími se změny výchozího nastavení připojení.

## <a name="connection-types"></a>Typy připojení

Azure SQL Database spravovaná instance podporuje následující dva typy připojení:

- **Přesměrování (doporučeno):** Klienti navážou připojení přímo k uzlu, který je hostitelem databáze. Pokud chcete povolit připojení pomocí přesměrování, musíte otevřít brány firewall a skupiny zabezpečení sítě (NSG), abyste mohli povolit přístup na portech 1433 a 11000-11999. Pakety se přecházejí přímo do databáze a díky přesměrování přes proxy se pak zvyšují latence a výkon.
- **Proxy server (výchozí):** V tomto režimu všechna připojení používají komponentu proxy serveru. Chcete-li povolit připojení, je nutné otevřít pouze port 1433 pro privátní sítě a port 3342 pro veřejné připojení. Výběr tohoto režimu může mít za následek vyšší latenci a nižší propustnost, a to v závislosti na povaze úlohy. Pro nejnižší latenci a nejvyšší propustnost důrazně doporučujeme zásady připojení přesměrování použít u zásad připojení k proxy serveru.

## <a name="redirect-connection-type"></a>Přesměrování – typ připojení

Typ připojení přesměrování znamená, že po navázání relace TCP na modul SQL klientská relace získá cílovou virtuální IP adresu uzlu virtuálního clusteru z nástroje pro vyrovnávání zatížení. Následné pakety se přecházejí přímo na uzel virtuálního clusteru a brána se vynechá. Tento tok přenosů znázorňuje následující diagram.

![přesměrovat. png](media/sql-database-managed-instance-connection-types/redirect.png)

> [!IMPORTANT]
> Typ připojení přesměrování aktuálně funguje pouze pro soukromý koncový bod. Bez ohledu na nastavení typu připojení by připojení přicházející prostřednictvím veřejného koncového bodu byla prostřednictvím proxy serveru.

## <a name="proxy-connection-type"></a>Typ připojení proxy

Typ připojení proxy znamená, že relace TCP se naváže pomocí brány a všech dalších paketů toku. Tento tok přenosů znázorňuje následující diagram.

![proxy. png](media/sql-database-managed-instance-connection-types/proxy.png)

## <a name="script-to-change-connection-type-settings-using-powershell"></a>Skript pro změnu nastavení typu připojení pomocí PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Následující skript prostředí PowerShell ukazuje, jak změnit typ připojení pro spravovanou instanci na přesměrování.

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
- Informace o tom, jak [nakonfigurovat veřejný koncový bod na spravované instanci](sql-database-managed-instance-public-endpoint-configure.md)
- Další informace o [architektuře připojení spravované instance](sql-database-managed-instance-connectivity-architecture.md)