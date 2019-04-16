---
title: Azure Data Box hraniční zařízení naprogramujete sledování | Dokumentace Microsoftu
description: Popisuje, jak pomocí webu Azure portal a místního webového uživatelského rozhraní pro monitorování Azure Data Box hranici.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 4d4cc8a3eaf2bf806d5b265be226482b23804f82
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2019
ms.locfileid: "59580332"
---
# <a name="monitor-your-azure-data-box-edge"></a>Monitorování Azure Data Box hranici

Tento článek popisuje, jak monitorovat Azure Data Box hranici. K monitorování zařízení, můžete použít portál Azure nebo místního webového uživatelského rozhraní. Pomocí webu Azure portal do zobrazení událostí zařízení, konfigurovat a spravovat výstrahy a zobrazení metrik. Použití místního webového uživatelského rozhraní na zařízení s fyzické zobrazení stavu hardwaru různých součástí zařízení.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Zobrazit odpovídající výstrahy a události zařízení
> * Zobrazení stavu hardwaru součástí zařízení
> * Zobrazit kapacita a transakce metriky pro vaše zařízení
> * Konfigurace a Správa výstrah

## <a name="view-device-events"></a>Zobrazení událostí modulu zařízení

Pomocí následujících kroků na webu Azure Portal do zobrazení zařízení události.

1. Na webu Azure Portal, přejděte na hranici vaší zařízení Data Box / prostředku brány dat pole a potom přejděte na **monitorování > události zařízení**.
2. Vyberte události a zobrazení podrobností výstrah. Proveďte příslušné akce k vyřešení se vyskytl výstražný stav.

    ![Vyberte události a zobrazení podrobností](media/data-box-edge-monitor/view-device-events.png)

## <a name="view-hardware-status"></a>Zobrazení stavu hardwaru

Proveďte následující kroky v místním webovém uživatelském rozhraní pro zobrazení stavu hardwaru součástí zařízení. Tato informace dostupná jenom pro zařízení Data Box Edge.

