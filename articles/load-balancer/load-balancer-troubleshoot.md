---
title: Řešení potíží pro vyrovnávání zatížení Azure | Microsoft Docs
description: Řešení známých problémů s nástrojem pro vyrovnávání zatížení Azure
services: load-balancer
documentationcenter: na
author: chadmath
manager: cshepard
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: genli
ms.openlocfilehash: 294bb6dd780d1df642d6e793b29267da1e8b8336
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="troubleshoot-azure-load-balancer"></a>Řešení potíží s nástrojem pro vyrovnávání zatížení Azure

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Tato stránka obsahuje informace o odstraňování potíží pro běžné otázky nástroj pro vyrovnávání zatížení Azure. Když je nástroj pro vyrovnávání zatížení připojení není k dispozici, nejčastěji jsou následující: 
- Virtuální počítače za nástrojem pro vyrovnávání zatížení nereagují na sondy stavu služby 
- Virtuální počítače za nástrojem pro vyrovnávání zatížení nereagují na provoz na konfigurovaném portu

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Příznak: Virtuální počítače za nástrojem pro vyrovnávání zatížení nereagují sondy stavu služby
Pro back-end serverů k účasti v sadě nástroje pro vyrovnávání zatížení se musí projít kontrola testu. Další informace o sondy stavu najdete v tématu [sondy nástroje pro vyrovnávání zatížení Principy](load-balancer-custom-probe-overview.md). 

Back-endový fond Vyrovnávání zatížení virtuálních počítačů nemusí odpovídat na sondy z některého z následujících důvodů: 
- Fond back-end pro vyrovnávání zatížení virtuálního počítače není v pořádku 
- Načíst back-endový fond vyrovnávání, které se virtuální počítač nenaslouchá na portu testu 
- Brána firewall nebo skupinu zabezpečení sítě je blokování port na back-endový fond Vyrovnávání zatížení virtuálních počítačů 
- Další chybné konfigurace pro vyrovnávání zatížení

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>Příčina 1: Back-endový fond Vyrovnávání zatížení virtuálního počítače není v pořádku 

**Ověření a řešení**

Chcete-li vyřešit tento problém, přihlášení do zúčastněných virtuálních počítačů a zkontrolujte, jestli je v pořádku stav virtuálního počítače a může reagovat na **Pspingu** nebo **TCPing** z jiného virtuálního počítače ve fondu. Pokud virtuální počítač není v pořádku nebo je schopna odpovědět na sondy, musíte opravit problém a získat virtuální počítač zpět do stavu v pořádku, aby se mohl účastnit vyrovnávání zatížení.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>Příčina 2: Back-endový fond Vyrovnávání zatížení virtuální počítač nenaslouchá na portu testu
Pokud virtuální počítač je v pořádku, ale nereaguje na sondy, pak jednou z možných důvodů může být, že port testu není otevřen v účasti virtuálního počítače nebo virtuální počítač nenaslouchá na tomto portu.

**Ověření a řešení**

1. Přihlaste se k back-end virtuálních počítačů. 
2. Otevřete příkazový řádek a spusťte následující příkaz k ověření, že je aplikace, která naslouchá na portu testu:   
            netstat - an
3. Pokud stav portu není uvedena jako **NASLOUCHAJÍCÍ**, nakonfigurujte správný port. 
4. Nebo můžete vybrat jiný port, který je uveden jako **NASLOUCHAJÍCÍ**a aktualizovat konfiguraci nástroje pro vyrovnávání zatížení odpovídajícím způsobem.              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Příčina 3: Brána Firewall nebo skupinu zabezpečení sítě je blokování port na back-endový fond Vyrovnávání zatížení virtuálních počítačů  
Pokud brána firewall na virtuálním počítači blokuje port testu nebo jeden nebo více skupin zabezpečení, které jsou nakonfigurované v podsíti nebo na virtuálním počítači sítě, která není umožňují test k dosažení port, virtuální počítač se nemůže reagovat na test stavu.          

**Ověření a řešení**

