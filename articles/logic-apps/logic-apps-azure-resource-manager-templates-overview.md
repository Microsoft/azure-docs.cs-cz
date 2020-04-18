---
title: Přehled – automatizace nasazení pro aplikace Azure Logic Apps
description: Informace o šablonách Azure Resource Manageru pro automatizaci nasazení pro Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: 7a99038f41043b899886c7161f9b12c77c807c4c
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641813"
---
# <a name="overview-automate-deployment-for-azure-logic-apps-by-using-azure-resource-manager-templates"></a>Přehled: Automatizace nasazení pro Azure Logic Apps pomocí šablon Azure Resource Manager

Až budete připraveni automatizovat vytváření a nasazování aplikace logiky, můžete rozšířit základní definici pracovního postupu aplikace logiky do [šablony Azure Resource Manager](../azure-resource-manager/management/overview.md). Tato šablona definuje infrastrukturu, prostředky, parametry a další informace pro zřizování a nasazování aplikace logiky. Definováním parametrů pro hodnoty, které se liší při nasazení, označované také jako *parametrizace*, můžete opakovaně a konzistentně nasazovat aplikace logiky na základě různých potřeb nasazení.

Například pokud nasadíte do prostředí pro vývoj, testování a produkční prostředí, pravděpodobně použít různé připojovací řetězce pro každé prostředí. Můžete deklarovat parametry šablony, které přijímají různé připojovací řetězce, a pak je uložit do samostatného [souboru parametrů](../azure-resource-manager/templates/parameter-files.md). Tímto způsobem můžete tyto hodnoty změnit, aniž byste museli aktualizovat a znovu nasadit šablonu. Pro scénáře, kde máte hodnoty parametrů, které jsou citlivé nebo musí být zabezpečené, jako jsou hesla a tajné klíče, můžete tyto hodnoty uložit v [úložišti klíčů Azure](../azure-resource-manager/templates/key-vault-parameter.md) a mít parametry soubor ukládat tyto hodnoty. Však v těchto scénářích byste znovu nasadit načíst aktuální hodnoty.

