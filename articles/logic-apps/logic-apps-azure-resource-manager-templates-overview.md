---
title: Přehled – automatizace nasazení pro Azure Logic Apps
description: Další informace o Azure Resource Manager šablonách pro automatizaci nasazení pro Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: a3d7386e976551d70fbbc08930b2ab5603aa5d50
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91269042"
---
# <a name="overview-automate-deployment-for-azure-logic-apps-by-using-azure-resource-manager-templates"></a>Přehled: Automatizace nasazení pro Azure Logic Apps pomocí šablon Azure Resource Manager

Až budete připraveni automatizovat vytváření a nasazení aplikace logiky, můžete svou definici pracovního postupu vaší aplikace logiky rozšířit do [šablony Azure Resource Manager](../azure-resource-manager/management/overview.md). Tato šablona definuje infrastrukturu, prostředky, parametry a další informace pro zřizování a nasazení aplikace logiky. Definováním parametrů pro hodnoty, které se liší v nasazení, označované také jako *Parametrizace*, můžete opakovaně a konzistentně nasazovat aplikace logiky na základě různých potřeb nasazení.

Například pokud nasadíte do prostředí pro vývoj, testování a produkci, pravděpodobně pro každé prostředí použijete různé připojovací řetězce. Můžete deklarovat parametry šablony, které přijímají různé připojovací řetězce, a pak tyto řetězce Uložit do samostatného [souboru parametrů](../azure-resource-manager/templates/parameter-files.md). Tímto způsobem můžete tyto hodnoty změnit, aniž byste museli šablonu aktualizovat a znovu nasadit. V případě scénářů, kde máte citlivé hodnoty parametrů, nebo musí být zabezpečené, jako jsou hesla a tajné kódy, můžete tyto hodnoty uložit v [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md) a nechat si soubory parametrů tyto hodnoty načíst. V těchto scénářích se však znovu nasadí, aby se načetly aktuální hodnoty.

