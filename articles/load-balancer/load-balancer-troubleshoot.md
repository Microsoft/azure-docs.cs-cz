---
title: Řešení potíží s nástrojem pro vyrovnávání zatížení Azure
description: Zjistěte, jak řešit známé problémy s Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/28/2020
ms.author: allensu
ms.openlocfilehash: ca9b70bd71a618f8e3d5f4fe9504ba66a9f14c6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76935485"
---
# <a name="troubleshoot-azure-load-balancer"></a>Řešení potíží s nástrojem pro vyrovnávání zatížení Azure

Tato stránka obsahuje informace o řešení potíží pro základní a standardní společné otázky Azure Load Balancer. Další informace o standardním nástroje pro vyrovnávání zatížení naleznete v [tématu Standardní přehled nástroje pro vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics).

Pokud není k dispozici funkce Vyrovnávání zatížení, jsou nejčastější příznaky následující: 

- Virtuální virtuální chod za vykladacím procesem zatížení nereagují na sondy stavu 
- Virtuální počítače za balancerem zatížení nereagují na provoz na nakonfigurovaném portu

Když externí klienti back-endové virtuální chod procházet vyrovnávání zatížení, IP adresa klientů se použije pro komunikaci. Ujistěte se, že IP adresa klientů jsou přidány do seznamu povolených nsg. 

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Příznak: Virtuální chod za vyrovnáváním zatížení nereagují na sondy stavu
Pro back-endové servery k účasti v sadě vyrovnávání zatížení, musí projít kontrolou sondy. Další informace o sondách stavu naleznete [v tématu Principy sond pro vyrovnávání zatížení](load-balancer-custom-probe-overview.md). 

Virtuální ms back-endového fondu vyrovnávání zatížení nemusí reagovat na sondy z některého z následujících důvodů: 
- Virtuální byl virtuální byl v pořádku vykladače vyrovnávání zatížení. 
- Vyrovnávání zatížení back-end ový fond virtuálního počítači nenaslouchá na portu sondy 
- Brána firewall nebo skupina zabezpečení sítě blokuje port na virtuálních počítačích back-endového fondu vyrovnávání zatížení 
- Jiné chybné konfigurace v nástroji pro vyrovnávání zatížení

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>Příčina 1: Virtuální virtuální byl virtuální virtuální byl v nepořádku back-endový fond vyrovnávání zatížení 

**Ověření a řešení**

Chcete-li tento problém vyřešit, přihlaste se k zúčastněným virtuálním mům a zkontrolujte, jestli je stav virtuálního soudu v pořádku a můžete reagovat na **psping** nebo **tcping** z jiného virtuálního uživatele ve fondu. Pokud virtuální hod není v pořádku nebo není schopen reagovat na sondu, musíte opravit problém a získat virtuální ho do stavu v pořádku, než se může účastnit vyrovnávání zatížení.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>Příčina 2: Back-endový virtuální počítač v back-endovém fondu vyrovnávání zatížení na portu sondy
Pokud je virtuální počítač v pořádku, ale nereaguje na sondu, pak jedním z možných důvodů může být, že port sondy není otevřený na zúčastněném virtuálním počítači nebo virtuální počítač nenaslouchá na tomto portu.

**Ověření a řešení**

1. Přihlaste se k back-endu virtuálního soudu. 
2. Otevřete příkazový řádek a spusťte následující příkaz k ověření, že na portu sondy naslouchá aplikace:   
            netstat -an
3. Pokud stav portu není uveden jako **NASLOUCHÁNÍ**, nakonfigurujte správný port. 
4. Případně vyberte jiný port, který je uveden jako **NASLOUCHÁNÍ**a odpovídajícím způsobem aktualizujte konfiguraci nástroje pro vyrovnávání zatížení.              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Příčina 3: Brána firewall nebo skupina zabezpečení sítě blokuje port na virtuálních počítačích back-endového fondu vyrovnávání zatížení  
Pokud brána firewall na virtuálním počítači blokuje port sondy nebo jedna nebo více skupin zabezpečení sítě nakonfigurovaných v podsíti nebo na virtuálním počítači neumožňuje sondě přístup k portu, virtuální počítač nemůže reagovat na sondu stavu.          

**Ověření a řešení**

