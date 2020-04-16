---
title: Přehled definice zobrazení
description: Popisuje koncept vytváření definice zobrazení pro spravované aplikace Azure.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: 7a3d2234a140d1fb2eede50e3fe2eef5575da648
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81391691"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Zobrazení artefaktu definice ve spravovaných aplikacích Azure

Definice zobrazení je volitelný artefakt ve spravovaných aplikacích Azure. Umožňuje přizpůsobit stránku přehledu a přidat další zobrazení, jako jsou metriky a vlastní prostředky.

Tento článek obsahuje přehled artefaktu definice zobrazení a jeho možností.

## <a name="view-definition-artifact"></a>Zobrazení definičních artefaktů

Artefakt definice zobrazení musí být pojmenován **viewDefinition.json** a umístěn na stejné úrovni jako **createUiDefinition.json** a **mainTemplate.json** v balíčku .zip, který vytvoří definici spravované aplikace. Informace o tom, jak vytvořit balíček ZIP a publikovat definici spravované aplikace, najdete [v tématu Publikování definice spravované aplikace Azure](publish-service-catalog-app.md)

## <a name="view-definition-schema"></a>Zobrazit schéma definice

Soubor **viewDefinition.json** má pouze `views` jednu vlastnost nejvyšší úrovně, což je pole zobrazení. Každé zobrazení je zobrazeno v uživatelském rozhraní spravované aplikace jako samostatná položka nabídky v obsahu. Každé zobrazení `kind` má vlastnost, která nastavuje typ zobrazení. Musí být nastavena na jednu z následujících hodnot: [Přehled](#overview), [Metriky](#metrics), [Vlastní zdroje](#custom-resources), [Přidružení](#associations). Další informace naleznete v [aktuálním schématu JSON pro viewDefinition.json](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).

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

Pokud toto zobrazení zadáte v **souboru viewDefinition.json**, přepíše výchozí stránku Přehled ve spravované aplikaci.

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
|záhlaví|Ne|Záhlaví stránky přehledu.|
|description|Ne|Popis spravované aplikace.|
|příkazy|Ne|Pole dalších tlačítek panelu nástrojů na stránce přehledu naleznete [v tématu příkazy](#commands).|

![Přehled](./media/view-definition/overview.png)

## <a name="metrics"></a>Metriky

`"kind": "Metrics"`

Zobrazení metrik umožňuje shromažďovat a agregovat data z prostředků spravovaných aplikací v [metrikách Azure Monitor](../../azure-monitor/platform/data-platform-metrics.md).

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
|version|Ne|Verze platformy slouží k vykreslení zobrazení.|
|Grafy|Ano|Pole grafů stránky metriky.|

### <a name="chart"></a>Graf

|Vlastnost|Požaduje se|Popis|
|---------|---------|---------|
|displayName|Ano|Zobrazený název grafu.|
|chartTyp|Ne|Vizualizace, která se má použít pro tento graf. Ve výchozím nastavení používá spojnicový graf. Podporované typy `Bar, Line, Area, Scatter`grafů: .|
|metriky|Ano|Pole metriky vykreslit v tomto grafu. Další informace o metrikách podporovaných na Webu Azure Portal najdete v tématu [Podporované metriky s Azure Monitorem.](../../azure-monitor/platform/metrics-supported.md)|

### <a name="metric"></a>Metrika

|Vlastnost|Požaduje se|Popis|
|---------|---------|---------|
|jméno|Ano|Název metriky.|
|agregaceTyp|Ano|Typ agregace, který se má použít pro tuto metriku. Podporované typy agregace:`none, sum, min, max, avg, unique, percentile, count`|
|namespace|Ne|Další informace, které se mají použít při určování správného zprostředkovatele metrik.|
|resourceTagFilter|Ne|Pole značek prostředků (bude `or` odděleno slovem), pro které by se zobrazovaly metriky. Použije se nad filtrem typu prostředku.|
|resourceType|Ano|Typ prostředku, pro který by se měly zobrazit metriky.|

![Metriky](./media/view-definition/metrics.png)

## <a name="custom-resources"></a>Vlastní prostředky

`"kind": "CustomResources"`

Můžete definovat více zobrazení tohoto typu. Každé zobrazení představuje **jedinečný** vlastní typ prostředku z vlastního zprostředkovatele, který jste definovali v **mainTemplate.json**. Úvod k vlastním zprostředkovatelům najdete v [tématu Přehled náhledu vlastních zprostředkovatelů Azure](../custom-providers/overview.md).

V tomto zobrazení můžete provádět operace GET, PUT, DELETE a POST pro váš vlastní typ prostředku. Operace POST mohou být globální vlastní akce nebo vlastní akce v kontextu vašeho vlastního typu prostředku.

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
|displayName|Ano|Zobrazený název zobrazení. Název by měl být **jedinečný** pro každé zobrazení CustomResources ve vašem **viewDefinition.json**.|
|version|Ne|Verze platformy slouží k vykreslení zobrazení.|
|resourceType|Ano|Vlastní typ prostředku. Musí se jednat o **jedinečný** typ vlastního prostředku vlastního zprostředkovatele.|
|Ikonu|Ne|Ikona zobrazení. Seznam příkladů ikon je definován v [JSON schématu](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|createUIDefinition|Ne|Vytvořte schéma definice ui pro vytvoření příkazu vlastní prostředek. Úvod k vytváření definic umítek o uznané, najdete [v tématu Začínáme s CreateUiDefinition](create-uidefinition-overview.md)|
|příkazy|Ne|Pole dalších tlačítek panelu nástrojů zobrazení CustomResources naleznete v [tématu příkazy](#commands).|
|columns|Ne|Pole sloupců vlastního prostředku. Pokud není `name` definováno sloupec se zobrazí ve výchozím nastavení. Sloupec musí `"key"` mít `"displayName"`a . Pro klíč zadejte klíč vlastnosti, která se má zobrazit v zobrazení. Pokud je vnořeno, použijte tečku `"key": "name"` jako `"key": "properties.property1"`oddělovač, například nebo . Pro zobrazovaný název zadejte zobrazovaný název vlastnosti, která se má zobrazit v zobrazení. Můžete také poskytnout `"optional"` nemovitost. Pokud je sloupec nastaven na hodnotu true, je ve výchozím nastavení skrytý v zobrazení.|

![Vlastní zdroje](./media/view-definition/customresources.png)

## <a name="commands"></a>Příkazy

Příkazy je pole dalších tlačítek panelu nástrojů, které jsou zobrazeny na stránce. Každý příkaz představuje akci POST od vlastního zprostředkovatele Azure definovaného v **mainTemplate.json**. Úvod k vlastním poskytovatelům najdete v [tématu Přehled vlastních zprostředkovatelů Azure](../custom-providers/overview.md).

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
|displayName|Ano|Zobrazený název příkazového tlačítka.|
|cesta|Ano|Název akce vlastního zprostředkovatele. Akce musí být definována v **mainTemplate.json**.|
|Ikonu|Ne|Ikona příkazového tlačítka. Seznam příkladů ikon je definován v [JSON schématu](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|createUIDefinition|Ne|Vytvořte schéma definice ui pro příkaz. Úvod k vytváření definic ui naleznete [v tématu Začínáme s CreateUiDefinition](create-uidefinition-overview.md).|

## <a name="associations"></a>Sdružení

`"kind": "Associations"`

Můžete definovat více zobrazení tohoto typu. Toto zobrazení umožňuje propojit existující prostředky se spravovanou aplikací prostřednictvím vlastního zprostředkovatele, který jste definovali v **souboru mainTemplate.json**. Úvod k vlastním zprostředkovatelům najdete v [tématu Přehled náhledu vlastních zprostředkovatelů Azure](../custom-providers/overview.md).

V tomto zobrazení můžete rozšířit existující `targetResourceType`prostředky Azure na základě . Je-li zdroj vybrán, vytvoří požadavek na připojení **pro veřejného** vlastního zprostředkovatele, který může na prostředek použít vedlejší účinek. 

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
|displayName|Ano|Zobrazený název zobrazení. Název by měl být **jedinečný** pro každé zobrazení přidružení v **zobrazeníDefinition.json**.|
|version|Ne|Verze platformy slouží k vykreslení zobrazení.|
|targetResourceType|Ano|Cílový typ prostředku. Toto je typ prostředku, který se zobrazí pro připojení zdroje.|
|createUIDefinition|Ne|Vytvořte schéma definice ui pro vytvoření příkazu prostředku přidružení. Úvod k vytváření definic umítek o uznané, najdete [v tématu Začínáme s CreateUiDefinition](create-uidefinition-overview.md)|

## <a name="looking-for-help"></a>Hledání pomoci

Pokud máte dotazy týkající se spravovaných aplikací Azure, zkuste se zeptat na [přetečení zásobníku](https://stackoverflow.com/questions/tagged/azure-managedapps). Podobná otázka již mohla být položena a zodpovězena, takže nejprve zkontrolujte před odesláním. Přidejte `azure-managedapps` značku, abyste získali rychlou odpověď!

## <a name="next-steps"></a>Další kroky

- Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací Azure](overview.md).
- Úvod k vlastním poskytovatelům najdete v [tématu Přehled vlastních zprostředkovatelů Azure](../custom-providers/overview.md).
- Vytvoření spravované aplikace Azure s vlastními zprostředkovateli Azure najdete [v tématu Kurz: Vytvoření spravované aplikace s akcemi a typy prostředků vlastního zprostředkovatele.](tutorial-create-managed-app-with-custom-provider.md)
