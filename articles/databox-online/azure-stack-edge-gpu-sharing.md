---
title: Sdílení GPU na zařízení s grafickým procesorem Azure Stack Edge pro
description: Popisuje přístupy ke sdílení GPU na zařízení s grafickým procesorem Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: 6683e39cfa3601b1ae1fbbe02e69e4dc0a54e8e7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564891"
---
# <a name="gpu-sharing-on-your-azure-stack-edge-pro-gpu-device"></a>Sdílení GPU na zařízení s grafickým procesorem Azure Stack Edge pro

Graphics Processing Unit (GPU) je specializovaný procesor navržený tak, aby urychlil vykreslování grafiky. GPU můžou souběžně zpracovávat spoustu dat, což je užitečné pro strojové učení, úpravy videa a herní aplikace. Kromě procesoru pro výpočetní prostředky pro obecné účely můžou vaše zařízení Azure Stack Edge pro používat jeden nebo dva GPU NVIDIA Tesla T4 pro úlohy náročné na výpočetní výkon, jako je například hardwarová akcelerace Inferencing. Další informace najdete v tématu [grafický procesor Tesla T4 na NVIDIA](https://www.nvidia.com/data-center/tesla-t4/).


## <a name="about-gpu-sharing"></a>O sdílení GPU

Řada strojového učení nebo jiných výpočetních úloh nemusí potřebovat vyhrazený grafický procesor. GPU můžete sdílet a sdílet s grafickými procesory v kontejnerech nebo úlohách virtuálních počítačů pomáhají zvýšit využití GPU bez výrazného vlivu na výkonnostní výhody GPU.  

## <a name="using-gpu-with-vms"></a>Použití GPU u virtuálních počítačů

Na zařízení Azure Stack Edge pro nemůžete při nasazování úloh virtuálních počítačů sdílet GPU. GPU se dá mapovat jenom na jeden virtuální počítač. To znamená, že můžete mít jenom jeden virtuální počítač GPU na zařízení s jedním GPU a dvěma virtuálními počítači na zařízení, které je vybavené dvěma GPU. K dispozici jsou i další faktory, které je potřeba vzít v úvahu při použití virtuálních počítačů GPU na zařízení s Kubernetes nakonfigurovaným pro kontejnerové úlohy. Další informace najdete v tématu [virtuální počítače GPU a Kubernetes](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#gpu-vms-and-kubernetes).


## <a name="using-gpu-with-containers"></a>Použití GPU s kontejnery

Pokud nasazujete kontejnerové úlohy, může se grafický procesor sdílet více než jedním způsobem na hardwarové a softwarové vrstvě. S grafickým procesorem Tesla T4 na zařízení Azure Stack Edge pro je omezeno na sdílení softwaru. V zařízení se používají následující dva přístupy pro sdílení softwaru GPU: 

- Prvním přístupem je použití proměnných prostředí k určení počtu GPU, které mohou být v čase sdíleny. Při použití tohoto přístupu Vezměte v úvahu následující upozornění:

    - Pomocí této metody můžete zadat jednu nebo žádnou GPU nebo žádnou z nich. Není možné určit zlomkové využití.
    - Více modulů lze namapovat na jeden grafický procesor, ale stejný modul nelze namapovat na více než jeden grafický procesor.
    - Pomocí výstupu NVIDIA SMI můžete zobrazit celkové využití GPU, včetně využití paměti.
    
    Další informace najdete v tématu Jak [nasadit modul IoT Edge, který používá GPU](azure-stack-edge-gpu-configure-gpu-modules.md) na vašem zařízení.

- Druhý přístup vyžaduje, abyste povolili službu Multi-Process ve vašich grafických procesorech NVIDIA. MP je běhová služba, která umožňuje souběžně běžet více procesů s využitím CUDA na jednom sdíleném grafickém procesoru. MP umožňuje překrývat operace jádra a memcopy z různých procesů v GPU a dosáhnout tak maximálního využití. Další informace najdete v tématu [Služba pro více procesů](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf).

    Při použití tohoto přístupu Vezměte v úvahu následující upozornění:
    
    - Pomocí sady MP můžete v nasazení GPU zadat další příznaky.
    - Můžete určit desetinné využití prostřednictvím MPS a tím omezit využití každé aplikace nasazené na zařízení. Můžete zadat procentuální hodnotu GPU, která se má použít pro každou aplikaci v `env` části v části `deployment.yaml` přidáním následujícího parametru: 

    ```yml
    // Example: application wants to limit gpu percentage to 20%
    
        env:
              - name: CUDA_MPS_ACTIVE_THREAD_PERCENTAGE 
                value: "20"    
    ```

## <a name="gpu-utilization"></a>Využití GPU
 
Když sdílíte GPU na kontejnerových úlohách nasazených v zařízení, můžete použít rozhraní NVIDIA-SMI (System Management Interface). NVIDIA-SMI je nástroj příkazového řádku, který pomáhá spravovat a monitorovat zařízení NVIDIA GPU. Další informace najdete v tématu [rozhraní pro správu systému NVIDIA](https://developer.nvidia.com/nvidia-system-management-interface).

Pokud chcete zobrazit využití GPU, nejdřív se připojte k rozhraní PowerShell zařízení. Spusťte `Get-HcsNvidiaSmi` příkaz a zobrazte výstup NVIDIA SMI. Můžete také zobrazit, jak se využití GPU mění tím, že povolíte MPS a pak na zařízení nasadíte víc úloh. Další informace najdete v tématu [povolení více procesní služby](azure-stack-edge-gpu-connect-powershell-interface.md#enable-multi-process-service-mps).


## <a name="next-steps"></a>Další kroky

- [Sdílení GPU pro nasazení Kubernetes na Azure Stack Edge pro](azure-stack-edge-gpu-deploy-kubernetes-gpu-sharing.md).
- [Sdílení GPU pro nasazení IoT na Azure Stack Edge pro](azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing.md).
