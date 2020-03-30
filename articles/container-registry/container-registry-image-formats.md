---
title: Podporované formáty obsahu
description: Další informace o formátech obsahu podporovaných azure container registry, včetně ibi kontejnerů kompatibilních s Dockerem, grafů helmu, ibiobrazů OCI a artefaktů OCI.
ms.topic: article
ms.date: 08/30/2019
ms.openlocfilehash: ab915385f46f83c7b655acd1a48d66df84b50653
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247004"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Formáty obsahu podporované v registru kontejnerů Azure

Ke správě jednoho z následujících formátů obsahu použijte privátní úložiště v registru kontejnerů Azure. 

## <a name="docker-compatible-container-images"></a>Ifotky kontejnerů kompatibilní s Dockerem

Podporovány jsou následující formáty obrázků kontejneru Dockeru:

* [Manifest obrázku Dockeru V2, schéma 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker Image Manifest V2, Schema 2](https://docs.docker.com/registry/spec/manifest-v2-2/) - obsahuje seznamy manifestů, které umožňují registrům ukládat víceplatformové bitové kopie pod jedním odkazem "image:tag"

## <a name="oci-images"></a>Obrázky OCI

Azure Container Registry podporuje image, které splňují [specifikace formátu image Open Container Initiative (OCI).](https://github.com/opencontainers/image-spec/blob/master/spec.md) Formáty balení zahrnují [formát singularity image format (SIF).](https://github.com/sylabs/sif)

## <a name="oci-artifacts"></a>Artefakty OCI

Azure Container Registry podporuje [specifikace distribuce OCI](https://github.com/opencontainers/distribution-spec), dodavateleneutrální, cloud-agnostik spec pro ukládání, sdílení, zabezpečení a nasazení ibi kontejnerů a další typy obsahu (artefakty). Specifikace umožňuje registru ukládat širokou škálu artefaktů kromě image kontejneru. Pomocí nástrojů vhodných pro artefakt push a pull artefakty. Například najdete v [tématu Push and pull artefakt OCI pomocí registru kontejneru Azure](container-registry-oci-artifacts.md).

Další informace o artefaktech OCI najdete v [úložišti OCI Registry as Storage (ORAS)](https://github.com/deislabs/oras) a úložišti [Artefakty OCI](https://github.com/opencontainers/artifacts) na GitHubu.

## <a name="helm-charts"></a>Kormidelníky

Azure Container Registry může hostovat úložiště pro [helmgrafy](https://helm.sh/), formát balení slouží k rychlé správě a nasazení aplikací pro Kubernetes. [Helm klient](https://docs.helm.sh/using_helm/#installing-helm) verze 2 (2.11.0 nebo novější) je podporován.

## <a name="next-steps"></a>Další kroky

* Podívejte se, jak [stlačovat a vytahovat](container-registry-get-started-docker-cli.md) image pomocí Azure Container Registry.

* Pomocí [úloh ACR](container-registry-tasks-overview.md) můžete vytvářet a testovat image kontejnerů. 

* Pomocí [Moby BuildKit](https://github.com/moby/buildkit) vytvářet a balíčkovat kontejnery ve formátu OCI.

* Nastavte [úložiště Helm](container-registry-helm-repos.md) hostované v registru kontejnerů Azure. 


