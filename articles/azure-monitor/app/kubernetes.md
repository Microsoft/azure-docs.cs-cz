---
title: Pomocí Application Insights můžete monitorovat službu Azure Kubernetes (AKS) nebo jiné aplikace hostované v Kubernetes – Azure Monitor | Microsoft Docs
description: Azure Monitor používá k zajištění monitorování aplikací pro všechny hostované aplikace Kubernetes v clusteru Kubernetes technologii sítě služby Istio. Díky tomu můžete shromažďovat Application Insights telemetrie týkající se příchozích a odchozích požadavků do a z lusků spuštěných ve vašem clusteru.
ms.topic: conceptual
author: tokaplan
ms.author: alkaplan
ms.date: 04/25/2019
ms.openlocfilehash: 3cd43963175594fcdc1c3c67d6b2493ce1ccd313
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87321917"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications-with-istio---deprecated"></a>Nula monitorování aplikace instrumentace pro aplikace hostované v Kubernetes s Istio – zastaralé

> [!IMPORTANT]
> Tato funkce je aktuálně zastaralá a již nebude podporována po 1. srpnu 2020.
> V současné době se monitorování bez kódu dá povolit jenom pro [Java prostřednictvím samostatného agenta](./java-in-process-agent.md). Pro jiné jazyky použijte sady SDK k monitorování aplikací na AKS: [ASP.NET Core](./asp-net-core.md), [ASP.NET](./asp-net.md), [Node.js](./nodejs.md), [JavaScript](./javascript.md)a [Python](./opencensus-python.md).

Azure Monitor teď využívá síť pro pracovní plochu v clusteru Kubernetes k tomu, aby poskytovala monitorování aplikací pro všechny hostované aplikace Kubernetes. Pomocí výchozích funkcí Application Insights, jako je [Mapa aplikací](./app-map.md) , můžete modelovat závislosti [Live Metrics Stream](./live-stream.md) pro monitorování v reálném čase, výkonné vizualizace s [výchozím řídicím panelem](./overview-dashboard.md), [průzkumníkem](../platform/metrics-getting-started.md)a [sešity](../platform/workbooks-overview.md). Tato funkce pomůže uživatelům, aby na všech svých Kubernetes úlohách v rámci vybraného oboru názvů Kubernetes zavedli slabá místa ve výkonu a hotspoty selhání. Díky tomu, že vaše stávající investice do sítě na síti s technologiemi, jako je Istio Azure Monitor, umožňují monitorování automaticky instrumentované aplikace bez jakýchkoli úprav kódu vaší aplikace.

> [!NOTE]
> Toto je jeden z mnoha způsobů, jak provádět monitorování aplikací v Kubernetes.Jakoukoli aplikaci hostovanou v Kubernetes můžete také instrumentovat pomocí [sady SDK Application Insights](../azure-monitor-app-hub.yml) bez nutnosti použití sítě. Pokud chcete monitorovat Kubernetes bez instrumentace aplikace pomocí sady SDK, můžete použít níže uvedenou metodu.

## <a name="prerequisites"></a>Požadavky

- [Cluster Kubernetes](../../aks/concepts-clusters-workloads.md).
- Konzola má přístup ke clusteru, aby bylo možné spustit *kubectl*.
- [Prostředek přehledu aplikace](create-new-resource.md)
- Mít síť pro službu. Pokud váš cluster nemá nasazené Istio, můžete se dozvědět, jak [nainstalovat a používat Istio ve službě Azure Kubernetes Service](../../aks/servicemesh-istio-install.md).

## <a name="capabilities"></a>Možnosti

Při použití nástroje s nulovým monitorováním aplikací instrumentace pro aplikace hostované v Kubernetes budete moct použít:

