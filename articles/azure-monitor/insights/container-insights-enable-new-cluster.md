---
title: Monitorování nový cluster Azure Kubernetes Service (AKS) | Dokumentace Microsoftu
description: Zjistěte, jak povolit monitorování pro kontejnery předplatné pro nový cluster Azure Kubernetes Service (AKS) prostřednictvím služby Azure Monitor.
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
ms.date: 04/25/2019
ms.author: magoedte
ms.openlocfilehash: d73ab2d5cca4f20f954a0b0e972111d3f395c3c8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65077527"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>Povolit monitorování nový cluster Azure Kubernetes Service (AKS)

Tento článek popisuje, jak nastavit službu Azure Monitor monitorovat hostitelem spravovaného clusteru Kubernetes pro kontejnery [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/) , který se chystáte nasadit ve vašem předplatném.

Můžete povolit monitorování clusteru AKS pomocí jedné z podporovaných metod:

* Azure CLI
* Terraform

## <a name="enable-using-azure-cli"></a>Povolení s využitím rozhraní příkazového řádku Azure

Chcete-li povolit monitorování pomocí Azure CLI vytvořili nový cluster AKS, postupujte podle kroku v části tohoto článku rychlý Start [clusteru AKS vytvořit](../../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Pokud se rozhodnete používat rozhraní příkazového řádku Azure, musíte nejprve nainstalovat a používat rozhraní příkazového řádku místně. Musíte používat Azure CLI verze 2.0.59 nebo novější. Zjistěte verzi, spusťte `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku Azure, najdete v článku [instalace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 
>

## <a name="enable-using-terraform"></a>Povolení s využitím Terraformu

Pokud jste [nasazení nového clusteru AKS pomocí Terraformu](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md), zadejte argumenty potřebné v profilu [vytvořit pracovní prostor Log Analytics](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html) Pokud není rozhodli zadejte existující. 

>[!NOTE]
>Pokud zvolíte použití Terraformu, musí běžet Terraformu pro Azure RM poskytovatele verze 1.17.0 nebo vyšší.

Přidat Azure monitorování kontejnerů do pracovního prostoru, naleznete v tématu [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) a dokončete profil zahrnutím [ **addon_profile** ](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) a zadat **oms_agent**. 

Poté, co jste povolili monitorování a úspěšném dokončení všech konfiguračních úloh, můžete sledovat výkon cluster v některém ze dvou způsobů:

* Přímo v clusteru AKS tak, že vyberete **stavu** v levém podokně.
* Výběrem **přehledy o kontejnerech monitorování** dlaždice na stránce clusteru AKS pro vybraný cluster. Ve službě Azure Monitor, v levém podokně vyberte **stavu**. 

  ![Možnosti pro výběr monitorování Azure pro kontejnery ve službě AKS](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

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

## <a name="next-steps"></a>Další postup

* Pokud dochází k problémům při pokusu o připojení řešení, přečtěte si [Průvodce odstraňováním potíží](container-insights-troubleshoot.md)

* Pomocí monitorování povoleno zachycení stavu metriky pro uzly clusteru AKS a podů, jsou tyto metriky stavu k dispozici na webu Azure Portal. Naučte se používat Azure Monitor pro kontejnery, najdete v článku [zobrazení Azure Kubernetes Service health](container-insights-analyze.md).
