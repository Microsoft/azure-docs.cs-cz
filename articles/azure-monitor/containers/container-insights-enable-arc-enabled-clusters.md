---
title: Monitorování clusterů Kubernetes s povoleným ARC Azure
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Shromažďování metrik a protokolů clusterů Kubernetes s povoleným ARC Azure pomocí Azure Monitor
ms.openlocfilehash: 0a983f6d7032310d02d35e713482de942bfbfd70
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443846"
---
# <a name="azure-monitor-container-insights-for-azure-arc-enabled-kubernetes-clusters"></a>Azure Monitor služby Container Insights pro clustery Kubernetes s podporou ARC Azure

[Azure monitor Container Insights](container-insights-overview.md) poskytuje bohatou monitorovací prostředí pro clustery Kubernetes s povoleným ARC Azure.

[!INCLUDE [preview features note](../../azure-arc/kubernetes/includes/preview/preview-callout.md)]

## <a name="supported-configurations"></a>Podporované konfigurace

- Azure Monitor Container Insights podporuje monitorování Kubernetes s povoleným zobrazením Azure ARC (Preview), jak je popsáno v článku [Přehled](container-insights-overview.md) s výjimkou funkce živá data (Preview). Uživatelé navíc nemusí mít oprávnění [vlastníka](../../role-based-access-control/built-in-roles.md#owner) [Povolit metriky](container-insights-update-metrics.md) .
- `Docker`, `Moby` a CRI kompatibilní modul runtime kontejnerů, jako jsou `CRI-O` a `containerd` .
- Podpora odchozího proxy serveru bez ověřování a odchozího proxy serveru se základním ověřováním je podporovaná. Odchozí proxy server, který očekává důvěryhodné certifikáty, se v tuto chvíli nepodporuje.

## <a name="prerequisites"></a>Požadavky

- Splnili jste požadavky uvedené v části [Obecná dokumentace k rozšířením clusteru](../../azure-arc/kubernetes/extensions.md#prerequisites).
- Log Analytics pracovní prostor: Azure Monitor Container Insights podporuje Log Analytics pracovní prostor v oblastech uvedených na [stránce produkty Azure podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Můžete vytvořit vlastní pracovní prostor prostřednictvím [Azure Resource Manager](../logs/resource-manager-workspace.md), [powershellu](../logs/powershell-sample-create-workspace.md)nebo [Azure Portal](../logs/quick-create-workspace.md).
- Musíte mít přiřazení role [přispěvatele](../../role-based-access-control/built-in-roles.md#contributor) v předplatném Azure, které obsahuje prostředek Kubernetes s povoleným ARC Azure. Pokud je pracovní prostor Log Analytics v jiném předplatném, je nutné v pracovním prostoru Log Analytics Log Analytics přiřazení role [přispěvatele](../logs/manage-access.md#manage-access-using-azure-permissions) .
- Chcete-li zobrazit data monitorování, je nutné mít přiřazení role [Log Analytics Reader](../logs/manage-access.md#manage-access-using-azure-permissions) v pracovním prostoru Log Analytics.
- Kromě těch, které jsou uvedené v části [připojení clusteru Kubernetes ke službě Azure ARC](../../azure-arc/kubernetes/quickstart-connect-cluster.md#meet-network-requirements), je třeba povolit pro odchozí přístup následující koncové body.

    | Koncový bod | Port |
    |----------|------|
    | `*.ods.opinsights.azure.com` | 443 |
    | `*.oms.opinsights.azure.com` | 443 |
    | `dc.services.visualstudio.com` | 443 |
    | `*.monitoring.azure.com` | 443 |
    | `login.microsoftonline.com` | 443 |

    Pokud je prostředek Kubernetes s povoleným ARC v prostředí Azure USA pro státní správu, musí být pro odchozí přístup povolené tyto koncové body:

    | Koncový bod | Port |
    |----------|------|
    | `*.ods.opinsights.azure.us` | 443 |
    | `*.oms.opinsights.azure.us` | 443 |
    | `dc.services.visualstudio.com` | 443 |
    

- Pokud jste dříve nasadili Azure Monitor Container Insights v tomto clusteru pomocí skriptu bez rozšíření clusteru, odstraňte tento graf Helm podle pokynů uvedených [tady](container-insights-optout-hybrid.md) . Potom můžete pokračovat v vytváření instance rozšíření clusteru pro Azure Monitor službu Container Insights.

    >[!NOTE]
    > Verze služby Azure Monitor Container Insights (Preview) založená na skriptu nahrazuje formou [rozšíření clusteru](../../azure-arc/kubernetes/extensions.md) nasazení. Azure Monitor nasazené dřív prostřednictvím skriptu se podporují jenom do června 2021 a proto se doporučuje migrovat na formulář rozšíření clusteru nasazení v nejbližší části.

### <a name="identify-workspace-resource-id"></a>Identifikace ID prostředku pracovního prostoru

Spusťte následující příkazy, abyste našli úplný Azure Resource Manager identifikátor Log Analytics pracovního prostoru. 

1. Vypíše seznam všech předplatných, ke kterým máte přístup, pomocí následujícího příkazu:

    ```azurecli
    az account list --all -o table
    ```

2. Přepněte do předplatného hostujícího Log Analytics pracovní prostor pomocí následujícího příkazu:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. Následující příklad zobrazí seznam pracovních prostorů v předplatných ve výchozím formátu JSON.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Ve výstupu vyhledejte název pracovního prostoru, který vás zajímá. `id`Pole, které představuje identifikátor Azure Resource Manager Log Analytics pracovním prostoru.

    >[!TIP]
    > To `id` lze také najít v okně *Přehled* pracovního prostoru Log Analytics pomocí Azure Portal.

## <a name="create-extension-instance-using-azure-cli"></a>Vytvoření instance rozšíření pomocí Azure CLI

### <a name="option-1---with-default-values"></a>Možnost 1 – s výchozími hodnotami

Tato možnost používá následující výchozí hodnoty:

- Vytvoří nebo použije existující výchozí pracovní prostor Log Analytics odpovídající oblasti clusteru.
- Pro rozšíření Azure Monitor clusteru je povolen automatický upgrade.

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers
```

### <a name="option-2---with-existing-azure-log-analytics-workspace"></a>Možnost 2 – stávající pracovní prostor služby Azure Log Analytics

Existující pracovní prostor služby Azure Log Analytics můžete použít v jakémkoli předplatném, ke kterému máte *Přispěvatel* nebo přiřazení přísnější role.

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings logAnalyticsWorkspaceResourceID=<armResourceIdOfExistingWorkspace>
```

### <a name="option-3---with-advanced-configuration"></a>Možnost 3 – s pokročilou konfigurací

Pokud chcete upravit výchozí požadavky a omezení prostředků, můžete použít Pokročilá nastavení konfigurace:

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings  omsagent.resources.daemonset.limits.cpu=150m omsagent.resources.daemonset.limits.memory=600Mi omsagent.resources.deployment.limits.cpu=1 omsagent.resources.deployment.limits.memory=750Mi
```

Pro dostupná nastavení konfigurace můžete rezervovat [oddíl požadavky na prostředky a omezení v grafu Helm](https://github.com/helm/charts/blob/master/incubator/azuremonitor-containers/values.yaml) .

### <a name="option-4---on-azure-stack-edge"></a>Možnost 4 – na Azure Stack Edge

Pokud je cluster Kubernetes s podporou Azure ARC na Azure Stack hraničních zařízeních, musí se použít vlastní cesta připojení `/home/data/docker` .

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings omsagent.logsettings.custommountpath=/home/data/docker
```

>[!NOTE]
> Pokud explicitně zadáte verzi rozšíření, která má být nainstalována v příkazu CREATE, pak zajistěte, aby zadaná verze byla >= ve verzi 2.8.2.

## <a name="create-extension-instance-using-azure-portal"></a>Vytvořit instanci rozšíření pomocí Azure Portal

>[!IMPORTANT]
>  Pokud nasazujete Azure Monitor v clusteru Kubernetes, který běží na Azure Stack Edge, pak je potřeba použít možnost Azure CLI namísto možnosti Azure Portal, protože pro tyto clustery je potřeba nastavit vlastní cestu pro připojení.    

### <a name="onboarding-from-the-azure-arc-enabled-kubernetes-resource-blade"></a>Připojování z okna prostředků Kubernetes s povoleným ARC Azure

1. V Azure Portal vyberte cluster Kubernetes s podporou ARC, který chcete monitorovat.

2. V části monitorování v okně prostředku vyberte položku přehledy (Preview).

3. Na stránce připojování vyberte tlačítko Konfigurovat Azure Monitor.

4. Nyní můžete vybrat [pracovní prostor Log Analytics](../logs/quick-create-workspace.md) a odeslat metriky a data protokolů do.

5. Kliknutím na tlačítko Konfigurovat nasadíte Azure Monitor rozšíření clusteru služby Container Insights.

### <a name="onboarding-from-azure-monitor-blade"></a>Připojování z okna Azure Monitor

1. V Azure Portal přejděte na okno monitor a v nabídce přehledy vyberte možnost kontejnery.

2. Výběrem karty nemonitorované clustery zobrazíte clustery Kubernetes s povoleným použitím ARC Azure, pro které můžete povolit monitorování.

3. Klikněte na odkaz Povolit vedle clusteru, pro který chcete povolit monitorování.

4. Vyberte pracovní prostor Log Analytics a pokračujte tím, že kliknete na tlačítko konfigurovat.

## <a name="create-extension-instance-using-azure-resource-manager"></a>Vytvořit instanci rozšíření pomocí Azure Resource Manager

1. Stažení šablony a parametru Azure Resource Manager:

    ```console
    curl -L https://aka.ms/arc-k8s-azmon-extension-arm-template -o arc-k8s-azmon-extension-arm-template.json
    curl -L https://aka.ms/arc-k8s-azmon-extension-arm-template-params -o  arc-k8s-azmon-extension-arm-template-params.json
    ```

2. Aktualizuje hodnoty parametrů v arc-k8s-azmon-extension-arm-template-params.jssouboru. Pro veřejný cloud Azure je `opinsights.azure.com` nutné použít jako hodnotu workspaceDomain.

3. Nasazení šablony pro vytvoření Azure Monitor rozšíření Container Insights 

    ```console
    az login
    az account set --subscription "Subscription Name"
    az deployment group create --resource-group <resource-group> --template-file ./arc-k8s-azmon-extension-arm-template.json --parameters @./arc-k8s-azmon-extension-arm-template-params.json
    ```

## <a name="delete-extension-instance"></a>Odstranit instanci rozšíření

Následující příkaz odstraní instanci rozšíření, ale neodstraní pracovní prostor Log Analytics. Data v prostředku Log Analyticsu jsou ponechána beze změn.

```bash
az k8s-extension delete --name azuremonitor-containers --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <resource-group>
```

## <a name="disconnected-cluster"></a>Odpojený cluster
Pokud je váš cluster odpojený od Azure po dobu > 48 hodin, pak Azure Resource Graph nebude mít informace o vašem clusteru. V důsledku toho může okno přehledů zobrazovat nesprávné informace o stavu clusteru.

## <a name="next-steps"></a>Další kroky

- Díky monitorování s povoleným shromažďováním informací o stavu a využití prostředků clusteru Kubernetes s povoleným obloukem a úlohám, které jsou na nich spuštěné, se naučíte, [Jak používat službu](container-insights-analyze.md) Container Insights.

- Ve výchozím nastavení agent kontejnerů shromažďuje protokoly kontejnerů stdout/stderr všech kontejnerů spuštěných ve všech oborech názvů kromě Kube-System. Pokud chcete nakonfigurovat kolekci protokolů kontejnerů specificky pro konkrétní obory názvů nebo obory názvů, zkontrolujte [konfiguraci agenta služby Container Insights](container-insights-agent-config.md) a nakonfigurujte požadovaná nastavení shromažďování dat na váš soubor konfigurace ConfigMap.

- Pokud si chcete vyřadit a analyzovat metriky Prometheus z clusteru, přečtěte si téma Konfigurace vyřazení [metrik Prometheus](container-insights-prometheus-integration.md) .
