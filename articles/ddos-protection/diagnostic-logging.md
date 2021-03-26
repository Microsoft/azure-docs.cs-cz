---
title: Azure DDoS Protection standardní sestavy a protokoly toků
description: Naučte se konfigurovat sestavy a protokoly toků.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: 7f8e3df927b74cff7e4dc8bf1456600740c07088
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567674"
---
# <a name="view-and-configure-ddos-diagnostic-logging"></a>Zobrazení a konfigurace protokolování diagnostiky DDoS

Azure DDoS Protection Standard poskytuje podrobné přehledy a vizualizace útoků pomocí DDoSch analýz. Zákazníci, kteří chrání své virtuální sítě před útoky DDoS, mají podrobnější přehled o útokech na útoky a akcích podniknutých za účelem zmírnění útoku prostřednictvím sestav o zmírnění útoků, & protokolů pro zmírnění rizik. Bohatá telemetrie se zveřejňuje prostřednictvím Azure Monitor včetně podrobných metrik během doby trvání útoku DDoS. Upozornění je možné nakonfigurovat pro všechny metriky služby Azure Monitor zveřejněné službou DDoS Protection. Protokolování se dá dál integrovat s [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), Splunk (Azure Event Hubs), OMS Log Analytics a Azure Storage pro pokročilou analýzu prostřednictvím rozhraní diagnostiky Azure monitor.

Pro Azure DDoS Protection Standard jsou k dispozici následující diagnostické protokoly: 

