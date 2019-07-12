---
title: Nastavení výstrah u diagnostický protokol událostí ze služby Azure VPN Gateway
description: Postup konfigurace výstrah pro VPN Gateway diagnostický protokol událostí
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: 4f18581b9ca5770b89be8ca37529c09d635dfb25
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607112"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>Nastavení výstrah u diagnostický protokol událostí ze služby VPN Gateway

Tento článek vám pomůže nastavit výstrahy na základě diagnostický protokol událostí ze služby Azure VPN Gateway pomocí Azure Log Analytics. 

Tyto protokoly jsou dostupné v Azure:

|***Název*** | ***Popis*** |
|---        | ---               |
|GatewayDiagnosticLog | Obsahuje diagnostické protokoly pro události konfigurace služby brány, primární změny a události údržby |
|TunnelDiagnosticLog | Obsahuje tunelové propojení událostí změny stavu. Tunelové propojení připojení/odpojení události mají souhrnný důvod pro změnu stavu, pokud je k dispozici |
|RouteDiagnosticLog | Protokoly se změní na statické trasy a protokolu BGP události, které brána |
|IKEDiagnosticLog | Protokoly IKE řídicí zprávy a události na bráně |
|P2SDiagnosticLog | Protokoly point-to-site řídicí zprávy a události na bráně |

## <a name="setup"></a>Nastavení výstrah

Následující příklady postupu vytvoří výstrahu pro událost odpojení, která zahrnuje tunelové připojení site-to-site VPN:


1. Na webu Azure Portal vyhledejte **Log Analytics** pod **všechny služby** a vyberte **pracovních prostorů Log Analytics**.

   ![Výběry pro přechod na pracovní prostory Log Analytics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Create")

2. Vyberte **vytvořit** na **Log Analytics** stránky.

   ![Log Analytics stránky pomocí tlačítka pro vytvoření](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "vyberte")

3. Vyberte **vytvořit nový** a vyplňte podrobnosti.

   ![Podrobnosti o vytváření pracovního prostoru Log Analytics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "vyberte")

4. Najít vaši bránu VPN na **monitorování** > **nastavení diagnostiky** okno.

   ![Výběry pro vyhledání brány sítě VPN v nastavení diagnostiky](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "vyberte")

5. Chcete-li zapnout diagnostiku, dvakrát klikněte na bránu a pak vyberte **zapnout diagnostiku**.

   ![Výběry pro zapínání diagnostik](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "vyberte")

6. Vyplňte podrobnosti a ujistěte se, že **odesílat do Log Analytics** a **TunnelDiagnosticLog** jsou vybrány. Zvolte pracovní prostor Log Analytics, který jste vytvořili v kroku 3.

   ![Zaškrtnuto zaškrtávací políčka](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "vyberte")

7. Přejít na Přehled pro prostředek brány virtuální sítě a vyberte **výstrahy** z **monitorování** kartu. Pak vytvořte nové pravidlo upozornění nebo upravit stávající pravidla upozornění.

   ![Vybrané možnosti pro vytvoření nové pravidlo upozornění](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "vyberte")

   ![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Select")
8. Vyberte pracovní prostor Log Analytics a prostředek.

   ![Výběr pracovního prostoru a prostředků](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "vyberte")

9. Vyberte **prohledávání protokolů vlastní** jako logiku signálů pod **přidat podmínku**.

   ![Výběry pro vlastní protokol hledání](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "vyberte")

10. Do textového pole **Vyhledávací dotaz** zadejte následující dotaz. Nahraďte hodnoty v <> podle potřeby.

     `AzureDiagnostics |
     where Category  == "TunnelDiagnosticLog" and ResourceId == toupper("<RESOURCEID OF GATEWAY>") and TimeGenerated > ago(5m) and
     remoteIP_s == "<REMOTE IP OF TUNNEL>" and status_s == "Disconnected"`

    Nastavte prahovou hodnotu na 0 a vyberte **provádí**.

    ![Zadáním dotazu a výběrem prahové hodnoty](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "vyberte")

11. Na **vytvořit pravidlo** stránce **vytvořit nový** pod **skupiny akcí** oddílu. Vyplňte podrobnosti a vyberte **OK**.

    ![Podrobnosti o nová skupina akcí](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "vyberte")

12. Na **vytvořit pravidlo** stránky, zadejte podrobnosti pro **přizpůsobit akce** a ujistěte se, že správný název se zobrazí v **název skupiny akcí** oddílu. Vyberte **vytvořit pravidlo upozornění** vytvoření pravidla.

    ![Vybrané možnosti pro vytvoření pravidla](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "vyberte")

## <a name="next-steps"></a>Další kroky

Konfigurace upozornění na metriky tunelové propojení, najdete v článku [nastavení upozornění na metriky brány VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
