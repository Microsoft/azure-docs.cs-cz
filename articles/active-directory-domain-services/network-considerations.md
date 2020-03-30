---
title: Plánování sítě a připojení pro služby Azure AD Domain Services | Dokumenty společnosti Microsoft
description: Přečtěte si o některých aspektech návrhu virtuální sítě a o prostředcích používaných pro připojení při spuštění služby Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 23a857a5-2720-400a-ab9b-1ba61e7b145a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: e00ec8448739ac30950877a2ae196aa78cde750c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264190"
---
# <a name="virtual-network-design-considerations-and-configuration-options-for-azure-ad-domain-services"></a>Důležité informace o návrhu virtuální sítě a možnosti konfigurace pro služby Azure AD Domain Services

Vzhledem k tomu, že služba Azure Active Directory Domain Services (AD DS) poskytuje služby ověřování a správy pro jiné aplikace a úlohy, připojení k síti je klíčovou součástí. Bez správně nakonfigurovaných prostředků virtuální sítě nemohou aplikace a úlohy komunikovat s funkcemi poskytovanými službou Azure AD DS a používat je. Naplánujte si požadavky na virtuální síť a ujistěte se, že Azure AD DS může obsluhovat vaše aplikace a úlohy podle potřeby.

Tento článek popisuje aspekty návrhu a požadavky na virtuální síť Azure pro podporu Azure AD DS.

## <a name="azure-virtual-network-design"></a>Návrh virtuální sítě Azure

Chcete-li zajistit připojení k síti a povolit aplikacím a službám ověřování proti Azure AD DS, použijte virtuální síť Azure a podsíť. V ideálním případě azure ad DS by měla být nasazena do své vlastní virtuální sítě. Můžete zahrnout samostatnou podsíť aplikace ve stejné virtuální síti pro hostování úloh virtuálního počítače pro správu nebo lehkých aplikačních úloh. Samostatná virtuální síť pro větší nebo složité úlohy aplikací, peered do virtuální sítě Azure AD DS, je obvykle nejvhodnější návrh. Ostatní návrhy volby jsou platné, za předpokladu, že splňujete požadavky uvedené v následujících částech pro virtuální síť a podsítě.

Při navrhování virtuální sítě pro Azure AD DS platí následující důležité informace:

* Azure AD DS musí být nasazené do stejné oblasti Azure jako vaše virtuální síť.
    * V tuto chvíli můžete nasadit jenom jednu spravovanou doménu Azure AD DS na klienta Azure AD. Spravovaná doména Azure AD DS se nasadí do jedné oblasti. Ujistěte se, že vytvoříte nebo vyberete virtuální síť v [oblasti, která podporuje Azure AD DS](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all).
* Zvažte blízkost jiných oblastí Azure a virtuálních sítí, které hostují úlohy vašich aplikací.
    * Chcete-li minimalizovat latenci, udržujte základní aplikace v blízkosti nebo ve stejné oblasti jako podsíť virtuální sítě pro spravovanou doménu Azure AD DS. Mezi virtuálními sítěmi Azure můžete použít připojení partnerského vztahu virtuální sítě nebo virtuální privátní sítě (VPN). Tyto možnosti připojení jsou popsány v následující části.
* Virtuální síť se nemůže spoléhat na jiné služby DNS než ty, které poskytuje Azure AD DS.
    * Azure AD DS poskytuje vlastní službu DNS. Virtuální síť musí být nakonfigurována tak, aby používala tyto adresy služeb DNS. Překlad názvů pro další obory názvů lze provést pomocí podmíněných prokladů.
    * Vlastní nastavení serveru DNS nelze použít k přesměrování dotazů z jiných serverů DNS, včetně virtuálních počítačích. Prostředky ve virtuální síti musí používat službu DNS poskytovanou službou Azure AD DS.

> [!IMPORTANT]
> Azure AD DS nelze přesunout do jiné virtuální sítě po povolení služby.

Spravovaná doména Azure AD DS se připojuje k podsíti ve virtuální síti Azure. Navrhněte tuto podsíť pro Azure AD DS s následujícími poznámkami:

* Azure AD DS musí být nasazeny ve své vlastní podsíti. Nepoužívejte existující podsíť nebo podsíť brány.
* Skupina zabezpečení sítě se vytvoří během nasazení spravované domény Azure AD DS. Tato skupina zabezpečení sítě obsahuje požadovaná pravidla pro správnou komunikaci se službami.
    * Nevytvářejte ani nepoužívejte existující skupinu zabezpečení sítě s vlastními pravidly.
