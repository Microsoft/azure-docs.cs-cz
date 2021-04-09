---
title: Vlastní DNS
titleSuffix: Azure SQL Managed Instance
description: Toto téma popisuje možnosti konfigurace pro vlastní DNS pomocí spravované instance Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 07/17/2019
ms.openlocfilehash: a54907dd3f7b3fbc06033624f14b12de14d9afb9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831497"
---
# <a name="configure-a-custom-dns-for-azure-sql-managed-instance"></a>Konfigurace vlastního systému DNS pro službu Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Spravovaná instance Azure SQL musí být nasazená v rámci [virtuální sítě Azure (VNET)](../../virtual-network/virtual-networks-overview.md). Existuje několik scénářů (například Databázová pošta nebo servery odkazované na jiné instance SQL Serveru ve vašem cloudovém nebo hybridním prostředí), které vyžadují překlad názvů privátních hostitelů ze služby SQL Managed Instance. V takovém případě musíte nakonfigurovat vlastní DNS v rámci Azure. 

Vzhledem k tomu, že spravovaná instance SQL používá pro vnitřní práci stejnou službu DNS, nakonfigurujte vlastní server DNS tak, aby mohl přeložit názvy veřejných domén.

> [!IMPORTANT]
> Vždy používejte plně kvalifikovaný název domény (FQDN) pro poštovní server, pro instanci SQL Server a pro další služby, a to i v případě, že se nachází v privátní zóně DNS. Použijte třeba `smtp.contoso.com` pro svůj poštovní server, protože se `smtp` nebude správně vyřešit. Vytvoření propojeného serveru nebo replikace odkazující na SQL Server virtuální počítače ve stejné virtuální síti taky vyžaduje plně kvalifikovaný název domény a výchozí příponu DNS. Například, `SQLVM.internal.cloudapp.net`. Další informace najdete v tématu [překlad názvů, který používá vlastní server DNS](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

> [!IMPORTANT]
> Aktualizace serverů DNS virtuální sítě neovlivní okamžitě spravovanou instanci SQL. Další podrobnosti najdete v tématu věnovaném [synchronizaci nastavení serverů DNS virtuální sítě ve virtuálním clusteru služby SQL Managed Instance](synchronize-vnet-dns-servers-setting-on-virtual-cluster.md).

## <a name="next-steps"></a>Další kroky

- Přehled najdete v tématu [co je Azure SQL Managed instance?](sql-managed-instance-paas-overview.md).
- Kurz, ve kterém se dozvíte, jak vytvořit novou spravovanou instanci, najdete v tématu [Vytvoření spravované instance](instance-create-quickstart.md).
- Informace o konfiguraci virtuální sítě pro spravovanou instanci najdete v tématu [Konfigurace virtuální sítě pro spravované instance](connectivity-architecture-overview.md).
