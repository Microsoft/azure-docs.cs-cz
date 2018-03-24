---
title: Cenové úrovně v databázi Azure pro PostgreSQL
description: Tento článek popisuje cenové úrovně v databázi Azure pro PostgreSQL.
services: postgresql
author: jan-eng
ms.author: janeng
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 03/20/2018
ms.openlocfilehash: 21f8eb795aa1675e2bbd5284f88b39c76ad59228
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-database-for-postgresql-pricing-tiers"></a>Azure databázi PostgreSQL cenové úrovně

Databázi Azure pro PostgreSQL server lze vytvořit v jednom ze tří různých cenových úrovní - Basic, obecné účely a paměťově optimalizovaná. Cenové úrovně jsou rozlišené pomocí objem výpočtů v vCores, který může být zřízen, paměť na vCore a technologie úložiště používat k ukládání dat. Všechny prostředky připravené na úrovni serveru PostgreSQL. Server může mít jednu nebo více databází.

|    | **Basic** | **Obecné účely** | **Paměťově optimalizované** |
|:---|:----------|:--------------------|:---------------------|
| Výpočetní generování | Gen 4, Gen 5 | Gen 4, Gen 5 | Gen 5 |
| vCores | 1, 2 | 2, 4, 8, 16, 32 |2, 4, 8, 16 |
| Paměť na vCore | 1x | 2x Basic | 2 x obecné účely |
| Velikost Storage | 5 GB až 1 TB | 5 GB až 1 TB | 5 GB až 1 TB |
| Typ úložiště | Úložiště Azure úrovně Standard | Azure Premium Storage | Azure Premium Storage |
| Doba uchovávání záloh databáze | 7 - 35 dní | 7 - 35 dní | 7 - 35 dní |

V následující tabulce lze použít jako východisko pro výběr cenové úrovně:

| Cenová úroveň | Cílová zátěž |
|:-------------|:-----------------|
| Basic | Úlohy vyžadující nízký výpočetní a I/O výkon. Příkladem mohou být servery používané pro vývoj a testování nebo pro malé a nepříliš často využívané aplikace. |
| Obecné použití | Většina obchodních úloh vyžaduje vyvážené výpočetní a paměťové prostředky se škálovatelnou I/O propustností. Mezi příklady patří server pro hostování webové a mobilní aplikace a jiné podnikové aplikace.|
| Paměťově optimalizované | Vysoce výkonné databáze procesy vyžadující výkonu v paměti pro rychlejší zpracování transakcí a vyšší souběžnosti. Mezi příklady patří server pro zpracování dat v reálném čase a vysoký výkon transakcí a analytické aplikací.|

Po vytvoření serveru počet vCores lze změnit nahoru nebo dolů v sekundách. Můžete také nezávisle upravit velikost úložiště nahoru a dobu uchovávání záloh nahoru nebo dolů s žádné výpadky aplikací. Najdete v části škálování níže další podrobnosti.

## <a name="compute-generations-vcores-and-memory"></a>Výpočetní generace, vCores a paměti

Výpočetní prostředky jsou uvedeny jako vCores, představující logického procesoru základní hardware. V současné době dvou generací výpočetní Gen 4 a 5 generace jsou nabízena můžete vybírat. Logické CPU 4. generace jsou založené na procesorech Intel E5-2673 v3 (Haswell) 2,4 GHz. Logické CPU 5. generace jsou založené na procesorech Intel E5-2673 v4 (Broadwell) 2,3 GHz. Gen 4 a 5 generace jsou k dispozici v následujících oblastech ("X" označuje k dispozici): 

| **Azure Region** | **Generování 4** | **Generování 5** |
|:---|:----------:|:--------------------:|
| Střed USA |  | X |
| Východ USA | X | X |
| Východní USA 2 | X |  |
| Střed USA – sever | X |  |
| Střed USA – jih | X |  |
| Západní USA | X | X |
| Západní USA 2 |  | X |
| Střední Kanada | X | X |
| Východní Kanada | X | X |
| Brazílie – jih | X |  |
| Severní Evropa | X | X |
| Západní Evropa | X | X |
| Spojené království – západ |  | X |
| Spojené království – jih |  | X |
| Východní Asie | X |  |
| Jihovýchodní Asie | X |  |
| Austrálie – východ |  | X |
| Střed Indie | X |  |
| Indie – západ | X |  |
| Japonsko – východ | X |  |
| Japonsko – západ | X |  |
| Korea – jih |  | X |

V závislosti na cenovou úroveň je zajištěna každý vCore s určitou velikostí paměti. Při zvýšení nebo snížení počtu vCores pro váš server, paměť zvýší nebo sníží úměrně. Úroveň obecné účely poskytuje double množství paměti na vCore ve srovnání s základní vrstvě. Paměťově optimalizovaná vrstvy poskytuje double množství paměti ve srovnání s vrstvě obecné účely.

