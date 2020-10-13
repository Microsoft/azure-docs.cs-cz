---
title: Nasazení bezstavové aplikace Kubernetes na zařízení GPU pro Azure Stack Edge pro pomocí kubectl | Microsoft Docs
description: Popisuje, jak vytvořit a spravovat Kubernetes Bezstavová nasazení aplikace pomocí kubectl na zařízení s Microsoft Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 91a2d08bf9eea2f5af0f6893712515cb2feeab8a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90890729"
---
# <a name="deploy-a-kubernetes-stateless-application-via-kubectl-on-your-azure-stack-edge-pro-gpu-device"></a>Nasazení bezstavové aplikace Kubernetes přes kubectl na zařízení GPU Azure Stack Edge pro

Tento článek popisuje, jak nasadit bezstavovou aplikaci pomocí příkazů kubectl v existujícím clusteru Kubernetes. Tento článek vás také provede procesem vytvoření a nastavení lusků v bezstavových aplikacích.

## <a name="prerequisites"></a>Požadavky

Než budete moct vytvořit cluster Kubernetes a použít `kubectl` Nástroj příkazového řádku, musíte zajistit, aby:

- Máte přihlašovací údaje pro přihlášení k zařízení Azure Stack Edge pro s jedním uzlem.

- Prostředí Windows PowerShell 5,0 nebo novější je nainstalováno v klientském systému Windows pro přístup k zařízení Azure Stack Edge pro. Můžete mít i jiné klienty s podporovaným operačním systémem. Tento článek popisuje postup při použití klienta systému Windows. Pokud si chcete stáhnout nejnovější verzi Windows PowerShellu, přečtěte si část [instalace Windows PowerShellu](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-7).

- Na zařízení Azure Stack Edge pro je povolené výpočetní prostředky. Pokud chcete povolit výpočetní prostředky, v místním uživatelském rozhraní zařízení se dostanete na stránku **COMPUTE** . Pak vyberte síťové rozhraní, které chcete povolit pro výpočetní prostředky. Vyberte **Povolit**. Povolení výpočetních výsledků při vytváření virtuálního přepínače na zařízení v tomto síťovém rozhraní. Další informace najdete v tématu [Povolení služby COMPUTE Network na Azure Stack Edge pro](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).

- Vaše zařízení Azure Stack Edge pro má spuštěný clusterový server s Kubernetes verze v 1.9 nebo novějším. Další informace najdete v tématu [Vytvoření a Správa clusteru Kubernetes na zařízení Microsoft Azure Stack Edge pro](azure-stack-edge-gpu-create-kubernetes-cluster.md).

- Máte nainstalovanou `kubectl` .

## <a name="deploy-a-stateless-application"></a>Nasazení bezstavové aplikace

Než začnete, měli byste mít:

1. Vytvořili jste cluster Kubernetes.
2. Nastavte obor názvů.
3. Byl přidružen uživatel s oborem názvů.
4. Do se uložila konfigurace uživatele `C:\Users\<username>\.kube` .
5. Nainstalováno `kubectl` .

Teď můžete začít spouštět a spravovat Bezstavová nasazení aplikací na zařízeních Azure Stack Edge pro. Než začnete používat `kubectl` , musíte ověřit, jestli máte správnou verzi `kubectl` .

### <a name="verify-you-have-the-correct-version-of-kubectl-and-set-up-configuration"></a>Ověřte, že máte správnou verzi kubectl a nastavte konfiguraci.

Chcete-li zjistit verzi nástroje `kubectl` :

1. Ověřte, že verze `kubectl` je větší nebo rovna 1,9:

   ```powershell
   kubectl version
   ```
    
   Tady je příklad výstupu:
    
   ```powershell
   PS C:\WINDOWS\system32> C:\windows\system32\kubectl.exe version
   Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.2", GitCommit:"f6278300bebbb750328ac16ee6dd3aa7d3549568", GitTreeState:"clean", BuildDate:"2019-08-05T09:23:26Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"windows/amd64"}
   Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.1", GitCommit:"4485c6f18cee9a5d3c3b4e523bd27972b1b53892", GitTreeState:"clean", BuildDate:"2019-07-18T09:09:21Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
   ```

   V takovém případě je verze klienta kubectl v 1.15.2 a je kompatibilní, aby bylo možné pokračovat.

