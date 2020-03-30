---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: e02c0b86cd542b3ea12914e35a6577cf4e9b43d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175458"
---
Můžete také zobrazit metriky pro sledování výkonu zařízení a v některých případech pro řešení problémů se zařízením.

Na webu Azure Portal vytvořte graf pro vybrané metriky zařízení následujícím postupem.

1. Pro váš prostředek na webu Azure Portal přejděte na **sledování > metriky** a vyberte **Přidat metriku**.

    ![Přidání metriky](media/data-box-edge-gateway-view-metrics/view-metrics-1.png)

2. Prostředek je automaticky naplněn.  

    ![Aktuální zdroj](media/data-box-edge-gateway-view-metrics/view-metrics-2.png)

    Chcete-li zadat jiný zdroj, vyberte jej. V **části Vybrat** okno prostředku vyberte předplatné, skupinu prostředků, typ prostředku a konkrétní prostředek, pro který chcete zobrazit metriky, a vyberte **použít**.

    ![Výběr jiného zdroje](media/data-box-edge-gateway-view-metrics/view-metrics-3.png)

3. V rozevíracím seznamu vyberte metriku, která má sledovat vaše zařízení. Metriky mohou být **metriky kapacity** nebo **metriky transakce**. Metriky kapacity se vztahují k kapacitě zařízení. Metriky transakcí se vztahují k operacím čtení a zápisu do Služby Azure Storage.

    |Metriky kapacity                     |Popis  |
    |-------------------------------------|-------------|
    |**Dostupná kapacita**               | Odkazuje na velikost dat, která mohou být zapsána do zařízení. Jinými slovy, jedná se o kapacitu, která může být k dispozici na zařízení. <br></br>Kapacitu zařízení můžete uvolnit odstraněním místní kopie souborů, které mají kopii v zařízení i v cloudu.        |
    |**Celková kapacita**                   | Odkazuje na celkový počet bajtů v zařízení pro zápis dat. To se také označuje jako celková velikost místní mezipaměti. <br></br> Nyní můžete zvýšit kapacitu existujícího virtuálního zařízení přidáním datového disku. Přidejte datový disk prostřednictvím správy hypervisoru pro virtuální počítač a restartujte virtuální počítač. Místní fond úložiště zařízení Gateway se rozbalí tak, aby vyhovoval nově přidanému datovému disku. <br></br>Další informace najdete [v najdete v doplňku Přidání pevného disku pro virtuální počítač Hyper-V](https://www.youtube.com/watch?v=EWdqUw9tTe4). |
    
    |Transakční metriky              | Popis         |
    |-------------------------------------|---------|
    |**Nahrané bajty v cloudu (zařízení)**    | Součet všech bajtů nahraných ve všech sdílených složek v zařízení        |
    |**Nahrané bajty v cloudu (sdílet)**     | Bajty nahrané na sdílenou složku. Může to být: <br></br> Avg, což je (Součet všech bajtů nahraných na akcii / počet akcií),  <br></br>Max, což je maximální počet bajtů nahraných ze sdílené položky <br></br>Min, což je minimální počet bajtů nahraných ze sdílené položky      |
    |**Propustnost stahování v cloudu (sdílená)**| Bajty stažené na sdílenou složku. Může to být: <br></br> Avg, což je (Součet všech bajtů přečtených nebo stažených do akcie / počet akcií) <br></br> Max, což je maximální počet bajtů stažených ze sdílené položky<br></br> a Min, což je minimální počet bajtů stažených z akcie  |
    |**Propustnost pro čtení v cloudu**            | Součet všech bajtů přečtených z cloudu ve všech sdílených složek v zařízení     |
    |**Propustnost nahrávání v cloudu**          | Součet všech bajtů zapsaných do cloudu ve všech sdílených složek v zařízení     |
    |**Propustnost nahrávání do cloudu (sdílení)**  | Součet všech bajtů zapsaných do cloudu z podílu / # akcií je průměrný, max a min na akcii      |
    |**Propustnost čtení (síť)**           | Zahrnuje propustnost systémové sítě pro všechny bajty přečtené z cloudu. Toto zobrazení může obsahovat data, která nejsou omezena na sdílené složky. <br></br>Rozdělení zobrazí provoz přes všechny síťové adaptéry v zařízení. To zahrnuje adaptéry, které nejsou připojeny nebo povoleny.      |
    |**Propustnost zápisu (síť)**       | Zahrnuje propustnost systémové sítě pro všechny bajty zapsané do cloudu. Toto zobrazení může obsahovat data, která nejsou omezena na sdílené složky. <br></br>Rozdělení zobrazí provoz přes všechny síťové adaptéry v zařízení. To zahrnuje adaptéry, které nejsou připojeny nebo povoleny.          |
    |**Edge compute – využití paměti**      | Tato metrika se nevztahuje na bránu datové schránky, a proto není naplněna.          |
    |**Edge compute - procento CPU**    | Tato metrika se nevztahuje na bránu datové schránky, a proto není naplněna.         |

4. Když je metrika vybrána z rozevíracího seznamu, lze také definovat agregaci. Agregace odkazuje na skutečnou hodnotu agregován v zadaném časovém rozpětí. Agregované hodnoty mohou být průměrné, minimální nebo maximální hodnoty. Vyberte agregaci z avg, max nebo min.

    ![Zobrazit graf](media/data-box-edge-gateway-view-metrics/view-metrics-4.png)

5. Pokud metrika, kterou jste vybrali, obsahuje více instancí, je k dispozici možnost rozdělení. Vyberte **Použít rozdělení** a pak vyberte hodnotu, podle které chcete zobrazit rozdělení.

    ![Použít rozdělení](media/data-box-edge-gateway-view-metrics/view-metrics-5.png)

6. Pokud nyní chcete rozdělení zobrazit pouze pro několik instancí, můžete data filtrovat. Například v tomto případě, pokud chcete zobrazit propustnost sítě pouze pro dvě připojená síťová rozhraní v zařízení, můžete filtrovat tato rozhraní. Vyberte **Přidat filtr** a určete název síťového rozhraní pro filtrování.

    ![Přidat filtr](media/data-box-edge-gateway-view-metrics/view-metrics-6.png)

7. Můžete také připnout graf na řídicí panel pro snadný přístup.

    ![Připnout na řídicí panel](media/data-box-edge-gateway-view-metrics/view-metrics-7.png)

8. Chcete-li exportovat data grafu do excelové tabulky nebo získat odkaz na graf, který můžete sdílet, vyberte možnost sdílení z panelu příkazů.

    ![Exportovat data](media/data-box-edge-gateway-view-metrics/view-metrics-8.png)