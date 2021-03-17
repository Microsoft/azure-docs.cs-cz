---
title: Kurz – monitorování Apache Spark metrik na úrovni aplikace pomocí Prometheus a Grafana
description: Kurz – Přečtěte si, jak nasadit řešení metriky aplikace Apache Spark do clusteru Azure Kubernetes Service (AKS) a Naučte se integrovat řídicí panely Grafana.
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 01/22/2021
ms.openlocfilehash: 7a95b32449da6dead1c35241ff09af127e0fae43
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102210133"
---
# <a name="tutorial-monitor-apache-spark-application-level-metrics-with-prometheus-and-grafana"></a>Kurz: monitorování Apache Spark metrik na úrovni aplikace pomocí Prometheus a Grafana

## <a name="overview"></a>Přehled

V tomto kurzu se naučíte, jak nasadit řešení metriky aplikace Apache Spark do clusteru Azure Kubernetes Service (AKS) a Naučte se integrovat řídicí panely Grafana.

Toto řešení můžete použít ke shromáždění a dotazování dat metrik Apache Spark v reálném čase. Integrované řídicí panely Grafana umožňují diagnostikovat a monitorovat aplikaci Apache Spark. Zdrojový kód a konfigurace byly otevřeny na GitHubu.

## <a name="prerequisites"></a>Předpoklady

