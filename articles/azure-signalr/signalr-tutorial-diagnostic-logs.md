---
title: Diagnostické protokoly pro službu Azure SignalR
description: Zjistěte, jak nastavit diagnostické protokoly pro službu Azure SignalR a jak ji využít k vlastnímřešením řešení potíží.
author: wanlwanl
ms.service: signalr
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: wanl
ms.openlocfilehash: 72f57ba4bbbbde07f6d26edc88c158f301ebe2f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536730"
---
# <a name="diagnostic-logs-for-azure-signalr-service"></a>Diagnostické protokoly pro službu Azure SignalR

Tento kurz popisuje, co jsou diagnostické protokoly pro službu Azure SignalR a jak nastavit diagnostické protokoly a jak řešit problémy s diagnostickými protokoly.

## <a name="prerequisites"></a>Požadavky
Chcete-li povolit diagnostické protokoly, budete muset někde uložit data protokolu. Tento kurz používá Azure Storage a Log Analytics.

* [Úložiště Azure](../azure-monitor/platform/resource-logs-collect-storage.md) – uchovádiagnostické protokoly pro audit zásad, statickou analýzu nebo zálohování.
* [Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md) – flexibilní nástroj pro vyhledávání a analýzu protokolů, který umožňuje analýzu nezpracovaných protokolů generovaných prostředkem Azure.

## <a name="set-up-diagnostic-logs-for-an-azure-signalr-service"></a>Nastavení diagnostických protokolů pro službu Azure SignalR

Můžete zobrazit diagnostické protokoly pro službu Azure SignalR. Tyto protokoly poskytují bohatší zobrazení připojení k instanci služby Azure SignalR. Diagnostické protokoly poskytují podrobné informace o každém připojení. Například základní informace (ID uživatele, ID připojení a typ přenosu a tak dále) a informace o událostech (připojení, odpojení a přerušení události a tak dále) připojení. Diagnostické protokoly lze použít pro identifikaci problému, sledování připojení a analýzu.

### <a name="enable-diagnostic-logs"></a>Povolení diagnostických protokolů

Diagnostické protokoly jsou ve výchozím nastavení zakázány. Chcete-li povolit diagnostické protokoly, postupujte takto:

