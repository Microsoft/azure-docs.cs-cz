---
title: Určit velikost virtuálních sítí/podsítí, Azure SQL Database Managed Instance | Dokumentace Microsoftu
description: Toto téma popisuje, jak vypočítat velikost podsítě, ve kterém se nasadí Azure SQL Database Managed instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 01/04/2019
ms.openlocfilehash: 12b0690c7653b03c8099253bee509a79a2ae2600
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55561854"
---
# <a name="determine-vnet-subnet-size-for-azure-sql-database-managed-instance"></a>Určit velikost podsítě virtuální sítě pro Azure SQL Database Managed Instance

Azure SQL Database Managed Instance musí být nasazen v rámci Azure [virtuální síť (VNet)](../virtual-network/virtual-networks-overview.md).

Počet spravovaných instancí, který je možné nasadit v podsíti virtuální sítě závisí na velikosti podsítě (rozsahu podsítě).

Když vytvoříte Managed Instance, Platforma Azure přiřadí počet virtuálních počítačů v závislosti na úroveň, na kterou jste zvolili během zřizování. Protože tyto virtuální počítače jsou spojeny s vaší podsítě, vyžadují IP adresy. K zajištění vysoké dostupnosti během standardních operací a údržby služby, může Azure přidělte další virtuální počítače. V důsledku toho počet požadované IP adresy v podsíti je větší než počet spravovaných instancí v této podsíti.

Návrh Managed Instance vyžaduje minimálně 16 IP adresy v podsíti a může použít až 256 IP adresy. V důsledku toho můžete použít masky podsítě velikosti/28 do /24 při definování rozsahy IP adres vaší podsítě.

> [!IMPORTANT]
> Velikost podsítě s IP adresami 16 je minimum, s omezenou potenciál pro další Managed Instance horizontální navýšení kapacity. Když zvolíte podsítě s předponou velikost/27 nebo pod důrazně doporučujeme.

## <a name="determine-subnet-size"></a>Určit velikost podsítě

Pokud budete chtít nasadit víc instancí v podsíti spravované a třeba optimalizovat na velikost podsítě, použijte k výpočtu tyto parametry:

- Azure používá 5 IP adres v podsíti pro vlastní potřeby
- Každá instance pro obecné účely potřebuje dvě adresy
- Každá instance pro důležité obchodní informace potřebuje čtyři adresy

**Příklad**: Budete mít tři pro obecné účely a dvě obchodní kritické Managed instance. Že znamená, že potřebujete 5 + 3 * 2 + 2 * 4 = 19 IP adresy. Když rozsahy IP adres jsou definovány v Power BI 2, budete potřebovat IP rozsahu 32 (2 ^ 5) IP adresy. Proto musíte rezervovat podsíť s maskou podsítě/27.

> [!IMPORTANT]
> Výpočet zobrazený nad bude stát zastaralým další vylepšení.

## <a name="next-steps"></a>Další postup

- Přehled najdete v tématu [co je Managed Instance](sql-database-managed-instance.md).
- Další informace o [připojení architektury pro Managed Instance](sql-database-managed-instance-connectivity-architecture.md).
- V tématu Jak [vytvořte virtuální síť, kde budete nasazovat spravované instance](sql-database-managed-instance-create-vnet-subnet.md)
- Problémy s DNS, najdete v článku [konfigurace vlastního DNS](sql-database-managed-instance-custom-dns.md)
