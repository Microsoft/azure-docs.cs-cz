---
title: Řešení běžných problémů Azure Load Balancer
description: Naučte se řešit běžné problémy s Azure Load Balancer.
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
ms.openlocfilehash: cddaf1bde84d7e60eb59bd4c58c65fa889e06ae3
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028807"
---
# <a name="troubleshoot-azure-load-balancer"></a>Řešení potíží s nástrojem pro vyrovnávání zatížení Azure

Tato stránka poskytuje informace pro řešení běžných otázek Azure Load Balancer Basic a Standard. Další informace o Standard Load Balancer najdete v tématu [Standard Load Balancer Overview](load-balancer-standard-diagnostics.md).

Když je připojení Load Balancer nedostupné, nejběžnější příznaky jsou následující:

- Virtuální počítače za Load Balancer nereagují na sondy stavu. 
- Virtuální počítače za Load Balancer nereagují na provoz na nakonfigurovaném portu.

Když externí klienti back-end virtuálních počítačů procházejí nástrojem pro vyrovnávání zatížení, použijí se pro tuto komunikaci IP adresa klientů. Ujistěte se, že se IP adresa klientů přidala do seznamu povolených NSG.

## <a name="no-outbound-connectivity-from-standard-internal-load-balancers-ilb"></a>Žádné odchozí připojení ze standardních interních nástrojů pro vyrovnávání zatížení (interního nástroje)

**Ověření a rozlišení**

Standardní ILBs jsou **ve výchozím nastavení zabezpečené**. Základní ILBs povoluje připojení k Internetu prostřednictvím *skryté* veřejné IP adresy. Nedoporučuje se pro produkční úlohy, protože IP adresa není ani statická ani uzamčena prostřednictvím skupin zabezpečení sítě, kterou vlastníte. Pokud jste nedávno přesunuli ze základní interního nástroje na standardní interního nástroje, měli byste vytvořit veřejnou IP adresu explicitně prostřednictvím konfigurace [jenom odchozího](egress-only.md) přenosu, která uzamkne IP přes skupin zabezpečení sítě. V podsíti můžete také použít [bránu NAT](../virtual-network/nat-overview.md) .

## <a name="cant-change-backend-port-for-existing-lb-rule-of-a-load-balancer-that-has-virtual-machine-scale-set-deployed-in-the-backend-pool"></a>Nemůžete změnit back-end port pro stávající pravidlo pro vyrovnávání zatížení, které má nasazenou sadu škálování virtuálních počítačů ve fondu back-endu.

### <a name="cause-the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-virtual-machine-scale-set"></a>Příčina: port back-endu nejde upravit pro pravidlo vyrovnávání zatížení, které používá sonda stavu pro nástroj pro vyrovnávání zatížení, na který odkazuje sada škálování virtuálního počítače.

**Řešení** Pokud chcete změnit port, můžete odstranit sondu stavu tak, že aktualizujete sadu škálování virtuálního počítače, aktualizujete port a pak znovu nakonfigurujete test stavu.

## <a name="small-traffic-is-still-going-through-load-balancer-after-removing-vms-from-backend-pool-of-the-load-balancer"></a>Po odebrání virtuálních počítačů ze back-endu fondu nástroje pro vyrovnávání zatížení stále probíhá malý provoz prostřednictvím nástroje pro vyrovnávání zatížení.

### <a name="cause-vms-removed-from-backend-pool-should-no-longer-receive-traffic-the-small-amount-of-network-traffic-could-be-related-to-storage-dns-and-other-functions-within-azure"></a>Příčina: virtuální počítače odebrané z back-end fondu by už neměly přijímat přenosy. Malé množství síťových přenosů může souviset s úložištěm, DNS a dalšími funkcemi v Azure.

K ověření můžete provést síťové trasování. Plně kvalifikovaný název domény, který se používá pro účty BLOB Storage, je uvedený v části vlastnosti každého účtu úložiště.  Z virtuálního počítače v rámci vašeho předplatného Azure můžete pomocí příkazu nslookup určit IP adresu Azure přiřazenou k tomuto účtu úložiště.

## <a name="additional-network-captures"></a>Další síťové zachycení

Pokud se rozhodnete otevřít případ podpory, shromážděte při rychlejším řešení následující informace. Vyberte jeden back-end virtuální počítač pro provedení následujících testů:

- K otestování odpovědi na port testu použijte z jednoho z back-end virtuálních počítačů na konci virtuálního počítače příkazem PS test Response (příklad: PS test 10.0.0.4:3389) a výsledky záznamu. 
- Pokud se v těchto testech testu příkazu otestuje žádná odpověď, spusťte pro virtuální počítač back-end a virtuální počítač s testovacím virtuálním počítačem při spuštění PsPing a pak zastavte trasování Netsh.

## <a name="load-balancer-in-failed-state"></a>Load Balancer ve stavu selhání

**Řešení**

- Jakmile identifikujete prostředek, který je ve stavu selhání, přejdete na [Azure Resource Explorer](https://resources.azure.com/) a Identifikujte prostředek v tomto stavu.
- Aktualizujte přepínač v pravém horním rohu pro čtení a zápis.
- Klikněte na upravit pro prostředek v neúspěšném stavu.
- Klikněte na tlačítko Vložit a potom na tlačítko získat a ověřte, zda byl stav zřizování aktualizován na úspěšné.
- Pak můžete pokračovat dalšími akcemi, protože prostředek je neúspěšného stavu.

## <a name="next-steps"></a>Další kroky

Pokud předchozí kroky problém nevyřeší, otevřete [lístek podpory](https://azure.microsoft.com/support/options/).
