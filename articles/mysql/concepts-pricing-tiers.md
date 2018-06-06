---
title: Cenové úrovně pro databázi Azure pro databázi MySQL
description: Tento článek popisuje cenové úrovně pro databázi Azure pro databázi MySQL.
services: mysql
author: jan-eng
ms.author: janeng
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 05/18/2018
ms.openlocfilehash: 0778492e6ff63bc80e9fc9d2c252f19d4ff78529
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757481"
---
# <a name="azure-database-for-mysql-pricing-tiers"></a>Azure databáze pro databázi MySQL cenové úrovně

Můžete vytvořit databázi Azure pro server databáze MySQL v jednom ze tří různých cenových úrovní: Basic, obecné účely a paměťově optimalizovaná. Cenové úrovně jsou rozlišené pomocí objem výpočtů v vCores, který může být zřízen, paměť na vCore a technologie úložiště používat k ukládání dat. Všechny prostředky připravené na úrovni serveru MySQL. Server může mít jednu nebo více databází.

|    | **Basic** | **Obecné účely** | **Paměťově optimalizované** |
|:---|:----------|:--------------------|:---------------------|
| Výpočetní generování | Gen 4, 5. generace | Gen 4, 5. generace | Gen 5 |
| Virtuální jádra | 1, 2 | 2, 4, 8, 16, 32 |2, 4, 8, 16 |
| Paměť na vCore | 2 GB | 5 GB | 10 GB |
| Velikost úložiště | 5 GB až 1 TB | 5 GB až 2 TB | 5 GB až 2 TB |
| Typ úložiště | Úložiště Azure úrovně Standard | Azure Premium Storage | Azure Premium Storage |
| Doba uchovávání záloh databáze | 7 až 35 dnů | 7 až 35 dnů | 7 až 35 dnů |

Chcete-li zvolte cenovou úroveň, použijte jako výchozí bod v následující tabulce.

| Cenová úroveň | Cílová zátěž |
|:-------------|:-----------------|
| Basic | Úlohy, které vyžadují světla výpočetní a vstupně-výstupní výkon. Mezi příklady patří servery, které jsou používány pro vývoj nebo testování nebo méně rozsáhlá zřídka používané aplikace. |
| Obecné použití | Většina obchodní úlohy, které vyžadují vyrovnáváním paměťovou a výpočetní s škálovatelná propustnost vstupu/výstupu. Mezi příklady patří servery pro hostování webové a mobilní aplikace a jiné podnikové aplikace.|
| Paměťově optimalizované | Databáze vysoce výkonné úlohy, které vyžadují výkon v paměti pro rychlejší zpracování transakcí a vyšší souběžnosti. Mezi příklady patří servery pro zpracování dat v reálném čase a vysoce výkonné transakcí a analytické aplikací.|

