---
title: Azure Container Registry obsahu formáty
description: Další informace o podporovaných formátů obsahu ve službě Azure Container Registry.
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 04/18/2019
ms.author: danlep
ms.openlocfilehash: fe129847e685c7151a9b7ad7ea65abbd38530733
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60827455"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Obsahu formáty podporované ve službě Azure Container Registry

Ke správě jednoho z následujících formátů obsahu použijte soukromé úložiště ve službě Azure Container Registry. 

## <a name="docker-compatible-container-images"></a>Image kontejnerů kompatibilní s dockerem

Jsou podporovány následující formáty image kontejneru Dockeru:

* [Verze 2 manifestu Image dockeru, schéma 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [V2 Manifest Image dockeru, schéma 2](https://docs.docker.com/registry/spec/manifest-v2-2/) – obsahuje Manifest uvádí, která jim umožňují registry k ukládání imagí více platforem v rámci jedné "image: tag" odkaz

## <a name="oci-images"></a>OCI imagí

Služba Azure Container Registry podporuje také bitové kopie, které splňují [specifikace formátu Image iniciativy otevřete kontejner (OCI)](https://github.com/opencontainers/image-spec/blob/master/spec.md). Balení formáty [Singularity bitové kopie formátu (FSP)](https://www.sylabs.io/2018/03/sif-containing-your-containers/).

## <a name="helm-charts"></a>Helmu

Služba Azure Container Registry můžete hostovat úložiště pro [grafy Helm](https://helm.sh/), balení formát používaný k rychlému spravovat a nasazovat aplikace pro Kubernetes. [Příkaz Helm klienta](https://docs.helm.sh/using_helm/#installing-helm) verze 2.11.0 nebo novější není podporováno.

## <a name="next-steps"></a>Další postup

* V tématu Jak [push a pull](container-registry-get-started-docker-cli.md) obrázků pomocí Azure Container Registry.

* Použití [ACR úlohy](container-registry-tasks-overview.md) pro vytváření a testování imagí kontejnerů. 

* Použití [Moby BuildKit](https://github.com/moby/buildkit) chcete sestavit a zabalit kontejnerů ve formátu OCI.

* Nastavení [do úložiště helmu](container-registry-helm-repos.md) hostované ve službě Azure Container Registry. 


