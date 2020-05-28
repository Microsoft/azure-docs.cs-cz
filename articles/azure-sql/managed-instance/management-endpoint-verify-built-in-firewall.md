---
title: Ověření zabezpečení portů v integrované bráně firewall
description: Přečtěte si, jak ověřit integrovanou ochranu brány firewall ve spravované instanci Azure SQL.
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
ms.openlocfilehash: 04a630e51a68fab8f6c60262fbd14fad2e4aef1c
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84045071"
---
# <a name="verify-the-azure-sql-managed-instance-built-in-firewall"></a>Ověřte vestavěnou bránu firewall Azure SQL Managed instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Povinná příchozí pravidla zabezpečení](connectivity-architecture-overview.md#mandatory-inbound-security-rules) Azure SQL Managed instance vyžadují, aby byly porty pro správu 9000, 9003, 1438, 1440, 1452 otevřené z **libovolného zdroje** ve skupině zabezpečení sítě (NSG), která chrání spravovanou instanci SQL. I když jsou tyto porty otevřeny na úrovni NSG, jsou chráněny na úrovni sítě pomocí integrované brány firewall.

## <a name="verify-firewall"></a>Ověřit bránu firewall

Chcete-li ověřit tyto porty, použijte k otestování těchto portů nástroj pro kontrolu zabezpečení. Následující snímek obrazovky ukazuje, jak použít jeden z těchto nástrojů.

![Ověřování integrované brány firewall](./media/management-endpoint-verify-built-in-firewall/03_verify_firewall.png)

## <a name="next-steps"></a>Další kroky

Další informace o spravovaných instancích SQL a připojení najdete v tématu [Architektura připojení spravované instance SQL Azure](connectivity-architecture-overview.md).
