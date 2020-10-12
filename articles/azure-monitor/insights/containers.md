---
title: Řešení monitorování kontejnerů v Azure Monitor | Microsoft Docs
description: Řešení pro monitorování kontejnerů v Azure Monitor vám pomůže zobrazit a spravovat hostitele kontejnerů pro Docker a Windows v jednom umístění.
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/06/2020
ms.openlocfilehash: b681e3fa4963a8fe899ccbad8dbf1bbdfbe452ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87326898"
---
# <a name="container-monitoring-solution-in-azure-monitor"></a>Řešení pro monitorování kontejnerů v Azure Monitor

![Symbol kontejnerů](./media/containers/containers-symbol.png)

Tento článek popisuje, jak nastavit a použít řešení pro monitorování kontejnerů v Azure Monitor, které vám pomůže zobrazit a spravovat hostitele kontejnerů pro Docker a Windows v jednom umístění. Docker je systém virtualizace softwaru, který slouží k vytváření kontejnerů, které automatizují nasazení softwaru do své IT infrastruktury.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Toto řešení ukazuje, které kontejnery jsou spuštěny, jaká image kontejneru je spuštěná a kde jsou spuštěné kontejnery. Můžete zobrazit podrobné informace o auditu, které zobrazují příkazy používané s kontejnery. A můžete řešit problémy s kontejnery zobrazením a prohledáváním centralizovaných protokolů, aniž byste museli vzdáleně zobrazovat hostitele Docker nebo Windows. Můžete najít kontejnery, které mohou být v hostiteli na vysokou úroveň a spotřebovávat nadbytečné prostředky. A můžete zobrazit centralizované informace o využití procesoru, paměti, úložiště a sítě a informace o výkonu pro kontejnery. V počítačích se systémem Windows můžete centralizovat a porovnat protokoly z Windows serveru, Hyper-V a kontejnerů Docker. Řešení podporuje následující orchestrace kontejnerů:

- Docker Swarm
- DC/OS
- Kubernetes
- Service Fabric
- Red Hat OpenShift

Pokud máte kontejnery nasazené v [Azure Service Fabric](../../service-fabric/service-fabric-overview.md), doporučujeme povolit [řešení Service Fabric](../../service-fabric/service-fabric-diagnostics-oms-setup.md) i toto řešení, aby zahrnovalo monitorování událostí clusteru. Než povolíte řešení Service Fabric, přečtěte si téma [řešení Service Fabric](../../service-fabric/service-fabric-diagnostics-event-analysis-oms.md) , abyste porozuměli tomu, co nabízí a jak ho používat.

Pokud vás zajímá monitorování výkonu vašich úloh nasazených do prostředí Kubernetes hostovaných ve službě Azure Kubernetes Service (AKS), přečtěte si téma [monitorování služby Azure Kubernetes](./container-insights-overview.md). Řešení pro monitorování kontejnerů nepodporuje monitorování této platformy.  

Následující diagram znázorňuje vztahy mezi různými hostiteli kontejnerů a agenty s Azure Monitor.

