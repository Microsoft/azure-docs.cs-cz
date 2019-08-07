---
title: Migrace stávajícího Azure SQL Data Warehouse do Gen2 | Microsoft Docs
description: Pokyny k migraci stávajícího datového skladu do Gen2 a plánu migrace podle oblasti.
services: sql-data-warehouse
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.topic: article
ms.date: 07/22/2019
ms.openlocfilehash: cd55e078e14ec34006df05096f161e7bdef39a03
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827223"
---
# <a name="upgrade-your-data-warehouse-to-gen2"></a>Upgrade datového skladu na Gen2

Společnost Microsoft pomáhá řídit náklady na úroveň vstupu pro provoz datového skladu.  Pro Azure SQL Data Warehouse jsou nyní k dispozici nižší výpočetní vrstvy, které umožňují zpracování náročných dotazů. Přečtěte si úplné oznámení o [podpoře výpočetních vrstev pro Gen2](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/). Nová nabídka je k dispozici v oblastech uvedených v následující tabulce. U podporovaných oblastí lze existující datové sklady Gen1 upgradovat na Gen2 prostřednictvím:

- **Proces automatického upgradu:** Automatické upgrady se nespustí hned po tom, co je služba dostupná v určité oblasti.  Když automatické upgrady začnou v konkrétní oblasti, během vybraného plánu údržby budou provedeny jednotlivé upgrady DW.
- [**Samoobslužný upgrade na Gen2:** ](#self-upgrade-to-gen2) Můžete řídit, kdy se má upgradovat, a to provedením samočinného upgradu na Gen2. Pokud vaše oblast ještě není podporovaná, můžete ji obnovit z bodu obnovení přímo do instance Gen2 v podporované oblasti.

## <a name="automated-schedule-and-region-availability-table"></a>Automatický plán a tabulka dostupnosti oblastí

Následující tabulka shrnuje podle oblasti v případě, že bude k dispozici nižší výpočetní vrstva Gen2 a když se spustí automatické upgrady. Data se mohou změnit. Vraťte se zpět a podívejte se, jestli je vaše oblast k dispozici.

\*indikuje, že konkrétní plán pro oblast není aktuálně k dispozici.

| **Oblast** | **Nižší Gen2 k dispozici** | **Začátek automatických upgradů** |
|:--- |:--- |:--- |
| Austrálie – východ |K dispozici |Dokončeno |
| Austrálie – jihovýchod |K dispozici |Dokončeno |
| Brazílie – jih |K dispozici |Dokončeno |
| Kanada – střed |K dispozici |Dokončeno |
| Kanada – východ |1\. června 2020 |Od 1. července 2020 |
| Střed USA |K dispozici |Dokončeno |
| Čína – východ |\* |\* |
| Čína – východ 2 |K dispozici |Dokončeno |
| Čína – sever |\* |\* |
| Čína – sever 2 |K dispozici |Dokončeno |
| Východní Asie |K dispozici |Dokončeno |
| East US |K dispozici |Dokončeno |
| Východní USA 2 |K dispozici |Dokončeno |
| Francie – střed |K dispozici |Probíhá |
| Střední Německo |\* |\* |
| Německo – středozápad |Od 1. září 2019|Od 1. října 2019 |
| Indie – střed |K dispozici |Dokončeno |
| Indie – jih |K dispozici |Dokončeno |
| Indie – západ |Od 1. července 2019 |Probíhá |
| Japonsko – východ |K dispozici |Dokončeno |
| Japonsko – západ |K dispozici |Dokončeno |
| Jižní Korea – střed |K dispozici |Dokončeno |
| Jižní Korea – jih |K dispozici |Dokončeno |
| Střed USA – sever |K dispozici |Dokončeno |
| Severní Evropa |K dispozici |Dokončeno |
| Jižní Afrika – sever |12. července 2019 |Dokončeno |
| Střed USA – jih |K dispozici |Dokončeno |
| Jihovýchodní Asie |K dispozici |Dokončeno |
| Spojené arabské emiráty – sever |20. července 2019 |Dokončeno |
| Velká Británie – jih |K dispozici |Probíhá |
| Spojené království – západ |K dispozici |Probíhá |
| Západní střed USA |Od 1. září 2019 |Od 1. října 2019|
| Západní Evropa |K dispozici |Dokončeno |
| USA – západ |K dispozici |Dokončeno |
| USA – západ 2 |K dispozici |Dokončeno |

## <a name="automatic-upgrade-process"></a>Proces automatického upgradu

V závislosti na grafu dostupnosti naplánujeme automatizované upgrady pro instance Gen1. Aby nedošlo k neočekávanému přerušení dostupnosti datového skladu, budou se automatizované upgrady naplánovaly během plánu údržby. Možnost vytvoření nové instance Gen1 se zakáže v oblastech, které provádějí automatický upgrade na Gen2. Po dokončení automatických upgradů se Gen1 přestane používat. Další informace o plánech najdete v tématu [zobrazení plánu údržby.](viewing-maintenance-schedule.md)

Proces upgradu bude při restartu datového skladu zahrnovat krátké přerušení připojení (přibližně 5 min).  Jakmile je datový sklad restartován, bude plně dostupný pro použití. Nicméně může docházet ke snížení výkonu v době, kdy proces upgradu pokračuje v upgradu datových souborů na pozadí. Celková doba snížení výkonu se bude lišit v závislosti na velikosti datových souborů.

Proces upgradu datového souboru můžete také urychlit spuštěním [příkazu ALTER index Rebuild](sql-data-warehouse-tables-index.md) ve všech primárních tabulkách columnstore s použitím větších tříd objektů slo a prostředků po restartování.

> [!NOTE]
> Příkaz ALTER index Rebuild je offline operace a tabulky nebudou k dispozici, dokud se znovu nedokončí sestavení.

## <a name="self-upgrade-to-gen2"></a>Samoobslužný upgrade na Gen2

Pomocí následujících kroků můžete v existujícím datovém skladu Gen1 vybrat samoobslužný upgrade. Pokud se rozhodnete pro vlastní upgrade, je nutné jej dokončit před zahájením procesu automatického upgradu ve vaší oblasti. Tím zajistíte, že předejdete jakémukoli riziku automatických upgradů, které způsobují konflikt.

Při provádění samoobslužného upgradu jsou k dispozici dvě možnosti.  Můžete buď upgradovat aktuální datový sklad místně, nebo můžete obnovit Gen1 datový sklad do instance Gen2.

- [Upgrade na místě](upgrade-to-latest-generation.md) – Tato možnost provede upgrade existujícího datového skladu Gen1 na Gen2. Proces upgradu bude při restartu datového skladu zahrnovat krátké přerušení připojení (přibližně 5 min).  Jakmile je datový sklad restartován, bude plně dostupný pro použití. Pokud při upgradu dojde k problémům, otevřete [žádost o podporu](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) a referenční informace "Gen2 upgrade" jako možnou příčinu.
- [Upgrade z bodu obnovení](sql-data-warehouse-restore.md) – vytvořte uživatelem definovaný bod obnovení v aktuálním datovém skladu Gen1 a proveďte obnovení přímo do instance Gen2. Existující datový sklad Gen1 zůstane na svém místě. Až se obnovení dokončí, váš datový sklad Gen2 bude plně dostupný pro použití.  Po spuštění všech procesů testování a ověřování na obnovené instanci Gen2 je možné odstranit původní instanci Gen1.

   - Krok 1: Z Azure Portal [Vytvořte bod obnovení definovaný uživatelem](sql-data-warehouse-restore-active-paused-dw.md#restore-an-existing-data-warehouse-through-the-azure-portal).
   - Krok 2: Při obnovení z bodu obnovení definovaného uživatelem nastavte úroveň výkonu na upřednostňovanou Gen2 úroveň.

Během toho, co proces upgradu upgraduje datové soubory na pozadí, může na určitou dobu dojít ke snížení výkonu. Celková doba snížení výkonu se bude lišit v závislosti na velikosti datových souborů.

Chcete-li urychlit proces migrace dat na pozadí, můžete ihned vynutit přesun dat spuštěním [příkazu ALTER index Rebuild](sql-data-warehouse-tables-index.md) na všech primárních tabulkách columnstore, které se dotazují na větší úrovni objektů slo a prostředků.

> [!NOTE]
> Příkaz ALTER index Rebuild je offline operace a tabulky nebudou k dispozici, dokud se znovu nedokončí sestavení.

Pokud narazíte na nějaké problémy s datovým skladem, vytvořte [žádost o podporu](sql-data-warehouse-get-started-create-support-ticket.md) a odkaz "Gen2 upgrade" jako možnou příčinu.

Další informace najdete v tématu [upgrade na Gen2](upgrade-to-latest-generation.md).

## <a name="migration-frequently-asked-questions"></a>Nejčastější dotazy k migraci

**Otázka: Jsou Gen2 náklady stejné jako Gen1?**

- Odpověď: Ano.

**Otázka: Jak budou upgrady ovlivněny se skripty pro automatizaci?**

- Odpověď: Jakýkoli skript Automation, který odkazuje na cíl na úrovni služby, by měl být změněn tak, aby odpovídal ekvivalentu Gen2.  [Tady](upgrade-to-latest-generation.md#sign-in-to-the-azure-portal)najdete podrobnosti.

**Otázka: Jak dlouho obvykle trvá samočinný upgrade?**

- Odpověď: Můžete upgradovat na místě nebo upgradovat z bodu obnovení.  
   - Upgrade na místě způsobí, že se datový sklad přestane pozastavit a obnoví se.  Proces na pozadí bude pokračovat, i když je datový sklad online.  
   - Pokud provádíte upgrade prostřednictvím bodu obnovení, trvá to déle, protože upgrade projde procesem úplného obnovení.

**Otázka: Jak dlouho bude automatický upgrade trvat?**

- Odpověď: Skutečná délka výpadku kvůli upgradu představuje pouze dobu potřebnou k pozastavení a obnovení služby, což je 5 až 10 minut. Po krátkém výpadku proces na pozadí spustí migraci úložiště. Délka trvání procesu na pozadí závisí na velikosti datového skladu.

**Otázka: Kdy bude tento automatický upgrade probíhat?**

- Odpověď: Během plánu údržby. Využití zvoleného plánu údržby způsobí minimalizaci přerušení na vaši firmu.

**Otázka: Co mám dělat, když se můj proces upgradu na pozadí jeví jako zablokované?**

 - Odpověď: Vypíná přeindexaci tabulek columnstore. Všimněte si, že při této operaci bude přeindexace tabulky v režimu offline.

**Otázka: Co když Gen2 nemá cíl na úrovni služby I v Gen1?**
- Odpověď: Pokud používáte úroveň DW600 nebo DW1200 na Gen1, doporučujeme použít DW500c nebo DW1000c, protože Gen2 poskytuje více paměti, prostředků a vyšší výkon než Gen1.

**Otázka: Můžu zakázat geografickou zálohu?**
- Odpověď: Ne. Geografické zálohování je podniková funkce, která zachovává dostupnost datového skladu v případě, že oblast nebude k dispozici. Pokud máte další obavy, otevřete [žádost o podporu](sql-data-warehouse-get-started-create-support-ticket.md) .

**Otázka: Existuje rozdíl v syntaxi T-SQL mezi Gen1 a Gen2?**

- Odpověď: V syntaxi jazyka T-SQL nedošlo k žádné změně z Gen1 na Gen2.

**Otázka: Podporuje Gen2 okna údržby?**

- Odpověď: Ano.

**Otázka: Budu po upgradu své oblasti moct vytvořit novou instanci Gen1?**

- Odpověď: Ne. Po upgradu oblasti budou vytváření nových instancí Gen1 zakázané.

## <a name="next-steps"></a>Další postup

- [Postup upgradu](upgrade-to-latest-generation.md)
- [Okna údržby](maintenance-scheduling.md)
- [Monitorování stavu prostředků](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Kontrola před zahájením migrace](upgrade-to-latest-generation.md#before-you-begin)
- [Upgrade na místě a upgrade z bodu obnovení](upgrade-to-latest-generation.md)
- [Vytvoření bodu obnovení definovaného uživatelem](sql-data-warehouse-restore-points.md)
- [Informace o tom, jak obnovit Gen2](sql-data-warehouse-restore-active-paused-dw.md#restore-an-existing-data-warehouse-through-the-azure-portal)
- [Otevřete žádost o podporu SQL Data Warehouse](https://go.microsoft.com/fwlink/?linkid=857950)
