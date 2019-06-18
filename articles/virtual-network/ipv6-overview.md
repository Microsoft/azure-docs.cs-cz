---
title: Přehled protokolu IPv6 pro virtuální sítě Azure (Preview)
titlesuffix: Azure Virtual Network
description: IPv6 popis cesty koncových bodů a data protokolu IPv6 ve službě Azure virtual network.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: 0ec650880a45f6383b24b5ac810fc2ee745806b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "62130876"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>Co je protokol IPv6 pro Azure Virtual Network? (Preview)

Protokol IPv6 pro Azure Virtual Network (VNET) umožňuje hostování aplikací v Azure s připojením k IPv6 a IPv4 i v rámci virtuální sítě a do a z Internetu. Z důvodu vyčerpání veřejné IPv4 adresy jsou často nové sítě pro mobilitu a Internet věcí (IoT) postavená na IPv6. Dokonce i dlouho zřizuje poskytovatele internetových služeb a mobilních sítí jsou prováděna transformace na IPv6. Výhradně s protokolem IPv4 služby můžete dostat na skutečné nevýhodou stávajících a nově vznikajícími trhy. Duálním zásobníkem připojení IPv4/IPv6 umožňuje služeb hostovaných v Azure k procházení během této technologie s globálně dostupné, skládaný dvou služeb, které se snadno připojit s existující IPv4 a tato nová zařízení IPv6 i sítě.

Původní připojení IPv6 pro Azure umožňuje snadno poskytnout duálním zásobníkem připojení k Internetu (IPv4 nebo IPv6) pro aplikace hostované v Azure. Umožňuje jednoduché nasazení virtuálních počítačů s připojením s vyrovnáváním zatížení IPv6 pro příchozí a odchozí připojení. Tato funkce je stále k dispozici a další informace jsou k dispozici [tady](../load-balancer/load-balancer-ipv6-overview.md).
IPv6 pro virtuální síť Azure je mnohem více úplné vybrané – povolení úplné architektury řešení IPv6 pro nasazení v Azure.

> [!Important]
> Protokol IPv6 pro Azure Virtual Network je aktuálně ve verzi public preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Následující diagram znázorňuje jednoduchý duálním zásobníkem (IPv4 nebo IPv6) nasazení v Azure:

![Diagram nasazení sítě IPv6](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Výhody

Výhody Azure IPv6 virtuální sítě:

- Pomáhá rozšířit dosah hostovaných v Azure aplikace do stále se rozšiřující mobilní a Internet of Things trhy.
- Dva virtuální počítače skládaný IPv4/IPv6 poskytují flexibilitu maximální provozní nasazení. Instance jedné službě můžou připojit k IPv4 a IPv6 podporující internetové klienty.
- Sestavení na tradici, stabilní Azure VM-na Internetu připojení IPv6.
- Ve výchozím nastavení zabezpečení, protože připojení IPv6 k Internetu je navázán pouze tehdy, když ho explicitně vyžádat ve vašem nasazení.

## <a name="capabilities"></a>Možnosti

Podpora protokolu IPv6 pro virtuální počítače zahrnují následující funkce:

- Zákazníci Azure mohou definovat adresní prostor virtuální sítě vlastní IPv6 pro potřeby svých aplikací, zákazníky, nebo bez problémů integrovat do své místní adresní prostor IP.
- Duálním zásobníkem (IPv4 a IPv6) virtuální sítě s duálním zásobníkem podsítěmi umožňují aplikacím připojit k prostředkům IPv4 a IPv6 ve své virtuální síti nebo – k Internetu.
- Chraňte své prostředky s pravidly protokolu IPv6 pro skupiny zabezpečení sítě
- Přizpůsobení, směrování provozu IPv6 ve službě virtual network s uživatelem definované trasy – zejména v případě využívání síťových virtuálních zařízení pro rozšíření vaší aplikace.
- Nástroj pro vyrovnávání zatížení IPv6 podpory vytvářet odolné, škálovatelné aplikace, včetně podpory Azure DNS záznamů AAAA pro veřejné IP adresy protokolu IPv6.
- Snadno přidáte připojení IPv6 k existující nasazení výhradně s protokolem IPv4 se Upgrade na místě.
- Vytváření aplikací duálním zásobníkem, které automaticky škálovat na zatížení se škálovacími sadami virtuálních počítačů.

## <a name="limitations"></a>Omezení
Verze preview protokolu IPv6 pro Azure virtual network má následující omezení:
- Protokol IPv6 pro virtuální sítě Azure (Preview) je k dispozici ve všech globálních oblastech Azure, ale pouze v globální Azure - není cloudů pro státní správu.   
- Podporu pro verzi preview je omezena na Zobrazit pouze pro mnoho, ale ne všechny konfigurace protokolu IPv6, ale protokol IPv6 pro virtuální síť má plnou podporu a dokumentaci (s ukázkami) k nasazení IPv6 pomocí Azure Powershellu a rozhraní příkazového řádku (CLI).
- Podpora sledovací proces sítě ve verzi Preview je omezena na protokoly toků NSG a zachytí síťového paketu.
- Podpora služby Vyrovnávání zatížení pro verzi preview bude od počátku omezena na základní nástroje pro vyrovnávání zatížení.
- Veřejné IP adresy na úrovni instance (veřejné IP adresy přímo na virtuálním počítači) není podporovaná ve verzi preview.  
- Partnerské vztahy virtuálních sítí (regionálně nebo globálně) není podporováno ve verzi preview. 

## <a name="pricing"></a>Ceny

Prostředky IPv6 Azure a šířky pásma jsou účtovány ve stejné výši jako pro protokol IPv4. Neúčtují žádné další nebo jiné poplatky pro protokol IPv6. Můžete najít podrobnosti o cenách pro [veřejné IP adresy](https://azure.microsoft.com/pricing/details/ip-addresses/), [šířka pásma sítě](https://azure.microsoft.com/pricing/details/bandwidth/), nebo [nástroje pro vyrovnávání zatížení](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [nasazení aplikace duálním zásobníkem IPv6 pomocí Azure Powershellu](virtual-network-ipv4-ipv6-dual-stack-powershell.md).
- Zjistěte, jak [nasazení aplikace duálním zásobníkem IPv6 pomocí rozhraní příkazového řádku Azure](virtual-network-ipv4-ipv6-dual-stack-cli.md).
