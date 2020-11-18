---
title: Řešení potíží s nástrojem pro vyrovnávání zatížení Azure
description: Naučte se řešit známé problémy s Azure Load Balancer.
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
ms.date: 01/28/2020
ms.author: allensu
ms.openlocfilehash: a1a8df6d503ec5f5bf9c1e739e5ecf6486a85776
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94697416"
---
# <a name="troubleshoot-azure-load-balancer"></a>Řešení potíží s nástrojem pro vyrovnávání zatížení Azure

Tato stránka poskytuje informace pro řešení běžných otázek Azure Load Balancer Basic a Standard. Další informace o Standard Load Balancer najdete v tématu [Standard Load Balancer Overview](load-balancer-standard-diagnostics.md).

Když je připojení Load Balancer nedostupné, nejběžnější příznaky jsou následující: 

- Virtuální počítače za Load Balancer nereagují na sondy stavu. 
- Virtuální počítače za Load Balancer nereagují na provoz na konfigurovaném portu.

Když externí klienti back-end virtuálních počítačů procházejí nástrojem pro vyrovnávání zatížení, použijí se pro tuto komunikaci IP adresa klientů. Ujistěte se, že se IP adresa klientů přidala do seznamu povolených NSG. 

## <a name="symptom-no-outbound-connectivity-from-standard-internal-load-balancers-ilb"></a>Příznak: žádné odchozí připojení ze standardních interních nástrojů pro vyrovnávání zatížení (interního nástroje)

**Ověření a rozlišení**

Standardní ILBs jsou **ve výchozím nastavení zabezpečené**. Základní ILBs povoluje připojení k Internetu prostřednictvím *skryté* veřejné IP adresy. To není doporučeno pro produkční úlohy, protože IP adresa není ani statická ani uzamčená prostřednictvím skupin zabezpečení sítě, kterou vlastníte. Pokud jste v nedávné době přesunuli ze základní interního nástroje na standardní interního nástroje, měli byste vytvořit veřejnou IP adresu explicitně prostřednictvím [odchozí jenom odchozí](egress-only.md) konfigurace, která uzamkne IP přes skupin zabezpečení sítě. V podsíti můžete také použít [bránu NAT](../virtual-network/nat-overview.md) .

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
2. Otevřete příkazový řádek a spuštěním následujícího příkazu ověřte, že aplikace naslouchá na portu sondy:   
            netstat – a
3. Pokud stav portu není uveden jako **naslouchání**, nakonfigurujte správný port. 
4. Případně vyberte jiný port, který je uveden jako **naslouchání**, a odpovídajícím způsobem aktualizujte konfiguraci nástroje pro vyrovnávání zatížení.              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Příčina 3: Brána firewall nebo skupina zabezpečení sítě blokuje port ve virtuálních počítačích back-endu fondu nástroje pro vyrovnávání zatížení.  
Pokud brána firewall na virtuálním počítači blokuje port sondy nebo jednu nebo více skupin zabezpečení sítě nakonfigurovaných v podsíti nebo na virtuálním počítači, nepovoluje testům přístup k portu, virtuální počítač nemůže odpovědět na sondu stavu.          

**Ověření a rozlišení**

