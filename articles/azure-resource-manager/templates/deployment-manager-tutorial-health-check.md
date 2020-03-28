---
title: Použití kontroly stavu Správce nasazení Azure
description: Pomocí kontroly stavu můžete bezpečně nasadit prostředky Azure pomocí Azure Deployment Manageru.
author: mumian
ms.date: 10/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 765c73a3ab8d5fa8939abe597d0141b24b59ac52
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76152473"
---
# <a name="tutorial-use-health-check-in-azure-deployment-manager-public-preview"></a>Kurz: Použití kontroly stavu ve Správci nasazení Azure (Veřejná verze preview)

Zjistěte, jak integrovat kontrolu stavu ve [Správci nasazení Azure](./deployment-manager-overview.md). Tento kurz je založen na [kurzu Použít Správce nasazení Azure se šablonami Správce prostředků.](./deployment-manager-tutorial.md) Musíte dokončit tento kurz, než budete pokračovat s tímto.

V šabloně pro zavedení použité v [šablonách Azure Deployment Manager se správcem prostředků](./deployment-manager-tutorial.md)jste použili krok čekání. V tomto kurzu nahradíte krok čekání krokem kontroly stavu.

> [!IMPORTANT]
> Pokud je vaše předplatné označeno pro Canary, aby otestovalo nové funkce Azure, můžete použít Azure Deployment Manager jenom k nasazení do kanárských oblastí. 

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Vytvoření simulátoru služby kontroly stavu
> * Revize šablony pro zavedení
> * Nasazení topologie
> * Nasazení se stavem Není v pořádku
> * Ověření nasazení
> * Nasazení se stavem V pořádku
> * Ověření nasazení
> * Vyčištění prostředků

Další prostředky:

