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
ms.openlocfilehash: 588fac1fc48396584188eec44f21a7005dc8ed96
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567552"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Konfigurace vlastního DNS pro Azure SQL Database spravovanou instanci

Spravovaná instance Azure SQL Database musí být nasazená v rámci [virtuální sítě Azure (VNET)](../virtual-network/virtual-networks-overview.md). Existuje několik scénářů (například databázová pošta, propojené servery na jiné instance SQL ve vašem cloudovém nebo hybridním prostředí), které vyžadují, aby byly názvy privátních hostitelů přeloženy ze spravované instance. V takovém případě musíte nakonfigurovat vlastní DNS v Azure. 

Vzhledem k tomu, že spravovaná instance používá pro vnitřní práci stejnou službu DNS, musíte nakonfigurovat vlastní server DNS tak, aby mohl přeložit názvy veřejných domén.

   > [!IMPORTANT]
   > Vždy používejte plně kvalifikované názvy domén (FQDN) pro poštovní servery, servery SQL a další služby, i když se nacházejí v privátní zóně DNS. Například použijte `smtp.contoso.com` pro poštovní server, protože jednoduchá `smtp` nebude vyřešena správně.

   > [!IMPORTANT]
   > Aktualizace serverů DNS virtuální sítě by neovlivnila přímo spravovanou instanci. Konfigurace DNS spravované instance se aktualizuje po vypršení platnosti zapůjčení DHCP nebo za platformou upgarade, ať už nastane dřív. **Před vytvořením první spravované instance doporučujeme uživatelům, aby si nastavili konfiguraci DNS virtuální sítě.**

## <a name="next-steps"></a>Další postup

- Přehled najdete v tématu [co je spravovaná instance](sql-database-managed-instance.md) .
- Kurz, ve kterém se dozvíte, jak vytvořit novou spravovanou instanci, najdete v tématu [Vytvoření spravované instance](sql-database-managed-instance-get-started.md).
- Informace o konfiguraci virtuální sítě pro spravovanou instanci najdete v tématu [Konfigurace virtuální sítě pro spravované instance](sql-database-managed-instance-connectivity-architecture.md) .