* Pokud je povolená brána firewall, ověřte, jestli je nakonfigurovaná tak, aby umožňovala port testu. V takovém případě nakonfigurujte bránu firewall tak, aby povolovala přenosy na portu sondy, a znovu spusťte test. 
* V seznamu skupin zabezpečení sítě ověřte, zda příchozí nebo odchozí provoz na portu sondy obsahuje rušivý vliv. 
* Také ověřte, zda pravidlo **Odepřít všechny** skupiny zabezpečení sítě na síťové kartě virtuálního počítače nebo podsítě s vyšší prioritou, než je výchozí pravidlo, které povoluje sondy po kg & provozu (skupiny zabezpečení sítě musí umožňovat Load Balancer IP adresy 168.63.129.16). 
* Pokud některá z těchto pravidel blokují provoz sondy, odeberte a překonfigurujte pravidla, aby umožňovala provoz sondy.  
* Otestujte, jestli virtuální počítač nyní začal reagovat na sondy stavu. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Příčina 4: jiné chyby v Load Balancer
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
* Změňte typ sondy (například HTTP na TCP) a nakonfigurujte odpovídající port v seznamech ACL skupin zabezpečení sítě a bránu firewall, abyste ověřili, jestli se jedná o problém s konfigurací odezvy testu. Další informace o konfiguraci sondy stavu najdete v tématu [Konfigurace sondy stavu služby Vyrovnávání zatížení koncového bodu](/archive/blogs/mast/endpoint-load-balancing-heath-probe-configuration-details).

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Příznak: virtuální počítače za Load Balancer nereagují na provoz na nakonfigurovaném datovém portu.

Pokud je virtuální počítač s back-end fondem uveden jako v pořádku a reaguje na sondy stavu, ale stále se neúčastní vyrovnávání zatížení nebo nereaguje na přenos dat, může to být z následujících důvodů: 
* Virtuální počítač Load Balancerového fondu back-endu nenaslouchá na datovém portu. 
* Skupina zabezpečení sítě blokuje port ve virtuálním počítači Load Balancer fondu back-endu.  
* Přístup k Load Balancer ze stejného virtuálního počítače a síťové karty 
* Přístup k Internetu Load Balancer front-endu z virtuálního počítače fondu back-endu Load Balancer 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Příčina 1: Load Balancer virtuální počítač fondu back-endu nenaslouchá na datovém portu 
Pokud virtuální počítač nereaguje na přenos dat, může to být způsobeno tím, že cílový port není otevřen na daném virtuálním počítači, nebo virtuální počítač na tomto portu nenaslouchá. 

**Ověření a rozlišení**

1. Přihlaste se k virtuálnímu počítači back-end. 
2. Otevřete příkazový řádek a spuštěním následujícího příkazu ověřte, že aplikace naslouchá na datovém portu:  
            netstat – a 
3. Pokud port není uveden se stavem "NASLOUCHÁní", nakonfigurujte správný port naslouchacího procesu. 
4. Pokud je port označený jako naslouchání, zkontrolujte u cílové aplikace na tomto portu případné problémy.

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Příčina 2: Skupina zabezpečení sítě blokuje port ve virtuálním počítači Load Balancer fondu back-endu.  

Pokud jedna nebo více skupin zabezpečení sítě nakonfigurovaných v podsíti nebo na VIRTUÁLNÍm počítači blokuje zdrojovou IP adresu nebo port, virtuální počítač nemůže odpovědět.

Pro veřejný Nástroj pro vyrovnávání zatížení se IP adresa internetových klientů použije pro komunikaci mezi klienty a back-end virtuálními počítači nástroje pro vyrovnávání zatížení. Ujistěte se, že IP adresa klientů je povolená ve skupině zabezpečení virtuálního počítače back-endu.

1. Vypíše skupiny zabezpečení sítě nakonfigurované na virtuálním počítači back-end. Další informace najdete v tématu [Správa skupin zabezpečení sítě](../virtual-network/manage-network-security-group.md) .
1. V seznamu skupin zabezpečení sítě ověřte, zda:
    - příchozí nebo odchozí provoz na datovém portu má rušivý vliv. 
    - **zamítne všechna** pravidla skupiny zabezpečení sítě na síťové kartě virtuálního počítače nebo podsítě s vyšší prioritou, jako je výchozí pravidlo, které umožňuje Load Balancer sondy a provozu (skupiny zabezpečení sítě musí umožňovat Load Balancer IP adresy 168.63.129.16, což je port testu).
