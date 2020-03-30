---
title: Nasazení instance kontejneru s podporou GPU
description: Zjistěte, jak nasadit instance kontejnerů Azure ke spouštění aplikací kontejnerů náročných na výpočetní výkon pomocí prostředků GPU.
ms.topic: article
ms.date: 02/19/2020
ms.openlocfilehash: 0f1d21c62be5d7ae099faa2c6fcc440829bb451f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77525282"
---
# <a name="deploy-container-instances-that-use-gpu-resources"></a>Nasazení instancí kontejnerů, které používají prostředky GPU

Chcete-li spustit určité úlohy náročné na výpočetní výkon v instanci kontejneru Azure, nasaďte [skupiny kontejnerů](container-instances-container-groups.md) s *prostředky GPU*. Instance kontejneru ve skupině mohou přistupovat k jedné nebo více grafickým procesorům NVIDIA Tesla při spouštění úloh kontejneru, jako je CUDA a aplikace pro hloubkové učení.

Tento článek ukazuje, jak přidat prostředky GPU při nasazení skupiny kontejnerů pomocí [souboru YAML](container-instances-multi-container-yaml.md) nebo [šablony Správce prostředků](container-instances-multi-container-group.md). Prostředky GPU můžete také určit, když nasadíte instanci kontejneru pomocí portálu Azure.

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview a platí určitá [omezení](#preview-limitations). Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="preview-limitations"></a>Omezení náhledu

Ve verzi Preview platí následující omezení při použití prostředků GPU ve skupinách kontejnerů. 

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]

V průběhu času bude přidána podpora pro další regiony.

**Podporované typy operačních systémů**: Pouze Linux

**Další omezení**: Prostředky GPU nelze použít při nasazování skupiny kontejnerů do [virtuální sítě](container-instances-vnet.md).

## <a name="about-gpu-resources"></a>O zdrojích GPU

> [!IMPORTANT]
> Zdroje GPU jsou k dispozici pouze na vyžádání. Chcete-li požádat o přístup k prostředkům GPU, odešlete [žádost o podporu Azure][azure-support].

### <a name="count-and-sku"></a>Počet a skladová položka

Chcete-li použít grafické procesory v instanci kontejneru, zadejte *prostředek GPU* s následujícími informacemi:

* **Počet** - Počet GPU: **1**, **2**nebo **4**.
* **Skladová položka** – skladová položka GPU: **K80**, **P100**nebo **V100**. Každá skladová položka mapuje gpu NVIDIA Tesla v jedné z následujících rodin virtuálních zařízení s podporou GpU Azure:

  | Skladová jednotka (SKU) | Rodina virtuálních montovek |
  | --- | --- |
  | K80 | [NC](../virtual-machines/nc-series.md) |
  | P100 | [NCv2](../virtual-machines/ncv2-series.md) |
  | V100 | [NCv3](../virtual-machines/ncv3-series.md) |