* Pokud je zapnutá brána firewall, zkontrolujte, jestli je konfigurovaný k povolení kontroly portu. Pokud ne, nakonfigurovat bránu firewall, aby povolovala přenosy na portu test a vyzkoušejte znovu. 
* Ze seznamu skupin zabezpečení sítě zkontrolujte, jestli má příchozí nebo odchozí přenosy na portu testu narušení. 
* Zkontrolujte také, pokud **Odepřít všechny** skupin zabezpečení sítě pravidla na síťový adaptér virtuálního počítače nebo podsítě, který má vyšší prioritu než výchozí pravidlo, které umožňuje LB sondy & provoz (skupiny zabezpečení sítě musí umožňovat IP nástroje pro vyrovnávání zatížení 168.63.129.16). 
* Pokud žádné z těchto pravidel, blokují přenosy testu, odebrat a znovu nakonfigurovat pravidla, které chcete povolit přenosy testu.  
* Test-li virtuální počítač je nyní spuštěna neodpovídá na požadavky sondy stavu. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>4 příčina: Ostatní chybné konfigurace pro vyrovnávání zatížení
Pokud předchozí příčiny zdá se, že k ověření a analyzovat správně, a back-end virtuální počítač stále nereaguje test stavu a potom ručně testování pro připojení k síti a shromažďovat některé trasování pochopit připojení.

**Ověření a řešení**

* Použití **Pspingu** z jednoho z ostatních virtuálních počítačů v rámci virtuální sítě k otestování portu odpovědi testu (Příklad: 10.0.0.4:3389.\psping.exe -t) a zaznamenejte výsledky. 
* Použití **TCPing** z jednoho z ostatních virtuálních počítačů v rámci virtuální sítě k otestování portu odpovědi testu (Příklad:.\tcping.exe 10.0.0.4 3389) a zaznamenejte výsledky. 
* Pokud je v těchto testech ping pak přijata žádná odpověď
    - Spusťte souběžných příkazu Netsh trace na cílový fond back-end virtuálního počítače a jiné testovací virtuální počítač z stejnou virtuální síť. Nyní spuštění testu Pspingu nějakou dobu, shromažďovat některé trasování sítě a poté jej zastavit test. 
    - Analýza zachycení dat ze sítě a podívejte se, pokud jsou příchozí a odchozí pakety související s příkazem ping dotaz. 
        - Pokud žádné příchozí pakety jsou dodržovány na back-end fondu virtuálních počítačů, je potenciálně skupin zabezpečení sítě nebo UDR chybná konfigurace blokování provozu. 
        - Pokud žádná odchozí pakety jsou dodržovány na back-end fondu virtuálních počítačů, virtuální počítač je potřeba zkontrolovat pro potíže, které nejsou (pro eample, aplikace blokování port testu). 
    - Ověřte, pokud jsou pakety testu vynucené na jiný cíl (případně prostřednictvím nastavení UDR) dříve, než dorazila nástroje pro vyrovnávání zatížení. To může způsobit provozu, který nikdy nedorazí back-end virtuálních počítačů. 
