---
title: zahrnout soubor
description: zahrnout soubor
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 05/29/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 92a5d162e7a0b2c752a2f8e9c5941edf43e539e3
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991073"
---
| Prostředek | Basic | Standard | Premium |
|---|---|---|---|---|
| Úložiště | 10 GiB | 100 GiB| 500 GiB |
| Maximální velikost vrstvy image | 20 GB | 20 GB | 50 GB |
| Operace čtení za minutu<sup>1, 2</sup> | 1 000 | 3000 | 10 000 |
| Operace za minutu<sup>1, 3</sup> | 100 | 500 | 2,000 |
| Stáhněte si MB/s šířky pásma<sup>1</sup> | 30 | 60 | 100 |
| Nahrát MB/s šířky pásma<sup>1</sup> | 10 | 20 | 50 |
| Webhooky | 2 | 10 | 100 |
| Geografická replikace | neuvedeno | neuvedeno | [Podporuje se](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication) |

<sup>1</sup> *operace čtení*, *operace*, a *šířky pásma* jsou minimální odhady. Služby ACR se snaží zlepšovat výkon podle využití.

<sup>2</sup> [operace docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) se přeloží na více operací čtení na základě počtu vrstvy v obrázku a načtení manifestu.

<sup>3</sup> [docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) se přeloží na více operací zápisu na základě počtu vrstvy, které musí být nahrány. A `docker push` zahrnuje *operace čtení* k načtení manifestu pro stávající bitovou kopii.
