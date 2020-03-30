---
title: zahrnout soubor
description: zahrnout soubor
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 03/11/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0090f02382e024e5539383328b55d58798002d63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79117151"
---
| Prostředek | Basic | Standard | Premium |
|---|---|---|---|
| Skladování<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| Maximální velikost vrstvy obrazu | 200 GiB | 200 GiB | 200 GiB |
| ReadOps za minutu<sup>2, 3</sup> | 1 000 | 3 000 | 10 000 |
| WriteOps za minutu<sup>2, 4</sup> | 100 | 500 | 2 000 |
| Stáhnout šířku pásma Mb/s<sup>2</sup> | 30 | 60 | 100 |
| Nahrát šířku pásma Mb/s<sup>2</sup> | 10 | 20 | 50 |
| Webhooky | 2 | 10 | 500 |
| Geografická replikace | Není dostupné. | Není dostupné. | [Podporovány][geo-replication] |
| Důvěryhodnost obsahu | Není dostupné. | Není dostupné. | [Podporovány][content-trust] |
| Přístup k virtuální síti | Není dostupné. | Není dostupné. | [Náhled][vnet] |
| Integrace privátního propojení | Není dostupné. | Není dostupné. | [Náhled][plink] |
| Klíče spravované zákazníkem | Není dostupné. | Není dostupné. | [Náhled][cmk] |
| Oprávnění s rozsahem úložiště | Není dostupné. | Není dostupné. | [Náhled][token]|
| &bull;Tokeny | Není dostupné. | Není dostupné. | 20 000 |
| &bull;Mapy oboru | Není dostupné. | Není dostupné. | 20 000 |
| &bull;Úložiště podle mapy oboru | Není dostupné. | Není dostupné. | 500 |


<sup>1.</sup> Zadané limity úložiště jsou velikost *zahrnuté* úložiště pro každou vrstvu. Za úložiště obrázků nad tyto limity se vám účtuje další denní sazba za GiB. Informace o sazbách najdete v [tématu Azure Container Registry pricing][pricing].

<sup>2</sup>*ReadOps*, *WriteOps*a *Bandwidth* jsou minimální odhady. Azure Container Registry se snaží zlepšit výkon podle potřeby využití.

<sup>3.</sup> Docker [pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) překládá na více operací čtení na základě počtu vrstev v obraze, plus načtení manifestu.

<sup>4.</sup> Docker [push](https://docs.docker.com/registry/spec/api/#pushing-an-image) překládá na více operací zápisu, na základě počtu vrstev, které musí být posunuty. A `docker push` obsahuje *ReadOps* načíst manifest pro existující image.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[plink]: ../articles/container-registry/container-registry-private-link.md
[cmk]: ../articles/container-registry/container-registry-customer-managed-keys.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md