---
title: Vytvoření šablony nasazení pro Azure Logic Apps | Dokumentace Microsoftu
description: Vytvoření šablony Azure Resource Manageru pro nasazení aplikací logiky
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: 85928ec6-d7cb-488e-926e-2e5db89508ee
ms.date: 10/18/2016
ms.openlocfilehash: 624539557b0bf57e9d919a3a46337f1cf93a4f07
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128596"
---
# <a name="create-azure-resource-manager-templates-for-deploying-logic-apps"></a>Vytvoření šablony Azure Resource Manageru pro nasazení aplikací logiky

Když vytvoříte aplikaci logiky, můžete rozbalit definici aplikace logiky do [šablony Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md). Potom můžete pomocí této šablony pro automatizaci nasazení tak, že definujete prostředky a parametry, který chcete použít pro nasazení a poskytnutí hodnoty parametru prostřednictvím [soubor parametrů](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).
Tímto způsobem nasazením aplikace logiky snadněji a do všech prostředí nebo skupiny prostředků Azure chcete. 

Poskytuje služba Azure Logic Apps [šablona Azure Resource Manageru předem připravených logic apps](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) , můžete znovu použít, nejen pro vytváření aplikací logiky, ale také k definování prostředky a parametry se mají použít pro nasazení. Můžete použít tuto šablonu pro vaše vlastní obchodní scénáře nebo šablonu přizpůsobit, aby splňovaly vaše požadavky. Další informace o [strukturu šablony Resource Manageru a syntaxe](../azure-resource-manager/resource-group-authoring-templates.md). Syntaxi JSON a vlastnostech najdete v tématu [typy prostředků Microsoft.Logic](/azure/templates/microsoft.logic/allversions).

Další informace o šablonách Azure Resource Manageru najdete v těchto článcích:

* [Tvorba šablon Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md)
* [Vývoj šablon Azure Resource Manageru pro zajištění konzistence cloudu](../azure-resource-manager/templates-cloud-consistency.md)

## <a name="logic-app-structure"></a>Struktura aplikace logiky

