---
title: Zjistit Azure SQL Database koncový bod správy spravované instance
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
ms.openlocfilehash: 0cc3ca3a2b8130e52d8c7fc670c0ee7997e4aa47
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73688033"
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
