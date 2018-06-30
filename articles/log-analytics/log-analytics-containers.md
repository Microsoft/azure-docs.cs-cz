---
title: Kontejner monitorování řešení v Azure Log Analytics | Microsoft Docs
description: Řešení monitorování kontejneru v analýzy protokolů umožňuje zobrazení a správa Docker a Windows hostitele kontejneru na jednom místě.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: e1e4b52b-92d5-4bfa-8a09-ff8c6b5a9f78
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 584e7a211cde83d7785c7fa0962c004af2b76968
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128907"
---
# <a name="container-monitoring-solution-in-log-analytics"></a>Řešení monitorování kontejneru v analýzy protokolů

![Kontejnery – symbol](./media/log-analytics-containers/containers-symbol.png)

Tento článek popisuje, jak nastavit a použít řešení monitorování kontejneru v analýzy protokolů, která slouží k zobrazení a správa Docker a Windows hostitele kontejneru na jednom místě. Docker je systém virtualizační software používá k vytvoření kontejnerů, které automatizují nasazení softwaru na počítačovou infrastrukturu.

Řešení ukazuje jsou kontejnery, které běží, jaké kontejneru image se používáte, a kde kontejnery běží. Můžete zobrazit podrobné informace o auditování zobrazující příkazy používané s kontejnery. A kontejnery můžete vyřešit tak, že zobrazení a hledání centralizované protokoly bez nutnosti Chcete-li zobrazit hostitelů Docker nebo systému Windows. Můžete najít kontejnery, které může být aktivní nebo využívání nadbytečné prostředky na hostiteli. A lze je zobrazit centralizované procesoru, paměti, úložiště a využití a výkonu informace o síti pro kontejnery. V počítačích se systémem Windows, můžete centralizovat a porovnání protokoly ze systému Windows Server, technologie Hyper-V a Docker kontejnerů. Řešení podporuje následující orchestrators kontejneru:

- Docker Swarm
- DC/OS
- Kubernetes
- Service Fabric
- Red Hat OpenShift

Pokud vás zajímá v monitorování výkonu pro zatížení nasazené do prostředí Kubernetes hostované na AKS (Azure Container Service), najdete v části [monitorování Azure Container Service](../monitoring/monitoring-container-health.md).  Řešení monitorování kontejneru nezahrnuje podporu pro monitorování této platformě.  

Následující diagram znázorňuje vztahy mezi různými hostiteli kontejneru a agenty s analýzy protokolů.

