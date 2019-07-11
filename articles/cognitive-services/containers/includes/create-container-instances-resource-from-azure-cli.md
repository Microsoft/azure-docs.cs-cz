---
title: Podpora kontejnerů
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak vytvořit prostředek instance kontejneru Azure z příkazového řádku Azure.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 5e7a3d849f726ae4dbbd559d541464404e427775
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717067"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Vytvořte prostředek instanci kontejneru Azure z příkazového řádku Azure

Níže YAML definuje prostředek Azure Container Instance. Zkopírujte a vložte obsah do nového souboru s názvem `my-aci.yaml` a nahraďte komentářem hodnoty vlastními. Odkazovat na [šablona format] [šablona formant] pro platné YAML. Odkazovat [kontejneru úložišť a imagí][repositories-and-images] pro názvy dostupných imagí a jejich odpovídající úložiště.

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
imageRegistryCredentials:
  - server: containerpreview.azurecr.io
    username: # < The username for the preview container registry >
    password: # < The password for the preview container registry >
properties:
  containers:
  - name: # < Container name >
    properties:
      image: # < Repository/Image name >
      environmentVariables: # These env vars are required
        - name: eula
          value: accept
        - name: billing
          value: # < Service specific Endpoint URL >
        - name: apikey
          value: # < Service specific API key >
      resources:
        requests:
          cpu: 4 # Always refer to recommended minimal resources
          memoryInGb: 8 # Always refer to recommended minimal resources
      ports:
        - port: 5000
  osType: Linux
  restartPolicy: OnFailure
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 5000
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

> [!NOTE]
> Ne všechny umístění mají stejné skupině dostupnosti procesoru a paměti. Odkazovat [umístění a prostředky][location-to-resource] tabulky seznam dostupných prostředků pro kontejnery na umístění a verze operačního systému.

Budete spoléháme na soubor YAML, jsme vytvořili pro [ `az container create` ][azure-container-create] příkazu. Z Azure CLI, spusťte `az container create` nahrazení příkaz `<resource-group>` vlastními. Kromě toho zabezpečení hodnot v rámci YAML nasazení najdete v tématu [zabezpečení hodnoty][secure-values].

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

Výstup příkazu je `Running...` pokud platný, po určité době výstupu se změní na řetězec JSON představující nově vytvořeného prostředku ACI. Image kontejneru je více než pravděpodobně nebude k dispozici na dobu, ale teď se prostředek nasazuje.

> [!TIP]
> Pozornosti umístění nabídek ve verzi public preview služby Azure Cognitive Services, pro potřeby YAML se upraví tak, aby odpovídaly umístění.

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format

[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values