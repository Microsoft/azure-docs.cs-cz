---
title: Nasazení aplikace PHP v knize webkniha na Kubernetes s povoleným obloukem na zařízení GPU pro Azure Stack Edge | Microsoft Docs
description: Popisuje, jak nasadit bezstavovou aplikaci PHP v programu pro vytváření souborů s Redis pomocí GitOps v clusteru Kubernetes s povoleným ARC pro vaše zařízení Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/25/2020
ms.author: alkohli
ms.openlocfilehash: d323504a46cd35525c889a94d2d044193c1471ac
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94535941"
---
# <a name="deploy-a-php-guestbook-stateless-application-with-redis-on-arc-enabled-kubernetes-cluster-on-azure-stack-edge-pro-gpu"></a>Nasazení bezstavové aplikace v jazyce PHP s Redisem v clusteru Kubernetes s povoleným obloukem na Azure Stack Edge pro GPU

V tomto článku se dozvíte, jak sestavit a nasadit jednoduchou vícevrstvou webovou aplikaci s využitím Kubernetes a ARC Azure. Tento příklad se skládá z následujících součástí:

- Redis hlavní server s jednou instancí pro ukládání položek knihy návštěv
- Více replikovaných instancí Redis, které slouží ke čtení
- Víc instancí webu front-endu

Nasazení se provádí pomocí GitOps na clusteru Kubernetes s povoleným ARC na vašem zařízení Azure Stack Edge pro. 

Tento postup je určený pro uživatele, kteří zkontrolovali [úlohy Kubernetes na zařízení Azure Stack Edge pro](azure-stack-edge-gpu-kubernetes-workload-management.md) a jsou obeznámeni s koncepty, [co je Azure ARC Enabled Kubernetes (Preview)](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview).


## <a name="prerequisites"></a>Požadavky

Než budete moct nasadit bezstavovou aplikaci, ujistěte se, že jste na svém zařízení dokončili následující požadavky a klienta, který budete používat pro přístup k zařízení:

### <a name="for-device"></a>Zařízení

1. Máte přihlašovací údaje pro přihlášení k zařízení Azure Stack Edge pro s jedním uzlem.
    1. Zařízení se aktivuje. Viz [Aktivace zařízení](azure-stack-edge-gpu-deploy-activate.md).
    1. Zařízení má výpočetní roli nakonfigurovanou prostřednictvím Azure Portal a má cluster Kubernetes. Viz [Konfigurace výpočtů](azure-stack-edge-gpu-deploy-configure-compute.md).

1. Na svém zařízení jste povolili Azure ARC na stávajícím clusteru Kubernetes a máte odpovídající prostředek Azure ARC v Azure Portal. Podrobný postup najdete v tématu [Povolení ARC Azure na zařízení Azure Stack Edge pro](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md).

### <a name="for-client-accessing-the-device"></a>Pro klientský přístup k zařízení

1. Máte klientský systém Windows, který se bude používat pro přístup k zařízení Azure Stack Edge pro.
  
    - Na klientovi běží Windows PowerShell 5,0 nebo novější. Nejnovější verzi Windows PowerShellu si stáhnete tak, že přejdete na [nainstalovat Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-7).
    
    - Můžete mít i jiné klienty s [podporovaným operačním systémem](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) . Tento článek popisuje postup při použití klienta systému Windows. 
    
1. Dokončili jste postup popsaný v tématu [přístup ke clusteru Kubernetes na zařízení Azure Stack Edge pro](azure-stack-edge-gpu-create-kubernetes-cluster.md). Máte:
    
    - Nainstalováno `kubectl` na klienta  <!--and saved the `kubeconfig` file with the user configuration to C:\\Users\\&lt;username&gt;\\.kube. -->
    
    - Ujistěte se, že `kubectl` verze klienta není ve verzi Kubernetes Master spuštěná na vašem zařízení Azure Stack Edge pro. 
      - Slouží `kubectl version` ke kontrole verze kubectl spuštěné v klientovi. Poznamenejte si plnou verzi.
      - V místním uživatelském rozhraní zařízení Azure Stack Edge pro se podívejte na **Přehled** a poznamenejte si číslo Kubernetes softwaru. 
      - Ověřte, že tyto dvě verze mají kompatibilitu z mapování uvedeného v podporované verzi Kubernetes. <!--insert link-->.

