---
title: Řešení monitorování kontejnerů ve službě Azure Monitor | Dokumenty společnosti Microsoft
description: Řešení monitorování kontejnerů ve službě Azure Monitor vám pomůže zobrazit a spravovat hostitele kontejnerů Dockeru a Windows na jednom místě.
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/22/2019
ms.openlocfilehash: 171f897f6e110e8f759281c139addab477ecede3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664690"
---
# <a name="container-monitoring-solution-in-azure-monitor"></a>Řešení monitorování kontejnerů ve službě Azure Monitor

![Symbol kontejnerů](./media/containers/containers-symbol.png)

Tento článek popisuje, jak nastavit a používat řešení monitorování kontejnerů v Azure Monitoru, které vám pomůže zobrazit a spravovat hostitele kontejnerů Dockeru a Windows na jednom místě. Docker je systém virtualizace softwaru, který se používá k vytváření kontejnerů, které automatizují nasazení softwaru do jejich INFRASTRUKTURY IT.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Řešení ukazuje, které kontejnery jsou spuštěny, jaké image kontejneru jsou spuštěny a kde kontejnery jsou spuštěny. Můžete zobrazit podrobné informace o auditu zobrazující příkazy používané s kontejnery. A můžete řešit problémy s kontejnery zobrazením a prohledáváním centralizovaných protokolů, aniž byste museli vzdáleně zobrazovat hostitele Dockeru nebo Windows. Můžete najít kontejnery, které mohou být hlučné a spotřebovávají přebytečné prostředky na hostiteli. A můžete zobrazit centralizované procesoru, paměti, úložiště a využití sítě a informace o výkonu pro kontejnery. V počítačích se systémem Windows můžete centralizovat a porovnávat protokoly z kontejnerů Windows Server, Hyper-V a Docker. Řešení podporuje následující kontejnerové orchestrátory:

- Docker Swarm
- DC/OS
- Kubernetes
- Service Fabric
- Red Hat OpenShift

Pokud máte kontejnery nasazené v [Azure Service Fabric](../../service-fabric/service-fabric-overview.md), doporučujeme povolit řešení Service [Fabric](../../service-fabric/service-fabric-diagnostics-oms-setup.md) a toto řešení zahrnout monitorování událostí clusteru. Před povolením service fabric řešení, zkontrolujte [pomocí service fabric řešení](../../service-fabric/service-fabric-diagnostics-event-analysis-oms.md) pochopit, co poskytuje a jak ji používat.

Pokud máte zájem o sledování výkonu vašich úloh nasazených v prostředích Kubernetes hostovaných ve službě Azure Kubernetes Service (AKS), přečtěte si část [Sledování služby Azure Kubernetes](../../azure-monitor/insights/container-insights-overview.md). Řešení monitorování kontejnerů nepodporuje monitorování této platformy.  

Následující diagram znázorňuje vztahy mezi různými hostiteli kontejnerů a agenty pomocí Azure Monitoru.

