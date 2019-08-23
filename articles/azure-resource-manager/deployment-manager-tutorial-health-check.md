---
title: Použití Azure Deployment Manageru s šablonami Resource Manageru | Microsoft Docs
description: Nasazujte prostředky Azure s využitím šablon Resource Manageru a Azure Deployment Manageru.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.date: 05/31/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: def218564511b65809e776f97d0962b72f72adc2
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69898139"
---
# <a name="tutorial-use-health-check-in-azure-deployment-manager-public-preview"></a>Kurz: Použití kontroly stavu v Azure Deployment Manager (Public Preview)

Přečtěte si, jak integrovat kontrolu stavu v [Azure Deployment Manager](./deployment-manager-overview.md). Tento kurz je založený na kurzu [použití Azure Deployment Manager with správce prostředků Templates](./deployment-manager-tutorial.md) . Před pokračováním v tomto kurzu je nutné provést tento kurz.

V šabloně zavedení používané v části [použití Azure Deployment Manager se šablonami správce prostředků](./deployment-manager-tutorial.md)jste použili čekací krok. V tomto kurzu nahradíte krok čekání krokem kontroly stavu.

> [!IMPORTANT]
> Pokud je vaše předplatné označené k testování nových funkcí Azure na Kanárských, můžete k nasazení do oblastí pro Kanárské použití použít jenom Azure Deployment Manager. 

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Vytvořit simulátor služby kontroly stavu
> * Revidovat šablonu zavedení
> * Nasazení topologie
> * Nasazení zavedení do stavu není v pořádku
> * Ověření nasazení zavedení
> * Nasazení zavedení se stavem v pořádku
> * Ověření nasazení zavedení
> * Vyčištění prostředků

Další materiály:

