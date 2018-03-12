---
title: "Správa Azure DDoS ochrany standardní pomocí portálu Azure | Microsoft Docs"
description: "Naučte se používat Azure DDoS ochrany standardní telemetrie v Azure monitorování zmírnit útok."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: 6a5ab1ba44197c0103e1e7d353a116dc01dfc163
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Správa Azure DDoS ochrany standardní pomocí portálu Azure

Zjistěte, jak povolit a zakázat distribuovaná útok na dostupnost služby (Denial) ochrany, a zmírnit útoku DDoS s Azure DDoS ochrany standardní pomocí telemetrie. DDoS ochrany standardní chrání prostředky Azure, jako jsou virtuální počítače, nástroje pro vyrovnávání zatížení a aplikačních bran, které mají Azure [veřejnou IP adresu](virtual-network-public-ip-address.md) přiřazen. Další informace o DDoS ochrany standardní a jeho funkce, najdete v části [DDoS ochrany standardní přehled](ddos-protection-overview.md). 

>[!IMPORTANT]
>Azure DDoS ochrany Standard (Ochrana proti útoku DDoS) se aktuálně ve verzi preview. Ochrana proti útoku DDoS podporovat omezený počet prostředků Azure a je k dispozici pouze v vyberte počet oblastí. Seznam dostupných oblastí najdete v tématu [DDoS ochrany standardní přehled](ddos-protection-overview.md). Budete muset [zaregistrovat pro službu](http://aka.ms/ddosprotection) během omezené preview získat ochrana proti útoku DDoS pro vaše předplatné povolený. Po registraci, vás kontaktovat tým Azure DDoS, který vás provede procesem povolování. 

## <a name="enable-ddos-protection-standard---new-virtual-network"></a>Povolit DDoS ochrany Standard - nové virtuální sítě

1. Přihlaste se k webu Azure Portal na adrese http://portal.azure.com. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
2. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.
3. Vyberte **sítě**a potom vyberte **virtuální sítě**.
4. Vytvoření virtuální sítě s zvolené nastavení. Další informace o vytváření virtuální sítě najdete v tématu [vytvořit virtuální síť](manage-virtual-network.md#create-a-virtual-network). V části **ochrana proti útoku DDoS**, klikněte na tlačítko **povoleno**a potom klikněte na **vytvořit**. Pokud nevidíte **ochrana proti útoku DDoS**, pravděpodobnou příčinou je, že vaše předplatné není zaregistrované pro funkci. Je třeba provést [registrace](http://aka.ms/ddosprotection)a přijímat oznámení, že vaše předplatné bylo povoleno pro funkci, před **ochrana proti útoku DDoS** se zobrazí.

    ![Vytvoření virtuální sítě](./media/ddos-protection-manage-portal/ddos-create-vnet.png)   

    > [!WARNING]
    > Když vyberete oblast, zvolte ze seznamu v podporované oblasti [Azure DDoS ochrany standardní přehled](ddos-protection-overview.md). Pokud nevyberete podporovanou oblast, vytvoření virtuální sítě se nezdaří.

    Upozornění s oznámením, že povolení ochrana proti útoku DDoS budou vám účtovány poplatky. Během preview se vám neúčtují žádné poplatky za ochrana proti útoku DDoS. Poplatky za zpoplatněná při obecné dostupnosti. Zobrazí se oznámení 30 dní, před začátek poplatky a obecné dostupnosti.

## <a name="enable-ddos-protection-standard---existing-virtual-network"></a>Povolit DDoS ochrany Standard - existující virtuální síť 

1. Klikněte na tlačítko **virtuální sítě** v nabídce portálu Azure a potom vyberte virtuální síť.
2. Klikněte na tlačítko **ochrana proti útoku DDoS**, klikněte na tlačítko **povoleno** na *ochrana proti útoku DDoS* obrazovky a pak klikněte na tlačítko **Uložit**. Pokud nevidíte **ochrana proti útoku DDoS**, pravděpodobnou příčinou je, že vaše předplatné není zaregistrované pro funkci. Je třeba provést [registrace](http://aka.ms/ddosprotection)a přijímat oznámení, že vaše předplatné bylo povoleno pro funkci, před **ochrana proti útoku DDoS** se zobrazí. 

    > [!WARNING]
    > Virtuální síť musí existovat v podporované oblasti. Seznam podporovaných oblastí najdete v tématu [Azure DDoS ochrany standardní přehled](ddos-protection-overview.md).

    Upozornění s oznámením, že povolení ochrana proti útoku DDoS budou vám účtovány poplatky. Během preview se vám neúčtují žádné poplatky za ochrana proti útoku DDoS. Poplatky za zpoplatněná při obecné dostupnosti. Zobrazí se oznámení 30 dní, před začátek poplatky a obecné dostupnosti.

## <a name="disable-ddos-protection-on-a-virtual-network"></a>Zakažte ochranu DDoS ve virtuální síti

1. Klikněte na tlačítko **virtuální sítě** v nabídce portálu Azure a potom vyberte virtuální síť.
2. Klikněte na tlačítko **ochrana proti útoku DDoS**, klikněte na tlačítko **zakázané** na *ochrana proti útoku DDoS* obrazovky a pak klikněte na tlačítko **Uložit**.

## <a name="configure-alerts-on-ddos-protection-metrics"></a>Nakonfigurujte upozornění na metriky ochrana proti útoku DDoS

Můžete vybrat všechny dostupné metriky ochrana proti útoku DDoS pro upozornění, když dojde active zmírnění při útoku pomocí konfigurace výstrah monitorování Azure. Pokud jsou splněny podmínky, zadaná adresa obdrží upozornění e-mailu.

1. Klikněte na tlačítko **monitorování**a potom klikněte na **metriky**.
2. Na *metriky* obrazovky, vyberte skupinu prostředků, typ prostředku **veřejnou IP adresu**a Azure veřejné IP adresy.
3. Chcete-li nakonfigurovat e-mailové upozornění pro metriku, klikněte na tlačítko **přidat metriky upozornění**. Na všechny metriky lze vytvořit e-mailové výstrahy, ale nejobvyklejší metrika **útoku DDoS pod nebo není**. Toto je logická hodnota, 1 nebo 0. A **1** znamená, můžete v útoku. A **0** znamená nejsou napadené.
4. Se má v případě útoku, nastavte metriku pro **útoku DDoS pod nebo není** a **podmínku, která má větší než nula (0) za posledních 5 minut**. Podobné výstrahy můžete nastavit pro jiné metriky.

    ![Konfigurace metrik](./media/ddos-protection-manage-portal/ddos-metrics.png)

    Během několika minut detekce útoku budete upozorněni, používání Azure monitorování metriky.

    ![Útok výstrahy](./media/ddos-protection-manage-portal/ddos-alert.png) 

Můžete si také přečíst informace o [konfigurace webhooky](../monitoring-and-diagnostics/insights-webhooks-alerts.md) a [aplikace logiky](../logic-apps/logic-apps-overview.md) pro vytvoření výstrahy.

## <a name="configure-logging-on-ddos-protection-standard-metrics"></a>Konfigurovat protokolování na DDoS ochrany standardní metriky

1. Klikněte na tlačítko **monitorování**a potom klikněte na **nastavení pro diagnostiku**.
2. Na *metriky* obrazovky, vyberte skupinu prostředků, typ prostředku **veřejnou IP adresu**a Azure veřejné IP adresy.
3. Klikněte na tlačítko **zapněte diagnostiku shromažďovat následující data**.

K dispozici pro protokolování jsou tři možnosti:

- **Archiv na účet úložiště**: zapisuje protokoly do účtu úložiště.
- **Datový proud do centra událostí**: umožňuje protokol příjemce pro vyzvednutí protokolů pomocí centra událostí. To umožňuje integraci s Splunk nebo jiných systémů SIEM.
- **Odeslat k analýze protokolů**: zapisuje protokoly ke službě Analýza protokolů Azure OMS.

## <a name="use-ddos-protection-telemetry"></a>Pomocí telemetrie ochrana proti útoku DDoS

Telemetrie na útok je zajišťováno prostřednictvím Azure monitorování v reálném čase. Telemetrie je k dispozici pouze po dobu, která veřejnou IP adresu je pod zmírnění dopadů. Neuvidíte telemetrie před nebo po zmírnit útok.

1. Klikněte na tlačítko **monitorování**a potom klikněte na **metriky**. 
2. Na *metriky* obrazovky, vyberte skupinu prostředků, typ prostředku **veřejnou IP adresu**a Azure veřejné IP adresy. Řadu **dostupné metriky** se zobrazí na levé straně obrazovky. Tyto metriky, pokud vybraná, vykreslovacích v **Azure monitorování metriky grafu** na obrazovce Přehled. 

Metriky názvy představovat různých typů paketů a bajtů oproti paketů, základní konstrukce názvů značky na jednotlivé metriky následujícím způsobem:

- **Název značky zahozené (například příchozí pakety vyřadit DDoS)**: počet paketů očistí ochrany systémem DDoS jejich vyřadit.
- **Název značky předané (např: příchozí pakety předají DDoS)**: počet paketů, které jsou předávány DDoS systému do cílového umístění VIP – provoz, který nebyl nefiltruje.
- **Žádný název značky (např: příchozí pakety DDoS):** celkový počet paketů, které byly dodány do čištění dat systému – představující součet pakety vyřazen a předávat.

## <a name="next-steps"></a>Další postup

- [Další informace o diagnostických protokolů Azure.](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Analýza protokolů z úložiště Azure s analýzy protokolů](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Začínáme s Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md?toc=%2fazure%2fvirtual-network%2ftoc.json)