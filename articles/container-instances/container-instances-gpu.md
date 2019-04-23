---
title: Nasazovat instance kontejneru Azure s podporou grafického procesoru
description: Zjistěte, jak nasadit Azure container instance pro spuštění na GPU prostředky.
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/17/2019
ms.author: danlep
ms.openlocfilehash: 5073b68f6ef3de330671e3ea25056e0cae976360
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "60000653"
---
# <a name="deploy-container-instances-that-use-gpu-resources"></a>Nasazení instance kontejnerů, které používají prostředky GPU

Pro spouštění určité úlohy náročné na výpočetní prostředky v Azure Container Instances, nasazení vaše [skupiny kontejnerů](container-instances-container-groups.md) s *GPU prostředky*. Instance kontejnerů ve skupině mají přístup k jedné nebo více GPU NVIDIA Tesla při spouštění úloh kontejneru, jako je například CUDA a obsáhlý learning aplikací.

Tento článek popisuje postup přidání prostředků GPU, když nasadíte skupinu kontejnerů s využitím [soubor YAML](container-instances-multi-container-yaml.md) nebo [šablony Resource Manageru](container-instances-multi-container-group.md). Můžete také určit prostředky GPU při nasazení instance kontejneru na webu Azure portal.

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi preview a některé [platí omezení](#preview-limitations). Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="preview-limitations"></a>Omezení verze Preview

Ve verzi preview platí následující omezení při použití GPU zdrojů v skupinách kontejnerů. 

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]

Podpora bude postupně přibudou další oblasti.

**Podporované typy operačních systémů**: Pouze pro Linux

**Další omezení**: GPU prostředky nelze použít při nasazování do skupiny kontejnerů [virtuální sítě](container-instances-vnet.md).

## <a name="about-gpu-resources"></a>O prostředcích GPU

### <a name="count-and-sku"></a>Počet a skladové položky

Chcete-li použít grafickými procesory v instanci kontejneru, zadejte *GPU prostředků* s následujícími informacemi:

