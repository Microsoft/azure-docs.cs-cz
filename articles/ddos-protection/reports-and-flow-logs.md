---
title: Azure DDoS Protection standardní sestavy a protokoly toků
description: Naučte se konfigurovat sestavy a protokoly toků.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 2c4349662b06ff281ef028c833c6c43dd2707051
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905166"
---
# <a name="configure-ddos-attack-mitigation-reports-and-flow-logs"></a>Konfigurace sestav o zmírnění útoků DDoS a toků v protokolech 

Azure DDoS Protection Standard poskytuje podrobné přehledy a vizualizace útoků pomocí DDoSch analýz. Zákazníci, kteří chrání své virtuální sítě před útoky DDoS, mají podrobnější přehled o útokech na útoky a akcích podniknutých za účelem zmírnění útoku prostřednictvím sestav o zmírnění útoků, & protokolů pro zmírnění rizik. Bohatá telemetrie se zveřejňuje prostřednictvím Azure Monitor včetně podrobných metrik během doby trvání útoku DDoS. Výstrahy je možné nakonfigurovat pro libovolnou Azure Monitor metriky, které jsou vystavené v DDoS Protection. Protokolování se dá dál integrovat s [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-ddos-protection), Splunk (Azure Event Hubs), OMS Log Analytics a Azure Storage pro pokročilou analýzu prostřednictvím rozhraní diagnostiky Azure monitor.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zobrazení a konfigurace sestav o zmírnění útoků DDoS
> * Zobrazení a konfigurace protokolů toku pro zmírnění útoků DDoS

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
- Než budete moct dokončit kroky v tomto kurzu, musíte nejdřív vytvořit [Plán Standard Protection pro Azure DDoS](manage-ddos-protection.md).

## <a name="view-and-configure-ddos-attack-mitigation-reports"></a>Zobrazení a konfigurace sestav o zmírnění útoků DDoS

Sestavy o zmírnění útoku využívají data protokolu NetFlow, která jsou agregovaná a poskytují podrobné informace o útoku na prostředek. V případě útoku na veřejné IP prostředky začne generování sestav hned po zahájení zmírnění. Pro celé období zmírnění rizika bude vygenerována přírůstková zpráva každých 5 minut a hlášení o zmírňování po zmírnění. K tomu je potřeba zajistit, aby v případě, že útok DDoS trvá delší dobu, budete moct zobrazit nejaktuálnější snímek sestavy o zmírnění každých 5 minut a kompletní souhrn po překročení omezení útoku. 

