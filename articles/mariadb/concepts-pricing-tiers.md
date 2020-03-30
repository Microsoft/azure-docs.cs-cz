---
title: Cenové úrovně – Azure Database for MariaDB
description: Seznamte se s různými cenovými úrovněmi pro Azure Database for MariaDB, včetně výpočetních generací, typů úložišť, velikosti úložiště, virtuálních jader, paměti a období uchovávání záloh.
author: jan-eng
ms.author: janeng
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 39af6850810fa471003cea27ed274972fb2ff046
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528026"
---
# <a name="azure-database-for-mariadb-pricing-tiers"></a>Databáze Azure pro cenové úrovně MariaDB

Databázi Azure pro server MariaDB můžete vytvořit v jedné ze tří různých cenových úrovní: Basic, General Purpose a Memory Optimized. Cenové úrovně se liší podle množství výpočetních prostředků ve virtuálních jádrech, které lze zřídit, paměti na virtuální jádro a technologie úložiště používané k ukládání dat. Všechny prostředky jsou zřízeny na úrovni serveru MariaDB. Server může mít jednu nebo více databází.

|    | **Basic** | **Obecný účel** | **Optimalizováno pro paměť** |
|:---|:----------|:--------------------|:---------------------|
| Výpočetní generace | Gen 5 |Gen 5 | Gen 5 |
| virtuální jádra | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Paměť na virtuální jádro | 2 GB | 5 GB | 10 GB |
| Velikost úložiště | 5 GB až 1 TB | 5 GB až 4 TB | 5 GB až 4 TB |
| Doba uchovávání záloh v databázi | 7 až 35 dní | 7 až 35 dní | 7 až 35 dní |

Chcete-li zvolit cenovou úroveň, použijte jako výchozí bod následující tabulku.

| Cenová úroveň | Cílová zátěž |
|:-------------|:-----------------|
| Basic | Úlohy, které vyžadují lehký výpočetní a vstupně-v.I výkon. Příklady zahrnují servery používané pro vývoj nebo testování nebo malé měřítko zřídka používané aplikace. |
| Pro obecné účely | Většina obchodních úloh, které vyžadují vyvážené výpočetní prostředky a paměť s škálovatelnou propustností vstupně-v.a. Příklady zahrnují servery pro hostování webových a mobilních aplikací a dalších podnikových aplikací.|
| Optimalizováno pro paměť | Vysoce výkonné databázové úlohy, které vyžadují výkon v paměti pro rychlejší zpracování transakcí a vyšší souběžnost. Mezi příklady patří servery pro zpracování dat v reálném čase a vysoce výkonné transakční nebo analytické aplikace.|

