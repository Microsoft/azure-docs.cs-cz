---
title: Nakonfigurujte cluster Kubernetes s povoleným ARC Azure pomocí Azure Monitor pro kontejnery | Microsoft Docs
description: Tento článek popisuje, jak nakonfigurovat monitorování pomocí Azure Monitor pro kontejnery v clusterech s podporou Kubernetes ARC Azure.
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 77b536141f0e7c6094964011719a0e536e8d33f1
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561474"
---
# <a name="enable-monitoring-of-azure-arc-enabled-kubernetes-cluster"></a>Povolení monitorování clusteru Kubernetes s podporou Azure Arc (Preview)

Azure Monitor for Containers poskytuje bohatou monitorovací prostředí pro clustery Azure Kubernetes Service (AKS) a AKS Engine. Tento článek popisuje, jak povolit monitorování clusterů Kubernetes hostovaných mimo Azure, které jsou povolené pomocí ARC Azure, abyste dosáhli podobných možností monitorování.

Azure Monitor pro kontejnery lze povolit pro jedno nebo více existujících nasazení Kubernetes pomocí skriptu PowerShell nebo bash.

## <a name="supported-configurations"></a>Podporované konfigurace

Azure Monitor for Containers podporuje monitorování Azure ARC s povoleným Kubernetes (Preview), jak je popsáno v článku [Přehled](container-insights-overview.md) , s výjimkou následujících funkcí:

- Živá data (Preview)

Následující je oficiálně podporovaná s Azure Monitor pro kontejnery:

- Verze Kubernetes a zásad podpory jsou stejné jako verze [podporovaných AKS](../../aks/supported-kubernetes-versions.md).

- Podporovány jsou následující moduly runtime kontejneru: rozhraní Docker, Moby a CRI kompatibilní s modulem runtime, jako jsou CRI-O a kontejnery.

- Podporovaná verze operačního systému Linux pro hlavní a pracovní uzly jsou: Ubuntu (18,04 LTS a 16,04 LTS).

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte následující:

