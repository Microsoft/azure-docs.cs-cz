---
title: Nasazení instance Prometheus v clusteru Azure Red Hat OpenShift
description: Vytvořte instanci Prometheus v clusteru Azure Red Hat OpenShift a sledujte metriky vaší aplikace.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: Prometheus, ARO, OpenShift, metriky, Red Hat
ms.openlocfilehash: 42ed8c90b35eba57fdc3db1f0ed93d44cf9a5e41
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92218608"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>Nasazení samostatné instance Prometheus v clusteru Azure Red Hat OpenShift

> [!IMPORTANT]
> Azure Red Hat OpenShift 3,11 bude vyřazeno 30. června 2022. Podpora pro vytváření nových clusterů Azure Red Hat OpenShift 3,11 pokračuje do 30. listopadu 2020. Po vyřazení z provozu budou zbývající clustery Azure Red Hat OpenShift 3,11 vypnuté, aby se předešlo chybám zabezpečení.
> 
> Podle tohoto průvodce [vytvořte cluster Azure Red Hat OpenShift 4](tutorial-create-cluster.md).
> Pokud máte konkrétní otázky, [kontaktujte nás prosím](mailto:arofeedback@microsoft.com).

Tento článek popisuje, jak nakonfigurovat samostatnou instanci Prometheus, která používá zjišťování služeb v clusteru Azure Red Hat OpenShift.

> [!NOTE]
> Přístup ke clusteru Azure Red Hat OpenShift není potřebný pro správce zákazníka.

Nastavení cíle:

- Jeden projekt (Prometheus-Project), který obsahuje Prometheus a Alertmanager.
- Dva projekty (App-Project1 a App-"Project2") obsahující aplikace, které chcete monitorovat.

Soubory konfigurace Prometheus budete připravovat místně. Vytvořte novou složku, do které se budou ukládat. Konfigurační soubory se ukládají v clusteru jako tajné klíče v případě, že se do clusteru přidají tajné tokeny později.

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>Přihlaste se ke clusteru pomocí nástroje OC.

