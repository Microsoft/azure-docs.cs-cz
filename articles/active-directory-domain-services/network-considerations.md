---
title: Plánování a připojení k síti pro Azure AD Domain Services | Microsoft Docs
description: Přečtěte si o některých faktorech návrhu virtuální sítě a o prostředcích, které se používají pro připojení při spuštění Azure Active Directory Domain Services.
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: joflore
ms.openlocfilehash: 43731f84066943b991b566ff5936e4288aa669eb
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96175215"
---
# <a name="virtual-network-design-considerations-and-configuration-options-for-azure-active-directory-domain-services"></a>Požadavky na návrh virtuální sítě a možnosti konfigurace pro Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure služba AD DS) poskytuje služby ověřování a správy pro jiné aplikace a úlohy. Připojení k síti je klíčová komponenta. Bez správně nakonfigurovaných prostředků virtuální sítě nemůžou aplikace a úlohy komunikovat s funkcemi, které poskytuje Azure služba AD DS, a používat je. Naplánujte požadavky na virtuální síť, abyste se ujistili, že Azure služba AD DS může vašim aplikacím a úlohám poskytovat podle potřeby.

Tento článek popisuje požadavky na návrh a požadavky služby Azure Virtual Network na podporu služba AD DS Azure.

## <a name="azure-virtual-network-design"></a>Návrh virtuální sítě Azure

Pokud chcete zajistit připojení k síti a umožnit aplikacím a službám ověřování ve spravované doméně Azure služba AD DS, použijte virtuální síť Azure a podsíť. V ideálním případě by se měla spravovaná doména nasadit do své vlastní virtuální sítě.

Můžete zahrnout samostatnou podsíť aplikace ve stejné virtuální síti, která bude hostovat vaše virtuální počítače pro správu nebo úlohy lehké aplikace. Nejvhodnějším návrhem je obvykle samostatná virtuální síť pro větší nebo složitou aplikační úlohu, která je rovnocenná službě Azure služba AD DS Virtual Network.

Další možnosti návrhů jsou platné, a to za předpokladu, že splňujete požadavky popsané v následujících částech pro virtuální síť a podsíť.

Při návrhu virtuální sítě pro Azure služba AD DS platí následující požadavky:

* Azure služba AD DS musí být nasazené do stejné oblasti Azure jako vaše virtuální síť.
    * V tuto chvíli můžete nasadit jenom jednu spravovanou doménu na tenanta Azure AD. Spravovaná doména je nasazena do jedné oblasti. Ujistěte se, že jste vytvořili nebo vybrali virtuální síť v [oblasti, která podporuje Azure služba AD DS](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all).
* Zvažte blízkost ostatních oblastí Azure a virtuálních sítí, které hostují úlohy vaší aplikace.
    * Chcete-li minimalizovat latenci, udržujte základní aplikace blízko nebo ve stejné oblasti jako podsíť virtuální sítě pro spravovanou doménu. Mezi virtuálními sítěmi Azure můžete použít připojení VPN s partnerským vztahem nebo virtuální privátní síť (VPN). Tyto možnosti připojení jsou popsány v následující části.
* Virtuální síť nemůže spoléhat na služby DNS jiné než služby poskytované spravovanou doménou.
    * Azure služba AD DS poskytuje vlastní službu DNS. Virtuální síť musí být nakonfigurovaná tak, aby používala tyto adresy služby DNS. Překlad názvů pro další obory názvů se dá provést pomocí podmíněného dopředných služeb.
    * Vlastní nastavení serveru DNS nemůžete použít k přímému nasměrování dotazů z jiných serverů DNS, včetně virtuálních počítačů. Prostředky ve virtuální síti musí používat službu DNS poskytovanou spravovanou doménou.

> [!IMPORTANT]
> Po povolení služby nemůžete přesunout služba AD DS Azure do jiné virtuální sítě.

Spravovaná doména se připojuje k podsíti ve službě Azure Virtual Network. Navrhněte tuto podsíť pro Azure služba AD DS s následujícími požadavky:

