---
title: Azure Monitor – monitorování kubernetes hostované aplikace nulové instrumentace aplikací | Dokumentace Microsoftu
description: Monitorování pro Kubernetes hostované aplikace nulové instrumentace aplikací je řešení pro monitorování, která umožňuje shromažďovat telemetrii Application Insights vztahující se na příchozí a odchozí požadavky do a z podů se spuštěnou v clusteru Kubernetes pomocí použití technologie sítě služby volat Istio.
services: application-insights
author: rishabjolly
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: rijolly
ms.openlocfilehash: 73f95ab75b49fb8ec5b61f6e30080f8f6d474c16
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149880"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Nulové instrumentace application monitoring pro aplikace pro systém Kubernetes hostované aplikace

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Monitor teď využívá technická sítě služby v clusteru Kubernetes poskytnout mimo monitorování pole aplikací pro libovolnou aplikaci Kubernetes hostované. S výchozí funkce Application Insights, například [Mapa aplikace](../../azure-monitor/app/app-map.md) pro modelování závislostí, [Live Metrics Stream](../../azure-monitor/app/live-stream.md) monitorování v reálném čase, užitečných vizualizací s [výchozí řídicí panel](../../azure-monitor/app/overview-dashboard.md), [Průzkumník metrik](../../azure-monitor/platform/metrics-getting-started.md), a [sešity](../../azure-monitor/app/usage-workbooks.md). Tato funkce vám pomůže uživatelům přímé výkonnostní kritické body a aktivní body selhání ve všech svých úloh Kubernetes ve vybraném oboru názvů Kubernetes. S využitím celé vaše stávající investice služby sítě s technologií, jako je Istio, Azure Monitor umožňuje monitorování aplikací instrumentována automaticky bez jakékoli změny kódu aplikace.

> [!NOTE]
> Toto je jeden z mnoha způsoby, jak provádět monitorování aplikací v Kubernetes. Také vám umožňuje instrumentovat všechny aplikace hostované v Kubernetes s použitím [Application Insights SDK](../../azure-monitor/azure-monitor-app-hub.md) bez potřeby sítě služby. Monitorování Kubernetes bez instrumentace aplikace pomocí sady SDK můžete použít pod metodu.

## <a name="prerequisites"></a>Požadavky

