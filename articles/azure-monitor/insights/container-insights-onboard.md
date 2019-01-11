---
title: Monitorování připojení Azure pro kontejnery | Dokumentace Microsoftu
description: Tento článek popisuje, jak můžete připojit a konfigurace služby Azure Monitor pro kontejnery to vám umožní pochopit, jaký je výkon vašeho kontejneru a byly zjištěny problémy související s výkonem.
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
ms.date: 12/06/2018
ms.author: magoedte
ms.openlocfilehash: 9355e5ffaaa5ca89f57f7e358d946b24d5fcb3ca
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54213850"
---
# <a name="how-to-onboard-azure-monitor-for-containers"></a>Monitorování připojení Azure pro kontejnery  
Tento článek popisuje, jak nastavit službu Azure Monitor k monitorování výkonu úlohy, které se nasazují do prostředí Kubernetes a hostitelem pro kontejnery [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/).

Azure Monitor pro kontejnery může být povoleno na nový, nebo nepodporuje jeden nebo více existujících nasazení AKS pomocí následující metody:

* Azure portal nebo pomocí rozhraní příkazového řádku Azure
* Pomocí [Terraform a AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

## <a name="prerequisites"></a>Požadavky 
Než začnete, ujistěte se, že máte následující:

- Pracovní prostor Log Analytics. Můžete jej vytvořit při povolení monitorování nový cluster AKS, nebo nechte prostředí registrace vytvoření výchozího pracovního prostoru do výchozí skupiny prostředků předplatného clusteru AKS. Pokud jste se rozhodli vytvořit sami, můžete vytvořit pomocí [Azure Resource Manageru](../../azure-monitor/platform/template-workspace-configuration.md), pomocí [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json), nebo [webu Azure portal](../../azure-monitor/learn/quick-create-workspace.md).
- Jste členem role Přispěvatel Log Analytics povolit monitorování kontejnerů. Další informace o tom, jak řídit přístup k pracovnímu prostoru Log Analytics najdete v tématu [Správa pracovních prostorů](../../azure-monitor/platform/manage-access.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

## <a name="components"></a>Komponenty 

Vaše schopnost sledování výkonu spoléhá na kontejnerizovaných agenta Log Analytics pro Linux, konkrétně vyvinuté pro monitorování Azure pro kontejnery. Tento speciální agent shromažďuje data událostí výkonu a ze všech uzlů v clusteru, a automaticky agenta nasazení a registraci s zadaný pracovní prostor Log Analytics během nasazení. Verze agenta je microsoft / oms:ciprod04202018 nebo novější a představuje datum v následujícím formátu: *mmddyyyy*. 

Po vydání nové verze agenta, dojde k automatickému upgradu na vaše spravované clustery Kubernetes hostované ve službě Azure Kubernetes Service (AKS). Postupujte podle vydané verze, najdete v článku [oznámení verzi agenta](https://github.com/microsoft/docker-provider/tree/ci_feature_prod). 

>[!NOTE] 
>Pokud už jste nasadili AKS cluster, povolte monitorování pomocí Azure CLI nebo zadané šablony Azure Resource Manageru, jak je uvedeno dále v tomto článku. Nemůžete použít `kubectl` k upgradu, odstranit, znovu nasadit nebo nasadit agenta. Šablona musí být nasazený ve stejné skupině prostředků jako cluster."

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal
Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

## <a name="enable-monitoring-for-a-new-cluster"></a>Zapněte sledování pro nový cluster
Během nasazení můžete povolit monitorování na webu Azure Portal, rozhraní příkazového řádku Azure nebo s využitím Terraformu nový cluster AKS.  Postupujte podle kroků v tomto článku rychlý Start [Nasaďte cluster Azure Kubernetes Service (AKS)](../../aks/kubernetes-walkthrough-portal.md) Pokud chcete povolit z portálu. Na **monitorování** stránky, pro **povolit monitorování** možnosti, vyberte **Ano**a potom vyberte existující pracovní prostor Log Analytics nebo vytvořte novou. 

### <a name="enable-using-azure-cli"></a>Povolení s využitím rozhraní příkazového řádku Azure
Chcete-li povolit monitorování pomocí Azure CLI vytvořili nový cluster AKS, postupujte podle kroku v části tohoto článku rychlý Start [clusteru AKS vytvořit](../../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Pokud se rozhodnete používat rozhraní příkazového řádku Azure, musíte nejprve nainstalovat a používat rozhraní příkazového řádku místně. Musíte používat Azure CLI verze 2.0.43 nebo novější. Zjistěte verzi, spusťte `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku Azure, najdete v článku [instalace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 
>

### <a name="enable-using-terraform"></a>Povolení s využitím Terraformu
Pokud jste [nasazení nového clusteru AKS pomocí Terraformu](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md), zadejte argumenty potřebné v profilu [vytvořit pracovní prostor Log Analytics](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html) Pokud není rozhodli zadejte existující. 

>[!NOTE]
>Pokud zvolíte použití Terraformu, musí běžet Terraformu pro Azure RM poskytovatele verze 1.17.0 nebo vyšší.

Přidat Azure monitorování kontejnerů do pracovního prostoru, naleznete v tématu [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) a dokončete profil zahrnutím [ **addon_profile** ](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) a zadat **oms_agent**. 

Poté, co jste povolili monitorování a úspěšném dokončení všech konfiguračních úloh, můžete sledovat výkon cluster v některém ze dvou způsobů:

* Přímo v clusteru AKS tak, že vyberete **stavu** v levém podokně.
* Výběrem **přehledy o kontejnerech monitorování** dlaždice na stránce clusteru AKS pro vybraný cluster. Ve službě Azure Monitor, v levém podokně vyberte **stavu**. 

  ![Možnosti pro výběr monitorování Azure pro kontejnery ve službě AKS](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

Po povolení sledování, může trvat přibližně 15 minut, než se zobrazí stav metriky pro cluster. 

## <a name="enable-monitoring-for-existing-managed-clusters"></a>Zapněte sledování pro existující spravované clustery
Monitorování clusteru AKS, který je už nasazená buď pomocí rozhraní příkazového řádku Azure na portálu nebo pomocí zadané šablony Azure Resource Manageru pomocí rutiny prostředí PowerShell můžete povolit `New-AzureRmResourceGroupDeployment`. 

### <a name="enable-monitoring-using-azure-cli"></a>Povolte monitorování pomocí Azure CLI
Následující krok zapne monitorování clusteru AKS pomocí Azure CLI. V tomto příkladu nemusíte za vytvoření nebo zadejte existující pracovní prostor. Tento příkaz zjednodušuje proces pro vás vytvořením výchozího pracovního prostoru do výchozí skupiny prostředků předplatného cluster AKS, pokud již neexistuje v oblasti.  Vytvoření výchozího pracovního prostoru vypadá podobně jako formát *DefaultWorkspace -<GUID>-<Region>*.  

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

### <a name="enable-monitoring-using-terraform"></a>Povolte monitorování pomocí Terraformu
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

### <a name="enable-monitoring-from-azure-monitor"></a>Povolit monitorování ze služby Azure Monitor
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

### <a name="enable-monitoring-from-aks-cluster-in-the-portal"></a>Povolit monitorování z clusteru AKS na portálu
Pokud chcete povolit monitorování vašeho kontejneru AKS na portálu Azure portal, postupujte takto:

1. Na webu Azure Portal vyberte **Všechny služby**. 
2. V seznamu prostředků, začněte psát **kontejnery**.  
    Seznam se průběžně filtruje podle vašeho zadání. 
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

### <a name="enable-monitoring-by-using-an-azure-resource-manager-template"></a>Povolte monitorování pomocí šablony Azure Resource Manageru
Tato metoda obsahuje dvě šablony JSON. Jedna šablona určuje konfiguraci povolení monitorování a druhý obsahuje hodnoty parametrů, které nakonfigurujete, zadejte následující informace:

* ID prostředku kontejneru AKS 
* Skupina prostředků, která je nasazená clusteru.
* Pracovní prostor Log Analytics a oblasti se má vytvořit v pracovním prostoru. 

>[!NOTE]
>Šablona musí být nasazený ve stejné skupině prostředků jako cluster.
>

Pracovní prostor Log Analytics je potřeba vytvořit ručně. Vytvořit pracovní prostor, můžete nastavit ji prostřednictvím [Azure Resource Manageru](../../azure-monitor/platform/template-workspace-configuration.md), pomocí [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json), nebo [webu Azure portal](../../azure-monitor/learn/quick-create-workspace.md).

Pokud nejste obeznámeni s konceptem nasazení prostředků pomocí šablony, naleznete v tématu:
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../../azure-resource-manager/resource-group-template-deploy.md)
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Pokud se rozhodnete používat rozhraní příkazového řádku Azure, musíte nejprve nainstalovat a používat rozhraní příkazového řádku místně. Musíte používat Azure CLI verze 2.0.27 nebo novější. Zjistěte verzi, spusťte `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku Azure, najdete v článku [instalace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

#### <a name="create-and-execute-a-template"></a>Vytvoření a provedení šablony

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
    "workspaceResourceId": {
      "type": "string",
      "metadata": {
         "description": "Azure Monitor Log Analytics Resource ID"
       }
    },
    "workspaceRegion": {
    "type": "string",
    "metadata": {
       "description": "Azure Monitor Log Analytics workspace region"
      }
     }
    },
    "resources": [
      {
    "name": "[split(parameters('aksResourceId'),'/')[8]]",
    "type": "Microsoft.ContainerService/managedClusters",
    "location": "[parameters('aksResourceLocation')]",
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
     },
    {
        "type": "Microsoft.Resources/deployments",
        "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]", 
        "apiVersion": "2017-05-10",
        "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
        "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
        "properties": {
            "mode": "Incremental",
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {},
                "variables": {},
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "location": "[parameters('workspaceRegion')]",
                        "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                        "properties": {
                            "workspaceResourceId": "[parameters('workspaceResourceId')]"
                        },
                        "plan": {
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                            "promotionCode": "",
                            "publisher": "Microsoft"
                        }
                    }
                ]
            },
            "parameters": {}
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
           "value": "/subscriptions/<SubscriptiopnId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
       },
       "aksResourceLocation": {
         "value": "<aksClusterLocation>"
       },
       "workspaceResourceId": {
         "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
       },
       "workspaceRegion": {
         "value": "<workspaceLocation>"
       }
     }
    }
    ```

4. Upravte hodnoty **aksResourceId** a **aksResourceLocation** pomocí hodnot na **Přehled služby AKS** stránky pro AKS cluster. Hodnota pro **workspaceResourceId** je úplné ID prostředku pracovního prostoru Log Analytics, která zahrnuje název pracovního prostoru. Také zadejte umístění pracovního prostoru pro **workspaceRegion**. 
5. Uložte soubor jako **existingClusterParam.json** do místní složky.
6. Jste připraveni k nasazení této šablony. 

    * Do složky obsahující šablonu použijte následující příkazy Powershellu:

        ```powershell
        New-AzureRmResourceGroupDeployment -Name OnboardCluster -ClusterResourceGroupName ClusterResourceGroupName -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
        ```
        Změna konfigurace může trvat několik minut. Když se dokončí, zobrazí se zpráva, která je podobný následujícímu a zahrnuje výsledek:

        ```powershell
        provisioningState       : Succeeded
        ```

    * Chcete-li pomocí Azure CLI, spusťte následující příkaz:
    
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
az aks show -g <resoourceGroupofAKSCluster> -n <nameofAksCluster>
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