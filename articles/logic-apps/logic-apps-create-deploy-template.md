---
title: Vytvoření nasazení šablon pro Azure Logic Apps | Microsoft Docs
description: Vytváření šablon Azure Resource Manageru pro nasazování aplikací logiky
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: jeconnoc
editor: ''
ms.assetid: 85928ec6-d7cb-488e-926e-2e5db89508ee
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 10/18/2016
ms.author: LADocs; estfan
ms.openlocfilehash: 647ffeb05542e12d19cefa3fa0dbf55e5585109a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297909"
---
# <a name="create-azure-resource-manager-templates-for-deploying-logic-apps"></a>Vytváření šablon Azure Resource Manageru pro nasazování aplikací logiky

Po vytvoření aplikace logiky můžete chtít vytvořit jako šablonu Azure Resource Manager.
Tímto způsobem můžete snadno nasadit aplikaci logiky žádné prostředí nebo skupinu prostředků, kde je může být nutné.
Další informace o šablonách Resource Manager najdete v tématu [vytváření šablon Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) a [nasazení prostředků pomocí šablony Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="logic-app-deployment-template"></a>Šablona nasazení aplikace logiky

Aplikace logiky má tři základní součásti:

* **Prostředek aplikace logiky**: obsahuje informace o třeba ceny plán, umístění a definice pracovního postupu.
* **Definice pracovního postupu**: popisuje kroky svou aplikaci logiky pracovního postupu a jak modul Logic Apps by měla spustit pracovní postup.
V aplikaci logiky můžete zobrazit tuto definici **zobrazení kódu** okno.
V prostředků aplikace logiky, můžete najít v této definici `definition` vlastnost.
* **Připojení**: odkazuje na jednotlivé prostředky, které bezpečně uložit metadata o všechna připojení konektoru, jako je například připojovací řetězec a přístupový token.
V prostředků aplikace logiky, aplikaci logiky odkazuje na tyto prostředky v `parameters` oddílu.

Všechny tyto údaje existující aplikace logiky můžete zobrazit pomocí některého nástroje, například [Průzkumníka prostředků Azure](http://resources.azure.com).

Chcete-li šablonu pro použití s nasazením skupin prostředků aplikace logiky, musíte definovat, které prostředky a Parametrizace podle potřeby.
Například Pokud nasazujete pro vývoj, testovací a produkční prostředí, budete pravděpodobně chtít v každém prostředí, použijte jiný připojovací řetězce k databázi SQL.
Nebo můžete chtít nasadit v různých předplatných nebo skupiny prostředků.  

## <a name="create-a-logic-app-deployment-template"></a>Vytvoření šablony nasazení aplikace logiky

Nejjednodušší způsob, jak mají šablony nasazení platný logiku aplikace je použití [Visual Studio Tools for Logic Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites).
Nástroje sady Visual Studio generovat platné nasazení šablonu, která lze použít v rámci žádné předplatné nebo umístění.

Několik dalších nástrojů vám může pomoci při vytváření šablony nasazení aplikace logiky.
Můžete vytvořit ručně, který je pomocí prostředky již tady popisovaných vytvořit parametry, podle potřeby.
Další možností je použít [creator šablona aplikace logiky](https://github.com/jeffhollan/LogicAppTemplateCreator) modul prostředí PowerShell. Tento modul open-source nejprve vyhodnotí aplikaci logiky a všechna připojení, používá a poté generuje prostředků šablony s potřebné parametry pro nasazení.
Například pokud máte aplikace logiky, která přijímá zprávy z fronty služby Azure Service Bus a přidá data do Azure SQL database, nástroj zachovává veškerou logiku orchestration a parameterizes připojovací řetězce SQL a sběrnice, takže lze nastavit v nasazení.

> [!NOTE]
> Připojení musí být ve stejné skupině prostředků jako aplikaci logiky.
>
>

### <a name="install-the-logic-app-template-powershell-module"></a>Instalace modulu PowerShell šablona aplikace logiky
Nejjednodušší způsob, jak nainstalovat modul je prostřednictvím [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1), pomocí příkazu `Install-Module -Name LogicAppTemplate`.  

Také můžete nainstalovat modul prostředí PowerShell ručně:

1. Stáhněte si nejnovější verzi [creator šablona aplikace logiky](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).  
2. Rozbalte složku ve složce modulu PowerShell (obvykle `%UserProfile%\Documents\WindowsPowerShell\Modules`).

Pro modul pro práci s všechny přístupy klienta a předplatné tokenu, doporučujeme použít je s [ARMClient](https://github.com/projectkudu/ARMClient) nástroj příkazového řádku.  To [příspěvku na blogu](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) popisuje ARMClient podrobněji.

### <a name="generate-a-logic-app-template-by-using-powershell"></a>Generovat šablonu aplikace logiky pomocí prostředí PowerShell
Po instalaci prostředí PowerShell můžete vygenerovat šablonu pomocí následujícího příkazu:

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` je ID předplatného Azure. Tento řádek nejprve získá přístupový token prostřednictvím ARMClient, potom prostřednictvím kanálu předá přes do skriptu prostředí PowerShell a potom vytvoří šablonu v souboru JSON.

## <a name="add-parameters-to-a-logic-app-template"></a>Přidání parametrů do šablony aplikace logiky
Po vytvoření šablony aplikace logiky, můžete přidat nebo upravit parametry, které může být nutné. Například pokud vaše definice obsahuje ID prostředku do Azure funkci nebo vnořený pracovní postup, který chcete nasadit v jednom nasazení, můžete do šablony přidat další zdroje informací a Parametrizace ID podle potřeby. Totéž platí i pro všechny odkazy na vlastní rozhraní API nebo Swagger koncové body, které chcete nasadit s každé skupině prostředků.

### <a name="add-references-for-dependent-resources-to-visual-studio-deployment-templates"></a>Přidání odkazů pro závislé prostředky pro nasazení šablony sady Visual Studio

Pokud chcete svou aplikaci logiky odkazovat na závislé prostředky, můžete použít [funkce šablon Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions) v šabloně nasazení aplikace logiky. Například můžete svou aplikaci logiky, chcete-li funkce Azure nebo integrace účtu, který chcete nasadit souběžně s svou aplikaci logiky. Postupujte podle těchto pokynů o tom, jak používat parametry v šabloně nasazení tak, aby návrháře aplikace logiky vykreslí správně. 

Používáte logiku aplikace parametry v těchto druhů triggery a akce:

*   Podřízené pracovní postup
*   Function App
*   APIM volání
*   Adresa URL rozhraní API připojení modulu runtime
*   Cesta připojení rozhraní API

A můžete použít šablonu funkce jako je například parametry, proměnné, resourceId, concat atd. Můžete zde je ukázka, jak můžete nahradit ID prostředku Azure funkce:

```
"parameters":{
    "functionName": {
        "type":"string",
        "minLength":1,
        "defaultValue":"<FunctionName>"
    }
},
```

A kde byste použili parametry:

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
Další příklad můžete parametrizovat operaci odeslání zprávy služby Service Bus:

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
> host.runtimeUrl je volitelné a lze odebrat ze šablony, pokud je k dispozici.
> 


> [!NOTE] 
> Pro návrháře logiku aplikace pro práci při použití parametrů je nutné zadat výchozí hodnoty, například:
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

## <a name="add-your-logic-app-to-an-existing-resource-group-project"></a>Přidat do existujícího projektu skupiny prostředků aplikace logiky

Pokud máte stávající projekt skupiny prostředků, můžete přidat aplikaci logiky do tohoto projektu v okně osnovy JSON. Můžete také přidat další aplikace logiky spolu s aplikaci, kterou jste vytvořili.

1. Otevřete soubor `<template>.json`.

2. Chcete-li otevřít okno osnovou JSON, přejděte na **zobrazení** > **ostatní okna** > **osnovy JSON**.

3. Chcete-li přidat prostředek k souboru šablony, klikněte na tlačítko **přidat prostředek** v horní části okna osnovy JSON. Nebo v okně osnovou JSON, klikněte pravým tlačítkem na **prostředky**a vyberte **přidat nový prostředek**.

    ![Osnova JSON – okno](./media/logic-apps-create-deploy-template/jsonoutline.png)
    
4. V **přidat prostředek** dialogové okno, vyhledejte a vyberte **aplikace logiky**. Název aplikace logiky a vyberte **přidat**.

    ![Přidat prostředek](./media/logic-apps-create-deploy-template/addresource.png)


## <a name="deploy-a-logic-app-template"></a>Nasazení šablony aplikace logiky

Šablony můžete nasadit pomocí všechny nástroje, například prostředí PowerShell, REST API, [Visual Studio Team Services Release Management](#team-services)a nasazení šablony prostřednictvím portálu Azure.
Navíc k uložení hodnoty parametrů, doporučujeme, abyste vytvořili [soubor parametrů](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).
Zjistěte, jak [nasazení prostředků pomocí šablony Azure Resource Manageru a prostředí PowerShell](../azure-resource-manager/resource-group-template-deploy.md) nebo [nasazení prostředků pomocí šablony Azure Resource Manageru a webu Azure portal](../azure-resource-manager/resource-group-template-deploy-portal.md).

### <a name="authorize-oauth-connections"></a>Autorizaci OAuth připojení

Po nasazení aplikace logiky funguje začátku do konce s platné parametry.
Je však stále nutné autorizovat OAuth připojení k vygenerování tokenu platný přístup.
K autorizaci OAuth připojení, otevřete aplikaci logiky v návrháři aplikace logiky a autorizaci těchto připojení. Nebo pro automatické nasazení, můžete použít skript o souhlas pro každé připojení OAuth.
Na Githubu v části je ukázkový skript [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) projektu.

<a name="team-services"></a>
## <a name="visual-studio-team-services-release-management"></a>Visual Studio Team Services Release Management

Běžný scénář pro nasazení a správě prostředí, je použít nástroj, jako je správa verzí ve Visual Studio Team Services, pomocí šablony nasazení aplikace logiky. Visual Studio Team Services zahrnuje [nasazení skupiny prostředků Azure](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup) úloh můžete přidat do každé sestavení nebo verzi kanálu. Je potřeba mít [instanční objekt](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) pro autorizaci k nasazení a pak může generovat definici verze.

1. Správa verzí, vyberte **prázdný** tak, aby vytvořit prázdnou definici.

    ![Vytvořte prázdnou definici][1]

2. Vyberte všechny prostředky, které jsou potřeba pro to, pravděpodobně včetně šablona aplikace logiky, generovaný ručně nebo jako součást procesu sestavení.
3. Přidat **nasazení skupiny prostředků Azure** úloh.
4. Nakonfigurovat [instanční objekt](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)a odkazovat na šablonu a parametry šablony soubory.
5. Nadále vytváří kroky v procesu verze pro všechny prostředí, automatizovaných testů nebo schvalovatelů podle potřeby.

<!-- Image References -->
[1]: ./media/logic-apps-create-deploy-template/emptyreleasedefinition.png
