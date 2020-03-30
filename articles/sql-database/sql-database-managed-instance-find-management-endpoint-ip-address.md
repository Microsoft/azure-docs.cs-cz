---
title: Zjištění koncového bodu správy spravovaných instancí
description: Zjistěte, jak získat veřejnou IP adresu koncového bodu služby Endpoint spravované instance Azure SQL Database spravované a ověřit její integrovanou ochranu brány firewall.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73825720"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Určení ip adresy koncového bodu pro správu

Virtuální cluster spravované instance Azure SQL Database obsahuje koncový bod pro správu, který Microsoft používá pro operace správy. Koncový bod správy je chráněn vestavěnou bránou firewall na úrovni sítě a vzájemným ověřováním certifikátů na úrovni aplikace. Můžete určit IP adresu koncového bodu správy, ale nemůžete získat přístup k tomuto koncovému bodu.

Chcete-li zjistit adresu IP pro správu, proveďte vyhledávání `mi-name.zone_id.database.windows.net`DNS ve spravované instanci FQDN spravované instance: . Tím se vrátí položka DNS, `trx.region-a.worker.vnet.database.windows.net`která je jako . Potom můžete provést vyhledávání DNS na tomto fQDN s ".vnet" odebrána. Tím se vrátí ip adresa správy. 

Toto prostředí PowerShell udělá vše \<za vás,\> pokud nahradíte MI FQDN položkou DNS spravované instance: `mi-name.zone_id.database.windows.net`:
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Další informace o spravovaných instancích a připojení najdete v [tématu Architektura připojení spravované instance Azure SQL Database .](sql-database-managed-instance-connectivity-architecture.md)
