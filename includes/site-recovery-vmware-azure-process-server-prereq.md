---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 96cba4e077be8b7658c270b09b177a845e16c8b0
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66169983"
---
V tomto článku se předpokládá následující:

1. Už bylo navázáno připojení **S2S VPN** nebo **Express Route** mezi vaší místní sítí a Azure Virtual Network.
2. Váš uživatelský účet má oprávnění k vytvoření nového virtuálního počítače v rámci předplatného Azure, do kterého bylo provedeno převzetí služeb při selhání virtuálních počítačů.
3. Vaše předplatné má minimálně 4 jádra, která jsou k dispozici pro rozběh nového virtuálního počítače procesového serveru.
4. Máte k dispozici **heslo konfiguračního serveru**.

> [!TIP]
> Ujistěte se, že se můžete připojit na port 443 konfiguračního serveru (spuštěného místně) z Azure Virtual Network, kam bylo provedeno převzetí služeb při selhání virtuálních počítačů.
