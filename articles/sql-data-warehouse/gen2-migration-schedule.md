---
title: Migrujte vaše stávající Azure SQL Data Warehouse na Gen2 | Dokumentace Microsoftu
description: Pokyny k migraci existujícího datového skladu na Gen2 a migrace plán, podle oblasti.
services: sql-data-warehouse
author: mlee3gsd
ms.author: anumjs
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.topic: article
ms.date: 02/09/2019
ms.openlocfilehash: a3ec74d0b22bfafb4353eca400b389b07a58ba39
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246343"
---
# <a name="upgrade-your-data-warehouse-to-gen2"></a>Upgrade služby data warehouse na Gen2
Microsoft pomáhá snížit základní náklady na provozování schopná zpracovat vyhovovat i vašim náročným dotazy přidáním nižší výpočetní úrovně pro Azure SQL Data Warehouse do datového skladu přečíst celé oznámení o [nižší výpočetní vrstva podpory pro Gen2](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/). Nová nabídka je již k dispozici v oblastech, které jste si poznamenali v následující tabulce. Pro oblasti jsou podporované je možné upgradovat na Gen2 stávajících datových skladů Gen1 prostřednictvím:
- **Automatický proces upgradu:** Automatické upgrady nebudou spuštěny poté, co je služba dostupná v oblasti.  Při spuštění automatických upgradů v konkrétní oblasti, jednotlivé upgradu datového skladu bude probíhat během svůj plán údržby vybraných. 
- **Samoobslužné upgradujte na Gen2:** Pokud chcete řídit, kdy se má upgradovat, můžete provádět samoobslužné upgradovat na Gen2. Pokud vaše oblast ještě není podporovaná, můžete migrovat váš datový Sklad na oblast, která je podporována a pak proveďte místním upgradu na 2. generace.

## <a name="automated-schedule-and-region-availability-table"></a>Plán automatizovaných a tabulka dostupnosti oblast
Následující tabulka shrnuje podle oblastí, při nižší Gen2 výpočetní vrstva bude k dispozici, a při spuštění automatických upgradů. Data se můžou změnit. Zkontrolujte zpět při vaší oblasti k dispozici.

\* Označuje, že konkrétní plán pro oblast je momentálně není k dispozici.

| **Oblast** | **K dispozici nižší Gen2** | **Zahájit automatické upgrady** |
|:--- |:--- |:--- |
| Austrálie – střed 1 |1. března 2019 |15. června 2019 |
| Austrálie – střed 2 |1. března 2019 |15. června 2019 |
| Austrálie – východ |K dispozici. |1. května 2019 |
| Austrálie – jihovýchod |1. března 2019 |15. června 2019 |
| Brazílie – jih |\* |\* |
| Kanada – střed |K dispozici. |1. května 2019 |
| Kanada – východ |\* |\* |
| USA – střed |K dispozici. |1. května 2019 |
| Čína – východ |\* |\* |
| Čína – sever 1 |\* |\* |
| Východní Asie |K dispozici. |1. května 2019 |
| USA – východ 1 |K dispozici. |16. březnem 2019 |
| Východní USA 2 |K dispozici. |16. březnem 2019 |
| Francie – střed |1. března 2019 |1. května 2019 |
| Německo – střed |\* |\* |
| Indie – střed |K dispozici. |1. května 2019 |
| Indie – jih 1 |1. března 2019 |15. června 2019 |
| Japonsko – východ |K dispozici. |1. května 2019 |
| Japonsko – západ |K dispozici. |15. června 2019 |
| Korea – střed |1. března 2019 |1. května 2019 |
| Korea – jih 1 |1. března 2019 |15. června 2019 |
| Středoseverní USA |1. března 2019 |15. června 2019 |
| Severní Evropa |K dispozici. |16. březnem 2019 |
| Středojižní USA |K dispozici. |1. května 2019 |
| Jihovýchodní Asie |K dispozici. |16. březnem 2019 |
| Velká Británie – jih |1. března 2019 |1. května 2019 |
| UK West 1 |1. března 2019 |15. června 2019 |
| Západní střed USA |\* |\* |
| Západní Evropa |K dispozici. |16. březnem 2019 |
| USA – západ 1 |1. března 2019 |15. června 2019 |
| Západní USA 2 |K dispozici. |16. březnem 2019 |

