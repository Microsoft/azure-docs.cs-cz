---
title: Azure Monitor pro kontejnery – nejčastější dotazy | Dokumentace Microsoftu
description: Azure Monitor pro kontejnery je řešení, které monitoruje stav clusteru AKS a v Azure Container Instances. Tento článek obsahuje odpovědi na běžné dotazy.
services: azure-monitor
author: mgoedtel
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: magoedte
ms.openlocfilehash: afa332b40884a79b5114b3b8093cd27108c39984
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65780007"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Azure Monitor pro kontejnery – nejčastější dotazy

Tato FAQ Microsoft je uveden seznam nejčastější dotazy týkající se Azure Monitor pro kontejnery. Pokud máte nějaké další dotazy ohledně řešení, přejděte [diskusní fórum](https://feedback.azure.com/forums/34192--general-feedback) a zveřejněte své dotazy. Pokud je dotaz pokládán často, přidáme ji k tomuto článku tak, aby jej lze rychle a snadno najít.

## <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Proč nevidím ve svém pracovním prostoru Log Analytics data?

Pokud chcete zobrazit všechna data v pracovním prostoru Log Analytics na některé běžné čas nemůžete, pravděpodobně bylo dosaženo výchozí limit 500 MB nebo k řízení množství dat pro denní shromažďování zadat denní limit. Při splnění limit pro denní shromažďování dat zastaví a bude pokračovat pouze za další den. Zkontrolujte využití dat a aktualizovat na jinou cenovou úroveň na základě způsobů váš předpokládaný využití najdete v tématu [protokolovat data využití a nákladů](../platform/manage-cost-storage.md). 

## <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Co jsou uvedená v tabulce ContainerInventory stavy kontejneru?

ContainerInventory tabulka obsahuje informace o zastavena a spuštěné kontejnery. V tabulce je vyplněn pracovního postupu uvnitř agenta, který dotazuje dockeru pro všechny kontejnery (spuštění a zastavení) a předá data pracovního prostoru Log Analytics.
 
## <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Jak vyřešit **registrace předplatného chybí** chyba?

Pokud se zobrazí chyba **registrace předplatného chybí pro Microsoft.OperationsManagement**, ho mohli vyřešit pomocí registrace poskytovatele prostředků **Microsoft.OperationsManagement** v předplatné, ve kterém je definována pracovní prostor. Najdete v dokumentaci k tom, jak to udělat [tady](../../azure-resource-manager/resource-manager-register-provider-errors.md).

## <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>Je k dispozici podpora pro RBAC povolené AKS clustery?

Řešení pro monitorování kontejnerů nepodporuje RBAC, ale je podporované službou Azure Monitor for Containers. Stránce s podrobnostmi řešení nemusí zobrazit správné informace v oknech, které zobrazují data pro tyto clustery.

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Jak povolit shromažďování protokolů pro kontejnery v oboru názvů kube-system prostřednictvím Helm?

Shromažďování protokolů z kontejnerů v oboru názvů kube-system je ve výchozím nastavení zakázána. Shromažďování protokolů lze povolit nastavením proměnné prostředí omsagent. Další informace najdete v tématu [monitorování Azure pro kontejnery](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) stránku na Githubu. 

## <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Jak můžu aktualizovat omsagent na nejnovější vydanou verzi?

Zjistěte, jak upgradovat agenta, najdete v článku [správy agenta](container-insights-manage-agent.md).

## <a name="how-do-i-enable-multi-line-logging"></a>Jak povolit protokolování Víceřádkový?

V současné době monitorování Azure pro kontejnery nepodporuje více řádky protokolování, ale nejsou k dispozici alternativní řešení. Můžete nakonfigurovat všechny služby pro zápis ve formátu JSON a pak Dockeru/Moby bude je zapíše jako jeden řádek.

Můžete například zabalit váš protokol jako objekt JSON, jak je znázorněno v příkladu níže pro ukázkové aplikace v node.js:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Tato data bude vypadat jako v následujícím příkladu ve službě Azure Monitor pro protokoly při dotazování pro něj:

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Podrobný pohled na problém, zkontrolujte následující [odkaz na github](https://github.com/moby/moby/issues/22920).

## <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Jak můžu Povolit živé protokoly vyřešit chyby služby Azure AD? 

Může se zobrazit následující chyba: **Odpověď url zadanou v požadavku se neshoduje s odpovědních adres URL nakonfigurované pro aplikace: ' < ID aplikace\>"** . Řešení k jeho řešení najdete v článku [zobrazení kontejneru protokoly reálném čase pomocí Azure monitoru pro kontejnery](container-insights-live-logs.md#configure-aks-with-azure-active-directory). 

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Proč nemohu upgradovat cluster po připojení?

Když povolíte monitorování Azure pro kontejnery pro AKS cluster, při odstranění pracovního prostoru Log Analytics clusteru posílal data, při pokusu o upgrade clusteru dojde k selhání. Chcete-li tento problém obejít, budete muset zakázat monitorování a pak znovu povolit odkazující na jinou platný pracovní prostor v rámci vašeho předplatného. Při pokusu o provedení upgradu clusteru znovu, měl by zpracovat a úspěšně dokončeno.  

## <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Které porty a domény potřeba otevřít/seznam povolených pro agenta?
- *.ods.opinsights.azure.com   443
- *.oms.opinsights.azure.com   443
- *.blob.core.windows.net      443
- dc.services.visualstudio.com 443

## <a name="next-steps"></a>Další postup

Chcete-li zahájit monitorování clusteru AKS, přečtěte si téma [Azure připojit jak monitorovat pro kontejnery](container-insights-onboard.md) vám pomohou pochopit požadavky a dostupné metody, které chcete povolit monitorování. 
