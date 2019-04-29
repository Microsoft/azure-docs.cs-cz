---
title: Zjistit koncový bod pro správu Azure SQL Database Managed Instance | Dokumentace Microsoftu
description: Zjistěte, jak získat Azure SQL Database Managed Instance koncový bod veřejné IP adresa pro správu a ověření integrované firewall ochrany
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: b7eb9ecd6b94aad263346ad6b5c45b694e0bd46f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60699978"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Určit IP adresu koncového bodu správy

Virtuální cluster Azure SQL Database Managed Instance obsahuje koncový bod správy, které společnost Microsoft používá pro operace správy. Koncový bod správy je chráněný pomocí integrované brány firewall na úrovni a vzájemné certifikát ověření sítě na úrovni aplikace. Můžete určit IP adresu koncového bodu správy, ale nemáte přístup k tomuto koncovému bodu.

## <a name="determine-ip-address"></a>Určení adresy IP

Předpokládejme, že je Managed Instance hostitele `mi-demo.xxxxxx.database.windows.net`. Spustit `nslookup` pomocí názvu hostitele.

![Překlad názvu interního hostitele](./media/sql-database-managed-instance-management-endpoint/01_find_internal_host.png)

Teď provádět jiné `nslookup` pro odebrání název zvýrazněný `.vnet.` segmentu. Při spuštění tohoto příkazu získáte veřejnou IP adresu.

![Řešení veřejnou IP adresu](./media/sql-database-managed-instance-management-endpoint/02_find_public_ip.png)

## <a name="next-steps"></a>Další postup

Další informace o Managed instance a připojení najdete v tématu [Azure SQL Database Managed Instance připojení architektury](sql-database-managed-instance-connectivity-architecture.md).
