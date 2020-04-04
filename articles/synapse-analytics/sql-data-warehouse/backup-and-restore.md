---
title: Zálohování a obnovení - snímky, geograficky redundantní
description: Zjistěte, jak zálohování a obnovení funguje ve fondu SQL Azure Synapse Analytics. Pomocí záloh můžete obnovit datový sklad do bodu obnovení v primární oblasti. Pomocí geograficky redundantních záloh můžete obnovit do jiné geografické oblasti.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/04/2020
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019"
ms.openlocfilehash: 1d82c7c22bb5aeb2740884b0d7ede4a4d8f07f86
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631215"
---
# <a name="backup-and-restore-in-azure-synapse-sql-pool"></a>Zálohování a obnovení ve fondu SQL Azure Synapse

Přečtěte si, jak používat zálohování a obnovení ve fondu SQL Azure Synapse. Pomocí bodů obnovení fondu SQL můžete obnovit nebo zkopírovat datový sklad do předchozího stavu v primární oblasti. Pomocí geograficky redundantních záloh datového skladu můžete obnovit do jiné geografické oblasti.

## <a name="what-is-a-data-warehouse-snapshot"></a>Co je snímek datového skladu

*Snímek datového skladu* vytvoří bod obnovení, který můžete využít k obnovení nebo zkopírování datového skladu do předchozího stavu.  Vzhledem k tomu, že fond SQL je distribuovaný systém, snímek datového skladu se skládá z mnoha souborů, které jsou umístěné v úložišti Azure. Snímky zachycují přírůstkové změny z dat uložených v datovém skladu.

*Obnovení datového skladu* je nový datový sklad, který se vytvoří z bodu obnovení existujícího nebo odstraněného datového skladu. Obnovení datového skladu je nezbytnou součástí každé strategie kontinuity podnikání a zotavení po havárii, protože po náhodném poškození nebo odstranění znovu vytvoří vaše data. Datový sklad je také výkonný mechanismus pro vytváření kopií datového skladu pro účely testování nebo vývoje.  Sazby obnovení fondu SQL se mohou lišit v závislosti na velikosti databáze a umístění zdrojového a cílového datového skladu.

## <a name="automatic-restore-points"></a>Automatické body obnovení

Snímky jsou vestavěnou funkcí služby, která vytváří body obnovení. Není třeba povolit tuto funkci. Fond SQL by však měl být v aktivním stavu pro vytvoření bodu obnovení. Pokud je fond SQL pozastaven často, nemusí být vytvořeny body automatického obnovení, proto nezapomeňte vytvořit uživatelem definovaný bod obnovení před pozastavením fondu SQL. Automatické body obnovení nemohou být aktuálně odstraněny uživateli, protože služba používá tyto body obnovení k udržování služeb SLA pro obnovení.

Snímky datového skladu jsou pořízené po celý den vytváření bodů obnovení, které jsou k dispozici po dobu sedmi dnů. Tuto dobu uchovávání nelze změnit. Fond SQL podporuje cíl bodu obnovení (RPO) za osm hodin. Datový sklad v primární oblasti můžete obnovit z libovolného snímku pořízeného za posledních sedm dní.

Chcete-li zjistit, kdy byl spuštěn poslední snímek, spusťte tento dotaz ve fondu SQL online.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Uživatelem definované body obnovení

