---
title: Připojení a Správa Microsoft Azure Stack hraničního zařízení přes rozhraní Windows PowerShellu | Microsoft Docs
description: Popisuje, jak se připojit k Azure Stack Edge přes rozhraní Windows PowerShell a potom ho spravovat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 26d4476217d5a6f6abea6093f453cd52d654911e
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2020
ms.locfileid: "89084512"
---
# <a name="manage-an-azure-stack-edge-gpu-device-via-windows-powershell"></a>Správa zařízení GPU Azure Stackového grafického okraje prostřednictvím prostředí Windows PowerShell

Řešení Azure Stack Edge umožňuje zpracovávat data a odesílat je přes síť do Azure. Tento článek popisuje některé úlohy konfigurace a správy pro zařízení Azure Stack Edge. Ke správě zařízení můžete použít rozhraní Azure Portal, místní webové uživatelské rozhraní nebo prostředí Windows PowerShell.

Tento článek se zaměřuje na to, jak se můžete připojit k rozhraní PowerShellu zařízení a úlohám, které můžete používat v tomto rozhraní. 


## <a name="connect-to-the-powershell-interface"></a>Připojení k rozhraní PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Vytvoření balíčku pro podporu

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Nahrání certifikátu

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

Můžete také nahrát IoT Edge certifikátů a povolit tak zabezpečené připojení mezi zařízením IoT Edge a zařízeními pro příjem dat, která se k němu mohou připojit. Existují tři certifikáty IoT Edge (formát *. pem* ), které je třeba nainstalovat:

- Certifikát kořenové certifikační autority nebo certifikační autorita vlastníka
- Certifikát certifikační autority zařízení
- Certifikát klíče zařízení

Následující příklad ukazuje použití této rutiny k instalaci IoT Edgech certifikátů:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
Při spuštění této rutiny se zobrazí výzva, abyste zadali heslo pro sdílenou síťovou složku.

Další informace o certifikátech najdete v [Azure IoT Edge certifikáty](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) nebo [Instalace certifikátů na bránu](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway).

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

Služba multi-Process (MPS) na NVIDIA GPU poskytuje mechanismus, ve kterém může být prostředí GPU sdíleno více úlohami, kde každá úloha má přiděleno určité procento prostředků GPU. Pokud chcete na zařízení Azure Stack Edge povolit MPS, postupujte podle těchto kroků:

1. Než začnete, ujistěte se, že: 

    1. Nakonfigurovali a [aktivovali jste Azure Stack hraniční zařízení](azure-stack-edge-gpu-deploy-activate.md) s Azure Stackm hraničním nebo data box Gatewaym prostředkem v Azure.
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



## <a name="debug-kubernetes-issues-related-to-iot-edge"></a>Ladění problémů s Kubernetes souvisejícími s IoT Edge

Když se vytvoří cluster Kubernetes, vytvoří `aseuser` se taky výchozí uživatel přidružený k oboru názvů System `iotedge` . Chcete-li ladit všechny problémy související s IoT Edge, můžete použít tento uživatelský a systémový obor názvů.  

### <a name="create-config-file-for-system-namespace"></a>Vytvořit konfigurační soubor pro obor názvů System

Chcete-li vyřešit potíže, nejprve vytvořte `config` soubor odpovídající `iotedge` oboru názvů pomocí `aseuser` .

Spusťte `Get-HcsKubernetesUserConfig -AseUser` příkaz a uložte výstup jako `config` soubor (bez přípony souboru). Uložte soubor do `.kube` složky vašeho uživatelského profilu na místním počítači.

Následuje ukázkový výstup `Get-HcsKubernetesUserConfig` příkazu.

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

Na Azure Stack hraničním zařízení s nakonfigurovanou výpočetní rolí `kubectl` jsou všechny příkazy k dispozici pro monitorování a odstraňování potíží s moduly. Pokud chcete zobrazit seznam dostupných příkazů, spusťte `kubectl --help` z příkazového řádku.

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

