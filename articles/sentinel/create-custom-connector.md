---
title: Prostředky pro vytváření vlastních konektorů služby Azure Sentinel | Microsoft Docs
description: Přečtěte si o dostupných prostředcích pro vytváření vlastních konektorů pro službu Azure Sentinel. Mezi metody patří agent Log Analytics a rozhraní API, Logstash, Logic Apps, PowerShell a Azure Functions.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2021
ms.author: bagol
ms.openlocfilehash: 25f83088bdc55dbafe7ccf0ff06b0c6595c9ea71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101724349"
---
# <a name="resources-for-creating-azure-sentinel-custom-connectors"></a>Prostředky pro vytváření vlastních konektorů Sentinel Azure

Služba Azure Sentinel poskytuje široké spektrum [integrovaných konektorů pro služby Azure a externí řešení](connect-data-sources.md)a také podporuje příjem dat z některých zdrojů bez vyhrazeného konektoru.

Pokud se zdroji dat nemůžete připojit ke službě Azure Sentinel pomocí některého z dostupných stávajících řešení, zvažte vytvoření vlastního konektoru zdroje dat.

Úplný seznam podporovaných konektorů najdete v příspěvku na blogu [Azure Sentinel (CEF, syslog, Direct, agent, Custom atd.)](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-the-connectors-grand-cef-syslog-direct-agent/ba-p/803891) .

## <a name="compare-custom-connector-methods"></a>Porovnání metod vlastních konektorů

Následující tabulka porovnává základní podrobnosti o jednotlivých metodách pro vytváření vlastních konektorů popsaných v tomto článku. Kliknutím na odkazy v tabulce zobrazíte další podrobnosti o jednotlivých metodách.