Definici aplikace logiky má tyto základní části, které se zobrazí při přechodu z "zobrazení návrháře" na "zobrazení kódu", nebo pomocí nástroje [Azure Resource Exploreru](http://resources.azure.com). Definic aplikací logiky Javascript Object Notation (JSON), takže pro další informace o syntaxi JSON a vlastnosti, najdete v tématu [typy prostředků Microsoft.Logic](/azure/templates/microsoft.logic/allversions).

* **Prostředek aplikace logiky**: Popisuje informace, jako je umístění aplikace logiky nebo oblast, ceny plánu a definice pracovního postupu.

* **Definice pracovního postupu**: Popisuje vaší aplikace logiky triggerů a akcí a jak služba Azure Logic Apps spustí pracovní postup. V zobrazení kódu, můžete najít v definici pracovního postupu `definition` oddílu.

* **Připojení**: Pokud používáte spravované konektory ve vaší aplikaci logiky `$connections` oddílu odkazuje na další prostředky, které bezpečně ukládají metadata o těchto připojení mezi vaší aplikace logiky a jinými systémy nebo služby, jako je například připojovací řetězce a přístupové tokeny. V definici aplikace logiky, odkazy na tato připojení se zobrazí uvnitř vaší definice aplikace logiky `parameters` oddílu.

Vytvoření šablony aplikace logiky, který vám pomůže s nasazením skupin prostředků Azure, musíte definovat prostředky a parametrizovat podle potřeby. Například pokud nasazení provádíte do vývoje, testování a produkční prostředí, budete pravděpodobně chtít použít jiné připojovací řetězce k databázi SQL v jednotlivých prostředích.
Nebo můžete chtít nasadit v rámci různých předplatných nebo skupinách prostředků.

## <a name="create-logic-app-deployment-templates"></a>Vytvoření šablony nasazení aplikací logiky

Pro nejjednodušší způsob, jak vytvořit šablonu nasazení aplikace logiky platný použijte Visual Studio a Azure Logic Apps Tools pro rozšíření sady Visual Studio. Stažením aplikace logiky na webu Azure Portal do sady Visual Studio získáte platné nasazení šablony, která můžete použít libovolné předplatné Azure a umístění. Automaticky stahovat aplikaci logiky navíc parametrizuje definici aplikace logiky, který je vložený v šabloně.
Další informace o vytváření a Správa aplikací logiky v sadě Visual Studio najdete v tématu [vytváření aplikací logiky pomocí sady Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) a [Správa aplikací logiky pomocí sady Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

Jiné než Visual Studio nebo ruční vytvoření šablony a potřebné parametry podle pokynů v tomto tématu, můžete použít také [modul Powershellu pro vytvoření šablony aplikací logiky](https://github.com/jeffhollan/LogicAppTemplateCreator). Tento modul open source vyhodnotí jako první aplikace logiky a všechna připojení, které aplikace logiky používá. Modul poté vygeneruje prostředků šablony s nezbytnými parametry pro nasazení. Předpokládejme například, že máte aplikaci logiky, která přijme zprávu z fronty služby Azure Service Bus a přidá data do služby Azure SQL database. Nástroj modulu zachová veškerou logiku orchestration a parametrizuje připojovací řetězce SQL a Service Bus tak, aby tyto hodnoty můžete nastavit na nasazení.

> [!IMPORTANT]
> Připojení musí existovat ve stejné skupině prostředků Azure jako aplikace logiky.
> Pro modul prostředí PowerShell pro práci s Azure tenanta a předplatné access token, použitím modulu s [klientský nástroj Azure Resource Manageru](https://github.com/projectkudu/ARMClient). Další informace najdete v tomto [článek týkající se nástroje Azure Resource Manageru klienta](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) ARMClient popisuje podrobněji.

### <a name="install-powershell-module-for-logic-app-templates"></a>Nainstalujte modul prostředí PowerShell pro šablony aplikace logiky

Nejjednodušší způsob instalace modulu z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1), použijte tento příkaz:

`Install-Module -Name LogicAppTemplate`

Můžete také ručně nainstalovat modul prostředí PowerShell:

1. Stáhněte si nejnovější [autora šablony aplikace logiky](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).

1. Rozbalte složku ve složce modulu prostředí PowerShell, který je obvykle `%UserProfile%\Documents\WindowsPowerShell\Modules`.

### <a name="generate-logic-app-template---powershell"></a>Generovat šablony aplikace logiky – PowerShell

Po instalaci prostředí PowerShell můžete generovat šablony pomocí následujícího příkazu:

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` je ID předplatného Azure. Tento řádek nejprve získá přístupový token prostřednictvím ARMClient, potom prostřednictvím kanálu předá ho pomocí skriptu prostředí PowerShell a pak vytvoří šablonu v souboru JSON.

## <a name="parameters-in-logic-app-templates"></a>Parametry šablony aplikací logiky

Po vytvoření šablony aplikace logiky můžete přidat a upravit všechny potřebné parametry. Šablony má více než jeden `parameters` části, například: 

* Definice pracovního postupu aplikace logiky má vlastní [ `parameters` části](../logic-apps/logic-apps-workflow-definition-language.md#parameters) Tady můžete definovat všechny parametry, které vaše aplikace logiky používá pro příjem vstupů v nasazení.

* Šablony Resource Manageru má svoji vlastní [ `parameters` části](../azure-resource-manager/resource-group-authoring-templates.md#parameters), nezávisle na aplikaci logiky `parameters` oddílu. Příklad:

  [!INCLUDE [logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

Předpokládejme například, definici aplikace logiky odkazuje na ID prostředku, který představuje funkci Azure nebo vnořené aplikaci logiky, a chcete nasadit tuto ID prostředku spolu se svou aplikaci logiky jako jedno nasazení. Můžete přidat toto ID jako prostředek v šabloně a parametrizujte ID tohoto. Stejný postup můžete použít pro odkazy na vlastní koncové body rozhraní API nebo OpenAPI (dříve "Swagger"), který má nasazený se všemi skupinami prostředků Azure.

Při použití parametrů do šablony nasazení, postupovat podle těchto pokynů, takže se návrhář pro Logic Apps můžete tyto parametry správné zobrazení:

* Použití parametrů pouze v těchto aktivační události a akce:

  * Aplikaci Azure Functions
  * Vnořený nebo podřízený pracovní postup aplikace logiky
  * Volání API Management
  * Adresa URL modulu runtime připojení rozhraní API
  * Cesta připojení rozhraní API

* Když definujete parametry, ujistěte se, že poskytují výchozí hodnoty pomocí `defaultValue` hodnota vlastnosti, například:

  ```json
  "parameters": {
     "IntegrationAccount": {
        "type":"string",
        "minLength": 1,
        "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource=group-name>/providers/Microsoft.Logic/integrationAccounts/<integration-account-name>"
     }
  },
  ```

* Zabezpečení nebo skrytí citlivých informací v šablonách, zabezpečte parametry. Další informace o [použití zabezpečeného parametry](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow).

Tady je příklad, který ukazuje, jak můžete parametrizovat akce "Odeslat zprávu" Azure Service Bus:

```json
"Send_message": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['servicebus']['connectionId']"
         },
         // If the `host.runtimeUrl` property appears in your template, 
         // you can remove this property, which is optional, for example:
         "runtimeUrl": {}
      },
      "method": "POST",
      "path": "[concat('/@{encodeURIComponent(''', parameters('<Azure-Service-Bus-queue-name>'), ''')}/messages')]",
      "body": {
         "ContentData": "@{base64(triggerBody())}"
      },
      "queries": {
         "systemProperties": "None"
      }
   },
   "runAfter": {}
},
```

### <a name="reference-dependent-resources"></a>Odkaz na závislé prostředky

Pokud svou aplikaci logiky vyžaduje odkazy závislé prostředky, můžete použít [funkce šablon Azure Resource Manageru](../azure-resource-manager/resource-group-template-functions.md) v šabloně nasazení vaší aplikace logiky. Předpokládejme například, že chcete, aby vaše aplikace logiky k odkazování funkce Azure nebo účtu pro integraci s definicemi pro partnery, smlouvy a další artefakty, které chcete nasadí společně se službou aplikace logiky.
Funkce šablon Resource Manageru, můžete použít například `parameters`, `variables`, `resourceId`, `concat`, a tak dále.

Tady je příklad, který ukazuje, jak můžete nahradit ID prostředku pro funkci Azure tak, že definujete tyto parametry:

``` json
"parameters": {
   "<Azure-function-name>": {
      "type": "string",
      "minLength": 1,
      "defaultValue": "<Azure-function-name>"
   }
},
```

Zde je, jak pomocí těchto parametrů při odkazování na funkce Azure Functions:

```json
"MyFunction": {
   "type": "Function",
   "inputs": {
      "body": {},
      "function": {
         "id":"[resourceid('Microsoft.Web/sites/functions','<Azure-Functions-app-name>', parameters('<Azure-function-name>'))]"
      }
   },
   "runAfter": {}
},
```

## <a name="add-logic-app-to-resource-group-project"></a>Přidejte do projektu skupiny prostředků aplikace logiky

Pokud máte stávající projekt skupiny prostředků Azure, můžete přidat aplikace logiky do tohoto projektu pomocí okna s osnovou JSON. Můžete také přidat další aplikace logiky spolu s aplikaci, kterou jste předtím vytvořili.

1. V Průzkumníku řešení otevřete `<template>.json` souboru.

2. Z **zobrazení** nabídce vyberte možnost **ostatní Windows** > **osnovy JSON**.

3. Chcete-li přidat prostředek k souboru šablony, zvolte **přidat prostředek** v horní části okna s osnovou JSON. Nebo do okna s osnovou JSON, klikněte pravým tlačítkem na **prostředky**a vyberte **přidat nový prostředek**.

   ![Okno pro osnovou JSON](./media/logic-apps-create-deploy-template/jsonoutline.png)

4. V **přidat prostředek** dialogové okno, vyhledejte a vyberte **aplikace logiky**. Název aplikace logiky a zvolte **přidat**.

   ![Přidat prostředek](./media/logic-apps-create-deploy-template/addresource.png)

## <a name="get-support"></a>Získat podporu

Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Nasazení šablony aplikací logiky](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md)
