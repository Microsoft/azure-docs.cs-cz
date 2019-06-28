---
title: Nasadit samostatný Prometheus v clusteru Azure Red Hat OpenShift | Dokumentace Microsoftu
description: Tady je postup pro vytvoření Prometheus instance v clusteru Azure Red Hat OpenShift monitorovat metriky vaší aplikace.
author: Makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus aro, openshift, red hat, metriky
ms.openlocfilehash: e66658151361edd43f61d398274c88c047928028
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342753"
---
# <a name="deploy-a-standalone-prometheus-in-an-azure-red-hat-openshift-cluster"></a>Nasadit samostatný Prometheus v clusteru Azure Red Hat OpenShift

Tato příručka popisuje, jak nakonfigurovat samostatnou Prometheus zjišťování služby v clusteru Azure Red Hat OpenShift.  "Zákazník admin" přístup ke clusteru se nevyžaduje.

Nastavení cílové vypadá takto:

- jednoho projektu (prometheus – projekt), který obsahuje Prometheus a Alertmanager
- dva projekty (project1 aplikace a aplikace "project2"), které obsahují aplikace, které chcete monitorovat

Připravíte budete některé Prometheus konfigurační soubory místně. Vytvořte novou složku, kam ji uložit.
Tyto konfigurační soubory se budou ukládat v clusteru jako tajné kódy v případě, že tajný tokeny jsou k nim přidány později.

## <a name="step-1-sign-in-to-the-cluster-using-the-oc-tool"></a>Krok 1: Přihlaste se ke clusteru pomocí `oc` nástroj
Pomocí webového prohlížeče, přejděte k webové konzole ve vašem clusteru (https://openshift. *náhodné id*. *oblast*. azmosa.io).
Přihlaste se pomocí přihlašovacích údajů Azure.
Klikněte na své uživatelské jméno v pravém horním rohu a vyberte "Kopie přihlašovací příkaz". Vložte ho do terminálu, který budete používat.

Můžete ověřit, pokud jste přihlášeni ke správné clusteru pomocí `oc whoami -c` příkazu.

## <a name="step-2-prepare-the-projects"></a>Krok 2: Příprava projekty

Vytváření projektů.
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> Můžete použít `-n` nebo `--namespace` parametru nebo vyberte aktivní projekt se `oc project` příkaz

## <a name="step-3-prepare-prometheus-config"></a>Krok 3: Příprava Prometheus config
Vytvořte soubor s názvem prometheus.yml s následujícím obsahem.
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
Vytvoření tajného kódu s názvem "prom" s konfigurací.
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

Soubor uvedených výše je základní Prometheus konfiguračním souboru.
Nastaví intervaly a nakonfiguruje automatické zjišťování v tří projektů (prometheus projektu, project1 aplikace, aplikace "project2").
V tomto příkladu automaticky zjištěné koncové body se získaný prostřednictvím protokolu HTTP bez ověřování.
Další informace o automatizované získávání dat koncové body, naleznete v tématu https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config.


## <a name="step-4-prepare-alertmanager-config"></a>Krok 4: Příprava Alertmanager config
Vytvořte soubor s názvem alertmanager.yml s následujícím obsahem.
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
Vytvoření tajného kódu s názvem "prom – výstrahy" s konfigurací.
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Soubor uvedených výše je konfigurační soubor Správce oznámení.

> [!NOTE]
> Můžete ověřit předchozí dva kroky s `oc get secret -n prometheus-project`

## <a name="step-5-start-prometheus-and-alertmanager"></a>Krok 5: Spustit Prometheus a Alertmanager
Stáhněte si [prometheus standalone.yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) šablonu z [úložiště openshift/zdroje](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) a použít v projektu prometheus
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Soubor prometheus standalone.yaml je OpenShift šablona, která vytvoří instanci Prometheus s proxy oauth před jeho a instanci Alertmanager také zabezpečený pomocí oauth proxy.  V této šabloně oauth proxy nakonfigurována, aby umožňovala každý uživatel, který může "get" oboru názvů "prometheus projekt" (Zobrazit `-openshift-sar` příznak).

> [!NOTE]
> Můžete ověřit, jestli má stejné "prom" StatefulSet *požadovaná* a *aktuální* počet replik s `oc get statefulset -n prometheus-project` příkazu.
> Můžete také zkontrolovat všechny prostředky v projektu s `oc get all -n prometheus-project`.

## <a name="step-6-add-permissions-to-allow-service-discovery"></a>Krok 6: Přidání oprávnění pro povolení zjišťování služby
Vytvoření prometheus sdrole.yml s následujícím obsahem.
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus Service Discovery Role
    description: |
      A role and rolebinding adding permissions required to perform Service Discovery in a given project.
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
Použijte šablonu pro všechny projekty, ve kterém chcete povolit zjišťování služby.
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
```
Pokud chcete také Prometheus moct shromažďovat metriky, od sebe sama, nezapomeňte použít oprávnění v projektu prometheus příliš.

> [!NOTE]
> Pokud Role a RoleBinding byly správně vytvořeny pomocí můžete ověřit `oc get role` a `oc get rolebinding` příkazy v uvedeném pořadí

## <a name="optional-deploy-example-application"></a>Volitelné: Nasazení ukázkové aplikace
Všechno funguje, ale neexistují žádné metriky zdroje. Přejděte na adresu URL Prometheus (https://prom-prometheus-project.apps. *náhodné id*. *oblast*.azmosa.io/), kterou lze nalézt pomocí následujícího příkazu.
```
oc get route prom -n prometheus-project
```
Mějte na paměti jako předpona názvu hostitele https://.

**Stav > zjišťování služby** stránka zobrazí aktivní cíle 0/0.

Pokud chcete nasadit ukázková aplikace, které zpřístupňuje základní python metrik v části /metrics endpoint spusťte následující příkazy.
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
Nová aplikace se zobrazí jako platné cíle na stránce zjišťování služby v rámci 30s po nasazení. Další podrobnosti získáte **Stav > cíle** stránky.

> [!NOTE]
> Pro každého úspěšně scraped cíle Prometheus přidá element datapoint v metrice "až". Klikněte na tlačítko **Prometheus** vlevo nahoře rohu, zadejte jako výraz a klikněte na tlačítko "nahoru" **Execute**.

## <a name="next-steps"></a>Další postup

Můžete přidat vlastní instrumentaci Prometheus pro vaše aplikace.

Prometheus klientské knihovny, který zjednodušuje Příprava Prometheus metriky, je připraven pro různé programovací jazyky.

 - Java https://github.com/prometheus/client_java
 - Python https://github.com/prometheus/client_python
 - Go https://github.com/prometheus/client_golang
 - Ruby https://github.com/prometheus/client_ruby
