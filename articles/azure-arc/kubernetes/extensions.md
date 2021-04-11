---
title: Rozšíření clusteru Kubernetes s povoleným rozšířením Azure ARC
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Nasazení a správa životního cyklu rozšíření v Kubernetes s povoleným ARC Azure
ms.openlocfilehash: 63fb14818d148dcc579300fdb4c89d636b47fd05
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450981"
---
# <a name="kubernetes-cluster-extensions"></a>Rozšíření clusteru Kubernetes

Funkce rozšíření Kubernetes umožňuje v clusterech Kubernetes s povoleným rozšířením Azure ARC tyto akce:

* Nasazení rozšíření clusteru založené na Azure Resource Manager.
* Správa životního cyklu pro Helm grafy rozšíření

Koncepční přehled této funkce je k dispozici v tématu [rozšíření clusteru – článek s povoleným Kubernetes ARC Azure](conceptual-extensions.md) .

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Požadavky

- [Nainstalujte nebo upgradujte Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) na verzi >= 2.16.0.
- `connectedk8s` (verze >= 1.1.0) a `k8s-extension` (verze >= 0.2.0) rozšíření rozhraní příkazového řádku Azure CLI. Nainstalujte tato rozšíření Azure CLI spuštěním následujících příkazů:
  
    ```azurecli
    az extension add --name connectedk8s
    az extension add --name k8s-extension
    ```
    
    Pokud `connectedk8s` `k8s-extension` jsou rozšíření a už nainstalovaná, můžete je aktualizovat na nejnovější verzi pomocí tohoto příkazu:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-extension
    ```

- Existující Kubernetes připojený cluster s podporou Azure ARC.
    - Pokud jste ještě nepřipojili cluster, Využijte náš [rychlý Start](quickstart-connect-cluster.md).
    - [Upgradujte agenty](agent-upgrade.md#manually-upgrade-agents) na verzi >= 1.1.0.

## <a name="currently-available-extensions"></a>Aktuálně dostupná rozšíření

| Linka | Popis |
| --------- | ----------- |
| [Azure Monitor](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json) | Poskytuje přehled o výkonu úloh nasazených v clusteru Kubernetes. Shromažďuje metriky využití paměti a procesoru z řadičů, uzlů a kontejnerů. |
| [Azure Defender](../../security-center/defender-for-kubernetes-azure-arc.md?toc=/azure/azure-arc/kubernetes/toc.json) | Shromažďuje data protokolu auditu z řídicích uzlů v clusteru Kubernetes. Poskytuje doporučení a upozornění na hrozby na základě shromážděných dat. |

## <a name="usage-of-cluster-extensions"></a>Využití rozšíření clusteru

### <a name="create-extensions-instance"></a>Vytvořit instanci rozšíření

Vytvořte novou instanci rozšíření s `k8s-extension create` a předejte hodnoty pro povinné parametry. Následující příkaz vytvoří instanci rozšíření Azure Monitor for Containers instance v clusteru Kubernetes s povoleným ARC Azure:

```azurecli
az k8s-extension create --name azuremonitor-containers  --extension-type Microsoft.AzureMonitor.Containers --scope cluster --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**Výkonem**

```json
{
  "autoUpgradeMinorVersion": true,
  "configurationProtectedSettings": null,
  "configurationSettings": {
    "logAnalyticsWorkspaceResourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/defaultresourcegroup-eus/providers/microsoft.operationalinsights/workspaces/defaultworkspace-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-eus"
  },
  "creationTime": "2021-04-02T12:13:06.7534628+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.azuremonitor.containers",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.Kubernetes/connectedClusters/demo/providers/Microsoft.KubernetesConfiguration/extensions/azuremonitor-containers",
  "identity": null,
  "installState": "Pending",
  "lastModifiedTime": "2021-04-02T12:13:06.753463+00:00",
  "lastStatusTime": null,
  "name": "azuremonitor-containers",
  "releaseTrain": "Stable",
  "resourceGroup": "demo",
  "scope": {
    "cluster": {
      "releaseNamespace": "azuremonitor-containers"
    },
    "namespace": null
  },
  "statuses": [],
  "systemData": null,
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "2.8.2"
}
```

> [!NOTE]
> * Služba nemůže uchovávat citlivé údaje po dobu delší než 48 hodin. Pokud Kubernetes agenti Azure mají připojení k síti déle než 48 hodin a nemůžou určit, jestli se má v clusteru vytvořit rozšíření, přechodem rozšíření na `Failed` stav. Jakmile budete ve `Failed` stavu, budete se muset `k8s-extension create` znovu spustit a vytvořit nový rozšíření prostředku Azure.
> * * Azure Monitor for Containers je rozšíření typu Singleton (pro každý cluster je potřeba jenom jeden). Před instalací stejných prostřednictvím rozšíření bude nutné vyčistit všechny předchozí instalace Helm grafů Azure Monitor pro kontejnery (bez rozšíření). [Před spuštěním `az k8s-extension create` použijte pokyny k odstranění grafu Helm ](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-optout-hybrid).