![Diagram kontejnery](./media/log-analytics-containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>Systémové požadavky a podporované platformy

Než začnete, zkontrolujte následující podrobnosti k ověření, že splňujete požadavky.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Podpora řešení monitorování kontejneru pro platformy Docker Orchestrator a verze operačního systému
Následující tabulka popisuje Docker orchestration a monitorování podporu kontejneru inventáře, výkonu a protokoly s analýzy protokolů operačního systému.   

| | ACS | Linux | Windows | Kontejner<br>Inventarizace | Image<br>Inventarizace | Node<br>Inventarizace | Kontejner<br>Výkon | Kontejner<br>Událost | Událost<br>Protokol | Kontejner<br>Protokol |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Kubernetes | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mesosphere<br>DC/OS | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Swarm | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Služba<br>Fabric | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Otevřete Red Hat<br>Shift | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
| Windows Server<br>(samostatně) | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Linux Server<br>(samostatně) | | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |


### <a name="docker-versions-supported-on-linux"></a>Verze docker podporované v systému Linux

- Docker 1.11 k 1.13
- V17.06 docker CE a EE

### <a name="x64-linux-distributions-supported-as-container-hosts"></a>x64 Linuxových distribucích podporovány jako hostitele kontejneru

- Ubuntu 14.04 LTS a 16.04 LTS
- CoreOS(stable)
- Amazon Linux 2016.09.0
- openSUSE 13.2
- openSUSE LEAP 42.2
- CentOS 7.2 a 7.3
- SLES 12
- RHEL 7.2 a 7.3
- Red Hat OpenShift kontejneru platformy (OCP) 3.4 a 3.5
- Služba ACS Mesosphere DC/OS 1.7.3 až 1.8.8
- Služba ACS Kubernetes 1.4.5 na 1.6
    - Kontejner procesy, Kubernetes inventáře a Kubernetes události jsou podporovány pouze s 1.4.1-45 verze a později agenta OMS pro Linux
- Služba ACS Docker Swarm

### <a name="supported-windows-operating-system"></a>Podporovaný operační systém Windows

- Windows Server 2016
- Windows 10 Anniversary Edition (Professional nebo Enterprise)

### <a name="docker-versions-supported-on-windows"></a>Verze docker podporované v systému Windows

- Docker 1.12 a 1.13
- Docker 17.03.0 a novější

## <a name="installing-and-configuring-the-solution"></a>Instalace a konfigurace řešení
K instalaci a konfiguraci řešení můžete použít následující informace.

1. Přidat kontejner monitorování řešení do pracovního prostoru analýzy protokolů z [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) nebo pomocí procesu popsaného v tématu [řešení přidat analýzy protokolů z Galerie řešení](log-analytics-add-solutions.md).

2. Nainstalovat a používat Docker s agentem OMS. Na základě vašeho operačního systému a Docker orchestrator, můžete použít následující metody konfigurace agenta.
  - Pro samostatné hostitele:
    - Na podporovaných operačních systémů Linux, nainstalujte a spusťte Docker a poté nainstalujte a nakonfigurujte [OMS agenta pro Linux](log-analytics-agent-linux.md).  
    - Na jádro operačního systému nelze spustit agenta OMS pro Linux. Místo toho spustíte kontejnerizované verzi agenta OMS pro Linux. Zkontrolujte [Linux kontejneru hostitele, včetně CoreOS](#for-all-linux-container-hosts-including-coreos) nebo [hostitele kontejner Azure Government Linux včetně CoreOS](#for-all-azure-government-linux-container-hosts-including-coreos) při práci s kontejnery v cloudu Azure Government.
    - V systému Windows Server 2016 a Windows 10 nainstalujte modul Docker a klienta pak připojit agenta pro shromažďování informací a odeslat ho k analýze protokolů. Zkontrolujte [nainstalujte a nakonfigurujte hostitele Windows kontejneru](#install-and-configure-windows-container-hosts) Pokud máte prostředí systému Windows.
  - Pro orchestration více hostitelů Docker:
    - Pokud máte Red Hat OpenShift prostředí, přečtěte si [konfigurace agenta OMS pro Red Hat OpenShift](#configure-an-oms-agent-for-red-hat-openshift).
    - Pokud máte Kubernetes clusteru Azure Container Service pomocí:
       - Zkontrolujte [konfigurace agenta OMS Linux pro Kubernetes](#configure-an-oms-linux-agent-for-kubernetes).
       - Zkontrolujte [konfigurace agenta OMS Windows pro Kubernetes](#configure-an-oms-windows-agent-for-kubernetes).
       - Zkontrolujte [Helm použijte k nasazení agenta OMS na Linux Kubernetes](#use-helm-to-deploy-oms-agent-on-linux-kubernetes).
    - Pokud máte cluster Azure Container Service DC/OS, přečtěte si informace v [monitorování clusteru Azure Container Service DC/OS s Operations Management Suite](../container-service/dcos-swarm/container-service-monitoring-oms.md).
    - Pokud máte prostředí režimu Docker Swarm, další informace v [konfigurace agenta OMS pro Docker Swarm](#configure-an-oms-agent-for-docker-swarm).
    - Pokud máte cluster Service Fabric, další informace v [monitorování kontejnery s analýzy protokolů OMS](../service-fabric/service-fabric-diagnostics-oms-containers.md).

Zkontrolujte [modulu Docker v systému Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon) článek Další informace o tom, jak nainstalovat a nakonfigurovat vaše Docker moduly v počítačích se systémem Windows.

> [!IMPORTANT]
> Docker musí být spuštěna **před** nainstalujete [OMS agenta pro Linux](log-analytics-agent-linux.md) v hostitelích kontejneru. Pokud jste již nainstalovali agenta před instalací Docker, budete muset znovu nainstalujte agenta OMS pro Linux. Další informace o Docker najdete v tématu [Docker webu](https://www.docker.com).


### <a name="install-and-configure-linux-container-hosts"></a>Nainstalujte a nakonfigurujte hostitele kontejneru Linux

Po instalaci Docker, použijte následující nastavení pro svého hostitele kontejneru konfigurace agenta pro použití s Docker. Je třeba nejprve ID pracovního prostoru analýzy protokolů a klíč, který můžete najít na portálu Azure. V pracovním prostoru, klikněte na tlačítko **rychlý Start** > **počítače** zobrazíte vaše **ID pracovního prostoru** a **primární klíč**.  Obě hodnoty zkopírujte a vložte do oblíbeného editoru.

**Pro všechny hostitele kontejneru Linux s výjimkou CoreOS:**

- Další informace a kroky k instalaci agenta OMS pro Linux najdete v tématu [připojení počítačů Linux k analýze protokolů](log-analytics-concept-hybrid.md).

**Pro všechny hostitele Linux kontejneru, včetně CoreOS:**

Spusťte kontejner, který chcete monitorovat. Upravit a použít v následujícím příkladu:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**Pro všechny hostitele Azure Government Linux kontejneru, včetně CoreOS:**

Spusťte kontejner, který chcete monitorovat. Upravit a použít v následujícím příkladu:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**Přepínání z pomocí nainstalovaného agenta systému Linux na jednu v kontejneru**

Pokud dříve použít agenta přímo nainstalovat a chcete místo toho použít agenta spuštěného v kontejneru, je nutné nejprve odebrat agenta OMS pro Linux. V tématu [odinstalování agenta OMS pro Linux](log-analytics-agent-linux.md) pochopit, jak úspěšně odinstalace agenta.  

#### <a name="configure-an-oms-agent-for-docker-swarm"></a>Konfigurace agenta OMS pro Docker Swarm

Spuštěním agenta OMS jako globální služby v nástroji Docker Swarm. Použijte následující informace pro vytvoření služby OMS Agent. Je třeba zadat ID pracovního prostoru analýzy protokolů a primární klíč.

- Spusťte následující na hlavní uzel.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock  -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

##### <a name="secure-secrets-for-docker-swarm"></a>Zabezpečené tajné klíče pro Docker Swarm

Pro Docker Swarm Jakmile tajný klíč pro ID pracovního prostoru a primární klíč je vytvořen, použijte následující informace pro vytvoření tajných informací.

1. Spusťte následující na hlavní uzel.

    ```
    echo "WSID" | docker secret create WSID -
    echo "KEY" | docker secret create KEY -
    ```

2. Ověřte, že tajné klíče byly vytvořeny správně.

    ```
    keiko@swarmm-master-13957614-0:/run# sudo docker secret ls
    ```

    ```
    ID                          NAME                CREATED             UPDATED
    j2fj153zxy91j8zbcitnjxjiv   WSID                43 minutes ago      43 minutes ago
    l9rh3n987g9c45zffuxdxetd9   KEY                 38 minutes ago      38 minutes ago
    ```

3. Spusťte následující příkaz připojit tajné klíče kontejnerizované Agent OMS.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="configure-an-oms-agent-for-red-hat-openshift"></a>Konfigurace agenta OMS pro OpenShift Red Hat
Existují tři způsoby, jak přidat agenta OMS na Red Hat OpenShift spustit shromažďování dat monitorování kontejneru.

* [Nainstalovat agenta OMS pro Linux](log-analytics-agent-linux.md) přímo na každém uzlu OpenShift  
* [Povolit rozšíření virtuálního počítače Log Analytics](log-analytics-azure-vm-extension.md) na každém uzlu OpenShift umístěných v Azure  
* Nainstalovat agenta OMS jako OpenShift démon sada  

V této části nabídneme kroky potřebné k instalaci agenta OMS jako démon OpenShift-set.  

1. Přihlaste se k hlavní uzel OpenShift a zkopírujte soubor yaml [ocp-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) z webu GitHub na hlavní uzel a změňte hodnotu s vaše ID pracovního prostoru analýzy protokolů a primární klíč.
2. Spusťte následující příkazy pro vytvoření projektu pro analýzy protokolů a nastavení uživatelského účtu.

    ```
    oadm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oadm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

4. Chcete-li nasadit sadu démon, spusťte následující:

    `oc create -f ocp-omsagent.yaml`

5. Ověřte, zda že je nakonfigurován a funguje správně, zadejte následující příkaz:

    `oc describe daemonset omsagent`  

    a měl by vypadat jako výstup:

    ```
    [ocpadmin@khm-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

Pokud chcete použít k zabezpečení ID pracovního prostoru analýzy protokolů a primární klíč při použití souboru démon set yaml agenta OMS tajné klíče, proveďte následující kroky.

1. Přihlaste se k hlavní uzel OpenShift a zkopírujte soubor yaml [ocp-ds-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) a tajný klíč generování skriptu [ocp-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) z Githubu.  Tento skript vygeneruje soubor yaml tajné klíče pro ID pracovního prostoru analýzy protokolů a primární klíč zabezpečit vaše secrete informace.  
2. Spusťte následující příkazy pro vytvoření projektu pro analýzy protokolů a nastavení uživatelského účtu. Tajný klíč generování skriptu požádá o vaše ID pracovního prostoru analýzy protokolů <WSID> a primární klíč <KEY> a po dokončení zpracování se vytvoří soubor ocp-secret.yaml.  

    ```
    oadm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oadm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

4. Nasaďte soubor tajný spuštěním následujícího:

    `oc create -f ocp-secret.yaml`

5. Ověření nasazení tak, že spustíte následující:

    `oc describe secret omsagent-secret`  

    a měl by vypadat jako výstup:  

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

6. Nasaďte soubor démon set yaml OMS agenta spuštěním následujícího:

    `oc create -f ocp-ds-omsagent.yaml`  

7. Ověření nasazení tak, že spustíte následující:

    `oc describe ds oms`

    a měl by vypadat jako výstup:

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe secret omsagent-secret  
    Name:           omsagent-secret  
    Namespace:      omslogging  
    Labels:         <none>  
    Annotations:    <none>  

    Type:   Opaque  

     Data  
     ====  
     KEY:    89 bytes  
     WSID:   37 bytes  
    ```

#### <a name="configure-an-oms-linux-agent-for-kubernetes"></a>Konfigurace agenta OMS Linux pro Kubernetes

Pro Kubernetes pomocí skriptu pro generování souboru yaml tajné klíče pro ID pracovního prostoru a primární klíč pro instalaci agenta OMS pro Linux. Na [OMS Docker Kubernetes Githubu](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) stránky, jsou soubory, které můžete použít s nebo bez tajné informace.

- Výchozí OMS agenta pro Linux DaemonSet nemá tajné informace (omsagent.yaml)
- OMS agenta pro Linux DaemonSet yaml soubor používá tajné informace (omsagent – ds-secrets.yaml) s tajný generování skriptů pro generování souboru yaml (omsagentsecret.yaml) tajných klíčů.

Můžete vytvořit omsagent DaemonSets s nebo bez tajných klíčů.

**Výchozí soubor yaml OMSagent DaemonSet bez tajné klíče**

- Výchozí DaemonSet agenta OMS yaml souboru, nahraďte `<WSID>` a `<KEY>` WSID a klíč. Zkopírujte soubor do hlavní uzel a spusťte následující:

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**Výchozí OMSagent DaemonSet yaml soubor obsahující tajné údaje**

1. Pokud chcete používat DaemonSet agenta OMS pomocí tajné informace, nejprve vytvořte těchto tajných klíčů.
    1. Zkopírujte skript a soubor tajný šablony a ujistěte se, že jsou ve stejném adresáři.
        - Generování skriptu - tajný klíč gen.sh tajný klíč
        - Šablona tajné – template.yaml tajný klíč
    2. Spusťte skript, jako v následujícím příkladu. Skript vyzve k zadání ID pracovního prostoru analýzy protokolů a primární klíč a po zadání je skript vytvoří soubor tajný yaml, můžete ji spustit.   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. Vytvoření tajných klíčů pod spuštěním následujícího:
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. Pokud chcete ověřit, spusťte následující:

        ```
        keiko@ubuntu16-13db:~# sudo kubectl get secrets
        ```

        Výstup by měl vypadat takto:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        ```

        ```
        keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
        ```

        Výstup by měl vypadat takto:

        ```
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. Vytvoření vaší omsagent démon set spuštěním ``` sudo kubectl create -f omsagent-ds-secrets.yaml ```

2. Ověřte, zda DaemonSet agenta OMS je spuštěna, podobný následujícímu:

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```


Pro Kubernetes pomocí skriptu pro generování souboru yaml tajné klíče pro ID pracovního prostoru a primární klíč pro agenta OMS pro Linux. Následující příklad informace s [omsagent yaml souboru](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml) zabezpečit vaše tajné informace.

```
keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
Name:           omsagent-secret
Namespace:      default
Labels:         <none>
Annotations:    <none>

Type:   Opaque

Data
====
WSID:   36 bytes
KEY:    88 bytes
```

#### <a name="configure-an-oms-windows-agent-for-kubernetes"></a>Konfigurace agenta OMS Windows pro Kubernetes
Pro Windows Kubernetes pomocí skriptu pro generování souboru yaml tajné klíče pro ID pracovního prostoru a primární klíč pro instalaci agenta OMS. Na [OMS Docker Kubernetes Githubu](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) stránky, jsou soubory, které můžete použít s tajné informace.  Musíte nainstalovat agenta OMS samostatně pro hlavní server a agenta uzly.  

1. Použít DaemonSet agenta OMS pomocí tajné informace na hlavním uzlu, přihlaste se a nejdříve vytvořit těchto tajných klíčů.
    1. Zkopírujte skript a soubor tajný šablony a ujistěte se, že jsou ve stejném adresáři.
        - Generování skriptu - tajný klíč gen.sh tajný klíč
        - Šablona tajné – template.yaml tajný klíč

    2. Spusťte skript, jako v následujícím příkladu. Skript vyzve k zadání ID pracovního prostoru OMS a primární klíč a po zadání je skript vytvoří soubor tajný yaml, můžete ji spustit.   

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. Vytvoření vaší omsagent démon set spuštěním ``` kubectl create -f omsagentsecret.yaml ```
    4. Pokud chcete zkontrolovat, spusťte následující:

        ```
        root@ubuntu16-13db:~# kubectl get secrets
        ```

        Výstup by měl vypadat takto:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. Vytvoření vaší omsagent démon set spuštěním ```kubectl create -f ws-omsagent-de-secrets.yaml```

2. Ověřte, zda DaemonSet agenta OMS je spuštěna, podobný následujícímu:

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Instalace agenta v pracovním uzlu, který se systémem Windows, postupujte podle kroků v části [instalace a konfigurace hostitelů Windows kontejneru](#install-and-configure-windows-container-hosts).

#### <a name="use-helm-to-deploy-oms-agent-on-linux-kubernetes"></a>Použití Helm nasazení agenta OMS na Linux Kubernetes
Abyste mohli použít helm nasazení agenta OMS na vašem prostředí Linux Kubernetes, proveďte následující kroky.

1. Vytvoření vaší omsagent démon set spuštěním ```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms```
2. Výsledky bude vypadat nějak takto:

    ```
    NAME:   omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED

    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     3s

    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         3s
    ```
3. Můžete zkontrolovat stav omsagent spuštěním: ```helm status "omsagent"``` a výstup bude vypadat podobně jako následující:

    ```
    keiko@k8s-master-3814F33-0:~$ helm status omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED
 
    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     17m
 
    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         17m
    ```
Další informace naleznete [kontejneru řešení Helm grafu](https://aka.ms/omscontainerhelm).

### <a name="install-and-configure-windows-container-hosts"></a>Instalace a konfigurace hostitelů Windows kontejneru

Použijte informace v části instalace a konfigurace hostitelů Windows kontejneru.

#### <a name="preparation-before-installing-windows-agents"></a>Příprava před instalací agentů v systému Windows

Před instalací agentů do počítačů se systémem Windows, musíte nakonfigurovat službu Docker. Konfigurace umožňuje agent služby Windows nebo analýzy protokolů rozšíření virtuálního počítače používat soketu Docker TCP tak agentů můžete přistupovat vzdáleně démon Docker a k zaznamenání dat pro monitorování.

##### <a name="to-start-docker-and-verify-its-configuration"></a>Spuštění Docker a ověřte jeho konfiguraci

Je třeba kroky potřebné k nastavení TCP pojmenovaný kanál pro systém Windows Server:

1. V prostředí Windows PowerShell povolte kanálu TCP a pojmenovaný kanál.

    ```
    Stop-Service docker
    dockerd --unregister-service
    dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
    Start-Service docker
    ```

2. Konfigurace Docker pomocí konfiguračního souboru pro TCP kanálu a pojmenovaný kanál. Konfigurační soubor se nachází v C:\ProgramData\docker\config\daemon.json.

    V souboru daemon.json budete potřebovat následující:

    ```
    {
    "hosts": ["tcp://0.0.0.0:2375", "npipe://"]
    }
    ```

Další informace o konfiguraci démon Docker použít s kontejnery Windows najdete v tématu [modulu Docker v systému Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon).


#### <a name="install-windows-agents"></a>Instalace agentů v systému Windows

Chcete-li povolit monitorování kontejneru systému Windows a technologie Hyper-V, nainstalujte Microsoft Monitoring Agent (MMA) na počítačích s Windows, které jsou hostiteli kontejneru. Pro počítače se systémem Windows v místním prostředí, najdete v části [počítače se systémem Windows se připojit k analýze protokolů](log-analytics-windows-agent.md). Pro virtuální počítače běžící v Azure, připojte je k analýze protokolů pomocí [rozšíření virtuálního počítače](log-analytics-azure-vm-extension.md).

Můžete monitorovat kontejnery Windows spuštěné v Service Fabric. Ale pouze [virtuální počítače běžící v Azure](log-analytics-azure-vm-extension.md) a [počítačů se systémem Windows v místním prostředí](log-analytics-windows-agent.md) jsou aktuálně podporovány pro Service Fabric.

Můžete ověřit, jestli je řešení monitorování kontejneru správně nastavené pro systém Windows. Chcete-li zkontrolovat, jestli byla sada management pack správně ke stažení, vyhledejte *ContainerManagement.xxx*. Soubory musí být ve složce C:\Program Files\Microsoft Monitoring Agent\Agent\Health služby State\Management balíčky.


## <a name="solution-components"></a>Součásti řešení

Z portálu OMS, přejděte na *řešení Galerie* a přidejte **řešení monitorování kontejner**. Pokud používáte agenty se systémem Windows, je při přidání tohoto řešení následující sady management pack nainstalované na každém počítači s agentem. Je požadován pro sadu management pack bez konfigurace nebo údržby.

- *ContainerManagement.xxx* nainstalované v C:\Program Files\Microsoft Monitoring Agent\Agent\Health State\Management SP

## <a name="container-data-collection-details"></a>Kontejner dat kolekce podrobnosti
Řešení monitorování kontejneru shromažďuje různé metriky a protokolu údaje o výkonu z kontejneru hostitelů a kontejnery pomocí agentů, které povolíte.

Data jsou shromažďována každé tři minuty následující typy agenta.

- [OMS agenta pro Linux](log-analytics-linux-agents.md)
- [Agent služby Windows](log-analytics-windows-agent.md)
- [Rozšíření virtuálního počítače analýzy protokolů](log-analytics-azure-vm-extension.md)


### <a name="container-records"></a>Záznamů kontejneru

V následující tabulce jsou uvedeny příklady záznamů shromážděných řešením pro monitorování kontejneru a datové typy, které se zobrazí ve výsledcích hledání protokolu.

| Typ dat | Datový typ v hledání protokolů | Fields (Pole) |
| --- | --- | --- |
| Výkon pro hostitele a kontejnery | `Perf` | Počítač, ObjectName, název_čítače &#40;% času procesoru, disku čte MB, zapíše MB, MB využití paměti, disku sítě přijatých bajtů, sítě odesílat bajtů, procesor doba využití, sítě&#41;, přepočtené, TimeGenerated, Cesta_k_čítači, SourceSystem |
| Kontejner inventáře | `ContainerInventory` | TimeGenerated, počítače a název kontejneru, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, příkazu, CreatedTime, StartedTime, FinishedTime, SourceSystem, identifikátor ContainerID, ID obrázku |
| Kontejner image inventáře | `ContainerImageInventory` | TimeGenerated, počítače, Image, ImageTag, ImageSize, VirtualSize, spuštění, pozastavena, zastavit, se nezdařilo, SourceSystem, ID obrázku, TotalContainer |
| Kontejner protokolu | `ContainerLog` | TimeGenerated, počítač, ID bitové kopie, název kontejneru, LogEntrySource, LogEntry, SourceSystem, identifikátor ContainerID |
| Protokol služby kontejneru | `ContainerServiceLog`  | TimeGenerated, počítače, TimeOfCommand, Image, příkazu, SourceSystem, identifikátor ContainerID |
| Uzel inventáře kontejneru | `ContainerNodeInventory_CL`| TimeGenerated, počítače, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Kubernetes inventáře | `KubePodInventory_CL` | TimeGenerated, Computer, PodLabel_deployment_s, PodLabel_deploymentconfig_s, PodLabel_docker_registry_s, Name_s, Namespace_s, PodStatus_s, PodIp_s, PodUid_g, PodCreationTimeStamp_t, SourceSystem |
| Proces kontejneru | `ContainerProcess_CL` | TimeGenerated, počítače, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Kubernetes události | `KubeEvents_CL` | TimeGenerated, počítače, Name_s, ObjectKind_s, Namespace_s, Reason_s, Type_s, SourceComponent_s, SourceSystem, zprávy |

Popisky připojenou k *PodLabel* datové typy jsou vlastní štítky. Připojením PodLabel popisky uvedené v tabulce jsou uvedeny příklady. Ano `PodLabel_deployment_s`, `PodLabel_deploymentconfig_s`, `PodLabel_docker_registry_s` se liší v sadě dat vaše prostředí a obecně vypadat jako `PodLabel_yourlabel_s`.


## <a name="monitor-containers"></a>Monitorování kontejnerů
Až budete mít povoleno na portálu analýzy protokolů řešení **kontejnery** dlaždice se zobrazí souhrnné informace o kontejneru hostitelů a kontejnerů, které jsou spuštěné v hostitelích.


![Dlaždice kontejnery](./media/log-analytics-containers/containers-title.png)

Na dlaždici ukazuje přehled o tom, kolik kontejnery, které máte v prostředí a jestli se nezdařila, spuštěná nebo zastavená.

### <a name="using-the-containers-dashboard"></a>Pomocí řídicího panelu kontejnery
Klikněte **kontejnery** dlaždici. Zde se zobrazí zobrazení uspořádané podle:

- **Události kontejneru** -zobrazuje stav kontejneru a počítače s kontejnery se nezdařilo.
- **Kontejner protokoly** – zobrazuje graf soubory protokolu kontejneru vygeneroval přes čas a seznam počítačů s nejvyšší počet souborů protokolu.
- **Události Kubernetes** – zobrazuje graf Kubernetes události generované přes čas a seznam důvodů, proč pracovními stanicemi soustředěnými kolem vygenerované události. *Tato datová sada se používá jenom v prostředích Linux.*
- **Kubernetes Namespace inventáře** – zobrazuje počet obory názvů a pracovními stanicemi soustředěnými kolem a zobrazí jejich hierarchie. *Tato datová sada se používá jenom v prostředích Linux.*
- **Uzel inventáře kontejneru** – zobrazuje počet orchestration typy používané v kontejneru uzly nebo hostitele. Uzly počítače nebo hostitelé jsou také uvedeny podle počet kontejnerů. *Tato datová sada se používá jenom v prostředích Linux.*
- **Kontejner Imagí inventáře** -zobrazuje celkový počet kontejneru obrázků použitých a počet typů bitové kopie. Počet bitových kopií jsou také uvedené podle značky obrázku.
- **Kontejnery stav** -zobrazuje celkový počet kontejneru uzly nebo hostitelské počítače, které mají spuštěných kontejnerů. Počítače jsou také uvedeny podle počet probíhajících hostitele.
- **Kontejner proces** -zobrazuje spojnicový graf kontejneru procesů spuštěných v čase. Kontejnery také uvádí spuštěním příkazu/procesu v rámci kontejnerů. *Tato datová sada se používá jenom v prostředích Linux.*
- **Výkon procesoru kontejneru** – zobrazuje graf řádku průměrné využití procesoru v čase pro počítač uzly nebo hostitele. Také seznamy počítače uzly nebo hostitelů na základě průměrné využití procesoru.
- **Kontejner výkonu paměti** -znázorňuje spojnicový graf využití paměti v průběhu času. Také uvádí na název instance na základě využití paměti počítače.
- **Výkon počítače** -znázorňuje spojnicových grafů procent výkonu procesoru v čase, procentuální využití paměti nad čas a MB volného místa na disku v průběhu času. Pozastavte ukazatel myši nad kterýkoli řádek v grafu zobrazíte další podrobnosti.


Každou oblast řídicí panel je vizuální reprezentace hledání, které běží na shromážděná data.

![Řídicí panel kontejnery](./media/log-analytics-containers/containers-dash01.png)

![Řídicí panel kontejnery](./media/log-analytics-containers/containers-dash02.png)

V **stav kontejneru** oblast, klikněte na hlavní oblasti, jak je uvedeno níže.

![Stav kontejnery](./media/log-analytics-containers/containers-status.png)

Protokol hledání se otevře, zobrazení informací o stavu kontejnerů.

![Hledání protokolů pro kontejnery](./media/log-analytics-containers/containers-log-search.png)

Zde můžete upravit vyhledávací dotaz jak v hotové najít konkrétní informace, že vás zajímá. Další informace o protokolu hledání najdete v tématu [přihlásit analýzy protokolů hledání](log-analytics-log-searches.md).

## <a name="troubleshoot-by-finding-a-failed-container"></a>Řešení potíží s tak, že najdete kontejner se nezdařilo

Analýzy protokolů označí kontejneru jako **se nezdařilo** Pokud byl ukončen s nenulový ukončovací kód. Zobrazí se přehled chyb nebo selhání v prostředí v **se nezdařilo kontejnery** oblasti.

### <a name="to-find-failed-containers"></a>K vyhledání chybných kontejnery
1. Klikněte **stav kontejneru** oblasti.  
   ![Stav kontejnery](./media/log-analytics-containers/containers-status.png)
2. Protokol hledání se zobrazí stav kontejnery, podobný následujícímu.  
   ![kontejnery stavu](./media/log-analytics-containers/containers-log-search.png)
3. Klikněte na tlačítko agregovaná hodnota selhání kontejnery zobrazíte další informace. Rozbalte položku **zobrazit další** zobrazíte ID obrázku.  
   ![Neúspěšné kontejnery](./media/log-analytics-containers/containers-state-failed.png)  
4. Potom zadejte následující příkaz v vyhledávací dotaz. `ContainerInventory <ImageID>` Chcete-li zobrazit podrobnosti o bitovou kopii například velikost bitové kopie a počet zastaven a k selhání bitové kopie.  
   ![Neúspěšné kontejnery](./media/log-analytics-containers/containers-failed04.png)

## <a name="search-logs-for-container-data"></a>Hledání protokoly pro kontejner dat
Pokud se řešení potíží s konkrétní chyby, může pomoct zobrazíte, kde je vznikl ve vašem prostředí. Následující typy protokolu vám pomůže vytvořit dotazy k vrácení informací, že chcete.


- **ContainerImageInventory** – tento typ použijte, když se pokoušíte najít informace o uspořádané podle bitové kopie a chcete-li zobrazit informace o obrázku například image ID nebo velikosti.
- **ContainerInventory** – tento typ použijte, pokud chcete informace o umístění kontejneru, jaké jsou jejich názvy a co bitové kopie, že používáte.
- **ContainerLog** – tento typ použijte, pokud chcete najít informace o konkrétní chybě protokolu a položky.
- **ContainerNodeInventory_CL** tento typ můžete použít informace o uzlu hostitele nebo kde je umístěný kontejnery. Poskytuje Docker verze, typ orchestration, úložiště a informace o síti.
- **ContainerProcess_CL** pomocí tohoto typu lze rychle zobrazit proces, který běží v kontejneru.
- **ContainerServiceLog** – tento typ použijte, když se pokoušíte najít informace protokolu auditu pro démona Docker, jako je například spuštění, zastavení, odstraňovat nebo příkazy pro vyžádání obsahu.
- **KubeEvents_CL** použít tento typ sledovat Kubernetes události.
- **KubePodInventory_CL** tento typ použijte, pokud chcete zjistit informace o clusteru hierarchie.


### <a name="to-search-logs-for-container-data"></a>K vyhledání protokoly pro kontejner dat
* Vyberte obrázek, který znáte selhával a najít v souborech protokolů chyb pro ni. Začněte tím, že název kontejneru, který běží této bitové kopie s hledání **ContainerInventory** vyhledávání. Například vyhledejte `ContainerInventory | where Image == "ubuntu" and ContainerState == "Failed"`  
    ![Hledat kontejnery Ubuntu](./media/log-analytics-containers/search-ubuntu.png)

  Název kontejneru Další **název**a vyhledejte tyto protokoly. V tomto příkladu je to `ContainerLog | where Name == "cranky_stonebreaker"`.

**Informace o zobrazení výkonu**

Pokud jste od vytvořit dotazy, může pomoct vidět co je možné nejprve. Například pokud chcete zobrazit všechny údaje o výkonu, zkuste široký dotaz zadáním následujících vyhledávací dotaz.

```
Perf
```

![kontejnery výkonu](./media/log-analytics-containers/containers-perf01.png)

Data výkonu, která se zobrazuje v určitém kontejneru zadáním názvu je napravo od dotazu, můžete určit obor.

```
Perf <containerName>
```

Který zobrazí seznam metriky výkonu, které se shromažďují pro jednotlivé kontejneru.

![kontejnery výkonu](./media/log-analytics-containers/containers-perf03.png)

## <a name="example-log-search-queries"></a>Příklad protokolu vyhledávací dotazy
Je často užitečné k vytvoření dotazů počínaje příklad nebo dva a pak úpravy, aby odpovídaly vašemu prostředí. Jako počáteční bod, můžete vyzkoušet **ukázkové dotazy** oblasti, které vám umožní vytvořit složitější dotazy.

![Kontejnery dotazy](./media/log-analytics-containers/containers-queries.png)


## <a name="saving-log-search-queries"></a>Uložení protokolu vyhledávacích dotazů
Uložení dotazů je standardní funkce v analýzy protokolů. Je uložíte, budete mít ty, které jste najít užitečné užitečný pro budoucí použití.

Jakmile vytvoříte dotaz, který pro vás užitečné, uložte kliknutím na **Oblíbené** v horní části stránky hledání protokolu. Potom ho později snadno přístup **vlastní řídicí panel** stránky.

## <a name="next-steps"></a>Další postup
* [V protokolech Hledat](log-analytics-log-searches.md) zobrazíte podrobné kontejneru datových záznamů.
