---
title: Streamování dat monitorování Azure do centra událostí a externích partnerů
description: Naučte se streamovat data monitorování Azure do centra událostí a získat tak data do služby partner SIEM nebo Analytics Tool.
services: azure-monitor
author: bwren
ms.author: bwren
ms.topic: conceptual
ms.date: 07/15/2020
ms.openlocfilehash: a929563df3e7e98575056d07519abfda0d6ac13b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102032955"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-or-external-partner"></a>Streamování dat monitorování Azure do centra událostí nebo externího partnera

Azure Monitor poskytuje kompletní řešení monitorování zásobníku pro aplikace a služby v Azure, v jiných cloudech a v místním prostředí. Kromě použití Azure Monitor k analýze těchto dat a jejich využití v různých scénářích monitorování ho možná budete muset poslat ostatním nástrojům pro monitorování ve vašem prostředí. Ve většině případů nejúčinnější metoda pro streamování dat monitorování do externích nástrojů používá službu [Azure Event Hubs](../../event-hubs/index.yml). Tento článek obsahuje stručný popis toho, jak to provést, a pak uvádí některé z partnerů, na které můžete odesílat data. Některé mají zvláštní integraci se Azure Monitor a můžou být hostované v Azure.  

## <a name="create-an-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs

Předtím, než nakonfigurujete streamování pro libovolný zdroj dat, je potřeba [vytvořit obor názvů Event Hubs a centrum událostí](../../event-hubs/event-hubs-create.md). Tento obor názvů a centrum událostí slouží jako cíl pro všechna data monitorování. Event Hubs obor názvů je logické seskupení Center událostí, která sdílejí stejné zásady přístupu, podobně jako účet úložiště má jednotlivé objekty BLOB v rámci tohoto účtu úložiště. Vezměte v úvahu následující podrobnosti o oboru názvů centra událostí a centrech událostí, které používáte pro streamování dat monitorování:

* Počet jednotek propustnosti umožňuje zvýšit míru propustnosti pro centra událostí. Obvykle je nutná pouze jedna jednotka propustnosti. Pokud potřebujete horizontální navýšení kapacity při zvyšování využití protokolu, můžete ručně zvýšit počet jednotek propustnosti pro obor názvů nebo povolit automatické inflace.
* Počet oddílů vám umožní paralelizovat spotřebu napříč mnoha spotřebiteli. Jeden oddíl může podporovat až 20MBps nebo přibližně 20 000 zpráv za sekundu. V závislosti na nástroji, který data spotřebovává, může nebo nemusí podporovat využívání více oddílů. Pokud si nejste jistí, kolik oddílů se má nastavit, je vhodné začít se čtyřmi oddíly.
* V centru událostí nastavili uchovávání zpráv na alespoň 7 dní. Pokud váš spotřebováváný nástroj přestane trvat déle než jeden den, zajistí to, aby se nástroj mohl vystavit tam, kde byl pro události až do 7 dnů starý.
* Pro centrum událostí byste měli použít výchozí skupinu uživatelů. Není nutné vytvářet další skupiny uživatelů ani používat samostatnou skupinu uživatelů, pokud neplánujete, aby se stejná data ze stejného centra událostí využila dvěma různými nástroji.
* V případě protokolu aktivit Azure vybíráte obor názvů Event Hubs a Azure Monitor vytvoří centrum událostí v oboru názvů s názvem _Insights-logs-Operational-logs_. U jiných typů protokolů můžete zvolit existující centrum událostí nebo Azure Monitor vytvořit centrum událostí podle kategorií protokolů.
* Odchozí porty 5671 a 5672 musí být obvykle otevřeny v počítači nebo virtuální síti, která spotřebovává data z centra událostí.

## <a name="monitoring-data-available"></a>Data monitorování k dispozici
[Zdroje dat monitorování pro Azure monitor](../agents/data-sources.md) popisují různé úrovně dat pro aplikace Azure a typy dat monitorování dostupných pro každé z nich. Následující tabulka uvádí každou z těchto úrovní a popis způsobu, jakým můžou být tato data streamovaná do centra událostí. Další podrobnosti najdete na odkazech uvedených níže.