* Spravovaná doména musí být nasazená ve vlastní podsíti. Nepoužívejte existující podsíť ani podsíť brány.
* Skupina zabezpečení sítě se vytvoří během nasazování spravované domény. Tato skupina zabezpečení sítě obsahuje požadovaná pravidla pro správnou komunikaci služby.
    * Nevytvářejte ani nepoužívejte existující skupinu zabezpečení sítě s vlastními pravidly.
* Spravovaná doména vyžaduje 3-5 IP adres. Ujistěte se, že rozsah IP adres podsítě může poskytovat tento počet adres.
    * Omezení dostupných IP adres může zabránit spravované doméně v údržbě dvou řadičů domény.

Následující příklad diagramu popisuje platný návrh, ve kterém má spravovaná doména svou vlastní podsíť, je k dispozici podsíť brány pro externí připojení a úlohy aplikace jsou v připojené podsíti v rámci virtuální sítě:

![Doporučený návrh podsítě](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

## <a name="connections-to-the-azure-ad-ds-virtual-network"></a>Připojení k virtuální síti Azure služba AD DS

Jak je uvedeno v předchozí části, můžete v Azure vytvořit jenom spravovanou doménu v rámci jedné virtuální sítě a pro každého tenanta Azure AD se dá vytvořit jenom jedna spravovaná doména. Na základě této architektury možná budete muset připojit jednu nebo více virtuálních sítí, které hostují úlohy vaší aplikace, do virtuální sítě spravované domény.

Aplikační úlohy hostované v jiných virtuálních sítích Azure můžete připojit pomocí jedné z následujících metod:

* Partnerský vztah virtuální sítě
* Virtuální privátní sítě (VPN)

### <a name="virtual-network-peering"></a>Partnerský vztah virtuální sítě

Partnerský vztah virtuálních sítí je mechanismus, který propojuje dvě virtuální sítě ve stejné oblasti prostřednictvím páteřní sítě Azure. Globální partnerské vztahy virtuálních sítí se můžou připojit k virtuální síti napříč oblastmi Azure. Po navázání partnerského vztahu mezi dvěma virtuálními sítěmi umožníte komunikaci přímo pomocí privátních IP adres, jako jsou třeba virtuální počítače. Pomocí partnerského vztahu virtuálních sítí můžete nasadit spravovanou doménu s úlohami vaší aplikace nasazenými v jiných virtuálních sítích.

![Připojení k virtuální síti pomocí partnerského vztahu](./media/active-directory-domain-services-design-guide/vnet-peering.png)

Další informace najdete v tématu [Přehled partnerských vztahů virtuálních sítí Azure](../virtual-network/virtual-network-peering-overview.md).

### <a name="virtual-private-networking-vpn"></a>Virtuální privátní sítě (VPN)

Virtuální síť můžete připojit k jiné virtuální síti (VNet-to-VNet) stejným způsobem, jakým můžete nakonfigurovat virtuální síť na místní umístění lokality. Obě připojení používají bránu VPN k vytvoření zabezpečeného tunelového propojení pomocí protokolu IPsec/IKE. Tento model připojení umožňuje nasadit spravovanou doménu do virtuální sítě Azure a pak připojit místní umístění nebo jiné cloudy.

![Připojení k virtuální síti pomocí VPN Gateway](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

Další informace o používání virtuálních privátních sítí najdete v tématu [Konfigurace připojení brány VPN typu VNet-to-VNet pomocí Azure Portal](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

## <a name="name-resolution-when-connecting-virtual-networks"></a>Překlad názvů při připojování k virtuálním sítím

Virtuální sítě připojené k virtuální síti spravované domény mají obvykle vlastní nastavení DNS. Když propojíte virtuální sítě, nekonfiguruje automaticky překlad IP adres pro připojující se virtuální síť, aby vyřešila služby poskytované spravovanou doménou. Překlad názvů na připojujících se virtuálních sítích musí být nakonfigurovaný tak, aby umožňoval aplikačním úlohám vyhledat spravovanou doménu.

Překlad adres IP můžete povolit pomocí podmíněného přeposílání DNS na serveru DNS, který podporuje propojování virtuálních sítí, nebo pomocí stejné IP adresy DNS z virtuální sítě spravované domény.

## <a name="network-resources-used-by-azure-ad-ds"></a>Síťové prostředky používané službou Azure služba AD DS

Spravovaná doména vytvoří během nasazení některé síťové prostředky. Tyto prostředky jsou nutné pro úspěšnou operaci a správu spravované domény a neměli byste je konfigurovat ručně.

| Prostředek Azure                          | Popis |
|:----------------------------------------|:---|
| Síťová karta                  | Azure služba AD DS hostuje spravovanou doménu na dvou řadičích domény (DCs), které běží na Windows serveru jako virtuální počítače Azure. Každý virtuální počítač má virtuální síťové rozhraní, které se připojuje k podsíti virtuální sítě. |
| Dynamická standardní veřejná IP adresa      | Azure služba AD DS komunikuje se službou synchronizace a správy pomocí veřejné IP adresy standardní SKU. Další informace o veřejných IP adresách najdete v tématu [typy IP adres a metody přidělování v Azure](../virtual-network/public-ip-addresses.md). |
| Azure Load Balancer úrovně Standard            | Azure služba AD DS používá nástroj pro vyrovnávání zatížení Standard SKU pro překlad síťových adres (NAT) a vyrovnávání zatížení (při použití se zabezpečeným protokolem LDAP). Další informace o nástrojích pro vyrovnávání zatížení Azure najdete v tématu [co je Azure Load Balancer?](../load-balancer/load-balancer-overview.md) |
| Pravidla překladu síťových adres (NAT) | Azure služba AD DS vytvoří a použije tři pravidla překladu adres (NAT) pro nástroj pro vyrovnávání zatížení – jedno pravidlo pro zabezpečený provoz HTTP a dvě pravidla pro zabezpečenou vzdálenou komunikaci PowerShellu. |
| Pravidla nástroje pro vyrovnávání zatížení                     | Když je spravovaná doména nakonfigurovaná na Secure LDAP na portu TCP 636, vytvoří se pro distribuci provozu tři pravidla a použijí se na nástroji pro vyrovnávání zatížení. |

> [!WARNING]
> Neodstraňujte ani neměňte žádný ze síťových prostředků vytvořených službou Azure služba AD DS, například ruční konfigurací nástroje pro vyrovnávání zatížení nebo pravidel. Pokud odstraníte nebo změníte kterýkoli ze síťových prostředků, může dojít k výpadku služby Azure služba AD DS.

## <a name="network-security-groups-and-required-ports"></a>Skupiny zabezpečení sítě a požadované porty

[Skupina zabezpečení sítě (NSG)](../virtual-network/network-security-groups-overview.md) obsahuje seznam pravidel, která povolují nebo zakazují síťový provoz do provozu ve službě Azure Virtual Network. Skupina zabezpečení sítě se vytvoří při nasazení spravované domény, která obsahuje sadu pravidel, která službě umožní poskytovat funkce ověřování a správy. Tato výchozí skupina zabezpečení sítě je přidružená k podsíti virtuální sítě, ve které je spravovaná doména nasazená.

Aby mohla spravovaná doména poskytovat služby ověřování a správy, vyžadují se následující pravidla skupiny zabezpečení sítě. Neupravujte ani neodstraňujte tato pravidla skupiny zabezpečení sítě pro podsíť virtuální sítě, do které je spravovaná doména nasazená.

| Číslo portu | Protokol | Zdroj                             | Cíl | Akce | Požaduje se | Účel |
|:-----------:|:--------:|:----------------------------------:|:-----------:|:------:|:--------:|:--------|
| 443         | TCP      | AzureActiveDirectoryDomainServices | Všechny         | Povolit  | Ano      | Synchronizace s vaším klientem služby Azure AD. |
| 3389        | TCP      | CorpNetSaw                         | Všechny         | Povolit  | Ano      | Správa vaší domény. |
| 5986        | TCP      | AzureActiveDirectoryDomainServices | Všechny         | Povolit  | Ano      | Správa vaší domény. |

Vytvoří se standardní nástroj pro vyrovnávání zatížení Azure, který vyžaduje, aby se tato pravidla mohla umístit. Tato skupina zabezpečení sítě zabezpečuje službu Azure služba AD DS a je potřeba, aby správně fungovala spravovaná doména. Tuto skupinu zabezpečení sítě neodstraňujte. Nástroj pro vyrovnávání zatížení nebude bez něj správně fungovat.

V případě potřeby můžete [vytvořit požadovanou skupinu zabezpečení sítě a pravidla pomocí Azure PowerShell](powershell-create-instance.md#create-a-network-security-group).

> [!WARNING]
> Neupravujte ručně tyto síťové prostředky a konfigurace. Pokud přiřadíte nesprávně nakonfigurovanou skupinu zabezpečení sítě nebo uživatelem definovanou tabulku směrování s podsítí, ve které je spravovaná doména nasazená, můžete narušit schopnost služby a správy domény od Microsoftu. Dojde také k přerušení synchronizace mezi vaším klientem služby Azure AD a vaší spravovanou doménou.
>
> Pokud používáte zabezpečený protokol LDAP, můžete přidat požadované pravidlo portu TCP 636, které v případě potřeby povolí externí provoz. Přidáním tohoto pravidla neumístíte pravidla skupiny zabezpečení sítě v nepodporovaném stavu. Další informace najdete v tématu [uzamčení zabezpečeného přístupu LDAP přes Internet](tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet) .
>
> Pro skupinu zabezpečení sítě existují také výchozí pravidla pro *AllowVnetInBound*, *AllowAzureLoadBalancerInBound*, *DenyAllInBound*, *AllowVnetOutBound*, *AllowInternetOutBound* a *DenyAllOutBound* . Tato výchozí pravidla neupravujte ani neodstraňujte.
>
> Smlouvy SLA Azure se nevztahují na nasazení, kde se používala nesprávně nakonfigurovaná skupina zabezpečení sítě nebo uživatelsky definované směrovací tabulky, které blokují Azure služba AD DS v rámci aktualizace a správy vaší domény.

### <a name="port-443---synchronization-with-azure-ad"></a>Port 443 – synchronizace s Azure AD

* Slouží k synchronizaci vašeho tenanta Azure AD se spravovanou doménou.
* Bez přístupu k tomuto portu se vaše spravovaná doména nemůže synchronizovat s vaším klientem služby Azure AD. Uživatelé se nemusí přihlašovat, protože změny hesel by nemusely být synchronizovány do vaší spravované domény.
* Příchozí přístup k tomuto portu na IP adresy je ve výchozím nastavení omezený pomocí značky služby **AzureActiveDirectoryDomainServices** .
* Neomezovat odchozí přístup z tohoto portu.

### <a name="port-3389---management-using-remote-desktop"></a>Port 3389 – Správa pomocí vzdálené plochy

* Používá se pro připojení vzdálené plochy k řadičům domény ve spravované doméně.
* Výchozí pravidlo skupiny zabezpečení sítě používá značku služby *CorpNetSaw* k dalšímu omezení provozu.
    * Tato značka služby umožňuje v podnikové síti Microsoftu pouze zabezpečené přístupové pracovní stanice, aby se k spravované doméně používala Vzdálená plocha.
    * Přístup je povolený jenom pro obchodní odůvodnění, například pro scénáře správy nebo řešení potíží.
* Toto pravidlo může být nastaveno na *Odepřít* a v případě potřeby nastaveno pouze na *povoleno* . Většina úloh správy a monitorování se provádí pomocí vzdálené komunikace PowerShellu. Protokol RDP se používá jenom ve vzácných událostech, které Microsoft potřebuje ke své spravované doméně vzdáleně připojit pro řešení potíží s pokročilým řešením.

> [!NOTE]
> Pokud se pokusíte upravit toto pravidlo skupiny zabezpečení sítě, nemůžete ručně vybrat značku služby *CorpNetSaw* z portálu. Pokud chcete ručně nakonfigurovat pravidlo, které používá značku služby *CorpNetSaw* , musíte použít Azure PowerShell nebo rozhraní příkazového řádku Azure CLI.
>
> Pomocí následujícího skriptu můžete například vytvořit pravidlo povolující protokol RDP: 
>
> `Get-AzureRmNetworkSecurityGroup -Name "nsg-name" -ResourceGroupName "resource-group-name" | Add-AzureRmNetworkSecurityRuleConfig -Name "new-rule-name" -Access "Allow" -Protocol "TCP" -Direction "Inbound" -Priority "priority-number" -SourceAddressPrefix "CorpNetSaw" -SourcePortRange "" -DestinationPortRange "3389" -DestinationAddressPrefix "" | Set-AzureRmNetworkSecurityGroup`

### <a name="port-5986---management-using-powershell-remoting"></a>Port 5986 – Správa pomocí vzdálené komunikace PowerShellu

* Slouží k provádění úloh správy pomocí vzdálené komunikace PowerShellu ve spravované doméně.
* Bez přístupu k tomuto portu se vaše spravovaná doména nedá aktualizovat, konfigurovat, zálohovat ani sledovat.
* U spravovaných domén, které používají Správce prostředků virtuální sítě, můžete omezit příchozí přístup k tomuto portu na značku služby *AzureActiveDirectoryDomainServices* .
    * U starších spravovaných domén pomocí klasické virtuální sítě můžete omezit příchozí přístup k tomuto portu na následující zdrojové IP adresy: *52.180.183.8*, *23.101.0.70*, *52.225.184.198*, *52.179.126.223*, *13.74.249.156*, *52.187.117.83*, *52.161.13.95*, *104.40.156.18* a *104.40.87.209*.

    > [!NOTE]
    > V 2017 je Azure AD Domain Services k dispozici pro hostování v Azure Resource Manager síti. Od té doby jsme dokázali vytvořit bezpečnější službu pomocí moderních možností Azure Resource Manager. Vzhledem k tomu, že Azure Resource Manager nasazení plně nahrazují klasická nasazení, nasazení Azure služba AD DS Classic Virtual Network se vyřadí 1. března 2023.
    >
    > Další informace najdete v [oficiálním oznámení o zastarání](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/) .

## <a name="user-defined-routes"></a>Trasy definované uživatelem

Uživatelem definované trasy nejsou ve výchozím nastavení vytvořeny a nejsou potřebné k tomu, aby služba Azure služba AD DS fungovala správně. Pokud potřebujete použít směrovací tabulky, vyhněte se jakýmkoli změnám v trase *0.0.0.0* . Změny této trasy narušují služba AD DS Azure a tuto spravovanou doménu vloží do nepodporovaného stavu.

Příchozí provoz musíte také směrovat z IP adres obsažených v příslušných značkách služby Azure do podsítě spravované domény. Další informace o značkách služeb a jejich přidružené IP adrese najdete v tématu [rozsahy IP adres Azure a značky služeb – veřejný cloud](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

> [!CAUTION]
> Tyto rozsahy IP adres datového centra Azure se můžou měnit bez předchozího upozornění. Ujistěte se, že máte procesy, abyste ověřili, že máte nejnovější IP adresy.

## <a name="next-steps"></a>Další kroky

Další informace o některých síťových prostředcích a možnostech připojení používaných službou Azure služba AD DS najdete v následujících článcích:

* [Partnerský vztah virtuálních sítí Azure](../virtual-network/virtual-network-peering-overview.md)
* [Brány VPN Azure](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)
* [Skupiny zabezpečení sítě Azure](../virtual-network/network-security-groups-overview.md)