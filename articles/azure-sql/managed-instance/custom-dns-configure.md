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
ms.openlocfilehash: 9fdd61c5799724ab3ab098584f85ce52cdd6ccf4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91283254"
---
# <a name="configure-a-custom-dns-for-azure-sql-managed-instance"></a>Konfigurace vlastního DNS pro spravovanou instanci SQL Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Spravovaná instance Azure SQL musí být nasazená v rámci [virtuální sítě Azure (VNET)](../../virtual-network/virtual-networks-overview.md). Existuje několik scénářů (například Databázová pošta nebo servery odkazované na jiné instance SQL Serveru ve vašem cloudovém nebo hybridním prostředí), které vyžadují překlad názvů privátních hostitelů ze služby SQL Managed Instance. V takovém případě musíte nakonfigurovat vlastní DNS v rámci Azure. 

Vzhledem k tomu, že spravovaná instance SQL používá pro vnitřní práci stejnou službu DNS, nakonfigurujte vlastní server DNS tak, aby mohl přeložit názvy veřejných domén.

> [!IMPORTANT]
> Vždy používejte plně kvalifikovaný název domény (FQDN) pro poštovní server, pro instanci SQL Server a pro další služby, a to i v případě, že se nachází v privátní zóně DNS. Použijte třeba `smtp.contoso.com` pro svůj poštovní server, protože se `smtp` nebude správně vyřešit. Vytvoření propojeného serveru nebo replikace odkazující na SQL Server virtuální počítače ve stejné virtuální síti taky vyžaduje plně kvalifikovaný název domény a výchozí příponu DNS. Například, `SQLVM.internal.cloudapp.net`. Další informace najdete v tématu [překlad názvů, který používá vlastní server DNS](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server).

> [!IMPORTANT]
> Aktualizace serverů DNS virtuální sítě neovlivní okamžitě spravovanou instanci SQL. Konfigurace DNS spravované instance SQL se aktualizuje po vypršení platnosti zapůjčení DHCP nebo po upgradu platformy, podle toho, co nastane dřív. **Před vytvořením první spravované instance doporučujeme uživatelům, aby si nastavili konfiguraci DNS virtuální sítě.**

## <a name="next-steps"></a>Další kroky

- Přehled najdete v tématu [co je Azure SQL Managed instance?](sql-managed-instance-paas-overview.md).
- Kurz, ve kterém se dozvíte, jak vytvořit novou spravovanou instanci, najdete v tématu [Vytvoření spravované instance](instance-create-quickstart.md).
- Informace o konfiguraci virtuální sítě pro spravovanou instanci najdete v tématu [Konfigurace virtuální sítě pro spravované instance](connectivity-architecture-overview.md).
