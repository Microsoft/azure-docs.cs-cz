---
title: Přehled definice zobrazení
description: Popisuje koncept vytváření definice zobrazení pro Azure Managed Applications.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: bff846b4b64778d5e40ea7f08f88faf3dde81d9e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91371605"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Zobrazit artefakt definice v Azure Managed Applications

Definice zobrazení je volitelný artefakt v Azure Managed Applications. Umožňuje přizpůsobit stránku s přehledem a přidat další zobrazení, jako jsou metriky a vlastní prostředky.

Tento článek poskytuje přehled artefaktů definice zobrazení a jeho schopností.

## <a name="view-definition-artifact"></a>Zobrazení definičních artefaktů

Artefakt definice zobrazení musí být pojmenován **viewDefinition.js** a umístěn na stejné úrovni jako **createUiDefinition.jsna** a **mainTemplate.js** v balíčku. zip, který vytváří definici spravované aplikace. Informace o vytvoření balíčku. zip a publikování definice spravované aplikace najdete v tématu věnovaném [publikování definice spravované aplikace Azure](publish-service-catalog-app.md) .

## <a name="view-definition-schema"></a>Zobrazit schéma definice

**viewDefinition.jsv** souboru má pouze jednu vlastnost nejvyšší úrovně `views` , což je pole zobrazení. Každé zobrazení se zobrazí v uživatelském rozhraní spravované aplikace jako samostatná položka nabídky v obsahu. Každé zobrazení má `kind` vlastnost, která nastavuje typ zobrazení. Musí být nastavená na jednu z následujících hodnot: [Přehled](#overview), [metriky](#metrics), [CustomResources](#custom-resources)a [přidružení](#associations). Další informace najdete v tématu aktuální [schéma JSON pro viewDefinition.jsv](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).

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
        },
        {
            "kind": "Associations",
            "properties": {
                "displayName": "Test association resource type",
                "version": "1.0.0",
                "targetResourceType": "Microsoft.Compute/virtualMachines",
                "createUIDefinition": { }
            }
        }
    ]
}
```

## <a name="overview"></a>Přehled

`"kind": "Overview"`

Když toto zobrazení zadáte v **viewDefinition.jsna**, potlačí se výchozí stránka přehledu ve spravované aplikaci.

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
|header|No|Záhlaví stránky s přehledem|
|Popis|No|Popis spravované aplikace.|
|příkazy|No|Pole dalších tlačítek panelu nástrojů na stránce Přehled naleznete v tématu [příkazy](#commands).|

![Snímek obrazovky znázorňující přehled spravované aplikace pomocí testovacího ovládacího prvku akce pro spuštění ukázkové aplikace.](./media/view-definition/overview.png)

## <a name="metrics"></a>Metriky

`"kind": "Metrics"`

Zobrazení metrik umožňuje shromažďovat a agregovat data z prostředků spravovaných aplikací v [Azure Monitorch metrik](../../azure-monitor/platform/data-platform-metrics.md).

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
|displayName|No|Zobrazený nadpis zobrazení|
|verze|No|Verze platformy použité k vykreslení zobrazení|
|spojnic|Yes|Pole grafů stránky metrik.|

### <a name="chart"></a>Graf

|Vlastnost|Požaduje se|Popis|
|---------|---------|---------|
|displayName|Yes|Zobrazený Nadpis grafu|
|chartType|No|Vizualizace, která se má použít pro tento graf Ve výchozím nastavení používá spojnicový graf. Podporované typy grafů: `Bar, Line, Area, Scatter` .|
|metriky|Yes|Pole metrik, která se mají vykreslovat v tomto grafu Další informace o metrikách podporovaných v Azure Portal najdete v tématu [podporované metriky s Azure monitor](../../azure-monitor/platform/metrics-supported.md)|

### <a name="metric"></a>Metrika

|Vlastnost|Požaduje se|Popis|
|---------|---------|---------|
|name|Yes|Název metriky.|
|aggregationType|Yes|Typ agregace, který se má použít pro tuto metriku Podporované typy agregace: `none, sum, min, max, avg, unique, percentile, count`|
|namespace|No|Další informace, které se mají použít při určování správného poskytovatele metrik.|
|resourceTagFilter|No|Pole značky prostředků (bude odděleno `or` slovem), pro které se zobrazila metrika. Použije se nad filtrem typu prostředku.|
|resourceType|Yes|Typ prostředku, pro který se budou zobrazovat metriky|

![Snímek obrazovky se zobrazí stránka monitorování s názvem moje metrika pro spravovanou aplikaci.](./media/view-definition/metrics.png)

## <a name="custom-resources"></a>Vlastní prostředky

`"kind": "CustomResources"`

Můžete definovat více zobrazení tohoto typu. Každé zobrazení představuje **jedinečný** vlastní typ prostředku z vlastního poskytovatele, kterého jste definovali v **mainTemplate.js**. Úvod k vlastním poskytovatelům najdete v tématu [Přehled vlastních zprostředkovatelů Azure ve verzi Preview](../custom-providers/overview.md).

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
|displayName|Yes|Zobrazený nadpis zobrazení Název by měl být **jedinečný** pro každé zobrazení CustomResources v **viewDefinition.js**v.|
|verze|No|Verze platformy použité k vykreslení zobrazení|
|resourceType|Yes|Vlastní typ prostředku. Musí se jednat o **jedinečný** vlastní typ prostředku vašeho vlastního poskytovatele.|
|ikona|No|Ikona zobrazení Seznam ukázkových ikon je definován ve [schématu JSON](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|createUIDefinition|No|Vytvořte schéma definice uživatelského rozhraní pro příkaz vytvořit vlastní prostředek. Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md) .|
|příkazy|No|Pole dalších tlačítek panelu nástrojů v zobrazení CustomResources, viz [příkazy](#commands).|
|columns|No|Pole sloupců vlastního prostředku Pokud není definován, `name` sloupec se zobrazí ve výchozím nastavení. Sloupec musí mít `"key"` a `"displayName"` . V poli klíč zadejte klíč vlastnosti, který se má zobrazit v zobrazení. Pokud je vnořený, použijte jako oddělovač znak tečky, například `"key": "name"` nebo `"key": "properties.property1"` . Do pole Zobrazovaný název zadejte zobrazovaný název vlastnosti, která se má zobrazit v zobrazení. Můžete také zadat `"optional"` vlastnost. Při nastavení na hodnotu true je sloupec v zobrazení ve výchozím nastavení skrytý.|

![Snímek obrazovky ukazuje stránku prostředků s názvem test vlastního typu prostředku a akci vlastního kontextu ovládacího prvku.](./media/view-definition/customresources.png)

## <a name="commands"></a>Příkazy

Příkazy jsou pole dalších tlačítek panelu nástrojů, která se zobrazují na stránce. Každý příkaz představuje akci POST od vlastního poskytovatele Azure definovaného v **mainTemplate.js**. Úvod k vlastním poskytovatelům najdete v tématu [Přehled vlastních zprostředkovatelů Azure](../custom-providers/overview.md).

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
|displayName|Yes|Zobrazované jméno příkazového tlačítka|
|program|Yes|Název akce vlastního zprostředkovatele. Akce musí být definována v **mainTemplate.js**.|
|ikona|No|Ikona příkazového tlačítka Seznam ukázkových ikon je definován ve [schématu JSON](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|createUIDefinition|No|Vytvořte schéma definice uživatelského rozhraní pro příkaz. Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).|

## <a name="associations"></a>Přidružení

`"kind": "Associations"`

Můžete definovat více zobrazení tohoto typu. Toto zobrazení umožňuje propojit existující prostředky se spravovanou aplikací prostřednictvím vlastního poskytovatele, kterého jste definovali v **mainTemplate.js**. Úvod k vlastním poskytovatelům najdete v tématu [Přehled vlastních zprostředkovatelů Azure ve verzi Preview](../custom-providers/overview.md).

V tomto zobrazení můžete roztáhnout existující prostředky Azure na základě `targetResourceType` . Když se vybere prostředek, vytvoří se žádost o registraci **veřejnému** poskytovateli, který může u prostředku použít vedlejší efekt. 

```json
{
    "kind": "Associations",
    "properties": {
        "displayName": "Test association resource type",
        "version": "1.0.0",
        "targetResourceType": "Microsoft.Compute/virtualMachines",
        "createUIDefinition": { }
    }
}
```

|Vlastnost|Požaduje se|Popis|
|---------|---------|---------|
|displayName|Yes|Zobrazený nadpis zobrazení Název by měl být **jedinečný** pro každé zobrazení přidružení v **viewDefinition.js**v.|
|verze|No|Verze platformy použité k vykreslení zobrazení|
|targetResourceType|Yes|Cílový typ prostředku. Jedná se o typ prostředku, který se zobrazí pro registraci prostředku.|
|createUIDefinition|No|Vytvořte schéma definice uživatelského rozhraní pro příkaz vytvořit prostředek přidružení. Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md) .|

## <a name="looking-for-help"></a>Hledáte nápovědu

Pokud máte dotazy týkající se Azure Managed Applications, zkuste požádat o [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-managedapps). Podobná otázka již mohla být požádána o zodpovězení a byla zodpovězena, proto nejprve před odesláním zaregistrujte. Přidejte značku `azure-managedapps` , abyste získali rychlou odpověď.

## <a name="next-steps"></a>Další kroky

- Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací Azure](overview.md).
- Úvod k vlastním poskytovatelům najdete v tématu [Přehled vlastních zprostředkovatelů Azure](../custom-providers/overview.md).
- Informace o vytvoření spravované aplikace Azure pomocí vlastních zprostředkovatelů Azure najdete v tématu [kurz: vytvoření spravované aplikace s akcemi vlastního zprostředkovatele a typy prostředků.](tutorial-create-managed-app-with-custom-provider.md)
