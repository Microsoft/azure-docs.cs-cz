---
title: Zálohování a obnovení – snímky, geograficky redundantní
description: Přečtěte si, jak zálohování a obnovení funguje ve vyhrazeném fondu SQL Azure synapse Analytics. Pomocí zálohování obnovte datový sklad do bodu obnovení v primární oblasti. Použijte geograficky redundantní zálohy k obnovení do jiné geografické oblasti.
services: synapse-analytics
author: joannapea
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019"
ms.openlocfilehash: 0fa6777dc5b587150f630ed8ccc110d16448cc21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104602243"
---
# <a name="backup-and-restore-in-azure-synapse-dedicated-sql-pool"></a>Zálohování a obnovení ve vyhrazeném fondu SQL ve službě Azure synapse

Naučte se používat službu Backup a Restore ve vyhrazeném fondu SQL Azure synapse. Pomocí vyhrazených bodů obnovení fondu SQL můžete obnovit nebo zkopírovat datový sklad do předchozího stavu v primární oblasti. Použití geograficky redundantních záloh datového skladu k obnovení do jiné geografické oblasti.

## <a name="what-is-a-data-warehouse-snapshot"></a>Co je snímek datového skladu

*Snímek datového skladu* vytvoří bod obnovení, který můžete využít k obnovení nebo zkopírování datového skladu do předchozího stavu.  Vzhledem k tomu, že vyhrazený fond SQL je distribuovaný systém, se snímek datového skladu skládá z mnoha souborů, které se nacházejí ve službě Azure Storage. Snímky zachytí přírůstkové změny dat uložených v datovém skladu.

*Obnovení datového skladu* je nový datový sklad, který je vytvořen z bodu obnovení existujícího nebo odstraněného datového skladu. Obnovení datového skladu je zásadní součástí jakékoli strategie pro provozní kontinuitu a zotavení po havárii, protože znovu vytvoří data po náhodném poškození nebo odstranění. Datový sklad je také výkonným mechanismem pro vytváření kopií datového skladu pro účely testování nebo vývoje. Vyhrazené míry obnovení fondu SQL se můžou lišit v závislosti na velikosti databáze a umístění zdrojového a cílového datového skladu.

## <a name="automatic-restore-points"></a>Automatické body obnovení

Snímky jsou vestavěnou funkcí, která vytváří body obnovení. Tuto možnost není nutné povolit. Vyhrazený fond SQL by ale měl být v aktivním stavu pro vytvoření bodu obnovení. Pokud je pozastaveno často, nelze vytvořit automatické body obnovení, takže před pozastavením vyhrazeného fondu SQL se musí vytvořit uživatelem definovaný bod obnovení. Automatické body obnovení aktuálně nelze odstranit uživateli, protože služba používá tyto body obnovení k údržbě SLA pro obnovení.

Snímky datového skladu jsou odebírány během celého dne vytváření bodů obnovení, které jsou k dispozici po dobu sedmi dnů. Tuto dobu uchování nelze změnit. Vyhrazený fond SQL podporuje osm hodin cíl bodu obnovení (RPO). Datový sklad můžete obnovit v primární oblasti z libovolného snímku, který jste provedli během posledních sedmi dnů.

Pokud chcete zjistit, kdy byl poslední snímek spuštěný, spusťte tento dotaz ve vašem online vyhrazeném fondu SQL.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Uživatelem definované body obnovení

