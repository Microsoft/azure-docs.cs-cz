---
title: Spravovaná instance určuje velikost virtuální sítě nebo podsítě
description: Toto téma popisuje, jak vypočítat velikost podsítě, kde se nasadí spravované instance azure SQL database.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 02/22/2019
ms.openlocfilehash: 7f0ef26343284b7b668e71676114586f4bec8b9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73825754"
---
# <a name="determine-vnet-subnet-size-for-azure-sql-database-managed-instance"></a>Určení velikosti podsítě virtuální sítě pro spravovanou instanci Azure SQL Database

Azure SQL Database Managed Instance musí být nasazená v rámci [virtuální sítě Azure .](../virtual-network/virtual-networks-overview.md)

Počet spravovaných instancí, které lze nasadit v podsíti virtuální sítě, závisí na velikosti podsítě (rozsah podsítí).

Když vytvoříte spravovanou instanci, Azure přidělí počet virtuálních počítačů v závislosti na úrovni, kterou jste vybrali během zřizování. Vzhledem k tomu, že tyto virtuální počítače jsou přidruženy k podsíti, vyžadují IP adresy. Chcete-li zajistit vysokou dostupnost během pravidelného provozu a údržby služeb, Azure může přidělit další virtuální počítače. V důsledku toho je počet požadovaných adres IP v podsíti větší než počet spravovaných instancí v této podsíti.

Podle návrhu potřebuje spravovaná instance minimálně 16 IP adres v podsíti a může používat až 256 IP adres. V důsledku toho můžete při definování rozsahů IP podsítě použít masky podsítě mezi /28 a /24. Bit masky sítě /28 (14 hostitelů na síť) je vhodná velikost pro jedno obecné účely nebo důležité nasazení pro firmy. Bit masky /27 (30 hostitelů na síť) je ideální pro více nasazení spravované instance v rámci stejné virtuální sítě. Nastavení bitů masky /26 (62 hostitelů) a /24 (254 hostitelů) umožňuje další škálování z virtuální sítě pro podporu dalších spravovaných instancí.

> [!IMPORTANT]
> Velikost podsítě s 16 ADRESAMI IP je minimum s omezeným potenciálem, kde není podporována operace škálování, jako je změna velikosti virtuálních jader. Důrazně doporučujeme zvolit podsíť s předponou /27 nebo nejdelší předponou.

## <a name="determine-subnet-size"></a>Určit velikost podsítě

Pokud plánujete nasadit více spravovaných instancí uvnitř podsítě a potřebujete optimalizovat na velikost podsítě, použijte tyto parametry k vytvoření výpočtu:

- Azure používá pět IP adres v podsíti pro své vlastní potřeby
- Každá instance pro obecné účely potřebuje dvě adresy
- Každá instance Kritické pro podnikání potřebuje čtyři adresy

**Příklad:** Plánujete mít tři obecné účely a dvě důležité spravované instance podniku. To znamená, že potřebujete 5 + 3 * 2 + 2 * 4 = 19 IP adres. Vzhledem k tomu, že rozsahy IP adres jsou definovány při výkonu 2, potřebujete rozsah IP adres 32 (2^5) IP adres. Proto je třeba rezervovat podsíť s maskou podsítě /27.

> [!IMPORTANT]
> Výše uvedený výpočet bude zastaralý s dalšími vylepšeními.

## <a name="next-steps"></a>Další kroky

- Přehled najdete v tématu [Co je spravovaná instance](sql-database-managed-instance.md).
- Další informace o [architektuře připojení pro spravovanou instanci](sql-database-managed-instance-connectivity-architecture.md).
- Podívejte se, jak [vytvořit virtuální síť, do které budete nasazovat spravované instance.](sql-database-managed-instance-create-vnet-subnet.md)
- Problémy se službou DNS naleznete [v tématu Konfigurace vlastního DNS](sql-database-managed-instance-custom-dns.md)
