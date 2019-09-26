---
title: Plánování a připojení k síti pro Azure AD Domain Services | Microsoft Docs
description: Přečtěte si o některých faktorech návrhu virtuální sítě a o prostředcích, které se používají pro připojení při spuštění Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 23a857a5-2720-400a-ab9b-1ba61e7b145a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 08/09/2019
ms.author: iainfou
ms.openlocfilehash: 81d20a973454db600d8be9ce036f001dd41784e7
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71314995"
---
# <a name="virtual-network-design-considerations-and-configuration-options-for-azure-ad-domain-services"></a>Požadavky na návrh virtuální sítě a možnosti konfigurace pro Azure AD Domain Services

Jak Azure Active Directory Domain Services (služba AD DS) poskytuje službám pro ověřování a správu jiné aplikace a úlohy, síťové připojení je klíčová komponenta. Bez patřičně nakonfigurovaných prostředků virtuální sítě nemůžou aplikace a úlohy komunikovat se službou a používat funkce, které poskytuje Azure služba AD DS. Pokud svou virtuální síť naplánujete správně, ujistěte se, že Azure služba AD DS může podle potřeby obsluhovat vaše aplikace a úlohy.

Tento článek popisuje požadavky na návrh a požadavky pro virtuální síť Azure, které podporují Azure služba AD DS.

## <a name="azure-virtual-network-design"></a>Návrh virtuální sítě Azure

Pokud chcete zajistit připojení k síti a umožnit aplikacím a službám ověřování v Azure služba AD DS, použijte virtuální síť Azure a podsíť. V ideálním případě by se měl Azure služba AD DS nasadit do své vlastní virtuální sítě. Můžete zahrnout samostatnou podsíť aplikace ve stejné virtuální síti, která bude hostovat vaše virtuální počítače pro správu nebo úlohy lehké aplikace. Nejvhodnějším návrhem je obvykle samostatná virtuální síť pro větší nebo složitou aplikační úlohu, která je rovnocenná službě Azure služba AD DS Virtual Network. Další možnosti návrhů jsou platné, a to za předpokladu, že splňujete požadavky popsané v následujících částech pro virtuální síť a podsíť.

Při návrhu virtuální sítě pro Azure služba AD DS platí následující požadavky:

* Azure služba AD DS musí být nasazené do stejné oblasti Azure jako vaše virtuální síť.
    * V tuto chvíli můžete nasadit jenom jednu spravovanou doménu Azure služba AD DS spravované domény na tenanta Azure AD. Spravovaná doména Azure služba AD DS je nasazená v jedné oblasti. Ujistěte se, že jste vytvořili nebo vybrali virtuální síť v [oblasti, která podporuje Azure služba AD DS](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all).
* Zvažte blízkost ostatních oblastí Azure a virtuálních sítí, které hostují úlohy vaší aplikace.
    * Chcete-li minimalizovat latenci, udržujte základní aplikace blízko nebo ve stejné oblasti jako podsíť virtuální sítě pro spravovanou doménu Azure služba AD DS. Mezi virtuálními sítěmi Azure můžete použít připojení VPN s partnerským vztahem nebo virtuální privátní síť (VPN).
* Virtuální síť nemůže spoléhat na služby DNS kromě těch, které poskytuje Azure služba AD DS.
    * Azure služba AD DS poskytuje vlastní službu DNS. Virtuální síť musí být nakonfigurovaná tak, aby používala tyto adresy služby DNS. Překlad názvů pro další obory názvů se dá provést pomocí podmíněného dopředných služeb.
    * Vlastní nastavení serveru DNS nemůžete použít k přímému nasměrování dotazů z jiných serverů DNS, včetně virtuálních počítačů. Prostředky ve virtuální síti musí používat službu DNS poskytovanou službou Azure služba AD DS.

> [!IMPORTANT]
> Po povolení služby nemůžete přesunout služba AD DS Azure do jiné virtuální sítě.

Spravovaná doména Azure služba AD DS se připojuje k podsíti ve službě Azure Virtual Network. Navrhněte tuto podsíť pro Azure služba AD DS s následujícími požadavky:

* Azure služba AD DS musí být nasazené ve vlastní podsíti. Nepoužívejte existující podsíť ani podsíť brány.
* Skupina zabezpečení sítě se vytvoří během nasazování spravované domény služba AD DS Azure. Tato skupina zabezpečení sítě obsahuje požadovaná pravidla pro správnou komunikaci služby.
    * Nevytvářejte ani nepoužívejte existující skupinu zabezpečení sítě s vlastními pravidly.
