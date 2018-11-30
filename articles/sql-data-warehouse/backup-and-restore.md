---
title: Azure SQL Data Warehouse zálohování a obnovení – snímky, geograficky redundantní | Dokumentace Microsoftu
description: Zjistěte, jak funguje zálohování a obnovení Azure SQL Data Warehouse. Zálohy datového skladu použít k obnovení svého datového skladu do bodu obnovení v primární oblasti. Použijte geograficky redundantní zálohy k obnovení do jiné geografické oblasti.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: e060075fbcf6a5083e51b753e119cb5471129a79
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52308462"
---
# <a name="backup-and-restore-in-azure-sql-data-warehouse"></a>Zálohování a obnovení ve službě Azure SQL Data Warehouse
Zjistěte, jak funguje zálohování a obnovení Azure SQL Data Warehouse. Použití datového skladu snímky k obnovení nebo zkopírujte váš datový sklad k určitému bodu obnovení v primární oblasti. Použití datového skladu geograficky redundantní zálohy k obnovení do jiné geografické oblasti. 

## <a name="what-is-a-data-warehouse-snapshot"></a>Co je snímek data warehouse?
A *datového skladu snímku* vytvoří bod obnovení, kterou můžete využít k obnovení, nebo zkopírováním vašeho datového skladu do předchozího stavu.  SQL Data Warehouse je distribuovaný systém, snímku datového skladu se skládá z mnoha soubory, které se nacházejí ve službě Azure storage. Snímky zachycují přírůstkové změny z dat uložených v datovém skladu.

A *datového skladu obnovení* je nový datový sklad, který je vytvořen z bodu obnovení pro existující nebo odstraněný datový sklad. Obnovení datového skladu je nedílnou součást každé strategie obchodní kontinuity podnikových procesů a po havárii pro obnovení, protože jej znovu vytvoří data po náhodným poškozením nebo odstranění. Datový sklad je také výkonný mechanismus pro vytvoření kopie vašeho datového skladu pro účely testovacím nebo vývojovém.  SQL Data Warehouse používá mechanismus rychlého obnovení v rámci stejné oblasti, který se měří na trvat méně než 20 minut pro libovolnou velikost dat. 

## <a name="automatic-restore-points"></a>Automatické body obnovení
Snímky jsou integrované funkce služby, která vytvoří body obnovení. Není potřeba aktivovat tuto funkci. Automatické body obnovení aktuálně nelze odstranit uživatele, kde se služba používá, obnovení odkazuje na Udržovat smlouvy o úrovni služeb pro obnovení.

SQL Data Warehouse pořizuje snímky datového skladu v průběhu dne vytváří body obnovení, které jsou k dispozici po dobu sedmi dní. Toto období uchovávání dat nelze změnit. SQL Data Warehouse podporuje cíl bodu obnovení osm hodin (RPO). Váš datový sklad v primární oblasti můžete obnovit z některého z snímkům pořízeným za posledních sedm dnů.