![Diagram kontejnerů](./media/containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>Systémové požadavky a podporované platformy

Před zahájením zkontrolujte následující podrobnosti a ověřte, zda splňujete požadavky.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Podpora řešení monitorování kontejnerů pro platformu Docker Orchestrator a OS

Následující tabulka popisuje podporu orchestrace dockeru a monitorování operačního systému inventáře kontejnerů, výkonu a protokolů pomocí Azure Monitoru.   

| | ACS | Linux | Windows | Kontejner<br>Inventarizace | Image<br>Inventarizace | Node<br>Inventarizace | Kontejner<br>Výkon | Kontejner<br>Událost | Událost<br>Protokol | Kontejner<br>Protokol |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Kubernetes | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mezosféra<br>DC/OS | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Roj | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Služba<br>Fabric | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Červený klobouk otevřený<br>Shift | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
| Windows Server<br>(samostatně) | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Linux Server<br>(samostatně) | | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |

### <a name="docker-versions-supported-on-linux"></a>Verze Dockeru podporované na Linuxu

- Docker 1,11 až 1,13
- Docker CE a EE v17.06

### <a name="x64-linux-distributions-supported-as-container-hosts"></a>Linuxové distribuce x64 podporované jako hostitelé kontejnerů

- Ubuntu 14.04 LTS a 16.04 LTS
- CoreOS (stabilní)
- Amazon Linux 2016.09.0
- openSUSE 13.2
- openSUSE LEAP 42.2
- CentOS 7,2 a 7,3
- SLES 12
- RHEL 7.2 a 7.3
- Red Hat OpenShift kontejnerová platforma (OCP) 3.4 a 3.5
- ACS Mesosphere DC/OS 1.7.3 až 1.8.8
- ACS Kubernetes 1,4,5 až 1,6
    - Kubernetesovy události, inventář Kubernetes a kontejnerové procesy jsou podporovány pouze ve verzi 1.4.1-45 a později s agentem Log Analytics pro Linux.
- ACS Docker Roj

[!INCLUDE [log-analytics-agent-note.md](../../../includes/log-analytics-agent-note.md)] 

### <a name="supported-windows-operating-system"></a>Podporovaný operační systém Windows

- Windows Server 2016
- Windows 10 Anniversary Edition (Professional nebo Enterprise)

### <a name="docker-versions-supported-on-windows"></a>Verze Dockeru podporované ve Windows

- Docker 1,12 a 1,13
- Docker 17.03.0 a novější

## <a name="installing-and-configuring-the-solution"></a>Instalace a konfigurace řešení

K instalaci a konfiguraci řešení můžete použít následující informace.

1. Přidejte řešení monitorování kontejnerů do pracovního prostoru Log Analytics z [webu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) nebo pomocí procesu popsaného v části Přidat řešení monitorování z Galerie [řešení](../../azure-monitor/insights/solutions.md).

2. Nainstalujte a používejte Docker s agentem Analýzy protokolů. Na základě operačního systému a Orchestrtor Docker, můžete použít následující metody ke konfiguraci agenta.
   - Pro samostatné hostitele:
     - Na podporovaných operačních systémech Linux nainstalujte a spusťte Docker a potom nainstalujte a nakonfigurujte [agenta Log Analytics pro Linux](../../azure-monitor/learn/quick-collect-linux-computer.md).  
     - Na CoreOS nelze spustit agenta Analýzy protokolů pro Linux. Místo toho spustíte kontejnerizovanou verzi agenta Log Analytics pro Linux. Zkontrolujte hostitele kontejnerů Linuxu, včetně hostitelů kontejnerů CoreOS nebo Azure Government Linux, včetně CoreOS, pokud pracujete s kontejnery v Azure Government Cloud.
     - Ve Windows Serveru 2016 a Windows 10 nainstalujte Docker Engine a klienta a pak připojte agenta, který shromažďuje informace a odesílá je do Azure Monitoru. Pokud máte prostředí Windows, [zkontrolujte instalaci a konfiguraci hostitelů kontejnerů systému Windows.](#install-and-configure-windows-container-hosts)
   - Pro orchestraci více hostitelů v Dockeru:
     - Pokud máte prostředí Red Hat OpenShift, přečtěte si recenzi konfigurace agenta Analýzy protokolů pro Red Hat OpenShift.
     - Pokud máte cluster Kubernetes pomocí služby Azure Container Service:
       - Zkontrolujte [Konfigurace agenta Linuxu log Analytics pro Kubernetes](#configure-a-log-analytics-linux-agent-for-kubernetes).
       - [Zkontrolujte, nakonfigurujte agenta systému Windows pro analýzu protokolů pro společnost Kubernetes](#configure-a-log-analytics-windows-agent-for-kubernetes).
       - Zkontrolujte, použijte Helm k nasazení agenta Log Analytics na Linux Kubernetes.
     - Pokud máte cluster Azure Container Service DC/OS, další informace najdete v [monitoru Azure Container Service DC/OS clusteru s Azure Monitor](../../container-service/dcos-swarm/container-service-monitoring-oms.md).
     - Pokud máte prostředí režimu Docker Swarm, další informace najdete na konfiguraci agenta Analýzy protokolů pro Docker Swarm.
     - Pokud máte cluster Service Fabric, další informace o [monitorování kontejnerů s Azure Monitor](../../service-fabric/service-fabric-diagnostics-oms-containers.md).

Další informace o instalaci a konfiguraci modulů Docker Engines v počítačích se systémem Windows naleznete v článku Modul u Dockerengine v [systému Windows.](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon)

> [!IMPORTANT]
> Docker musí být spuštěn **před** instalací [agenta Analýzy protokolů pro Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) na hostitele kontejneru. Pokud jste agenta nainstalovali před instalací Dockeru, musíte přeinstalovat agenta Log Analytics pro Linux. Další informace o Dockeru najdete na [webu Dockeru](https://www.docker.com).

### <a name="install-and-configure-linux-container-hosts"></a>Instalace a konfigurace hostitelů kontejnerů Linuxu

Po instalaci Dockeru použijte následující nastavení pro hostitele kontejneru ke konfiguraci agenta pro použití s Dockerem. Nejprve potřebujete ID pracovního prostoru analýzy protokolů a klíč, který najdete na webu Azure Portal. V pracovním prostoru klepněte na **tlačítko Počítače s rychlým spuštěním** > **Computers** a zobrazte **ID pracovního prostoru** a **primární klíč**.  Obě hodnoty zkopírujte a vložte do oblíbeného editoru.

**Pro všechny hostitele linuxových kontejnerů s výjimkou CoreOS:**

- Další informace a kroky k instalaci agenta Log Analytics pro Linux najdete v [tématu Přehled agenta Log Analytics](../../azure-monitor/platform/log-analytics-agent.md).

**Pro všechny hostitele linuxových kontejnerů včetně CoreOS:**

Spusťte kontejner, který chcete sledovat. Upravte a použijte následující příklad:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**Pro všechny hostitele kontejnerů Azure Government Linux včetně CoreOS:**

Spusťte kontejner, který chcete sledovat. Upravte a použijte následující příklad:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**Přechod z používání nainstalovaného agenta Linuxu na agenta linuxu v kontejneru**

Pokud jste dříve používali přímo nainstalovaného agenta a chcete místo toho použít agenta spuštěného v kontejneru, musíte nejprve odebrat agenta Log Analytics pro Linux. Informace o tom, jak úspěšně odinstalovat agenta agenta, najdete v [tématu Odinstalace agenta Analýzy protokolů pro Linux.](../../azure-monitor/learn/quick-collect-linux-computer.md)  

#### <a name="configure-a-log-analytics-agent-for-docker-swarm"></a>Konfigurace agenta analýzy protokolů pro Docker Swarm

Agenta analýzy protokolů můžete spustit jako globální službu v Docker Swarm. Následující informace slouží k vytvoření služby agenta Log Analytics. Je třeba zadat ID pracovního prostoru analýzy protokolu a primární klíč.

- Spusťte následující v hlavním uzlu.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

##### <a name="secure-secrets-for-docker-swarm"></a>Zabezpečte tajemství pro Docker Swarm

Pro Docker Swarm, po vytvoření tajného klíče pro ID pracovního prostoru a primární klíč, použijte následující informace k vytvoření tajných informací.

1. Spusťte následující v hlavním uzlu.

    ```
    echo "WSID" | docker secret create WSID -
    echo "KEY" | docker secret create KEY -
    ```

2. Ověřte, zda byly tajné klíče vytvořeny správně.

    ```
    keiko@swarmm-master-13957614-0:/run# sudo docker secret ls
    ```

    ```
    ID                          NAME                CREATED             UPDATED
    j2fj153zxy91j8zbcitnjxjiv   WSID                43 minutes ago      43 minutes ago
    l9rh3n987g9c45zffuxdxetd9   KEY                 38 minutes ago      38 minutes ago
    ```

3. Spusťte následující příkaz pro připojení tajných kódů k agentovi analýzy protokolů kontejnerizovaného protokolu.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="configure-a-log-analytics-agent-for-red-hat-openshift"></a>Konfigurace agenta analýzy protokolů pro Red Hat OpenShift

Existují tři způsoby, jak přidat agenta Log Analytics do Red Hat OpenShift začít shromažďovat data monitorování kontejnerů.

* [Instalace agenta Log Analytics pro Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) přímo na každém uzlu OpenShift  
* [Povolení rozšíření virtuálního počítače Analýzy protokolů](../../azure-monitor/learn/quick-collect-azurevm.md) na každém uzlu OpenShift s bydlištěm v Azure  
* Instalace agenta Log Analytics jako sady daemonů OpenShift  

V této části se zabýváme kroky potřebnými k instalaci agenta Log Analytics jako sady daemonů OpenShift.  

1. Přihlaste se k hlavnímu uzlu OpenShift a zkopírujte soubor yaml [ocp-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) z GitHubu do hlavního uzlu a upravte hodnotu pomocí ID pracovního prostoru Analýzy protokolů a pomocí primárního klíče.
2. Spusťte následující příkazy k vytvoření projektu pro Azure Monitor a nastavte uživatelský účet.

    ```
    oc adm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Chcete-li nasadit sadu daemonů, spusťte následující:

    `oc create -f ocp-omsagent.yaml`

4. Chcete-li ověřit, zda je nakonfigurovaný a funguje správně, zadejte následující příkaz:

    `oc describe daemonset omsagent`  

    a výstup by měl připomínat:

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

Pokud chcete použít tajné klíče k zabezpečení ID pracovního prostoru analýzy protokolu a primárníklíč při použití log Analytics agent daemon-set yaml soubor, proveďte následující kroky.

1. Přihlaste se k hlavnímu uzlu OpenShift a zkopírujte soubor yaml [ocp-ds-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) a skript pro generování tajných služeb [ocp-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) z GitHubu.  Tento skript vygeneruje soubor yaml tajných kódů pro ID pracovního prostoru Analýzy protokolů a primární klíč pro zabezpečení informací o vylučování.  
2. Spusťte následující příkazy k vytvoření projektu pro Azure Monitor a nastavte uživatelský účet. Skript pro generování tajných klíčů požádá o `<WSID>` ID `<KEY>` pracovního prostoru analýzy protokolu a primární klíč a po dokončení vytvoří soubor ocp-secret.yaml.  

    ```
    oc adm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Nasadit tajný soubor spuštěním následující:

    `oc create -f ocp-secret.yaml`

4. Ověřte nasazení spuštěním následujícího:

    `oc describe secret omsagent-secret`  

    a výstup by měl připomínat:  

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

5. Nasadit soubor yaml agenta Analýzy protokolů spuštěním následujícího:

    `oc create -f ocp-ds-omsagent.yaml`  

6. Ověřte nasazení spuštěním následujícího:

    `oc describe ds oms`

    a výstup by měl připomínat:

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

#### <a name="configure-a-log-analytics-linux-agent-for-kubernetes"></a>Konfigurace agenta Linuxu log Analytics pro Kubernetes

Pro Kubernetes pomocí skriptu ke generování souboru yaml tajných kódů pro vaše ID pracovního prostoru a primární klíč k instalaci agenta Log Analytics pro Linux. Na stránce [GitHub Log Analytics Docker Kubernetes](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) jsou soubory, které můžete použít s tajnými informacemi nebo bez něj.

- Výchozí agent Analýzy protokolů pro Linux DaemonSet nemá tajné informace (omsagent.yaml)
- Agent Log Analytics pro soubor Linux DaemonSet yaml používá tajné informace (omsagent-ds-secrets.yaml) s tajnými skripty generování ke generování tajných kódů yaml (omsagentsecret.yaml) soubor.

Můžete vytvořit omsagent DaemonSets s nebo bez tajných kódů.

**Výchozí OMSagent DaemonSet yaml soubor bez tajných kódů**

- Pro výchozí log analytics agent DaemonSet yaml soubor, nahradit `<WSID>` a `<KEY>` na WSID a KLÍČ. Zkopírujte soubor do hlavního uzlu a spusťte následující:

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**Výchozí OMSagent DaemonSet yaml soubor s tajnými kódy**

1. Chcete-li použít agentlog Analytics DaemonSet pomocí tajných informací, vytvořte nejprve tajné kódy.
    1. Zkopírujte soubor šablony skriptu a tajného klíče a ujistěte se, že jsou ve stejném adresáři.
        - Tajný generující skript - secret-gen.sh
        - tajná šablona - secret-template.yaml
    2. Spusťte skript, stejně jako v následujícím příkladu. Skript požádá o ID pracovního prostoru analýzy protokolů a primární klíč a po jejich zadání skript vytvoří tajný soubor yaml, abyste jej mohli spustit.   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. Vytvořte pod tajných klíčů spuštěním následující:
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. Chcete-li ověřit, spusťte následující:

        ```
        keiko@ubuntu16-13db:~# sudo kubectl get secrets
        ```

        Výstup by měl připomínat:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        ```

        ```
        keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
        ```

        Výstup by měl připomínat:

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

    5. Vytvořte si omsagent daemon-set spuštěním```sudo kubectl create -f omsagent-ds-secrets.yaml```

2. Ověřte, zda je spuštěna sada agenta Log Analytics, podobně jako následující:

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```

Pro Kubernetes použijte skript ke generování souboru yaml tajných kódů pro ID pracovního prostoru a primárníklíč pro agenta Log Analytics pro Linux. Pomocí následujících ukázkových informací se [souborem omsagent yaml](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml) zabezpečte tajné informace.

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

#### <a name="configure-a-log-analytics-windows-agent-for-kubernetes"></a>Konfigurace agenta systému Windows pro analýzu protokolů pro Kubernetes

Pro Windows Kubernetes pomocí skriptu ke generování souboru yaml tajných kódů pro id pracovního prostoru a primární klíč k instalaci agenta Log Analytics. Na stránce [GitHub Log Analytics Docker Kubernetes](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) jsou soubory, které můžete použít s tajnými informacemi.  Je třeba nainstalovat agenta Analýzy protokolů samostatně pro hlavní uzly a uzly agenta.  

1. Chcete-li použít agenta Log Analytics DaemonSet pomocí tajných informací na hlavním uzlu, přihlaste se a vytvořte tajné klíče jako první.
    1. Zkopírujte soubor šablony skriptu a tajného klíče a ujistěte se, že jsou ve stejném adresáři.
        - Tajný generující skript - secret-gen.sh
        - tajná šablona - secret-template.yaml

    2. Spusťte skript, stejně jako v následujícím příkladu. Skript požádá o ID pracovního prostoru analýzy protokolů a primární klíč a po jejich zadání skript vytvoří tajný soubor yaml, abyste jej mohli spustit.

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. Vytvořte si omsagent daemon-set spuštěním```kubectl create -f omsagentsecret.yaml```
    4. Chcete-li zkontrolovat, spusťte následující:

        ```
        root@ubuntu16-13db:~# kubectl get secrets
        ```

        Výstup by měl připomínat:

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

    5. Vytvořte si omsagent daemon-set spuštěním```kubectl create -f ws-omsagent-de-secrets.yaml```

2. Ověřte, zda je spuštěna sada agenta Log Analytics, podobně jako následující:

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Chcete-li nainstalovat agenta do pracovního uzlu se systémem Windows, postupujte podle pokynů v části [Instalace a konfigurace hostitelů kontejnerů systému Windows](#install-and-configure-windows-container-hosts).

#### <a name="use-helm-to-deploy-log-analytics-agent-on-linux-kubernetes"></a>Použití helmu k nasazení agenta Log Analytics na Linux Kubernetes

Chcete-li použít kormidlo k nasazení agenta Log Analytics v prostředí Linux Kubernetes, proveďte následující kroky.

1. Vytvořte si omsagent daemon-set spuštěním```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms```
2. Výsledky budou vypadat podobně jako následující:

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

3. Stav omsagent můžete zkontrolovat spuštěním: ```helm status "omsagent"``` a výstup bude vypadat podobně jako následující:

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
   
    Další informace naleznete na stránce [Container Solution Helm Chart](https://aka.ms/omscontainerhelm).

### <a name="install-and-configure-windows-container-hosts"></a>Instalace a konfigurace hostitelů kontejnerů systému Windows

Informace v části slouží k instalaci a konfiguraci hostitelů kontejnerů systému Windows.

#### <a name="preparation-before-installing-windows-agents"></a>Příprava před instalací agentů systému Windows

Před instalací agentů do počítačů se systémem Windows je třeba nakonfigurovat službu Docker. Konfigurace umožňuje agentovi Windows nebo rozšíření virtuálního počítače Azure Monitor používat soket TCP Dockeru tak, aby agenti měli vzdálený přístup k daemonu Dockeru a zachycovali data pro monitorování.

##### <a name="to-configure-the-docker-service"></a>Konfigurace služby Docker  

Chcete-li povolit kanál TCP a pojmenovaný kanál pro systém Windows Server, proveďte následující příkazy prostředí PowerShell:

```
Stop-Service docker
dockerd --unregister-service
dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
Start-Service docker
```

Další informace o konfiguraci demonů Dockeru používané v kontejnerech Windows najdete v [tématu Docker Engine v systému Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon).

#### <a name="install-windows-agents"></a>Instalace agentů systému Windows

Chcete-li povolit monitorování kontejnerů systému Windows a Technologie Hyper-V, nainstalujte do počítačů se systémem Windows, které jsou hostiteli kontejnerů, nainstalujte agenta Microsoft Monitoring Agent (MMA). Počítače se systémem Windows v místním prostředí najdete v tématu [Připojení počítačů s Windows k Azure Monitoru](../../azure-monitor/platform/agent-windows.md). U virtuálních počítačů spuštěných v Azure je připojte k Azure Monitoru pomocí [rozšíření virtuálního počítače](../../azure-monitor/learn/quick-collect-azurevm.md).

Kontejnery systému Windows spuštěné na service fabric. Pro Service Fabric jsou však aktuálně podporované pouze [virtuální počítače spuštěné v Azure](../../azure-monitor/learn/quick-collect-azurevm.md) a počítače se systémem Windows ve vašem místním [prostředí.](../../azure-monitor/platform/agent-windows.md)

Můžete ověřit, zda je řešení monitorování kontejnerů správně nastaveno pro systém Windows. Chcete-li zkontrolovat, zda byla sada Management Pack správně stažena, vyhledejte *ContainerManagement.xxx*. Soubory by měly být ve složce C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Pack.

## <a name="solution-components"></a>Součásti řešení

Na portálu Azure přejděte do *Galerie řešení* a přidejte **řešení monitorování kontejnerů**. Pokud používáte agenty systému Windows, je při přidání tohoto řešení v každém počítači nainstalována následující sada Management Pack s agentem. Pro sadu Management Pack není vyžadována žádná konfigurace ani údržba.

- *ContainerManagement.xxx* nainstalována v programu C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Pack

## <a name="container-data-collection-details"></a>Podrobnosti o shromažďování dat kontejneru

Řešení monitorování kontejnerů shromažďuje různé metriky výkonu a data protokolu z hostitelů kontejnerů a kontejnerů pomocí agentů, které povolíte.

Data jsou shromažďována každé tři minuty podle následujících typů agentů.

- [Agent Log Analytics pro Linux](../../azure-monitor/learn/quick-collect-linux-computer.md)
- [Agent systému Windows](../../azure-monitor/platform/agent-windows.md)
- [Rozšíření virtuálního vás analýzy protokolů](../../azure-monitor/learn/quick-collect-azurevm.md)

### <a name="container-records"></a>Kontejnerové záznamy

V následující tabulce jsou uvedeny příklady záznamů shromážděných řešením monitorování kontejnerů a datových typů, které se zobrazují ve výsledcích hledání protokolu.

| Datový typ | Datový typ ve vyhledávání protokolů | Pole |
| --- | --- | --- |
| Výkon pro hostitele a kontejnery | `Perf` | Počítač, Název_objektu, Název_čítače &#40;%Čas procesoru, Disk čtení MB, MB zápisy na disk, MB využití paměti, bajty síťového příjmu, bajty síťového odesílání, oddělení využití procesoru, síťové&#41;, hodnota_času,timegenerated, proticestní, zdrojsystém |
| Zásoby kontejnerů | `ContainerInventory` | TimeGenerated, Počítač, název kontejneru, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Inventář obrázků kontejneru | `ContainerImageInventory` | TimeGenerated, Počítač, Image, ImageTag, ImageSize, VirtualSize, Spuštěno, Pozastaveno, Zastaveno, Se nezdařilo, SourceSystem, ImageID, TotalContainer |
| Protokol kontejneru | `ContainerLog` | TimeGenerated, Počítač, ID bitové kopie, název kontejneru, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Protokol služby kontejneru | `ContainerServiceLog`  | TimeGenerated, Počítač, TimeOfCommand, Obrázek, Příkaz, SourceSystem, ContainerID |
| Inventář uzlů kontejneru | `ContainerNodeInventory_CL`| TimeGenerated, Počítač, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Kubernetes zásoby | `KubePodInventory_CL` | TimeGenerated, Počítač, PodLabel_deployment_s, PodLabel_deploymentconfig_s, PodLabel_docker_registry_s, Name_s, Namespace_s, PodStatus_s, PodIp_s, PodUid_g, PodCreationTimeStamp_t, SourceSystem |
| Kontejnerový proces | `ContainerProcess_CL` | TimeGenerated, Počítač, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Akce Kubernetes | `KubeEvents_CL` | TimeGenerated, Počítač, Name_s, ObjectKind_s, Namespace_s, Reason_s, Type_s, SourceComponent_s, SourceSystem, Zpráva |

Popisky připojené k datovým typům *PodLabel* jsou vaše vlastní popisky. Připojené popisky PodLabel zobrazené v tabulce jsou příklady. Takže `PodLabel_deployment_s`, `PodLabel_deploymentconfig_s` `PodLabel_docker_registry_s` , se bude lišit v datové sadě `PodLabel_yourlabel_s`vašeho prostředí a obecně se podobají .

## <a name="monitor-containers"></a>Monitorování kontejnerů
Po povolení řešení na webu Azure Portal se na **dlaždici Kontejnery** zobrazí souhrnné informace o hostitelích kontejnerů a kontejnerech spuštěnéch v hostitelích.

![Kontejnery dlaždice](./media/containers/containers-title.png)

Na dlaždici je uveden přehled počtu kontejnerů, které máte v prostředí a jestli se nezdařilo, běželo nebo zastavilo.

### <a name="using-the-containers-dashboard"></a>Použití řídicího panelu Kontejnery

Klikněte na **dlaždici Kontejnery.** Odtud uvidíte zobrazení uspořádaná podle:

- **Události kontejneru** – zobrazuje stav kontejneru a počítače s kontejnery, které selhaly.
- **Protokoly kontejnerů** – zobrazuje graf souborů protokolu kontejnerů generovaných v průběhu času a seznam počítačů s nejvyšším počtem souborů protokolu.
- **Události Kubernetes** - Zobrazuje graf událostí Kubernetes generovaných v průběhu času a seznam důvodů, proč pody generovaly události. *Tato datová sada se používá pouze v linuxových prostředích.*
- **Kubernetes Název oboru Zásoby** - Zobrazuje počet oborů názvů a podů a zobrazuje jejich hierarchii. *Tato datová sada se používá pouze v linuxových prostředích.*
- **Inventář uzlů kontejneru** – zobrazuje počet typů orchestrace používaných v uzlech nebo hostitelích kontejnerů. Uzly počítače jsou také uvedeny podle počtu kontejnerů. *Tato datová sada se používá pouze v linuxových prostředích.*
- **Inventář obrázků kontejnerů –** zobrazuje celkový počet použitých bitových kopií kontejnerů a počet typů bitových kopií. Počet obrázků je také uveden podle značky obrázku.
- **Stav kontejnerů** – zobrazuje celkový počet uzlů kontejnerů nebo hostitelských počítačů, které mají spuštěné kontejnery. Počítače jsou také uvedeny podle počtu spuštěných hostitelů.
- **Proces kontejneru** – zobrazuje spojnicový graf procesů kontejneru spuštěných v průběhu času. Kontejnery jsou také uvedeny spuštěnípříkaz/proces v rámci kontejnerů. *Tato datová sada se používá pouze v linuxových prostředích.*
- **Výkon procesoru kontejneru** – zobrazuje spojnicový graf průměrného využití procesoru v čase pro uzly nebo hostitele počítače. Také uvádí uzly počítače nebo hostitele na základě průměrného využití procesoru.
- **Výkon paměti kontejneru** – zobrazuje spojnicový graf využití paměti v čase. Také uvádí využití paměti počítače na základě názvu instance.
- **Výkon počítače** – zobrazuje spojnicové grafy procenta výkonu procesoru v čase, procento využití paměti v čase a megabajty volného místa na disku v čase. Chcete-li zobrazit další podrobnosti, můžete najet na libovolnou čáru v grafu.

Každá oblast řídicího panelu je vizuální reprezentace hledání, která je spuštěna na shromážděná data.

![Řídicí panel kontejnerů](./media/containers/containers-dash01.png)

![Řídicí panel kontejnerů](./media/containers/containers-dash02.png)

V oblasti **Stav kontejneru** klikněte na horní oblast, jak je znázorněno níže.

![Stav kontejnerů](./media/containers/containers-status.png)

Otevře se služba Log Analytics s informacemi o stavu kontejnerů.

![Analýza protokolů pro kontejnery](./media/containers/containers-log-search.png)

Zde můžete vyhledávací dotaz upravit a upravit tak, aby našel konkrétní informace, které vás zajímají. Další informace o dotazech protokolu najdete [v tématu Protokoldotazy v Azure Monitor](../log-query/log-query-overview.md).

## <a name="troubleshoot-by-finding-a-failed-container"></a>Poradce při potížích při hledání kontejneru se nezdařilo

Log Analytics označí kontejner jako **neúspěšný,** pokud byl ukončen s nenulovým ukončovacím kódem. Můžete zobrazit přehled chyb a selhání v prostředí v oblasti **Neúspěšné kontejnery.**

### <a name="to-find-failed-containers"></a>Vyhledání neúspěšných kontejnerů

1. Klikněte na oblast **Stav kontejneru.**  
   ![stav kontejnerů](./media/containers/containers-status.png)
2. Log Analytics otevře a zobrazí stav kontejnerů, podobně jako následující.  
   ![kontejnery stát](./media/containers/containers-log-search.png)
3. Rozbalte řádek Se nezdařilo a kliknutím na + přidejte jeho kritéria do dotazu. Pak zakomentujte řádek Souhrn v dotazu.
   ![neúspěšné kontejnery](./media/containers/containers-state-failed-select.png)  
1. Spusťte dotaz a potom rozbalte řádek ve výsledcích, abyste zobrazili ID obrázku.  
   ![neúspěšné kontejnery](./media/containers/containers-state-failed.png)  
1. Do protokolu zadejte následující text. `ContainerImageInventory | where ImageID == <ImageID>`zobrazíte podrobnosti o obrázku, například velikost obrázku a počet zastavených a neúspěšných obrázků.  
   ![neúspěšné kontejnery](./media/containers/containers-failed04.png)

## <a name="query-logs-for-container-data"></a>Protokoly dotazů pro data kontejneru

Při řešení konkrétní chyby může pomoci zjistit, kde se ve vašem prostředí vyskytuje. Následující typy protokolů vám pomohou vytvořit dotazy, které vrátí požadované informace.

- **ContainerImageInventory** – Tento typ použijte, když se pokoušíte najít informace uspořádané podle obrázku a zobrazit informace o obrázku, jako jsou ID nebo velikosti obrázků.
- **ContainerInventory** – tento typ použijte, pokud chcete informace o umístění kontejneru, jaké jsou jejich názvy a jaké obrázky jsou spuštěny.
- **ContainerLog** – tento typ použijte, pokud chcete najít konkrétní informace a položky protokolu chyb.
- **ContainerNodeInventory_CL**  Tento typ použijte, pokud chcete informace o hostiteli nebo uzlu, kde jsou umístěny kontejnery. Poskytuje verzi Dockeru, typ orchestrace, úložiště a informace o síti.
- **ContainerProcess_CL** Tento typ slouží k rychlému zobrazení procesu spuštěného v kontejneru.
- **ContainerServiceLog** – použijte tento typ, když se pokoušíte najít informace o protokolu auditu pro daemon Dockeru, jako je například start, stop, delete nebo pull příkazy.
- **KubeEvents_CL**  Tento typ slouží k zobrazení událostí Kubernetes.
- **KubePodInventory_CL**  Tento typ použijte, pokud chcete porozumět informacím hierarchie clusteru.


### <a name="to-query-logs-for-container-data"></a>Dotazovat se protokolů pro data kontejneru

* Vyberte obrázek, o kterém víte, že se v poslední době nezdařil, a vyhledejte pro něj protokoly chyb. Začněte vyhledáním názvu kontejneru, který je spuštěn tuto bitovou kopii s **hledáním ContainerInventory.** Například vyhledejte`ContainerInventory | where Image == "ubuntu" and ContainerState == "Failed"`  
    ![Hledat kontejnery Ubuntu](./media/containers/search-ubuntu.png)

  Rozbalte libovolný řádek ve výsledcích a zobrazte podrobnosti pro tento kontejner.

## <a name="example-log-queries"></a>Příklad y protokolů

Často je užitečné vytvářet dotazy počínaje příkladem nebo dvěma a pak je upravit tak, aby vyhovovaly vašemu prostředí. Jako výchozí bod můžete experimentovat s oblastí **Ukázkové dotazy,** které vám pomohou vytvářet pokročilejší dotazy.

![Dotazy kontejnerů](./media/containers/containers-queries.png)

## <a name="saving-log-queries"></a>Ukládání dotazů protokolu

Ukládání dotazů je standardní funkce ve službě Azure Monitor. Uložením je budete mít ty, které jste našli užitečné užitečné pro budoucí použití.

Po vytvoření dotazu, který považujete za užitečný, ho uložte kliknutím na **Oblíbené položky** v horní části stránky Hledání protokolu. Později k němu můžete snadno přistupovat na stránce **Můj řídicí panel.**

## <a name="next-steps"></a>Další kroky

[Protokoly dotazů](../log-query/log-query-overview.md) pro zobrazení podrobných datových záznamů kontejneru.
