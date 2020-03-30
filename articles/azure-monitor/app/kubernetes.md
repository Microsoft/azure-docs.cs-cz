---
title: Pomocí přehledů aplikací můžete monitorovat službu Azure Kubernetes Service (AKS) nebo jiné hostované aplikace Kubernetes – Azure Monitor | Dokumenty společnosti Microsoft
description: Azure Monitor používá technologii sítě služeb Istio ve vašem clusteru Kubernetes k poskytování monitorování aplikací pro všechny hostované aplikace Kubernetes. To umožňuje shromažďovat telemetrie Application Insights týkající se příchozích a odchozích požadavků do a z podů spuštěných ve vašem clusteru.
ms.topic: conceptual
author: tokaplan
ms.author: alkaplan
ms.date: 04/25/2019
ms.openlocfilehash: 56a0cb66f5b54c817067970ab369d7ca471a1696
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132349"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications"></a>Monitorování aplikací nulové instrumentace pro hostované aplikace Kubernetes

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Monitor teď využívá technologii sítě služeb ve vašem clusteru Kubernetes k poskytování monitorování aplikací po vybalení z krabice pro všechny hostované aplikace Kubernetes. S výchozími funkcemi Application [Insight,](../../azure-monitor/app/app-map.md) jako je Mapování aplikací pro modelování vašich závislostí, [Živé metriky Stream](../../azure-monitor/app/live-stream.md) pro monitorování v reálném čase, výkonné vizualizace s [výchozím řídicím panelem](../../azure-monitor/app/overview-dashboard.md), [Průzkumník metrik](../../azure-monitor/platform/metrics-getting-started.md)a [sešity](../../azure-monitor/app/usage-workbooks.md). Tato funkce pomůže uživatelům rozpoznat problémová místa výkonu a hotspoty selhání ve všech jejich úlohách Kubernetes v rámci vybraného oboru názvů Kubernetes. Díky využití vašich stávajících investic do sítě služeb pomocí technologií, jako je Istio, Azure Monitor umožňuje monitorování aplikací s automatickým vybavením bez jakýchkoli úprav kódu vaší aplikace.

> [!NOTE]
> To je jeden z mnoha způsobů, jak provádět monitorování aplikací na Kubernetes.Můžete také instrumentovat libovolnou aplikaci hostovanou v Kubernetes pomocí [sady Application Insights SDK](../../azure-monitor/azure-monitor-app-hub.yml) bez nutnosti sítě služeb. Pro sledování Kubernetes bez instrumentace aplikace s SDK můžete použít níže uvedenou metodu.

## <a name="prerequisites"></a>Požadavky

