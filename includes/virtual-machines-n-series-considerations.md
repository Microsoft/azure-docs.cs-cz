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
ms.openlocfilehash: 3267f649e360c512a5523ce1d5948719a1969934
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32297582"
---
## <a name="deployment-considerations"></a>Aspekty nasazování

* Dostupnost virtuálních počítačů N-series, najdete v části [produkty podle oblasti](https://azure.microsoft.com/regions/services/).

* N-series virtuálních počítačů lze nasadit pouze v modelu nasazení Resource Manager.

* N-series virtuálních počítačů se liší v typ podporují pro jejich disky úložiště Azure. NC a virtuální počítače vs podporují jenom disky virtuálních počítačů, které jsou zajišťované pomocí standardní úložiště disku (HDD). NCv2 ND a virtuální počítače NCv3 podporují jenom disky virtuálních počítačů, které jsou zajišťované pomocí disku úložiště Premium (SSD).

* Pokud chcete nasadit víc než několik virtuálních počítačů N-series, zvažte průběžnými platbami předplatné nebo jiné možnosti nákupu. Pokud používáte [bezplatný účet Azure](https://azure.microsoft.com/free/), můžete použít pouze omezený počet výpočetních jader Azure.

* Může být nutné zvýšíte kvóty jader (podle oblasti) ve vašem předplatném Azure a samostatné kvótu pro počet jader NC NCv2, NCv3 ND nebo vs. Požádat o zvýšení kvóty, [otevřete žádosti o podporu online zákazníka](../articles/azure-supportability/how-to-create-azure-support-request.md) zdarma. Výchozí omezení se může lišit v závislosti na vaše předplatné kategorii.




