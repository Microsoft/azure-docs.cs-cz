---
title: Určení požadované velikosti podsítě & rozsahu
titleSuffix: Azure SQL Managed Instance
description: Toto téma popisuje, jak vypočítat velikost podsítě, do které se nasadí spravovaná instance Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 02/22/2019
ms.openlocfilehash: 156a4c74eea24b20c28df88be85cb32c0ebe2981
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91617634"
---
# <a name="determine-required-subnet-size--range-for-azure-sql-managed-instance"></a>Určení požadované velikosti podsítě & rozsahu pro spravovanou instanci SQL Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Spravovaná instance Azure SQL musí být nasazená v rámci [virtuální sítě Azure (VNET)](../../virtual-network/virtual-networks-overview.md).

Počet spravovaných instancí, které mohou být nasazeny v podsíti virtuální sítě, závisí na velikosti podsítě (rozsahu podsítě).

Při vytváření spravované instance Azure přiděluje počet virtuálních počítačů v závislosti na vrstvě, kterou jste vybrali během zřizování. Vzhledem k tomu, že tyto virtuální počítače jsou přidruženy k vaší podsíti, vyžadují IP adresy. Pro zajištění vysoké dostupnosti během pravidelných operací a údržby služeb může Azure přidělovat další virtuální počítače. V důsledku toho je počet požadovaných IP adres v podsíti větší než počet spravovaných instancí v této podsíti.

Spravovaná instance v podsíti záměrně vyžaduje minimálně 32 IP adres. Proto při definování rozsahů IP adres podsítě můžete jako minimální masku podsítě použít /27. Doporučujeme pečlivě naplánovat velikost podsítě pro vaše nasazení spravované instance. Vstupy, které by se měly vzít v úvahu během plánování:

- Počet spravovaných instancí včetně následujících parametrů instance:
  - úroveň služby
  - generování hardwaru
  - počet virtuální jádra
- Plány pro horizontální navýšení nebo snížení kapacity nebo změna úrovně služeb

> [!IMPORTANT]
> Velikost podsítě s 16 IP adresami (maska podsítě/28) umožní nasazení spravované instance uvnitř ní, ale měla by se používat jenom pro nasazení jedné instance používané pro vyhodnocení nebo ve scénářích pro vývoj a testování, ve kterých se operace škálování neprovede.

## <a name="determine-subnet-size"></a>Určení velikosti podsítě

Velikost podsítě proveďte v souladu s budoucím nasazením a škálováním instance. Následující parametry vám pomůžou při vytváření výpočtu:

- Azure používá v podsíti pět IP adres pro vlastní potřeby.
- Každý virtuální cluster přiděluje další počet adres. 
- Každá spravovaná instance používá počet adres, které závisí na cenové úrovni a generaci hardwaru.

> [!IMPORTANT]
> Rozsah adres podsítě není možné změnit, pokud existuje libovolný prostředek v podsíti. Je také možné přesunout spravované instance z jedné podsítě do jiné. Kdykoli je to možné, zvažte použití větších podsítí místo menších, aby v budoucnu nedocházelo k problémům.

GP = obecné účely; BC = kritické pro podnikání; VC = virtuální cluster

| **Hardware – obecné** | **Cenová úroveň** | **Využití Azure** | **Využití VC** | **Použití instance** | **Čtení*** |
| --- | --- | --- | --- | --- | --- |
| COMPUTE GEN4 – | GP | 5 | 1 | 5 | 11 |
| COMPUTE GEN4 – | BC | 5 | 1 | 5 | 11 |
| Gen5 | GP | 5 | 6 | 3 | 14 |
| Gen5 | BC | 5 | 6 | 5 | 16 |

  \* Sloupec celkem zobrazuje počet adres, které se provedou při nasazení jedné instance v podsíti. Každá další instance v podsíti přidá počet adres představovaných sloupcem použití instance. Adresy reprezentované sloupcem využití Azure se sdílejí napříč několika virtuálními clustery a adresy reprezentované sloupcem využití VC se sdílejí mezi instancemi, které jsou umístěné v daném virtuálním clusteru.

