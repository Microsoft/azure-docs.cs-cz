---
title: Zjistit koncový bod správy spravované instance
description: Přečtěte si, jak získat veřejnou IP adresu koncového bodu správy spravované instance Azure SQL Database a ověřit jeho integrovanou ochranu brány firewall.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 03cd89084c2bae3339311f2f684a0d5e7bac1f68
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825720"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Určení IP adresy koncového bodu správy

Virtuální cluster Azure SQL Database Managed instance obsahuje koncový bod správy, který Microsoft používá pro operace správy. Koncový bod správy je chráněný integrovanou bránou firewall na úrovni sítě a vzájemného ověřování certifikátů na úrovni aplikace. Můžete určit IP adresu koncového bodu správy, ale nemůžete získat přístup k tomuto koncovému bodu.

IP adresu pro správu zjistíte tak, že provedete vyhledávání DNS v plně kvalifikovaném názvu domény spravované instance: `mi-name.zone_id.database.windows.net`. Tím se vrátí položka DNS, jako `trx.region-a.worker.vnet.database.windows.net`. Potom můžete provést vyhledání DNS v tomto plně kvalifikovaném názvu domény s odebraným ". VNet". Tím se vrátí IP adresa pro správu. 

Tento PowerShell vám to všechno udělá, pokud nahradíte \<\> plně kvalifikovaný název domény pomocí položky DNS vaší spravované instance: `mi-name.zone_id.database.windows.net`:
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Další informace o spravovaných instancích a připojeních najdete v tématu [Azure SQL Database architektura připojení spravované instance](sql-database-managed-instance-connectivity-architecture.md).
