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
ms.date: 02/18/2019
ms.author: magoedte
ms.openlocfilehash: 27a191bb62ae59aa154167a22c99d3e699f3eb5a
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418539"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Azure Monitor pro kontejnery – nejčastější dotazy
Tato FAQ Microsoft je uveden seznam nejčastější dotazy týkající se Azure Monitor pro kontejnery. Pokud máte nějaké další dotazy ohledně řešení, přejděte [diskusní fórum](https://feedback.azure.com/forums/34192--general-feedback) a zveřejněte své dotazy. Pokud je dotaz pokládán často, přidáme ji k tomuto článku tak, aby jej lze rychle a snadno najít.

## <a name="why-am-i-not-seeing-data-if-log-analytics-workspace-is-configured-with-the-free-pricing-tier"></a>Proč mi zobrazuje data, pokud pracovní prostor Log Analytics je nakonfigurována pomocí cenové úrovně Free? 

Můžete mít byl dosažen limit 500 MB výchozí nebo zadat denní limit k řízení množství dat pro denní shromažďování. Zkontrolujte a správou vašeho využití dat najdete v tématu [protokolovat data využití a nákladů](../platform/manage-cost-storage.md). 

## <a name="what-are-the-states-of-containers-specified-in-the-containerinventory-table"></a>Co jsou stavy kontejnery uvedená v tabulce ContainerInventory?
ContainerInventory tabulka obsahuje informace o zastavena a spuštěné kontejnery. V tabulce je vyplněn pracovního postupu uvnitř agenta, který dotazuje dockeru pro všechny kontejnery (spuštění a zastavení) a předá data pracovního prostoru Log Analytics.
 
## <a name="how-do-i-resolve-errors-related-to-missing-subscription-registration-for-microsoftoperationsmanagement"></a>Jak vyřeším chyby související s **registrace předplatného chybí pro Microsoft.OperationsManagement**?
Chcete-li vyřešit chybu, zaregistrujte poskytovatele prostředků **Microsoft.OperationsManagement** v rámci předplatného, ve kterém je definována pracovní prostor. Najdete v dokumentaci k tom, jak to udělat [tady](../../azure-resource-manager/resource-manager-register-provider-errors.md).

## <a name="does-azure-monitor-for-containers-include-support-for-rbac-enabled-aks-clusters"></a>Zahrnuje monitorování Azure pro kontejnery podporu pro RBAC povolené AKS clustery?
RBAC povolené AKS clustery nejsou aktuálně podporovány řešením. Stránce s podrobnostmi řešení nemusí zobrazit správné informace v oknech, které zobrazují data pro tyto clustery.

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Jak povolit shromažďování protokolů pro kontejnery v oboru názvů kube-system prostřednictvím Helm?
Shromažďování protokolů z kontejnerů v oboru názvů kube-system je ve výchozím nastavení zakázána. Shromažďování protokolů lze povolit nastavením proměnné prostředí omsagent. Další informace najdete v článku [monitorování Azure pro kontejnery](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) stránku na Githubu. 

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

Tato data bude vypadat jako následující ve službě Azure Monitor pro protokoly při dotazování pro něj:

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Podrobný pohled na problém, zkontrolujte následující [odkaz na github](https://github.com/moby/moby/issues/22920).

## <a name="how-do-i-resolve-azure-active-directory-errors-when-i-enable-live-logs"></a>Jak můžu Povolit živé protokoly vyřešit chyby služby Azure Active Directory? 
Může se zobrazit následující chyba: **Odpověď url zadanou v požadavku se neshoduje s odpovědních adres URL nakonfigurované pro aplikaci: "60b4dec7-5a69-4165-a211-12c40b5c0435"**. Oprava najdete v článku [zobrazení kontejneru protokoly reálném čase pomocí Azure monitoru pro kontejnery](container-insights-live-logs.md#configure-aks-with-azure-active-directory). 

## <a name="next-steps"></a>Další postup
Chcete-li zahájit monitorování clusteru AKS, přečtěte si téma [Azure připojit jak monitorovat pro kontejnery](container-insights-onboard.md) vám pomohou pochopit požadavky a dostupné metody, které chcete povolit monitorování. 