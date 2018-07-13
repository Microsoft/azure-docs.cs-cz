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
ms.openlocfilehash: 4bec8c8ea29c10b8c0d0351a41ebc9183bb45d4f
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38942727"
---
## <a name="deployment-considerations"></a>Aspekty nasazování

* Dostupnost virtuálních počítačů řady N-series, naleznete v tématu [dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/regions/services/).

* Virtuální počítače řady N-series je možné nasadit jenom v modelu nasazení Resource Manager.

* Virtuální počítače řady N-series se liší v typu Azure Storage podporují pro jejich disků. NC a NV virtuální počítače podporují jenom disky virtuálních počítačů, které využívají pomocí standardní úložiště disku (HDD). NCv2, ND a NCv3 virtuální počítače podporují jenom disky virtuálních počítačů, které se zálohují na podle Disk Storage úrovně Premium (SSD).

* Pokud chcete nasadit více než několik virtuálních počítačů řady N-series, zvažte předplatné s průběžnými platbami nebo jiné možnosti nákupu. Pokud používáte [bezplatný účet Azure](https://azure.microsoft.com/free/), můžete použít pouze omezený počet výpočetních jader Azure.

* Může být potřeba zvýšit kvótu pro jádra (podle oblasti) ve vašem předplatném Azure a zvýšit samostatné kvóty pro jádra NC, NCv2, NCv3, ND nebo NV. Požádat o zvýšení kvóty, [otevřete žádost o online zákaznickou podporu](../articles/azure-supportability/how-to-create-azure-support-request.md) bez poplatků. Výchozí omezení může lišit v závislosti na vaše předplatné kategorie.




