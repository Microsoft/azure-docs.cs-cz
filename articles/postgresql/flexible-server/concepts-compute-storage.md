---
title: Možnosti COMPUTE a Storage – Azure Database for PostgreSQL – flexibilní Server
description: Tento článek popisuje možnosti výpočtů a úložiště v Azure Database for PostgreSQL-flexibilním serveru.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: 6bcfbc19cbaa078373d6857bdac74eaf86eca104
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048047"
---
# <a name="compute-and-storage-options-in-azure-database-for-postgresql---flexible-server"></a>Možnosti výpočtů a úložiště v Azure Database for PostgreSQL – flexibilní Server

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.

Server Azure Database for PostgreSQL můžete vytvořit v jedné ze tří různých cenových úrovní: shluky, Pro obecné účely a paměť optimalizované. Cenové úrovně jsou rozlišené o množství výpočtů v virtuální jádra, které se dá zřídit, paměť na vCore a technologie úložiště, která se používá k ukládání dat. Všechny prostředky jsou zřízené na úrovni serveru PostgreSQL. Server může mít jednu nebo více databází.

| Prostředek/vrstva | **S shluky** | **Obecné použití** | **Paměťově optimalizovaná** |
|:---|:----------|:--------------------|:---------------------|
| Virtuální jádra | 1, 2 | 2, 4, 8, 16, 32, 48, 64 | 2, 4, 8, 16, 32, 48, 64 |
| Paměť na vCore | Proměnná | 4 GB | 6,75 až 8 GB |
| Velikost úložiště | 32 GB až 16 TB | 32 GB až 16 TB | 32 GB až 16 TB |
| Doba uchování zálohy databáze | 7 až 35 dní | 7 až 35 dní | 7 až 35 dní |

Pokud chcete zvolit cenovou úroveň, použijte jako výchozí bod následující tabulku.

| Cenová úroveň | Cílová zátěž |
|:-------------|:-----------------|
| S shluky | Nejvhodnější pro úlohy, které nepotřebují nepřetržitě plný procesor. |
| Pro obecné účely | Většina obchodních úloh, které vyžadují vyvážené výpočetní prostředky a paměť s škálovatelnou vstupně-výstupní propustností. K příkladům patří servery, které hostují webové, mobilní a další podnikové aplikace.|
| Optimalizováno pro paměť | Vysoce výkonné databázové úlohy, které vyžadují výkon v paměti pro rychlejší zpracování transakcí a vyšší souběžnost. K příkladům patří servery, které zpracovávají data v reálném čase, a transakční nebo analytické aplikace vyžadující vysoký výkon.|

