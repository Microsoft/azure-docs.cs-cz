---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: e02c0b86cd542b3ea12914e35a6577cf4e9b43d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "67175458"
---
Můžete také zobrazit metriky pro monitorování výkonu zařízení a v některých případech pro řešení potíží se zařízením.

Proveďte následující kroky v Azure Portal k vytvoření grafu pro vybrané metriky zařízení.

1. Pro prostředek v Azure Portal klikněte na **monitorování > metriky** a vyberte **Přidat metriky**.

    ![Přidání metriky](media/data-box-edge-gateway-view-metrics/view-metrics-1.png)

2. Prostředek se vyplní automaticky.  

    ![Aktuální prostředek](media/data-box-edge-gateway-view-metrics/view-metrics-2.png)

    Pokud chcete zadat jiný prostředek, vyberte prostředek. V okně **Vybrat prostředek** vyberte předplatné, skupinu prostředků, typ prostředku a konkrétní prostředek, pro který chcete zobrazit metriky, a vyberte **použít**.

    ![Zvolit jiný prostředek](media/data-box-edge-gateway-view-metrics/view-metrics-3.png)

3. V rozevíracím seznamu vyberte metriku pro monitorování zařízení. Metrikami můžou být metriky **kapacity** nebo metriky **transakcí**. Metriky kapacity se týkají kapacity zařízení. Metriky transakcí souvisejí s operacemi čtení a zápisu, které jsou Azure Storage.

    |Metriky kapacity                     |Popis  |
    |-------------------------------------|-------------|
    |**Dostupná kapacita**               | Odkazuje na velikost dat, která se dají zapsat do zařízení. Jinými slovy, to je kapacita, kterou lze v zařízení zpřístupnit. <br></br>Kapacitu zařízení můžete uvolnit tak, že odstraníte místní kopii souborů, která má kopii na zařízení i v cloudu.        |
    |**Celková kapacita**                   | Odkazuje na celkový počet bajtů na zařízení, do kterého se mají zapisovat data. To se také označuje jako celková velikost místní mezipaměti. <br></br> Kapacitu stávajícího virtuálního zařízení teď můžete zvýšit přidáním datového disku. Přidejte datový disk prostřednictvím správy hypervisoru pro virtuální počítač a pak restartujte virtuální počítač. Místní fond úložiště zařízení brány se rozšíří tak, aby odpovídal nově přidanému datovému disku. <br></br>Další informace získáte, když přejdete na [Přidat pevný disk pro virtuální počítač Hyper-V](https://www.youtube.com/watch?v=EWdqUw9tTe4). |
    
    |Transakční metriky              | Popis         |
    |-------------------------------------|---------|
    |**Odeslané bajty v cloudu (zařízení)**    | Součet všech bajtů odeslaných napříč všemi sdílenými složkami ve vašem zařízení        |
    |**Odeslané bajty v cloudu (sdílená složka)**     | Počet odeslaných bajtů na sdílenou složku. Může to být: <br></br> Střední, což je (součet všech odeslaných bajtů na sdílenou položku/počet sdílených složek),  <br></br>Max, což je maximální počet bajtů odeslaných ze sdílené složky <br></br>Min, což je minimální počet bajtů odeslaných ze sdílené složky      |
    |**Propustnost stahování do cloudu (sdílení)**| Počet stažených bajtů na sdílenou složku. Může to být: <br></br> Střední, což je (součet všech přečtených nebo stažených bajtů do sdílené složky/počtu sdílených složek) <br></br> Max, což je maximální počet bajtů stažených ze sdílené složky<br></br> a min, což je minimální počet bajtů stažených ze sdílené složky  |
    |**Propustnost čtení v cloudu**            | Součet všech přečtených bajtů z cloudu napříč všemi sdílenými složkami v zařízení     |
    |**Propustnost nahrávání do cloudu**          | Součet všech bajtů zapsaných do cloudu napříč všemi sdílenými složkami v zařízení     |
    |**Propustnost nahrávání do cloudu (sdílení)**  | Součet všech bajtů zapsaných do cloudu ze sdílené složky/počtu sdílených složek je průměrně, Max a min na sdílenou složku.      |
    |**Propustnost čtení (síť)**           | Zahrnuje propustnost systémové sítě pro všechny přečtené bajty z cloudu. Toto zobrazení může obsahovat data, která nejsou omezená na sdílené složky. <br></br>Při rozdělování se zobrazí přenos všech síťových adaptérů na zařízení. To zahrnuje adaptéry, které nejsou připojené nebo nejsou povolené.      |
    |**Propustnost zápisu (síť)**       | Zahrnuje propustnost systémové sítě pro všechny bajty zapsané do cloudu. Toto zobrazení může obsahovat data, která nejsou omezená na sdílené složky. <br></br>Při rozdělování se zobrazí přenos všech síťových adaptérů na zařízení. To zahrnuje adaptéry, které nejsou připojené nebo nejsou povolené.          |
    |**Výpočet využití paměti na hraničních zařízeních**      | Tato metrika se nedá použít pro Data Box Gateway a proto není naplněna.          |
    |**Výpočetní prostředí Edge – procento využití procesoru**    | Tato metrika se nedá použít pro Data Box Gateway a proto není naplněna.         |

4. Je-li v rozevíracím seznamu vybrána metrika, je možné definovat také agregaci. Agregace odkazuje na skutečnou hodnotu agregovanou v zadaném časovém rozsahu. Agregované hodnoty mohou být průměr, minimum nebo maximální hodnota. Vyberte agregaci z průměrných, maximálních nebo minimálních hodnot.

    ![Zobrazit graf](media/data-box-edge-gateway-view-metrics/view-metrics-4.png)

5. Pokud vybraná metrika má více instancí, je možnost rozdělení k dispozici. Vyberte **použít rozdělení** a pak vyberte hodnotu, podle které chcete zobrazit rozpis.

    ![Použít rozdělení](media/data-box-edge-gateway-view-metrics/view-metrics-5.png)

6. Pokud teď chcete zobrazit rozpis jenom pro pár instancí, můžete data filtrovat. Například v takovém případě, pokud chcete zobrazit propustnost sítě pouze pro dvě připojená síťová rozhraní v zařízení, můžete tato rozhraní filtrovat. Vyberte **Přidat filtr** a zadejte název síťového rozhraní pro filtrování.

    ![Přidat filtr](media/data-box-edge-gateway-view-metrics/view-metrics-6.png)

7. Můžete také připnout graf na řídicí panel pro snadný přístup.

    ![Připnout na řídicí panel](media/data-box-edge-gateway-view-metrics/view-metrics-7.png)

8. Chcete-li exportovat data grafu do tabulky aplikace Excel nebo získat odkaz na graf, který lze sdílet, vyberte možnost sdílení z panelu příkazů.

    ![Exportovat data](media/data-box-edge-gateway-view-metrics/view-metrics-8.png)