Operace aktualizace obvykle vyžaduje změnu velikosti virtuálního clusteru. V některých případech bude operace aktualizace vyžadovat vytvoření virtuálního clusteru (další podrobnosti najdete v [článku o operacích správy](sql-managed-instance-paas-overview.md#management-operations)). V případě vytvoření virtuálního clusteru se počet požadovaných dalších adres rovná počtu adres, které jsou reprezentované sloupcem využití VC, sečtený s adresami vyžadovanými pro instance uložené ve virtuálním clusteru (sloupec použití instance).

**Příklad 1**: plánujete mít jednu spravovanou spravovanou instanci (COMPUTE GEN4 – hardware) a jednu z důležitých podnikových spravovaných instancí (Gen5 hardware). To znamená, že potřebujete minimálně 5 + 1 + 1 * 5 + 6 + 1 * 5 = 22 IP adres, které je možné nasadit. Vzhledem k tomu, že rozsahy IP adres jsou definované ve mocnině 2, vyžaduje vaše podsíť minimální rozsah IP adres 32 (2 ^ 5) pro toto nasazení.<br><br>
Jak je uvedeno výše, v některých případech bude operace aktualizace vyžadovat vytvoření virtuálního clusteru. To znamená, že jako příklad v případě aktualizace Gen5 spravovaného podniku, který vyžaduje vytvoření virtuálního clusteru, budete muset mít k dispozici další 6 + 5 = 11 IP adres. Vzhledem k tomu, že již používáte 22 adres 32, nejsou k dispozici žádné adresy pro tuto operaci. Proto je nutné rezervovat podsíť s maskou podsítě/26 (64 adres).

**Příklad 2**: plánujete mít tři obecné účely (Gen5 hardware) a dvě obchodní důležité spravované instance (Gen5 hardware) umístěné ve stejné podsíti. To znamená, že potřebujete 5 + 6 + 3 * 3 + 2 * 5 = 30 IP adres. Proto je nutné rezervovat podsíť s maskou podsítě/26. Výběr masky podsítě/27 způsobí, že zbývající počet adres bude 2 (32 – 30), což zabrání operacím aktualizace pro všechny instance, protože v podsíti jsou pro provádění škálování instancí vyžadovány další adresy.

**Příklad 3**: plánujete mít jednu spravovanou spravovanou instanci (Gen5 hardware) a provádět operaci aktualizace virtuální jádra v čase. To znamená, že potřebujete 5 + 6 + 1 * 3 + 3 = 17 IP adres. Jelikož jsou rozsahy IP adres definovány v mocnině 2, potřebujete rozsah IP adres 32 (2 ^ 5) IP adres. Proto je nutné rezervovat podsíť s maskou podsítě/27.

### <a name="address-requirements-for-update-scenarios"></a>Požadavky na adresy pro scénáře aktualizací

Během škálování instancí operací se dočasně vyžaduje další kapacita IP, která závisí na cenové úrovni a na generaci hardwaru.

| **Hardware – obecné** | **Cenová úroveň** | **Scénář** | **Další adresy*** |
| --- | --- | --- | --- |
| COMPUTE GEN4 – | GP nebo BC | Škálování virtuální jádra | 5 |
| COMPUTE GEN4 – | GP nebo BC | Škálování úložiště | 5 |
| COMPUTE GEN4 – | GP nebo BC | Přepínání z GP na BC nebo BC na GP | 5 |
| COMPUTE GEN4 – | GP | Přepínání na Gen5 * | 9 |
| COMPUTE GEN4 – | BC | Přepínání na Gen5 * | 11 |
| Gen5 | GP | Škálování virtuální jádra | 3 |
| Gen5 | GP | Škálování úložiště | 0 |
| Gen5 | GP | Přepnutí na BC | 5 |
| Gen5 | BC | Škálování virtuální jádra | 5 |
| Gen5 | BC | Škálování úložiště | 5 |
| Gen5 | BC | Přechod na GP | 3 |

  \* COMPUTE GEN4 – hardware se rozchází a už není k dispozici pro nová nasazení. Aktualizujte generaci hardwaru z COMPUTE GEN4 – na Gen5, abyste mohli využívat výhody, které jsou specifické pro Gen5 generaci hardwaru.

## <a name="next-steps"></a>Další kroky

- Přehled najdete v tématu [co je Azure SQL Managed instance?](sql-managed-instance-paas-overview.md).
- Přečtěte si další informace o [architektuře připojení pro spravovanou instanci SQL](connectivity-architecture-overview.md).
- Podívejte se, jak [vytvořit virtuální síť, ve které budete nasazovat spravovanou instanci SQL](virtual-network-subnet-create-arm-template.md).
- Problémy se službou DNS najdete v tématu [Konfigurace vlastního serveru DNS](custom-dns-configure.md).
