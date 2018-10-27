---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 96cba4e077be8b7658c270b09b177a845e16c8b0
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164173"
---
V tomto článku se předpokládá následující:

1. Už bylo navázáno připojení **S2S VPN** nebo **Express Route** mezi vaší místní sítí a Azure Virtual Network.
2. Váš uživatelský účet má oprávnění k vytvoření nového virtuálního počítače v rámci předplatného Azure, do kterého bylo provedeno převzetí služeb při selhání virtuálních počítačů.
3. Vaše předplatné má minimálně 4 jádra, která jsou k dispozici pro rozběh nového virtuálního počítače procesového serveru.
4. Máte k dispozici **heslo konfiguračního serveru**.

> [!TIP]
> Ujistěte se, že se můžete připojit na port 443 konfiguračního serveru (spuštěného místně) z Azure Virtual Network, kam bylo provedeno převzetí služeb při selhání virtuálních počítačů.