1. Otevřete webový prohlížeč a pak klikněte na webovou konzoli vašeho clusteru ( https://openshift .* ID náhodného*. *region*. azmosa.IO).
2. Přihlaste se pomocí přihlašovacích údajů Azure.
3. V pravém horním rohu vyberte své uživatelské jméno a pak vyberte **příkaz Kopírovat přihlášení**.
4. Vložte své uživatelské jméno do terminálu, který budete používat.

> [!NOTE]
> Pokud se chcete podívat, jestli jste se přihlásili ke správnému clusteru, spusťte `oc whoami -c` příkaz.

## <a name="prepare-the-projects"></a>Příprava projektů

Chcete-li vytvořit projekty, spusťte následující příkazy:
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> Můžete buď použít `-n` `--namespace` parametr nebo, nebo vybrat aktivní projekt spuštěním `oc project` příkazu.

## <a name="prepare-the-prometheus-configuration-file"></a>Příprava konfiguračního souboru Prometheus
Zadáním následujícího obsahu vytvořte soubor Prometheus. yml:
```
global:
  scrape_interval: 30s
  evaluation_interval: 5s

scrape_configs:
    - job_name: prom-sd
      scrape_interval: 30s
      scrape_timeout: 10s
      metrics_path: /metrics
      scheme: http
      kubernetes_sd_configs:
      - api_server: null
        role: endpoints
        namespaces:
          names:
          - prometheus-project
          - app-project1
          - app-project2
```
Zadáním následující konfigurace vytvořte tajný klíč s názvem prom:
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

Soubor Prometheus. yml je základní konfigurační soubor Prometheus. Nastaví intervaly a nakonfiguruje automatické zjišťování ve třech projektech (Prometheus-Project, App-Project1, App-"Project2"). V předchozím konfiguračním souboru jsou automaticky zjištěné koncové body vyřazeny přes protokol HTTP bez ověřování.

Další informace o koncových bodech pro vyřazení najdete v tématu [Prometheus Scape config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config).


## <a name="prepare-the-alertmanager-config-file"></a>Příprava konfiguračního souboru Alertmanager
Zadáním následujícího obsahu vytvořte soubor alertmanager. yml:
```
global:
  resolve_timeout: 5m
route:
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 12h
  receiver: default
  routes:
  - match:
      alertname: DeadMansSwitch
    repeat_interval: 5m
    receiver: deadmansswitch
receivers:
- name: default
- name: deadmansswitch
```
Pomocí následující konfigurace vytvořte tajný klíč s názvem Prom-Alerts:
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager. yml je konfigurační soubor správce výstrah.

> [!NOTE]
> Chcete-li ověřit dva předchozí kroky, spusťte `oc get secret -n prometheus-project` příkaz.

## <a name="start-prometheus-and-alertmanager"></a>Spustit Prometheus a Alertmanager
Přejít do [úložiště OpenShift/Origin](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) a stáhnout šablonu [Prometheus-Standalone. yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) . Použijte šablonu na Prometheus-Project zadáním následující konfigurace:
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Soubor Prometheus-Standalone. yaml je šablonou OpenShift. Vytvoří instanci Prometheus s protokolem OAuth-proxy před IT a instancí Alertmanager, která je zabezpečená také pomocí proxy OAuth-proxy. V této šabloně je proxy server OAuth nakonfigurovaný tak, aby umožňoval každému uživateli, který může získat obor názvů Prometheus-Project (viz `-openshift-sar` příznak).

> [!NOTE]
> Chcete-li ověřit, zda má prom StatefulSet stejný a aktuální počet replik, spusťte `oc get statefulset -n prometheus-project` příkaz. Chcete-li kontrolovat všechny prostředky v projektu, spusťte `oc get all -n prometheus-project` příkaz.

## <a name="add-permissions-to-allow-service-discovery"></a>Přidání oprávnění pro povolení zjišťování služeb

Zadáním následujícího obsahu vytvořte soubor Prometheus-sdrole. yml:
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus service discovery role
    description: |
      Role and rolebinding added permissions required for service discovery in a given project.
    iconClass: fa fa-cogs
    tags: "monitoring,prometheus,alertmanager,time-series"
parameters:
- description: The project name, where a standalone Prometheus is deployed
  name: PROMETHEUS_PROJECT
  value: prometheus-project
objects:
- apiVersion: rbac.authorization.k8s.io/v1
  kind: Role
  metadata:
    name: prometheus-sd
  rules:
  - apiGroups:
    - ""
    resources:
    - services
    - endpoints
    - pods
    verbs:
    - list
    - get
    - watch
- apiVersion: rbac.authorization.k8s.io/v1
  kind: RoleBinding
  metadata:
    name: prometheus-sd
  roleRef:
    apiGroup: rbac.authorization.k8s.io
    kind: Role
    name: prometheus-sd
  subjects:
  - kind: ServiceAccount
    name: prom
    namespace: ${PROMETHEUS_PROJECT}
```
Chcete-li šablonu použít pro všechny projekty, ze kterých chcete povolení zjišťování služby, spusťte následující příkazy:
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
oc process -f prometheus-sdrole.yml | oc apply -f - -n prometheus-project
```

> [!NOTE]
> Chcete-li ověřit, zda byla role a RoleBinding správně vytvořena, spusťte `oc get role` `oc get rolebinding` příkazy a.

## <a name="optional-deploy-example-application"></a>Volitelné: příklad nasazení aplikace

Vše funguje, ale neexistují žádné zdroje metriky. Přejít na adresu URL Prometheus ( https://prom-prometheus-project.apps .* ID náhodného*. *region*. azmosa.IO/). Můžete ji najít pomocí následujícího příkazu:

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> Nezapomeňte přidat předponu https://na začátek názvu hostitele.

Na stránce **stav > zjišťování služby** se zobrazí 0/0 aktivní cíle.

Pokud chcete nasadit ukázkovou aplikaci, která zveřejňuje základní metriky Pythonu pod koncovým bodem/Metrics, spusťte následující příkazy:
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
Nové aplikace by se měly zobrazit jako platné cíle na stránce zjišťování služby během 30 sekund po nasazení.

Další podrobnosti najdete v výběru **Status**  >  **cíle**stavu.

> [!NOTE]
> Pro každý úspěšně vyřazený cíl Prometheus přidá datový bod do metriky nahoru. V levém horním rohu vyberte **Prometheus** **, jako výraz** zadejte a pak vyberte Execute ( **Spustit**).

## <a name="next-steps"></a>Další kroky

Do svých aplikací můžete přidat vlastní instrumentaci Prometheus. Klientská knihovna Prometheus, která zjednodušuje přípravu metrik Prometheus, je připravená pro různé programovací jazyky.

Další informace najdete v následujících knihovnách GitHubu:

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [Přejít](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
