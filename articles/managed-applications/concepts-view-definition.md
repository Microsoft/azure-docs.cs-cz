---
title: Přehled zobrazit definici spravované aplikace Azure | Dokumentace Microsoftu
description: Popisuje pojem vytváří definice zobrazení pro spravované aplikace Azure.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: 6735787f9b43f98ab611584f3c7191c9f927dbc2
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478742"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Zobrazení definice artefaktů ve spravovaných aplikací Azure

Definice zobrazení je volitelné artefakt do spravovaných aplikací Azure. Umožňuje přizpůsobit stránce s přehledem a přidání více zobrazení, jako jsou například metriky a vlastní prostředky.

Tento článek obsahuje přehled artefaktů definice zobrazení a její možnosti.

## <a name="view-definition-artifact"></a>Zobrazení definičních artefaktů

Musí mít název artefaktu definice zobrazení **viewDefinition.json** a je umístěná na stejné úrovni jako **createUiDefinition.json** a **mainTemplate.json** v ZIP balíček, který vytvoří definice spravované aplikace. Zjistěte, jak vytvořit balíček .zip a publikovat definici spravované aplikace, najdete v článku [publikovat definici spravované aplikace Azure](publish-managed-app-definition-quickstart.md)

## <a name="view-definition-schema"></a>Zobrazení definice schématu