* Azure AD DS vyžaduje 3-5 IP adresy. Ujistěte se, že rozsah ip adres podsítě může poskytnout tento počet adres.
    * Omezení dostupných IP adres může zabránit službě Azure AD Domain Services v udržování dvou řadičů domény.

Následující ukázkový diagram popisuje platný návrh, kde Azure AD DS má vlastní podsíť, je podsíť brány pro externí připojení a úlohy aplikací jsou v připojené podsíti v rámci virtuální sítě:

![Doporučený návrh podsítě](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

## <a name="connections-to-the-azure-ad-ds-virtual-network"></a>Připojení k virtuální síti Azure AD DS

Jak je uvedeno v předchozí části, můžete vytvořit jenom spravované domény služby Azure AD Domain Services v jedné virtuální síti v Azure a jenom jednu spravovanou doménu lze vytvořit na klienta Azure AD. Na základě této architektury může být nutné připojit jednu nebo více virtuálních sítí, které hostují úlohy vašich aplikací, k virtuální síti Azure AD DS.

Úlohy aplikací hostované v jiných virtuálních sítích Azure můžete připojit pomocí jedné z následujících metod:

* Partnerské vztahy virtuálních sítí
* Virtuální privátní sítě (VPN)

### <a name="virtual-network-peering"></a>Partnerské vztahy virtuálních sítí

Partnerský vztah virtuální sítě je mechanismus, který spojuje dvě virtuální sítě ve stejné oblasti prostřednictvím páteřní sítě Azure. Partnerský vztah globální virtuální sítě můžete připojit virtuální síť napříč oblastmi Azure. Po vzájemném vztahu umožňují dvě virtuální sítě prostředky, jako jsou virtuální počítače, vzájemně komunikovat přímo pomocí privátních IP adres. Pomocí partnerského vztahu virtuální sítě umožňuje nasadit spravovanou doménu Azure AD DS s úlohami aplikací nasazenými v jiných virtuálních sítích.

![Připojení k virtuální síti pomocí partnerského vztahu](./media/active-directory-domain-services-design-guide/vnet-peering.png)

Další informace najdete v tématu [Přehled partnerského vztahu virtuální sítě Azure](../virtual-network/virtual-network-peering-overview.md).

### <a name="virtual-private-networking-vpn"></a>Virtuální privátní sítě (VPN)

Virtuální síť můžete připojit k jiné virtuální síti (Virtuální síť k virtuální síti) stejným způsobem, jakým můžete virtuální síť nakonfigurovat do místního umístění lokality. Obě připojení používají bránu VPN k vytvoření zabezpečeného tunelu pomocí protokolu IPsec/IKE. Tento model připojení umožňuje nasadit Azure AD DS do virtuální sítě Azure a pak připojit místní umístění nebo jiné cloudy.

![Připojení k virtuální síti pomocí brány VPN](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

Další informace o používání virtuálníprivutánní sítě [načta načlánekl konfigurace připojení brány VPN virtuální sítě k virtuální síti pomocí portálu Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal).

## <a name="name-resolution-when-connecting-virtual-networks"></a>Překlad názvů při připojování virtuálních sítí

Virtuální sítě připojené k virtuální síti Azure AD Domain Services mají obvykle vlastní nastavení DNS. Když připojíte virtuální sítě, nebude automaticky konfigurovat překlad názvů pro připojení virtuální sítě k vyřešení služeb poskytovaných spravovanou doménou Azure AD DS. Překlad názvů v připojujících se virtuálních sítích musí být nakonfigurovaný tak, aby úlohy aplikací mohly vyhledat služby Azure AD Domain Services.

Překlad názvů můžete povolit pomocí podmíněných serverů pro předávání DNS na serveru DNS podporujících připojující se virtuální sítě nebo pomocí stejných IP adres DNS z virtuální sítě Azure AD Domain Service.

## <a name="network-resources-used-by-azure-ad-ds"></a>Síťové prostředky používané službou Azure AD DS

Spravovaná doména Azure AD DS vytvoří během nasazení některé síťové prostředky. Tyto prostředky jsou potřebné pro úspěšné operace a správu spravované domény Azure AD DS a neměly by být ručně nakonfigurované.

| Prostředek Azure                          | Popis |
|:----------------------------------------|:---|
| Karta síťového rozhraní                  | Azure AD DS hostuje spravovanou doménu na dvou řadičích domény (DC), které běží na Windows Serveru jako virtuální počítače Azure. Každý virtuální počítač má virtuální síťové rozhraní, které se připojuje k podsíti virtuální sítě. |
| Dynamická standardní veřejná IP adresa      | Azure AD DS komunikuje se službou synchronizace a správy pomocí standardní veřejné IP adresy skladové položky. Další informace o veřejných IP adresách najdete [v tématu typy IP adres a metody přidělení v Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md). |
| Standardní vyrovnávání zatížení Azure            | Azure AD DS používá standardní skladovou položku pro překlad síťových adres (NAT) a vyrovnávání zatížení (při použití se zabezpečeným LDAP). Další informace o vydělávačích zatížení Azure najdete [v tématu Co je Azure Load Balancer?](../load-balancer/load-balancer-overview.md) |
| Pravidla překladu síťových adres (NAT) | Azure AD DS vytvoří a používá tři pravidla NAT na nástroj pro vyrovnávání zatížení – jedno pravidlo pro zabezpečené přenosy HTTP a dvě pravidla pro zabezpečené vzdálené komunikace prostředí PowerShell. |
| Pravidla nástroje pro vyrovnávání zatížení                     | Když je spravovaná doména Azure AD DS nakonfigurovaná pro zabezpečené LDAP na portu TCP 636, vytvoří se tři pravidla a použijí se na vyrovnávání zatížení k distribuci provozu. |

> [!WARNING]
> Neodstraňujte žádný síťový prostředek vytvořený službou Azure AD DS. Pokud odstraníte některý ze síťových prostředků, dojde k výpadku služby Azure AD DS.

## <a name="network-security-groups-and-required-ports"></a>Skupiny zabezpečení sítě a požadované porty

[Skupina zabezpečení sítě (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) obsahuje seznam pravidel, která povolují nebo zapírají síťový provoz na provoz ve virtuální síti Azure. Skupina zabezpečení sítě se vytvoří při nasazení služby Azure AD DS, která obsahuje sadu pravidel, která umožňují službě poskytovat funkce ověřování a správy. Tato výchozí skupina zabezpečení sítě je přidružená k podsíti virtuální sítě, do které se nasadí vaše spravovaná doména Azure AD DS.

Následující pravidla skupiny zabezpečení sítě jsou vyžadovány pro Azure AD DS poskytovat služby ověřování a správy. Neupravujte ani neodstraňujte tato pravidla skupiny zabezpečení sítě pro podsíť virtuální sítě, do které se nasadí vaše spravovaná doména Služby Azure AD DS.

| Číslo portu | Protocol (Protokol) | Zdroj                             | Cíl | Akce | Požaduje se | Účel |
|:-----------:|:--------:|:----------------------------------:|:-----------:|:------:|:--------:|:--------|
| 443         | TCP      | Služby AzureActiveDirectoryDomainServices | Všechny         | Povolit  | Ano      | Synchronizace s klientem Azure AD. |
| 3389        | TCP      | CorpNetSaw                         | Všechny         | Povolit  | Ano      | Správa vaší domény. |
| 5986        | TCP      | Služby AzureActiveDirectoryDomainServices | Všechny         | Povolit  | Ano      | Správa vaší domény. |
| 636         | TCP      | Všechny                                | Všechny         | Povolit  | Ne       | Povoleno pouze při konfiguraci zabezpečeného protokolu LDAP (LDAPS). |

> [!WARNING]
> Tyto síťové prostředky a konfigurace neupravujte ručně. Když přidružíte nesprávně nakonfigurovanou skupinu zabezpečení sítě nebo uživatelem definovanou směrovací tabulku k podsíti, ve které se nasazuje služba Azure AD DS, můžete narušit schopnost společnosti Microsoft obsluhovat a spravovat doménu. Synchronizace mezi klientem Azure AD a spravovanou doménou Azure AD DS je také narušena.
>
> Výchozí pravidla pro *AllowVnetInBound*, *AllowAzureLoadBalancerInBound*, *DenyAllInBound*, *AllowVnetOutBound*, *AllowInternetOutBound*a *DenyAllOutBound* existují také pro skupinu zabezpečení sítě. Tato výchozí pravidla neupravujte ani neodstraňujte.
>
> Azure SLA se nevztahuje na nasazení, kde byly použity nesprávně nakonfigurované skupiny zabezpečení sítě nebo uživatelem definované směrovací tabulky, které blokují Azure AD DS z aktualizace a správy domény.

### <a name="port-443---synchronization-with-azure-ad"></a>Port 443 – synchronizace s Azure AD

* Používá se k synchronizaci klienta Azure AD se spravovanou doménou Azure AD DS.
* Bez přístupu k tomuto portu se vaše spravovaná doména Azure AD DS nemůže synchronizovat s vaším klientem Azure AD. Uživatelé nemusí být schopni se přihlásit, protože změny jejich hesel by se nesynchronizovaly do vaší spravované domény Azure AD DS.
* Příchozí přístup k tomuto portu k IP adresám je ve výchozím nastavení omezen pomocí značky služby **AzureActiveDirectoryDomainServices.**
* Neomezujte odchozí přístup z tohoto portu.

### <a name="port-3389---management-using-remote-desktop"></a>Port 3389 - správa pomocí vzdálené plochy

* Používá se pro připojení ke vzdálené ploše k řadičům domény ve spravované doméně Azure AD DS.
* Výchozí pravidlo skupiny zabezpečení sítě používá značku služby *CorpNetSaw* k dalšímu omezení provozu.
    * Tato značka služby povoluje pouze pracovní stanice zabezpečeného přístupu v podnikové síti Microsoft používat vzdálenou plochu do spravované domény Azure AD DS.
    * Přístup je povolen pouze s obchodním odůvodněním, například pro scénáře správy nebo řešení potíží.
* Toto pravidlo lze nastavit na *odepřít*a pouze v případě *potřeby povolit.* Většina úloh správy a monitorování se provádí pomocí vzdálené komunikace prostředí PowerShell. Protokol RDP se používá pouze ve výjimečných případech, kdy se společnost Microsoft potřebuje vzdáleně připojit ke spravované doméně pro řešení potíží pro pokročilé uživatele.

> [!NOTE]
> Pokud se pokusíte upravit toto pravidlo skupiny zabezpečení sítě, nemůžete ručně vybrat výrobní značku *CorpNetSaw* z portálu. Azure PowerShell nebo Azure CLI musíte ručně nakonfigurovat pravidlo, které používá značku služby *CorpNetSaw.*

### <a name="port-5986---management-using-powershell-remoting"></a>Port 5986 - správa pomocí vzdálené komunikace prostředí PowerShell

* Používá se k provádění úloh správy pomocí vzdálené komunikace prostředí PowerShell ve spravované doméně Azure AD DS.
* Bez přístupu k tomuto portu nelze aktualizovat, nakonfigurovat, zálohovat ani monitorovat vaši spravovanou doménu Služby Azure AD DS.
* U spravovaných domén Azure AD DS, které používají virtuální síť založenou na Správci prostředků, můžete omezit příchozí přístup k tomuto portu na značku *služby AzureActiveDirectoryDomainServices.*
    * Pro starší spravované domény Azure AD DS pomocí virtuální sítě založené na klasickém zařízení Můžete omezit příchozí přístup k tomuto portu na následující zdrojové IP adresy: *52.180.183.8*, *23.101.0.70*, *52.225.184.198*, *52.179.126.223*, *13.74.249.156*, *52.187.117.83*, *52.161.13.95*, *104.40.156.18*a *104.40.87.209*.

    > [!NOTE]
    > V roce 2017 se služby Azure AD Domain Services staly dostupnými pro hostování v síti Azure Resource Manager. Od té doby jsme byli schopni vytvořit bezpečnější službu pomocí moderních funkcí Azure Resource Manager. Vzhledem k tomu, že nasazení Azure Resource Manager plně nahradit klasická nasazení, Nasazení klasické virtuální sítě Azure AD DS bude vyřazena na 1 března 2023.
    >
    > Další informace naleznete v [oficiálním oznámení o vyřazení](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="user-defined-routes"></a>Trasy definované uživatelem

Uživatelem definované trasy nejsou vytvořeny ve výchozím nastavení a nejsou potřeba pro Azure AD DS pracovat správně. Pokud jste povinni používat tabulky tras, neprovázte žádným změnám trasy *0.0.0.0.* Změny této trasy narušit služby Domény Azure AD a umístí spravované domény v nepodporovaném stavu.

Musíte také směrovat příchozí provoz z IP adres zahrnutých v příslušných značkách služby Azure do podsítě služby Azure AD Domain Services. Další informace o značkách služeb a jejich přidružené IP adrese z webu [Azure IP rozsahy a značky služeb – veřejný cloud](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

> [!CAUTION]
> Tyto rozsahy IP adres datového centra Azure se můžou změnit bez předchozího upozornění. Ujistěte se, že máte procesy k ověření, že máte nejnovější IP adresy.

## <a name="next-steps"></a>Další kroky

Další informace o některých síťových prostředcích a možnostech připojení používaných službou Azure AD DS naleznete v následujících článcích:

* [Partnerský vztah virtuální sítě Azure](../virtual-network/virtual-network-peering-overview.md)
* [Brány VPN Azure](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)
* [Skupiny zabezpečení sítě Azure](../virtual-network/security-overview.md)