- [Mapa aplikace](./app-map.md)
- [Live Stream metriky](./live-stream.md)
- [Řídicí panely](./overview-dashboard.md)
- [Průzkumník metrik](../platform/metrics-getting-started.md)
- [Distribuované – trasování](./distributed-tracing.md)
- [Monitorování koncové transakce](../learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>Kroky instalace

Pokud chcete řešení povolit, provedeme tyto kroky:
- Nasaďte aplikaci (Pokud ještě není nasazená).
- Ujistěte se, že je aplikace součástí sítě.
- Sledujte shromážděnou telemetrii.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>Konfigurace aplikace tak, aby fungovala s sítí služby

Istio podporuje dva způsoby [instrumentace pod](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/).
Ve většině případů je nejjednodušší označit obor názvů Kubernetes, který obsahuje vaši aplikaci, pomocí popisku *istio pro vložení* :

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> Vzhledem k tomu, že síť vyzvedá data z provozu, nemůžeme zachytit šifrovaný provoz. U provozu, který neopouští cluster, použijte nešifrovaný protokol (například HTTP). U externích přenosů, které musí být šifrované, zvažte [Nastavení ukončení protokolu TLS](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) v řadiči příchozího přenosu dat.

Aplikace spuštěné mimo síť nejsou ovlivněny.

### <a name="deploy-your-application"></a>Nasazení aplikace

- Nasaďte aplikaci do oboru názvů *Moje App-Namespace* . Pokud už je aplikace nasazená a Vy jste následovali metodu automatického vstřiku na postranním vozíku popsanou výše, musíte znovu vytvořit lusky, abyste zajistili, že Istio vloží svůj postranní vozík. buď zahajte postupnou aktualizaci, nebo odstraňte jednotlivé lusky a počkejte na jejich opětovné vytvoření.
- Ujistěte se, že vaše aplikace splňuje [požadavky Istio](https://istio.io/docs/setup/kubernetes/prepare/requirements/).

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Nasazení monitorování aplikací s nulovou hodnotou instrumentace pro aplikace hostované v Kubernetes

1. Stáhněte a extrahujte [verzi *Application Insightsho adaptéru* ](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/).
2. V rámci složky pro vydání přejděte na */Src/Kubernetes/* .
3. Úprava *Application-Insights-istio-mixer-Adapter-Deployment. yaml*
    - Upravte hodnotu proměnné prostředí *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* tak, aby obsahovala klíč instrumentace prostředku Application Insights v Azure Portal, aby obsahovala telemetrii.
    - V případě potřeby upravte hodnotu proměnné prostředí *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* tak, aby obsahovala seznam oborů názvů oddělených čárkami, pro které chcete monitorování povolit. Pokud chcete monitorovat všechny obory názvů, ponechte pole prázdné.
4. Použijte *všechny* soubory YAML nalezené pod *Src/Kubernetes/* spuštěním následujícího (stále musíte být uvnitř */Src/Kubernetes/*):

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>Ověření nasazení

- Ujistěte se, že je nasazený Application Insights adaptér:

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> V některých případech se vyžaduje ladění s vyladěním. Chcete-li zahrnout nebo vyloučit telemetrii pro jednotlivec pod shromažďováním, použijte *appinsights/monitoring.* on, na kterém je povolený popisek. Tato akce bude mít prioritu pro všechny konfigurace založené na oborech názvů. Nastavte *appinsights/monitoring. Enabled* na *hodnotu true* , chcete-li zahrnout pole pod a na *hodnotu false* pro vyloučení.

### <a name="view-application-insights-telemetry"></a>Zobrazit telemetrii Application Insights

- Vygenerujte vzorový požadavek na vaši aplikaci a potvrďte tak, že monitorování funguje správně.
- Během 3-5 minut byste měli začít zobrazovat telemetrii v Azure Portal. Ujistěte se, že se v portálu Application Insights prostředku na portálu nachází část s *mapou aplikace* .

## <a name="troubleshooting"></a>Řešení potíží

Níže je uvedený postup řešení potíží, který se použije v případě, že se telemetrie neobjeví v Azure Portal podle očekávání.

1. Ujistěte se, že je aplikace zatížená a posílá nebo přijímá požadavky v prostém protokolu HTTP. Vzhledem k tomu, že telemetrie jsou z provozu přerušená, není šifrovaný provoz podporovaný. Pokud neexistují žádné příchozí nebo odchozí požadavky, nebudete ani žádné telemetrie.
2. Ujistěte se, že je ve *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* proměnné prostředí v *Application-Insights-ISTIO-mixer-Adapter-Deployment. yaml*zadaný správný klíč instrumentace. Klíč instrumentace najdete na kartě *přehled* Application Insights prostředku v Azure Portal.
3. Ujistěte se, že je ve *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* proměnné prostředí v *Application-Insights-ISTIO-mixer-Adapter-Deployment. yaml*zadaný správný obor názvů Kubernetes. Pokud chcete monitorovat všechny obory názvů, ponechte pole prázdné.
4. Zajistěte, aby byly lusky vaší aplikace na vozíku vloženy pomocí Istio. Ověřte, že se na každém z pod nachází postranní vozík Istio.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Ověřte, zda je na seznamu spuštěn kontejner s názvem *istio-proxy* .

5. Zobrazit trasování adaptéru Application Insights.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   Počet přijatých položek telemetrie se aktualizuje jednou za minutu. Pokud nerůstá minuta za minutu – Istio se do adaptéru neodesílají žádné telemetrie.
   Vyhledejte případné chyby v protokolu.
6. Pokud bylo navázáno, že *Application Insight for Kubernetes* Adapter neposílá telemetrii, podívejte se do protokolů směšovače Istio a zjistěte, proč neodesílají data do adaptéru:

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   Vyhledejte případné chyby, zejména související s komunikací s *applicationinsightsadapter* adaptérem.

## <a name="faq"></a>Časté otázky

Nejnovější informace o průběhu tohoto projektu najdete na [githubu Application Insights Adapter pro projekt mixer Istio](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq).

## <a name="uninstall"></a>Odinstalovat

Chcete-li odinstalovat produkt, pro *každý* soubor YAML nalezený v části *Src/Kubernetes/* Run:

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>Další kroky

Další informace o tom, jak Azure Monitor a kontejnery spolupracují, najdete [v tématu Azure monitor for Containers Overview](../insights/container-insights-overview.md) .

