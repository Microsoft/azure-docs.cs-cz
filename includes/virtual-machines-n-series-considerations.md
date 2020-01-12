---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 06/19/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 202bffb03a73acde67f0d4a03b31aa9a6fbf9824
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75901769"
---
## <a name="deployment-considerations"></a>Aspekty nasazování

* Dostupnost virtuálních počítačů řady N-Series najdete v tématu [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/regions/services/).

* Virtuální počítače řady N-Series jde nasadit jenom v modelu nasazení Správce prostředků.

* Virtuální počítače řady N-Series se liší v typu Azure Storage podporují jejich disky. Virtuální počítače síťového adaptéru a NV podporují jenom disky virtuálních počítačů, které jsou založené na standardu Disk Storage (HDD). Virtuální počítače NCv2, NCv3, ND, NDv2 a NVv2 podporují pouze disky virtuálních počítačů, které jsou zajištěny pomocí Premium Disk Storage (SSD).

* Pokud chcete nasadit více než několik virtuálních počítačů řady N-Series, vezměte v úvahu předplatné s průběžnými platbami nebo jiné možnosti nákupu. Pokud používáte [bezplatný účet Azure](https://azure.microsoft.com/free/), můžete použít pouze omezený počet výpočetních jader Azure.

* V rámci vašeho předplatného Azure možná budete muset zvýšit kvótu jader (na oblast) a zvýšit samostatnou kvótu pro NC, NCv2, NCv3, ND, NDv2, NV nebo NVv2 jádra. Chcete-li požádat o zvýšení kvóty, [otevřete online žádost o zákaznickou podporu](../articles/azure-portal/supportability/how-to-create-azure-support-request.md) zdarma. Výchozí omezení se můžou lišit v závislosti na vaší kategorii předplatného.