Pokud chcete zobrazit při spuštění poslední snímek, spuštění tohoto dotazu na datový sklad SQL online. 

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs 
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Uživatelem definované body obnovení
Tato funkce umožňuje ruční aktivační událost snímky vytvářet body obnovení datového skladu, před a po velké změny. Tato funkce zajišťuje, že body obnovení jsou logicky konzistentní vzhledem k aplikacím, které nabízí další ochranu dat v případě jakékoli úlohy přerušení nebo chyby uživatele pro rychlé obnovení čas. Body obnovení uživatelem definované jsou k dispozici po dobu sedmi dní a se automaticky odstraní vaším jménem. Nelze změnit doby uchovávání bodů obnovení definovaný uživatelem. **body obnovení 42 uživatelem definované** jsou mechanismem zaručujícím libovolného bodu v čase, musí být [odstranit](https://go.microsoft.com/fwlink/?linkid=875299) dříve než vytvoříte další bod obnovení. Můžete aktivovat snímky vytvářet body obnovení uživatelem definované prostřednictvím [Powershellu](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabaserestorepoint?view=azurermps-6.2.0#examples) nebo na webu Azure portal.


> [!NOTE]
> Pokud potřebujete body obnovení delší než 7 dní, prosím hlasovat pro tuto funkci [tady](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points). Můžete také vytvořit bod obnovení definované uživatelem a obnovení z bodu obnovení vytvořeného na nový datový sklad. Po obnovení, můžete mít online datový sklad a můžete pozastavit, po neomezenou dobu uložíte náklady na výpočetní výkon. Pozastavený databáze neúčtují poplatky za úložiště za sazby Azure Storage úrovně Premium. Pokud budete potřebovat aktivní kopií obnovená data warehouse, můžete obnovit, což by mělo trvat jenom pár minut.
>

### <a name="restore-point-retention"></a>Uchování bodu obnovení
Následující část popisuje podrobnosti v období uchování bodu obnovení:
1. SQL Data Warehouse Odstraní bod obnovení při volání dobu uchování 7 dní **a** po nejméně 42 celkový počet bodů obnovení (včetně definované uživatelem a automatické)
2. Snímky nejsou provedeny, když je datový sklad pozastavený
3. Stáří bodu obnovení se měří podle absolutní kalendářních dnů od doby, kdy se používá bod obnovení, včetně toho, když je datový sklad pozastavený
4. V libovolném okamžiku v čase je zaručeno, že datový sklad možné uložit až 42 body obnovení definované uživatelem a 42 Automatické body obnovení, dokud tyto body obnovení nedosáhly dobu uchování 7 dní
5. Pokud se pořídí snímek, je datový sklad pozastavený pak pro více než 7 dní a pak bude pokračovat, je možné pro bod obnovení na budou zachovány, dokud nejsou 42 celkový počet bodů obnovení (včetně definované uživatelem a automatické)

### <a name="snapshot-retention-when-a-data-warehouse-is-dropped"></a>Snímek uchování při přetažení datového skladu
Při umístění datového skladu SQL Data Warehouse vytvoří konečný snímek a uloží jej po dobu sedmi dní. Datový sklad můžete obnovit do bodu obnovení poslední vytvořený v odstranění. 

> [!IMPORTANT]
> Pokud odstraníte logickou instanci SQL serveru, odstraní se také všechny databáze patřící k instanci a nelze obnovit. Nelze obnovit server odstranil.
>

## <a name="geo-backups"></a>Geografické zálohování
SQL Data Warehouse provede geografické zálohování jednou denně, abyste [spárovaném datovém centru](../best-practices-availability-paired-regions.md). Plánovaný bod obnovení geografické obnovení je 24 hodin. Geografické zálohování můžete obnovit na server v jiné oblasti, kde je podporován SQL Data Warehouse. Geografické zálohování zajišťuje, že datový sklad můžete provést obnovení v případě, že nemáte přístup body obnovení v primární oblasti.

Geografické zálohy jsou standardně povoleny. Pokud váš datový sklad je Gen1, můžete si [Odhlásit se totiž](/powershell/module/azurerm.sql/set-azurermsqldatabasegeobackuppolicy) Pokud chcete. Nelze se rozhodnete z geografické zálohy pro Gen2 jako ochrana dat je zaručeno, že integrované.

> [!NOTE]
> Pokud potřebujete kratší cíle bodu obnovení pro geografické zálohování, hlasovat pro tuto funkci [tady](https://feedback.azure.com/forums/307516-sql-data-warehouse). Můžete také vytvořit bod obnovení definované uživatelem a obnovení z bodu obnovení vytvořeného do nového datového skladu v jiné oblasti. Po obnovení, můžete mít online datový sklad a můžete pozastavit, po neomezenou dobu uložíte náklady na výpočetní výkon. Pozastavený databáze neúčtují poplatky za úložiště za sazby Azure Storage úrovně Premium. a potom pozastavte.<!-- should this be removed or is something missing? --> Budete potřebovat aktivní kopie datového skladu, můžete obnovit, což by mělo trvat jenom pár minut.
>


## <a name="backup-and-restore-costs"></a>Poplatky za zálohování a obnovení
Můžete si všimnout, že vyúčtování služeb Azure má položku řádku pro úložiště a položky řádku pro úložiště pro zotavení po havárii. Poplatek za úložiště se celkové náklady na ukládání dat v primární oblasti spolu s přírůstkové změny zachycených snímků. Podrobnější vysvětlení na jak aktuálně pocházejí snímků, najdete to [dokumentaci](https://docs.microsoft.com/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?redirectedfrom=MSDN#snapshot-billing-scenarios). Geograficky redundantní poplatek zahrnují náklady na ukládání geografické zálohování.  

Celkové náklady na primární datového skladu a sedm dní změny snímku se zaokrouhlí na nejbližší TB. Například pokud váš datový sklad velikost 1,5 TB a zachytí 100 GB snímků, vám účtovat 2 TB dat sazby Azure Storage úrovně Premium. 

Pokud používáte geografické redundantní úložiště, se zobrazí samostatný poplatek za úložiště. Geograficky redundantní úložiště se účtuje standardní sazbou jen pro čtení geograficky redundantní úložiště (RA-GRS).

Další informace o SQL Data Warehouse – ceny najdete v tématu [SQL Data Warehouse – ceny](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) a [poplatky za výchozí přenos dat](https://azure.microsoft.com/pricing/details/bandwidth/) při obnovení mezi oblastmi.

## <a name="restoring-from-restore-points"></a>Obnovení z bodů obnovení
Každý snímek vytvoří bod obnovení, který představuje čas spuštění snímku. K obnovení datového skladu, zvolte bod obnovení a vydejte příkaz restore.  

Můžete zachovat obnovené datového skladu a aktuální, nebo odstraňte jednu z nich. Pokud chcete nahradit aktuální datový sklad obnovená data warehouse, můžete ji jakkoli přejmenovat pomocí [ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) možnost změnit název. 

Chcete-li obnovit datový sklad, přečtěte si téma [obnovení datového skladu pomocí webu Azure portal](sql-data-warehouse-restore-database-portal.md), [obnovit data warehouse pomocí prostředí PowerShell](sql-data-warehouse-restore-database-powershell.md), nebo [obnovení datového skladu pomocí jazyka T-SQL](sql-data-warehouse-restore-database-rest-api.md) .

Chcete-li obnovit odstraněné nebo pozastaveného datového skladu, můžete [vytvořit lístek podpory](sql-data-warehouse-get-started-create-support-ticket.md). 


## <a name="geo-redundant-restore"></a>Geograficky redundantní obnovení
Je možné [obnovit váš datový sklad](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region) do libovolné oblasti podporuje SQL Data Warehouse na vaší úrovni zvolené výkonu. 

> [!NOTE]
> Geograficky redundantní obnovení nesmí Nesouhlasili jste tuto funkci.
>

## <a name="next-steps"></a>Další postup
Další informace o plánování řešení systému po havárii najdete v tématu [přehled zajištění provozní kontinuity firmy](../sql-database/sql-database-business-continuity.md)
