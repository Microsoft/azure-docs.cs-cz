---
title: Připojení a Správa zařízení GPU Microsoft Azure Stack Edge pro prostřednictvím rozhraní Windows PowerShellu | Microsoft Docs
description: Popisuje, jak se připojit ke službě Azure Stack Edge pro a následně spravovat přes rozhraní Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/06/2020
ms.author: alkohli
ms.openlocfilehash: 27af230f8fa157f76865bd38a48c17640491d7db
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896185"
---
# <a name="manage-an-azure-stack-edge-pro-gpu-device-via-windows-powershell"></a>Správa zařízení GPU Azure Stack Edge pro pomocí prostředí Windows PowerShell

Řešení Azure Stack Edge pro umožňuje zpracovávat data a odesílat je přes síť do Azure. Tento článek popisuje některé úlohy konfigurace a správy pro zařízení Azure Stack Edge pro. Ke správě zařízení můžete použít rozhraní Azure Portal, místní webové uživatelské rozhraní nebo prostředí Windows PowerShell.

Tento článek se zaměřuje na to, jak se můžete připojit k rozhraní PowerShellu zařízení a úlohám, které můžete používat v tomto rozhraní. 


## <a name="connect-to-the-powershell-interface"></a>Připojte se k rozhraní PowerShellu.

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Vytvoření balíčku pro podporu

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

<!--## Upload certificate

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

You can also upload IoT Edge certificates to enable a secure connection between your IoT Edge device and the downstream devices that may connect to it. There are three IoT Edge certificates (*.pem* format) that you need to install:

- Root CA certificate or the owner CA
- Device CA certificate
- Device key certificate

The following example shows the usage of this cmdlet to install IoT Edge certificates:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
When you run this cmdlet, you will be prompted to provide the password for the network share.

For more information on certificates, go to [Azure IoT Edge certificates](../iot-edge/iot-edge-certs.md) or [Install certificates on a gateway](../iot-edge/how-to-create-transparent-gateway.md).-->

## <a name="view-device-information"></a>Zobrazit informace o zařízení
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="view-gpu-driver-information"></a>Zobrazit informace o ovladači GPU

Pokud je v zařízení nakonfigurovaná výpočetní role, můžete informace o ovladači GPU získat taky přes rozhraní PowerShell. 

