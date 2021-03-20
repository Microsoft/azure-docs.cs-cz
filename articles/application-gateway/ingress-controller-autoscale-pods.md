---
title: Automatické škálování AKS s využitím metrik Azure Application Gateway
description: Tento článek poskytuje pokyny, jak škálovat AKS back-endu pomocí metrik Application Gateway a adaptéru Azure Kubernetes metric.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a8f015085baa8fffa6f208e9d8dd749e397c76c3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397429"
---
# <a name="autoscale-your-aks-pods-using-application-gateway-metrics-beta"></a>Automatické škálování AKSch lusků pomocí Application Gatewaych metrik (beta verze)

Vzhledem k tomu, že se příchozí provoz zvyšuje, je důležité škálovat aplikace na základě požadavků.

V následujícím kurzu vyvysvětlíme, jak můžete použít `AvgRequestCountPerHealthyHost` metriku Application Gateway k horizontálnímu navýšení kapacity aplikace. `AvgRequestCountPerHealthyHost` měří průměrné požadavky odeslané na konkrétní back-end fond a kombinaci nastavení back-endu HTTP.

Budeme používat tyto dvě komponenty:

* [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) – K vystavení metriky Application Gateway prostřednictvím serveru metrik budeme používat adaptér metriky. Adaptér metriky Azure Kubernetes je otevřený zdrojový projekt v Azure, podobně jako kontroler Application Gateway příchozího přenosu dat. 
* [`Horizontal Pod Autoscaler`](../aks/concepts-scale.md#horizontal-pod-autoscaler) – Použijeme HPA k použití metrik Application Gateway a cílem nasazení pro škálování.

## <a name="setting-up-azure-kubernetes-metric-adapter"></a>Nastavení adaptéru metriky Azure Kubernetes

1. Nejprve vytvoříme instanční objekt služby Azure AAD a přiřadíte mu `Monitoring Reader` přístup přes skupinu prostředků Application Gateway. 

    ```azurecli
        applicationGatewayGroupName="<application-gateway-group-id>"
        applicationGatewayGroupId=$(az group show -g $applicationGatewayGroupName -o tsv --query "id")
        az ad sp create-for-rbac -n "azure-k8s-metric-adapter-sp" --role "Monitoring Reader" --scopes applicationGatewayGroupId
    ```

1. Teď nasadíme [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) pomocí instančního objektu služby AAD, který jsme vytvořili výše.

    ```bash
    kubectl create namespace custom-metrics
    # use values from service principal created above to create secret
    kubectl create secret generic azure-k8s-metrics-adapter -n custom-metrics \
        --from-literal=azure-tenant-id=<tenantid> \
        --from-literal=azure-client-id=<clientid> \
        --from-literal=azure-client-secret=<secret>
    kubectl apply -f kubectl apply -f https://raw.githubusercontent.com/Azure/azure-k8s-metrics-adapter/master/deploy/adapter.yaml -n custom-metrics
    ```

1. Vytvoříme `ExternalMetric` prostředek s názvem `appgw-request-count-metric` . Tento prostředek instruuje adaptér metriky, aby vystavoval `AvgRequestCountPerHealthyHost` metriku pro `myApplicationGateway` prostředek ve `myResourceGroup` skupině prostředků. Pomocí tohoto pole můžete `filter` cílit na konkrétní back-end fond a nastavení back-endu http v Application Gateway.

    ```yaml
    apiVersion: azure.com/v1alpha2
    kind: ExternalMetric
    metadata:
    name: appgw-request-count-metric
    spec:
        type: azuremonitor
        azure:
            resourceGroup: myResourceGroup # replace with your application gateway's resource group name
            resourceName: myApplicationGateway # replace with your application gateway's name
            resourceProviderNamespace: Microsoft.Network
            resourceType: applicationGateways
        metric:
            metricName: AvgRequestCountPerHealthyHost
            aggregation: Average
            filter: BackendSettingsPool eq '<backend-pool-name>~<backend-http-setting-name>' # optional
    ```

Teď můžete na server metriky vytvořit žádost, abyste viděli, jestli je naše nová metrika vystavená:
```bash
kubectl get --raw "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric"
# Sample Output
# {
#   "kind": "ExternalMetricValueList",
#   "apiVersion": "external.metrics.k8s.io/v1beta1",
#   "metadata":
#     {
#       "selfLink": "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric",
#     },
#   "items":
#     [
#       {
#         "metricName": "appgw-request-count-metric",
#         "metricLabels": null,
#         "timestamp": "2019-11-05T00:18:51Z",
#         "value": "30",
#       },
#     ],
# }
```

## <a name="using-the-new-metric-to-scale-up-the-deployment"></a>Použití nové metriky k horizontálnímu navýšení kapacity nasazení

Až bude možné vystavit `appgw-request-count-metric` prostřednictvím serveru metrik, můžeme použít [`Horizontal Pod Autoscaler`](../aks/concepts-scale.md#horizontal-pod-autoscaler) k horizontálnímu navýšení kapacity cílového nasazení.

V následujícím příkladu budeme cílit na ukázkové nasazení `aspnet` . Až `appgw-request-count-metric` do maximálního počtu lusků nasadíme > 200 na `10` .

Nahraďte název cílového nasazení a použijte následující konfiguraci automatického škálování:
```yaml
apiVersion: autoscaling/v2beta1
kind: HorizontalPodAutoscaler
metadata:
  name: deployment-scaler
spec:
  scaleTargetRef:
    apiVersion: extensions/v1beta1
    kind: Deployment
    name: aspnet # replace with your deployment's name
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: External
    external:
      metricName: appgw-request-count-metric
      targetAverageValue: 200
```

Otestujte nastavení pomocí nástroje zátěžového testu, jako je Apache.
```bash
ab -n10000 http://<applicaiton-gateway-ip-address>/
```

## <a name="next-steps"></a>Další kroky
- [**Řešení potíží s řadičem**](ingress-controller-troubleshoot.md)příchozích dat: řešení potíží s řadičem příchozího přenosu dat.