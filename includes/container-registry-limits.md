---
title: zahrnout soubor
description: zahrnout soubor
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 08/30/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: bddb17a5163333a5aeb86b296a21867f0611d12f
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43302522"
---
| Prostředek | Basic | Standard | Premium |
|---|---|---|---|---|
| Úložiště<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| Maximální velikost vrstvy image | 20 GB | 20 GB | 50 GB |
| Operace čtení za minutu<sup>2, 3</sup> | 1 000 | 3000 | 10 000 |
| Operace za minutu<sup>2, 4</sup> | 100 | 500 | 2,000 |
| Stáhněte si MB/s šířky pásma<sup>2</sup> | 30 | 60 | 100 |
| Nahrát MB/s šířky pásma<sup>2</sup> | 10 | 20 | 50 |
| Webhooky | 2 | 10 | 100 |
| Geografická replikace | neuvedeno | neuvedeno | [Podporované][geo-replication] |
| Důvěryhodnost obsahu (Preview) | neuvedeno | neuvedeno | [Podporované][content-trust] |

<sup>1</sup> zadané omezení jsou množství *zahrnuté* úložiště pro jednotlivé úrovně. Vám budou účtovány další denní sazba za GiB pro úložiště obrázků nad tyto limity. Míra informace najdete v tématu [Container Registry ceny][pricing].

<sup>2</sup> *operace čtení*, *operace*, a *šířky pásma* jsou minimální odhady. Služby ACR se snaží zlepšovat výkon podle využití.

<sup>3</sup> [operace docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) se přeloží na více operací čtení na základě počtu vrstvy v obrázku a načtení manifestu.

<sup>4</sup> [docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) se přeloží na více operací zápisu na základě počtu vrstvy, které musí být nahrány. A `docker push` zahrnuje *operace čtení* k načtení manifestu pro stávající bitovou kopii.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md