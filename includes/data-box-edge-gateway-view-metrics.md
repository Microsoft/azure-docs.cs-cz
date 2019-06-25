---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: e02c0b86cd542b3ea12914e35a6577cf4e9b43d8
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67175458"
---
Můžete také zobrazit metriky monitorovat výkon zařízení a v některých případech pro odstraňování problémů se zařízením.

Pomocí následujících kroků na webu Azure Portal k vytvoření grafu pro vybrané zařízení metriky.

1. Pro prostředek na webu Azure Portal, přejděte na **monitorování > metriky** a vyberte **přidat metriku**.

    ![Přidání metriky](media/data-box-edge-gateway-view-metrics/view-metrics-1.png)

2. Prostředek se vyplní automaticky.  

    ![Aktuální prostředek](media/data-box-edge-gateway-view-metrics/view-metrics-2.png)

    Chcete-li zadat jiný prostředek, vyberte prostředek. Na **vyberte prostředek** okno, vyberte předplatné, skupinu prostředků, typ prostředku a tento konkrétní prostředek, pro kterou chcete zobrazit metriky a vybrat **použít**.

    ![Zvolte jiný prostředek](media/data-box-edge-gateway-view-metrics/view-metrics-3.png)

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
    |**Edge výpočetních služeb – využití paměti**      | Tato metrika není možné použít bránu dat pole a proto není naplněná.          |
    |**Edge výpočty – procento CPU**    | Tato metrika není možné použít bránu dat pole a proto není naplněná.         |

4. Při výběru metriky z rozevíracího seznamu, můžete také definovat agregace. Agregace odkazuje na skutečnou hodnotu souhrn za zadané časové rozmezí. Agregované hodnoty můžou být průměrné, minimální nebo maximální hodnoty. Vyberte agregaci Avg, Max nebo Min.

    ![Zobrazení grafu](media/data-box-edge-gateway-view-metrics/view-metrics-4.png)

5. Pokud má metriku, kterou jste vybrali více instancí, rozdělení možnost je k dispozici. Vyberte **použít rozdělení** a pak vyberte hodnotu, podle kterého chcete zobrazit rozpis.

    ![Použít rozdělení](media/data-box-edge-gateway-view-metrics/view-metrics-5.png)

6. Pokud chcete zobrazit rozpis pouze pro několik instancí, můžete data filtrovat. Například v tomto případě, pokud chcete zobrazit propustnost sítě pouze pro dvě připojená síťová rozhraní na zařízení, můžete vyfiltrovat těchto rozhraní. Vyberte **přidat filtr** a zadejte název síťového rozhraní pro filtrování.

    ![Přidat filtr](media/data-box-edge-gateway-view-metrics/view-metrics-6.png)

7. Také můžete například připnout graf na řídicí panel pro usnadnění přístupu.

    ![Připnout na řídicí panel](media/data-box-edge-gateway-view-metrics/view-metrics-7.png)

8. Pokud chcete exportovat data grafu do aplikace Excel nebo získání odkazu na graf, který můžete sdílet, vyberte možnost sdílené složky na panelu příkazů.

    ![Exportovat data](media/data-box-edge-gateway-view-metrics/view-metrics-8.png)