- **DDoSProtectionNotifications**: oznámení vás upozorní na to, že dojde k útoku na prostředek veřejné IP adresy, a když dojde k překročení omezení útoků.
- **DDoSMitigationFlowLogs**: protokoly o omezeních toků útoků na útoky umožňují kontrolovat vyřazený provoz, předaný provoz a další zajímavé databody během útoku aktivní DDoS téměř v reálném čase. Konstantní datový proud těchto dat můžete ingestit do Azure Sentinel nebo do systémů SIEM třetích stran prostřednictvím centra událostí pro monitorování téměř v reálném čase, provádět potenciální akce a řešit nutnost vašich operací obrany.
- **DDoSMitigationReports**: sestavy o zmírnění útoku využívají data protokolu NetFlow, která jsou agregovaná a poskytují podrobné informace o útoku na prostředek. V případě útoku na veřejné IP prostředky začne generování sestav hned po zahájení zmírnění. Pro celé období zmírnění rizika bude vygenerována přírůstková zpráva každých 5 minut a hlášení o zmírňování po zmírnění. K tomu je potřeba zajistit, aby v případě, že útok DDoS trvá delší dobu, budete moct zobrazit nejaktuálnější snímek sestavy o zmírnění každých 5 minut a kompletní souhrn po překročení omezení útoku. 
- **AllMetrics**: poskytuje všechny možné metriky dostupné během doby trvání útoku DDoS. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nakonfigurujte diagnostické protokoly DDoS, včetně oznámení, zpráv o zmírnění rizik a protokolů toku. 
> * Povolí protokolování diagnostiky pro všechny veřejné IP adresy v definovaném oboru.
> * Zobrazení dat protokolu v sešitech.

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
- Než budete moct dokončit kroky v tomto kurzu, musíte nejdřív vytvořit [plán Azure DDoS Standard](manage-ddos-protection.md) a na virtuální síti musí být povolený DDoS Protection Standard.
- DDoS sleduje veřejné IP adresy přiřazené k prostředkům v rámci virtuální sítě. Pokud ve virtuální síti nemáte žádné prostředky s veřejnými IP adresami, musíte nejprve vytvořit prostředek s veřejnou IP adresou. Pro [služby Azure](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (včetně nástrojů pro vyrovnávání zatížení Azure, kde jsou virtuální počítače back-end ve virtuální síti), s výjimkou prostředí Azure App Service můžete monitorovat veřejnou IP adresu všech prostředků nasazených prostřednictvím Správce prostředků (ne Classic). Pokud chcete pokračovat v tomto kurzu, můžete rychle vytvořit virtuální počítač s [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Linuxem](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .    

## <a name="configure-ddos-diagnostic-logs"></a>Konfigurace diagnostických protokolů DDoS

Pokud chcete automaticky povolit protokolování diagnostiky u všech veřejných IP adres v rámci prostředí, přeskočte na [Povolit protokolování diagnostiky u všech veřejných IP adres](#enable-diagnostic-logging-on-all-public-ips).

1. V levé horní části portálu vyberte **Všechny služby**.
2. Do pole **Filtr** zadejte *Monitor*. Jakmile se ve výsledcích zobrazí služba **Monitor**, vyberte ji.
3. V části **Nastavení** vyberte **Nastavení diagnostiky**.
4. Vyberte **předplatné** a **skupinu prostředků** obsahující veřejnou IP adresu, kterou chcete protokolovat.
5. Vyberte možnost **Veřejná IP adresa** pro **typ prostředku** a pak vyberte konkrétní veřejnou IP adresu, pro kterou chcete povolit protokoly.
6. Vyberte **Přidat nastavení diagnostiky**. V části **Podrobnosti o kategorii** vyberte libovolný počet požadovaných možností a pak vyberte **Uložit**.

    ![DDoS nastavení diagnostiky](./media/ddos-attack-telemetry/ddos-diagnostic-settings.png)

7. V části **Podrobnosti o cíli** vyberte tolik z následujících možností, kolik budete potřebovat:

    - **Archivace do účtu úložiště**: data se zapisují do účtu Azure Storage. Další informace o této možnosti najdete v tématu [archivní protokoly prostředků](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage).
    - **Streamování do centra událostí**: umožňuje přijímači protokolu vybírat protokoly pomocí centra událostí Azure. Centra událostí umožňují integraci s Splunk nebo jinými systémy SIEM. Další informace o této možnosti najdete v tématu [streamování protokolů prostředků do centra událostí](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs).
    - **Odeslat do Log Analytics**: zapisuje protokoly do služby Azure monitor. Další informace o této možnosti najdete v tématu [shromáždění protokolů pro použití v protokolech Azure monitor](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-log-analytics-workspace).

### <a name="log-schemas"></a>Schémata protokolů

V následující tabulce jsou uvedené názvy a popisy polí:

# <a name="ddosprotectionnotifications"></a>[DDoSProtectionNotifications](#tab/DDoSProtectionNotifications)

| Název pole | Description |
| --- | --- |
| **TimeGenerated** | Datum a čas ve standardu UTC, kdy bylo oznámení vytvořeno. |
| **ResourceId** | ID prostředku vaší veřejné IP adresy. |
| **Kategorie** | Pro oznámení bude to `DDoSProtectionNotifications` .|
| **ResourceGroup** | Skupina prostředků, která obsahuje vaši veřejnou IP adresu a virtuální síť. |
| **SubscriptionId** | ID předplatného plánu DDoS Protection. |
| **Prostředek** | Název vaší veřejné IP adresy. |
| **ResourceType** | Tato akce bude vždycky `PUBLICIPADDRESS` . |
| **OperationName** | Pro oznámení bude to `DDoSProtectionNotifications` .  |
| **Zpráva** | Podrobnosti útoku |
| **Typ** | Typ oznámení Mezi možné hodnoty patří `MitigationStarted` . `MitigationStopped`. |
| **PublicIpAddress** | Vaše veřejná IP adresa. |

# <a name="ddosmitigationflowlogs"></a>[DDoSMitigationFlowLogs](#tab/DDoSMitigationFlowLogs)

| Název pole | Description |
| --- | --- |
| **TimeGenerated** | Datum a čas ve standardu UTC, kdy se protokol toku vytvořil. |
| **ResourceId** | ID prostředku vaší veřejné IP adresy. |
| **Kategorie** | V případě protokolů Flow bude to `DDoSMitigationFlowLogs` .|
| **ResourceGroup** | Skupina prostředků, která obsahuje vaši veřejnou IP adresu a virtuální síť. |
| **SubscriptionId** | ID předplatného plánu DDoS Protection. |
| **Prostředek** | Název vaší veřejné IP adresy. |
| **ResourceType** | Tato akce bude vždycky `PUBLICIPADDRESS` . |
| **OperationName** | V případě protokolů Flow bude to `DDoSMitigationFlowLogs` . |
| **Zpráva** | Podrobnosti útoku |
| **SourcePublicIpAddress** | Veřejná IP adresa klienta, který generuje provoz na veřejnou IP adresu. |
| **SourcePort** | Číslo portu od 0 do 65535. |
| **DestPublicIpAddress** | Vaše veřejná IP adresa. |
| **DestPort** | Číslo portu od 0 do 65535. |
| **Protokol** | Typ protokolu Možné hodnoty zahrnují `tcp` , `udp` , `other` .|

# <a name="ddosmitigationreports"></a>[DDoSMitigationReports](#tab/DDoSMitigationReports)

| Název pole | Description |
| --- | --- |
| **TimeGenerated** | Datum a čas ve standardu UTC, kdy se sestava vytvořila. |
| **ResourceId** | ID prostředku vaší veřejné IP adresy. |
| **Kategorie** | Pro oznámení bude to `DDoSProtectionNotifications` .|
| **ResourceGroup** | Skupina prostředků, která obsahuje vaši veřejnou IP adresu a virtuální síť. |
| **SubscriptionId** | ID předplatného plánu DDoS Protection. |
| **Prostředek** | Název vaší veřejné IP adresy. |
| **ResourceType** | Tato akce bude vždycky `PUBLICIPADDRESS` . |
| **OperationName** | V případě rizikových sestav to bude `DDoSMitigationReports` . |
| **ReportType** | Možné hodnoty zahrnují `Incremental` , `PostMitigation` .|
| **MitigationPeriodStart** | Datum a čas ve standardu UTC, kdy se toto zmírnění spustilo.  |
| **MitigationPeriodEnd** | Datum a čas ve standardu UTC, kdy bylo zmírnění dokončeno. |
| **IPAddress** | Vaše veřejná IP adresa. |
| **AttackVectors** |  Rozpis typů útoků. Mezi klíče patří `TCP SYN flood` , `TCP flood` ,, `UDP flood` `UDP reflection` , `Other packet flood` .|
| **TrafficOverview** |  Rozpis přenosů útoků. Mezi klíče patří `Total packets` , `Total packets dropped` ,, `Total TCP packets` `Total TCP packets dropped` , `Total UDP packets` , `Total UDP packets dropped` , `Total Other packets` , `Total Other packets dropped` . |
| **Protokoly** | Rozpis protokolů, které jsou součástí. Mezi klíče patří `TCP` , `UDP` , `Other` . |
| **DropReasons** | Rozpis důvodů pro vyřazené pakety. Mezi klíče patří `Protocol violation invalid TCP syn` , `Protocol violation invalid TCP` ,, `Protocol violation invalid UDP` `UDP reflection` , `TCP rate limit exceeded` , `UDP rate limit exceeded` , `Destination limit exceeded` , `Other packet flood` , `Rate limit exceeded` , `Packet was forwarded to service` . |
| **TopSourceCountries** | Rozpis hlavních 10 zdrojových zemí příchozího provozu. |
| **TopSourceCountriesForDroppedPackets** | Členění prvních 10 zdrojových zemí útoku, které se sníží nebo byly omezeny. |
| **TopSourceASNs** | Rozpis prvních 10 zdrojových autonomních systémů (ASN) příchozího provozu.  |
| **SourceContinents** | Rozpis zdrojového kontinentu příchozího provozu. |
***

## <a name="enable-diagnostic-logging-on-all-public-ips"></a>Povolit protokolování diagnostiky u všech veřejných IP adres

Tato [Šablona](https://aka.ms/ddosdiaglogs) vytvoří definici Azure Policy pro automatické povolení protokolování diagnostiky pro všechny protokoly veřejné IP adresy v definovaném oboru.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%20DDoS%20Protection%2FPolicy%20-%20DDOS%20Enable%20Diagnostic%20Logging%2FAzure%20Policy%2FDDoSLogs.json)

## <a name="view-log-data-in-workbooks"></a>Zobrazení dat protokolu v sešitech

### <a name="azure-sentinel-data-connector"></a>Datový konektor Azure Sentinel

Protokoly můžete připojit k Azure Sentinel, zobrazit a analyzovat data v sešitech, vytvářet vlastní výstrahy a začlenit je do vyšetřovacích procesů. Pokud se chcete připojit k Azure Sentinel, přečtěte si téma [připojení k Azure Sentinel](../sentinel/connect-azure-ddos-protection.md). 

![Konektor Azure Sentinel DDoS](./media/ddos-attack-telemetry/azure-sentinel-ddos.png)

### <a name="azure-ddos-protection-workbook"></a>Sešit Azure DDoS Protection

[Tuto šablonu Azure Resource Manager (ARM)](https://aka.ms/ddosworkbook) můžete použít k nasazení sešitu analýzy útoků. Tento sešit vám umožní vizualizovat data útoku napříč několika panely, které umožňují snadno pochopit, co je k dispozici. 

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%20DDoS%20Protection%2FWorkbook%20-%20Azure%20DDOS%20monitor%20workbook%2FAzureDDoSWorkbook_ARM.json)

![Sešit DDoS Protection](./media/ddos-attack-telemetry/ddos-attack-analytics-workbook.png)

## <a name="validate-and-test"></a>Ověřit a otestovat

Chcete-li simulovat útok DDoS k ověření protokolů, přečtěte si téma [ověření detekce DDoS](test-through-simulations.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

- Nakonfigurujte diagnostické protokoly DDoS, včetně oznámení, zpráv o zmírnění rizik a protokolů toku. 
- Povolí protokolování diagnostiky pro všechny veřejné IP adresy v definovaném oboru.
- Zobrazení dat protokolu v sešitech.

Pokud se chcete dozvědět, jak nakonfigurovat výstrahy útoku, přejděte k dalšímu kurzu.

> [!div class="nextstepaction"]
> [Zobrazení a konfigurace upozornění ochrany před útoky DDoS](alerts.md)
