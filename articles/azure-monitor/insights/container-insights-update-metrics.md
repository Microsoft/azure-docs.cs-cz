---
title: Postup aktualizace Azure Monitor pro kontejnery pro metriky | Microsoft Docs
description: Tento článek popisuje, jak aktualizovat Azure Monitor pro kontejnery pro povolení funkce vlastní metriky, která podporuje prozkoumávání a upozorňování na agregované metriky.
ms.topic: conceptual
ms.date: 09/24/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6c420c91e20cc1cf9ab5e4f58bdd352ead3ba4d0
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91618141"
---
# <a name="how-to-update-azure-monitor-for-containers-to-enable-metrics"></a>Jak aktualizovat službu Azure Monitor pro kontejnery a povolit metriky

Azure Monitor for Containers zavádí podporu pro shromažďování metrik ze služeb Azure Kubernetes Services (AKS) a uzlů Kubernetes clusterů Azure s podporou ARC a jejich zápis do úložiště Azure Monitor metrik. Tato změna má poskytovat vylepšenou časovou osu při vytváření agregačních výpočtů (střední, Count, Max, min, Sum) v grafech výkonu, podpora připnutí grafů výkonu v Azure Portal řídicích panelech a podpora upozornění na metriky.

>[!NOTE]
>Tato funkce v současné době nepodporuje clustery Azure Red Hat OpenShift.
>

V rámci této funkce jsou povoleny následující metriky:

| Obor názvů metriky | Metrika | Popis |
|------------------|--------|-------------|
| Insights. Container/Nodes | cpuUsageMillicores, cpuUsagePercentage, memoryRssBytes, memoryRssPercentage, memoryWorkingSetBytes, memoryWorkingSetPercentage, nodesCount, diskUsedPercentage, | Jako metriky *uzlů* zahrnují *hostitel* jako dimenzi. Zahrnují taky<br> název uzlu jako hodnota pro dimenzi *hostitele* . |
| Přehledy. kontejner/lusky | podCount, completedJobsCount, restartingContainerCount, oomKilledContainerCount, podReadyPercentage | Stejně *jako metriky* obsahují tyto údaje: Dimensions-Controller, Kubernetes Namespace, Name, Phase. |
| Insights. Container/Containers | cpuExceededPercentage, memoryRssExceededPercentage, memoryWorkingSetExceededPercentage | |

V rámci podpory těchto nových funkcí je ve vydané verzi zahrnutý nový agent s podporou kontejnerů, verze **Microsoft/OMS: ciprod05262020** for AKS a verze **Microsoft/OMS: ciprod09252020** pro clustery Kubernetes s povoleným obloukem Azure. Nová nasazení AKS automaticky zahrnují tuto změnu konfigurace a možnosti. Aktualizace clusteru pro podporu této funkce se dá provést z Azure Portal, Azure PowerShell nebo pomocí Azure CLI. Pomocí Azure PowerShell a CLI. Tento cluster můžete povolit pro všechny clustery v rámci vašeho předplatného.

