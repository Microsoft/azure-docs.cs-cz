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
ms.openlocfilehash: 6707e844948ac76d4cec29faf69d80b3c9cb3c0f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75392439"
---
| Prostředek | Úroveň Basic | Úroveň Standard | Premium |
|---|---|---|---|
| Úložiště<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| Maximální velikost vrstvy obrázku | 200 GiB | 200 GiB | 200 GiB |
| ReadOps za minutu<sup>2, 3</sup> | 1 000 | 3000 | 10 000 |
| WriteOps za minutu<sup>2, 4</sup> | 100 | 500 | 2 000 |
| Šířka pásma pro stažení<sup>2</sup> MB/s | 30 | 60 | 100 |
| Šířka pásma při nahrávání<sup>2</sup> MB/s | 10 | 20 | 50 |
| Webhooky | 2 | 10 | 500 |
| Geografická replikace | Nevztahuje se | Nevztahuje se | [Podporuje se][geo-replication] |
| Důvěryhodnost obsahu | Nevztahuje se | Nevztahuje se | [Podporuje se][content-trust] |
| Přístup k virtuální síti | Nevztahuje se | Nevztahuje se | [Preview][vnet] |
| Oprávnění rozsahu úložiště | Nevztahuje se | Nevztahuje se | [Preview][token]|
| Tokeny &bull; | Nevztahuje se | Nevztahuje se | 20,000 |
| mapy oboru &bull; | Nevztahuje se | Nevztahuje se | 20,000 |
| mapování &bull; úložišť na obor | Nevztahuje se | Nevztahuje se | 500 |


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