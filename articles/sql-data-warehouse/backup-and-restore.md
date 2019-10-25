---
title: Azure SQL Data Warehouse zálohování a obnovení – snímky, geograficky redundantní | Microsoft Docs
description: Přečtěte si, jak zálohování a obnovení funguje v Azure SQL Data Warehouse. Pomocí záloh datového skladu obnovte datový sklad na bod obnovení v primární oblasti. Použijte geograficky redundantní zálohy k obnovení do jiné geografické oblasti.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 10/21/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 1cf6444b155830326f4876d2d65bcdaa5923fc35
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72788817"
---
# <a name="backup-and-restore-in-azure-sql-data-warehouse"></a>Zálohování a obnovení v Azure SQL Data Warehouse

Naučte se používat Backup a Restore v Azure SQL Data Warehouse. Pomocí bodů obnovení datového skladu můžete obnovit nebo zkopírovat datový sklad do předchozího stavu v primární oblasti. Použití geograficky redundantních záloh datového skladu k obnovení do jiné geografické oblasti.

## <a name="what-is-a-data-warehouse-snapshot"></a>Co je snímek datového skladu

*Snímek datového skladu* vytvoří bod obnovení, který můžete využít k obnovení nebo zkopírování datového skladu do předchozího stavu.  Vzhledem k tomu, že SQL Data Warehouse je distribuovaný systém, se snímek datového skladu skládá z mnoha souborů, které se nacházejí ve službě Azure Storage. Snímky zachytí přírůstkové změny dat uložených v datovém skladu.

*Obnovení datového skladu* je nový datový sklad, který je vytvořen z bodu obnovení existujícího nebo odstraněného datového skladu. Obnovení datového skladu je zásadní součástí jakékoli strategie pro provozní kontinuitu a zotavení po havárii, protože znovu vytvoří data po náhodném poškození nebo odstranění. Datový sklad je také výkonným mechanismem pro vytváření kopií datového skladu pro účely testování nebo vývoje.  Frekvence obnovení SQL Data Warehouse se může lišit v závislosti na velikosti databáze a umístění zdrojového a cílového datového skladu. 

## <a name="automatic-restore-points"></a>Automatické body obnovení

Snímky jsou vestavěnou funkcí služby, která vytváří body obnovení. Tuto možnost není nutné povolit. Datový sklad by však měl být v aktivním stavu pro vytvoření bodu obnovení. Pokud je datový sklad často pozastaven, nelze vytvořit automatické body obnovení, takže před pozastavením datového skladu nezapomeňte vytvořit uživatelem definovaný bod obnovení. Automatické body obnovení aktuálně nelze odstranit uživateli, protože služba používá tyto body obnovení k údržbě SLA pro obnovení.

SQL Data Warehouse pořizování snímků datového skladu během dne vytváření bodů obnovení, které jsou k dispozici po dobu sedmi dnů. Tuto dobu uchování nelze změnit. SQL Data Warehouse podporuje osm hodin cíl bodu obnovení (RPO). Datový sklad můžete obnovit v primární oblasti z libovolného snímku, který jste provedli během posledních sedmi dnů.

Pokud chcete zjistit, kdy byl poslední snímek spuštěný, spusťte tento dotaz na online SQL Data Warehouse.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Uživatelem definované body obnovení