2. Získejte seznam lusků spuštěných v clusteru Kubernetes. Pod je kontejner aplikace nebo proces spuštěný v clusteru Kubernetes.

   ```powershell
   kubectl get pods -n <namespace-string>
   ```
    
   Tady je příklad použití příkazu:
    
   ```powershell
   PS C:\WINDOWS\system32> kubectl get pods -n "test1"
   No resources found.
   PS C:\WINDOWS\system32>
   ```
    
   Výstup by měl uvádět, že se nenašly žádné prostředky (lusky), protože v clusteru nejsou spuštěné žádné aplikace.

   Příkaz naplní adresářovou strukturu "C:\Users \\ &lt; UserName &gt; \\ . Kube" \" konfiguračními soubory. Nástroj příkazového řádku kubectl bude tyto soubory používat k vytváření a správě bezstavových aplikací v clusteru Kubernetes.

3. Ručně zkontrolujte adresářovou strukturu "C:\Users \\ &lt; UserName &gt; \\ . Kube \" , abyste ověřili, že *kubectl* ji naplní následujícími podsložkami:

   ```powershell
   PS C:\Users\username> ls .kube
    
    
      Directory: C:\Users\user\.kube
    
   Mode                LastWriteTime         Length Name
   ----                -------------         ------ ----
   d-----         2/18/2020 11:05 AM                cache
   d-----         2/18/2020 11:04 AM                http-cache
   -a----         2/18/2020 10:41 AM           5377 config
   ```

> [!NOTE]
> Chcete-li zobrazit seznam všech příkazů kubectl, zadejte `kubectl --help` .

### <a name="create-a-stateless-application-using-a-deployment"></a>Vytvoření bezstavové aplikace pomocí nasazení

Teď, když jste ověřili, že verze příkazového řádku kubectl je správná a že má požadované konfigurační soubory, můžete vytvořit bezstavové nasazení aplikace.

Pod je základní jednotkou spuštění aplikace Kubernetes, nejmenší a nejjednodušší jednotka v objektovém modelu Kubernetes, který vytvoříte nebo nasadíte. Objekt pod také zapouzdřuje prostředky úložiště, jedinečnou IP adresu sítě a možnosti, které určují, jak by měly být kontejnery spuštěny.

Typ bezstavové aplikace, kterou vytvoříte, je nasazení webového serveru Nginx.

Všechny příkazy kubectl, které slouží k vytváření a správě bezstavových nasazení aplikací, musí určovat obor názvů přidružený ke konfiguraci. Obor názvů jste vytvořili při připojení ke clusteru na zařízení Azure Stack Edge pro v kurzu [Vytvoření a Správa clusteru Kubernetes v zařízení se službou Microsoft Azure Stack Edge pro](azure-stack-edge-gpu-create-kubernetes-cluster.md) `New-HcsKubernetesNamespace` .

Chcete-li zadat obor názvů v příkazu kubectl, použijte `kubectl <command> -n <namespace-string>` .

Pomocí těchto kroků vytvořte nasazení Nginx:

1. Použijte bezstavovou aplikaci vytvořením objektu nasazení Kubernetes:

   ```powershell
   kubectl apply -f <yaml-file> -n <namespace-string>
   ```

   V tomto příkladu je cesta k souboru YAML aplikace externím zdrojem.

   Tady je ukázka použití příkazu a výstupu:

   ```powershell
   PS C:\WINDOWS\system32> kubectl apply -f https://k8s.io/examples/application/deployment.yaml -n "test1"
    
   deployment.apps/nginx-deployment created
   ```

   Alternativně můžete uložit následující Markdownu do místního počítače a nahradit cestu a název souboru v parametru *-f* . Například "C:\Kubernetes\deployment.yaml". Tady je konfigurace nasazení aplikace:

   ```markdown
   apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
   kind: Deployment
   metadata:
     name: nginx-deployment
   spec:
     selector:
       matchLabels:
         app: nginx
     replicas: 2 # tells deployment to run 2 pods matching the template
     template:
       metadata:
         labels:
           app: nginx
       spec:
         containers:
         - name: nginx
           image: nginx:1.7.9
           ports:
           - containerPort: 80
   ```

   Tento příkaz vytvoří výchozí Nginx – nasazení, které má dvě lusky ke spuštění vaší aplikace.

