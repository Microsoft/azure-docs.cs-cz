---
title: Integrace Azure Monitoru pro Azure Red Hat OpenShift 4.3
description: Přečtěte si, jak povolit Azure Monitor v clusteru Microsoft Azure Red Hat OpenShift.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
ms.openlocfilehash: a784fc070400995c56d16a3bc264d589bcb1f64e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082842"
---
# <a name="azure-monitor-integration-for-azure-red-hat-openshift-43"></a>Integrace Azure Monitoru pro Azure Red Hat OpenShift 4.3

> [!IMPORTANT] 
> Upozorňujeme, že Azure Red Hat OpenShift 4.3 je momentálně k dispozici jenom v privátní verzi Preview v USA – východ. Přijetí soukromé verze preview je pouze na pozvání. Než se pokusíte povolit tuto funkci: [Azure Red Hat OpenShift Private Preview Registration,](https://aka.ms/aro-preview-register) nezapomeňte si zaregistrovat předplatné.

> [!NOTE]
> Funkce náhledu jsou samoobslužné a jsou poskytovány tak, jak jsou k dispozici a jsou vyloučeny ze smlouvy o úrovni služeb (SLA) a omezené záruky. Proto funkce nejsou určeny pro produkční použití.

Tento článek popisuje, jak povolit privátní náhled Azure Monitor pro kontejnery pro clustery OpenShift 4.3 hostované na prem nebo v jakémkoli cloudovém prostředí. Stejné pokyny platí také k povolení monitorování clusterů Azure Red Hat OpenShift (ARO) 4.3.  

## <a name="prerequisites"></a>Požadavky

- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Kormidlo 3](https://helm.sh/docs/intro/install/)
- Přístup k kubeconfig clusteru kubernetes
- Přístup k předplatnému Azure
- Přístup ke clusteru OpenShift 4.3 k instalaci grafu Azure Monitor for Containers Helm
- Oprávnění role RBAC minimálního přispěvatele u předplatného Azure  
- Agent monitorování vyžaduje následující odchozí porty – a domény k odeslání dat monitorování do back-endu Azure Monitoru (pokud je blokován proxy nebo bránou firewall):
  - *.ods.opinsights.azure.com 443
  - *.oms.opinsights.azure.com 443
  - *.blob.core.windows.net 443
  - dc.services.visualstudio.com 443

## <a name="onboarding"></a>Onboarding

> [!TIP]
> Skript používá funkce bash 4, takže se ujistěte, že váš bash je aktuální. Aktuální verzi můžete zkontrolovat `bash --version`pomocí aplikace .

### <a name="download-the-onboarding-script"></a>Stažení onboardingového skriptu

```bash
curl -LO  https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/openshiftV4/onboarding_azuremonitor_for_containers.sh
```

Spusťte následující skript s azureSubscriptionId, oblast pracovního prostoru, název clusteru a kontextu clusteru Kubernetes.

```bash
bash onboarding_azuremonitor_for_containers.sh <azureSubscriptionId> <azureRegionforLogAnalyticsWorkspace> <clusterName> <kubeconfigContextNameOftheCluster>
```

Například:

```bash
 bash onboarding_azuremonitor_for_containers.sh 27ac26cf-a9f0-4908-b300-9a4e9a0fb205 eastus myocp42 admin 
```

## <a name="configure-agent-data-collection"></a>Konfigurace shromažďování dat agenta

Ve výchozím nastavení monitorování agent shromažďuje {stdout; stderr} kontejnerprotokoly všech kontejnerů spuštěných ve všech oborech názvů s výjimkou kube-system.  Pokud chcete nakonfigurovat kolekci protokolu kontejneru specifickou pro konkrétní obor názvů nebo obory názvů, můžete odkazovat na [konfiguraci agenta Container Insights](../azure-monitor/insights/container-insights-agent-config.md). Zde můžete nakonfigurovat agenta monitorování s požadovaným nastavením shromažďování dat pomocí konfigurační mapy.

## <a name="configure-scraping-of-prometheus-metrics"></a>Konfigurace škrábání metrik Prometheus

Azure Monitor pro kontejnery seškrábne metriky Prometheus a ingestuje do back-endu Azure Monitor. Pokyny ke konfiguraci prometheus naleznete v [části Kontejner Insights Prometheus.](../azure-monitor/insights/container-insights-prometheus-integration.md)

Po úspěšném zapnutí přejděte do [hybridního monitorování](https://aka.ms/azmon-containers-hybrid) a vyberte možnost Prostředí jako **"Vše", chcete-li** zobrazit nově založený cluster OpenShift v4.

## <a name="disable-monitoring"></a>Zákaz monitorování

Pokud chcete zakázat monitorování, můžete odstranit Azure Monitor pro kontejnery Helm graf pomocí následujícího příkazu zastavit shromažďování a ingestování dat monitorování do Azure Monitor pro kontejnery back-endu.

``` bash
helm del azmon-containers-release-1
```

## <a name="update-monitoring"></a>Sledování aktualizací

Znovu spusťte skript registrace, jak je popsáno v části [Onboarding](#onboarding) se stejným parametrem, abyste aktualizovali na nejnovější graf Helm.

## <a name="after-successful-onboarding"></a>Po úspěšném nástupu

Přejděte na [hybridní monitorování](https://aka.ms/azmon-containers-hybrid)a nově povolený cluster OpenShift/ARO v4 se stavem na kartě **Sledované clustery.** Kliknutím na sloupec **Cluster** se zde můžete dostat k hlubším přehledům, jako jsou metriky, inventář a protokoly.

## <a name="supported-features"></a>Podporované funkce

Další informace o podporovaných funkcích a funkcích najdete v [tématu Přehled přehledu přehledu přehledů kontejnerů](../azure-monitor/insights/container-insights-overview.md).

Kontaktujte askcoin@microsoft.com nás prostřednictvím zpětné vazby a dotazy.

## <a name="next-steps"></a>Další kroky

Další informace o monitorování najdete v tématu:
- [Přehledy kontejnerů – přehled](../azure-monitor/insights/container-insights-overview.md)

- [Protokolovat dotaz – přehled](../azure-monitor/log-query/log-query-overview.md)