### <a name="to-get-ip-of-service-or-module-exposed-outside-of-kubernetes-cluster"></a>Získání IP adresy služby nebo modulu vystaveného mimo cluster Kubernetes

Pokud chcete získat IP adresu služby Vyrovnávání zatížení nebo modulů vystavených mimo Kubernetes, spusťte následující příkaz:

`kubectl get svc -n iotedge`

Následuje ukázkový výstup všech služeb nebo modulů, které jsou vystaveny mimo cluster Kubernetes. 


```powershell
C:\Users\user>kubectl get svc -n iotedge
NAME           TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                                       AGE
edgehub        LoadBalancer   10.103.52.225   10.128.44.243   443:31987/TCP,5671:32336/TCP,8883:30618/TCP   34h
iotedged       ClusterIP      10.107.236.20   <none>          35000/TCP,35001/TCP                           3d8h
webserverapp   LoadBalancer   10.105.186.35   10.128.44.244   8080:30976/TCP                                16h

C:\Users\user>
```
IP adresa ve sloupci externí IP adresa odpovídá externímu koncovému bodu pro službu nebo modul. [Externí IP adresu můžete získat i na řídicím panelu Kubernetes](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules).


### <a name="to-check-if-module-deployed-successfully"></a>Postup kontroly úspěšného nasazení modulu

Výpočetní moduly jsou kontejnery, které mají implementaci obchodní logiky. Kubernetes pod může mít spuštěno více kontejnerů. Pokud chcete zjistit, jestli je výpočetní modul úspěšně nasazený, spusťte `get pods` příkaz a ověřte, jestli je spuštěný kontejner (odpovídající modulu COMPUTE).

Chcete-li získat seznam všech lusků spuštěných v určitém oboru názvů, spusťte následující příkaz:

`get pods -n <namespace>`

Následuje ukázkový výstup všech lusků spuštěných v `iotedge` oboru názvů.

```
C:\Users\myuser>kubectl get pods -n iotedge
NAME                        READY   STATUS    RESTARTS   AGE
edgeagent-cf6d4ffd4-q5l2k   2/2     Running   0          20h
edgehub-8c9dc8788-2mvwv     2/2     Running   0          56m
filemove-66c49984b7-h8lxc   2/2     Running   0          56m
iotedged-675d7f4b5f-9nml4   1/1     Running   0          20h

C:\Users\myuser>
```

**Stav stav označuje,** že všechny lusky v oboru názvů jsou spuštěné a kde **Ready** označuje počet kontejnerů nasazených v poli pod. V předchozí ukázce jsou spuštěny všechny lusky a jsou spuštěny všechny moduly nasazené v každém z obou lusků. 

Podrobnější výstup konkrétního pole pod pro daný obor názvů můžete spustit pomocí následujícího příkazu:

`kubectl describe pod <pod name> -n <namespace>` 

Ukázkový výstup se zobrazí zde.

```
C:\Users\myuser>kubectl describe pod filemove-66c49984b7 -n iotedge
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


C:\Users\myuser>
```

### <a name="to-get-container-logs"></a>Získání protokolů kontejnerů

Chcete-li získat protokoly pro modul, spusťte následující příkaz:

`kubectl logs <pod_name> -n <namespace> --all-containers` 

Vzhledem k tomu `all-containers` , že příznak vypíše všechny protokoly pro všechny kontejnery, dobrým způsobem, jak zobrazit nedávné chyby, je použít možnost `--tail 10` .

Následuje ukázkový výstup. 

```
C:\Users\myuser>kubectl logs filemove-66c49984b7-h8lxc -n iotedge --all-containers --tail 10
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

C:\Users\myuser>
```

## <a name="exit-the-remote-session"></a>Ukončit vzdálenou relaci

Pokud chcete ukončit vzdálenou relaci PowerShellu, zavřete okno PowerShell.

## <a name="next-steps"></a>Další kroky

- Nasaďte [Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md) do Azure Portal.
