---
title: Monitorování nasazeného clusteru služby Azure Kubernetes Service (AKS) | Dokumenty společnosti Microsoft
description: Zjistěte, jak povolit monitorování clusteru služby Azure Kubernetes Service (AKS) pomocí Azure Monitoru pro kontejnery, které už máte nasazené ve vašem předplatném.
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 8589ea71b5c7affadc61d5e4543f734a660ab543
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275448"
---
# <a name="enable-monitoring-of-azure-kubernetes-service-aks-cluster-already-deployed"></a>Povolit monitorování clusteru Služby Azure Kubernetes (AKS), který je již nasazený

Tento článek popisuje, jak nastavit Azure Monitor pro kontejnery pro monitorování spravovaného clusteru Kubernetes hostovaného ve [službě Azure Kubernetes,](https://docs.microsoft.com/azure/aks/) které už byly nasazeny ve vašem předplatném.

Můžete povolit monitorování clusteru AKS, který je již nasazen pomocí jedné z podporovaných metod:

* Azure CLI
* Terraform
* [Z Azure Monitoru](#enable-from-azure-monitor-in-the-portal) nebo [přímo z clusteru AKS](#enable-directly-from-aks-cluster-in-the-portal) na webu Azure Portal
* S [poskytnutou šablonou Azure Resource Manager](#enable-using-an-azure-resource-manager-template) pomocí rutiny Azure PowerShell `New-AzResourceGroupDeployment` nebo pomocí azure cli.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure](https://portal.azure.com).

## <a name="enable-using-azure-cli"></a>Povolení používání azure cli

Následující krok umožňuje monitorování clusteru AKS pomocí Azure CLI. V tomto příkladu není nutné vytvořit nebo zadat existující pracovní prostor. Tento příkaz zjednodušuje proces vytvořením výchozího pracovního prostoru ve výchozí skupině prostředků předplatného clusteru AKS, pokud v oblasti ještě neexistuje.  Výchozí vytvořený pracovní prostor se podobá formátu *\<DefaultWorkspace-\<GUID>- Region>*.  

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG  
```

Výstup se bude podobat následujícímu:

```output
provisioningState       : Succeeded
```

### <a name="integrate-with-an-existing-workspace"></a>Integrace s existujícím pracovním prostorem

Pokud byste raději integrovat s existujícím pracovním prostorem, proveďte následující kroky k první identifikaci `--workspace-resource-id` úplné ID prostředku vašeho pracovního prostoru Log Analytics potřebné pro parametr a pak spusťte příkaz povolit monitorování doplněk proti zadanému pracovnímu prostoru.  

1. Seznam všech předplatných, ke kterým máte přístup pomocí následujícího příkazu:

    ```azurecli
    az account list --all -o table
    ```

    Výstup se bude podobat následujícímu:

    ```output
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    Zkopírujte hodnotu **pro SubscriptionId**.

2. Přepněte na předplatné hostující pracovní prostor Log Analytics pomocí následujícího příkazu:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. Následující příklad zobrazuje seznam pracovních prostorů ve vašich předplatných ve výchozím formátu JSON.

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Ve výstupu najděte název pracovního prostoru a zkopírujte celé ID prostředku tohoto pracovního prostoru Analýzy protokolů pod **ID**pole .

4. Spuštěním následujícího příkazu povolte doplněk monitorování a `--workspace-resource-id` nahrazujte hodnotu parametru. Hodnota řetězce musí být v rámci dvojitých uvozovek:

    ```azurecli
    az aks enable-addons -a monitoring -n ExistingManagedCluster -g ExistingManagedClusterRG --workspace-resource-id "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>"
    ```

    Výstup se bude podobat následujícímu:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-using-terraform"></a>Povolit pomocí Terraform

1. Přidání profilu doplňku **oms_agent** do existujícího [zdroje azurerm_kubernetes_cluster](https://www.terraform.io/docs/providers/azurerm/d/kubernetes_cluster.html#addon_profile)

   ```
   addon_profile {
    oms_agent {
      enabled                    = true
      log_analytics_workspace_id = "${azurerm_log_analytics_workspace.test.id}"
     }
   }
   ```

2. Přidejte [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) podle kroků v dokumentaci Terraform.

## <a name="enable-from-azure-monitor-in-the-portal"></a>Povolení z Azure Monitoru na portálu

Pokud chcete povolit monitorování clusteru AKS na webu Azure Portal z Azure Monitoru, postupujte takto:

1. Na webu Azure Portal vyberte **Monitor**.

2. Ze seznamu vyberte **Kontejnery.**

3. Na stránce **Monitor - kontejnery** vyberte **Nemonitorované clustery**.

4. V seznamu nesledovaných clusterů vyhledejte kontejner v seznamu a klepněte na tlačítko **Povolit**.   

5. Na stránce **Onboarding to Azure Monitor for containers,** pokud máte existující pracovní prostor Log Analytics ve stejném předplatném jako cluster, vyberte ho z rozevíracího seznamu.  
    Seznam předem vybere výchozí pracovní prostor a umístění, které kontejner AKS je nasazen do v předplatném.

    ![Povolení monitorování přehledů kontejnerů AKS](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Pokud chcete vytvořit nový pracovní prostor Analýzy protokolů pro ukládání dat monitorování z clusteru, postupujte podle pokynů v [části Vytvoření pracovního prostoru Analýzy protokolů](../../azure-monitor/learn/quick-create-workspace.md). Nezapomeňte vytvořit pracovní prostor ve stejném předplatném, které kontejner AKS je nasazen.

Po povolení monitorování může trvat přibližně 15 minut, než budete moci zobrazit metriky stavu pro cluster.

## <a name="enable-directly-from-aks-cluster-in-the-portal"></a>Povolení přímo z clusteru AKS na portálu

Pokud chcete povolit monitorování přímo z jednoho z vašich clusterů AKS na webu Azure Portal, postupujte takto:

1. Na webu Azure Portal vyberte **Všechny služby**.

2. V seznamu zdrojů začněte psát **kontejnery**.  Seznam filtruje na základě vašeho vstupu.

3. Vyberte **služby Kubernetes**.  

    ![Odkaz služby Kubernetes](./media/container-insights-onboard/portal-search-containers-01.png)

4. V seznamu kontejnerů vyberte kontejner.

5. Na stránce přehled kontejnerů vyberte **Monitor kontejnery**.  

6. Na stránce **Onboarding to Azure Monitor for containers,** pokud máte existující pracovní prostor Log Analytics ve stejném předplatném jako cluster, vyberte ho v rozevíracím seznamu.  
    Seznam předem vybere výchozí pracovní prostor a umístění, které kontejner AKS je nasazen do v předplatném.

    ![Povolení monitorování stavu kontejneru AKS](./media/container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >Pokud chcete vytvořit nový pracovní prostor Analýzy protokolů pro ukládání dat monitorování z clusteru, postupujte podle pokynů v [části Vytvoření pracovního prostoru Analýzy protokolů](../../azure-monitor/learn/quick-create-workspace.md). Nezapomeňte vytvořit pracovní prostor ve stejném předplatném, které kontejner AKS je nasazen.

Po povolení monitorování může trvat přibližně 15 minut, než budete moci zobrazit provozní data pro cluster.

## <a name="enable-using-an-azure-resource-manager-template"></a>Povolení použití šablony Azure Resource Manageru

Tato metoda zahrnuje dvě šablony JSON. Jedna šablona určuje konfiguraci pro povolení monitorování a druhá obsahuje hodnoty parametrů, které nakonfigurujete tak, aby určila následující:

* ID prostředku kontejneru AKS.
* Skupina prostředků, ve které je cluster nasazen.

>[!NOTE]
>Šablonu je třeba nasadit ve stejné skupině prostředků jako cluster.
>

Pracovní prostor Log Analytics musí být vytvořen před povolením monitorování pomocí Azure PowerShell nebo CLI. Pokud chcete vytvořit pracovní prostor, můžete ho nastavit prostřednictvím [Azure Resource Manageru](../../azure-monitor/platform/template-workspace-configuration.md), přes [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)nebo na [webu Azure Portal](../../azure-monitor/learn/quick-create-workspace.md).

Pokud nejste obeznámeni s konceptem nasazování prostředků pomocí šablony, přečtěte si:

* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../../azure-resource-manager/templates/deploy-powershell.md)

* [Nasazení prostředků pomocí šablon Správce prostředků a nastavení příkazového uživatelského příkazu Azure](../../azure-resource-manager/templates/deploy-cli.md)

Pokud se rozhodnete použít azure cli, musíte nejprve nainstalovat a použít příkazového příkazového příkazu místně. Musíte spouštět Azure CLI verze 2.0.59 nebo novější. Chcete-li identifikovat `az --version`verzi, spusťte aplikaci . Pokud potřebujete nainstalovat nebo upgradovat vázačitelné příkazy k Webu Azure, přečtěte si informace [o instalaci příkazového příkazového příkazu k azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="create-and-execute-a-template"></a>Vytvoření a spuštění šablony

1. Zkopírujte a vložte do souboru následující syntaxi JSON:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "type": "string",
          "metadata": {
            "description": "AKS Cluster Resource ID"
          }
        },
        "aksResourceLocation": {
          "type": "string",
          "metadata": {
            "description": "Location of the AKS resource e.g. \"East US\""
          }
        },
        "aksResourceTagValues": {
          "type": "object",
          "metadata": {
            "description": "Existing all tags on AKS Cluster Resource"
          }
        },
        "workspaceResourceId": {
          "type": "string",
          "metadata": {
            "description": "Azure Monitor Log Analytics Resource ID"
          }
        }
      },
      "resources": [
        {
          "name": "[split(parameters('aksResourceId'),'/')[8]]",
          "type": "Microsoft.ContainerService/managedClusters",
          "location": "[parameters('aksResourceLocation')]",
          "tags": "[parameters('aksResourceTagValues')]",
          "apiVersion": "2018-03-31",
          "properties": {
            "mode": "Incremental",
            "id": "[parameters('aksResourceId')]",
            "addonProfiles": {
              "omsagent": {
                "enabled": true,
                "config": {
                  "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
                }
              }
            }
          }
        }
      ]
    }
    ```

2. Uložte tento soubor jako **existingClusterOnboarding.json** do místní složky.

3. Do souboru vložte následující syntaxi JSON:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "value": "/subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
        },
        "aksResourceLocation": {
          "value": "<aksClusterLocation>"
        },
        "workspaceResourceId": {
          "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
        },
        "aksResourceTagValues": {
          "value": {
            "<existing-tag-name1>": "<existing-tag-value1>",
            "<existing-tag-name2>": "<existing-tag-value2>",
            "<existing-tag-nameN>": "<existing-tag-valueN>"
          }
        }
      }
    }
    ```

4. Upravte hodnoty pro **aksResourceId** a **aksResourceLocation** pomocí hodnot na stránce **Přehled AKS** pro cluster AKS. Hodnota pro **pracovní prostorResourceId** je úplné ID prostředku pracovního prostoru Analýzy protokolů, který obsahuje název pracovního prostoru.

    Upravte hodnoty **aksResourceTagValues** tak, aby odpovídaly existujícím hodnotám značek určeným pro cluster AKS.

5. Uložte tento soubor jako **existující ClusterParam.json** do místní složky.

6. Jste připraveni k nasazení této šablony.

   * Pokud chcete nasadit pomocí Azure PowerShellu, použijte ve složce, která šablonu obsahuje, následující příkazy:

       ```powershell
       New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
       ```

       Změna konfigurace může trvat několik minut. Po dokončení se zobrazí zpráva podobná následující a obsahuje výsledek:

       ```output
       provisioningState       : Succeeded
       ```

   * Chcete-li nasadit pomocí příkazového příkazu k řešení Azure, spusťte následující příkazy:

       ```azurecli
       az login
       az account set --subscription "Subscription Name"
       az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
       ```

       Změna konfigurace může trvat několik minut. Po dokončení se zobrazí zpráva podobná následující a obsahuje výsledek:

       ```output
       provisioningState       : Succeeded
       ```

       Po povolení monitorování může trvat přibližně 15 minut, než budete moci zobrazit metriky stavu pro cluster.

## <a name="verify-agent-and-solution-deployment"></a>Ověření nasazení agenta a řešení

S verzí *agenta 06072018* nebo novější, můžete ověřit, že agent i řešení byly úspěšně nasazeny. S dřívějšími verzemi agenta můžete ověřit pouze nasazení agenta.

### <a name="agent-version-06072018-or-later"></a>Agent verze 06072018 nebo novější

Spusťte následující příkaz a ověřte, zda je agent úspěšně nasazen.

```
kubectl get ds omsagent --namespace=kube-system
```

Výstup by se měl podobat následujícímu, což znamená, že byl správně nasazen:

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Chcete-li ověřit nasazení řešení, spusťte následující příkaz:

```
kubectl get deployment omsagent-rs -n=kube-system
```

Výstup by se měl podobat následujícímu, což znamená, že byl správně nasazen:

```output
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Verze agenta starší než 06072018

Chcete-li ověřit, zda je verze agenta Analýzy protokolů vydaná před správnou nasazením *06072018,* spusťte následující příkaz:  

```
kubectl get ds omsagent --namespace=kube-system
```

Výstup by se měl podobat následujícímu, což znamená, že byl správně nasazen:  

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Zobrazit konfiguraci pomocí cli

Pomocí `aks show` příkazu můžete získat podrobnosti, jako je například povoleno nebo ne, co je ID prostředků pracovního prostoru Analýzy protokolů a souhrnné podrobnosti o clusteru.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Po několika minutách příkaz dokončí a vrátí informace o řešení ve formátu JSON.  Výsledky příkazu by měly zobrazovat profil doplňku monitorování a podobat se následujícímu ukázkovému výstupu:

```output
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="next-steps"></a>Další kroky

* Pokud při pokusu o připojení k řešení narazíte na problémy, přečtěte si [průvodce odstraňováním potíží](container-insights-troubleshoot.md)

* Díky monitorování, které umožňuje shromažďovat využití stavu a prostředků vašeho clusteru AKS a úloh, které na nich běží, najdete informace o [tom, jak používat](container-insights-analyze.md) Azure Monitor pro kontejnery.
