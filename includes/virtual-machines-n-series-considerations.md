---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 06/19/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 1de7221f100077e07a2211bdb94e0198b35cb77c
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51716153"
---
## <a name="deployment-considerations"></a>Aspekty nasazování

* Dostupnost virtuálních počítačů řady N-series, naleznete v tématu [dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/regions/services/).

* Virtuální počítače řady N-series je možné nasadit jenom v modelu nasazení Resource Manager.

* Virtuální počítače řady N-series se liší v typu Azure Storage podporují pro jejich disků. NC a NV virtuální počítače podporují jenom disky virtuálních počítačů, které využívají pomocí standardní úložiště disku (HDD). NCv2, NCv3, ND, NDv2 a NVv2 virtuální počítače podporují jenom disky virtuálních počítačů, které se zálohují na podle Disk Storage úrovně Premium (SSD).

* Pokud chcete nasadit více než několik virtuálních počítačů řady N-series, zvažte předplatné s průběžnými platbami nebo jiné možnosti nákupu. Pokud používáte [bezplatný účet Azure](https://azure.microsoft.com/free/), můžete použít pouze omezený počet výpočetních jader Azure.

* Může být potřeba zvýšit kvótu pro jádra (podle oblasti) ve vašem předplatném Azure a zvýšit samostatné kvóty pro jádra NC, NCv2, NCv3, ND, NDv2, NV nebo NVv2. Požádat o zvýšení kvóty, [otevřete žádost o online zákaznickou podporu](../articles/azure-supportability/how-to-create-azure-support-request.md) bez poplatků. Výchozí omezení může lišit v závislosti na vaše předplatné kategorie.