Po vytvoření serveru se výpočetní vrstva, počet virtuální jádra a velikost úložiště dá během několika sekund změnit nahoru nebo dolů. Dobu uchovávání záloh můžete také nezávisle upravit směrem nahoru nebo dolů. Další informace najdete v části [Scale Resources](#scale-resources) .

## <a name="compute-tiers-vcores-and-server-types"></a>Výpočetní úrovně, virtuální jádra a typy serverů

Výpočetní prostředky se dají vybrat na základě vrstvy, virtuální jádra a velikosti paměti. Virtuální jádra představuje logický procesor základního hardwaru.

Podrobné specifikace dostupných typů serverů jsou následující:

| Název SKU             | Virtuální jádra | Velikost paměti | Maximální podporovaný počet IOPS | Maximální podporovaná vstupně-výstupní šířka pásma |
|----------------------|--------|-------------|--------------------|-----------------------------|
| **S shluky**        |        |             |                    |                             |
| B1ms                 | 1      | 2 GiB       | 640                | 15 MiB/s                  |
| B2s                  | 2      | 4 GiB       | 1280               | 15 MiB/s                  |
| **Obecné použití**  |        |             |                    |                             |
| D2s_v3               | 2      | 8 GiB       | 3200               | 48 MiB/s                  |
| D4s_v3               | 4      | 16 GiB      | 6400               | 96 MiB/s                  |
| D8s_v3               | 8      | 32 GiB      | 12800              | 192 MiB/s                 |
| D16s_v3              | 16     | 64 GiB      | 18000              | 384 MiB/s                 |
| D32s_v3              | 32     | 128 GiB     | 18000              | 750 MiB/s                 |
| D48s_v3              | 48     | 192 GiB     | 18000              | 750 MiB/s                 |
| D64s_v3              | 64     | 256 GB     | 18000              | 750 MiB/s                 |
| **Paměťově optimalizovaná** |        |             |                    |                             |
| E2s_v3               | 2      | 16 GiB      | 3200               | 48 MiB/s                  |
| E4s_v3               | 4      | 32 GiB      | 6400               | 96 MiB/s                  |
| E8s_v3               | 8      | 64 GiB      | 12800              | 192 MiB/s                 |
| E16s_v3              | 16     | 128 GiB     | 18000              | 384 MiB/s                 |
| E32s_v3              | 32     | 256 GB     | 18000              | 750 MiB/s                 |
| E48s_v3              | 48     | 384 GiB     | 18000              | 750 MiB/s                 |
| E64s_v3              | 64     | 432 GiB     | 18000              | 750 MiB/s                 |

## <a name="storage"></a>Storage

Úložiště, které zřizujete, je množství úložné kapacity dostupné pro váš server Azure Database for PostgreSQL. Úložiště se používá pro soubory databáze, dočasné soubory, transakční protokoly a protokoly serveru PostgreSQL. Celková velikost úložiště, kterou zřizujete, také definuje kapacitu v/v k dispozici pro váš server.

Úložiště je k dispozici v následujících pevných velikostech:

| Velikost disku | IOPS |
|:---|:---|
| 32 GiB | Zřízené 120, až 3 500 |
| 64 GiB | Zřízené 240, až 3 500 |
| 128 GiB | Zřízené 500, až 3 500 |
| 256 GB | Zřízené 1100, až 3 500 |
| 512 GiB | Zřízené 2300, až 3 500 |
| 1 TiB | 5 000 |
| 2 TB | 7 500 |
| 4 TiB | 7 500 |
| 8 TiB | 16 000 |
| 16 TiB | 18 000 |

Všimněte si, že IOPS jsou taky omezené vaším typem virtuálního počítače. I když můžete vybrat libovolnou velikost úložiště nezávisle na typu serveru, možná nebudete moct použít všechny vstupně-výstupní operace, které úložiště poskytuje, zejména když zvolíte Server s malým počtem virtuální jádra.

Můžete přidat další kapacitu úložiště během a po vytvoření serveru.

>[!NOTE]
> Úložiště se dá škálovat jenom nahoru, ne dolů.

Spotřebu vstupu a výstupu můžete monitorovat v Azure Portal nebo pomocí příkazů rozhraní příkazového řádku Azure CLI. Příslušné metriky, které je potřeba monitorovat [, jsou omezení úložiště, procento úložiště, využité úložiště a procento vstupně-výstupních operací](concepts-monitoring.md).

### <a name="maximum-iops-for-your-configuration"></a>Maximální počet IOPS pro vaši konfiguraci

|Název SKU            |Velikost úložiště v GiB                       |32 |64 |128 |256 |512  |1 024|2 048|4 096|8 192 |16 384|
|--------------------|------------------------------------------|---|---|----|----|-----|-----|-----|-----|------|------|
|                    |Maximální IOPS                              |120|240|500 |1100|2300 |5000 |7 500 |7 500 |16000 |18000 |
|**S shluky**       |                                          |   |   |    |    |     |     |     |     |      |      |
|B1ms                |640 IOPS                                  |120|240|500 |640 *|640 * |640 * |640 * |640 * |640 *  |640 *  |
|B2s                 |1280 IOPS                                 |120|240|500 |1100|1280 *|1280 *|1280 *|1280 *|1280 * |1280 * |
|**Obecné použití** |                                          |   |   |    |    |     |     |     |     |      |      |
|D2s_v3              |3200 IOPS                                 |120|240|500 |1100|2300 |3200 *|3200 *|3200 *|3200 * |3200 * |
|D4s_v3              |6 400 IOPS                                |120|240|500 |1100|2300 |5000 |6400 *|6400 *|6400 * |6400 * |
|D8s_v3              |12 800 IOPS                               |120|240|500 |1100|2300 |5000 |7 500 |7 500 |12800 *|12800 *|
|D16s_v3             |18 000 IOPS                               |120|240|500 |1100|2300 |5000 |7 500 |7 500 |16000 |18000 |
|D32s_v3             |18 000 IOPS                               |120|240|500 |1100|2300 |5000 |7 500 |7 500 |16000 |18000 |
|D48s_v3             |18 000 IOPS                               |120|240|500 |1100|2300 |5000 |7 500 |7 500 |16000 |18000 |
|D64s_v3             |18 000 IOPS                               |120|240|500 |1100|2300 |5000 |7 500 |7 500 |16000 |18000 |
|**Paměťově optimalizovaná**|                                          |   |   |    |    |     |     |     |     |      |      |
|E2s_v3              |3200 IOPS                                 |120|240|500 |1100|2300 |3200 *|3200 *|3200 *|3200 * |3200 * |
|E4s_v3              |6 400 IOPS                                |120|240|500 |1100|2300 |5000 |6400 *|6400 *|6400 * |6400 * |
|E8s_v3              |12 800 IOPS                               |120|240|500 |1100|2300 |5000 |7 500 |7 500 |12800 *|12800 *|
|E16s_v3             |18 000 IOPS                               |120|240|500 |1100|2300 |5000 |7 500 |7 500 |16000 |18000 |
|E32s_v3             |18 000 IOPS                               |120|240|500 |1100|2300 |5000 |7 500 |7 500 |16000 |18000 |
|E48s_v3             |18 000 IOPS                               |120|240|500 |1100|2300 |5000 |7 500 |7 500 |16000 |18000 |
|E64s_v3             |18 000 IOPS                               |120|240|500 |1100|2300 |5000 |7 500 |7 500 |16000 |18000 |

V případě, že je označena jako \* , má IOPS omezený typ virtuálního počítače, který jste vybrali. V opačném případě se IOPS omezí podle zvolené velikosti úložiště.

>[!NOTE]
> V metrikách se může zobrazit větší IOPS v důsledku nárůstu úrovně disku. Další podrobnosti najdete v [dokumentaci](../../virtual-machines/disk-bursting.md#disk-level-bursting) . 

### <a name="maximum-io-bandwidth-mibsec-for-your-configuration"></a>Maximální I/O šířka pásma (MiB/s) pro vaši konfiguraci

|Název SKU            |Velikost úložiště, GiB                             |32 |64 |128 |256 |512  |1 024|2 048|4 096|8 192 |16 384|
|--------------------|----------------------------------------------|---|---|----|----|-----|-----|-----|-----|------|------|
|                    |**Šířka pásma úložiště, MiB/s**                |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|**S shluky**       |                                              |   |   |    |    |     |     |     |     |      |      |
|B1ms                |10 MiB/s                                    |10pruhový|10pruhový|10pruhový |10pruhový |10pruhový  |10pruhový  |10pruhový  |10pruhový  |10pruhový   |10pruhový   |
|B2s                 |15 MiB/s                                    |15|15|15 |15 |15  |15  |15  |15  |15   |15   |
|**Obecné použití** |                                              |   |   |    |    |     |     |     |     |      |      |
|D2s_v3              |48 MiB/s                                    |25 |48 *|48 * |48 * |48 *  |48 *  |48 *  |48 *  |48 *   |48 *   |
|D4s_v3              |96 MiB/s                                    |25 |50 |96 * |96 * |96 *  |96 *  |96 *  |96 *  |96 *   |96 *   |
|D8s_v3              |192 MiB/s                                   |25 |50 |100 |125 |150  |192 * |192 * |192 * |192 *  |192 *  |
|D16s_v3             |384 MiB/s                                   |25 |50 |100 |125 |150  |200  |250  |250  |384 *  |384 *  |
|D32s_v3             |750 MiB/s                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|D48s_v3             |750 MiB/s                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|D64s_v3             |750 MiB/s                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|**Paměťově optimalizovaná**|                                              |   |   |    |    |     |     |     |     |      |      |
|E2s_v3              |48 MiB/s                                    |25 |48 *|48 * |48 * |48 *  |48 *  |48 *  |48 *  |48 *   |48 *   |
|E4s_v3              |96 MiB/s                                    |25 |50 |96 * |96 * |96 *  |96 *  |96 *  |96 *  |96 *   |96 *   |
|E8s_v3              |192 MiB/s                                   |25 |50 |100 |125 |150  |192 * |192 * |192 * |192 *  |192 *  |
|E16s_v3             |384 MiB/s                                   |25 |50 |100 |125 |150  |200  |250  |250  |384 *  |384 *  |
|E32s_v3             |750 MiB/s                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|E48s_v3             |750 MiB/s                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|E64s_v3             |750 MiB/s                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |

Pokud \* je tato možnost označená jako, je vstupně-výstupní šířka pásma omezená typem virtuálního počítače, který jste vybrali. V opačném případě se I/O šířka pásma omezí zvolenou velikostí úložiště.

### <a name="reaching-the-storage-limit"></a>Dosažení limitu úložiště

Při dosažení limitu úložiště začne server vracet chyby a zabrání v dalších úpravách. To může také způsobit problémy s jinými provozními aktivitami, jako jsou zálohování a archivace WAL.

Aby k této situaci nedocházelo, když využití úložiště dosáhne 95% nebo pokud je dostupná kapacita menší než 5 GiB, server se automaticky přepne do **režimu jen pro čtení**.

Doporučujeme aktivně monitorovat místo na disku, které se používá, a zvětšit velikost disku před jakoukoli situací, kdy se úložiště nachází. Můžete nastavit výstrahu, která vás upozorní, když se úložiště serveru přiblíží k vycházejícímu z disku, abyste se vyhnuli jakýmkoli problémům s vychodem z provozu. Další informace najdete v dokumentaci k [Nastavení výstrahy](howto-alert-on-metrics.md).


### <a name="storage-auto-grow"></a>Automatické zvětšování úložiště

Automatické zvětšování úložiště ještě není k dispozici pro flexibilní Server.

## <a name="backup"></a>Backup

Služba automaticky provede zálohování vašeho serveru. Můžete vybrat dobu uchování z rozsahu 7 až 35 dní. Další informace o zálohách [najdete v článku koncepty](concepts-backup-restore.md).

## <a name="scale-resources"></a>Škálování prostředků

Po vytvoření serveru můžete nezávisle změnit virtuální jádra, výpočetní úroveň, velikost úložiště a dobu uchování zálohy. Počet virtuální jádra se dá škálovat nahoru nebo dolů. Doba uchovávání záloh se dá škálovat nahoru nebo dolů od 7 do 35 dnů. Velikost úložiště se dá zvýšit jenom. Škálování prostředků se dá provést buď prostřednictvím portálu, nebo pomocí Azure CLI.

> [!NOTE]
> Velikost úložiště se dá zvýšit jenom. Po zvýšení se nemůžete vrátit k menší velikosti úložiště.

Když změníte počet virtuální jádra nebo výpočetní úrovně, server se restartuje, aby se nový typ serveru projevil. Během přepínání systému na nový server není možné navazovat nová připojení a všechny nepotvrzené transakce se vrátí zpět. Tento časový interval je různý, ale ve většině případů je kratší než minuta. Škálování úložiště funguje stejným způsobem a také vyžaduje krátké restartování.

Změna období uchovávání záloh je online operace.

## <a name="pricing"></a>Ceny

Nejaktuálnější informace o cenách najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/PostgreSQL/)služeb. Chcete-li zobrazit náklady na konfiguraci, kterou požadujete, [Azure Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) na kartě **cenová úroveň** na základě možností, které jste vybrali, zobrazí měsíční náklady. Pokud nemáte předplatné Azure, můžete získat odhadovanou cenu pomocí cenové kalkulačky Azure. Na webu [cenové kalkulačky Azure](https://azure.microsoft.com/pricing/calculator/) vyberte **Přidat položky**, rozbalte kategorii **databáze** a zvolte **Azure Database for PostgreSQL** pro přizpůsobení možností.

## <a name="next-steps"></a>Další kroky

- Naučte se [vytvořit server PostgreSQL na portálu](how-to-manage-server-portal.md).
- Přečtěte si o [omezeních služeb](concepts-limits.md).