Tento přehled popisuje atributy v šabloně Správce prostředků, které obsahují definici pracovního postupu aplikace logiky. Šablona i definice pracovního postupu používají syntaxi JSON, ale některé rozdíly existují, protože definice pracovního postupu také následuje po [schématu jazyka definice pracovního postupu](../logic-apps/logic-apps-workflow-definition-language.md). Například výrazy šablony a výrazy definice pracovního postupu se liší v tom, jak [odkazují na parametry](#parameter-references) a hodnoty, které mohou přijmout.

> [!TIP]
> Pro nejjednodušší způsob, jak získat platnou šablonu parametrizované aplikace logiky, která je většinou připravená pro nasazení, použijte Visual Studio (zdarma Community Edition nebo vyšší) a nástroje Azure Logic Apps pro Visual Studio. Pak můžete [vytvořit aplikaci logiky v aplikaci Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) nebo [Najít a stáhnout existující aplikaci logiky z Azure do sady Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).
>
> Nebo můžete vytvořit šablony aplikace logiky pomocí [Azure PowerShell s modulem LogicAppTemplate](../logic-apps/logic-apps-create-azure-resource-manager-templates.md#azure-powershell).

Ukázková aplikace logiky v tomto tématu používá [aktivační proceduru Office 365 Outlook](/connectors/office365/) , která se aktivuje při přijetí nového e-mailu, a [akci Azure Blob Storage](/connectors/azureblob/) , která vytvoří objekt BLOB pro tělo e-mailu a nahraje tento objekt blob do kontejneru úložiště Azure. Příklady také ukazují, jak parametrizovat hodnoty, které se liší v nasazení.

Další informace o šablonách Správce prostředků najdete v těchto tématech:

* [Azure Resource Manager struktura a syntaxe šablony](../azure-resource-manager/templates/template-syntax.md)
* [Osvědčené postupy pro šablony Azure Resource Manageru](../azure-resource-manager/templates/template-best-practices.md)
* [Vývoj šablon Azure Resource Manageru pro konzistenci cloudu](../azure-resource-manager/templates/templates-cloud-consistency.md)

Informace o prostředcích šablon specifických pro aplikace logiky, integrační účty, artefakty integračních účtů a prostředí integrační služby najdete v tématu [typy prostředků Microsoft. Logic](/azure/templates/microsoft.logic/allversions).

Ukázkové šablony aplikací logiky najdete v těchto příkladech:

* [Úplná šablona](#full-example-template) , která se používá v příkladech tohoto tématu
* [Ukázka šablony aplikace logiky rychlého](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create) startu v GitHubu

Pro REST API Logic Apps začněte s [přehledem Azure Logic Apps REST API](/rest/api/logic).

<a name="template-structure"></a>

## <a name="template-structure"></a>Struktura šablon

Na nejvyšší úrovni se Správce prostředků šablona řídí touto strukturou, která je plně popsána v tématu [Azure Resource Manager struktura šablony a syntaxe](../azure-resource-manager/templates/template-syntax.md) :

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

Pro šablonu aplikace logiky, primárně pracujete s těmito objekty šablony:

| Atribut | Popis |
|-----------|-------------|
| `parameters` | Deklaruje [parametry šablony](../azure-resource-manager/templates/template-syntax.md#parameters) pro přijetí hodnot, které se mají použít při vytváření a přizpůsobení prostředků pro nasazení v Azure. Tyto parametry například přijímají hodnoty pro název a umístění vaší aplikace logiky, připojení a další prostředky, které jsou nezbytné pro nasazení. Tyto hodnoty parametrů můžete uložit do [souboru parametrů](#template-parameter-files), který je popsán dále v tomto tématu. Obecné podrobnosti naleznete v tématu [Parameters-správce prostředků Structure a syntax šablony](../azure-resource-manager/templates/template-syntax.md#parameters). |
| `resources` | Definuje [prostředky](../azure-resource-manager/templates/template-syntax.md#resources) pro vytvoření nebo aktualizaci a nasazení skupiny prostředků Azure, například vaší aplikace logiky, připojení, účtů Azure Storage atd. Obecné podrobnosti naleznete v tématu [Resources-správce prostředků Structure a syntax šablony](../azure-resource-manager/templates/template-syntax.md#resources). |
||||

Šablona vaší aplikace logiky používá tento formát názvu souboru:

**<*Logic-App-Name* # C0.json**

> [!IMPORTANT]
> Syntaxe šablony rozlišuje velká a malá písmena, takže se ujistěte, že používáte konzistentní použití velkých a malých písmen. 

<a name="template-parameters"></a>

## <a name="template-parameters"></a>Parametry šablony

Šablona aplikace logiky má více `parameters` objektů, které existují na různých úrovních a provádějí různé funkce. Například na nejvyšší úrovni můžete deklarovat [parametry šablony](../azure-resource-manager/templates/template-syntax.md#parameters) pro hodnoty, které chcete přijmout a použít při nasazení při vytváření a nasazování prostředků v Azure, například:

* Vaše aplikace logiky
* Připojení, která vaše logika používá pro přístup k dalším službám a systémům prostřednictvím [spravovaných konektorů](../connectors/apis-list.md)
* Další prostředky, které vaše aplikace logiky potřebuje k nasazení

  Například pokud vaše aplikace logiky používá [integrační účet](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) pro scénáře B2B (Business-to-Business), objekt nejvyšší úrovně šablony `parameters` deklaruje parametr, který přijímá ID prostředku pro tento účet integrace.

Zde je obecná struktura a syntaxe pro definici parametru, která je plně popsána [parametry-správce prostředků strukturou šablony a syntaxí](../azure-resource-manager/templates/template-syntax.md#parameters):

```json
"<parameter-name>": {
   "type": "<parameter-type>",
   "defaultValue": <default-parameter-value>,
   <other-parameter-attributes>,
   "metadata": {
      "description": "<parameter-description>"
   }
},
```

Tento příklad ukazuje jenom parametry šablony pro hodnoty používané k vytvoření a nasazení těchto prostředků v Azure:

* Název a umístění vaší aplikace logiky
* ID, které se má použít pro účet pro integraci, který je propojený s aplikací logiky

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "minLength": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location for the logic app"
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

S výjimkou parametrů, které zpracovávají citlivé hodnoty, nebo musí být zabezpečené, jako jsou uživatelská jména, hesla a tajné klíče, všechny tyto parametry obsahují `defaultValue` atributy, i když v některých případech jsou výchozí hodnoty prázdné hodnoty. Hodnoty nasazení, které se mají použít pro tyto parametry šablony, jsou k dispozici v souboru ukázkových [parametrů](#template-parameter-files) popsaných dále v tomto tématu.

Další informace o zabezpečení parametrů šablon najdete v těchto tématech:

* [Doporučení zabezpečení pro parametry šablony](../azure-resource-manager/templates/template-best-practices.md#parameters)
* [Zvýšení zabezpečení pro parametry šablony](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)
* [Předání zabezpečených hodnot parametrů pomocí Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

Jiné objekty šablon často odkazují na parametry šablony, aby mohli použít hodnoty, které předávají parametry šablony, například:

* [Objekt prostředků šablony](#template-resources), popsaný dále v tomto tématu, definuje všechny prostředky v Azure, které chcete vytvořit a nasadit, například [definici prostředků vaší aplikace logiky](#logic-app-resource-definition). Tyto prostředky často používají hodnoty parametrů šablony, jako je název a umístění aplikace logiky a informace o připojení.

* V hlubší úrovni definice prostředků vaší aplikace logiky [objekt parametrů definice pracovního postupu](#workflow-definition-parameters) deklaruje parametry pro hodnoty, které se mají použít v modulu runtime vaší aplikace logiky. Můžete například deklarovat parametry definice pracovního postupu pro uživatelské jméno a heslo, které Trigger HTTP používá pro ověřování. Pokud chcete zadat hodnoty pro parametry definice pracovního postupu, použijte `parameters` objekt, který je *mimo* vaši definici pracovního postupu, ale ještě *v* definici prostředků vaší aplikace logiky. V tomto vnějším `parameters` objektu můžete odkazovat na dříve deklarované parametry šablony, které mohou přijmout hodnoty při nasazení ze souboru parametrů.

Při odkazování na parametry používají výrazy šablony a funkce odlišnou syntaxi a chovají se jinak než výrazy a funkce definice pracovního postupu. Další informace o těchto rozdílech naleznete v části [odkazy na parametry](#parameter-references) dále v tomto tématu.

<a name="best-practices-template-parameters"></a>

## <a name="best-practices---template-parameters"></a>Osvědčené postupy – parametry šablony

Tady je několik osvědčených postupů pro definování parametrů:

* Deklarovat parametry pouze pro hodnoty, které se liší v závislosti na potřebách nasazení. Nedeklarujte parametry pro hodnoty, které zůstávají stejné napříč různými požadavky na nasazení.

* Zahrňte `defaultValue` atribut, který může zadat prázdné hodnoty pro všechny parametry s výjimkou hodnot, které jsou citlivé nebo musí být zabezpečené. Pro uživatelská jména, hesla a tajné klíče vždy používejte zabezpečené parametry. Pokud chcete skrýt nebo chránit citlivé hodnoty parametrů, postupujte podle pokynů v těchto tématech:

  * [Doporučení zabezpečení pro parametry šablony](../azure-resource-manager/templates/template-best-practices.md#parameters)

  * [Zvýšení zabezpečení pro parametry šablony](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)

  * [Předání zabezpečených hodnot parametrů pomocí Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

* Chcete-li odlišit názvy parametrů šablony z názvů parametrů definice pracovního postupu, můžete použít například názvy parametrů popisné šablony, například: `TemplateFabrikamPassword`

Další doporučené postupy pro šablonu najdete v tématu [osvědčené postupy pro parametry šablon](../azure-resource-manager/templates/template-best-practices.md#parameters).

<a name="template-parameter-files"></a>

## <a name="template-parameters-file"></a>Soubor parametrů šablony

Chcete-li zadat hodnoty parametrů šablony, uložte tyto hodnoty do [souboru parametrů](../azure-resource-manager/templates/parameter-files.md). Tímto způsobem můžete použít různé soubory parametrů v závislosti na potřebách nasazení. Tady je formát názvu souboru, který se má použít:

* Název souboru šablony aplikace logiky: ** < *Logic-App-Name* # C0.json**
* Název souboru parametrů: ** < *Logic-App-Name* # C0.parameters.json**

Tady je struktura v souboru parametrů, která obsahuje odkaz na Trezor klíčů pro [předávání hodnoty zabezpečeného parametru pomocí Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md):

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "<parameter-name-1>": {
         "value": "<parameter-value>"
      },
      "<parameter-name-2>": {
         "value": "<parameter-value>"
      },
      "<secured-parameter-name>": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/<key-vault-name>",
            },
            "secretName: "<secret-name>"
         }
      },
      <other-parameter-values>
   }
}
```

Tento příklad souboru parametrů určuje hodnoty pro parametry šablony deklarované dříve v tomto tématu:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      }
   }
}
```

<a name="template-resources"></a>

## <a name="template-resources"></a>Prostředky šablon

Vaše šablona má `resources` objekt, což je pole, které obsahuje definice jednotlivých prostředků pro vytvoření a nasazení v Azure, jako je [definice prostředků vaší aplikace logiky](#logic-app-resource-definition), [definice prostředků připojení](#connection-resource-definitions)a všechny další prostředky, které vaše aplikace logiky potřebuje k nasazení.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

> [!NOTE]
> Šablony můžou zahrnovat definice prostředků pro několik aplikací logiky, takže se ujistěte, že všechny prostředky aplikace logiky určují stejnou skupinu prostředků Azure. Když nasadíte šablonu do skupiny prostředků Azure pomocí sady Visual Studio, zobrazí se výzva k zadání aplikace logiky, kterou chcete otevřít. Projekt skupiny prostředků Azure taky může obsahovat víc než jednu šablonu, takže po zobrazení výzvy vyberte správný soubor parametrů.

<a name="view-resource-definitions"></a>

### <a name="view-resource-definitions"></a>Zobrazit definice prostředků

Pokud chcete zkontrolovat definice prostředků pro všechny prostředky ve skupině prostředků Azure, Stáhněte si [aplikaci logiky z Azure do sady Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md), což je nejjednodušší způsob, jak vytvořit platnou šablonu parametrizované aplikace logiky, která je většinou připravená pro nasazení.

Obecné informace o prostředcích šablon a jejich atributech najdete v těchto tématech:

* [Prostředky – Správce prostředků struktura a syntaxe šablony](../azure-resource-manager/templates/template-syntax.md#resources)
* [Osvědčené postupy pro prostředky šablon](../azure-resource-manager/templates/template-best-practices.md#resources)

<a name="logic-app-resource-definition"></a>

### <a name="logic-app-resource-definition"></a>Definice prostředků aplikace logiky

[Definice prostředků pracovního postupu](/azure/templates/microsoft.logic/workflows) vaší aplikace logiky v šabloně začíná `properties` objektem, který obsahuje tyto informace:

* Stav vaší aplikace logiky při nasazení
* ID pro libovolný účet pro integraci, který používá aplikace logiky
* Definice pracovního postupu vaší aplikace logiky
* `parameters`Objekt, který nastaví hodnoty, které se mají použít za běhu
* Další informace o zdroji vaší aplikace logiky, jako je název, typ, umístění atd.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            "state": "<Enabled or Disabled>",
            "integrationAccount": {
               "id": "[parameters('LogicAppIntegrationAccount')]" // Template parameter reference
            },
            "definition": {<workflow-definition>},
            "parameters": {<workflow-definition-parameter-values>},
            "accessControl": {}
         },
         "name": "[parameters('LogicAppName')]", // Template parameter reference
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]", // Template parameter reference
         "tags": {
           "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

Tady jsou atributy, které jsou specifické pro vaši definici prostředků aplikace logiky:

| Atribut | Požaduje se | Typ | Popis |
|-----------|----------|------|-------------|
| `state` | Ano | Řetězec | Stav vaší aplikace logiky při nasazení, kde je vaše aplikace logiky `Enabled` živá a `Disabled` to znamená, že vaše aplikace logiky je neaktivní. Pokud například nejste připraveni k tomu, aby vaše aplikace logiky přešla do provozu, ale chcete nasadit koncept verze, můžete použít `Disabled` možnost. |
| `integrationAccount` | No | Objekt | Pokud vaše aplikace logiky používá účet pro integraci, který ukládá artefakty pro scénáře B2B (Business-to-Business), tento objekt obsahuje `id` atribut, který určuje ID pro účet pro integraci. |
| `definition` | Yes | Objekt | Základní definice pracovního postupu aplikace logiky, což je stejný objekt, který se zobrazuje v zobrazení kódu a je plně popsán v tématu [Referenční dokumentace schématu pro jazyk definice pracovního postupu](../logic-apps/logic-apps-workflow-definition-language.md) . V této definici pracovního postupu `parameters` objekt deklaruje parametry pro hodnoty, které mají být použity v prostředí Logic App runtime. Další informace najdete v tématu [definice a parametry pracovního postupu](#workflow-definition-parameters). <p><p>Chcete-li zobrazit atributy v definici pracovního postupu vaší aplikace logiky, přepněte z "zobrazení návrhu" na "zobrazení kódu" v Azure Portal nebo v aplikaci Visual Studio nebo pomocí nástroje, jako je například [Azure Resource Explorer](https://resources.azure.com). |
| `parameters` | No | Objekt | [Hodnoty parametrů definice pracovního postupu](#workflow-definition-parameters) , které se mají použít v prostředí Logic App runtime Definice parametrů těchto hodnot se zobrazí uvnitř [objektu parametrů definice pracovního postupu](#workflow-definition-parameters). Také Pokud vaše aplikace logiky používá [spravované konektory](../connectors/apis-list.md) pro přístup k jiným službám a systémům, tento objekt obsahuje `$connections` objekt, který nastaví hodnoty připojení, které se mají použít za běhu. |
| `accessControl` | No | Objekt | Pro zadání atributů zabezpečení pro vaši aplikaci logiky, jako je například omezení přístupu IP k aktivačním událostem žádosti nebo vstupy a výstupy historie spouštění. Další informace najdete v tématu [zabezpečený přístup k Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md). |
||||

Další informace o definicích prostředků pro tyto Logic Apps objekty najdete v tématu [typy prostředků Microsoft. Logic](/azure/templates/microsoft.logic/allversions):

* [Definice prostředku pracovního postupu](/azure/templates/microsoft.logic/workflows)
* [Definice prostředků prostředí integrační služby](/azure/templates/microsoft.logic/integrationserviceenvironments)
* [Definice prostředků rozhraní API spravovaného prostředí integrační služby](/azure/templates/microsoft.logic/integrationserviceenvironments/managedapis)

* [Definice prostředků účtu pro integraci](/azure/templates/microsoft.logic/integrationaccounts)

* Artefakty účtu pro integraci:

  * [Definice prostředků smlouvy](/azure/templates/microsoft.logic/integrationaccounts/agreements)

  * [Definice prostředků sestavení](/azure/templates/microsoft.logic/integrationaccounts/assemblies)

  * [Definice prostředků konfigurace dávky](/azure/templates/microsoft.logic/integrationaccounts/batchconfigurations)

  * [Definice prostředků certifikátu](/azure/templates/microsoft.logic/integrationaccounts/certificates)

  * [Mapování definice prostředků](/azure/templates/microsoft.logic/integrationaccounts/maps)

  * [Definice partnerského prostředku](/azure/templates/microsoft.logic/integrationaccounts/partners)

  * [Definice prostředků schématu](/azure/templates/microsoft.logic/integrationaccounts/schemas)

  * [Definice prostředku relace](/azure/templates/microsoft.logic/integrationaccounts/sessions)

<a name="workflow-definition-parameters"></a>

## <a name="workflow-definition-and-parameters"></a>Definice a parametry pracovního postupu

Definice pracovního postupu vaší aplikace logiky se zobrazí v `definition` objektu, který se zobrazí v `properties` objektu v definici prostředků vaší aplikace logiky. Tento `definition` objekt je stejný objekt, který se zobrazí v zobrazení kódu a je plně popsán v tématu [reference schématu pro jazyk definice pracovního postupu](../logic-apps/logic-apps-workflow-definition-language.md) . Definice pracovního postupu zahrnuje vnitřní `parameters` objekt deklarace, kde můžete definovat nové nebo upravit existující parametry pro hodnoty, které jsou použity vaší definicí pracovního postupu za běhu. Pak můžete odkazovat na tyto parametry uvnitř triggeru nebo akcí v pracovním postupu. Ve výchozím nastavení `parameters` je tento objekt prázdný, pokud aplikace logiky nevytváří připojení k jiným službám a systémům prostřednictvím [spravovaných konektorů](../connectors/apis-list.md).

Pokud chcete nastavit hodnoty pro parametry definice pracovního postupu, použijte `parameters` objekt, který je *mimo* vaši definici pracovního postupu, ale ještě *v* definici prostředků vaší aplikace logiky. V tomto vnějším `parameters` objektu pak můžete odkazovat na dříve deklarované parametry šablony, které mohou přijmout hodnoty při nasazení ze souboru parametrů.

> [!TIP]
>
> V souladu s osvědčeným postupem neodkazujte přímo na parametry šablony, které jsou vyhodnocovány při nasazení, a to v rámci definice pracovního postupu. Místo toho deklarujte parametr definice pracovního postupu, který pak můžete nastavit v `parameters` objektu, který je *mimo* vaši definici pracovního postupu, ale ještě *v* definici prostředků vaší aplikace logiky. Další informace naleznete v tématu [odkazy na parametry](#parameter-references).

Tato syntaxe ukazuje, kde můžete deklarovat parametry na úrovni šablony i definice pracovního postupu spolu s tím, kde můžete nastavit tyto hodnoty parametrů odkazem na parametry šablony a definice pracovního postupu:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "<template-parameter-name>": {
         "type": "<parameter-type>",
         "defaultValue": "<parameter-default-value>",
         "metadata": {
            "description": "<parameter-description>"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "<workflow-definition-parameter-name>": {
                     "type": "<parameter-type>",
                     "defaultValue": "<parameter-default-value>",
                     "metadata": {
                        "description": "<parameter-description>"
                     }
                  }
               },
               "triggers": {
                  "<trigger-name>": {
                     "type": "<trigger-type>",
                     "inputs": {
                         // Workflow definition parameter reference
                         "<attribute-name>": "@parameters('<workflow-definition-parameter-name')"
                     }
                  }
               },
               <...>
            },
            // Workflow definition parameter value
            "parameters": {
               "<workflow-definition-parameter-name>": { 
                  "value": "[parameters('<template-parameter-name>')]"
               }
            },
            "accessControl": {}
         },
         <other-logic-app-resource-definition-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

<a name="secure-workflow-definition-parmameters"></a>

### <a name="secure-workflow-definition-parameters"></a>Parametry definice zabezpečeného pracovního postupu

Pro parametr definice pracovního postupu, který zpracovává citlivé informace, hesla, přístupové klíče nebo tajné klíče za běhu, deklarujte nebo upravte parametr pro použití `securestring` `secureobject` typu parametru nebo. Na tento parametr můžete odkazovat v rámci definice pracovního postupu. Na nejvyšší úrovni šablony deklarujte parametr, který má stejný typ pro zpracování těchto informací při nasazení.

Pokud chcete nastavit hodnotu parametru definice pracovního postupu, použijte `parameters` objekt, který je *mimo* vaši definici pracovního postupu, ale ještě *v* definici prostředků vaší aplikace logiky se odkazuje na parametr šablony. Nakonec, pokud chcete předat hodnotu parametru šablony při nasazení, uložte tuto hodnotu do [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md) a odkazujte na tento trezor klíčů v [souboru parametrů](#template-parameter-files) , který je použit vaší šablonou při nasazení.

Tato příklad šablony ukazuje, jak můžete tyto úlohy dokončit definováním zabezpečených parametrů v případě potřeby, abyste mohli ukládat jejich hodnoty do Azure Key Vault:

* Deklarujete zabezpečené parametry pro hodnoty používané k ověření přístupu.
* Tyto hodnoty použijte jak na úrovni šablony, tak na úrovni definice pracovního postupu.
* Zadejte tyto hodnoty pomocí souboru parametrů.

**Šablona**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      <previously-defined-template-parameters>,
      // Additional template parameters for passing values to use in workflow definition
      "TemplateAuthenticationType": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The type of authentication used for the Fabrikam portal"
         }
      },
      "TemplateFabrikamPassword": {
         "type": "securestring",
         "metadata": {
            "description": "The password for the Fabrikam portal"
         }
      },
      "TemplateFabrikamUserName": {
         "type": "securestring",
         "metadata": {
            "description": "The username for the Fabrikam portal"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            // Start workflow definition
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "authenticationType": {
                     "type": "string",
                     "defaultValue": "",
                     "metadata": {
                        "description": "The type of authentication used for the Fabrikam portal"
                     }
                  },
                  "fabrikamPassword": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The password for the Fabrikam portal"
                     }
                  },
                  "fabrikamUserName": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The username for the Fabrikam portal"
                     }
                  }
               },
               "triggers": {
                  "HTTP": {
                     "inputs": {
                        "authentication": {
                           // Reference workflow definition parameters
                           "password": "@parameters('fabrikamPassword')",
                           "type": "@parameters('authenticationType')",
                           "username": "@parameters('fabrikamUserName')"
                        }
                     },
                     "recurrence": {<...>},
                     "type": "Http"
                  }
               },
               <...>
            },
            // End workflow definition
            // Start workflow definition parameter values
            "parameters": {
               "authenticationType": "[parameters('TemplateAuthenticationType')]", // Template parameter reference
               "fabrikamPassword": "[parameters('TemplateFabrikamPassword')]", // Template parameter reference
               "fabrikamUserName": "[parameters('TemplateFabrikamUserName')]" // Template parameter reference
            },
            "accessControl": {}
         },
         <other-logic-app-resource-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

**Soubor parametrů**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      <previously-defined-template-parameter-values>,
     "TemplateAuthenticationType": {
        "value": "Basic"
     },
     "TemplateFabrikamPassword": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamPassword"
        }
     },
     "TemplateFabrikamUserName": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamUserName"
        }
     }
   }
}
```

<a name="best-practices-workflow-definition-parameters"></a>

## <a name="best-practices---workflow-definition-parameters"></a>Osvědčené postupy – parametry definice pracovního postupu

Abyste se ujistili, že návrhář aplikace logiky může správně zobrazit parametry definice pracovního postupu, použijte tyto osvědčené postupy:

* Zahrňte `defaultValue` atribut, který může zadat prázdné hodnoty pro všechny parametry s výjimkou hodnot, které jsou citlivé nebo musí být zabezpečené.

* Pro uživatelská jména, hesla a tajné klíče vždy používejte zabezpečené parametry. Pokud chcete skrýt nebo chránit citlivé hodnoty parametrů, postupujte podle pokynů v těchto tématech:

  * [Doporučení zabezpečení pro parametry akce](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)

  * [Doporučení zabezpečení pro parametry v definicích pracovních postupů](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow)

  * [Předání hodnot zabezpečeného parametru pomocí Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

Další informace o parametrech definice pracovního postupu najdete v tématu [parametry – jazyk definice pracovního postupu](../logic-apps/logic-apps-workflow-definition-language.md#parameters).

<a name="connection-resource-definitions"></a>

## <a name="connection-resource-definitions"></a>Definice prostředků připojení

Když aplikace logiky vytvoří a použije připojení k ostatním službám a systému pomocí [spravovaných konektorů](../connectors/apis-list.md), objekt šablony `resources` obsahuje definice prostředků pro tato připojení. I když vytvoříte připojení z aplikace logiky, připojení jsou samostatné prostředky Azure s vlastními definicemi prostředků. Pokud si chcete prohlédnout tyto definice prostředků připojení, [Stáhněte si aplikaci logiky z Azure do sady Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md), což je nejjednodušší způsob, jak vytvořit platnou šablonu parametrizované aplikace logiky, která je většinou připravená pro nasazení.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

Definice prostředků připojení odkazují na parametry nejvyšší úrovně šablony pro jejich hodnoty, což znamená, že tyto hodnoty můžete zadat v nasazení pomocí souboru parametrů. Zajistěte, aby připojení používala stejnou skupinu prostředků Azure a umístění jako vaše aplikace logiky.

Tady je příklad definice prostředků pro připojení Office 365 Outlook a odpovídajících parametrů šablony:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name for the Office 365 Outlook connection"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

Definice prostředků vaší aplikace logiky funguje také s definicemi prostředků připojení v těchto způsobech:

* V rámci definice pracovního postupu `parameters` objekt deklaruje `$connections` parametr pro hodnoty připojení, které se mají použít v prostředí Logic App runtime. Aktivační událost nebo akce, která vytváří připojení, používá také odpovídající hodnoty, které přejdou pomocí tohoto `$connections` parametru.

* *Mimo* definici pracovního postupu, ale pořád i *v* definici prostředků vaší aplikace logiky, další `parameters` objekt nastaví hodnoty, které se mají použít za běhu pro `$connections` parametr odkazem na odpovídající parametry šablony. Tyto hodnoty pomocí výrazů šablony odkazují na prostředky, které bezpečně ukládají metadata pro připojení ve vaší aplikaci logiky.

  Metadata můžou například zahrnovat připojovací řetězce a přístupové tokeny, které můžete ukládat v [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md). Chcete-li tyto hodnoty předat parametrům šablony, odkazujte na tento trezor klíčů v [souboru parametrů](#template-parameter-files) , který je použit vaší šablonou při nasazení. Další informace o rozdílech v referenčních parametrech naleznete v tématu [odkazy na parametry](#parameter-references) dále v tomto tématu.

  Když otevřete definici pracovního postupu aplikace logiky v zobrazení kód prostřednictvím Azure Portal nebo sady Visual Studio, `$connections` objekt se zobrazí mimo vaši definici pracovního postupu, ale na stejné úrovni. Toto řazení v zobrazení kódu usnadňuje odkazování těchto parametrů při ruční aktualizaci definice pracovního postupu:

  ```json
  {
     "$connections": {<workflow-definition-parameter-connection-values-runtime},
     "definition": {<workflow-definition>}
  }
  ```

* Definice prostředků vaší aplikace logiky obsahuje `dependsOn` objekt, který určuje závislosti na připojeních, která vaše aplikace logiky používá.

Každé vytvořené připojení má v Azure jedinečný název. Když vytvoříte několik připojení ke stejné službě nebo systému, každý název připojení se připojí s číslem, které se zvýší u každého nového vytvořeného připojení, například, a `office365` `office365-1` tak dále.

Tento příklad ukazuje interakce mezi definicí prostředků vaší aplikace logiky a definicí prostředků připojení pro Office 365 Outlook:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {<parameter-definition>},
      "office365_1_Connection_DisplayName": {<parameter-definition>}
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <...>,
            "definition": {
               <...>,
               "parameters": {
                  // Workflow definition "$connections" parameter
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               <...>
            },
            "parameters": {
               // Workflow definition "$connections" parameter values to use at runtime
               "$connections": {
                  "value": {
                     "office365": {
                        // Template parameter references
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            <other-logic-app-resource-information>,
            "dependsOn": [
               "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
            ]
         }
         // End logic app resource definition
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-connection-parameters"></a>

### <a name="secure-connection-parameters"></a>Parametry zabezpečeného připojení

Pro parametr připojení, který zpracovává citlivé informace, hesla, přístupové klíče a tajné kódy, zahrnuje definice prostředků připojení `parameterValues` objekt, který určuje tyto hodnoty ve formátu dvojice název-hodnota. Chcete-li skrýt tyto informace, můžete deklarovat nebo upravit parametry šablony pro tyto hodnoty pomocí `securestring` `secureobject` typů parametrů nebo. Tyto informace pak můžete uložit v [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md). Chcete-li tyto hodnoty předat parametrům šablony, odkazujte na tento trezor klíčů v [souboru parametrů](#template-parameter-files) , který je použit vaší šablonou při nasazení.

Tady je příklad, který poskytuje název účtu a přístupový klíč pro připojení k Azure Blob Storage:

**Soubor parametrů**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      },
      "azureblob_1_Connection_Name": {
         "value": "Fabrikam-Azure-Blob-Storage-Connection"
      },
      "azureblob_1_Connection_DisplayName": {
         "value": "Fabrikam-Storage"
      },
      "azureblob_1_accountName": {
         "value": "Fabrikam-Storage-Account"
      },
      "azureblob_1_accessKey": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
            },
            "secretName": "FabrikamStorageKey"
         }
      }
   }
}
```

**Šablona**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "azureblob_1_Connection_Name": {<parameter-definition>},
      "azureblob_1_Connection_DisplayName": {<parameter-definition>},
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "secureobject",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  // Azure Blob Storage action
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              // Workflow definition parameter reference for values to use at runtime
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  // Workflow definition parameter for values to use at runtime
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {<trigger-definition>},
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     // Template parameter references for values to use at runtime
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                    }
                  }
               }
            },
            "name": "[parameters('LogicAppName')]",
            "type": "Microsoft.Logic/workflows",
            "location": "[parameters('LogicAppLocation')]",
            "tags": {
               "displayName": "LogicApp"
            },
            "apiVersion": "2016-06-01",
            // Template parameter reference for value to use at deployment
            "dependsOn": [
               "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]"
            ]
         }
      },
      // Azure Blob Storage API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            // Template parameter reference for values to use at deployment
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="authenticate-connections"></a>

