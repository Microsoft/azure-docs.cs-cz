---
title: Cenové úrovně – Azure Database for MariaDB
description: Seznamte se s různými cenovými úrovněmi pro Azure Database for MariaDB, včetně výpočetních generací, typů úložišť, velikosti úložiště, virtuální jádra, paměti a období uchovávání záloh.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: b5b5a506b2f932d20a617634ace7ebf02093fbfa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664244"
---
# <a name="azure-database-for-mariadb-pricing-tiers"></a>Azure Database for MariaDB cenové úrovně

Server Azure Database for MariaDB můžete vytvořit v jedné ze tří různých cenových úrovní: optimalizováno Basic, Pro obecné účely a paměť. Cenové úrovně jsou rozlišené o množství výpočtů v virtuální jádra, které se dá zřídit, paměť na vCore a technologie úložiště, která se používá k ukládání dat. Všechny prostředky jsou zřízené na úrovni serveru MariaDB. Server může mít jednu nebo více databází.

| Prostředek | **Basic** | **Obecné použití** | **Paměťově optimalizovaná** |
|:---|:----------|:--------------------|:---------------------|
| Generování výpočtů | Gen 5 |Gen 5 | Gen 5 |
| Virtuální jádra | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Paměť na vCore | 2 GB | 5 GB | 10 GB |
| Velikost úložiště | 5 GB až 1 TB | 5 GB až 4 TB | 5 GB až 4 TB |
| Doba uchování zálohy databáze | 7 až 35 dní | 7 až 35 dní | 7 až 35 dní |

Pokud chcete zvolit cenovou úroveň, použijte jako výchozí bod následující tabulku.

| Cenová úroveň | Cílová zátěž |
|:-------------|:-----------------|
| Basic | Úlohy, které vyžadují lehký výpočetní výkon a vstupně-výstupní výkon. Mezi příklady patří servery používané pro vývoj nebo testování nebo pro nečasto používané aplikace v malých měřítkech. |
| Pro obecné účely | Většina obchodních úloh, které vyžadují vyvážené výpočetní prostředky a paměť s škálovatelnou vstupně-výstupní propustností. K příkladům patří servery, které hostují webové, mobilní a další podnikové aplikace.|
| Optimalizováno pro paměť | Vysoce výkonné databázové úlohy, které vyžadují výkon v paměti pro rychlejší zpracování transakcí a vyšší souběžnost. K příkladům patří servery, které zpracovávají data v reálném čase, a transakční nebo analytické aplikace vyžadující vysoký výkon.|