1.  [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
2.  [Klient Helm 3.30 +](https://github.com/helm/helm/releases)
3.  [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
4.  [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/en-us/services/kubernetes-service/)

Nebo použijte [Azure Cloud Shell](https://shell.azure.com/), který už obsahuje klienta Azure CLI, klienta Helm a kubectl.

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.

```bash
az login
az account set --subscription "<subscription_id>"
```

## <a name="create-an-azure-kubernetes-service-instance-aks"></a>Vytvoření instance služby Azure Kubernetes (AKS)

Pomocí příkazu Azure CLI vytvořte v předplatném cluster Kubernetes.

```
az aks create --name <kubernetes_name> --resource-group <kubernetes_resource_group> --location <location> --node-vm-size Standard_D2s_v3
az aks get-credentials --name <kubernetes_name> --resource-group <kubernetes_resource_group>
```

Poznámka: Tento krok se dá přeskočit, pokud už máte cluster AKS.

## <a name="create-a-service-principal-and-grant-permission-to-synapse-workspace"></a>Vytvoření instančního objektu a udělení oprávnění synapse pracovnímu prostoru

```bash
az ad sp create-for-rbac --name <service_principal_name>
```

Výsledek by měl vypadat takto:

```json
{
  "appId": "abcdef...",
  "displayName": "<service_principal_name>",
  "name": "http://<service_principal_name>",
  "password": "abc....",
  "tenant": "<tenant_id>"
}
```

Poznamenejte si appId, Password a tenantID.

[![srbac oprávnění udělení obrazovky](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png#lightbox)

1. Přihlaste se ke svému [pracovnímu prostoru Azure synapse Analytics](https://web.azuresynapse.net/) jako správce synapse.

2. V synapse studiu v levém podokně vyberte **spravovat > řízení přístupu** .

3. Kliknutím na tlačítko Přidat v levém horním rohu **přidáte přiřazení role** .

4. Pro rozsah vyberte **pracovní prostor** .

5. V případě role vyberte **operátor synapse COMPUTE** .

6. Pro možnost vybrat uživatele zadejte **<service_principal_name>** a klikněte na instanční objekt.

7. Klikněte na **použít** (počkejte 3 minuty, než se oprávnění projeví.)

## <a name="install-connector-prometheus-server-grafana-dashboard"></a>Nainstalovat konektor, server Prometheus, řídicí panel Grafana

1. Přidejte úložiště synapse-Charts do klienta Helm.

```bash
helm repo add synapse-charts https://github.com/microsoft/azure-synapse-spark-metrics/releases/download/helm-chart
```

2.  Instalovat součásti prostřednictvím klienta Helm:

```bash
helm install spo synapse-charts/synapse-prometheus-operator --create-namespace --namespace spo \
    --set synapse.workspaces[0].workspace_name="<workspace_name>" \
    --set synapse.workspaces[0].tenant_id="<tenant_id>" \
    --set synapse.workspaces[0].service_principal_name="<service_principal_app_id>" \
    --set synapse.workspaces[0].service_principal_password="<service_principal_password>" \
    --set synapse.workspaces[0].subscription_id="<subscription_id>" \
    --set synapse.workspaces[0].resource_group="<workspace_resource_group_name>"
```

 - workspace_name: název pracovního prostoru synapse.
 - subscription_id: ID předplatného synapse pracovního prostoru.
 - workspace_resource_group_name: název skupiny prostředků synapse pracovního prostoru.
 - tenant_id: ID tenanta synapse pracovního prostoru.
 - service_principal_app_id: instanční objekt "appId"
 - service_principal_password: heslo instančního objektu, které jste vytvořili.

## <a name="log-in-to-grafana"></a>Přihlášení k Grafana

Získejte výchozí heslo a adresu Grafana. Heslo můžete změnit v nastavení Grafana.

```bash
kubectl get secret --namespace spo spo-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
kubectl -n spo get svc spo-grafana
```

Získejte IP adresu služby, zkopírujte & vložte externí IP adresu do prohlížeče a přihlaste se pomocí uživatelského jména "admin" a heslo.

## <a name="use-grafana-dashboards"></a>Použití řídicích panelů Grafana

V levém horním rohu stránky Grafana Najděte řídicí panel synapse (pracovní prostor pro Home-> synapse/synapse), zkuste spustit vzorový kód v synapse studiu a počkejte několik sekund, než se metriky najdou.

K získání přehledu o vašem pracovním prostoru a fondech Apache Spark můžete také použít řídicí panely pracovní prostor (synapse) a pracovní prostor synapse a fondy Sparku.

## <a name="uninstall"></a>Odinstalovat

Odeberte součásti pomocí příkazu Helm následujícím způsobem.

```bash
helm delete <release_name> -n <namespace>
```

Odstraňte cluster AKS.

```bash
az aks delete --name <kubernetes_cluster_name> --resource-group <kubernetes_cluster_rg>
```

## <a name="components-introduction"></a>Úvod k součástem

Azure synapse Analytics poskytuje [graf Helm](https://github.com/microsoft/azure-synapse-spark-metrics/tree/main/helm) založený na operátoru Prometheus a konektoru synapse Prometheus. Graf Helm zahrnuje Prometheus Server, server Grafana a řídicí panely Grafana pro Apache Spark metriky na úrovni aplikace. K shromažďování těchto metrik téměř v reálném čase můžete využít [Prometheus](https://prometheus.io/), oblíbený Open Source monitorovací systém a používat [Grafana](https://github.com/grafana/grafana) pro vizualizaci.

### <a name="synapse-prometheus-connector"></a>Konektor synapse Prometheus

Konektor synapse Prometheus pomáhá propojit Azure synapse Apache Spark fond a server Prometheus. Implementuje:

1.  Ověřování: Jedná se o ověřování založené na AAD a může automaticky aktualizovat token AAD instančního objektu pro zjišťování aplikací, přijímání metrik a další funkce.
2.  Apache Spark zjišťování aplikací: když v cílovém pracovním prostoru odešlete aplikace, konektor synapse Prometheus může automaticky zjistit tyto aplikace.
3.  Apache Spark metadata aplikace: shromažďuje základní informace o aplikaci a exportuje data do Prometheus.

Konektor synapse Prometheus je vydaný jako image Docker hostovaná v [Microsoft Container Registry](https://github.com/microsoft/containerregistry). Je to open source a nachází se v [metrikách aplikací Azure synapse Spark](https://github.com/microsoft/azure-synapse-spark-metrics).

### <a name="prometheus-server"></a>Server Prometheus

Prometheus je open source sada nástrojů pro monitorování a upozorňování. Prometheus se od cloudu Native Computing Foundation (CNCF) a stala se de facto standard pro monitorování nativního cloudu. Prometheus může nám přispět ke shromažďování, dotazování a ukládání velkých objemů dat časových řad a dá se snadno integrovat s Grafana. V tomto řešení nasadíme komponentu Prometheus založenou na grafu Helm.

### <a name="grafana-and-dashboards"></a>Grafana a řídicí panely

Grafana je open source vizualizace a analytický software. Umožňuje dotazování, vizualizaci, upozorňování a prozkoumání metrik. Azure synapse Analytics poskytuje sadu výchozích řídicích panelů Grafana pro vizualizaci Apache Spark metrik na úrovni aplikace.

Řídicí panel "pracovní prostor synapse/pracovní prostor" poskytuje zobrazení na úrovni pracovního prostoru pro všechny fondy Apache Spark, počty aplikací, jádra procesoru atd.

[![pracovní prostor řídicího panelu snímků obrazovky](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-workspace.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-workspace.png#lightbox)

Řídicí panel "synapse pracovní prostor/Spark" obsahuje metriky Apache Spark aplikací spuštěných ve vybraném fondu Apache Spark během tohoto časového období.

[![řídicí panel obrazovky sparkpool](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-sparkpool.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-sparkpool.png#lightbox)

Řídicí panel "pracovní prostor synapse/aplikace Spark" obsahuje vybranou aplikaci Apache Spark.

[![aplikace řídicího panelu snímků obrazovky](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-application.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-application.png#lightbox)

Výše uvedené šablony řídicích panelů jsou otevřené – zdrojové [metriky aplikací Azure synapse Spark](https://github.com/microsoft/azure-synapse-spark-metrics/tree/main/helm/synapse-prometheus-operator/grafana_dashboards).
