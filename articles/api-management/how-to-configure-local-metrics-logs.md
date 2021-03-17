---
title: Konfigurace místních metrik a protokolů pro službu Azure API Management samoobslužná brána | Microsoft Docs
description: Naučte se konfigurovat místní metriky a protokoly pro bránu Azure API Management v místním prostředí pro Kubernetes Custer.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/01/2021
ms.author: apimpm
ms.openlocfilehash: 2b66663c9ee8033bcb12bfac57964ea0eafecdac
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594179"
---
# <a name="configure-local-metrics-and-logs-for-azure-api-management-self-hosted-gateway"></a>Konfigurace místních metrik a protokolů pro bránu Azure API Management pro samoobslužnou hostování

Tento článek poskytuje podrobné informace o konfiguraci místních metrik a protokolů pro [samoobslužnou bránu](./self-hosted-gateway-overview.md) nasazenou v clusteru Kubernetes. Informace o konfiguraci metrik a protokolů cloudu najdete v [tomto článku](how-to-configure-cloud-metrics-logs.md). 

## <a name="metrics"></a>Metriky
Samoobslužná brána podporuje [statistiku](https://github.com/statsd/statsd), která se stala sjednocením protokolu pro shromažďování a agregaci metrik. Tato část vás provede kroky pro nasazení statistik Kubernetes, konfiguraci brány k vygenerování metrik přes statistiku a použití [Prometheus](https://prometheus.io/) pro monitorování metrik. 

### <a name="deploy-statsd-and-prometheus-to-the-cluster"></a>Nasazení statistiky a Prometheus do clusteru

Níže je uvedená Ukázková konfigurace YAML pro nasazení statistiky a Prometheus do clusteru Kubernetes, ve kterém je nasazená samoobslužná brána. Pro každou taky vytvoří [službu](https://kubernetes.io/docs/concepts/services-networking/service/) . Brána v místním prostředí bude publikovat metriky do statistické služby. K řídicímu panelu Prometheus se dostanete prostřednictvím služby.   

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: sputnik-metrics-config
data:
  statsd.yaml: ""
  prometheus.yaml: |
    global:
      scrape_interval:     3s
      evaluation_interval: 3s
    scrape_configs:
      - job_name: 'prometheus'
        static_configs:
          - targets: ['localhost:9090']
      - job_name: 'test_metrics'
        static_configs:
          - targets: ['localhost:9102']
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sputnik-metrics
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sputnik-metrics
  template:
    metadata:
      labels:
        app: sputnik-metrics
    spec:
      containers:
      - name: sputnik-metrics-statsd
        image: mcr.microsoft.com/aks/hcp/prom/statsd-exporter
        ports:
        - name: tcp
          containerPort: 9102
        - name: udp
          containerPort: 8125
          protocol: UDP
        args:
          - --statsd.mapping-config=/tmp/statsd.yaml
          - --statsd.listen-udp=:8125
          - --web.listen-address=:9102
        volumeMounts:
          - mountPath: /tmp
            name: sputnik-metrics-config-files
      - name: sputnik-metrics-prometheus
        image: mcr.microsoft.com/oss/prometheus/prometheus
        ports:
        - name: tcp
          containerPort: 9090
        args:
          - --config.file=/tmp/prometheus.yaml
        volumeMounts:
          - mountPath: /tmp
            name: sputnik-metrics-config-files
      volumes:
        - name: sputnik-metrics-config-files
          configMap:
            name: sputnik-metrics-config
---
apiVersion: v1
kind: Service
metadata:
  name: sputnik-metrics-statsd
spec:
  type: NodePort
  ports:
  - name: udp
    port: 8125
    targetPort: 8125
    protocol: UDP
  selector:
    app: sputnik-metrics
---
apiVersion: v1
kind: Service
metadata:
  name: sputnik-metrics-prometheus
spec:
  type: LoadBalancer
  ports:
  - name: http
    port: 9090
    targetPort: 9090
  selector:
    app: sputnik-metrics
```

Uložte konfigurace do souboru s názvem `metrics.yaml` a pomocí následujícího příkazu nasaďte vše do clusteru:

```console
kubectl apply -f metrics.yaml
```

Až se nasazení dokončí, spusťte následující příkaz, abyste zkontrolovali, jestli jsou spuštěné lusky. Všimněte si, že název pod se bude lišit. 

```console
kubectl get pods
NAME                                   READY   STATUS    RESTARTS   AGE
sputnik-metrics-f6d97548f-4xnb7        2/2     Running   0          1m
```

Spusťte následující příkaz a ověřte, zda jsou služby spuštěny. Poznamenejte si `CLUSTER-IP` a `PORT` v statované službě ji budeme potřebovat později. Řídicí panel Prometheus můžete navštívit pomocí jeho `EXTERNAL-IP` a `PORT` .

```console
kubectl get services
NAME                         TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE
sputnik-metrics-prometheus   LoadBalancer   10.0.252.72   13.89.141.90    9090:32663/TCP               18h
sputnik-metrics-statsd       NodePort       10.0.41.179   <none>          8125:32733/UDP               18h
```

### <a name="configure-the-self-hosted-gateway-to-emit-metrics"></a>Konfigurace brány pro samoobslužné hostování k vygenerování metrik

Teď, když jsme nasadili jak statistiky, tak i Prometheus, můžeme aktualizovat konfigurace samoobslužné brány, aby bylo možné metriky vysílat prostřednictvím statistik. Tuto funkci můžete povolit nebo zakázat pomocí `telemetry.metrics.local` klíče v ConfigMap nasazení samoobslužné brány s dalšími možnostmi. Níže je uveden přehled dostupných možností:

| Pole  | Výchozí | Description |
| ------------- | ------------- | ------------- |
| telemetrie. metriky. Local  | `none` | Povolí protokolování prostřednictvím statistik. Hodnota může být `none` , `statsd` . |
| telemetrie. metriky. Local. stated. Endpoint  | Není k dispozici | Určuje postatový koncový bod. |
| telemetrie. metriky. Local. stated. vzorkování  | Není k dispozici | Určuje vzorkovací frekvenci metrik. Hodnota může být mezi 0 a 1. např.: `0.5`|
| telemetrie. metriky. Local. stated. Tag-Format  | Není k dispozici | [Formát označování značek](https://github.com/prometheus/statsd_exporter#tagging-extensions)exportéra Hodnota může být `none` , `librato` , `dogStatsD` , `influxDB` . |

Tady je Ukázková konfigurace:

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<self-hosted-gateway-management-endpoint>"
        telemetry.metrics.local: "statsd"
        telemetry.metrics.local.statsd.endpoint: "10.0.41.179:8125"
        telemetry.metrics.local.statsd.sampling: "1"
        telemetry.metrics.local.statsd.tag-format: "dogStatsD"
```

Aktualizujte soubor YAML nasazení samoobslužné brány pomocí výše uvedených konfigurací a použijte změny pomocí následujícího příkazu: 

```console
kubectl apply -f <file-name>.yaml
 ```

Chcete-li si vybrat nejnovější změny konfigurace, restartujte nasazení brány pomocí příkazu níže:

```console
kubectl rollout restart deployment/<deployment-name>
```

### <a name="view-the-metrics"></a>Zobrazit metriky

Teď máme všechno nasazené a nakonfigurované, ale v samoobslužné bráně by se měla hlásit metriky prostřednictvím statistiky. Prometheus vybere metriky ze statistik. Na řídicím panelu Prometheus se dostanete `EXTERNAL-IP` pomocí `PORT` služeb a služby Prometheus. 

Navázání některých volání rozhraní API prostřednictvím samoobslužné brány, pokud je vše nakonfigurované správně, byste měli být schopni zobrazit níže uvedené metriky:

| Metric  | Popis |
| ------------- | ------------- |
| Žádosti  | Počet požadavků na rozhraní API v období |
| DurationInMS | Počet milisekund od chvíle, kdy brána přijala požadavek, do chvíle odeslání úplné odpovědi. |
| BackendDurationInMS | Počet milisekund strávený na všech vstupně-výstupních operacích back-endu (připojování, odesílání a příjem bajtů).  |
| ClientDurationInMS | Počet milisekund strávený na všech vstupně-výstupních operacích klienta (připojování, odesílání a příjem bajtů).  |

## <a name="logs"></a>Protokoly

V samoobslužné bráně se `stdout` `stderr` ve výchozím nastavení protokolují protokoly. Protokoly můžete snadno zobrazit pomocí následujícího příkazu:

```console
kubectl logs <pod-name>
```

Pokud je vaše místní hostská brána nasazená ve službě Azure Kubernetes, můžete povolit [Azure monitor pro kontejnery](../azure-monitor/containers/container-insights-overview.md) shromažďovat `stdout` a `stderr` z vašich úloh a zobrazovat protokoly v Log Analytics. 

Samoobslužná brána také podporuje několik protokolů `localsyslog` , a to včetně, `rfc5424` a `journal` . Níže uvedená tabulka shrnuje všechny podporované možnosti. 

| Pole  | Výchozí | Description |
| ------------- | ------------- | ------------- |
| telemetrie. logs. std  | `text` | Povolí protokolování do standardních datových proudů. Hodnota může být `none` , `text` , `json` |
| telemetrie. log. Local  | `none` | Povolí místní protokolování. Hodnota může být `none` , `auto` , `localsyslog` , `rfc5424` , `journal`  |
| telemetrie. log. Local. localsyslog. Endpoint  | Není k dispozici | Určuje koncový bod localsyslog.  |
| telemetrie. log. Local. localsyslog. facilita  | Není k dispozici | Určuje [kód zařízení](https://en.wikipedia.org/wiki/Syslog#Facility)localsyslog. např.: `7` 
| telemetrie. log. Local. rfc5424. Endpoint  | Není k dispozici | Určuje koncový bod rfc5424.  |
| telemetrie. log. Local. rfc5424. facilita  | Není k dispozici | Určuje kód zařízení na [rfc5424](https://tools.ietf.org/html/rfc5424). např.: `7`  |
| telemetrie. log. Local. Journal. Endpoint  | Není k dispozici | Určuje koncový bod deníku.  |

Tady je ukázka konfigurace místního protokolování:

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<self-hosted-gateway-management-endpoint>"
        telemetry.logs.std: "text"
        telemetry.logs.local.localsyslog.endpoint: "/dev/log"
        telemetry.logs.local.localsyslog.facility: "7"
```
 
## <a name="next-steps"></a>Další kroky

* Další informace o samoobslužné bráně najdete v tématu [Přehled služby Azure API Management v místním prostředí pro samoobslužné hostování](self-hosted-gateway-overview.md) .
* Informace o [konfiguraci a uchování protokolů v cloudu](how-to-configure-local-metrics-logs.md)
