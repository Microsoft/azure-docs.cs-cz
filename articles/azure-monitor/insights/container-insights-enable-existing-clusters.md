---
title: Monitorování nasazeného clusteru AKS (Azure Kubernetes Service) | Microsoft Docs
description: Naučte se, jak povolit monitorování clusteru Azure Kubernetes Service (AKS) s Azure Monitor pro kontejnery, které jsou už ve vašem předplatném nasazené.
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 8589ea71b5c7affadc61d5e4543f734a660ab543
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275448"
---
# <a name="enable-monitoring-of-azure-kubernetes-service-aks-cluster-already-deployed"></a>Povolení monitorování clusteru Azure Kubernetes Service (AKS) již nasazeného

Tento článek popisuje, jak nastavit Azure Monitor pro kontejnery pro monitorování spravovaného clusteru Kubernetes hostovaného ve [službě Azure Kubernetes](https://docs.microsoft.com/azure/aks/) , které už jsou v předplatném nasazené.

Můžete povolit monitorování clusteru AKS, který je už nasazený, pomocí jedné z podporovaných metod:

* Azure CLI
* Terraform
* [Z Azure monitor](#enable-from-azure-monitor-in-the-portal) nebo [přímo z clusteru AKS](#enable-directly-from-aks-cluster-in-the-portal) v Azure Portal
* Pomocí [zadané šablony Azure Resource Manager](#enable-using-an-azure-resource-manager-template) pomocí rutiny Azure PowerShell `New-AzResourceGroupDeployment` nebo pomocí Azure CLI.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

## <a name="enable-using-azure-cli"></a>Povolení s využitím rozhraní příkazového řádku Azure

Následující krok zapne monitorování clusteru AKS pomocí Azure CLI. V tomto příkladu nemusíte za vytvoření nebo zadejte existující pracovní prostor. Tento příkaz zjednodušuje proces pro vás vytvořením výchozího pracovního prostoru do výchozí skupiny prostředků předplatného cluster AKS, pokud již neexistuje v oblasti.  Výchozí vytvořený pracovní prostor se podobá formátu *DefaultWorkspace\<>\<oblasti >* .  

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG  
```

Výstup bude vypadat takto:

```output
provisioningState       : Succeeded
```

### <a name="integrate-with-an-existing-workspace"></a>Integrace s existujícím pracovním prostorem

Pokud místo toho budete chtít provést integraci s existujícím pracovním prostorem, proveďte následující kroky, abyste nejdřív identifikovali úplné ID prostředku Log Analytics pracovního prostoru požadovaného pro parametr `--workspace-resource-id` a pak spustíte příkaz pro povolení doplňku monitorování v zadaném pracovním prostoru.  

1. Vypíše seznam všech předplatných, ke kterým máte přístup, pomocí následujícího příkazu:

    ```azurecli
    az account list --all -o table
    ```

    Výstup bude vypadat takto:

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

    Ve výstupu vyhledejte název pracovního prostoru a zkopírujte úplné ID prostředku, které Log Analytics pracovní prostor pod **ID**pole.

4. Spusťte následující příkaz pro povolení doplňku monitorování a nahraďte hodnotu parametru `--workspace-resource-id`. Řetězcová hodnota musí být v uvozovkách:

    ```azurecli
    az aks enable-addons -a monitoring -n ExistingManagedCluster -g ExistingManagedClusterRG --workspace-resource-id "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>"
    ```

    Výstup bude vypadat takto:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-using-terraform"></a>Povolení s využitím Terraformu

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

Chcete-li povolit monitorování clusteru AKS na portálu Azure portal ze služby Azure Monitor, postupujte takto:

1. V Azure Portal vyberte **monitorovat**.

2. V seznamu vyberte **kontejnery** .

3. Na stránce **monitor – kontejnery** vyberte **nemonitorované clustery**.

4. V seznamu nemonitorovaných clusterů Najděte kontejner v seznamu a klikněte na **Povolit**.   

5. Pokud máte existující pracovní prostor Log Analytics v rámci stejného předplatného jako cluster, vyberte v rozevíracím seznamu na stránce **připojování k Azure monitor for Containers** .  
    V seznamu vybrána hodnota výchozího pracovního prostoru a umístění, ke kterému kontejneru AKS nasazuje v rámci předplatného.

    ![Povolit monitorování insights kontejneru AKS](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Pokud chcete vytvořit nový pracovní prostor Log Analytics pro uložení dat monitorování z clusteru, postupujte podle pokynů v tématu [Vytvoření pracovního prostoru Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Je potřeba vytvořit pracovní prostor v rámci stejného předplatného, který se nasazuje kontejneru AKS.

Po povolení sledování, může trvat přibližně 15 minut, než se zobrazí stav metriky pro cluster.

## <a name="enable-directly-from-aks-cluster-in-the-portal"></a>Povolení přímo z clusteru AKS na portálu

Pokud chcete monitorování povolit přímo z jednoho z clusterů AKS v Azure Portal, udělejte toto:

1. Na webu Azure Portal vyberte **Všechny služby**.

2. V seznamu prostředků začněte psát **kontejnery**.  Seznam se průběžně filtruje podle vašeho zadání.

3. Vyberte **Kubernetes Services**.  

    ![Propojení služby Kubernetes](./media/container-insights-onboard/portal-search-containers-01.png)

4. V seznamu kontejnerů vyberte kontejner.

5. Na stránce Přehled kontejneru vyberte **monitorovat kontejnery**.  

6. Pokud máte existující pracovní prostor Log Analytics ve stejném předplatném jako cluster, vyberte v rozevíracím seznamu na stránce **připojování k Azure monitor for Containers** .  
    V seznamu vybrána hodnota výchozího pracovního prostoru a umístění, ke kterému kontejneru AKS nasazuje v rámci předplatného.

    ![Povolit monitorování stavu kontejneru AKS](./media/container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >Pokud chcete vytvořit nový pracovní prostor Log Analytics pro uložení dat monitorování z clusteru, postupujte podle pokynů v tématu [Vytvoření pracovního prostoru Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Je potřeba vytvořit pracovní prostor v rámci stejného předplatného, který se nasazuje kontejneru AKS.

Po povolení sledování, může trvat přibližně 15 minut, než lze zobrazit provozní data pro cluster.

## <a name="enable-using-an-azure-resource-manager-template"></a>Povolení použití šablony Azure Resource Manager

Tato metoda obsahuje dvě šablony JSON. Jedna šablona určuje konfiguraci povolení monitorování a druhý obsahuje hodnoty parametrů, které nakonfigurujete, zadejte následující informace:

* ID prostředku kontejneru AKS
* Skupina prostředků, která je nasazená clusteru.

>[!NOTE]
>Šablona musí být nasazený ve stejné skupině prostředků jako cluster.
>

Aby bylo možné povolit monitorování pomocí Azure PowerShell nebo rozhraní příkazového řádku, je třeba vytvořit pracovní prostor Log Analytics. Pokud chcete vytvořit pracovní prostor, můžete ho nastavit prostřednictvím [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), prostřednictvím [PowerShellu](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)nebo v [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md).

Pokud nejste obeznámeni s konceptem nasazení prostředků pomocí šablony, naleznete v tématu:

* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../../azure-resource-manager/templates/deploy-powershell.md)

* [Nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Pokud se rozhodnete používat rozhraní příkazového řádku Azure, musíte nejprve nainstalovat a používat rozhraní příkazového řádku místně. Musíte používat Azure CLI verze 2.0.59 nebo novější. Pro identifikaci vaší verze spusťte `az --version`. Pokud potřebujete nainstalovat nebo upgradovat rozhraní příkazového řádku Azure CLI, přečtěte si téma [instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="create-and-execute-a-template"></a>Vytvoření a provedení šablony

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

2. Uložte tento soubor jako **existingClusterOnboarding. JSON** do místní složky.

3. Vložte následující syntaxi JSON do souboru:

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

5. Uložte tento soubor jako **existingClusterParam. JSON** do místní složky.

6. Jste připraveni k nasazení této šablony.

   * K nasazení pomocí Azure PowerShell použijte ve složce obsahující šablonu tyto příkazy:

       ```powershell
       New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
       ```

       Změna konfigurace může trvat několik minut. Když se dokončí, zobrazí se zpráva, která je podobný následujícímu a zahrnuje výsledek:

       ```output
       provisioningState       : Succeeded
       ```

   * Pokud ho chcete nasadit pomocí Azure CLI, spusťte následující příkazy:

       ```azurecli
       az login
       az account set --subscription "Subscription Name"
       az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
       ```

       Změna konfigurace může trvat několik minut. Když se dokončí, zobrazí se zpráva, která je podobný následujícímu a zahrnuje výsledek:

       ```output
       provisioningState       : Succeeded
       ```

       Po povolení sledování, může trvat přibližně 15 minut, než se zobrazí stav metriky pro cluster.

## <a name="verify-agent-and-solution-deployment"></a>Ověření nasazení agenta a řešení

S agentem verze *06072018* nebo novější můžete ověřit, že se agent i řešení úspěšně nasadily. V předchozích verzích agenta můžete ověřit pouze nasazení agenta.

### <a name="agent-version-06072018-or-later"></a>Verze agenta 06072018 nebo novější

Spusťte následující příkaz k ověření, že je agent úspěšně nasazen.

```
kubectl get ds omsagent --namespace=kube-system
```

Výstup by měl vypadat podobně jako následující text, který označuje, že byla správně nasazena:

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Pokud chcete ověřit nasazení řešení, spusťte následující příkaz:

```
kubectl get deployment omsagent-rs -n=kube-system
```

Výstup by měl vypadat podobně jako následující text, který označuje, že byla správně nasazena:

```output
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Dříve než 06072018 verze agenta

Pokud chcete ověřit, jestli je verze agenta Log Analytics vydaná před správným nasazením *06072018* , spusťte následující příkaz:  

```
kubectl get ds omsagent --namespace=kube-system
```

Výstup by měl vypadat podobně jako následující text, který označuje, že byla správně nasazena:  

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Zobrazit konfiguraci pomocí rozhraní příkazového řádku

Pomocí příkazu `aks show` získat podrobnosti, jako je řešení povoleno nebo ne, co je Log Analytics pracovní prostor resourceID a souhrnné podrobnosti o clusteru.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Po několika minutách se příkaz dokončí a vrátí hodnotu ve formátu JSON informace o řešení.  Výsledky příkazu by se měla zobrazit profil sledování doplněk a vypadá podobně jako následující příklad výstupu:

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