Tato funkce umožňuje ručně aktivovat snímky pro vytváření bodů obnovení datového skladu před a po velkých změnách. Tato možnost zajistí, že body obnovení jsou logicky konzistentní, což poskytuje dodatečnou ochranu dat v případě přerušení úloh nebo chyby uživatelů pro dobu rychlého obnovení. Uživatelem definované body obnovení jsou k dispozici po dobu sedmi dnů a jsou automaticky odstraněny vaším jménem. Dobu uchování uživatelem definovaných bodů obnovení nelze změnit. **42 uživatelem definované body obnovení** jsou zaručeny v jakémkoli okamžiku, aby je bylo nutné před vytvořením dalšího bodu obnovení [Odstranit](/powershell/module/azurerm.sql/remove-azurermsqldatabaserestorepoint) . Snímky můžete aktivovat pro vytváření uživatelem definovaných bodů obnovení prostřednictvím [PowerShellu](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsont#examples) nebo Azure Portal.

> [!NOTE]
> Pokud potřebujete body obnovení delší než 7 dní, Hlasujte prosím tuto funkci [tady](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points). Můžete také vytvořit uživatelem definovaný bod obnovení a obnovit ho z nově vytvořeného bodu obnovení do nového datového skladu. Po obnovení máte vyhrazený fond SQL online a můžete ho pozastavit nekonečně, aby se ušetřily náklady na výpočetní výkon. Pozastavená databáze má za následek poplatky za úložiště Azure synapse Storage. Pokud potřebujete aktivní kopii obnoveného datového skladu, můžete pokračovat, což by mělo trvat jen několik minut.

### <a name="restore-point-retention"></a>Uchování bodu obnovení

Následující seznam obsahuje podrobné informace o obdobích uchování bodu obnovení:

1. Vyhrazený fond SQL odstraní bod obnovení, když má k dispozici dobu uchování 7 dní **, a** v případě, že jsou k dispozici alespoň 42 celkových bodů obnovení (včetně uživatelsky definovaného i automatického).
2. Snímky se neprovádí při pozastavení vyhrazeného fondu SQL.
3. Stáří bodu obnovení se měří v rámci absolutních kalendářních dnů od doby, kdy se provede bod obnovení, včetně okamžiku pozastavení fondu SQL.
4. V jakémkoli okamžiku je zaručeno, že vyhrazený fond SQL je schopný ukládat až 42 uživatelem definovaných bodů obnovení a 42 bodů obnovení, pokud tyto body obnovení nedosáhly doby uchování 7 dnů.
5. Pokud se snímek povede, vyhrazený fond SQL se pak pozastaví po dobu delší než 7 dní a pak se obnoví, dokud nebude mít 42 celkový počet bodů obnovení (včetně uživatelsky definovaného a automatického).

### <a name="snapshot-retention-when-a-sql-pool-is-dropped"></a>Uchování snímku při vyřazení fondu SQL

Když vyřadíte vyhrazený fond SQL, vytvoří se konečný snímek a uloží se sedm dní. Vyhrazený fond SQL můžete obnovit do konečného bodu obnovení vytvořeného při odstranění. Pokud je vyhrazený fond SQL vyřazený v pozastaveném stavu, žádný snímek se nebere. V takovém případě nezapomeňte před vyřazením vyhrazeného fondu SQL vytvořit uživatelem definovaný bod obnovení.

> [!IMPORTANT]
> Pokud odstraníte Server nebo pracovní prostor hostující vyhrazený fond SQL, odstraní se také všechny databáze patřící k serveru nebo pracovnímu prostoru a nelze je obnovit. Odstraněný Server nelze obnovit.

## <a name="geo-backups-and-disaster-recovery"></a>Geografické zálohování a zotavení po havárii

Geografické zálohování se vytvoří jednou denně pro [spárované datové centrum](../../best-practices-availability-paired-regions.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). RPO pro geografické obnovení je 24 hodin. Geografickou zálohu můžete obnovit na server v libovolné jiné oblasti, kde je podporovaný vyhrazený fond SQL. Geografická záloha zajišťuje, aby bylo možné obnovit datový sklad pro případ, že nebudete mít přístup k bodům obnovení v primární oblasti.

Pokud nepotřebujete pro vyhrazený fond SQL geografickou zálohu, můžete je zakázat a uložit na náklady na úložiště pro zotavení po havárii. Postup najdete v tématu Návod [: zakázání geografického zálohování pro vyhrazený fond SQL (dřív SQL DW)](disable-geo-backup.md). Pamatujte, že pokud se geografické zálohy zakáže, nebudete moci obnovit vyhrazený fond SQL do vaší spárované oblasti Azure, pokud vaše primární datové centrum Azure není k dispozici. 

> [!NOTE]
> Pokud pro geografické zálohy vyžadujete kratší cíl bodu obnovení, Hlasujte [sem](https://feedback.azure.com/forums/307516-sql-data-warehouse)tuto možnost. Můžete také vytvořit uživatelem definovaný bod obnovení a obnovit ho z nově vytvořeného bodu obnovení do nového datového skladu v jiné oblasti. Po obnovení máte datový sklad online a můžete ho pozastavit neomezeně, aby se ušetřily náklady na výpočetní výkon. Pozastavená databáze má za následek poplatky za úložiště za Premium Storageou sazbu za Azure. Pokud potřebujete aktivní kopii datového skladu, můžete pokračovat, což by mělo trvat jen několik minut.

## <a name="data-residency"></a>Rezidence dat 

Pokud se spárované datové centrum nachází mimo vaši zeměpisnou hranici, můžete zajistit, aby data zůstala v rámci geografické hranice tím, že se z geograficky redundantního úložiště zachová. To se dá udělat při zřizování vyhrazeného fondu SQL (dřív SQL DW) prostřednictvím možnosti geograficky redundantního úložiště při vytváření nebo obnovování vyhrazeného fondu SQL (dřív SQL DW). 

Pokud se chcete ujistit, že spárované datové centrum je v jiné zemi, podívejte se na [spárované oblasti Azure](../../best-practices-availability-paired-regions.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="backup-and-restore-costs"></a>Náklady na zálohování a obnovení

Všimněte si, že Azure Bill má položku řádku pro úložiště a položku řádku pro úložiště pro zotavení po havárii. Poplatek za úložiště je celková cena za ukládání dat v primární oblasti společně s přírůstkovým změnami zaznamenanými snímky. Podrobnější vysvětlení, jak se účtují snímky, najdete v tématu  [Vysvětlení způsobu, jakým se účtují poplatky za snímky](/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Geograficky redundantní poplatek pokrývá náklady na ukládání geografických záloh.  

Celkové náklady na váš primární datový sklad a sedm dní pro změny snímku se zaokrouhlují na nejbližší TB. Pokud je například datový sklad 1,5 TB a snímky zachytí 100 GB, bude se vám účtovat 2 TB dat za Azure Premium Storage tarify.

Pokud používáte geografické redundantní úložiště, obdržíte samostatný poplatek za úložiště. Geograficky redundantní úložiště se účtuje ve výši standardní Read-Access geograficky redundantního úložiště (RA-GRS).

Další informace o cenách Azure synapse najdete v článku [ceny Azure synapse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/). Při obnovování v různých oblastech se vám neúčtují odchozí data.

## <a name="restoring-from-restore-points"></a>Obnovení z bodů obnovení

Každý snímek vytvoří bod obnovení, který představuje čas spuštění snímku. Chcete-li obnovit datový sklad, vyberte bod obnovení a proveďte příkaz k obnovení.  

Můžete zachovat obnovený datový sklad a stávající a odstranit jeden z nich. Pokud chcete nahradit aktuální datový sklad obnoveným datovým skladem, můžete ho přejmenovat pomocí [příkazu ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) s možností upravit název.

Pokud chcete obnovit datový sklad, přečtěte si téma [obnovení vyhrazeného fondu SQL](sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-the-azure-portal).

K obnovení odstraněného nebo pozastaveného datového skladu můžete [vytvořit lístek podpory](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="cross-subscription-restore"></a>Obnovení mezi předplatnými

Pokud potřebujete přímo obnovit v rámci předplatného, Hlasujte [sem](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore)pro tuto funkci. Obnovení na jiný server a [přesunutí](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) serveru mezi předplatnými, aby bylo možné provést obnovení mezi předplatnými.

## <a name="geo-redundant-restore"></a>Geograficky redundantní obnovení

[Vyhrazený fond SQL můžete obnovit](sql-data-warehouse-restore-from-geo-backup.md#restore-from-an-azure-geographical-region-through-powershell) do jakékoli oblasti podporující vyhrazený fond SQL na zvolené úrovni výkonu.

> [!NOTE]
> Chcete-li provést geograficky redundantní obnovení, nesmíte mít z této funkce výslovný souhlas.

## <a name="next-steps"></a>Další kroky

Další informace o bodech obnovení najdete v tématu [uživatelem definované body obnovení](sql-data-warehouse-restore-points.md) .