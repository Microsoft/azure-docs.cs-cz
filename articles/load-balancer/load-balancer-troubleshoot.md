---
title: Řešení potíží s nástroji Azure Load Balancer | Dokumentace Microsoftu
description: Řešení známých problémů s nástrojem Azure Load Balancer
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
ms.date: 08/09/2018
ms.author: genli
ms.openlocfilehash: 1a4be7b5caba751f0f90e865d8ef23e5e9c899d6
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/11/2018
ms.locfileid: "42054211"
---
# <a name="troubleshoot-azure-load-balancer"></a>Řešení potíží s nástrojem pro vyrovnávání zatížení Azure

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Tato stránka obsahuje informace o odstraňování potíží pro běžné dotazy pro Azure Load Balancer. Při připojení nástroje pro vyrovnávání zatížení není k dispozici, nejběžnější příznaky jsou následující: 
- Virtuální počítače za nástrojem pro vyrovnávání zatížení neodpovídají na sond stavu 
- Virtuální počítače za nástrojem pro vyrovnávání zatížení neodpovídají na provoz na konfigurovaném portu

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Příznak: Virtuální počítače za nástrojem pro vyrovnávání zatížení neodpovídají na sond stavu
Pro back-end serverů k účasti v sadě nástroje pro vyrovnávání zatížení se musí předat kontrola testu. Další informace o sond stavu najdete v tématu [sondy nástroje pro vyrovnávání zatížení Principy](load-balancer-custom-probe-overview.md). 

Je možné, neodpovídá back-endového fondu nástroje pro vyrovnávání zatížení virtuálních počítačů pro testy z některého z následujících důvodů: 
- Back-endového fondu nástroje pro vyrovnávání zatížení virtuálního počítače není v pořádku 
- Načíst back-endového fondu nástroje pro vyrovnávání, kterou virtuální počítač nenaslouchá na portu sondy 
- Brána firewall nebo skupinu zabezpečení sítě neblokuje port v back-endového fondu nástroje pro vyrovnávání zatížení virtuálních počítačů 
- Další konfigurace v nástroji pro vyrovnávání zatížení

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>Příčina 1: Back-endového fondu nástroje pro vyrovnávání zatížení virtuálních počítačů není v pořádku 

**Ověřování a řešení**

Chcete-li vyřešit tento problém, přihlášení do zúčastněných virtuálních počítačů a zkontrolujte, jestli je stav virtuálního počítače v pořádku a můžou reagovat na **PsPing** nebo **použít příkaz TCPing** z jiného virtuálního počítače ve fondu. Pokud virtuální počítač není v pořádku nebo je schopna odpovědět na test, musíte mohli problém vyřešit a získat virtuální počítač zpět do stavu v pořádku, předtím, než se mohl podílet na Vyrovnávání zatížení.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>2. příčina: Back-endového fondu nástroje pro vyrovnávání zatížení virtuálních počítačů nenaslouchá na portu sondy
Pokud virtuální počítač je v pořádku, ale neodpovídá na test, pak jednou z možných důvodů může být, že port testu není otevřen v účasti virtuální počítač nebo virtuální počítač nenaslouchá na portu.

**Ověřování a řešení**

1. Přihlaste se k back-endového virtuálního počítače. 
2. Otevřete příkazový řádek a spusťte následující příkaz k ověření, že je aplikace, která naslouchá na portu sondy:   
            příkaz netstat - an
3. Pokud státu port není uvedena jako **NASLOUCHAJÍCÍ**, nakonfigurujte správný port. 
4. Můžete také vybrat jiný port, který je uveden jako **NASLOUCHAJÍCÍ**a aktualizujte konfiguraci nástroje pro vyrovnávání zatížení odpovídajícím způsobem.              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>3. důvod: Brána Firewall nebo skupinu zabezpečení sítě neblokuje port v back-endového fondu nástroje pro vyrovnávání zatížení virtuálních počítačů  
Pokud brána firewall na virtuálním počítači blokuje port testu nebo jeden nebo více síťové skupiny zabezpečení nakonfigurované v podsíti nebo na virtuálním počítači, neumožňuje testu a Oslovte port, je schopna odpovědět na sondu stavu virtuálního počítače.          

**Ověřování a řešení**

