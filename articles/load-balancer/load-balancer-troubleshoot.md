---
title: Řešení potíží s nástrojem pro vyrovnávání zatížení Azure
titlesuffix: Azure Load Balancer
description: Řešení známých problémů s Azure Load Balancer
services: load-balancer
documentationcenter: na
author: chadmath
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/09/2018
ms.author: genli
ms.openlocfilehash: 4e0e3cf6067467947bcb799a915a93d1bb342ea1
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71154929"
---
# <a name="troubleshoot-azure-load-balancer"></a>Řešení potíží s nástrojem pro vyrovnávání zatížení Azure

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Tato stránka poskytuje informace pro odstraňování běžných otázek Azure Load Balancer. Když je připojení Load Balancer nedostupné, nejběžnější příznaky jsou následující: 
- Virtuální počítače za Load Balancer nereagují na sondy stavu. 
- Virtuální počítače za Load Balancer nereagují na provoz na konfigurovaném portu.

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Příznak Virtuální počítače za Load Balancer nereagují na sondy stavu.
Aby se servery back-end účastnily sady nástroje pro vyrovnávání zatížení, musí projít kontrolu sondy. Další informace o sondách stavu najdete v tématu [principy Load Balancer sondy](load-balancer-custom-probe-overview.md). 

Virtuální počítače Load Balancer fondu back-endu nereagují na sondy z některého z následujících důvodů: 
- Virtuální počítač fondu back-endu Load Balancer není v pořádku. 
- Virtuální počítač fondu back-endu Load Balancer nenaslouchá na portu testu paměti 
- Brána firewall nebo skupina zabezpečení sítě blokuje port ve virtuálních počítačích Load Balancer fondu back-endu. 
- Další neLoad Balanceré konfigurace v

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>Příčina 1: Virtuální počítač fondu back-endu Load Balancer není v pořádku. 

**Ověření a rozlišení**

Pokud chcete tento problém vyřešit, přihlaste se k zúčastněným virtuálním počítačům a ověřte, jestli je stav virtuálního počítače v pořádku, a můžete reagovat na **PsPing** nebo **TCPing** z jiného virtuálního počítače ve fondu. Pokud virtuální počítač není v pořádku nebo není schopen reagovat na test paměti, je nutné problém vyřešit a získat virtuální počítač zpátky do správného stavu, než se bude moci zúčastnit vyrovnávání zatížení.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>Příčina 2: Virtuální počítač fondu back-endu Load Balancer nenaslouchá na portu testu paměti
Pokud je virtuální počítač v pořádku, ale nereaguje na test, pak může být jedním z možných důvodů, že port testu není otevřený na zapojeném virtuálním počítači, nebo virtuální počítač na tomto portu nenaslouchá.

**Ověření a rozlišení**

1. Přihlaste se k virtuálnímu počítači back-end. 
2. Otevřete příkazový řádek a spuštěním následujícího příkazu ověřte, že aplikace naslouchá na portu sondy:   
            netstat – a
3. Pokud stav portu není uveden jako naslouchání, nakonfigurujte správný port. 
4. Případně vyberte jiný port, který je uveden jako **naslouchání**, a odpovídajícím způsobem aktualizujte konfiguraci nástroje pro vyrovnávání zatížení.              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Příčina 3: Brána firewall nebo skupina zabezpečení sítě blokuje port ve virtuálních počítačích back-endu fondu nástroje pro vyrovnávání zatížení.  
Pokud brána firewall na virtuálním počítači blokuje port sondy nebo jednu nebo více skupin zabezpečení sítě nakonfigurovaných v podsíti nebo na virtuálním počítači, nepovoluje testům přístup k portu, virtuální počítač nemůže odpovědět na sondu stavu.          

**Ověření a rozlišení**