1. Na [webu Azure Portal](https://portal.azure.com)klikněte v části **Monitoring**na **Diagnostic kázně**.

    ![Navigace v podokně do nastavení diagnostiky](./media/signalr-tutorial-diagnostic-logs/diagnostic-settings-menu-item.png)

1. Potom klepněte na **tlačítko Přidat diagnostické nastavení**.

    ![Přidání diagnostických protokolů](./media/signalr-tutorial-diagnostic-logs/add-diagnostic-setting.png)

1. Nastavte požadovaný cíl archivu. V současné době podporujeme **archivovat účet úložiště** a **odeslat do log Analytics**.

1. Vyberte protokoly, které chcete archivovat.

    ![Podokno nastavení diagnostiky](./media/signalr-tutorial-diagnostic-logs/diagnostics-settings-pane.png)


1. Uložte nové nastavení diagnostiky.

Nové nastavení se projeví asi za 10 minut. Poté se protokoly zobrazí v nakonfigurovaném cíli archivace v podokně **Protokoly diagnostiky.**

Další informace o konfiguraci diagnostiky najdete v [přehledu protokolů diagnostiky Azure](../azure-monitor/platform/platform-logs-overview.md).

### <a name="diagnostic-logs-categories"></a>Kategorie diagnostických protokolů

Služba Azure SignalR zachycuje diagnostické protokoly v jedné kategorii:

* **Všechny protokoly:** Sledování připojení, které se připojují ke službě Azure SignalR. Protokoly poskytují informace o připojení nebo odpojení, ověřování a omezení. Další informace naleznete v následujícím oddílu.

### <a name="archive-to-a-storage-account"></a>Archivovat v účtu úložiště

Protokoly jsou uloženy v účtu úložiště, který byl nakonfigurován v **podokně protokoly diagnostiky.** Kontejner s `insights-logs-alllogs` názvem je vytvořen automaticky pro ukládání diagnostických protokolů. Uvnitř kontejneru jsou protokoly uloženy v souboru `resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX/y=YYYY/m=MM/d=DD/h=HH/m=00/PT1H.json`. V podstatě je cesta `resource ID` kombinována a `Date Time`. Soubory protokolu jsou `hour`rozděleny podle . Proto minuty vždy `m=00`být .

Všechny protokoly jsou uloženy ve formátu JavaScript Object Notation (JSON). Každá položka má řetězcová pole, která používají formát popsaný v následujících částech.

Řetězce JSON protokolu archivu obsahují prvky uvedené v následujících tabulkách:

**Formát**

Name (Název) | Popis
------- | -------
time | Čas události protokolu
level | Úroveň události protokolu
resourceId | ID prostředku služby Azure SignalR
location | Umístění služby Azure SignalR
category | Kategorie události protokolu
operationName | Název operace události
callerIpAddress | IP adresa vašeho serveru/klienta
properties | Podrobné vlastnosti související s touto událostí protokolu. Další podrobnosti naleznete v tabulce vlastností níže

**Tabulka vlastností**

Name (Název) | Popis
------- | -------
type | Typ události protokolu. V současné době poskytujeme informace o připojení ke službě Azure SignalR. K `ConnectivityLogs` dispozici je pouze typ
 – kolekce | Kolekce události protokolu. Povolené hodnoty `Connection`jsou: a `Authorization``Throttling`
connectionId | Identita připojení
transportType | Typ přenosu připojení. Povolené hodnoty `Websockets` \| `ServerSentEvents` \| jsou:`LongPolling`
connectionType | Typ připojení. Povolené hodnoty `Server` \| `Client`jsou: . `Server`: připojení ze strany serveru; `Client`: připojení ze strany klienta
userId | Identita uživatele
zpráva | Podrobná zpráva o události protokolu

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

### <a name="archive-logs-schema-for-log-analytics"></a>Schéma protokolů archivu pro analýzu protokolů

Chcete-li zobrazit diagnostické protokoly, postupujte takto:

1. Klikněte do `Logs` cílové analýzy protokolů.

    ![Položka nabídky Log Analytics](./media/signalr-tutorial-diagnostic-logs/log-analytics-menu-item.png)

2. Zadejte `SignalRServiceDiagnosticLogs` a vyberte časový rozsah pro dotaz na diagnostické protokoly. Rozšířené dotazy najdete v tématu [Začínáme s Analýzou protokolů na Azure Monitoru.](../azure-monitor/log-query/get-started-portal.md)

    ![Protokol dotazu v Log Analytics](./media/signalr-tutorial-diagnostic-logs/query-log-in-log-analytics.png)

Sloupce protokolu archivu obsahují prvky uvedené v následující tabulce:

Name (Název) | Popis
------- | ------- 
TimeGenerated | Čas události protokolu
Kolekce | Kolekce události protokolu. Povolené hodnoty `Connection`jsou: a `Authorization``Throttling`
OperationName | Název operace události
Umístění | Umístění služby Azure SignalR
Úroveň | Úroveň události protokolu
Adresa CallerIpAddress | IP adresa vašeho serveru/klienta
Zpráva | Podrobná zpráva o události protokolu
UserId | Identita uživatele
ConnectionId | Identita připojení
ConnectionType | Typ připojení. Povolené hodnoty `Server` \| `Client`jsou: . `Server`: připojení ze strany serveru; `Client`: připojení ze strany klienta
TransportType | Typ přenosu připojení. Povolené hodnoty `Websockets` \| `ServerSentEvents` \| jsou:`LongPolling`

### <a name="troubleshooting-with-diagnostic-logs"></a>Poradce při potížích s diagnostickými protokoly

Chcete-li řešit potíže se službou Azure SignalR, můžete povolit protokoly na straně serveru nebo klienta pro zachycení selhání. V současné době služba Azure SignalR zveřejňuje diagnostické protokoly, můžete také povolit protokoly na straně služby.

Při výskytu připojení neočekávané rostoucí nebo přetažení situace, můžete využít diagnostické protokoly k řešení potíží.

Typické problémy jsou často o neočekávaných změnách množství připojení, připojení dosáhnout omezení připojení a selhání autorizace. Podívejte se na další části o řešení potíží.

#### <a name="unexpected-connection-number-changes"></a>Neočekávané změny čísla připojení

##### <a name="unexpected-connection-dropping"></a>Neočekávané připojení klesá

Pokud dojde k neočekávané připojení přetažení, nejprve povolit protokoly v servisu, serveru a klienta stranách.

Pokud se připojení odpojí, diagnostické protokoly zaznamenají tuto `ConnectionAborted` `ConnectionEnded` událost `operationName`odpojení, zobrazí se nebo se zobrazí .

Rozdíl mezi `ConnectionAborted` `ConnectionEnded` a `ConnectionEnded` je, že je očekávané odpojení, které je spuštěno na straně klienta nebo serveru. Zatímco `ConnectionAborted` je obvykle neočekávané připojení pádu události a přerušení `message`důvod bude k dispozici v .

Důvody přerušení jsou uvedeny v následující tabulce:

Důvod | Popis
------- | ------- 
Počet připojení dosáhne limitu | Počet připojení dosáhne limitu aktuální cenové úrovně. Zvažte navýšení kapacity servisní jednotky
Aplikační server ukončil připojení | App server spustí potrat. Lze jej považovat za očekávaný potrat
Časový limit příkazu ping připojení | Obvykle je to způsobeno problémem sítě. Zvažte kontrolu dostupnosti aplikačního serveru z internetu.
Opětovné načtení služby, opětovné připojení | Služba Azure SignalR se znovu načítá. Azure SignalR podporuje automatické opětovné připojení, můžete počkat, až se znovu připojíte nebo se ručně znovu připojíte ke službě Azure SignalR
Interní chyba přechodu serveru | Ve službě Azure SignalR dojde k přechodné chybě, měla by být automaticky obnovena.
Připojení k serveru bylo přerušeno. | Připojení k serveru klesne s neznámou chybou, zvažte vlastní řešení problémů s protokolem na straně služby/serveru/klienta. Pokuste se vyloučit základní problémy (např. problém se sítí, problém na straně aplikačního serveru a tak dále). Pokud problém není vyřešen, kontaktujte nás pro další pomoc. Další informace naleznete v části [Získat nápovědu.](#get-help) 

##### <a name="unexpected-connection-growing"></a>Neočekávané připojení roste

Chcete-li řešit problémy s neočekávaným rozšířením připojení, první věc, kterou musíte udělat, je odfiltrovat další připojení. Do připojení testovacího klienta můžete přidat jedinečné ID testovacího uživatele. Pak ověřte v diagnostických protokolech, pokud vidíte více než jedno připojení klienta mají stejné id testovacího uživatele nebo IP, pak je pravděpodobné, že na straně klienta vytvořit a vytvořit více připojení, než je očekávání. Zkontrolujte stranu klienta.

#### <a name="authorization-failure"></a>Selhání autorizace

Pokud se dostanete 401 Neoprávněné vráceny pro požadavky klientů, zkontrolujte diagnostické protokoly. Pokud narazíte `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>`na , znamená to, že všechny okruhy uživatelů v přístupovém tokenu jsou neplatné. Zkuste použít platné cílové skupiny navržené v protokolu.


#### <a name="throttling"></a>Throttling

Pokud zjistíte, že nelze navázat signalr připojení klientů ke službě Azure SignalR, zkontrolujte diagnostické protokoly. Pokud narazíte `Connection count reaches limit` v diagnostické protokolu, navážete příliš mnoho připojení ke službě SignalR, které dosáhnou limitu počtu připojení. Zvažte navýšení kapacity služby SignalR. Pokud narazíte `Message count reaches limit` v diagnostické protokolu, znamená to, že používáte úroveň free a vypotřebujete kvótu zpráv. Pokud chcete odeslat další zprávy, zvažte změnu služby SignalR na standardní úroveň pro odesílání dalších zpráv. Další informace najdete v [tématu Azure SignalR Service Pricing](https://azure.microsoft.com/pricing/details/signalr-service/).

### <a name="get-help"></a>Podpora

Doporučujeme, abyste nejprve řešili problémy sami. Většina problémů je způsobena problémy s aplikačním serverem nebo sítí. Postupujte [podle průvodce odstraňováním problémů s diagnostickým protokolem](#troubleshooting-with-diagnostic-logs) a [základním průvodcem pro odstraňování problémů,](https://github.com/Azure/azure-signalr/blob/dev/docs/tsg.md) abyste našli hlavní příčinu.
Pokud problém pořád nejde vyřešit, zvažte otevření problému na GitHubu nebo vytvoření lístku na Webu Azure Portal.
Poskytnout:
1. Časový rozsah přibližně 30 minut, když dojde k problému
2. ID prostředku služby Azure SignalR
3. Podrobnosti o problému, co nejkonkrétnější: Například appserver neodesílá zprávy, připojení klienta klesá a tak dále
4. Protokoly shromážděné ze strany serveru/klienta a další materiály, které mohou být užitečné
5. [Nepovinné] Repro kód

> [!NOTE]
> Pokud otevřete problém v GitHubu, udržujte své citlivé informace (například ID prostředků, protokoly serveru/klienta) soukromé, pouze odesílejte členům v organizaci Microsoftu soukromě.