1. Máte [konfiguraci GitOps, kterou můžete použít ke spuštění nasazení ARC Azure](https://github.com/kagoyal/dbehaikudemo). V tomto příkladu použijete následující `yaml` soubory k nasazení na zařízení Azure Stack Edge pro.

    - `frontend-deployment.yaml`<!-- - The guestbook application has a web frontend serving the HTTP requests written in PHP. It is configured to connect to the redis-master Service for write requests and the redis-slave service for Read requests. This file describes a deployment that runs the frontend of the guestbook application.-->
    - `frontend-service.yaml` <!-- - This allows you to configure an externally visible frontend Service that can be accessed from outside the Kubernetes cluster on your device.-->
    - `redis-master-deployment.yaml` <!-- - This deployment file runs a single replica Redis master Pod. The guestbook application uses Redis to store its data. It writes its data to a Redis master instance and reads data from multiple Redis slave instances.-->
    - `redis-master-service.yaml` <!-- - The guestbook application needs to communicate to the Redis master to write its data. You need to apply a Service to proxy the traffic to the Redis master Pod. A Service defines a policy to access the Pods.-->
    - `redis-slave-deployment.yaml` <!-- - Although the Redis master is a single pod, you can make it highly available to meet traffic demands by adding replica Redis slaves. The Redis Slave Deployment specifies two replicas.-->
    - `redis-slave-service.yaml` <!-- - The guestbook application needs to communicate to Redis slaves to read data. To make the Redis slaves discoverable, you need to set up a Service. A Service provides transparent load balancing to a set of Pods.-->

<!-- az cli version requirements-->


## <a name="deploy-configuration"></a>Nasazení konfigurace

Pomocí těchto kroků nakonfigurujete prostředek Azure ARC k nasazení konfigurace GitOps prostřednictvím Azure Portal: 

1. V Azure Portal přejdete do prostředku Azure ARC, který jste vytvořili při povolování Azure ARC v clusteru Kubernetes na vašem zařízení. 

    ![Přejít na prostředek ARC Azure](media/azure-stack-edge-gpu-connect-powershell-interface/verify-azure-arc-enabled-1.png)

1. Přejít na **Konfigurace** a vyberte **+ Přidat konfiguraci**.

    ![Snímek obrazovky ukazuje cluster Kubernetes s povoleným nastavením Přidat konfiguraci.](media/azure-stack-edge-gpu-connect-powershell-interface/select-configurations-1.png)

1. V části **Přidat konfiguraci** zadejte odpovídající hodnoty pro pole a vyberte **použít**.

    |Parametr  |Popis |
    |---------|---------|
    |Název konfigurace     | Název prostředku konfigurace.        |
    |Název instance operátoru     |Název instance operátoru, který identifikuje konkrétní konfiguraci. Název je řetězec o maximální 253 znaků, který musí být malými písmeny, alfanumerické znaky, spojovníky a tečky.         |
    |Obor názvů operátoru     | Nastavte na **demotestguestbook** , protože se shoduje s oborem názvů zadaným v nasazení `yaml` . <br> Pole definuje obor názvů, ve kterém je operátor nainstalován. Název je řetězec o maximální 253 znaků, který musí být malými písmeny, alfanumerické znaky, spojovníky a tečky.         |
    |Adresa URL úložiště     |<br>Cesta k úložišti Git v `http://github.com/username/repo` nebo ve `git://github.com/username/repo` formátu, kde se nachází konfigurace GitOps         |
    |Rozsah operátoru     | Vyberte **obor názvů**. <br>Tato definice definuje rozsah, ve kterém je operátor nainstalován. Vyberte tento obor názvů. Váš operátor se nainstaluje do oboru názvů určeného v souborech YAML nasazení.       |
    |Typ operátoru     | Ponechte ve výchozím nastavení. <br>Určuje typ operátoru, ve výchozím nastavení nastaven jako tok.        |
    |Params operátorů     | Ponechte toto nastavení prázdné. <br>Toto pole obsahuje parametry, které se mají předat operátoru toku.        |
    |Helm     | Nastavte tuto hodnotu jako **zakázanou**. <br>Tuto možnost povolte, pokud budete provádět nasazení na základě grafů.        |


    ![Přidání konfigurace](media/azure-stack-edge-gpu-connect-powershell-interface/add-configuration-1.png)


1. Spustí se nasazení konfigurace a **stav operátoru** se zobrazí jako **čeká na vyřízení**. 

    ![Snímek obrazovky ukazuje cluster Kubernetes s povoleným obloukem Azure v nedokončeném stavu, když se aktualizuje.](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-1.png)

1. Nasazení trvá několik minut. Po dokončení nasazení se **stav operátora** zobrazí jako **nainstalované**.

    ![Snímek obrazovky ukazuje cluster Kubernetes s povoleným ARC Azure v nainstalovaném stavu.](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-2.png)


## <a name="verify-deployment"></a>Ověření nasazení

Nasazení prostřednictvím konfigurace GitOps vytvoří `demotestguestbook` obor názvů, jak je uvedeno v souborech nasazení `yaml` umístěných v úložišti Git.

1. Po použití konfigurace GitOps se [připojte k rozhraní PowerShell zařízení](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. Spuštěním následujícího příkazu zobrazíte seznam lusků spuštěných v `demotestguestbook` oboru názvů, který odpovídá nasazení.

    `kubectl get pods -n <your-namespace>`
  
    Zde je ukázkový výstup.
  
    ```powershell
    [10.128.44.240]: PS>kubectl get pods -n demotestguestbook
    NAME                            READY   STATUS    RESTARTS   AGE
    aseoperator1-5569658644-cqtb5   1/1     Running   0          91m
    frontend-6cb7f8bd65-4xb4f       1/1     Running   0          91m
    frontend-6cb7f8bd65-q9cxj       1/1     Running   0          91m
    frontend-6cb7f8bd65-xpzs6       1/1     Running   0          91m
    memcached-86bdf9f56b-5l2fq      1/1     Running   0          91m
    redis-master-7db7f6579f-2z29w   1/1     Running   0          91m
    redis-slave-7664787fbc-lgr2n    1/1     Running   0          91m
    redis-slave-7664787fbc-vlvzn    1/1     Running   0          91m
    [10.128.44.240]: PS>
    ```  

1. V tomto příkladu byla služba front-end nasazena jako typ: Vyrovnávání zatížení. Pokud chcete zobrazit knihu návštěv, budete muset najít IP adresu této služby. Spusťte následující příkaz.

    `kubectl get service -n <your-namespace>`
    
    ```powershell
    [10.128.44.240]: PS>kubectl get service -n demotestguestbook
    NAME           TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)        AGE
    frontend       LoadBalancer   10.96.79.38      10.128.44.245   80:31238/TCP   85m
    memcached      ClusterIP      10.102.47.75     <none>          11211/TCP      85m
    redis-master   ClusterIP      10.104.32.99     <none>          6379/TCP       85m
    redis-slave    ClusterIP      10.104.215.146   <none>          6379/TCP       85m
    [10.128.44.240]: PS>
    ```
1. Služba front-end `type:LoadBalancer` má externí IP adresu. Tato IP adresa pochází z rozsahu IP adres, který jste zadali pro externí služby při konfiguraci nastavení výpočetního sítě v zařízení. Pomocí této IP adresy můžete zobrazit knihu návštěv na adrese URL: `https://<external-IP-address>` .

    ![Zobrazit knihu návštěv](media/azure-stack-edge-gpu-connect-powershell-interface/view-guestbook-1.png)

## <a name="delete-deployment"></a>Odstranit nasazení

Chcete-li odstranit nasazení, můžete odstranit konfiguraci z Azure Portal. Tím se odstraní objekty vytvořené včetně nasazení a služeb.

1. V Azure Portal otevřete > konfigurace prostředků ARC Azure. 
1. Vyhledejte konfiguraci, kterou chcete odstranit. Vyberte... Chcete-li vyvolat kontextovou nabídku a vybrat možnost **Odstranit**.
    ![Odstranit konfiguraci](media/azure-stack-edge-gpu-connect-powershell-interface/delete-configuration-1.png)

Odstranění konfigurace může trvat několik minut.
 
<!--```powershell
kubectl delete deployment <deployment-name> -n <your-namespace>
kubectl delete service <service-name> -n <your-namespace>
```

Here is a sample output of when you delete the deployments and the services.

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```-->


## <a name="next-steps"></a>Další kroky

Naučte [se používat řídicí panel Kubernetes k monitorování nasazení na zařízení Azure Stack Edge pro](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md) .
