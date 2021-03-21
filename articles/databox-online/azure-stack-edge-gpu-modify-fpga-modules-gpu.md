---
title: Úprava IoT Edgech modulů na zařízení FPGA pro spuštění na zařízení GPU Azure Stack Edge pro
description: Popisuje, jaké úpravy jsou potřeba pro existující IoT Edge moduly na stávajících zařízeních FPGA ke spuštění na zařízení GPU Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 660fbf7cc4dd28c800d8f49fd5d990c99f97c4c8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102442991"
---
# <a name="run-existing-iot-edge-modules-from-azure-stack-edge-pro-fpga-devices-on-azure-stack-edge-pro-gpu-device"></a>Spouštět existující IoT Edge moduly ze zařízení Azure Stack Edge pro FPGA na zařízení s grafickým procesorem Azure Stack Edge pro

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Tento článek podrobně popisuje změny potřebné pro modul IoT Edge založený na Docker, který běží na Azure Stack Edge pro FPGA, takže ho můžete spustit na platformě IoT Edge založenou na Kubernetes na zařízení GPU Azure Stack Edge pro. 

## <a name="about-iot-edge-implementation"></a>O IoT Edge implementaci 

Implementace IoT Edge je odlišná na zařízeních Azure Stack Edge pro FPGA vs. ta na zařízeních GPU Azure Stack Edge pro. Pro zařízení GPU se Kubernetes používá jako hostující platforma pro IoT Edge. IoT Edge na zařízeních FPGA využívá platformu na bázi Docker. Model aplikace založený na IoT Edge Docker je automaticky přeložen na model nativní aplikace Kubernetes. Některé změny ale můžou být potřeba i v případě, že je podporovaná jenom malá podmnožina aplikačního modelu Kubernetes.

Pokud migrujete úlohy ze zařízení FPGA na zařízení GPU, budete muset provést změny v existujících modulech IoT Edge, aby se spouštěly úspěšně na platformě Kubernetes. Možná budete muset určit úložiště, sítě, využití prostředků a požadavky webového proxy. 

## <a name="storage"></a>Storage

Při určování úložiště pro IoT Edge moduly Vezměte v úvahu následující informace.

- Úložiště pro kontejnery v Kubernetes se zadává pomocí připojení svazků.
- Nasazení na Kubernetes nemůže mít vazby pro přidružení trvalého úložiště nebo hostitelských cest.
    - V případě trvalého úložiště použijte `Mounts` s typem `volume` .
    - Pro cesty hostitele použijte `Mounts` typ with `bind` .
- Pro IoT Edge v Kubernetes `Mounts` funguje vazba jenom pro adresář, a ne pro soubor.

#### <a name="example---storage-via-volume-mounts"></a>Příklad – úložiště přes připojení svazků 

Pro IoT Edge v Docker se vazby cesty hostitele používají k mapování sdílených složek na zařízení na cesty uvnitř kontejneru. Tady jsou možnosti vytváření kontejnerů, které se používají na zařízeních FPGA:

```
{
  "HostConfig": 
  {
   "Binds": 
    [
     "<Host storage path for Edge local share>:<Module storage path>"
    ]
   }
}
```
<!-- is this how it will look on GPU device?-->
V případě cest hostitele pro IoT Edge v Kubernetes se `Mounts` tady zobrazuje příklad použití s typem `bind` :
```
{
    "HostConfig": {
        "Mounts": [
            {
                "Target": "<Module storage path>",
                "Source": "<Host storage path>",
                "Type": "bind"
            }
        ]
    }
}
```


<!--following example is for persistent storage where we use mounts w/ type volume-->

Pro zařízení GPU se systémem IoT Edge v Kubernetes se k určení úložiště používají připojení svazků. Pokud chcete zřídit úložiště pomocí sdílených složek, hodnota `Mounts.Source` by byla název sdílené složky SMB nebo NFS, která byla zřízena na vašem zařízení GPU. `/home/input`Je cesta, ke které je svazek přístupný v rámci kontejneru. Tady jsou možnosti vytváření kontejnerů, které se používají na zařízeních GPU:

