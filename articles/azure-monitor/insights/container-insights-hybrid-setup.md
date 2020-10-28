---
title: Konfigurace Hybrid Kubernetes clusterů pomocí Azure Monitor pro kontejnery | Microsoft Docs
description: Tento článek popisuje, jak můžete nakonfigurovat Azure Monitor pro kontejnery, abyste mohli monitorovat clustery Kubernetes hostované v Azure Stack nebo jiném prostředí.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: d481af07013c0a5b4c5a381527c6f555400a2559
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890458"
---
# <a name="configure-hybrid-kubernetes-clusters-with-azure-monitor-for-containers"></a>Konfigurace Hybrid Kubernetes clusterů pomocí Azure Monitor pro kontejnery

Azure Monitor for Containers poskytuje bohatou monitorovací prostředí pro Azure Kubernetes Service (AKS) a [AKS Engine v Azure](https://github.com/Azure/aks-engine), což je samoobslužný cluster Kubernetes hostovaný v Azure. Tento článek popisuje, jak povolit monitorování clusterů Kubernetes hostovaných mimo Azure a dosáhnout podobných možností monitorování.

## <a name="supported-configurations"></a>Podporované konfigurace

Následující konfigurace jsou oficiálně podporované Azure Monitor for Containers. Pokud máte jinou verzi Kubernetes a verze operačního systému, pošlete prosím e-mail na askcoin@microsoft.com .

- Environment

    - Místní Kubernetes
    - AKS Engine v Azure a Azure Stack. Další informace najdete v tématu [AKS Engine on Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908&preserve-view=true)
    - [OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) verze 4 a vyšší, místní nebo jiná cloudová prostředí.

- Verze Kubernetes a zásad podpory jsou stejné jako verze [podporovaných AKS](../../aks/supported-kubernetes-versions.md).

- Podporovány jsou následující moduly runtime kontejneru: rozhraní Docker, Moby a CRI kompatibilní s modulem runtime, jako jsou CRI-O a kontejnery.

- Podporovaná verze operačního systému Linux pro hlavní a pracovní uzly: Ubuntu (18,04 LTS a 16,04 LTS) a Red Hat Enterprise Linux CoreOS 43,81.

- Podpora řízení přístupu: Kubernetes RBAC a non-RBAC

## <a name="prerequisites"></a>Předpoklady

Než začnete, ujistěte se, že máte následující:

- [Pracovní prostor služby Log Analytics](../platform/design-logs-deployment.md).

    Azure Monitor for Containers podporuje pracovní prostor Log Analytics v oblastech uvedených v [produktech Azure podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Pokud chcete vytvořit vlastní pracovní prostor, můžete ho vytvořit prostřednictvím [Azure Resource Manager](../samples/resource-manager-workspace.md), prostřednictvím [PowerShellu](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)nebo v [Azure Portal](../learn/quick-create-workspace.md).

    >[!NOTE]
    >Možnost monitorování více clusterů se stejným názvem clusteru do stejného Log Analytics pracovní prostor není podporována. Názvy clusterů musí být jedinečné.
    >

- Jste členem **role přispěvatel Log Analytics** , abyste povolili monitorování kontejnerů. Další informace o tom, jak řídit přístup k pracovnímu prostoru Log Analytics, najdete v tématu [Správa přístupu k pracovním prostorům a datům protokolu](../platform/manage-access.md).

- Chcete-li zobrazit data monitorování, je nutné mít v pracovním prostoru Log Analytics [*Log Analytics roli Čtenář*](../platform/manage-access.md#manage-access-using-azure-permissions) , nakonfigurovanou pomocí Azure monitor for Containers.

- [Helm klientovi](https://helm.sh/docs/using_helm/) , aby se připojil diagram Azure monitor for Containers pro zadaný cluster Kubernetes.

- Následující informace o konfiguraci proxy serveru a brány firewall jsou vyžadovány pro kontejnerové verze Log Analytics agenta pro Linux pro komunikaci s Azure Monitor:

    |Prostředek agenta|Porty |
    |------|---------|
    |*.ods.opinsights.azure.com |Port 443 |
    |*.oms.opinsights.azure.com |Port 443 |
    |*. dc.services.visualstudio.com |Port 443 |

- Kontejner s označením vyžaduje, aby se Kubelet `cAdvisor secure port: 10250` nebo `unsecure port :10255` otevřel na všech uzlech v clusteru za účelem shromažďování metrik výkonu. Doporučujeme, abyste nakonfigurovali `secure port: 10250` na cAdvisor pro Kubelet, pokud už není nakonfigurovaná.

- Kontejner s označením vyžaduje, aby v kontejneru bylo zadáno následující proměnné prostředí, aby bylo možné komunikovat se službou Kubernetes API v rámci clusteru za účelem shromažďování dat inventáře `KUBERNETES_SERVICE_HOST` a `KUBERNETES_PORT_443_TCP_PORT` .

>[!IMPORTANT]
>Minimální verze agenta podporovaná pro monitorování clusterů Hybrid Kubernetes je ciprod10182019 nebo novější.

## <a name="enable-monitoring"></a>Povolení monitorování

Povolení Azure Monitor pro kontejnery pro cluster Hybrid Kubernetes se skládá z následujících kroků v uvedeném pořadí.

1. Nakonfigurujte svůj pracovní prostor Log Analytics pomocí řešení Container Insights.   

2. Povolte Azure Monitor pro kontejnery HELM s Log Analytics pracovním prostorem.

Další informace o řešeních monitorování v Azure Monitor najdete [tady](../../azure-monitor/insights/solutions.md).

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Postup přidání řešení Azure Monitor Containers

Řešení můžete nasadit pomocí zadané šablony Azure Resource Manager pomocí rutiny Azure PowerShell `New-AzResourceGroupDeployment` nebo pomocí Azure CLI.

Pokud nejste obeznámeni s konceptem nasazení prostředků pomocí šablony, přečtěte si téma:

- [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../../azure-resource-manager/templates/deploy-powershell.md)

- [Nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Pokud se rozhodnete používat rozhraní příkazového řádku Azure, musíte nejdřív nainstalovat a používat rozhraní příkazového řádku (CLI). Musíte používat Azure CLI verze 2.0.59 nebo novější. Pro identifikaci vaší verze spusťte `az --version` . Pokud potřebujete nainstalovat nebo upgradovat rozhraní příkazového řádku Azure CLI, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli).

Tato metoda zahrnuje dvě šablony JSON. Jedna šablona určuje konfiguraci pro povolení monitorování a druhá obsahuje hodnoty parametrů, které nakonfigurujete, aby určovaly následující:

- **workspaceResourceId** – úplné ID prostředku pracovního prostoru Log Analytics.
- **workspaceRegion** – oblast, ve které je pracovní prostor vytvořen, který se také označuje jako **umístění** ve vlastnostech pracovního prostoru při prohlížení z Azure Portal.

Abyste nejdřív identifikovali úplné ID prostředku Log Analytics pracovního prostoru vyžadovaného pro `workspaceResourceId` hodnotu parametru v souboru **containerSolutionParams.json** , proveďte následující kroky a potom spusťte rutinu PowerShellu nebo příkaz Azure CLI, abyste mohli řešení přidat.

1. Vypíše seznam všech předplatných, ke kterým máte přístup, pomocí následujícího příkazu:

    ```azurecli
    az account list --all -o table
    ```

    Výstup bude vypadat přibližně takto:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

    Zkopírujte hodnotu pro **SubscriptionId** .

2. Přepněte do předplatného hostujícího Log Analytics pracovní prostor pomocí následujícího příkazu:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. Následující příklad zobrazí seznam pracovních prostorů v předplatných ve výchozím formátu JSON.

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Ve výstupu vyhledejte název pracovního prostoru a zkopírujte úplné ID prostředku, které Log Analytics pracovní prostor pod **ID** pole.

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

5. Uložte tento soubor jako containerSolution.jsdo místní složky.

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

7. Upravte hodnoty pro **workspaceResourceId** pomocí hodnoty, kterou jste zkopírovali v kroku 3, a pro **WorkspaceRegion** Zkopírujte hodnotu **oblasti** po spuštění příkazu Azure CLI [AZ monitor Log-Analytics pracovní prostor zobrazit](/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest#az-monitor-log-analytics-workspace-list&preserve-view=true).

8. Uložte tento soubor jako containerSolutionParams.jsdo místní složky.

9. Jste připraveni k nasazení této šablony.

   - K nasazení pomocí Azure PowerShell použijte ve složce obsahující šablonu tyto příkazy:

       ```powershell
       # configure and login to the cloud of Log Analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of Log Analytics workspace>
       ```

       ```powershell
       # execute deployment command to add Container Insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of Log Analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```

       Dokončení změny konfigurace může trvat několik minut. Po dokončení se zobrazí zpráva podobná následující zprávě, která obsahuje výsledek:

       ```powershell
       provisioningState       : Succeeded
       ```

   - Pokud ho chcete nasadit pomocí Azure CLI, spusťte následující příkazy:

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az deployment group create --resource-group <resource group of log analytics workspace> --name <deployment name> --template-file  ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       Dokončení změny konfigurace může trvat několik minut. Po dokončení se zobrazí zpráva podobná následující zprávě, která obsahuje výsledek:

       ```azurecli
       provisioningState       : Succeeded
       ```

       Po povolení monitorování může trvat přibližně 15 minut, než budete moct zobrazit metriky stavu clusteru.

## <a name="install-the-helm-chart"></a>Instalace grafu HELM

V této části nainstalujete agenta s kontejnery pro Azure Monitor pro kontejnery. Než budete pokračovat, je nutné určit ID pracovního prostoru požadované pro `omsagent.secret.wsid` parametr a primární klíč vyžadovaný pro `omsagent.secret.key` parametr. Tyto informace můžete identifikovat provedením následujících kroků a následným spuštěním příkazů pro instalaci agenta pomocí grafu HELM.

1. Pro identifikaci ID pracovního prostoru spusťte následující příkaz:

    `az monitor log-analytics workspace list --resource-group <resourceGroupName>`

    Ve výstupu vyhledejte pod **názvem** pole název pracovního prostoru a zkopírujte ID pracovního prostoru Log Analytics pracovního prostoru pod pole **KódZákazníka** .

2. Pro identifikaci primárního klíče pracovního prostoru spusťte následující příkaz:

    `az monitor log-analytics workspace get-shared-keys --resource-group <resourceGroupName> --workspace-name <logAnalyticsWorkspaceName>`

    Ve výstupu vyhledejte primární klíč pod polem **primarySharedKey** a potom zkopírujte hodnotu.

>[!NOTE]
>Následující příkazy jsou použitelné pouze pro Helm verze 2. Použití parametru není `--name` použitelné pro Helm verze 3. 

>[!NOTE]
>Pokud váš cluster Kubernetes komunikuje prostřednictvím proxy server, nakonfigurujte parametr `omsagent.proxy` s adresou URL proxy server. Pokud cluster nekomunikuje prostřednictvím proxy server, nemusíte tento parametr zadávat. Další informace najdete v části [Konfigurace koncového bodu proxy serveru](#configure-proxy-endpoint) dále v tomto článku.

3. Přidejte úložiště Azure Charts do svého místního seznamu spuštěním následujícího příkazu:

    ```
    helm repo add microsoft https://microsoft.github.io/charts/repo
    ````

4. Nainstalujte graf spuštěním následujícího příkazu:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<my_prod_cluster> microsoft/azuremonitor-containers
    ```

    Pokud je pracovní prostor Log Analytics v Azure Čína 21Vianet, spusťte následující příkaz:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Pokud je pracovní prostor Log Analytics ve vládě Azure USA, spusťte následující příkaz:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

### <a name="enable-the-helm-chart-using-the-api-model"></a>Povolení grafu Helm pomocí modelu rozhraní API

Doplněk můžete zadat v souboru JSON specifikace clusteru AKS Engine, označovaný také jako model rozhraní API. V tomto doplňku zadejte verzi kódovaného kódu base64 `WorkspaceGUID` a `WorkspaceKey` Log Analytics pracovní prostor, ve kterém jsou shromážděná data monitorování uložená. Můžete najít `WorkspaceGUID` a `WorkspaceKey` pomocí kroků 1 a 2 v předchozí části.

Podporované definice rozhraní API pro cluster centra Azure Stack najdete v tomto příkladu – [kubernetes-container-monitoring_existing_workspace_id_and_key.jsna](https://github.com/Azure/aks-engine/blob/master/examples/addons/container-monitoring/kubernetes-container-monitoring_existing_workspace_id_and_key.json). Konkrétně Najděte vlastnost **Doplňky** v **kubernetesConfig** :

```json
"orchestratorType": "Kubernetes",
       "kubernetesConfig": {
         "addons": [
           {
             "name": "container-monitoring",
             "enabled": true,
             "config": {
               "workspaceGuid": "<Azure Log Analytics Workspace Id in Base-64 encoded>",
               "workspaceKey": "<Azure Log Analytics Workspace Key in Base-64 encoded>"
             }
           }
         ]
       }
```

## <a name="configure-agent-data-collection"></a>Konfigurace shromažďování dat agenta

V případě sestavování pomocí grafu verze 1.0.0 se nastavení shromažďování dat agenta řídí z ConfigMap. [Tady](container-insights-agent-config.md)najdete informace o nastavení shromažďování dat agenta v dokumentaci.

Po úspěšném nasazení grafu můžete zkontrolovat data pro svůj cluster Hybrid Kubernetes ve službě Azure Monitor for Containers z Azure Portal.  

>[!NOTE]
>Latence příjmu je od agenta pět do deseti minut od agenta k potvrzení v pracovním prostoru Azure Log Analytics. Stav clusteru zobrazí hodnotu **žádná data** nebo **neznámé** , dokud nejsou v Azure monitor k dispozici všechna požadovaná data monitorování.

## <a name="configure-proxy-endpoint"></a>Konfigurace koncového bodu proxy serveru

Počínaje grafem verze 2.7.1 bude graf podporovat zadání koncového bodu proxy s `omsagent.proxy` parametrem grafu. To umožňuje komunikaci pomocí proxy server. Komunikace mezi Azure Monitor pro agenta kontejnerů a Azure Monitor může být proxy server HTTP nebo HTTPS a podporuje se anonymní i základní ověřování (uživatelské jméno a heslo).

Hodnota konfigurace proxy má následující syntaxi: `[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
>Pokud vaše proxy server nevyžaduje ověřování, je stále nutné zadat uživatelské jméno nebo heslo psuedo. Může to být jakékoli uživatelské jméno nebo heslo.

|Vlastnost| Popis |
|--------|-------------|
|Protokol | http nebo https |
|uživatel | Volitelné uživatelské jméno pro ověřování proxy |
|heslo | Volitelné heslo pro ověřování proxy serveru |
|proxyhost | Adresa nebo plně kvalifikovaný název domény proxy server |
|port | Volitelné číslo portu pro proxy server |

Příklad: `omsagent.proxy=http://user01:password@proxy01.contoso.com:8080`

Pokud zadáte protokol jako **http** , požadavky HTTP se vytvoří pomocí zabezpečeného připojení SSL/TLS. Vaše proxy server musí podporovat protokoly SSL/TLS.

## <a name="troubleshooting"></a>Řešení potíží

Pokud dojde k chybě při pokusu o povolení monitorování pro cluster hybridního Kubernetes, zkopírujte skript PowerShellu [TroubleshootError_nonAzureK8s.ps1](https://aka.ms/troubleshoot-non-azure-k8s) a uložte ho do složky ve vašem počítači. Tento skript je k dispozici, aby bylo možné zjistit a opravit zjištěné problémy. Problémy, které je navrženo pro detekci a pokus o opravu, jsou následující:

- Zadaný pracovní prostor Log Analytics je platný.
- Pracovní prostor Log Analytics je nakonfigurovaný pomocí řešení Azure Monitor for Containers. V takovém případě nakonfigurujte pracovní prostor.
- OmsAgent REPLICASET lusky jsou spuštěné.
- OmsAgent daemonset lusky jsou spuštěné.
- Služba Health OmsAgent je spuštěná.
- ID a klíč pracovního prostoru Log Analytics nakonfigurované na kontejnerovém agentovi se shodují s pracovním prostorem, pomocí kterého je tento přehled nakonfigurovaný.
- Ověří, jestli mají všechny uzly pro Linux Worker `kubernetes.io/role=agent` popisek pro naplánování RS pod. Pokud neexistuje, přidejte ho.
- Ověřte `cAdvisor secure port:10250` nebo `unsecure port: 10255` je otevřeno na všech uzlech v clusteru.

Chcete-li provést příkaz s Azure PowerShell, použijte ve složce obsahující skript následující příkazy:

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>Další kroky

Díky monitorování s povoleným shromažďováním informací o stavu a využití prostředků hybridního clusteru Kubernetes a spuštěných úloh se naučíte [používat](container-insights-analyze.md) Azure monitor pro kontejnery.