---
title: Vlastní DNS
titleSuffix: Azure SQL Managed Instance
description: Toto téma popisuje možnosti konfigurace pro vlastní DNS pomocí spravované instance Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 07/17/2019
ms.openlocfilehash: 6a6cca1228c9891c023ce96532dc89b2f527e9a3
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84655526"
---
# <a name="configure-a-custom-dns-for-azure-sql-managed-instance"></a>Konfigurace vlastního DNS pro spravovanou instanci SQL Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Spravovaná instance Azure SQL musí být nasazená v rámci [virtuální sítě Azure (VNET)](../../virtual-network/virtual-networks-overview.md). Existuje několik scénářů (například databázová pošta, propojené servery s ostatními SQL Server instance v cloudu nebo hybridním prostředí), které vyžadují, aby byly názvy privátních hostitelů přeloženy ze spravované instance SQL. V takovém případě musíte nakonfigurovat vlastní DNS v Azure. 

Vzhledem k tomu, že spravovaná instance SQL používá pro vnitřní práci stejnou službu DNS, nakonfigurujte vlastní server DNS tak, aby mohl přeložit názvy veřejných domén.

> [!IMPORTANT]
> Vždy používejte plně kvalifikovaný název domény (FQDN) pro poštovní server, pro instanci SQL Server a pro další služby, a to i v případě, že se nachází v privátní zóně DNS. Použijte třeba `smtp.contoso.com` pro svůj poštovní server, protože se `smtp` nebude správně vyřešit. Vytvoření propojeného serveru nebo replikace odkazující na SQL Server virtuální počítače ve stejné virtuální síti taky vyžaduje plně kvalifikovaný název domény a výchozí příponu DNS. Například, `SQLVM.internal.cloudapp.net`. Další informace najdete v tématu [překlad názvů, který používá vlastní server DNS](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server).

> [!IMPORTANT]
> Aktualizace serverů DNS virtuální sítě neovlivní okamžitě spravovanou instanci SQL. Konfigurace DNS spravované instance SQL se aktualizuje po vypršení platnosti zapůjčení DHCP nebo po upgradu platformy, podle toho, co nastane dřív. **Před vytvořením první spravované instance doporučujeme uživatelům, aby si nastavili konfiguraci DNS virtuální sítě.**

## <a name="next-steps"></a>Další kroky

- Přehled najdete v tématu [co je Azure SQL Managed instance?](sql-managed-instance-paas-overview.md).
- Kurz, ve kterém se dozvíte, jak vytvořit novou spravovanou instanci, najdete v tématu [Vytvoření spravované instance](instance-create-quickstart.md).
- Informace o konfiguraci virtuální sítě pro spravovanou instanci najdete v tématu [Konfigurace virtuální sítě pro spravované instance](connectivity-architecture-overview.md).
