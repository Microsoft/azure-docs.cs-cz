---
title: Nasazení samostatné instance Prometheus v clusteru Azure Red Hat OpenShift | Dokumenty společnosti Microsoft
description: Vytvořte instanci Prometheus v clusteru Azure Red Hat OpenShift ke sledování metrik vaší aplikace.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus, aro, openshift, metriky, červený klobouk
ms.openlocfilehash: f81a993caa31578e689fb3a90108f3cf0ca81fc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69875123"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>Nasazení samostatné instance Prometheus v clusteru Azure Red Hat OpenShift

Tento článek popisuje, jak nakonfigurovat samostatnou instanci Prometheus, která používá zjišťování služby v clusteru Azure Red Hat OpenShift.

> [!NOTE]
> Přístup správce zákazníků ke clusteru Azure Red Hat OpenShift není vyžadován.

Nastavení cíle:

- Jeden projekt (prometheus-project), který obsahuje Prometheus a Alertmanager.
- Dva projekty (project1 a app-project2), které obsahují aplikace ke sledování.

Budete připravovat některé prometheus config soubory místně. Vytvořte novou složku pro jejich uložení. Konfigurační soubory jsou uloženy v clusteru jako tajné klíče, v případě, že tajné tokeny jsou přidány později do clusteru.

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>Přihlášení ke clusteru pomocí nástroje OC

1. Otevřete webový prohlížeč a přejděte do webovéhttps://openshiftkonzole clusteru ( .* náhodné id*. *(azmosa.io).*
2. Přihlaste se pomocí přihlašovacích údajů Azure.
3. V pravém horním rohu vyberte své uživatelské jméno a pak vyberte **Příkaz pro přihlášení do kopírovat**.
4. Vložte své uživatelské jméno do terminálu, který budete používat.

> [!NOTE]
> Chcete-li zjistit, zda jste přihlášeni ke `oc whoami -c` správnému clusteru, spusťte příkaz.

## <a name="prepare-the-projects"></a>Příprava projektů

Chcete-li vytvořit projekty, spusťte následující příkazy:
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> Můžete buď použít `-n` `--namespace` parametr or, nebo vybrat aktivní `oc project` projekt spuštěním příkazu.

## <a name="prepare-the-prometheus-configuration-file"></a>Příprava konfiguračního souboru Prometheus
Vytvořte soubor prometheus.yml zadáním následujícího obsahu:
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
Vytvořte tajný klíč s názvem Prom zadáním následující konfigurace:
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

Soubor prometheus.yml je základní konfigurační soubor Prometheus. Nastavuje intervaly a konfiguruje automatické zjišťování ve třech projektech (prometheus-project, app-project1, app-project2). V předchozím konfiguračním souboru jsou automaticky zjištěné koncové body scraped přes HTTP bez ověření.

Další informace o škrábání koncových bodů naleznete v tématu [Prometheus scape config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config).


## <a name="prepare-the-alertmanager-config-file"></a>Příprava konfiguračního souboru Správce výstrah
Vytvořte soubor alertmanager.yml zadáním následujícího obsahu:
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
Vytvořte tajný klíč s názvem Prom-Alerts zadáním následující konfigurace:
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager.yml je konfigurační soubor Správce výstrah.

> [!NOTE]
> Chcete-li ověřit dva předchozí `oc get secret -n prometheus-project` kroky, spusťte příkaz.

## <a name="start-prometheus-and-alertmanager"></a>Spuštění promethea a správce výstrah
Přejděte do [úložiště openshift/origin](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) a stáhněte si šablonu [prometheus-standalone.yaml.](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) Použijte šablonu pro projekt prometheus zadáním následující konfigurace:
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Soubor prometheus-standalone.yaml je šablona OpenShift. Vytvoří instanci Prometheus s oauth-proxy před ním a Instanci Alertmanager, také zabezpečenou oauth-proxy. V této šabloně je oauth-proxy nakonfigurován tak, aby umožňoval každému uživateli, `-openshift-sar` který může "získat" obor názvů projektu prometheus (viz příznak).

> [!NOTE]
> Chcete-li ověřit, zda má prom StatefulSet stejné `oc get statefulset -n prometheus-project` repliky čísel DESIRED a CURRENT, spusťte příkaz. Chcete-li zkontrolovat všechny zdroje `oc get all -n prometheus-project` v projektu, spusťte příkaz.

## <a name="add-permissions-to-allow-service-discovery"></a>Přidání oprávnění k povolení zjišťování služby

Vytvořte soubor prometheus-sdrole.yml zadáním následujícího obsahu:
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
Chcete-li šablonu použít pro všechny projekty, ze kterých chcete povolit zjišťování služby, spusťte následující příkazy:
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
oc process -f prometheus-sdrole.yml | oc apply -f - -n prometheus-project
```

> [!NOTE]
> Chcete-li ověřit, že role a `oc get role` rolebinding byly vytvořeny správně, spusťte příkazy a. `oc get rolebinding`

## <a name="optional-deploy-example-application"></a>Volitelné: Nasazení ukázkové aplikace

Všechno funguje, ale neexistují žádné zdroje metrik. Přejděte na adresu URLhttps://prom-prometheus-project.appspromethea ( .* náhodné id*. *(azmosa.io/).* Můžete ji najít pomocí následujícího příkazu:

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> Nezapomeňte přidat předponu https:// na začátek názvu hostitele.

**Stránka Zjišťování služby status >** zobrazí aktivní cíle 0/0.

Chcete-li nasadit ukázkovou aplikaci, která zveřejňuje základní metriky Pythonu v koncovém bodě /metrics, spusťte následující příkazy:
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
Nové aplikace by se měly zobrazit jako platné cíle na stránce Zjišťování služby do 30 sekund po nasazení.

Další podrobnosti zobrazíte vyberte **Stavové** > **cíle**.

> [!NOTE]
> Pro každý úspěšně seškrábaný cíl Prometheus přidá datový bod v metriku nahoru. V levém horním rohu vyberte **Prometheus,** zadejte **jako** výraz nahoru a pak vyberte **Spustit**.

## <a name="next-steps"></a>Další kroky

Do svých aplikací můžete přidat vlastní instrumentaci Prometheus. Klientská knihovna Prometheus, která zjednodušuje přípravu metrik Prometheus, je připravena pro různé programovací jazyky.

Další informace najdete v následujících knihovnách GitHubu:

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [Přejít](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
