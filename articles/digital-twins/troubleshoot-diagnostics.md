---
title: Nastavení diagnostiky
titleSuffix: Azure Digital Twins
description: Viz jak povolit protokolování s nastavením diagnostiky.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: fc397b6d6beb719e11dc3959bbcf4d75c08a8dda
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88723924"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Řešení potíží se službou Azure Digital nevláken: protokolování diagnostiky

Digitální vlákna Azure shromažďuje [metriky](troubleshoot-metrics.md) pro vaši instanci služby, která poskytuje informace o stavu vašich prostředků. Tyto metriky můžete použít k vyhodnocení celkového stavu služby digitálních vláken Azure a prostředků, které jsou k ní připojené. Tyto údaje zaměřené na uživatele vám pomůžou zjistit, co se týká vašich digitálních vláken Azure, a pomáhat s analýzou příčin potíží, aniž byste museli kontaktovat podporu Azure.

V tomto článku se dozvíte, jak zapnout **protokolování diagnostiky** pro data metrik z instance digitálního vlákna Azure. Tyto protokoly můžete použít k usnadnění odstraňování potíží se službou a ke konfiguraci nastavení diagnostiky pro odesílání metriky digitálních vláken Azure do různých míst určení. Další informace o těchto nastaveních najdete v tématu [*Vytvoření nastavení diagnostiky pro odesílání protokolů a metrik platforem do různých umístění*](../azure-monitor/platform/diagnostic-settings.md).

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Zapnout nastavení diagnostiky pomocí Azure Portal

Tady je postup povolení nastavení diagnostiky instance digitálního vlákna Azure:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte k instanci digitálních vláken Azure. Můžete ji najít zadáním jejího názvu do panelu hledání na portálu. 

2. V nabídce vyberte **nastavení diagnostiky** a pak **přidejte nastavení diagnostiky**.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Snímek obrazovky zobrazující stránku nastavení diagnostiky a tlačítko, které se má přidat":::

