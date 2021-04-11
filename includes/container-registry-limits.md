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
ms.openlocfilehash: b4701260a7d8da030f9f3019060aaa83e7a3a483
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803592"
---
| Prostředek | Basic | Standard | Premium |
|---|---|---|---|
| Zahrnuté úložiště<sup>1</sup> (GIB) | 10 | 100 | 500 |
| Limit úložiště (TiB) | 20| 20 | 20 |
| Maximální velikost vrstvy obrázku (GiB) | 200 | 200 | 200 |
| ReadOps za minutu<sup>2, 3</sup> | 1 000 | 3 000 | 10 000 |
| WriteOps za minutu<sup>2, 4</sup> | 100 | 500 | 2 000 |
| Stáhnout šířku pásma<sup>2</sup> (MB/s) | 30 | 60 | 100 |
| Nahrát šířku pásma <sup>2</sup> (MB/s) | 10 | 20 | 50 |
| Webhooky | 2 | 10 | 500 |
| Geografická replikace | N/A | N/A | [Podporováno][geo-replication] |
| Zóny dostupnosti | N/A | N/A | [Preview][zones] |
| Důvěryhodnost obsahu | N/A | N/A | [Podporováno][content-trust] |
| Privátní odkaz s privátními koncovými body | N/A | N/A | [Podporováno][plink] |
| &bull; Soukromé koncové body | N/A | N/A | 10 |
| Pravidla sítě veřejných IP adres | N/A | N/A | 100 |
| Přístup k virtuální síti koncového bodu služby | N/A | N/A | [Preview][vnet] |
| Klíče spravované zákazníkem | N/A | N/A | [Podporováno][cmk] |
| Oprávnění rozsahu úložiště | N/A | N/A | [Preview][token]|
| &bull; Klíčov | N/A | N/A | 20 000 |
| &bull; Mapy oboru | N/A | N/A | 20 000 |
| &bull; Mapování úložišť na obor | N/A | N/A | 500 |


<sup>1</sup> úložiště zahrnuté do denní sazby pro každou úroveň. Další úložiště může být využito až do limitu úložiště v registru za další denní sazbu za GiB. Informace o sazbách najdete v tématu [Azure Container Registry ceny][pricing]. Pokud potřebujete úložiště nad rámec limitu úložiště registru, obraťte se prosím na podporu Azure.

<sup>2</sup>*ReadOps*, *WriteOps* a *Šířka pásma* jsou minimální odhady. Azure Container Registry se snaží zvýšit výkon, protože vyžaduje použití.

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
[zones]: ../articles/container-registry/zone-redundancy.md
