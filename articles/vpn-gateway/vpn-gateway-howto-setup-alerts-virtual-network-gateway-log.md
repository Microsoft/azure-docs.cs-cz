---
title: 'Azure VPN Gateway: Konfigurace výstrah pro události diagnostického protokolu'
description: Postup konfigurace výstrah pro události diagnostického protokolu VPN Gateway
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: eb8f0204771b204af740c4ddc8e359499520a012
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045946"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>Nastavení výstrah pro události diagnostického protokolu z VPN Gateway

Tento článek vám pomůže nastavit výstrahy na základě událostí diagnostického protokolu z Azure VPN Gateway pomocí Azure Log Analytics. 

V Azure jsou k dispozici následující protokoly:

|***Název*** | ***Popis*** |
|---        | ---               |
|GatewayDiagnosticLog | Obsahuje protokoly diagnostiky pro události konfigurace brány, primární změny a události údržby. |
|TunnelDiagnosticLog | Obsahuje události změny stavu tunelu. Události připojení a odpojení tunelu mají souhrnný důvod změny stavu, pokud je to možné. |
|RouteDiagnosticLog | Zaznamená změny do statických tras a událostí protokolu BGP, ke kterým dojde v bráně. |
|IKEDiagnosticLog | Protokoluje zprávy řízení IKE a události v bráně. |
|P2SDiagnosticLog | Protokoluje zprávy a události řízení Point-to-site v bráně. |

## <a name="setup"></a>Nastavení výstrah

Následující příklad postupu vytvoří výstrahu pro událost odpojení, která zahrnuje tunel VPN typu Site-to-site:


1. V Azure Portal vyhledejte **Log Analytics** v části **všechny služby** a vyberte **Log Analytics pracovní prostory**.

   ![Výběry pro přechodu na Log Analytics pracovní prostory](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Vytváření")

2. Na stránce **Log Analytics** vyberte **vytvořit** .

   ![Stránka Log Analytics s tlačítkem vytvořit](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Vyberte")

3. Vyberte **vytvořit novou** a vyplňte podrobnosti.

   ![Podrobnosti o vytvoření pracovního prostoru Log Analytics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Vyberte")

4. Vyhledejte bránu VPN v okně **sledování** > **nastavení diagnostiky** .

   ![Výběry pro vyhledání brány VPN v nastavení diagnostiky](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "Vyberte")

5. Pokud chcete zapnout diagnostiku, poklikejte na bránu a pak vyberte **zapnout diagnostiku**.

   ![Výběry pro zapnutí diagnostiky](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "Vyberte")

6. Vyplňte podrobnosti a ujistěte se, že je vybraná možnost **Odeslat do Log Analytics** a **TunnelDiagnosticLog** . Vyberte pracovní prostor Log Analytics, který jste vytvořili v kroku 3.

   ![Vybraná zaškrtávací políčka](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Vyberte")

   > [!NOTE]
   > Počáteční zobrazení dat může trvat několik hodin.

7. Přejít na přehled prostředku brány virtuální sítě a vybrat **výstrahy** na kartě **monitorování** . Pak vytvořte nové pravidlo výstrahy nebo upravte existující pravidlo výstrahy.

   ![Výběry pro vytvoření nového pravidla výstrahy](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Vyberte")

   ![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Vyberte")
8. Vyberte pracovní prostor Log Analytics a prostředek.

   ![Výběry pro pracovní prostor a prostředek](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Vyberte")

9. Jako logiku signálu v části **Přidat podmínku**vyberte **vlastní prohledávání protokolu** .

   ![Výběry pro vlastní prohledávání protokolu](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "Vyberte")

10. Do textového pole **Vyhledávací dotaz** zadejte následující dotaz. Nahraďte hodnoty v < > a TimeGenerated podle potřeby.

    ```
    AzureDiagnostics
    | where Category == "TunnelDiagnosticLog"
    | where _ResourceId == tolower("<RESOURCEID OF GATEWAY>")
    | where TimeGenerated > ago(5m) 
    | where remoteIP_s == "<REMOTE IP OF TUNNEL>"
    | where status_s == "Disconnected"
    | project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId 
    | sort by TimeGenerated asc
    ```

    Nastavte mezní hodnotu na 0 a vyberte **Hotovo**.

    ![Zadání dotazu a výběr prahové hodnoty](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Vyberte")

11. Na stránce **vytvořit pravidlo** vyberte v části **skupiny akcí** položku **vytvořit nový** . Vyplňte podrobnosti a vyberte **OK**.

    ![Podrobnosti nové skupiny akcí](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Vyberte")

12. Na stránce **vytvořit pravidlo** vyplňte podrobnosti pro **vlastní akce** a ujistěte se, že se v části **název skupiny akcí** zobrazuje správný název. Vyberte **vytvořit pravidlo upozornění** a vytvořte pravidlo.

    ![Výběry pro vytvoření pravidla](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Vyberte")

## <a name="next-steps"></a>Další kroky

Pokud chcete konfigurovat výstrahy pro metriky tunelu, přečtěte si téma [Nastavení výstrah pro VPN Gateway metriky](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
