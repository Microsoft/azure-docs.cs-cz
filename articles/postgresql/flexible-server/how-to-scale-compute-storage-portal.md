---
title: Škálování operací – Azure Portal-Azure Database for PostgreSQL-flexibilní Server
description: Tento článek popisuje, jak provádět operace škálování v Azure Database for PostgreSQL prostřednictvím Azure Portal.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 1542bba53b51ffdf2129953a81e5d13975ade434
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90936123"
---
# <a name="scale-operations-in-flexible-server"></a>Škálování operací na flexibilním serveru

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.

Tento článek popisuje kroky pro provádění operací škálování pro výpočetní výkon a úložiště. Můžete změnit výpočetní úrovně mezi možnostmi prostupné, pro obecné účely a paměťově optimalizované SKU, včetně výběru počtu virtuální jádra, který je vhodný ke spuštění vaší aplikace. Můžete také škálovat úložiště. Očekávaný počet IOPS se zobrazuje na základě výpočetní úrovně, virtuální jádra a kapacity úložiště. Odhad nákladů je zobrazen také na základě vašeho výběru.

> [!IMPORTANT]
> Nemůžete škálovat úložiště.

## <a name="pre-requisites"></a>Požadavky

K dokončení tohoto průvodce budete potřebovat:

-   Musíte mít Azure Database for PostgreSQL Server, který je flexibilní. Stejný postup platí i pro flexibilní server nakonfigurovaný s redundancí zóny.
> [!IMPORTANT]
> Pokud je nakonfigurovaná s vysokou dostupností, nemůžete vybrat SKU. Během operace škálování se v pohotovostním stavu napřed změní na požadovanou velikost, primární server se převezme při selhání a primární se škáluje. 

## <a name="scaling-the-compute-tier-and-size"></a>Škálování výpočetní úrovně a velikosti

Pomocí těchto kroků vyberte výpočetní vrstvu.
 
1.  V [Azure Portal](https://portal.azure.com/)vyberte flexibilní Server, ze kterého chcete zálohu obnovit.

2.  Klikněte na **výpočty + úložiště**.

3.  Zobrazí se stránka s aktuálním nastavením.
 :::image type="content" source="./media/how-to-scale-compute-storage-portal/click-compute-storage.png" alt-text="zobrazení COMPUTE + úložiště":::

4.  Můžete zvolit výpočetní třídu mezi úrovněmi, které jsou k disstupnému přenosu, pro obecné účely a paměťově optimalizované.
   :::image type="content" source="./media/how-to-scale-compute-storage-portal/list-compute-tiers.png" alt-text="výpis výpočetních úrovní":::


5.  Pokud máte dobrý výchozí virtuální jádra a velikost paměti, můžete další krok přeskočit.

6.  Pokud chcete změnit počet virtuální jádra, můžete kliknout na rozevírací seznam **Velikost výpočtu** a kliknout na požadovaný počet virtuální jádra/paměti ze seznamu.
    
    - Zátěžová výpočetní vrstva: :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-burstable-dropdown.png" alt-text="COMPUTE s shlukovým"::: přenosem

    - Výpočetní vrstva pro obecné účely: :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-general-purpose-dropdown.png" alt-text="COMPUTE pro obecné účely":::

    - Paměťově optimalizovaná výpočetní vrstva: :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-memory-optimized-dropdown.png" alt-text="paměťově optimalizovaná COMPUTE":::

7.  Klikněte na **Uložit**. 
8.  Zobrazí se potvrzovací zpráva. Pokud chcete pokračovat, klikněte na **OK** . 
9.  Oznámení o probíhající operaci škálování.


## <a name="scaling-storage-size"></a>Škálování velikosti úložiště

Chcete-li zvětšit velikost úložiště, postupujte podle těchto kroků.

1.  V [Azure Portal](https://portal.azure.com/)vyberte flexibilní Server, pro který chcete zvětšit velikost úložiště.
2.  Klikněte na **výpočty + úložiště**.

3.  Zobrazí se stránka s aktuálním nastavením.
   
:::image type="content" source="./media/how-to-scale-compute-storage-portal/click-compute-storage.png" alt-text="klikněte na výpočty + úložiště.":::
4.  **Velikost úložiště polí v GIB** se zobrazením panelu se zobrazí v aktuální velikosti.

5.  Vysuňte pruh na požadovanou velikost. Zobrazí se odpovídající číslo IOPS. IOPS závisí na výpočetní úrovni a velikosti. Zobrazí se také informace o nákladech. 

 :::image type="content" source="./media/how-to-scale-compute-storage-portal/storage-scaleup.png" alt-text="horizontální navýšení kapacity úložiště":::

6.  Pokud je velikost úložiště dobrá, klikněte na **Uložit**. 
7.  Zobrazí se potvrzovací zpráva. Pokud chcete pokračovat, klikněte na **OK** . 
8.  Oznámení o probíhající operaci škálování.

## <a name="next-steps"></a>Další kroky

-   Informace o [kontinuitě podnikových aplikací](./concepts-business-continuity.md)
-   Další informace o [vysoké dostupnosti](./concepts-high-availability.md)
-   Další informace o [zálohování a obnovení](./concepts-backup-restore.md)