* Pokud je brána firewall povolena, zkontrolujte, zda je nakonfigurována tak, aby umožňovala port sondy. Pokud ne, nakonfigurujte bránu firewall tak, aby umožňovala přenosy na portu sondy, a znovu vyzkoušejte. 
* Ze seznamu skupin zabezpečení sítě zkontrolujte, zda příchozí nebo odchozí provoz na portu sondy došlo k rušení. 
* Zkontrolujte také, zda **pravidlo odepřít všechny** skupiny zabezpečení sítě na nic virtuálního počítači nebo podsítě, která má vyšší prioritu než výchozí pravidlo, které umožňuje LB sondy & provozu (skupiny zabezpečení sítě musí povolit IP rozhraní pro vyrovnávání zatížení 168.63.129.16). 
* Pokud některý z těchto pravidel blokují provoz sondy, odeberte a překonfigurujte pravidla tak, aby provoz sondy.  
* Otestujte, pokud virtuální ho dosud začala reagovat na sondy stavu. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Příčina 4: Jiné chybné konfigurace v nástroji pro vyrovnávání zatížení
Pokud všechny předchozí příčiny se zdají být ověřeny a vyřešeny správně a back-endový virtuální účet stále nereaguje na sondu stavu, pak ručně otestujte připojení a shromážděte některá trasování, abyste porozuměli připojení.

**Ověření a řešení**

* Pomocí **psping** z jednoho z ostatních virtuálních počítačů v rámci virtuální sítě otestujte odezvu portu sondy (příklad: .\psping.exe -t 10.0.0.4:3389) a zaznamenejte výsledky. 
* Pomocí **příkazu TCPing** z jednoho z ostatních virtuálních počítačů ve virtuální síti otestujte odezvu portu sondy (příklad: .\tcping.exe 10.0.0.4 3389) a zaznamenejte výsledky. 
* Pokud v těchto testech ping neobdržíte žádnou odpověď,
    - Spusťte simultánní trasování Netsh na cílovém virtuálním počítači back-endového fondu a další testovací virtuální počítač ze stejné virtuální sítě. Nyní spusťte psping test na nějakou dobu, shromažďovat některé trasování sítě a pak zastavit test. 
    - Analyzujte zachytávání sítě a zjistěte, zda existují příchozí i odchozí pakety související s dotazem ping. 
        - Pokud nejsou ve virtuálním počítači back-endového fondu pozorovány žádné příchozí pakety, je potenciálně blokování provozu skupinou zabezpečení sítě nebo chybnou konfigurací UDR. 
        - Pokud žádné odchozí pakety jsou pozorovány na virtuálním počítači back-endového fondu, je třeba zkontrolovat virtuální počítač pro všechny nesouvisející problémy (například aplikace blokování portu sondy). 
    - Před dosažením systému vyrovnávání zatížení ověřte, zda jsou pakety sondy vynuceny do jiného cíle (případně prostřednictvím nastavení UDR). To může způsobit, že provoz nikdy dosáhnout back-endu virtuálního soudu. 
