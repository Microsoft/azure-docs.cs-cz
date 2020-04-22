---
title: Streamování dat monitorování Azure do centra událostí
description: Zjistěte, jak streamovat data monitorování Azure do centra událostí, abyste je získali do partnerského nástroje SIEM nebo analytického nástroje.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 32bc90cc069ac82641c3aa7692c900c60db7ba87
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733102"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub"></a>Streamování dat monitorování Azure do centra událostí
Azure Monitor poskytuje kompletní kompletní řešení monitorování zásobníku pro aplikace a služby v Azure, v jiných cloudech a místně. Kromě použití Azure Monitor pro analýzu dat a jejich využití pro různé scénáře monitorování, budete muset odeslat do jiných nástrojů monitorování ve vašem prostředí. Nejúčinnější metodou pro streamování dat monitorování do externích nástrojů ve většině případů je použití [Azure Event Hubs](/azure/event-hubs/). Tento článek obsahuje stručný popis, jak můžete streamovat data monitorování z různých zdrojů do centra událostí a odkazy na podrobné pokyny.


## <a name="create-an-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs

Před konfigurací streamování pro libovolný zdroj dat je třeba [vytvořit obor názvů event hubů a centrum událostí](../../event-hubs/event-hubs-create.md). Tento obor názvů a centrum událostí je cílem všech dat monitorování. Obor názvů Event Hubs je logické seskupení center událostí, které sdílejí stejné zásady přístupu, podobně jako účet úložiště má jednotlivé objekty BLOB v rámci tohoto účtu úložiště. Vezměte v úvahu následující podrobnosti o oboru názvů center centra událostí a rozbočovačích událostí, které používáte pro streamování dat monitorování:

* Počet jednotek propustnost umožňuje zvýšit propustnost škálování pro vaše centra událostí. Obvykle je nutná pouze jedna jednotka propustnosti. Pokud potřebujete vertikálně navýšit kapacitu, jak se zvyšuje využití protokolu, můžete ručně zvýšit počet jednotek propustnost pro obor názvů nebo povolit automatické nafukování.
* Počet oddílů umožňuje paralelizovat spotřebu mezi mnoha příjemci. Jeden oddíl může podporovat až 20 mb/s nebo přibližně 20 000 zpráv za sekundu. V závislosti na nástroji, který spotřebovává data, může nebo nemusí podporovat spotřebovávající z více oddílů. Čtyři oddíly je rozumné začít, pokud si nejste jisti, pokud si nejste jisti, o počtu oddílů nastavit.
* Uchovávání zpráv v centru událostí nastavíte na nejméně 7 dní. Pokud váš konzumní nástroj klesne na více než jeden den, zajistíse, že nástroj může pokračovat tam, kde skončil pro události až 7 dní staré.
* Pro centrum událostí byste měli použít výchozí skupinu spotřebitelů. Není nutné vytvářet jiné skupiny spotřebitelů nebo používat samostatnou skupinu spotřebitelů, pokud neplánujete, že dva různé nástroje budou využívat stejná data ze stejného centra událostí.
* Pro protokol aktivit Azure vyberete obor názvů Event Hubs a Azure Monitor vytvoří centrum událostí v rámci tohoto oboru názvů nazývané _insights-logs-operational-logs_. Pro jiné typy protokolů můžete buď zvolit existující centrum událostí, nebo nechat Azure Monitor vytvořit centrum událostí pro každou kategorii protokolu.
* Odchozí port 5671 a 5672 musí být obvykle otevřen v počítači nebo virtuální nenosné síti náročné data z centra událostí.

## <a name="monitoring-data-available"></a>Dostupné údaje z monitorování
[Zdroje dat monitorování pro Azure Monitor](data-sources.md) popisuje různé úrovně dat pro aplikace Azure a druhy dat monitorování, které jsou k dispozici pro každou z nich. V následující tabulce jsou uvedeny všechny tyto úrovně a popis toho, jak lze tato data streamovat do centra událostí. Další podrobnosti naleznete v odkazech.