### <a name="authenticate-connections"></a>Ověřování připojení

Po nasazení aplikace logiky funguje na konci až po platné parametry. Přesto však musíte autorizovat všechna připojení OAuth pro vygenerování platných přístupových tokenů pro [ověřování vašich přihlašovacích údajů](../active-directory/develop/authentication-vs-authorization.md). Další informace najdete v tématu [autorizace připojení OAuth](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections).

Některá připojení podporují použití [instančního objektu služby](../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory (Azure AD) k autorizaci připojení pro aplikaci logiky, která je [zaregistrovaná ve službě Azure AD](../active-directory/develop/quickstart-register-app.md). Například tato definice prostředku Azure Data Lake připojení ukazuje, jak odkazovat na parametry šablony, které zpracovávají informace o instančním objektu a jak šablona deklaruje tyto parametry:

**Definice prostředků připojení**

```json
{
   <other-template-objects>
   "type": "MICROSOFT.WEB/CONNECTIONS",
   "apiVersion": "2016-06-01",
   "name": "[parameters('azuredatalake_1_Connection_Name')]",
   "location": "[parameters('LogicAppLocation')]",
   "properties": {
      "api": {
         "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', 'resourceGroup().location', '/managedApis/', 'azuredatalake')]"
      },
      "displayName": "[parameters('azuredatalake_1_Connection_DisplayName')]",
      "parameterValues": {
         "token:clientId": "[parameters('azuredatalake_1_token:clientId')]",
         "token:clientSecret": "[parameters('azuredatalake_1_token:clientSecret')]",
         "token:TenantId": "[parameters('azuredatalake_1_token:TenantId')]",
         "token:grantType": "[parameters('azuredatalake_1_token:grantType')]"
      }
   }
}
```

| Atribut | Popis |
|-----------|-------------|
| `token:clientId` | ID aplikace nebo klienta přidružené k vašemu objektu služby |
| `token:clientSecret` | Hodnota klíče přidružená k objektu služby |
| `token:TenantId` | ID adresáře pro vašeho tenanta Azure AD |
| `token:grantType` | Požadovaný typ udělení, který musí být `client_credentials` . Další informace najdete v tématu [tok přihlašovacích údajů klienta Microsoft Identity Platform a OAuth 2,0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). |
|||

**Definice parametrů šablony**

Objekt nejvyšší úrovně šablony `parameters` deklaruje tyto parametry pro příklad připojení:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "azuredatalake_1_Connection_Name": {
        "type": "string",
        "defaultValue": "azuredatalake"
      },
      "azuredatalake_1_Connection_DisplayName": {
        "type": "string",
        "defaultValue": "<connection-name>"
      },
      "azuredatalake_1_token:clientId": {
        "type": "securestring",
        "metadata": {
          "description": "Client (or Application) ID of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:clientSecret": {
        "type": "securestring",
        "metadata": {
          "description": "Client secret of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:TenantId": {
        "type": "securestring",
        "metadata": {
          "description": "The tenant ID of for the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:resourceUri": {
        "type": "string",
        "metadata": {
          "description": "The resource you are requesting authorization to use."
        }
      },
      "azuredatalake_1_token:grantType": {
        "type": "string",
        "metadata": {
          "description": "Grant type"
        },
        "defaultValue": "client_credentials",
        "allowedValues": [
          "client_credentials"
        ]
      },
      // Other template parameters
   }
   // Other template objects
}
```

Další informace o práci s instančními objekty najdete v těchto tématech:

* [Vytvoření instančního objektu pomocí služby Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)
* [Vytvoření instančního objektu Azure pomocí služby Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps)
* [Vytvoření instančního objektu s certifikátem pomocí služby Azure PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

<a name="parameter-references"></a>

## <a name="references-to-parameters"></a>Odkazy na parametry

Chcete-li odkazovat na parametry šablony, můžete použít výrazy šablony s [funkcemi šablony](../azure-resource-manager/templates/template-functions.md), které jsou vyhodnocovány při nasazení. Výrazy šablony používají hranaté závorky (**[]**):

`"<attribute-name>": "[parameters('<template-parameter-name>')]"`

Chcete-li odkazovat na parametry definice pracovního postupu, použijte [výrazy a funkce jazyka definice pracovního postupu](../logic-apps/workflow-definition-language-functions-reference.md), které jsou vyhodnocovány za běhu. Můžete si všimnout, že některé funkce šablon a funkce definice pracovního postupu mají stejný název. Výrazy definice pracovního postupu začínají symbolem "at" ( **@** ):

`"<attribute-name>": "@parameters('<workflow-definition-parameter-name>')"`

Hodnoty parametrů šablony můžete předat do definice pracovního postupu, aby se vaše aplikace logiky použila za běhu. Nepoužívejte však parametry šablony, výrazy a syntaxi v definici pracovního postupu, protože Návrhář aplikace logiky nepodporuje prvky šablony. Syntaxe šablony a výrazy mohou také zkomplikovat kód z důvodu rozdílů v době, kdy jsou výrazy vyhodnocovány.

Místo toho postupujte podle těchto obecných kroků k deklaraci a odkazování na parametry definice pracovního postupu, které se mají použít při spuštění, deklaraci a odkazování parametrů šablony pro použití při nasazení a určení hodnot, které se mají předat při nasazení, pomocí souboru parametrů. Úplné podrobnosti najdete v části [definice a parametry pracovního postupu](#workflow-definition-parameters) výše v tomto tématu.

1. Vytvořte šablonu a deklarujte parametry šablony pro hodnoty, které chcete přijmout a použít při nasazení.

1. V definici pracovního postupu deklarujte parametry pro hodnoty, které se mají přijmout a použít za běhu. Pak můžete na tyto hodnoty odkazovat v rámci definice pracovního postupu a.

1. V `parameters` objektu, který je *mimo* vaši definici pracovního postupu, *inside* ale ještě v definici prostředků vaší aplikace logiky, nastavte hodnoty pro parametry definice pracovního postupu odkazem na odpovídající parametry šablony. Tímto způsobem můžete hodnoty parametrů šablony předat do parametrů definice pracovního postupu.

1. V souboru parametrů zadejte hodnoty pro šablonu, která se má použít při nasazení.

<a name="full-example-template"></a>

## <a name="full-example-template"></a>Úplný příklad šablony

Tady je parametrizovaná vzorová šablona, kterou používají příklady tohoto tématu:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name to use for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "minLength": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location to use for the logic app"
         }
      },
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name to use for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name to use for the Office 365 Outlook connection"
         }
      },
      "azureblob_1_Connection_Name": {
         "type": "string",
         "defaultValue": "azureblob",
         "metadata": {
            "description": "The resource name to use for the Azure Blob storage account connection"
         }
      },
      "azureblob_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         },

      },
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "securestring",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "integrationAccount": {
              "id": "[parameters('LogicAppIntegrationAccount')]"
            },
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {
                  "When_a_new_email_arrives": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['office365']['connectionId']"
                           }
                        },
                        "method": "get",
                        "path": "/Mail/OnNewEmail",
                        "queries": {
                           "folderPath": "Inbox",
                           "importance": "Any",
                           "fetchOnlyWithAttachment": false,
                           "includeAttachments": false
                        }
                     },
                     "recurrence": {
                        "frequency": "Day",
                        "interval": 1
                     },
                     "splitOn": "@triggerBody()?['value']"
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                     },
                     "office365": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            "accessControl": {}
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
            "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
            "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
         ]
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('office365_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
                "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytváření šablon pro aplikace logiky](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
