---
title: Integrace Azure Monitor pro Azure Red Hat OpenShift 4,3
description: Naučte se, jak povolit Azure Monitor v clusteru Microsoft Azure Red Hat OpenShift.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
ms.openlocfilehash: a784fc070400995c56d16a3bc264d589bcb1f64e
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082842"
---
# <a name="azure-monitor-integration-for-azure-red-hat-openshift-43"></a>Integrace Azure Monitor pro Azure Red Hat OpenShift 4,3

> [!IMPORTANT] 
> Upozorňujeme, že Azure Red Hat OpenShift 4,3 je teď k dispozici jenom v privátní verzi Preview v Východní USA. Přijetí privátní verze Preview je jenom na pozvání. Před pokusem o povolení této funkce nezapomeňte zaregistrovat své předplatné: [registrace Azure Red Hat OpenShift Private Preview](https://aka.ms/aro-preview-register)

> [!NOTE]
> Funkce ve verzi Preview jsou samoobslužné a jsou k dispozici, jak jsou dostupné a jsou vyloučené z smlouvy o úrovni služeb (SLA) a omezené záruky. Proto funkce nejsou určeny pro použití v produkčním prostředí.

Tento článek popisuje, jak povolit privátní verzi Preview Azure Monitor pro kontejnery pro clustery s OpenShift 4,3 hostované na Prem nebo v jakémkoli cloudovém prostředí. Stejné pokyny platí i pro povolení monitorování clusterů Azure Red Hat OpenShift (ARO) 4,3.  

## <a name="prerequisites"></a>Předpoklady

- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Helm 3](https://helm.sh/docs/intro/install/)
- Přístup k kubeconfig clusteru Kubernetes
- Přístup k předplatnému Azure
- Přístup k clusteru OpenShift 4,3 pro instalaci sady Azure Monitor for Containers Helm Chart
- Minimální oprávnění role RBAC přispěvatele v předplatném Azure  
- Agent monitorování vyžaduje následující Odchozí porty a domény pro odeslání dat monitorování do Azure Monitor back-endu (Pokud blokován proxy/firewall):
  - *. ods.opinsights.azure.com 443
  - *. oms.opinsights.azure.com 443
  - *. blob.core.windows.net 443
  - dc.services.visualstudio.com 443

## <a name="onboarding"></a>Onboarding

> [!TIP]
> Tento skript používá funkce bash 4, takže se ujistěte, že je vaše bash aktuální. Aktuální verzi můžete ověřit pomocí `bash --version`.

### <a name="download-the-onboarding-script"></a>Stažení skriptu pro registraci

```bash
curl -LO  https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/openshiftV4/onboarding_azuremonitor_for_containers.sh
```

Spusťte následující skript s azureSubscriptionId, oblastí pracovního prostoru, clusterem a kontextem clusteru Kubernetes.

```bash
bash onboarding_azuremonitor_for_containers.sh <azureSubscriptionId> <azureRegionforLogAnalyticsWorkspace> <clusterName> <kubeconfigContextNameOftheCluster>
```

Příklad:

```bash
 bash onboarding_azuremonitor_for_containers.sh 27ac26cf-a9f0-4908-b300-9a4e9a0fb205 eastus myocp42 admin 
```

## <a name="configure-agent-data-collection"></a>Konfigurace shromažďování dat agenta

Ve výchozím nastavení agent monitorování shromažďuje protokoly kontejnerů {stdout; stderr} všech kontejnerů spuštěných ve všech oborech názvů kromě Kube-System.  Pokud chcete nakonfigurovat kolekci protokolů kontejnerů specificky pro konkrétní obory názvů nebo obory názvů, můžete se podívat na téma [Konfigurace agenta kontejneru Insights](../azure-monitor/insights/container-insights-agent-config.md). Tady můžete nakonfigurovat agenta monitorování s požadovaným nastavením shromažďování dat pomocí mapování konfigurace.

## <a name="configure-scraping-of-prometheus-metrics"></a>Konfigurace likvidace metrik Prometheus

Azure Monitor pro kontejnery vyřadí metriky Prometheus a ingestuje do Azure Monitor back-endu. Pokyny ke konfiguraci likvidace Prometheus najdete v tématu [Konfigurace Prometheus pro Container Insights](../azure-monitor/insights/container-insights-prometheus-integration.md) .

Po úspěšné registraci přejděte na [hybridní monitorování](https://aka.ms/azmon-containers-hybrid) a vyberte prostředí jako **"vše"** , abyste si zobrazili svůj nově integrovaný cluster OpenShift v4.

## <a name="disable-monitoring"></a>Zákaz monitorování

Chcete-li zakázat monitorování, můžete odstranit graf Azure Monitor for Containers Helm pomocí následujícího příkazu pro zastavení shromažďování a ingestování dat monitorování do Azure Monitor pro back-end kontejnery.

``` bash
helm del azmon-containers-release-1
```

## <a name="update-monitoring"></a>Aktualizovat monitorování

Spusťte skript připojování znovu, jak je popsáno v části věnované [registraci](#onboarding) , se stejným parametrem, který se má aktualizovat na nejnovější Helm graf.

## <a name="after-successful-onboarding"></a>Po úspěšné registraci

Přejděte do [hybridního monitorování](https://aka.ms/azmon-containers-hybrid)a na kartě **monitorované clustery** si můžete prohlédnout nově aktivovaný cluster OpenShift/ARO verze v4 se stavem stavu. V takovém případě můžete získat hlubší přehledy, jako jsou metriky, inventář a protokoly, kliknutím na sloupec **clusteru** .

## <a name="supported-features"></a>Podporované funkce

Další informace o podporovaných funkcích a funkcích najdete v tématu [Přehled služby Container Insights](../azure-monitor/insights/container-insights-overview.md).

Kontaktujte nás prostřednictvím askcoin@microsoft.com, kde můžete získat zpětnou vazbu a otázky.

## <a name="next-steps"></a>Další kroky

Další informace o monitorování najdete tady:
- [Přehled kontejneru Insights](../azure-monitor/insights/container-insights-overview.md)

- [Přehled dotazů protokolu](../azure-monitor/log-query/log-query-overview.md)
