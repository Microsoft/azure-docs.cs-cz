---
title: Monitorování nového clusteru Azure Kubernetes Service (AKS) | Microsoft Docs
description: Naučte se, jak povolit monitorování pro nový cluster Azure Kubernetes Service (AKS) s předplatným Azure Monitor for Containers.
ms.topic: conceptual
ms.date: 04/25/2019
ms.custom: devx-track-terraform, devx-track-azurecli
ms.openlocfilehash: 19c4a88cee8776136593b041e94dd14c7c9c28d6
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92735075"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>Povolit monitorování nového clusteru Azure Kubernetes Service (AKS)

Tento článek popisuje, jak nastavit Azure Monitor pro kontejnery pro monitorování spravovaného clusteru Kubernetes hostovaného ve [službě Azure Kubernetes](../../aks/index.yml) , kterou jste připravili k nasazení v rámci vašeho předplatného.

Monitorování clusteru AKS můžete povolit pomocí jedné z podporovaných metod:

* Azure CLI
* Terraform

## <a name="enable-using-azure-cli"></a>Povolení pomocí Azure CLI

Pokud chcete povolit monitorování nového clusteru AKS vytvořeného pomocí Azure CLI, postupujte podle kroků v článku rychlý Start v části [Vytvoření clusteru AKS](../../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Pokud se rozhodnete používat rozhraní příkazového řádku Azure, musíte nejdřív nainstalovat a používat rozhraní příkazového řádku (CLI). Musíte používat Azure CLI verze 2.0.74 nebo novější. Pro identifikaci vaší verze spusťte `az --version` . Pokud potřebujete nainstalovat nebo upgradovat rozhraní příkazového řádku Azure CLI, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli). Pokud máte nainstalovanou verzi rozšíření AKS-Preview CLI 0.4.12 nebo vyšší, odeberte všechny provedené změny, aby bylo možné povolit rozšíření verze Preview, protože funkce AKS Preview nejsou dostupné v cloudu Azure US governmnet.

## <a name="enable-using-terraform"></a>Povolit pomocí Terraformu

Pokud [nasazujete nový cluster AKS pomocí terraformu](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks), zadáte argumenty požadované v profilu [k vytvoření pracovního prostoru Log Analytics](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html) , pokud se nerozhodnete zadat existující. 

>[!NOTE]
>Pokud se rozhodnete používat Terraformu, musíte mít spuštěného poskytovatele Terraformu Azure RM verze 1.17.0 nebo vyšší.

Chcete-li přidat Azure Monitor pro kontejnery do pracovního prostoru, přečtěte si téma [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) a dokončete profil zahrnutím [**addon_profile**](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) a zadáním **oms_agent** . 

Jakmile povolíte monitorování a všechny úlohy konfigurace budou úspěšně dokončeny, můžete monitorovat výkon clusteru jedním ze dvou způsobů:

* Přímo v clusteru AKS výběrem možnosti **stav** v levém podokně.
* Výběrem dlaždice **monitorovat službu Container Insights** na stránce clusteru AKS pro vybraný cluster. V Azure Monitor v levém podokně vyberte **stav** . 

  ![Možnosti výběru Azure Monitor pro kontejnery v AKS](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

Po povolení monitorování může trvat přibližně 15 minut, než budete moct zobrazit metriky stavu clusteru. 

## <a name="verify-agent-and-solution-deployment"></a>Ověření nasazení agenta a řešení
S agentem verze *06072018* nebo novější můžete ověřit, že se agent i řešení úspěšně nasadily. V dřívějších verzích agenta můžete ověřovat jenom nasazení agenta.

### <a name="agent-version-06072018-or-later"></a>Agent verze 06072018 nebo novější
Spusťte následující příkaz a ověřte, zda byl agent úspěšně nasazen. 

```
kubectl get ds omsagent --namespace=kube-system
```

Výstup by měl vypadat podobně jako v následujícím příkladu, což znamená, že byl správně nasazen:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Pokud chcete ověřit nasazení řešení, spusťte následující příkaz:

```
kubectl get deployment omsagent-rs -n=kube-system
```

Výstup by měl vypadat podobně jako v následujícím příkladu, což znamená, že byl správně nasazen:

```
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

```
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

## <a name="next-steps"></a>Další kroky

* Pokud při pokusu o připojení řešení dochází k problémům, přečtěte si [příručku k odstraňování potíží](container-insights-troubleshoot.md) .

* Díky monitorování s povoleným shromažďováním informací o stavu a využití prostředků v clusteru AKS a úlohách, které se na nich běží, se naučíte, [Jak používat](container-insights-analyze.md) Azure monitor pro kontejnery.

