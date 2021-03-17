---
title: Ověření zabezpečení portů v integrované bráně firewall
description: Přečtěte si, jak ověřit integrovanou ochranu brány firewall ve spravované instanci Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: d8d46518415dc56e2792ef090b0be654d538426f
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2020
ms.locfileid: "96853267"
---
# <a name="verify-the-azure-sql-managed-instance-built-in-firewall"></a>Ověření integrované brány firewall služby Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Povinná příchozí pravidla zabezpečení](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration) Azure SQL Managed instance vyžadují, aby byly porty pro správu 9000, 9003, 1438, 1440 a 1452 otevřené z **libovolného zdroje** ve skupině zabezpečení sítě (NSG), která chrání SQL Managed instance. I když jsou tyto porty otevřeny na úrovni NSG, jsou chráněny na úrovni sítě pomocí integrované brány firewall.

## <a name="verify-firewall"></a>Ověřit bránu firewall

Chcete-li ověřit tyto porty, použijte k otestování těchto portů nástroj pro kontrolu zabezpečení. Následující snímek obrazovky ukazuje, jak použít jeden z těchto nástrojů.

![Ověřování integrované brány firewall](./media/management-endpoint-verify-built-in-firewall/03_verify_firewall.png)

## <a name="next-steps"></a>Další kroky

Další informace o službě SQL Managed instance a připojení najdete v tématu [Architektura připojení spravované instance Azure SQL](connectivity-architecture-overview.md).