* Pokud je povolená brána firewall, zkontrolujte, pokud je nakonfigurovaná k povolení portu sondy. V opačném případě konfigurace brány firewall pro povolení provozu na portu sondy a znovu otestujte. 
* Ze seznamu skupin zabezpečení sítě zkontrolujte, jestli má příchozí nebo odchozí přenosy na portu sondy rušení. 
* Zkontrolujte taky, pokud **odmítnout všechny** pravidla skupiny zabezpečení sítě u síťového rozhraní virtuálního počítače nebo podsíť, která má vyšší prioritu než výchozí pravidlo, které umožňuje sondy LB & provoz (skupiny zabezpečení sítě musí umožňovat IP adresu nástroje pro vyrovnávání zatížení z 168.63.129.16). 
* Pokud některý z těchto pravidel, blokují přenosy testu, odeberte a znovu nakonfigurovat pravidla pro povolení provozu testu.  
* Test, pokud spuštění virtuálního počítače teď reagovat na požadavky sondy stavu. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>4 příčina: Ostatní chyby v konfiguraci ve službě Load Balancer
Pokud předchozí příčiny zdá se, že se ověří a analyzovat správně, a back-endový virtuální počítač stále nereaguje na sondu stavu a pak ručně test pro připojení a shromažďování některá trasování o připojení.

**Ověřování a řešení**

* Použití **Psping** z jednoho z ostatních virtuálních počítačů v rámci virtuální sítě k otestování odpovědi portu sondy (Příklad: -t 10.0.0.4:3389.\psping.exe) a zaznamenejte výsledky. 
* Použití **použít příkaz TCPing** z jednoho z ostatních virtuálních počítačů v rámci virtuální sítě k otestování odpovědi portu sondy (Příklad:.\tcping.exe 10.0.0.4 3389) a zaznamenejte výsledky. 
* Pokud je v tyto testy, pak přijata žádná odpověď
    - Spusťte současné trasování Netsh na cílový fond back-endového virtuálního počítače a jiném testovacím virtuálním počítači z stejné virtuální síti. Nyní spusťte PsPing test nechystáte nějakou dobu, shromažďování některá trasování sítě a pak test zastavte. 
    - Analýza zachytávání sítě a zjistíte, jestli jsou příchozí i odchozí pakety týkající se dotaz ping. 
        - Pokud žádná příchozí pakety jsou dodržovány na virtuálních počítačích back-endového fondu, je potenciálně skupiny zabezpečení sítě nebo UDR chybná konfigurace blokování provozu. 
        - Pokud žádná odchozí pakety jsou dodržovány na virtuálních počítačích back-endového fondu, je potřeba zkontrolovat problémy nesouvisejících (pro Příkladem může být aplikace blokuje portu sondy) virtuálního počítače. 
    - Ověření, pokud jsou pakety test vynutit do jiné cíle (případně prostřednictvím nastavení UDR) před dosažením nástroje pro vyrovnávání zatížení. To může způsobit provoz do nikdy nedorazí back-endového virtuálního počítače. 
