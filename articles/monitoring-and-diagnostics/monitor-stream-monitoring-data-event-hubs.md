---
title: Azure Stream monitorování dat do služby Event Hubs
description: Zjistěte, jak pro všemi daty Azure monitorování do centra událostí k získání dat do partnerský server SIEM nebo nástroj pro analýzu streamování.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 7/06/2018
ms.author: johnkem
ms.component: ''
ms.openlocfilehash: 5e8d8947643494e06faaabb5335c52df5908303e
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2018
ms.locfileid: "37902985"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-for-consumption-by-an-external-tool"></a>Pomocí externího nástroje pro monitorování data do centra událostí pro používání Azure Stream

Platforma Azure Monitor poskytuje jeden kanál pro získání přístupu ke všem data monitorování z vašeho prostředí Azure, umožňuje snadno nastavit partnera SIEM a monitorovací nástroje pro tato data zpracovat. Tento článek vás provede nastavením různé úrovně dat z vašeho prostředí Azure k odeslání pro jedno služby Event Hubs oboru názvů nebo událost centrum, kde může být shromažďovány externího nástroje.

## <a name="what-data-can-i-send-into-an-event-hub"></a>Jaká data můžete odesílat do centra událostí? 

V rámci prostředí Azure existuje několik "vrstvy" dat monitorování a způsob přístupu k datům z každé vrstvy se mírně liší. Obvykle tyto vrstvy lze popsat jako:

- **Monitorování dat aplikací:** Data o výkonu a funkce kódu sami napsali a běží v Azure. Příklady monitorování dat aplikací: trasování výkonu a protokoly aplikací a telemetrii uživatelů. Monitorování dat aplikace se shromažďují v jednom z následujících způsobů:
  - Upravením kódu pomocí sady SDK [Application Insights SDK](../application-insights/app-insights-overview.md).
  - Spuštěním agenta monitorování, která naslouchá novou aplikaci protokoly na počítači provozování vaší aplikace, jako [Windows agenta diagnostiky Azure](./azure-diagnostics.md) nebo [agenta diagnostiky Azure Linux](../virtual-machines/linux/diagnostic-extension.md).
- **Data monitorování hostovaného operačního systému:** Data o operačním systému, na kterém je aplikace spuštěna. Příklady data monitorování hostovaného operačního systému by události systému Windows nebo protokolu syslog v Linuxu. Pokud chcete shromažďovat data tohoto typu, je potřeba nainstalovat agenta, jako [Windows agenta diagnostiky Azure](./azure-diagnostics.md) nebo [agenta diagnostiky Azure Linux](../virtual-machines/linux/diagnostic-extension.md).
- **Data monitorování prostředků Azure:** Data o provozu prostředku Azure. Pro některé typy prostředků Azure, jako jsou virtuální počítače je hostovaný operační systém a aplikace pro monitorování v rámci této služby Azure. Za další prostředky Azure, jako jsou skupiny zabezpečení sítě zdroje dat monitorování je na nejvyšší úrovni nejsou k dispozici data (protože neexistuje žádný hostovaný operační systém nebo aplikace běžící v těchto prostředků). Tato data lze shromažďovat pomocí [nastavení diagnostiky prostředků](./monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings).
- **Data monitorování platformy Azure:** Data o provozu a správy předplatného Azure nebo tenanta, jakož i informace o stavu a provoz Azure samotný. [Protokolu aktivit](./monitoring-overview-activity-logs.md), včetně data o stavu služby a služby Active Directory audity jsou příkladem platforma dat monitorování. Tato data lze shromažďovat pomocí také nastavení diagnostiky.

Data ze všech úrovních lze odesílat do centra událostí, kde lze načíst do nástroje partnera. Tento oddíl popisuje, jak nakonfigurovat data z každé vrstvy Streamovat do centra událostí. Kroky předpokládají, že už máte prostředky na dané úrovni, která se má monitorovat.

## <a name="set-up-an-event-hubs-namespace"></a>Nastavit obor názvů služby Event Hubs

