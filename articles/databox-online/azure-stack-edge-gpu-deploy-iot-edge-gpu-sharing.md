---
title: Nasazení IoT Edge úlohy pomocí sdílení GPU na zařízení GPU Azure Stack Edge pro
description: Popisuje, jak můžete nasadit sdílenou úlohu GPU přes IoT Edge na zařízení GPU Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: b52d1e834772a2a6e0e000b3df15d8aa0fa866a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103564955"
---
# <a name="deploy-an-iot-edge-workload-using-gpu-sharing-on-your-azure-stack-edge-pro"></a>Nasazení úlohy IoT Edge pomocí sdílení GPU na Azure Stack Edge pro

Tento článek popisuje, jak mohou kontejnery v kontejneru sdílet GPU na zařízení GPU Azure Stack Edge pro. Tento přístup zahrnuje povolení služby Multi-Process (MPS) a následné určení úloh GPU pomocí IoT Edge nasazení. 

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

1. Máte přístup k [aktivovanému](azure-stack-edge-gpu-deploy-activate.md) zařízení GPU Azure Stack Edge pro, které je aktivované a má [nakonfigurované výpočty](azure-stack-edge-gpu-deploy-configure-compute.md). Máte [koncový bod rozhraní Kubernetes API](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints) a Přidali jste tento koncový bod do `hosts` souboru v klientovi, který bude přistupovat k zařízení.

1. Máte přístup k klientskému systému s [podporovaným operačním systémem](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device). Při použití klienta systému Windows by měl systém při přístupu k zařízení spustit PowerShell 5,0 nebo novější.

