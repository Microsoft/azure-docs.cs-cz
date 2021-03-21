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
ms.openlocfilehash: bfe2b21a86f2ce4b4630ba69cde87796fd367f4b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98029175"
---
# <a name="troubleshoot-azure-load-balancer-backend-traffic-responses"></a>Řešení potíží s Azure Load Balancermi odpověďmi na back-end

Tato stránka poskytuje informace pro řešení potíží s Azure Load Balancer.

## <a name="vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Virtuální počítače za Load Balancer nereagují na provoz na nakonfigurovaném datovém portu.

Pokud je virtuální počítač s back-end fondem uveden jako v pořádku a reaguje na sondy stavu, ale stále se neúčastní vyrovnávání zatížení nebo nereaguje na přenos dat, může to být z následujících důvodů:
* Virtuální počítač Load Balancerového fondu back-endu nenaslouchá na datovém portu.
* Skupina zabezpečení sítě blokuje port ve virtuálním počítači Load Balancer fondu back-endu.  
* Přístup k Load Balancer ze stejného virtuálního počítače a síťové karty
* Přístup k Internetu Load Balancer front-endu z virtuálního počítače fondu back-endu Load Balancer

## <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Příčina 1: Load Balancer virtuální počítač fondu back-endu nenaslouchá na datovém portu

Pokud virtuální počítač nereaguje na přenos dat, může to být způsobeno tím, že cílový port není otevřen na daném virtuálním počítači, nebo virtuální počítač na tomto portu nenaslouchá. 

**Ověření a rozlišení**

1. Přihlaste se k virtuálnímu počítači back-end. 
2. Otevřete příkazový řádek a spuštěním následujícího příkazu ověřte, že aplikace naslouchá na datovém portu:  
            netstat – a 
3. Pokud port není uveden se stavem "NASLOUCHÁní", nakonfigurujte správný port naslouchacího procesu. 
4. Pokud je port označený jako naslouchání, zkontrolujte u cílové aplikace na tomto portu případné problémy.

## <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Příčina 2: Skupina zabezpečení sítě blokuje port ve virtuálním počítači Load Balancer fondu back-endu.  

Pokud jedna nebo více skupin zabezpečení sítě nakonfigurovaných v podsíti nebo na VIRTUÁLNÍm počítači blokuje zdrojovou IP adresu nebo port, virtuální počítač nemůže odpovědět.

Pro veřejný Nástroj pro vyrovnávání zatížení se IP adresa internetových klientů použije pro komunikaci mezi klienty a back-end virtuálními počítači nástroje pro vyrovnávání zatížení. Ujistěte se, že IP adresa klientů je povolená ve skupině zabezpečení virtuálního počítače back-endu.

1. Vypíše skupiny zabezpečení sítě nakonfigurované na virtuálním počítači back-end. Další informace najdete v tématu [Správa skupin zabezpečení sítě](../virtual-network/manage-network-security-group.md) .
1. V seznamu skupin zabezpečení sítě ověřte, zda:
    - příchozí nebo odchozí provoz na datovém portu má rušivý vliv. 
    - **zamítne všechna** pravidla skupiny zabezpečení sítě na síťové kartě virtuálního počítače nebo podsítě s vyšší prioritou, jako je výchozí pravidlo, které umožňuje Load Balancer sondy a provozu (skupiny zabezpečení sítě musí umožňovat Load Balancer IP adresy 168.63.129.16, což je port testu).
1. Pokud některá pravidla blokují provoz, odeberte a překonfigurujte tato pravidla, aby umožňovala přenos dat.  
1. Otestujte, jestli virtuální počítač teď začal reagovat na sondy stavu.

## <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Příčina 3: přístup k Load Balancer ze stejného virtuálního počítače a síťového rozhraní 

Pokud se vaše aplikace hostovaná na virtuálním počítači back-endu Load Balancer pokouší o přístup k jiné aplikaci hostované ve stejném virtuálním počítači back-end přes stejné síťové rozhraní, jedná se o nepodporovaný scénář a nezdaří se. 