Po vytvoření serveru se dá změnit nebo snížit počet virtuální jádra a cenová úroveň (s výjimkou a od úrovně Basic) během několika sekund. Velikost úložiště můžete také nezávisle upravovat a dobu uchovávání záloh nahoru nebo dolů bez výpadků aplikací. Po vytvoření serveru nejde typ úložiště zálohy změnit. Další informace najdete v části [Scale Resources](#scale-resources) .

## <a name="compute-generations-and-vcores"></a>Generace a virtuální jádray výpočtů

Výpočetní prostředky se poskytují jako virtuální jádra, což představuje logický procesor základního hardwaru. Logické procesory Gen 5 jsou založené na procesorech Intel E5-2673 v4 (Broadwell) 2,3 GHz.

## <a name="storage"></a>Storage

Úložiště, které zřizujete, je množství úložné kapacity dostupné pro váš server Azure Database for MariaDB. Úložiště se používá pro soubory databáze, dočasné soubory, transakční protokoly a protokoly serveru MariaDB. Celková velikost úložiště, kterou zřizujete, také definuje kapacitu v/v k dispozici pro váš server.

| Atributy úložiště   | Basic | Pro obecné účely | Optimalizováno pro paměť |
|:---|:----------|:--------------------|:---------------------|
| Typ úložiště | Základní úložiště | Pro obecné účely úložiště | Pro obecné účely úložiště |
| Velikost úložiště | 5 GB až 1 TB | 5 GB až 4 TB | 5 GB až 4 TB |
| Velikost přírůstku úložiště | 1 GB | 1 GB | 1 GB |
| IOPS | Proměnná |3 IOPS/GB<br/>Minimální 100 IOPS<br/>Maximální 6000 IOPS | 3 IOPS/GB<br/>Minimální 100 IOPS<br/>Maximální 6000 IOPS |

Můžete přidat další úložnou kapacitu během a po vytvoření serveru a nechat systém, aby automaticky rozšiřoval úložiště na základě spotřeby úložiště vašich úloh.

>[!NOTE]
> Úložiště se dá škálovat jenom nahoru, ne dolů.

Úroveň Basic neposkytuje záruku IOPS. V Pro obecné účely a paměťově optimalizované cenové úrovně se za vstupně-výstupní operace v poměru 3:1 omezuje velikost IOPS s zřízenou velikostí úložiště.

Spotřebu vstupu a výstupu můžete monitorovat v Azure Portal nebo pomocí příkazů rozhraní příkazového řádku Azure CLI. Příslušné metriky, které je potřeba monitorovat [, jsou omezení úložiště, procento úložiště, využité úložiště a procento vstupně-výstupních operací](concepts-monitoring.md).

### <a name="large-storage-preview"></a>Velké úložiště (Preview)

Zvýšili jsme limity úložiště v našich Pro obecné účely a paměťově optimalizovaných úrovních. Nově vytvořené servery, které se přihlašují k verzi Preview, můžou zřídit až 16 TB úložiště. Škálování IOPS v 3:1 poměr až 20 000 IOPS. Stejně jako u aktuálního všeobecně dostupného úložiště můžete po vytvoření serveru přidat další úložnou kapacitu a nechat systém automaticky rozšiřovat úložiště na základě využití úložiště vašich úloh.

| Atributy úložiště | Pro obecné účely | Optimalizováno pro paměť |
|:-------------|:--------------------|:---------------------|
| Typ úložiště | Premium Storage Azure | Premium Storage Azure |
| Velikost úložiště | 32 GB až 16 TB| 32 až 16 TB |
| Velikost přírůstku úložiště | 1 GB | 1 GB |
| IOPS | 3 IOPS/GB<br/>Minimální 100 IOPS<br/>Maximální 20 000 IOPS| 3 IOPS/GB<br/>Minimální 100 IOPS<br/>Maximální 20 000 IOPS |

> [!IMPORTANT]
> Služba large Storage je v současnosti ve verzi Public Preview v těchto oblastech: Východní USA, Východní USA 2, Brazílie – jih, Střed USA, Západní USA, Střed USA – sever, Střed USA – jih, Severní Evropa, Západní Evropa, Velká Británie – jih, Velká Británie – západ, jihovýchodní Asie, Východní Asie, Japonsko – východ, Japonsko – západ, Korea – jih, Austrálie – východ, Austrálie – jih, Západní USA 2, Středozápadní USA, Kanada – východ. a Kanada – střed.
>
> Všechny ostatní oblasti podporují až 4 TB úložiště a až 6000 vstupně-výstupních operací.
>

### <a name="reaching-the-storage-limit"></a>Dosažení limitu úložiště

Servery se zřízeným úložištěm o velikosti 100 GB nebo méně se označí jako jen pro čtení v případě, že velikost volného úložiště klesne pod 5 % velikosti zřízeného úložiště. Servery s více než 100 GB zřízeného úložiště se označí jako jen pro čtení v případě, že velikost volného úložiště klesne pod 5 GB.

Pokud jste například zřídili 110 GB úložiště a skutečné využití dosáhne více než 105 GB, server je označen jen pro čtení. Případně, pokud jste zřídili 5 GB úložiště, server je označen jen pro čtení, pokud volné úložiště dosáhne méně než 256 MB.

Zatímco se služba pokouší nastavit server jen pro čtení, všechny požadavky transakcí zápisu se zablokují a stávající aktivní transakce se budou provádět dál. Když je server nastavený jen pro čtení, všechny další operace zápisu a potvrzení transakcí selžou. Dotazy na čtení budou fungovat dál bez přerušení. Jakmile navýšíte velikost zřízeného úložiště, bude server připravený znovu přijímat transakce zápisu.

Doporučujeme zapnout automatické zvětšování úložiště nebo nastavit výstrahu, která vás upozorní, když se serverové úložiště blíží prahové hodnotě, takže se můžete vyhnout přístupu do stavu jen pro čtení. Další informace najdete v dokumentaci k [Nastavení výstrahy](howto-alert-metric.md).

### <a name="storage-auto-grow"></a>Automatické zvětšování úložiště

Automatické zvětšování úložiště zabrání vašemu serveru v provozu z úložiště a nejenom pro čtení. Pokud je povolené automatické zvětšování úložiště, úložiště se automaticky zvětšuje, aniž by to ovlivnilo zatížení. U serverů s méně než 100 GB zřízené úložiště se velikost zřízeného úložiště zvýší o 5 GB, pokud je volné úložiště menší než 10% zřízeného úložiště. U serverů s více než 100 GB zřízeného úložiště se velikost zřízeného úložiště zvyšuje o 5%, pokud je volný prostor úložiště nižší než 10 GB zřízené velikosti úložiště. Platí omezení maximální velikosti úložiště, jak je uvedeno výše.

Pokud jste například zřídili 1000 GB úložiště a skutečné využití dosáhne více než 990 GB, zvýší se velikost úložiště serveru na 1050 GB. Případně, pokud jste zřídili 10 GB úložiště, velikost úložiště se zvýší na 15 GB, pokud je úložiště menší než 1 GB volného místa.

Mějte na paměti, že úložiště je možné škálovat pouze nahoru, ne dolů.

## <a name="backup"></a>Backup

Azure Database for MariaDB poskytuje úložiště zřízeného serveru jako úložiště pro zálohování až 100%, a to bez dalších nákladů. Jakékoli úložiště záloh, které využijete, se nad rámec této částky účtuje za GB za měsíc. Pokud například zřídíte Server s 250 GB úložiště, budete mít k dispozici až 250 GB dalších úložiště pro zálohy serveru zdarma. Úložiště pro zálohy převyšující 250 GB se účtuje podle [cenového modelu](https://azure.microsoft.com/pricing/details/mariadb/). Pokud chcete pochopit faktory ovlivňující využití úložiště záloh, monitorování a řízení nákladů na úložiště zálohování, můžete se podívat na [dokumentaci k zálohování](concepts-backup.md).

## <a name="scale-resources"></a>Škálování prostředků

Po vytvoření serveru můžete nezávisle změnit virtuální jádra, cenovou úroveň (s výjimkou a od úrovně Basic), velikost úložiště a dobu uchování zálohy. Po vytvoření serveru nejde typ úložiště zálohy změnit. Počet virtuální jádra se dá škálovat nahoru nebo dolů. Doba uchovávání záloh se dá škálovat nahoru nebo dolů od 7 do 35 dnů. Velikost úložiště se dá zvýšit jenom. Škálování prostředků se dá provést buď prostřednictvím portálu, nebo pomocí Azure CLI. 

Když změníte počet virtuální jádra nebo cenovou úroveň, vytvoří se kopie původního serveru s novým přidělením výpočtů. Po zprovoznění nového serveru se připojení přepnou na nový server. Během přepínání systému na nový server není možné navazovat nová připojení a všechny nepotvrzené transakce se vrátí zpět. Tento časový interval je různý, ale ve většině případů je kratší než minuta.

Škálování úložiště a Změna doby uchovávání záloh jsou pravdivé online operace. Nedochází k výpadkům a vaše aplikace to nijak neovlivní. Když se v případě IOPS škáluje velikost zřízeného úložiště, můžete prodloužit kapacitu úložiště, která je pro váš server dostupná.

## <a name="pricing"></a>Ceny

Nejaktuálnější informace o cenách najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/mariadb/)služeb. Chcete-li zobrazit náklady na konfiguraci, kterou požadujete, [Azure Portal](https://portal.azure.com/#create/Microsoft.MariaDBServer) na kartě **cenová úroveň** na základě možností, které jste vybrali, zobrazí měsíční náklady. Pokud nemáte předplatné Azure, můžete získat odhadovanou cenu pomocí cenové kalkulačky Azure. Na webu [cenové kalkulačky Azure](https://azure.microsoft.com/pricing/calculator/) vyberte **Přidat položky**, rozbalte kategorii **databáze** a zvolte **Azure Database for MariaDB** pro přizpůsobení možností.

## <a name="next-steps"></a>Další kroky
- Přečtěte si o [omezeních služeb](concepts-limits.md).
- Naučte se [vytvářet MariaDB Server v Azure Portal](quickstart-create-mariadb-server-database-using-azure-portal.md).