## <a name="storage"></a>Úložiště

Úložiště, který zřídíte je množství kapacity úložiště k dispozici k vaší databázi Azure pro PostgreSQL server. Úložiště se používá pro soubory databáze, dočasné soubory, transakční protokoly a PostgreSQL server protokoly. Celkovou velikost úložiště, který zřídíte také definuje dostupná kapacita vstupně-výstupních operací na váš server:

|    | **Basic** | **Obecné účely** | **Paměťově optimalizované** |
|:---|:----------|:--------------------|:---------------------|
| Typ úložiště | Úložiště Azure úrovně Standard | Azure Premium Storage | Azure Premium Storage |
| Velikost Storage | 5 GB až 1 TB | 5 GB až 1 TB | 5 GB až 1 TB |
| Velikost úložiště přírůstku | 1 GB | 1 GB | 1 GB |
| IOPS | Proměnná |3 IOPS/GB<br/>Min 100 IOPS | 3 IOPS/GB<br/>Min 100 IOPS |

Během a po vytvoření serveru se dá přidat kapacitu další úložiště. Základní vrstvě neposkytuje záruku IOPS. V obecné účely a paměťově optimalizované cenové úrovně IOPS škálování se velikost zřízeného úložiště v poměru 3:1.

Můžete monitorovat vaší spotřeby vstupně-výstupních operací v portálu Azure nebo pomocí rozhraní příkazového řádku Azure. Metriku relevantní pro monitorování jsou [limit úložiště, procento úložiště, používá úložiště a vstupně-výstupní operace procent](concepts-monitoring.md).

## <a name="backup"></a>Backup

Služba automaticky provede zálohování serveru. Doba uchování minimální zálohy je sedm dní. Můžete nastavit dobu uchování o délce až 35 dnů. Uchovávání lze upravit kdykoli po dobu platnosti na server. Můžete zvolit místně redundantní a geograficky redundantní zálohy. Geograficky redundantní zálohy jsou uloženy ve [spárovat geografické oblasti](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) oblasti vašeho serveru se vytvoří v. To poskytuje úroveň ochrany v případě havárie. Získáte také možnost obnovit do jiné Azure oblasti, ve kterém je služba k dispozici s geograficky redundantní zálohy vašeho serveru. Všimněte si, že není možné změnit ze dvou možností úložiště záloh, jakmile je vytvořena serveru.

## <a name="scale-resources"></a>Škálování prostředků

Po vytvoření serveru, můžete nezávisle změnit vCores, velikost úložiště a dobu uchovávání záloh. Po vytvoření serveru nelze změnit typ úložiště pro zálohy nebo cenovou úroveň. vCores a období uchovávání záloh můžete škálovat nahoru nebo dolů. Velikost úložiště může být pouze zvýšena. Škálování prostředků můžete udělat buď prostřednictvím portálu nebo rozhraní příkazového řádku Azure. Příklad pro škálování pomocí rozhraní příkazového řádku můžete najít [zde](scripts/sample-scale-server-up-or-down.md).

Při změně počtu vCores, se s nové přidělení výpočetní vytvoří kopii původního serveru. Jakmile nový server je spuštěný a funkční, připojení jsou přepnutí na nový server. Během stručný chvíli, kdy systému přepne na nový server žádná nová připojení lze navázat a jsou všechny nepotvrzené transakce vráceny zpět. Toto okno se liší, ale ve většině případů je méně než minutu.

Škálování úložiště a změnit období uchovávání záloh jsou true online operace. Neexistuje žádný výpadek ani dopad do vaší aplikace. Jako IOPS škálování se velikost zřízeného úložiště, můžete zvýšit IOPS, která je k dispozici na váš server ve vertikálním navýšení kapacity úložiště.

## <a name="pricing"></a>Ceny

Zkontrolujte službu [stránce s cenami](https://azure.microsoft.com/pricing/details/PostgreSQL/) pro co nejaktuálnější informace o cenách. Zobrazit vaše náklady, požadované konfigurace, [portál Azure](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) ukazuje měsíční náklady v **cenová úroveň** karty založené na vybrané možnosti. Pokud nemáte předplatné Azure, můžete získat odhadované ceny Azure cenové kalkulačky. Navštivte [Azure cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) web, pak klikněte na tlačítko **přidat položky**, rozbalte **databáze** kategorie a zvolte **databáze Azure pro PostgreSQL**  přizpůsobit možnosti.

## <a name="next-steps"></a>Další kroky

- Zjistěte, jak [vytvoření serveru PostgreSQL v portálu.](tutorial-design-database-using-azure-portal.md)
- Zjistěte, jak [sledování a škálování Azure Database pro PostgreSQL server pomocí rozhraní příkazového řádku Azure](scripts/sample-scale-server-up-or-down.md)
- Další informace o [služby omezení](concepts-limits.md)