* Pokud je povolená brána firewall, ověřte, jestli je nakonfigurovaná tak, aby umožňovala port testu. V takovém případě nakonfigurujte bránu firewall tak, aby povolovala přenosy na portu sondy, a znovu spusťte test. 
* V seznamu skupin zabezpečení sítě ověřte, zda příchozí nebo odchozí provoz na portu sondy obsahuje rušivý vliv. 
* Také ověřte, zda pravidlo **Odepřít všechny** skupiny zabezpečení sítě na síťové kartě virtuálního počítače nebo podsítě s vyšší prioritou, než je výchozí pravidlo, které povoluje sondy po kg & provozu (skupiny zabezpečení sítě musí umožňovat Load Balancer IP adresy 168.63.129.16). 
* Pokud některá z těchto pravidel blokují provoz sondy, odeberte a překonfigurujte pravidla, aby umožňovala provoz sondy.  
* Otestujte, jestli virtuální počítač nyní začal reagovat na sondy stavu. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Příčina 4: Další neLoad Balanceré konfigurace v
Pokud se zdá, že se všechny předchozí příčiny ověřují a správně vyřeší, a back-end virtuální počítač pořád nereaguje na sondu stavu, pak ručně otestuje připojení a shromáždí některá trasování pro pochopení připojení.

**Ověření a rozlišení**

* Pomocí **Psping** z jednoho z dalších virtuálních počítačů v rámci virtuální sítě otestujte odpověď na port testu paměti (příklad: .\psping.exe-t 10.0.0.4:3389) a výsledky záznamu. 
* Pomocí **TCPing** z jednoho z dalších virtuálních počítačů v rámci virtuální sítě otestujte odpověď na port testu paměti (příklad: .\tcping.exe 10.0.0.4 3389) a výsledky záznamu. 
* Pokud v těchto testech otestujete žádnou odpověď, pak
    - Spusťte souběžné trasování Netsh v cílovém virtuálním počítači back-end fondu a další testovací virtuální počítač ze stejné virtuální sítě. Nyní spusťte test PsPing ještě jednou, shromážděte několik trasování sítě a potom test zastavte. 
    - Analyzujte síťové zachycení a zkontrolujte, jestli jsou příchozí i odchozí pakety související s dotazem na příkazy. 
        - Pokud se na virtuálním počítači s back-end fondem nepozorovany žádné příchozí pakety, může se stát, že se zablokuje přenos pomocí skupin zabezpečení sítě nebo UDR MIS. 
        - Pokud na virtuálním počítači fondu back-end nejsou žádné odchozí pakety, musí být virtuální počítač zkontrolován na případné nesouvisející problémy (například aplikace blokující port testu). 
    - Před dosažením nástroje pro vyrovnávání zatížení ověřte, zda jsou pakety sondy vynuceny do jiného cílového umístění (případně prostřednictvím nastavení UDR). To může způsobit, že se přenosy nikdy nedostanou do back-endu virtuálního počítače. 