1. Připojte se k místního webového uživatelského rozhraní vašeho zařízení.
2. Přejděte na **údržby > Stav hardwaru**. Můžete zobrazit stav různých součástí zařízení.

    ![Zobrazení stavu hardwaru](media/data-box-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Zobrazit metriky

Můžete také zobrazit metriky monitorovat výkon zařízení a v některých případech pro odstraňování problémů se zařízením.

Pomocí následujících kroků na webu Azure Portal k vytvoření grafu pro vybrané zařízení metriky.

1. Pro prostředek na webu Azure Portal, přejděte na **monitorování > metriky** a vyberte **přidat metriku**.

    ![Přidání metriky](media/data-box-edge-monitor/view-metrics-1.png)

2. Prostředek se vyplní automaticky.  

    ![Aktuální prostředek](media/data-box-edge-monitor/view-metrics-2.png)

    Chcete-li zadat jiný prostředek, vyberte prostředek. Na **vyberte prostředek** okno, vyberte předplatné, skupinu prostředků, typ prostředku a tento konkrétní prostředek, pro kterou chcete zobrazit metriky a vybrat **použít**.

    ![Zvolte jiný prostředek](media/data-box-edge-monitor/view-metrics-3.png)

3. Z rozevíracího seznamu vyberte metriku k monitorování zařízení. Metriky lze **metriky kapacity** nebo **metriku transakcí**. Kapacitní metriky se vztahují ke kapacitě zařízení. Metriku transakcí se vztahují k čtení a operace zápisu do služby Azure Storage.

    |Kapacitní metriky                     |Popis  |
    |-------------------------------------|-------------|
    |**Dostupná kapacita**               | Odkazuje na velikost dat, která je možné zapisovat na zařízení. Jinými slovy to je kapacita, která může být k dispozici na zařízení. <br></br>Kapacita zařízení můžete uvolnit tak, že odstraníte místní kopie souborů, které obsahují kopii na zařízení i cloud.        |
    |**Celková kapacita**                   | Odkazuje na celkový počet bajtů na zařízení k zápisu dat do. To se také označuje jako celková velikost v místní mezipaměti. <br></br> Nyní můžete zvýšit kapacitu stávajících virtuálních zařízení tak, že přidáte datový disk. Přidání datového disku pomocí správy hypervisoru pro virtuální počítač a restartujte virtuální počítač. Místní úložiště fondu zařízení brány se rozšířit, aby odpovídala nově přidal datový disk. <br></br>Další informace najdete v části [pevný disk pro virtuální počítač Hyper-V přidat](https://www.youtube.com/watch?v=EWdqUw9tTe4). |
    
    |Metriku transakcí              | Popis         |
    |-------------------------------------|---------|
    |**Cloud bajtů odeslaných (zařízení)**    | Součtu všech bajtů nahrávaných napříč všechny sdílené složky na vašem zařízení        |
    |**Nahrání bajtů cloudu (sdílená složka)**     | Bajty nahraný objem na sdílenou složku. To může být: <br></br> Prům., což je (součtu všech bajtů nahraný objem na sdílenou složku / počet sdílených složek),  <br></br>Maximální počet, což je maximální počet bajtů odeslat ze sdílené složky <br></br>Minimální, což je minimální počet bajtů odeslat ze sdílené složky      |
    |**Propustnost stahování cloudu (sdílená složka)**| Bajtů se stáhne jednotlivou sdílenou složku. To může být: <br></br> Prům., což je (součtu všech bajtů číst nebo do sdílené složky stažené / počet sdílených složek) <br></br> Maximální počet, což je maximální počet bajtů se stáhne ze sdílené složky<br></br> a minimální, což je minimální počet bajtů se stáhne ze sdílené složky  |
    |**Propustnost čtení cloudu**            | Součtu všech bajtů číst všechny sdílené složky na vašem zařízení z cloudu     |
    |**Propustnost odesílání cloudu**          | Součtu všech bajtů zapsaných do cloudu přes všechny sdílené složky na vašem zařízení     |
    |**Propustnost odesílání cloudu (sdílená složka)**  | Součtu všech bajtů zapsaných do cloudu ze sdílené složky / počet sdílených složek je průměr, maximální a minimální jednotlivou sdílenou složku      |
    |**Propustnost pro čtení (síť)**           | Zahrnuje systém propustnost sítě pro všechny bajty čtení z cloudu. Toto zobrazení může obsahovat data, která nejsou s omezeným přístupem ke sdíleným složkám. <br></br>Rozdělení zobrazí přenosy přes všechny síťové adaptéry na zařízení. To zahrnuje adaptéry, které nejsou připojené nebo povolen.      |
    |**Zápis propustnost (síť)**       | Zahrnuje systém propustnost sítě pro všechny bajtů zapsaných do cloudu. Toto zobrazení může obsahovat data, která nejsou s omezeným přístupem ke sdíleným složkám. <br></br>Rozdělení zobrazí přenosy přes všechny síťové adaptéry na zařízení. To zahrnuje adaptéry, které nejsou připojené nebo povolen.          |
    |**Edge výpočetních služeb – využití paměti**      | Využití paměti pro zařízení IoT Edge pro hranici Data Box. Pokud se zobrazí vysoké využití a výkon vašich zařízení je ovlivněna aktuální úlohy, které jste nasadili, obraťte se na Microsoft Support určit další kroky. <br></br>Tato metrika není vyplněný pro bránu dat pole.          |
    |**Edge výpočty – procento CPU**    | Využití procesoru pro zařízení IoT Edge pro hranici Data Box. Pokud se zobrazí vysoké využití a výkon vašich zařízení je ovlivněna aktuální úlohy, které jste nasadili, obraťte se na Microsoft Support určit další kroky. <br></br>Tato metrika není vyplněný pro bránu dat pole.        |
1. Při výběru metriky z rozevíracího seznamu, můžete také definovat agregace. Agregace odkazuje na skutečnou hodnotu souhrn za zadané časové rozmezí. Agregované hodnoty můžou být průměrné, minimální nebo maximální hodnoty. Vyberte agregaci Avg, Max nebo Min.

    ![Zobrazení grafu](media/data-box-edge-monitor/view-metrics-4.png)

5. Pokud má metriku, kterou jste vybrali více instancí, rozdělení možnost je k dispozici. Vyberte **použít rozdělení** a pak vyberte hodnotu, podle kterého chcete zobrazit rozpis.

    ![Použít rozdělení](media/data-box-edge-monitor/view-metrics-5.png)

6. Pokud chcete zobrazit rozpis pouze pro několik instancí, můžete data filtrovat. Například v tomto případě, pokud chcete zobrazit propustnost sítě pouze pro dvě připojená síťová rozhraní na zařízení, můžete vyfiltrovat těchto rozhraní. Vyberte **přidat filtr** a zadejte název síťového rozhraní pro filtrování.

    ![Přidat filtr](media/data-box-edge-monitor/view-metrics-6.png)

7. Také můžete například připnout graf na řídicí panel pro usnadnění přístupu.

    ![Připnout na řídicí panel](media/data-box-edge-monitor/view-metrics-7.png)

8. Pokud chcete exportovat data grafu do aplikace Excel nebo získání odkazu na graf, který můžete sdílet, vyberte možnost sdílené složky na panelu příkazů.

    ![Exportovat data](media/data-box-edge-monitor/view-metrics-8.png)

## <a name="manage-alerts"></a>Správa upozornění

Konfigurace pravidla upozornění pro informování o podmínky upozornění týkajících se spotřeby prostředků ve vašem zařízení. Můžete nakonfigurovat pravidla upozornění k monitorování zařízení pro podmínky upozornění. Další podrobné informace o výstrahách, přejděte na [vytvoření, zobrazení a Správa upozornění metrik ve službě Azure monitor](../azure-monitor/platform/alerts-metric.md).

## <a name="next-steps"></a>Další postup 

Další informace o [správě šířky pásma](data-box-edge-manage-bandwidth-schedules.md).