---
title: zahrnout soubor
description: zahrnout soubor
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 03/23/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 575483192954f4e05db50e701e223829e041cffc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
| Prostředek | Basic | Standard | Premium |
|---|---|---|---|---|
| Úložiště | 10 GiB | 100 GiB| 500 GiB |
| ReadOps za minutu<sup>1, 2</sup> | 1000 | 3000 | 10000 |
| WriteOps za minutu<sup>1, 3</sup> | 100 | 500 | 2000 |
| Stáhnout MB/s šířky pásma<sup>1</sup> | 30 | 60 | 100 |
| Nahrát MB/s šířky pásma<sup>1</sup> | 10 | 20 | 50 |
| Webhooky | 2 | 10 | 100 |
| Geografická replikace | neuvedeno | neuvedeno | [Podporované *(preview)*](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication) |

<sup>1</sup> *ReadOps*, *WriteOps*, a *šířky pásma* jsou odhady minimální. ACR se snaží zlepšit výkon, protože vyžaduje použití.

<sup>2</sup> [docker vyžádání](https://docs.docker.com/registry/spec/api/#pulling-an-image) překládá do více operací čtení na základě počtu vrstev v bitovou kopii a načtení manifestu.

<sup>3</sup> [docker nabízené](https://docs.docker.com/registry/spec/api/#pushing-an-image) překládá do více operací zápisu na základě počtu vrstvy, které musí být posunuta. A `docker push` zahrnuje *ReadOps* k načtení manifestu pro stávající image.