**ViewDefinition.json** soubor obsahuje pouze jeden prvek nejvyšší úrovně `views` vlastnost, která je pole zobrazení. Každé zobrazení se zobrazuje v uživatelském rozhraní spravované aplikace jako samostatné nabídky položky v tabulce obsahu. Každé zobrazení obsahuje `kind` vlastnost, která nastaví typ zobrazení. Musí být nastavena na jednu z následujících hodnot: [Přehled](#overview), [metriky](#metrics), [CustomResources](#custom-resources). Další informace najdete v tématu aktuální [schéma JSON pro viewDefinition.json](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).

Ukázka JSON pro definici zobrazení:

```json
{
    "views": [
        {
            "kind": "Overview",
            "properties": {
                "header": "Welcome to your Azure Managed Application",
                "description": "This managed application is for demo purposes only.",
                "commands": [
                    {
                        "displayName": "Test Action",
                        "path": "testAction"
                    }
                ]
            }
        },
        {
            "kind": "Metrics",
            "properties": {
                "displayName": "This is my metrics view",
                "version": "1.0.0",
                "charts": [
                    {
                        "displayName": "Sample chart",
                        "chartType": "Bar",
                        "metrics": [
                            {
                                "name": "Availability",
                                "aggregationType": "avg",
                                "resourceTagFilter": [ "tag1" ],
                                "resourceType": "Microsoft.Storage/storageAccounts",
                                "namespace": "Microsoft.Storage/storageAccounts"
                            }
                        ]
                    }
                ]
            }
        },
        {
            "kind": "CustomResources",
            "properties": {
                "displayName": "Test custom resource type",
                "version": "1.0.0",
                "resourceType": "testCustomResource",
                "createUIDefinition": { },
                "commands": [
                    {
                        "displayName": "Custom Test Action",
                        "path": "testAction"
                    },
                    {
                        "displayName": "Custom Context Action",
                        "path": "testCustomResource/testContextAction",
                        "icon": "Stop",
                        "createUIDefinition": { },
                    }
                ],
                "columns": [
                    {"key": "name", "displayName": "Name"},
                    {"key": "properties.myProperty1", "displayName": "Property 1"},
                    {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
                ]
            }
        }
    ]
}

```

## <a name="overview"></a>Přehled

`"kind": "Overview"`

Pokud zadáte v tomto zobrazení **viewDefinition.json**, přepíše výchozí stránka s přehledem ve spravované aplikaci.

```json
{
    "kind": "Overview",
    "properties": {
        "header": "Welcome to your Azure Managed Application",
        "description": "This managed application is for demo purposes only.",
        "commands": [
            {
                "displayName": "Test Action",
                "path": "testAction"
            }
        ]
    }
}
```

|Vlastnost|Požaduje se|Popis|
|---------|---------|---------|
|záhlaví|Ne|Hlavička stránka s přehledem.|
|description|Ne|Popis spravované aplikace.|
|Příkazy|Ne|Pole tlačítka Další panelu nástrojů stránky přehled najdete v článku [příkazy](#commands).|

## <a name="metrics"></a>Metriky

`"kind": "Metrics"`

Zobrazení metrik umožňuje shromažďování a agregace dat z vašich prostředků spravované aplikace v [metrik Azure monitoru](../azure-monitor/platform/data-platform-metrics.md).

```json
{
    "kind": "Metrics",
    "properties": {
        "displayName": "This is my metrics view",
        "version": "1.0.0",
        "charts": [
            {
                "displayName": "Sample chart",
                "chartType": "Bar",
                "metrics": [
                    {
                        "name": "Availability",
                        "aggregationType": "avg",
                        "resourceTagFilter": [ "tag1" ],
                        "resourceType": "Microsoft.Storage/storageAccounts",
                        "namespace": "Microsoft.Storage/storageAccounts"
                    }
                ]
            }
        ]
    }
}
```

|Vlastnost|Požaduje se|Popis|
|---------|---------|---------|
|displayName|Ne|Zobrazený název zobrazení.|
|version|Ne|Verze platforma sloužící k vykreslení zobrazení.|
|Grafy|Ano|Pole grafy na stránce metriky.|

### <a name="chart"></a>Graf

|Vlastnost|Požaduje se|Popis|
|---------|---------|---------|
|displayName|Ano|Zobrazený název grafu.|
|chartType|Ne|Vizualizaci má použít pro tento graf. Ve výchozím nastavení použije spojnicový graf. Podporované typy grafů: `Bar, Line, Area, Scatter`.|
|metrics metrik|Ano|Pole metriky k vykreslení v tomto grafu. Další informace o metrikách podporované na webu Azure portal najdete v tématu [podporované metriky ve službě Azure Monitor](../azure-monitor/platform/metrics-supported.md)|

### <a name="metric"></a>Metrika

|Vlastnost|Požaduje se|Popis|
|---------|---------|---------|
|name|Ano|Název metriky.|
|aggregationType|Ano|Typ agregace pro tuto metriku. Podporované typy agregace: `none, sum, min, max, avg, unique, percentile, count`|
|– obor názvů|Ne|Další informace o použití při určování správné metriky zprostředkovatele.|
|resourceTagFilter|Ne|Zdroj značky pole (oddělené s `or` word) pro metriky, které bude zobrazen. Se vztahuje na filtr typu prostředku.|
|resourceType|Ano|Typ prostředku, pro který bude zobrazen metriky.|

## <a name="custom-resources"></a>Vlastní prostředky

`"kind": "CustomResources"`

Můžete definovat více zobrazení tohoto typu. Každé zobrazení představuje **jedinečný** vlastní prostředek typu z vlastního zprostředkovatele jste definovali v **mainTemplate.json**. Úvod do vlastních poskytovatelů najdete v tématu [přehled Azure Vlastní zprostředkovatelé Preview](custom-providers-overview.md).

V tomto zobrazení, které můžete provádět GET, PUT, DELETE a ZVEŘEJŇOVAT operace pro vaše vlastní typ prostředku. Operace POST může být globální vlastní akce nebo vlastní akce v kontextu vašeho vlastního typu prostředku.

```json
{
    "kind": "CustomResources",
    "properties": {
        "displayName": "Test custom resource type",
        "version": "1.0.0",
        "resourceType": "testCustomResource",
        "createUIDefinition": { },
        "commands": [
            {
                "displayName": "Custom Test Action",
                "path": "testAction"
            },
            {
                "displayName": "Custom Context Action",
                "path": "testCustomResource/testContextAction",
                "icon": "Stop",
                "createUIDefinition": { },
            }
        ],
        "columns": [
            {"key": "name", "displayName": "Name"},
            {"key": "properties.myProperty1", "displayName": "Property 1"},
            {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
        ]
    }
}
```

|Vlastnost|Požaduje se|Popis|
|---------|---------|---------|
|displayName|Ano|Zobrazený název zobrazení. Název by měl být **jedinečný** pro každé zobrazení CustomResources ve vaší **viewDefinition.json**.|
|version|Ne|Verze platforma sloužící k vykreslení zobrazení.|
|resourceType|Ano|Typ vlastního prostředku. Musí být **jedinečný** vlastního typu prostředku vlastního poskytovatele.|
|createUIDefinition|Ne|Vytvoření uživatelského rozhraní definice schématu pro vytvoření vlastního prostředku příkazu. Úvod do vytváření definic uživatelského rozhraní, naleznete v tématu [Začínáme s funkcí CreateUiDefinition](create-uidefinition-overview.md)|
|Příkazy|Ne|Zobrazit pole Další tlačítka CustomResources zobrazení [příkazy](#commands).|
|Sloupce|Ne|Pole sloupců vlastního prostředku. Pokud není definován `name` sloupce ukázány ve výchozím nastavení. Sloupec musí mít `"key"` a `"displayName"`. Pro klíč zadejte klíč vlastnosti chcete zobrazit v zobrazení. Pokud je vnořený, použít tečku jako oddělovač, například `"key": "name"` nebo `"key": "properties.property1"`. Zobrazovaný název zadejte zobrazovaný název vlastnosti, která se zobrazí v zobrazení. Můžete také zadat `"optional"` vlastnost. Pokud nastavenou na hodnotu true, sloupce skryté v zobrazení ve výchozím nastavení.|

## <a name="commands"></a>Příkazy

Příkazy je pole Další tlačítka, které jsou zobrazeny na stránce. Každý příkaz představuje akci po z vašeho vlastního zprostředkovatele Azure definované v **mainTemplate.json**. Úvod do vlastních poskytovatelů najdete v tématu [Přehled vlastních poskytovatelů Azure](custom-providers-overview.md).

```json
{
    "commands": [
        {
            "displayName": "Start Test Action",
            "path": "testAction",
            "icon": "Start",
            "createUIDefinition": { }
        },
    ]
}
```

|Vlastnost|Požaduje se|Popis|
|---------|---------|---------|
|displayName|Ano|Zobrazovaný název příkazové tlačítko.|
|path|Ano|Název akce vlastního zprostředkovatele. Akce musí být definován v **mainTemplate.json**.|
|Ikona|Ne|Ikona kliknutí na příkazové tlačítko. Seznam podporovaných ikony je definována v [schématu JSON](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|createUIDefinition|Ne|Vytvoření schématu definice uživatelského rozhraní pro příkaz. Úvod do vytváření definic uživatelského rozhraní, naleznete v tématu [Začínáme s funkcí CreateUiDefinition](create-uidefinition-overview.md).|

## <a name="next-steps"></a>Další postup

- Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací Azure](overview.md).
- Úvod do vlastních poskytovatelů najdete v tématu [Přehled vlastních poskytovatelů Azure](custom-providers-overview.md).
