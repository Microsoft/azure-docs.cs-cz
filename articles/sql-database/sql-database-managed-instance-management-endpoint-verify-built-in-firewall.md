---
title: Zjišťování integrované brány firewall spravované instance
description: Zjistěte, jak ověřit integrovanou ochranu brány firewall ve spravované instanci Azure SQL Database.
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
ms.openlocfilehash: 555ef56aafa37a1e1d384f945b04f9237adc5f7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821803"
---
# <a name="verifying-the-managed-instance-built-in-firewall"></a>Ověření integrované brány firewall spravované instance

[Povinná pravidla příchozího zabezpečení](sql-database-managed-instance-connectivity-architecture.md#mandatory-inbound-security-rules) spravované instance vyžadují, aby porty pro správu 9000, 9003, 1438, 1440, 1452 byly otevřeny z **libovolného zdroje** skupiny zabezpečení sítě (NSG), který chrání spravovanou instanci. Přestože jsou tyto porty otevřené na úrovni sítě Zabezpečení sítě, jsou na úrovni sítě chráněny vestavěnou bránou firewall.

## <a name="verify-firewall"></a>Ověřit bránu firewall

Chcete-li tyto porty ověřit, otestujte tyto porty pomocí libovolného nástroje pro skener zabezpečení. Následující snímek obrazovky ukazuje, jak používat jeden z těchto nástrojů.

![Ověření integrované brány firewall](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## <a name="next-steps"></a>Další kroky

Další informace o spravovaných instancích a připojení najdete v [tématu Architektura připojení spravované instance Azure SQL Database .](sql-database-managed-instance-connectivity-architecture.md)