![Diagram kontejnerů](./media/containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>Požadavky na systém a podporované platformy

Než začnete, Projděte si následující podrobnosti, abyste ověřili splnění požadovaných požadavků.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Podpora řešení pro monitorování kontejnerů pro Docker Orchestrator a platformu OS

Následující tabulka popisuje podporu pro orchestraci a monitorování operačního systému pro inventář kontejnerů, výkon a protokoly s Azure Monitor.   

|Orchestrace Docker | ACS | Linux | Windows | Kontejner<br>inventář | Image<br>inventář | Node<br>inventář | Kontejner<br>Výkon | Kontejner<br>Událost | Událost<br>Protokol | Kontejner<br>Protokol |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Kubernetes | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mesosphere<br>DC/OS | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Swarm | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Služba<br>Fabric | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Red Hat – otevřeno<br>Posouvá | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
| Windows Server<br>skupin | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Server Linux<br>skupin | | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |

### <a name="docker-versions-supported-on-linux"></a>Verze Docker podporované v systému Linux

- Docker 1,11 na 1,13
- Docker CE a EE v 17.06

### <a name="x64-linux-distributions-supported-as-container-hosts"></a>distribuce x64 pro Linux podporovaná jako hostitelé kontejnerů

- Ubuntu 14,04 LTS a 16,04 LTS
- CoreOS (stabilní)
- Amazon Linux 2016.09.0
- openSUSE 13.2
- openSUSE, přestupné 42,2
- CentOS 7,2 a 7,3
- SLES 12
- RHEL 7,2 a 7,3
- Platforma Red Hat OpenShift Container Platform (OCP) 3,4 a 3,5
- ACS Mesosphere DC/OS 1.7.3 na 1.8.8
- ACS Kubernetes 1.4.5 na 1,6
    - Kubernetes události, inventář Kubernetes a procesy kontejneru se podporují jenom s verzí 1.4.1-45 a novějším agenta Log Analytics pro Linux.
- Swarm Docker služby ACS

[!INCLUDE [log-analytics-agent-note.md](../../../includes/log-analytics-agent-note.md)] 

### <a name="supported-windows-operating-system"></a>Podporovaný operační systém Windows

- Windows Server 2016
- Windows 10 výročí Edition (Professional nebo Enterprise)

### <a name="docker-versions-supported-on-windows"></a>Verze Docker podporované ve Windows

- Docker 1,12 a 1,13
- Docker 17.03.0 a novější

## <a name="installing-and-configuring-the-solution"></a>Instalace a konfigurace řešení

K instalaci a konfiguraci řešení můžete použít následující informace.

1. Přidejte řešení pro monitorování kontejnerů do pracovního prostoru Log Analytics z [webu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) nebo pomocí procesu popsaného v tématu [Přidání řešení monitorování z galerie řešení](./solutions.md).

2. Nainstalujte a použijte Docker s agentem Log Analytics. V závislosti na operačním systému a Docker Orchestrator můžete ke konfiguraci agenta použít následující metody.
   - Pro samostatné hostitele:
     - V podporovaných operačních systémech Linux nainstalujte a spusťte Docker a pak nainstalujte a nakonfigurujte [agenta Log Analytics pro Linux](../learn/quick-collect-linux-computer.md).  
     - V CoreOS nemůžete spustit agenta Log Analytics pro Linux. Místo toho spustíte kontejnerovou verzi Log Analytics agenta pro Linux. Pokud pracujete s kontejnery v Azure Government cloudu, přečtěte si téma hostitelé pro Linux kontejnerů, včetně CoreOS nebo Azure Government hostitelů kontejnerů Linux, včetně CoreOS.
     - V systémech Windows Server 2016 a Windows 10 nainstalujte modul Docker a klienta a pak připojte agenta, abyste mohli shromažďovat informace a odesílali je Azure Monitor. Pokud máte prostředí systému Windows, přečtěte si téma [instalace a konfigurace hostitelů kontejnerů Windows](#install-and-configure-windows-container-hosts) .
   - Pro orchestraci více hostitelů Docker:
     - Pokud máte prostředí Red Hat OpenShift, přečtěte si téma Konfigurace agenta Log Analytics pro Red Hat OpenShift.
     - Pokud máte cluster Kubernetes s použitím Azure Container Service:
       - Přečtěte si téma [Konfigurace agenta Log Analytics Linux pro Kubernetes](#configure-a-log-analytics-linux-agent-for-kubernetes).
       - Přečtěte si téma [konfigurace Log Analytics agenta pro Windows pro Kubernetes](#configure-a-log-analytics-windows-agent-for-kubernetes).
       - Přečtěte si téma použití Helm k nasazení agenta Log Analytics na Linux Kubernetes.
     - Pokud máte cluster Azure Container Service DC/OS, přečtěte si další informace o [monitorování Azure Container Service clusteru DC/OS pomocí Azure monitor](/previous-versions/azure/container-service/dcos-swarm/container-service-monitoring-oms).
     - Pokud máte prostředí režimu Docker Swarm, přečtěte si další informace v konfiguraci agenta Log Analytics pro Docker Swarm.
     - Pokud máte Cluster Service Fabric, přečtěte si další informace o [monitorování kontejnerů pomocí Azure monitor](../../service-fabric/service-fabric-diagnostics-oms-containers.md).

Další informace o tom, jak nainstalovat a nakonfigurovat moduly Docker v počítačích se systémem Windows, najdete v článku o nástroji [Docker v systému Windows](/virtualization/windowscontainers/manage-docker/configure-docker-daemon) .

> [!IMPORTANT]
> **Aby** bylo možné nainstalovat [agenta Log Analytics pro Linux](../learn/quick-collect-linux-computer.md) do hostitelů kontejnerů, musí být spuštěný Docker. Pokud jste již agenta nainstalovali před instalací Docker, je nutné přeinstalovat agenta Log Analytics pro Linux. Další informace o Docker najdete na [webu Docker](https://www.docker.com).

### <a name="install-and-configure-linux-container-hosts"></a>Instalace a konfigurace hostitelů kontejnerů pro Linux

Po instalaci Docker použijte následující nastavení pro hostitele kontejneru a nakonfigurujte agenta pro použití s Docker. Nejdřív potřebujete Log Analytics ID a klíč pracovního prostoru, který najdete v Azure Portal. V pracovním prostoru kliknutím na **rychlé zprovoznění**  >  **počítače** zobrazíte **ID vašeho pracovního prostoru** a **primární klíč**.  Obě hodnoty zkopírujte a vložte do oblíbeného editoru.

**Pro všechny hostitele kontejnerů pro Linux s výjimkou CoreOS:**

- Další informace a postup pro instalaci agenta Log Analytics pro Linux najdete v tématu [Přehled agenta Log Analytics](../platform/log-analytics-agent.md).

**Pro všechny hostitele kontejnerů pro Linux, včetně CoreOS:**

Spusťte kontejner, který chcete monitorovat. Upravte a použijte následující příklad:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**Pro všechny Azure Government hostitele kontejnerů Linux, včetně CoreOS:**

Spusťte kontejner, který chcete monitorovat. Upravte a použijte následující příklad:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**Přepínání z použití nainstalovaného agenta Linux na jeden v kontejneru**

Pokud jste dříve používali přímo nainstalovaného agenta a chcete místo toho použít agenta běžícího v kontejneru, je nutné nejprve odebrat agenta Log Analytics pro Linux. Informace o tom, jak úspěšně odinstalovat agenta, najdete v tématu [odinstalace agenta Log Analytics pro systém Linux](../learn/quick-collect-linux-computer.md) .  

#### <a name="configure-a-log-analytics-agent-for-docker-swarm"></a>Konfigurace agenta Log Analytics pro Docker Swarm

Agenta Log Analytics můžete spustit jako globální službu v Docker Swarm. Pomocí následujících informací vytvořte službu agenta Log Analytics. Je potřeba zadat ID a primární klíč svého Log Analyticsho pracovního prostoru.

- Spusťte následující příkaz na hlavním uzlu.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

##### <a name="secure-secrets-for-docker-swarm"></a>Zabezpečené tajné kódy pro Docker Swarm

V případě Docker Swarm se po vytvoření tajného klíče pro ID pracovního prostoru a primární klíč vytvoří informace o tajných klíčích pomocí následujících informací.

1. Spusťte následující příkaz na hlavním uzlu.

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

3. Spusťte následující příkaz pro připojení tajných kódů k kontejneru Log Analytics kontejnerů.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="configure-a-log-analytics-agent-for-red-hat-openshift"></a>Konfigurace agenta Log Analytics pro Red Hat OpenShift

Existují tři způsoby, jak přidat agenta Log Analytics do Red Hat OpenShift a začít shromažďovat data monitorování kontejnerů.

* [Instalace agenta Log Analytics pro Linux](../learn/quick-collect-linux-computer.md) přímo na každý uzel OpenShift  
* [Povolení rozšíření Log Analytics virtuálního počítače](../learn/quick-collect-azurevm.md) v každém uzlu OpenShift, který se nachází v Azure  
* Instalace agenta Log Analytics jako sady OpenShift démona  

V této části se zabýváme kroky potřebnými k instalaci agenta Log Analytics jako sady démonů OpenShift.  

1. Přihlaste se k uzlu hlavní server OpenShift a zkopírujte soubor YAML [OCP-omsagent. yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) z GitHubu do svého hlavního uzlu a upravte hodnotu pomocí ID vašeho pracovního prostoru Log Analytics a s vaším primárním klíčem.
2. Spuštěním následujících příkazů vytvořte projekt pro Azure Monitor a nastavte uživatelský účet.

    ```
    oc adm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Chcete-li nasadit sadu démonů, spusťte následující příkaz:

    `oc create -f ocp-omsagent.yaml`

4. Pokud chcete ověřit, že je nakonfigurovaná a funguje správně, zadejte následující:

    `oc describe daemonset omsagent`  

    a výstup by měl vypadat přibližně takto:

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

Pokud chcete použít tajné klíče k zabezpečení Log Analytics ID a primárního klíče při použití souboru s démonem Log Analytics agenta, proveďte následující kroky.

1. Přihlaste se k uzlu hlavní server OpenShift a zkopírujte soubor YAML [OCP-DS-omsagent. yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) a tajný kód pro generování skriptu [OCP-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) z GitHubu.  Tento skript vygeneruje soubor YAML tajných klíčů pro Log Analytics ID pracovního prostoru a primární klíč k zabezpečení informací o tajnosti.  
2. Spuštěním následujících příkazů vytvořte projekt pro Azure Monitor a nastavte uživatelský účet. Skript pro generování tajného klíče požádá o vaše ID pracovního prostoru Log Analytics `<WSID>` a primární klíč `<KEY>` a po dokončení vytvoří soubor OCP-Secret. yaml.  

    ```
    oc adm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Soubor tajného klíče nasaďte spuštěním následujícího kódu:

    `oc create -f ocp-secret.yaml`

4. Ověřte nasazení spuštěním následujícího:

    `oc describe secret omsagent-secret`  

    a výstup by měl vypadat přibližně takto:  

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

5. Nasaďte soubor YAML s démonem Log Analytics agenta spuštěním následujícího:

    `oc create -f ocp-ds-omsagent.yaml`  

6. Ověřte nasazení spuštěním následujícího:

    `oc describe ds oms`

    a výstup by měl vypadat přibližně takto:

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

Pro Kubernetes pomocí skriptu vygenerujte soubor tajných klíčů YAML pro ID pracovního prostoru a primární klíč pro instalaci agenta Log Analytics pro Linux. Na stránce [Log Analytics Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) se nacházejí soubory, které můžete použít s nebo bez tajných informací.

- Výchozí agent Log Analytics pro Linux DaemonSet nemá tajné informace (omsagent. yaml).
- Agent Log Analytics pro Linux DaemonSet YAML používá k vygenerování souboru tajných kódů YAML (omsagentsecret. yaml) tajné informace (omsagent-DS-tajné soubory. yaml) se skripty pro generování tajného klíče.

Můžete si vybrat, že chcete vytvořit omsagent DaemonSets s tajnými kódy nebo bez nich.

**Výchozí OMSagent soubor YAML DaemonSet bez tajných klíčů**

- Pro výchozí soubor Log Analytics agenta DaemonSet YAML, nahraďte `<WSID>` a `<KEY>` pro WSID a klíč. Zkopírujte soubor do hlavního uzlu a spusťte následující příkaz:

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**Výchozí OMSagent soubor YAML DaemonSet s tajnými kódy**

1. Pokud chcete použít Log Analytics agenta DaemonSet s použitím tajných informací, vytvořte nejdřív tajné klíče.
    1. Zkopírujte skriptový a tajný soubor šablony a ujistěte se, že jsou ve stejném adresáři.
        - Tajný kód generování skriptu – secret-gen.sh
        - Šablona tajného kódu-tajné šablony. yaml
    2. Spusťte skript, podobně jako v následujícím příkladu. Skript zobrazí výzvu k zadání ID a primárního klíče pracovního prostoru Log Analytics a jakmile je zadáte, skript vytvoří tajný soubor YAML, abyste ho mohli spustit.   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. Tajné klíče vytvoříte spuštěním následujícího:
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. Chcete-li ověřit, spusťte následující příkaz:

        ```
        keiko@ubuntu16-13db:~# sudo kubectl get secrets
        ```

        Výstup by měl vypadat přibližně takto:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        ```

        ```
        keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
        ```

        Výstup by měl vypadat přibližně takto:

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

    5. Vytvoření omsagent procesu démon-nastaveného spuštěním ```sudo kubectl create -f omsagent-ds-secrets.yaml```

2. Ověřte, že je spuštěný DaemonSet agenta Log Analytics, podobně jako v následujícím příkladu:

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```

Pro Kubernetes pomocí skriptu vygenerujte soubor tajných klíčů YAML pro ID pracovního prostoru a primární klíč pro agenta Log Analytics pro Linux. K zabezpečení tajných informací použijte následující příklady informací se [souborem omsagent YAML](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml) .

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

#### <a name="configure-a-log-analytics-windows-agent-for-kubernetes"></a>Konfigurace Log Analytics agenta pro Windows pro Kubernetes

V případě sady Windows Kubernetes použijte skript k vygenerování souboru tajných klíčů YAML pro ID pracovního prostoru a primární klíč pro instalaci agenta Log Analytics. Na stránce [Log Analytics Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) se nacházejí soubory, které můžete použít s vašimi tajnými informacemi.  Pro hlavní uzly a uzly agentů je potřeba nainstalovat agenta Log Analytics samostatně.  

1. Pokud chcete použít Log Analytics agenta DaemonSet s použitím tajných informací na hlavním uzlu, přihlaste se a vytvořte nejdřív tajné klíče.
    1. Zkopírujte skriptový a tajný soubor šablony a ujistěte se, že jsou ve stejném adresáři.
        - Tajný kód generování skriptu – secret-gen.sh
        - Šablona tajného kódu-tajné šablony. yaml

    2. Spusťte skript, podobně jako v následujícím příkladu. Skript zobrazí výzvu k zadání ID a primárního klíče pracovního prostoru Log Analytics a jakmile je zadáte, skript vytvoří tajný soubor YAML, abyste ho mohli spustit.

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. Vytvoření omsagent procesu démon-nastaveného spuštěním ```kubectl create -f omsagentsecret.yaml```
    4. Chcete-li ověřit, spusťte následující příkaz:

        ```
        root@ubuntu16-13db:~# kubectl get secrets
        ```

        Výstup by měl vypadat přibližně takto:

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

    5. Vytvoření omsagent procesu démon-nastaveného spuštěním ```kubectl create -f ws-omsagent-de-secrets.yaml```

2. Ověřte, že je spuštěný DaemonSet agenta Log Analytics, podobně jako v následujícím příkladu:

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Chcete-li nainstalovat agenta do pracovního uzlu, ve kterém je spuštěn systém Windows, postupujte podle kroků v části [instalace a konfigurace hostitelů kontejnerů Windows](#install-and-configure-windows-container-hosts).

#### <a name="use-helm-to-deploy-log-analytics-agent-on-linux-kubernetes"></a>Nasazení agenta Log Analytics v systému Linux Kubernetes pomocí Helm

Pokud chcete použít Helm k nasazení agenta Log Analytics v prostředí Kubernetes pro Linux, proveďte následující kroky.

1. Vytvoření omsagent procesu démon-nastaveného spuštěním ```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms```
2. Výsledky budou vypadat podobně jako v následujícím příkladu:

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

### <a name="install-and-configure-windows-container-hosts"></a>Instalace a konfigurace hostitelů kontejnerů Windows

K instalaci a konfiguraci hostitelů kontejnerů Windows použijte část informace v části.

#### <a name="preparation-before-installing-windows-agents"></a>Příprava před instalací agentů Windows

Než nainstalujete agenty na počítače s Windows, musíte nakonfigurovat službu Docker. Tato konfigurace umožňuje agentu Windows nebo rozšíření virtuálního počítače Azure Monitor používat soket rozhraní Docker TCP, aby mohli agenti přistupovat ke vzdálenému démonu Docker a zachytit data pro monitorování.

##### <a name="to-configure-the-docker-service"></a>Konfigurace služby Docker  

Pro povolení kanálu TCP a pojmenovaného kanálu pro Windows Server proveďte následující příkazy PowerShellu:

```
Stop-Service docker
dockerd --unregister-service
dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
Start-Service docker
```

Další informace o konfiguraci démona Docker používaného s kontejnery Windows najdete v tématu [modul Docker ve Windows](/virtualization/windowscontainers/manage-docker/configure-docker-daemon).

#### <a name="install-windows-agents"></a>Nainstalovat agenty Windows

Chcete-li povolit monitorování kontejnerů systému Windows a technologie Hyper-V, nainstalujte Microsoft Monitoring Agent (MMA) do počítačů se systémem Windows, které jsou hostiteli kontejneru. Počítače se systémem Windows v místním prostředí najdete v tématu [připojení počítačů se systémem Windows k Azure monitor](../platform/agent-windows.md). Pro virtuální počítače běžící v Azure je připojte k Azure Monitor pomocí [rozšíření virtuálního počítače](../learn/quick-collect-azurevm.md).

Kontejnery Windows běžící na Service Fabric můžete monitorovat. V současné době se ale pro Service Fabric podporují jenom [virtuální počítače běžící v Azure](../learn/quick-collect-azurevm.md) a [počítače s Windows v místním prostředí](../platform/agent-windows.md) .

Můžete ověřit, jestli je řešení pro monitorování kontejnerů správně nastavené pro Windows. Chcete-li zkontrolovat, zda byl Management Pack správně stažen, vyhledejte *ContainerManagement.xxx*. Soubory by měly být ve složce C:\Program Files\Microsoft monitoring Agent\Agent\Health Service State\Management Packs.

## <a name="solution-components"></a>Součásti řešení

Z Azure Portal přejděte na *Galerie řešení* a přidejte **řešení pro monitorování kontejnerů**. Pokud používáte agenty Windows, při přidání tohoto řešení se do každého počítače s agentem nainstaluje následující Management Pack. Pro Management Pack není nutná žádná konfigurace ani údržba.

- *ContainerManagement.xxx* nainstalované v adresáři C:\Program Files\Microsoft monitoring Agent\Agent\Health Service State\Management Pack

## <a name="container-data-collection-details"></a>Podrobnosti shromažďování dat kontejneru

Řešení pro monitorování kontejnerů shromažďuje různé metriky výkonu a data protokolů z hostitelů kontejnerů a kontejnerů pomocí agentů, které povolíte.

Následující typy agentů shromažďují data každé tři minuty.

- [Agent Log Analytics pro Linux](../learn/quick-collect-linux-computer.md)
- [Agent pro Windows](../platform/agent-windows.md)
- [Rozšíření virtuálního počítače Log Analytics](../learn/quick-collect-azurevm.md)

### <a name="container-records"></a>Záznamy kontejneru

V následující tabulce jsou uvedeny příklady záznamů shromážděných řešením monitorování kontejnerů a typy dat, které se zobrazí ve výsledcích prohledávání protokolu.

| Datový typ | Datový typ v hledání v protokolu | Pole |
| --- | --- | --- |
| Výkon pro hostitele a kontejnery | `Perf` | Počítač, ObjectName, CounterName &#40;% času procesoru, čtení z disku MB, zápisy na disk MB, využití paměti MB, počet přijatých bajtů sítě, počet bajtů pro odesílání, využití procesoru sec, síť&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem |
| Inventář kontejneru | `ContainerInventory` | TimeGenerated, počítač, název kontejneru, ContainerHostname, image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Inventář imagí kontejneru | `ContainerImageInventory` | TimeGenerated, počítač, image, ImageTag, ImageSize, VirtualSize, spuštěno, pozastaveno, zastaveno, selhalo, SourceSystem, ImageID, TotalContainer |
| Protokol kontejneru | `ContainerLog` | TimeGenerated, počítač, ID image, název kontejneru, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Protokol služby kontejneru | `ContainerServiceLog`  | TimeGenerated, Computer, TimeOfCommand, image, Command, SourceSystem, ContainerID |
| Inventář uzlů kontejneru | `ContainerNodeInventory_CL`| TimeGenerated, počítač, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventář Kubernetes | `KubePodInventory_CL` | TimeGenerated, počítač, PodLabel_deployment_s, PodLabel_deploymentconfig_s, PodLabel_docker_registry_s, Name_s, Namespace_s, PodStatus_s, PodIp_s, PodUid_g, PodCreationTimeStamp_t, SourceSystem |
| Kontejnerový proces | `ContainerProcess_CL` | TimeGenerated, počítač, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Události Kubernetes | `KubeEvents_CL` | TimeGenerated, počítač, Name_s, ObjectKind_s, Namespace_s, Reason_s, Type_s, SourceComponent_s, SourceSystem, zpráva |

Popisky připojené k datovým typům *PodLabel* jsou vlastní popisky. Příklady přidaných popisků PodLabel jsou uvedené v tabulce. Takže, `PodLabel_deployment_s` , `PodLabel_deploymentconfig_s` , se `PodLabel_docker_registry_s` bude lišit v sadě dat vašeho prostředí a obecně se bude podobat `PodLabel_yourlabel_s` .

## <a name="monitor-containers"></a>Monitorování kontejnerů
Po povolení řešení v Azure Portal dlaždice **kontejnery** zobrazí souhrnné informace o hostitelích kontejnerů a kontejnerech spuštěných v hostitelích.

![Dlaždice kontejnery](./media/containers/containers-title.png)

Tato dlaždice zobrazuje přehled o počtu kontejnerů v prostředí a o tom, jestli jsou neúspěšné, spuštěné nebo zastavené.

### <a name="using-the-containers-dashboard"></a>Používání řídicího panelu kontejnerů

Klikněte na dlaždici **kontejnery** . Tady uvidíte zobrazení uspořádaná podle:

- **Události kontejneru** – zobrazuje stav kontejneru a počítače s neúspěšnými kontejnery.
- **Protokoly kontejneru** – zobrazuje graf souborů protokolu kontejneru vygenerovaných v průběhu času a seznam počítačů s největším počtem souborů protokolu.
- **Události Kubernetes** – zobrazuje graf událostí Kubernetes generovaných v průběhu času a seznam důvodů, proč lusky vygenerovaly události. *Tato datová sada se používá jenom v prostředích Linux.*
- **Inventář oboru názvů Kubernetes** – zobrazuje počet oborů názvů a lusků a ukazuje jejich hierarchii. *Tato datová sada se používá jenom v prostředích Linux.*
- **Inventář uzlů kontejneru** – zobrazuje počet typů orchestrace používaných na uzlech nebo hostitelích kontejneru. Uzly počítače/hostitelé jsou uvedeny také podle počtu kontejnerů. *Tato datová sada se používá jenom v prostředích Linux.*
- **Inventář imagí kontejneru** – zobrazuje celkový počet použitých imagí kontejneru a počet typů obrázků. Počet obrázků je také uveden pomocí značky image.
- **Stav kontejnerů** – zobrazuje celkový počet uzlů nebo hostitelských počítačů, ve kterých jsou spuštěné kontejnery. Počítače jsou také uvedeny podle počtu spuštěných hostitelů.
- **Proces kontejneru** – zobrazí spojnicový graf procesů kontejneru spuštěných v průběhu času. Kontejnery jsou také uvedeny spuštěním příkazu nebo procesu v rámci kontejnerů. *Tato datová sada se používá jenom v prostředích Linux.*
- **Výkon procesoru kontejneru** – zobrazuje spojnicový graf průměrného využití procesoru v čase pro uzly počítače nebo hostitele. Uvádí také seznam uzlů a hostitelů počítačů na základě průměrného využití procesoru.
- **Výkon paměti kontejneru** – zobrazuje spojnicový graf využití paměti v průběhu času. Také uvádí využití paměti počítače na základě názvu instance.
- **Výkon počítače** – zobrazí spojnicové grafy s procentem výkonu procesoru v čase, využití paměti v průběhu času a MB volného místa na disku v průběhu času. Pokud si chcete zobrazit další podrobnosti, můžete ukazatel myši umístit na libovolný řádek v grafu.

Každá oblast řídicího panelu je vizuální znázornění hledání, které se spouští na shromážděných datech.

![Řídicí panel kontejnerů](./media/containers/containers-dash01.png)

![Řídicí panel kontejnerů](./media/containers/containers-dash02.png)

V oblasti **stav kontejneru** klikněte na horní oblast, jak je znázorněno níže.

![Stav kontejnerů](./media/containers/containers-status.png)

Log Analytics se otevře a zobrazí se informace o stavu vašich kontejnerů.

![Log Analytics pro kontejnery](./media/containers/containers-log-search.png)

Odtud můžete upravit vyhledávací dotaz, abyste mohli najít konkrétní informace, které vás zajímají. Další informace o dotazech protokolu najdete [v tématu Protokolování dotazů v Azure monitor](../log-query/log-query-overview.md).

## <a name="troubleshoot-by-finding-a-failed-container"></a>Řešení potíží hledáním neúspěšného kontejneru

Log Analytics označí kontejner jako **neúspěšný** , pokud byl ukončen s nenulovým ukončovacím kódem. Přehled chyb a selhání v prostředí najdete v oblasti **selhání kontejnerů** .

### <a name="to-find-failed-containers"></a>Vyhledání kontejnerů, které selhaly

1. Klikněte na oblast **stavu kontejneru** .  
   ![stav kontejnerů](./media/containers/containers-status.png)
2. Log Analytics se otevře a zobrazí stav kontejnerů, podobně jako v následujícím příkladu.  
   ![stav kontejnerů](./media/containers/containers-log-search.png)
3. Rozbalte řádek selhání a kliknutím na + přidejte jeho kritéria do dotazu. Pak přidejte do dotazu řádek souhrnu.
   ![neúspěšné kontejnery](./media/containers/containers-state-failed-select.png)  
1. Spusťte dotaz a potom rozbalte čáru ve výsledcích, abyste zobrazili ID obrázku.  
   ![neúspěšné kontejnery](./media/containers/containers-state-failed.png)  
1. V dotazu protokolu zadejte následující text. `ContainerImageInventory | where ImageID == <ImageID>` Chcete-li zobrazit podrobnosti o imagi, jako je například velikost obrázku a počet zastavených a neúspěšných imagí.  
   ![neúspěšné kontejnery](./media/containers/containers-failed04.png)

## <a name="query-logs-for-container-data"></a>Dotazy na data kontejneru v protokolech

Při odstraňování potíží s konkrétní chybou vám může pomáhat zjistit, kde se ve vašem prostředí objevuje. Následující typy protokolů vám pomůžou vytvořit dotazy, které vrátí požadované informace.

- **ContainerImageInventory** – tento typ použijte, pokud se pokoušíte najít informace uspořádané podle obrázku a zobrazit informace o obrázku, jako jsou ID nebo velikosti obrázků.
- **ContainerInventory** – tento typ použijte, když chcete získat informace o umístění kontejneru, o jejich názvech a o tom, jaké image jsou spuštěné.
- **ContainerLog** – tento typ použijte, když chcete najít konkrétní informace a položky protokolu chyb.
- **ContainerNodeInventory_CL**  Tento typ použijte, pokud chcete informace o hostiteli nebo uzlu, kde se nacházejí kontejnery. Poskytuje informace o verzi Docker, typu orchestrace, úložišti a síti.
- **ContainerProcess_CL** Tento typ slouží k rychlému zobrazení procesu spuštěného v rámci kontejneru.
- **ContainerServiceLog** – tento typ použijte, když se pokoušíte najít informace o záznamech auditu pro démona Docker, jako je třeba spustit, zastavit, odstranit nebo přijmout příkazy.
- **KubeEvents_CL**  Pomocí tohoto typu můžete zobrazit události Kubernetes.
- **KubePodInventory_CL**  Tento typ použijte, pokud chcete pochopit informace o hierarchii clusteru.

### <a name="to-query-logs-for-container-data"></a>Dotazování protokolů na data kontejneru

* Vyberte bitovou kopii, která se nedávno nezdařila, a vyhledejte v ní protokoly chyb. Začněte hledáním názvu kontejneru, na kterém je spuštěná tato image, pomocí hledání **ContainerInventory** . Vyhledejte například `ContainerInventory | where Image == "ubuntu" and ContainerState == "Failed"`  
    ![Hledat kontejnery Ubuntu](./media/containers/search-ubuntu.png)

  Rozbalením libovolného řádku ve výsledcích zobrazíte podrobnosti o daném kontejneru.

## <a name="example-log-queries"></a>Příklady dotazů protokolu

Často je užitečné vytvářet dotazy počínaje příkladem nebo dvěma a pak je upravit tak, aby vyhovovaly vašemu prostředí. Jako výchozí bod můžete experimentovat s oblastí **vzorových dotazů** na pravé straně stránky řešení, která vám umožní sestavovat pokročilejší dotazy.

![Dotazy na kontejnery](./media/containers/containers-queries.png)

## <a name="saving-log-queries"></a>Ukládání dotazů protokolu

Ukládání dotazů je standardní funkcí v Azure Monitor. Když je uložíte, budete mít ty, které jste našli užitečnou užitečnou pro budoucí použití.

Až vytvoříte dotaz, který je užitečný, uložte ho kliknutím na **Oblíbené** v horní části stránky hledání v protokolu. Pak k němu můžete snadno přistupovat později ze stránky **Můj řídicí panel** .

## <a name="next-steps"></a>Další kroky

V [protokolech dotazů](../log-query/log-query-overview.md) zobrazíte podrobné záznamy dat kontejneru.

