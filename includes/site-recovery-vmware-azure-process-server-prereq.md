---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 11/28/2019
ms.author: raynew
ms.openlocfilehash: de15e3028cf22cdd03ce29385278fc5e2babaa9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008466"
---
V tomto článku se předpokládá následující:

1. Už bylo navázáno připojení **S2S VPN** nebo **Express Route** mezi vaší místní sítí a Azure Virtual Network.
2. Váš uživatelský účet má oprávnění k vytvoření nového virtuálního počítače v rámci předplatného Azure, do kterého bylo provedeno převzetí služeb při selhání virtuálních počítačů.
3. K vytvoření nového virtuálního počítače procesového serveru má vaše předplatné minimálně 8 jader.
4. Máte k dispozici **heslo konfiguračního serveru**.

> [!TIP]
> Ujistěte se, že se můžete připojit na port 443 konfiguračního serveru (spuštěného místně) z Azure Virtual Network, kam bylo provedeno převzetí služeb při selhání virtuálních počítačů.
