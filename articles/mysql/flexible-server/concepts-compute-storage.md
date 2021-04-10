---
title: Možnosti COMPUTE a Storage – Azure Database for MySQL – flexibilní Server
description: Tento článek popisuje možnosti výpočtů a úložiště v Azure Database for MySQL-flexibilním serveru.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: conceptual
ms.date: 1/28/2021
ms.openlocfilehash: 7165cdc072ffaa5b0d862e1fe17f94e35c35aeec
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105034533"
---
# <a name="compute-and-storage-options-in-azure-database-for-mysql---flexible-server-preview"></a>Možnosti výpočtů a úložiště v Azure Database for MySQL – flexibilní Server (Preview)

> [!IMPORTANT] 
> Azure Database for MySQL – flexibilní Server je momentálně ve verzi Public Preview.

Azure Database for MySQL flexibilní Server můžete vytvořit v jedné ze tří různých výpočetních vrstev: shluky, Pro obecné účely a paměť optimalizované. Výpočetní vrstvy jsou rozlišené základními SKU virtuálních počítačů, které používaly B-Series, řady D-Series a elektronické řady. Volba výpočetní úrovně a velikosti určuje paměť a virtuální jádra k dispozici na serveru. V rámci všech výpočetních vrstev se používá stejná technologie úložiště. Všechny prostředky jsou zřízené na úrovni serveru MySQL. Server může mít jednu nebo více databází.

| Prostředek/vrstva | **S shluky** | **Obecné použití** | **Paměťově optimalizovaná** |
|:---|:----------|:--------------------|:---------------------|
| Řada virtuálních počítačů| Řady B-Series | Ddsv4-Series | Edsv4-Series|
| Virtuální jádra | 1, 2 | 2, 4, 8, 16, 32, 48, 64 | 2, 4, 8, 16, 32, 48, 64 |
| Paměť na vCore | Proměnná | 4 GiB | 8 GiB * |
| Velikost úložiště | 5 GiB až 16 TiB | 5 GiB až 16 TiB | 5 GiB až 16 TiB |
| Doba uchování zálohy databáze | 1 až 35 dní | 1 až 35 dní | 1 až 35 dní |

\* S výjimkou položky E64ds_v4 (paměťově optimalizovaná) SKU, která má 504 GB paměti

Pokud chcete zvolit výpočetní vrstvu, použijte jako výchozí bod následující tabulku.

| Úroveň výpočetních prostředků | Cílová zátěž |
|:-------------|:-----------------|
| S shluky | Nejvhodnější pro úlohy, které nepotřebují nepřetržitě plný procesor. |
| Pro obecné účely | Většina obchodních úloh, které vyžadují vyvážené výpočetní prostředky a paměť s škálovatelnou vstupně-výstupní propustností. K příkladům patří servery, které hostují webové, mobilní a další podnikové aplikace.|
| Optimalizováno pro paměť | Vysoce výkonné databázové úlohy, které vyžadují výkon v paměti pro rychlejší zpracování transakcí a vyšší souběžnost. K příkladům patří servery, které zpracovávají data v reálném čase, a transakční nebo analytické aplikace vyžadující vysoký výkon.|