1. Pokud některá pravidla blokují provoz, odeberte a překonfigurujte tato pravidla, aby umožňovala přenos dat.  
1. Otestujte, jestli virtuální počítač teď začal reagovat na sondy stavu.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Příčina 3: přístup k Load Balancer ze stejného virtuálního počítače a síťového rozhraní 

Pokud se vaše aplikace hostovaná na virtuálním počítači back-endu Load Balancer pokouší o přístup k jiné aplikaci hostované ve stejném virtuálním počítači back-end přes stejné síťové rozhraní, jedná se o nepodporovaný scénář a nezdaří se. 

**Řešení** Tento problém můžete vyřešit pomocí jedné z následujících metod:
* Nakonfigurujte samostatné virtuální počítače back-end fondu na aplikaci. 
* Nakonfigurujte aplikaci na virtuálních počítačích s více síťovými kartami, aby každá aplikace používala vlastní síťové rozhraní a IP adresu. 

### <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>Příčina 4: přístup k internímu Load Balancer front-endu z virtuálního počítače fondu back-endu Load Balancer

Pokud je interní Load Balancer nakonfigurovaný v rámci virtuální sítě a jeden z back-end účastníka se snaží získat přístup k internímu front-endu Load Balancer, můžou se selhání vyskytnout, když se tok namapuje na původní virtuální počítač. Takový scénář se nepodporuje.

**Řešení** Existuje několik způsobů, jak tento scénář odblokovat, včetně použití proxy serveru. Vyhodnoťte Application Gateway nebo jiné proxy servery třetích stran (například Nginx nebo HAProxy). Další informace o Application Gateway najdete v tématu [přehled Application Gateway](../application-gateway/overview.md)

**Podrobnosti o** Interní nástroje pro vyrovnávání zatížení nepřevádějí odchozí vytvořená připojení na front-end interního Load Balancer, protože obě jsou v privátním adresním prostoru IP adres. Veřejné nástroje pro vyrovnávání zatížení poskytují [odchozí připojení](load-balancer-outbound-connections.md) z privátních IP adres uvnitř virtuální sítě k veřejným IP adresám. U interních nástrojů pro vyrovnávání zatížení tento přístup zabraňuje možnému vyčerpání portů SNAT v rámci jedinečného interního adresního prostoru IP adres, kde není požadován překlad.

Vedlejším účinkem je to, že pokud odchozí tok z virtuálního počítače v záložním fondu pokusy o tok do front-endu interního Load Balancer ve svém fondu _a_ namapuje se zpátky na sebe, dvě ramena toku se neshodují. Vzhledem k tomu, že se neshodují, tok se nezdařil. Tok se úspěšně dokončí, pokud tok nemapoval zpátky na stejný virtuální počítač ve fondu back-end, který vytvořil tok do front-endu.

Když se tok mapuje zpátky na sebe samé, zdá se, že odchozí tok pochází z virtuálního počítače na front-end a odpovídající příchozí tok se zdá, že pochází z virtuálního počítače do sebe samé. Z pohledu hostovaného operačního systému se v rámci tohoto virtuálního počítače neshodují vstupní a výstupní části stejného toku. Zásobník TCP nerozpozná tyto poloviny stejného toku jako součást stejného toku. Zdroj a cíl se neshodují. Když se tok mapuje na jakýkoli jiný virtuální počítač ve fondu back-end, budou se tyto poloviny toku shodovat a virtuální počítač může na tento tok reagovat.

Příznakem pro tento scénář je přerušovaná prodleva připojení, když se tok vrátí ke stejnému back-endu, který daný tok vytvořil. K běžným řešením patří vložení vrstvy proxy za interní Load Balancer a použití pravidel stylu "přímé vrácení serveru (DSR)". Další informace najdete v tématu [více front-endu pro Azure Load Balancer](load-balancer-multivip-overview.md).