- A [clusteru Kubernetes](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads).
- Přístup ke clusteru pro spuštění konzoly *kubectl*.
- [Prostředku Application Insights](create-new-resource.md)
- Být mřížku služby. Pokud váš cluster nemá Istio nasazení, můžete se dozvědět jak [nainstalovat a používat ve službě Azure Kubernetes Service Istio](https://docs.microsoft.com/azure/aks/istio-install).

## <a name="capabilities"></a>Možnosti

Pomocí nulové instrumentace aplikace pro monitorování Kubernetes hostované aplikace bude moct používat:

- [Mapa aplikace](../../azure-monitor/app/app-map.md)
- [Live Stream Metrics](../../azure-monitor/app/live-stream.md)
- [Řídicí panely](../../azure-monitor/app/overview-dashboard.md)
- [Průzkumník metrik](../../azure-monitor/platform/metrics-getting-started.md)
- [Distribuované trasování](../../azure-monitor/app/distributed-tracing.md)
- [Monitorování transakcí začátku do konce](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>Postup instalace

Pokud chcete řešení povolit, můžeme vám provedením následujících kroků:
- Nasazení aplikace (pokud ještě není nasazený).
- Ujistěte se, že aplikace je součástí služby sítě.
- Sledujte shromažďovat telemetrická data.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>Konfigurace aplikace pro práci s sítě služby

Istio podporuje dva způsoby [instrumentace pod](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/).
Ve většině případů je nejjednodušší označit Kubernetes obor názvů obsahující aplikaci *istio vkládání* popisku:

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> Protože data výtahy sítě služby vypnuté přenosu, jsme nelze zachytit šifrovaný síťový provoz. Pro přenosy nezůstávají clusteru použijte nešifrovaný protokol (například HTTP). Pro externí provoz, který musí být šifrované, vezměte v úvahu [nastavení ukončení protokolu SSL](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) v kontroleru příchozího přenosu.

Aplikace běžící mimo síť služby nejsou ovlivněny.

### <a name="deploy-your-application"></a>Nasazení aplikace

- Nasazení aplikace na *my namespace aplikace* oboru názvů. Pokud je aplikace již nasazená, a jste postupovali podle metody Automatické sajdkára vkládání je popsáno výše, budete muset znovu vytvořit podů Ujistěte se, že Istio vkládá jeho sajdkára; Spustit kumulativní aktualizaci nebo odstranění jednotlivých podů a počkat na jejich znovu vytvořit.
- Ujistěte se vaše aplikace splňuje [Istio požadavky](https://istio.io/docs/setup/kubernetes/prepare/requirements/).

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Nasazení aplikace žádnou instrumentaci monitorování Kubernetes hostované aplikace

1. Stažení a extrakci [ *adaptér Application Insights* release](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/).
2. Přejděte do */src/kubernetes/* ve složce release.
3. Edit *application-insights-istio-mixer-adapter-deployment.yaml*
    - Upravit hodnotu daného *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* proměnnou prostředí tak, aby obsahovala Instrumentační klíč tohoto prostředku Application Insights na webu Azure portal tak, aby obsahovala telemetrická data.
    - V případě potřeby upravte hodnotu *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* proměnnou prostředí tak, aby obsahovala čárkou oddělený seznam oborů názvů, pro kterou chcete povolit monitorování. Ponechte prázdné, pokud chcete monitorovat všechny obory názvů.
4. Použít *každý* soubor YAML nalezené pod *src/kubernetes/* spuštěním následujícího (přesto musí být uvnitř */src/kubernetes/*):

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>Ověření nasazení

- Ověřte, že byla nasazena adaptér Application Insights:

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> V některých případech dolaďování ladění se vyžaduje. Chcete-li zahrnout nebo vyloučit ze shromažďování telemetrie pro jednotlivé pod, použijte *appinsights/monitoring.enabled* popisku na tuto pod. To se mají přednost před všechny konfigurace založená na obor názvů. Nastavte *appinsights/monitoring.enabled* k *true* zahrnout pod a získat *false* chcete vyloučit.

### <a name="view-application-insights-telemetry"></a>Zobrazit telemetrii Application Insights

- Generovat ukázkový požadavek proti vašim aplikaci potvrďte, že monitorování funguje správně.
- Během 3 až 5 minut začnou zobrazovat příslušné údaje telemetrie se zobrazí na webu Azure Portal. Nezapomeňte se podívat *Mapa aplikace* oddílu prostředku Application Insights na portálu.

## <a name="troubleshooting"></a>Řešení potíží

Níže se řešení potíží tok při telemetrická data nezobrazí na portálu Azure Portal jako očekává.

1. Ujistěte se, aplikace je zatížení a se posílají nebo přijímají žádosti o prostý protokolu HTTP. Protože telemetrie je zrušeno vs. vypnutí přenosu, šifrovaný provoz se nepodporuje. Pokud neexistují žádné příchozí nebo odchozí požadavky, nebudou bez telemetrie buď.
2. Zajistěte správné Instrumentační klíč je součástí *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* proměnnou prostředí v *application-insights-istio-mixer-adapter-deployment.yaml*. Klíč instrumentace se nachází na *přehled* kartu prostředek služby Application Insights na webu Azure Portal.
3. Zajištění správný obor názvů Kubernetes je součástí *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* proměnnou prostředí v *application-insights-istio-mixer-adapter-deployment.yaml*. Ponechte prázdné, pokud chcete monitorovat všechny obory názvů.
4. Zajistěte, aby že se sajdkára vloženy Istio podů vaší aplikace. Ověřte, zda existuje pro Istio sajdkára na každý pod.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Ověřte, zda existuje kontejner s názvem *istio proxy* běžící na pod.

5. Zobrazení trasování Application Insights adaptér.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   Počet položek získaná telemetrická data se aktualizuje jednou za minutu. Pokud ho nebude zvětšovat minutu za minutu - žádné telemetrických dat je odesíláno adaptéru Istio.
   Vyhledejte chyby v protokolu.
6. Pokud se zjistí, která *Application Insights pro Kubernetes* adaptér není právě dodáni telemetrických dat, naleznete v protokolech Mixer Istio společnosti zjistit, proč se odesílání dat do adaptéru:

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   Vyhledejte chyby, zejména vztahujícímu se ke komunikaci s *applicationinsightsadapter* adaptér.

## <a name="faq"></a>Nejčastější dotazy

Nejnovější informace o průběhu na tento projekt, najdete [adaptér Application Insights pro GitHub project Istio Mixer](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq).

## <a name="uninstall"></a>Odinstalace

Chcete-li odinstalovat produkt, pro *každý* soubor YAML nalezené pod *src/kubernetes/* spustit:

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>Další postup

Další informace o tom, jak Azure Monitor a kontejnery pracují společně návštěvu [monitorování Azure pro kontejnery – přehled](../../azure-monitor/insights/container-insights-overview.md)