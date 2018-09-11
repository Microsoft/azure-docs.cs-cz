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
ms.openlocfilehash: 393543bbb1891e14ed67487aff26a7bda1eebcd5
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44304233"
---
# <a name="create-azure-resource-manager-templates-for-deploying-logic-apps"></a>Vytvoření šablony Azure Resource Manageru pro nasazení aplikací logiky

Po vytvoření aplikace logiky můžete chtít vytvořit jako šablony Azure Resource Manageru.
Díky tomu můžete snadno nasadit aplikace logiky do všech prostředí nebo skupinu prostředků, ve kterém může být nutná.
Další informace o šablonách Resource Manageru najdete v tématu [Tvorba šablon Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md) a [nasazení prostředků pomocí šablon Azure Resource Manageru](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="logic-app-deployment-template"></a>Nasazení šablony aplikace logiky

Aplikace logiky má tři základní součásti:

* **Prostředek aplikace logiky**: obsahuje informace o věci, jako je ceny plánu, umístění a definice pracovního postupu.
* **Definice pracovního postupu**: popisuje kroky pracovního postupu aplikace logiky a jak modul Logic Apps by se měl spustit pracovní postup.
Tato definice můžete zobrazit v aplikaci logiky **zobrazení kódu** okna.
V prostředku aplikace logiky, najdete tuto definici v `definition` vlastnost.
* **Připojení**: odkazuje na samostatné prostředky, které bezpečně ukládají metadata o všechna připojení konektoru, jako je například připojovací řetězec a přístupového tokenu.
V prostředku aplikace logiky, aplikace logiky, odkazuje na tyto prostředky v `parameters` oddílu.

Všechny tyto části existující aplikace logiky můžete zobrazit pomocí některého nástroje, například [Azure Resource Exploreru](http://resources.azure.com).

Chcete-li šablonu pro aplikace logiky pro použití s nasazeními skupin prostředků, musí definovat prostředky a parametrizovat podle potřeby.
Například pokud nasazení provádíte do vývoje, testování a produkční prostředí, budete pravděpodobně chtít použít jiné připojovací řetězce k databázi SQL v jednotlivých prostředích.
Nebo můžete chtít nasadit v rámci různých předplatných nebo skupinách prostředků.  

## <a name="create-a-logic-app-deployment-template"></a>Vytvoření šablony nasazení aplikace logiky

Nejjednodušší způsob, jak jste šablonu nasazení aplikace logiky platná, je použít [Visual Studio Tools pro Logic Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites).
Nástroje sady Visual Studio vygenerovat platné nasazení šablony, která lze použít v rámci žádné předplatné nebo umístění.

Několik dalších nástrojů vám může pomoci při vytváření šablony nasazení aplikace logiky.
Můžete vytvářet ručně, to znamená, s použitím prostředků již zde popsané parametry vytvořte podle potřeby.
Další možností je použít [autora šablony aplikace logiky](https://github.com/jeffhollan/LogicAppTemplateCreator) modul prostředí PowerShell. Tento modul open source vyhodnocen jako první aplikaci logiky a všechna připojení, že používá a poté vygeneruje prostředků šablony s nezbytnými parametry pro nasazení.
Například pokud máte aplikaci logiky, která přijme zprávu z fronty služby Azure Service Bus a přidá data do služby Azure SQL database, nástroj zachová veškerou logiku orchestration a parametrizuje připojení SQL a Service Bus, které řetězců tak, aby to můžete udělat v nasazení značky.

> [!NOTE]
> Připojení musí být ve stejné skupině prostředků jako aplikace logiky.
>
>

### <a name="install-the-logic-app-template-powershell-module"></a>Nainstalujte modul prostředí PowerShell šablony aplikace logiky
Nejjednodušší způsob, jak nainstalovat modul je prostřednictvím [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1), pomocí příkazu `Install-Module -Name LogicAppTemplate`.  

Také můžete nainstalovat modul prostředí PowerShell ručně:

1. Stáhněte si nejnovější verzi [autora šablony aplikace logiky](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).  
2. Rozbalte složku ve složce modulu prostředí PowerShell (obvykle `%UserProfile%\Documents\WindowsPowerShell\Modules`).

Pro modul pro práci s přístup tenanta a předplatné token, doporučujeme použít je s [ARMClient](https://github.com/projectkudu/ARMClient) nástroj příkazového řádku.  To [blogový příspěvek](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) ARMClient popisuje podrobněji.

### <a name="generate-a-logic-app-template-by-using-powershell"></a>Generovat šablony aplikace logiky s použitím prostředí PowerShell
Po instalaci prostředí PowerShell můžete generovat šablony pomocí následujícího příkazu:

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` je ID předplatného Azure. Tento řádek nejprve získá přístupový token prostřednictvím ARMClient, potom prostřednictvím kanálu předá ho pomocí skriptu prostředí PowerShell a pak vytvoří šablonu v souboru JSON.

## <a name="add-parameters-to-a-logic-app-template"></a>Přidání parametrů do šablony aplikace logiky
Po vytvoření šablony aplikace logiky můžete nadále přidat nebo upravit parametry, které můžete potřebovat. Například pokud vaše definice obsahuje ID prostředku Azure funkci nebo vnořený pracovní postup, který máte v úmyslu nasadit v jednom nasazení, můžete přidat další prostředky do šablony a parametrizovat ID podle potřeby. Totéž platí i pro všechny odkazy na vlastní rozhraní API nebo Swagger koncové body, které plánujete nasadit s každou skupinou prostředků.

### <a name="add-references-for-dependent-resources-to-visual-studio-deployment-templates"></a>Přidání odkazů pro závislé prostředky k nasazení šablony sady Visual Studio

Pokud chcete svou aplikaci logiky tak, aby odkazovaly závislé prostředky, můžete použít [funkce šablon Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions) do šablony nasazení aplikace logiky. Například můžete svou aplikaci logiky tak, aby odkazovaly funkce Azure Functions nebo integračního účtu, který chcete nasadit společně s vaší aplikace logiky. Postupujte podle následujících pokynů o tom, jak používat parametry v šabloně nasazení tak, aby se správně vykresluje návrhář aplikace logiky. 

Můžete použít parametry aplikace logiky v tyto druhy aktivačních procedur a akcí:

*   Podřízený pracovní postup
*   Function App
*   Volání služby APIM
*   Adresa URL modulu runtime připojení rozhraní API
*   Cesta připojení rozhraní API

A můžete použít šablonu funkce, jako je například parametry, proměnné, resourceId, concat atd. Tady je například jak můžete nahradit ID prostředku, které funkce Azure Functions:

```
"parameters":{
    "functionName": {
        "type":"string",
        "minLength":1,
        "defaultValue":"<FunctionName>"
    }
},
```

A pokud použijete parametry:

```
"MyFunction": {
    "type": "Function",
    "inputs": {
        "body":{},
        "function":{
            "id":"[resourceid('Microsoft.Web/sites/functions','functionApp',parameters('functionName'))]"
        }
    },
    "runAfter":{}
}
```
Jiný příklad můžete parametrizovat operaci odeslání zprávy služby Service Bus:

```
"Send_message": {
    "type": "ApiConnection",
        "inputs": {
            "host": {
                "connection": {
                    "name": "@parameters('$connections')['servicebus']['connectionId']"
                }
            },
            "method": "post",
            "path": "[concat('/@{encodeURIComponent(''', parameters('queueuname'), ''')}/messages')]",
            "body": {
                "ContentData": "@{base64(triggerBody())}"
            },
            "queries": {
                "systemProperties": "None"
            }
        },
        "runAfter": {}
    }
```
> [!NOTE] 
> host.runtimeUrl je volitelný a je možné odebrat ze šablony, pokud jsou k dispozici.
> 


> [!NOTE] 
> Pro návrhář aplikace logiky pro práci při použití parametrů je nutné zadat výchozí hodnoty, například:
> 
> ```
> "parameters": {
>     "IntegrationAccount": {
>     "type":"string",
>     "minLength":1,
>     "defaultValue":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Logic/integrationAccounts/<integrationAccountName>"
>     }
> },
> ```

## <a name="add-your-logic-app-to-an-existing-resource-group-project"></a>Přidat do aplikace logiky do existujícího projektu skupiny prostředků

Pokud máte stávající projekt skupiny prostředků, můžete přidat aplikace logiky do tohoto projektu v okně pro osnovou JSON. Můžete také přidat další aplikace logiky spolu s aplikaci, kterou jste předtím vytvořili.

1. Otevřete soubor `<template>.json`.

2. Chcete-li otevřít okno pro osnovou JSON, přejděte na **zobrazení** > **ostatní Windows** > **osnovy JSON**.

3. Chcete-li přidat prostředek k souboru šablony, klikněte na tlačítko **přidat prostředek** v horní části okna s osnovou JSON. Nebo do okna s osnovou JSON, klikněte pravým tlačítkem na **prostředky**a vyberte **přidat nový prostředek**.

    ![Okno pro osnovou JSON](./media/logic-apps-create-deploy-template/jsonoutline.png)
    
4. V **přidat prostředek** dialogové okno, vyhledejte a vyberte **aplikace logiky**. Název aplikace logiky a zvolte **přidat**.

    ![Přidat prostředek](./media/logic-apps-create-deploy-template/addresource.png)


## <a name="deploy-a-logic-app-template"></a>Nasazení šablony aplikace logiky

Šablony můžete nasadit pomocí všechny nástroje, jako je PowerShell, rozhraní REST API [Azure DevOps Release Management](#team-services)a šablony nasazení na webu Azure portal.
K ukládání hodnot pro parametry, doporučujeme také, že vytvoříte [soubor parametrů](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).
Zjistěte, jak [nasazení prostředků pomocí šablon Azure Resource Manageru a Powershellu](../azure-resource-manager/resource-group-template-deploy.md) nebo [nasazení prostředků pomocí šablon Azure Resource Manageru a webu Azure portal](../azure-resource-manager/resource-group-template-deploy-portal.md).

### <a name="authorize-oauth-connections"></a>Autorizovat připojení OAuth

Po nasazení aplikace logiky funguje začátku do konce se platné parametry.
Však musí i nadále autorizovat připojení OAuth pro generování platným přístupovým tokenem.
Připojení OAuth, otevřete aplikaci logiky v návrháři pro Logic Apps, a tato připojení. Nebo pro automatické nasazení, můžete použít skript souhlas pro každé připojení OAuth.
Na Githubu v části je ukázkový skript [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) projektu.

<a name="team-services"></a>
## <a name="azure-devops-release-management"></a>Azure DevOps Release Management

Běžný scénář pro nasazení a správu prostředí je použití nástroje, jako je Release Management v Azure DevOps s šablony nasazení aplikace logiky. Zahrnuje Azure DevOps [nasazení skupiny prostředků Azure](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup) úloh, můžete přidat do jakékoli sestavení nebo kanál verze. Je potřeba mít [instanční objekt služby](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) pro autorizaci k nasazení a pak můžete vygenerovat kanál pro vydávání verzí.

1. V produktu Release Management, vyberte **prázdný** tak, že vytvoříte kanál prázdný.

    ![Vytvoření prázdné kanálu][1]

2. Vyberte všechny prostředky, které potřebujete v takovém případě pravděpodobně včetně šablony aplikace logiky, který je generován ručně nebo jako součást procesu sestavení.
3. Přidat **nasazení skupiny prostředků Azure** úloh.
4. Nakonfigurovat [instanční objekt služby](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)a odkazují na soubory šablonu a parametry šablony.
5. Pokračujte v sestavení kroky v procesu vydávání verzí u jiných prostředí, automatizovaných testů nebo schvalovatelů podle potřeby.

<!-- Image References -->
[1]: ./media/logic-apps-create-deploy-template/emptyreleasedefinition.png
