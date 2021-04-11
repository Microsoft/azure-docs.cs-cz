---
title: Čtení replik – Azure Database for PostgreSQL – Citus (škálování)
description: Tento článek popisuje funkci pro čtení repliky v Azure Database for PostgreSQL-Citus (škálování).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: cf8c64bf3858f0d3b7d633b94bc7302fbe563f87
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023947"
---
# <a name="read-replicas-in-azure-database-for-postgresql---hyperscale-citus"></a>Čtení replik v Azure Database for PostgreSQL – Citus (škálování)

> [!IMPORTANT]
> Čtení replik v Citus) je aktuálně ve verzi Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
>
> Úplný seznam dalších nových funkcí najdete v části [funkce ve verzi Preview pro Citus (škálování)](hyperscale-preview-features.md).

Funkce replika čtení umožňuje replikovat data ze skupiny serverů Citus () do skupiny serverů jen pro čtení. Repliky se **asynchronně** aktualizují pomocí technologie fyzické replikace PostgreSQL. Replikaci můžete provést z primárního serveru na neomezený počet replik.

Repliky jsou nové skupiny serverů, které spravujete podobně jako běžné skupiny serverů Citus (). Pro každou repliku čtení se vám bude účtovat zajištěné výpočetní prostředky v virtuální jádra a úložišti v GB/měsíc.

Naučte se [vytvářet a spravovat repliky](howto-hyperscale-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Kdy použít repliku čtení

Funkce replika čtení pomáhá zlepšit výkon a škálu úloh náročných na čtení. Úlohy čtení se dají pro repliky izolovat, zatímco úlohy zápisu můžou být směrované na primární.

Běžným scénářem je, aby úlohy BI a analýzy používaly jako zdroj dat pro vytváření sestav repliku pro čtení.

Vzhledem k tomu, že repliky jsou jen pro čtení, nesnižují přímo na primární úrovni zátěže s kapacitou pro zápis.

### <a name="considerations"></a>Požadavky

Tato funkce je určena pro scénáře, kde je zpoždění replikace přijatelné a je určeno pro zpracování nepřesměrování dotazů. Není určen pro scénáře synchronní replikace, ve kterých se očekává, že data repliky jsou aktuální. Mezi primárním serverem a replikou bude měřitelné zpoždění. Zpoždění může být minuty nebo dokonce hodiny v závislosti na zatížení a latence mezi primárním serverem a replikou.  Data v replice nakonec budou konzistentní s daty na primárním virtuálním počítači. Tato funkce se používá pro úlohy, které můžou toto zpoždění obsloužit. 

## <a name="create-a-replica"></a>Vytvoření repliky

Když spustíte pracovní postup vytvoření repliky, vytvoří se prázdná skupina serverů Citus (Velká měřítko). Nová skupina je vyplněna daty, která byla ve skupině primárních serverů. Čas vytvoření závisí na množství dat na primárním a času od posledního týdenního úplného zálohování. Čas může být v rozsahu od několika minut až po několik hodin.

Funkce replika čtení používá fyzickou replikaci PostgreSQL, ne logickou replikaci. Výchozím režimem je replikace streamování pomocí slotů replikace.
V případě potřeby se k zachytávání použije přenos protokolu.

Naučte se [vytvořit repliku pro čtení v Azure Portal](howto-hyperscale-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Připojení k replice

Když vytváříte repliku, nedědí pravidla brány firewall jako primární skupinu serverů. Tato pravidla musí být pro repliku nastavena nezávisle.

Replika zdědí účet správce ("citus") z primární skupiny serverů.
Všechny uživatelské účty se replikují do replik pro čtení. K replice pro čtení se můžete připojit pouze pomocí uživatelských účtů, které jsou k dispozici na primárním serveru.

K uzlu koordinátora repliky se můžete připojit pomocí jeho názvu hostitele a platného uživatelského účtu, stejně jako v rámci běžné skupiny serverů Citus ().
Pro server s názvem **Moje replika** s uživatelským jménem správce **citus** se můžete připojit k uzlu koordinátora repliky pomocí psql:

```bash
psql -h c.myreplica.postgres.database.azure.com -U citus@myreplica -d postgres
```

Na příkazovém řádku zadejte heslo pro uživatelský účet.

## <a name="considerations"></a>Požadavky

V této části najdete přehled informací o funkci Replika čtení.

### <a name="new-replicas"></a>Nové repliky

Replika čtení je vytvořená jako nová skupina serverů Citus (velká škála). Existující skupinu serverů nelze vytvořit do repliky. Nelze vytvořit repliku jiné repliky pro čtení.

### <a name="replica-configuration"></a>Konfigurace repliky

Replika se vytvoří pomocí stejného nastavení výpočetního, úložného a pracovního uzlu jako primární. Po vytvoření repliky je možné změnit několik nastavení, včetně doby uchování úložiště a zálohy. Jiná nastavení se v replikách nedají změnit, jako je třeba velikost úložiště a počet uzlů pracovních procesů.

Nezapomeňte zajistit, aby repliky byly dostatečně silné, aby se změny projevily na primárním. Například nezapomeňte škálovat výpočetní výkon v replikách, pokud ho nakonfigurujete na primárním virtuálním počítači.

Pravidla brány firewall a nastavení parametrů nejsou děděna z primárního serveru do repliky, když je replika vytvořena nebo následně.

### <a name="regions"></a>Oblasti

Skupiny serverů Citus () podporují pouze replikaci ve stejné oblasti.

## <a name="next-steps"></a>Další kroky

* Naučte se [vytvářet a spravovat repliky pro čtení v Azure Portal](howto-hyperscale-read-replicas-portal.md).
