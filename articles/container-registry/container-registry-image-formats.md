---
title: Azure Container Registry obsahu formáty
description: Další informace o podporovaných formátů obsahu ve službě Azure Container Registry.
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 10/31/2018
ms.author: danlep
ms.openlocfilehash: e7155604339bc634078fd022e05ede5f902bc0d8
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634697"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Obsahu formáty podporované ve službě Azure Container Registry

Ke správě jednoho z následujících formátů obsahu použijte soukromé úložiště ve službě Azure Container Registry. 

## <a name="docker-compatible-container-images"></a>Image kontejnerů kompatibilní s dockerem

* [Verze 2 manifestu Image dockeru, schéma 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [V2 Manifest Image dockeru, schéma 2](https://docs.docker.com/registry/spec/manifest-v2-2/) – obsahuje Manifest uvádí, která jim umožňují registry k ukládání imagí více platforem v rámci jedné "image: tag" odkaz

* [Otevřete specifikace formátu Image kontejneru iniciativy (OCI)](https://github.com/opencontainers/image-spec/blob/master/spec.md) 


## <a name="helm-charts"></a>Helmu

Služba Azure Container Registry se můžou hostovat taky úložiště pro [grafy Helm](https://helm.sh/), balení formát používaný k rychlému spravovat a nasazovat aplikace pro Kubernetes. [Příkaz Helm klienta](https://docs.helm.sh/using_helm/#installing-helm) verze 2.11.0 nebo novější není podporováno.

## <a name="next-steps"></a>Další postup

* V tématu Jak [push a pull](container-registry-get-started-docker-cli.md) obrázků pomocí Azure Container Registry.

* Použití [ACR úlohy](container-registry-tasks-overview.md) pro vytváření a testování imagí kontejnerů. 

* Použití [Moby BuildKit](https://github.com/moby/buildkit) chcete sestavit a zabalit kontejnerů ve formátu OCI.

* Nastavení [do úložiště helmu](container-registry-helm-repos.md) hostované ve službě Azure Container Registry. 


