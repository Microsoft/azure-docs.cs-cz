---
title: Řešení monitorování kontejnerů v Azure Monitor | Microsoft Docs
description: Řešení pro monitorování kontejnerů v Azure Monitor vám pomůže zobrazit a spravovat hostitele kontejnerů pro Docker a Windows v jednom umístění.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/22/2019
ms.openlocfilehash: 7dab80f901304a727b75c7861c5d865fee03bec3
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2020
ms.locfileid: "77482986"
---
# <a name="container-monitoring-solution-in-azure-monitor"></a>Řešení pro monitorování kontejnerů v Azure Monitor

![Symbol kontejnery](./media/containers/containers-symbol.png)

Tento článek popisuje, jak nastavit a použít řešení pro monitorování kontejnerů v Azure Monitor, které vám pomůže zobrazit a spravovat hostitele kontejnerů pro Docker a Windows v jednom umístění. Docker je software virtualizace systému použité k vytvoření kontejnerů, které automatizují nasazení softwaru na infrastrukturu IT.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Toto řešení ukazuje, které kontejnery jsou spuštěná, jaké image kontejnerů běží a kde kontejnery běží. Můžete zobrazit podrobné informace o auditování zobrazuje příkazy, které používá s kontejnery. A kontejnerů můžete řešit pomocí zobrazení a hledání centralizované protokoly bez nutnosti Chcete-li zobrazit hostitele Docker nebo Windows. Můžete najít kontejnery, které mohou být na hostiteli hlučného a využívání nadbytečné prostředky. A můžete zobrazit centralizované procesoru, paměti, úložiště a využití a výkonu informace o síti pro kontejnery. V počítačích se systémem Windows, můžete centralizovat a porovnat protokoly ze systému Windows Server Hyper-V a kontejnery Dockeru. Řešení podporuje následující orchestrátorů kontejnerů:

- Docker Swarm
- DC/OS
- Kubernetes
- Service Fabric
- Red Hat OpenShift

Pokud máte kontejnery nasazené v [Azure Service Fabric](../../service-fabric/service-fabric-overview.md), doporučujeme povolit [řešení Service Fabric](../../service-fabric/service-fabric-diagnostics-oms-setup.md) i toto řešení, aby zahrnovalo monitorování událostí clusteru. Než povolíte řešení Service Fabric, přečtěte si téma [řešení Service Fabric](../../service-fabric/service-fabric-diagnostics-event-analysis-oms.md) , abyste porozuměli tomu, co nabízí a jak ho používat.

Pokud vás zajímá monitorování výkonu vašich úloh nasazených do prostředí Kubernetes hostovaných ve službě Azure Kubernetes Service (AKS), přečtěte si téma [monitorování služby Azure Kubernetes](../../azure-monitor/insights/container-insights-overview.md). Řešení pro monitorování kontejnerů nepodporuje monitorování této platformy.  

Následující diagram znázorňuje vztahy mezi různými hostiteli kontejnerů a agenty s Azure Monitor.

