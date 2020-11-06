---
title: Řešení potíží s Application Gatewaym řadičem příchozího přenosu dat
description: Tento článek poskytuje dokumentaci, jak řešit běžné otázky a problémy s Application Gatewaym řadičem příchozího přenosu dat.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 06/18/2020
ms.author: caya
ms.openlocfilehash: d6bcb9125cdfc07eb249353cb85b40a22d3e468c
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397361"
---
# <a name="troubleshoot-common-questions-or-issues-with-ingress-controller"></a>Řešení běžných otázek nebo potíží s řadičem příchozího přenosu dat

[Azure Cloud Shell](https://shell.azure.com/) je nejpohodlnější způsob, jak řešit problémy s instalací nástroje AKS a AGIC. Spusťte prostředí z [Shell.Azure.com](https://shell.azure.com/) nebo kliknutím na odkaz:

[![Vložit spuštění](https://shell.azure.com/images/launchcloudshell.png "Spuštění služby Azure Cloud Shell")](https://shell.azure.com)


## <a name="test-with-a-simple-kubernetes-app"></a>Testování pomocí jednoduché aplikace Kubernetes

Následující postup předpokládá:
  - Máte cluster AKS s povolenými pokročilými sítěmi
  - AGIC je nainstalovaný v clusteru AKS.
  - Už máte Application Gateway ve virtuální síti sdílené s vaším clusterem AKS.

Chcete-li ověřit, zda je instalace Application Gateway + AKS + AGIC správně nastavena, nasaďte nejjednodušší možnou aplikaci:

```bash
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: test-agic-app-pod
  labels:
    app: test-agic-app
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP
---
apiVersion: v1
kind: Service
metadata:
  name: test-agic-app-service
spec:
  selector:
    app: test-agic-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test-agic-app-ingress
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: test.agic.contoso.com
      http:
        paths:
          - path: /
            backend:
              serviceName: test-agic-app-service
              servicePort: 80
EOF
```

Zkopírujte a vložte všechny řádky z výše uvedeného skriptu do [Azure Cloud Shell](https://shell.azure.com/). Ujistěte se prosím, že se celý příkaz zkopíroval – počínaje `cat` a včetně posledního `EOF` .

![apply](./media/application-gateway-ingress-controller-troubleshooting/tsg--apply-config.png)

Po úspěšném nasazení aplikace nad cluster AKS bude k dispozici nový pod, služba a příchozí přenos dat.

Seznam lusků získáte pomocí [Cloud Shell](https://shell.azure.com/): `kubectl get pods -o wide` .
Očekáváme, že se vytvoří název pod názvem test-agic-App-pod. Bude mít IP adresu. Tato adresa musí být v rámci virtuální sítě Application Gateway, která se používá s AKS.

![Snímek obrazovky okna bash v Azure Cloud Shell zobrazující seznam lusků, který zahrnuje test-agic-App-pod v seznamu.](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-pods.png)

Získat seznam služeb: `kubectl get services -o wide` . Očekáváme, že se zobrazí služba s názvem test-agic-App-Service.

![Snímek obrazovky okna bash v Azure Cloud Shell zobrazující seznam služeb, které obsahují test-agic-App-pod v seznamu.](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-services.png)

Získat seznam příchozích dat: `kubectl get ingress` . Očekáváme, že prostředek příchozího přenosu s názvem test-agic-App-příchozí je vytvořený. Prostředek bude mít název hostitele ' test.agic.contoso.com '.

![Snímek obrazovky okna bash v Azure Cloud Shell zobrazující seznam vstupů, které v seznamu obsahují test-agic-App-in.](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-ingress.png)

Jedna z lusků bude AGIC. `kubectl get pods` Zobrazí seznam lusků, z nichž jedna začíná "příchozí – Azure". Získejte všechny protokoly, které jsou pod nástrojem, `kubectl logs <name-of-ingress-controller-pod>` a ověřte tak, že máme úspěšné nasazení. Úspěšné nasazení by do protokolu přidalo následující řádky:
```
I0927 22:34:51.281437       1 process.go:156] Applied Application Gateway config in 20.461335266s
I0927 22:34:51.281585       1 process.go:165] cache: Updated with latest applied config.
I0927 22:34:51.282342       1 process.go:171] END AppGateway deployment
```

Případně můžete z [Cloud Shell](https://shell.azure.com/) načíst pouze řádky indikující úspěšnou konfiguraci Application Gateway pomocí `kubectl logs <ingress-azure-....> | grep 'Applied App Gateway config in'` , kde `<ingress-azure....>` by měl být přesný název AGIC pod.

Application Gateway bude použita následující konfigurace:

- Naslouchací proces: ![ naslouchací proces](./media/application-gateway-ingress-controller-troubleshooting/tsg--listeners.png)

- Pravidlo směrování: ![ routing_rule](./media/application-gateway-ingress-controller-troubleshooting/tsg--rule.png)

- Back-end fond:
  - Ve fondu back-end adres bude jedna IP adresa a bude se shodovat s IP adresou, kterou jsme dříve zjistili pomocí `kubectl get pods -o wide` 
 ![ backend_pool](./media/application-gateway-ingress-controller-troubleshooting/tsg--backendpools.png)


Nakonec můžeme pomocí příkazu v `cURL` rámci [Cloud Shell](https://shell.azure.com/) navázat připojení HTTP k nově nasazené aplikaci:

1. Použijte `kubectl get ingress` k získání veřejné IP adresy Application Gateway
2. Použití `curl -I -H 'test.agic.contoso.com' <publitc-ip-address-from-previous-command>`

![Snímek obrazovky okna bash v Azure Cloud Shell znázorňující příkaz složeného příkazu se úspěšně navázáním připojení HTTP k testovací aplikaci.](./media/application-gateway-ingress-controller-troubleshooting/tsg--curl.png)

Výsledek `HTTP/1.1 200 OK` znamená, že systém Application Gateway + AKS + AGIC pracuje podle očekávání.


## <a name="inspect-kubernetes-installation"></a>Kontrola instalace Kubernetes

### <a name="pods-services-ingress"></a>Lusky, služby, příchozí přenosy
Kontroler příchozího přenosu Application Gateway (AGIC) nepřetržitě sleduje tyto Kubernetes prostředky: [nasazení](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#creating-a-deployment) nebo [pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/#what-is-a-pod), [službu](https://kubernetes.io/docs/concepts/services-networking/service/), příchozí [přenosy](https://kubernetes.io/docs/concepts/services-networking/ingress/)


Aby funkce AGIC fungovala podle očekávání, musí být na tomto místě:
  1. AKS musí mít jednu nebo více zdravých **lusků**.
     Ověřte to z [Cloud Shell](https://shell.azure.com/) `kubectl get pods -o wide --show-labels` , pokud máte pod s `apsnetapp` , váš výstup může vypadat takto:
     ```bash
     delyan@Azure:~$ kubectl get pods -o wide --show-labels

     NAME                   READY   STATUS    RESTARTS   AGE   IP          NODE                       NOMINATED NODE   READINESS GATES   LABELS
     aspnetapp              1/1     Running   0          17h   10.0.0.6    aks-agentpool-35064155-1   <none>           <none>            app=aspnetapp
     ```

  2. Jednu nebo více **služeb** s odkazem na lusky přes párové `selector` popisky.
     Ověřte tuto z [Cloud Shell](https://shell.azure.com/) s `kubectl get services -o wide`
     ```bash
     delyan@Azure:~$ kubectl get services -o wide --show-labels

     NAME                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE   SELECTOR        LABELS
     aspnetapp           ClusterIP   10.2.63.254    <none>        80/TCP    17h   app=aspnetapp   <none>     
     ```

  3. **Ingress** Příchozí s poznámkami s `kubernetes.io/ingress.class: azure/application-gateway` odkazem na výše uvedenou službu ověřte tuto [Cloud Shell](https://shell.azure.com/) s`kubectl get ingress -o wide --show-labels`
     ```bash
     delyan@Azure:~$ kubectl get ingress -o wide --show-labels

     NAME        HOSTS   ADDRESS   PORTS   AGE   LABELS
     aspnetapp   *                 80      17h   <none>
     ```

  4. Zobrazit poznámky k příchozímu přenosu dat výše: `kubectl get ingress aspnetapp -o yaml` (nahraďte názvem vašeho příchozího přenosu dat. `aspnetapp` )
     ```bash
     delyan@Azure:~$ kubectl get ingress aspnetapp -o yaml

     apiVersion: extensions/v1beta1
     kind: Ingress
     metadata:
       annotations:
         kubernetes.io/ingress.class: azure/application-gateway
       name: aspnetapp
     spec:
       backend:
         serviceName: aspnetapp
         servicePort: 80
     ```

     K prostředku příchozího přenosu dat je nutné zadat poznámku `kubernetes.io/ingress.class: azure/application-gateway` .
 

### <a name="verify-observed-namespace"></a>Ověřit pozorovaný obor názvů

* Získejte existující obory názvů v clusteru Kubernetes. V jakém oboru názvů vaše aplikace běží? Sleduje tento obor názvů AGIC? Informace o tom, jak správně konfigurovat pozorované obory názvů, najdete v dokumentaci k [podpoře více oborů názvů](./ingress-controller-multiple-namespace-support.md#enable-multiple-namespace-support) .

    ```bash
    # What namespaces exist on your cluster
    kubectl get namespaces
    
    # What pods are currently running
    kubectl get pods --all-namespaces -o wide
    ```


* AGIC pod by měl být v `default` oboru názvů (viz sloupec `NAMESPACE` ). Ve sloupci by měl být dobrý stav `Running` `STATUS` . Měl by existovat alespoň jeden AGIC pod.

    ```bash
    # Get a list of the Application Gateway Ingress Controller pods
    kubectl get pods --all-namespaces --selector app=ingress-azure
    ```


* Pokud AGIC pod není v pořádku ( `STATUS` sloupec z příkazu výše není `Running` ):
  - Získejte protokoly, abyste zjistili, proč: `kubectl logs <pod-name>`
  - pro předchozí instanci rozhraní pod: `kubectl logs <pod-name> --previous`
  - Popište pole pod, aby se získalo více kontextu: `kubectl describe pod <pod-name>`


* Máte [službu](https://kubernetes.io/docs/concepts/services-networking/service/) [Kubernetes a prostředky](https://kubernetes.io/docs/concepts/services-networking/ingress/) příchozího přenosu dat?
    
    ```bash
    # Get all services across all namespaces
    kubectl get service --all-namespaces -o wide
    
    # Get all ingress resources across all namespaces
    kubectl get ingress --all-namespaces -o wide
    ```


* [Máte k](https://kubernetes.io/docs/concepts/services-networking/ingress/) disznámce poznámky: `kubernetes.io/ingress.class: azure/application-gateway` ? AGIC se bude sledovat jenom u prostředků Kubernetes příchozího přenosu dat, které mají tuto poznámku.
    
    ```bash
    # Get the YAML definition of a particular ingress resource
    kubectl get ingress --namespace  <which-namespace?>  <which-ingress?>  -o yaml
    ```


* AGIC generuje události Kubernetes pro určité kritické chyby. Můžete je zobrazit:
  - v terminálu prostřednictvím `kubectl get events --sort-by=.metadata.creationTimestamp`
  - v prohlížeči pomocí [webového uživatelského rozhraní Kubernetes (řídicí panel)](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)


## <a name="logging-levels"></a>Úrovně protokolování

AGIC má 3 úrovně protokolování. První úroveň je výchozí a zobrazuje minimální počet řádků protokolu.
Úroveň 5 na druhé straně by zobrazila všechny protokoly, včetně upraveného obsahu konfigurace použitého pro ARM.

Komunita Kubernetes zřídila 9 úrovní protokolování pro nástroj [kubectl](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#kubectl-output-verbosity-and-debugging) . V tomto úložišti využíváme 3 z těchto verzí s podobnou sémantikou:


| Podrobnosti | Popis |
|-----------|-------------|
|  1        | Výchozí úroveň protokolu; zobrazuje podrobnosti o spuštění, upozornění a chyby. |
|  3        | Rozšířené informace o událostech a změnách; seznamy vytvořených objektů |
|  5        | Zařazování objektů do protokolu; zobrazuje upravenou konfiguraci JSON použitou pro ARM. |


Úrovně podrobností lze nastavit přes `verbosityLevel` proměnnou v souboru [Helm-config. yaml](#sample-helm-config-file) . Zvyšte úroveň podrobností na, `5` aby se získala konfigurace JSON odeslané do [ARM](../azure-resource-manager/management/overview.md):
  - Přidat `verbosityLevel: 5` na sebe sebe v [Helm-config. yaml](#sample-helm-config-file) a znovu nainstalovat
  - získat protokoly pomocí `kubectl logs <pod-name>`

### <a name="sample-helm-config-file"></a>Ukázkový konfigurační soubor Helm
```yaml
# This file contains the essential configs for the ingress controller helm chart

# Verbosity level of the App Gateway Ingress Controller
verbosityLevel: 3

################################################################################
# Specify which application gateway the ingress controller will manage
#
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>

    # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
    # This prohibits AGIC from applying config for any host/path.
    # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
    shared: false

################################################################################
# Specify which kubernetes namespace the ingress controller will watch
# Default value is "default"
# Leaving this variable out or setting it to blank or empty string would
# result in Ingress Controller observing all acessible namespaces.
#
# kubernetes:
#   watchNamespace: <namespace>

################################################################################
# Specify the authentication with Azure Resource Manager
#
# Two authentication methods are available:
# - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
armAuth:
    type: aadPodIdentity
    identityResourceID: <identityResourceId>
    identityClientID:  <identityClientId>

## Alternatively you can use Service Principal credentials
# armAuth:
#    type: servicePrincipal
#    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>

################################################################################
# Specify if the cluster is RBAC enabled or not
rbac:
    enabled: false # true/false

# Specify aks cluster related information. THIS IS BEING DEPRECATED.
aksClusterConfiguration:
    apiServerAddress: <aks-api-server-address>
```