Buď proces přiřadí roli **vydavatele metrik monitorování** k instančnímu objektu clusteru nebo k souboru MSI přiřazenému uživateli pro doplněk monitorování, aby data shromážděná agentem mohla být publikována do vašeho prostředku clusterů. Vydavatel monitorování metrik má oprávnění pouze k odeslání metrik do prostředku, nemůže změnit žádný stav, aktualizovat prostředek ani číst žádná data. Další informace o roli najdete v tématu [monitorování role vydavatele metrik](../../role-based-access-control/built-in-roles.md#monitoring-metrics-publisher). Požadavek role vydavatele monitorování metriky se nevztahuje na clustery Kubernetes s povoleným ARC Azure.

> [!IMPORTANT]
> Upgrade není vyžadován pro clustery Kubernetes s podporou ARC Azure, protože již mají minimální požadovanou verzi agenta.

## <a name="prerequisites"></a>Požadavky

Před aktualizací clusteru potvrďte následující:

* Vlastní metriky jsou dostupné jenom v podmnožině oblastí Azure. [Tady](../platform/metrics-custom-overview.md#supported-regions)je popsán seznam podporovaných oblastí.

* Jste členem role **[vlastníka](../../role-based-access-control/built-in-roles.md#owner)** v prostředku clusteru AKS, aby bylo možné povolit shromažďování uzlů a pod vlastním metriku výkonu. Tento požadavek neplatí pro clustery Kubernetes s povoleným ARC Azure.

Pokud se rozhodnete používat rozhraní příkazového řádku Azure, musíte nejdřív nainstalovat a používat rozhraní příkazového řádku (CLI). Musíte používat Azure CLI verze 2.0.59 nebo novější. Pro identifikaci vaší verze spusťte `az --version` . Pokud potřebujete nainstalovat nebo upgradovat rozhraní příkazového řádku Azure CLI, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="upgrade-a-cluster-from-the-azure-portal"></a>Upgrade clusteru z Azure Portal

V případě existujících AKS clusterů monitorovaných službou Azure Monitor for Containers po výběru clusteru k zobrazení jeho stavu z zobrazení více clusterů v Azure Monitor nebo přímo z clusteru výběrem možnosti **přehledy** v levém podokně by se v horní části portálu měla zobrazit banner.

![Upgradovat banner clusteru AKS v Azure Portal](./media/container-insights-update-metrics/portal-banner-enable-01.png)

Kliknutím na **Povolit** zahájíte proces upgradu clusteru. Dokončení tohoto procesu může trvat několik sekund a průběh můžete sledovat v části oznámení z nabídky.

## <a name="upgrade-all-clusters-using-bash-in-azure-command-shell"></a>Upgrade všech clusterů pomocí bash ve službě Azure Command Shell

Provedením následujících kroků aktualizujte všechny clustery ve vašem předplatném pomocí bash ve službě Azure Command Shell.

1. Spusťte následující příkaz pomocí Azure CLI.  Upravte hodnotu **SubscriptionId** pomocí hodnoty ze stránky **přehledu AKS** pro cluster AKS.

    ```azurecli
    az login
    az account set --subscription "Subscription Name"
    curl -sL https://aka.ms/ci-md-onboard-atscale | bash -s subscriptionId   
    ```

    Dokončení změny konfigurace může trvat několik sekund. Po dokončení se zobrazí zpráva podobná následující zprávě, která obsahuje výsledek:

    ```azurecli
    completed role assignments for all AKS clusters in subscription: <subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-cli"></a>Upgrade na cluster pomocí Azure CLI

Provedením následujících kroků aktualizujete konkrétní cluster v předplatném pomocí Azure CLI.

1. Spusťte následující příkaz pomocí Azure CLI. Upravte hodnoty pro **SubscriptionId**, **resourceGroupName**a **název_clusteru** pomocí hodnot na stránce **Přehled AKS** pro cluster AKS.  Chcete-li získat hodnotu **clientIdOfSPN**, je vrácena při spuštění příkazu, jak je `az aks show` znázorněno v následujícím příkladu.

    ```azurecli
    az login
    az account set --subscription "<subscriptionName>"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPN> --scope <clusterResourceId> --role "Monitoring Metrics Publisher" 
    ```

    Chcete-li získat hodnotu pro **clientIdOfSPNOrMsi**, můžete spustit příkaz, `az aks show` jak je znázorněno v následujícím příkladu. Pokud má objekt **servicePrincipalProfile** platnou hodnotu *ClientID* , můžete jej použít. V opačném případě, pokud je nastaveno na *MSI*, musíte předat ClientID z `addonProfiles.omsagent.identity.clientId` .

    ```azurecli
    az login
    az account set --subscription "<subscriptionName>"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPNOrMsi> --scope <clusterResourceId> --role "Monitoring Metrics Publisher"
    ```

## <a name="upgrade-all-clusters-using-azure-powershell"></a>Upgrade všech clusterů pomocí Azure PowerShell

Provedením následujících kroků aktualizujte všechny clustery v rámci předplatného pomocí Azure PowerShell.

1. [Stáhněte](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/docs/aks/mdmonboarding/mdm_onboarding_atscale.ps1) si skript **mdm_onboarding_atscale.ps1** a uložte ho do místní složky z našeho úložiště GitHub.
2. Spusťte následující příkaz pomocí Azure PowerShell.  Upravte hodnotu **SubscriptionId** pomocí hodnoty ze stránky **přehledu AKS** pro cluster AKS.

    ```powershell
    .\mdm_onboarding_atscale.ps1 subscriptionId
    ```
    Dokončení změny konfigurace může trvat několik sekund. Po dokončení se zobrazí zpráva podobná následující zprávě, která obsahuje výsledek:

    ```powershell
    Completed adding role assignment for the aks clusters in subscriptionId :<subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-powershell"></a>Upgradovat na cluster pomocí Azure PowerShell

Provedením následujících kroků aktualizujte konkrétní cluster pomocí Azure PowerShell.

1. [Stáhněte](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/docs/aks/mdmonboarding/mdm_onboarding.ps1) si skript **mdm_onboarding.ps1** a uložte ho do místní složky z našeho úložiště GitHub.

2. Spusťte následující příkaz pomocí Azure PowerShell. Upravte hodnoty pro **SubscriptionId**, **resourceGroupName**a **název_clusteru** pomocí hodnot na stránce **Přehled AKS** pro cluster AKS.

    ```powershell
    .\mdm_onboarding.ps1 subscriptionId <subscriptionId> resourceGroupName <resourceGroupName> clusterName <clusterName>
    ```

    Dokončení změny konfigurace může trvat několik sekund. Po dokončení se zobrazí zpráva podobná následující zprávě, která obsahuje výsledek:

    ```powershell
    Successfully added Monitoring Metrics Publisher role assignment to cluster : <clusterName>
    ```

## <a name="verify-update"></a>Ověřit aktualizaci

Po zahájení aktualizace pomocí jedné z výše popsaných metod můžete použít Azure Monitor Průzkumníku metrik a ověřit z **oboru názvů metriky** , na které je **Přehled** uvedený. Pokud je, můžete pokračovat a začít nastavovat [výstrahy metriky](../platform/alerts-metric.md) nebo připínat grafy k [řídicím panelům](../../azure-portal/azure-portal-dashboards.md).  