Po vytvoření serveru lze počet virtuálních jader a cenové úrovně (s výjimkou do a ze basicu) změnit nahoru nebo dolů během několika sekund. Můžete také nezávisle upravit velikost úložiště nahoru a dobu uchování zálohy nahoru nebo dolů bez prostojů aplikace. Po vytvoření serveru nelze změnit typ úložiště záloh. Další informace naleznete v části [Škálování prostředků.](#scale-resources)

## <a name="compute-generations-and-vcores"></a>Výpočetní generace a virtuální jádra

Výpočetní prostředky jsou k dispozici jako virtuální jádra, které představují logický procesor základního hardwaru. Logické procesory Gen 5 jsou založeny na procesorech Intel E5-2673 v4 (Broadwell) 2.3-GHz.

## <a name="storage"></a>Úložiště

Úložiště, které zřídíte, je množství kapacity úložiště, které je k dispozici pro váš server Azure Database pro server MariaDB. Úložiště se používá pro databázové soubory, dočasné soubory, protokoly transakcí a protokoly serveru MariaDB. Celková velikost úložiště, které zřídíte, také definuje kapacitu vstupně-videa, která je k dispozici pro váš server.

|    | **Basic** | **Obecný účel** | **Optimalizováno pro paměť** |
|:---|:----------|:--------------------|:---------------------|
| Typ úložiště | Základní úložiště | Úložiště pro všeobecné účely | Úložiště pro všeobecné účely |
| Velikost úložiště | 5 GB až 1 TB | 5 GB až 4 TB | 5 GB až 4 TB |
| Velikost přírůstku úložiště | 1 GB | 1 GB | 1 GB |
| IOPS | Proměnná |3 iOPS/GB<br/>Min 100 IOPS<br/>Maximální množství 6000 IOPS | 3 iOPS/GB<br/>Min 100 IOPS<br/>Maximální množství 6000 IOPS |

Můžete přidat další kapacitu úložiště během a po vytvoření serveru a umožnit systému automaticky rozšiřovat úložiště na základě spotřeby úložiště vašeho pracovního vytížení.

>[!NOTE]
> Úložiště lze škálovat pouze nahoru, nikoli dolů.

Úroveň Basic neposkytuje záruku vstupně-operace. V cenových úrovních optimalizované pro obecné účely a paměť se škálování VOPS s velikostí zřízeného úložiště v poměru 3:1.

Spotřebu vstupně-va můžete sledovat na webu Azure Portal nebo pomocí příkazů Azure CLI. Relevantní metriky, které je třeba sledovat, jsou [limit úložiště, procento úložiště, použité úložiště a procento vi .](concepts-monitoring.md)

### <a name="reaching-the-storage-limit"></a>Dosažení limitu úložiště

Servery s méně než 100 GB zřízeného úložiště jsou označeny jen pro čtení, pokud je bezplatné úložiště menší než 5 % zřízenévelikosti úložiště. Servery s více než 100 GB zřízeného úložiště se označí jako jen pro čtení v případě, že velikost volného úložiště klesne pod 5 GB.

Například pokud jste zřídit 110 GB úložiště a skutečné využití přejde přes 105 GB, server je označen jen pro čtení. Případně pokud jste zřídit 5 GB úložiště, server je označen jen pro čtení, když volné úložiště dosáhne méně než 256 MB.

Zatímco se služba pokouší nastavit server jen pro čtení, všechny požadavky transakcí zápisu se zablokují a stávající aktivní transakce se budou provádět dál. Když je server nastavený jen pro čtení, všechny další operace zápisu a potvrzení transakcí selžou. Dotazy na čtení budou fungovat dál bez přerušení. Jakmile navýšíte velikost zřízeného úložiště, bude server připravený znovu přijímat transakce zápisu.

Doporučujeme zapnout automatické zvětšování úložiště nebo nastavit výstrahu, která vás upozorní, když se úložiště serveru blíží prahové hodnotě, abyste se vyhnuli tomu, že se dostanete do stavu jen pro čtení. Další informace naleznete v dokumentaci [k nastavení výstrahy](howto-alert-metric.md).

### <a name="storage-auto-grow"></a>Automatické růst úložiště

Automatické zvětšování úložiště zabraňuje serveru v nedostatku úložiště a stává se jen pro čtení. Pokud úložiště automatické růst je povolena, úložiště automaticky roste bez dopadu na zatížení. Pro servery s méně než 100 GB zřízeného úložiště, zřízené úložiště se zvýší o 5 GB, když je volné úložiště nižší než 10 % zřízeného úložiště. U serverů s více než 100 GB zřízeného úložiště se zřízené úložiště zvýší o 5 %, když je volný úložný prostor menší než 10 GB zřízené velikosti úložiště. Platí maximální limity úložiště, jak je uvedeno výše.

Například pokud jste zřídit 1000 GB úložiště a skutečné využití přejde přes 990 GB, velikost úložiště serveru se zvýší na 1050 GB. Případně pokud jste zřídit 10 GB úložiště, velikost úložiště se zvětší na 15 GB, pokud je méně než 1 GB úložiště zdarma.

Nezapomeňte, že úložiště lze škálovat pouze nahoru, nikoli dolů.

## <a name="backup"></a>Zálohování

Služba automaticky přebírá zálohy serveru. Můžete vybrat dobu uchovávání od 7 do 35 dnů. Servery s optimalizací pro obecné účely a paměť se mohou rozhodnout, že budou mít geograficky redundantní úložiště pro zálohování. Další informace o zálohách v [článku koncepty](concepts-backup.md).

## <a name="scale-resources"></a>Škálování prostředků

Po vytvoření serveru můžete nezávisle změnit virtuální jádra, cenovou úroveň (s výjimkou do a ze Basicu), velikost úložiště a dobu uchování zálohy. Po vytvoření serveru nelze změnit typ úložiště záloh. Počet virtuálních jader lze škálovat nahoru nebo dolů. Dobu uchování zálohy lze zvětšit ze 7 na 35 dní. Velikost úložiště lze pouze zvýšit. Škálování prostředků lze provést buď prostřednictvím portálu nebo Azure CLI. 

<!--For an example of scaling by using Azure CLI, see [Monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->

Když změníte počet virtuálních jader nebo cenovou úroveň, vytvoří se kopie původního serveru s novým přidělením výpočetních prostředků. Po zprovoznění nového serveru se připojení přepnou na nový server. Během přepínání systému na nový server není možné navazovat nová připojení a všechny nepotvrzené transakce se vrátí zpět. Tento časový interval je různý, ale ve většině případů je kratší než minuta.

Škálování úložiště a změna doby uchování zálohy jsou skutečné online operace. Neexistuje žádné prostoje a vaše aplikace není ovlivněna. Při škálování vstupně-up s velikostí zřízeného úložiště můžete zvýšit iOPS, které jsou k dispozici pro váš server, navýšením kapacity úložiště.

## <a name="pricing"></a>Ceny

Nejaktuálnější informace o cenách najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/mariadb/)služeb . Chcete-li zobrazit náklady na požadovanou konfiguraci, [portál Azure](https://portal.azure.com/#create/Microsoft.MariaDBServer) zobrazuje měsíční náklady na kartě **Cenová úroveň** na základě možností, které vyberete. Pokud nemáte předplatné Azure, můžete použít cenovou kalkulačku Azure k získání odhadované ceny. Na webu [Cenové kalkulačky Azure](https://azure.microsoft.com/pricing/calculator/) vyberte **Přidat položky**, rozbalte kategorii **Databáze** a zvolte Azure Database **for MariaDB** a přizpůsobte možnosti.

## <a name="next-steps"></a>Další kroky
- Informace o [omezeních služeb](concepts-limits.md).
- Přečtěte si, jak [vytvořit server MariaDB na webu Azure Portal](quickstart-create-mariadb-server-database-using-azure-portal.md).

<!--
- Learn how to [monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->
