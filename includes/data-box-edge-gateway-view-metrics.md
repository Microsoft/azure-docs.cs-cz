---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 9ac7966538102273b91d6b7f15b90e18ceedd421
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83779934"
---
Můžete také zobrazit metriky pro monitorování výkonu zařízení a v některých případech pro řešení potíží se zařízením.

Proveďte následující kroky v Azure Portal k vytvoření grafu pro vybrané metriky zařízení.

1. Pro prostředek v Azure Portal klikněte na **monitorování > metriky** a vyberte **Přidat metriky**.

    ![Přidání metriky](media/data-box-edge-gateway-view-metrics/view-metrics-1.png)

2. Prostředek se vyplní automaticky.  

    ![Aktuální prostředek](media/data-box-edge-gateway-view-metrics/view-metrics-2.png)

    Pokud chcete zadat jiný prostředek, vyberte prostředek. V okně **Vybrat prostředek** vyberte předplatné, skupinu prostředků, typ prostředku a konkrétní prostředek, pro který chcete zobrazit metriky, a vyberte **použít**.

    ![Zvolit jiný prostředek](media/data-box-edge-gateway-view-metrics/view-metrics-3.png)

3. V rozevíracím seznamu vyberte metriku pro monitorování zařízení. Úplný seznam těchto metrik najdete v tématu [metriky na vašem zařízení](#metrics-on-your-device).

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
