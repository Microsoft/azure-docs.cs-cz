---
title: Podpora kontejnerů
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak vytvořit prostředek instance kontejneru Azure z azure cli.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e3542b976921aa45794d62cad9517984c8348ce3
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875104"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Vytvoření prostředku instance kontejneru Azure z azure cli

Níže uvedený yaml definuje prostředek instance kontejneru Azure. Zkopírujte a vložte obsah do `my-aci.yaml` nového souboru s názvem a nahraďte komentované hodnoty vlastními. Platný formát YAML naleznete ve [formátu šablony.][template-format] Názvy dostupných obrázků a jejich odpovídající úložiště naleznete v [úložišti kontejnerů a na obrázcích.][repositories-and-images] Další informace o odkazu YAML pro instance kontejnerů najdete v [tématu Odkaz YAML: Instance kontejnerů Azure][aci-yaml-ref].

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
imageRegistryCredentials: # This is required when pulling a non-public image
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
> Ne všechna umístění mají stejnou dostupnost procesoru a paměti. Seznam dostupných prostředků pro kontejnery pro umístění a operační operační režim naleznete v tabulce [umístění a zdrojů.][location-to-resource]

Budeme se spoléhat na soubor YAML, který jsme vytvořili [`az container create`][azure-container-create] pro příkaz. Z azure cli, `az container create` spusťte `<resource-group>` příkaz nahrazení vlastní. Navíc pro zabezpečení hodnot v rámci nasazení YAML odkazují na [zabezpečené hodnoty][secure-values].

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

Výstup příkazu je, pokud je `Running...` platný, po někdy se výstup změní na řetězec JSON představující nově vytvořený prostředek ACI. Image kontejneru je více než pravděpodobné, že není k dispozici na chvíli, ale prostředek je nyní nasazen.

> [!TIP]
> Věnujte velkou pozornost umístění nabídky služby Azure Cognitive Service ve verzi Public Preview, protože yaml bude potřeba odpovídajícím způsobem upravit tak, aby odpovídalumístění.

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format
[aci-yaml-ref]: ../../../container-instances/container-instances-reference-yaml.md
[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
