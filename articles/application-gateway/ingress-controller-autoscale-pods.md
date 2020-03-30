---
title: Automatické škálování podů AKS s metrikami Azure Application Gateway
description: Tento článek obsahuje pokyny, jak škálovat pody back-endu AKS pomocí metrik aplikační brány a adaptéru Metrika Azure Kubernetes
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 1169ed0e9a2b970ee0e30d73ea20c87001b62786
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239446"
---
# <a name="autoscale-your-aks-pods-using-application-gateway-metrics-beta"></a>Automatické škálování podů AKS pomocí metrik aplikační brány (beta)

S tím, jak se zvyšuje příchozí provoz, je důležité škálovat aplikace na základě poptávky.

V následujícím kurzu vysvětlujeme, jak můžete použít `AvgRequestCountPerHealthyHost` metriku aplikační brány ke zvýšení kapacity aplikace. `AvgRequestCountPerHealthyHost`měří průměrné požadavky odeslané do konkrétního back-endu fondu a back-endu nastavení HTTP kombinace.

Budeme používat následující dvě složky:

* [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter)- Použijeme adaptér metriky k vystavení metriky aplikační brány prostřednictvím metrického serveru. Azure Kubernetes Metric Adapter je open source projekt v rámci Azure, podobně jako řadič příchozího přenosu dat aplikační brány. 
* [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler)- Budeme používat HPA používat metriky aplikační brány a zaměřit nasazení pro škálování.

## <a name="setting-up-azure-kubernetes-metric-adapter"></a>Nastavení metrického adaptéru Azure Kubernetes

1. Nejprve vytvoříme instanční objekt služby `Monitoring Reader` Azure AAD a přiřadíme mu přístup přes skupinu prostředků aplikační brány. 

    ```azurecli
        applicationGatewayGroupName="<application-gateway-group-id>"
        applicationGatewayGroupId=$(az group show -g $applicationGatewayGroupName -o tsv --query "id")
        az ad sp create-for-rbac -n "azure-k8s-metric-adapter-sp" --role "Monitoring Reader" --scopes applicationGatewayGroupId
    ```

1. Nyní nasadíme [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) pomocí hlavního povinného instančního objektu AAD vytvořeného výše.

    ```bash
    kubectl create namespace custom-metrics
    # use values from service principle created above to create secret
    kubectl create secret generic azure-k8s-metrics-adapter -n custom-metrics \
        --from-literal=azure-tenant-id=<tenantid> \
        --from-literal=azure-client-id=<clientid> \
        --from-literal=azure-client-secret=<secret>
    kubectl apply -f kubectl apply -f https://raw.githubusercontent.com/Azure/azure-k8s-metrics-adapter/master/deploy/adapter.yaml -n custom-metrics
    ```

1. Vytvoříme `ExternalMetric` zdroj s `appgw-request-count-metric`názvem . Tento prostředek dá pokyn adaptéru metriky, aby zpřístupnioval `AvgRequestCountPerHealthyHost` metriku pro `myApplicationGateway` prostředek ve `myResourceGroup` skupině prostředků. Toto `filter` pole můžete použít k cílení na konkrétní back-endový fond a nastavení HTTP back-endu v aplikační bráně.

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

Nyní můžete požádat na metrický server, abyste zjistili, zda se naše nová metrika nedostává do posudku:
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

## <a name="using-the-new-metric-to-scale-up-the-deployment"></a>Použití nové metriky k navýšení kapacity nasazení

Jakmile jsme schopni `appgw-request-count-metric` vystavit prostřednictvím serveru metriky, [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler) jsme připraveni použít k škálování našeho cílového nasazení.

V následujícím příkladu se zaměříme na ukázkové nasazení `aspnet`. Budeme škálovat lusky, když `appgw-request-count-metric` > 200 za `10` Pod až max lusků.

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

Otestujte si nastavení pomocí nástroje zátěžového testu, jako je apache bench:
```bash
ab -n10000 http://<applicaiton-gateway-ip-address>/
```

## <a name="next-steps"></a>Další kroky
- [**Poradce při potížích s řadičem přenosu dat**](ingress-controller-troubleshoot.md): Řešení problémů s řadičem příchozího přenosu dat.