2. Získejte popis Kubernetes Nginx – nasazení, které jste vytvořili:

   ```powershell
   kubectl describe deployment nginx-deployment -n <namespace-string>
   ```

   Zde je ukázka použití příkazu a výstupu:
    
   ```powershell
   PS C:\Users\user> kubectl describe deployment nginx-deployment -n "test1"
    
   Name:                   nginx-deployment
   Namespace:              test1
   CreationTimestamp:      Tue, 18 Feb 2020 13:35:29 -0800
   Labels:                 <none>
   Annotations:            deployment.kubernetes.io/revision: 1
                           kubectl.kubernetes.io/last-applied-configuration:
                             {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"nginx-deployment","namespace":"test1"},"spec":{"repl...
   Selector:               app=nginx
   Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
   StrategyType:           RollingUpdate
   MinReadySeconds:        0
   RollingUpdateStrategy:  25% max unavailable, 25% max surge
   Pod Template:
      Labels:  app=nginx
      Containers:
       nginx:
        Image:        nginx:1.7.9
        Port:         80/TCP
        Host Port:    0/TCP
        Environment:  <none>
        Mounts:       <none>
      Volumes:        <none>
   Conditions:
      Type           Status  Reason
      ----           ------  ------
      Available      True    MinimumReplicasAvailable
      Progressing    True    NewReplicaSetAvailable
   OldReplicaSets:  <none>
   NewReplicaSet:   nginx-deployment-5754944d6c (2/2 replicas created)
   Events:
     Type    Reason             Age    From                   Message
     ----    ------             ----   ----                   -------
     Normal  ScalingReplicaSet  2m22s  deployment-controller  Scaled up replica set nginx-deployment-5754944d6c to 2
   ```

   Pokud přesně hledáte v nastavení *repliky* , zobrazí se:
    
   ```powershell
   Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
   ```

   Nastavení *repliky* určuje, že vaše specifikace nasazení vyžadovala dvě lusky, přičemž tyto lusky se vytvořily a aktualizovaly a že jsou připravené k použití.

   > [!NOTE]
   > Sada replik nahrazuje lusky, které se z nějakého důvodu odstraňují nebo ukončí, například v případě selhání uzlu zařízení nebo při upgradu na rušivý stav zařízení. Z tohoto důvodu doporučujeme použít sadu replik i v případě, že vaše aplikace vyžaduje jenom jedno pod.

3. Chcete-li zobrazit seznam lusků v nasazení:

   ```powershell
   kubectl get pods -l app=nginx -n <namespace-string>
   ```
    
   Zde je ukázka použití příkazu a výstupu:
    
   ```powershell
   PS C:\Users\user> kubectl get pods -l app=nginx -n "test1"
    
   NAME                                READY   STATUS    RESTARTS   AGE
   nginx-deployment-5754944d6c-7wqjd   1/1     Running   0          3m13s
   nginx-deployment-5754944d6c-nfj2h   1/1     Running   0          3m13s
   ```

   Výstup ověří, že máme dvě lusky s jedinečnými názvy, na které můžeme odkazovat pomocí kubectl.