## <a name="automatic-upgrade-process"></a>Proces automatického upgradu

Od 16. března 2019 začneme plánování automatizované upgrady pro vaše instance Gen1. Pokud chcete vyhnout neočekávaným přerušení na dostupnost datového skladu, bude naplánováno automatizované upgrady během vašeho plánu údržby. Další informace v rámci plánů najdete v tématu [zobrazit plán údržby](viewing-maintenance-schedule.md)

Procesu upgradu bude zahrnovat stručný pokles připojení (přibližně 5 minut), jak jsme restartování služby data warehouse.  Po restartování služby data warehouse je plně k dispozici pro použití ale, zaznamenáte snížení výkonu během procesu upgradu nadále upgrade datové soubory na pozadí. Celková doba pro snížení výkonu se budou lišit závisí na velikosti datových souborů.

Může také urychlit proces upgradu soubor dat spuštěním [Alter Index znovu sestavit](sql-data-warehouse-tables-index.md) ve všech tabulkách primární columnstore pomocí větší třídu cíle na úrovni služby a prostředky po restartování.

> [!NOTE]
> Příkaz ALTER Index sestavení je v režimu offline operace a tabulky budou dostupné, až se dokončí sestavení.

## <a name="self-upgrade-to-gen2"></a>Samoobslužné upgradovat na Gen2

Volitelně Pokud chcete upgradovat, můžete pomocí následujících kroků na existující datový sklad Gen1 místním upgradu. Pokud zvolíte tuto možnost, musíte upgradovat svým před zahájením procesu upgradu automatické v dané oblasti. Tím se zajistí, že byste se vyhnout rizika automatických upgradů, což způsobí konflikt.

Existují dvě možnosti při místním upgradu.  Můžete buď upgradovat vaše aktuální data warehouse na místě nebo Gen1 datový sklad můžete provést obnovení do Gen2 instance.

