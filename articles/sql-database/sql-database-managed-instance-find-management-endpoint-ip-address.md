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
ms.openlocfilehash: 83eea565a12ee5201c42b543cdbdad72ddc28ca9
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53346068"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Určit IP adresu koncového bodu správy

Virtuální cluster Azure SQL Database Managed Instance obsahuje koncový bod správy, které společnost Microsoft používá pro operace správy. Koncový bod správy je chráněný pomocí integrované brány firewall na úrovni a vzájemné certifikát ověření sítě na úrovni aplikace. Můžete určit IP adresu koncového bodu správy, ale nemáte přístup k tomuto koncovému bodu.

## <a name="determine-ip-address"></a>Určení adresy IP

Předpokládejme, že je Managed Instance hostitele `mi-demo.xxxxxx.database.windows.net`. Spustit `nslookup` pomocí názvu hostitele.

![Překlad názvu interního hostitele](./media/sql-database-managed-instance-management-endpoint/01_find_internal_host.png)

Teď provádět jiné `nslookup` pro odebrání název zvýrazněný `.vnet.` segmentu. Budete mít veřejnou IP adresu v důsledku spuštění tohoto příkazu.

![Řešení veřejnou IP adresu](./media/sql-database-managed-instance-management-endpoint/02_find_public_ip.png)


## <a name="next-steps"></a>Další postup

Další informace o Managed instance a připojení najdete v tématu [Azure SQL Database Managed Instance připojení architektury](sql-database-managed-instance-connectivity-architecture.md).