3. Na následující stránce zadejte následující hodnoty:
     * **Název nastavení diagnostiky**: zadejte název diagnostického nastavení.
     * **Podrobnosti kategorie**: vyberte operace, které chcete monitorovat, a zaškrtněte políčka pro povolení diagnostiky pro tyto operace. Operace, které nastavení diagnostiky mohou hlásit:
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        Další podrobnosti o těchto možnostech najdete v části [*Podrobnosti o kategorii*](#category-details) níže.
     * **Podrobnosti cíle**: vyberte, kam chcete protokoly odesílat. Můžete vybrat libovolnou kombinaci tří možností:
        - Odeslání do Log Analytics
        - Archivovat v účtu úložiště
        - Streamovat do centra událostí

        Pokud jsou nutné pro výběr cíle, bude vám pravděpodobně požádáno, abyste doplnili další podrobnosti.  
    
4. Uložte nová nastavení. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Snímek obrazovky zobrazující stránku nastavení diagnostiky a tlačítko, které se má přidat":::

Nové nastavení se projeví přibližně po dobu 10 minut. Po této konfiguraci se protokoly zobrazí na stránce **nastavení diagnostiky na stránce nastavení diagnostiky** pro vaši instanci. 

## <a name="category-details"></a>Podrobnosti kategorie

Tady jsou další podrobnosti o kategoriích protokolů, které se dají vybrat v části **Podrobnosti o kategorii** při nastavování nastavení diagnostiky.

| Kategorie protokolu | Description |
| --- | --- |
| ADTModelsOperation | Protokolovat všechna volání rozhraní API týkající se modelů |
| ADTQueryOperation | Protokolování všech volání rozhraní API vztahujících se k dotazům |
| ADTEventRoutesOperation | Protokolujte všechna volání rozhraní API, která souvisí s trasami událostí, a také výstup událostí z digitálních vláken Azure do služby koncového bodu, jako je Event Grid, Event Hubs a Service Bus |
| ADTDigitalTwinsOperation | Protokolovat všechna volání rozhraní API týkající se digitálních vláken Azure |

Každá kategorie protokolu se skládá z operací zápisu, čtení, odstranění a akce.  Tato mapa pro REST API volání následujícím způsobem:

| Typ události | REST API operace |
| --- | --- |
| Zápis | Vložit a opravit |
| Číst | GET |
| Odstranit | DELETE |
| Akce | POST |

Tady je vyčerpávající seznam operací a odpovídajících [digitálních vláken Azure REST API volání](https://docs.microsoft.com/rest/api/azure-digitaltwins/) , která se protokolují v jednotlivých kategoriích. 

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

`ADTDigitalTwinsOperation`, `ADTModelsOperation` a `ADTQueryOperation` používejte konzistentní schéma protokolu rozhraní API; `ADTEventRoutesOperation` má vlastní samostatné schéma.

### <a name="api-log-schemas"></a>Schémata protokolů rozhraní API

Toto schéma protokolu je konzistentní pro `ADTDigitalTwinsOperation` , `ADTModelsOperation` a `ADTQueryOperation` . Obsahuje informace týkající se volání rozhraní API k instanci digitálních vláken Azure.

Tady jsou popisy polí a vlastností pro protokoly rozhraní API.

| Název pole | Datový typ | Popis |
|-----|------|-------------|
| `Time` | DateTime | Datum a čas, kdy k této události došlo, v UTC |
| `ResourceID` | Řetězec | ID prostředku Azure Resource Manager prostředku, ve kterém se událost uskutečnila |
| `OperationName` | Řetězec  | Typ akce prováděné během události |
| `OperationVersion` | Řetězec | Verze rozhraní API využitá během události |
| `Category` | Řetězec | Typ prostředku, který se emituje. |
| `ResultType` | Řetězec | Výsledek události |
| `ResultSignature` | Řetězec | Stavový kód HTTP události |
| `ResultDescription` | Řetězec | Další podrobnosti o události |
| `DurationMs` | Řetězec | Jak dlouho trvalo provádění události v milisekundách |
| `CallerIpAddress` | Řetězec | Maskovaná zdrojová IP adresa pro událost |
| `CorrelationId` | Identifikátor GUID | Pro událost se zadal jedinečný identifikátor zákazníka. |
| `Level` | Řetězec | Závažnost protokolování události |
| `Location` | Řetězec | Oblast, ve které byla událost provedena |
| `RequestUri` | Identifikátor URI | Koncový bod využíval během události |

Níže jsou uvedeny příklady tělo JSON pro tyto typy protokolů.

#### <a name="adtdigitaltwinsoperation"></a>ADTDigitalTwinsOperation

```json
{
  "time": "2020-03-14T21:11:14.9918922Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/digitaltwins/write",
  "operationVersion": "2020-05-31-preview",
  "category": "DigitalTwinOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "314",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "2f6a8e64-94aa-492a-bc31-16b9f0b16ab3",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/digitaltwins/factory-58d81613-2e54-4faa-a930-d980e6e2a884?api-version=2020-05-31-preview"
}
```

#### <a name="adtmodelsoperation"></a>ADTModelsOperation

```json
{
  "time": "2020-10-29T21:12:24.2337302Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/models/write",
  "operationVersion": "2020-05-31-preview",
  "category": "ModelsOperation",
  "resultType": "Success",
  "resultSignature": "201",
  "resultDescription": "",
  "durationMs": "935",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "9dcb71ea-bb6f-46f2-ab70-78b80db76882",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/Models?api-version=2020-05-31-preview",
}
```

#### <a name="adtqueryoperation"></a>ADTQueryOperation

```json
{
  "time": "2020-12-04T21:11:44.1690031Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/query/action",
  "operationVersion": "2020-05-31-preview",
  "category": "QueryOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "255",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "1ee2b6e9-3af4-4873-8c7c-1a698b9ac334",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/query?api-version=2020-05-31-preview",
}
```

### <a name="egress-log-schemas"></a>Schémata protokolu odchozího přenosu dat

Toto je schéma pro `ADTEventRoutesOperation` protokoly. Obsahují podrobnosti týkající se výjimek a operace rozhraní API kolem koncových bodů, které se připojují k instanci digitálních vláken Azure.

|Název pole | Datový typ | Popis |
|-----|------|-------------|
| `Time` | DateTime | Datum a čas, kdy k této události došlo, v UTC |
| `ResourceId` | Řetězec | ID prostředku Azure Resource Manager prostředku, ve kterém se událost uskutečnila |
| `OperationName` | Řetězec  | Typ akce prováděné během události |
| `Category` | Řetězec | Typ prostředku, který se emituje. |
| `ResultDescription` | Řetězec | Další podrobnosti o události |
| `Level` | Řetězec | Závažnost protokolování události |
| `Location` | Řetězec | Oblast, ve které byla událost provedena |
| `EndpointName` | Řetězec | Název výstupního koncového bodu vytvořeného v rámci digitálních vláken Azure |

Níže jsou uvedeny příklady tělo JSON pro tyto typy protokolů.

#### <a name="adteventroutesoperation"></a>ADTEventRoutesOperation

```json
{
  "time": "2020-11-05T22:18:38.0708705Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/eventroutes/action",
  "category": "EventRoutesOperation",
  "resultDescription": "Unable to send EventGrid message to [my-event-grid.westus-1.eventgrid.azure.net] for event Id [f6f45831-55d0-408b-8366-058e81ca6089].",
  "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
  "level": "3",
  "location": "southcentralus",
  "properties": {
    "endpointName": "endpointEventGridInvalidKey"
  }
}
```

## <a name="next-steps"></a>Další kroky

* Další informace o konfiguraci diagnostiky najdete v tématu [*shromažďování a využívání dat protokolů z prostředků Azure*](../azure-monitor/platform/platform-logs-overview.md).
* Informace o metrikách digitálních vláken Azure najdete v tématu [*řešení potíží: zobrazení metrik pomocí Azure monitor*](troubleshoot-metrics.md).
* Informace o tom, jak povolit výstrahy pro metriky, najdete v tématu [*řešení potíží: nastavení výstrah*](troubleshoot-alerts.md).
