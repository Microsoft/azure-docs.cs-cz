---
title: Cenové úrovně pro Azure Database for MariaDB
description: Tento článek popisuje cenové úrovně pro Azure Database for MariaDB.
author: jan-eng
ms.author: janeng
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: b1b9353feb9142dd0709b89cffb942ec5efaf936
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2019
ms.locfileid: "71179175"
---
# <a name="azure-database-for-mariadb-pricing-tiers"></a>Azure Database for MariaDB cenové úrovně

Server Azure Database for MariaDB můžete vytvořit v jedné ze tří různých cenových úrovní: Basic, Pro obecné účely a paměť optimalizované. Cenové úrovně jsou rozlišené o množství výpočtů v virtuální jádra, které se dá zřídit, paměť na vCore a technologie úložiště, která se používá k ukládání dat. Všechny prostředky jsou zřízené na úrovni serveru MariaDB. Server může mít jednu nebo více databází.

|    | **Basic** | **Pro obecné účely** | **Paměťově optimalizovaná** |
|:---|:----------|:--------------------|:---------------------|
| Generace výpočetních prostředků | Gen 5 |Gen 5 | Gen 5 |
| Virtuální jádra | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Paměť na vCore | 2 GB | 5 GB | 10 GB |
| Velikost úložiště | 5 GB až 1 TB | 5 GB až 4 TB | 5 GB až 4 TB |
| Typ úložiště | Úložiště Azure úrovně Standard | Azure Premium Storage | Azure Premium Storage |
| Doba uchování zálohy databáze | 7 až 35 dní | 7 až 35 dní | 7 až 35 dní |

Pokud chcete zvolit cenovou úroveň, použijte jako výchozí bod následující tabulku.

| Cenová úroveň | Cílová zátěž |
|:-------------|:-----------------|
| Basic | Úlohy, které vyžadují lehký výpočetní výkon a vstupně-výstupní výkon. Mezi příklady patří servery používané pro vývoj nebo testování nebo pro nečasto používané aplikace v malých měřítkech. |
| Obecné použití | Většina obchodních úloh, které vyžadují vyvážené výpočetní prostředky a paměť s škálovatelnou vstupně-výstupní propustností. Mezi příklady patří servery pro hostování webových a mobilních aplikací a dalších podnikových aplikací.|
| Paměťově optimalizované | Vysoce výkonné databázové úlohy, které vyžadují výkon v paměti pro rychlejší zpracování transakcí a vyšší souběžnost. Mezi příklady patří servery pro zpracování dat v reálném čase a vysoce výkonné transakční nebo analytické aplikace.|