Tato funkce umožňuje ručně aktivovat snímky pro vytvoření bodů obnovení datového skladu před a po velkých úpravách. Tato funkce zajišťuje, že body obnovení jsou logicky konzistentní, což poskytuje další ochranu dat v případě přerušení pracovního vytížení nebo chyb y uživatelů pro rychlou dobu obnovení. Uživatelem definované body obnovení jsou k dispozici po dobu sedmi dnů a jsou automaticky odstraněny vaším jménem. Dobu uchování uživatelem definovaných bodů obnovení nelze změnit. **42 uživatelem definovaných bodů obnovení** je zaručeno v libovolném okamžiku, takže musí být před vytvořením jiného bodu obnovení [odstraněny.](https://go.microsoft.com/fwlink/?linkid=875299) Můžete aktivovat snímky k vytvoření uživatelem definované body obnovení prostřednictvím [PowerShell](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsont#examples) nebo portálAzure.

> [!NOTE]
> Pokud požadujete body obnovení delší než 7 dní, hlasujte pro tuto funkci [zde](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points). Můžete také vytvořit uživatelem definovaný bod obnovení a obnovit z nově vytvořeného bodu obnovení do nového datového skladu. Po obnovení máte fond SQL online a můžete jej pozastavit na neurčito ušetřit výpočetní náklady. Pozastavená databáze účtuje poplatky za úložiště při sazbě úložiště Azure Premium. Pokud potřebujete aktivní kopii obnoveného datového skladu, můžete pokračovat, což by mělo trvat jen několik minut.

### <a name="restore-point-retention"></a>Obnovení bodu uchování

V následujícím seznamu jsou uvedeny podrobnosti o obdobích uchování bodů obnovení:

1. Fond SQL odstraní bod obnovení, když narazí na 7 denní retenční období **a** pokud existuje alespoň 42 celkový počet bodů obnovení (včetně uživatelem definované a automatické).
2. Snímky nejsou pořízeny při pozastavení fondu SQL.
3. Stáří bodu obnovení se měří absolutní kalendářní dny od okamžiku, kdy je bod obnovení přijata, včetně při pozastavení fondu SQL.
4. V libovolném okamžiku je zaručeno, že fond SQL bude schopen uložit až 42 uživatelem definovaných bodů obnovení a 42 bodů automatického obnovení, pokud tyto body obnovení nedosáhly 7denního období uchovávání.
5. Pokud snímek je přijata, fond SQL je pak pozastavena na více než 7 dní a potom pokračuje, bod obnovení bude přetrvávat, dokud existuje 42 celkový počet bodů obnovení (včetně uživatelem definované a automatické)

### <a name="snapshot-retention-when-a-sql-pool-is-dropped"></a>Uchovávání snímků při vynechání fondu SQL

Při přetažení fondu SQL je vytvořen konečný snímek a uložen po dobu sedmi dnů. Fond SQL můžete obnovit do konečného bodu obnovení vytvořeného při odstranění. Pokud je fond SQL vynechán v pozastaveném stavu, je pořízen žádný snímek. V tomto scénáři nezapomeňte vytvořit uživatelem definovaný bod obnovení před uvolněním fondu SQL.

> [!IMPORTANT]
> Pokud odstraníte logickou instanci serveru SQL, budou odstraněny také všechny databáze, které patří do instance, a nelze je obnovit. Odstraněný server nelze obnovit.

## <a name="geo-backups-and-disaster-recovery"></a>Geografické zálohy a zotavení po havárii

Geografická záloha se vytváří jednou denně do [spárovaného datového centra](../../best-practices-availability-paired-regions.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). RPO pro geografické obnovení je 24 hodin. Geografickou zálohu můžete obnovit na server v jakékoli jiné oblasti, kde je podporován fond SQL. Geografická záloha zajišťuje obnovení datového skladu v případě, že nemáte přístup k bodům obnovení v primární oblasti.

> [!NOTE]
> Pokud požadujete kratší rpo pro geografické zálohování, hlasujte pro tuto funkci [zde](https://feedback.azure.com/forums/307516-sql-data-warehouse). Můžete také vytvořit uživatelem definovaný bod obnovení a obnovit z nově vytvořeného bodu obnovení do nového datového skladu v jiné oblasti. Po obnovení máte datový sklad online a můžete ho pozastavit na neurčito, abyste ušetřili výpočetní náklady. Pozastavená databáze účtuje poplatky za úložiště při sazbě úložiště Azure Premium. Pokud potřebujete aktivní kopii datového skladu, můžete pokračovat, což by mělo trvat jen několik minut.

## <a name="backup-and-restore-costs"></a>Náklady na zálohování a obnovení

Všimněte si, že účet Azure má řádkovou položku pro úložiště a řádkovou položku pro úložiště zotavení po havárii. Poplatek za úložiště je celkové náklady na ukládání dat v primární oblasti spolu s přírůstkové změny zachycené snímky. Podrobnější vysvětlení, jak jsou účtovány snímky, naleznete [v části Principy, jak snímky časově rozlišují poplatky](/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Geograficky redundantní poplatek pokrývá náklady na ukládání geografických záloh.  

Celkové náklady na primární datový sklad a sedm dní změn snímků se zaokrouhlí na nejbližší TB. Pokud má například váš datový sklad 1,5 TB a snímky zachytí 100 GB, budou se vám účtovat 2 TB dat sazby za úložiště Azure Premium.

Pokud používáte geograficky redundantní úložiště, obdržíte samostatný poplatek za úložiště. Geograficky redundantní úložiště se účtuje standardní geograficky redundantní úložiště pro čtení (RA-GRS).

Další informace o cenách Azure Synapse najdete v [tématu Azure Synapse pricing](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/). Při obnovení napříč oblastmi se za odchozí data neúčtují poplatky.

## <a name="restoring-from-restore-points"></a>Obnovení z bodů obnovení

Každý snímek vytvoří bod obnovení, který představuje čas spuštění snímku. Chcete-li obnovit datový sklad, zvolte bod obnovení a vydat příkaz obnovení.  

Můžete buď zachovat obnovený datový sklad a aktuální, nebo jeden z nich odstranit. Pokud chcete nahradit aktuální datový sklad obnoveným datovým skladem, můžete jej přejmenovat pomocí [ALTER DATABASE (FOND SQL)](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) na možnost ZMĚNIT NÁZEV.

Pokud chcete obnovit datový sklad, [přečtěte si](sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-the-azure-portal)informace o obnovení fondu SQL .

Chcete-li obnovit odstraněný nebo pozastavený datový sklad, můžete [vytvořit lístek podpory](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="cross-subscription-restore"></a>Obnovení křížového předplatného

Pokud potřebujete přímo obnovit přes předplatné, hlasujte pro tuto funkci [zde](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore). Obnovení na jiný logický server a ['Přesunout'](/azure/azure-resource-manager/resource-group-move-resources?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) server přes odběry provést obnovení křížového předplatného.

## <a name="geo-redundant-restore"></a>Geograficky redundantní obnovení

Fond [SQL](sql-data-warehouse-restore-from-geo-backup.md#restore-from-an-azure-geographical-region-through-powershell) můžete obnovit do libovolné oblasti podporující fond SQL na vámi zvolené úrovni výkonu.

> [!NOTE]
> Chcete-li provést geograficky redundantní obnovení, nesmíte se od této funkce odhlásili.

## <a name="next-steps"></a>Další kroky

Další informace o plánování katastrof naleznete v tématu [Přehled kontinuity provozu](../../sql-database/sql-database-business-continuity.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
