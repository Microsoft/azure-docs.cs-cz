---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 35f506235f698fbcf42308e6f0b0f400e925df29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "68361541"
---
### <a name="bastion-tier"></a>Bastionu úroveň

Hostitel bastionu je volitelná součást, kterou můžete použít jako server pro odkazy pro přístup k instancím aplikace a databáze. K virtuálnímu počítači hostitele bastionu může být přiřazena veřejná IP adresa, i když doporučujeme pro zabezpečený přístup nastavit připojení ExpressRoute nebo VPN typu Site-to-site s místní sítí. Pro příchozí provoz by měl být kromě toho otevřen pouze SSH (port 22, Linux) nebo RDP (port 3389, Windows Server). Pro zajištění vysoké dostupnosti nasaďte hostitele bastionu ve dvou zónách dostupnosti nebo v jedné skupině dostupnosti.

Můžete také povolit předávání agenta SSH na virtuálních počítačích, což vám umožní přístup k dalším virtuálním počítačům ve virtuální síti předáním přihlašovacích údajů z hostitele bastionu. Nebo použijte tunelové propojení SSH pro přístup k jiným instancím.

Tady je příklad předávání agenta:

```
ssh -A -t user@BASTION_SERVER_IP ssh -A root@TARGET_SERVER_IP`
```

Tento příkaz se připojí k bastionu a okamžitě se spustí `ssh` , takže získáte terminál na cílové instanci. Pokud je váš cluster nakonfigurovaný odlišně, možná budete muset zadat jiného uživatele než root v cílové instanci. `-A`Argument přepošle připojení agenta, aby se váš privátní klíč na místním počítači automaticky použil. Všimněte si, že přesměrování agenta je řetěz, takže druhý `ssh` příkaz také obsahuje, aby `-A` všechny následné připojení SSH iniciované z cílové instance také používalo místní privátní klíč.