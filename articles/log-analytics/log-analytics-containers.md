---
title: Řešení pro monitorování kontejnerů ve službě Azure Log Analytics | Dokumentace Microsoftu
description: Řešení monitorování kontejnerů ve službě Log Analytics umožňuje zobrazit a spravovat Docker a Windows hostitelích kontejnerů na jednom místě.
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
ms.date: 11/13/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 6f4f2f6a7999b2d26e198a11bb7cee27991a775b
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51711952"
---
# <a name="container-monitoring-solution-in-log-analytics"></a>Řešení pro monitorování kontejnerů ve službě Log Analytics

![Symbol kontejnery](./media/log-analytics-containers/containers-symbol.png)

Tento článek popisuje, jak nastavit a začít používat řešení pro monitorování kontejnerů ve službě Log Analytics, což vám umožňuje zobrazit a spravovat vaše Docker a Windows hostitelích kontejnerů na jednom místě. Docker je software virtualizace systému použité k vytvoření kontejnerů, které automatizují nasazení softwaru na infrastrukturu IT.

Toto řešení ukazuje, které kontejnery jsou spuštěná, jaké image kontejnerů běží a kde kontejnery běží. Můžete zobrazit podrobné informace o auditování zobrazuje příkazy, které používá s kontejnery. A kontejnerů můžete řešit pomocí zobrazení a hledání centralizované protokoly bez nutnosti Chcete-li zobrazit hostitele Docker nebo Windows. Můžete najít kontejnery, které mohou být na hostiteli hlučného a využívání nadbytečné prostředky. A můžete zobrazit centralizované procesoru, paměti, úložiště a využití a výkonu informace o síti pro kontejnery. V počítačích se systémem Windows, můžete centralizovat a porovnat protokoly ze systému Windows Server Hyper-V a kontejnery Dockeru. Řešení podporuje následující orchestrátorů kontejnerů:

- Docker Swarm
- DC/OS
- Kubernetes
- Service Fabric
- Red Hat OpenShift

Pokud vás zajímají sledování výkonu vašich úloh nasazených do hostovaného prostředí Kubernetes ve službě Azure Kubernetes Service (AKS), najdete v článku [monitorování Azure Kubernetes Service](../azure-monitor/insights/container-insights-overview.md). Řešení pro monitorování kontejnerů nezahrnuje podporu pro monitorování této platformy.  

Následující diagram znázorňuje vztahy mezi různými hostitelích kontejnerů a agenty pomocí Log Analytics.

![Diagram kontejnery](./media/log-analytics-containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>Požadavky na systém a podporovaných platformách

Než začnete, projděte si následující podrobnosti, které chcete ověřit, že máte splněné požadavky.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Podpora řešení pro monitorování kontejnerů Dockeru Orchestrator a verze operačního systému platformy
Následující tabulka popisuje orchestraci Dockeru a operační systém sledování podpory kontejneru inventáře, výkonu a protokolech pomocí Log Analytics.   

| | ACS | Linux | Windows | Kontejner<br>Inventarizace | Image<br>Inventarizace | Node<br>Inventarizace | Kontejner<br>Výkon | Kontejner<br>Událost | Událost<br>Protokol | Kontejner<br>Protokol |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Kubernetes | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mesosphere<br>DC/OS | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Swarm | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Služba<br>Fabric | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Red Hat Open<br>Shift | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
| Windows Server<br>(samostatný) | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Linux Server<br>(samostatný) | | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |


### <a name="docker-versions-supported-on-linux"></a>Verze dockeru v Linuxu podporováno

- Docker 1.11 k 1.13
- Docker CE a EE v17.06

### <a name="x64-linux-distributions-supported-as-container-hosts"></a>x64 distribucí systému Linux podporované jako hostitelích kontejnerů


- Ubuntu 14.04 LTS a 16.04 LTS
- CoreOS(stable)
- Amazon Linux 2016.09.0
- openSUSE 13.2
- využitím openSUSE LEAP 42.2
- CentOS 7.2 a 7.3
- SLES 12
- RHEL 7.2 a 7.3
- Red Hat OpenShift Container Platform (OCP) 3.4 a 3.5
- ACS Mesosphere DC/OS 1.7.3 až 1.8.8
- ACS Kubernetes 1.4.5 na 1.6
    - Události Kubernetes, Kubernetes inventáře a kontejner procesy jsou podporovány pouze s verzí 1.4.1-45 a později agenta Log Analytics pro Linux
- ACS Docker Swarm

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)] 

### <a name="supported-windows-operating-system"></a>Podporovaný operační systém Windows

