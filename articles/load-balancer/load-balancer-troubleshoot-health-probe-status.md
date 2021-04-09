---
title: Řešení potíží s Azure Load Balancer stav sondy stavu
description: Naučte se řešit známé problémy se stavem stavu testu Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/02/2020
ms.author: allensu
ms.openlocfilehash: 28823c997cd974d5061829df88680ed52075caa0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98029181"
---
# <a name="troubleshoot-azure-load-balancer-health-probe-status"></a>Řešení potíží s Azure Load Balancer stav sondy stavu

Tato stránka poskytuje informace pro řešení běžných otázek sondy stavu Azure Load Balancer.

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Příznak: virtuální počítače za Load Balancer nereagují na sondy stavu.
Aby se servery back-end účastnily sady nástroje pro vyrovnávání zatížení, musí projít kontrolu sondy. Další informace o sondách stavu najdete v tématu [principy Load Balancer sondy](load-balancer-custom-probe-overview.md). 

Virtuální počítače Load Balancer fondu back-endu nereagují na sondy z některého z následujících důvodů: 
- Virtuální počítač fondu back-endu Load Balancer není v pořádku. 
- Virtuální počítač fondu back-endu Load Balancer nenaslouchá na portu testu paměti 
- Brána firewall nebo skupina zabezpečení sítě blokuje port ve virtuálních počítačích Load Balancer fondu back-endu. 
- Další neLoad Balanceré konfigurace v

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>Příčina 1: Load Balancer virtuální počítač fondu back-endu není v pořádku

**Ověření a rozlišení**

Pokud chcete tento problém vyřešit, přihlaste se k zúčastněným virtuálním počítačům a ověřte, jestli je stav virtuálního počítače v pořádku, a můžete reagovat na **PsPing** nebo **TCPing** z jiného virtuálního počítače ve fondu. Pokud virtuální počítač není v pořádku nebo není schopen reagovat na test paměti, je nutné problém vyřešit a získat virtuální počítač zpátky do správného stavu, než se bude moci zúčastnit vyrovnávání zatížení.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>Příčina 2: Load Balancer virtuální počítač fondu back-endu nenaslouchá na portu testu paměti
Pokud je virtuální počítač v pořádku, ale nereaguje na test, pak může být jedním z možných důvodů, že port testu není otevřený na zapojeném virtuálním počítači, nebo virtuální počítač na tomto portu nenaslouchá.

**Ověření a rozlišení**

1. Přihlaste se k virtuálnímu počítači back-end.
2. Otevřete příkazový řádek a spuštěním následujícího příkazu ověřte, že aplikace naslouchá na portu sondy: netstat-a
3. Pokud stav portu není uveden jako **naslouchání**, nakonfigurujte správný port. 
4. Případně vyberte jiný port, který je uveden jako **naslouchání**, a odpovídajícím způsobem aktualizujte konfiguraci nástroje pro vyrovnávání zatížení.

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Příčina 3: Brána firewall nebo skupina zabezpečení sítě blokuje port ve virtuálních počítačích back-endu fondu nástroje pro vyrovnávání zatížení.
Pokud brána firewall na virtuálním počítači blokuje port sondy nebo jednu nebo více skupin zabezpečení sítě nakonfigurovaných v podsíti nebo na virtuálním počítači, nepovoluje testům přístup k portu, virtuální počítač nemůže odpovědět na sondu stavu.

**Ověření a rozlišení**

1. Pokud je povolená brána firewall, ověřte, jestli je nakonfigurovaná tak, aby umožňovala port testu. V takovém případě nakonfigurujte bránu firewall tak, aby povolovala přenosy na portu sondy, a znovu spusťte test.
2. V seznamu skupin zabezpečení sítě ověřte, zda příchozí nebo odchozí provoz na portu sondy obsahuje rušivý vliv.
3. Také ověřte, zda pravidlo **Odepřít všechny** skupiny zabezpečení sítě na síťové kartě virtuálního počítače nebo podsítě s vyšší prioritou, než je výchozí pravidlo, které povoluje sondy po kg & provozu (skupiny zabezpečení sítě musí umožňovat Load Balancer IP adresy 168.63.129.16).
4. Pokud některá z těchto pravidel blokují provoz sondy, odeberte a překonfigurujte pravidla, aby umožňovala provoz sondy.  
5. Otestujte, jestli virtuální počítač nyní začal reagovat na sondy stavu.

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Příčina 4: jiné chyby v Load Balancer
Pokud se zdá, že se všechny předchozí příčiny ověřují a správně vyřeší, a back-end virtuální počítač pořád nereaguje na sondu stavu, pak ručně otestuje připojení a shromáždí některá trasování pro pochopení připojení.

**Ověření a rozlišení**

1. Pomocí **Psping** z jednoho z dalších virtuálních počítačů v rámci virtuální sítě otestujte odpověď na port testu paměti (příklad: .\psping.exe-t 10.0.0.4:3389) a výsledky záznamu. 
2. Pomocí **TCPing** z jednoho z dalších virtuálních počítačů v rámci virtuální sítě otestujte odpověď na port testu paměti (příklad: .\tcping.exe 10.0.0.4 3389) a výsledky záznamu. 
3. Pokud v těchto testech otestujete žádnou odpověď, pak
    - Spusťte souběžné trasování Netsh v cílovém virtuálním počítači back-end fondu a další testovací virtuální počítač ze stejné virtuální sítě. Nyní spusťte test PsPing ještě jednou, shromážděte několik trasování sítě a potom test zastavte. 
    - Analyzujte síťové zachycení a zkontrolujte, jestli jsou příchozí i odchozí pakety související s dotazem na příkazy. 
        - Pokud se na virtuálním počítači s back-end fondem nepozorovany žádné příchozí pakety, může se stát, že se zablokuje přenos pomocí skupin zabezpečení sítě nebo UDR MIS. 
        - Pokud na virtuálním počítači fondu back-end nejsou žádné odchozí pakety, musí být virtuální počítač zkontrolován na případné nesouvisející problémy (například aplikace blokující port testu). 
    - Před dosažením nástroje pro vyrovnávání zatížení ověřte, zda jsou pakety sondy vynuceny do jiného cílového umístění (případně prostřednictvím nastavení UDR). To může způsobit, že se přenosy nikdy nedostanou do back-endu virtuálního počítače. 
4. Změňte typ sondy (například HTTP na TCP) a nakonfigurujte odpovídající port v seznamech ACL skupin zabezpečení sítě a bránu firewall, abyste ověřili, jestli se jedná o problém s konfigurací odezvy testu. Další informace o konfiguraci sondy stavu najdete v tématu [Konfigurace sondy stavu služby Vyrovnávání zatížení koncového bodu](/archive/blogs/mast/endpoint-load-balancing-heath-probe-configuration-details).

## <a name="next-steps"></a>Další kroky

Pokud předchozí kroky problém nevyřeší, otevřete [lístek podpory](https://azure.microsoft.com/support/options/).