|Popis metody  |Schopnost | Bez serveru    |Složitost  |
|---------|---------|---------|---------|
|**[Agent Log Analytics](#connect-with-the-log-analytics-agent)** <br>Nejlepší pro shromažďování souborů z místních a IaaS zdrojů   | Pouze kolekce souborů  |   No      |Nízká         |
|**[Logstash](#connect-with-logstash)** <br>Nejlepší pro místní a IaaS zdroje, jakýkoli zdroj, pro který je k dispozici modul plug-in a organizace, které jsou už obeznámené s Logstash  | Dostupné moduly plug-in a vlastní modul plug-in poskytují značnou flexibilitu.   |   Žádné vyžaduje spuštění virtuálního počítače nebo clusteru virtuálních počítačů.           |   Slab podporuje mnoho scénářů s moduly plug-in      |
|**[Logic Apps](#connect-with-logic-apps)** <br>Vysoké náklady; Nepoužívejte pro data s vysokým objemem dat <br>Nejlepší pro cloudové zdroje s nízkým objemem  | Programování bez kódu umožňuje omezená flexibilitu bez podpory implementace algoritmů.<br><br> Pokud vaše požadavky už žádné dostupné akce nepodporují, může vytvoření vlastní akce přidat složitost.    |    Yes         |   Slab jednoduchý a bezkódový vývoj      |
|**[PowerShell](#connect-with-powershell)** <br>Nejlepší pro vytváření prototypů a periodické nahrávání souborů | Přímá podpora pro kolekci souborů. <br><br>PowerShell se dá použít ke shromažďování dalších zdrojů, ale vyžaduje kódování a konfiguraci skriptu jako služby.      |No               |  Nízká       |
|**[Rozhraní API pro Log Analytics](#connect-with-the-log-analytics-api)** <br>Nejlepší pro implementaci integrace nezávislého výrobce softwaru a pro jedinečné požadavky na kolekci   | Podporuje všechny možnosti, které jsou k dispozici v kódu.  | Závisí na implementaci           |     Vysoká    |
|**[Azure Functions](#connect-with-azure-functions)** Nejvhodnější pro zdroje cloudu s vysokým objemem a pro jedinečné požadavky na kolekci  | Podporuje všechny možnosti, které jsou k dispozici v kódu.  |  Yes             |     Maximální vyžaduje znalosti programování.    |
|     |         |                |

> [!TIP]
> Porovnání použití Logic Apps a Azure Functions pro stejný konektor naleznete zde:
> 
> - [Rychlé ingestování protokolů firewallu webových aplikací do Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-fastly-web-application-firewall-logs-into-azure-sentinel/ba-p/1238804)
> - Office 365 (komunita GitHubu pro Azure Sentinel): konektor [Aplikace logiky](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Get-O365Data)  |  [Azure Functions](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data)
> 

## <a name="connect-with-the-log-analytics-agent"></a>Připojit pomocí agenta Log Analytics

Pokud váš zdroj dat doručuje události do souborů, doporučujeme, abyste k vytvoření vlastního konektoru použili agenta Azure Monitor Log Analytics.

- Další informace najdete v tématu [shromažďování vlastních protokolů v Azure monitor](../azure-monitor/agents/data-sources-custom-logs.md).

- Příklad této metody najdete [v tématu shromažďování vlastních zdrojů dat JSON s agentem Log Analytics pro Linux v Azure monitor](../azure-monitor/agents/data-sources-json.md).

## <a name="connect-with-logstash"></a>Připojení pomocí Logstash

Pokud jste obeznámeni s [Logstash](https://www.elastic.co/logstash), možná budete chtít použít Logstash s [modulem plug-in Logstash Output pro Azure Sentinel](connect-logstash.md) a vytvořit vlastní konektor.

Pomocí modulu plug-in Azure Sentinel Logstash můžete používat libovolné moduly plug-in Logstash Input a Filtering a jako výstup pro kanál Logstash nakonfigurovat Azure Sentinel. Logstash má rozsáhlou knihovnu modulů plug-in, které umožňují vstup z různých zdrojů, jako jsou Event Hubs, Apache Kafka, soubory, databáze a cloudové služby. Použijte filtrování modulů plug-in k analýze událostí, filtrování zbytečných událostí, zazmatení hodnot a dalších.

Příklady použití Logstash jako vlastního konektoru najdete v tématech:

- [Lov po jedno porušení TTPs v protokolech AWS pomocí služby Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/hunting-for-capital-one-breach-ttps-in-aws-logs-using-azure/ba-p/1019767) (blog)
- [Průvodce implementací Azure Sentinel Radware](https://support.radware.com/ci/okcsFattach/get/1025459_3)

Příklady užitečných modulů plug-in Logstash najdete v těchto tématech:

- [Vstupní modul plug-in CloudWatch](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-cloudwatch.html)
- [Modul plug-in Azure Event Hubs](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-azure_event_hubs.html)
- [Vstupní modul plug-in úložiště Google Cloud](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_cloud_storage.html)
- [Vstupní modul plug-in Google_pubsub](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_pubsub.html)

> [!TIP]
> Logstash také umožňuje shromažďování dat škálované pomocí clusteru. Další informace najdete v tématu [použití virtuálního počítače s Logstash s vyrovnáváním zatížení ve velkém měřítku](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854).
>

## <a name="connect-with-logic-apps"></a>Připojení pomocí Logic Apps

Pomocí [Aplikace logiky Azure](../logic-apps/index.yml) můžete vytvořit vlastní konektor pro službu Azure Sentinel bez serveru.

> [!NOTE]
> Při vytváření konektorů bez serveru s využitím Logic Apps může být užitečné, použití Logic Apps pro vaše konektory může být nákladné pro velké objemy dat.
>
> Tuto metodu doporučujeme použít jenom pro zdroje dat s nízkým objemem nebo rozšíření pro nahrávání dat.
>

1. **Pomocí jedné z následujících triggerů spusťte Logic Apps**:

    |Trigger  |Description  |
    |---------|---------|
    |**Opakovaný úkol**     |   Můžete například naplánovat, aby aplikace logiky načetla data pravidelně ze specifických souborů, databází nebo externích rozhraní API. <br>Další informace najdete v tématu [vytváření, plánování a spouštění opakujících se úloh a pracovních postupů v Azure Logic Apps](../connectors/connectors-native-recurrence.md).      |
    |**Aktivace na vyžádání**     | Spusťte aplikaci logiky na vyžádání pro ruční shromažďování a testování dat. <br>Další informace najdete v tématu  [volání, triggery nebo vnořené aplikace logiky pomocí koncových bodů https](../logic-apps/logic-apps-http-endpoint.md).        |
    |**Koncový bod HTTP/S**     |  Doporučuje se pro streamování a pokud zdrojový systém může spustit přenos dat. <br>Další informace najdete v tématu [koncové body služby volání přes protokol HTTP nebo https](../connectors/connectors-native-http.md).       |
    |     |         |

1. **K získání událostí použijte libovolný konektor aplikace logiky, který čte informace**. Například:

    - [Připojení k REST API](/connectors/custom-connectors/)
    - [Připojení k SQL Server](/connectors/sql/)
    - [Připojení k systému souborů](/connectors/filesystem/)

    > [!TIP]
    > Vlastní konektory pro rozhraní REST API, servery SQL a systémy souborů podporují také načítání dat z místních zdrojů dat. Další informace najdete v tématu Instalace místní dokumentace pro [bránu dat](/connectors/filesystem/) .
    >

1. **Připravte informace, které chcete načíst**.

    Například použijte [akci analyzovat JSON](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) pro přístup k vlastnostem v obsahu JSON, který vám umožní vybrat tyto vlastnosti ze seznamu dynamického obsahu při zadávání vstupů pro vaši aplikaci logiky.

    Další informace najdete v tématu [provádění operací s daty v Azure Logic Apps](../logic-apps/logic-apps-perform-data-operations.md).

1. **Zapište data do Log Analytics**.

    Další informace najdete v dokumentaci ke [službě Azure Log Analytics data collector](/connectors/azureloganalyticsdatacollector/) .

Příklady, jak můžete vytvořit vlastní konektor pro službu Azure Sentinel pomocí Logic Apps, najdete v těchto tématech:

- [Vytvoření datového kanálu pomocí rozhraní API kolekce dat](/connectors/azureloganalyticsdatacollector/)
- [Palo Alto Prisma Logic App Connector s použitím Webhooku](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Ingest-Prisma) (komunita Azure Sentinel GitHub)
- [Zabezpečení volání Microsoft Teams pomocí plánované aktivace](https://techcommunity.microsoft.com/t5/azure-sentinel/secure-your-calls-monitoring-microsoft-teams-callrecords/ba-p/1574600) (blog)
- Ingestování [indikátorů hrozeb ALIENVAULT otx do Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-alien-vault-otx-threat-indicators-into-azure-sentinel/ba-p/1086566) (blog)
- [Odesílání protokolů PROOFPOINT klepněte na službu Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/sending-proofpoint-tap-logs-to-azure-sentinel/ba-p/767727) (blog)


## <a name="connect-with-powershell"></a>Připojení přes PowerShell

[Skript PowerShellu pro nahrání AzMonitorLog](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) umožňuje použít PowerShell ke streamování událostí nebo kontextových informací do Azure Sentinel z příkazového řádku. Tento Stream efektivně vytvoří vlastní konektor mezi zdrojem dat a službou Azure Sentinel.

Například následující skript nahraje soubor CSV do Azure Sentinel:

``` PowerShell
Import-Csv .\testcsv.csv
| .\Upload-AzMonitorLog.ps1
-WorkspaceId '69f7ec3e-cae3-458d-b4ea-6975385-6e426'
-WorkspaceKey $WSKey
-LogTypeName 'MyNewCSV'
-AddComputerName
-AdditionalDataTaggingName "MyAdditionalField"
-AdditionalDataTaggingValue "Foo"
```

Skript [skriptu PowerShellu pro nahrání AzMonitorLog](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) používá následující parametry:

|Parametr  |Popis  |
|---------|---------|
|**ID pracovního prostoru**     |   Vaše ID pracovního prostoru Azure Sentinel, kam budete ukládat svá data.  [Vyhledejte ID a klíč vašeho pracovního prostoru](#find-your-workspace-id-and-key).  |
|**WorkspaceKey**     |   Primární nebo sekundární klíč pro pracovní prostor Azure Sentinel, kam budete ukládat data. [Vyhledejte ID a klíč vašeho pracovního prostoru](#find-your-workspace-id-and-key).  |
|**LogTypeName**     |    Název vlastní tabulky protokolu, do které chcete ukládat data. Přípona **_CL** bude automaticky přidána na konec názvu tabulky.  |
|**AddComputerName**     |   Pokud tento parametr existuje, skript přidá aktuální název počítače do každého záznamu protokolu v poli s názvem **Computer**.      |
|**TaggedAzureResourceId**     | Pokud tento parametr existuje, připojí skript všechny záznamy odeslaných protokolů se zadaným prostředkem Azure. <br><br>Toto přidružení umožňuje nahrané záznamy protokolů pro dotazy v kontextu prostředků a řídí řízení přístupu na základě role, které je zaměřené na role.       |
|**AdditionalDataTaggingName**     |      Pokud tento parametr existuje, skript přidá do každého záznamu protokolu další pole s nakonfigurovaným názvem a hodnotou, která je nakonfigurovaná pro parametr **AdditionalDataTaggingValue** . <br><br>V takovém případě nesmí být **AdditionalDataTaggingValue** prázdné. |
|**AdditionalDataTaggingValue**     |   Pokud tento parametr existuje, skript přidá do každého záznamu protokolu další pole s konfigurovanou hodnotou a název pole nakonfigurovaný pro parametr **AdditionalDataTaggingName** . <br><br>Pokud je parametr **AdditionalDataTaggingName** prázdný, ale je nakonfigurovaná hodnota, výchozí název pole je **datatagování**.       |
|     |         |

### <a name="find-your-workspace-id-and-key"></a>Najít ID a klíč vašeho pracovního prostoru

Podrobnosti o parametrech **ID pracovního prostoru** a **WorkspaceKey** najdete v části Azure Sentinel:

1. V Azure Sentinel vyberte **Nastavení** na levé straně a pak vyberte kartu **Nastavení pracovního prostoru** .

1. V části Začínáme **s Log Analytics**  >  **1 Připojte zdroj dat** vyberte **Správa agentů pro Windows a Linux**.

1. Na kartách **servery Windows** vyhledejte ID vašeho pracovního prostoru, primární klíč a sekundární klíč.
## <a name="connect-with-the-log-analytics-api"></a>Připojení pomocí rozhraní Log Analytics API

Události můžete streamovat do Azure Sentinel pomocí Log Analytics rozhraní API kolekce dat k přímému volání koncového bodu RESTful.

Při volání koncového bodu RESTful přímo vyžaduje více programování, poskytuje také větší flexibilitu.

Další informace najdete v tématu [Log Analytics rozhraní API kolekce dat](../azure-monitor/logs/data-collector-api.md), zejména v následujících příkladech:

- [C#](../azure-monitor/logs/data-collector-api.md#c-sample)
- [Python 2](../azure-monitor/logs/data-collector-api.md#python-2-sample)

## <a name="connect-with-azure-functions"></a>Připojení pomocí Azure Functions

Pomocí Azure Functions společně s rozhraním API RESTful a různými jazyky kódování, jako je [PowerShell](../azure-functions/functions-reference-powershell.md), můžete vytvořit vlastní konektor bez serveru.

Příklady této metody najdete v těchto tématech:

- [Připojení koncového bodu standardu VMware z černého cloudu ke službě Azure Sentinel pomocí funkce Azure Functions](connect-vmware-carbon-black.md)
- [Připojení okta jednoho Sign-On ke službě Azure Sentinel pomocí funkce Azure Functions](connect-okta-single-sign-on.md)
- [Připojení Proofpoint klepnutím do Azure Sentinel pomocí funkce Azure Functions](connect-proofpoint-tap.md)
- [Připojení virtuálního počítače s Qualys ke službě Azure Sentinel pomocí funkce Azure Functions](connect-qualys-vm.md)
- [Ingestování souborů XML, CSV nebo jiných formátů dat](../azure-monitor/logs/create-pipeline-datacollector-api.md#ingesting-xml-csv-or-other-formats-of-data)
- [Monitorování přiblížení pomocí Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/monitoring-zoom-with-azure-sentinel/ba-p/1341516) (blog)
- [Nasazení Function App pro získání dat rozhraní API pro správu sady Office 365 do služby Azure Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data) (komunita Azure Sentinel GitHub)

## <a name="parse-your-custom-connector-data"></a>Analýza dat vlastního konektoru

K extrakci relevantních informací a naplnění příslušných polí v kontextu Azure Sentinel můžete použít integrovanou techniku vlastního konektoru.

Například:

- **Pokud jste používali Logstash**, analyzujte data pomocí modulu plug-in [grok](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html) Filter.
- **Pokud jste použili funkci Azure Functions**, analyzujte data pomocí kódu. Další informace najdete v tématu [analyzátory](normalization.md#parsers).

Sentinel Azure podporuje analýzu v době dotazu. Analýza v době dotazu vám umožní zapsat data v původním formátu a v případě potřeby analyzovat na vyžádání.

Analýza v době dotazu také znamená, že nemusíte znát přesnou strukturu vašich dat, když vytvoříte vlastní konektor, nebo dokonce i informace, které budete potřebovat k extrakci. Místo toho Analyzujte data kdykoli, i během šetření.

> [!NOTE]
> Aktualizace vašeho analyzátoru platí i pro data, která jste už do Azure Sentinel udělali.
> 
## <a name="next-steps"></a>Další kroky

Použijte data ingestovaná do Azure Sentinel a zabezpečte své prostředí pomocí některého z následujících postupů:

- [Získání přehledu o upozorněních](quickstart-get-visibility.md)
- [Vizualizace a monitorování dat](tutorial-monitor-your-data.md)
- [Šetření incidentů](tutorial-investigate-cases.md)
- [Detekce hrozeb](tutorial-detect-threats-built-in.md)
- [Automatizace prevence hrozeb](tutorial-respond-threats-playbook.md)
- [Proaktivní vyhledávání hrozeb](hunting.md)