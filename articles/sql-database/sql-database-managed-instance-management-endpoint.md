---
title: Zjistit koncový bod pro správu Azure SQL Database Managed Instance | Dokumentace Microsoftu
description: Zjistěte, jak získat Azure SQL Database Managed Instance koncový bod veřejné IP adresa pro správu a ověření integrované firewall ochrany
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab
manager: craigg
ms.date: 12/03/2018
ms.openlocfilehash: 99ec559429d66becc20e038e43349f5369afac39
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52855704"
---
# <a name="discover-azure-sql-database-managed-instance-management-endpoint"></a>Zjistit koncový bod pro správu Azure SQL Database Managed Instance 

## <a name="overview"></a>Přehled
Spravované Instance Azure SQL [virtuální cluster](sql-database-managed-instance-connectivity-architecture.md) obsahuje koncový bod správy, který Microsoft používá ke správě Instance.  

Koncový bod správy je chráněný pomocí předdefinovaných brány firewall sítě certifikát úrovně a proces vzájemného ověření na úrovni aplikace. 

Po připojení se zahájilo Managed Instance (CLR odkazovaný server, zálohování, auditu protokolu atd.) zdá se, že provoz pochází z veřejnou IP adresu koncového bodu správy. Může omezit přístup k veřejné služby ze Managed Instance, tak, že nastavíte pravidla firewallu povolující právě tuto IP adresu.

> [!NOTE]
> To neplatí pro nastavení pravidla brány firewall pro služby Azure, které patří do stejné oblasti jako spravované Instance jako platforma má optimalizace pro provoz, který prochází mezi službami, které jsou společně umístěná. 

Tento článek vysvětluje, jak může správa koncový bod veřejné IP adresy a jak získat ověření integrované firewall ochrany.

## <a name="finding-management-endpoint-public-ip-address"></a>Hledání veřejnou IP adresu koncového bodu správy
Předpokládejme, že se MI hostitel _mi demo.xxxxxx.database.windows.net_. Spustit _nslookup_ pomocí názvu hostitele.

![Překlad názvu interního hostitele](./media/sql-database-managed-instance-management-endpoint/01_find_internal_host.png)

V současnosti jiného _nslookup_ pro název zvýrazněný removed_.vnet._segment. Budete mít veřejnou IP adresu v důsledku spuštění tohoto příkazu.

![Řešení veřejnou IP adresu](./media/sql-database-managed-instance-management-endpoint/02_find_public_ip.png)

## <a name="verifying-managed-instance-built-in-firewall"></a>Ověření integrované firewall Managed Instance
Spravovaná Instance [povinné zabezpečení příchozích pravidel](sql-database-managed-instance-vnet-configuration.md#mandatory-inbound-security-rules) vyžadují portům pro správu 9000 9003, 1438, 1440, otevřete 1452 bude z jakéhokoli zdroje na skupiny zabezpečení sítě, která chrání Managed Instance. I když tyto porty jsou spuštěné na úrovni skupiny zabezpečení sítě, jsou chráněny na úrovni sítě pomocí integrovaných brány firewall.

Ověření tyto porty, můžete použít libovolný nástroj skener zabezpečení k testování těchto portech. Následující snímek obrazovky ukazuje, jak použít jeden z těchto nástrojů.

![Ověření integrované brány firewall](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)
