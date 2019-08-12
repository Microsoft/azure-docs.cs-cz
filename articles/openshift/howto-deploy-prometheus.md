---
title: Nasadit samostatnou instanci Prometheus v clusteru Azure Red Hat OpenShift | Dokumentace Microsoftu
description: Vytvoření Prometheus instance v clusteru Azure Red Hat OpenShift monitorovat metriky vaší aplikace.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus aro, openshift, red hat, metriky
ms.openlocfilehash: a9748932a72106413677b21fe0efd1f69fb02e47
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827021"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>Nasadit samostatnou instanci Prometheus v clusteru Azure Red Hat OpenShift

Tento článek popisuje, jak nakonfigurovat samostatnou instanci Prometheus používající zjišťování služby v clusteru Azure Red Hat OpenShift.

> [!NOTE]
> Zákazník přístup správce ke clusteru Azure Red Hat OpenShift není povinné.

Cíl instalace:

- Jeden projekt (prometheus – projekt), která obsahuje Prometheus a Alertmanager.
- Dva projekty (project1 aplikace a aplikace "project2"), které obsahují aplikace, které chcete monitorovat.

Připravíte budete některé Prometheus config soubory místně. Vytvořte novou složku, kam ji uložit. Konfigurační soubory se ukládají v clusteru jako tajné kódy, v případě, že tajný tokeny později přidaného do clusteru.

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>Přihlaste se ke clusteru pomocí nástroje OS

1. Otevřete webový prohlížeč a přejděte k webové konzole ve vašem clusteru (https://openshift. *náhodné id*. *oblast*. azmosa.io).
2. Přihlaste se pomocí přihlašovacích údajů Azure.
3. Vyberte své uživatelské jméno v pravém horním rohu a pak vyberte **kopírování přihlašovací příkaz**.
4. Vložte své uživatelské jméno do terminálu, které budete používat.

> [!NOTE]
> Chcete-li zobrazit, pokud jste přihlášeni ke správné clusteru, spusťte `oc whoami -c` příkazu.

## <a name="prepare-the-projects"></a>Příprava projekty

Pokud chcete vytvořit projekty, spusťte následující příkazy:
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> Můžete použít `-n` nebo `--namespace` parametru nebo vyberte nějaký aktivní projekt spuštěním `oc project` příkazu.

## <a name="prepare-the-prometheus-configuration-file"></a>Příprava Prometheus konfigurační soubor
Vytvořte soubor prometheus.yml tak, že zadáte následující obsah:
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
Vytvoření tajného kódu volá Prom tak, že zadáte následující konfiguraci:
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

Soubor prometheus.yml je základní Prometheus konfiguračního souboru. Nastaví intervaly a nakonfiguruje automatické zjišťování v tří projektů (prometheus projektu, project1 aplikace, aplikace "project2"). V konfiguračním souboru jsou automaticky zjistit koncové body získaný prostřednictvím protokolu HTTP bez ověřování.

Další informace o automatizované získávání dat koncové body, naleznete v tématu [Prometheus šířku config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config).


## <a name="prepare-the-alertmanager-config-file"></a>Příprava Alertmanager konfiguračního souboru
Vytvořte soubor alertmanager.yml tak, že zadáte následující obsah:
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
Vytvoření tajného kódu volá Prom výstrah tak, že zadáte následující konfiguraci:
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager.yml je konfigurační soubor Správce oznámení.

> [!NOTE]
> Pokud chcete ověřit předchozí dva kroky, spusťte `oc get secret -n prometheus-project` příkazu.

## <a name="start-prometheus-and-alertmanager"></a>Spustit Prometheus a Alertmanager
Přejděte na [úložiště openshift/zdroje](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) a stáhněte si [prometheus standalone.yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) šablony. Použijte šablonu projektu prometheus tak, že zadáte následující konfiguraci:
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Soubor prometheus standalone.yaml je OpenShift šablona. Vytvoří instanci Prometheus pomocí oauth proxy před jeho a instanci Alertmanager také zabezpečený pomocí oauth proxy. V této šabloně oauth proxy nakonfigurována, aby umožňovala každý uživatel, který může "get" názvů prometheus projektu (viz `-openshift-sar` příznak).

> [!NOTE]
> Chcete-li zkontrolovat, jestli má prom StatefulSet rovná požadovaná a aktuální počet replik, `oc get statefulset -n prometheus-project` příkazu. Chcete-li zkontrolovat všechny prostředky v projektu, spusťte `oc get all -n prometheus-project` příkazu.

## <a name="add-permissions-to-allow-service-discovery"></a>Přidání oprávnění pro povolení zjišťování služby

Vytvořte soubor prometheus sdrole.yml tak, že zadáte následující obsah:
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
Použít šablonu pro všechny projekty, které chcete povolit zjišťování služby, spusťte následující příkazy:
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
```
Pokud chcete, aby Prometheus shromažďovat metriky, od sebe sama, přiřaďte oprávnění v prometheus projektu.

> [!NOTE]
> Chcete-li ověřit, zda byly správně vytvořena Role a RoleBinding, spusťte `oc get role` a `oc get rolebinding` příkazy.

## <a name="optional-deploy-example-application"></a>Volitelné: Nasazení ukázkové aplikace

Všechno funguje, ale neexistují žádné metriky zdroje. Přejděte na adresu URL Prometheus (https://prom-prometheus-project.apps.*náhodné id*.*oblast*.azmosa.io/). Najdete ho s použitím následující příkaz:

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> Nezapomeňte přidat předponu https:// na začátku názvu hostitele.

**Stav > zjišťování služby** stránka zobrazí aktivní cíle 0/0.

Pokud chcete nasadit ukázková aplikace, které zpřístupňuje základní metriky Pythonu v rámci /metrics koncový bod, spusťte následující příkazy:
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
Nové aplikace by se zobrazit jako platné cíle na stránce zjišťování služby během 30 sekund po nasazení.

Další podrobnosti, vyberte možnost **stav** > **cíle**.

> [!NOTE]
> Pro každý cíl úspěšně scraped Prometheus přidá na datový bod v aktuálním metriku. Vyberte **Prometheus** v levém horním rohu, zadejte **nahoru** jako výraz a pak vyberte **Execute**.

## <a name="next-steps"></a>Další postup

Můžete přidat vlastní instrumentaci Prometheus pro vaše aplikace. Prometheus klientské knihovny, který zjednodušuje Prometheus metriky přípravy, je připraven pro různé programovací jazyky.

Další informace najdete v následující knihovny Githubu:

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [Go](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
