---
title: Vyhledat vestavěnou bránu firewall spravované instance
description: Přečtěte si, jak ověřit integrovanou ochranu brány firewall v Azure SQL Database Managed instance.
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
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821803"
---
# <a name="verifying-the-managed-instance-built-in-firewall"></a>Ověřování integrované brány firewall spravované instance

[Povinná příchozí pravidla zabezpečení](sql-database-managed-instance-connectivity-architecture.md#mandatory-inbound-security-rules) spravované instance vyžadují, aby porty pro správu 9000, 9003, 1438, 1440, 1452 byly otevřené z **libovolného zdroje** ve skupině zabezpečení sítě (NSG), která chrání spravovanou instanci. I když jsou tyto porty otevřeny na úrovni NSG, jsou chráněny na úrovni sítě pomocí integrované brány firewall.

## <a name="verify-firewall"></a>Ověřit bránu firewall

Chcete-li ověřit tyto porty, použijte k otestování těchto portů nástroj pro kontrolu zabezpečení. Následující snímek obrazovky ukazuje, jak použít jeden z těchto nástrojů.

![Ověřování integrované brány firewall](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## <a name="next-steps"></a>Další kroky

Další informace o spravovaných instancích a připojeních najdete v tématu [Azure SQL Database architektura připojení spravované instance](sql-database-managed-instance-connectivity-architecture.md).