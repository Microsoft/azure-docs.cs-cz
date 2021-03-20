---
title: IP adresa koncového bodu správy zjišťování
titleSuffix: Azure SQL Managed Instance
description: Přečtěte si, jak získat veřejnou IP adresu koncového bodu správy spravované instance SQL Azure a ověřit integrovanou ochranu brány firewall.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: a9a2b904bd7526f00a8f8a5d013be0c1e42e38a8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91617362"
---
# <a name="determine-the-management-endpoint-ip-address---azure-sql-managed-instance"></a>Určení IP adresy koncového bodu správy – spravovaná instance Azure SQL 
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Virtuální cluster spravované instance SQL Azure obsahuje koncový bod správy, který Azure používá pro operace správy. Koncový bod správy je chráněný integrovanou bránou firewall na úrovni sítě a vzájemného ověřování certifikátů na úrovni aplikace. Můžete určit IP adresu koncového bodu správy, ale nemůžete získat přístup k tomuto koncovému bodu.

IP adresu pro správu určíte tak, že provedete [vyhledávání DNS](/windows-server/administration/windows-commands/nslookup) v plně kvalifikovaném názvu domény spravované instance SQL: `mi-name.zone_id.database.windows.net` . Tato akce vrátí položku DNS, jako je `trx.region-a.worker.vnet.database.windows.net` . Potom můžete provést vyhledání DNS v tomto plně kvalifikovaném názvu domény s odebraným ". VNet". Tím se vrátí IP adresa pro správu. 

Tento kód PowerShellu to vše provede za vás, pokud se nahradíte \<MI FQDN\> položkou DNS spravované instance SQL: `mi-name.zone_id.database.windows.net`
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Další informace o službě SQL Managed instance a připojení najdete v tématu [Architektura připojení spravované instance Azure SQL](connectivity-architecture-overview.md).