**Řešení** Tento problém můžete vyřešit pomocí jedné z následujících metod:
* Nakonfigurujte samostatné virtuální počítače back-end fondu na aplikaci. 
* Nakonfigurujte aplikaci na virtuálních počítačích s více síťovými kartami, aby každá aplikace používala vlastní síťové rozhraní a IP adresu. 

## <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>Příčina 4: přístup k internímu Load Balancer front-endu z virtuálního počítače fondu back-endu Load Balancer

Pokud je interní Load Balancer nakonfigurovaný v rámci virtuální sítě a jeden z back-end účastníka se snaží získat přístup k internímu front-endu Load Balancer, můžou se selhání vyskytnout, když se tok namapuje na původní virtuální počítač. Takový scénář se nepodporuje.

**Řešení** Existuje několik způsobů, jak tento scénář odblokovat, včetně použití proxy serveru. Vyhodnoťte Application Gateway nebo jiné proxy servery třetích stran (například Nginx nebo HAProxy). Další informace o Application Gateway najdete v tématu [přehled Application Gateway](../application-gateway/overview.md)

**Podrobnosti o** Interní nástroje pro vyrovnávání zatížení nepřevádějí odchozí vytvořená připojení na front-end interního Load Balancer, protože obě jsou v privátním adresním prostoru IP adres. Veřejné nástroje pro vyrovnávání zatížení poskytují [odchozí připojení](load-balancer-outbound-connections.md) z privátních IP adres uvnitř virtuální sítě k veřejným IP adresám. U interních nástrojů pro vyrovnávání zatížení tento přístup zabraňuje možnému vyčerpání portů SNAT v rámci jedinečného interního adresního prostoru IP adres, kde není požadován překlad.

Vedlejším účinkem je to, že pokud odchozí tok z virtuálního počítače v záložním fondu pokusy o tok do front-endu interního Load Balancer ve svém fondu _a_ namapuje se zpátky na sebe, dvě ramena toku se neshodují. Vzhledem k tomu, že se neshodují, tok se nezdařil. Tok se úspěšně dokončí, pokud tok nemapoval zpátky na stejný virtuální počítač ve fondu back-end, který vytvořil tok do front-endu.

Když se tok mapuje zpátky na sebe samé, zdá se, že odchozí tok pochází z virtuálního počítače na front-end a odpovídající příchozí tok se zdá, že pochází z virtuálního počítače do sebe samé. Z pohledu hostovaného operačního systému se v rámci tohoto virtuálního počítače neshodují vstupní a výstupní části stejného toku. Zásobník TCP nerozpozná tyto poloviny stejného toku jako součást stejného toku. Zdroj a cíl se neshodují. Když se tok mapuje na jakýkoli jiný virtuální počítač ve fondu back-end, budou se tyto poloviny toku shodovat a virtuální počítač může na tento tok reagovat.

Příznakem pro tento scénář je přerušovaná prodleva připojení, když se tok vrátí ke stejnému back-endu, který daný tok vytvořil. K běžným řešením patří vložení vrstvy proxy za interní Load Balancer a použití pravidel stylu "přímé vrácení serveru (DSR)". Další informace najdete v tématu [více front-endu pro Azure Load Balancer](load-balancer-multivip-overview.md).

Interní Load Balancer můžete kombinovat s jakýmkoli proxy třetích stran nebo použít interní [Application Gateway](../application-gateway/overview.md) pro scénáře proxy pomocí protokolu HTTP/HTTPS. I když můžete použít veřejný Load Balancer k zmírnění tohoto problému, je výsledný scénář náchylný k [vyčerpání SNAT](load-balancer-outbound-connections.md). Vyhněte se tomuto druhému přístupu, pokud se pečlivě nespravuje.

## <a name="next-steps"></a>Další kroky

Pokud předchozí kroky problém nevyřeší, otevřete [lístek podpory](https://azure.microsoft.com/support/options/).