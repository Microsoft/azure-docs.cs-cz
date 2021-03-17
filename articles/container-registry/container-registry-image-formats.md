---
title: Podporované formáty obsahu
description: Přečtěte si o formátech obsahu podporovaných nástrojem Azure Container Registry, včetně imagí kontejneru kompatibilních s Docker, Helm grafy, image OCI a artefakty OCI.
ms.topic: article
ms.date: 08/30/2019
ms.openlocfilehash: b2a54c65d149a27ed9eae85c3308d657ed3471a3
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2021
ms.locfileid: "100008328"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Formáty obsahu podporované v Azure Container Registry

Pomocí privátního úložiště v Azure Container Registry můžete spravovat jeden z následujících formátů obsahu. 

## <a name="docker-compatible-container-images"></a>Image kontejneru kompatibilní s Docker

Podporují se tyto formáty imagí kontejneru Docker:

* [Manifest Docker image v2, schéma 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Manifest Docker image v2, schéma 2](https://docs.docker.com/registry/spec/manifest-v2-2/) – obsahuje seznam manifestů, které umožňují registrům ukládat [image s více architekturami](push-multi-architecture-images.md) pod jedinou `image:tag` referencí.

## <a name="oci-images"></a>Image OCI

Azure Container Registry podporuje image, které splňují [specifikaci formátu rozhraní OCI (Open container Initiative)](https://github.com/opencontainers/image-spec/blob/master/spec.md), včetně specifikace volitelného [indexu obrázku](https://github.com/opencontainers/image-spec/blob/master/image-index.md) . Formáty balení zahrnují [Formát (SIF) pro jednotné přidanou bitovou kopii](https://github.com/sylabs/sif).

## <a name="oci-artifacts"></a>Artefakty OCI

Azure Container Registry podporuje pro ukládání, sdílení, zabezpečení a nasazování imagí kontejnerů a dalších typů obsahu (artefakty) nezávisláou [specifikaci distribuce OCI](https://github.com/opencontainers/distribution-spec), což je neutrální od dodavatele. Specifikace umožňuje registru kromě imagí kontejnerů ukládat široké spektrum artefaktů. Použijete nástroje vhodné pro artefakt k nasdílení a vyžádání artefaktů. Příklad najdete v tématu [push a pull artefaktu OCI pomocí služby Azure Container Registry](container-registry-oci-artifacts.md).

Další informace o artefaktech rozhraní OCI najdete v části [registr OCI jako úložiště (Oras)](https://github.com/deislabs/oras) a v úložišti [artefaktů OCI](https://github.com/opencontainers/artifacts) na GitHubu.

## <a name="helm-charts"></a>Charty Helm

Azure Container Registry mohou hostovat úložiště pro [grafy Helm](https://helm.sh/), formát balení používaný k rychlé správě a nasazování aplikací pro Kubernetes. Podporuje se [klient Helm](https://docs.helm.sh/using_helm/#installing-helm) verze 2 (2.11.0 nebo novější).

## <a name="next-steps"></a>Další kroky

* Podívejte se [, jak vyžádat a načíst](container-registry-get-started-docker-cli.md) obrázky pomocí Azure Container Registry.

* Použijte [úlohy ACR](container-registry-tasks-overview.md) k sestavení a testování imagí kontejneru. 

* Použijte [Moby BuildKit](https://github.com/moby/buildkit) k sestavení a balíčků kontejnerů ve formátu OCI.

* Nastavte [úložiště Helm](container-registry-helm-repos.md) hostované v Azure Container Registry. 