**Požadované parametry**

| Název parametru | Description |
|----------------|------------|
| `--name` | Název instance rozšíření |
| `--extension-type` | Typ rozšíření, které chcete v clusteru nainstalovat. Například: Microsoft. AzureMonitor. containers, Microsoft. azuredefender. Kubernetes | 
| `--scope` | Rozsah instalace rozšíření – `cluster` nebo `namespace` |
| `--cluster-name` | Název prostředku Kubernetes s povoleným obloukem Azure, na kterém se má instance rozšíření vytvořit |
| `--resource-group` | Skupina prostředků obsahující prostředek Kubernetes s povoleným ARC Azure |
| `--cluster-type` | Typ clusteru, na kterém má být instance rozšíření vytvořena. Jenom aktuální `connectedClusters` , což odpovídá Kubernetes s povoleným ARC Azure, je přijatá hodnota. |

**Volitelné parametry**

| Název parametru | Description |
|--------------|------------|
| `--auto-upgrade-minor-version` | Logická vlastnost, která určuje, zda bude vedlejší verze rozšíření automaticky upgradována nebo ne. Výchozí: `true`.  Pokud je tento parametr nastavený na hodnotu true, nemůžete nastavit `version` parametr, protože verze se bude dynamicky aktualizovat. Pokud je nastavená na `false` , rozšíření se automaticky neupgraduje ani pro verze patch. |
| `--version` | Verze rozšíření, které se má nainstalovat (konkrétní verze, na kterou se má připnout instance rozšíření) Nesmí být zadána, je-li automatický upgrade-vedlejší verze nastaven na `true` . |
| `--configuration-settings` | Nastavení, které lze předat do rozšíření pro řízení jeho funkcí. Mají být předány jako páry oddělené mezerou `key=value` za názvem parametru. Pokud je tento parametr použit v příkazu, `--configuration-settings-file` nelze jej použít ve stejném příkazu. |
| `--configuration-settings-file` | Cesta k souboru JSON s páry klíč-hodnota, která se má použít pro předání nastavení konfigurace rozšíření. Pokud je tento parametr použit v příkazu, `--configuration-settings` nelze jej použít ve stejném příkazu. |
| `--configuration-protected-settings` | Tato nastavení se nedají získat pomocí `GET` volání rozhraní API ani příkazů a používají se tak `az k8s-extension show` k předávání citlivých nastavení. Mají být předány jako páry oddělené mezerou `key=value` za názvem parametru. Pokud je tento parametr použit v příkazu, `--configuration-protected-settings-file` nelze jej použít ve stejném příkazu. |
| `--configuration-protected-settings-file` | Cesta k souboru JSON s páry klíč-hodnota, která se má použít pro předávání citlivých nastavení k rozšíření. Pokud je tento parametr použit v příkazu, `--configuration-protected-settings` nelze jej použít ve stejném příkazu. |
| `--release-namespace` | Tento parametr označuje obor názvů, ve kterém má být vydaná verze vytvořena. Tento parametr je relevantní pouze v případě `scope` , že je parametr nastaven na hodnotu `cluster` . |
| `--release-train` |  Autoři rozšíření mohou publikovat verze v různých vydáních, například `Stable` , `Preview` atd. Pokud tento parametr není nastaven explicitně, `Stable` použije se jako výchozí. Tento parametr nelze použít, `autoUpgradeMinorVersion` je-li parametr nastaven na hodnotu `false` . |
| `--target-namespace` | Tento parametr označuje obor názvů, ve kterém bude vydaná verze vytvořena. Oprávnění systémového účtu vytvořeného pro tuto instanci rozšíření bude omezeno na tento obor názvů. Tento parametr je vhodný pouze v případě, že `scope` je parametr nastaven na hodnotu `namespace` . |

### <a name="show-details-of-an-extension-instance"></a>Zobrazit podrobnosti instance rozšíření

Zobrazte podrobnosti o aktuálně nainstalované instanci rozšíření s `k8s-extension show` a předejte hodnoty pro povinné parametry:

```azurecli
az k8s-extension show --name azuremonitor-containers --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**Výkonem**

```json
{
  "autoUpgradeMinorVersion": true,
  "configurationProtectedSettings": null,
  "configurationSettings": {
    "logAnalyticsWorkspaceResourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/defaultresourcegroup-eus/providers/microsoft.operationalinsights/workspaces/defaultworkspace-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-eus"
  },
  "creationTime": "2021-04-02T12:13:06.7534628+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.azuremonitor.containers",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.Kubernetes/connectedClusters/demo/providers/Microsoft.KubernetesConfiguration/extensions/azuremonitor-containers",
  "identity": null,
  "installState": "Installed",
  "lastModifiedTime": "2021-04-02T12:13:06.753463+00:00",
  "lastStatusTime": "2021-04-02T12:13:49.636+00:00",
  "name": "azuremonitor-containers",
  "releaseTrain": "Stable",
  "resourceGroup": "demo",
  "scope": {
    "cluster": {
      "releaseNamespace": "azuremonitor-containers"
    },
    "namespace": null
  },
  "statuses": [],
  "systemData": null,
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "2.8.2"
}
```

### <a name="list-all-extensions-installed-on-the-cluster"></a>Vypíše všechna rozšíření, která jsou nainstalovaná v clusteru.

Vypíše všechna rozšíření, která jsou nainstalovaná v clusteru `k8s-extension list` , s předáním hodnot pro povinné parametry.

```azurecli
az k8s-extension list --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**Výkonem**