- [Kubernetescluster](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads).
- Konzolový přístup ke clusteru ke spuštění *kubectl*.
- [Prostředek Přehled aplikací](create-new-resource.md)
- Mít servisní síť. Pokud váš cluster nemá istio nasazen, můžete se dozvědět, jak [nainstalovat a používat Istio ve službě Azure Kubernetes](https://docs.microsoft.com/azure/aks/istio-install)Service .

## <a name="capabilities"></a>Možnosti

Pomocí nulového monitorování přístrojové aplikace pro hostované aplikace Kubernetes budete moci používat:

- [Mapa aplikace](../../azure-monitor/app/app-map.md)
- [Metriky živého přenosu](../../azure-monitor/app/live-stream.md)
- [Řídicí panely](../../azure-monitor/app/overview-dashboard.md)
- [Průzkumník metrik](../../azure-monitor/platform/metrics-getting-started.md)
- [Distribuované trasování](../../azure-monitor/app/distributed-tracing.md)
- [Komplexní monitorování transakcí](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>Kroky instalace

Chcete-li povolit řešení, provedeme následující kroky:
- Nasazení aplikace (pokud již není nasazena).
- Ujistěte se, že aplikace je součástí sítě služby.
- Sledujte shromážděné telemetrie.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>Konfigurace aplikace pro práci se síťovou sítí služby

Istio podporuje dva způsoby [instrumentace lusku](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/).
Ve většině případů je nejjednodušší označit obor názvů Kubernetes obsahující vaši aplikaci štítkem *istio-injection:*

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> Vzhledem k tomu, že síť služeb zvedá data z drátu, nemůžeme zachytit šifrovaný provoz. Pro přenosy, které neopustí cluster, použijte nešifrovaný protokol (například HTTP). Pro externí přenosy, které musí být šifrovány, zvažte [nastavení ukončení TLS](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) na řadiči příchozího přenosu dat.

Aplikace spuštěné mimo síť služby nejsou ovlivněny.

### <a name="deploy-your-application"></a>Nasazení aplikace

- Nasaďte aplikaci do oboru *názvů my-app.Deploy your application to my-app-namespace* namespace. Pokud je aplikace již nasazena a vy jste postupovali podle výše popsané metody automatického vstřikování postranního asamosu, musíte znovu vytvořit lusky, abyste zajistili, že Istio vstříkne svůj postranní vůz; buď iniciovat postupnou aktualizaci, nebo odstranit jednotlivé pody a čekat na jejich opětovné vytvoření.
- Ujistěte se, že vaše aplikace splňuje [požadavky Istio](https://istio.io/docs/setup/kubernetes/prepare/requirements/).

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Nasazení monitorování aplikací nulové instrumentace pro hostované aplikace Kubernetes

1. Stáhněte a extrahujte vydání [ *adaptéru Application Insights* ](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/).
2. Přejděte do */src/kubernetes/* uvnitř složky vydání.
3. Úprava *aplikace-insights-istio-mixer-adapter-deployment.yaml*
    - upravte hodnotu proměnné *prostředí ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* tak, aby obsahovala klíč instrumentace prostředku Application Insights na webu Azure Portal, aby obsahovala telemetrii.
    - V případě potřeby upravte hodnotu *proměnné prostředí ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* tak, aby obsahovala seznam oborů názvů oddělených čárkami, pro který chcete povolit monitorování. Ponechejte prázdné sledovat všechny obory názvů.
4. Aplikujte *každý* soubor YAML nalezený pod *src/kubernetes/* spuštěním následujícího (musíte být stále uvnitř */src/kubernetes/*):

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>Ověření nasazení

- Ujistěte se, že byl nasazen adaptér Application Insights:

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> V některých případech je vyžadováno jemné doladění. Chcete-li zahrnout nebo vyloučit telemetrii pro jednotlivé pod ze shromažďování, použijte *appinsights/monitoring.enabled* popisek na tomto podu. To bude mít přednost před všemi konfigurace mise založené na oboru názvů. Nastavte *appinsights/monitoring.enabled* na *true* zahrnout pod a *false* vyloučit.

### <a name="view-application-insights-telemetry"></a>Zobrazit telemetrická data Přehledy aplikací

- Vygenerujte ukázkový požadavek proti vaší aplikaci a ověřte, zda monitorování funguje správně.
- Během 3 až 5 minut byste měli začít vidět telemetrie se zobrazí na webu Azure Portal. Nezapomeňte se podívat na část *Mapa aplikací* vašeho prostředku Application Insights na portálu.

## <a name="troubleshooting"></a>Řešení potíží

Níže je tok řešení potíží použít, když telemetrie nezobrazí na webu Azure Portal podle očekávání.

1. Ujistěte se, že aplikace je pod zatížením a odesílá/přijímá požadavky ve prostém protokolu HTTP. Vzhledem k tomu, že telemetrie je zrušena z drátu, šifrovaný provoz není podporován. Pokud neexistují žádné příchozí nebo odchozí požadavky, bude existovat žádné telemetrie buď.
2. Ujistěte se, že správný klíč instrumentace je k dispozici v proměnné *prostředí ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* v *application-insights-istio-mixer-adapter-deployment.yaml*. Klíč instrumentace se nachází na kartě *Přehled* prostředků Application Insights na webu Azure Portal.
3. Ujistěte se, že správný obor názvů Kubernetes je k dispozici v proměnné *prostředí ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* v *application-insights-istio-mixer-adapter-deployment.yaml*. Ponechejte prázdné sledovat všechny obory názvů.
4. Ujistěte se, že vaše aplikace lusky byly sidecar-injekčně Istio. Ověřte, zda istio je sajdkárna existuje na každém lusku.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Ověřte, zda je v podu spuštěn kontejner s názvem *istio-proxy.*

5. Zobrazení trasování adaptéru Application Insights.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   Počet přijatých položek telemetrie se aktualizuje jednou za minutu. Pokud neroste minutu za minutu – žádná telemetrie je odesílána do adaptéru Istio.
   Vyhledejte všechny chyby v protokolu.
6. Pokud bylo zjištěno, že *Application Insight pro adaptér Kubernetes* není podáván telemetrii, zkontrolujte protokoly Mixer Istio zjistit, proč to není odesílání dat do adaptéru:

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   Vyhledejte všechny chyby, zejména týkající se komunikace s adaptérem *applicationinsightsadapter.*

## <a name="faq"></a>Nejčastější dotazy

Nejnovější informace o průběhu tohoto projektu naleznete na [adaptéru Application Insights pro GitHub projektu Istio Mixer](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq).

## <a name="uninstall"></a>Odinstalace

Chcete-li produkt odinstalovat, pro *každý* soubor YAML nalezený pod *src/kubernetes/* run:

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>Další kroky

Další informace o tom, jak Azure Monitor a kontejnery spolupracují, najdete v [přehledu informací o Azure Monitoru pro kontejnery.](../../azure-monitor/insights/container-insights-overview.md)