* **Počet** – počet GPU: **1**, **2**, nebo **4**.
* **Skladová položka** -GPU SKU: **K80**, **P100**, nebo **V100**. Každý SKU mapuje na GPU NVIDIA Tesla v jednom rodiny virtuálních počítačů s podporou grafického procesoru Azure následující:

  | Skladová jednotka (SKU) | Rodina virtuálního počítače |
  | --- | --- |
  | K80 | [NC](../virtual-machines/linux/sizes-gpu.md#nc-series) |
  | P100 | [NCv2](../virtual-machines/linux/sizes-gpu.md#ncv2-series) |
  | V100 | [NCv3](../virtual-machines/linux/sizes-gpu.md#ncv3-series) |

[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

Při nasazování prostředků GPU, nastavte prostředky procesoru a paměti vhodné pro úlohy, až do maximální hodnoty uvedené v předchozí tabulce. Tyto hodnoty jsou aktuálně větší než prostředky procesoru a paměti, které jsou k dispozici ve skupinách kontejnerů bez prostředků GPU.  

### <a name="things-to-know"></a>Co je potřeba vědět

* **Čas nasazení** – vytvoření skupiny kontejnerů obsahující GPU prostředků trvá až **8 až 10 minut**. Příčinou je další čas ke zřizování a konfiguraci virtuálního počítače s GPU v Azure. 

* **Ceny** – podobně jako na skupiny kontejnerů bez prostředků GPU, Azure účtuje poplatky za pro prostředky využité nad *doba trvání* skupiny kontejnerů s prostředky GPU. Doba trvání se počítá od času si přetáhněte svůj první kontejner image, dokud skupina kontejnerů ukončí. Nezahrnuje dobu nasazení skupiny kontejnerů.

  Zobrazit [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/container-instances/).

* **Ovladače CUDA** – Container instances, s GPU prostředky jsou předem zřízené ovladače NVIDIA CUDA a vyvinuté kontejnerové moduly runtime, abyste mohli používat Image kontejneru pro CUDA, úlohy.

  V této fázi podporujeme CUDA 9.0. Například můžete použít následující základní Image pro váš soubor Docker:
  * [nvidia/cuda:9.0-base-ubuntu16.04](https://hub.docker.com/r/nvidia/cuda/)
  * [tensorflow/tensorflow: 1.12.0-GPU-py3](https://hub.docker.com/r/tensorflow/tensorflow)
    
## <a name="yaml-example"></a>Příklad YAML

Jedním ze způsobů přidání prostředků GPU je nasadit skupinu kontejnerů s využitím [soubor YAML](container-instances-multi-container-yaml.md). Zkopírujte následující kód YAML do nového souboru s názvem *gpu nasazení aci.yaml*, pak soubor uložte. Tato YAML vytvoří skupinu kontejnerů *gpucontainergroup* zadání instanci kontejneru s grafickým Procesorem K80. Instance spuštění ukázkové CUDA vektoru přidání aplikace. Požadavky prostředků jsou dostatečné ke spouštění úloh.

```YAML
additional_properties: {}
apiVersion: '2018-10-01'
name: gpucontainergroup
properties:
  containers:
  - name: gpucontainer
    properties:
      image: k8s-gcrio.azureedge.net/cuda-vector-add:v0.1
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
          gpu:
            count: 1
            sku: K80
  osType: Linux
  restartPolicy: OnFailure
```

Nasadit skupinu kontejnerů s [az container vytvořit] [ az-container-create] příkaz a určete název souboru YAML `--file` parametru. Je potřeba zadat název skupiny prostředků a umístění pro skupinu kontejnerů například *eastus* , který podporuje prostředky GPU.  

```azurecli
az container create --resource-group myResourceGroup --file gpu-deploy-aci.yaml --location eastus
```

Dokončení nasazení trvá několik minut. Potom kontejneru spustí a spustí operaci sčítání CUDA vektoru. Spustit [protokoly kontejneru az] [ az-container-logs] příkazu zobrazte výstup protokolu:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergroup --container-name gpucontainer
```

Výstup:

```Console
[Vector addition of 50000 elements]
Copy input data from the host memory to the CUDA device
CUDA kernel launch with 196 blocks of 256 threads
Copy output data from the CUDA device to the host memory
Test PASSED
Done
```

## <a name="resource-manager-template-example"></a>Příklad šablony Resource Manageru

Dalším způsobem, jak nasadit skupinu kontejnerů s prostředky GPU je pomocí [šablony Resource Manageru](container-instances-multi-container-group.md). Začněte vytvořením souboru s názvem `gpudeploy.json`, pak zkopírujte do něj následující kód JSON. Tento příklad nasadí instanci kontejneru s V100 grafického procesoru, na kterém běží [TensorFlow](https://www.tensorflow.org/versions/r1.1/get_started/mnist/beginners) trénovací úlohu proti [datovou sadu mnist ručně](http://yann.lecun.com/exdb/mnist/). Požadavky prostředků jsou dostatečné ke spouštění úloh.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "gpucontainergrouprm",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "variables": {
      "containername": "gpucontainer",
      "containerimage": "microsoft/samples-tf-mnist-demo:gpu"
    },
    "resources": [
      {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2018-10-01",
        "location": "[resourceGroup().location]",
        "properties": {
            "containers": [
            {
              "name": "[variables('containername')]",
              "properties": {
                "image": "[variables('containerimage')]",
                "resources": {
                  "requests": {
                    "cpu": 4.0,
                    "memoryInGb": 12.0,
                    "gpu": {
                        "count": 1,
                        "sku": "V100"
                  }
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "restartPolicy": "OnFailure"
        }
      }
    ]
}
```

Nasazení šablony s [vytvořit nasazení skupiny pro az] [ az-group-deployment-create] příkazu. Je třeba zadat název skupiny prostředků, který byl vytvořen v oblasti, jako například *eastus* , který podporuje prostředky GPU.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file gpudeploy.json
```

Dokončení nasazení trvá několik minut. Potom kontejner spustí a spustí úlohu TensorFlow. Spustit [protokoly kontejneru az] [ az-container-logs] příkazu zobrazte výstup protokolu:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergrouprm --container-name gpucontainer
```

Výstup:

```Console
2018-10-25 18:31:10.155010: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2018-10-25 18:31:10.305937: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties:
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: ccb6:00:00.0
totalMemory: 11.92GiB freeMemory: 11.85GiB
2018-10-25 18:31:10.305981: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: ccb6:00:00.0, compute capability: 3.7)
2018-10-25 18:31:14.941723: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.097
Accuracy at step 10: 0.6993
Accuracy at step 20: 0.8208
Accuracy at step 30: 0.8594
...
Accuracy at step 990: 0.969
Adding run metadata for 999
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Protože prostředky GPU může být nákladné, ujistěte se, že kontejnery nespouštět neočekávaně dlouhou dobu. Monitorování kontejnerů na webu Azure Portal, nebo zkontrolujte stav skupinu kontejnerů s [az container show] [ az-container-show] příkazu. Příklad:

```azurecli
az container show --resource-group myResourceGroup --name gpucontainergroup --output table
```

Po dokončení práce se službou container instances jste vytvořili, je odstranit pomocí následujících příkazů:

```azurecli
az container delete --resource-group myResourceGroup --name gpucontainergroup -y
az container delete --resource-group myResourceGroup --name gpucontainergrouprm -y
```

## <a name="next-steps"></a>Další postup

* Další informace o nasazení kontejneru pomocí skupiny [soubor YAML](container-instances-multi-container-yaml.md) nebo [šablony Resource Manageru](container-instances-multi-container-group.md).
* Další informace o [GPU optimalizované velikosti virtuálních počítačů](../virtual-machines/linux/sizes-gpu.md) v Azure.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