- [Upgrade na místě](upgrade-to-latest-generation.md) – tato možnost provede upgrade existující datový sklad Gen1 na Gen2. Procesu upgradu bude zahrnovat stručný pokles připojení (přibližně 5 minut), jak jsme restartování služby data warehouse.  Po restartování služby data warehouse je plně k dispozici pro použití. Pokud z nějakého důvodu potřebujete obnovit zpět do Gen1 z Gen2 instance, otevřete [žádost o podporu](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket).
- [Upgrade z bodu obnovení](sql-data-warehouse-restore.md) – vytvořit bod obnovení definovaný uživatelem ve vaší aktuální data warehouse Gen1 a potom obnovit přímo do Gen2 instance. Existující datový sklad Gen1 zůstane na místě. Po dokončení obnovení budou plně k dispozici pro použití služby data warehouse Gen2.  Po spuštění všech procesů testování a ověřování na obnovené instance Gen2 je možné odstranit původní instanci Gen1.
    - Krok 1: Na webu Azure Portal [vytvořit bod obnovení uživatelem definované](sql-data-warehouse-restore.md#create-a-user-defined-restore-point-using-the-azure-portal).
    - Krok 2: Při obnovení z uživatelem definované bod obnovení, nastavte "výkonu úroveň" pro upřednostňovanou úroveň Gen2.

Během procesu upgradu nadále upgrade datové soubory na pozadí se můžete setkat s určitou snížení výkonu. Celková doba pro snížení výkonu se budou lišit závisí na velikosti datových souborů.

Pokud chcete urychlit proces migrace dat na pozadí, můžete okamžitě vynutit přesun dat spuštěním [Alter Index znovu sestavit](sql-data-warehouse-tables-index.md) ve všech tabulkách primární columnstore by dotazování na větší třídu cíle na úrovni služby a prostředky.

> [!NOTE]
> Příkaz ALTER Index sestavení je v režimu offline operace a tabulky budou dostupné, až se dokončí sestavení.

Pokud narazíte na případné problémy s váš datový sklad, vytvořte [žádost o podporu](sql-data-warehouse-get-started-create-support-ticket.md) a odkazovat na "Gen2 upgrade" jako možnou příčinu.

Další informace najdete v tématu [upgradovat na Gen2](upgrade-to-latest-generation.md).

## <a name="migration-frequently-asked-questions"></a>Nejčastější dotazy k migraci

**Otázka: Gen2 stojí stejné jako Gen1?**
- Odpověď: Ano.

**Otázka: Upgrady vliv skripty pro automatizaci?**
- Odpověď: Automatizační skript, který odkazuje cíl na úrovni služby musí změnit tak, aby odpovídaly ekvivalent Gen2.  Zobrazit podrobnosti [tady](upgrade-to-latest-generation.md#sign-in-to-the-azure-portal).

**Otázka: Jak dlouho místním upgradu obvykle trvá?**
- Odpověď: Můžete upgradovat na místě nebo upgradovat z bodu obnovení.  
   - Upgrade na místě způsobí okamžité pozastavení a obnovení datového skladu.  Proces na pozadí bude pokračovat, zatímco data warehouse je online.  
   - Trvá déle, pokud provádíte upgrade pomocí bodu obnovení, protože upgrade bude projít procesem úplného obnovení.

**Otázka: Jak dlouho se automatický upgrade provést?**
- Odpověď: Skutečná délka výpadku pro upgrade je pouze čas potřebný k pozastavení a obnovení služby, která je mezi 5 až 10 minut. Po krátké dolů čas proces na pozadí se spustí migraci úložiště. Doba pro proces na pozadí je závisí na velikosti vašeho datového skladu.

**Otázka: Když tento automatický upgrade proběhne?**
- Odpověď: Během vašeho plánu údržby. Využití vašeho plánu údržby zvolené se minimalizovalo přerušení pro vaši firmu.

**Otázka: Co mám dělat, když proces upgradu na pozadí se zdají být zablokované?**
 - Odpověď: Pusťte se do reindex tabulek Columnstore. Všimněte si, že Reindexace tabulky budou offline během této operace.

**Otázka: Co když Gen2 nemá žádné cíle na úrovni služby je nutné na Gen1?**
- Odpověď: Pokud používáte úroveň DW600 nebo DW1200 na Gen1, se doporučuje použít DW500c nebo DW1000c v uvedeném pořadí, protože Gen2 poskytuje více paměti, prostředky a vyšší výkon než Gen1.

**Otázka: Můžete zakázat geografické zálohování?**
- Odpověď: Ne. Geografické zálohování je funkce enterprise pro zachování vašich dat skladu dostupnost v případě, že oblast stane nedostupnou. Otevřít [žádost o podporu](sql-data-warehouse-get-started-create-support-ticket.md) Pokud máte další otázky.

**Otázka: Je nějaký rozdíl mezi Gen1 a Gen2 v syntaxi T-SQL?**
- Odpověď: Není žádná změna v syntaxi jazyka T-SQL z Gen1 na Gen2.

**Otázka: Podporuje Gen2 údržby Windows?**
- Odpověď: Ano.

**Otázka: Bude možné vytvořit novou instanci Gen1 po upgradu mojí oblasti?**
- Odpověď: Ne. Po upgradu v oblasti vytváření nových instancí Gen1 se deaktivuje.

## <a name="next-steps"></a>Další postup

- [Postup upgradu](upgrade-to-latest-generation.md)
- [Časová období údržby](maintenance-scheduling.md)
- [Monitorování stavu prostředků](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Projděte si před zahájením migrace](upgrade-to-latest-generation.md#before-you-begin)
- [Místní upgrade a upgrade z bodu obnovení](upgrade-to-latest-generation.md)
- [Vytvořit bod obnovení definovaný uživatelem](sql-data-warehouse-restore.md#restore-through-the-azure-portal)
- [Zjistěte, jak obnovení na Gen2](sql-data-warehouse-restore.md#restore-an-active-or-paused-database-using-the-azure-portal)
- [Žádost o podporu SQL Data Warehouse](https://go.microsoft.com/fwlink/?linkid=857950)