![Diagram kontejnery](./media/containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>Požadavky na systém a podporovaných platformách

Než začnete, projděte si následující podrobnosti, které chcete ověřit, že máte splněné požadavky.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Podpora řešení pro monitorování kontejnerů Dockeru Orchestrator a verze operačního systému platformy

Následující tabulka popisuje podporu pro orchestraci a monitorování operačního systému pro inventář kontejnerů, výkon a protokoly s Azure Monitor.   

| | ACS | Linux | Windows | Kontejner<br>Inventář | Obrázek<br>Inventář | Uzel<br>Inventář | Kontejner<br>Výkon | Kontejner<br>Událost | Událost<br>Protokol | Kontejner<br>Protokol |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Kubernetes | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mesosphere<br>DC/OS | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Swarm | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Service<br>Fabric | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Red Hat Open<br>SHIFT | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
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

[!INCLUDE [log-analytics-agent-note.md](../../../includes/log-analytics-agent-note.md)] 

### <a name="supported-windows-operating-system"></a>Podporovaný operační systém Windows

- Windows Server 2016
- Windows 10 Anniversary Edition (Professional nebo Enterprise)

### <a name="docker-versions-supported-on-windows"></a>Verze dockeru, které jsou podporované ve Windows

- Dockeru 1.12 a 1.13
- Docker 17.03.0 a novější

## <a name="installing-and-configuring-the-solution"></a>Instalace a konfigurace řešení

K instalaci a konfiguraci řešení můžete použít následující informace.

1. Přidejte řešení pro monitorování kontejnerů do pracovního prostoru Log Analytics z [webu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) nebo pomocí procesu popsaného v tématu [Přidání řešení monitorování z galerie řešení](../../azure-monitor/insights/solutions.md).

2. Nainstalovat a používat Docker pomocí agenta Log Analytics. Na základě vašeho operačního systému a orchestrátor Docker, můžete použít následující metody konfigurace agenta.
   - Pro samostatné hostitele:
     - V podporovaných operačních systémech Linux nainstalujte a spusťte Docker a pak nainstalujte a nakonfigurujte [agenta Log Analytics pro Linux](../../azure-monitor/learn/quick-collect-linux-computer.md).  
     - V systému CoreOS nelze spustit agenta Log Analytics pro Linux. Místo toho spustíte kontejnerizovaných verzi agenta Log Analytics pro Linux. Pokud pracujete s kontejnery v Azure Government cloudu, přečtěte si téma hostitelé pro Linux kontejnerů, včetně CoreOS nebo Azure Government hostitelů kontejnerů Linux, včetně CoreOS.
     - V systémech Windows Server 2016 a Windows 10 nainstalujte modul Docker a klienta a pak připojte agenta, abyste mohli shromažďovat informace a odesílali je Azure Monitor. Pokud máte prostředí systému Windows, přečtěte si téma [instalace a konfigurace hostitelů kontejnerů Windows](#install-and-configure-windows-container-hosts) .
   - Orchestrace prostřednictvím Docker více hostitelů:
     - Pokud máte prostředí Red Hat OpenShift, přečtěte si téma Konfigurace agenta Log Analytics pro Red Hat OpenShift.
     - Pokud máte cluster Kubernetes pomocí služby Azure Container Service:
       - Přečtěte si téma [Konfigurace agenta Log Analytics Linux pro Kubernetes](#configure-a-log-analytics-linux-agent-for-kubernetes).
       - Přečtěte si téma [konfigurace Log Analytics agenta pro Windows pro Kubernetes](#configure-a-log-analytics-windows-agent-for-kubernetes).
       - Přečtěte si téma použití Helm k nasazení agenta Log Analytics na Linux Kubernetes.
     - Pokud máte cluster Azure Container Service DC/OS, přečtěte si další informace o [monitorování Azure Container Service clusteru DC/OS pomocí Azure monitor](../../container-service/dcos-swarm/container-service-monitoring-oms.md).
     - Pokud máte prostředí režimu Docker Swarm, přečtěte si další informace v konfiguraci agenta Log Analytics pro Docker Swarm.
     - Pokud máte Cluster Service Fabric, přečtěte si další informace o [monitorování kontejnerů pomocí Azure monitor](../../service-fabric/service-fabric-diagnostics-oms-containers.md).

Další informace o tom, jak nainstalovat a nakonfigurovat moduly Docker v počítačích se systémem Windows, najdete v článku o nástroji [Docker v systému Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon) .

> [!IMPORTANT]
> **Aby** bylo možné nainstalovat [agenta Log Analytics pro Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) do hostitelů kontejnerů, musí být spuštěný Docker. Pokud jste již nainstalovali agenta před instalací Dockeru, budete muset přeinstalovat agenta Log Analytics pro Linux. Další informace o Docker najdete na [webu Docker](https://www.docker.com).

### <a name="install-and-configure-linux-container-hosts"></a>Instalace a konfigurace hostitelé kontejneru s Linuxem

Po instalaci Dockeru, použijte následující nastavení pro hostitele kontejneru konfigurace agenta pro použití s Dockerem. Nejprve potřebujete ID pracovního prostoru Log Analytics a klíč, který najdete na webu Azure Portal. V pracovním prostoru kliknutím na **rychlé zprovoznění** > **počítače** zobrazíte **ID vašeho pracovního prostoru** a **primární klíč**.  Obě hodnoty zkopírujte a vložte do oblíbeného editoru.

**Pro všechny hostitele kontejnerů pro Linux s výjimkou CoreOS:**

- Další informace a postup pro instalaci agenta Log Analytics pro Linux najdete v tématu [Přehled agenta Log Analytics](../../azure-monitor/platform/log-analytics-agent.md).

**Pro všechny hostitele kontejnerů pro Linux, včetně CoreOS:**

Spuštění kontejneru, který chcete monitorovat. Upravit a pomocí následujícího příkladu:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**Pro všechny Azure Government hostitele kontejnerů Linux, včetně CoreOS:**

Spuštění kontejneru, který chcete monitorovat. Upravit a pomocí následujícího příkladu:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**Přepínání z použití nainstalovaného agenta Linux na jeden v kontejneru**

Pokud dříve použít přímo nainstalován agent a místo toho použít agenta spuštěného v kontejneru, je nutné nejprve odebrat agenta Log Analytics pro Linux. Informace o tom, jak úspěšně odinstalovat agenta, najdete v tématu [odinstalace agenta Log Analytics pro systém Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) .  

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

* [Instalace agenta Log Analytics pro Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) přímo na každý uzel OpenShift  
* [Povolení rozšíření Log Analytics virtuálního počítače](../../azure-monitor/learn/quick-collect-azurevm.md) v každém uzlu OpenShift, který se nachází v Azure  
* Instalace agenta Log Analytics jako sady OpenShift démona  

V této části probereme kroky potřebné k instalaci agenta Log Analytics jako démon OpenShift-set.  

1. Přihlaste se k uzlu hlavní server OpenShift a zkopírujte soubor YAML [OCP-omsagent. yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) z GitHubu do svého hlavního uzlu a upravte hodnotu pomocí ID vašeho pracovního prostoru Log Analytics a s vaším primárním klíčem.
2. Spuštěním následujících příkazů vytvořte projekt pro Azure Monitor a nastavte uživatelský účet.

    ```
    oc adm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Pokud chcete nasadit sady démon, spusťte následující příkaz:

    `oc create -f ocp-omsagent.yaml`

4. Ověřte, zda že je nakonfigurován a funguje správně, zadejte následující příkaz:

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

1. Přihlaste se k uzlu hlavní server OpenShift a zkopírujte soubor YAML [OCP-DS-omsagent. yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) a tajný kód pro generování skriptu [OCP-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) z GitHubu.  Tento skript vytvoří soubor yaml tajných kódů pro ID pracovního prostoru Log Analytics a primární klíč pro zabezpečení vašich secrete informace.  
2. Spuštěním následujících příkazů vytvořte projekt pro Azure Monitor a nastavte uživatelský účet. Skript pro generování tajného klíče žádá o Log Analytics ID pracovního prostoru `<WSID>` a primární klíč `<KEY>` a po dokončení vytvoří soubor OCP-Secret. yaml.  

    ```
    oc adm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Nasaďte soubor tajného kódu spuštěním následujícího:

    `oc create -f ocp-secret.yaml`

4. Ověření nasazení spuštěním následujícího:

    `oc describe secret omsagent-secret`  

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

5. Nasaďte soubor yaml démon sady agenta Log Analytics spuštěním následujícího:

    `oc create -f ocp-ds-omsagent.yaml`  

6. Ověření nasazení spuštěním následujícího:

    `oc describe ds oms`

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

#### <a name="configure-a-log-analytics-linux-agent-for-kubernetes"></a>Konfigurace agenta Log Analytics Linux pro Kubernetes

Pro Kubernetes použijte skript se vygenerovat soubor yaml tajných kódů pro ID pracovního prostoru a primární klíč pro instalaci agenta Log Analytics pro Linux. Na stránce [Log Analytics Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) se nacházejí soubory, které můžete použít s nebo bez tajných informací.

- Výchozí Log Analytics agenta pro Linux DaemonSet nemá tajných informací (omsagent.yaml)
- Agenta Log Analytics pro Linux DaemonSet soubor yaml používá tajných informací (omsagent-ds-secrets.yaml) pomocí tajného kódu generování skriptů se vygenerovat soubor yaml (omsagentsecret.yaml) tajných kódů.

Můžete vytvořit omsagent DaemonSets s nebo bez něj tajných kódů.

**Výchozí OMSagent soubor YAML DaemonSet bez tajných klíčů**

- Pro výchozí soubor Log Analytics agenta DaemonSet YAML nahraďte `<WSID>` a `<KEY>` na WSID a klíč. Zkopírujte soubor do hlavního uzlu a spusťte následující příkaz:

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**Výchozí OMSagent soubor YAML DaemonSet s tajnými kódy**

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

    5. Vytvoření omsagent procesu démon-set spuštěním ```sudo kubectl create -f omsagent-ds-secrets.yaml```

2. Ověřte, že agenta Log Analytics DaemonSet běží, podobný následujícímu:

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```

Pro Kubernetes vygenerujte soubor yaml tajných kódů pro ID pracovního prostoru a primární klíč pro agenta Log Analytics pro Linux pomocí skriptu. K zabezpečení tajných informací použijte následující příklady informací se [souborem omsagent YAML](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml) .

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

Pro Windows Kubernetes pomocí skriptu se vygenerovat soubor yaml tajných kódů pro ID pracovního prostoru a primární klíč pro instalaci agenta Log Analytics. Na stránce [Log Analytics Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) se nacházejí soubory, které můžete použít s vašimi tajnými informacemi.  Je potřeba nainstalovat agenta Log Analytics samostatně pro hlavních a agentských uzlů.  

1. Použít agenta Log Analytics DaemonSet pomocí tajných informací na hlavní uzel, přihlaste se a nejdříve vytvořit tajné klíče.
    1. Zkopírujte skript a soubor tajného kódu šablony a ujistěte se, že jsou ve stejném adresáři.
        - Generování skriptu - secret gen.sh tajný klíč
        - Šablona tajné – template.yaml tajný klíč

    2. Spusťte skript, jako v následujícím příkladu. Skript vyzve k zadání ID pracovního prostoru Log Analytics a primární klíč a po při zadávání, skript vytvoří soubor yaml tajného kódu, můžete ji spustit.

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. Vytvoření omsagent procesu démon-set spuštěním ```kubectl create -f omsagentsecret.yaml```
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

    5. Vytvoření omsagent procesu démon-set spuštěním ```kubectl create -f ws-omsagent-de-secrets.yaml```

2. Ověřte, že agenta Log Analytics DaemonSet běží, podobný následujícímu:

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Chcete-li nainstalovat agenta do pracovního uzlu, ve kterém je spuštěn systém Windows, postupujte podle kroků v části [instalace a konfigurace hostitelů kontejnerů Windows](#install-and-configure-windows-container-hosts).

#### <a name="use-helm-to-deploy-log-analytics-agent-on-linux-kubernetes"></a>Nasadit agenta Log Analytics na Linuxu Kubernetes pomocí Helm

Pokud chcete nasadit agenta Log Analytics v prostředí Linux Kubernetes pomocí helm, proveďte následující kroky.

1. Vytvoření omsagent procesu démon-set spuštěním ```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms```
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

3. Stav omsagent můžete zkontrolovat spuštěním: ```helm status "omsagent"``` a výstup bude vypadat podobně jako v následujícím příkladu:

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
   
    Další informace najdete v [grafu řešení kontejnerů Helm](https://aka.ms/omscontainerhelm).

### <a name="install-and-configure-windows-container-hosts"></a>Instalace a konfigurace hostitelích kontejnerů Windows

Použijte informace v části instalace a konfigurace hostitelích kontejnerů Windows.

#### <a name="preparation-before-installing-windows-agents"></a>Příprava před instalací agentů Windows

Před instalací agentů v počítačích se systémem Windows, musíte nakonfigurovat služba Docker. Tato konfigurace umožňuje agentu Windows nebo rozšíření virtuálního počítače Azure Monitor používat soket rozhraní Docker TCP, aby mohli agenti přistupovat ke vzdálenému démonu Docker a zachytit data pro monitorování.

##### <a name="to-configure-the-docker-service"></a>Konfigurace služby Docker  

Pro povolení kanálu TCP a pojmenovaného kanálu pro Windows Server proveďte následující příkazy PowerShellu:

```
Stop-Service docker
dockerd --unregister-service
dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
Start-Service docker
```

Další informace o konfiguraci démona Docker používaného s kontejnery Windows najdete v tématu [modul Docker ve Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon).

#### <a name="install-windows-agents"></a>Instalace agentů Windows

Pokud chcete povolit monitorování kontejnerů Windows a Hyper-V, nainstalujte Microsoft Monitoring Agent (MMA) v počítačích s Windows, které jsou hostitelé kontejneru. Počítače se systémem Windows v místním prostředí najdete v tématu [připojení počítačů se systémem Windows k Azure monitor](../../azure-monitor/platform/agent-windows.md). Pro virtuální počítače běžící v Azure je připojte k Azure Monitor pomocí [rozšíření virtuálního počítače](../../azure-monitor/learn/quick-collect-azurevm.md).

Můžete monitorovat kontejnery Windows se spuštěnou v Service Fabric. V současné době se ale pro Service Fabric podporují jenom [virtuální počítače běžící v Azure](../../azure-monitor/learn/quick-collect-azurevm.md) a [počítače s Windows v místním prostředí](../../azure-monitor/platform/agent-windows.md) .

Můžete ověřit, jestli je správně nastavené řešení pro monitorování kontejnerů pro Windows. Chcete-li zkontrolovat, zda byl Management Pack správně stažen, vyhledejte *ContainerManagement.xxx*. Soubory musí být ve složce C:\Program Files\Microsoft Monitoring Agent\Agent\Health State\Management aktualizace.

## <a name="solution-components"></a>Součásti řešení

Z Azure Portal přejděte na *Galerie řešení* a přidejte **řešení pro monitorování kontejnerů**. Pokud používáte Windows agenty, je při přidání tohoto řešení následující sady management pack nainstalované na každém počítači s agentem. Žádná konfigurace ani údržba je vyžadován pro sadu management pack.

- *ContainerManagement.xxx* nainstalované v adresáři C:\Program Files\Microsoft monitoring Agent\Agent\Health Service State\Management Pack

## <a name="container-data-collection-details"></a>Podrobnosti o kontejneru dat kolekce

Řešení pro monitorování kontejnerů různých metrik a protokolů shromažďuje údaje o výkonu z hostitelů kontejnerů a kontejnery pomocí agentů, které povolíte.

Data jsou shromažďována každé 3 minuty, následující typy agenta.

- [Agent Log Analytics pro Linux](../../azure-monitor/learn/quick-collect-linux-computer.md)
- [Agent pro Windows](../../azure-monitor/platform/agent-windows.md)
- [Rozšíření virtuálního počítače Log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md)

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

Popisky připojené k datovým typům *PodLabel* jsou vlastní popisky. Připojený PodLabel popisky zobrazeny v tabulce jsou příklady. Takže se `PodLabel_deployment_s`, `PodLabel_deploymentconfig_s`, `PodLabel_docker_registry_s` se v sadě dat vašeho prostředí liší a obecně se podobá `PodLabel_yourlabel_s`.

## <a name="monitor-containers"></a>Monitorovat kontejnery
Po povolení řešení v Azure Portal dlaždice **kontejnery** zobrazí souhrnné informace o hostitelích kontejnerů a kontejnerech spuštěných v hostitelích.

![Kontejnery dlaždice](./media/containers/containers-title.png)

Dlaždice zobrazí přehled o tom, kolik kontejnerů, je nutné v prostředí a určuje, zda se nepovedlo, spuštěná nebo zastavená.

### <a name="using-the-containers-dashboard"></a>Na řídicím panelu kontejnery

Klikněte na dlaždici **kontejnery** . Tady se zobrazí zobrazení uspořádané podle:

- **Události kontejneru** – zobrazuje stav kontejneru a počítače s neúspěšnými kontejnery.
- **Protokoly kontejneru** – zobrazuje graf souborů protokolu kontejneru vygenerovaných v průběhu času a seznam počítačů s největším počtem souborů protokolu.
- **Události Kubernetes** – zobrazuje graf událostí Kubernetes generovaných v průběhu času a seznam důvodů, proč lusky vygenerovaly události. *Tato datová sada se používá jenom v prostředích Linux.*
- **Inventář oboru názvů Kubernetes** – zobrazuje počet oborů názvů a lusků a ukazuje jejich hierarchii. *Tato datová sada se používá jenom v prostředích Linux.*
- **Inventář uzlů kontejneru** – zobrazuje počet typů orchestrace používaných na uzlech nebo hostitelích kontejneru. Počítač uzlů a hostuje jsou také uvedeny podle počtu kontejnerů. *Tato datová sada se používá jenom v prostředích Linux.*
- **Inventář imagí kontejneru** – zobrazuje celkový počet použitých imagí kontejneru a počet typů obrázků. Počet obrázků jsou také uvedeny podle obrázku značky.
- **Stav kontejnerů** – zobrazuje celkový počet uzlů nebo hostitelských počítačů, ve kterých jsou spuštěné kontejnery. Počítače jsou také uvedeny podle počtu spuštění hostitele.
- **Proces kontejneru** – zobrazí spojnicový graf procesů kontejneru spuštěných v průběhu času. Kontejnery jsou také uvedeny spuštěním příkazu/procesu v rámci kontejnerů. *Tato datová sada se používá jenom v prostředích Linux.*
- **Výkon procesoru kontejneru** – zobrazuje spojnicový graf průměrného využití procesoru v čase pro uzly počítače nebo hostitele. Také seznamy počítač uzlů a hostuje v závislosti na průměrném využití procesoru.
- **Výkon paměti kontejneru** – zobrazuje spojnicový graf využití paměti v průběhu času. Také uvádí na název instance na základě využití paměti počítače.
- **Výkon počítače** – zobrazí spojnicové grafy s procentem výkonu procesoru v čase, využití paměti v průběhu času a MB volného místa na disku v průběhu času. Při najetí myší nad libovolný řádek v grafu zobrazíte další podrobnosti.

Každé oblasti řídicího panelu je vizuální znázornění vyhledávání, která se spouští na shromážděná data.

![Řídicí panel kontejnery](./media/containers/containers-dash01.png)

![Řídicí panel kontejnery](./media/containers/containers-dash02.png)

V oblasti **stav kontejneru** klikněte na horní oblast, jak je znázorněno níže.

![Stav kontejneru](./media/containers/containers-status.png)

Log Analytics se otevře a zobrazí se informace o stavu vašich kontejnerů.

![Log Analytics pro kontejnery](./media/containers/containers-log-search.png)

Tady můžete upravit vyhledávací dotaz upravit ho k nalezení konkrétních informací, že máte zájem. Další informace o dotazech protokolu najdete [v tématu Protokolování dotazů v Azure monitor](../log-query/log-query-overview.md).

## <a name="troubleshoot-by-finding-a-failed-container"></a>Řešení potíží s tím, že hledá selhání kontejneru

Log Analytics označí kontejner jako **neúspěšný** , pokud byl ukončen s nenulovým ukončovacím kódem. Přehled chyb a selhání v prostředí najdete v oblasti **selhání kontejnerů** .

### <a name="to-find-failed-containers"></a>Chcete-li najít neúspěšné kontejnery

1. Klikněte na oblast **stavu kontejneru** .  
   stav ![kontejnerů](./media/containers/containers-status.png)
2. Log Analytics se otevře a zobrazí stav kontejnerů, podobně jako v následujícím příkladu.  
   ![Stav kontejnerů](./media/containers/containers-log-search.png)
3. Rozbalte řádek selhání a kliknutím na + přidejte jeho kritéria do dotazu. Pak přidejte do dotazu řádek souhrnu.
   neúspěšné kontejnery ![](./media/containers/containers-state-failed-select.png)  
1. Spusťte dotaz a potom rozbalte čáru ve výsledcích, abyste zobrazili ID obrázku.  
   ![neúspěšné kontejnery](./media/containers/containers-state-failed.png)  
1. V dotazu protokolu zadejte následující text. `ContainerImageInventory | where ImageID == <ImageID>` zobrazíte podrobnosti o imagi, jako je například velikost obrázku a počet zastavených a neúspěšných imagí.  
   neúspěšné kontejnery ![](./media/containers/containers-failed04.png)

## <a name="query-logs-for-container-data"></a>Dotazy na data kontejneru v protokolech

Pokud řešíte potíže s konkrétní chyba, může být snazší zobrazíte, kde se děje ve vašem prostředí. Následující typy protokolů umožňují dotazů, které vrátí informace, že chcete vytvořit.

- **ContainerImageInventory** – tento typ použijte, pokud se pokoušíte najít informace uspořádané podle obrázku a zobrazit informace o obrázku, jako jsou ID nebo velikosti obrázků.
- **ContainerInventory** – tento typ použijte, když chcete získat informace o umístění kontejneru, o jejich názvech a o tom, jaké image jsou spuštěné.
- **ContainerLog** – tento typ použijte, když chcete najít konkrétní informace a položky protokolu chyb.
- **ContainerNodeInventory_CL**  Tento typ použijte, pokud chcete informace o hostiteli nebo uzlu, kde se nacházejí kontejnery. Poskytne vám verze Dockeru, typ Orchestrace, úložiště a informace o síti.
- **ContainerProcess_CL** Tento typ slouží k rychlému zobrazení procesu spuštěného v rámci kontejneru.
- **ContainerServiceLog** – tento typ použijte, když se pokoušíte najít informace o záznamech auditu pro démona Docker, jako je třeba spustit, zastavit, odstranit nebo přijmout příkazy.
- **KubeEvents_CL**  Pomocí tohoto typu můžete zobrazit události Kubernetes.
- **KubePodInventory_CL**  Tento typ použijte, pokud chcete pochopit informace o hierarchii clusteru.


### <a name="to-query-logs-for-container-data"></a>Dotazování protokolů na data kontejneru

* Vyberte bitovou kopii, o kterém víte nedávno selhaly a najít protokoly chyb pro něj. Začněte hledáním názvu kontejneru, na kterém je spuštěná tato image, pomocí hledání **ContainerInventory** . Vyhledejte například `ContainerInventory | where Image == "ubuntu" and ContainerState == "Failed"`  
    ![Hledat kontejnery Ubuntu](./media/containers/search-ubuntu.png)

  Rozbalením libovolného řádku ve výsledcích zobrazíte podrobnosti o daném kontejneru.

## <a name="example-log-queries"></a>Příklady dotazů protokolu

Často je užitečné k sestavování dotazů od jedné až dvou příklad a následnou úpravou jim budou odpovídat vašemu prostředí. Jako výchozí bod můžete experimentovat s oblastí **vzorových dotazů** , které vám pomůžou sestavovat pokročilejší dotazy.

![Kontejnery dotazy](./media/containers/containers-queries.png)

## <a name="saving-log-queries"></a>Ukládání dotazů protokolu

Ukládání dotazů je standardní funkcí v Azure Monitor. Uložením, budete mít ty, které připadají užitečné po ruce pro pozdější použití.

Až vytvoříte dotaz, který je užitečný, uložte ho kliknutím na **Oblíbené** v horní části stránky hledání v protokolu. Pak k němu můžete snadno přistupovat později ze stránky **Můj řídicí panel** .

## <a name="next-steps"></a>Další kroky

V [protokolech dotazů](../log-query/log-query-overview.md) zobrazíte podrobné záznamy dat kontejneru.
