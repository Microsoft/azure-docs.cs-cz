---
title: Nasazení instance kontejneru s podporou GPU
description: Naučte se nasazovat služby Azure Container Instances pro spouštění kontejnerových aplikací náročných na výpočetní prostředky pomocí prostředků GPU.
ms.topic: article
ms.date: 07/22/2020
ms.openlocfilehash: 0d645d1fce24d1324e485d74e20bcf492d4444a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93127004"
---
# <a name="deploy-container-instances-that-use-gpu-resources"></a>Nasazení instancí kontejnerů, které používají prostředky GPU

Pokud chcete spouštět určité úlohy náročné na výpočetní výkon na Azure Container Instances, nasaďte [skupiny kontejnerů](container-instances-container-groups.md) pomocí *prostředků GPU*. Instance kontejnerů ve skupině mají přístup k jednomu nebo více GPU NVIDIA Tesla při spouštění úloh kontejneru, jako jsou CUDA a aplikace s hloubkovým učením.

Tento článek popisuje, jak přidat prostředky GPU při nasazení skupiny kontejnerů pomocí [souboru YAML](container-instances-multi-container-yaml.md) nebo [šablony Správce prostředků](container-instances-multi-container-group.md). Prostředky GPU můžete zadat také při nasazení instance kontejneru pomocí Azure Portal.

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview a [platí některá omezení](#preview-limitations). Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="preview-limitations"></a>Omezení verze Preview

V rámci verze Preview platí při používání prostředků GPU ve skupinách kontejnerů následující omezení. 

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]

Podpora bude v průběhu času přidána pro další oblasti.

**Podporované typy operačních systémů**: jenom Linux

**Další omezení**: prostředky GPU nelze použít při nasazování skupiny kontejnerů do [virtuální sítě](container-instances-vnet.md).

## <a name="about-gpu-resources"></a>O prostředcích GPU

### <a name="count-and-sku"></a>Počet a SKU

Chcete-li použít GPU v instanci kontejneru, zadejte *prostředek GPU* s následujícími informacemi:

* **Count** – počet GPU: **1**, **2** nebo **4**.
* **SKU** – SKU GPU: **K80**, **P100** nebo **V100**. Každá SKU se mapuje na grafický procesor NVIDIA Tesla v jedné z následujících rodin virtuálních počítačů s podporou GPU Azure:

  | SKU | Řada virtuálních počítačů |
  | --- | --- |
  | K80 | [NC](../virtual-machines/nc-series.md) |
  | P100 | [NCv2](../virtual-machines/ncv2-series.md) |
  | V100 | [NCv3](../virtual-machines/ncv3-series.md) |

