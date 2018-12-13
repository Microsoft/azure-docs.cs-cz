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
ms.date: 12/04/2018
ms.openlocfilehash: fc04422f5e32a77fff42dc07e2441e84c9b15866
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53271874"
---
# <a name="discover-azure-sql-database-managed-instance-management-endpoint"></a>Zjistit koncový bod pro správu Azure SQL Database Managed Instance

Azure SQL Database Managed Instance [virtuální cluster](sql-database-managed-instance-connectivity-architecture.md) obsahuje koncový bod správy, který Microsoft používá ke správě Managed Instance. Koncový bod správy je chráněný pomocí předdefinovaných brány firewall sítě certifikát úrovně a proces vzájemného ověření na úrovni aplikace.

Zdá se, že provoz pochází z veřejnou IP adresu koncového bodu správy po připojení se zahájilo Managed Instance (zálohování, protokolu auditu). Může omezit přístup k veřejné služby ze Managed Instance, tak, že nastavíte pravidla brány firewall pro povolení pouze spravované Instance IP adres.

> [!NOTE]
> To neplatí pro nastavení pravidla brány firewall pro služby Azure, které jsou ve stejné oblasti jako Managed Instance, Platforma Azure má optimalizace pro provoz, který prochází mezi službami, které jsou společně umístěná.

Tento článek vysvětluje, jak může správa koncový bod veřejné IP adresy a jak získat ověření integrované firewall ochrany.

## <a name="finding-the-management-endpoint-public-ip-address"></a>Vyhledání veřejné IP adresy koncového bodu správy

Předpokládejme, že je Managed Instance hostitele `mi-demo.xxxxxx.database.windows.net`. Spustit `nslookup` pomocí názvu hostitele.

![Překlad názvu interního hostitele](./media/sql-database-managed-instance-management-endpoint/01_find_internal_host.png)

Teď provádět jiné `nslookup` pro odebrání název zvýrazněný `.vnet.` segmentu. Budete mít veřejnou IP adresu v důsledku spuštění tohoto příkazu.

![Řešení veřejnou IP adresu](./media/sql-database-managed-instance-management-endpoint/02_find_public_ip.png)

## <a name="verifying-the-managed-instance-built-in-firewall"></a>Ověření integrované firewall Managed Instance

Managed Instance [povinné zabezpečení příchozích pravidel](sql-database-managed-instance-vnet-configuration.md#mandatory-inbound-security-rules) vyžadují portům pro správu 9000 9003, 1438, 1440, 1452 otevřené z **libovolného zdroje** na zabezpečení skupiny sítě (NSG), který chrání spravované Instance. I když tyto porty jsou spuštěné na úrovni skupiny zabezpečení sítě, jsou chráněny na úrovni sítě pomocí integrovaných brány firewall.

Pokud chcete ověřit tyto porty, použijte libovolný skener nástroj zabezpečení k testování těchto portech. Následující snímek obrazovky ukazuje, jak použít jeden z těchto nástrojů.

![Ověření integrované brány firewall](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## <a name="next-steps"></a>Další postup

Další informace o Managed instance a připojení najdete v tématu [Azure SQL Database Managed Instance připojení architektury](sql-database-managed-instance-connectivity-architecture.md).