---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/16/2020
ms.author: alkohli
ms.openlocfilehash: 27503d1d60d961bac6dd41ebfe4fb083948cb251
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96581676"
---
Můžete také zobrazit metriky pro monitorování výkonu zařízení a v některých případech pro řešení potíží se zařízením.

Proveďte následující kroky v Azure Portal k vytvoření grafu pro vybrané metriky zařízení.

1. Pro prostředek v Azure Portal klikněte na **monitorování > metriky** a vyberte **Přidat metriky**.

    ![Přidání metriky](media/data-box-gateway-view-metrics/view-metrics-add-metric.png)

2. Prostředek se vyplní automaticky.  

    ![Aktuální prostředek](media/data-box-gateway-view-metrics/view-metrics-current-resource.png)

    Pokud chcete zadat jiný prostředek, vyberte prostředek. V okně **Vybrat prostředek** vyberte předplatné, skupinu prostředků, typ prostředku a konkrétní prostředek, pro který chcete zobrazit metriky, a vyberte **použít**.

    ![Zvolit jiný prostředek](media/data-box-gateway-view-metrics/view-metrics-choose-another-resource.png)

3. V rozevíracím seznamu vyberte metriku pro monitorování zařízení. Úplný seznam těchto metrik najdete v tématu [metriky na vašem zařízení](#metrics-on-your-device).

4. Je-li v rozevíracím seznamu vybrána metrika, je možné definovat také agregaci. Agregace odkazuje na skutečnou hodnotu agregovanou v zadaném časovém rozsahu. Agregované hodnoty můžou být průměrnou, minimální nebo maximální hodnotou. Vyberte agregaci z průměrných, maximálních nebo minimálních hodnot.

    ![Zobrazit graf](media/data-box-gateway-view-metrics/view-metrics-view-chart.png)

5. Pokud vybraná metrika má více instancí, je možnost rozdělení k dispozici. Vyberte **použít rozdělení** a potom vyberte hodnotu, podle které chcete zobrazit rozpis.

    ![Použít rozdělení](media/data-box-gateway-view-metrics/view-metrics-apply-splitting.png)

6. Pokud teď chcete zobrazit rozpis jenom pro pár instancí, můžete data filtrovat. Například v takovém případě, pokud chcete zobrazit propustnost sítě pouze pro dvě připojená síťová rozhraní v zařízení, můžete tato rozhraní filtrovat. Vyberte **Přidat filtr** a zadejte název síťového rozhraní pro filtrování.

    ![Přidat filtr](media/data-box-gateway-view-metrics/view-metrics-add-filter.png)

7. Můžete také připnout graf na řídicí panel pro snadný přístup.

    ![Připnout na řídicí panel](media/data-box-gateway-view-metrics/view-metrics-pin-to-dashboard.png)

8. Chcete-li exportovat data grafu do tabulky aplikace Excel nebo získat odkaz na graf, který lze sdílet, vyberte možnost sdílení z panelu příkazů.

    ![Exportovat data](media/data-box-gateway-view-metrics/view-metrics-export-data.png)