Tento přehled popisuje atributy v šabloně Správce prostředků, která obsahuje definici pracovního postupu aplikace logiky. Šablona i definice pracovního postupu používají syntaxi JSON, ale existují určité rozdíly, protože definice pracovního postupu se také řídí [schématem jazyka definice pracovního postupu](../logic-apps/logic-apps-workflow-definition-language.md). Například výrazy šablony a výrazy definice pracovního postupu se liší v tom, jak [odkazují na parametry](#parameter-references) a hodnoty, které mohou přijmout.

> [!TIP]
> Nejjednodušší způsob, jak získat platnou parametrizovanou šablonu aplikace logiky, která je většinou připravená k nasazení, použijte Visual Studio (free Community Edition nebo vyšší) a Nástroje Azure Logic Apps pro Visual Studio. Potom můžete buď [vytvořit aplikaci logiky v Sadě Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) nebo najít a stáhnout existující [aplikaci logiky z Azure do Visual Studia](../logic-apps/manage-logic-apps-with-visual-studio.md).
>
> Nebo můžete vytvořit šablony aplikace logiky pomocí [Azure PowerShell u modulu LogicAppTemplate](../logic-apps/logic-apps-create-azure-resource-manager-templates.md#azure-powershell).

Ukázková aplikace logiky v tomto tématu používá [aktivační událost Aplikace Office 365 Outlook,](/connectors/office365/) která se aktivuje při příchodu nového e-mailu, a akce úložiště objektů [blob Azure,](/connectors/azureblob/) která vytvoří objekt blob pro tělo e-mailu a nahraje tento objekt blob do kontejneru úložiště Azure. Příklady také ukazují, jak parametrizovat hodnoty, které se liší při nasazení.

Další informace o šablonách Správce prostředků naleznete v těchto tématech:

* [Struktura a syntaxe šablony Azure Resource Manageru](../azure-resource-manager/templates/template-syntax.md)
* [Osvědčené postupy pro šablony Azure Resource Manageru](../azure-resource-manager/templates/template-best-practices.md)
* [Vývoj šablon Azure Resource Manageru pro konzistenci cloudu](../azure-resource-manager/templates/templates-cloud-consistency.md)

Ukázkové šablony aplikací logiky najdete v těchto příkladech:

* [Úplná šablona,](#full-example-template) která se používá pro příklady tohoto tématu
* [Ukázka šablony aplikace logiky rychlého startu](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create) na GitHubu

Informace o prostředcích šablony specifické pro aplikace logiky, účty integrace a artefakty účtů integrace naleznete v [tématu Microsoft.Logic typy prostředků](https://docs.microsoft.com/azure/templates/microsoft.logic/allversions).

<a name="template-structure"></a>

## <a name="template-structure"></a>Struktura šablon

Na nejvyšší úrovni se šablona Správce prostředků řídí touto strukturou, která je plně popsána ve [struktuře šablony Azure Resource Managera a tématu syntaxe:](../azure-resource-manager/templates/template-syntax.md)

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

Pro šablonu aplikace logiky pracujete především s těmito objekty šablony:

| Atribut | Popis |
|-----------|-------------|
| `parameters` | Deklaruje [parametry šablony](../azure-resource-manager/templates/template-syntax.md#parameters) pro přijetí hodnot, které se mají použít při vytváření a přizpůsobení prostředků pro nasazení v Azure. Tyto parametry například akceptují hodnoty pro název a umístění aplikace logiky, připojení a další prostředky potřebné pro nasazení. Tyto hodnoty parametrů můžete uložit do [souboru parametrů](#template-parameter-files), který je popsán dále v tomto tématu. Obecné podrobnosti naleznete v [tématu Parametry – Struktura a syntaxe Správce prostředků](../azure-resource-manager/templates/template-syntax.md#parameters). |
| `resources` | Definuje [prostředky](../azure-resource-manager/templates/template-syntax.md#resources) pro vytvoření nebo aktualizaci a nasazení do skupiny prostředků Azure, jako je vaše aplikace logiky, připojení, účty úložiště Azure a tak dále. Obecné podrobnosti naleznete v [tématu Zdroje – Struktura a syntaxe šablony Správce prostředků](../azure-resource-manager/templates/template-syntax.md#resources). |
||||

Šablona aplikace logiky používá tento formát názvu souboru:

**<*název aplikace _logika*>.json**

> [!IMPORTANT]
> Syntaxe šablony rozlišuje malá a velká písmena, takže se ujistěte, že používáte konzistentní kryt. 

<a name="template-parameters"></a>

## <a name="template-parameters"></a>Parametry šablony

Šablona aplikace logiky má více `parameters` objektů, které existují na různých úrovních a provádět různé funkce. Například na nejvyšší úrovni můžete deklarovat [parametry šablony](../azure-resource-manager/templates/template-syntax.md#parameters) pro hodnoty, které mají být při nasazení při nasazování při vytváření a nasazování prostředků v Azure přijímat, například:

* Vaše aplikace logiky
* Připojení, která vaše logika používá pro přístup k dalším službám a systémům prostřednictvím [spravovaných konektorů](../connectors/apis-list.md)
* Další prostředky, které vaše aplikace logiky potřebuje pro nasazení

  Například pokud vaše aplikace logiky používá [účet integrace](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) pro scénáře business-to-business (B2B), objekt nejvyšší úrovně `parameters` šablony deklaruje parametr, který přijímá ID prostředku pro tento účet integrace.

Zde je obecná struktura a syntaxe pro definici parametru, která je plně popsána [parametry - struktura šablony Správce prostředků a syntaxe](../azure-resource-manager/templates/template-syntax.md#parameters):

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

Tento příklad ukazuje pouze parametry šablony pro hodnoty použité k vytvoření a nasazení těchto prostředků v Azure:

* Název a umístění aplikace logiky
* ID pro účet integrace, který je propojený s aplikací logiky

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

S výjimkou parametrů, které zpracovávají hodnoty, které jsou citlivé nebo musí být zabezpečeny, `defaultValue` jako jsou uživatelská jména, hesla a tajné kódy, všechny tyto parametry zahrnují atributy, i když v některých případech jsou výchozí hodnoty prázdné hodnoty. Hodnoty nasazení, které mají být pro tyto parametry šablony používány, jsou uvedeny v [souboru ukázkových parametrů popsaných](#template-parameter-files) dále v tomto tématu.

Další informace o zabezpečení parametrů šablony naleznete v těchto tématech:

* [Doporučení zabezpečení pro parametry šablony](../azure-resource-manager/templates/template-best-practices.md#parameters)
* [Zlepšení zabezpečení parametrů šablony](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)
* [Předání zabezpečených hodnot parametrů pomocí azure key vaultu](../azure-resource-manager/templates/key-vault-parameter.md)

Jiné objekty šablony často odkazují na parametry šablony, aby mohly používat hodnoty, které procházejí parametry šablony, například:

* [Objekt prostředků šablony](#template-resources), popsaný dále v tomto tématu, definuje každý prostředek v Azure, který chcete vytvořit a nasadit, jako je [například definice prostředků aplikace logiky](#logic-app-resource-definition). Tyto prostředky často používají hodnoty parametrů šablony, jako je název aplikace logiky a informace o umístění a připojení.

* Na hlubší úrovni v definici prostředků vaší aplikace [logiky, parametry definice pracovního postupu objekt](#workflow-definition-parameters) deklaruje parametry pro hodnoty, které mají být používány v době běhu aplikace logiky. Můžete například deklarovat parametry definice pracovního postupu pro uživatelské jméno a heslo, které aktivační událost HTTP používá pro ověřování. Chcete-li zadat hodnoty parametrů definice `parameters` pracovního postupu, použijte objekt, který je *mimo* definici pracovního postupu, ale stále *uvnitř* definice prostředku aplikace logiky. V tomto `parameters` vnějším objektu můžete odkazovat na dříve deklarované parametry šablony, které mohou přijímat hodnoty při nasazení ze souboru parametrů.

Při odkazování na parametry používají výrazy a funkce šablony jinou syntaxi a chovají se odlišně od výrazů a funkcí definice pracovního postupu. Další informace o těchto rozdílech naleznete v [tématu Odkazy na parametry](#parameter-references) dále v tomto tématu.

<a name="best-practices-template-parameters"></a>

## <a name="best-practices---template-parameters"></a>Osvědčené postupy – parametry šablony

Zde jsou některé osvědčené postupy pro definování parametrů:

* Deklarujte parametry pouze pro hodnoty, které se liší v závislosti na potřebách nasazení. Nedeklarujte parametry pro hodnoty, které zůstávají stejné v rámci různých požadavků na nasazení.

* Zahrňte `defaultValue` atribut, který může určit prázdné hodnoty pro všechny parametry s výjimkou hodnot, které jsou citlivé nebo musí být zabezpečeny. Vždy používejte zabezpečené parametry pro uživatelská jména, hesla a tajné klíče. Chcete-li skrýt nebo chránit hodnoty citlivých parametrů, postupujte podle pokynů v těchto tématech:

  * [Doporučení zabezpečení pro parametry šablony](../azure-resource-manager/templates/template-best-practices.md#parameters)

  * [Zlepšení zabezpečení parametrů šablony](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)

  * [Předání zabezpečených hodnot parametrů pomocí azure key vaultu](../azure-resource-manager/templates/key-vault-parameter.md)

* Chcete-li odlišit názvy parametrů šablony od názvů parametrů definice pracovního postupu, můžete použít názvy parametrů popisné šablony, například:`TemplateFabrikamPassword`

Další doporučené postupy pro šablony naleznete v [tématu Doporučené postupy pro parametry šablony](../azure-resource-manager/templates/template-best-practices.md#parameters).

<a name="template-parameter-files"></a>

## <a name="template-parameters-file"></a>Soubor parametrů šablony

Chcete-li zadat hodnoty parametrů šablony, uložte tyto hodnoty do [souboru parametrů](../azure-resource-manager/templates/parameter-files.md). Tímto způsobem můžete použít různé parametry soubory na základě vašich potřeb nasazení. Zde je formát názvu souboru, který chcete použít:

* Název souboru šablony aplikace ** <logiky: *název aplikace logiky*>.json**
* Název souboru parametry: ** < *logic-app-name*>.parameters.json**

Zde je struktura uvnitř souboru parametrů, který obsahuje odkaz trezoru klíčů pro [předání hodnoty zabezpečeného parametru s trezorem klíčů Azure](../azure-resource-manager/templates/key-vault-parameter.md):

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

Tento příklad parametry soubor určuje hodnoty pro parametry šablony deklarované dříve v tomto tématu:

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

Vaše šablona `resources` má objekt, což je pole, které obsahuje definice pro každý prostředek k vytvoření a nasazení v Azure, jako [je například definice prostředků aplikace logiky](#logic-app-resource-definition), všechny definice prostředků připojení a všechny další prostředky, které vaše aplikace [logiky](#connection-resource-definitions)potřebuje pro nasazení.

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
> Šablony mohou obsahovat definice prostředků pro více aplikací logiky, takže se ujistěte, že všechny prostředky aplikace logiky určují stejnou skupinu prostředků Azure. Když nasadíte šablonu do skupiny prostředků Azure pomocí Visual Studia, budete vyzváni, pro kterou aplikaci logiky, kterou chcete otevřít. Projekt skupiny prostředků Azure může také obsahovat více než jednu šablonu, takže se ujistěte, že jste po zobrazení výzvy vybrali správný soubor parametrů.

Obecné informace o zdrojích šablon a jejich atributech naleznete v těchto tématech:

* [Zdroje – struktura a syntaxe šablony Správce prostředků](../azure-resource-manager/templates/template-syntax.md#resources)
* [Doporučené postupy pro zdroje šablon](../azure-resource-manager/templates/template-best-practices.md#resources)

<a name="logic-app-resource-definition"></a>

### <a name="logic-app-resource-definition"></a>Definice prostředku aplikace logiky

Definice prostředku aplikace logiky začíná `properties` objektem, který obsahuje tyto informace:

* Stav aplikace logiky při nasazení
* ID pro všechny integrační účet používaný vaší aplikací logiky
* Definice pracovního postupu aplikace logiky
* Objekt, `parameters` který nastavuje hodnoty, které mají být používány za běhu
* Další informace o prostředcích aplikace logiky, jako je název, typ, umístění a tak dále

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

Tady jsou atributy, které jsou specifické pro definici prostředku aplikace logiky:

| Atribut | Požaduje se | Typ | Popis |
|-----------|----------|------|-------------|
| `state` | Ano | Řetězec | Stav aplikace logiky při `Enabled` nasazení, kde znamená, `Disabled` že aplikace logiky je aktivní a znamená, že aplikace logiky je neaktivní. Například pokud nejste připraveni pro aplikaci logiky jít žít, ale chcete `Disabled` nasadit verzi konceptu, můžete použít možnost. |
| `integrationAccount` | Ne | Objekt | Pokud vaše aplikace logiky používá účet integrace, který ukládá artefakty pro scénáře business-to-business (B2B), tento objekt obsahuje `id` atribut, který určuje ID pro účet integrace. |
| `definition` | Ano | Objekt | Základní definice pracovního postupu aplikace logiky, což je stejný objekt, který se zobrazí v zobrazení kódu a je plně popsán v odkazu na schéma pro téma [Jazyk definice pracovního postupu.](../logic-apps/logic-apps-workflow-definition-language.md) V této definici `parameters` pracovního postupu objekt deklaruje parametry pro hodnoty, které mají být používány za běhu aplikace logiky. Další informace naleznete [v tématu Definice a parametry pracovního postupu](#workflow-definition-parameters). <p><p>Chcete-li zobrazit atributy v definici pracovního postupu aplikace logiky, přepněte z "návrhového zobrazení" na "zobrazení kódu" na webu Azure Portal nebo Visual Studiu nebo pomocí nástroje, jako je [Azure Resource Explorer](https://resources.azure.com). |
| `parameters` | Ne | Objekt | [Hodnoty parametrů definice pracovního postupu,](#workflow-definition-parameters) které se mají použít v době běhu aplikace logiky. Definice parametrů pro tyto hodnoty se zobrazí uvnitř [objektu parametrů definice pracovního postupu](#workflow-definition-parameters). Také pokud vaše aplikace logiky používá [spravované konektory](../connectors/apis-list.md) pro přístup `$connections` k jiným službám a systémům, tento objekt obsahuje objekt, který nastaví hodnoty připojení pro použití za běhu. |
| `accessControl` | Ne | Objekt | Pro určení atributů zabezpečení pro aplikaci logiky, jako je například omezení přístupu IP na aktivační události požadavku nebo spuštění historie vstupů a výstupů. Další informace najdete [v tématu Zabezpečený přístup k aplikacím logiky](../logic-apps/logic-apps-securing-a-logic-app.md). |
||||

Informace o prostředcích šablony specifické pro aplikace logiky, účty integrace a artefakty účtů integrace naleznete v [tématu Microsoft.Logic typy prostředků](https://docs.microsoft.com/azure/templates/microsoft.logic/allversions).

<a name="workflow-definition-parameters"></a>

## <a name="workflow-definition-and-parameters"></a>Definice a parametry pracovního postupu

Definice pracovního postupu aplikace logiky se zobrazí v objektu, `definition` který se zobrazí v objektu `properties` uvnitř definice prostředku aplikace logiky. Tento `definition` objekt je stejný objekt, který se zobrazí v zobrazení kódu a je plně popsán v odkazu schéma pro téma [Jazyk definice pracovního postupu.](../logic-apps/logic-apps-workflow-definition-language.md) Definice pracovního postupu `parameters` obsahuje vnitřní objekt deklarace, kde můžete definovat nové nebo upravit existující parametry pro hodnoty, které jsou používány definicí pracovního postupu za běhu. Potom můžete odkazovat na tyto parametry uvnitř aktivační události nebo akce v pracovním postupu. Ve výchozím `parameters` nastavení je tento objekt prázdný, pokud aplikace logiky nevytvoří připojení k jiným službám a systémům prostřednictvím [spravovaných konektorů](../connectors/apis-list.md).

Chcete-li nastavit hodnoty parametrů definice `parameters` pracovního postupu, použijte objekt, který je *mimo* definici pracovního postupu, ale stále *uvnitř* definice prostředku aplikace logiky. V tomto `parameters` vnějším objektu pak můžete odkazovat na dříve deklarované parametry šablony, které mohou přijímat hodnoty při nasazení ze souboru parametrů.

> [!TIP]
>
> Jako osvědčený postup není přímo odkazovat na parametry šablony, které jsou vyhodnocovány při nasazení, z definice pracovního postupu. Místo toho deklarujte parametr definice pracovního `parameters` postupu, který pak můžete nastavit v objektu, který je *mimo* definici pracovního postupu, ale stále *uvnitř* definice prostředku aplikace logiky. Další informace naleznete v [tématu Reference s parametry](#parameter-references).

Tato syntaxe ukazuje, kde můžete deklarovat parametry na úrovni definice šablony i pracovního postupu spolu s tím, kde můžete tyto hodnoty parametrů nastavit odkazem na parametry definice šablony a pracovního postupu:

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

### <a name="secure-workflow-definition-parameters"></a>Zabezpečené parametry definice pracovního postupu

Pro parametr definice pracovního postupu, který zpracovává citlivé informace, hesla, přístupové klíče nebo `securestring` tajné `secureobject` kódy za běhu, deklarujte nebo upravte parametr tak, aby používal typ parametru nebo. Na tento parametr můžete odkazovat v rámci definice pracovního postupu a v rámci definice pracovního postupu. Na nejvyšší úrovni šablony deklarujte parametr, který má stejný typ pro zpracování těchto informací při nasazení.

Chcete-li nastavit hodnotu parametru `parameters` definice pracovního postupu, použijte objekt, který je *mimo* definici pracovního postupu, ale stále *uvnitř* definice prostředku aplikace logiky, abyste odkazovali na parametr šablony. Nakonec předat hodnotu parametru šablony při nasazení, uložte tuto hodnotu v [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md) a odkaz, že trezor klíčů v [souboru parametrů,](#template-parameter-files) který používá vaše šablona při nasazení.

Šablona tohoto příkladu ukazuje, jak můžete tyto úkoly dokončit definováním zabezpečených parametrů v případě potřeby, abyste mohli ukládat jejich hodnoty v úložišti klíčů Azure:

* Deklarujte zabezpečené parametry pro hodnoty použité k ověření přístupu.
* Tyto hodnoty použijte na úrovni definice šablony i pracovního postupu.
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

Chcete-li se ujistit, že Návrhář aplikace logiky může správně zobrazit parametry definice pracovního postupu, postupujte podle následujících doporučených postupů:

* Zahrňte `defaultValue` atribut, který může určit prázdné hodnoty pro všechny parametry s výjimkou hodnot, které jsou citlivé nebo musí být zabezpečeny.

* Vždy používejte zabezpečené parametry pro uživatelská jména, hesla a tajné klíče. Chcete-li skrýt nebo chránit hodnoty citlivých parametrů, postupujte podle pokynů v těchto tématech:

  * [Doporučení zabezpečení pro parametry akce](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)

  * [Doporučení zabezpečení pro parametry v definicích pracovního postupu](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow)

  * [Předání zabezpečených hodnot parametrů pomocí azure key vaultu](../azure-resource-manager/templates/key-vault-parameter.md)

Další informace o parametrech definice pracovního postupu naleznete v [tématu Parametry - Jazyk definice pracovního postupu](../logic-apps/logic-apps-workflow-definition-language.md#parameters).

<a name="connection-resource-definitions"></a>

## <a name="connection-resource-definitions"></a>Definice prostředků připojení

Když vaše aplikace logiky vytvoří a použije připojení k jiným službám `resources` a systému pomocí [spravovaných konektorů](../connectors/apis-list.md), objekt šablony obsahuje definice prostředků pro tato připojení.

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

Definice prostředků připojení odkazují na parametry nejvyšší úrovně šablony pro jejich hodnoty, což znamená, že tyto hodnoty můžete zadat při nasazení pomocí souboru parametrů. Ujistěte se, že připojení používají stejnou skupinu prostředků Azure a umístění jako aplikace logiky.

Tady je příklad definice prostředků pro připojení Office 365 Outlook a odpovídající parametry šablony:

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

Definice prostředků aplikace logiky funguje také s definicemi prostředků připojení těmito způsoby:

* Uvnitř definice pracovního `parameters` postupu objekt `$connections` deklaruje parametr pro hodnoty připojení, které mají být používány za běhu aplikace logiky. Aktivační událost nebo akce, která vytvoří připojení, také `$connections` používá odpovídající hodnoty, které procházejí tímto parametrem.

* *Mimo* definici pracovního postupu, ale stále *uvnitř* definice prostředků aplikace logiky, `parameters` `$connections` jiný objekt nastaví hodnoty, které mají být používány za běhu parametru odkazem na odpovídající parametry šablony. Tyto hodnoty používají výrazy šablony k odkazování na prostředky, které bezpečně ukládají metadata pro připojení v aplikaci logiky.

  Metadata mohou například obsahovat připojovací řetězce a přístupové tokeny, které můžete uložit v [úložišti klíčů Azure](../azure-resource-manager/templates/key-vault-parameter.md). Chcete-li předat tyto hodnoty parametrům šablony, odkazujete na tento trezor klíčů v [souboru parametrů,](#template-parameter-files) který používá vaše šablona při nasazení. Další informace o rozdílech v odkazování na parametry naleznete v [tématu Odkazy na parametry](#parameter-references) dále v tomto tématu.

  Když otevřete definici pracovního postupu aplikace logiky v zobrazení `$connections` kódu prostřednictvím portálu Azure nebo Visual Studia, objekt se zobrazí mimo definici pracovního postupu, ale na stejné úrovni. Toto pořadí v zobrazení kódu usnadňuje odkazování na tyto parametry při ruční aktualizaci definice pracovního postupu:

  ```json
  {
     "$connections": {<workflow-definition-parameter-connection-values-runtime},
     "definition": {<workflow-definition>}
  }
  ```

* Definice prostředku aplikace logiky `dependsOn` má objekt, který určuje závislosti na připojení chod vaší aplikace logiky.

Každé připojení, které vytvoříte, má v Azure jedinečný název. Při vytváření více připojení ke stejné službě nebo systému je každý název připojení připojen s číslem, které `office365` `office365-1`se připojuje ke každému nově vytvořenému připojení, například , , a tak dále.

Tento příklad ukazuje interakce mezi definicí prostředků aplikace logiky a definicí prostředků připojení pro Office 365 Outlook:

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

Pro parametr připojení, který zpracovává citlivé informace, hesla, přístupové klíče nebo tajné `parameterValues` klíče, obsahuje definice prostředku připojení objekt, který určuje tyto hodnoty ve formátu dvojice název-hodnota. Chcete-li tyto informace skrýt, můžete deklarovat nebo `securestring` upravit `secureobject` parametry šablony pro tyto hodnoty pomocí typů parametrů nebo. Tyto informace pak můžete uložit do [úložiště klíčů Azure](../azure-resource-manager/templates/key-vault-parameter.md). Chcete-li předat tyto hodnoty parametrům šablony, odkazujete na tento trezor klíčů v [souboru parametrů,](#template-parameter-files) který používá vaše šablona při nasazení.

Tady je příklad, který poskytuje název účtu a přístupový klíč pro připojení úložiště objektů blob Azure:

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

### <a name="authenticate-connections"></a>Ověření připojení

Po nasazení aplikace logiky funguje od konce do konce s platnými parametry. Je však nutné ještě autorizovat všechna připojení OAuth generovat platné přístupové tokeny pro [ověřování vašich pověření](../active-directory/develop/authentication-scenarios.md). Další informace naleznete [v tématu Authorize OAuth connections](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections).

Některá připojení podporují pomocí [hlavního mocního serveru Azure](../active-directory/develop/app-objects-and-service-principals.md) Active Directory (Azure AD) k autorizaci připojení pro aplikaci logiky, která je [registrovaná ve službě Azure AD](../active-directory/develop/quickstart-register-app.md). Například tato definice prostředku připojení Datového jezera Azure ukazuje, jak odkazovat na parametry šablony, které zpracovávají informace instančního objektu a jak šablona deklaruje tyto parametry:

**Definice prostředku připojení**

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
| `token:clientId` | ID aplikace nebo klienta přidružené k instančnímu objektu |
| `token:clientSecret` | Hodnota klíče přidružená k instančnímu objektu |
| `token:TenantId` | ID adresáře pro vašeho klienta Azure AD |
| `token:grantType` | Požadovaný typ grantu, `client_credentials`který musí být . Další informace naleznete [v tématu Platforma identit microsoftu a tok pověření klienta OAuth 2.0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). |
|||

**Definice parametrů šablony**

Objekt nejvyšší úrovně `parameters` šablony deklaruje tyto parametry pro příklad připojení:

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

Další informace o práci s instančními objekty naleznete v těchto tématech:

* [Vytvoření instančního objektu pomocí portálu Azure](../active-directory/develop/howto-create-service-principal-portal.md)
* [Vytvoření instančního objektu Azure pomocí Azure PowerShellu](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps)
* [Vytvoření instančního objektu s certifikátem pomocí Azure PowerShellu](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

<a name="parameter-references"></a>

## <a name="references-to-parameters"></a>Odkazy na parametry

Chcete-li odkazovat na parametry šablony, můžete použít výrazy šablony s [funkcemi šablony](../azure-resource-manager/templates/template-functions.md), které jsou vyhodnocovány při nasazení. Výrazy šablony používají hranatá závorka (**[]**):

`"<attribute-name>": "[parameters('<template-parameter-name>')]"`

Chcete-li odkazovat na parametry definice pracovního postupu, použijte [výrazy a funkce jazyka definice pracovního postupu](../logic-apps/workflow-definition-language-functions-reference.md), které jsou vyhodnocovány za běhu. Můžete si všimnout, že některé funkce šablony a funkce definice pracovního postupu mají stejný název. Výrazy definice pracovního postupu začínají**@** symbolem "at" ( ):

`"<attribute-name>": "@parameters('<workflow-definition-parameter-name>')"`

Hodnoty parametrů šablony můžete předat definici pracovního postupu, aby se aplikace logiky používala za běhu. Vyhněte se však použití parametrů šablony, výrazů a syntaxe v definici pracovního postupu, protože Návrhář aplikace logiky nepodporuje prvky šablony. Syntaxe šablony a výrazy mohou také komplikovat váš kód z důvodu rozdílů v při vyhodnocení výrazů.

Místo toho postupujte podle těchto obecných kroků deklarovat a odkazovat na parametry definice pracovního postupu pro použití za běhu, deklarovat a odkazovat na parametry šablony pro použití při nasazení a zadejte hodnoty, které mají být při nasazení předávány pomocí souboru parametrů. Podrobné informace naleznete v části [Definice a parametry pracovního postupu](#workflow-definition-parameters) dříve v tomto tématu.

1. Vytvořte šablonu a deklarujte parametry šablony pro hodnoty, které mají být při nasazení přijímat.

1. V definici pracovního postupu deklarujte parametry pro hodnoty, které mají být přijímány a používány za běhu. Tyto hodnoty pak můžete odkazovat v rámci definice pracovního postupu a v rámci definice pracovního postupu.

1. V `parameters` objektu, který je *mimo* definici pracovního postupu, ale stále *uvnitř* definice prostředků aplikace logiky, nastavte hodnoty parametrů definice pracovního postupu odkazem na odpovídající parametry šablony. Tímto způsobem můžete předat hodnoty parametrů šablony do parametrů definice pracovního postupu.

1. V souboru parametrů zadejte hodnoty šablony, které se mají použít při nasazení.

<a name="full-example-template"></a>

## <a name="full-example-template"></a>Úplná ukázková šablona

Zde je parametrizovaná ukázková šablona, která se používá v příkladech tohoto tématu:

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
