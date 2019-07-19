---
title: Azure Container Registry formáty obsahu
description: Přečtěte si o podporovaných formátech obsahu v Azure Container Registry.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 04/18/2019
ms.author: danlep
ms.openlocfilehash: c804dd7b73ea4a51c02c8b342a4ac60d992ec7c5
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310676"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Formáty obsahu podporované v Azure Container Registry

Pomocí privátního úložiště v Azure Container Registry můžete spravovat jeden z následujících formátů obsahu. 

## <a name="docker-compatible-container-images"></a>Image kontejneru kompatibilní s Docker

Podporují se tyto formáty imagí kontejneru Docker:

* [Manifest Docker image v2, schéma 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Manifest Docker image v2, schéma 2](https://docs.docker.com/registry/spec/manifest-v2-2/) – obsahuje seznam manifestů, které umožňují registrům ukládat multiplatformní image pod jedinou referencí "Image: tag".

## <a name="oci-images"></a>Image OCI

Azure Container Registry podporuje i image, které splňují [specifikaci formátu rozhraní OCI (Open container Initiative)](https://github.com/opencontainers/image-spec/blob/master/spec.md). Formáty balení zahrnují [Formát (SIF) pro jednotné](https://www.sylabs.io/2018/03/sif-containing-your-containers/)přidanou bitovou kopii.

## <a name="helm-charts"></a>Grafy Helm

Azure Container Registry mohou hostovat úložiště pro [grafy Helm](https://helm.sh/), formát balení používaný k rychlé správě a nasazování aplikací pro Kubernetes. Podpora [klienta Helm](https://docs.helm.sh/using_helm/#installing-helm) verze 2.11.0 nebo vyšší je podporována.

## <a name="next-steps"></a>Další postup

* Podívejte se [, jak vyžádat a načíst](container-registry-get-started-docker-cli.md) obrázky pomocí Azure Container Registry.

* Použijte [úlohy ACR](container-registry-tasks-overview.md) k sestavení a testování imagí kontejneru. 

* Použijte [Moby BuildKit](https://github.com/moby/buildkit) k sestavení a balíčků kontejnerů ve formátu OCI.

* Nastavte [úložiště Helm](container-registry-helm-repos.md) hostované v Azure Container Registry. 


