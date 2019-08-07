---
title: Azure Monitor pro kontejnery často kladené dotazy | Microsoft Docs
description: Azure Monitor for Containers je řešení, které monitoruje stav clusterů AKS a Container Instances v Azure. Tento článek obsahuje odpovědi na běžné dotazy.
services: azure-monitor
author: mgoedtel
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/02/2019
ms.author: magoedte
ms.openlocfilehash: 3644b40311c037df800eb89ca26d1285fbf1e082
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2019
ms.locfileid: "68741505"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Azure Monitor pro kontejnery často kladené dotazy

Toto je seznam nejčastějších dotazů týkajících se Azure Monitor kontejnerů. Pokud máte další dotazy týkající se řešení, navštivte [diskuzní fórum](https://feedback.azure.com/forums/34192--general-feedback) a publikujte své dotazy. Pokud je dotaz pokládán často, přidáme ji k tomuto článku tak, aby jej lze rychle a snadno najít.

## <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>Můžu monitorovat cluster AKS s Azure Monitor pro kontejnery?

Azure Monitor for Containers podporuje monitorování úloh kontejneru nasazených do AKS (dřív označovaných jako ACS-Engine) clusterů hostovaných v Azure. Další podrobnosti a přehled kroků požadovaných k povolení monitorování pro tento scénář najdete v tématu [použití Azure monitor pro kontejnery pro AKS modul](https://github.com/microsoft/OMS-docker/tree/aks-engine).

## <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Proč se mi nezobrazují data v pracovním prostoru Log Analytics?

Pokud v pracovním prostoru Log Analytics v průběhu času nevidíte žádná data, možná jste dosáhli výchozího limitu 500 MB nebo denního limitu pro kontrolu množství dat, která se mají shromažďovat denně. Pokud je limit splněn pro daný den, shromažďování dat se zastaví a obnoví pouze následující den. Pokud chcete zkontrolovat využití dat a aktualizovat ji na jinou cenovou úroveň v závislosti na předpokládaných vzorcích použití, přečtěte si téma [využití dat protokolu a náklady](../platform/manage-cost-storage.md). 

## <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Jaké jsou stavy kontejneru zadané v tabulce ContainerInventory?

Tabulka ContainerInventory obsahuje informace o zastavených a spuštěných kontejnerech. Tabulka je vyplněna pracovním postupem v rámci agenta, který se dotáže do Docker pro všechny kontejnery (spuštěné a zastaveno) a předává tato data Log Analytics pracovním prostoru.
 
## <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Návody vyřešit chybu **registrace chybějícího** předplatného?

Pokud se zobrazí chyba **chybějící registrace odběru pro Microsoft. OperationsManagement**, můžete ji vyřešit registrací poskytovatele prostředků **Microsoft. OperationsManagement** v předplatném, ve kterém je tento pracovní prostor definovaný. Dokumentaci k tomu, jak to udělat, najdete [tady](../../azure-resource-manager/resource-manager-register-provider-errors.md).

## <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>Podporuje AKS clustery s podporou RBAC?

Řešení pro monitorování kontejnerů nepodporuje RBAC, ale podporuje se Azure Monitor pro kontejnery. Na stránce s podrobnostmi řešení se nemusí zobrazovat správné informace v oknech, které zobrazují data pro tyto clustery.

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Návody Povolit shromažďování protokolů pro kontejnery v oboru názvů Kube-System prostřednictvím Helm?

Kolekce protokolů z kontejnerů v oboru názvů Kube-System je ve výchozím nastavení zakázaná. Shromažďování protokolů lze povolit nastavením proměnné prostředí v omsagent. Další informace najdete na stránce [Azure monitor pro kontejnery](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) na webu GitHub. 

## <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Návody aktualizovat omsagent na nejnovější vydanou verzi?

Informace o tom, jak upgradovat agenta, najdete v tématu [Správa agentů](container-insights-manage-agent.md).

## <a name="how-do-i-enable-multi-line-logging"></a>Návody povolit víceřádkové protokolování?

V současné době Azure Monitor pro kontejnery nepodporuje víceřádkové protokolování, ale k dispozici jsou alternativní řešení. Můžete nakonfigurovat všechny služby pro zápis ve formátu JSON a potom Docker/Moby je zapíše jako jeden řádek.

Můžete například zabalit protokol jako objekt JSON, jak je znázorněno v následujícím příkladu pro ukázkovou aplikaci Node. js:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Tato data budou vypadat jako v následujícím příkladu v Azure Monitor pro protokoly při dotazování na ni:

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Podrobný přehled tohoto problému najdete v následujícím [odkazu na GitHub](https://github.com/moby/moby/issues/22920).

## <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Návody vyřešit chyby Azure AD, když povolíte živé protokoly? 

Může se zobrazit následující chyba: **Adresa URL odpovědi zadaná v požadavku neodpovídá adresám URL odpovědí nakonfigurovaným pro aplikaci: ' < ID\>aplikace '** . Řešení, které se má vyřešit, najdete v článku [jak zobrazit protokoly kontejneru v reálném čase s Azure monitor pro kontejnery](container-insights-live-logs.md#configure-aks-with-azure-active-directory). 

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Proč není možné upgradovat cluster po registraci?

Pokud po povolení Azure Monitor pro kontejnery pro cluster AKS odstraníte pracovní prostor Log Analytics, který cluster odeslal do, při pokusu o upgrade clusteru se nezdaří. Pokud chcete tento problém obejít, budete muset zakázat monitorování a pak ho znovu povolit odkazování na jiný platný pracovní prostor v rámci vašeho předplatného. Pokud se pokusíte upgradovat cluster znovu, měl by se zpracovat a dokončit úspěšně.  

## <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Které porty a domény potřebuji k otevření nebo seznamu povolených pro agenta?
- *.ods.opinsights.azure.com   443
- *.oms.opinsights.azure.com   443
- *.blob.core.windows.net      443
- dc.services.visualstudio.com 443

## <a name="next-steps"></a>Další postup

Chcete-li zahájit monitorování clusteru AKS, přečtěte si téma [Azure připojit jak monitorovat pro kontejnery](container-insights-onboard.md) vám pomohou pochopit požadavky a dostupné metody, které chcete povolit monitorování. 
