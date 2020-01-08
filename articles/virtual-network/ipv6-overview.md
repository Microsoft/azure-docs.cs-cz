---
title: Přehled protokolu IPv6 pro Azure Virtual Network (Preview)
titlesuffix: Azure Virtual Network
description: Popis IPv6 koncových bodů IPv6 a cest k datům ve službě Azure Virtual Network.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 12/19/2019
ms.author: kumud
ms.openlocfilehash: a44f99b5a7f25a7350e6a63cf85c6b2287647162
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75373243"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>Co je protokol IPv6 pro Azure Virtual Network? (Preview)

Protokol IPv6 pro Azure Virtual Network (VNet) umožňuje hostovat aplikace v Azure s připojením IPv6 a IPv4 jak v rámci virtuální sítě, tak i z Internetu. V důsledku vyčerpání veřejných IPv4 adres jsou nové sítě pro mobilitu a Internet věcí (IoT) často založené na protokolu IPv6. Dokonce i dlouhodobě zavedené sítě poskytovatele internetových služeb a mobilních sítí jsou transformované na IPv6. Služby jenom s protokolem IPv4 můžou na stávajících i nově vznikajících trzích najít skutečnou nevýhody. Připojení IPv4/IPv6 s duálním zásobníkem umožňuje službám hostovaným v Azure procházet tuto softwarovou mezeru pomocí globálně dostupných, duálních služeb, které se dají snadno připojit k existujícím IPv4 i těmto novým zařízením a sítím s protokolem IPv6.

Původní připojení IPv6 v Azure usnadňuje poskytování připojení k internetu přes duální zásobník (IPv4/IPv6) pro aplikace hostované v Azure. Umožňuje jednoduché nasazení virtuálních počítačů s připojením IPv6 s vyrovnáváním zatížení pro příchozí i odchozí připojení iniciovaná. Tato funkce je stále k dispozici a další informace jsou k dispozici [zde](../load-balancer/load-balancer-ipv6-overview.md).
Protokol IPv6 pro službu Azure Virtual Network je mnohem více úplný a umožňuje nasazení úplných architektur řešení IPv6 v Azure.

> [!Important]
> Protokol IPv6 pro Azure Virtual Network je momentálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Následující diagram znázorňuje jednoduché nasazení duálního zásobníku (IPv4/IPv6) v Azure:

![Diagram nasazení sítě IPv6](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Výhody

Protokol IPv6 pro výhody virtuální sítě Azure:

- Pomáhá rozšířit dosah vašich aplikací hostovaných v Azure na rostoucí mobilní a Internet věcí trhy.
- Duální skládané virtuální počítače IPv4/IPv6 poskytují maximální flexibilitu nasazení služeb. Jedna instance služby se může připojit k internetovým klientům podporujícím IPv4 i IPv6.
- Vychází z dlouhotrvajícího a stabilního připojení k protokolu IPv6 mezi virtuálními počítači v síti Azure.
- Zabezpečení ve výchozím nastavení, protože připojení IPv6 k Internetu je navázáno pouze v případě, že je výslovně vyžádáte ve svém nasazení.

## <a name="capabilities"></a>Možnosti

Protokol IPv6 pro virtuální síť Azure zahrnuje tyto funkce:

- Zákazníci Azure můžou definovat vlastní adresní prostor virtuální sítě IPv6, který bude vyhovovat potřebám svých aplikací, zákazníkům nebo bezproblémové integraci do svého místního IP prostoru.
- Virtuální sítě duálního zásobníku (IPv4 a IPv6) s podsítěmi s duálním zásobníkem umožňují aplikacím připojení pomocí prostředků IPv4 i IPv6 ve své virtuální síti nebo v Internetu.
    > [!IMPORTANT]
    > Podsítě pro protokol IPv6 musí mít velikost přesně/64.  To zajistí budoucí kompatibilitu, pokud se rozhodnete povolit směrování podsítě do místní sítě, protože některé směrovače můžou přijmout jenom trasy IPv6 (/64).  
- Chraňte své prostředky pomocí pravidel IPv6 pro skupiny zabezpečení sítě.
    - A ochrana distribuovaných služeb DOS (DDoS) platformy Azure se rozšiřuje na internetovou veřejnou IP adresu.
- Přizpůsobte směrování provozu protokolu IPv6 ve virtuální síti pomocí uživatelem definovaných tras – zejména při využití síťových virtuálních zařízení k rozšíření vaší aplikace.
- Linux a Windows Virtual Machines můžou všechny používat protokol IPv6 pro virtuální síť Azure.
- [Standardní podpora veřejných Load Balancer protokolu IPv6](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) pro vytváření odolných a škálovatelných aplikací, mezi které patří:
    - Volitelná sonda stavu protokolu IPv6, která určuje, které instance fondu back-endu jsou v pořádku, a tedy může přijímat nové toky.
    - Volitelná odchozí pravidla, která poskytují úplnou deklarativní kontrolu nad odchozím připojením ke škálování a optimalizaci této možnosti podle vašich konkrétních potřeb.
    - Volitelné vícenásobné konfigurace front-endu, které umožňují, aby jeden nástroj pro vyrovnávání zatížení používal více veřejných IP adres IPv6 – stejný protokol a port front-end je možné znovu použít napříč adresami front-endu.
    - Volitelné porty IPv6 se dají znovu použít na back-endové instance pomocí *plovoucí IP* funkce pravidel vyrovnávání zatížení. 
- [Standardní podpora interních Load Balancer IPv6](ipv6-dual-stack-standard-internal-load-balancer-powershell.md) pro vytváření odolných vícevrstvých aplikací v rámci služby Azure virtuální sítě.  
- Základní podpora protokolu IPv6 Public Load Balancer pro kompatibilitu se staršími nasazeními
- [Vyhrazené veřejné IP adresy a rozsahy adres IPv6](ipv6-public-ip-address-prefix.md) poskytují stabilní a předvídatelné adresy IPv6, které usnadňují seznamování vašich aplikací hostovaných v Azure pro vaši firmu a zákazníky.
- Veřejná IP adresa na úrovni instance poskytuje připojení k Internetu IPv6 přímo k jednotlivým virtuálním počítačům.
- [Přidat IPv6 do existujících nasazení jenom s protokolem IPv4](ipv6-add-to-existing-vnet-powershell.md)– Tato funkce umožňuje snadno přidat připojení IPv6 k existujícím nasazením jenom s protokolem IPv4, aniž by bylo potřeba znovu vytvářet nasazení.  Síťový provoz IPv4 není během tohoto procesu ovlivněný, takže v závislosti na vaší aplikaci a operačním systému budete moct protokol IPv6 přidat i do živých služeb.    
- Umožněte internetovým klientům bezproblémový přístup k aplikaci s duálním zásobníkem pomocí vlastního protokolu, který je Azure DNS podpora záznamů IPv6 (AAAA). 
- Vytvářejte aplikace s duálním zásobníkem, které se automaticky škálují na zatížení pomocí služby Virtual Machine Scale Sets s protokolem IPv6.
- [Partnerský vztah Virtual Network (VNET)](virtual-network-peering-overview.md) – v rámci regionálního i globálního partnerského vztahu – umožňuje bezproblémové připojit duální zásobník virtuální sítě – koncové body IPv4 a IPv6 na virtuálních počítačích v partnerských sítích budou moct vzájemně komunikovat. Můžete dokonce i partnerský duální zásobník s virtuální sítě jenom s protokolem IPv4, protože přecházíte mezi nasazeními do duálního zásobníku. 
- Řešení potíží a diagnostiky protokolu IPv6 jsou k dispozici s metrikami vyrovnávání zátěže/výstrahami a Network Watcher funkcemi, jako je zachycení paketů, protokoly toků NSG, řešení potíží s připojením a monitorování připojení.   

## <a name="scope"></a>Rozsah
Protokol IPv6 pro virtuální síť Azure je základní sada funkcí, která zákazníkům umožňuje hostovat aplikace s duálním zásobníkem (IPv4 + IPv6) v Azure.  Chystáme se přidat podporu protokolu IPv6 do více síťových funkcí Azure v průběhu času a nakonec nabízet duální verze služby Azure PaaS, ale mezitím všechny služby Azure PaaS lze využívat prostřednictvím koncových bodů IPv4 v duálním zásobníku Virtual Machines.   

## <a name="limitations"></a>Omezení
Aktuální protokol IPv6 pro vydanou verzi služby Azure Virtual Network má následující omezení:
- Protokol IPv6 pro Azure Virtual Network (Preview) je k dispozici ve všech globálních oblastech Azure, ale v cloudech státní správy je jenom v globálním Azure.
- V síti VNET s povoleným protokolem IPv6 se nedají používat Express Route a brány VPN Gateway, a to buď přímo, nebo s partnerským vztahem "UseRemoteGateway". 
- Platforma Azure (AKS atd.) nepodporuje komunikaci IPv6 pro kontejnery.  

## <a name="pricing"></a>Ceny

Prostředky a šířka pásma Azure Azure se účtují stejnou sazbou jako IPv4. Pro protokol IPv6 se neúčtují žádné další ani jiné poplatky. Můžete najít podrobnosti o cenách pro [veřejné IP adresy](https://azure.microsoft.com/pricing/details/ip-addresses/), [šířku pásma sítě](https://azure.microsoft.com/pricing/details/bandwidth/)nebo [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [nasadit aplikaci s duálním zásobníkem IPv6 pomocí Azure PowerShell](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md).
- Naučte se, jak [pomocí Azure CLI nasadit aplikaci s duálním zásobníkem IPv6](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md).
- Přečtěte si, jak [nasadit aplikaci s duálním zásobníkem IPv6 pomocí šablon Správce prostředků (JSON)](ipv6-configure-standard-load-balancer-template-json.md) .