* Změňte typ sondy (například HTTP na TCP) a nakonfigurujte odpovídající port ve skupinách ACL zabezpečení sítě a bránu firewall k ověření, zda je problém s konfigurací odezvy sondy. Další informace o konfiguraci sondy stavu naleznete [v tématu Konfigurace sondy služby Vyrovnávání zatížení koncového bodu](https://blogs.msdn.microsoft.com/mast/2016/01/26/endpoint-load-balancing-heath-probe-configuration-details/).

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Příznak: Virtuální počítače za balancerem zatížení nereagují na provoz na nakonfigurovaném datovém portu

Pokud je virtuální virtuální soud back-endového fondu uveden jako v pořádku a reaguje na sondy stavu, ale stále se neúčastní vyrovnávání zatížení nebo nereaguje na datový provoz, může to být z některého z následujících důvodů: 
* Back-endový virtuální počítač back-endového fondu vyrovnávání zatížení na datovém portu 
* Skupina zabezpečení sítě blokuje port na virtuálním počítači back-endového fondu vyrovnávání zatížení  
* Přístup k balanceru zatížení ze stejného virtuálního serveru a nic 
* Přístup k frontendu internetového vyrovnávání zatížení ze zúčastněného back-endového fondu vyrovnávání zatížení 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Příčina 1: Back-endový virtuální počítač fondu vyrovnávání zatížení nenaslouchá na datovém portu 
Pokud virtuální počítač nereaguje na datový provoz, může to být proto, že buď cílový port není otevřený na zúčastněném virtuálním počítači, nebo virtuální počítač nenaslouchá na tomto portu. 

**Ověření a řešení**

1. Přihlaste se k back-endu virtuálního soudu. 
2. Otevřete příkazový řádek a spusťte následující příkaz k ověření, že na datovém portu naslouchá aplikace:  netstat -an 
3. Pokud port není uveden s stavem "NASLOUCHÁNÍ", nakonfigurujte správný port posluchače 
4. Pokud je port označen jako Naslouchání, zkontrolujte cílovou aplikaci na tomto portu pro všechny možné problémy.

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Příčina 2: Skupina zabezpečení sítě blokuje port na virtuálním počítači back-endového fondu vyrovnávání zatížení  

Pokud jedna nebo více skupin zabezpečení sítě nakonfigurovaných v podsíti nebo na virtuálním počítači blokuje zdrojovou IP adresu nebo port, pak virtuální počítač nemůže reagovat.

Pro veřejný vyrovnávání zatížení ip adresa internetových klientů se použije pro komunikaci mezi klienty a back-endové virtuální chod y vyrovnávání zatížení. Ujistěte se, že IP adresa klientů je povolena ve skupině zabezpečení sítě back-endového virtuálního soudu.

1. Seznam skupin zabezpečení sítě nakonfigurovaných na back-endovém virtuálním počítači. Další informace naleznete v tématu [Správa skupin zabezpečení sítě](../virtual-network/manage-network-security-group.md)
1. Ze seznamu skupin zabezpečení sítě zkontrolujte, zda:
    - příchozí nebo odchozí provoz na datovém portu má rušení. 
    - a **Odepřít pravidlo** skupiny zabezpečení sítě na síťové konekvsíti virtuálního počítači nebo podsíti, která má vyšší prioritu než výchozí pravidlo, které umožňuje sondy a provoz vykladatela systému zatížení (skupiny zabezpečení sítě musí povolit IP adresu pro vyrovnávání zatížení 168.63.129.16, což je port sondy)
1. Pokud některý z pravidel blokuje provoz, odeberte a překonfigurujte tato pravidla tak, aby datový provoz.  
1. Otestujte, pokud virtuální ho dosud začala reagovat na sondy stavu.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Příčina 3: Přístup k balanceru zatížení ze stejného virtuálního zařízení a síťového rozhraní 

Pokud vaše aplikace hostovaná v back-endovém virtuálním počítači vykladaču zatížení se pokouší o přístup k jiné aplikaci hostované ve stejném back-endovém virtuálním počítači přes stejné síťové rozhraní, jedná se o nepodporovaný scénář a nezdaří se. 

**Rozlišení** Tento problém můžete vyřešit jedním z následujících způsobů:
* Konfigurace samostatných virtuálních počítače back-end fondu pro jednotlivé aplikace. 
* Nakonfigurujte aplikaci ve dvou virtuálních virtuálních sítích síťových rozhraní, aby každá aplikace používala vlastní síťové rozhraní a IP adresu. 

### <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>Příčina 4: Přístup k internímu frontendu vykladače vyrovnávání zatížení ze zúčastněného back-endového fondu vyrovnávání zatížení

Pokud je interní vydělávač zatížení nakonfigurovaný uvnitř virtuální sítě a jeden z účastníků back-endové virtuální počítače se pokouší o přístup k interní vyrovnávání zatížení front-endu, může dojít k chybám při namapování toku na původní virtuální počítače. Takový scénář se nepodporuje. Zkontrolujte [omezení](concepts-limitations.md#limitations) pro podrobnou diskusi.

**Rozlišení** Existuje několik způsobů, jak odblokovat tento scénář, včetně použití proxy serveru. Vyhodnoťte aplikační bránu nebo jiné proxy třetích stran (například nginx nebo haproxy). Další informace o aplikační bráně najdete v [tématu Přehled aplikační brány](../application-gateway/application-gateway-introduction.md)

## <a name="symptom-cannot-change-backend-port-for-existing-lb-rule-of-a-load-balancer-which-has-vm-scale-set-deployed-in-the-backend-pool"></a>Příznak: Nelze změnit back-endový port pro existující pravidlo LB v yvižného systému vyrovnávání zatížení, který má sadu škálování virtuálních her nasazenou v back-endovém fondu. 
### <a name="cause--the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-vm-scale-set"></a>Příčina: Back-endový port nelze upravit pro pravidlo vyrovnávání zatížení, které používá sonda stavu pro vykladač zatížení, na který odkazuje sada měřítka virtuálního měniče.
**Rozlišení** Chcete-li změnit port, můžete odebrat sondu stavu aktualizací škálovací sady virtuálních zařízení, aktualizovat port a znovu nakonfigurovat sondu stavu.

## <a name="additional-network-captures"></a>Další síťové sběry
Pokud se rozhodnete otevřít případ podpory, shromážděte následující informace pro rychlejší řešení. Zvolte jeden back-endový virtuální virtuální ms k provedení následujících testů:
- Pomocí příkazového příkazu z jednoho z back-endových virtuálních sítí ve virtuální síti otestujte odezvu portu sondy (příklad: psping 10.0.0.4:3389) a zaznamenejte výsledky. 
- Pokud v těchto testech ping není přijata žádná odpověď, spusťte simultánní trasování Netsh na back-endovém virtuálním počítači a testovacím virtuálním počítači virtuální sítě při spuštění příkazového příkazu psping a zastavte trasování Netsh. 
  
## <a name="next-steps"></a>Další kroky

Pokud předchozí kroky problém nevyřeší, otevřete [lístek podpory](https://azure.microsoft.com/support/options/).

