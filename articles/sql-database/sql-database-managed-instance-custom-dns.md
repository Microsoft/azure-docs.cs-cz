---
title: Azure SQL Database vlastní DNS spravované instance | Microsoft Docs
description: Toto téma popisuje možnosti konfigurace pro vlastní DNS s Azure SQL Database spravované instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 07/17/2019
ms.openlocfilehash: 9b15ebc40e99c1cd454396ccde5cca6b1a46abbc
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244748"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Konfigurace vlastního DNS pro Azure SQL Database spravovanou instanci

Spravovaná instance Azure SQL Database musí být nasazená v rámci [virtuální sítě Azure (VNET)](../virtual-network/virtual-networks-overview.md). Existuje několik scénářů (například databázová pošta, propojené servery na jiné instance SQL ve vašem cloudovém nebo hybridním prostředí), které vyžadují, aby byly názvy privátních hostitelů přeloženy ze spravované instance. V takovém případě musíte nakonfigurovat vlastní DNS v Azure. 

Vzhledem k tomu, že spravovaná instance používá pro vnitřní práci stejnou službu DNS, nakonfigurujte vlastní server DNS tak, aby mohl přeložit názvy veřejných domén.

> [!IMPORTANT]
> U poštovního serveru, SQL Server instance a dalších služeb vždycky používejte plně kvalifikovaný název domény (FQDN), a to i v případě, že se nachází v privátní zóně DNS. Například pro poštovní server použijte `smtp.contoso.com`, protože `smtp` se nebude překládat správně. Vytvoření propojeného serveru nebo replikace, která odkazuje na virtuální počítače SQL ve stejné virtuální síti, taky vyžaduje plně kvalifikovaný název domény a výchozí příponu DNS. Například `SQLVM.internal.cloudapp.net`. Další informace najdete v tématu [překlad názvů, který používá vlastní server DNS](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server).

> [!IMPORTANT]
> Aktualizace serverů DNS virtuální sítě nebude mít na spravovanou instanci okamžitě vliv. Konfigurace DNS spravované instance se aktualizuje po vypršení platnosti zapůjčení DHCP nebo za upgarade platformy, podle toho, co nastane dřív. **Před vytvořením první spravované instance doporučujeme uživatelům, aby si nastavili konfiguraci DNS virtuální sítě.**

## <a name="next-steps"></a>Další kroky

- Přehled najdete v tématu [co je spravovaná instance](sql-database-managed-instance.md) .
- Kurz, ve kterém se dozvíte, jak vytvořit novou spravovanou instanci, najdete v tématu [Vytvoření spravované instance](sql-database-managed-instance-get-started.md).
- Informace o konfiguraci virtuální sítě pro spravovanou instanci najdete v tématu [Konfigurace virtuální sítě pro spravované instance](sql-database-managed-instance-connectivity-architecture.md) .