Interní Load Balancer můžete kombinovat s jakýmkoli proxy třetích stran nebo použít interní [Application Gateway](../application-gateway/overview.md) pro scénáře proxy pomocí protokolu HTTP/HTTPS. I když můžete použít veřejný Load Balancer k zmírnění tohoto problému, je výsledný scénář náchylný k [vyčerpání SNAT](load-balancer-outbound-connections.md). Vyhněte se tomuto druhému přístupu, pokud se pečlivě nespravuje.

## <a name="symptom-cannot-change-backend-port-for-existing-lb-rule-of-a-load-balancer-which-has-vm-scale-set-deployed-in-the-backend-pool"></a>Příznak: nejde změnit back-end port pro stávající pravidlo pro vyrovnávání zatížení, které má ve fondu back-end nasazenou službu VM Scale set. 
### <a name="cause--the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-vm-scale-set"></a>Příčina: port back-endu nejde upravit pro pravidlo vyrovnávání zatížení, které používá sonda stavu pro nástroj pro vyrovnávání zatížení, na který odkazuje sada škálování virtuálního počítače.
**Řešení** Pokud chcete změnit port, můžete odstranit sondu stavu tak, že aktualizujete sadu škálování virtuálního počítače, aktualizujte port a pak znovu nakonfigurujete test stavu.

## <a name="symptom-small-traffic-is-still-going-through-load-balancer-after-removing-vms-from-backend-pool-of-the-load-balancer"></a>Příznak: po odebrání virtuálních počítačů ze back-endu fondu pro vyrovnávání zatížení stále probíhá malý provoz prostřednictvím nástroje pro vyrovnávání zatížení. 
### <a name="cause--vms-removed-from-backend-pool-should-no-longer-receive-traffic-the-small-amount-of-network-traffic-could-be-related-to-storage-dns-and-other-functions-within-azure"></a>Příčina: virtuální počítače odebrané z back-end fondu by už neměly přijímat přenosy. Malé množství síťových přenosů může souviset s úložištěm, DNS a dalšími funkcemi v Azure. 
K ověření můžete provést síťové trasování. Plně kvalifikovaný název domény, který se používá pro účty BLOB Storage, je uvedený v části vlastnosti každého účtu úložiště.  Z virtuálního počítače v rámci vašeho předplatného Azure můžete pomocí nástroje Nslookup určit IP adresu Azure přiřazenou k tomuto účtu úložiště.

## <a name="additional-network-captures"></a>Další síťové zachycení
Pokud se rozhodnete otevřít případ podpory, shromážděte při rychlejším řešení následující informace. Vyberte jeden back-end virtuální počítač pro provedení následujících testů:
- Pomocí Psping z jednoho ze back-end virtuálních počítačů v rámci virtuální sítě otestujte odpověď na port testu paměti (příklad: Psping 10.0.0.4:3389) a výsledky záznamu. 
- Pokud se v těchto testech testu příkazu otestuje žádná odpověď, spusťte pro virtuální počítač back-end a virtuální počítač s testovacím virtuálním počítačem při spuštění PsPing a pak zastavte trasování Netsh. 
 
## <a name="symptom-load-balancer-in-failed-state"></a>Příznak: Load Balancer ve stavu selhání 

**Řešení**

- Jakmile identifikujete prostředek, který je ve stavu selhání, přejdete na [Azure Resource Explorer](https://resources.azure.com/) a Identifikujte prostředek v tomto stavu. 
- Aktualizujte přepínač na pravém horním rohu pro čtení a zápis.
- Klikněte na upravit pro prostředek v neúspěšném stavu.
- Klikněte na tlačítko Vložit a potom na tlačítko získat a ověřte, zda byl stav zřizování aktualizován na úspěšné.
- Potom můžete pokračovat v provádění dalších akcí, protože prostředek je neúspěšného stavu.


## <a name="next-steps"></a>Další kroky

Pokud předchozí kroky problém nevyřeší, otevřete [lístek podpory](https://azure.microsoft.com/support/options/).