1. Vyberte **všechny služby** nahoře, vlevo na portálu.
2. Do pole **Filtr** zadejte *monitor* . Když se **monitor** zobrazí ve výsledcích, vyberte ho.
3. V části **Nastavení** vyberte **nastavení diagnostiky** .
4. Vyberte **předplatné** a **skupinu prostředků** obsahující veřejnou IP adresu, kterou chcete protokolovat.
5. Vyberte možnost **Veřejná IP adresa** pro **typ prostředku** a pak vyberte konkrétní veřejnou IP adresu, pro kterou chcete metriky protokolovat.
6. Vyberte **zapnout diagnostiku pro shromáždění protokolu DDoSMitigationReports** a pak vyberte tolik z následujících možností, kolik budete potřebovat:

    - **Archivace do účtu úložiště** : data se zapisují do účtu Azure Storage. Další informace o této možnosti najdete v tématu [archivní protokoly prostředků](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Streamování do centra událostí** : umožňuje přijímači protokolu vybírat protokoly pomocí centra událostí Azure. Centra událostí umožňují integraci s Splunk nebo jinými systémy SIEM. Další informace o této možnosti najdete v tématu [streamování protokolů prostředků do centra událostí](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Odeslat do Log Analytics** : zapisuje protokoly do služby Azure monitor. Další informace o této možnosti najdete v tématu [shromáždění protokolů pro použití v protokolech Azure monitor](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Mezi přírůstkové & sestavy o zmírnění útoků po útoku patří následující pole:
- Vektory útoku
- Statistika provozu
- Důvod pro vyřazené pakety
- Zapojené protokoly
- Prvních 10 zdrojových zemí nebo oblastí
- Prvních 10 zdrojů čísla ASN

## <a name="view-and-configure-ddos-attack-mitigation-flow-logs"></a>Zobrazení a konfigurace protokolů toku pro zmírnění útoků DDoS
Protokoly o omezeních toků útoků na útoky umožňují kontrolovat vyřazený provoz, předávané přenosy a další zajímavé databody v rámci aktivního DDoS útoku téměř v reálném čase. Konstantní datový proud těchto dat můžete ingestit do Azure Sentinel nebo do systémů SIEM třetích stran prostřednictvím centra událostí pro monitorování téměř v reálném čase, provádět potenciální akce a řešit nutnost vašich operací obrany.

1. Vyberte **všechny služby** nahoře, vlevo na portálu.
2. Do pole **Filtr** zadejte *monitor* . Když se **monitor** zobrazí ve výsledcích, vyberte ho.
3. V části **Nastavení** vyberte **nastavení diagnostiky** .
4. Vyberte **předplatné** a **skupinu prostředků** obsahující veřejnou IP adresu, kterou chcete protokolovat.
5. Vyberte možnost **Veřejná IP adresa** pro **typ prostředku** a pak vyberte konkrétní veřejnou IP adresu, pro kterou chcete metriky protokolovat.
6. Vyberte **zapnout diagnostiku pro shromáždění protokolu DDoSMitigationFlowLogs** a pak vyberte tolik z následujících možností, kolik budete potřebovat:

    - **Archivace do účtu úložiště** : data se zapisují do účtu Azure Storage. Další informace o této možnosti najdete v tématu [archivní protokoly prostředků](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Streamování do centra událostí** : umožňuje přijímači protokolu vybírat protokoly pomocí centra událostí Azure. Centra událostí umožňují integraci s Splunk nebo jinými systémy SIEM. Další informace o této možnosti najdete v tématu [streamování protokolů prostředků do centra událostí](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Odeslat do Log Analytics** : zapisuje protokoly do služby Azure monitor. Další informace o této možnosti najdete v tématu [shromáždění protokolů pro použití v protokolech Azure monitor](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="azure-sentinel-data-connector"></a>Datový konektor Azure Sentinel

Můžete se připojit k Azure Sentinel, zobrazit a analyzovat data v sešitech, vytvořit vlastní výstrahy a začlenit je do vyšetřovacích procesů. Pokud se chcete připojit k Azure Sentinel, přečtěte si téma [připojení k Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-ddos-protection). 

![Konektor Azure Sentinel DDoS](./media/ddos-attack-telemetry/azure-sentinel-ddos.png)

### <a name="azure-ddos-protection-workbook"></a>Sešit Azure DDoS Protection

Pokud chcete zobrazit data protokolů toku na řídicím panelu Azure Analytics, můžete si ukázkový řídicí panel naimportovat z https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20DDoS%20Protection%20Workbook

Protokoly toku budou obsahovat následující pole: 
- Zdrojová IP adresa
- Cílová IP adresa
- Zdrojový port 
- Cílový port 
- Typ protokolu 
- Akce prováděná během zmírnění

![Sešit DDoS Protection](./media/ddos-attack-telemetry/ddos-attack-analytics-workbook.png)

Analýza útoků bude fungovat jenom v případě, že je ve virtuální síti veřejné IP adresy povolený DDoS Protection Standard. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

- Zobrazení a konfigurace sestav o zmírnění útoků DDoS
- Zobrazení a konfigurace protokolů toku pro zmírnění útoků DDoS

Informace o testování a simulaci DDoS útoku najdete v průvodci simulacem testování:

> [!div class="nextstepaction"]
> [Testování prostřednictvím simulací](test-through-simulations.md)

