---
title: zahrnout soubor
description: zahrnout soubor
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/14/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: ee8ff3529524a63ca2e54a64327570197f363538
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "67175371"
---
| Resource | Basic | Standard | Premium |
|---|---|---|---|
| Úložiště<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| Maximální velikost vrstvy obrázku | 200 GiB | 200 GiB | 200 GiB |
| ReadOps za minutu<sup>2, 3</sup> | 1 000 | 3 000 | 10,000 |
| WriteOps za minutu<sup>2, 4</sup> | 100 | 500 | 2 000 |
| Šířka pásma pro stažení<sup>2</sup> MB/s | 30 | 60 | 100 |
| Šířka pásma při nahrávání<sup>2</sup> MB/s | 10 | 20 | 50 |
| webhooks | 2 | 10 | 100 |
| Geografická replikace | Není k dispozici | Není k dispozici | [Podporuje se][geo-replication] |
| Důvěryhodnost obsahu | Není k dispozici | Není k dispozici | [Podporuje se][content-trust] |

<sup>1</sup> Zadané limity úložiště představují velikost zahrnutého úložiště pro jednotlivé úrovně. Za úložiště imagí nad rámec těchto limitů se účtují další denní sazby za GiB. Informace o sazbách najdete v tématu [Azure Container Registry ceny][pricing].

<sup>2</sup> . *ReadOps*, *WriteOps*a *Šířka pásma* jsou minimální odhady. Azure Container Registry se snaží zvýšit výkon, protože vyžaduje použití.

<sup>3</sup> . [Docker Pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) se překládá na více operací čtení na základě počtu vrstev v imagi a načítání manifestu.

<sup>4</sup> . [Nabízená oznámení Docker](https://docs.docker.com/registry/spec/api/#pushing-an-image) se překládá na více operací zápisu na základě počtu vrstev, které musí být vloženy. A `docker push` obsahuje *ReadOps* pro načtení manifestu pro existující bitovou kopii.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
