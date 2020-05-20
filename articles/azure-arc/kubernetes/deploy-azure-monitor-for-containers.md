---
title: Zprovoznění Azure ARC s Azure Monitor for Containers (Preview)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Zprovoznění Azure ARC s Azure Monitor for Containers
keywords: Kubernetes, oblouk, Azure, K8s, Containers
ms.openlocfilehash: 3e1ea96a9241211b25a4e5b356723290fa647412
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680747"
---
# <a name="onboard-azure-monitor-for-containers-with-arc-preview"></a>Připojení Azure Monitor kontejnerů s obloukem (Náhled)

Připojení [kontejnerů](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview) Kubernetes do clusterů Azure ARC s povolenými kontejnery. Azure monitor

## <a name="before-you-begin"></a>Před zahájením

* [Verze Kubernetes](https://docs.microsoft.com/azure/aks/supported-kubernetes-versions)
* Linux distribuce pro uzly clusteru (hlavní & pracovního procesu) – Ubuntu (18,04 LTS a 16,04 LTS)
* Minimální oprávnění role RBAC přispěvatele v předplatném Azure s povoleným clusterem Kubernetes Azure ARC
* Plně kvalifikované ID prostředku Azure pro cluster Kubernetes s povoleným ARC Azure
* Kontext Kubeconfig clusteru Kubernetes
* Agent monitorování vyžaduje, aby cAdvisor na Kubelet běžel buď na zabezpečeném portu: 10250 nebo nezabezpečeném portu: 10255 na všech uzlech, které vyžádají metriky výkonu.   
* Doporučuje se nakonfigurovat port Kubelet cAdvisor na zabezpečený port: 10250.
* Agent monitorování vyžaduje následující Odchozí porty a domény k odeslání dat monitorování do Azure Monitor back-endu (Pokud blokován proxy/firewall).
    -  *. ods.opinsights.azure.com 443
    -  *. oms.opinsights.azure.com 443
    -  *. blob.core.windows.net 443
    -  dc.services.visualstudio.com 443

## <a name="onboarding"></a>Onboarding

### <a name="using-helm-chart"></a>Použití grafu HELM

### <a name="option-1-using-powershell--script"></a>Možnost 1: použití skriptu prostředí PowerShell

1. Stažení skriptu pro registraci

    ```console
    curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/haiku/onboarding_azuremonitor_for_containers.ps1
     ```

2. Nainstalujte [PowerShell Core](https://docs.microsoft.com/PowerShell/scripting/install/installing-PowerShell?view=PowerShell-6) do vývojového počítače, abyste mohli spustit skript PowerShellu pro zprovoznění.

3. Přihlášení k Azure

    ```console
    az login --use-device-code
    ```

4. Spustit pod skriptem cluster Azure ARC K8s s clusterem a kontextem clusteru Kubernetes

    ```console
    .\onboarding_azuremonitor_for_containers.ps1 -azureArcClusterResourceId <resourcedIdOfAzureArcCluster> -kubeContext <kube-context>

    For Example ..
    .\onboarding_azuremonitor_for_containers.ps1 -azureArcClusterResourceId /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1 -kubeContext MyK8sTestCluster
     ```

### <a name="option-2-using-bash-script"></a>Možnost 2: použití skriptu bash

> **Tip:** Tento skript používá funkce bash 4, takže se ujistěte, že je vaše bash aktuální. Aktuální verzi můžete ověřit pomocí `bash --version` .

1. Stažení skriptu pro registraci

    ```console
    curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/haiku/onboarding_azuremonitor_for_containers.sh
     ```

2. Spustit pod skriptem cluster Azure ARC K8s s clusterem a kontextem clusteru Kubernetes

    ```console
    bash onboarding_azuremonitor_for_containers.sh <resourcedIdOfAzureArcCluster>  <kube-context>

    For Example:
    bash onboarding_azuremonitor_for_containers.sh /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1 MyK8sTestCluster

     ```

## <a name="configure-agent-data-collection"></a>Konfigurace shromažďování dat agenta

Ve výchozím nastavení agent neshromažďuje v oboru názvů Kube-System protokoly stdout a stderr kontejnerů.
Chcete https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-agent-config -li nakonfigurovat agenta s požadovaným nastavením shromažďování dat, přečtěte si téma.

## <a name="configure-scraping-of-prometheus-metrics"></a>Konfigurace likvidace metrik Prometheus

Azure Monitor pro kontejnery vyřadí metriky Prometheus a ingestuje do Azure Monitor back-endu.
https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-prometheus-integrationPokyny ke konfiguraci likvidace Prometheus najdete v tématu.

## <a name="user-interface"></a>Uživatelské rozhraní

Přejděte na adresu https://aka.ms/azmon-containers-azurearc a zobrazte cluster s integrovaným připojením.

## <a name="disable-monitoring"></a>Zakázat monitorování

Pokud chcete monitorování z nějakého důvodu zakázat, stačí jednoduše odstranit Azure Monitor graf kontejnerů HELM, abyste zastavili shromažďování a příjem dat monitorování do Azure Monitor pro back-end kontejnery.

    ```console
    helm del azmon-containers-release-1
    ```

## <a name="next-steps"></a>Další kroky

* [Řízení konfigurace clusteru pomocí Azure Policy](./use-azure-policy.md)

