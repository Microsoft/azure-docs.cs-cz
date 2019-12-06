---
title: Konfigurace Hybrid Kubernetes clusterů pomocí Azure Monitor pro kontejnery | Microsoft Docs
description: Tento článek popisuje, jak můžete nakonfigurovat Azure Monitor pro kontejnery, abyste mohli monitorovat clustery Kubernetes hostované v Azure Stack nebo jiném prostředí.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 12/04/2019
ms.openlocfilehash: 0d6615d832059a8b58c0d5d52533b8c8c962640d
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74841570"
---
# <a name="configure-hybrid-kubernetes-clusters-with-azure-monitor-for-containers"></a>Konfigurace Hybrid Kubernetes clusterů pomocí Azure Monitor pro kontejnery

Azure Monitor for Containers poskytuje bohatou monitorovací prostředí pro clustery Azure Kubernetes Service (AKS) a AKS Engine hostované v Azure. Tento článek popisuje, jak povolit monitorování clusterů Kubernetes hostovaných mimo Azure a dosáhnout podobných možností monitorování.

## <a name="prerequisites"></a>Předpoklady

Než začnete, ujistěte se, že máte následující:

* Pracovní prostor služby Log Analytics.

    Azure Monitor for Containers podporuje pracovní prostor Log Analytics v oblastech uvedených v [produktech Azure podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Pokud chcete vytvořit vlastní pracovní prostor, můžete ho vytvořit prostřednictvím [Azure Resource Manager](../platform/template-workspace-configuration.md), prostřednictvím [PowerShellu](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)nebo v [Azure Portal](../learn/quick-create-workspace.md).

    >[!NOTE]
    >Možnost monitorování více clusterů se stejným názvem clusteru do stejného Log Analytics pracovní prostor není podporována. Názvy clusterů musí být jedinečné.
    >

* Jste členem **role přispěvatel Log Analytics** , abyste povolili monitorování kontejnerů. Další informace o tom, jak řídit přístup k pracovnímu prostoru Log Analytics, najdete v tématu [Správa přístupu k pracovním prostorům a datům protokolu](../platform/manage-access.md) .

* [Helm klientovi](https://helm.sh/docs/using_helm/) , aby se připojil diagram Azure monitor for Containers pro zadaný cluster Kubernetes.

* Následující informace o konfiguraci proxy serveru a brány firewall jsou vyžadovány pro kontejnerové verze Log Analytics agenta pro Linux pro komunikaci s Azure Monitor:

    |Prostředek agenta|Porty |
    |------|---------|   
    |*.ods.opinsights.azure.com |Port 443 |  
    |*.oms.opinsights.azure.com |Port 443 |  
    |*.blob.core.windows.net |Port 443 |  
    |*. dc.services.visualstudio.com |Port 443 | 

* Kontejner s označením vyžaduje, `cAdvisor port: 10255` otevřít na všech uzlech v clusteru za účelem shromažďování metrik výkonu.

* Kontejner s podporou kontejneru vyžaduje, aby se v kontejneru zadaly následující proměnné prostředí, aby bylo možné komunikovat se službou Kubernetes API v rámci clusteru za účelem shromažďování dat inventáře – `KUBERNETES_SERVICE_HOST` a `KUBERNETES_PORT_443_TCP_PORT`. 

>[!IMPORTANT]
>Minimální verze agenta podporovaná pro monitorování clusterů Hybrid Kubernetes je ciprod10182019 nebo novější. 

## <a name="supported-configurations"></a>Podporované konfigurace

Následující je oficiálně podporovaná s Azure Monitor pro kontejnery.

- Prostředí: Kubernetes místně, AKS Engine v Azure a Azure Stack. Další informace najdete v tématu [AKS Engine on Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
- Verze Kubernetes a zásad podpory jsou stejné jako verze [podporovaných AKS](../../aks/supported-kubernetes-versions.md). 
- Modul runtime kontejneru: Docker a Moby
- Verze operačního systému Linux pro hlavní a zpracovávané uzly: Ubuntu (18,04 LTS a 16,04 LTS)
- Podpora řízení přístupu: Kubernetes RBAC a non-RBAC

## <a name="enable-monitoring"></a>Povolení monitorování

Povolení Azure Monitor pro kontejnery pro cluster Hybrid Kubernetes se skládá z následujících kroků v uvedeném pořadí.

1. Nakonfigurujte svůj pracovní prostor Log Analytics pomocí řešení Container Insights.

2. Povolte Azure Monitor pro kontejnery HELM s Log Analytics pracovním prostorem.

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Postup přidání řešení Azure Monitor Containers

Řešení můžete nasadit pomocí zadané šablony Azure Resource Manager pomocí rutiny Azure PowerShell `New-AzResourceGroupDeployment` nebo pomocí Azure CLI.

Pokud nejste obeznámeni s konceptem nasazení prostředků pomocí šablony, naleznete v tématu:

* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../../azure-resource-manager/resource-group-template-deploy.md)

* [Nasazení prostředků pomocí šablon Resource Manageru a Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Pokud se rozhodnete používat rozhraní příkazového řádku Azure, musíte nejprve nainstalovat a používat rozhraní příkazového řádku místně. Musíte používat Azure CLI verze 2.0.59 nebo novější. Zjistěte verzi, spusťte `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku Azure, najdete v článku [instalace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

Tato metoda obsahuje dvě šablony JSON. Jedna šablona určuje konfiguraci povolení monitorování a druhý obsahuje hodnoty parametrů, které nakonfigurujete, zadejte následující informace:

- **workspaceResourceId** – úplné ID prostředku pracovního prostoru Log Analytics.
- **workspaceRegion** – oblast, ve které je pracovní prostor vytvořen, který se také označuje jako **umístění** ve vlastnostech pracovního prostoru při prohlížení z Azure Portal.

Abyste nejdřív identifikovali úplné ID prostředku Log Analytics pracovního prostoru vyžadovaného pro `workspaceResourceId` hodnotu parametru v souboru **containerSolutionParams. JSON** , proveďte následující kroky a potom spusťte rutinu PowerShellu nebo příkaz Azure CLI, abyste mohli řešení přidat.

1. Vypíše seznam všech předplatných, ke kterým máte přístup, pomocí následujícího příkazu:

    ```azurecli
    az account list --all -o table
    ```

    Výstup bude vypadat takto:

    ```azurecli
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

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Ve výstupu vyhledejte název pracovního prostoru a zkopírujte úplné ID prostředku, které Log Analytics pracovní prostor pod **ID**pole.

4. Zkopírujte a vložte do souboru následující syntaxi JSON:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
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

5. Uložte tento soubor jako containerSolution. JSON do místní složky.

6. Vložte následující syntaxi JSON do souboru:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceResourceId": {
          "value": "<workspaceResourceId>"
      },
      "workspaceRegion": {
        "value": "<workspaceRegion>"
      }
     }
    }
    ```

7. Upravte hodnoty pro **workspaceResourceId** pomocí hodnoty, kterou jste zkopírovali v kroku 3, a pro **WorkspaceRegion** Zkopírujte hodnotu **oblasti** po spuštění příkazu Azure CLI [AZ monitor Log-Analytics pracovní prostor zobrazit](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest#az-monitor-log-analytics-workspace-list).

8. Uložte tento soubor jako containerSolutionParams. JSON do místní složky.

9. Jste připraveni k nasazení této šablony. 

   * K nasazení pomocí Azure PowerShell použijte ve složce obsahující šablonu tyto příkazy:

       ```powershell
       # configure and login to the cloud of log analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of log analytics workspace>
       ```
       
       ```powershell
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of log analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```
       
       Změna konfigurace může trvat několik minut. Když se dokončí, zobrazí se zpráva, která je podobný následujícímu a zahrnuje výsledek:

       ```powershell
       provisioningState       : Succeeded
       ```

   * Pokud ho chcete nasadit pomocí Azure CLI, spusťte následující příkazy:
    
       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az group deployment create --resource-group <resource group of log analytics workspace> --template-file ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       Změna konfigurace může trvat několik minut. Když se dokončí, zobrazí se zpráva, která je podobný následujícímu a zahrnuje výsledek:

       ```azurecli
       provisioningState       : Succeeded
       ```
     
       Po povolení sledování, může trvat přibližně 15 minut, než se zobrazí stav metriky pro cluster. 

## <a name="install-the-chart"></a>Instalace grafu

Chcete-li povolit graf HELM, postupujte takto:

1. Přidejte úložiště Azure Charts do svého místního seznamu spuštěním následujícího příkazu:

    ```
    helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
    ````

