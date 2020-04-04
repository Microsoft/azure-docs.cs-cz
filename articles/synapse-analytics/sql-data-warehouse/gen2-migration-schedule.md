---
title: Migrace fondu SQL do Gen2
description: Pokyny pro migraci existujícího fondu SQL do Gen2 a plán migrace podle oblasti.
services: synapse-analytics
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: synapse-analytics
ms.topic: article
ms.date: 01/21/2020
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 6f2af826473bfd13f8100796a540d41cbedbb037
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631569"
---
# <a name="upgrade-your-sql-pool-to-gen2"></a>Upgrade fondu SQL na Gen2

Společnost Microsoft pomáhá snížit náklady na základní úroveň spuštění fondu SQL.  Nižší výpočetní úrovně schopné zpracování náročných dotazů jsou teď k dispozici pro fond SQL. Přečtěte si úplné oznámení [Nižší výpočetní úroveň podpory pro Gen2](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/). Nová nabídka je k dispozici v regionech uvedených v následující tabulce. Pro podporované oblasti lze existující fondy Gen1 SQL upgradovat na Gen2 buď pomocí:

- **Proces automatického upgradu:** Automatické upgrady se nespustí, jakmile je služba k dispozici v oblasti.  Při spuštění automatických inovací v určité oblasti budou jednotlivé upgrady datového skladu probíhat během vybraného plánu údržby.
- [**Vlastní upgrade na Gen2:**](#self-upgrade-to-gen2) Můžete určit, kdy upgradovat provedením vlastního upgradu na Gen2. Pokud vaše oblast ještě není podporována, můžete obnovit z bodu obnovení přímo do instance Gen2 v podporované oblasti.

## <a name="automated-schedule-and-region-availability-table"></a>Tabulka automatického plánu a dostupnosti oblastí

Následující tabulka shrnuje podle oblasti, kdy bude k dispozici výpočetní úroveň Nižší Gen2 a kdy se spustí automatické upgrady. Data se mohou změnit. Zkontrolujte, kdy bude oblast dostupná.

\*označuje, že konkrétní plán pro oblast není momentálně k dispozici.

| **Oblasti** | **Nižší Gen2 k dispozici** | **Automatické upgrady začínají** |
|:--- |:--- |:--- |
| Kanada – východ |1. června 2020 |1. července 2020 |
| Čína – východ |\* |\* |
| Čína – sever |\* |\* |
| Německo – střed |\* |\* |
| Německo – západ – střed |K dispozici. |1. května 2020 |
| Indie – západ |K dispozici. |1. května 2020  |

## <a name="automatic-upgrade-process"></a>Proces automatického upgradu

Na základě výše uvedeného grafu dostupnosti budeme plánovat automatické upgrady pro vaše instance Gen1. Chcete-li se vyhnout neočekávaným přerušením dostupnosti fondu SQL, budou automatické inovace naplánovány během plánu údržby. Možnost vytvořit novou instanci Gen1 bude zakázána v oblastech, které procházejí automatickým upgradem na Gen2. Gen1 se po dokončení automatických upgradů zastaral. Další informace o plánech naleznete [v tématu Zobrazení plánu údržby](maintenance-scheduling.md#view-a-maintenance-schedule)

Proces upgradu bude zahrnovat krátký pokles připojení (přibližně 5 min) při restartování fondu SQL.  Po restartování fondu SQL bude plně k dispozici pro použití. Však může dojít ke snížení výkonu při procesu upgradu pokračuje v upgradu datových souborů na pozadí. Celková doba snížení výkonu se bude lišit v závislosti na velikosti datových souborů.

Proces upgradu datového souboru můžete také urychlit spuštěním [funkce Alter Index rebuild](sql-data-warehouse-tables-index.md) ve všech primárních tabulkách columnstore pomocí větší třídy SLO a prostředků po restartování.

> [!NOTE]
> Alter Index znovu sestavit je operace offline a tabulky nebudou k dispozici, dokud znovu sestavit dokončí.

## <a name="self-upgrade-to-gen2"></a>Vlastní upgrade na Gen2

Můžete zvolit vlastní upgrade podle následujících kroků v existujícím fondu Gen1 SQL. Pokud se rozhodnete pro vlastní upgrade, musíte jej dokončit před zahájením procesu automatického upgradu ve vaší oblasti. Tím zajistíte, že se vyhnete riziku, že automatické inovace způsobí konflikt.

Existují dvě možnosti při provádění vlastního upgradu.  Můžete buď upgradovat aktuální fond SQL na místě, nebo můžete obnovit Gen1 SQL fond do instance Gen2.

- [Upgrade na místě](upgrade-to-latest-generation.md) – tato možnost upgraduje stávající fond Gen1 SQL na Gen2. Proces upgradu bude zahrnovat krátký pokles připojení (přibližně 5 min) při restartování fondu SQL.  Po restartování fondu SQL bude plně k dispozici pro použití. Pokud se během upgradu vyskytnou problémy, otevřete [žádost o podporu](sql-data-warehouse-get-started-create-support-ticket.md) a jako možnou příčinu uveďte odkaz na upgrade Gen2.
- [Upgrade z bodu obnovení](sql-data-warehouse-restore-points.md) – vytvořte uživatelem definovaný bod obnovení v aktuálním fondu GEN1 SQL a pak obnovte přímo do instance Gen2. Stávající Gen1 SQL fond zůstane na místě. Po dokončení obnovení bude váš fond Gen2 SQL plně k dispozici pro použití.  Po spuštění všech procesů testování a ověřování na obnovené instanci Gen2 lze původní instanci Gen1 odstranit.

  - Krok 1: Z portálu Azure [vytvořte uživatelem definovaný bod obnovení](sql-data-warehouse-restore-active-paused-dw.md).
  - Krok 2: Při obnovení z uživatelem definovaného bodu obnovení nastavte "úroveň výkonu" na upřednostňovanou úroveň Gen2.

Během toho, co proces upgradu upgraduje datové soubory na pozadí, může na určitou dobu dojít ke snížení výkonu. Celková doba snížení výkonu se bude lišit v závislosti na velikosti datových souborů.

Chcete-li urychlit proces migrace dat na pozadí, můžete okamžitě vynutit přesun dat spuštěním [alter indexu znovu na](sql-data-warehouse-tables-index.md) všech primárních tabulkách columnstore, na které byste se dotazovali na větší třídu SLO a třídy prostředků.

> [!NOTE]
> Alter Index znovu sestavit je operace offline a tabulky nebudou k dispozici, dokud znovu sestavit dokončí.

Pokud narazíte na nějaké problémy s fondem SQL, vytvořte [žádost o podporu](sql-data-warehouse-get-started-create-support-ticket.md) a odkazovat "Gen2 upgrade" jako možnou příčinu.

Další informace naleznete v [tématu Upgrade na Gen2](upgrade-to-latest-generation.md).

## <a name="migration-frequently-asked-questions"></a>Nejčastější dotazy k migraci

**Otázka: Stojí Gen2 stejně jako Gen1?**

- Odpověď: Ano.

**Otázka: Jak budou upgrady mít vliv na mé automatizační skripty?**

- A: Všechny automatizační skript, který odkazuje na cíl na úrovni služby by mělbýt změněn tak, aby odpovídaly gen2 ekvivalent.  Podrobnosti naleznete [zde](upgrade-to-latest-generation.md#upgrade-in-a-supported-region-using-the-azure-portal).

**Otázka: Jak dlouho trvá vlastní upgrade obvykle trvá?**

- A: Můžete upgradovat na místě nebo upgrade z bodu obnovení.

  - Upgrade na místě způsobí, že váš fond SQL na okamžik pozastavit a pokračovat.  Proces na pozadí bude pokračovat, zatímco fond SQL je online.  
  - Pokud provádíte upgrade prostřednictvím bodu obnovení, trvá déle, protože upgrade projde procesem úplného obnovení.

**Otázka: Jak dlouho bude automatický upgrade trvat?**

- A: Skutečné prostoje pro upgrade je pouze čas potřebný k pozastavení a obnovení služby, která je mezi 5 až 10 minut. Po krátkém výpadku proces na pozadí spustí migraci úložiště. Doba pro proces na pozadí závisí na velikosti fondu SQL.

**Otázka: Kdy bude tento automatický upgrade probíhat?**

- A: Během plánu údržby. Využití zvoleného plánu údržby minimalizuje narušení vašeho podnikání.

**Otázka: Co mám dělat, když se zdá, že se proces upgradu na pozadí zasekl?**

- A: Kick off reindex tabulky Columnstore. Všimněte si, že přeindexování tabulky bude během této operace offline.

**Otázka: Co když Gen2 nemá cíl úrovně služeb, který mám na Gen1?**

- A: Pokud používáte DW600 nebo DW1200 na Gen1, doporučuje se použít DW500c nebo DW1000c, protože Gen2 poskytuje více paměti, prostředky a vyšší výkon než Gen1.

**Otázka: Mohu zakázat geografické zálohování?**

- Odpověď: Ne. Geografické zálohování je podniková funkce pro zachování dostupnosti fondu SQL v případě, že oblast nebude k dispozici. Pokud máte další obavy, otevřete [žádost o podporu.](sql-data-warehouse-get-started-create-support-ticket.md)

**Otázka: Existuje rozdíl v syntaxi T-SQL mezi Gen1 a Gen2?**

- A: Neexistuje žádná změna v syntaxi jazyka T-SQL z Gen1 na Gen2.

**Otázka: Podporuje Gen2 údržbu Windows?**

- Odpověď: Ano.

**Otázka: Budu moci vytvořit novou instanci Gen1 po upgradu oblasti?**

- Odpověď: Ne. Po upgradu oblasti bude zakázáno vytváření nových instancí Gen1.

## <a name="next-steps"></a>Další kroky

- [Kroky upgradu](upgrade-to-latest-generation.md)
- [Časové intervaly pro správu a údržbu](maintenance-scheduling.md)
- [Monitorování stavu prostředků](../../service-health/resource-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
- [Revize Před zahájením migrace](upgrade-to-latest-generation.md#before-you-begin)
- [Upgrade na místě a upgrade z bodu obnovení](upgrade-to-latest-generation.md)
- [Vytvoření uživatelem definovaného bodu obnovení](sql-data-warehouse-restore-points.md)
- [Naučte se, jak obnovit Gen2](sql-data-warehouse-restore-active-paused-dw.md)
- [Otevření žádosti o podporu datového skladu SQL](https://go.microsoft.com/fwlink/?linkid=857950)
