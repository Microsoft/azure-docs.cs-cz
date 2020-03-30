---
title: Konfigurace clusterů hybridních kubernetes pomocí Azure Monitoru pro kontejnery | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak můžete nakonfigurovat Azure Monitor pro kontejnery pro monitorování clusterů Kubernetes hostovaných v Azure Stack nebo v jiném prostředí.
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 6d03716b988b1139e01d41120f48ea9a9bf34be1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198050"
---
# <a name="configure-hybrid-kubernetes-clusters-with-azure-monitor-for-containers"></a>Konfigurace hybridních clusterů Kubernetes pomocí Azure Monitoru pro kontejnery

Azure Monitor pro kontejnery poskytuje bohaté možnosti monitorování pro služby Azure Kubernetes Service (AKS) a [AKS Engine v Azure](https://github.com/Azure/aks-engine), což je samoobslužný cluster Kubernetes hostovaný v Azure. Tento článek popisuje, jak povolit monitorování clusterů Kubernetes hostovaných mimo Azure a dosáhnout podobného prostředí monitorování.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte následující:

* Pracovní prostor služby Log Analytics.

    Azure Monitor pro kontejnery podporuje pracovní prostor Analýzy protokolů v oblastech uvedených v [Azure Products podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Chcete-li vytvořit vlastní pracovní prostor, můžete jej vytvořit prostřednictvím [Azure Resource Manager](../platform/template-workspace-configuration.md), prostřednictvím [PowerShellu](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)nebo na [webu Azure Portal](../learn/quick-create-workspace.md).

    >[!NOTE]
    >Povolit monitorování více clusterů se stejným názvem clusteru do stejného pracovního prostoru Log Analytics není podporováno. Názvy clusterů musí být jedinečné.
    >

* Jste členem role **přispěvatele Log Analytics** povolit monitorování kontejnerů. Další informace o řízení přístupu k pracovnímu prostoru Log Analytics naleznete v [tématu Správa přístupu k datům pracovního prostoru a protokolu.](../platform/manage-access.md)

* [HELM klienta](https://helm.sh/docs/using_helm/) na palubě Azure Monitor pro kontejnery grafu pro zadaný cluster Kubernetes.

* Pro kontejnerizovanou verzi agenta Log Analytics pro Linux ke komunikaci s Azure Monitorem jsou vyžadovány následující informace o konfiguraci serveru proxy a brány firewall:

    |Prostředek agenta|Porty |
    |------|---------|   
    |*.ods.opinsights.azure.com |Přístav 443 |  
    |*.oms.opinsights.azure.com |Přístav 443 |  
    |*.blob.core.windows.net |Přístav 443 |  
    |*.dc.services.visualstudio.com |Přístav 443 |

* Kontejnerizovaný agent vyžaduje Kubelet `cAdvisor secure port: 10250` `unsecure port :10255` nebo otevřít na všech uzlech v clusteru shromažďovat metriky výkonu. Doporučujeme, `secure port: 10250` abyste na konfigurovali na kubeletově cAdvisoru, pokud ještě není nakonfigurován.

* Kontejnerizovaný agent vyžaduje, aby byly v kontejneru zadány následující proměnné prostředí, aby bylo možné komunikovat se `KUBERNETES_SERVICE_HOST` službou Rozhraní API Kubernetes v rámci clusteru za účelem shromažďování dat inventáře – a `KUBERNETES_PORT_443_TCP_PORT`.

>[!IMPORTANT]
>Minimální verze agenta podporovaná pro monitorování hybridních clusterů Kubernetes je ciprod10182019 nebo novější.

## <a name="supported-configurations"></a>Podporované konfigurace

Následující je oficiálně podporována pomocí Azure Monitor pro kontejnery.

- Prostředí: Kubernetes místní, AKS Engine v Azure a Azure Stack. Další informace najdete v tématu [AKS Engine na Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
- Verze Kubernetes a zásady podpory jsou stejné jako verze [podporovaných AKS](../../aks/supported-kubernetes-versions.md).
- Kontejnerový běh: Docker a Moby
- Linux OS verze pro master a pracoval uzly: Ubuntu (18.04 LTS a 16.04 LTS)
- Podporováno řízení přístupu: Kubernetes RBAC a non-RBAC

## <a name="enable-monitoring"></a>Povolení monitorování

Povolení Azure Monitor pro kontejnery pro hybridní cluster Kubernetes se skládá z provedení následujících kroků v pořadí.

1. Nakonfigurujte pracovní prostor Log Analytics pomocí řešení Container Insights.

2. Povolte azure monitor pro kontejnery HELM graf s pracovním prostorem Log Analytics.

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Jak přidat řešení Azure Monitor Containers

Řešení můžete nasadit pomocí zařízené šablony Azure Resource Manager pomocí `New-AzResourceGroupDeployment` rutiny Azure PowerShell nebo pomocí azure cli.

Pokud nejste obeznámeni s konceptem nasazování prostředků pomocí šablony, přečtěte si:

* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../../azure-resource-manager/templates/deploy-powershell.md)

* [Nasazení prostředků pomocí šablon Správce prostředků a nastavení příkazového uživatelského příkazu Azure](../../azure-resource-manager/templates/deploy-cli.md)

Pokud se rozhodnete použít azure cli, musíte nejprve nainstalovat a použít příkazového příkazového příkazu místně. Musíte spouštět Azure CLI verze 2.0.59 nebo novější. Chcete-li identifikovat `az --version`verzi, spusťte aplikaci . Pokud potřebujete nainstalovat nebo upgradovat vázačitelné příkazy k Webu Azure, přečtěte si informace [o instalaci příkazového příkazového příkazu k azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

Tato metoda zahrnuje dvě šablony JSON. Jedna šablona určuje konfiguraci pro povolení monitorování a druhá obsahuje hodnoty parametrů, které nakonfigurujete tak, aby určila následující:

- **workspaceResourceId** – úplné ID prostředku pracovního prostoru Analýzy protokolů.
- **workspaceRegion** – oblast, ve které se pracovní prostor vytvoří, která se také označuje jako **Umístění** ve vlastnostech pracovního prostoru při zobrazení z portálu Azure.

Chcete-li nejprve identifikovat úplné ID prostředku pracovního `workspaceResourceId` prostoru Analýzy protokolů požadovaného pro hodnotu parametru v **souboru containerSolutionParams.json,** proveďte následující kroky a pak spusťte rutinu prostředí PowerShell nebo příkaz Azure CLI pro přidání řešení.

1. Seznam všech předplatných, ke kterým máte přístup pomocí následujícího příkazu:

    ```azurecli
    az account list --all -o table
    ```

    Výstup se bude podobat následujícímu:

    ```azurecli
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

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Ve výstupu vyhledejte název pracovního prostoru a potom zkopírujte úplné ID prostředku tohoto pracovního prostoru Analýzy protokolů pod **ID**pole .

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

5. Uložte tento soubor jako containerSolution.json do místní složky.

6. Do souboru vložte následující syntaxi JSON:

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

7. Upravte hodnoty pro **workspaceResourceId** pomocí hodnoty, kterou jste zkopírovali v kroku 3, a pro **oblast pracovního prostoru** zkopírujte hodnotu **Region** po spuštění zobrazení [protokolu analýzy monitorování az](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest#az-monitor-log-analytics-workspace-list)příkazu Azure CLI .

8. Uložte tento soubor jako kontejnerSolutionParams.json do místní složky.

9. Jste připraveni k nasazení této šablony.

   * Pokud chcete nasadit pomocí Azure PowerShellu, použijte ve složce, která šablonu obsahuje, následující příkazy:

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

       Změna konfigurace může trvat několik minut. Po dokončení se zobrazí zpráva podobná následující a obsahuje výsledek:

       ```powershell
       provisioningState       : Succeeded
       ```

   * Chcete-li nasadit pomocí příkazového příkazu k řešení Azure, spusťte následující příkazy:

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az group deployment create --resource-group <resource group of log analytics workspace> --template-file ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       Změna konfigurace může trvat několik minut. Po dokončení se zobrazí zpráva podobná následující a obsahuje výsledek:

       ```azurecli
       provisioningState       : Succeeded
       ```

       Po povolení monitorování může trvat přibližně 15 minut, než budete moci zobrazit metriky stavu pro cluster.

## <a name="install-the-chart"></a>Instalace grafu

Chcete-li povolit graf HELM, postupujte takto:

1. Přidejte úložiště grafů Azure do místního seznamu spuštěním následujícího příkazu:

    ```
    helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
    ````

2. Graf nainstalujte spuštěním následujícího příkazu:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
    ```

    Pokud je pracovní prostor Log Analytics v Azure V Číně, spusťte následující příkaz:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Pokud je pracovní prostor Log Analytics v Azure US Government, spusťte následující příkaz:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

## <a name="configure-agent-data-collection"></a>Konfigurace shromažďování dat agenta

Při pohledu s grafem verze 1.0.0 jsou nastavení sběru dat agenta řízena z configmap. Naleznete v dokumentaci o nastavení shromažďování dat [agenta zde](container-insights-agent-config.md).

Po úspěšném nasazení grafu můžete zkontrolovat data pro hybridní cluster Kubernetes v Azure Monitor pro kontejnery z portálu Azure.  

>[!NOTE]
>Latence ingestování je přibližně pět až deset minut od agenta k potvrzení v pracovním prostoru Azure Log Analytics. Stav clusteru zobrazit hodnotu **Žádná data** nebo **Neznámý,** dokud všechna požadovaná data monitorování je k dispozici ve službě Azure Monitor.

## <a name="troubleshooting"></a>Řešení potíží

Pokud při pokusu o povolení monitorování hybridního clusteru Kubernetes narazíte na chybu, zkopírujte skript prostředí PowerShell [TroubleshootError_nonAzureK8s.ps1](https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/Troubleshoot/TroubleshootError_nonAzureK8s.ps1) a uložte jej do složky v počítači. Tento skript je k dispozici pomoci zjistit a opravit problémy, které se vyskytly. Problémy, které je určen k detekci a pokus o opravu jsou následující:

* Zadaný pracovní prostor Log Analytics je platný.
* Pracovní prostor Log Analytics je nakonfigurovaný s řešením Azure Monitor for Containers. Pokud ne, nakonfigurujte pracovní prostor.
* Jsou spuštěny pody repliksad OmsAgent
* OmsAgent daemonset lusky jsou spuštěny
* Služba OmsAgent Health je spuštěna
* ID pracovního prostoru Analýzy protokolů a klíč nakonfigurovaný v kontejnerizovaném agentovi se shoduje s pracovním prostorem, se kterým je insight nakonfigurován.
* Ověřte všechny pracovní `kubernetes.io/role=agent` uzly Linuxu mají popisek pro naplánování rs pod. Pokud neexistuje, přidejte ji.
* Ověřte `cAdvisor secure port:10250` nebo `unsecure port: 10255` je otevřen a otevře se ve všech uzlech v clusteru.

Chcete-li provést s Azure PowerShell, použijte následující příkazy ve složce, která obsahuje skript:

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>Další kroky

Díky monitorování, které umožňuje shromažďovat využití stavu a prostředků hybridního clusteru Kubernetes a úloh, které na nich běží, najdete informace o [tom, jak používat](container-insights-analyze.md) Azure Monitor pro kontejnery.
