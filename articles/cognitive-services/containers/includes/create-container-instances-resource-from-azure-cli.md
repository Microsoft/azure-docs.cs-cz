---
title: Podpora kontejnerů
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak vytvořit prostředek Azure Container instance z Azure CLI.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 32b00e031e3cf865093c267084117a8704b6e272
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800083"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Vytvoření prostředku Azure Container instance z Azure CLI

Následující YAML definuje prostředek instance kontejneru Azure. Zkopírujte a vložte obsah do nového souboru s názvem `my-aci.yaml` a nahraďte hodnoty s poznámkami vlastními. Platný YAML najdete ve [formátu šablony][template-format] . Podívejte se na [úložiště kontejnerů a obrázky][repositories-and-images] pro dostupné názvy imagí a jejich odpovídající úložiště. Další informace o YAML reference pro instance kontejnerů naleznete v tématu [reference YAML: Azure Container Instances][aci-yaml-ref].

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
properties:
  imageRegistryCredentials: # This is only required if you are pulling a non-public image that requires authentication to access. For example Text Analytics for health.
  - server: containerpreview.azurecr.io
    username: # < The username for the preview container registry >
    password: # < The password for the preview container registry >
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
  volumes: # This node, is only required for container instances that pull their model in at runtime, such as LUIS.
  - name: aci-file-share
    azureFile:
      shareName: # < File share name >
      storageAccountName: # < Storage account name>
      storageAccountKey: # < Storage account key >
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
> Ne všechna umístění mají stejnou dostupnost procesoru a paměti. Seznam dostupných prostředků pro kontejnery na umístění a operační systém najdete v tabulce [umístění a prostředky][location-to-resource] .

Budeme spoléhat na soubor YAML, který jsme vytvořili pro [`az container create`][azure-container-create] příkaz. V Azure CLI spusťte `az container create` příkaz, který nahradí `<resource-group>` vlastní. Kromě toho se pro zabezpečení hodnot v rámci nasazení YAML odkazují na [zabezpečené hodnoty][secure-values].

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

Výstup příkazu je v `Running...` případě, že se výstup změní na řetězec JSON, který představuje nově vytvořený prostředek ACI, po nějaké době. Bitová kopie kontejneru je větší, než je pravděpodobně k dispozici v době běhu, ale prostředek je nyní nasazen.

> [!TIP]
> Zaměřte se na umístění veřejné verze Preview nabídky služby pro rozpoznávání Azure ve verzi Public Preview, protože YAML bude nutné přizpůsobit tak, aby odpovídala umístění.

[azure-container-create]: /cli/azure/container#az_container_create
[template-format]: /azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format
[aci-yaml-ref]: ../../../container-instances/container-instances-reference-yaml.md
[repositories-and-images]: ../container-image-tags.md
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