4. Zobrazení informací o jednotlivých objektech pod vaším nasazením:

   ```powershell
   kubectl describe pod <podname-string> -n <namespace-string>
   ```

   Zde je ukázka použití příkazu a výstupu:

   ```powershell
   PS C:\Users\user> kubectl describe pod "nginx-deployment-5754944d6c-7wqjd" -n "test1"

   Name:           nginx-deployment-5754944d6c-7wqjd
   Namespace:      test1
   Priority:       0
   Node:           k8s-1d9qhq2cl-n1/10.128.46.184
   Start Time:     Tue, 18 Feb 2020 13:35:29 -0800
   Labels:         app=nginx
                   pod-template-hash=5754944d6c
   Annotations:    <none>
   Status:         Running
   IP:             172.17.246.200
   Controlled By:  ReplicaSet/nginx-deployment-5754944d6c
    Containers:
      nginx:
        Container ID:   docker://280b0f76bfdc14cde481dc4f2b8180cf5fbfc90a084042f679d499f863c66979
        Image:          nginx:1.7.9
        Image ID:       docker-pullable://nginx@sha256:e3456c851a152494c3e4ff5fcc26f240206abac0c9d794affb40e0714846c451
        Port:           80/TCP
        Host Port:      0/TCP
        State:          Running
          Started:      Tue, 18 Feb 2020 13:35:35 -0800
        Ready:          True
        Restart Count:  0
        Environment:    <none>
        Mounts:
          /var/run/secrets/kubernetes.io/serviceaccount from default-token-8gksw (ro)
    Conditions:
      Type              Status
      Initialized       True
      Ready             True
      ContainersReady   True
      PodScheduled      True
    Volumes:
      default-token-8gksw:
        Type:        Secret (a volume populated by a Secret)
        SecretName:  default-token-8gksw
        Optional:    false
    QoS Class:       BestEffort
    Node-Selectors:  <none>
    Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                     node.kubernetes.io/unreachable:NoExecute for 300s
    Events:
      Type    Reason     Age    From                       Message
      ----    ------     ----   ----                       -------
      Normal  Scheduled  4m58s  default-scheduler          Successfully assigned test1/nginx-deployment-5754944d6c-7wqjd to k8s-1d9qhq2cl-n1
      Normal  Pulling    4m57s  kubelet, k8s-1d9qhq2cl-n1  Pulling image "nginx:1.7.9"
      Normal  Pulled     4m52s  kubelet, k8s-1d9qhq2cl-n1  Successfully pulled image "nginx:1.7.9"
      Normal  Created    4m52s  kubelet, k8s-1d9qhq2cl-n1  Created container nginx
      Normal  Started    4m52s  kubelet, k8s-1d9qhq2cl-n1  Started container nginx
   ```

### <a name="rescale-the-application-deployment-by-increasing-the-replica-count"></a>Škálovat nasazení aplikace zvýšením počtu replik

Každé z nich je určeno ke spuštění jedné instance dané aplikace. Pokud chcete aplikaci škálovat vodorovně a spustit více instancí, můžete zvýšit počet lusků, jeden pro každou instanci. V Kubernetes se tento postup označuje jako replikace.
Počet lusků v nasazení aplikace můžete zvýšit použitím nového souboru YAML. Soubor YAML změní nastavení replik na 4, což zvyšuje počet lusků v nasazení na čtyři lusky. Pokud chcete zvýšit počet lusků od 2 do 4:

```powershell
PS C:\WINDOWS\system32> kubectl apply -f https://k8s.io/examples/application/deployment-scale.yaml -n "test1"
```

Alternativně můžete uložit následující Markdownu na místním počítači a nahradit cestu a název souboru pro parametr *-f* pro `kubectl apply` . Například "C:\Kubernetes\deployment-scale.yaml". Tady je konfigurace pro škálování nasazení aplikace:

```markdown
apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 4 # Update the replicas from 2 to 4
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.8
        ports:
        - containerPort: 80
```

Chcete-li ověřit, zda má nasazení čtyři lusky:

```powershell
kubectl get pods -l app=nginx
```

Tady je příklad výstupu pro změnu měřítka v nasazení ze dvou na čtyři lusky:

```powershell
PS C:\WINDOWS\system32> kubectl get pods -l app=nginx

NAME                               READY     STATUS    RESTARTS   AGE
nginx-deployment-148880595-4zdqq   1/1       Running   0          25s
nginx-deployment-148880595-6zgi1   1/1       Running   0          25s
nginx-deployment-148880595-fxcez   1/1       Running   0          2m
nginx-deployment-148880595-rwovn   1/1       Running   0          2m
```

Jak vidíte z výstupu, teď máte ve svém nasazení čtyři lusky, které můžou aplikaci spouštět.

### <a name="delete-a-deployment"></a>Odstranění nasazení

Chcete-li odstranit nasazení, včetně všech lusků, je třeba spustit `kubectl delete deployment` zadání názvu nasazení *Nginx-Deployment* a názvu oboru názvů. Postup odstranění nasazení:

   ```powershell
   kubectl delete deployment nginx-deployment -n <namespace-string>
   ```

Tady je příklad použití příkazů a výstupu:

```powershell
PS C:\Users\user> kubectl delete deployment nginx-deployment -n "test1"
deployment.extensions "nginx-deployment" deleted
```

## <a name="next-steps"></a>Další kroky

[Kubernetes – přehled](azure-stack-edge-gpu-kubernetes-overview.md)
