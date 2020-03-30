---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 35f506235f698fbcf42308e6f0b0f400e925df29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361541"
---
### <a name="bastion-tier"></a>Úroveň bašty

Hostitel bastionu je volitelná součást, kterou můžete použít jako přechodný server pro přístup k instancím aplikace a databáze. Hostitelský virtuální počítač bašty může mít přiřazenou veřejnou IP adresu, i když se doporučuje nastavit připojení ExpressRoute nebo VPN site-to-site s místní sítí pro zabezpečený přístup. Kromě toho by měly být otevřeny pouze SSH (port 22, Linux) nebo RDP (port 3389, Windows Server) by měly být otevřeny pro příchozí provoz. Pokud hledáte vysokou dostupnost, nasaďte hostitele bašty ve dvou zónách dostupnosti nebo v jedné sadě dostupnosti.

Můžete také povolit předávání agentů SSH na virtuálních počítačích, což umožňuje přístup k ostatním virtuálním počítačům ve virtuální síti předáním přihlašovacích údajů z hostitele bastionu. Nebo použijte tunelové propojení SSH pro přístup k jiným instancím.

Tady je příklad předávání agentů:

```
ssh -A -t user@BASTION_SERVER_IP ssh -A root@TARGET_SERVER_IP`
```

Tento příkaz se připojí k baště `ssh` a okamžitě se znovu spustí, takže získáte terminál na cílové instanci. Pokud je cluster nakonfigurován jinak, bude pravděpodobně nutné zadat jiného uživatele než root v cílové instanci. Argument `-A` předá připojení agenta, aby byl automaticky použit soukromý klíč v místním počítači. Všimněte si, že předávání agentů `ssh` je řetěz, takže druhý příkaz také zahrnuje `-A` tak, aby všechna následná připojení SSH inicializovaná z cílové instance také používala místní soukromý klíč.