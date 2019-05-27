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
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66148964"
---
| Resource | Basic | Standard | Premium |
|---|---|---|---|
| Úložiště<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| Maximální velikost vrstvy | 200 GiB | 200 GiB | 200 GiB |
| Operace čtení za minutu<sup>2, 3</sup> | 1 000 | 3 000 | 10,000 |
| Operace za minutu<sup>2, 4</sup> | 100 | 500 | 2 000 |
| Stáhněte si MB/s šířky pásma<sup>2</sup> | 30 | 60 | 100 |
| Nahrát MB/s šířky pásma<sup>2</sup> | 10 | 20 | 50 |
| Webhooky | 2 | 10 | 100 |
| Geografická replikace | neuvedeno | neuvedeno | [Podporované][geo-replication] |
| Důvěryhodnost obsahu | neuvedeno | neuvedeno | [Podporované][content-trust] |

<sup>1</sup>zadané omezení jsou množství *zahrnuté* úložiště pro jednotlivé úrovně. Vám budou účtovány další denní sazba za GiB pro úložiště obrázků nad tyto limity. Míra informace najdete v tématu [ceny Azure Container Registry][pricing].

<sup>2</sup>*operace čtení*, *operace*, a *šířky pásma* jsou minimální odhady. Služba Azure Container Registry se snaží zlepšovat výkon podle využití.

<sup>3</sup>A [operace docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) se přeloží na více operací čtení na základě počtu vrstvy v obrázku a načtení manifestu.

<sup>4</sup>A [docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) se přeloží na více operací zápisu na základě počtu vrstvy, které musí být nahrány. A `docker push` zahrnuje *operace čtení* k načtení manifestu pro stávající bitovou kopii.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