* Azure služba AD DS vyžaduje IP adresy 3-5. Ujistěte se, že rozsah IP adres podsítě může poskytovat tento počet adres.
    * Omezení dostupných IP adres může zabránit Azure AD Domain Services v údržbě dvou řadičů domény.

Následující příklad diagramu popisuje platný návrh, ve kterém má Azure služba AD DS vlastní podsíť, existuje podsíť brány pro externí připojení a úlohy aplikací jsou v připojené podsíti v rámci virtuální sítě:

![Doporučený návrh podsítě](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

## <a name="connections-to-the-azure-ad-ds-virtual-network"></a>Připojení k virtuální síti Azure služba AD DS

Jak je uvedeno v předchozí části, můžete v Azure vytvořit jenom Azure AD Domain Services spravovanou doménu jenom v jedné virtuální síti a pro každého tenanta Azure AD se dá vytvořit jenom jedna spravovaná doména. Na základě této architektury možná budete muset připojit jednu nebo více virtuálních sítí, které hostují úlohy vaší aplikace, do vaší virtuální sítě Azure služba AD DS.

Aplikační úlohy hostované v jiných virtuálních sítích Azure můžete připojit pomocí jedné z následujících metod:

* Partnerské vztahy virtuálních sítí
* Virtuální privátní sítě (VPN)

### <a name="virtual-network-peering"></a>Partnerský vztah virtuální sítě

Partnerský vztah virtuálních sítí je mechanismus, který propojuje dvě virtuální sítě ve stejné oblasti prostřednictvím páteřní sítě Azure. Globální partnerské vztahy virtuálních sítí se můžou připojit k virtuální síti napříč oblastmi Azure. Po navázání partnerského vztahu mezi dvěma virtuálními sítěmi umožníte komunikaci přímo pomocí privátních IP adres, jako jsou třeba virtuální počítače. Pomocí partnerského vztahu virtuálních sítí můžete nasadit Azure služba AD DS spravované domény pomocí úloh aplikací nasazených v jiných virtuálních sítích.

![Připojení k virtuální síti pomocí partnerského vztahu](./media/active-directory-domain-services-design-guide/vnet-peering.png)

Další informace najdete v tématu [Přehled partnerských vztahů virtuálních sítí Azure](../virtual-network/virtual-network-peering-overview.md).

### <a name="virtual-private-networking"></a>Virtuální privátní sítě

Virtuální síť můžete připojit k jiné virtuální síti (VNet-to-VNet) stejným způsobem, jakým můžete nakonfigurovat virtuální síť na místní umístění lokality. Obě připojení používají bránu VPN k vytvoření zabezpečeného tunelového propojení pomocí protokolu IPsec/IKE. Tento model připojení umožňuje nasadit Azure služba AD DS do virtuální sítě Azure a pak připojit místní umístění nebo jiné cloudy.

![Připojení k virtuální síti pomocí VPN Gateway](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

Další informace o používání virtuálních privátních sítí najdete v tématu [Konfigurace připojení brány VPN typu VNet-to-VNet pomocí Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal).

## <a name="name-resolution-when-connecting-virtual-networks"></a>Překlad názvů při připojování k virtuálním sítím

Virtuální sítě připojené k virtuální síti Azure AD Domain Services mají obvykle vlastní nastavení DNS. Když propojíte virtuální sítě, nekonfiguruje automaticky překlad IP adres pro připojení virtuální sítě, aby vyřešila služby poskytované službou Azure služba AD DS spravované domény. Překlad názvů na připojující se virtuální sítě musí být nakonfigurovaný tak, aby umožňoval aplikačním úlohám najít Azure AD Domain Services.

Překlad adres IP můžete povolit pomocí podmíněného přesměrování DNS na serveru DNS, který podporuje propojování virtuálních sítí, nebo pomocí stejných IP adres DNS z virtuální sítě služby Azure AD Domain Service.

## <a name="network-resources-used-by-azure-ad-ds"></a>Síťové prostředky používané službou Azure služba AD DS

Spravovaná doména Azure služba AD DS během nasazení vytvoří několik síťových prostředků. Tyto prostředky jsou nutné pro úspěšnou operaci a správu spravované domény Azure služba AD DS a neměli byste je konfigurovat ručně.

| Prostředek Azure                          | Popis |
|:----------------------------------------|:---|
| Síťová karta                  | Azure služba AD DS hostuje spravovanou doménu na dvou řadičích domény (DCs), které běží na Windows serveru jako virtuální počítače Azure. Každý virtuální počítač má virtuální síťové rozhraní, které se připojuje k podsíti virtuální sítě. |
| Dynamická základní veřejná IP adresa         | Azure služba AD DS komunikuje se službou synchronizace a správou pomocí veřejné IP adresy základní SKU. Další informace o veřejných IP adresách najdete v tématu [typy IP adres a metody přidělování v Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md). |
| Nástroj pro vyrovnávání zatížení Azure úrovně Basic               | Azure služba AD DS využívá základní nástroj pro vyrovnávání zatížení SKU pro překlad adres (NAT) a vyrovnávání zatížení (při použití se zabezpečeným protokolem LDAP). Další informace o nástrojích pro vyrovnávání zatížení Azure najdete v tématu [co je Azure Load Balancer?](../load-balancer/load-balancer-overview.md) |
| Pravidla překladu síťových adres (NAT) | Azure služba AD DS vytvoří a použije tři pravidla překladu adres (NAT) pro nástroj pro vyrovnávání zatížení – jedno pravidlo pro zabezpečený provoz HTTP a dvě pravidla pro zabezpečenou vzdálenou komunikaci PowerShellu. |
| Pravidla nástroje pro vyrovnávání zatížení                     | Když je na serveru TCP 636 spravovaná doména spravované službou Azure služba AD DS, vytvoří se při distribuci provozu tři pravidla a použijí se na nástroji pro vyrovnávání zatížení. |

> [!WARNING]
> Neodstraňujte žádný ze síťových prostředků vytvořených v Azure služba AD DS. Pokud odstraníte kterýkoli ze síťových prostředků, dojde k výpadku služby Azure služba AD DS.

## <a name="network-security-groups-and-required-ports"></a>Skupiny zabezpečení sítě a požadované porty

[Skupina zabezpečení sítě (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) obsahuje seznam pravidel, která povolují nebo zakazují síťový provoz do provozu ve službě Azure Virtual Network. Skupina zabezpečení sítě se vytvoří při nasazení služba AD DS Azure, která obsahuje sadu pravidel, která službě umožní poskytovat funkce ověřování a správy. Tato výchozí skupina zabezpečení sítě je přidružená k podsíti virtuální sítě, do které se spravovaná doména Azure služba AD DS spravované domény nasadí.

Pro Azure služba AD DS k poskytování služeb ověřování a správy se vyžadují následující pravidla skupiny zabezpečení sítě. Neupravujte ani neodstraňujte tato pravidla skupiny zabezpečení sítě pro podsíť virtuální sítě, ve které je vaše spravovaná doména Azure služba AD DS nasazená.

| Číslo portu | Protocol | Zdroj                             | Destination | Action | Požadováno | Účel |
|:-----------:|:--------:|:----------------------------------:|:-----------:|:------:|:--------:|:--------|
| 443         | TCP      | AzureActiveDirectoryDomainServices | Any         | Allow  | Ano      | Synchronizace s vaším klientem služby Azure AD. |
| 3389        | TCP      | CorpNetSaw                         | Any         | Allow  | Ano      | Správa vaší domény. |
| 5986        | TCP      | AzureActiveDirectoryDomainServices | Any         | Allow  | Ano      | Správa vaší domény. |
| 636         | TCP      | Any                                | Any         | Allow  | Ne       | Povoluje se jenom při konfiguraci zabezpečení LDAP (LDAPs). |

> [!WARNING]
> Neupravujte ručně tyto síťové prostředky a konfigurace. Když přiřadíte nesprávně nakonfigurovanou skupinu zabezpečení sítě nebo uživatelem definovanou tabulku směrování s podsítí, ve které je nasazená služba Azure služba AD DS, můžete přerušit schopnost služby a správy domény od Microsoftu. Dojde také k přerušení synchronizace mezi vaším klientem služby Azure AD a službou Azure služba AD DS spravované domény.
>
> Pro skupinu zabezpečení sítě existují také výchozí pravidla pro *AllowVnetInBound*, *AllowAzureLoadBalancerInBound*, *DenyAllInBound*, *AllowVnetOutBound*, *AllowInternetOutBound*a *DenyAllOutBound* . Tato výchozí pravidla neupravujte ani neodstraňujte.
>
> Smlouvy SLA Azure se nevztahují na nasazení, kde se používala nesprávně nakonfigurovaná skupina zabezpečení sítě nebo uživatelsky definované směrovací tabulky, které blokují Azure služba AD DS v rámci aktualizace a správy vaší domény.

### <a name="port-443---synchronization-with-azure-ad"></a>Port 443 – synchronizace s Azure AD

* Slouží k synchronizaci vašeho tenanta Azure AD s vaší spravovanou doménou Azure služba AD DS.
* Bez přístupu k tomuto portu se vaše spravovaná doména Azure služba AD DS nemůže synchronizovat s vaším tenant Azure AD. Uživatelé se nemusí přihlašovat, protože změny hesel by nemusely být synchronizovány do spravované domény Azure služba AD DS.
* Příchozí přístup k tomuto portu na IP adresy je ve výchozím nastavení omezený pomocí značky služby **AzureActiveDirectoryDomainServices** .
* Neomezovat odchozí přístup z tohoto portu.

### <a name="port-3389---management-using-remote-desktop"></a>Port 3389 – Správa pomocí vzdálené plochy

* Používá se pro připojení vzdálené plochy k řadičům domény ve spravované doméně Azure služba AD DS.
* Výchozí pravidlo skupiny zabezpečení sítě používá značku služby *CorpNetSaw* k dalšímu omezení provozu.
    * Tato značka služby povoluje pouze zabezpečené pracovní stanice v podnikové síti Microsoftu, aby mohli používat vzdálenou plochu do spravované domény Azure služba AD DS.
    * Přístup je povolený jenom pro obchodní odůvodnění, například pro scénáře správy nebo řešení potíží.
* Toto pravidlo může být nastaveno na *Odepřít*a v případě potřeby nastaveno pouze na *povoleno* . Většina úloh správy a monitorování se provádí pomocí vzdálené komunikace PowerShellu. Protokol RDP se používá jenom ve vzácných událostech, které Microsoft potřebuje ke své spravované doméně vzdáleně připojit pro řešení potíží s pokročilým řešením.

> [!NOTE]
> Pokud se pokusíte upravit toto pravidlo skupiny zabezpečení sítě, nemůžete ručně vybrat značku služby *CorpNetSaw* z portálu. Pokud chcete ručně nakonfigurovat pravidlo, které používá značku služby *CorpNetSaw* , musíte použít Azure PowerShell nebo rozhraní příkazového řádku Azure CLI.

### <a name="port-5986---management-using-powershell-remoting"></a>Port 5986 – Správa pomocí vzdálené komunikace PowerShellu

* Slouží k provádění úloh správy pomocí vzdálené komunikace PowerShellu ve spravované doméně Azure služba AD DS.
* Bez přístupu k tomuto portu se vaše spravovaná doména Azure služba AD DS nedá aktualizovat, konfigurovat, zálohovat ani sledovat.
* Pro Azure služba AD DS spravované domény, které používají virtuální síť založenou na Správce prostředků, můžete omezit příchozí přístup k tomuto portu na značku služby *AzureActiveDirectoryDomainServices* .
    * Pro starší verze Azure služba AD DS spravované domény pomocí klasické virtuální sítě můžete omezit příchozí přístup k tomuto portu na následující zdrojové IP adresy: *52.180.183.8*, *23.101.0.70*, *52.225.184.198*, *52.179.126.223*, *13.74.249.156*, *52.187.117.83*, *52.161.13.95*, *104.40.156.18*a *104.40.87.209*.

## <a name="user-defined-routes"></a>Trasy definované uživatelem

Uživatelem definované trasy nejsou ve výchozím nastavení vytvořeny a nejsou potřebné k tomu, aby služba Azure služba AD DS fungovala správně. Pokud potřebujete použít směrovací tabulky, vyhněte se jakýmkoli změnám v trase *0.0.0.0* . Změny v této trase můžou přerušit Azure AD Domain Services.

Příchozí provoz musíte také směrovat z IP adres obsažených v příslušných značkách služby Azure do podsítě Azure AD Domain Services. Další informace o značkách služeb a jejich přidružené IP adrese najdete v tématu [rozsahy IP adres Azure a značky služeb – veřejný cloud](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

> [!CAUTION]
> Tyto rozsahy IP adres datového centra Azure se můžou měnit bez předchozího upozornění. Ujistěte se, že máte procesy, abyste ověřili, že máte nejnovější IP adresy.

## <a name="next-steps"></a>Další kroky

Další informace o některých síťových prostředcích a možnostech připojení používaných službou Azure služba AD DS najdete v následujících článcích:

* [Partnerský vztah virtuálních sítí Azure](../virtual-network/virtual-network-peering-overview.md)
* [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)
* [Skupiny zabezpečení sítě Azure](../virtual-network/security-overview.md)

<!-- INTERNAL LINKS -->

<!-- EXTERNAL LINKS -->
