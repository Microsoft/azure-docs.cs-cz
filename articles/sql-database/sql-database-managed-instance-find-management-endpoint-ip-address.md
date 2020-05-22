---
title: Zjistit koncový bod správy spravované instance
description: Přečtěte si, jak získat veřejnou IP adresu koncového bodu správy spravované instance Azure SQL Database a ověřit jeho integrovanou ochranu brány firewall.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: ac4675be25cb5f2f65cc444e65bd25d65d3c3856
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83770036"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Určení IP adresy koncového bodu správy

Virtuální cluster Azure SQL Database Managed instance obsahuje koncový bod správy, který Microsoft používá pro operace správy. Koncový bod správy je chráněný integrovanou bránou firewall na úrovni sítě a vzájemného ověřování certifikátů na úrovni aplikace. Můžete určit IP adresu koncového bodu správy, ale nemůžete získat přístup k tomuto koncovému bodu.

IP adresu pro správu určíte tak, že provedete vyhledávání DNS v plně kvalifikovaném názvu domény spravované instance: `mi-name.zone_id.database.windows.net` . Tato akce vrátí položku DNS, jako je `trx.region-a.worker.vnet.database.windows.net` . Potom můžete provést vyhledání DNS v tomto plně kvalifikovaném názvu domény s odebraným ". VNet". Tím se vrátí IP adresa pro správu. 

Tento PowerShell to vše udělá za vás, pokud nahradíte \< plně kvalifikovaný název domény názvem mi \> pomocí položky DNS vaší spravované instance: `mi-name.zone_id.database.windows.net`
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Další informace o spravovaných instancích a připojeních najdete v tématu [Azure SQL Database architektura připojení spravované instance](sql-database-managed-instance-connectivity-architecture.md).