- Pracovní prostor služby Log Analytics.

    Azure Monitor for Containers podporuje pracovní prostor Log Analytics v oblastech uvedených v [produktech Azure podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Pokud chcete vytvořit vlastní pracovní prostor, můžete ho vytvořit prostřednictvím [Azure Resource Manager](../samples/resource-manager-workspace.md), prostřednictvím [PowerShellu](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)nebo v [Azure Portal](../learn/quick-create-workspace.md).

- Pokud chcete povolit a přistupovat k funkcím v Azure Monitor pro kontejnery, minimálně musíte být členem role *přispěvatele* Azure v předplatném Azure a členem role [*přispěvatele Log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) v pracovním prostoru Log Analytics s nakonfigurovaným Azure monitor for Containers.

- Jste členem role [Přispěvatel](../../role-based-access-control/built-in-roles.md#contributor) v prostředku clusteru ARC Azure.

- Chcete-li zobrazit data monitorování, jste členem oprávnění role [*čtenář Log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) s pracovním prostorem Log Analytics nakonfigurovaným Azure monitor for Containers.

- [Helm klientovi](https://helm.sh/docs/using_helm/) , aby se připojil diagram Azure monitor for Containers pro zadaný cluster Kubernetes.

- Následující informace o konfiguraci proxy serveru a brány firewall jsou vyžadovány pro kontejnerové verze Log Analytics agenta pro Linux pro komunikaci s Azure Monitor:

    |Prostředek agenta|Porty |
    |------|---------|
    |`*.ods.opinsights.azure.com` |Port 443 |
    |`*.oms.opinsights.azure.com` |Port 443 |
    |`*.dc.services.visualstudio.com` |Port 443 |

- Kontejner s označením vyžaduje, aby se Kubelet `cAdvisor secure port: 10250` nebo `unsecure port :10255` otevřel na všech uzlech v clusteru za účelem shromažďování metrik výkonu. Doporučujeme, abyste nakonfigurovali `secure port: 10250` na cAdvisor pro Kubelet, pokud už není nakonfigurovaná.

- Kontejner s označením vyžaduje, aby v kontejneru bylo zadáno následující proměnné prostředí, aby bylo možné komunikovat se službou Kubernetes API v rámci clusteru za účelem shromažďování dat inventáře `KUBERNETES_SERVICE_HOST` a `KUBERNETES_PORT_443_TCP_PORT` .

    >[!IMPORTANT]
    >Minimální verze agenta podporovaná pro monitorování clusterů Kubernetes s podporou ARC je ciprod04162020 nebo novější.

- Pokud povolíte monitorování pomocí skriptované metody PowerShellu, je potřeba [PowerShell Core](/powershell/scripting/install/installing-powershell?view=powershell-6&preserve-view=true) .

- [Bash verze 4](https://www.gnu.org/software/bash/) je vyžadována, pokud povolíte monitorování pomocí skriptové metody bash.

## <a name="identify-workspace-resource-id"></a>Identifikace ID prostředku pracovního prostoru

Pokud chcete monitorovat svůj cluster pomocí skriptu prostředí PowerShell nebo bash, který jste stáhli dříve, a integrujte ho s existujícím pracovním prostorem Log Analytics proveďte následující kroky, abyste nejdřív identifikovali úplné ID prostředku pracovního prostoru Log Analytics. Tato možnost je vyžadována pro `workspaceResourceId` parametr při spuštění příkazu pro povolení doplňku monitorování proti zadanému pracovnímu prostoru. Pokud nemáte pracovní prostor, který by se měl zadat, můžete přeskočit včetně `workspaceResourceId` parametru a nechat skript vytvořit nový pracovní prostor.

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

    Zkopírujte hodnotu pro **SubscriptionId**.

2. Přepněte do předplatného hostujícího Log Analytics pracovní prostor pomocí následujícího příkazu:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. Následující příklad zobrazí seznam pracovních prostorů v předplatných ve výchozím formátu JSON.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Ve výstupu vyhledejte název pracovního prostoru a zkopírujte úplné ID prostředku, které Log Analytics pracovní prostor pod **ID** pole.

## <a name="enable-monitoring-using-powershell"></a>Povolení monitorování pomocí prostředí PowerShell

1. Stáhněte a uložte skript do místní složky, která konfiguruje cluster pomocí doplňku monitorování pomocí následujících příkazů:

    ```powershell
    Invoke-WebRequest https://aka.ms/enable-monitoring-powershell-script -OutFile enable-monitoring.ps1
    ```

2. Nakonfigurujte `$azureArcClusterResourceId` proměnnou tak, že nastavíte odpovídající hodnoty `subscriptionId` pro `resourceGroupName` a `clusterName` zastupujete ID prostředku vašeho prostředku clusteru Kubernetes s podporou ARC Azure.

    ```powershell
    $azureArcClusterResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. `$kubeContext`Pomocí příkazu nakonfigurujte proměnnou pomocí **Kube kontextu** clusteru `kubectl config get-contexts` . Pokud chcete použít aktuální kontext, nastavte hodnotu na `""` .

    ```powershell
    $kubeContext = "<kubeContext name of your k8s cluster>"
    ```

4. Pokud chcete použít existující pracovní prostor Azure Monitor Log Analytics, nakonfigurujte proměnnou `$logAnalyticsWorkspaceResourceId` s odpovídající hodnotou, která představuje ID prostředku pracovního prostoru. Jinak nastavte proměnnou na `""` a skript vytvoří výchozí pracovní prostor ve výchozí skupině prostředků v rámci předplatného clusteru, pokud ještě neexistuje v této oblasti. Výchozí vytvořený pracovní prostor se podobá formátu *DefaultWorkspace- \<SubscriptionID> - \<Region>*.

    ```powershell
    $logAnalyticsWorkspaceResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.operationalinsights/workspaces/<workspaceName>"
    ```

5. Pokud váš Kubernetes cluster s podporou ARC komunikuje prostřednictvím proxy server, nakonfigurujte proměnnou `$proxyEndpoint` s adresou URL proxy server. Pokud cluster nekomunikuje prostřednictvím proxy server, můžete hodnotu nastavit na `""` .  Další informace najdete v části [Konfigurace koncového bodu proxy serveru](#configure-proxy-endpoint) dále v tomto článku.

6. Spusťte následující příkaz, který povolí monitorování.

    ```powershell
    .\enable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext -workspaceResourceId $logAnalyticsWorkspaceResourceId -proxyEndpoint $proxyEndpoint
    ```

Po povolení monitorování může trvat přibližně 15 minut, než budete moct zobrazit metriky stavu clusteru.

### <a name="using-service-principal"></a>Použití instančního objektu
Skript *enable-monitoring.ps1* používá přihlášení interaktivního zařízení. Pokud dáváte přednost neinteraktivnímu přihlášení, můžete použít existující instanční objekt nebo vytvořit nový, který má požadovaná oprávnění, jak je popsáno v části [požadavky](#prerequisites). Chcete-li použít instanční objekt, bude nutné předat $servicePrincipalClientId, $servicePrincipalClientSecret a $tenantId parametry s hodnotami instančního objektu, který chcete použít ke *enable-monitoring.ps1* skriptu.

```powershell
$subscriptionId = "<subscription Id of the Azure Arc connected cluster resource>"
$servicePrincipal = New-AzADServicePrincipal -Role Contributor -Scope "/subscriptions/$subscriptionId"
```

Přiřazení role níže se dá použít jenom v případě, že používáte existující Log Analytics pracovní prostor v jiném předplatném Azure než K8s připojený clusterový prostředek.

```powershell
$logAnalyticsWorkspaceResourceId = "<Azure Resource Id of the Log Analytics Workspace>" # format of the Azure Log Analytics workspace should be /subscriptions/<subId>/resourcegroups/<rgName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>
New-AzRoleAssignment -RoleDefinitionName 'Log Analytics Contributor'  -ObjectId $servicePrincipal.Id -Scope  $logAnalyticsWorkspaceResourceId

$servicePrincipalClientId =  $servicePrincipal.ApplicationId.ToString()
$servicePrincipalClientSecret = [System.Net.NetworkCredential]::new("", $servicePrincipal.Secret).Password
$tenantId = (Get-AzSubscription -SubscriptionId $subscriptionId).TenantId
```

Příklad:

```powershell
.\enable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -servicePrincipalClientId $servicePrincipalClientId -servicePrincipalClientSecret $servicePrincipalClientSecret -tenantId $tenantId -kubeContext $kubeContext -workspaceResourceId $logAnalyticsWorkspaceResourceId -proxyEndpoint $proxyEndpoint
```



## <a name="enable-using-bash-script"></a>Povolit pomocí skriptu bash

Provedením následujících kroků povolíte monitorování pomocí zadaného skriptu bash.

1. Stáhněte a uložte skript do místní složky, která konfiguruje cluster pomocí doplňku monitorování pomocí následujících příkazů:

    ```bash
    curl -o enable-monitoring.sh -L https://aka.ms/enable-monitoring-bash-script
    ```

2. Nakonfigurujte `azureArcClusterResourceId` proměnnou tak, že nastavíte odpovídající hodnoty `subscriptionId` pro `resourceGroupName` a `clusterName` zastupujete ID prostředku vašeho prostředku clusteru Kubernetes s podporou ARC Azure.

    ```bash
    export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. `kubeContext`Pomocí příkazu nakonfigurujte proměnnou pomocí **Kube kontextu** clusteru `kubectl config get-contexts` . Pokud chcete použít aktuální kontext, nastavte hodnotu na `""` .

    ```bash
    export kubeContext="<kubeContext name of your k8s cluster>"
    ```

4. Pokud chcete použít existující pracovní prostor Azure Monitor Log Analytics, nakonfigurujte proměnnou `logAnalyticsWorkspaceResourceId` s odpovídající hodnotou, která představuje ID prostředku pracovního prostoru. Jinak nastavte proměnnou na `""` a skript vytvoří výchozí pracovní prostor ve výchozí skupině prostředků v rámci předplatného clusteru, pokud ještě neexistuje v této oblasti. Výchozí vytvořený pracovní prostor se podobá formátu *DefaultWorkspace- \<SubscriptionID> - \<Region>*.

    ```bash
    export logAnalyticsWorkspaceResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.operationalinsights/workspaces/<workspaceName>"
    ```

5. Pokud váš Kubernetes cluster s podporou ARC komunikuje prostřednictvím proxy server, nakonfigurujte proměnnou `proxyEndpoint` s adresou URL proxy server. Pokud cluster nekomunikuje prostřednictvím proxy server, můžete hodnotu nastavit na `""` . Další informace najdete v části [Konfigurace koncového bodu proxy serveru](#configure-proxy-endpoint) dále v tomto článku.

6. Pokud chcete povolit monitorování clusteru, jsou na základě scénáře nasazení k dispozici různé příkazy.

    Spusťte následující příkaz, který umožní monitorování s výchozími možnostmi, jako je například použití aktuálního Kube-Context, vytvoření výchozího pracovního prostoru Log Analytics a bez zadání proxy server:

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId
    ```

    Spuštěním následujícího příkazu vytvořte výchozí pracovní prostor Log Analytics a bez zadání proxy server:

    ```bash
   bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext
    ```

    Spusťte následující příkaz, který použije existující Log Analytics pracovní prostor a bez zadání proxy server:

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId
    ```

    Spusťte následující příkaz, který použije existující Log Analytics pracovní prostor a určí proxy server:

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId --proxy $proxyEndpoint
    ```

Po povolení monitorování může trvat přibližně 15 minut, než budete moct zobrazit metriky stavu clusteru.

### <a name="using-service-principal"></a>Použití instančního objektu
Skript bash *Enable-monitoring.sh* používá přihlášení interaktivního zařízení. Pokud dáváte přednost neinteraktivnímu přihlášení, můžete použít existující instanční objekt nebo vytvořit nový, který má požadovaná oprávnění, jak je popsáno v části [požadavky](#prerequisites). Chcete-li použít instanční objekt, budete muset předat--Client-ID,--Client-Secret a--tenant-ID instančního objektu, který jste chtěli použít ke *Enable-monitoring.sh* skriptu bash.

```bash
subscriptionId="<subscription Id of the Azure Arc connected cluster resource>"
servicePrincipal=$(az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${subscriptionId}")
servicePrincipalClientId=$(echo $servicePrincipal | jq -r '.appId')
```

Přiřazení role níže se dá použít jenom v případě, že používáte existující Log Analytics pracovní prostor v jiném předplatném Azure než K8s připojený clusterový prostředek.

```bash
logAnalyticsWorkspaceResourceId="<Azure Resource Id of the Log Analytics Workspace>" # format of the Azure Log Analytics workspace should be /subscriptions/<subId>/resourcegroups/<rgName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>
az role assignment create --role 'Log Analytics Contributor' --assignee $servicePrincipalClientId --scope $logAnalyticsWorkspaceResourceId

servicePrincipalClientSecret=$(echo $servicePrincipal | jq -r '.password')
tenantId=$(echo $servicePrincipal | jq -r '.tenant')
```

Příklad:

```bash
bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --client-id $servicePrincipalClientId --client-secret $servicePrincipalClientSecret  --tenant-id $tenantId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId --proxy $proxyEndpoint
```

## <a name="configure-proxy-endpoint"></a>Konfigurace koncového bodu proxy serveru

Pomocí kontejnerového agenta pro Azure Monitor pro kontejnery můžete nakonfigurovat koncový bod proxy serveru tak, aby mohl komunikovat přes proxy server. Komunikace mezi kontejnerovým agentem a Azure Monitor může být proxy server HTTP nebo HTTPS a podporuje se anonymní i základní ověřování (uživatelské jméno a heslo).

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

Příklad: `http://user01:password@proxy01.contoso.com:3128`

Pokud zadáte protokol jako **http**, požadavky HTTP se vytvoří pomocí zabezpečeného připojení SSL/TLS. Vaše proxy server musí podporovat protokoly SSL/TLS.

### <a name="configure-using-powershell"></a>Konfigurace prostřednictvím prostředí PowerShell

Zadejte uživatelské jméno a heslo, IP adresu nebo plně kvalifikovaný název domény a číslo portu pro proxy server. Příklad:

```powershell
$proxyEndpoint = https://<user>:<password>@<proxyhost>:<port>
```

### <a name="configure-using-bash"></a>Konfigurace pomocí bash

Zadejte uživatelské jméno a heslo, IP adresu nebo plně kvalifikovaný název domény a číslo portu pro proxy server. Příklad:

```bash
export proxyEndpoint=https://<user>:<password>@<proxyhost>:<port>
```

## <a name="next-steps"></a>Další kroky

- Díky monitorování s povoleným shromažďováním informací o stavu a využití prostředků v clusteru Kubernetes s povoleným obloukem a úlohám, které jsou na nich spuštěné, se naučíte, [Jak používat](container-insights-analyze.md) Azure monitor pro kontejnery.

- Ve výchozím nastavení agent kontejnerů shromažďuje protokoly kontejnerů stdout/stderr všech kontejnerů spuštěných ve všech oborech názvů kromě Kube-System. Pokud chcete nakonfigurovat kolekci protokolů kontejnerů specificky pro konkrétní obory názvů nebo obory názvů, zkontrolujte [konfiguraci agenta služby Container Insights](container-insights-agent-config.md) a nakonfigurujte požadovaná nastavení shromažďování dat na váš soubor konfigurace ConfigMap.

- Pokud si chcete vyřadit a analyzovat metriky Prometheus z clusteru, přečtěte si téma Konfigurace vyřazení [metrik Prometheus](container-insights-prometheus-integration.md) .

- Informace o tom, jak zastavit monitorování clusteru Kubernetes s povoleným ARC pomocí Azure Monitor for Containers, najdete v tématu [Postup zastavení monitorování hybridního clusteru](container-insights-optout-hybrid.md#how-to-stop-monitoring-on-arc-enabled-kubernetes).