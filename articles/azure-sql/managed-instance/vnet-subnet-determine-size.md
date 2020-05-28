---
title: Určení požadované velikosti podsítě & rozsahu
titleSuffix: Azure SQL Managed Instance
description: Toto téma popisuje, jak vypočítat velikost podsítě, do které se nasadí spravované instance Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 02/22/2019
ms.openlocfilehash: 5eebde1fc95cb50f8ff7c13b6cbc411484ddf943
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84050930"
---
# <a name="determine-required-subnet-size--range-for-azure-sql-managed-instance"></a>Určení požadované velikosti podsítě & rozsahu pro spravovanou instanci SQL Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Spravovaná instance Azure SQL musí být nasazená v rámci [virtuální sítě Azure (VNET)](../../virtual-network/virtual-networks-overview.md).

Počet spravovaných instancí SQL, které mohou být nasazeny v podsíti virtuální sítě, závisí na velikosti podsítě (rozsahu podsítě).

Když vytvoříte spravovanou instanci SQL, Azure přidělí několik virtuálních počítačů v závislosti na vrstvě, kterou jste vybrali během zřizování. Vzhledem k tomu, že tyto virtuální počítače jsou přidruženy k vaší podsíti, vyžadují IP adresy. Pro zajištění vysoké dostupnosti během pravidelných operací a údržby služeb může Azure přidělovat další virtuální počítače. V důsledku toho je počet požadovaných IP adres v podsíti větší než počet spravovaných instancí SQL v této podsíti.

V rámci návrhu vyžaduje spravovaná instance SQL minimálně 16 IP adres v podsíti a může používat až 256 IP adres. V důsledku toho můžete při definování rozsahů IP adres podsítí použít masky podsítě mezi/28 a/24. Bitová maska sítě/28 (14 hostitelů na síť) je vhodná pro jedno obecné účely nebo pro důležité obchodní nasazení. Bitová maska/27 (30 hostitelů na síť) je ideální pro nasazení více instancí SQL spravované instance v rámci stejné virtuální sítě. Bitové nastavení maskování/26 (62 hostitelů) a/24 (254 hostitelů) umožňuje další horizontální navýšení kapacity z virtuální sítě na podporu dalších spravovaných instancí SQL.

> [!IMPORTANT]
> Velikost podsítě s 16 IP adresami je minimum s omezeným potenciálem, u kterého není podporována operace škálování, jako je vCore Změna velikosti. Důrazně doporučujeme vybrat podsíť s předponou předpony/27 nebo nejdelší.

## <a name="determine-subnet-size"></a>Určení velikosti podsítě

Pokud máte v úmyslu nasadit několik spravovaných instancí SQL do podsítě a potřebujete optimalizovat velikost podsítě, použijte tyto parametry k vytvoření výpočtu:

- Azure používá v podsíti pět IP adres pro vlastní potřeby.
- Každá instance Pro obecné účely potřebuje dvě adresy.
- Každá instance Pro důležité obchodní informace potřebuje čtyři adresy.

**Příklad**: plánujete mít tři pro obecné účely a dvě pro důležité obchodní informace spravované instance SQL. To znamená, že potřebujete 5 + 3 × 2 + 2 × 4 = 19 IP adres. Jelikož jsou rozsahy IP adres definovány v mocnině 2, potřebujete rozsah IP adres 32 (2 ^ 5) IP adres. Proto je nutné rezervovat podsíť s maskou podsítě/27.

> [!IMPORTANT]
> Výše zobrazené kalkulace se zastaralá s dalšími vylepšeními.

## <a name="next-steps"></a>Další kroky

- Přehled najdete v tématu [co je spravovaná instance SQL](sql-managed-instance-paas-overview.md).
- Přečtěte si další informace o [architektuře připojení pro spravovanou instanci SQL](connectivity-architecture-overview.md).
- Podívejte se, jak [vytvořit virtuální síť, kde budete nasazovat spravované instance SQL](virtual-network-subnet-create-arm-template.md) .
- Problémy se službou DNS najdete v tématu [Konfigurace vlastního serveru DNS](custom-dns-configure.md) .
