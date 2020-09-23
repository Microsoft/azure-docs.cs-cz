---
title: Koncepty pro distribuci dat a horizontální navýšení kapacity pomocí PostgreSQL skupiny serverů s podporou ARC
titleSuffix: Azure Arc enabled data services
description: Koncepty pro distribuci dat pomocí PostgreSQL skupiny serverů s podporou ARC
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: c01da4aed9e27296ea7b570420bb190b16749848
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936379"
---
# <a name="concepts-for-distributing-data-with-arc-enabled-postgresql-hyperscale-server-group"></a>Koncepty pro distribuci dat pomocí PostgreSQL skupiny serverů s podporou ARC

Tento článek vysvětluje klíčové koncepty, které jsou důležité k tomu, aby bylo možné těžit z nejvyšší škály PostgreSQL s povoleným rozšířením Azure ARC.
Články odkazované níže odkazují na koncepty Azure Database for PostgreSQL Citus (škálování na úrovni). Je to stejná technologie jako Azure ARC s povoleným PostgreSQLm škálováním, aby se mohly použít stejné koncepty a perspektivy.

**Jaký je rozdíl mezi nimi?**
- _Azure Database for PostgreSQL Hyperscale (Citus)_

Toto je faktor formy Postgres databázového stroje, který je k dispozici jako služba v Azure (PaaS). Využívá rozšíření Citus, které umožňuje prostředí s technologií škálování. V tomto formuláři je služba spuštěna v datových centrech společnosti Microsoft a je provozována společností Microsoft.

- _PostgreSQL s povoleným škálováním Azure ARC_

Toto je měřítko formuláře Postgres Database Engine nabízené k dispozici pro datovou službu s podporou ARC Azure. V tomto formuláři poskytují naši zákazníci infrastrukturu, která je hostitelem systémů a pracuje s nimi.

Tady jsou shrnuté klíčové pojmy kolem PostgreSQL s povoleným rozšířením Azure ARC:

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="nodes-and-tables"></a>Uzly a tabulky
Je důležité, abyste měli informace o následujících konceptech, abyste využili maximum Postgres s povoleným rozšířením Azure ARC:
- Specializované Postgres uzly v PostgreSQL s povoleným rozšířením Azure ARC: koordinátoři a pracovníci
- Typy tabulek: distribuované tabulky, referenční tabulky a místní tabulky
- Horizontálních oddílů

Další informace najdete v tématu [uzly a tabulky v Azure Database for PostgreSQL – Citus (škálování)](../../postgresql/concepts-hyperscale-nodes.md). 

## <a name="determine-the-application-type"></a>Určení typu aplikace
Je důležité jasně identifikovat typ aplikace, kterou vytváříte. Proč? Vzhledem k tomu, že spouštění efektivních dotazů na PostgreSQL skupině s povoleným rozšířením Azure ARC vyžaduje, aby byly tabulky správně distribuovány mezi servery. Doporučená distribuce se liší podle typu aplikace a jejích vzorů dotazů. Existují široké dva druhy aplikací, které dobře fungují na Postgres škálování na úrovni Azure ARC:
- Víceklientské aplikace
- Aplikace v reálném čase

Prvním krokem při modelování dat je určení, které z nich je lépe podobá vaší aplikaci.

Podívejte se na podrobnosti o [určení typu aplikace](../../postgresql/concepts-hyperscale-app-type.md).


## <a name="choose-a-distribution-column"></a>Volba distribučního sloupce
Proč zvolit distribuovaný sloupec?

Jedná se o jedno z nejdůležitějších rozhodnutí o modelování, která provedete. PostgreSQL se zapnutým rozšířením Azure ARC ukládá v horizontálních oddílů řádky na základě hodnoty distribučního sloupce řádky. Správné skupiny voleb vztahující se k datům společně na stejných fyzických uzlech, které urychlují dotazy a přidávají podporu pro všechny funkce SQL. Nesprávná volba způsobí, že systém pracuje pomalu a nepodporuje všechny funkce SQL napříč uzly. Tento článek obsahuje popisy pro distribuční sloupce dvou nejběžnějších scénářů pro práci s škálovatelnými škálováními.

V části [Vybrat distribuční sloupce](../../postgresql/concepts-hyperscale-choose-distribution-column.md)si přečtěte podrobnosti.


## <a name="table-colocation"></a>Společné umístění tabulek

Společné umístění je informace o ukládání souvisejících informací společně na stejných uzlech. Dotazy můžou být rychlé, pokud jsou všechna potřebná data dostupná bez jakýchkoli síťových přenosů. Spolulokalizace souvisejících dat na různých uzlech umožňuje, aby dotazy byly efektivně spouštěny paralelně na jednotlivých uzlech.

Podívejte se na podrobnosti v [tabulce kolokace](../../postgresql/concepts-hyperscale-colocation.md).


## <a name="next-steps"></a>Další kroky
- [Přečtěte si o vytváření PostgreSQL škálování s povoleným rozšířením Azure ARC.](create-postgresql-hyperscale-server-group.md)
- [Přečtěte si o škálování povolených PostgreSQL skupin serverů s rozšířením Azure ARC, které jsou vytvořené v řadiči dat ARC.](scale-out-postgresql-hyperscale-server-group.md)
- [Přečtěte si o Data Services s podporou ARC Azure](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
- [Přečtěte si o ARC Azure](https://aka.ms/azurearc)