```
{
    "HostConfig": {
        "Mounts": [
        {
            "Target": "/home/input",
            "Source": "<nfs-or-smb-share-name-here>",
            "Type": "volume"
        },
        {
            "Target": "/home/output",
            "Source": "<nfs-or-smb-share-name-here>",
            "Type": "volume"
        }]
    }
}
```



## <a name="network"></a>Síť

Při určování sítě pro moduly IoT Edge Vezměte v úvahu následující informace. 

- `HostPort` specifikace je nutná k vystavení služby uvnitř i vně clusteru.
    - K8sExperimental možnosti pro omezení angažovanosti služby jenom na cluster.
- Komunikace mezi moduly vyžaduje `HostPort` specifikaci a připojení pomocí mapovaného portu (nepoužívá port vystaveného kontejneru).
- Hostitelská síť funguje se službou `dnsPolicy = ClusterFirstWithHostNet` , přičemž všechny kontejnery (obzvláště `edgeHub` ) nemusí být v síti hostitele taky. <!--Need further clarifications on this one-->
- Přidání mapování portů pro TCP, UDP ve stejném požadavku nefunguje.

#### <a name="example---external-access-to-modules"></a>Příklad – externí přístup k modulům 

Pro všechny IoT Edge moduly, které určují vazby portů, se IP adresa přiřadí pomocí rozsahu IP adres externí služby Kubernetes, který jste zadali v místním uživatelském rozhraní zařízení. V kontejneru nejsou žádné změny, které by bylo možné vytvořit v kontejneru IoT Edge v Docker vs IoT Edge na Kubernetes, jak je znázorněno v následujícím příkladu.  

```json 
{
    "HostConfig": {
        "PortBindings": {
            "5000/tcp": [
                {
                    "HostPort": "5000"
                }
            ]
        }
    }
}
```

Pokud ale chcete zadat dotaz na IP adresu přiřazenou k vašemu modulu, můžete použít řídicí panel Kubernetes, jak je popsáno v tématu [získání IP adresy pro služby nebo moduly](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules). 

Případně se můžete [připojit k rozhraní PowerShell zařízení](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) a pomocí `iotedge` příkazu list Zobrazit seznam všech modulů spuštěných ve vašem zařízení. [Výstup příkazu](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge) bude také označovat externí IP adresy přidružené k modulu.


## <a name="resource-usage"></a>Využití prostředků 

U zařízení s grafickým rozhraním Kubernetes IoT Edge nastavení na zařízeních GPU jsou prostředky, jako je hardwarová akcelerace, paměť a požadavky na procesor, určené jinak než na zařízeních FPGA. 

#### <a name="compute-acceleration-usage"></a>Využití akcelerace pro výpočty

Pokud chcete nasadit moduly v FPGA, použijte možnosti vytvoření kontejneru. <!--with Device Bindings--> Jak je znázorněno v následující konfiguraci: <!--not sure where are device bindings in this config--> 

```json
{
    "HostConfig": {
    "Privileged": true,
    "PortBindings": {
        "50051/tcp": [
        {
            "HostPort": "50051"
        }
        ]
    }
    },
    "k8s-experimental": {
    "resources": {
        "limits": {
        "microsoft.com/fpga_catapult": 2
        },
        "requests": {
        "microsoft.com/fpga_catapult": 2
        }
    }
    },
    "Env": [
    "WIRESERVER_ADDRESS=10.139.218.1"
    ]
}
``` 

    
<!--Note: The IP address assigned to your FPGA module's service can be used to send inferencing requests from outside the cluster OR your ML module can be used along with DBE Simple Module Flow by passing files to the module using an input share.-->
    
U GPU použijte specifikace požadavků prostředků namísto vazeb zařízení, jak je znázorněno v následující minimální konfiguraci. Vyžádáte prostředky NVIDIA místo posuňte a Vy nemusí podepisovat určíte `wireserver` . 

```json     
{
    "HostConfig": {
    "Privileged": true,
    "PortBindings": {
        "50051/tcp": [
        {
            "HostPort": "50051"
        }
        ]
    }
    },
    "k8s-experimental": {
    "resources": {
        "limits": {
        "nvidia.com/gpu": 2
        }    
    }
}
```