Po vytvoření serveru počet vCores lze změnit nahoru nebo dolů (v rámci stejné cenové úrovně) během několika sekund. Také můžete samostatně upravit velikost úložiště nahoru a dobu uchovávání záloh nahoru nebo dolů s žádné výpadky aplikací. Po vytvoření serveru nelze změnit typ úložiště pro zálohy nebo cenovou úroveň. Další informace najdete v tématu [škálovat prostředky](#scale-resources) části.

## <a name="compute-generations-and-vcores"></a>Výpočetní generace a vCores

Výpočetní prostředky jsou k dispozici jako vCores, která představují logické procesoru základní hardware. V současné době můžete ze dvou generací výpočetní Gen 4 a 5. generace. Gen 4 logické procesory jsou založené na v3 Intel E5-2673 2,4 GHz procesorů (Haswell). Gen 5 logické procesory jsou založené na Intel E5-2673 v4 procesory 2.3 GHz (Broadwell). Gen 4 a 5 generace jsou k dispozici v následujících oblastech ("X" označuje k dispozici). 

| **Oblast Azure** | **Gen 4** | **Gen 5** |
|:---|:----------:|:--------------------:|
| Střed USA | X |  |
| Východ USA | X | X |
| Východní USA 2 | X | X |
| Střed USA – sever | X |  |
| Střed USA – jih | X | X |
| Západní USA | X | X |
| Západní USA 2 |  | X |
| Střední Kanada | X | X |
| Východní Kanada | X | X |
| Brazílie – jih | X | X |
| Severní Evropa | X | X |
| Západní Evropa |  | X |
| Spojené království – západ |  | X |
| Spojené království – jih |  | X |
| Východní Asie | X |  |
| Jihovýchodní Asie | X | X |
| Austrálie – východ |  | X |
| Austrálie – jihovýchod |  | X |
| Střed Indie | X | X |
| Indie – západ | X | X |
| Indie – jih |  | X |
| Japonsko – východ | X | X |
| Japonsko – západ | X | X |
| Korea – jih |  | X |

## <a name="storage"></a>Úložiště

Úložiště, který zřídíte je množství kapacity úložiště k dispozici k vaší databázi Azure pro server databáze MySQL. Úložiště se používá pro soubory databáze, dočasné soubory, transakční protokoly a MySQL server protokoly. Celkovou velikost úložiště, který zřídíte také definuje dostupná kapacita vstupně-výstupních operací na váš server.

|    | **Basic** | **Obecné účely** | **Paměťově optimalizované** |
|:---|:----------|:--------------------|:---------------------|
| Typ úložiště | Úložiště Azure úrovně Standard | Azure Premium Storage | Azure Premium Storage |
| Velikost úložiště | 5 GB až 1 TB | 5 GB až 2 TB | 5 GB až 2 TB |
| Velikost úložiště přírůstku | 1 GB | 1 GB | 1 GB |
| IOPS | Proměnná |3 IOPS/GB<br/>Min 100 IOPS | 3 IOPS/GB<br/>Min 100 IOPS |

Další úložiště kapacity můžete přidat během a po vytvoření serveru. Základní vrstvě neposkytuje záruku IOPS. V obecné účely a paměťově optimalizované cenové úrovně IOPS škálování se velikost zřízeného úložiště v poměru 3:1.

Můžete monitorovat vaší spotřeby vstupně-výstupních operací na portálu Azure nebo pomocí rozhraní příkazového řádku Azure. Metriku relevantní pro monitorování jsou [limit úložiště, procento úložiště, používá úložiště a vstupně-výstupní operace procent](concepts-monitoring.md).

### <a name="reaching-the-storage-limit"></a>Dosažení limitu úložiště

Server je označen jen pro čtení, když velikost volného místa dosáhne menší než 5 GB nebo zřízené úložiště % 5, podle toho, která je menší. Například, pokud máte zřízen 100 GB úložiště, a skutečné využití prochází přes 95 GB, server je označen jen pro čtení. Alternativně zřízením 5 GB úložiště, server je označen jen pro čtení Pokud volný úložný prostor dosáhne méně než 250 MB.  

Když se služba pokusí zajistí, že server jen pro čtení, všechny nové požadavky na zápis transakce jsou zablokované a provést bude pokračovat existující aktivních transakcí. Pokud server je nastaven na jen pro čtení, všechny následné zápisu operace a transakce potvrdí selhání. Dotazy pro čtení budou nadále fungovat bez přerušení. Jakmile zvýšíte zřízené úložiště, server bude připravena přijímat transakcí zápisu znovu.

## <a name="backup"></a>Backup

Služba automaticky provede zálohování serveru. Doba uchování minimální zálohy je sedm dní. Můžete nastavit dobu uchování o délce až 35 dnů. Uchovávání lze upravit kdykoli po dobu platnosti na server. Můžete zvolit místně redundantní a geograficky redundantní zálohy. Geograficky redundantní zálohy se také ukládají do [spárovat geografické oblasti](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) oblasti, kde se má vytvořit váš server. Tato redundance poskytuje úroveň ochrany v případě havárie. Získáte také možnost obnovit do jiné Azure oblasti, ve kterém je služba k dispozici s geograficky redundantní zálohy vašeho serveru. Není možné změnit ze dvou možností úložiště záloh, po vytvoření serveru.

## <a name="scale-resources"></a>Škálování prostředků

Po vytvoření serveru, můžete nezávisle změnit vCores, velikost úložiště a dobu uchovávání záloh. Po vytvoření serveru nelze změnit typ úložiště pro zálohy nebo cenovou úroveň. Počet vCores můžete škálovat nahoru nebo dolů v rámci stejné cenovou úroveň. Doba uchovávání záloh můžete škálovat nahoru nebo dolů z 7 na 35 dní. Velikost úložiště může být pouze zvýšena. Škálování prostředků můžete udělat buď prostřednictvím portálu nebo rozhraní příkazového řádku Azure. Příklad škálování pomocí rozhraní příkazového řádku Azure, naleznete v části [sledování a škálování Azure databáze MySQL server pomocí rozhraní příkazového řádku Azure pro](scripts/sample-scale-server.md).

Při změně počtu vCores kopii původního serveru se vytvoří se nové přidělení výpočetní. Po nový server je spuštěný a funkční, připojení se přepnutí na nový server. Během okamžiku, kdy systému přepne na nový server žádná nová připojení lze navázat a jsou všechny nepotvrzené transakce vráceny zpět. Toto okno se liší, ale ve většině případů je méně než minutu.

Škálování úložiště a změnit období uchovávání záloh jsou true online operace. Neexistuje žádné výpadky, a aplikace nemá vliv. Jako IOPS škálování se velikost zřízeného úložiště, můžete zvýšit IOPS, která je k dispozici na váš server ve vertikálním navýšení kapacity úložiště.

## <a name="pricing"></a>Ceny

Aktuální cenová informace najdete v tématu službu [stránce s cenami](https://azure.microsoft.com/pricing/details/mysql/). Zobrazit náklady pro konfiguraci chcete, [portál Azure](https://portal.azure.com/#create/Microsoft.MySQLServer) zobrazuje měsíční náklady na **cenová úroveň** karta podle možností, které zvolíte. Pokud nemáte předplatné Azure, můžete získat odhadované ceny Azure cenové kalkulačky. Na [Azure cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) web, vyberte **přidat položky**, rozbalte **databáze** kategorie a zvolte **Azure Database pro databázi MySQL**přizpůsobit možnosti.

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [vytvoření databáze MySQL serveru portálu](howto-create-manage-server-portal.md).
- Zjistěte, jak [sledování a škálování Azure Database MySQL serveru pomocí rozhraní příkazového řádku Azure](scripts/sample-scale-server.md).
- Další informace o [služby omezení](concepts-limits.md).