- Windows Server 2016
- Windows 10 Anniversary Edition (Professional nebo Enterprise)

### <a name="docker-versions-supported-on-windows"></a>Verze dockeru, které jsou podporované ve Windows

- Dockeru 1.12 a 1.13
- Docker 17.03.0 a novější

## <a name="installing-and-configuring-the-solution"></a>Instalace a konfigurace řešení
K instalaci a konfiguraci řešení můžete použít následující informace.

1. Přidejte řešení pro monitorování kontejnerů do pracovního prostoru Log Analytics z [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) nebo pomocí procesu popsaného v [přidání řešení Log Analytics z Galerie řešení](../azure-monitor/insights/solutions.md).

2. Nainstalovat a používat Docker pomocí agenta Log Analytics. Na základě vašeho operačního systému a orchestrátor Docker, můžete použít následující metody konfigurace agenta.
  - Pro samostatné hostitele:
    - Na podporovaných operačních systémech Linux, instalace a spuštění Dockeru a pak nainstalujte a nakonfigurujte [agenta Log Analytics pro Linux](log-analytics-quick-collect-linux-computer.md).  
    - V systému CoreOS nelze spustit agenta Log Analytics pro Linux. Místo toho spustíte kontejnerizovaných verzi agenta Log Analytics pro Linux. Kontrola [hostitelé kontejneru s Linuxem včetně CoreOS](#for-all-linux-container-hosts-including-coreos) nebo [hostitelích kontejnerů Azure Government s Linuxem, včetně CoreOS](#for-all-azure-government-linux-container-hosts-including-coreos) při práci s kontejnery v cloudu Azure Government.
    - V systému Windows Server 2016 a Windows 10 nainstalujte klienta a modul Docker potom připojit agenta k shromažďování informací a jejich odesílání do Log Analytics. Kontrola [instalace a konfigurace hostitelů Windows kontejneru](#install-and-configure-windows-container-hosts) Pokud používáte prostředí Windows.
  - Orchestrace prostřednictvím Docker více hostitelů:
    - Pokud máte v prostředí Red Hat OpenShift, přečtěte si [konfigurace agenta Log Analytics pro Red Hat OpenShift](#configure-an-oms-agent-for-red-hat-openshift).
    - Pokud máte cluster Kubernetes pomocí služby Azure Container Service:
       - Kontrola [konfigurace agenta Log Analytics Linux pro Kubernetes](#configure-an-oms-linux-agent-for-kubernetes).
       - Kontrola [konfigurace agenta Log Analytics Windows pro Kubernetes](#configure-an-oms-windows-agent-for-kubernetes).
       - Kontrola [pomocí Helm nasadit agenta Log Analytics na Linux Kubernetes](#use-helm-to-deploy-oms-agent-on-linux-kubernetes).
    - Pokud máte cluster Azure Container Service DC/OS, další informace najdete na [monitorování clusteru služby Azure Container Service DC/OS pomocí Log Analytics](../container-service/dcos-swarm/container-service-monitoring-oms.md).
    - Pokud máte prostředí s režimem Docker Swarm, další informace najdete na [konfigurace agenta Log Analytics pro Docker Swarm](#configure-an-oms-agent-for-docker-swarm).
    - Pokud máte cluster Service Fabric, další informace najdete na [monitorování kontejnerů pomocí Log Analytics Log Analytics](../service-fabric/service-fabric-diagnostics-oms-containers.md).

Zkontrolujte [modul Docker na Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon) Další informace o tom, jak nainstalovat a nakonfigurovat váš moduly Docker v počítačích se systémem Windows.

> [!IMPORTANT]
> Docker musí běžet **před** nainstalujete [agenta Log Analytics pro Linux](log-analytics-quick-collect-linux-computer.md) v hostitelích kontejnerů. Pokud jste již nainstalovali agenta před instalací Dockeru, budete muset přeinstalovat agenta Log Analytics pro Linux. Další informace o Dockeru najdete v článku [Docker webu](https://www.docker.com).


### <a name="install-and-configure-linux-container-hosts"></a>Instalace a konfigurace hostitelé kontejneru s Linuxem

Po instalaci Dockeru, použijte následující nastavení pro hostitele kontejneru konfigurace agenta pro použití s Dockerem. Nejprve potřebujete ID pracovního prostoru Log Analytics a klíč, který najdete na webu Azure Portal. V pracovním prostoru, klikněte na tlačítko **rychlý Start** > **počítače** zobrazíte vaše **ID pracovního prostoru** a **primární klíč**.  Obě hodnoty zkopírujte a vložte do oblíbeného editoru.

**Pro všechny hostitele kontejneru Linuxu s výjimkou CoreOS:**

- Další informace a pokyny k instalaci agenta Log Analytics pro Linux najdete v tématu [přehled agenta Log Analytics](log-analytics-agent-overview.md).

**Pro včetně CoreOS všichni hostitelé s kontejneru Linuxu:**

Spuštění kontejneru, který chcete monitorovat. Upravit a pomocí následujícího příkladu:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**Pro všechny státní správy Azure s Linuxem kontejneru hostitele včetně CoreOS:**

Spuštění kontejneru, který chcete monitorovat. Upravit a pomocí následujícího příkladu:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**Přepínání z pomocí nainstalovaného agenta na jeden v kontejneru Linuxu**

Pokud dříve použít přímo nainstalován agent a místo toho použít agenta spuštěného v kontejneru, je nutné nejprve odebrat agenta Log Analytics pro Linux. Zobrazit [odinstalace agenta Log Analytics pro Linux](log-analytics-quick-collect-linux-computer.md) pochopit, jak úspěšně odinstalace agenta.  

#### <a name="configure-a-log-analytics-agent-for-docker-swarm"></a>Konfigurace agenta Log Analytics pro Docker Swarm

Agenta Log Analytics jako globální službu můžete spouštět Docker Swarm. Pokud chcete vytvořit službu agenta Log Analytics, použijte následující informace. Je třeba zadat ID pracovního prostoru Log Analytics a primární klíč.

- Spusťte následující příkaz na hlavní uzel.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

##### <a name="secure-secrets-for-docker-swarm"></a>Zabezpečené tajné kódy pro Docker Swarm

Pro Docker Swarm Jakmile je vytvořen tajný klíč pro ID pracovního prostoru a primární klíč, použijte následující informace k vytvoření tajných informací.

1. Spusťte následující příkaz na hlavní uzel.

    ```
    echo "WSID" | docker secret create WSID -
    echo "KEY" | docker secret create KEY -
    ```

2. Ověřte, že byly správně vytvořeny tajných kódů.

    ```
    keiko@swarmm-master-13957614-0:/run# sudo docker secret ls
    ```

    ```
    ID                          NAME                CREATED             UPDATED
    j2fj153zxy91j8zbcitnjxjiv   WSID                43 minutes ago      43 minutes ago
    l9rh3n987g9c45zffuxdxetd9   KEY                 38 minutes ago      38 minutes ago
    ```

3. Spusťte následující příkaz pro připojení tajné klíče kontejnerizovaných agenta Log Analytics.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="configure-a-log-analytics-agent-for-red-hat-openshift"></a>Konfigurace agenta Log Analytics pro Red Hat OpenShift
Existují tři způsoby, jak přidat agenta Log Analytics do Red Hat OpenShift spustíte shromažďování dat pro monitorování kontejnerů.

* [Instalace agenta Log Analytics pro Linux](log-analytics-quick-collect-linux-computer.md) přímo na každém uzlu Openshiftu  
* [Povolení rozšíření Log Analytics pro virtuální počítač](log-analytics-quick-collect-azurevm.md) na každém uzlu OpenShift v Azure  
* Instalace agenta Log Analytics jako OpenShift démon sadu  

V této části probereme kroky potřebné k instalaci agenta Log Analytics jako démon OpenShift-set.  

1. Přihlaste se k hlavnímu uzlu OpenShift a zkopírujte tento soubor yaml [ocp-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) z Githubu k hlavnímu uzlu a změňte hodnotu s ID vašeho pracovního prostoru Log Analytics a primární klíč.
2. Spuštěním následujících příkazů vytvořte projekt pro Log Analytics a nastavit uživatelský účet.

    ```
    oadm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oadm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

4. Pokud chcete nasadit sady démon, spusťte následující příkaz:

    `oc create -f ocp-omsagent.yaml`

5. Ověřte, zda že je nakonfigurován a funguje správně, zadejte následující příkaz:

    `oc describe daemonset omsagent`  

    a výstup by měl vypadat:

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

Pokud chcete pro použití tajných kódů k zabezpečení ID pracovního prostoru Log Analytics a primární klíč, když pomocí souboru yaml démon sady agenta Log Analytics, proveďte následující kroky.

1. Přihlaste se k hlavnímu uzlu OpenShift a zkopírujte tento soubor yaml [ocp-ds-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) a tajný kód generování skriptu [ocp-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) z Githubu.  Tento skript vytvoří soubor yaml tajných kódů pro ID pracovního prostoru Log Analytics a primární klíč pro zabezpečení vašich secrete informace.  
2. Spuštěním následujících příkazů vytvořte projekt pro Log Analytics a nastavit uživatelský účet. Tajný kód generování skriptu vyzve k zadání ID vašeho pracovního prostoru Log Analytics <WSID> a primární klíč <KEY> a po dokončení se vytvoří soubor ocp secret.yaml.  

    ```
    oadm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oadm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

4. Nasaďte soubor tajného kódu spuštěním následujícího:

    `oc create -f ocp-secret.yaml`

5. Ověření nasazení spuštěním následujícího:

    `oc describe secret omsagent-secret`  

    a výstup by měl vypadat:  

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

6. Nasaďte soubor yaml démon sady agenta Log Analytics spuštěním následujícího:

    `oc create -f ocp-ds-omsagent.yaml`  

7. Ověření nasazení spuštěním následujícího:

    `oc describe ds oms`

    a výstup by měl vypadat:

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

#### <a name="configure-a-log-analytics-linux-agent-for-kubernetes"></a>Konfigurace agenta Log Analytics Linux pro Kubernetes

Pro Kubernetes použijte skript se vygenerovat soubor yaml tajných kódů pro ID pracovního prostoru a primární klíč pro instalaci agenta Log Analytics pro Linux. Na [Log Analytics Docker Kubernetes Githubu](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) stránce, jsou soubory, které můžete použít s nebo bez něj tajných informací.

- Výchozí Log Analytics agenta pro Linux DaemonSet nemá tajných informací (omsagent.yaml)
- Agenta Log Analytics pro Linux DaemonSet soubor yaml používá tajných informací (omsagent-ds-secrets.yaml) pomocí tajného kódu generování skriptů se vygenerovat soubor yaml (omsagentsecret.yaml) tajných kódů.

Můžete vytvořit omsagent DaemonSets s nebo bez něj tajných kódů.

**Výchozí soubor yaml OMSagent DaemonSet bez tajných kódů**

- Výchozí Log Analytics DaemonSet yaml soubor agenta, nahraďte `<WSID>` a `<KEY>` WSID a klíč. Zkopírujte soubor do hlavního uzlu a spusťte následující příkaz:

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**Výchozí soubor yaml OMSagent DaemonSet s tajnými kódy**

1. Chcete-li používat službu Log Analytics agenta DaemonSet pomocí tajné informace, tvorba tajné klíče.
    1. Zkopírujte skript a soubor tajného kódu šablony a ujistěte se, že jsou ve stejném adresáři.
        - Generování skriptu - secret gen.sh tajný klíč
        - Šablona tajné – template.yaml tajný klíč
    2. Spusťte skript, jako v následujícím příkladu. Skript vyzve k zadání ID pracovního prostoru Log Analytics a primární klíč a po při zadávání, skript vytvoří soubor yaml tajného kódu, můžete ji spustit.   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. Vytvořte pod tajných kódů spuštěním následujícího:
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. Pokud chcete ověřit, spusťte následující příkaz:

        ```
        keiko@ubuntu16-13db:~# sudo kubectl get secrets
        ```

        Výstup by měl vypadat:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        ```

        ```
        keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
        ```

        Výstup by měl vypadat:

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

    5. Vytvoření vašeho omsagent démon sady spuštěním ``` sudo kubectl create -f omsagent-ds-secrets.yaml ```

2. Ověřte, že agenta Log Analytics DaemonSet běží, podobný následujícímu:

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```


Pro Kubernetes vygenerujte soubor yaml tajných kódů pro ID pracovního prostoru a primární klíč pro agenta Log Analytics pro Linux pomocí skriptu. Následující příklad informace s [soubor yaml omsagent](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml) zabezpečit tajné informace.

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

#### <a name="configure-a-log-analytics-windows-agent-for-kubernetes"></a>Konfigurace agenta Log Analytics Windows pro Kubernetes
Pro Windows Kubernetes pomocí skriptu se vygenerovat soubor yaml tajných kódů pro ID pracovního prostoru a primární klíč pro instalaci agenta Log Analytics. Na [Log Analytics Docker Kubernetes Githubu](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) stránce, jsou soubory, které můžete použít s tajných informací.  Je potřeba nainstalovat agenta Log Analytics samostatně pro hlavních a agentských uzlů.  

1. Použít agenta Log Analytics DaemonSet pomocí tajných informací na hlavní uzel, přihlaste se a nejdříve vytvořit tajné klíče.
    1. Zkopírujte skript a soubor tajného kódu šablony a ujistěte se, že jsou ve stejném adresáři.
        - Generování skriptu - secret gen.sh tajný klíč
        - Šablona tajné – template.yaml tajný klíč

    2. Spusťte skript, jako v následujícím příkladu. Skript vyzve k zadání ID pracovního prostoru Log Analytics a primární klíč a po při zadávání, skript vytvoří soubor yaml tajného kódu, můžete ji spustit.   

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. Vytvoření vašeho omsagent démon sady spuštěním ``` kubectl create -f omsagentsecret.yaml ```
    4. Pokud chcete zkontrolovat, spusťte následující příkaz:

        ```
        root@ubuntu16-13db:~# kubectl get secrets
        ```

        Výstup by měl vypadat:

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

    5. Vytvoření vašeho omsagent démon sady spuštěním ```kubectl create -f ws-omsagent-de-secrets.yaml```

2. Ověřte, že agenta Log Analytics DaemonSet běží, podobný následujícímu:

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Agent na uzlu pracovního procesu, na kterém běží Windows, postupujte podle kroků v části [instalace a konfigurace hostitelů Windows kontejneru](#install-and-configure-windows-container-hosts).

#### <a name="use-helm-to-deploy-log-analytics-agent-on-linux-kubernetes"></a>Nasadit agenta Log Analytics na Linuxu Kubernetes pomocí Helm
Pokud chcete nasadit agenta Log Analytics v prostředí Linux Kubernetes pomocí helm, proveďte následující kroky.

1. Vytvoření vašeho omsagent démon sady spuštěním ```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms```
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
3. Stav omsagent můžete zjistit spuštěním: ```helm status "omsagent"``` a výstup bude vypadat nějak takto:

    ```
    keiko@k8s-master-3814F33-0:~$ helm status omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED
 
    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     17m
 
    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         17m
    ```
Další informace, navštivte prosím [kontejner grafu helmu řešení](https://aka.ms/omscontainerhelm).

### <a name="install-and-configure-windows-container-hosts"></a>Instalace a konfigurace hostitelích kontejnerů Windows

Použijte informace v části instalace a konfigurace hostitelích kontejnerů Windows.

#### <a name="preparation-before-installing-windows-agents"></a>Příprava před instalací agentů Windows

Před instalací agentů v počítačích se systémem Windows, musíte nakonfigurovat služba Docker. Konfigurace umožňuje agenta Windows nebo rozšíření virtuálního počítače Log Analytics, použití soket Docker TCP tak, aby agenti vzdálený přístup k démona Dockeru a k zaznamenání dat pro monitorování.

##### <a name="to-start-docker-and-verify-its-configuration"></a>Spuštění Docker a ověřit jeho konfiguraci

Existují kroky potřebné k nastavení pro systém Windows Server s názvem kanálu TCP:

1. Ve Windows Powershellu povolte kanálu TCP a pojmenovaného kanálu.

    ```
    Stop-Service docker
    dockerd --unregister-service
    dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
    Start-Service docker
    ```

2. Konfigurace Dockeru pomocí konfiguračního souboru pro kanálu TCP a pojmenovaného kanálu. Konfigurační soubor je umístěn v C:\ProgramData\docker\config\daemon.json.

    V souboru daemon.json budete potřebovat následující:

    ```
    {
    "hosts": ["tcp://0.0.0.0:2375", "npipe://"]
    }
    ```

Další informace o konfiguraci démona Dockeru, který se používá s kontejnery Windows najdete v tématu [modul Docker na Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon).


#### <a name="install-windows-agents"></a>Instalace agentů Windows

Pokud chcete povolit monitorování kontejnerů Windows a Hyper-V, nainstalujte Microsoft Monitoring Agent (MMA) v počítačích s Windows, které jsou hostitelé kontejneru. Pro počítače se systémem Windows ve vašem místním prostředí, najdete v článku [počítače Windows se připojit ke službě Log Analytics](log-analytics-agent-windows.md). Pro virtuální počítače spuštěné v Azure a jejich připojení k Log Analytics pomocí [rozšíření virtuálního počítače](log-analytics-quick-collect-azurevm.md).

Můžete monitorovat kontejnery Windows se spuštěnou v Service Fabric. Nicméně pouze [virtuální počítače provozované v Azure](log-analytics-quick-collect-azurevm.md) a [počítače se systémem Windows ve vašem místním prostředí](log-analytics-agent-windows.md) jsou aktuálně podporovány pro Service Fabric.

Můžete ověřit, jestli je správně nastavené řešení pro monitorování kontejnerů pro Windows. Chcete-li zkontrolovat, zda byla sada management pack správně ke stažení, vyhledejte *ContainerManagement.xxx*. Soubory musí být ve složce C:\Program Files\Microsoft Monitoring Agent\Agent\Health State\Management aktualizace.


## <a name="solution-components"></a>Součásti řešení

Na webu Azure Portal, přejděte *Galerie řešení* a přidejte **řešení pro monitorování kontejnerů**. Pokud používáte Windows agenty, je při přidání tohoto řešení následující sady management pack nainstalované na každém počítači s agentem. Žádná konfigurace ani údržba je vyžadován pro sadu management pack.

- *ContainerManagement.xxx* nainstalované v C:\Program Files\Microsoft Monitoring Agent\Agent\Health State\Management SP

## <a name="container-data-collection-details"></a>Podrobnosti o kontejneru dat kolekce
Řešení pro monitorování kontejnerů různých metrik a protokolů shromažďuje údaje o výkonu z hostitelů kontejnerů a kontejnery pomocí agentů, které povolíte.

Data jsou shromažďována každé 3 minuty, následující typy agenta.

- [Agenta log Analytics pro Linux](log-analytics-quick-collect-linux-computer.md)
- [Agenta Windows](log-analytics-agent-windows.md)
- [Rozšíření log Analytics virtuální počítače](log-analytics-quick-collect-azurevm.md)


### <a name="container-records"></a>Záznamy kontejneru

Následující tabulka uvádí příklady záznamy shromážděné řešení pro monitorování kontejnerů a datové typy, které se zobrazí ve výsledcích hledání v protokolu.

| Typ dat | Datový typ v prohledávání protokolu | Fields (Pole) |
| --- | --- | --- |
| Výkon pro hostitele a kontejnery | `Perf` | Počítače, název_objektu, CounterName &#40;% času procesoru, disku přečte MB, zapíše MB, MB využití paměti, disku sítě přijatých bajtů, síť posílat bajtů, procesor doby využití, sítě&#41;, CounterValue TimeGenerated, Cesta_k_čítači, SourceSystem |
| Kontejner inventáře | `ContainerInventory` | TimeGenerated, počítače, název kontejneru, ContainerHostname, Image, ImageTag, ContainerState, ukončovací kód, EnvironmentVar, příkaz, čas vytvoření, StartedTime, FinishedTime, SourceSystem, identifikátor ContainerID, ID obrázku |
| Inventář imagí kontejnerů | `ContainerImageInventory` | TimeGenerated, počítače, Image, ImageTag, ImageSize, VirtualSize, spuštění, pozastavení, zastavení, se nezdařilo, SourceSystem, ID obrázku, TotalContainer |
| Protokol kontejneru | `ContainerLog` | TimeGenerated, počítač, ID bitové kopie, název kontejneru, LogEntrySource LogEntry, SourceSystem, identifikátor ContainerID |
| Protokol služby kontejneru | `ContainerServiceLog`  | TimeGenerated, počítače, TimeOfCommand, Image, příkaz, SourceSystem, identifikátor ContainerID |
| Inventář kontejnerových uzlů | `ContainerNodeInventory_CL`| TimeGenerated, počítače, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventář Kubernetes | `KubePodInventory_CL` | TimeGenerated, Computer, PodLabel_deployment_s, PodLabel_deploymentconfig_s, PodLabel_docker_registry_s, Name_s, Namespace_s, PodStatus_s, PodIp_s, PodUid_g, PodCreationTimeStamp_t, SourceSystem |
| Kontejnerový proces | `ContainerProcess_CL` | TimeGenerated, počítače, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Události Kubernetes | `KubeEvents_CL` | TimeGenerated, počítače, Name_s, ObjectKind_s, Namespace_s, Reason_s, Type_s, SourceComponent_s, SourceSystem, zprávy |

Popisky připojenou k *PodLabel* datové typy jsou vlastní popisky. Připojený PodLabel popisky zobrazeny v tabulce jsou příklady. Ano `PodLabel_deployment_s`, `PodLabel_deploymentconfig_s`, `PodLabel_docker_registry_s` se liší ve vašem prostředí datové sady a obecně se podobají `PodLabel_yourlabel_s`.


## <a name="monitor-containers"></a>Monitorování kontejnerů
Až budete mít povolené na portálu Log Analytics, řešení **kontejnery** dlaždici se zobrazí souhrnné informace o vaší hostitelích kontejnerů a kontejnery spuštěné v hostitelích.


![Kontejnery dlaždice](./media/log-analytics-containers/containers-title.png)

Dlaždice zobrazí přehled o tom, kolik kontejnerů, je nutné v prostředí a určuje, zda se nepovedlo, spuštěná nebo zastavená.

### <a name="using-the-containers-dashboard"></a>Na řídicím panelu kontejnery
Klikněte na tlačítko **kontejnery** dlaždici. Tady se zobrazí zobrazení uspořádané podle:

- **Události kontejneru** – zobrazuje stav kontejneru a počítače s neúspěšné kontejnery.
- **Protokoly kontejneru** – zobrazuje graf, kontejner soubory protokolu vygenerované průběhu času a seznam počítačů s nejvyšší počet souborů protokolu.
- **Události Kubernetes** – zobrazuje graf, Kubernetes události generované průběhu času a seznam z důvodů, proč podů generovány události. *Tato datová sada se používá jenom v prostředí Linux.*
- **Kubernetes Namespace inventáře** – zobrazuje počet obory názvů a podů a jejich hierarchii. *Tato datová sada se používá jenom v prostředí Linux.*
- **Inventář kontejnerových uzlů** – zobrazuje počet typů orchestration používá na uzlech a hostitelích kontejnerů. Počítač uzlů a hostuje jsou také uvedeny podle počtu kontejnerů. *Tato datová sada se používá jenom v prostředí Linux.*
- **Inventář Imagí kontejneru** – zobrazuje celkový počet imagí kontejneru použít a počet typy obrázků. Počet obrázků jsou také uvedeny podle obrázku značky.
- **Stav kontejneru** – zobrazuje celkový počet kontejnerů uzly/hostitelských počítačů, které mají spuštěné kontejnery. Počítače jsou také uvedeny podle počtu spuštění hostitele.
- **Kontejnerový proces** – zobrazuje spojnicový graf kontejneru procesů spuštěných v čase. Kontejnery jsou také uvedeny spuštěním příkazu/procesu v rámci kontejnerů. *Tato datová sada se používá jenom v prostředí Linux.*
- **Výkon procesoru kontejneru** – zobrazuje spojnicový graf průměrné využití procesoru v čase pro počítač uzly/hostitele. Také seznamy počítač uzlů a hostuje v závislosti na průměrném využití procesoru.
- **Výkon paměti kontejneru** -zobrazí spojnicového grafu využití paměti v průběhu času. Také uvádí na název instance na základě využití paměti počítače.
- **Výkon počítače** -zobrazí spojnicové grafy procento výkon procesoru v čase, procento využití paměti v čase a MB volného místa na disku v průběhu času. Při najetí myší nad libovolný řádek v grafu zobrazíte další podrobnosti.


Každé oblasti řídicího panelu je vizuální znázornění vyhledávání, která se spouští na shromážděná data.

![Řídicí panel kontejnery](./media/log-analytics-containers/containers-dash01.png)

![Řídicí panel kontejnery](./media/log-analytics-containers/containers-dash02.png)

V **stav kontejneru** oblasti, jak je znázorněno níže, klikněte na tlačítko horní části.

![Stav kontejneru](./media/log-analytics-containers/containers-status.png)

Hledání v protokolu se otevře, zobrazení informací o stavu kontejnery.

![Prohledávání protokolů pro kontejnery](./media/log-analytics-containers/containers-log-search.png)

Tady můžete upravit vyhledávací dotaz upravit ho k nalezení konkrétních informací, že máte zájem. Další informace o hledání v protokolu najdete v tématu [prohledávání protokolů v Log Analytics](log-analytics-queries.md).

## <a name="troubleshoot-by-finding-a-failed-container"></a>Řešení potíží s tím, že hledá selhání kontejneru

Log Analytics označí jako kontejner **neúspěšné** Pokud se ukončil s nenulový ukončovací kód. Zobrazí se přehled chyb a selhání v prostředí **kontejnery se nezdařilo** oblasti.

### <a name="to-find-failed-containers"></a>Chcete-li najít neúspěšné kontejnery
1. Klikněte na tlačítko **stav kontejneru** oblasti.  
   ![Stav kontejneru](./media/log-analytics-containers/containers-status.png)
2. Hledání v protokolu se otevře a zobrazí stav své kontejnery, podobný následujícímu.  
   ![Stav kontejnerů](./media/log-analytics-containers/containers-log-search.png)
3. Dále klikněte na agregovanou hodnotu neúspěšné kontejnery zobrazíte další informace. Rozbalte **zobrazit více** zobrazíte ID image.  
   ![Neúspěšné kontejnery](./media/log-analytics-containers/containers-state-failed.png)  
4. Dále zadejte následující příkaz do vyhledávacího dotazu. `ContainerInventory <ImageID>` Chcete-li zobrazit informace o imagi, jako je například velikost bitové kopie a počet obrázků zastavena a selhání.  
   ![Neúspěšné kontejnery](./media/log-analytics-containers/containers-failed04.png)

## <a name="search-logs-for-container-data"></a>Hledat protokoly pro kontejner dat
Pokud řešíte potíže s konkrétní chyba, může být snazší zobrazíte, kde se děje ve vašem prostředí. Následující typy protokolů umožňují dotazů, které vrátí informace, že chcete vytvořit.


- **ContainerImageInventory** – tento typ použijte, pokud se snažíte se najít informace o uspořádané podle obrázku a chcete-li zobrazit informace o obrázku například image ID nebo velikosti.
- **ContainerInventory** – tento typ použijte, pokud chcete získat informace o umístění kontejneru, jaké jsou jejich názvy a co spuštěnými bitové kopie.
- **ContainerLog** – tento typ použijte, pokud chcete najít informace o konkrétní chybě protokolu a položky.
- **ContainerNodeInventory_CL** pomocí tohoto typu potřebujete informace o hostiteli/uzlu, kde je umístěný kontejnery. Poskytne vám verze Dockeru, typ Orchestrace, úložiště a informace o síti.
- **ContainerProcess_CL** pomocí tohoto typu můžete rychle zobrazit proces, který běží v kontejneru.
- **ContainerServiceLog** – tento typ použijte, pokud se snažíte se najít informace protokolu auditu pro démona Dockeru, například spuštění, zastavení, odstranit nebo o přijetí změn příkazy.
- **KubeEvents_CL** pomocí tohoto typu můžete zobrazit události Kubernetes.
- **KubePodInventory_CL** tento typ použijte, pokud chcete se dozvědět informace o hierarchii clusteru.


### <a name="to-search-logs-for-container-data"></a>Chcete-li hledat protokoly pro kontejner dat.
* Vyberte bitovou kopii, o kterém víte nedávno selhaly a najít protokoly chyb pro něj. Začněte tím, jak najít název kontejneru, na kterém běží této bitové kopie s **ContainerInventory** vyhledávání. Například vyhledejte `ContainerInventory | where Image == "ubuntu" and ContainerState == "Failed"`  
    ![Hledat kontejnery Ubuntu](./media/log-analytics-containers/search-ubuntu.png)

  Název kontejneru vedle **název**a vyhledejte tyto protokoly. V tomto příkladu je to `ContainerLog | where Name == "cranky_stonebreaker"`.

**Informace o zobrazení výkonu**

Když začínáte vytvářet dotazy, může pomoct co je možné nejprve. Pokud chcete zobrazit všechny údaje o výkonu, zkuste například široké dotazu zadáním následujícím vyhledávacím dotazem.

```
Perf
```

![kontejnery výkonu](./media/log-analytics-containers/containers-perf01.png)

Můžete omezit rozsah data o výkonu, jak vidíte na konkrétní kontejner zadáním názvu je napravo od vašeho dotazu.

```
Perf <containerName>
```

Která zobrazuje seznam metrik výkonu shromážděných pro kontejner.

![kontejnery výkonu](./media/log-analytics-containers/containers-perf03.png)

## <a name="example-log-search-queries"></a>Příklad protokolu vyhledávacích dotazů
Často je užitečné k sestavování dotazů od jedné až dvou příklad a následnou úpravou jim budou odpovídat vašemu prostředí. Jako výchozí bod můžete experimentovat s **ukázkové dotazy** oblasti, které vám pomůžou vytvářet složitější dotazy.

![Kontejnery dotazy](./media/log-analytics-containers/containers-queries.png)


## <a name="saving-log-search-queries"></a>Ukládá vyhledávací dotazy protokolů
Ukládání dotazů je standardní funkce v Log Analytics. Uložením, budete mít ty, které připadají užitečné po ruce pro pozdější použití.

Jakmile vytvoříte dotaz, který je pro vás užitečné, uložte ho kliknutím **Oblíbené** v horní části stránky prohledávání protokolů. Pak můžete jednoduše přejít později **Můj řídicí panel** stránky.

## <a name="next-steps"></a>Další postup
* [Hledání protokolů](log-analytics-queries.md) zobrazíte podrobné kontejneru datových záznamů.
