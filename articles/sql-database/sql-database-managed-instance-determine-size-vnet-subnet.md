---
title: Azure SQL Database Managed instance – určení velikosti virtuální sítě/podsítě | Microsoft Docs
description: Toto téma popisuje, jak vypočítat velikost podsítě, do které se nasadí Azure SQL Database spravované instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 02/22/2019
ms.openlocfilehash: 4b627b13fb79cd5105a95d9161d9239f28f2e062
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567502"
---
# <a name="determine-vnet-subnet-size-for-azure-sql-database-managed-instance"></a>Určení velikosti podsítě virtuální sítě pro Azure SQL Database spravovanou instanci

Azure SQL Database spravovaná instance musí být nasazená v rámci [virtuální sítě Azure (VNET)](../virtual-network/virtual-networks-overview.md).

Počet spravovaných instancí, které mohou být nasazeny v podsíti virtuální sítě, závisí na velikosti podsítě (rozsahu podsítě).

Při vytváření spravované instance Azure přiděluje počet virtuálních počítačů v závislosti na vrstvě, kterou jste vybrali během zřizování. Vzhledem k tomu, že tyto virtuální počítače jsou přidruženy k vaší podsíti, vyžadují IP adresy. Pro zajištění vysoké dostupnosti během pravidelných operací a údržby služeb může Azure přidělovat další virtuální počítače. V důsledku toho je počet požadovaných IP adres v podsíti větší než počet spravovaných instancí v této podsíti.

V rámci návrhu vyžaduje spravovaná instance minimálně 16 IP adres v podsíti a může používat až 256 IP adres. V důsledku toho můžete při definování rozsahů IP adres podsítí použít masky podsítě mezi/28 a/24. Bitová maska sítě/28 (14 hostitelů na síť) je vhodná pro jedno obecné účely nebo pro důležité obchodní nasazení. Bitová maska/27 (30 hostitelů na síť) je ideální pro nasazení více spravovaných instancí v rámci stejné virtuální sítě. Bitové nastavení maskování/26 (62 hostitelů) a/24 (254 hostitelů) umožňuje další škálování z virtuální sítě na podporu dalších spravovaných instancí.

> [!IMPORTANT]
> Velikost podsítě s 16 IP adresami je minimum s omezeným potenciálem pro další navýšení kapacity spravované instance. Volba podsítě s předponou/27 nebo níže se důrazně doporučuje.

## <a name="determine-subnet-size"></a>Určení velikosti podsítě

Pokud plánujete nasadit více spravovaných instancí v rámci podsítě a potřebujete optimalizovat velikost podsítě, použijte tyto parametry k vytvoření výpočtu:

- Azure používá v podsíti pět IP adres pro vlastní potřeby.
- Každá instance Pro obecné účely potřebuje dvě adresy.
- Každá instance Pro důležité obchodní informace potřebuje čtyři adresy.

**Příklad**: Plánujete mít tři Pro obecné účely a dvě Pro důležité obchodní informace spravované instance. To znamená, že potřebujete 5 + 3 × 2 + 2 × 4 = 19 IP adres. Jelikož jsou rozsahy IP adres definovány v mocnině 2, potřebujete rozsah IP adres 32 (2 ^ 5) IP adres. Proto je nutné rezervovat podsíť s maskou podsítě/27.

> [!IMPORTANT]
> Výše zobrazené kalkulace se zastaralá s dalšími vylepšeními.

## <a name="next-steps"></a>Další kroky

- Přehled najdete v tématu [co je spravovaná instance](sql-database-managed-instance.md).
- Přečtěte si další informace o [architektuře připojení pro spravovanou instanci](sql-database-managed-instance-connectivity-architecture.md).
- Podívejte se, jak [vytvořit virtuální síť, kde nasadíte spravované instance](sql-database-managed-instance-create-vnet-subnet.md) .
- Problémy se službou DNS najdete v tématu [Konfigurace vlastního serveru DNS](sql-database-managed-instance-custom-dns.md) .