| Úroveň | Data | Metoda |
|:---|:---|:---|
| [Tenant Azure](../agents/data-sources.md#azure-tenant) | Protokoly auditu Azure Active Directory | Nakonfigurujte nastavení diagnostiky tenanta v tenantovi AAD. Podrobnosti najdete v tématu  [kurz: Stream Azure Active Directory protokoly do centra událostí Azure](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) . |
| [Předplatné Azure](../agents/data-sources.md#azure-subscription) | Protokol aktivit Azure | Vytvořte profil protokolu pro export událostí protokolu aktivit do Event Hubs.  Podrobnosti najdete v tématu [streamování protokolů platformy Azure do azure Event Hubs](../essentials/resource-logs.md#send-to-azure-event-hubs) . |
| [Prostředky Azure](../agents/data-sources.md#azure-resources) | Metriky platformy<br> Protokoly prostředků |Oba typy dat se odesílají do centra událostí pomocí nastavení diagnostiky prostředků. Podrobnosti najdete v tématu [streamování protokolů prostředků Azure do centra událostí](../essentials/resource-logs.md#send-to-azure-event-hubs) . |
| [Operační systém (host)](../agents/data-sources.md#operating-system-guest) | Virtuální počítače Azure | Nainstalujte [Azure Diagnostics rozšíření](../agents/diagnostics-extension-overview.md) na virtuální počítače s Windows a Linux v Azure. Podrobnosti o virtuálních počítačích se systémem Windows najdete [v tématu streamování Azure Diagnostics data v místní cestě pomocí Event Hubs](../agents/diagnostics-extension-stream-event-hubs.md) . [k monitorování metrik a protokolů použijte diagnostické rozšíření](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings) pro Linux. |
| [Kód aplikace](../agents/data-sources.md#application-code) | Application Insights | Application Insights neposkytuje přímou metodu pro streamování dat do Center událostí. Můžete [nastavit průběžný export](../app/export-telemetry.md) dat Application Insights do účtu úložiště a potom pomocí aplikace logiky odesílat data do centra událostí, jak je popsáno v tématu [Ruční streamování pomocí aplikace logiky](#manual-streaming-with-logic-app). |

## <a name="manual-streaming-with-logic-app"></a>Ruční streamování s využitím aplikace logiky
Data, která nemůžete přímo zasílat do centra událostí, můžete zapisovat do služby Azure Storage a potom použít aplikaci logiky aktivované časovým obdobím, která načte [data z úložiště objektů BLOB](../../connectors/connectors-create-api-azureblobstorage.md#add-action) a [pošle ji jako zprávu do centra událostí](../../connectors/connectors-create-api-azure-event-hubs.md#add-action). 


## <a name="partner-tools-with-azure-monitor-integration"></a>Partnerské nástroje s integrací Azure Monitor

Směrování dat monitorování do centra událostí pomocí Azure Monitor vám umožní snadnou integraci s externími nástroji pro SIEM a monitorování. Mezi příklady nástrojů s Azure Monitor integrací patří následující:

| Nástroj | Hostovaná v Azure | Description |
|:---|:---| :---|
|  IBM QRadar | No | Protokol Microsoft Azure DSM a Microsoft Azure centra událostí je k dispozici ke stažení na [webu podpory IBM](https://www.ibm.com/support). Další informace o integraci s Azure najdete v části [QRADAR DSM Configuration](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0). |
| Splunk | No | [Microsoft Azure Add-On pro Splunk](https://splunkbase.splunk.com/app/3757/) je open source projekt dostupný v Splunkbase. <br><br> Pokud do instance Splunk nemůžete nainstalovat doplněk, pokud například používáte proxy server nebo běží v cloudu Splunk, můžete tyto události pře do kolektoru událostí Splunk HTTP pomocí [funkce Azure Functions pro Splunk](https://github.com/Microsoft/AzureFunctionforSplunkVS), která se aktivuje novými zprávami v centru událostí. |
| SumoLogic | No | Pokyny k nastavení SumoLogic pro využívání dat z centra událostí jsou k dispozici v tématu [shromáždění protokolů pro aplikaci Azure audit z centra událostí](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub). |
| ArcSight | No | ArcSight Azure Event hub Smart Connector je k dispozici jako součást [kolekce inteligentních konektorů ArcSight](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852). |
| Server syslogu | No | Pokud chcete streamovat data Azure Monitor přímo na server syslog, můžete použít [řešení založené na funkci Azure Functions](https://github.com/miguelangelopereira/azuremonitor2syslog/).
| LogRhythm | No| Pokyny k nastavení LogRhythm pro shromažďování protokolů z centra událostí jsou k dispozici [zde](https://logrhythm.com/six-tips-for-securing-your-azure-cloud-environment/). 
|Logz.io | Yes | Další informace najdete v tématu [Začínáme s monitorováním a protokolováním pomocí LOGZ.IO pro aplikace Java běžící v Azure](/azure/developer/java/fundamentals/java-get-started-with-logzio) .

Další partneři mohou být také k dispozici. Úplnější seznam všech partnerů Azure Monitor a jejich schopností najdete v tématu [Azure monitor integrace partnerů](../partners.md).

## <a name="next-steps"></a>Další kroky
* [Archivace protokolu aktivit do účtu úložiště](./activity-log.md#legacy-collection-methods)
* [Přečtěte si přehled protokolu aktivit Azure.](../essentials/platform-logs-overview.md)
* [Nastavení výstrahy na základě události protokolu aktivit](../alerts/alerts-log-webhook.md)