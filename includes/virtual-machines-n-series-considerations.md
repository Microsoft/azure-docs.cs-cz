---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 03/19/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: c1e57ea28f597293d8bb52207bbbb76892b1d5f5
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2018
---
## <a name="deployment-considerations"></a>Aspekty nasazování

* Dostupnost virtuálních počítačů N-series, najdete v části [produkty podle oblasti](https://azure.microsoft.com/en-us/regions/services/).

* N-series virtuálních počítačů lze nasadit pouze v modelu nasazení Resource Manager.

* N-series virtuálních počítačů se liší v typ podporují pro jejich disky úložiště Azure. NC a virtuální počítače vs podporují jenom disky virtuálních počítačů, které jsou zajišťované pomocí standardní úložiště disku (HDD). NCv2 ND a virtuální počítače NCv3 podporují jenom disky virtuálních počítačů, které jsou zajišťované pomocí disku úložiště Premium (SSD).

* Pokud chcete nasadit víc než několik virtuálních počítačů N-series, zvažte průběžnými platbami předplatné nebo jiné možnosti nákupu. Pokud používáte [bezplatný účet Azure](https://azure.microsoft.com/free/), můžete použít pouze omezený počet výpočetních jader Azure.

* Může být nutné zvýšíte kvóty jader (podle oblasti) ve vašem předplatném Azure a samostatné kvótu pro počet jader NC NCv2, NCv3 ND nebo vs. Požádat o zvýšení kvóty, [otevřete žádosti o podporu online zákazníka](../articles/azure-supportability/how-to-create-azure-support-request.md) zdarma. Výchozí omezení se může lišit v závislosti na vaše předplatné kategorii.




