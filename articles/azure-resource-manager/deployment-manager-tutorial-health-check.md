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
ms.openlocfilehash: 4d6832346fb25ee09d97575c755cb2d85eb73cbe
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206604"
---
# <a name="tutorial-use-health-check-in-azure-deployment-manager-public-preview"></a>Kurz: Kontrola stavu používání v Azure Deployment Manager (Public preview)

Zjistěte, jak integrovat Kontrola stavu [Azure Deployment Manager](./deployment-manager-overview.md). V tomto kurzu vychází z [pomocí Správce nasazení Azure pomocí šablon Resource Manageru](./deployment-manager-tutorial.md) kurzu. Tento kurz je třeba provést předtím, než budete pokračovat tímto tématem.

V šabloně zavedení použité při [pomocí Správce nasazení Azure pomocí šablon Resource Manageru](./deployment-manager-tutorial.md), použít krok čekání. V tomto kurzu nahraďte krok čekání kroku kontroly stavu.

> [!IMPORTANT]
> Pokud vaše předplatné je označen pro testovací a otestujte nové funkce Azure, můžete nasadit do oblastí, testovací použít pouze Azure Deployment Manager. 

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Vytvoření služby simulátor kontroly stavu
> * Revidovat šablona uvedení
> * Topologie nasazení
> * Nasazení tohoto uvedení se stavem není v pořádku
> * Ověření nasazení zavedení
> * Nasazení tohoto uvedení se stavem v pořádku
> * Ověření nasazení zavedení
> * Vyčištění prostředků

Další zdroje informací:

- [Reference k rozhraní REST API Azure Deployment Manager](https://docs.microsoft.com/rest/api/deploymentmanager/).
- [Ukázky Azure Deployment Manager](https://github.com/Azure-Samples/adm-quickstart).

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* Kompletní [pomocí Správce nasazení Azure pomocí šablon Resource Manageru](./deployment-manager-tutorial.md).
* Stáhněte si [šablon a artefaktů](https://armtutorials.blob.core.windows.net/admtutorial/ADMTutorial.zip) , který se používá v rámci tohoto kurzu.

## <a name="create-a-health-check-service-simulator"></a>Vytvoření služby simulátor kontroly stavu

V produkčním prostředí obvykle použijete jeden nebo více monitorování poskytovatelů. Aby integrace stavu co nejjednodušší, Microsoft má pracovali s některými monitorování společnosti, kde přinášejí jednoduché kopírování/vkládání řešení pro integraci kontroly stavu s nasazeními hlavní služby stavu. Seznam těchto společnostech najdete v tématu [zprostředkovatele sledování stavu](./deployment-manager-health-check.md#health-monitoring-providers). Pro účely tohoto kurzu vytvoříte [funkce Azure Functions](/azure/azure-functions/) pro simulaci sledování služby stavu. Tato funkce přebírá stavový kód a vrací stejný kód. Šablony Azure Deployment Manager používá stavový kód a zjistěte, jak chcete pokračovat s nasazením.

Následující dva soubory se používají k nasazení funkce Azure functions. Není nutné stáhnout tyto soubory do absolvovat kurz.

* Šablony Resource Manageru se nachází v [ https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json ](https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json). Nasazení této šablony můžete vytvořit funkci Azure.
* Soubor zip se zdrojový kód funkce Azure Functions, [ http://armtutorials.blob.core.windows.net/admtutorial/ADMHCFunction0417.zip ](http://armtutorials.blob.core.windows.net/admtutorial/ADMHCFunction0417.zip). Tato zip, volá se, je volána pomocí šablony Resource Manageru.

Pokud chcete nasadit funkci Azure, vyberte **vyzkoušet** otevřete Azure Cloud shell a vložte následující skript do okna prostředí.  Vložte kód, pravým tlačítkem myši na okno prostředí a potom vyberte **vložte**.

> [!IMPORTANT]
> **projectName** v Powershellu skript slouží ke generování názvů služeb Azure, které jsou nasazeny v tomto kurzu. Různé služby Azure mají různé požadavky na názvy. K zajištění, že je nasazení úspěšné, zvolte název s méně než 12 znaků s vlastností jenom malá písmena a číslice.
> Uložte kopii názvu projektu. Můžete použít stejný název projektu v průběhu kurzu.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json" -projectName $projectName
```

Ověřit a otestovat funkce Azure Functions:

1. Otevřete web [Azure Portal](https://portal.azure.com).
1. Otevřete skupinu prostředků.  Výchozí název je název projektu s **rg** připojí.
1. Vyberte službu app service ze skupiny prostředků.  Výchozí název služby app Service je název projektu s **webapp** připojí.
1. Rozbalte **funkce**a pak vyberte **HttpTrigger1**.

    ![Azure Deployment Manager Kontrola stavu funkce Azure functions](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-function.png)

1. Vyberte  **&lt;/ > získat adresu URL funkce**.
1. Vyberte **kopírování** zkopírujte adresu URL do schránky.  Adresa URL je podobný:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/{healthStatus}?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Nahraďte `{healthStatus}` v adrese URL se stavovým kódem. V tomto kurzu pomocí **není v pořádku** k otestování tohoto scénáře není v pořádku a použijte kterýkoli **v pořádku** nebo **upozornění** k otestování tohoto scénáře jsou v pořádku. Vytvořte dvě adresy URL. jednu se stavem není v pořádku a druhý se stavem v pořádku. Příklady:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/healthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Obě adresy URL k dokončení tohoto kurzu potřebujete.

1. K otestování monitorování stavu simulátoru, otevřete adresy URL, které jste vytvořili v předchozím kroku.  Výsledky pro stav není v pořádku, musí být podobně jako:

    ```
    Status: unhealthy
    ```

## <a name="revise-the-rollout-template"></a>Revidovat šablona uvedení

Účelem této části je ukazují, jak mají být zahrnuty šablony zavedení kroku kontroly stavu. Není nutné vytvořit vlastní soubor CreateADMRollout.json k dokončení tohoto kurzu. Šablona revidované uvedení je sdílena v účtu úložiště, který se používá v dalších částech.

1. Open **CreateADMRollout.json**. Tento soubor JSON je součástí souboru ke stažení.  Viz [Požadavky](#prerequisites).
1. Přidejte další dva parametry:

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

1. Nahraďte definici prostředků čekání krok s definicí prostředků kroku kontroly stavu:

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

    Na základě definice, uvedení pokračuje, pokud stav není buď *v pořádku* nebo *upozornění*.

1. Aktualizace **dependsON** definice zavedení k zahrnutí kroku kontrola nově definované stavu:

    ```json
    "dependsOn": [
        "[resourceId('Microsoft.DeploymentManager/artifactSources', variables('rolloutArtifactSource').name)]",
        "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
    ],
    ```

1. Aktualizace **stepGroups** k zahrnutí kroku kontroly stavu. **HealthCheckStep** je volána **postDeploymentSteps** z **stepGroup2**. **stepGroup3** a **stepGroup4** nasazených pouze pokud je v pořádku stav buď *v pořádku* nebo *upozornění*.

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

    Pokud srovnáte **stepGroup3** části před a po je revidován, tato část nyní závisí na **stepGroup2**.  To je nezbytné, kdy **stepGroup3** a následném kroku skupiny závisí na výsledcích monitorování stavu.

    Následující snímek obrazovky ukazuje upravit oblasti a použití kroku kontroly stavu:

    ![Šablony Azure kontroly stavu Deployment Manager](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-rollout-template.png)

## <a name="deploy-the-topology"></a>Topologie nasazení

Pro zjednodušení tento kurz, topologie šablon a artefaktů jsou sdíleny v následujících umístěních tak, že není nutné připravit své vlastní kopie. Pokud chcete použít vlastní, postupujte podle pokynů v [kurzu: Pomocí Správce nasazení Azure pomocí šablon Resource Manageru](./deployment-manager-tutorial.md).

* Šablona topologie: https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplates/CreateADMServiceTopology.json
* Úložiště artefaktů: https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore

Pokud chcete nasadit topologii, vyberte **vyzkoušet** otevřete Cloud shell a vložte skript prostředí PowerShell.

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

## <a name="deploy-the-rollout-with-the-unhealthy-status"></a>Nasazení tohoto uvedení se stavem není v pořádku

Pro zjednodušení tento kurz, je sdílen šablona revidované uvedení v následujících umístěních tak, že není nutné připravit své vlastní kopie. Pokud chcete použít vlastní, postupujte podle pokynů v [kurzu: Pomocí Správce nasazení Azure pomocí šablon Resource Manageru](./deployment-manager-tutorial.md).

* Šablona topologie: https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMRollout.json
* Úložiště artefaktů: https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore

Použijte adresu URL není v pořádku stav, kterou jste vytvořili v [vytvořit simulátor služby stavu zaškrtnutí](#create-a-health-check-service-simulator). Pro **managedIdentityID**, naleznete v tématu [vytvoření uživatelsky přiřazené spravovanou identitu](./deployment-manager-tutorial.md#create-the-user-assigned-managed-identity).

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
> `New-AzResourceGroupDeployment` je asynchronní volání. Úspěch zprávy pouze znamená, že nasazení se úspěšně spustila. Pokud chcete ověřit nasazení, použijte `Get-AZDeploymentManagerRollout`.  Viz následující postup.

Chcete-li zkontrolovat průběh zavedení pomocí následujícího skriptu prostředí PowerShell:

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

Následující ukázkový výstup ukazuje nasazení se nezdařilo z důvodu není v pořádku, stav:

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

Po dokončení tohoto uvedení se zobrazit jedna skupina dalších prostředků vytvořené pro oblast západní USA.

## <a name="deploy-the-rollout-with-the-healthy-status"></a>Nasazení tohoto uvedení se stavem v pořádku

Opakování této části se znovu nasadit zavedení s adresou URL stav v pořádku.  Po dokončení tohoto uvedení zobrazí jeden další skupiny prostředků vytvořené v oblasti východní USA.

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

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak používat funkce kontroly stavu Správce nasazení Azure. Další informace najdete v [dokumentaci k Azure Resource Manageru](/azure/azure-resource-manager/).