* Změnit typ testu (například HTTP do protokolu TCP) a nakonfigurovat příslušný port skupiny zabezpečení sítě seznamy řízení přístupu a brány firewall, ověřte, jestli je problém s konfigurací testu odpovědi. Další informace o Konfigurace testu stavu najdete v tématu [koncový bod Vyrovnávání zatížení Konfigurace testu stavu](https://blogs.msdn.microsoft.com/mast/2016/01/26/endpoint-load-balancing-heath-probe-configuration-details/).

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Příznak: Virtuální počítače za službou Vyrovnávání zatížení nereagují přenosy na portu nakonfigurované dat

Pokud k back-end fondu virtuálních počítačů je uveden jako v pořádku a odpovídá na sondy stavu, ale stále se neúčastní vyrovnávání zatížení nebo nereaguje na přenos dat, může být z některého z následujících důvodů: 
* Načíst fond back-end pro vyrovnávání, kterou virtuální počítač nenaslouchá na portu dat 
* Skupina zabezpečení sítě je blokování port na back-endový fond Vyrovnávání zatížení virtuálních počítačů  
* Přístup k vyrovnávání zatížení ze stejné virtuálních počítačů a síťový adaptér 
* Přístup k Internetu VIP pro vyrovnávání zatížení od zúčastněných back-endový fond Vyrovnávání zatížení virtuálních počítačů 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Příčina 1: Back-endový fond Vyrovnávání zatížení virtuální počítač nenaslouchá na portu dat 
Pokud virtuální počítač neodpovídá na přenos dat, může to být proto cílový port není otevřen v zúčastněných virtuální počítač nebo virtuální počítač nenaslouchá na tomto portu. 

**Ověření a řešení**

1. Přihlaste se k back-end virtuálních počítačů. 
2. Otevřete příkazový řádek a spusťte následující příkaz k ověření, že je aplikace, která naslouchá na portu dat:  
            netstat - an 
3. Pokud není port uvedené se stavem "Naslouchá", nakonfigurovat port správné naslouchacího procesu 
4. Pokud port je označen jako naslouchající, zkontrolujte cílová aplikace na tomto portu pro všechny možné problémy. 

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Příčina 2: Skupina zabezpečení sítě je blokování port na back-endový fond Vyrovnávání zatížení virtuálních počítačů  

Pokud jeden nebo více skupin zabezpečení sítě nakonfigurované v podsíti nebo na virtuálním počítači, je blokování zdrojové IP adresy nebo portu, potom virtuální počítač není schopen odpovídat.

* Zobrazí seznam skupin zabezpečení sítě nakonfigurované na back-end virtuálních počítačů. Další informace najdete v tématu [spravovat skupiny zabezpečení sítě](../virtual-network/manage-network-security-group.md).
* Ze seznamu skupin zabezpečení sítě zkontrolujte, zda:
    - příchozí nebo odchozí přenosy na portu dat má narušení. 
    - **Odepřít všechny** pravidlo skupiny zabezpečení na síťový adaptér virtuálního počítače nebo podsítě, který má vyšší prioritu, která sondy výchozí pravidlo, které umožňuje Vyrovnávání zatížení sítě a provoz (skupiny zabezpečení sítě musí umožňovat IP nástroje pro vyrovnávání zatížení 168.63.129.16, který je port testu) 
* Pokud žádné z pravidel, blokují přenosy dat, odebrat a znovu nakonfigurujte tato pravidla, aby se povolily přenosy dat.  
* Test-li virtuální počítač je nyní spuštěna reagovat na sondy stavu.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Příčina 3: Přístupu ke službě Vyrovnávání zatížení ze stejné rozhraní virtuálního počítače a sítě 

Pokud vaše aplikace hostované na back-end virtuálních počítačů služby Vyrovnávání zatížení se pokouší o přístup jiná aplikace hostované na stejnou back-end virtuálních počítačů přes rozhraní stejné sítě, se o nepodporovaný scénář a se nezdaří. 

**Řešení** vyřešíte tento problém prostřednictvím jednoho z následujících metod:
* Nakonfigurujte samostatné back-end fondu virtuálních počítačů na aplikaci. 
* Nakonfigurujte aplikace ve virtuálních počítačích duální síťovou kartu, tak, aby byla každá aplikace pomocí vlastní rozhraní sítě a IP adresu. 

### <a name="cause-4-accessing-the-internal-load-balancer-vip-from-the-participating-load-balancer-backend-pool-vm"></a>4 příčina: Přístup k interní VIP pro vyrovnávání zatížení od zúčastněných back-endový fond Vyrovnávání zatížení virtuálních počítačů

Pokud je nakonfigurovaný ILB VIP uvnitř virtuální sítě, a jeden z virtuálních počítačů účastnické back-end je pokusu o přístup k interní VIP pro vyrovnávání zatížení, která způsobuje selhání. Tento scénář se nepodporuje.
**Řešení** vyhodnotit aplikační bránou nebo jiné servery proxy (například nginx nebo haproxy) pro podporu tento druh scénář. Další informace o Application Gateway najdete v tématu [Přehled služby Application Gateway](../application-gateway/application-gateway-introduction.md)

## <a name="additional-network-captures"></a>Dalších síťových přenosů
Pokud se rozhodnete otevřít případ podpory, shromážděte následující informace pro rychlejší řešení. Vyberte jeden back-end virtuálních počítačů k provedení následujících testů:
- Pomocí Pspingu z jednoho z back-end virtuálních počítačů v rámci virtuální sítě k otestování portu odpovědi testu (Příklad: pspingu 10.0.0.4:3389) a zaznamenejte výsledky. 
- Použít TCPing z jednoho z back-end virtuálních počítačů v rámci virtuální sítě k otestování portu odpovědi testu (Příklad: pspingu 10.0.0.4:3389) a zaznamenejte výsledky.
- Pokud je v těchto testech ping žádná odpověď, spusťte souběžných příkazu Netsh trace v back-end virtuálního počítače a virtuální síť testovacího virtuálního počítače při spuštění Pspingu pak Zastavit trasování Netsh. 
  
## <a name="next-steps"></a>Další postup

Pokud předchozí kroky není problém vyřešit, otevřete [lístek podpory](https://azure.microsoft.com/support/options/).