[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

Při nasazování prostředků GPU nastavte prostředky procesoru a paměti vhodné pro úlohu až do maximálních hodnot uvedených v předchozí tabulce. Tyto hodnoty jsou aktuálně větší než prostředky procesoru a paměti, které jsou k dispozici ve skupinách kontejnerů bez prostředků GPU.  

### <a name="things-to-know"></a>Co je potřeba vědět

* **Doba nasazení** – vytvoření skupiny kontejnerů obsahující prostředky GPU trvá až **8 až 10 minut**. To je způsobeno další čas na zřízení a konfiguraci virtuálního počítače GPU v Azure. 

* **Ceny** – Podobně jako skupiny kontejnerů bez prostředků GPU, Azure účtuje za prostředky spotřebované po *dobu trvání* skupiny kontejnerů s prostředky GPU. Doba trvání se počítá od doby, kdy chcete vytáhnout image prvního kontejneru, dokud skupina kontejnerů neskončí. Nezahrnuje čas nasazení skupiny kontejnerů.

  Viz [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/container-instances/).

* **Ovladače CUDA** – instance kontejnerů s prostředky GPU jsou předem zřízeny pomocí ovladačů NVIDIA CUDA a runčase kontejnerů, takže můžete použít ibligrafické obrazy kontejnerů vyvinuté pro úlohy CUDA.

  Podporujeme CUDA 9.0 v této fázi. Pro soubor Dockeru můžete například použít následující základní image:
  * [nvidia/cuda:9.0-base-ubuntu16.04](https://hub.docker.com/r/nvidia/cuda/)
  * [tentenzorový/tenzorový průtok: 1.12.0-gpu-py3](https://hub.docker.com/r/tensorflow/tensorflow)
    
## <a name="yaml-example"></a>Příklad YAML

Jedním ze způsobů, jak přidat prostředky GPU, je nasazení skupiny kontejnerů pomocí [souboru YAML](container-instances-multi-container-yaml.md). Zkopírujte následující yaml do nového souboru s názvem *gpu-deploy-aci.yaml*, a pak soubor uložte. Tento YAML vytvoří skupinu kontejnerů s názvem *gpucontainergroup* určující instanci kontejneru s GPU K80. Instance spustí ukázkovou aplikaci cuda vektorsčítání. Požadavky na prostředky jsou dostatečné ke spuštění úlohy.

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

Nasaďte skupinu kontejnerů pomocí příkazu [az container create][az-container-create] a `--file` zadejte název souboru YAML pro parametr. Je třeba zadat název skupiny prostředků a umístění pro skupinu kontejnerů, jako je *například eastus,* který podporuje prostředky GPU.  

```azurecli
az container create --resource-group myResourceGroup --file gpu-deploy-aci.yaml --location eastus
```

Dokončení nasazení trvá několik minut. Potom kontejner spustí a spustí operaci přidání vektoru CUDA. Spuštěním příkazu [protokoly kontejneru az][az-container-logs] zobrazte výstup protokolu:

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

Dalším způsobem nasazení skupiny kontejnerů s prostředky GPU je použití [šablony Správce prostředků](container-instances-multi-container-group.md). Začněte vytvořením `gpudeploy.json`souboru s názvem a zkopírujte do něj následující json. Tento příklad nasazuje instanci kontejneru s grafickým procesorem V100, který spouští trénovací úlohu [Tententory ve](https://www.tensorflow.org/) formátu Proti datové sadě MNIST. Požadavky na prostředky jsou dostatečné ke spuštění úlohy.

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

Nasaďte šablonu pomocí příkazu [vytvořit nasazení skupiny az.][az-group-deployment-create] Je třeba zadat název skupiny prostředků, která byla vytvořena v oblasti, jako je *eastus,* který podporuje prostředky GPU.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file gpudeploy.json
```

Dokončení nasazení trvá několik minut. Potom kontejner spustí a spustí úlohu TensorFlow. Spuštěním příkazu [protokoly kontejneru az][az-container-logs] zobrazte výstup protokolu:

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

Vzhledem k tomu, že použití prostředků GPU může být nákladné, ujistěte se, že vaše kontejnery nespustí neočekávaně po dlouhou dobu. Sledujte kontejnery na webu Azure Portal nebo zkontrolujte stav skupiny kontejnerů pomocí příkazu [az container show.][az-container-show] Například:

```azurecli
az container show --resource-group myResourceGroup --name gpucontainergroup --output table
```

Až skončíte s prací s instancemi kontejneru, které jste vytvořili, odstraňte je pomocí následujících příkazů:

```azurecli
az container delete --resource-group myResourceGroup --name gpucontainergroup -y
az container delete --resource-group myResourceGroup --name gpucontainergrouprm -y
```

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o nasazení skupiny kontejnerů pomocí [souboru YAML](container-instances-multi-container-yaml.md) nebo [šablony Správce prostředků](container-instances-multi-container-group.md).
* Přečtěte si další informace o [velikostech virtuálních zařízení optimalizovaných](../virtual-machines/linux/sizes-gpu.md) pro GPU v Azure.


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
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
