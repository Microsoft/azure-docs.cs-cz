---
title: Přehled definice zobrazení v Azure Managed Applications | Microsoft Docs
description: Popisuje koncept vytváření definice zobrazení pro Azure Managed Applications.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: f51dbce3c251f4e89483d925ac657aac7eb928d8
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72804076"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Zobrazit artefakt definice v Azure Managed Applications

Definice zobrazení je volitelný artefakt v Azure Managed Applications. Umožňuje přizpůsobit stránku s přehledem a přidat další zobrazení, jako jsou metriky a vlastní prostředky.

Tento článek poskytuje přehled artefaktů definice zobrazení a jeho schopností.

## <a name="view-definition-artifact"></a>Zobrazení definičních artefaktů

Artefakt definice zobrazení musí mít název **položku galerie. JSON** a umístit na stejnou úroveň jako soubor **createUiDefinition. JSON** a **mainTemplate. JSON** v balíčku. zip, který vytvoří definici spravované aplikace. Informace o vytvoření balíčku. zip a publikování definice spravované aplikace najdete v tématu věnovaném [publikování definice spravované aplikace Azure](publish-managed-app-definition-quickstart.md) .

## <a name="view-definition-schema"></a>Zobrazit schéma definice

Soubor **položku galerie. JSON** má pouze jednu vlastnost nejvyšší úrovně `views`, což je pole zobrazení. Každé zobrazení se zobrazí v uživatelském rozhraní spravované aplikace jako samostatná položka nabídky v obsahu. Každé zobrazení má vlastnost `kind`, která nastavuje typ zobrazení. Musí být nastavená na jednu z následujících hodnot: [Přehled](#overview), [metriky](#metrics), [CustomResources](#custom-resources). Další informace najdete v tématu aktuální [schéma JSON pro položku galerie. JSON](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).

Ukázka JSON pro definici zobrazení:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#",
    "contentVersion": "0.0.0.1",
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
                        "displayName": "Custom Context Action",
                        "path": "testCustomResource/testContextAction",
                        "icon": "Stop",
                        "createUIDefinition": { }
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

Pokud toto zobrazení zadáte v **položku galerie. JSON**, přepíše výchozí stránku přehledu ve spravované aplikaci.

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
|záhlaví|Ne|Záhlaví stránky s přehledem|
|description|Ne|Popis spravované aplikace.|
|příkaz|Ne|Pole dalších tlačítek panelu nástrojů na stránce Přehled naleznete v tématu [příkazy](#commands).|

![Přehled](./media/view-definition/overview.png)

## <a name="metrics"></a>Metriky

`"kind": "Metrics"`

Zobrazení metrik umožňuje shromažďovat a agregovat data z prostředků spravovaných aplikací v [Azure Monitorch metrik](../azure-monitor/platform/data-platform-metrics.md).

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
|DisplayName|Ne|Zobrazený nadpis zobrazení|
|version|Ne|Verze platformy použité k vykreslení zobrazení|
|spojnic|Ano|Pole grafů stránky metrik.|

### <a name="chart"></a>Graf

|Vlastnost|Požaduje se|Popis|
|---------|---------|---------|
|DisplayName|Ano|Zobrazený Nadpis grafu|
|chartType|Ne|Vizualizace, která se má použít pro tento graf Ve výchozím nastavení používá spojnicový graf. Podporované typy grafů: `Bar, Line, Area, Scatter`.|
|metriky|Ano|Pole metrik, která se mají vykreslovat v tomto grafu Další informace o metrikách podporovaných v Azure Portal najdete v tématu [podporované metriky s Azure monitor](../azure-monitor/platform/metrics-supported.md)|

### <a name="metric"></a>Metrika

|Vlastnost|Požaduje se|Popis|
|---------|---------|---------|
|jméno|Ano|Název metriky.|
|aggregationType|Ano|Typ agregace, který se má použít pro tuto metriku Podporované typy agregace: `none, sum, min, max, avg, unique, percentile, count`|
|Obor názvů|Ne|Další informace, které se mají použít při určování správného poskytovatele metrik.|
|resourceTagFilter|Ne|Pole značky prostředků (bude odděleno `or` Word), pro které se zobrazila metrika. Použije se nad filtrem typu prostředku.|
|resourceType|Ano|Typ prostředku, pro který se budou zobrazovat metriky|

![Metriky](./media/view-definition/metrics.png)

## <a name="custom-resources"></a>Vlastní prostředky

`"kind": "CustomResources"`

Můžete definovat více zobrazení tohoto typu. Každé zobrazení představuje **jedinečný** vlastní typ prostředku z vlastního zprostředkovatele, který jste definovali v **mainTemplate. JSON**. Úvod k vlastním poskytovatelům najdete v tématu [Přehled vlastních zprostředkovatelů Azure ve verzi Preview](custom-providers-overview.md).

V tomto zobrazení můžete provádět operace GET, PUT, DELETE a POST pro vlastní typ prostředku. Operace POST můžou být globálními vlastními akcemi nebo vlastními akcemi v kontextu vlastního typu prostředku.

```json
{
    "kind": "CustomResources",
    "properties": {
        "displayName": "Test custom resource type",
        "version": "1.0.0",
        "resourceType": "testCustomResource",
        "icon": "Polychromatic.ResourceList",
        "createUIDefinition": { },
        "commands": [
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
|DisplayName|Ano|Zobrazený nadpis zobrazení Název by měl být **jedinečný** pro každé zobrazení CustomResources ve vaší **položku galerie. JSON**.|
|version|Ne|Verze platformy použité k vykreslení zobrazení|
|resourceType|Ano|Vlastní typ prostředku. Musí se jednat o **jedinečný** vlastní typ prostředku vašeho vlastního poskytovatele.|
|Ikona|Ne|Ikona zobrazení Seznam ukázkových ikon je definován ve [schématu JSON](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|createUIDefinition|Ne|Vytvořte schéma definice uživatelského rozhraní pro příkaz vytvořit vlastní prostředek. Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md) .|
|příkaz|Ne|Pole dalších tlačítek panelu nástrojů v zobrazení CustomResources, viz [příkazy](#commands).|
|Sloupcích|Ne|Pole sloupců vlastního prostředku Pokud není definován, zobrazí se ve výchozím nastavení sloupec `name`. Sloupec musí mít `"key"` a `"displayName"`. V poli klíč zadejte klíč vlastnosti, který se má zobrazit v zobrazení. Pokud je vnořený, použijte jako oddělovač tečku, například `"key": "name"` nebo `"key": "properties.property1"`. Do pole Zobrazovaný název zadejte zobrazovaný název vlastnosti, která se má zobrazit v zobrazení. Můžete také zadat vlastnost `"optional"`. Při nastavení na hodnotu true je sloupec v zobrazení ve výchozím nastavení skrytý.|

![CustomResources](./media/view-definition/customresources.png)

## <a name="commands"></a>Příkazy

Příkazy jsou pole dalších tlačítek panelu nástrojů, která se zobrazují na stránce. Každý příkaz představuje akci POST od vlastního poskytovatele Azure definovaného v **mainTemplate. JSON**. Úvod k vlastním poskytovatelům najdete v tématu [Přehled vlastních zprostředkovatelů Azure](custom-providers-overview.md).

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
|DisplayName|Ano|Zobrazované jméno příkazového tlačítka|
|dílčí|Ano|Název akce vlastního zprostředkovatele. Akce musí být definována v **mainTemplate. JSON**.|
|Ikona|Ne|Ikona příkazového tlačítka Seznam ukázkových ikon je definován ve [schématu JSON](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|createUIDefinition|Ne|Vytvořte schéma definice uživatelského rozhraní pro příkaz. Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).|

## <a name="looking-for-help"></a>Hledáte nápovědu

Pokud máte dotazy týkající se Azure Managed Applications, zkuste požádat o [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-managedapps). Podobná otázka již mohla být požádána o zodpovězení a byla zodpovězena, proto nejprve před odesláním zaregistrujte. Přidejte `azure-managedapps` značek, abyste získali rychlou odezvu.

## <a name="next-steps"></a>Další kroky

- Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací Azure](overview.md).
- Úvod k vlastním poskytovatelům najdete v tématu [Přehled vlastních zprostředkovatelů Azure](custom-providers-overview.md).
- Informace o vytvoření spravované aplikace Azure pomocí vlastních zprostředkovatelů Azure najdete v tématu [kurz: vytvoření spravované aplikace s akcemi vlastního zprostředkovatele a typy prostředků.](tutorial-create-managed-app-with-custom-provider.md)
