---
title: zahrnout soubor
description: zahrnout soubor
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 06/18/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 643cf8fd400adf06bf61f070947bd78ba7be50eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85242103"
---
| Prostředek | Basic | Standard | Premium |
|---|---|---|---|
| Zahrnuté úložiště<sup>1</sup> (GIB) | 10 | 100 | 500 |
| Limit úložiště (TiB) | 20| 20 | 20 |
| Maximální velikost vrstvy obrázku (GiB) | 200 | 200 | 200 |
| ReadOps za minutu<sup>2, 3</sup> | 1 000 | 3 000 | 10 000 |
| WriteOps za minutu<sup>2, 4</sup> | 100 | 500 | 2 000 |
| Šířka pásma pro stažení<sup>2</sup> MB/s | 30 | 60 | 100 |
| Šířka pásma při nahrávání<sup>2</sup> MB/s | 10 | 20 | 50 |
| Webhooky | 2 | 10 | 500 |
| Geografická replikace | Není k dispozici | Není k dispozici | [Doložen][geo-replication] |
| Důvěryhodnost obsahu | Není k dispozici | Není k dispozici | [Doložen][content-trust] |
| Privátní odkaz s privátními koncovými body | Není k dispozici | Není k dispozici | [Doložen][plink] |
| Přístup k virtuální síti koncového bodu služby | Není k dispozici | Není k dispozici | [Preview][vnet] |
| Klíče spravované zákazníkem | Není k dispozici | Není k dispozici | [Doložen][cmk] |
| Oprávnění rozsahu úložiště | Není k dispozici | Není k dispozici | [Preview][token]|
| &bull;Klíčov | Není k dispozici | Není k dispozici | 20 000 |
| &bull;Mapy oboru | Není k dispozici | Není k dispozici | 20 000 |
| &bull;Mapování úložišť na obor | Není k dispozici | Není k dispozici | 500 |


<sup>1</sup> úložiště zahrnuté do denní sazby pro každou úroveň. V případě dalšího úložiště se vám bude účtovat další denní sazba za GiB, a to až do limitu úložiště. Informace o sazbách najdete v tématu [Azure Container Registry ceny][pricing].

<sup>2</sup>*ReadOps*, *WriteOps*a *Šířka pásma* jsou minimální odhady. Azure Container Registry se snaží zvýšit výkon, protože vyžaduje použití.

<sup>3</sup> . [Docker Pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) se překládá na více operací čtení na základě počtu vrstev v imagi a načítání manifestu.

<sup>4</sup> . [Nabízená oznámení Docker](https://docs.docker.com/registry/spec/api/#pushing-an-image) se překládá na více operací zápisu na základě počtu vrstev, které musí být vloženy. A `docker push` obsahuje *ReadOps* pro načtení manifestu pro existující bitovou kopii.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[plink]: ../articles/container-registry/container-registry-private-link.md
[cmk]: ../articles/container-registry/container-registry-customer-managed-keys.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md