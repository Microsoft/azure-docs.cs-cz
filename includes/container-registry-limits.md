---
title: zahrnout soubor
description: zahrnout soubor
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 11/05/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: bc4ac68cb415a43ac34d36afc2adc30307e6d37c
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795257"
---
| Prostředek | Basic | Standard | Premium |
|---|---|---|---|
| Úložiště<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| Maximální velikost vrstvy obrázku | 200 GiB | 200 GiB | 200 GiB |
| ReadOps za minutu<sup>2, 3</sup> | 1 000 | 3000 | 10 000 |
| WriteOps za minutu<sup>2, 4</sup> | 100 | 500 | 2 000 |
| Šířka pásma pro stažení<sup>2</sup> MB/s | 30 | 60 | 100 |
| Šířka pásma při nahrávání<sup>2</sup> MB/s | 10 | 20 | 50 |
| Webhooky | 2 | 10 | 100 |
| Geografická replikace | Není dostupné. | Není dostupné. | [Podporuje se][geo-replication] |
| Důvěryhodnost obsahu | Není dostupné. | Není dostupné. | [Podporuje se][content-trust] |
| Přístup k virtuální síti | Není dostupné. | Není dostupné. | [Preview][vnet] |
| Oprávnění rozsahu úložiště | Není dostupné. | Není dostupné. | [Preview][token]|
| Tokeny &bull; | Není dostupné. | Není dostupné. | 20 000 |
| mapy oboru &bull; | Není dostupné. | Není dostupné. | 20 000 |
| mapování &bull; úložišť na obor | Není dostupné. | Není dostupné. | 500 |


<sup>1</sup> Zadané limity úložiště představují velikost *zahrnutého* úložiště pro jednotlivé úrovně. Za úložiště imagí nad rámec těchto limitů se účtují další denní sazby za GiB. Informace o sazbách najdete v tématu [Azure Container Registry ceny][pricing].

<sup>2</sup>*ReadOps*, *WriteOps*a *Šířka pásma* jsou minimální odhady. Azure Container Registry se snaží zvýšit výkon, protože vyžaduje použití.

<sup>3</sup> . [Docker Pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) se překládá na více operací čtení na základě počtu vrstev v imagi a načítání manifestu.

<sup>4</sup> . [Nabízená oznámení Docker](https://docs.docker.com/registry/spec/api/#pushing-an-image) se překládá na více operací zápisu na základě počtu vrstev, které musí být vloženy. `docker push` obsahuje *ReadOps* pro načtení manifestu pro existující bitovou kopii.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md