| Úroveň | Data | Metoda |
|:---|:---|:---|
| [Tenant Azure](data-sources.md#azure-tenant) | Protokoly auditu služby Azure Active Directory | Nakonfigurujte nastavení diagnostiky klienta v tenantovi AAD. Viz [kurz: Streamujte protokoly služby Azure Active Directory do centra událostí Azure,](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) kde najdete podrobnosti. |
| [Předplatné Azure](data-sources.md#azure-subscription) | Protokol aktivit Azure | Vytvořte profil protokolu pro export událostí protokolu aktivit do centra událostí.  Podrobnosti najdete [v tématu Stream Azure platformy protokoly do Centra událostí Azure](resource-logs-stream-event-hubs.md) podrobnosti. |
| [Prostředky Azure](data-sources.md#azure-resources) | Metriky platformy<br> Protokoly prostředků |Oba typy dat jsou odesílány do centra událostí pomocí nastavení diagnostiky prostředků. Podrobnosti najdete [v tématu Stream ování protokolů prostředků Azure do centra událostí.](resource-logs-stream-event-hubs.md) |
| [Operační systém (host)](data-sources.md#operating-system-guest) | Virtuální počítače Azure | Nainstalujte [rozšíření Diagnostika Azure](diagnostics-extension-overview.md) na virtuální počítače s Windows a Linuxem v Azure. Viz [Streamování dat Azure Diagnostics v horké cestě pomocí centra událostí](diagnostics-extension-stream-event-hubs.md) pro podrobnosti o virtuálních počítačích s Windows a použití [linuxového diagnostického rozšíření ke sledování metrik a protokolů](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings) pro podrobnosti o virtuálních počítačích s Linuxem. |
| [Kód aplikace](data-sources.md#application-code) | Application Insights | Application Insights neposkytuje přímou metodu pro streamování dat do centra událostí. Můžete [nastavit nepřetržitý export](../../azure-monitor/app/export-telemetry.md) dat Application Insights do účtu úložiště a pak pomocí aplikace logiky odeslat data do centra událostí, jak je popsáno v [ručnístreamování s aplikací logiky](#manual-streaming-with-logic-app). |

## <a name="manual-streaming-with-logic-app"></a>Ruční streamování pomocí aplikace logiky
Pro data, která nelze přímo streamovat do centra událostí, můžete zapisovat do úložiště Azure a pak použít časově spouštěnou aplikaci [logiky, která natáká data z úložiště objektů blob](../../connectors/connectors-create-api-azureblobstorage.md#add-action) a [odesílá je jako zprávu do centra událostí](../../connectors/connectors-create-api-azure-event-hubs.md#add-action). 


## <a name="partner-tools-with-azure-monitor-integration"></a>Partnerské nástroje s integrací Azure Monitoru

Směrování dat monitorování do centra událostí pomocí Azure Monitoru umožňuje snadnou integraci s externími nástroji SIEM a monitorovacími nástroji. Příklady nástrojů s integrací Azure Monitoru zahrnují následující:

| Nástroj | Hostované v Azure | Popis |
|:---|:---| :---|
|  IBM QRadar | Ne | Microsoft Azure DSM a Microsoft Azure Event Hub Protocol jsou k dispozici ke stažení [na webu podpory IBM](https://www.ibm.com/support). Další informace o integraci s Azure najdete v [konfiguraci QRadar DSM](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0). |
| Splunk | Ne | [Doplněk Azure Monitor pro Splunk](https://splunkbase.splunk.com/app/3534/) je open source projekt dostupný v Splunkbase. Dokumentace je k dispozici na [Azure Monitor Addon Pro Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk).<br><br> Pokud nemůžete nainstalovat doplněk v instanci Splunk, pokud například používáte proxy server nebo běží na Splunk Cloud, můžete předat tyto události splunk HTTP event collector pomocí [funkce Azure pro Splunk](https://github.com/Microsoft/AzureFunctionforSplunkVS), který je spuštěn novými zprávami v centru událostí. |
| SumoLogic | Ne | Pokyny pro nastavení SumoLogic využívat data z centra událostí jsou k dispozici na [sbírat protokoly pro Azure Audit App z Centra událostí](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub). |
| ArcSight | Ne | Inteligentní konektor ArcSight Azure Event Hub je k dispozici jako součást [kolekce inteligentních konektorů ArcSight](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852). |
| Server syslogu | Ne | Pokud chcete streamovat data Azure Monitor přímo na server syslogu, můžete použít [řešení založené na funkci Azure](https://github.com/miguelangelopereira/azuremonitor2syslog/).
| LogRytmus | Ne| Pokyny k nastavení LogRhythm pro sběr protokolů z centra událostí jsou k dispozici [zde](https://logrhythm.com/six-tips-for-securing-your-azure-cloud-environment/). 
|Logz.io | Ano | Další informace najdete [v tématu Začínáme s monitorováním a protokolováním pomocí Logz.io pro java aplikace spuštěné v Azure.](https://docs.microsoft.com/azure/developer/java/fundamentals/java-get-started-with-logzio)


## <a name="next-steps"></a>Další kroky
* [Archivovat protokol aktivit na účet úložiště](../../azure-monitor/platform/archive-activity-log.md)
* [Přečtěte si přehled protokolu aktivit Azure](../../azure-monitor/platform/platform-logs-overview.md)
* [Nastavení výstrahy na základě události protokolu aktivit](../../azure-monitor/platform/alerts-log-webhook.md)