* [Odkaz na rozhraní REST správce nasazení Azure](https://docs.microsoft.com/rest/api/deploymentmanager/).
* [Ukázka Správce nasazení Azure](https://github.com/Azure-Samples/adm-quickstart).

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* [Dokončete použití Správce nasazení Azure pomocí šablon Správce prostředků](./deployment-manager-tutorial.md).

## <a name="install-the-artifacts"></a>Instalace artefaktů

Stáhněte si [šablony a artefakty](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip) a rozbalte je místně, pokud jste tak neučinili. A pak spusťte skript Prostředí PowerShell nalezený v [připravte artefakty](./deployment-manager-tutorial.md#prepare-the-artifacts). Skript vytvoří skupinu prostředků, vytvoří kontejner úložiště, vytvoří kontejner objektů blob, nahraje stažené soubory a pak vytvoří token SAS.

Vytvořte kopii adresy URL pomocí tokenu SAS. Tuto hodnotu je potřeba vyplnit do příslušného pole v obou souborech parametrů (soubor parametrů topologie a soubor parametrů uvedení).

Otevřete soubor CreateADMServiceTopology.Parameters.json a aktualizujte hodnoty **název_projektu** a **artefaktuSourceSASLocation**.

Otevřete soubor CreateADMRollout.Parameters.json a aktualizujte hodnoty **název_projektu** a **artefaktuSourceSASLocation**.

## <a name="create-a-health-check-service-simulator"></a>Vytvoření simulátoru služby kontroly stavu

V produkčním prostředí obvykle používáte jednoho nebo více poskytovatelů monitorování. Aby byla integrace stavu co nejjednodušší, společnost Microsoft spolupracuje s některými špičkovými společnostmi pro sledování stavu služeb, aby vám poskytla jednoduché řešení kopírování a vkládání pro integraci kontrol stavu s vašimi nasazeními. Seznam těchto společností naleznete v tématu [Poskytovatelé monitorování stavu](./deployment-manager-health-check.md#health-monitoring-providers). Pro účely tohoto kurzu vytvoříte [funkci Azure](/azure/azure-functions/) pro simulaci služby monitorování stavu. Tato funkce přebírá stavový kód a vrací stejný kód. Vaše šablona Správce nasazení Azure používá stavový kód k určení, jak pokračovat v nasazení.

Následující dva soubory se používají k nasazení funkce Azure. Nemusíte stahovat tyto soubory projít výukový program.

* Šablona Správce prostředků [https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json)umístěná na adrese . Tuto šablonu nasadíte k vytvoření funkce Azure.
* Soubor zip zdrojového kódu funkce [https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMHCFunction0417.zip](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMHCFunction0417.zip)Azure . Toto volání zip je voláno šablonou Správce prostředků.

Pokud chcete nasadit funkci Azure, vyberte **Try it** to open the Azure Cloud shell a then paste the following script into the shell window.  Pokud chcete kód vložit, klikněte pravým tlačítkem myši na okno prostředí a pak vyberte **Vložit**.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json" -projectName $projectName
```

Ověření a testování funkce Azure:

1. Otevřete [portál Azure](https://portal.azure.com).
1. Otevřete skupinu prostředků.  Výchozí název je název projektu s **rg** připojen.
1. Vyberte službu aplikace ze skupiny prostředků.  Výchozí název služby aplikace je název projektu s **připojeným webapp.**
1. Rozbalte **funkce**a vyberte **možnost HttpTrigger1**.

    ![Kontrola stavu Azure Správce nasazení Azure](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-function.png)

1. Vyberte ** &lt;/> Získat adresu URL funkce**.
1. Vyberte **Kopírovat,** chcete-li zkopírovat adresu URL do schránky.  Adresa URL je podobná:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/{healthStatus}?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Nahraďte `{healthStatus}` adresu URL stavovým kódem. V tomto kurzu použijte **není v pořádku** otestovat scénář není v pořádku a použít buď v **pořádku** nebo **upozornění** k testování stavu scénář. Vytvořte dvě adresy URL, jednu se stavem Není v pořádku a druhou se stavem V pořádku. Příklady:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/healthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    K dokončení tohoto kurzu potřebujete obě adresy URL.

1. Chcete-li otestovat simulátor monitorování stavu, otevřete adresy URL, které jste vytvořili v posledním kroku.  Výsledky stavu není v pořádku musí být podobné:

    ```
    Status: unhealthy
    ```

## <a name="revise-the-rollout-template"></a>Revize šablony pro zavedení

Účelem této části je ukázat, jak zahrnout krok kontroly stavu do šablony zavedení.

1. Otevřete **soubor CreateADMRollout.json,** který jste vytvořili ve [Správci nasazení Azure se šablonami Správce prostředků](./deployment-manager-tutorial.md). Tento soubor JSON je součástí stahování.  Viz [Požadavky](#prerequisites).
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

1. Nahraďte definici prostředku kroku čekání definicí prostředku kroku kontroly stavu:

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

    Na základě definice pokračuje zavedení, pokud je stav ový stav *v pořádku* nebo *upozornění*.

1. Aktualizujte **dependsON** definice zavedení zahrnout nově definovaný krok kontroly stavu:

    ```json
    "dependsOn": [
      "[resourceId('Microsoft.DeploymentManager/artifactSources', variables('rolloutArtifactSource').name)]",
      "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
    ],
    ```

1. Aktualizujte **krokové skupiny** tak, aby zahrnovaly krok kontroly stavu. **HealthCheckStep** se nazývá v **postDeploymentSteps** of **stepGroup2**. **stepGroup3** a **stepGroup4** jsou nasazeny pouze v případě, že stav v pořádku je *v pořádku* nebo *upozornění*.

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

    Pokud porovnáte **krokGroup3** oddíl před a po revizi, tato část nyní závisí na **krokGroup2**.  To je nezbytné, když **krokGroup3** a následné krok skupiny závisí na výsledcích monitorování stavu.

    Následující snímek obrazovky znázorňuje upravené oblasti a způsob použití kroku kontroly stavu:

    ![Šablona kontroly stavu Správce nasazení Azure](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-rollout-template.png)

## <a name="deploy-the-topology"></a>Nasazení topologie

Spusťte následující skript Prostředí PowerShell a nasaďte topologii. Potřebujete stejné **CreateADMServiceTopology.json** a **CreateADMServiceTopology.Parameters.json,** které jste použili v [použití Správce nasazení Azure se šablonami Správce prostředků](./deployment-manager-tutorial.md).

```azurepowershell
# Create the service topology
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
    -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
```

Pomocí webu Azure Portal ověřte úspěšné vytvoření topologie služby a prostředků:

![Kurz Azure Deployment Manageru – nasazené prostředky topologie služby](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

Políčko **Zobrazit skryté typy** musí být zaškrtnuté, aby se prostředky zobrazily.

## <a name="deploy-the-rollout-with-the-unhealthy-status"></a>Nasazení s stavem Není v pořádku

Použijte adresu URL stavu Není v pořádku, kterou jste vytvořili v [okně Vytvořit simulátor služby kontroly stavu](#create-a-health-check-service-simulator). Potřebujete revidovanou soubor **CreateADMServiceTopology.json** a stejný **soubor CreateADMServiceTopology.Parameters.json,** který jste použili ve [Správci nasazení Azure se šablonami Správce prostředků](./deployment-manager-tutorial.md).

```azurepowershell-interactive
$healthCheckUrl = Read-Host -Prompt "Enter the health check Azure function URL"
$healthCheckAuthAPIKey = $healthCheckUrl.Substring($healthCheckUrl.IndexOf("?code=")+6, $healthCheckUrl.Length-$healthCheckUrl.IndexOf("?code=")-6)
$healthCheckUrl = $healthCheckUrl.Substring(0, $healthCheckUrl.IndexOf("?"))

# Create the rollout
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
    -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json" `
    -healthCheckUrl $healthCheckUrl `
    -healthCheckAuthAPIKey $healthCheckAuthAPIKey
```

> [!NOTE]
> `New-AzResourceGroupDeployment`je asynchronní volání. Zpráva o úspěchu pouze znamená, že nasazení bylo úspěšně zahájeno. Chcete-li ověřit `Get-AZDeploymentManagerRollout`nasazení, použijte .  Podívejte se na další postup.

Postup zavádění pomocí následujícího skriptu Prostředí PowerShell:

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

Následující ukázkový výstup ukazuje, že nasazení se nezdařilo z důvodu stavu Není v pořádku:

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

Po dokončení zavedení se zobrazí jedna další skupina prostředků vytvořená pro západní USA.

## <a name="deploy-the-rollout-with-the-healthy-status"></a>Nasazení s stavem V pořádku

Opakováním této části znovu nasaďte zavedení s adresou URL stavu v pořádku.  Po dokončení zavedení se zobrazí další skupina prostředků vytvořená pro usa – východ.

## <a name="verify-the-deployment"></a>Ověření nasazení

1. Otevřete [portál Azure](https://portal.azure.com).
2. Přejděte k nově vytvořeným webovým aplikacím v nových skupinách prostředků vytvořených nasazením uvedení.
3. Otevřete webovou aplikaci ve webovém prohlížeči. Zkontrolujte umístění a verzi souboru index.html.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure vyberte **skupinu prostředků** z levé nabídky.
2. Pomocí pole **Filtrovat podle názvu** můžete vyfiltrovat skupiny prostředků vytvořené v tomto kurzu. Měly by být 3 až 4:

    * název_projektu>rg : obsahuje prostředky Správce nasazení. ** &lt;**
    * název_projektu>ServiceWUSrg : obsahuje zdroje definované službou ServiceWUS. ** &lt;**
    * název projektu>ServiceEUSrg : obsahuje zdroje definované serviceEUS. ** &lt;**
    * Skupina prostředků pro spravovanou identitu přiřazenou uživatelem.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků.**
5. Zopakujte poslední dva kroky a odstraňte ostatní skupiny prostředků vytvořené v rámci tohoto kurzu.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili používat funkci kontroly stavu správce nasazení Azure. Další informace najdete v [dokumentaci k Azure Resource Manageru](/azure/azure-resource-manager/).
