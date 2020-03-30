---
title: Vlastní dns spravované instance
description: Toto téma popisuje možnosti konfigurace pro vlastní DNS se spravovanou instanci Azure SQL Database.
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
ms.openlocfilehash: 9c98176413f858d236902c332553d47f749aa3c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247074"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Konfigurace vlastního systému DNS pro spravovanou instanci Azure SQL Database

Správa databáze Azure SQL musí být nasazená v rámci [virtuální sítě Azure .](../virtual-network/virtual-networks-overview.md) Existuje několik scénářů (například db mail, propojené servery s jinými instancemi SQL ve vašem cloudu nebo hybridním prostředí), které vyžadují, aby byly názvy soukromých hostitelů vyřešeny ze spravované instance. V takovém případě je potřeba nakonfigurovat vlastní DNS uvnitř Azure. 

Vzhledem k tomu, že spravovaná instance používá pro své vnitřní fungování stejný dns, nakonfigurujte vlastní server DNS tak, aby mohl přeložit názvy veřejných domén.

> [!IMPORTANT]
> Pro poštovní server, instanci serveru SQL Server a pro další služby vždy používejte plně kvalifikovaný název domény (FQDN), a to i v případě, že jsou v privátní zóně DNS. Použijte například `smtp.contoso.com` pro poštovní `smtp` server, protože se nevyřeší správně. Vytvoření propojeného serveru nebo replikace, která odkazuje na virtuální počítače SQL uvnitř stejné virtuální sítě, vyžaduje také hlavní název domény a výchozí příponu DNS. Například, `SQLVM.internal.cloudapp.net`. Další informace naleznete v [tématu Překlad názvů, který používá vlastní server DNS](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server).

> [!IMPORTANT]
> Aktualizace serverů DNS virtuální sítě neovlivní spravovanou instanci okamžitě. Konfigurace DNS spravované instance je aktualizována po vypršení platnosti zapůjčení DHCP nebo po upgradu platformy podle toho, co nastane dříve. **Uživatelům se doporučuje nastavit konfiguraci DNS virtuální sítě před vytvořením první spravované instance.**

## <a name="next-steps"></a>Další kroky

- Přehled najdete v tématu [Co je spravovaná instance](sql-database-managed-instance.md)
- Kurz, který vám ukáže, jak vytvořit novou spravovanou instanci, najdete [v tématu Vytvoření spravované instance](sql-database-managed-instance-get-started.md).
- Informace o konfiguraci virtuální sítě pro spravovanou instanci najdete v tématu [Konfigurace virtuální sítě pro spravované instance.](sql-database-managed-instance-connectivity-architecture.md)
