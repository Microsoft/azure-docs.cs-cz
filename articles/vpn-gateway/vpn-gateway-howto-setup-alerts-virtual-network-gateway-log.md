---
title: Jak nastavit výstrahy na události protokolu diagnostiky Azure VPN Gateway
description: Postup konfigurace výstrah pro VPN Gateway diagnostický protokol událostí
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: 39a6d2e6201dd0635149159cb3727fd3c40f710a
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769733"
---
# <a name="setting-up-alerts-on-vpn-gateway-diagnostic-log-events"></a>Nastavení upozornění na události diagnostický protokol VPN Gateway

Tento článek vám pomůže nastavit výstrahy na základě událostí diagnostický protokol VPN Gateway.


## <a name="setup"></a>Nastavení Azure Monitor výstrah na základě diagnostický protokol událostí pomocí portálu

Následující příklad postup vytvoří výstrahu pro událost odpojení tunelového propojení sítě VPN typu site-to-site



1. Vyhledejte "Log Analytics" v části všechny služby a vyberte "Pracovní prostory Log Analytics" ![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Create")

2. Klikněte na možnost "Vytvořit" na stránce Log Analytics.
![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Select")

3. Zkontrolujte "Vytvořit nový" pracovní prostor a vyplňte podrobnosti.
![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Select")

4. Vyhledejte bránu sítě VPN v části "Monitorování" > okno "Nastavení diagnostiky" ![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "vyberte")

5. Chcete-li zapnout diagnostiku, dvakrát klikněte na bránu a potom klikněte na "Zapnout diagnostiku" ![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "vyberte")

6. Vyplňte podrobnosti a ověřte, že je zaškrtnuté políčko "Odeslání do Log Analytics" a "TunnelDiagnosticLog". Vyberte pracovní prostor Log Analytics, který byl vytvořen v kroku 3.
![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Select")

7. Přejít na Přehled prostředků brány virtuální sítě a na kartě monitorování vyberte "Oznámení" a vytvořit nové pravidlo upozornění nebo upravit stávající pravidla upozornění.
![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Select")

8. Vyberte pracovní prostor Log Analytics a prostředek.
![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Select")

9. Vyberte možnost "vlastní prohledávání protokolů" jako logiku signálů v části "Přidat podmínku" ![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "vyberte")

10. Zadejte následující dotaz v textovém poli "Vyhledávání", nahraďte hodnoty v <> podle potřeby.

    AzureDiagnostics | Pokud kategorie == "TunnelDiagnosticLog" a ResourceId == toupper ("<RESOURCEID OF GATEWAY>") a TimeGenerated > ago(5m) a remoteIP_s == "<REMOTE IP OF TUNNEL>" a status_s == "Odpojeno"

    Nastavte prahovou hodnotu na 0 a klikněte na "Hotovo"

    ![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Select")

11. Na stránce "Vytvořit pravidlo" klikněte na možnost "Vytvořit nový" v části skupiny akcí. Vyplňte podrobnosti a klikněte na tlačítko OK

![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Select")

12. Na stránce "Vytvořit pravidlo" Vyplňte podrobnosti pro "Přizpůsobit akce" a ujistěte se, že se zobrazí správný název skupiny akcí v části "Název skupiny akcí". Klikněte na tlačítko "Vytvoření pravidla upozornění" Vytvoření pravidla.
![point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Select")

## <a name="next-steps"></a>Další postup

Konfigurace upozornění na metriky tunelové propojení, najdete v článku [jak nastavit výstrahy na metriky brány VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