1. Uložte následující nasazení `json` do místního systému. Informace z tohoto souboru použijete ke spuštění nasazení IoT Edge. Toto nasazení vychází z [jednoduchých CUDA kontejnerů](https://docs.nvidia.com/cuda/wsl-user-guide/index.html#running-simple-containers) , které jsou veřejně dostupné z NVIDIA. 

    ```json
    {
        "modulesContent": {
            "$edgeAgent": {
                "properties.desired": {
                    "modules": {
                        "cuda-sample1": {
                            "settings": {
                                "image": "nvidia/samples:nbody",
                                "createOptions": "{\"Entrypoint\":[\"/bin/sh\"],\"Cmd\":[\"-c\",\"/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done\"],\"HostConfig\":{\"IpcMode\":\"host\",\"PidMode\":\"host\"}}"
                            },
                            "type": "docker",
                            "version": "1.0",
                            "env": {
                                "NVIDIA_VISIBLE_DEVICES": {
                                    "value": "0"
                                }
                            },
                            "status": "running",
                            "restartPolicy": "never"
                        },
                        "cuda-sample2": {
                            "settings": {
                                "image": "nvidia/samples:nbody",
                                "createOptions": "{\"Entrypoint\":[\"/bin/sh\"],\"Cmd\":[\"-c\",\"/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done\"],\"HostConfig\":{\"IpcMode\":\"host\",\"PidMode\":\"host\"}}"
                            },
                            "type": "docker",
                            "version": "1.0",
                            "env": {
                                "NVIDIA_VISIBLE_DEVICES": {
                                    "value": "0"
                                }
                            },
                            "status": "running",
                            "restartPolicy": "never"
                        }
                    },
                    "runtime": {
                        "settings": {
                            "minDockerVersion": "v1.25"
                        },
                        "type": "docker"
                    },
                    "schemaVersion": "1.1",
                    "systemModules": {
                        "edgeAgent": {
                            "settings": {
                                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                                "createOptions": ""
                            },
                            "type": "docker"
                        },
                        "edgeHub": {
                            "settings": {
                                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                            },
                            "type": "docker",
                            "status": "running",
                            "restartPolicy": "always"
                        }
                    }
                }
            },
            "$edgeHub": {
                "properties.desired": {
                    "routes": {
                        "route": "FROM /messages/* INTO $upstream"
                    },
                    "schemaVersion": "1.1",
                    "storeAndForwardConfiguration": {
                        "timeToLiveSecs": 7200
                    }
                }
            },
            "cuda-sample1": {
                "properties.desired": {}
            },
            "cuda-sample2": {
                "properties.desired": {}
            }
        }
    }
    ```

## <a name="verify-gpu-driver-cuda-version"></a>Ověření ovladače GPU, verze CUDA

Prvním krokem je ověření, že na zařízení běží požadovaný ovladač GPU a verze CUDA.

1. [Připojte se k rozhraní PowerShell vašeho zařízení](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

1. Spusťte následující příkaz:

    `Get-HcsGpuNvidiaSmi`

1. Ve výstupu NVIDIA SMI si poznamenejte verzi GPU a verzi CUDA na vašem zařízení. Pokud používáte software Azure Stack Edge 2102, bude tato verze odpovídat následujícím verzím ovladačů:

    - Verze ovladače GPU: 460.32.03
    - Verze CUDA: 11,2
    
    Tady je příklad výstupu:

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Tue Feb 23 10:34:01 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 0000041F:00:00.0 Off |                    0 |
    | N/A   40C    P8    15W /  70W |      0MiB / 15109MiB |      0%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>  
    ```

1. Tuto relaci nechte otevřenou, protože ji použijete k zobrazení výstupu NVIDIA SMI v celém článku.


## <a name="deploy-without-context-sharing"></a>Nasazení bez kontextu sdílení

Na zařízení teď můžete nasadit aplikaci, pokud není spuštěná víceklientská služba a neexistuje žádné sdílení kontextu. Nasazení probíhá prostřednictvím Azure Portal v `iotedge` oboru názvů, který se nachází na vašem zařízení.

### <a name="create-user-in-iot-edge-namespace"></a>Vytvořit uživatele v IoT Edgem oboru názvů

Nejprve vytvoříte uživatele, který se připojí k `iotedge` oboru názvů. Moduly IoT Edge jsou nasazeny v oboru názvů iotedge. Další informace najdete v tématu [obory názvů Kubernetes na vašem zařízení](azure-stack-edge-gpu-kubernetes-rbac.md#namespaces-types).

Pomocí těchto kroků můžete vytvořit uživatele a udělit uživateli přístup k `iotedge` oboru názvů. 

1. [Připojte se k rozhraní PowerShell vašeho zařízení](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

1. Vytvoří nového uživatele v `iotedge` oboru názvů. Spusťte následující příkaz:

    `New-HcsKubernetesUser -UserName <user name>`

    Tady je příklad výstupu:

    ```powershell
    [10.100.10.10]: PS>New-HcsKubernetesUser -UserName iotedgeuser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: 
    ===========================//snipped //======================// snipped //=============================
        server: https://compute.myasegpudev.wdshcsso.com:6443
      name: kubernetes
    contexts:
    - context:
        cluster: kubernetes
        user: iotedgeuser
      name: iotedgeuser@kubernetes
    current-context: iotedgeuser@kubernetes
    kind: Config
    preferences: {}
    users:
    - name: iotedgeuser
      user:
        client-certificate-data: 
    ===========================//snipped //======================// snipped //=============================
        client-key-data: 
    ===========================//snipped //======================// snipped ============================
    PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=
    ```

1. Zkopíruje výstup zobrazený v prostém textu. Uložte výstup jako *konfigurační* soubor (bez přípony) do `.kube` složky vašeho profilu uživatele na místním počítači, například `C:\Users\<username>\.kube` . 

1. Udělte uživateli, který jste vytvořili, přístup k `iotedge` oboru názvů. Spusťte následující příkaz:

    `Grant-HcsKubernetesNamespaceAccess -Namespace iotedge -UserName <user name>`    

    Tady je příklad výstupu:

    ```python
    [10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace iotedge -UserName iotedgeuser
    [10.100.10.10]: PS>    
    ```
Podrobné pokyny najdete v tématu [připojení a Správa clusteru Kubernetes přes kubectl na zařízení GPU Azure Stack Edge pro](azure-stack-edge-gpu-create-kubernetes-cluster.md#configure-cluster-access-via-kubernetes-rbac).

### <a name="deploy-modules-via-portal"></a>Nasazení modulů přes portál

Nasaďte IoT Edge moduly přes Azure Portal. Nasadíte veřejně dostupné ukázkové moduly NVIDIA CUDA, které spouští simulaci n těla. Další informace najdete v tématu [simulace N-body](https://physics.princeton.edu//~fpretori/Nbody/intro.htm).

1. Ujistěte se, že je na vašem zařízení spuštěná služba IoT Edge.

    ![Služba IoT Edge spuštěná.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-1.png)

1. V pravém podokně vyberte dlaždici IoT Edge. Přejít na **IoT Edge vlastnosti >**. V pravém podokně vyberte prostředek IoT Hub přidružený k vašemu zařízení.

    ![Zobrazit vlastnosti.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-2.png)

1. V prostředku IoT Hub v **> IoT Edge přejít na automatické řízení zařízení**. V pravém podokně vyberte zařízení IoT Edge přidružené k vašemu zařízení.

    ![Přejít na IoT Edge.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-3.png)

1. Vyberte **Set modules** (Nastavit moduly).

    ![Přejít na nastavit moduly.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-4.png)

1. Vyberte **+ Přidat modul > + IoT Edge**.

    ![Přidejte IoT Edge modul.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-5.png)

1. Na kartě **nastavení modulu** zadejte **název modulu IoT Edge** a **identifikátor URI obrázku**. Nastavte **u obrázku při vytváření** **zásadu vyžádané replikace** .

    ![Nastavení modulu.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-6.png)
1. Na kartě **proměnné prostředí** zadejte **NVIDIA_VISIBLE_DEVICES** jako **0**.

    ![Proměnné prostředí.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-7.png)

1. Na kartě **možnosti vytvoření kontejneru** zadejte následující možnosti:

    ```json
    {
        "Entrypoint": [
            "/bin/sh"
        ],
        "Cmd": [
            "-c",
            "/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done"
        ],
        "HostConfig": {
            "IpcMode": "host",
            "PidMode": "host"
        }
    }    
    ```
    Možnosti se zobrazí takto:

    ![Možnosti vytvoření kontejneru](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-8.png)

    Vyberte **Přidat**.

1. Modul, který jste přidali, by měl zobrazovat jako **spuštěný**. 

    ![Zkontrolujte a vytvořte nasazení.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-9.png)


1. Opakováním všech kroků přidejte modul, který jste sledovali při přidávání prvního modulu. V tomto příkladu zadejte název modulu jako `cuda-sample2` . 

    ![Nastavení modulu pro druhý modul.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-12.png)

    Použijte stejnou proměnnou prostředí, protože oba moduly budou sdílet stejný GPU.

    ![Proměnná prostředí pro druhý modul](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-13.png)

    Použijte stejné možnosti vytvoření kontejneru, jaké jste zadali pro první modul, a vyberte **Přidat**.

    ![Možnosti vytváření kontejnerů pro tyto 2 moduly.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-14.png)

1. Na stránce **nastavit moduly** vyberte **zkontrolovat + vytvořit** a pak vyberte **vytvořit**. 

    ![Zkontrolujte a vytvořte druhé nasazení.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-15.png)

1. **Běhový stav** obou modulů by nyní měl zobrazovat jako **spuštěný**.  

    ![stav druhého nasazení.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-16.png)


### <a name="monitor-workload-deployment"></a>Monitorování nasazení úlohy

1. Otevřete novou relaci PowerShellu.

1. Vypíše lusky běžící v `iotedge` oboru názvů. Spusťte následující příkaz:

    `kubectl get pods -n iotedge`   

    Tady je příklad výstupu:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n iotedge --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1
    NAME                            READY   STATUS    RESTARTS   AGE
    cuda-sample1-869989578c-ssng8   2/2     Running   0          5s
    cuda-sample2-6db6d98689-d74kb   2/2     Running   0          4s
    edgeagent-79f988968b-7p2tv      2/2     Running   0          6d21h
    edgehub-d6c764847-l8v4m         2/2     Running   0          24h
    iotedged-55fdb7b5c6-l9zn8       1/1     Running   1          6d21h
    PS C:\WINDOWS\system32>   
    ```
    `cuda-sample1-97c494d7f-lnmns` `cuda-sample2-d9f6c4688-2rld9` Na svém zařízení běží dvě lusky.

1. I když oba kontejnery spouští simulaci n těla, Prohlédněte si využití GPU z výstupu NVIDIA SMI. Přejít na rozhraní PowerShellu zařízení a spustit `Get-HcsGpuNvidiaSmi` .

    Tady je příklad výstupu, pokud jsou v kontejnerech spuštěná simulace n-body:

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Fri Mar  5 13:31:16 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   52C    P0    69W /  70W |    221MiB / 15109MiB |    100%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    188342      C   /tmp/nbody                        109MiB |
    |    0   N/A  N/A    188413      C   /tmp/nbody                        109MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>
    ```
    Jak vidíte, existují dva kontejnery se spuštěnou simulací n-body na GPU 0. Můžete si také prohlédnout jejich odpovídající využití paměti.

1. Po dokončení simulace bude výstup NVIDIA SMI ukazovat na to, že na zařízení nejsou spuštěné žádné procesy.

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Fri Mar  5 13:54:48 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   34C    P8     9W /  70W |      0MiB / 15109MiB |      0%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>
    ```
 
1. Po dokončení simulace n-body Zobrazte protokoly, abyste pochopili podrobnosti o nasazení a dobu potřebnou k dokončení simulace. 

    Tady je příklad výstupu z prvního kontejneru:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample1-869989578c-ssng8 cuda-sample1
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170171.531 ms
    = 98.590 billion interactions per second
    = 1971.801 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>
    ```
    Tady je příklad výstupu z druhého kontejneru:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample2-6db6d98689-d74kb cuda-sample2
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170054.969 ms
    = 98.658 billion interactions per second
    = 1973.152 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>
    ```

1. Zastavte nasazení modulu. V IoT Hub prostředek pro vaše zařízení:
    1. Přejít na **automatické nasazení zařízení > IoT Edge**. Vyberte zařízení IoT Edge odpovídající vašemu zařízení.

    1. Přejít na **nastavit moduly** a vybrat modul. 

        ![Vyberte nastavit modul.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-1.png)

    1. Na kartě **moduly** vyberte modul.
    
        ![Vyberte modul.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-2.png)

    1.  Na kartě **nastavení modulu** nastavte **požadovaný stav** na zastaveno. Vyberte **Aktualizovat**.

        ![Upravte nastavení modulu.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-3.png)

    1. Zopakováním postupu zastavte druhý modul nasazený v zařízení. Vyberte **Zkontrolovat a vytvořit** a potom **Vytvořit**. Tato aktualizace by měla aktualizovat nasazení.

        ![Zkontrolujte a vytvořte aktualizované nasazení.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-6.png)
 
    1. Aktualizujte **nastavení modulů** několikrát. dokud se **stav modulu runtime** modulu nezobrazuje jako **Zastaveno**.

        ![Ověřte stav nasazení.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-8.png) 
    

## <a name="deploy-with-context-sharing"></a>Nasazení s použitím sdílení kontextu

V případě, že na zařízení běží MPS, teď můžete na dva kontejnery CUDA nasadit simulaci n-těla. Nejdřív na zařízení povolíte MPS.


1. [Připojte se k rozhraní PowerShell vašeho zařízení](azure-stack-edge-gpu-connect-powershell-interface.md).

1. Pokud chcete na svém zařízení povolit MPS, spusťte `Start-HcsGpuMPS` příkaz.

    ```powershell
    [10.100.10.10]: PS>Start-HcsGpuMPS
    K8S-1HXQG13CL-1HXQG13:
    Set compute mode to EXCLUSIVE_PROCESS for GPU 0000191E:00:00.0.
    All done.
    Created nvidia-mps.service
    [10.100.10.10]: PS>    
    ```
1. Získejte výstup NVIDIA SMI z rozhraní PowerShellu zařízení. Můžete vidět `nvidia-cuda-mps-server` proces nebo na zařízení běží služba MPS.

    Tady je příklad výstupu:

    ```yml
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Thu Mar  4 12:37:39 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   36C    P8     9W /  70W |     28MiB / 15109MiB |      0%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    122792      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    ```

1. Nasaďte moduly, které jste zastavili dříve. Nastavte **požadovaný stav** na spouštění prostřednictvím **Nastavení moduly**.

    Tady je příklad výstupu:

    ```yml
    PS C:\WINDOWS\system32> kubectl get pods -n iotedge --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1
    NAME                            READY   STATUS    RESTARTS   AGE
    cuda-sample1-869989578c-2zxh6   2/2     Running   0          44s
    cuda-sample2-6db6d98689-fn7mx   2/2     Running   0          44s
    edgeagent-79f988968b-7p2tv      2/2     Running   0          5d20h
    edgehub-d6c764847-l8v4m         2/2     Running   0          27m
    iotedged-55fdb7b5c6-l9zn8       1/1     Running   1          5d20h
    PS C:\WINDOWS\system32>
    ```
    Můžete vidět, že se moduly nasazují a spouštějí na vašem zařízení.

1. Když jsou moduly nasazeny, simulace n-body také začíná běžet na obou kontejnerech. Tady je příklad výstupu, když se simulace dokončila v prvním kontejneru:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge logs cuda-sample1-869989578c-2zxh6 cuda-sample1
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 155256.062 ms
    = 108.062 billion interactions per second
    = 2161.232 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32> 
    ```
    Tady je příklad výstupu, když se simulace dokončí u druhého kontejneru:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample2-6db6d98689-fn7mx cuda-sample2
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 155366.359 ms
    = 107.985 billion interactions per second
    = 2159.697 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>    
    ```      

1. Pokud jsou v kontejnerech spuštěné simulace n-těla, získá výstup NVIDIA SMI z rozhraní PowerShell zařízení. Tady je příklad výstupu. Existují tři procesy, `nvidia-cuda-mps-server` proces (typ C) odpovídá službě MPS a `/tmp/nbody` Process (typ M + C) odpovídá úlohám n-těle nasazeným moduly. 

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Thu Mar  4 12:59:44 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   54C    P0    69W /  70W |    242MiB / 15109MiB |    100%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A     56832    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A     56900    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A    122792      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    ```
    
## <a name="next-steps"></a>Další kroky

- [Nasaďte sdílenou úlohu KUBERNETES GPU na Azure Stack Edge pro](azure-stack-edge-gpu-deploy-kubernetes-gpu-sharing.md).