1. [Připojte se k rozhraní PowerShell](#connect-to-the-powershell-interface).
2. Použijte `Get-HcsGpuNvidiaSmi` k získání informací o ovladači GPU pro vaše zařízení.

    Následující příklad ukazuje použití této rutiny:

    ```powershell
    Get-HcsGpuNvidiaSmi
    ```
    Poznamenejte si informace o ovladači z ukázkového výstupu této rutiny.

    ```powershell    
    +-----------------------------------------------------------------------------+    
    | NVIDIA-SMI 440.64.00    Driver Version: 440.64.00    CUDA Version: 10.2     |    
    |-------------------------------+----------------------+----------------------+    
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |    
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |    
    |===============================+======================+======================|    
    |   0  Tesla T4            On   | 000029CE:00:00.0 Off |                    0 |    
    | N/A   60C    P0    29W /  70W |   1539MiB / 15109MiB |      0%      Default |    
    +-------------------------------+----------------------+----------------------+    
    |   1  Tesla T4           On  | 0000AD50:00:00.0 Off |                    0 |
    | N/A   58C    P0    29W /  70W |    330MiB / 15109MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    ```

## <a name="enable-multi-process-service-mps"></a>Povolit službu Multi-Process Service (MPS)

Služba multi-Process (MPS) na NVIDIA GPU poskytuje mechanismus, ve kterém může být prostředí GPU sdíleno více úlohami, kde každá úloha má přiděleno určité procento prostředků GPU. Sada MPS je funkcí Preview na zařízení GPU Azure Stack Edge pro. Pokud chcete na svém zařízení povolit MPS, postupujte podle těchto kroků:

1. Než začnete, ujistěte se, že: 

    1. Nakonfigurovali a [aktivovali jste zařízení Azure Stack Edge pro](azure-stack-edge-gpu-deploy-activate.md) v Azure pomocí prostředku služby Azure Stack Edge pro/data box Gateway.
    1. [Na tomto zařízení jste nakonfigurovali COMPUTE na Azure Portal](azure-stack-edge-deploy-configure-compute.md#configure-compute).
    
1. [Připojte se k rozhraní PowerShell](#connect-to-the-powershell-interface).
1. Pomocí následujícího příkazu povolte na svém zařízení MPS.

    ```powershell
    Start-HcsGpuMPS
    ```

## <a name="reset-your-device"></a>Resetování zařízení

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Získat výpočetní protokoly

Pokud je v zařízení nakonfigurovaná výpočetní role, můžete získat výpočetní protokoly i přes rozhraní PowerShell.

1. [Připojte se k rozhraní PowerShell](#connect-to-the-powershell-interface).
2. Použijte `Get-AzureDataBoxEdgeComputeRoleLogs` k získání výpočetních protokolů pro vaše zařízení.

    Následující příklad ukazuje použití této rutiny:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection    
    ```

    Tady je popis parametrů použitých pro rutinu:
    - `Path`: Zadejte síťovou cestu ke sdílené složce, ve které chcete vytvořit balíček výpočetního protokolu.
    - `Credential`: Zadejte uživatelské jméno pro sdílenou síťovou složku. Při spuštění této rutiny budete muset zadat heslo pro sdílení.
    - `FullLogCollection`: Tento parametr zajišťuje, že balíček protokolu bude obsahovat všechny protokoly výpočtů. Ve výchozím nastavení obsahuje balíček protokolu pouze podmnožinu protokolů.


## <a name="change-kubernetes-pod-and-service-subnets"></a>Změna Kubernetes pod a podsítí služby

Ve výchozím nastavení Kubernetes Azure Stack na zařízení Edge používá podsítě 172.27.0.0/16 a 172.28.0.0/16 pro pod a službu. Pokud se tyto podsítě už ve vaší síti používají, můžete spuštěním `Set-HcsKubeClusterNetworkInfo` rutiny tyto podsítě změnit.

Tuto konfiguraci chcete provést před konfigurací výpočtů z Azure Portal, protože se v tomto kroku vytvoří cluster Kubernetes.

1. [Připojte se k rozhraní PowerShell zařízení](#connect-to-the-powershell-interface).
1. Z rozhraní PowerShellu zařízení spusťte:

    `Set-HcsKubeClusterNetworkInfo -PodSubnet <subnet details> -ServiceSubnet <subnet details>`

    Nahraďte <subnet details> rozsahem podsítě, který chcete použít. 

1. Po spuštění tohoto příkazu můžete pomocí `Get-HcsKubeClusterNetworkInfo` příkazu ověřit, zda byly podsítě pod a služby změněny.

Zde je ukázkový výstup tohoto příkazu.

```powershell
[10.100.10.10]: PS>Set-HcsKubeClusterNetworkInfo -PodSubnet 10.96.0.1/16 -ServiceSubnet 10.97.0.1/16
[10.100.10.10]: PS>Get-HcsKubeClusterNetworkInfo

Id                                   PodSubnet    ServiceSubnet
--                                   ---------    -------------
6dbf23c3-f146-4d57-bdfc-76cad714cfd1 10.96.0.1/16 10.97.0.1/16
[10.100.10.10]: PS>
```


## <a name="debug-kubernetes-issues-related-to-iot-edge"></a>Ladění problémů s Kubernetes souvisejícími s IoT Edge

<!--When the Kubernetes cluster is created, there are two system namespaces created: `iotedge` and `azure-arc`. --> 

<!--### Create config file for system namespace

To troubleshoot, first create the `config` file corresponding to the `iotedge` namespace with `aseuser`.

Run the `Get-HcsKubernetesUserConfig -AseUser` command and save the output as `config` file (no file extension). Save the file in the `.kube` folder of your user profile on the local machine.

Following is the sample output of the `Get-HcsKubernetesUserConfig` command.

```PowerShell
[10.100.10.10]: PS>Get-HcsKubernetesUserConfig -AseUser
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01EVXhNekl4TkRRME5sb1hEVE13TURVeE1USXhORFEwTmxvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBS0M1CjlJbzRSU2hudG90QUdxdjNTYmRjOVd4UmJDYlRzWXU5S0RQeU9xanVoZE1UUE9PcmROOGNoa0x4NEFyZkZaU1AKZithUmhpdWZqSE56bWhucnkvZlprRGdqQzQzRmV5UHZzcTZXeVVDV0FEK2JBdi9wSkJDbkg2MldoWGNLZ1BVMApqU1k0ZkpXenNFbzBaREhoeUszSGN3MkxkbmdmaEpEanBQRFJBNkRWb2pIaktPb29OT1J1dURvUHpiOTg2dGhUCkZaQXJMZjRvZXRzTEk1ZzFYRTNzZzM1YVhyU0g3N2JPYVVsTGpYTzFYSnpFZlZWZ3BMWE5xR1ZqTXhBMVU2b1MKMXVJL0d1K1ArY
===========CUT=========================================CUT===================
    server: https://compute.myasegpu1.wdshcsso.com:6443
    name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: aseuser
    name: aseuser@kubernetes
current-context: aseuser@kubernetes
kind: Config
preferences: {}
users:
- name: aseuser
    user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwRENDQWJpZ0F3SUJBZ0lJY1hOTXRPU2VwbG93RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURBMU1UTXlNVFEwTkRaYUZ3MHlNVEExTVRNeU1UVXhNVEphTUJJeApFREFPQmdOVkJBTVRCMkZ6WlhWelpYSXdnZ0VpTUEwR0NTcUdTSWIzRFFFQkFRVUFBNElCRHdBd2dnRUtBb0lCCkFRRHVjQ1pKdm9qNFIrc0U3a1EyYmVjNEJkTXdpUEhmU2R2WnNDVVY0aTRRZGY1Yzd0dkE3OVRSZkRLQTY1d08Kd0h0QWdlK3lLK0hIQ1Qyd09RbWtNek1RNjZwVFEzUlE0eVdtRDZHR1cWZWMExBR1hFUUxWWHRuTUdGCi0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==

[10.100.10.10]: PS>
```
-->

Na zařízení Azure Stack Edge pro, ve kterém je nakonfigurovaná výpočetní role, můžete řešit nebo monitorovat zařízení pomocí dvou různých sad příkazů.

- Použití `iotedge` příkazů. Tyto příkazy jsou k dispozici pro základní operace vašeho zařízení.
- Použití `kubectl` příkazů. Tyto příkazy jsou k dispozici pro rozsáhlou sadu operací pro vaše zařízení.

Pokud chcete spustit některou z výše uvedených příkazů, musíte se [připojit k rozhraní PowerShell](#connect-to-the-powershell-interface).

### <a name="use-iotedge-commands"></a>Použití `iotedge` příkazů

Pokud chcete zobrazit seznam dostupných příkazů, [Připojte se k rozhraní PowerShell](#connect-to-the-powershell-interface) a použijte `iotedge` funkci.

```powershell
[10.100.10.10]: PS>iotedge -?                                                                                                                           
Usage: iotedge COMMAND

Commands:
   list
   logs
   restart

[10.100.10.10]: PS>
```

Následující tabulka obsahuje stručný popis příkazů, které jsou k dispozici pro `iotedge` :

|command  |Popis |
|---------|---------|
|`list`     | Seznam modulů         |
|`logs`     | Načtení protokolů modulu        |
|`restart`     | Zastavení a restartování modulu         |


Pokud chcete zobrazit seznam všech modulů spuštěných v zařízení, použijte `iotedge list` příkaz.

Zde je ukázkový výstup tohoto příkazu. Tento příkaz vypíše všechny moduly, přidruženou konfiguraci a externí IP adresy přidružené k modulům. Můžete například získat přístup k aplikaci **webserver** na adrese `https://10.128.44.244` . 


```powershell
[10.100.10.10]: PS>iotedge list

NAME                   STATUS  DESCRIPTION CONFIG                                             EXTERNAL-IP
----                   ------  ----------- ------                                             -----
gettingstartedwithgpus Running Up 10 days  mcr.microsoft.com/intelligentedge/solutions:latest
iotedged               Running Up 10 days  azureiotedge/azureiotedge-iotedged:0.1.0-beta10    <none>
edgehub                Running Up 10 days  mcr.microsoft.com/azureiotedge-hub:1.0             10.128.44.243
edgeagent              Running Up 10 days  azureiotedge/azureiotedge-agent:0.1.0-beta10
webserverapp           Running Up 10 days  nginx:stable                                       10.128.44.244

[10.100.10.10]: PS>
```


### <a name="use-kubectl-commands"></a>Použití příkazů kubectl

Na zařízení Azure Stack Edge pro, ve kterém je nakonfigurovaná role COMPUTE, `kubectl` jsou k dispozici všechny příkazy pro monitorování a odstraňování potíží s moduly. Pokud chcete zobrazit seznam dostupných příkazů, spusťte `kubectl --help` z příkazového řádku.

```PowerShell
C:\Users\myuser>kubectl --help

kubectl controls the Kubernetes cluster manager.

Find more information at: https://kubernetes.io/docs/reference/kubectl/overview/

Basic Commands (Beginner):
    create         Create a resource from a file or from stdin.
    expose         Take a replication controller, service, deployment or pod and expose it as a new Kubernetes Service
    run            Run a particular image on the cluster
    set            Set specific features on objects
    run-container  Run a particular image on the cluster. This command is deprecated, use "run" instead
==============CUT=============CUT============CUT========================

Usage:
    kubectl [flags] [options]

Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).

C:\Users\myuser>
```

Úplný seznam `kubectl` příkazů najdete na [ `kubectl` k vytištění](https://kubernetes.io/docs/reference/kubectl/cheatsheet/).


#### <a name="to-get-ip-of-service-or-module-exposed-outside-of-kubernetes-cluster"></a>Získání IP adresy služby nebo modulu vystaveného mimo cluster Kubernetes

Pokud chcete získat IP adresu služby Vyrovnávání zatížení nebo modulů vystavených mimo Kubernetes, spusťte následující příkaz:

`kubectl get svc -n iotedge`

Následuje ukázkový výstup všech služeb nebo modulů, které jsou vystaveny mimo cluster Kubernetes. 


```powershell
[10.100.10.10]: PS>kubectl get svc -n iotedge
NAME           TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                                       AGE
edgehub        LoadBalancer   10.103.52.225   10.128.44.243   443:31987/TCP,5671:32336/TCP,8883:30618/TCP   34h
iotedged       ClusterIP      10.107.236.20   <none>          35000/TCP,35001/TCP                           3d8h
webserverapp   LoadBalancer   10.105.186.35   10.128.44.244   8080:30976/TCP                                16h

[10.100.10.10]: PS>
```
IP adresa ve sloupci externí IP adresa odpovídá externímu koncovému bodu pro službu nebo modul. [Externí IP adresu můžete získat i na řídicím panelu Kubernetes](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules).


#### <a name="to-check-if-module-deployed-successfully"></a>Postup kontroly úspěšného nasazení modulu

Výpočetní moduly jsou kontejnery, které mají implementaci obchodní logiky. Kubernetes pod může mít spuštěno více kontejnerů. 

Pokud chcete zjistit, jestli je výpočetní modul úspěšně nasazený, připojte se k rozhraní PowerShell zařízení.
Spusťte `get pods` příkaz a ověřte, jestli je spuštěný kontejner (odpovídající modulu COMPUTE).

Chcete-li získat seznam všech lusků spuštěných v určitém oboru názvů, spusťte následující příkaz:

`get pods -n <namespace>`

Chcete-li kontrolovat moduly nasazené pomocí IoT Edge, spusťte následující příkaz:

`get pods -n iotedge`

Následuje ukázkový výstup všech lusků spuštěných v `iotedge` oboru názvů.

```
[10.100.10.10]: PS>kubectl get pods -n iotedge
NAME                        READY   STATUS    RESTARTS   AGE
edgeagent-cf6d4ffd4-q5l2k   2/2     Running   0          20h
edgehub-8c9dc8788-2mvwv     2/2     Running   0          56m
filemove-66c49984b7-h8lxc   2/2     Running   0          56m
iotedged-675d7f4b5f-9nml4   1/1     Running   0          20h

[10.100.10.10]: PS>
```

**Stav stav označuje,** že všechny lusky v oboru názvů jsou spuštěné a kde **Ready** označuje počet kontejnerů nasazených v poli pod. V předchozí ukázce jsou spuštěny všechny lusky a jsou spuštěny všechny moduly nasazené v každém z obou lusků. 

Pokud chcete kontrolovat moduly nasazené přes Azure ARC, spusťte následující příkaz:

`get pods -n azure-arc`

Alternativně se můžete [připojit k řídicímu panelu Kubernetes a zobrazit IoT Edge nebo nasazení ARC Azure](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#view-module-status).


Podrobnější výstup konkrétního pole pod pro daný obor názvů můžete spustit pomocí následujícího příkazu:

`kubectl describe pod <pod name> -n <namespace>` 

Ukázkový výstup se zobrazí zde.

```
[10.100.10.10]: PS>kubectl describe pod filemove-66c49984b7 -n iotedge
Name:           filemove-66c49984b7-h8lxc
Namespace:      iotedge
Priority:       0
Node:           k8s-1hwf613cl-1hwf613/10.139.218.12
Start Time:     Thu, 14 May 2020 12:46:28 -0700
Labels:         net.azure-devices.edge.deviceid=myasegpu-edge
                net.azure-devices.edge.hub=myasegpu2iothub.azure-devices.net
                net.azure-devices.edge.module=filemove
                pod-template-hash=66c49984b7
Annotations:    net.azure-devices.edge.original-moduleid: filemove
Status:         Running
IP:             172.17.75.81
IPs:            <none>
Controlled By:  ReplicaSet/filemove-66c49984b7
Containers:
    proxy:
    Container ID:   docker://fd7975ca78209a633a1f314631042a0892a833b7e942db2e7708b41f03e8daaf
    Image:          azureiotedge/azureiotedge-proxy:0.1.0-beta8
    Image ID:       docker://sha256:5efbf6238f13d24bab9a2b499e5e05bc0c33ab1587d6cf6f289cdbe7aa667563
    Port:           <none>
    Host Port:      <none>
    State:          Running
        Started:      Thu, 14 May 2020 12:46:30 -0700
    Ready:          True
    Restart Count:  0
    Environment:
        PROXY_LOG:  Debug
=============CUT===============================CUT===========================
Volumes:
    config-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-config
    Optional:  false
    trust-bundle-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-trust-bundle
    Optional:  false
    myasesmb1local:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1local
    ReadOnly:   false
    myasesmb1:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1
    ReadOnly:   false
    filemove-token-pzvw8:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  filemove-token-pzvw8
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                    node.kubernetes.io/unreachable:NoExecute for 300s
Events:          <none>


[10.100.10.10]: PS>
```

#### <a name="to-get-container-logs"></a>Získání protokolů kontejnerů

Pokud chcete získat protokoly pro modul, spusťte následující příkaz z rozhraní PowerShell zařízení:

`kubectl logs <pod_name> -n <namespace> --all-containers` 

Vzhledem k tomu `all-containers` , že příznak vypíše všechny protokoly pro všechny kontejnery, dobrým způsobem, jak zobrazit nedávné chyby, je použít možnost `--tail 10` .

Následuje ukázkový výstup. 

```
[10.100.10.10]: PS>kubectl logs filemove-66c49984b7-h8lxc -n iotedge --all-containers --tail 10
DEBUG 2020-05-14T20:40:42Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:12Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
05/14/2020 19:46:44: Info: Opening module client connection.
05/14/2020 19:46:45: Info: Open done.
05/14/2020 19:46:45: Info: Initializing with input: /home/input, output: /home/output, protocol: Amqp.
05/14/2020 19:46:45: Info: IoT Hub module client initialized.

[10.100.10.10]: PS>
```

### <a name="change-memory-processor-limits-for-kubernetes-worker-node"></a>Změna paměti, omezení procesoru pro pracovní uzel Kubernetes

Chcete-li změnit omezení paměti nebo procesoru pro pracovní uzel Kubernetes, proveďte následující kroky:

1. [Připojte se k rozhraní PowerShell zařízení](#connect-to-the-powershell-interface).
1. Aktuální prostředky pro pracovní uzel a možnosti role získáte spuštěním následujícího příkazu:

    `Get-AzureDataBoxEdgeRole`

    Zde je ukázkový výstup. Všimněte si hodnot pro `Name` a `Compute` v `Resources` části. `MemoryInBytes` a `ProcessorCount` Všimněte si aktuálně přiřazených hodnot paměti a počtu procesorů pro pracovní uzel Kubernetes.  

    ```powershell
    [10.100.10.10]: PS>Get-AzureDataBoxEdgeRole
    ImageDetail                : Name:mcr.microsoft.com/azureiotedge-agent
                                 Tag:1.0
                                 PlatformType:Linux
    EdgeDeviceConnectionString :
    IotDeviceConnectionString  :
    HubHostName                : ase-srp-007.azure-devices.net
    IotDeviceId                : srp-007-storagegateway
    EdgeDeviceId               : srp-007-edge
    Version                    :
    Id                         : 6ebeff9f-84c5-49a7-890c-f5e05520a506
    Name                       : IotRole
    Type                       : IOT
    Resources                  : Compute:
                                 MemoryInBytes:34359738368
                                 ProcessorCount:12
                                 VMProfile:
    
                                 Storage:
                                 EndpointMap:
                                 EndpointId:c0721210-23c2-4d16-bca6-c80e171a0781
                                 TargetPath:mysmbedgecloudshare1
                                 Name:mysmbedgecloudshare1
                                 Protocol:SMB
    
                                 EndpointId:6557c3b6-d3c5-4f94-aaa0-6b7313ab5c74
                                 TargetPath:mysmbedgelocalshare
                                 Name:mysmbedgelocalshare
                                 Protocol:SMB
                                 RootFileSystemStorageSizeInBytes:0
    
    HostPlatform               : KubernetesCluster
    State                      : Created
    PlatformType               : Linux
    HostPlatformInstanceId     : 994632cb-853e-41c5-a9cd-05b36ddbb190
    IsHostPlatformOwner        : True
    IsCreated                  : True    
    [10.100.10.10]: PS>
    ```
    
1. Chcete-li změnit hodnoty paměti a procesorů pro pracovní uzel, spusťte následující příkaz:

    Set-AzureDataBoxEdgeRoleCompute-name <Name value from the output of Get-AzureDataBoxEdgeRole> -paměť <Value in Bytes> – ProcessorCount <ne. jader>

    Zde je ukázkový výstup. 
    
    ```powershell
    [10.100.10.10]: PS>Set-AzureDataBoxEdgeRoleCompute -Name IotRole -MemoryInBytes 32GB -ProcessorCount 16
    
    ImageDetail                : Name:mcr.microsoft.com/azureiotedge-agent
                                 Tag:1.0
                                 PlatformType:Linux
    
    EdgeDeviceConnectionString :
    IotDeviceConnectionString  :
    HubHostName                : ase-srp-007.azure-devices.net
    IotDeviceId                : srp-007-storagegateway
    EdgeDeviceId               : srp-007-edge
    Version                    :
    Id                         : 6ebeff9f-84c5-49a7-890c-f5e05520a506
    Name                       : IotRole
    Type                       : IOT
    Resources                  : Compute:
                                 MemoryInBytes:34359738368
                                 ProcessorCount:16
                                 VMProfile:
    
                                 Storage:
                                 EndpointMap:
                                 EndpointId:c0721210-23c2-4d16-bca6-c80e171a0781
                                 TargetPath:mysmbedgecloudshare1
                                 Name:mysmbedgecloudshare1
                                 Protocol:SMB
    
                                 EndpointId:6557c3b6-d3c5-4f94-aaa0-6b7313ab5c74
                                 TargetPath:mysmbedgelocalshare
                                 Name:mysmbedgelocalshare
                                 Protocol:SMB
    
                                 RootFileSystemStorageSizeInBytes:0
    
    HostPlatform               : KubernetesCluster
    State                      : Created
    PlatformType               : Linux
    HostPlatformInstanceId     : 994632cb-853e-41c5-a9cd-05b36ddbb190
    IsHostPlatformOwner        : True
    IsCreated                  : True
    
    [10.100.10.10]: PS>    
    ```

Při změně využití paměti a procesoru postupujte podle těchto pokynů.

- Výchozí paměť je 25% specifikace zařízení.
- Výchozí počet procesorů je 30% specifikace zařízení.
- Při změně hodnot paměti a počtu procesorů doporučujeme změnit hodnoty mezi 15 až 65% paměti zařízení a počtem procesorů. 
- Horní limit 65% doporučujeme, aby bylo k dispozici dostatek prostředků pro systémové součásti. 

## <a name="connect-to-bmc"></a>Připojení k řadiči pro správu základní desky

Řadič pro správu základní desky (BMC) se používá ke vzdálenému monitorování a správě zařízení. Tato část popisuje rutiny, které se dají použít ke správě konfigurace řadiče pro správu základní desky. Před spuštěním kterékoli z těchto rutin se [připojte k rozhraní PowerShell zařízení](#connect-to-the-powershell-interface).

- `Get-HcsNetBmcInterface`: Pomocí této rutiny můžete získat vlastnosti konfigurace sítě řadiče pro správu základní desky (BMC), například,, `IPv4Address` `IPv4Gateway` `IPv4SubnetMask` , `DhcpEnabled` . 
    
    Zde je ukázkový výstup:
    
    ```powershell
    [10.100.10.10]: PS>Get-HcsNetBmcInterface
    IPv4Address   IPv4Gateway IPv4SubnetMask DhcpEnabled
    -----------   ----------- -------------- -----------
    10.128.53.186 10.128.52.1 255.255.252.0        False
    [10.100.10.10]: PS>
    ```
- `Set-HcsNetBmcInterface`: Tuto rutinu můžete použít následujícími dvěma způsoby.

    - Pomocí rutiny povolte nebo zakažte konfiguraci DHCP pro řadič pro správu základní desky pomocí příslušné hodnoty `UseDhcp` parametru. 

        ```powershell
        Set-HcsNetBmcInterface -UseDhcp $true
        ```

        Zde je ukázkový výstup: 

        ```powershell
        [10.100.10.10]: PS>Set-HcsNetBmcInterface -UseDhcp $true
        [10.100.10.10]: PS>Get-HcsNetBmcInterface
        IPv4Address IPv4Gateway IPv4SubnetMask DhcpEnabled
        ----------- ----------- -------------- -----------
        10.128.54.8 10.128.52.1 255.255.252.0         True
        [10.100.10.10]: PS>
        ```

    - Pomocí této rutiny můžete nakonfigurovat statickou konfiguraci pro řadič pro správu základní desky. Můžete zadat hodnoty pro `IPv4Address` , `IPv4Gateway` a `IPv4SubnetMask` . 
    
        ```powershell
        Set-HcsNetBmcInterface -IPv4Address "<IPv4 address of the device>" -IPv4Gateway "<IPv4 address of the gateway>" -IPv4SubnetMask "<IPv4 address for the subnet mask>"
        ```        
        
        Zde je ukázkový výstup: 

        ```powershell
        [10.100.10.10]: PS>Set-HcsNetBmcInterface -IPv4Address 10.128.53.186 -IPv4Gateway 10.128.52.1 -IPv4SubnetMask 255.255.252.0
        [10.100.10.10]: PS>Get-HcsNetBmcInterface
        IPv4Address   IPv4Gateway IPv4SubnetMask DhcpEnabled
        -----------   ----------- -------------- -----------
        10.128.53.186 10.128.52.1 255.255.252.0        False
        [10.100.10.10]: PS>
        ```    

- `Set-HcsBmcPassword`: Pomocí této rutiny můžete změnit heslo řadiče pro správu základní desky pro `EdgeUser` . Uživatelské jméno – rozlišuje `EdgeUser` velká a malá písmena.

    Zde je ukázkový výstup: 

    ```powershell
    [10.100.10.10]: PS> Set-HcsBmcPassword -NewPassword "Password1"
    [10.100.10.10]: PS>
    ```

## <a name="exit-the-remote-session"></a>Ukončit vzdálenou relaci

Pokud chcete ukončit vzdálenou relaci PowerShellu, zavřete okno PowerShell.

## <a name="next-steps"></a>Další kroky

- Nasaďte [Azure Stack Edge pro](azure-stack-edge-gpu-deploy-prep.md) v Azure Portal.