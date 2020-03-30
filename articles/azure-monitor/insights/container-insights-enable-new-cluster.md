---
title: Monitorování nového clusteru služby Azure Kubernetes Service (AKS) | Dokumenty společnosti Microsoft
description: Zjistěte, jak povolit monitorování pro nový cluster služby Azure Kubernetes Service (AKS) s předplatným Azure Monitor for containers.
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: c731826f2780c45358730f9ce20d6a6151f6f259
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275435"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>Povolení monitorování nového clusteru služby Azure Kubernetes Service (AKS)

Tento článek popisuje, jak nastavit Azure Monitor pro kontejnery pro monitorování spravovaného clusteru Kubernetes hostovaného ve [službě Azure Kubernetes,](https://docs.microsoft.com/azure/aks/) kterou připravujete k nasazení ve vašem předplatném.

Monitorování clusteru AKS můžete povolit pomocí jedné z podporovaných metod:

* Azure CLI
* Terraform

## <a name="enable-using-azure-cli"></a>Povolení používání azure cli

Chcete-li povolit monitorování nového clusteru AKS vytvořeného pomocí příkazového příkazu k řešení Azure, postupujte podle kroku v článku o rychlém startu v části [Vytvoření clusteru AKS](../../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Pokud se rozhodnete použít azure cli, musíte nejprve nainstalovat a použít příkazového příkazového příkazu místně. Musíte spouštět Azure CLI verze 2.0.74 nebo novější. Chcete-li identifikovat `az --version`verzi, spusťte aplikaci . Pokud potřebujete nainstalovat nebo upgradovat vázačitelné příkazy k Webu Azure, přečtěte si informace [o instalaci příkazového příkazového příkazu k azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Pokud jste nainstalovali rozšíření aks-preview CLI verze 0.4.12 nebo vyšší, odeberte všechny změny, které jste provedli k povolení rozšíření náhledu, protože můžete přepsat výchozí chování rozhraní příkazového příkazu Azure, protože funkce AKS Preview nejsou k dispozici v cloudu Azure US Governmnet.

## <a name="enable-using-terraform"></a>Povolit pomocí Terraform

Pokud [nasazujete nový cluster AKS pomocí Terraform](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md), zadejte argumenty požadované v profilu [k vytvoření pracovního prostoru Log Analytics,](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html) pokud jste se nerozhodli zadat existující. 

>[!NOTE]
>Pokud se rozhodnete používat Terraform, musíte spustit Terraform Azure RM Provider verze 1.17.0 nebo vyšší.

Pokud chcete přidat Azure Monitor pro kontejnery do pracovního prostoru, najdete v tématu [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) a dokončete profil zahrnutím [**addon_profile**](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) a zadejte **oms_agent**. 

Po povolení monitorování a úspěšném dokončení všech konfiguračních úloh můžete sledovat výkon clusteru dvěma způsoby:

* Přímo v clusteru AKS výběrem **stavu** v levém podokně.
* Výběrem dlaždice **Přehledy kontejnerů monitorování** na stránce clusteru AKS pro vybraný cluster. V Azure Monitoru v levém podokně vyberte **Stav**. 

  ![Možnosti výběru Azure Monitoru pro kontejnery v AKS](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

Po povolení monitorování může trvat přibližně 15 minut, než budete moci zobrazit metriky stavu pro cluster. 

## <a name="verify-agent-and-solution-deployment"></a>Ověření nasazení agenta a řešení
S verzí *agenta 06072018* nebo novější, můžete ověřit, že agent i řešení byly úspěšně nasazeny. S dřívějšími verzemi agenta můžete ověřit pouze nasazení agenta.

### <a name="agent-version-06072018-or-later"></a>Agent verze 06072018 nebo novější
Spusťte následující příkaz a ověřte, zda je agent úspěšně nasazen. 

```
kubectl get ds omsagent --namespace=kube-system
```

Výstup by se měl podobat následujícímu, což znamená, že byl správně nasazen:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Chcete-li ověřit nasazení řešení, spusťte následující příkaz:

```
kubectl get deployment omsagent-rs -n=kube-system
```

Výstup by se měl podobat následujícímu, což znamená, že byl správně nasazen:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Verze agenta starší než 06072018

Chcete-li ověřit, zda je verze agenta Analýzy protokolů vydaná před správnou nasazením *06072018,* spusťte následující příkaz:  

```
kubectl get ds omsagent --namespace=kube-system
```

Výstup by se měl podobat následujícímu, což znamená, že byl správně nasazen:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Zobrazit konfiguraci pomocí cli
Pomocí `aks show` příkazu můžete získat podrobnosti, jako je například povoleno nebo ne, co je ID prostředků pracovního prostoru Analýzy protokolů a souhrnné podrobnosti o clusteru.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Po několika minutách příkaz dokončí a vrátí informace o řešení ve formátu JSON.  Výsledky příkazu by měly zobrazovat profil doplňku monitorování a podobat se následujícímu ukázkovému výstupu:

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

* Pokud při pokusu o připojení k řešení narazíte na problémy, přečtěte si [průvodce odstraňováním potíží](container-insights-troubleshoot.md)

* Díky monitorování, které umožňuje shromažďovat využití stavu a prostředků vašeho clusteru AKS a úloh, které na nich běží, najdete informace o [tom, jak používat](container-insights-analyze.md) Azure Monitor pro kontejnery.