2. Nainstalujte graf spuštěním následujícího příkazu:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
    ```

    Pokud je pracovní prostor Log Analytics v Azure Čína, spusťte následující příkaz:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers 
    ```

    Pokud je pracovní prostor Log Analytics ve vládě Azure USA, spusťte následující příkaz:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

## <a name="configure-agent-data-collection"></a>Konfigurace shromažďování dat agenta

V případě sestavování pomocí grafu verze 1.0.0 se nastavení shromažďování dat agenta řídí z ConfigMap. [Tady](container-insights-agent-config.md)najdete informace o nastavení shromažďování dat agenta v dokumentaci. 

Po úspěšném nasazení grafu můžete zkontrolovat data pro svůj cluster Hybrid Kubernetes ve službě Azure Monitor for Containers z Azure Portal.  

>[!NOTE]
>Latence příjmu je od agenta pět do deseti minut od agenta k potvrzení v pracovním prostoru Azure Log Analytics. Stav clusteru zobrazí hodnotu **žádná data** nebo **neznámé** , dokud nejsou v Azure monitor k dispozici všechna požadovaná data monitorování. 

## <a name="troubleshooting"></a>Řešení potíží

Pokud dojde k chybě při pokusu o povolení monitorování pro cluster Hybrid Kubernetes, zkopírujte skript PowerShellu [TroubleshootError_nonAzureK8s. ps1](https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/Troubleshoot/TroubleshootError_nonAzureK8s.ps1) a uložte ho do složky ve vašem počítači. Tento skript je k dispozici, aby bylo možné zjistit a opravit zjištěné problémy. Problémy, které je navrženo pro detekci a pokus o opravu, jsou následující:

* Zadaný pracovní prostor Log Analytics je platný. 
* Pracovní prostor Log Analytics je nakonfigurovaný pomocí řešení Azure Monitor for Containers. V takovém případě nakonfigurujte pracovní prostor.
* OmsAgent REPLICASET pod je spuštěn
* OmsAgent daemonset pod je spuštěn
* Služba Health OmsAgent je spuštěná. 
* ID a klíč pracovního prostoru Log Analytics nakonfigurované na kontejnerovém agentovi se shodují s pracovním prostorem, pomocí kterého je tento přehled nakonfigurovaný.
* Ověří, jestli mají všechny uzly pro Linux Worker `kubernetes.io/role=agent` popisek pro naplánování RS pod. Pokud neexistuje, přidejte ho.
* Ověří `cAdvisor port: 10255` je otevřen na všech uzlech v clusteru.

Chcete-li provést příkaz s Azure PowerShell, použijte ve složce obsahující skript následující příkazy:

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile>
```

## <a name="next-steps"></a>Další kroky

Díky monitorování s povoleným shromažďováním informací o stavu a využití prostředků hybridního clusteru Kubernetes a spuštěných úloh se naučíte [používat](container-insights-analyze.md) Azure monitor pro kontejnery.
