---
title: Zjistit integrované brány firewall Azure SQL Database Managed Instance | Dokumentace Microsoftu
description: Zjistěte, jak ověření integrované firewall ochrany v Azure SQL Database Managed Instance.
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
ms.openlocfilehash: d7b781a2cfcf17285b105a104588fe1569dffe4b
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53345958"
---
# <a name="verifying-the-managed-instance-built-in-firewall"></a>Ověření integrované firewall Managed Instance

Managed Instance [povinné zabezpečení příchozích pravidel](sql-database-managed-instance-connectivity-architecture.md#mandatory-inbound-security-rules) vyžadují portům pro správu 9000 9003, 1438, 1440, 1452 otevřené z **libovolného zdroje** na zabezpečení skupiny sítě (NSG), který chrání spravované Instance. I když tyto porty jsou spuštěné na úrovni skupiny zabezpečení sítě, jsou chráněny na úrovni sítě pomocí integrovaných brány firewall.

## <a name="verify-firewall"></a>Ověření brány firewall

Pokud chcete ověřit tyto porty, použijte libovolný skener nástroj zabezpečení k testování těchto portech. Následující snímek obrazovky ukazuje, jak použít jeden z těchto nástrojů.

![Ověření integrované brány firewall](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## <a name="next-steps"></a>Další postup

Další informace o Managed instance a připojení najdete v tématu [Azure SQL Database Managed Instance připojení architektury](sql-database-managed-instance-connectivity-architecture.md).