Po vytvoření serveru se změní výpočetní vrstva, velikost výpočetní služby a velikost úložiště. Výpočetní škálování vyžaduje restart a trvá 60-120 sekund, zatímco škálování úložiště nevyžaduje restart. Dobu uchovávání záloh můžete také nezávisle upravit směrem nahoru nebo dolů. Další informace najdete v části [Scale Resources](#scale-resources) .

## <a name="compute-tiers-size-and-server-types"></a>Výpočetní úrovně, velikost a typy serverů

Výpočetní prostředky se dají vybrat na základě úrovně a velikosti. Tím se určuje velikost virtuální jádra a paměti. Virtuální jádra představuje logický procesor základního hardwaru.

Podrobné specifikace dostupných typů serverů jsou následující:

| Velikost výpočetního prostředí         | Virtuální jádra | Velikost paměti (GiB) | Maximální podporovaný počet IOPS | Maximální podporovaná I/O šířka pásma (MB/s)|
|----------------------|--------|-------------------| ------------------ |-----------------------------------|
| **S shluky**        |        |                   | 
| Standard_B1s         | 1      | 1                 | 320                | 10                                | 
| Standard_B1ms        | 1      | 2                 | 640                | 10                                |
| Standard_B2s         | 2      | 4                 | 1280               | 15                                |
| **Obecné použití**  |        |                   |                    |                                   |
| Standard_D2ds_v4     | 2      | 8                 | 3200               | 48                                |
| Standard_D4ds_v4     | 4      | 16                | 6400               | 96                                |
| Standard_D8ds_v4     | 8      | 32                | 12800              | 192                               |
| Standard_D16ds_v4    | 16     | 64                | 20000              | 384                               |
| Standard_D32ds_v4    | 32     | 128               | 20000              | 768                               |
| Standard_D48ds_v4    | 48     | 192               | 20000              | 1152                              |
| Standard_D64ds_v4    | 64     | 256               | 20000              | 1200                              |
| **Paměťově optimalizovaná** |        |                   |                    |                                   |
| Standard_E2ds_v4     | 2      | 16                | 3200               | 48                                |
| Standard_E4ds_v4     | 4      | 32                | 6400               | 96                                |
| Standard_E8ds_v4     | 8      | 64                | 12800              | 192                               |
| Standard_E16ds_v4    | 16     | 128               | 20000              | 384                               |
| Standard_E32ds_v4    | 32     | 256               | 20000              | 768                               |
| Standard_E48ds_v4    | 48     | 384               | 20000              | 1152                              |
| Standard_E64ds_v4    | 64     | 504               | 20000              | 1200                              |

Další podrobnosti o dostupných výpočetních řadách najdete v dokumentaci k VIRTUÁLNÍm počítačům Azure pro [shluky (B-Series)](../../virtual-machines/sizes-b-series-burstable.md), [pro obecné účely (Ddsv4-Series)](../../virtual-machines/ddv4-ddsv4-series.md)a [paměťově optimalizovaná (Edsv4-Series)](../../virtual-machines/edv4-edsv4-series.md).

>[!NOTE]
>Pokud se virtuální počítač spustí, zastaví nebo restartuje, může dojít ke ztrátě kreditů pro [výpočetní vrstvu (B-Series)](../../virtual-machines/sizes-b-series-burstable.md) . Další informace najdete v tématu [Nejčastější dotazy k roztržení (B-Series)](../../virtual-machines/sizes-b-series-burstable.md#q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart).

## <a name="storage"></a>Storage

Úložiště, které zřizujete, je množství úložné kapacity dostupné pro váš flexibilní Server. Úložiště se používá pro soubory databáze, dočasné soubory, transakční protokoly a protokoly serveru MySQL. Ve všech výpočetních úrovních je minimální podporovaná velikost úložiště 5 GiB a maximum je 16 TiB. Velikost úložiště se škáluje v 1 přírůstcích GiB a dá se škálovat až po vytvoření serveru.

>[!NOTE]
> Úložiště se dá škálovat jenom nahoru, ne dolů.

Spotřebu úložiště můžete monitorovat v Azure Portal (s Azure Monitor) pomocí omezení úložiště, procenta úložiště a metrik využití úložiště. Informace o metrikách najdete v [článku o monitorování](./concepts-monitoring.md) . 

### <a name="reaching-the-storage-limit"></a>Dosažení limitu úložiště

Když je úložiště spotřebované na serveru blízko, aby se dosáhlo limitu stanoveného, server je přepnut do režimu jen pro čtení, aby chránil všechny ztracené zápisy na serveru. Servery s menší než 100 GiB zřízené úložiště jsou označené jen pro čtení, pokud je volné úložiště menší než 5% velikosti zřízeného úložiště. Servery s více než 100 GiB zřízené úložiště jsou označené jen pro čtení, pokud je volné úložiště menší než 5 GiB.

Pokud jste například zřídili 110 GiB úložiště a skutečné využití překročí 105 GiB, server je označen jen pro čtení. Případně, pokud jste zřídili 5 GiB úložiště, server je označen jen pro čtení, pokud volné úložiště dosáhne méně než 256 MB.

Zatímco se služba pokouší nastavit server jen pro čtení, všechny požadavky transakcí zápisu se zablokují a stávající aktivní transakce se budou provádět dál. Když je server nastavený jen pro čtení, všechny další operace zápisu a potvrzení transakcí selžou. Dotazy na čtení budou fungovat dál bez přerušení. 

Pokud chcete server získat z režimu jen pro čtení, měli byste na serveru zvětšit zřízené úložiště. Můžete to udělat pomocí Azure Portal nebo Azure CLI. Po zvýšení bude server připraven přijímat transakce zápisu znovu.

Doporučujeme nastavit výstrahu, která vás upozorní, když se serverové úložiště blíží prahové hodnotě, takže se můžete vyhnout přístupu do stavu jen pro čtení. Informace o dostupných metrikách najdete v [článku o monitorování](./concepts-monitoring.md) . 

Doporučujeme, abyste <!--turn on storage auto-grow or to--> Nastavte výstrahu, která vás upozorní, když se serverové úložiště blíží prahové hodnotě, takže se můžete vyhnout nedostatku do stavu jen pro čtení. Další informace najdete v dokumentaci k výstraze, [jak nastavit výstrahu](how-to-alert-on-metric.md).

### <a name="storage-auto-grow"></a>Automatické zvětšování úložiště

Automatické zvětšování úložiště ještě není k dispozici pro Azure Database for MySQL flexibilní Server.

## <a name="iops"></a>IOPS

Azure Database for MySQL – flexibilní Server podporuje zřizování dalších IOPS. Tato funkce vám umožní zřídit další IOPS nad limitem pro zdvořilostní IOPS. Pomocí této funkce můžete kdykoli nebo snížit počet IOPS zřízených podle požadavků na úlohy. 

Minimální hodnota IOPS je 100 napříč všemi výpočetními velikostmi a maximální IOPS je určena zvolenou velikostí výpočtu. Ve verzi Preview je maximální podporovaná hodnota IOPS 20 000 vstupně-výstupních operací.

Další informace o maximálním počtu vstupně-výstupních operací na výpočetní velikosti najdete níže: 

| Velikost výpočetního prostředí         | Maximální IOPS        | 
|----------------------|---------------------|
| **S shluky**        |                     |
| Standard_B1s         | 320                 |
| Standard_B1ms        | 640                 |
| Standard_B2s         | 1280                | 
| **Obecné použití**  |                     |
| Standard_D2ds_v4     | 3200                |
| Standard_D4ds_v4     | 6400                |
| Standard_D8ds_v4     | 12800               |
| Standard_D16ds_v4    | 20000               |
| Standard_D32ds_v4    | 20000               |
| Standard_D48ds_v4    | 20000               | 
| Standard_D64ds_v4    | 20000               | 
| **Paměťově optimalizovaná** |                     | 
| Standard_E2ds_v4     | 3200                | 
| Standard_E4ds_v4     | 6400                | 
| Standard_ E8ds_v4    | 12800               | 
| Standard_ E16ds_v4   | 20000               | 
| Standard_E32ds_v4    | 20000               | 
| Standard_E48ds_v4    | 20000               | 
| Standard_E64ds_v4    | 20000               |  

Maximální IOPS závisí na maximálním počtu dostupných IOPS na výpočetní velikost. Přečtěte si část *maximální propustnost disku bez mezipaměti: IOPS/MB/s* v dokumentaci [B-Series](../../virtual-machines/sizes-b-series-burstable.md), [Ddsv4-Series](../../virtual-machines/ddv4-ddsv4-series.md)a [Edsv4-Series](../../virtual-machines/edv4-edsv4-series.md) .

> [!Important]
> **Zdvořilostní IOPS** se rovná minimální velikosti (maximální propustnost disku bez mezipaměti: IOPS/MB/s) výpočetní velikosti, úložiště zřízené v GIB * 3).<br>
> **Minimální IOPS** je 100 napříč všemi výpočetními velikostmi.<br>
> **Maximální IOPS** je určena zvolenou velikostí výpočtu. Ve verzi Preview je maximální podporovaná hodnota IOPS 20 000 vstupně-výstupních operací.

Spotřebu v/v můžete monitorovat v Azure Portal (s Azure Monitor) pomocí metriky v/v [%](./concepts-monitoring.md) . Pokud budete potřebovat více IOPS, pak na základě výpočetního počtu IOPS budete potřebovat škálovat výpočetní výkon serveru.

## <a name="backup"></a>Backup

Služba automaticky provede zálohování vašeho serveru. Můžete vybrat dobu uchování z rozsahu 1 až 35 dní. Další informace o zálohách [najdete v článku o zálohování a obnovení konceptů](concepts-backup-restore.md).

## <a name="scale-resources"></a>Škálování prostředků

Po vytvoření serveru můžete nezávisle změnit výpočetní vrstvu, výpočetní velikost (virtuální jádra a paměť) a velikost úložiště a dobu uchování zálohy. Velikost výpočetní kapacity se dá škálovat nahoru nebo dolů. Doba uchovávání záloh se dá škálovat nahoru nebo dolů od 1 do 35 dnů. Velikost úložiště se dá zvýšit jenom. Škálování prostředků se dá dělat přes portál nebo Azure CLI.

> [!NOTE]
> Velikost úložiště se dá zvýšit jenom. Po zvýšení se nemůžete vrátit k menší velikosti úložiště.

Když změníte výpočetní úroveň nebo výpočetní velikost, server se restartuje, aby se nový typ serveru projevil. Během přepínání systému na nový server není možné navazovat nová připojení a všechny nepotvrzené transakce se vrátí zpět. Toto okno se liší, ale ve většině případů je mezi 60-120 sekundami. 

Škálování úložiště a Změna doby uchovávání záloh jsou online operace a nevyžadují restart serveru.

## <a name="pricing"></a>Ceny

Nejaktuálnější informace o cenách najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/MySQL/)služeb. Chcete-li zobrazit náklady na konfiguraci, kterou požadujete, [Azure Portal](https://portal.azure.com/#create/Microsoft.MySQLServer/flexibleServers) na kartě **COMPUTE + úložiště** na základě možností, které jste vybrali, zobrazí měsíční náklady. Pokud nemáte předplatné Azure, můžete získat odhadovanou cenu pomocí cenové kalkulačky Azure. Na webu [cenové kalkulačky Azure](https://azure.microsoft.com/pricing/calculator/) vyberte **Přidat položky**, rozbalte kategorii **databáze** , zvolte **Azure Database for MySQL** a **flexibilní Server** jako typ nasazení pro přizpůsobení možností.

Pokud chcete optimalizovat náklady na server, můžete zvážit následující tipy:

- Pokud je výpočetní prostředí nevyužité, je potřeba snížit kapacitu výpočetní úrovně nebo výpočetní velikosti (virtuální jádra).
- Pokud vaše úloha nepotřebuje nepřetržitě kompletní výpočetní kapacitu z Pro obecné účely a paměťově optimalizovaných vrstev, zvažte přechod na výpočetní vrstvu s možností rozšíření.
- Zastavte Server, pokud se nepoužívá.
- Zkraťte dobu uchovávání záloh, pokud není nutné delší dobu uchování zálohy.

## <a name="next-steps"></a>Další kroky

- Naučte se [vytvořit server MySQL na portálu](quickstart-create-server-portal.md).
- Přečtěte si o [omezeních služeb](concepts-limitations.md).
