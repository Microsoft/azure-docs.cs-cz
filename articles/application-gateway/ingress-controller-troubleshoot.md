---
title: Řešení potíží s řadičem příchozího přenosu dat aplikační brány
description: Tento článek obsahuje dokumentaci o řešení běžných otázek nebo problémů s řadičem příchozího přenosu dat brány aplikace.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a64a9ce5e080308674893273e90a0e83686e339e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795514"
---
# <a name="troubleshoot-common-questions-or-issues-with-ingress-controller"></a>Řešení běžných otázek nebo problémů s řadičem příchozího přenosu dat

[Azure Cloud Shell](https://shell.azure.com/) je nejpohodlnější způsob řešení problémů s instalací AKS a AGIC. Spusťte svůj shell z [shell.azure.com](https://shell.azure.com/) nebo kliknutím na odkaz:

[![Spuštění vložení](https://shell.azure.com/images/launchcloudshell.png "Spuštění služby Azure Cloud Shell")](https://shell.azure.com)


## <a name="test-with-a-simple-kubernetes-app"></a>Testování pomocí jednoduché aplikace Kubernetes

Níže uvedené kroky předpokládají:
  - Máte cluster AKS s povolenou rozšířenou sítí.
  - AGIC bylo nainstalováno v clusteru AKS.
  - Už máte aplikační bránu ve virtuální síti sdílené s vaším clusterem AKS.

Chcete-li ověřit, zda je správně nastavena instalace aplikační brány + AKS + AGIC, nasaďte nejjednodušší možnou aplikaci:

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

Zkopírujte a vložte všechny řádky najednou ze skriptu výše do [prostředí Azure Cloud Shell](https://shell.azure.com/). Ujistěte se, že celý příkaz `cat` je zkopírován - počínaje a včetně poslední `EOF`.

![apply](./media/application-gateway-ingress-controller-troubleshooting/tsg--apply-config.png)

Po úspěšném nasazení aplikace nad clusteru AKS bude mít nový Pod, služba a příchozí přenos dat.

Získejte seznam lusků s `kubectl get pods -o wide`Cloud [Shell](https://shell.azure.com/): .
Očekáváme, že pro pod s názvem 'test-agic-app-pod' byly vytvořeny. Bude mít IP adresu. Tato adresa musí být v rámci virtuální sítě aplikační brány, která se používá s AKS.

![Lusky](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-pods.png)

Získejte seznam služeb: `kubectl get services -o wide`. Očekáváme, že se zobrazí služba s názvem "test-agic-app-service".

![Lusky](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-services.png)

Získat seznam příchozích: `kubectl get ingress`. Očekáváme, že ingress prostředek s názvem "test-agic-app-ingress' byly vytvořeny. Prostředek bude mít název hostitele "test.agic.contoso.com".

![Lusky](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-ingress.png)

Jeden z lusků bude AGIC. `kubectl get pods`zobrazí seznam podů, z nichž jeden bude začínat "ingress-azure". Získejte všechny protokoly `kubectl logs <name-of-ingress-controller-pod>` tohoto modulu s ověřit, že jsme měli úspěšné nasazení. Úspěšné nasazení by do protokolu přidalo následující řádky:
```
I0927 22:34:51.281437       1 process.go:156] Applied Application Gateway config in 20.461335266s
I0927 22:34:51.281585       1 process.go:165] cache: Updated with latest applied config.
I0927 22:34:51.282342       1 process.go:171] END AppGateway deployment
```

Alternativně z [Cloud Shell](https://shell.azure.com/) můžeme načíst pouze řádky označující úspěšnou konfiguraci aplikační brány s `kubectl logs <ingress-azure-....> | grep 'Applied App Gateway config in'`, kde `<ingress-azure....>` by měl být přesný název podu AGIC.

Bude použita následující konfigurace aplikace:

- Posluchač: ![posluchač](./media/application-gateway-ingress-controller-troubleshooting/tsg--listeners.png)

- Pravidlo směrování: ![routing_rule](./media/application-gateway-ingress-controller-troubleshooting/tsg--rule.png)

- Fond back-endu:
  - Ve fondu adres back-end bude jedna ADRESA IP a bude odpovídat IP `kubectl get pods -o wide` 
 ![adrese podu, který jsme pozorovali dříve, backend_pool](./media/application-gateway-ingress-controller-troubleshooting/tsg--backendpools.png)


Nakonec můžeme použít `cURL` příkaz z [cloudového prostředí](https://shell.azure.com/) k navázání připojení HTTP k nově nasazené aplikaci:

1. Slouží `kubectl get ingress` k získání veřejné IP adresy aplikační brány
2. Použití `curl -I -H 'test.agic.contoso.com' <publitc-ip-address-from-previous-command>`

![Lusky](./media/application-gateway-ingress-controller-troubleshooting/tsg--curl.png)

Výsledek označuje, `HTTP/1.1 200 OK` že aplikační brána + AKS + AGIC systém funguje podle očekávání.


## <a name="inspect-kubernetes-installation"></a>Kontrola instalace Kubernetes

### <a name="pods-services-ingress"></a>Pody, Služby, Vstup
Řadič příchozího přenosu dat aplikační brány (AGIC) nepřetržitě monitoruje následující prostředky Kubernetes: [Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#creating-a-deployment) or [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/#what-is-a-pod), [Service](https://kubernetes.io/docs/concepts/services-networking/service/), [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)


Aby AGIC fungovala podle očekávání, musí být na místě:
  1. AKS musí mít jeden nebo více **zdravých lusků**.
     Ověřte to z [prostředí Cloud Shell](https://shell.azure.com/) pomocí aplikace `kubectl get pods -o wide --show-labels` Pokud máte pod s modulem `apsnetapp`, může výstup vypadat takto:
     ```bash
     delyan@Azure:~$ kubectl get pods -o wide --show-labels

     NAME                   READY   STATUS    RESTARTS   AGE   IP          NODE                       NOMINATED NODE   READINESS GATES   LABELS
     aspnetapp              1/1     Running   0          17h   10.0.0.6    aks-agentpool-35064155-1   <none>           <none>            app=aspnetapp
     ```

  2. Jedna nebo více **služeb**, odkazující `selector` na pody výše prostřednictvím odpovídajících popisků.
     Ověřte to z [prostředí Cloud Shell](https://shell.azure.com/)`kubectl get services -o wide`
     ```bash
     delyan@Azure:~$ kubectl get services -o wide --show-labels

     NAME                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE   SELECTOR        LABELS
     aspnetapp           ClusterIP   10.2.63.254    <none>        80/TCP    17h   app=aspnetapp   <none>     
     ```

  3. **Příchozí přenos dat**, `kubernetes.io/ingress.class: azure/application-gateway`s odkazem na službu [Cloud Shell](https://shell.azure.com/) nad`kubectl get ingress -o wide --show-labels`
     ```bash
     delyan@Azure:~$ kubectl get ingress -o wide --show-labels

     NAME        HOSTS   ADDRESS   PORTS   AGE   LABELS
     aspnetapp   *                 80      17h   <none>
     ```

  4. Zobrazit poznámky příchozího přenosu dat `kubectl get ingress aspnetapp -o yaml` výše: (nahraďte `aspnetapp` název příchozího přenosu dat)
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

     Prostředek příchozího přenosu dat musí `kubernetes.io/ingress.class: azure/application-gateway`být anotován s .
 

### <a name="verify-observed-namespace"></a>Ověření vyčápěnen oboru názvů

* Získejte existující obory názvů v clusteru Kubernetes. V jakém oboru názvů je vaše aplikace spuštěna? Sleduje AGIC tento obor názvů? Informace o správné konfiguraci sledovaných oborů názvů naleznete v dokumentaci k [podpoře více oborů](./ingress-controller-multiple-namespace-support.md#enable-multiple-namespace-support) názvů.

    ```bash
    # What namespaces exist on your cluster
    kubectl get namespaces
    
    # What pods are currently running
    kubectl get pods --all-namespaces -o wide
    ```


* Pod AGIC by měl `default` být v `NAMESPACE`oboru názvů (viz sloupec). Zdravý modul by `Running` měl `STATUS` ve sloupci. Měl by tam být alespoň jeden AGIC modul.

    ```bash
    # Get a list of the Application Gateway Ingress Controller pods
    kubectl get pods --all-namespaces --selector app=ingress-azure
    ```


* Pokud pod AGIC není`STATUS` v pořádku (sloupec `Running`z výše uvedeného příkazu není ):
  - získejte protokoly, abyste pochopili, proč:`kubectl logs <pod-name>`
  - pro předchozí instanci podu:`kubectl logs <pod-name> --previous`
  - popište pod získat více kontextu:`kubectl describe pod <pod-name>`


* Máte prostředky [služby](https://kubernetes.io/docs/concepts/services-networking/service/) Kubernetes service a [ingress?](https://kubernetes.io/docs/concepts/services-networking/ingress/)
    
    ```bash
    # Get all services across all namespaces
    kubectl get service --all-namespaces -o wide
    
    # Get all ingress resources across all namespaces
    kubectl get ingress --all-namespaces -o wide
    ```


* Je příchozí [anotace](https://kubernetes.io/docs/concepts/services-networking/ingress/) s: `kubernetes.io/ingress.class: azure/application-gateway`? AGIC bude sledovat pouze kubernetes příchozí přenos prostředků, které mají tuto poznámku.
    
    ```bash
    # Get the YAML definition of a particular ingress resource
    kubectl get ingress --namespace  <which-namespace?>  <which-ingress?>  -o yaml
    ```


* AGIC vyzařuje události Kubernetes pro určité kritické chyby. Můžete zobrazit tyto:
  - ve vašem terminálu přes`kubectl get events --sort-by=.metadata.creationTimestamp`
  - ve vašem prohlížeči pomocí [webového uživatelského uživatelského panelu Kubernetes (Dashboard)](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)


## <a name="logging-levels"></a>Úrovně protokolování

AGIC má 3 úrovně protokolování. Úroveň 1 je výchozí a zobrazuje minimální počet řádků protokolu.
Úroveň 5, na druhé straně, by se zobrazí všechny protokoly, včetně dezinfikovaného obsahu config aplikované arm.

Komunita Kubernetes vytvořila 9 úrovní těžby dřeva pro nástroj [kubectl.](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#kubectl-output-verbosity-and-debugging) V tomto úložišti využíváme 3 z nich, s podobnou sémantikou:


| Podrobnosti | Popis |
|-----------|-------------|
|  1        | Výchozí úroveň protokolu; zobrazuje podrobnosti o spuštění, upozornění a chyby |
|  3        | Rozšířené informace o událostech a změnách; seznamy vytvořených objektů |
|  5        | Protokoly zařazované objekty; zobrazuje dezinfikovanou konfiguraci JSON aplikovanou na ARM |


Úrovně podrobností jsou nastavitelné prostřednictvím `verbosityLevel` proměnné v souboru [helm-config.yaml.](#sample-helm-config-file) Zvýšení úrovně podrobností `5` získat JSON config odeslány do [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview):
  - přidat `verbosityLevel: 5` na řádek sám v [helm-config.yaml](#sample-helm-config-file) a re-instalaci
  - získat protokoly s`kubectl logs <pod-name>`

### <a name="sample-helm-config-file"></a>Ukázkový konfigurační soubor helmy
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