```json
[
  {
    "autoUpgradeMinorVersion": true,
    "creationTime": "2020-09-15T02:26:03.5519523+00:00",
    "errorInfo": {
      "code": null,
      "message": null
    },
    "extensionType": "Microsoft.AzureMonitor.Containers",
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myRg/providers/Microsoft.Kubernetes/connectedClusters/myCluster/providers/Microsoft.KubernetesConfiguration/extensions/myExtInstanceName",
    "identity": null,
    "installState": "Pending",
    "lastModifiedTime": "2020-09-15T02:48:45.6469664+00:00",
    "lastStatusTime": null,
    "name": "myExtInstanceName",
    "releaseTrain": "Stable",
    "resourceGroup": "myRG",
    "scope": {
      "cluster": {
        "releaseNamespace": "myExtInstanceName1"
      }
    },
    "statuses": [],
    "type": "Microsoft.KubernetesConfiguration/extensions",
    "version": "0.1.0"
  },
  {
    "autoUpgradeMinorVersion": true,
    "creationTime": "2020-09-02T00:41:16.8005159+00:00",
    "errorInfo": {
      "code": null,
      "message": null
    },
    "extensionType": "microsoft.azuredefender.kubernetes",
    "id": "/subscriptions/0e849346-4343-582b-95a3-e40e6a648ae1/resourceGroups/myRg/providers/Microsoft.Kubernetes/connectedClusters/myCluster/providers/Microsoft.KubernetesConfiguration/extensions/defender",
    "identity": null,
    "installState": "Pending",
    "lastModifiedTime": "2020-09-02T00:41:16.8005162+00:00",
    "lastStatusTime": null,
    "name": "microsoft.azuredefender.kubernetes",
    "releaseTrain": "Stable",
    "resourceGroup": "myRg",
    "scope": {
      "cluster": {
        "releaseNamespace": "myExtInstanceName2"
      }
    },
    "type": "Microsoft.KubernetesConfiguration/extensions",
    "version": "0.1.0"
  }
]
```

### <a name="update-an-existing-extension-instance"></a>Aktualizuje existující instanci rozšíření.

Aktualizujte instanci rozšíření na clusteru s `k8s-extension update` předáváním hodnot, které se mají aktualizovat.  Tento příkaz aktualizuje jenom `auto-upgrade-minor-version` vlastnosti, `release-train` a `version` . Například:

- **Výuka vydání aktualizací:**

    ```azurecli
    az k8s-extension update --name azuremonitor-containers --cluster-type connectedClusters --cluster-name <clusterName> --resource-group <resourceGroupName> --release-train Preview
    ```

- **Vypnout automatické upgrady a připnout instanci rozšíření na konkrétní verzi:**

    ```azurecli
    az k8s-extension update --name azuremonitor-containers --cluster-type connectedClusters --cluster-name <clusterName> --resource-group <resourceGroupName> --auto-upgrade-minor-version false --version 2.2.2
    ```

- **Zapnout automatický upgrade pro instanci rozšíření:**

    ```azurecli
    az k8s-extension update --name azuremonitor-containers --cluster-type connectedClusters --cluster-name <clusterName> --resource-group <resourceGroupName> --auto-upgrade-minor-version true
    ```

> [!NOTE]
> `version`Parametr lze nastavit pouze v případě `--auto-upgrade-minor-version` , že je nastavena na hodnotu `false` .

### <a name="delete-extension-instance"></a>Odstranit instanci rozšíření

Odstraní instanci rozšíření v clusteru s a předává `k8s-extension delete` hodnoty pro povinné parametry.

```azurecli
az k8s-extension delete --name azuremonitor-containers --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

>[!NOTE]
> Prostředek Azure představující toto rozšíření se hned odstraní. Vydaná verze Helm clusteru přidruženého k tomuto rozšíření se odstraní jenom v případě, že agenti běžící v clusteru Kubernetes mají připojení k síti a můžou se znovu spojit se službami Azure a načíst požadovaný stav.


## <a name="next-steps"></a>Další kroky

Další informace o rozšířeních clusteru, které jsou aktuálně k dispozici pro Kubernetes s podporou ARC Azure:
> [!div class="nextstepaction"]
> [Azure monitor](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json) 
>  [Azure Defender](../../security-center/defender-for-kubernetes-azure-arc.md?toc=/azure/azure-arc/kubernetes/toc.json)