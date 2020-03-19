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
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79117151"
---
| Prostředek | Basic | Standard | Premium |
|---|---|---|---|
| Úložiště<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| Maximální velikost vrstvy image | 200 GiB | 200 GiB | 200 GiB |
| Operace čtení za minutu<sup>2, 3</sup> | 1 000 | 3 000 | 10 000 |
| Operace zápisu za minutu<sup>2, 4</sup> | 100 | 500 | 2 000 |
| Šířka pásma pro stahování v MB/s<sup>2</sup> | 30 | 60 | 100 |
| Šířka pásma pro nahrávání v MB/s<sup>2</sup> | 10 | 20 | 50 |
| Webhooky | 2 | 10 | 500 |
| Geografická replikace | – | – | [Podporuje se][geo-replication] |
| Důvěryhodnost obsahu | – | – | [Podporuje se][content-trust] |
| Přístup k virtuální síti | – | – | [Preview][vnet] |
| Integrace služby Private Link | – | – | [Preview][plink] |
| Klíče spravované zákazníkem | – | – | [Preview][cmk] |
| Oprávnění vymezená úložištěm | – | – | [Preview][token]|
| &bull; Tokeny | – | – | 20 000 |
| &bull; Mapy oborů | – | – | 20 000 |
| &bull; Úložiště na mapu oborů | – | – | 500 |


<sup>1</sup> Uvedené limity úložiště představují velikost *zahrnutého* úložiště pro jednotlivé úrovně. Za úložiště imagí nad rámec těchto limitů se vám bude navíc účtovat denní sazba za GiB. Informace o sazbách najdete v tématu [Ceny služby Azure Container Registry][pricing].

<sup>2</sup> *Operace čtení*, *operace zápisu* a *šířka pásma* představují minimální odhady. Azure Container Registry se snaží zvyšovat výkon s ohledem na využití.

<sup>3</sup> Příkaz [docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) se překládá na několik operací čtení v závislosti na počtu vrstev v imagi a na načtení manifestu.

<sup>4</sup> Příkaz [docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) se překládá na několik operací zápisu v závislosti na počtu vrstev, které je potřeba odeslat. Příkaz `docker push` zahrnuje *operace čtení* umožňující načtení manifestu pro stávající image.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[plink]: ../articles/container-registry/container-registry-private-link.md
[cmk]: ../articles/container-registry/container-registry-customer-managed-keys.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md