#### <a name="memory-and-cpu-usage"></a>Využití paměti a procesoru
 
K nastavení paměti a využití procesoru použijte omezení procesoru pro moduly v `k8s-experimental` části. <!--can we verify if this is how we set limits of memory and CPU-->

```json
    "k8s-experimental": {
    "resources": {
        "limits": {
            "memory": "128Mi",
            "cpu": "500m",
            "nvidia.com/gpu": 2
        },
        "requests": {
            "nvidia.com/gpu": 2
        }
}
```
Paměť a specifikace procesoru nejsou nutné, ale obecně dobrým postupem. `requests`Není-li parametr zadán, jsou hodnoty nastavené v omezeních použity jako požadované minimum. 

Použití sdílené paměti pro moduly také vyžaduje jiný způsob. Například můžete použít hostitelský režim IPC pro přístup ke sdílené paměti mezi živými a odvozenými řešeními, jak je popsáno v tématu [nasazení živých videí Analytics na Azure Stack Edge](../media-services/live-video-analytics-edge/deploy-azure-stack-edge-how-to.md#deploy-live-video-analytics-edge-module-using-azure-portal).


## <a name="web-proxy"></a>Webový proxy server 

Při konfiguraci webového proxy serveru Vezměte v úvahu následující informace:

Pokud máte ve vaší síti nakonfigurovaný webový proxy server, nakonfigurujte pro `edgeHub` nasazení na zařízeních FPGA následující proměnné prostředí pro instalaci na IoT Edge základě Docker:

- `https_proxy : <proxy URL>`
- `UpstreamProtocol : AmqpWs` (Pokud webový proxy server nepovoluje `Amqp` provoz)

Pro IoT Edge nastavení na zařízeních GPU budete muset během nasazení nakonfigurovat tuto další proměnnou:

- `no_proxy`: localhost

- IoT Edge proxy na platformě Kubernetes používá porty 35000 a 35001. Ujistěte se, že modul není na těchto portech spuštěný, nebo může způsobit konflikty portů. 

## <a name="other-differences"></a>Jiné rozdíly

- **Strategie nasazení**: možná budete muset změnit chování nasazení pro jakékoli aktualizace modulu. Výchozím chováním pro IoT Edge moduly je aktualizace. Toto chování brání v restartování aktualizovaného modulu, pokud modul používá prostředky, jako je hardwarová akcelerace nebo síťové porty. Toto chování může mít neočekávané účinky, speciálně při obchodování s trvalými svazky na platformě Kubernetes pro zařízení GPU. Chcete-li přepsat toto výchozí chování, můžete zadat `Recreate` v `k8s-experimental` části v modulu.

    ```    
    {
      "k8s-experimental": {
        "strategy": {
          "type": "Recreate"
        }
      }
    }
    ```

- **Názvy modulů**: názvy modulů by měly splňovat konvence pojmenování Kubernetes. Při přesunutí těchto modulů do IoT Edge pomocí Kubernetes možná budete muset přejmenovat moduly běžící na IoT Edge s použitím Docker. Další informace o pojmenování najdete v tématu zásady [vytváření názvů pro Kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/).
- **Další možnosti**: 
    - Některé možnosti vytváření Docker, které fungovaly na zařízeních FPGA, nebudou fungovat v prostředí Kubernetes na zařízeních GPU. Například:, like – EntryPoint.<!--can we confirm what exactly is required here-->
    - Proměnné prostředí, jako je `:` třeba nahrazení pomocí `__` .
    - **Kontejner vytvářející** stav pro Kubernetes pod vede ke stavu **omezení rychlosti** pro modul na prostředku IoT Hub. I když existuje několik důvodů, proč by měl být v tomto stavu, běžným důvodem je, že se rozsáhlá image kontejneru vyřazuje přes připojení s nízkou šířkou pásma sítě. Když je v tomto stavu, ve službě IOT hub se stav modulu zobrazí jako **omezení rychlosti** , i když se modul právě spouští.


## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o tom, jak [nakonfigurovat GPU pro použití modulu](azure-stack-edge-j-series-configure-gpu-modules.md).