* Změnit typ testu (například HTTP na TCP) a nakonfigurovat příslušný port do skupiny zabezpečení sítě ACL a brány firewall, ověřte, jestli je problém s konfigurací testu odpovědi. Další informace o konfiguraci sondy stavu najdete v tématu [koncový bod Vyrovnávání zatížení konfiguraci sondy stavu](https://blogs.msdn.microsoft.com/mast/2016/01/26/endpoint-load-balancing-heath-probe-configuration-details/).

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Příznak: Virtuální počítače za nástrojem pro vyrovnávání zatížení neodpovídají na provoz na portu nakonfigurované dat

Pokud back-endový fond, virtuální počítač je uveden jako v pořádku a odpovídá sond stavu, ale není stále účastní Vyrovnávání zatížení nebo nereaguje na přenos dat, může být příčinou některého z následujících důvodů: 
* Načíst nástroj pro vyrovnávání back-endový fond, který virtuální počítač není připraven na datovém portu 
* Skupina zabezpečení sítě neblokuje port v back-endového fondu nástroje pro vyrovnávání zatížení virtuálních počítačů  
* Přístup k nástroje pro vyrovnávání zatížení ze stejného virtuálního počítače a síťovou kartu 
* Přístup k front-endu nástroje pro vyrovnávání zatížení Internet ze zúčastněných back-endového fondu nástroje pro vyrovnávání zatížení virtuálních počítačů 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Příčina 1: Back-endového fondu nástroje pro vyrovnávání zatížení virtuálních počítačů nenaslouchá na datovém portu 
Pokud virtuální počítač neodpovídá na přenos dat, může být vzhledem k tomu, že cílový port není otevřen v zapojený virtuální počítač nebo virtuální počítač nenaslouchá na portu. 

**Ověřování a řešení**

1. Přihlaste se k back-endového virtuálního počítače. 
2. Otevřete příkazový řádek a spusťte následující příkaz k ověření, že je aplikace, která naslouchá na datovém portu:  
            příkaz netstat - an 
3. Pokud port není uvedená se stavem "NASLOUCHÁNÍ", nakonfigurovat na port naslouchacího procesu správné 
4. Pokud port, který je označen jako naslouchající, zkontrolujte cílové aplikace na tomto portu pro všechny možné problémy. 

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>2. příčina: Skupina zabezpečení sítě neblokuje port v back-endového fondu nástroje pro vyrovnávání zatížení virtuálních počítačů  

Pokud jeden nebo více skupin zabezpečení sítě nakonfigurované v podsíti nebo na virtuálním počítači, blokuje Zdrojová IP adresa nebo portu pro virtuální počítač se nemůže odpovědět.

* Seznam skupin zabezpečení sítě nakonfigurované na back-endového virtuálního počítače. Další informace najdete v tématu [Správa skupin zabezpečení sítě](../virtual-network/manage-network-security-group.md).
* Ze seznamu skupin zabezpečení sítě zkontrolujte, zda:
    - příchozí nebo odchozí přenosy na datovém portu se rušení. 
    - **odmítnout všechny** pravidlo skupiny zabezpečení v síťovém adaptéru virtuálního počítače nebo podsíť, která má vyšší prioritu, která sondy výchozí pravidlo, které umožňuje nástroje pro vyrovnávání zatížení sítě a provozu (skupiny zabezpečení sítě musí umožňovat IP adresu nástroje pro vyrovnávání zatížení z adresy 168.63.129.16, port testu je) 
* Pokud žádné z pravidel, blokují přenosy, odeberte a znovu nakonfigurovat pravidla pro povolení provozu data.  
* Test, pokud virtuální počítač má teď začali reagovat na sond stavu.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>3. příčina: Přístup k nástroje pro vyrovnávání zatížení virtuálních počítačů a síťových rozhraní 

Pokud vaše aplikace hostované v back-endového virtuálního počítače nástroje pro vyrovnávání zatížení pokouší o přístup k jiné aplikace hostované na stejnou back-endového virtuálního počítače přes stejném síťovém rozhraní, se o nepodporovaný scénář a se nezdaří. 

**Rozlišení** vyřešíte tento problém některou z následujících metod:
* Nakonfigurujte samostatné back-endový fond virtuálních počítačů na aplikaci. 
* Nakonfigurujte aplikaci ve dvou virtuálních počítačích síťové karty, každá aplikace používala vlastní síťové rozhraní a IP adresu. 

### <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>4. příčina: Přístup k interní front-endu nástroje pro vyrovnávání zatížení od zúčastněných back-endového fondu nástroje pro vyrovnávání zatížení virtuálních počítačů

Interního nástroje Load Balancer je nakonfigurován v síti VNet a jeden z virtuálních počítačů účastníka back-end se snaží získat přístup k interním front-endu nástroje pro vyrovnávání zatížení, můžete při tok je namapována na původním virtuálním počítači dojde k selhání. Tento scénář není podporován. Kontrola [omezení](load-balancer-overview.md#limitations) podrobné informace.

**Rozlišení** existuje několik způsobů, jak odblokovat tento scénář, včetně použití proxy serveru. Vyhodnocení, jestli služba Application Gateway nebo další 3. stran proxy servery (například nginx nebo haproxy). Další informace o službě Application Gateway najdete v tématu [Přehled služby Application Gateway](../application-gateway/application-gateway-introduction.md)

## <a name="additional-network-captures"></a>Zachytávání dalších síťových
Pokud se rozhodnete otevírat případ podpory, shromážděte následující informace pro rychlejší řešení. Zvolte jeden back-endu. virtuální počítač k provádění následujících testů:
- Pomocí Pspingu z jednoho z back-endový virtuální počítače v rámci virtuální sítě k otestování odpovědi portu sondy (Příklad: psping 10.0.0.4:3389) a zaznamenejte výsledky. 
- Pokud není žádná odpověď v tyto testy, a současně spusťte PsPing pak Zastavit trasování Netsh spusťte souběžných Netsh trasování na back-endového virtuálního počítače a virtuální síť testovacího virtuálního počítače. 
  
## <a name="next-steps"></a>Další postup

Pokud předchozí kroky není problém vyřešit, otevřete [lístek podpory](https://azure.microsoft.com/support/options/).

