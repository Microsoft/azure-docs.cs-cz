---
title: Monitorování nasazeného clusteru AKS (Azure Kubernetes Service) | Microsoft Docs
description: Naučte se, jak povolit monitorování clusteru Azure Kubernetes Service (AKS) s Azure Monitor pro kontejnery, které jsou už ve vašem předplatném nasazené.
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: devx-track-terraform, devx-track-azurecli
ms.openlocfilehash: 547c22e4d82aa728009a2fdb42f2c3b481b7a625
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2020
ms.locfileid: "97695648"
---
# <a name="enable-monitoring-of-azure-kubernetes-service-aks-cluster-already-deployed"></a>Povolení monitorování clusteru Azure Kubernetes Service (AKS) již nasazeného

Tento článek popisuje, jak nastavit Azure Monitor pro kontejnery pro monitorování spravovaného clusteru Kubernetes hostovaného ve [službě Azure Kubernetes](../../aks/index.yml) , které už jsou v předplatném nasazené.

Můžete povolit monitorování clusteru AKS, který je už nasazený, pomocí jedné z podporovaných metod:

* Azure CLI
* Terraform
* [Z Azure monitor](#enable-from-azure-monitor-in-the-portal) nebo [přímo z clusteru AKS](#enable-directly-from-aks-cluster-in-the-portal) v Azure Portal
* S [poskytnutou šablonou Azure Resource Manager](#enable-using-an-azure-resource-manager-template) pomocí rutiny Azure PowerShell `New-AzResourceGroupDeployment` nebo pomocí Azure CLI.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="enable-using-azure-cli"></a>Povolení pomocí Azure CLI

Následující krok umožňuje monitorovat cluster AKS pomocí Azure CLI. V tomto příkladu není nutné předem vytvořit nebo zadat existující pracovní prostor. Tento příkaz zjednodušuje proces vytvořením výchozího pracovního prostoru ve výchozí skupině prostředků v rámci předplatného clusteru AKS, pokud ještě neexistuje v oblasti.  Výchozí vytvořený pracovní prostor se podobá formátu *DefaultWorkspace- \<GUID> - \<Region>*.

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Výstup bude vypadat přibližně takto:

```output
provisioningState       : Succeeded
```

### <a name="integrate-with-an-existing-workspace"></a>Integrace s existujícím pracovním prostorem

Pokud místo toho budete chtít provést integraci s existujícím pracovním prostorem, proveďte následující kroky, abyste nejdřív identifikovali úplné ID prostředku Log Analytics pracovního prostoru požadovaného pro daný `--workspace-resource-id` parametr, a pak spuštěním příkazu povolíte doplněk monitorování v zadaném pracovním prostoru.

1. Vypíše seznam všech předplatných, ke kterým máte přístup, pomocí následujícího příkazu:

    ```azurecli
    az account list --all -o table
    ```

    Výstup bude vypadat přibližně takto:

    ```output
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    Zkopírujte hodnotu pro **SubscriptionId**.

2. Přepněte do předplatného hostujícího Log Analytics pracovní prostor pomocí následujícího příkazu:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. Následující příklad zobrazí seznam pracovních prostorů v předplatných ve výchozím formátu JSON.

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Ve výstupu vyhledejte název pracovního prostoru a zkopírujte úplné ID prostředku, které Log Analytics pracovní prostor pod **ID** pole.

4. Spuštěním následujícího příkazu povolte doplněk monitorování a nahraďte hodnotu `--workspace-resource-id` parametru. Řetězcová hodnota musí být v uvozovkách:

    ```azurecli
    az aks enable-addons -a monitoring -n ExistingManagedCluster -g ExistingManagedClusterRG --workspace-resource-id "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>"
    ```

    Výstup bude vypadat přibližně takto:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-using-terraform"></a>Povolit pomocí Terraformu

1. Přidání profilu **oms_agentho** doplňku do existujícího [prostředku azurerm_kubernetes_cluster](https://www.terraform.io/docs/providers/azurerm/d/kubernetes_cluster.html#addon_profile)

   ```
   addon_profile {
    oms_agent {
      enabled                    = true
      log_analytics_workspace_id = "${azurerm_log_analytics_workspace.test.id}"
     }
   }
   ```

2. Přidejte [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) podle kroků v dokumentaci k terraformu.

## <a name="enable-from-azure-monitor-in-the-portal"></a>Povolení z Azure Monitor na portálu

Pokud chcete povolit monitorování clusteru AKS v Azure Portal z Azure Monitor, udělejte toto:

1. V Azure Portal vyberte **monitorovat**.

2. V seznamu vyberte **kontejnery** .

3. Na stránce **monitor – kontejnery** vyberte **nemonitorované clustery**.

4. V seznamu nemonitorovaných clusterů Najděte kontejner v seznamu a klikněte na **Povolit**.

5. Pokud máte existující pracovní prostor Log Analytics v rámci stejného předplatného jako cluster, vyberte v rozevíracím seznamu na stránce **připojování k Azure monitor for Containers** .
    Seznam předchází výchozí pracovní prostor a umístění, do kterého je kontejner AKS nasazený v rámci předplatného.

    ![Povolit monitorování AKS Container Insights](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Pokud chcete vytvořit nový pracovní prostor Log Analytics pro uložení dat monitorování z clusteru, postupujte podle pokynů v tématu [Vytvoření pracovního prostoru Log Analytics](../learn/quick-create-workspace.md). Nezapomeňte vytvořit pracovní prostor ve stejném předplatném, do kterého je kontejner AKS nasazený.

Po povolení monitorování může trvat přibližně 15 minut, než budete moct zobrazit metriky stavu clusteru.

## <a name="enable-directly-from-aks-cluster-in-the-portal"></a>Povolení přímo z clusteru AKS na portálu

Pokud chcete monitorování povolit přímo z jednoho z clusterů AKS v Azure Portal, udělejte toto:

1. V Azure Portal vyberte **všechny služby**.

2. V seznamu prostředků začněte psát **kontejnery**.  Seznam se filtruje podle vašeho zadání.

3. Vyberte **Kubernetes Services**.
    
4. V seznamu služeb Kubernetes vyberte službu.

5. Na stránce Přehled služby Kubernetes vyberte **monitorování – přehledy**.

6. Pokud máte existující pracovní prostor Log Analytics ve stejném předplatném jako cluster, vyberte v rozevíracím seznamu na stránce **připojování k Azure monitor for Containers** .
    Seznam předchází výchozí pracovní prostor a umístění, do kterého je kontejner AKS nasazený v rámci předplatného.

    ![Povolit monitorování stavu kontejneru AKS](./media/container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >Pokud chcete vytvořit nový pracovní prostor Log Analytics pro uložení dat monitorování z clusteru, postupujte podle pokynů v tématu [Vytvoření pracovního prostoru Log Analytics](../learn/quick-create-workspace.md). Nezapomeňte vytvořit pracovní prostor ve stejném předplatném, do kterého je kontejner AKS nasazený.

Po povolení monitorování může trvat přibližně 15 minut, než budete moct zobrazit provozní data clusteru.

## <a name="enable-using-an-azure-resource-manager-template"></a>Povolení použití šablony Azure Resource Manager

Tato metoda zahrnuje dvě šablony JSON. Jedna šablona určuje konfiguraci pro povolení monitorování a druhá obsahuje hodnoty parametrů, které nakonfigurujete, aby určovaly následující:

* ID prostředku kontejneru AKS
* Skupina prostředků, ve které je cluster nasazen.

>[!NOTE]
>Šablona musí být nasazená ve stejné skupině prostředků jako cluster.
>

Aby bylo možné povolit monitorování pomocí Azure PowerShell nebo rozhraní příkazového řádku, je třeba vytvořit pracovní prostor Log Analytics. Pokud chcete vytvořit pracovní prostor, můžete ho nastavit prostřednictvím [Azure Resource Manager](../samples/resource-manager-workspace.md), prostřednictvím [PowerShellu](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)nebo v [Azure Portal](../learn/quick-create-workspace.md).

Pokud nejste obeznámeni s konceptem nasazení prostředků pomocí šablony, přečtěte si téma:

* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../../azure-resource-manager/templates/deploy-powershell.md)

* [Nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Pokud se rozhodnete používat rozhraní příkazového řádku Azure, musíte nejdřív nainstalovat a používat rozhraní příkazového řádku (CLI). Musíte používat Azure CLI verze 2.0.59 nebo novější. Pro identifikaci vaší verze spusťte `az --version` . Pokud potřebujete nainstalovat nebo upgradovat rozhraní příkazového řádku Azure CLI, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli).

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

2. Uložte tento soubor jako **existingClusterOnboarding.js** do místní složky.

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

4. Upravte hodnoty pro **aksResourceId** a **aksResourceLocation** pomocí hodnot na stránce s **přehledem AKS** pro cluster AKS. Hodnota pro **workspaceResourceId** je úplné ID prostředku pracovního prostoru Log Analytics, který zahrnuje název pracovního prostoru.

    Upravte hodnoty pro **aksResourceTagValues** tak, aby odpovídaly existujícím hodnotám značek zadaným pro cluster AKS.

5. Uložte tento soubor jako **existingClusterParam.js** do místní složky.

6. Jste připraveni k nasazení této šablony.

   * K nasazení pomocí Azure PowerShell použijte ve složce obsahující šablonu tyto příkazy:

       ```powershell
       New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
       ```

       Dokončení změny konfigurace může trvat několik minut. Po dokončení se zobrazí zpráva podobná následující zprávě, která obsahuje výsledek:

       ```output
       provisioningState       : Succeeded
       ```

   * Pokud ho chcete nasadit pomocí Azure CLI, spusťte následující příkazy:

       ```azurecli
       az login
       az account set --subscription "Subscription Name"
       az deployment group create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
       ```

       Dokončení změny konfigurace může trvat několik minut. Po dokončení se zobrazí zpráva podobná následující zprávě, která obsahuje výsledek:

       ```output
       provisioningState       : Succeeded
       ```

       Po povolení monitorování může trvat přibližně 15 minut, než budete moct zobrazit metriky stavu clusteru.

## <a name="verify-agent-and-solution-deployment"></a>Ověření nasazení agenta a řešení

S agentem verze *06072018* nebo novější můžete ověřit, že se agent i řešení úspěšně nasadily. V dřívějších verzích agenta můžete ověřovat jenom nasazení agenta.

### <a name="agent-version-06072018-or-later"></a>Agent verze 06072018 nebo novější

Spusťte následující příkaz a ověřte, zda byl agent úspěšně nasazen.

```
kubectl get ds omsagent --namespace=kube-system
```

Výstup by měl vypadat podobně jako v následujícím příkladu, což znamená, že byl správně nasazen:

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```

Pokud v clusteru existují uzly Windows serveru, můžete spustit následující příkaz, který ověří, jestli je agent úspěšně nasazený.

```
kubectl get ds omsagent-win --namespace=kube-system
```

Výstup by měl vypadat podobně jako v následujícím příkladu, což znamená, že byl správně nasazen:

```output
User@aksuser:~$ kubectl get ds omsagent-win --namespace=kube-system
NAME                   DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                   AGE
omsagent-win           2         2         2         2            2           beta.kubernetes.io/os=windows   1d
```

Pokud chcete ověřit nasazení řešení, spusťte následující příkaz:

```
kubectl get deployment omsagent-rs -n=kube-system
```

Výstup by měl vypadat podobně jako v následujícím příkladu, což znamená, že byl správně nasazen:

```output
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Verze agenta starší než 06072018

Pokud chcete ověřit, jestli je verze agenta Log Analytics vydaná před správným nasazením *06072018* , spusťte následující příkaz:

```
kubectl get ds omsagent --namespace=kube-system
```

Výstup by měl vypadat podobně jako v následujícím příkladu, což znamená, že byl správně nasazen:

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```

## <a name="view-configuration-with-cli"></a>Zobrazení konfigurace pomocí rozhraní příkazového řádku

Pomocí `aks show` příkazu získáte podrobné informace, jako je řešení povoleno nebo ne, co je Log Analytics pracovní prostor ResourceID a souhrnné podrobnosti o clusteru.

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Po několika minutách se příkaz dokončí a vrátí informace o řešení ve formátu JSON.  Výsledky příkazu by měly zobrazit profil doplňku monitorování a vypadat podobně jako v následujícím příkladu výstupu:

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

* Pokud při pokusu o připojení řešení dochází k problémům, přečtěte si [příručku k odstraňování potíží](container-insights-troubleshoot.md) .

* Díky monitorování s povoleným shromažďováním informací o stavu a využití prostředků v clusteru AKS a úlohách, které se na nich běží, se naučíte, [Jak používat](container-insights-analyze.md) Azure monitor pro kontejnery.
