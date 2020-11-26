---
title: Protokoly prostředků pro službu Azure Signal Service
description: Naučte se, jak nastavit protokoly prostředků pro službu Azure Signal Service a jak ji využít k tomu, aby se mohla sami řešit.
author: wanlwanl
ms.service: signalr
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: wanl
ms.openlocfilehash: 5ad40ca051677ced0c6d8b5c35e8563272ff598f
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183970"
---
# <a name="resource-logs-for-azure-signalr-service"></a>Protokoly prostředků pro službu Azure Signal Service

Tento kurz popisuje, jaké jsou protokoly prostředků pro službu Azure Signal, jak je nastavit a jak s nimi řešit potíže. 

## <a name="prerequisites"></a>Předpoklady
Pokud chcete povolit protokoly prostředků, budete si muset někam uložit data protokolu. V tomto kurzu se používá Azure Storage a Log Analytics.

* [Azure Storage](../azure-monitor/platform/resource-logs.md#send-to-azure-storage) – zachovává protokoly prostředků pro audit zásad, statickou analýzu nebo zálohování.
* [Log Analytics](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace) – flexibilní nástroj pro hledání a analýzu protokolů, který umožňuje analýzu nezpracovaných protokolů generovaných prostředkem Azure.

## <a name="set-up-resource-logs-for-an-azure-signalr-service"></a>Nastavení protokolů prostředků pro službu Azure Signal

Můžete zobrazit protokoly prostředků pro službu Azure Signal Service. Tyto protokoly poskytují bohatší přehled o připojení ke své instanci služby signalizace Azure. Protokoly prostředků poskytují podrobné informace o každém připojení. Například základní informace (ID uživatele, ID připojení a typ přenosu atd.) a informace o událostech (připojit, odpojit a přerušit událost atd.) připojení. protokoly prostředků se dají použít k identifikaci problémů, sledování a analýze připojení.

### <a name="enable-resource-logs"></a>Povolení protokolů prostředků

Ve výchozím nastavení jsou protokoly prostředků zakázané. Pokud chcete povolit protokoly prostředků, postupujte podle těchto kroků:

1. V [Azure Portal](https://portal.azure.com)v části **monitorování** klikněte na **nastavení diagnostiky**.

    ![Navigační podokno s nastavením diagnostiky](./media/signalr-tutorial-diagnostic-logs/diagnostic-settings-menu-item.png)

1. Pak klikněte na **Přidat nastavení diagnostiky**.

    ![Přidat protokoly prostředků](./media/signalr-tutorial-diagnostic-logs/add-diagnostic-setting.png)

1. Nastavte cíl archivu, který chcete. V současné době podporujeme **archivaci na účet úložiště** a **odeslání do Log Analytics**.

1. Vyberte protokoly, které chcete archivovat.

    ![Podokno nastavení diagnostiky](./media/signalr-tutorial-diagnostic-logs/diagnostics-settings-pane.png)


1. Uložte nová nastavení diagnostiky.

Nové nastavení se projeví přibližně po dobu 10 minut. Pak se protokoly objeví v nakonfigurovaném cíli archivace v podokně **diagnostické protokoly** .

Další informace o konfiguraci diagnostiky najdete v tématu [Přehled protokolů prostředků Azure](../azure-monitor/platform/platform-logs-overview.md).

### <a name="resource-logs-categories"></a>Kategorie protokolů prostředků

Služba signalizace Azure zachycuje protokoly prostředků v jedné kategorii:

* **Všechny protokoly**: Sledujte připojení, která se připojují ke službě Azure Signal. Protokoly obsahují informace o připojení a odpojení, ověřování a omezování. Další informace naleznete v následujícím oddílu.

### <a name="archive-to-a-storage-account"></a>Archivovat v účtu úložiště

Protokoly se ukládají do účtu úložiště, který je nakonfigurovaný v podokně **diagnostické protokoly** . Kontejner s názvem `insights-logs-alllogs` se vytvoří automaticky pro ukládání protokolů prostředků. V rámci kontejneru jsou protokoly uloženy v souboru `resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX/y=YYYY/m=MM/d=DD/h=HH/m=00/PT1H.json` . V podstatě je cesta kombinována s `resource ID` a `Date Time` . Soubory protokolu jsou rozděleny podle `hour` . Proto jsou minuty vždy minuty `m=00` .

Všechny protokoly jsou uložené ve formátu JavaScript Object Notation (JSON). Každá položka má pole řetězce, která používají formát popsaný v následujících částech.

Řetězce JSON protokolu archivu obsahují prvky uvedené v následujících tabulkách:

**Formát**

Název | Popis
------- | -------
time | Čas události protokolu
úroveň | Úroveň události protokolu
resourceId | ID prostředku služby Azure Signal
location | Umístění služby signalizace Azure
category | Kategorie události protokolu
operationName | Název operace události
callerIpAddress | IP adresa vašeho serveru/klienta
properties | Podrobné vlastnosti související s touto událostí protokolu Další podrobnosti najdete v tabulce vlastností níže.

**Tabulka vlastností**

Název | Popis
------- | -------
typ | Typ události protokolu V současné době poskytujeme informace o připojení ke službě Azure Signal. `ConnectivityLogs`K dispozici je jenom typ.
 – kolekce | Kolekce události protokolu Povolené hodnoty jsou: `Connection` `Authorization` a `Throttling`
connectionId | Identita připojení
transportType | Typ přenosu připojení. Povolené hodnoty jsou: `Websockets` \| `ServerSentEvents` \|`LongPolling`
connectionType | Typ připojení. Povolené hodnoty jsou: `Server` \| `Client` . `Server`: připojení ze strany serveru; `Client`: připojení ze strany klienta
userId | Identita uživatele
zpráva | Podrobná zpráva události protokolu

Následující kód je příkladem řetězce JSON protokolu archivu:

```json
{
    "properties": {
        "message": "Entered Serverless mode.",
        "type": "ConnectivityLogs",
        "collection": "Connection",
        "connectionId": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "userId": "User",
        "transportType": "WebSockets",
        "connectionType": "Client"
    },
    "operationName": "ServerlessModeEntered",
    "category": "AllLogs",
    "level": "Informational",
    "callerIpAddress": "xxx.xxx.xxx.xxx",
    "time": "2019-01-01T00:00:00Z",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX",
    "location": "xxxx"
}
```

### <a name="archive-logs-schema-for-log-analytics"></a>Archiv protokolů schématu pro Log Analytics

Chcete-li zobrazit protokoly prostředků, postupujte podle následujících kroků:

1. Klikněte na `Logs` cílový Log Analytics.

    ![Položka nabídky Log Analytics](./media/signalr-tutorial-diagnostic-logs/log-analytics-menu-item.png)

2. Zadejte `SignalRServiceDiagnosticLogs` a vyberte časový rozsah pro dotazování protokolů prostředků. Rozšířené dotazy najdete v tématu [Začínáme s Log Analytics v Azure monitor](../azure-monitor/log-query/log-analytics-tutorial.md)

    ![Dotaz pro přihlášení Log Analytics](./media/signalr-tutorial-diagnostic-logs/query-log-in-log-analytics.png)

Sloupce protokolu archivu obsahují prvky uvedené v následující tabulce:

Název | Popis
------- | ------- 
TimeGenerated | Čas události protokolu
Kolekce | Kolekce události protokolu Povolené hodnoty jsou: `Connection` `Authorization` a `Throttling`
OperationName | Název operace události
Umístění | Umístění služby signalizace Azure
Úroveň | Úroveň události protokolu
CallerIpAddress | IP adresa vašeho serveru/klienta
Zpráva | Podrobná zpráva události protokolu
UserId | Identita uživatele
ConnectionId | Identita připojení
ConnectionType | Typ připojení. Povolené hodnoty jsou: `Server` \| `Client` . `Server`: připojení ze strany serveru; `Client`: připojení ze strany klienta
TransportType | Typ přenosu připojení. Povolené hodnoty jsou: `Websockets` \| `ServerSentEvents` \|`LongPolling`

### <a name="troubleshooting-with-resource-logs"></a>Řešení potíží s protokoly prostředků

Pokud chcete řešit potíže s Azure Signal Service, můžete povolit protokoly na straně serveru nebo klienta pro zachycení selhání. Služba signalizace Azure v současné době zpřístupňuje protokoly prostředků. můžete také povolit protokoly pro stranu služby.

Pokud se setkáte s připojením neočekávané situace při rostoucím nebo vyřazování, můžete využít výhod protokolů prostředků k řešení potíží.

Typické problémy se často týkají neočekávaných změn množství připojení, připojení dosáhnou limitů připojení a selhání autorizace. V dalších částech najdete informace o tom, jak řešit potíže.

#### <a name="unexpected-connection-number-changes"></a>Neočekávané změny počtu připojení

##### <a name="unexpected-connection-dropping"></a>Neočekávané připojení

Pokud dojde k neočekávanému výpadku připojení, nejprve povolte protokoly na straně služby, serveru a klienta.

Pokud se připojení odpojí, protokoly prostředků zaznamenají tuto událost odpojení. zobrazí se `ConnectionAborted` nebo `ConnectionEnded` v ní `operationName` .

Rozdíl mezi `ConnectionAborted` a `ConnectionEnded` je `ConnectionEnded` očekávané odpojení, které se aktivuje na straně klienta nebo serveru. I když `ConnectionAborted` se obvykle jedná o neočekávanou událost připojení a důvod přerušení bude k dispozici v `message` .

Důvody přerušení jsou uvedeny v následující tabulce:

Důvod | Popis
------- | ------- 
Počet připojení dosáhl limitu. | Počet připojení dosáhl limitu aktuální cenové úrovně. Zvažte horizontální navýšení kapacity jednotek služby
Aplikační server uzavřel připojení. | App Server aktivuje přerušení. Dá se zvážit jako očekávané přerušení.
Časový limit testu připojení | Obvykle je to způsobeno problémem v síti. Zvažte kontrolu dostupnosti aplikačního serveru z Internetu.
Opětovné načtení služby, opětovné připojení | Služba signalizace Azure se znovu načítá. Služba Azure Signal podporuje automatické opětovné připojení. můžete počkat, až se znovu připojíte nebo ručně znovu připojíte ke službě Azure Signal Service.
Přechodná chyba interního serveru | K přechodné chybě dochází ve službě Azure Signal Service, měla by se automaticky obnovit.
Připojení k serveru bylo vyřazeno. | Připojení k serveru se nehodí s neznámou chybou. nejdřív zvažte řešení potíží s protokolem Service/Server/klienta na straně klienta. Zkuste vyloučit základní problémy (např. problém se sítí, problém na straně aplikačního serveru atd.). Pokud se problém nevyřeší, kontaktujte nás a požádejte o další pomoc. Další informace najdete v části [získat nápovědu](#get-help) . 

##### <a name="unexpected-connection-growing"></a>Neočekávané rostoucí spojení

Pokud chcete řešit potíže týkající se neočekávaného připojení, musíte nejdřív udělat odfiltrování dalších připojení. K testovacímu klientskému připojení můžete přidat jedinečné ID testovacího uživatele. Pak ho ověřte v části s protokoly prostředků. Pokud vidíte víc než jedno připojení klientů, které má stejné ID nebo IP adresy testovacího uživatele, je pravděpodobné, že se na straně klienta vytvoří a naváže více připojení, než je očekávané. Podívejte se na stranu klienta.

#### <a name="authorization-failure"></a>Selhání autorizace

Pokud obdržíte pro žádosti klientů 401 neautorizovaných prostředků, ověřte jejich protokoly. Pokud se setkáte `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>` , znamená to, že všechny cílové skupiny ve vašem přístupovém tokenu jsou neplatné. Zkuste použít platné cílové skupiny navržené v protokolu.


#### <a name="throttling"></a>Throttling

Pokud zjistíte, že nemůžete navázat připojení klientů ke službě Azure Signal Service, Projděte si protokoly prostředků. Pokud narazíte `Connection count reaches limit` na protokol prostředků, nakonfigurujete příliš mnoho připojení ke službě signalizace, která dosáhla limitu počtu připojení. Zvažte možnost škálování služby Signal. Pokud se setkáte `Message count reaches limit` v protokolu prostředků, znamená to, že použijete úroveň Free a použijete kvótu zpráv. Pokud chcete odesílat další zprávy, zvažte změnu služby signalizace na úroveň Standard, aby se odesílaly další zprávy. Další informace najdete v tématu [ceny služby Azure Signal Service](https://azure.microsoft.com/pricing/details/signalr-service/).

### <a name="get-help"></a>Získání pomoci

Doporučujeme, abyste si nejdřív vypomohli. Většina problémů je způsobená aplikačním serverem nebo problémy se sítí. Podle pokynů [Průvodce odstraňováním potíží s protokolem prostředků](#troubleshooting-with-resource-logs) a průvodcem řešením potíží se [základními](https://github.com/Azure/azure-signalr/blob/dev/docs/tsg.md) příčinami Najděte hlavní příčinu.
Pokud se problém ještě nedá vyřešit, zvažte otevření problému v GitHubu nebo vytvoření lístku na webu Azure Portal.
Sdělit
1. Časový rozsah přibližně 30 minut při výskytu problému
2. ID prostředku služby signalizace Azure
3. Podrobnosti o problému, jak je to možné, například AppServer neodesílá zprávy, připojení klienta a tak dále.
4. Protokoly shromážděné na straně serveru/klienta a další materiály, které mohou být užitečné
5. Volitelné Reprodukci kód

> [!NOTE]
> Pokud otevřete problém na GitHubu, zachovejte vaše citlivé informace (například ID prostředku, protokol serveru/klienta) privátní, stačí odeslat členům v organizaci Microsoftu soukromě.