Než začnete, budete muset [vytvoření Event Hubs oboru názvů a Centrum událostí](../event-hubs/event-hubs-create.md). Tento obor názvů a Centrum událostí je cílem pro všemi daty monitorování. Obor názvů služby Event Hubs je logické seskupení služby event hubs, které sdílejí stejné zásady přístupu, podobně jako úložiště má účet jednotlivých objektů BLOB v účtu úložiště. Mějte prosím na paměti několik podrobností o obor názvů služby event hubs a centra událostí, které jste vytvořili:
* Doporučujeme používat obor názvů Event Hubs úrovně Standard.
* Obvykle je nezbytné pouze jednu jednotku propustnosti. Pokud potřebujete vertikálně navýšit kapacitu jako vaše zvýšení využití protokolu, můžete vždy ručně zvýšit počet jednotek propustnosti oboru názvů později nebo povolit automatické inflaci.
* Počet jednotek propustnosti můžete zvýšit propustnost škálování pro vaši službu event hubs. Počet oddílů umožňuje paralelní zpracování využití napříč mnoha příjemci. Jeden oddíl můžete provést až 20MBps nebo přibližně 20 000 zpráv za sekundu. V závislosti na využívání dat nástroj může nebo nemusí podporovat používání z několika oddílů. Pokud si nejste jistí, počet oddílů, chcete-li nastavit, doporučujeme začít s čtyřmi oddíly.
* Doporučujeme nastavit uchovávání zpráv ve službě event hub do 7 dní. Pokud využívání nástroj ocitne mimo provoz pro více než jedním dnem, tím se zajistí, že můžete pokračovat tam, kde skončila nástroj (pro události až 7 dní).
* Doporučujeme použít výchozí skupinu příjemců centra událostí. Není nutné vytvořit další skupiny uživatelů nebo používají skupiny konzumentů samostatné, pokud bude mít dva různé nástroje využívat stejná data ze stejného centra událostí.
* Pro protokol aktivit Azure vyberte obor názvů služby Event Hubs a Azure Monitor vytvoří Centrum událostí v daném oboru názvů nazývá "insights protokoly operationallogs." Pro ostatní typy protokolů můžete buď zvolit existující centrum událostí (díky tomu umožňuje znovu použít stejné centra událostí insights. protokoly operationallogs) nebo Azure Monitor vytvořit Centrum událostí podle jednotlivých kategorií protokolu.
* Obvykle je nutné otevřít port 5671 a 5672 v počítači využívající data z centra událostí.

Podrobnosti najdete také [Azure Event Hubs – nejčastější dotazy](../event-hubs/event-hubs-faq.md).

## <a name="how-do-i-set-up-azure-platform-monitoring-data-to-be-streamed-to-an-event-hub"></a>Jak nastavit monitorování dat platformy Azure Streamovat do centra událostí?

Data monitorování platformy Azure pochází z dva hlavní zdroje:
1. [Protokolu aktivit Azure](./monitoring-overview-activity-logs.md)obsahující vytvořit, aktualizovat a operace odstranění z Resource Manageru, změny v [službě Azure service health](../service-health/service-health-overview.md) , který může mít vliv na prostředky ve vašem předplatném, [služba resource health](../service-health/resource-health-overview.md) přechodů mezi stavy a několik dalších typů událostí na úrovni předplatného. [Tento článek podrobně popisuje všechny kategorie událostí, které se zobrazují v protokolu aktivit Azure](./monitoring-activity-log-schema.md).
2. [Generování sestav v Azure Active Directory](../active-directory/active-directory-reporting-azure-portal.md), která obsahuje historii přihlašovací aktivitu a revizní záznam změn provedených v rámci konkrétního tenanta. Ještě není možné data Azure Active Directory Streamovat do centra událostí.

### <a name="stream-azure-activity-log-data-into-an-event-hub"></a>Stream data protokolu aktivit Azure do centra událostí

K odesílání dat z protokolu aktivit Azure do Event Hubs oboru názvů, můžete nastavit profil protokolu v rámci předplatného. [Tento návod](./monitoring-stream-activity-logs-event-hubs.md) nastavení profilu protokolu v rámci předplatného. To proveďte, jakmile na jedno předplatné, které chcete monitorovat.

> [!TIP]
> Profil protokolu aktuálně pouze vám umožní vybrat Event Hubs oboru názvů, ve kterém se vytvoří Centrum událostí s název "insights-operational-logs." Ještě není možné určit vlastní název centra událostí v profilu protokolu.

## <a name="how-do-i-set-up-azure-resource-monitoring-data-to-be-streamed-to-an-event-hub"></a>Jak nastavit data monitorování prostředků Azure Streamovat do centra událostí?

Prostředky Azure generují data monitorování dva typy:
1. [Protokolů diagnostiky prostředků](./monitoring-overview-of-diagnostic-logs.md)
2. [Metriky](monitoring-overview-metrics.md)

Oba typy dat se posílají do centra událostí pomocí nastavení diagnostiky prostředku. [Tento návod](./monitoring-stream-diagnostic-logs-to-event-hubs.md) nastavit nastavení diagnostiky prostředků u určitého prostředku. Nastaví nastavení diagnostiky prostředků u jednotlivých prostředků, ze kterého chcete shromažďovat protokoly.

