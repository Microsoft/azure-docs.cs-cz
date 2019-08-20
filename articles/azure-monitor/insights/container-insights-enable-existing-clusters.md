---
title: Monitorování nasazeného clusteru AKS (Azure Kubernetes Service) | Microsoft Docs
description: Naučte se, jak povolit monitorování clusteru Azure Kubernetes Service (AKS) s Azure Monitor pro kontejnery, které jsou už ve vašem předplatném nasazené.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/19/2019
ms.author: magoedte
ms.openlocfilehash: 650729269370bfcd6608b82fc14c3306da1ed222
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624437"
---
# <a name="enable-monitoring-of-azure-kubernetes-service-aks-cluster-already-deployed"></a>Povolení monitorování clusteru Azure Kubernetes Service (AKS) již nasazeného

Tento článek popisuje, jak nastavit Azure Monitor pro kontejnery pro monitorování spravovaného clusteru Kubernetes hostovaného ve [službě Azure Kubernetes](https://docs.microsoft.com/azure/aks/) , které už jsou v předplatném nasazené.

Můžete povolit monitorování clusteru AKS, který je už nasazený, pomocí jedné z podporovaných metod:

* Azure CLI
* Terraform
* [Z Azure monitor](#enable-from-azure-monitor-in-the-portal) nebo [přímo z clusteru AKS](#enable-directly-from-aks-cluster-in-the-portal) v Azure Portal 
* S [poskytnutou šablonou Azure Resource Manager](#enable-using-an-azure-resource-manager-template) pomocí rutiny `New-AzResourceGroupDeployment` Azure PowerShell nebo pomocí Azure CLI. 

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

## <a name="enable-using-azure-cli"></a>Povolení s využitím rozhraní příkazového řádku Azure

Následující krok zapne monitorování clusteru AKS pomocí Azure CLI. V tomto příkladu nemusíte za vytvoření nebo zadejte existující pracovní prostor. Tento příkaz zjednodušuje proces pro vás vytvořením výchozího pracovního prostoru do výchozí skupiny prostředků předplatného cluster AKS, pokud již neexistuje v oblasti.  Výchozí vytvořený pracovní prostor se podobá formátu *DefaultWorkspace-\<\<GUID > > oblasti*.  

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG  
```

Výstup bude vypadat takto:

```azurecli
provisioningState       : Succeeded
```

Pokud by místo toho integrace s existující pracovní prostor, použijte následující příkaz k zadání tohoto pracovního prostoru.

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG --workspace-resource-id <ExistingWorkspaceResourceID> 
```

Výstup bude vypadat takto:

```azurecli
provisioningState       : Succeeded
```

## <a name="enable-using-terraform"></a>Povolení s využitím Terraformu

1. Přidat **oms_agent** doplněk profilu k existujícím [azurerm_kubernetes_cluster prostředků](https://www.terraform.io/docs/providers/azurerm/d/kubernetes_cluster.html#addon_profile)

   ```
   addon_profile {
    oms_agent {
      enabled                    = true
      log_analytics_workspace_id = "${azurerm_log_analytics_workspace.test.id}"
     }
   }
   ```

2. Přidat [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) následující kroky v dokumentaci k Terraformu.

## <a name="enable-from-azure-monitor-in-the-portal"></a>Povolení z Azure Monitor na portálu 

Chcete-li povolit monitorování clusteru AKS na portálu Azure portal ze služby Azure Monitor, postupujte takto:

1. Na webu Azure Portal, vyberte **monitorování**. 

2. Vyberte **kontejnery** ze seznamu.

3. Na **monitorování – kontejnery** stránce **monitorovat mimo clustery**.

4. Ze seznamu nesledovaných clustery, najít kontejner v seznamu a klikněte na tlačítko **povolit**.   

5. Na **připojení ke službě Azure Monitor pro kontejnery** stránky, pokud máte existující Log Analytics pracovní prostor ve stejném předplatném jako cluster, vyberte z rozevíracího seznamu.  
    V seznamu vybrána hodnota výchozího pracovního prostoru a umístění, ke kterému kontejneru AKS nasazuje v rámci předplatného. 

    ![Povolit monitorování insights kontejneru AKS](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Pokud chcete vytvořit nový pracovní prostor Log Analytics pro ukládání dat monitorování z clusteru, postupujte podle pokynů v [vytvořit pracovní prostor Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Je potřeba vytvořit pracovní prostor v rámci stejného předplatného, který se nasazuje kontejneru AKS. 
 
Po povolení sledování, může trvat přibližně 15 minut, než se zobrazí stav metriky pro cluster. 

## <a name="enable-directly-from-aks-cluster-in-the-portal"></a>Povolení přímo z clusteru AKS na portálu

Pokud chcete monitorování povolit přímo z jednoho z clusterů AKS v Azure Portal, udělejte toto:

1. Na webu Azure Portal vyberte **Všechny služby**. 

2. V seznamu prostředků, začněte psát **kontejnery**.  Seznam se průběžně filtruje podle vašeho zadání. 

3. Vyberte **služby Kubernetes**.  

    ![Propojení služby Kubernetes](./media/container-insights-onboard/portal-search-containers-01.png)

4. V seznamu kontejnerů vyberte kontejner.

5. Na stránce Přehled kontejnerů, vyberte **monitorování kontejnerů**.  

6. Na **připojení ke službě Azure Monitor pro kontejnery** stránky, pokud máte existující Log Analytics vyberte pracovní prostor ve stejném předplatném jako cluster, v rozevíracím seznamu.  
    V seznamu vybrána hodnota výchozího pracovního prostoru a umístění, ke kterému kontejneru AKS nasazuje v rámci předplatného. 

    ![Povolit monitorování stavu kontejneru AKS](./media/container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >Pokud chcete vytvořit nový pracovní prostor Log Analytics pro ukládání dat monitorování z clusteru, postupujte podle pokynů v [vytvořit pracovní prostor Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Je potřeba vytvořit pracovní prostor v rámci stejného předplatného, který se nasazuje kontejneru AKS. 
 
Po povolení sledování, může trvat přibližně 15 minut, než lze zobrazit provozní data pro cluster. 

## <a name="enable-using-an-azure-resource-manager-template"></a>Povolení použití šablony Azure Resource Manager

Tato metoda obsahuje dvě šablony JSON. Jedna šablona určuje konfiguraci povolení monitorování a druhý obsahuje hodnoty parametrů, které nakonfigurujete, zadejte následující informace:

* ID prostředku kontejneru AKS 
* Skupina prostředků, která je nasazená clusteru.

>[!NOTE]
>Šablona musí být nasazený ve stejné skupině prostředků jako cluster.
>

Aby bylo možné povolit monitorování pomocí Azure PowerShell nebo rozhraní příkazového řádku, je třeba vytvořit pracovní prostor Log Analytics. Vytvořit pracovní prostor, můžete nastavit ji prostřednictvím [Azure Resource Manageru](../../azure-monitor/platform/template-workspace-configuration.md), pomocí [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json), nebo [webu Azure portal](../../azure-monitor/learn/quick-create-workspace.md).

Pokud nejste obeznámeni s konceptem nasazení prostředků pomocí šablony, naleznete v tématu:

* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../../azure-resource-manager/resource-group-template-deploy.md)

* [Nasazení prostředků pomocí šablon Resource Manageru a Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Pokud se rozhodnete používat rozhraní příkazového řádku Azure, musíte nejprve nainstalovat a používat rozhraní příkazového řádku místně. Musíte používat Azure CLI verze 2.0.59 nebo novější. Zjistěte verzi, spusťte `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku Azure, najdete v článku [instalace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

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

2. Uložte soubor jako **existingClusterOnboarding.json** do místní složky.

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

4. Upravte hodnoty pro **aksResourceId** a **aksResourceLocation** pomocí hodnot na stránce s **přehledem AKS** pro cluster AKS. Hodnota pro **workspaceResourceId** je úplné ID prostředku pracovního prostoru Log Analytics, která zahrnuje název pracovního prostoru. 

    Upravte hodnoty pro **aksResourceTagValues** tak, aby odpovídaly existujícím hodnotám značek zadaným pro cluster AKS.

5. Uložte soubor jako **existingClusterParam.json** do místní složky.

6. Jste připraveni k nasazení této šablony. 

   * K nasazení pomocí Azure PowerShell použijte ve složce obsahující šablonu tyto příkazy:

       ```powershell
       New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
       ```
       
       Změna konfigurace může trvat několik minut. Když se dokončí, zobrazí se zpráva, která je podobný následujícímu a zahrnuje výsledek:

       ```powershell
       provisioningState       : Succeeded
       ```

   * Pokud ho chcete nasadit pomocí Azure CLI, spusťte následující příkazy:
    
       ```azurecli
       az login
       az account set --subscription "Subscription Name"
       az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
       ```

       Změna konfigurace může trvat několik minut. Když se dokončí, zobrazí se zpráva, která je podobný následujícímu a zahrnuje výsledek:

       ```azurecli
       provisioningState       : Succeeded
       ```
     
       Po povolení sledování, může trvat přibližně 15 minut, než se zobrazí stav metriky pro cluster. 

## <a name="verify-agent-and-solution-deployment"></a>Ověření nasazení agenta a řešení

Verze agenta *06072018* nebo později, můžete ověřit, že agent a řešení se úspěšně nasadily. V předchozích verzích agenta můžete ověřit pouze nasazení agenta.

### <a name="agent-version-06072018-or-later"></a>Verze agenta 06072018 nebo novější

Spusťte následující příkaz k ověření, že je agent úspěšně nasazen. 

```
kubectl get ds omsagent --namespace=kube-system
```

Výstup by měl vypadat podobně jako následující text, který označuje, že byla správně nasazena:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Pokud chcete ověřit nasazení řešení, spusťte následující příkaz:

```
kubectl get deployment omsagent-rs -n=kube-system
```

Výstup by měl vypadat podobně jako následující text, který označuje, že byla správně nasazena:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Dříve než 06072018 verze agenta

Chcete-li ověřit, že verze agenta Log Analytics vydané dřív než *06072018* nasazení správně, spusťte následující příkaz:  

```
kubectl get ds omsagent --namespace=kube-system
```

Výstup by měl vypadat podobně jako následující text, který označuje, že byla správně nasazena:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Zobrazit konfiguraci pomocí rozhraní příkazového řádku

Použití `aks show` příkaz můžete získat podrobnosti o těchto tak, jak jsou řešení povolené, nebo Ne, co je ID prostředku pracovního prostoru Log Analytics a souhrnné detaily o clusteru.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Po několika minutách se příkaz dokončí a vrátí hodnotu ve formátu JSON informace o řešení.  Výsledky příkazu by se měla zobrazit profil sledování doplněk a vypadá podobně jako následující příklad výstupu:

```
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="next-steps"></a>Další postup

* Pokud dochází k problémům při pokusu o připojení řešení, přečtěte si [Průvodce odstraňováním potíží](container-insights-troubleshoot.md)

* Pomocí monitorování povoleno zachycení stavu metriky pro uzly clusteru AKS a podů, jsou tyto metriky stavu k dispozici na webu Azure Portal. Naučte se používat Azure Monitor pro kontejnery, najdete v článku [zobrazení Azure Kubernetes Service health](container-insights-analyze.md).
