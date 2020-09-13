---
title: Škálování skupiny serverů – Citus (Scale-Scale) – Azure Database for PostgreSQL
description: Úprava paměti, disku a prostředků procesoru skupiny serverů, aby bylo možné řešit zvýšené zatížení
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 8/10/2020
ms.openlocfilehash: 10d253b3e470ea009944d4ccd756d4bbbe3766e7
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90029813"
---
# <a name="server-group-size"></a>Velikost skupiny serverů

Možnost nasazení Citus () používá spolupracující databázové servery k paralelizovat provádění dotazů a ukládání dalších dat. Skupina serverů "velikost" odkazuje jak na počet serverů, tak na hardwarové prostředky.

## <a name="picking-initial-size"></a>Počáteční velikost výběru

Velikost skupiny serverů, s ohledem na počet uzlů a jejich hardwarovou kapacitu, se snadno mění ([viz níže](#scale-a-hyperscale-citus-server-group)). Pořád ale musíte zvolit počáteční velikost pro novou skupinu serverů. Zde jsou některé tipy pro rozumnou volbu.

### <a name="multi-tenant-saas-use-case"></a>Multi-tenant SaaS – případ použití

Pro ty, které se migrují na Citus (a) z existující instance databáze PostgreSQL s jedním uzlem, doporučujeme vybrat cluster, ve kterém je celkový počet pracovních virtuální jádraů a paměti RAM v celkovém rozsahu stejný jako původní instance. V takových scénářích jsme viděli 3x vylepšení výkonu, protože horizontálního dělení vylepšuje využití prostředků a povoluje menší indexy atd.

Počet virtuální jádra vyžadovaných pro uzel koordinátora závisí na stávajícím zatížení (propustnost zápisu/čtení). Uzel koordinátora nevyžaduje jako pracovní uzly tolik paměti RAM, ale přidělování paměti RAM se určuje na základě počtu vCore (jak je popsáno v [možnostech konfigurace pro škálování na úrovni](concepts-hyperscale-configuration-options.md)cloudu), takže počet Vcore je v podstatě reálné rozhodnutí.

### <a name="real-time-analytics-use-case"></a>Případ použití analýzy v reálném čase

Total virtuální jádra: při práci s daty v paměti RAM můžete očekávat lineární vylepšení výkonu na úrovni Citus (s poměrem k počtu jader pracovních procesů). Pro určení správného počtu virtuální jádra pro vaše potřeby zvažte aktuální latenci pro dotazy v databázi s jedním uzlem a požadovanou latenci v rámci škálování (Citus). Aktuální latenci vydělte požadovanou latencí a výsledek zaokrouhlete.

Pracovní paměť RAM: Ideální by bylo zajistit dostatek paměti, aby se do ní vešla pracovní sady. Typ dotazů, které vaše aplikace používá, ovlivňuje požadavky na paměť. Pokud chcete zjistit, kolik paměti vyžaduje, můžete spustit příkaz vysvětlit analyzovat na dotazu. Pamatujte, že virtuální jádra a paměť RAM se škálují společně, jak je popsáno v článku [Možnosti konfigurace pro škálování na úrovni](concepts-hyperscale-configuration-options.md) .

## <a name="scale-a-hyperscale-citus-server-group"></a>Škálování skupiny serverů Citus (Scale-Scale)

Azure Database for PostgreSQL – Citus () zajišťuje škálování samoobslužných služeb pro řešení zvýšeného zatížení. Azure Portal usnadňuje přidávání nových pracovních uzlů a zvýšení virtuální jádra stávajících uzlů. Přidáním uzlů dojde k žádnému výpadku a dokonce i přesunutí horizontálních oddílů do nových uzlů (nazývaných [horizontálních oddílů rebalancing](#rebalance-shards)) proběhne bez přerušení dotazů.

### <a name="add-worker-nodes"></a>Přidat pracovní uzly

Pokud chcete přidat uzly, klikněte na kartu **COMPUTE + Storage** ve skupině serverů Citus (škálování).  Přetažením posuvníku **počtu pracovních uzlů** se změní hodnota.

![Posuvníky prostředků](./media/howto-hyperscale-scaling/01-sliders-workers.png)

Klikněte na tlačítko **Uložit** , aby se změna hodnoty projevila.

> [!NOTE]
> Po zvýšení a uložení nelze počet uzlů pracovních procesů snížit pomocí posuvníku.

#### <a name="rebalance-shards"></a>Vyvážit horizontálních oddílů

Aby bylo možné využít nově přidaných uzlů, je nutné znovu vyrovnávat distribuovanou tabulku [horizontálních oddílů](concepts-hyperscale-distributed-data.md#shards), což znamená přesunutí některých horizontálních oddílů z existujících uzlů do nových. Nejdřív ověřte, že nové pracovní procesy úspěšně dokončily zřizování. Pak spusťte nástroj horizontálních oddílů pro vyrovnávání zatížení tak, že se připojíte k uzlu koordinátora clusteru s psql a spuštěným:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

`rebalance_table_shards`Funkce znovu vyrovnává všechny tabulky ve skupině [kolocation](concepts-hyperscale-colocation.md) v tabulce s názvem v její argumentu. Proto nemusíte volat funkci pro každou distribuovanou tabulku, stačí ji volat na reprezentativní tabulku z každé skupiny kolokace.

### <a name="increase-or-decrease-vcores-on-nodes"></a>Zvětšení nebo zmenšení virtuální jádra na uzlech

> [!NOTE]
> Tato funkce je aktuálně ve verzi Preview. Pokud chcete požádat o změnu v virtuální jádra pro uzly ve skupině serverů, obraťte se prosím na [podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Kromě přidávání nových uzlů můžete zvýšit možnosti existujících uzlů. Navýšení a snížení kapacity výpočetní kapacity může být užitečné při experimentech s výkonem a na krátkodobé nebo dlouhodobé změny v požadavcích na provoz.

Chcete-li změnit virtuální jádra pro všechny pracovní uzly, upravte posuvník **virtuální jádra** v části **konfigurace (na jeden pracovní uzel)**. Virtuální jádra uzlu koordinátora se dá upravovat nezávisle. V části **konfigurace (uzel koordinátora)** upravte posuvník **virtuální jádra** .

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [možnostech výkonu](concepts-hyperscale-configuration-options.md)skupiny serverů.
