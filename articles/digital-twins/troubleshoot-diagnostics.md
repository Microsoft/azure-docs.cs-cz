---
title: Povolení diagnostických protokolů a dotazování na ně
titleSuffix: Azure Digital Twins
description: Podívejte se, jak povolit protokolování s nastavením diagnostiky a zadat dotaz na protokoly pro okamžité prohlížení.
author: baanders
ms.author: baanders
ms.date: 2/24/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 08db4d92da5213b1ce1b79867650da9df8c38ee4
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385075"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Řešení potíží se službou Azure Digital nevláken: protokolování diagnostiky

Digitální vlákna Azure může shromažďovat protokoly pro instanci služby za účelem sledování jeho výkonu, přístupu a dalších dat. Pomocí těchto protokolů můžete získat představu o tom, co se děje v instanci digitálních vláken Azure, a provádět analýzy původních příčin problémů, aniž byste museli kontaktovat podporu Azure.

V tomto článku se dozvíte, jak [**nakonfigurovat nastavení diagnostiky**](#turn-on-diagnostic-settings) v [Azure Portal](https://portal.azure.com) spustit shromažďování protokolů z instance digitálních vláken Azure. Můžete také zadat, kam se mají ukládat protokoly (například Log Analytics nebo účet úložiště podle vašeho výběru).

Tento článek obsahuje také seznam všech [kategorií protokolů](#log-categories) a [schémat protokolů](#log-schemas) , které shromažďuje digitální vlákna Azure.

Po nastavování protokolů můžete také [**dotazovat protokoly**](#view-and-query-logs) a rychle tak shromáždit vlastní přehledy.

## <a name="turn-on-diagnostic-settings"></a>Zapnout nastavení diagnostiky 

Zapnutím nastavení diagnostiky zahajte shromažďování protokolů na instanci digitálních vláken Azure. Můžete také zvolit cíl, ve kterém se mají ukládat exportované protokoly. Tady je postup, jak povolit nastavení diagnostiky pro instanci digitálních vláken Azure.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte k instanci digitálních vláken Azure. Můžete ji najít zadáním jejího názvu do panelu hledání na portálu. 

2. V nabídce vyberte **nastavení diagnostiky** a pak **přidejte nastavení diagnostiky**.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Snímek obrazovky zobrazující stránku nastavení diagnostiky a tlačítko, které se má přidat" lightbox="media/troubleshoot-diagnostics/diagnostic-settings.png":::

3. Na následující stránce zadejte následující hodnoty:
     * **Název nastavení diagnostiky**: zadejte název diagnostického nastavení.
     * **Podrobnosti kategorie**: vyberte operace, které chcete monitorovat, a zaškrtněte políčka pro povolení diagnostiky pro tyto operace. Operace, které nastavení diagnostiky mohou hlásit:
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        Další podrobnosti o těchto kategoriích a informacích, které obsahují, najdete v části [*Kategorie protokolů*](#log-categories) níže.
     * **Podrobnosti cíle**: vyberte, kam chcete protokoly odesílat. Můžete vybrat libovolnou kombinaci tří možností:
        - Odeslání do Log Analytics
        - Archivovat v účtu úložiště
        - Streamovat do centra událostí

        Pokud jsou nutné pro výběr cíle, bude vám pravděpodobně požádáno, abyste doplnili další podrobnosti.  
    
4. Uložte nová nastavení. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Snímek obrazovky zobrazující stránku nastavení diagnostiky, kde uživatel vyplnil název nastavení diagnostiky a provedl některé výběry zaškrtávacích políček pro podrobnosti kategorie a podrobnosti o cíli. Tlačítko Uložit je zvýrazněno." lightbox="media/troubleshoot-diagnostics/diagnostic-settings-details.png":::

Nové nastavení se projeví přibližně po dobu 10 minut. Po této konfiguraci se protokoly zobrazí na stránce **nastavení diagnostiky na stránce nastavení diagnostiky** pro vaši instanci. 

Podrobnější informace o nastavení diagnostiky a jejich možnostech instalace najdete na stránce [*Vytvoření nastavení diagnostiky pro odesílání protokolů a metrik platforem do různých umístění*](../azure-monitor/essentials/diagnostic-settings.md).

## <a name="log-categories"></a>Kategorie protokolů

Tady jsou další podrobnosti o kategoriích protokolů, které shromažďuje digitální vlákna Azure.

| Kategorie protokolu | Popis |
| --- | --- |
| ADTModelsOperation | Protokolovat všechna volání rozhraní API týkající se modelů |
| ADTQueryOperation | Protokolování všech volání rozhraní API vztahujících se k dotazům |
| ADTEventRoutesOperation | Protokolujte všechna volání rozhraní API, která souvisí s trasami událostí, a také výstup událostí z digitálních vláken Azure do služby koncového bodu, jako je Event Grid, Event Hubs a Service Bus |
| ADTDigitalTwinsOperation | Protokolovat všechna volání rozhraní API týkající se jednotlivých vláken |

Každá kategorie protokolu se skládá z operací zápisu, čtení, odstranění a akce.  Tato mapa pro REST API volání následujícím způsobem:

| Typ události | REST API operace |
| --- | --- |
| Zápis | Vložit a opravit |
| Číst | GET |
| Odstranit | DELETE |
| Akce | POST |

Tady je vyčerpávající seznam operací a odpovídajících [digitálních vláken Azure REST API volání](/rest/api/azure-digitaltwins/) , která se protokolují v jednotlivých kategoriích. 

>[!NOTE]
> Každá kategorie protokolu obsahuje několik operací/REST API volání. V následující tabulce jsou všechny kategorie protokolů namapovány na všechny operace/REST API volání pod ní, dokud není uvedena další kategorie protokolu. 

| Kategorie protokolu | Operace | REST API volání a další události |
| --- | --- | --- |
| ADTModelsOperation | Microsoft. DigitalTwins/modely/Write | Digitální nazdvojené modely – rozhraní API pro aktualizaci |
|  | Microsoft. DigitalTwins/modely/číst | Digitální zdvojené modely, které získají podle ID a rozhraní API seznamů |
|  | Microsoft. DigitalTwins/modely/DELETE | Digitální vyzdvojené modely – odstranění rozhraní API |
|  | Microsoft. DigitalTwins/modely/akce | Digitální nazdvojené modely – přidání rozhraní API |
| ADTQueryOperation | Microsoft. DigitalTwins/dotaz/akce | Rozhraní API pro vyzdvojené dotazy |
| ADTEventRoutesOperation | Microsoft. DigitalTwins/eventroutes/Write | Směrování událostí – přidat rozhraní API |
|  | Microsoft. DigitalTwins/eventroutes/Read | Rozhraní API pro směrování událostí Get podle ID a seznamu |
|  | Microsoft. DigitalTwins/eventroutes/DELETE | Rozhraní API pro odstranění tras událostí |
|  | Microsoft. DigitalTwins/eventroutes/Action | Při pokusu o publikování událostí do služby koncového bodu došlo k chybě (ne volání rozhraní API). |
| ADTDigitalTwinsOperation | Microsoft. DigitalTwins/DigitalTwins/Write | Digitální vlákna přidat, přidat vztah, aktualizovat, aktualizovat komponentu |
|  | Microsoft. DigitalTwins/DigitalTwins/Read | Digitální vlákna získá podle ID, získá komponentu, získá relaci podle ID, vypíše příchozí vztahy, vypíše relace. |
|  | Microsoft. DigitalTwins/DigitalTwins/DELETE | Digitální vlákna odstranit, odstranit relaci |
|  | Microsoft. DigitalTwins/DigitalTwins/Action | Digitální vlákna odesílají telemetrii komponent, odesílají telemetrii. |

## <a name="log-schemas"></a>Schémata protokolů 

Každá kategorie protokolu má schéma, které definuje způsob hlášení událostí v této kategorii. Jednotlivé položky protokolu se ukládají jako text a naformátují se jako objekt BLOB JSON. Pole v části log a příklady JSON jsou k dispozici pro každý níže uvedený typ protokolu. 

`ADTDigitalTwinsOperation`, `ADTModelsOperation` a `ADTQueryOperation` používejte konzistentní schéma protokolu rozhraní API. `ADTEventRoutesOperation` rozšiřuje schéma tak, aby obsahovalo `endpointName` pole ve vlastnostech.

### <a name="api-log-schemas"></a>Schémata protokolů rozhraní API

Toto schéma protokolu je konzistentní pro `ADTDigitalTwinsOperation` , `ADTModelsOperation` , `ADTQueryOperation` . Stejné schéma se používá také pro `ADTEventRoutesOperation` , s **výjimkou** `Microsoft.DigitalTwins/eventroutes/action` názvu operace (Další informace o tomto schématu najdete v další části [*schémata protokolů odchozího protokolu*](#egress-log-schemas)).

Schéma obsahuje informace týkající se volání rozhraní API k instanci digitálních vláken Azure.

Tady jsou popisy polí a vlastností pro protokoly rozhraní API.

| Název pole | Datový typ | Popis |
|-----|------|-------------|
| `Time` | DateTime | Datum a čas, kdy k této události došlo, v UTC |
| `ResourceId` | Řetězec | ID prostředku Azure Resource Manager prostředku, ve kterém se událost uskutečnila |
| `OperationName` | Řetězec  | Typ akce prováděné během události |
| `OperationVersion` | Řetězec | Verze rozhraní API využitá během události |
| `Category` | Řetězec | Typ prostředku, který se emituje. |
| `ResultType` | Řetězec | Výsledek události |
| `ResultSignature` | Řetězec | Stavový kód HTTP události |
| `ResultDescription` | Řetězec | Další podrobnosti o události |
| `DurationMs` | Řetězec | Jak dlouho trvalo provádění události v milisekundách |
| `CallerIpAddress` | Řetězec | Maskovaná zdrojová IP adresa pro událost |
| `CorrelationId` | Identifikátor GUID | Pro událost se zadal jedinečný identifikátor zákazníka. |
| `ApplicationId` | Identifikátor GUID | ID aplikace používané v autorizačním držiteli |
| `Level` | Int | Závažnost protokolování události |
| `Location` | Řetězec | Oblast, ve které byla událost provedena |
| `RequestUri` | Identifikátor URI | Koncový bod využíval během události |
| `TraceId` | Řetězec | `TraceId`, jako součást [kontextu trasování W3C's](https://www.w3.org/TR/trace-context/). ID celého trasování, které slouží k jednoznačné identifikaci distribuované trasování napříč systémy. |
| `SpanId` | Řetězec | `SpanId` jako součást [kontextu trasování W3C's](https://www.w3.org/TR/trace-context/). ID této žádosti v trasování |
| `ParentId` | Řetězec | `ParentId` jako součást [kontextu trasování W3C's](https://www.w3.org/TR/trace-context/). Požadavek bez nadřazeného ID je kořenem trasování. |
| `TraceFlags` | Řetězec | `TraceFlags` jako součást [kontextu trasování W3C's](https://www.w3.org/TR/trace-context/). Ovládá příznaky trasování, jako je vzorkování, úroveň trasování atd. |
| `TraceState` | Řetězec | `TraceState` jako součást [kontextu trasování W3C's](https://www.w3.org/TR/trace-context/). Další informace o identifikaci trasování specifické pro dodavatele v různých systémech distribuovaného trasování. |

Níže jsou uvedeny příklady tělo JSON pro tyto typy protokolů.

#### <a name="adtdigitaltwinsoperation"></a>ADTDigitalTwinsOperation

```json
{
  "time": "2020-03-14T21:11:14.9918922Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/digitaltwins/write",
  "operationVersion": "2020-10-31",
  "category": "DigitalTwinOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": 8,
  "callerIpAddress": "13.68.244.*",
  "correlationId": "2f6a8e64-94aa-492a-bc31-16b9f0b16ab3",
  "identity": {
    "claims": {
      "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
    }
  },
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/digitaltwins/factory-58d81613-2e54-4faa-a930-d980e6e2a884?api-version=2020-10-31",
  "properties": {},
  "traceContext": {
    "traceId": "95ff77cfb300b04f80d83e64d13831e7",
    "spanId": "b630da57026dd046",
    "parentId": "9f0de6dadae85945",
    "traceFlags": "01",
    "tracestate": "k1=v1,k2=v2"
  }
}
```

#### <a name="adtmodelsoperation"></a>ADTModelsOperation

```json
{
  "time": "2020-10-29T21:12:24.2337302Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/models/write",
  "operationVersion": "2020-10-31",
  "category": "ModelsOperation",
  "resultType": "Success",
  "resultSignature": "201",
  "resultDescription": "",
  "durationMs": "80",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "9dcb71ea-bb6f-46f2-ab70-78b80db76882",
  "identity": {
    "claims": {
      "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
    }
  },
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/Models?api-version=2020-10-31",
  "properties": {},
  "traceContext": {
    "traceId": "95ff77cfb300b04f80d83e64d13831e7",
    "spanId": "b630da57026dd046",
    "parentId": "9f0de6dadae85945",
    "traceFlags": "01",
    "tracestate": "k1=v1,k2=v2"
  }
}
```

#### <a name="adtqueryoperation"></a>ADTQueryOperation

```json
{
  "time": "2020-12-04T21:11:44.1690031Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/query/action",
  "operationVersion": "2020-10-31",
  "category": "QueryOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "314",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "1ee2b6e9-3af4-4873-8c7c-1a698b9ac334",
  "identity": {
    "claims": {
      "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
    }
  },
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/query?api-version=2020-10-31",
  "properties": {},
  "traceContext": {
    "traceId": "95ff77cfb300b04f80d83e64d13831e7",
    "spanId": "b630da57026dd046",
    "parentId": "9f0de6dadae85945",
    "traceFlags": "01",
    "tracestate": "k1=v1,k2=v2"
  }
}
```

#### <a name="adteventroutesoperation"></a>ADTEventRoutesOperation

Tady je příklad těla zprávy JSON pro `ADTEventRoutesOperation` typ, který **není** `Microsoft.DigitalTwins/eventroutes/action` typu (Další informace o tomto schématu najdete v další části [*schémata protokolů odchozího protokolu*](#egress-log-schemas)).

```json
  {
    "time": "2020-10-30T22:18:38.0708705Z",
    "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
    "operationName": "Microsoft.DigitalTwins/eventroutes/write",
    "operationVersion": "2020-10-31",
    "category": "EventRoutesOperation",
    "resultType": "Success",
    "resultSignature": "204",
    "resultDescription": "",
    "durationMs": 42,
    "callerIpAddress": "212.100.32.*",
    "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
    "identity": {
      "claims": {
        "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
      }
    },
    "level": "4",
    "location": "southcentralus",
    "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/EventRoutes/egressRouteForEventHub?api-version=2020-10-31",
    "properties": {},
    "traceContext": {
      "traceId": "95ff77cfb300b04f80d83e64d13831e7",
      "spanId": "b630da57026dd046",
      "parentId": "9f0de6dadae85945",
      "traceFlags": "01",
      "tracestate": "k1=v1,k2=v2"
    }
  },
```

### <a name="egress-log-schemas"></a>Schémata protokolu odchozího přenosu dat

Toto je schéma pro `ADTEventRoutesOperation` protokoly specifické pro `Microsoft.DigitalTwins/eventroutes/action` název operace. Obsahují podrobnosti týkající se výjimek a operace rozhraní API kolem koncových bodů, které se připojují k instanci digitálních vláken Azure.

|Název pole | Datový typ | Popis |
|-----|------|-------------|
| `Time` | DateTime | Datum a čas, kdy k této události došlo, v UTC |
| `ResourceId` | Řetězec | ID prostředku Azure Resource Manager prostředku, ve kterém se událost uskutečnila |
| `OperationName` | Řetězec  | Typ akce prováděné během události |
| `Category` | Řetězec | Typ prostředku, který se emituje. |
| `ResultDescription` | Řetězec | Další podrobnosti o události |
| `CorrelationId` | Identifikátor GUID | Pro událost se zadal jedinečný identifikátor zákazníka. |
| `ApplicationId` | Identifikátor GUID | ID aplikace používané v autorizačním držiteli |
| `Level` | Int | Závažnost protokolování události |
| `Location` | Řetězec | Oblast, ve které byla událost provedena |
| `TraceId` | Řetězec | `TraceId`, jako součást [kontextu trasování W3C's](https://www.w3.org/TR/trace-context/). ID celého trasování, které slouží k jednoznačné identifikaci distribuované trasování napříč systémy. |
| `SpanId` | Řetězec | `SpanId` jako součást [kontextu trasování W3C's](https://www.w3.org/TR/trace-context/). ID této žádosti v trasování |
| `ParentId` | Řetězec | `ParentId` jako součást [kontextu trasování W3C's](https://www.w3.org/TR/trace-context/). Požadavek bez nadřazeného ID je kořenem trasování. |
| `TraceFlags` | Řetězec | `TraceFlags` jako součást [kontextu trasování W3C's](https://www.w3.org/TR/trace-context/). Ovládá příznaky trasování, jako je vzorkování, úroveň trasování atd. |
| `TraceState` | Řetězec | `TraceState` jako součást [kontextu trasování W3C's](https://www.w3.org/TR/trace-context/). Další informace o identifikaci trasování specifické pro dodavatele v různých systémech distribuovaného trasování. |
| `EndpointName` | Řetězec | Název výstupního koncového bodu vytvořeného v rámci digitálních vláken Azure |

Níže jsou uvedeny příklady tělo JSON pro tyto typy protokolů.

#### <a name="adteventroutesoperation-for-microsoftdigitaltwinseventroutesaction"></a>ADTEventRoutesOperation pro Microsoft. DigitalTwins/eventroutes/Action

Tady je příklad těla JSON pro `ADTEventRoutesOperation` typ, který je `Microsoft.DigitalTwins/eventroutes/action` typu.

```json
{
  "time": "2020-11-05T22:18:38.0708705Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/eventroutes/action",
  "operationVersion": "",
  "category": "EventRoutesOperation",
  "resultType": "",
  "resultSignature": "",
  "resultDescription": "Unable to send EventHub message to [myPath] for event Id [f6f45831-55d0-408b-8366-058e81ca6089].",
  "durationMs": -1,
  "callerIpAddress": "",
  "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
  "identity": {
    "claims": {
      "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
    }
  },
  "level": "4",
  "location": "southcentralus",
  "uri": "",
  "properties": {
    "endpointName": "myEventHub"
  },
  "traceContext": {
    "traceId": "95ff77cfb300b04f80d83e64d13831e7",
    "spanId": "b630da57026dd046",
    "parentId": "9f0de6dadae85945",
    "traceFlags": "01",
    "tracestate": "k1=v1,k2=v2"
  }
},
```

## <a name="view-and-query-logs"></a>Zobrazení a dotazování protokolů

Dříve v tomto článku jste nakonfigurovali typy protokolů pro ukládání a určení jejich umístění úložiště.

K řešení potíží a generování přehledů z těchto protokolů můžete vytvářet **vlastní dotazy**. Chcete-li začít, můžete také využít několik ukázkových dotazů, které vám poskytla služba, což řeší běžné otázky, které mohou zákazníci mít o své instanci.

Tady je postup dotazování protokolů vaší instance.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte k instanci digitálních vláken Azure. Můžete ji najít zadáním jejího názvu do panelu hledání na portálu. 

2. V nabídce vyberte **protokoly** a otevřete stránku dotaz na protokol. Stránka se otevře v okně s názvem *dotazy*.

    :::image type="content" source="media/troubleshoot-diagnostics/logs.png" alt-text="Snímek obrazovky se stránkou protokolů pro instanci digitálních vláken Azure Je překrytý pomocí okna dotazů, které zobrazuje předem sestavené dotazy pojmenované po různých možnostech protokolu, jako je latence rozhraní API DigitalTwin a latence rozhraní API modelu." lightbox="media/troubleshoot-diagnostics/logs.png":::

    Jedná se o předem připravené Ukázkové dotazy napsané pro různé protokoly. Můžete vybrat jeden z dotazů a načíst ho do editoru dotazů a spustit ho pro zobrazení těchto protokolů vaší instance.

    Můžete také zavřít okno *dotazy* , aniž byste museli spustit cokoli, a přejít přímo na stránku Editor dotazů, kde můžete napsat nebo upravit vlastní kód dotazu.

3. Po ukončení okna *dotazů* uvidíte hlavní stránku editoru dotazů. Tady můžete zobrazit a upravit text ukázkových dotazů nebo napsat vlastní dotazy od začátku.
    :::image type="content" source="media/troubleshoot-diagnostics/logs-query.png" alt-text="Snímek obrazovky se stránkou protokolů pro instanci digitálních vláken Azure Okno dotazy zmizelo a místo toho je zobrazen seznam různých protokolů, podokno úprav s upravitelným kódem dotazu a podokno s historií dotazů." lightbox="media/troubleshoot-diagnostics/logs-query.png":::

    V levém podokně 
    - Karta *tabulky* zobrazuje různé [Kategorie protokolů](#log-categories) digitálních vláken Azure, které jsou k dispozici pro použití ve vašich dotazech. 
    - Karta *dotazy* obsahuje příklady dotazů, které lze načíst do editoru.
    - Karta *Filtr* umožňuje přizpůsobit filtrované zobrazení dat vrácených dotazem.

Podrobnější informace o dotazech protokolů a o tom, jak je napsat, najdete [*v tématu Přehled dotazů protokolu v Azure monitor*](../azure-monitor/logs/log-query-overview.md).

## <a name="next-steps"></a>Další kroky

* Další informace o konfiguraci diagnostiky najdete v tématu [*shromažďování a využívání dat protokolů z prostředků Azure*](../azure-monitor/essentials/platform-logs-overview.md).
* Informace o metrikách digitálních vláken Azure najdete v tématu [*řešení potíží: zobrazení metrik pomocí Azure monitor*](troubleshoot-metrics.md).
* Informace o tom, jak povolit výstrahy pro metriky, najdete v tématu [*řešení potíží: nastavení výstrah*](troubleshoot-alerts.md).