* Změňte typ sondy (například HTTP na TCP) a nakonfigurujte odpovídající port v seznamech ACL skupin zabezpečení sítě a bránu firewall, abyste ověřili, jestli se jedná o problém s konfigurací odezvy testu. Další informace o konfiguraci sondy stavu najdete v tématu [Konfigurace sondy stavu služby Vyrovnávání zatížení koncového bodu](https://blogs.msdn.microsoft.com/mast/2016/01/26/endpoint-load-balancing-heath-probe-configuration-details/).

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Příznak Virtuální počítače za Load Balancer nereagují na provoz na nakonfigurovaném datovém portu.

Pokud je virtuální počítač s back-end fondem uveden jako v pořádku a reaguje na sondy stavu, ale stále se neúčastní vyrovnávání zatížení nebo nereaguje na přenos dat, může to být z následujících důvodů: 
* Virtuální počítač Load Balancerového fondu back-endu nenaslouchá na datovém portu. 
* Skupina zabezpečení sítě blokuje port ve virtuálním počítači Load Balancer fondu back-endu.  
* Přístup k Load Balancer ze stejného virtuálního počítače a síťové karty 
* Přístup k Internetu Load Balancer front-endu z virtuálního počítače fondu back-endu Load Balancer 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Příčina 1: Virtuální počítač Load Balancerového fondu back-endu nenaslouchá na datovém portu. 
Pokud virtuální počítač nereaguje na přenos dat, může to být způsobeno tím, že cílový port není otevřen na daném virtuálním počítači, nebo virtuální počítač na tomto portu nenaslouchá. 

**Ověření a rozlišení**

1. Přihlaste se k virtuálnímu počítači back-end. 
2. Otevřete příkazový řádek a spuštěním následujícího příkazu ověřte, že aplikace naslouchá na datovém portu:  netstat-a 
3. Pokud port není uveden se stavem "NASLOUCHÁní", nakonfigurujte správný port naslouchacího procesu. 
4. Pokud je port označený jako naslouchání, zkontrolujte u cílové aplikace na tomto portu případné problémy. 

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Příčina 2: Skupina zabezpečení sítě blokuje port ve virtuálním počítači Load Balancer fondu back-endu.  

Pokud jedna nebo více skupin zabezpečení sítě nakonfigurovaných v podsíti nebo na VIRTUÁLNÍm počítači blokuje zdrojovou IP adresu nebo port, virtuální počítač nemůže odpovědět.

* Vypíše skupiny zabezpečení sítě nakonfigurované na virtuálním počítači back-end. Další informace najdete v tématu [Správa skupin zabezpečení sítě](../virtual-network/manage-network-security-group.md).
* V seznamu skupin zabezpečení sítě ověřte, zda:
    - příchozí nebo odchozí provoz na datovém portu má rušivý vliv. 
    - **zamítne všechna** pravidla skupiny zabezpečení sítě na síťové kartě virtuálního počítače nebo podsítě s vyšší prioritou, jako je výchozí pravidlo, které umožňuje Load Balancer sondy a provozu (skupiny zabezpečení sítě musí umožňovat Load Balancer IP adresy 168.63.129.16, což je port testu). 
* Pokud některá pravidla blokují provoz, odeberte a překonfigurujte tato pravidla, aby umožňovala přenos dat.  
* Otestujte, jestli virtuální počítač teď začal reagovat na sondy stavu.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Příčina 3: Přístup k Load Balancer ze stejného virtuálního počítače a síťového rozhraní 

Pokud se vaše aplikace hostovaná na virtuálním počítači back-endu Load Balancer pokouší o přístup k jiné aplikaci hostované ve stejném virtuálním počítači back-end přes stejné síťové rozhraní, jedná se o nepodporovaný scénář a nezdaří se. 

**Řešení** Tento problém můžete vyřešit pomocí jedné z následujících metod:
* Nakonfigurujte samostatné virtuální počítače back-end fondu na aplikaci. 
* Nakonfigurujte aplikaci na virtuálních počítačích s více síťovými kartami, aby každá aplikace používala vlastní síťové rozhraní a IP adresu. 

### <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>Příčina 4: Přístup k internímu Load Balancer front-endu z virtuálního počítače fondu back-endu Load Balancer

Pokud je interní Load Balancer nakonfigurovaný v rámci virtuální sítě a jeden z back-end účastníka se snaží získat přístup k internímu front-endu Load Balancer, můžou se selhání vyskytnout, když se tok namapuje na původní virtuální počítač. Tento scénář není podporován. Přečtěte si [omezení](load-balancer-overview.md#limitations) pro podrobnou diskuzi.

**Řešení** Existuje několik způsobů, jak tento scénář odblokovat, včetně použití proxy serveru. Vyhodnoťte Application Gateway nebo jiné proxy servery třetích stran (například Nginx nebo HAProxy). Další informace o Application Gateway najdete v tématu [přehled Application Gateway](../application-gateway/application-gateway-introduction.md)

## <a name="additional-network-captures"></a>Další síťové zachycení
Pokud se rozhodnete otevřít případ podpory, shromážděte při rychlejším řešení následující informace. Vyberte jeden back-end virtuální počítač pro provedení následujících testů:
- Pomocí Psping z jednoho ze back-end virtuálních počítačů v rámci virtuální sítě otestujte odpověď na port testu paměti (příklad: Psping 10.0.0.4:3389) a výsledky záznamu. 
- Pokud se v těchto testech testu příkazu otestuje žádná odpověď, spusťte pro virtuální počítač back-end a virtuální počítač s testovacím virtuálním počítačem při spuštění PsPing a pak zastavte trasování Netsh. 
  
## <a name="next-steps"></a>Další kroky

Pokud předchozí kroky problém nevyřeší, otevřete [lístek podpory](https://azure.microsoft.com/support/options/).