[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

Při nasazování prostředků GPU nastavte prostředky procesoru a paměti, které jsou vhodné pro zatížení, až do maximálních hodnot uvedených v předchozí tabulce. Tyto hodnoty jsou aktuálně větší než prostředky procesoru a paměti dostupné ve skupinách kontejnerů bez prostředků GPU.  

> [!IMPORTANT]
> Výchozí [limity předplatného](container-instances-quotas.md) (kvóty) pro prostředky GPU se liší podle SKU. Výchozí limity procesoru pro skladové položky P100 a V100 jsou zpočátku nastavené na 0. Pokud si chcete vyžádat zvýšení dostupné oblasti, odešlete [žádost o podporu Azure][azure-support].

### <a name="things-to-know"></a>Co je potřeba vědět

* **Čas nasazení** – vytvoření skupiny kontejnerů obsahující prostředky GPU trvá až **8-10 minut**. Důvodem je další čas při zřizování a konfiguraci virtuálního počítače GPU v Azure. 

* **Ceny** – podobně jako u skupin kontejnerů bez prostředků GPU, Azure účtuje prostředky spotřebované po *dobu trvání* skupiny kontejnerů pomocí prostředků GPU. Doba trvání se vypočítává z doby, kdy se má načíst první obrázek kontejneru, dokud se neukončí skupina kontejnerů. Nezahrnuje čas k nasazení skupiny kontejnerů.

  Podívejte se na [Podrobnosti o cenách](https://azure.microsoft.com/pricing/details/container-instances/).

* **Ovladače CUDA** – instance kontejnerů s prostředky GPU jsou předem zřízené pomocí ovladačů NVIDIA CUDA a modulu runtime kontejnerů, takže můžete používat image kontejnerů vyvinuté pro úlohy CUDA.

  V této fázi podporujeme jenom CUDA 9,0. Pro soubor Docker můžete použít například následující základní Image:
  * [NVIDIA/CUDA: 9.0 – Base-Ubuntu 16.04](https://hub.docker.com/r/nvidia/cuda/)
  * [tensorflow/tensorflow: 1.12.0-GPU-py3](https://hub.docker.com/r/tensorflow/tensorflow)
    
## <a name="yaml-example"></a>Příklad YAML

Jedním ze způsobů, jak přidat prostředky GPU, je nasadit skupinu kontejnerů pomocí [souboru YAML](container-instances-multi-container-yaml.md). Zkopírujte následující YAML do nového souboru s názvem *GPU-Deploy-ACI. yaml* a pak soubor uložte. Tento YAML vytvoří skupinu kontejnerů s názvem *gpucontainergroup* , která určuje instanci kontejneru s grafickým procesorem K80. Instance spustí ukázkovou aplikaci doplňku CUDA Vector. Požadavky na prostředky jsou dostačující ke spuštění úlohy.

```YAML
additional_properties: {}
apiVersion: '2019-12-01'
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

Nasaďte skupinu kontejnerů pomocí příkazu [AZ Container Create][az-container-create] a zadáním názvu souboru YAML pro `--file` parametr. Je potřeba, abyste zadali název skupiny prostředků a umístění pro skupinu kontejnerů, jako je *eastus* , které podporují prostředky GPU.  

```azurecli
az container create --resource-group myResourceGroup --file gpu-deploy-aci.yaml --location eastus
```

Dokončení nasazení trvá několik minut. Kontejner se pak spustí a spustí operaci přidání vektoru CUDA. Spuštěním příkazu [AZ Container logs][az-container-logs] zobrazíte výstup protokolu:

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

## <a name="resource-manager-template-example"></a>Příklad šablony Správce prostředků

Dalším způsobem, jak nasadit skupinu kontejnerů pomocí prostředků GPU, je použití [šablony Správce prostředků](container-instances-multi-container-group.md). Začněte vytvořením souboru s názvem `gpudeploy.json` a zkopírujte do něj následující kód JSON. Tento příklad nasadí instanci kontejneru s grafickým procesorem V100, který spouští školicí úlohu [TensorFlow](https://www.tensorflow.org/) s datovou sadou mnist ručně zapsaných. Požadavky na prostředky jsou dostačující ke spuštění úlohy.

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
      "containerimage": "mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu"
    },
    "resources": [
      {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
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

Nasaďte šablonu pomocí příkazu [AZ Deployment Group Create][az-deployment-group-create] . Je potřeba, abyste zadali název skupiny prostředků, která byla vytvořená v oblasti, jako je *eastus* , která podporuje prostředky GPU.

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file gpudeploy.json
```

Dokončení nasazení trvá několik minut. Pak kontejner spustí a spustí úlohu TensorFlow. Spuštěním příkazu [AZ Container logs][az-container-logs] zobrazíte výstup protokolu:

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

Vzhledem k tomu, že používání prostředků GPU může být náročné, zajistěte, aby vaše kontejnery neočekávaně neběžely po dlouhou dobu. Monitorujte kontejnery v Azure Portal nebo pomocí příkazu [AZ Container show][az-container-show] ověřte stav skupiny kontejnerů. Například:

```azurecli
az container show --resource-group myResourceGroup --name gpucontainergroup --output table
```

Až budete pracovat s instancemi kontejnerů, které jste vytvořili, odstraňte je pomocí následujících příkazů:

```azurecli
az container delete --resource-group myResourceGroup --name gpucontainergroup -y
az container delete --resource-group myResourceGroup --name gpucontainergrouprm -y
```

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o nasazení skupiny kontejnerů pomocí [souboru YAML](container-instances-multi-container-yaml.md) nebo [šablony Správce prostředků](container-instances-multi-container-group.md).
* Přečtěte si další informace o [velikostech virtuálních počítačů optimalizovaných pro GPU](../virtual-machines/sizes-gpu.md) v Azure.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