Tato funkce umožňuje ručně aktivovat snímky pro vytváření bodů obnovení datového skladu před a po velkých změnách. Tato možnost zajistí, že body obnovení jsou logicky konzistentní, což poskytuje dodatečnou ochranu dat v případě přerušení úloh nebo chyby uživatelů pro dobu rychlého obnovení. Uživatelem definované body obnovení jsou k dispozici po dobu sedmi dnů a jsou automaticky odstraněny vaším jménem. Dobu uchování uživatelem definovaných bodů obnovení nelze změnit. **42 uživatelem definované body obnovení** jsou zaručeny v jakémkoli okamžiku, aby je bylo nutné před vytvořením dalšího bodu obnovení [Odstranit](https://go.microsoft.com/fwlink/?linkid=875299) . Snímky můžete aktivovat pro vytváření uživatelem definovaných bodů obnovení prostřednictvím [PowerShellu](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint#examples) nebo Azure Portal.

> [!NOTE]
> Pokud potřebujete body obnovení delší než 7 dní, Hlasujte prosím tuto funkci [tady](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points). Můžete také vytvořit uživatelem definovaný bod obnovení a obnovit ho z nově vytvořeného bodu obnovení do nového datového skladu. Po obnovení máte datový sklad online a můžete ho pozastavit neomezeně, aby se ušetřily náklady na výpočetní výkon. Pozastavená databáze má za následek poplatky za úložiště za Premium Storageou sazbu za Azure. Pokud potřebujete aktivní kopii obnoveného datového skladu, můžete pokračovat, což by mělo trvat jen několik minut.

### <a name="restore-point-retention"></a>Uchování bodu obnovení

Následující seznam obsahuje podrobné informace o obdobích uchování bodu obnovení:

1. SQL Data Warehouse odstraní bod obnovení, když má k dispozici dobu uchovávání 7 dní **, a** Pokud je aspoň 42 celkových bodů obnovení (včetně uživatelsky definovaného a automatického).
2. Při pozastavení datového skladu se snímky neprovádí.
3. Stáří bodu obnovení se měří v rámci absolutních kalendářních dnů od doby, kdy se provede bod obnovení, včetně okamžiku pozastavení datového skladu.
4. V jakémkoli okamžiku je zaručeno, že datový sklad bude moci ukládat až 42 uživatelem definovaných bodů obnovení a 42 bodů obnovení, pokud tyto body obnovení nedosáhly retenčního období 7 dní.
5. Pokud se snímek povede, pak se datový sklad pozastaví po dobu delší než 7 dní a pak se obnoví, aby bylo možné zachovat bod obnovení, dokud nebude mít 42 celkový počet bodů obnovení (včetně uživatelsky definovaného a automatického).

### <a name="snapshot-retention-when-a-data-warehouse-is-dropped"></a>Uchování snímku při vyřazení datového skladu

Když vyřadíte datový sklad, SQL Data Warehouse vytvoří konečný snímek a uloží ho po dobu sedmi dní. Datový sklad můžete obnovit do konečného bodu obnovení vytvořeného při odstranění. Pokud je datový sklad vyřazený v pozastaveném stavu, žádný snímek se nebere. V takovém případě nezapomeňte před vyřazením datového skladu vytvořit uživatelem definovaný bod obnovení.

> [!IMPORTANT]
> Odstraníte-li logickou instanci systému SQL Server, budou odstraněny také všechny databáze patřící do této instance a nelze je obnovit. Odstraněný Server nelze obnovit.

## <a name="geo-backups-and-disaster-recovery"></a>Geografické zálohování a zotavení po havárii

SQL Data Warehouse pro [spárované datové centrum](../best-practices-availability-paired-regions.md)jednou denně provede geografickou zálohu. RPO pro geografické obnovení je 24 hodin. Geografickou zálohu můžete obnovit na server v libovolné jiné oblasti, kde je SQL Data Warehouse podporováno. Geografická záloha zajišťuje, aby bylo možné obnovit datový sklad pro případ, že nebudete mít přístup k bodům obnovení v primární oblasti.

> [!NOTE]
> Pokud pro geografické zálohy vyžadujete kratší cíl bodu obnovení, Hlasujte [sem](https://feedback.azure.com/forums/307516-sql-data-warehouse)tuto možnost. Můžete také vytvořit uživatelem definovaný bod obnovení a obnovit ho z nově vytvořeného bodu obnovení do nového datového skladu v jiné oblasti. Po obnovení máte datový sklad online a můžete ho pozastavit neomezeně, aby se ušetřily náklady na výpočetní výkon. Pozastavená databáze má za následek poplatky za úložiště za Premium Storageou sazbu za Azure. Pokud potřebujete aktivní kopii datového skladu, můžete pokračovat, což by mělo trvat jen několik minut.

## <a name="backup-and-restore-costs"></a>Náklady na zálohování a obnovení

Všimněte si, že Azure Bill má položku řádku pro úložiště a položku řádku pro úložiště pro zotavení po havárii. Poplatek za úložiště je celková cena za ukládání dat v primární oblasti společně s přírůstkovým změnami zaznamenanými snímky. Podrobnější vysvětlení, jak se účtují snímky, najdete v tématu [Vysvětlení způsobu, jakým se účtují poplatky za snímky](https://docs.microsoft.com/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?redirectedfrom=MSDN#snapshot-billing-scenarios). Geograficky redundantní poplatek pokrývá náklady na ukládání geografických záloh.  

Celkové náklady na váš primární datový sklad a sedm dní pro změny snímku se zaokrouhlují na nejbližší TB. Pokud je například datový sklad 1,5 TB a snímky zachytí 100 GB, bude se vám účtovat 2 TB dat za Azure Premium Storage tarify.

Pokud používáte geografické redundantní úložiště, obdržíte samostatný poplatek za úložiště. Geograficky redundantní úložiště se účtuje standardní sazbou geograficky redundantního úložiště s přístupem pro čtení (RA-GRS).

Další informace o cenách SQL Data Warehouse najdete v tématu [SQL Data Warehouse ceny](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/). Při obnovování v různých oblastech se vám neúčtují odchozí data.

## <a name="restoring-from-restore-points"></a>Obnovení z bodů obnovení

Každý snímek vytvoří bod obnovení, který představuje čas spuštění snímku. Chcete-li obnovit datový sklad, vyberte bod obnovení a proveďte příkaz k obnovení.  

Můžete zachovat obnovený datový sklad a stávající a odstranit jeden z nich. Pokud chcete nahradit aktuální datový sklad obnoveným datovým skladem, můžete ho přejmenovat pomocí [příkazu ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) s možností upravit název.

Chcete-li obnovit datový sklad, přečtěte si téma [Obnovení datového skladu pomocí Azure Portal](sql-data-warehouse-restore-database-portal.md), [Obnovení datového skladu pomocí prostředí PowerShell](sql-data-warehouse-restore-database-powershell.md)nebo [Obnovení datového skladu pomocí rozhraní REST API](sql-data-warehouse-restore-database-rest-api.md).

K obnovení odstraněného nebo pozastaveného datového skladu můžete [vytvořit lístek podpory](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="cross-subscription-restore"></a>Obnovení mezi předplatnými

Pokud potřebujete přímo obnovit v rámci předplatného, Hlasujte [sem](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore)pro tuto funkci. Obnovení na jiný logický Server a [přesunutí](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) serveru mezi předplatnými, aby bylo možné provést obnovení mezi předplatnými. 

## <a name="geo-redundant-restore"></a>Geograficky redundantní obnovení

[Datový sklad můžete obnovit](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-from-geo-backup#restore-from-an-azure-geographical-region-through-powershell) do jakékoli oblasti, která podporuje SQL Data Warehouse na zvolené úrovni výkonu.

> [!NOTE]
> Chcete-li provést geograficky redundantní obnovení, nesmíte mít z této funkce výslovný souhlas.

## <a name="next-steps"></a>Další kroky

Další informace o plánování havárií najdete v tématu [Přehled provozní kontinuity](../sql-database/sql-database-business-continuity.md) .