- [Reference k Azure Deployment Manager REST API](https://docs.microsoft.com/rest/api/deploymentmanager/).
- [Ukázka Azure Deployment Manager](https://github.com/Azure-Samples/adm-quickstart).

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* Dokončete [použití Azure Deployment Manager se šablonami správce prostředků](./deployment-manager-tutorial.md).
* Stáhněte si [šablony a artefakty](https://armtutorials.blob.core.windows.net/admtutorial/ADMTutorial.zip) , které jsou používány v tomto kurzu.

## <a name="create-a-health-check-service-simulator"></a>Vytvořit simulátor služby kontroly stavu

V produkčním prostředí obvykle používáte jednoho nebo více poskytovatelů monitorování. Abychom mohli co nejsnáze integrovat stav, společnost Microsoft spolupracuje s některými špičkovými společnostmi sledování stavu služby, které vám poskytnou jednoduché řešení kopírování a vkládání za účelem integrace kontrol stavu s nasazeními. Seznam těchto společností najdete v tématu poskytovatelé [monitorování stavu](./deployment-manager-health-check.md#health-monitoring-providers). Pro účely tohoto kurzu vytvoříte [funkci Azure](/azure/azure-functions/) pro simulaci služby sledování stavu. Tato funkce přebírá stavový kód a vrací stejný kód. Vaše šablona Azure Deployment Manager používá stavový kód k určení, jak pokračovat v nasazení.

Následující dva soubory se používají k nasazení funkce Azure Functions. Tyto soubory nemusíte stahovat, abyste procházeli v tomto kurzu.

* Správce prostředků šablonu umístěnou na [https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json](https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json)adrese. Tuto šablonu nasadíte, chcete-li vytvořit funkci Azure Functions.
* Soubor zip zdrojového kódu funkce Azure, [https://armtutorials.blob.core.windows.net/admtutorial/ADMHCFunction0417.zip](https://armtutorials.blob.core.windows.net/admtutorial/ADMHCFunction0417.zip). Tato metoda ZIP je volána šablonou Správce prostředků.

Pokud chcete službu Azure Functions nasadit, vyberte **zkusit** , aby se otevřelo prostředí Azure Cloud Shell, a pak do okna prostředí vložte následující skript.  Kód vložíte tak, že kliknete pravým tlačítkem myši na okno prostředí a pak vyberete **Vložit**.

> [!IMPORTANT]
> **ProjectName** ve skriptu PowerShellu se používá ke generování názvů služeb Azure, které jsou nasazené v tomto kurzu. Různé služby Azure mají různé požadavky na názvy. Chcete-li zajistit, aby bylo nasazení úspěšné, vyberte název, který má méně než 12 znaků a použijte pouze malá písmena a číslice.
> Uložte kopii názvu projektu. V tomto kurzu použijete stejný projectName.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json" -projectName $projectName
```

Ověření a otestování funkce Azure Functions:

1. Otevřete web [Azure Portal](https://portal.azure.com).
1. Otevřete skupinu prostředků.  Výchozí název je název projektu s připojeným **RG** .
1. Vyberte službu App Service ze skupiny prostředků.  Výchozím názvem služby App Service je název projektu s připojenou **WebApp** .
1. Rozbalte položku **funkce**a pak vyberte **HttpTrigger1**.

    ![Azure Deployment Manager – funkce kontroly stavu Azure Functions](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-function.png)

1. **Vyberte&lt;nebo > získat adresu URL funkce**.
1. Výběrem **Kopírovat** zkopírujte adresu URL do schránky.  Adresa URL je podobná:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/{healthStatus}?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Nahraďte `{healthStatus}` v adrese URL stavovým kódem. V tomto kurzu pomocí není v **pořádku** otestujete scénář, ve kterém není v pořádku, a vyzkoušíte dobrý scénář pomocí **pořádku** nebo **Upozornění** . Vytvořte dvě adresy URL, jeden se stavem není v pořádku a druhý s dobrým stavem. Příklady:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/healthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    K dokončení tohoto kurzu potřebujete obě adresy URL.

1. Chcete-li otestovat simulátor monitorování stavu, otevřete adresy URL, které jste vytvořili v posledním kroku.  Výsledky pro stav není v pořádku budou vypadat přibližně takto:

    ```
    Status: unhealthy
    ```

## <a name="revise-the-rollout-template"></a>Revidovat šablonu zavedení

Účelem této části je Ukázat, jak zahrnout do šablony zavedení krok kontroly stavu. K dokončení tohoto kurzu nemusíte vytvářet vlastní soubor CreateADMRollout. JSON. Šablona revidovaného zavedení se sdílí v účtu úložiště, který se používá v následujících oddílech.

1. Otevřete **CreateADMRollout. JSON**. Tento soubor JSON je součástí stahování.  Viz [Požadavky](#prerequisites).
1. Přidejte dva další parametry:

    ```json
    "healthCheckUrl": {
        "type": "string",
        "metadata": {
            "description": "Specifies the health check URL."
        }
    },
    "healthCheckAuthAPIKey": {
        "type": "string",
        "metadata": {
            "description": "Specifies the health check Azure Function function authorization key."
        }
    }
    ```

1. Nahraďte definici prostředků kroku čekání pomocí definice prostředku kroku kontroly stavu:

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

    V závislosti na definici pokračuje zavedení, pokud je stav buď *v pořádku* , nebo v *Upozornění*.

1. Aktualizujte **dependsON** definice zavedení tak, aby zahrnovala nově definovaný krok kontroly stavu:

    ```json
    "dependsOn": [
        "[resourceId('Microsoft.DeploymentManager/artifactSources', variables('rolloutArtifactSource').name)]",
        "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
    ],
    ```

1. Aktualizujte **stepGroups** tak, aby zahrnoval krok kontroly stavu. **HealthCheckStep** se volá v **postDeploymentSteps** of **stepGroup2**. **stepGroup3** a **stepGroup4** se nasazují jenom v případě, že je stav v pořádku buď *v pořádku* , nebo v *Upozornění*.

    ```json
    "stepGroups": [
        {
            "name": "stepGroup1",
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup2",
            "dependsOnStepGroups": ["stepGroup1"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
            "postDeploymentSteps": [
                {
                    "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
                }
            ]
        },
        {
            "name": "stepGroup3",
            "dependsOnStepGroups": ["stepGroup2"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup4",
            "dependsOnStepGroups": ["stepGroup3"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
            "postDeploymentSteps": []
        }
    ]
    ```

    Pokud porovnáte část **stepGroup3** před a poté, co je revidována, Tato část je teď závislá na **stepGroup2**.  To je nezbytné v případě, že **stepGroup3** a další skupiny kroků závisejí na výsledcích monitorování stavu.

    Na následujícím snímku obrazovky vidíte upravované oblasti a postup, jak se používá krok kontroly stavu:

    ![Šablona kontroly stavu služby Azure Deployment Manager](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-rollout-template.png)

## <a name="deploy-the-topology"></a>Nasazení topologie

Pro zjednodušení tohoto kurzu se šablona topologie a artefakty sdílejí v následujících umístěních, takže nemusíte připravovat svoji vlastní kopii. Pokud chcete použít vlastní, postupujte podle pokynů v [kurzu: Použijte Azure Deployment Manager se šablonami](./deployment-manager-tutorial.md)správce prostředků.

* Šablona topologie:\\[https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplates/CreateADMServiceTopology.json](https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplates/CreateADMServiceTopology.json)
* Úložiště artefaktů:\\[https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore](https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore)

Pokud chcete topologii nasadit, vyberte **vyzkoušet** a otevřete Cloud Shell a pak vložte powershellový skript.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$resourceGroupName = "${projectName}rg"
$artifactLocation = "https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore?st=2019-05-06T03%3A57%3A31Z&se=2020-05-07T03%3A57%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=gOh%2Bkhi693rmdxiZFQ9xbKZMU1kbLJDqXw7EP4TaGlI%3D" | ConvertTo-SecureString -AsPlainText -Force

# Create the service topology
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMServiceTopology.json" `
    -namePrefix $projectName `
    -azureResourceLocation $location `
    -artifactSourceSASLocation $artifactLocation
```

Pomocí webu Azure Portal ověřte úspěšné vytvoření topologie služby a prostředků:

![Kurz Azure Deployment Manageru – nasazené prostředky topologie služby](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

Políčko **Zobrazit skryté typy** musí být zaškrtnuté, aby se prostředky zobrazily.

## <a name="deploy-the-rollout-with-the-unhealthy-status"></a>Nasazení zavedení do stavu není v pořádku

Pro zjednodušení tohoto kurzu se revidovaná Šablona zavedení sdílí v následujících umístěních, takže nemusíte připravovat svoji vlastní kopii. Pokud chcete použít vlastní, postupujte podle pokynů v [kurzu: Použijte Azure Deployment Manager se šablonami](./deployment-manager-tutorial.md)správce prostředků.

* Šablona topologie:\\[https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMRollout.json](https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMRollout.json)
* Úložiště artefaktů:\\[https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore](https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore)

Použijte adresu URL stavu není v pořádku, kterou jste vytvořili v části [Vytvoření simulátoru služby kontroly stavu](#create-a-health-check-service-simulator). **ManagedIdentityID**najdete v tématu [Vytvoření spravované identity přiřazené uživatelem](./deployment-manager-tutorial.md#create-the-user-assigned-managed-identity).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$managedIdentityID = Read-Host -Prompt "Enter a user-assigned managed identity"
$healthCheckUrl = Read-Host -Prompt "Enter the health check Azure function URL"
$healthCheckAuthAPIKey = $healthCheckUrl.Substring($healthCheckUrl.IndexOf("?code=")+6, $healthCheckUrl.Length-$healthCheckUrl.IndexOf("?code=")-6)
$healthCheckUrl = $healthCheckUrl.Substring(0, $healthCheckUrl.IndexOf("?"))

$resourceGroupName = "${projectName}rg"
$artifactLocation = "https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore?st=2019-05-06T03%3A57%3A31Z&se=2020-05-07T03%3A57%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=gOh%2Bkhi693rmdxiZFQ9xbKZMU1kbLJDqXw7EP4TaGlI%3D" | ConvertTo-SecureString -AsPlainText -Force

# Create the rollout
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMRollout.json" `
    -namePrefix $projectName `
    -azureResourceLocation $location `
    -artifactSourceSASLocation $artifactLocation `
    -managedIdentityID $managedIdentityID `
    -healthCheckUrl $healthCheckUrl `
    -healthCheckAuthAPIKey $healthCheckAuthAPIKey
```

> [!NOTE]
> `New-AzResourceGroupDeployment`je asynchronní volání. Zpráva o úspěchu pouze znamená, že nasazení bylo úspěšně zahájeno. Chcete-li ověřit nasazení, `Get-AZDeploymentManagerRollout`použijte.  Podívejte se na další postup.

Postup kontroly zavedení pomocí následujícího skriptu prostředí PowerShell:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$resourceGroupName = "${projectName}rg"
$rolloutName = "${projectName}Rollout"

# Get the rollout status
Get-AzDeploymentManagerRollout `
    -ResourceGroupName $resourceGroupName `
    -Name $rolloutName `
    -Verbose
```

Následující vzorový výstup ukazuje, že nasazení nebylo úspěšné, protože stav není v pořádku:

```output
Service: myhc0417ServiceWUSrg
    TargetLocation: WestUS
    TargetSubscriptionId: <Subscription ID>

    ServiceUnit: myhc0417ServiceWUSWeb
        TargetResourceGroup: myhc0417ServiceWUSrg

        Step: RestHealthCheck/healthCheckStep.PostDeploy
            Status: Failed
            StepGroup: stepGroup2
            Operation Info:
                Start Time: 05/06/2019 17:58:31
                End Time: 05/06/2019 17:58:32
                Total Duration: 00:00:01
                Error:
                    Code: ResourceReportedUnhealthy
                    Message: Health checks failed as the following resources were unhealthy: '05/06/2019 17:58:32 UTC: Health check 'appHealth' failed with the following errors: Response from endpoint 'https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy' does not match the regex pattern(s): 'Status: healthy, Status: warning.'. Response content: "Status: unhealthy"..'.
Get-AzDeploymentManagerRollout :
Service: myhc0417ServiceWUSrg
    ServiceUnit: myhc0417ServiceWUSWeb
        Step: RestHealthCheck/healthCheckStep.PostDeploy
            Status: Failed
            StepGroup: stepGroup2
            Operation Info:
                Start Time: 05/06/2019 17:58:31
                End Time: 05/06/2019 17:58:32
                Total Duration: 00:00:01
                Error:
                    Code: ResourceReportedUnhealthy
                    Message: Health checks failed as the following resources were unhealthy: '05/06/2019 17:58:32 UTC: Health check 'appHealth' failed with the following errors: Response from endpoint 'https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy' does not match the regex pattern(s): 'Status: healthy, Status: warning.'. Response content: "Status: unhealthy"..'.
At line:1 char:1
+ Get-AzDeploymentManagerRollout `
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : NotSpecified: (:) [Get-AzDeploymentManagerRollout], Exception
+ FullyQualifiedErrorId : RolloutFailed,Microsoft.Azure.Commands.DeploymentManager.Commands.GetRollout


ResourceGroupName       : myhc0417rg
BuildVersion            : 1.0.0.0
ArtifactSourceId        : /subscriptions/<Subscription ID>/resourceGroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/artifactSources/myhc0417ArtifactSourceRollout
TargetServiceTopologyId : /subscriptions/<Subscription ID>/resourceGroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/serviceTopologies/myhc0417ServiceTopology
Status                  : Failed
TotalRetryAttempts      : 0
Identity                : Microsoft.Azure.Commands.DeploymentManager.Models.PSIdentity
OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
Services                : {myhc0417ServiceWUS, myhc0417ServiceWUSrg}
Name                    : myhc0417Rollout
Type                    : Microsoft.DeploymentManager/rollouts
Location                : centralus
Id                      : /subscriptions/<Subscription ID>/resourcegroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/rollouts/myhc0417Rollout
Tags                    :
```

Po dokončení zavedení se zobrazí jedna další skupina prostředků vytvořená pro Západní USA.

## <a name="deploy-the-rollout-with-the-healthy-status"></a>Nasazení zavedení se stavem v pořádku

Zopakováním této části znovu nasaďte zavedení do stavové adresy URL stavu v pořádku.  Po dokončení zavedení se zobrazí jedna skupina prostředků vytvořená pro Východní USA.

## <a name="verify-the-deployment"></a>Ověření nasazení

1. Otevřete web [Azure Portal](https://portal.azure.com).
2. Přejděte k nově vytvořeným webovým aplikacím v nových skupinách prostředků vytvořených nasazením uvedení.
3. Otevřete webovou aplikaci ve webovém prohlížeči. Zkontrolujte umístění a verzi souboru index.html.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
2. Pomocí pole **Filtrovat podle názvu** můžete vyfiltrovat skupiny prostředků vytvořené v tomto kurzu. Měly by být 3 až 4:

    * **&lt;namePrefix>rg:** Obsahuje prostředky Deployment Manageru.
    * **&lt;namePrefix>ServiceWUSrg:** Obsahuje prostředky definované službou ServiceWUS.
    * **&lt;namePrefix>ServiceEUSrg:** Obsahuje prostředky definované službou ServiceEUS.
    * Skupina prostředků pro spravovanou identitu přiřazenou uživatelem.
3. Vyberte název skupiny prostředků.
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.
5. Zopakujte poslední dva kroky a odstraňte ostatní skupiny prostředků vytvořené v rámci tohoto kurzu.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak používat funkci kontroly stavu služby Azure Deployment Manager. Další informace najdete v [dokumentaci k Azure Resource Manageru](/azure/azure-resource-manager/).