> [!TIP]
> Azure Policy můžete použít k zajištění, že každý prostředek v rámci určitého oboru vždy nastavit pomocí nastavení diagnostiky [pomocí DeployIfNotExists efekt v pravidlu zásad](../azure-policy/policy-definition.md#policy-rule). Dnes DeployIfNotExists je podporována pouze na předdefinovaných zásadách.

## <a name="how-do-i-set-up-guest-os-monitoring-data-to-be-streamed-to-an-event-hub"></a>Jak nastavit data monitorování hostovaného operačního systému Streamovat do centra událostí?

Je potřeba nainstalovat agenta k odeslání dat monitorování operačního systému hosta do centra událostí. Pro Windows nebo Linux zadejte data, která má k odeslání do centra událostí, jakož i Centrum událostí, ke kterému data, by měly být odeslány v konfiguračním souboru a předat tento konfigurační soubor agenta spuštěného na virtuálním počítači.

### <a name="stream-linux-data-to-an-event-hub"></a>Stream dat s Linuxem do centra událostí

[Agenta diagnostiky Azure Linux](../virtual-machines/extensions/diagnostics-linux.md) je možné odesílat data monitorování z počítače s Linuxem do centra událostí. To proveďte přidáním centra událostí jako jímka v vaše LAD chráněný soubor nastavení JSON. [Najdete v tomto článku se dozvíte informace o přidávání jímky událostí centra diagnostiky Azure Linux agent](../virtual-machines/extensions/diagnostics-linux.md#protected-settings).

> [!NOTE]
> Nejde nastavit vysílání datového proudu data monitorování hostovaného operačního systému do centra událostí na portálu. Místo toho musíte ručně upravit konfigurační soubor.

### <a name="stream-windows-data-to-an-event-hub"></a>Stream Windows data do centra událostí

[Agenta diagnostiky Azure Windows](./azure-diagnostics.md) je možné odesílat data monitorování z počítače s Windows do centra událostí. To lze proveďte přidáním centra událostí jako jímka v části privateConfig WAD konfiguračního souboru. [Najdete v tomto článku se dozvíte informace o přidávání jímky centra událostí pro Windows Azure diagnostiky agenta](./azure-diagnostics-streaming-event-hubs.md).

> [!NOTE]
> Nejde nastavit vysílání datového proudu data monitorování hostovaného operačního systému do centra událostí na portálu. Místo toho musíte ručně upravit konfigurační soubor.

## <a name="how-do-i-set-up-application-monitoring-data-to-be-streamed-to-event-hub"></a>Jak nastavit aplikaci data monitorování se streamují do centra událostí?

Monitorování dat aplikací vyžaduje, že váš kód je instrumentovaná pomocí sady SDK, proto není k dispozici pro obecné účely řešení, které směrování application monitoring data do centra událostí v Azure. Ale [Azure Application Insights](../application-insights/app-insights-overview.md) je jedna služba, která slouží ke shromažďování dat Azure úrovni aplikace. Pokud používáte Application Insights, můžete Streamovat data monitorování do centra událostí následujícím způsobem:

1. [Nastavte si průběžný export](../application-insights/app-insights-export-telemetry.md) dat Application Insights do účtu úložiště.

2. Nastavit aplikaci logiky aktivované pomocí časovače, který [si vyžádá data z úložiště objektů blob](../connectors/connectors-create-api-azureblobstorage.md#add-action) a [nasdílí jako zprávy do centra událostí](../connectors/connectors-create-api-azure-event-hubs.md#add-action).

## <a name="what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub"></a>Co můžete dělat s monitorování dat odesílaných do mé centra událostí?

Směrování dat monitorování do centra událostí pomocí Azure Monitor umožňuje snadnou integraci s partnerem SIEM a monitorovací nástroje. Většina nástrojů vyžaduje určitá oprávnění k předplatnému Azure číst data z centra událostí a připojovací řetězec centra událostí. Tady je seznam není vyčerpávající nástrojů se službou Azure Monitor integrace:

* **IBM QRadar** – The Microsoft Azure DSM a Microsoft Azure Event Hub protokolu jsou k dispozici ke stažení z [webu podpory IBM](http://www.ibm.com/support). Je možné [Další informace o integraci s Azure zde](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0).
* **Splunk** – v závislosti na nastavení Splunk existují dvě metody:
    1. [Monitorování rozšíření Azure umožňující Splunk](https://splunkbase.splunk.com/app/3534/) je k dispozici v Splunkbase a projekt open source. [Tady je dokumentace](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk).
    2. Pokud doplněk nemůžete nainstalovat ve vaší instanci Splunk (např.) Pokud používáte proxy server nebo v cloudu Splunk spuštěna), můžete tyto události do protokolu událostí Splunk HTTP pomocí přeposílat [tuto funkci, která se spustí pomocí nové zprávy do centra událostí](https://github.com/Microsoft/AzureFunctionforSplunkVS).
* **SumoLogic** – pokyny pro nastavení SumoLogic využívají data z centra událostí jsou [tady k dispozici](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub)

## <a name="next-steps"></a>Další kroky
* [Archivace protokolu aktivit do účtu úložiště](monitoring-archive-activity-log.md)
* [Přečtěte si přehled protokolu aktivit Azure](monitoring-overview-activity-logs.md)
* [Nastavte si výstrahy na základě události protokolu aktivit](insights-auditlog-to-webhook-email.md)