Po vytvoření serveru se dá změnit nebo snížit počet virtuální jádra a cenová úroveň (s výjimkou a od úrovně Basic) během několika sekund. Velikost úložiště můžete také nezávisle upravovat a dobu uchovávání záloh nahoru nebo dolů bez výpadků aplikací. Po vytvoření serveru nejde typ úložiště zálohy změnit. Další informace najdete v části [Scale](#scale-resources) Resources.

## <a name="compute-generations-and-vcores"></a>Generace a virtuální jádray výpočtů

Výpočetní prostředky se poskytují jako virtuální jádra, což představuje logický procesor základního hardwaru. Logické procesory Gen 5 jsou založené na procesorech Intel E5-2673 v4 (Broadwell) 2,3 GHz.

## <a name="storage"></a>Storage

Úložiště, které zřizujete, je množství úložné kapacity dostupné pro váš server Azure Database for MariaDB. Úložiště se používá pro soubory databáze, dočasné soubory, transakční protokoly a protokoly serveru MariaDB. Celková velikost úložiště, kterou zřizujete, také definuje kapacitu v/v k dispozici pro váš server.

|    | **Basic** | **Pro obecné účely** | **Paměťově optimalizovaná** |
|:---|:----------|:--------------------|:---------------------|
| Typ úložiště | Úložiště Azure úrovně Standard | Azure Premium Storage | Azure Premium Storage |
| Velikost úložiště | 5 GB až 1 TB | 5 GB až 4 TB | 5 GB až 4 TB |
| Velikost přírůstku úložiště | 1 GB | 1 GB | 1 GB |
| IOPS | Proměnná |3 IOPS/GB<br/>Minimální 100 IOPS<br/>Maximální 6000 IOPS | 3 IOPS/GB<br/>Minimální 100 IOPS<br/>Maximální 6000 IOPS |

Můžete přidat další úložnou kapacitu během a po vytvoření serveru a nechat systém, aby automaticky rozšiřoval úložiště na základě spotřeby úložiště vašich úloh.

>[!NOTE]
> Úložiště se dá škálovat jenom nahoru, ne dolů.

Úroveň Basic neposkytuje záruku IOPS. V Pro obecné účely a paměťově optimalizované cenové úrovně se za vstupně-výstupní operace v poměru 3:1 omezuje velikost IOPS s zřízenou velikostí úložiště.

Spotřebu vstupu a výstupu můžete monitorovat v Azure Portal nebo pomocí příkazů rozhraní příkazového řádku Azure CLI. Příslušné metriky, které je potřeba monitorovat [, jsou omezení úložiště, procento úložiště, využité úložiště a procento vstupně-výstupních operací](concepts-monitoring.md).

### <a name="reaching-the-storage-limit"></a>Dosáhlo se limitu úložiště.

Servery s méně než 100 GB zřízené úložiště jsou označené jen pro čtení, pokud je volné úložiště menší než 512 MB nebo 5% velikosti zřízeného úložiště. Servery s více než 100 GB zřízené úložiště jsou označené jen pro čtení, pokud je volné úložiště menší než 5 GB.

Pokud jste například zřídili 110 GB úložiště a skutečné využití dosáhne více než 105 GB, server je označen jen pro čtení. Případně, pokud jste zřídili 5 GB úložiště, server je označen jen pro čtení, pokud volné úložiště dosáhne méně než 256 MB.

Zatímco se služba pokouší nastavit server jen pro čtení, všechny požadavky transakcí zápisu se zablokují a stávající aktivní transakce se budou provádět dál. Když je server nastavený jen pro čtení, všechny další operace zápisu a potvrzení transakcí selžou. Dotazy na čtení budou fungovat dál bez přerušení. Jakmile navýšíte velikost zřízeného úložiště, bude server připravený znovu přijímat transakce zápisu.

Doporučujeme zapnout automatické zvětšování úložiště nebo nastavit výstrahu, která vás upozorní, když se serverové úložiště blíží prahové hodnotě, takže se můžete vyhnout přístupu do stavu jen pro čtení. Další informace najdete v dokumentaci k [Nastavení výstrahy](howto-alert-metric.md).

### <a name="storage-auto-grow"></a>Automatické zvětšování úložiště

Automatické zvětšování úložiště zabrání vašemu serveru v provozu z úložiště a nejenom pro čtení. Pokud je povolené automatické zvětšování úložiště, úložiště se automaticky zvětšuje, aniž by to ovlivnilo zatížení. U serverů s úložištěm menším než 100 GB zřízené úložiště se velikost zřízeného úložiště zvyšuje o 5 GB, pokud je volné úložiště menší než 10% zřízeného úložiště. U serverů s více než 100 GB zřízeného úložiště se velikost zřízeného úložiště zvyšuje o 5%, pokud je volný prostor úložiště menší než 10% velikosti zřízené úložiště. Platí omezení maximální velikosti úložiště, jak je uvedeno výše.

Pokud jste například zřídili 1000 GB úložiště a skutečné využití dosáhne více než 900 GB, zvýší se velikost úložiště serveru na 1050 GB. Případně, pokud jste zřídili 10 GB úložiště, velikost úložiště se zvýší na 15 GB, pokud je úložiště menší než 1 GB volného místa.

Mějte na paměti, že úložiště je možné škálovat pouze nahoru, ne dolů.

## <a name="backup"></a>Zálohování

Služba automaticky provede zálohování vašeho serveru. Minimální doba uchovávání záloh je sedm dní. Můžete nastavit dobu uchovávání až 35 dní. Uchovávání lze v jakémkoli okamžiku během životnosti serveru upravit. Můžete si vybrat mezi místně redundantními a geograficky redundantními zálohováními. Geograficky redundantní zálohy jsou také uloženy v [geograficky spárované oblasti](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) oblasti, kde je server vytvořen. Tato redundance poskytuje úroveň ochrany v případě havárie. Máte také možnost obnovit server do jakékoli jiné oblasti Azure, ve které je služba dostupná v geograficky redundantních zálohách. Po vytvoření serveru není možné měnit mezi dvěma možnostmi úložiště zálohování.

## <a name="scale-resources"></a>Škálování prostředků

Po vytvoření serveru můžete nezávisle změnit virtuální jádra, cenovou úroveň (s výjimkou a od úrovně Basic), velikost úložiště a dobu uchování zálohy. Po vytvoření serveru nejde typ úložiště zálohy změnit. Počet virtuální jádra se dá škálovat nahoru nebo dolů. Doba uchovávání záloh se dá škálovat nahoru nebo dolů od 7 do 35 dnů. Velikost úložiště se dá zvýšit jenom. Škálování prostředků se dá provést buď prostřednictvím portálu, nebo pomocí Azure CLI. 

<!--For an example of scaling by using Azure CLI, see [Monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->

Když změníte počet virtuální jádra nebo cenovou úroveň, vytvoří se kopie původního serveru s novým přidělením výpočtů. Po zprovoznění nového serveru se připojení přepnou na nový server. Během přepínání systému na nový server není možné navazovat nová připojení a všechny nepotvrzené transakce se vrátí zpět. Tento časový interval je různý, ale ve většině případů je kratší než minuta.

Škálování úložiště a Změna doby uchovávání záloh jsou pravdivé online operace. Nedochází k výpadkům a vaše aplikace to nijak neovlivní. Když se v případě IOPS škáluje velikost zřízeného úložiště, můžete prodloužit kapacitu úložiště, která je pro váš server dostupná.

## <a name="pricing"></a>Ceny

Nejaktuálnější informace o cenách najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/mariadb/)služeb. Chcete-li zobrazit náklady na konfiguraci, kterou požadujete, [Azure Portal](https://portal.azure.com/#create/Microsoft.MariaDBServer) na kartě **cenová úroveň** na základě možností, které jste vybrali, zobrazí měsíční náklady. Pokud nemáte předplatné Azure, můžete získat odhadovanou cenu pomocí cenové kalkulačky Azure. Na webu [cenové kalkulačky Azure](https://azure.microsoft.com/pricing/calculator/) vyberte **Přidat položky**, rozbalte kategorii **databáze** a zvolte **Azure Database for MariaDB** pro přizpůsobení možností.

## <a name="next-steps"></a>Další kroky
- Přečtěte si o [omezeních služeb](concepts-limits.md).
- Naučte se [vytvářet MariaDB Server v Azure Portal](quickstart-create-mariadb-server-database-using-azure-portal.md).

<!--
- Learn how to [monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->
