---
title: Monitorování nového clusteru Azure Kubernetes Service (AKS) | Microsoft Docs
description: Naučte se, jak povolit monitorování pro nový cluster Azure Kubernetes Service (AKS) s předplatným Azure Monitor for Containers.
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: c731826f2780c45358730f9ce20d6a6151f6f259
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78385457"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>Povolit monitorování nového clusteru Azure Kubernetes Service (AKS)

Tento článek popisuje, jak nastavit Azure Monitor pro kontejnery pro monitorování spravovaného clusteru Kubernetes hostovaného ve [službě Azure Kubernetes](https://docs.microsoft.com/azure/aks/) , kterou jste připravili k nasazení v rámci vašeho předplatného.

Monitorování clusteru AKS můžete povolit pomocí jedné z podporovaných metod:

* Azure CLI
* Terraform

## <a name="enable-using-azure-cli"></a>Povolení s využitím rozhraní příkazového řádku Azure

Pokud chcete povolit monitorování nového clusteru AKS vytvořeného pomocí Azure CLI, postupujte podle kroků v článku rychlý Start v části [Vytvoření clusteru AKS](../../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Pokud se rozhodnete používat rozhraní příkazového řádku Azure, musíte nejprve nainstalovat a používat rozhraní příkazového řádku místně. Musíte používat Azure CLI verze 2.0.74 nebo novější. Pro identifikaci vaší verze spusťte `az --version`. Pokud potřebujete nainstalovat nebo upgradovat rozhraní příkazového řádku Azure CLI, přečtěte si téma [instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). Pokud máte nainstalovanou verzi rozšíření AKS-Preview CLI 0.4.12 nebo vyšší, odeberte všechny provedené změny, aby bylo možné povolit rozšíření verze Preview, protože funkce AKS Preview nejsou dostupné v cloudu Azure US governmnet.

## <a name="enable-using-terraform"></a>Povolení s využitím Terraformu

Pokud [nasazujete nový cluster AKS pomocí terraformu](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md), zadáte argumenty požadované v profilu [k vytvoření pracovního prostoru Log Analytics](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html) , pokud se nerozhodnete zadat existující. 

>[!NOTE]
>Pokud zvolíte použití Terraformu, musí běžet Terraformu pro Azure RM poskytovatele verze 1.17.0 nebo vyšší.

Chcete-li přidat Azure Monitor pro kontejnery do pracovního prostoru, přečtěte si téma [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) a dokončete profil zahrnutím [**addon_profile**](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) a zadáním **oms_agent**. 

Poté, co jste povolili monitorování a úspěšném dokončení všech konfiguračních úloh, můžete sledovat výkon cluster v některém ze dvou způsobů:

* Přímo v clusteru AKS výběrem možnosti **stav** v levém podokně.
* Výběrem dlaždice **monitorovat službu Container Insights** na stránce clusteru AKS pro vybraný cluster. V Azure Monitor v levém podokně vyberte **stav**. 

  ![Možnosti pro výběr monitorování Azure pro kontejnery ve službě AKS](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

Po povolení sledování, může trvat přibližně 15 minut, než se zobrazí stav metriky pro cluster. 

## <a name="verify-agent-and-solution-deployment"></a>Ověření nasazení agenta a řešení
S agentem verze *06072018* nebo novější můžete ověřit, že se agent i řešení úspěšně nasadily. V předchozích verzích agenta můžete ověřit pouze nasazení agenta.

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

Pokud chcete ověřit, jestli je verze agenta Log Analytics vydaná před správným nasazením *06072018* , spusťte následující příkaz:  

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
Pomocí příkazu `aks show` získat podrobnosti, jako je řešení povoleno nebo ne, co je Log Analytics pracovní prostor resourceID a souhrnné podrobnosti o clusteru.  

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

## <a name="next-steps"></a>Další kroky

* Pokud při pokusu o připojení řešení dochází k problémům, přečtěte si [příručku k odstraňování potíží](container-insights-troubleshoot.md) .

* Díky monitorování s povoleným shromažďováním informací o stavu a využití prostředků v clusteru AKS a úlohách, které se na nich běží, se naučíte, [Jak používat](container-insights-analyze.md) Azure monitor pro kontejnery.
