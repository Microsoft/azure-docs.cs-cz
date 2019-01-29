---
title: 'Azure AD Domain Services: Pokyny pro sítě | Dokumentace Microsoftu'
description: Důležité informace o sítích pro Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 23a857a5-2720-400a-ab9b-1ba61e7b145a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/01/2017
ms.author: ergreenl
ms.openlocfilehash: 3eba9f504f98d250ad60de3b4dd775effaad2040
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55185546"
---
# <a name="networking-considerations-for-azure-ad-domain-services"></a>Důležité informace o sítích pro Azure AD Domain Services
## <a name="how-to-select-an-azure-virtual-network"></a>Jak vybrat virtuální síť Azure
Následující pokyny vám pomůže vybrat virtuální síť pro použití s Azure AD Domain Services.

### <a name="type-of-azure-virtual-network"></a>Typ virtuální sítě Azure
* **Virtuální sítě Resource Manageru**: Azure AD Domain Services je možné povolit ve virtuálních sítích vytvořených pomocí Azure Resource Manageru.
* Nejde povolit Azure AD Domain Services ve klasické virtuální síti Azure.
* Jiným virtuálním sítím můžete připojit k virtuální síti, ve kterém je povolena služba Azure AD Domain Services. Další informace najdete v tématu [připojení k síti](active-directory-ds-networking.md#network-connectivity) oddílu.

### <a name="azure-region-for-the-virtual-network"></a>Oblast Azure pro virtuální síť
* Služby Azure AD Domain spravovanou doménu je nasazená ve stejné oblasti Azure jako virtuální síť, budete chtít povolit službu.
* Vyberte virtuální síť v oblasti Azure podporované službou Azure AD Domain Services.
* Na stránce [Služby Azure podle oblasti](https://azure.microsoft.com/regions/#services/) se dozvíte, pro jaké oblasti je dostupná služba Azure AD Domain Services.

### <a name="requirements-for-the-virtual-network"></a>Požadavky na virtuální síť
* **Vzdálenost pro vaše úlohy Azure**: Vyberte virtuální síť, která právě hostí nebo bude hostit virtuální počítače, které potřebují přístup ke službě Azure AD Domain Services. Pokud vaše úlohy jsou nasazené v jiné virtuální sítě, než spravovaná doména, můžete také propojit virtuální sítě.
* **Vlastní/přineste vlastní servery DNS**: Ujistěte se, že neexistují žádné vlastní servery DNS, které jsou nakonfigurované pro virtuální síť. Příklad vlastního serveru DNS je instance DNS systému Windows Server běžící na serveru virtuálního počítače s Windows, který jste nasadili ve virtuální síti. Azure AD Domain Services není integrováno se všechny vlastní servery DNS, které jsou nasazené v rámci virtuální sítě.
* **Stávajících domén se stejným názvem domény**: Ujistěte se, že nemáte existující doménu se stejným názvem domény dostupným v dané virtuální síti. Například předpokládejme, že již máte doménu „contoso.com“ k dispozici na vybrané virtuální síti. Později Zkuste povolit spravované domény služby Azure AD Domain Services se stejným názvem domény (tedy "contoso.com") v dané virtuální síti. Dojde k chybě při pokusu o povolení služby Azure AD Domain Services. To je způsobeno konflikty názvů pro název domény v této virtuální síti. V této situaci musíte použít jiný název pro nastavení domény spravované službou Azure AD Domain Services. Případně můžete zrušit existující doménu a poté pokračovat v povolení služby Azure AD Domain Services.

> [!WARNING]
> Domain Services nelze přesunout do jiné virtuální sítě, budete mít povolenou službu.
>
>


## <a name="guidelines-for-choosing-a-subnet"></a>Pokyny pro výběr podsítě

![Doporučené podsíti](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

* Nasazení Azure AD Domain Services **oddělení vyhrazenou podsíť** v rámci vaší virtuální sítí Azure.
* Skupiny Nsg nevztahují na vyhrazenou podsíť vaší spravované domény. Pokud musíte použít skupiny Nsg na vyhrazenou podsíť, zkontrolujte, že jste **nemají blokovat porty vyžadované pro služby a spravovat vaše doména**.
* Neomezují zbytečně počet IP adres, které jsou k dispozici ve vyhrazené podsíti pro vaši spravovanou doménu. Toto omezení brání službě ve zpřístupnění dva řadiče domény pro spravovanou doménu.
* **Nepovolujte Azure AD Domain Services v podsíti brány** vaší virtuální sítě.

> [!WARNING]
> Pokud přidružíte skupinu NSG k podsíti, ve kterém Azure AD Domain Services je povoleno, mohou narušit schopnost Microsoftu služby a spravovat domény. Kromě toho dojde k narušení synchronizace mezi vaším tenantem Azure AD a vaší spravované domény. **Smlouva SLA nevztahuje na nasazení, ve kterém je skupina zabezpečení sítě byl použit blokující služby Azure AD Domain Services z aktualizace a správa vaší domény.**
>
>

## <a name="ports-required-for-azure-ad-domain-services"></a>Porty vyžadované pro službu Azure AD Domain Services
Následující porty jsou povinné pro Azure AD Domain Services do služby a udržovat vaší spravované domény. Ujistěte se, že tyto porty nejsou blokované pro podsíť, ve kterém jste povolili vaší spravované domény.

| Číslo portu | Povinné? | Účel |
| --- | --- | --- |
| 443 | Povinné |Synchronizace v tenantu Azure AD |
| 5986 | Povinné | Správa domény |
| 3389 | Povinné | Správa domény |
| 636 | Nepovinné | Zabezpečený LDAP (LDAPS) přístup k vaší spravované domény |

**Port 443 (synchronizaci se službou Azure AD)**
* Používá se pro synchronizaci adresáře Azure AD s vaší spravované domény.
* Je nutné povolit přístup na tento port v NSG. Bez přístupu na tento port není synchronizovaná s adresářem Azure AD vaší spravované domény. Uživatelé se možná nebudou moct přihlásit, protože změny hesla se nesynchronizují do spravované domény.
* Na tento port na IP adresy, které patří do rozsah adres IP adres Azure můžete omezit příchozí přístup. Všimněte si, že rozsah adres IP adres Azure je jiný rozsah než je rozsah prostředí PowerShell je znázorněno v následující pravidlo.

**Port 5986 (Vzdálená komunikace prostředí PowerShell)**
* Používá se k provádění úloh správy ve vaší spravované doméně pomocí vzdálené komunikace Powershellu.
* Je nutné povolit přístup přes tento port v NSG. Bez přístupu na tento port nemůže být vaší spravované domény aktualizované, nakonfigurované, zálohovaná nebo monitorované.
* Pro nové domény nebo domény s virtuální sítí Azure Resource Manageru můžete omezit příchozí přístup na tento port pro následující zdrojové IP adresy: 52.180.179.108, 52.180.177.87, 13.75.105.168, 52.175.18.134, 52.138.68.41, 52.138.65.157, 104.41.159.212, 104.45.138.161, 52.169.125.119, 52.169.218.0, 52.187.19.1, 52.187.120.237, 13.78.172.246, 52.161.110.169, 52.174.189.149, 40.68.160.142, 40.83.144.56, 13.64.151.161, 52.180.183.67, 52.180.181.39, 52.175.28.111, 52.175.16.141, 52.138.70.93, 52.138.64.115, 40.80.146.22, 40.121.211.60, 52.138.143.173, 52.169.87.10, 13.76.171.84, 52.187.169.156, 13.78.174.255, 13.78.191.178, 40.68.163.143, 23.100.14.28, 13.64.188.43, 23.99.93.197
* U domén s klasickou virtuální síť můžete omezit příchozí přístup na tento port pro následující zdrojové IP adresy: 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209
* Řadiče domény vaší spravované domény obvykle Nenaslouchat na tomto portu. Služba otevře tento port na spravovaných řadičích domény se pouze v případě, že operace správy nebo údržby je třeba provést pro spravovanou doménu. Co nejdříve po dokončení této operace ukončení služby tento port spravovaných řadičích domény.

**Port 3389 (Vzdálená plocha)**
* Používá se pro připojení ke vzdálené ploše na řadiče domény vaší spravované domény.
* Můžete omezit příchozí přístup k následující zdrojové IP adresy: 207.68.190.32/27, 13.106.78.32/27, 13.106.174.32/27, 13.106.4.96/27
* Tento port taky zůstane ve vaší spravované doméně do značné míry vypnuté. Tento mechanismus není průběžně použít, protože správa a monitorování úloh se provádějí pomocí vzdálené komunikace Powershellu. Tento port se používá jenom v výjimečná událost, kterou je potřeba Microsoft vzdálené připojení k vaší spravované domény pro řešení potíží pro pokročilé. Port je zavřen, co nejdříve po dokončení operace odstraňování potíží.

**Port 636 (LDAPS)**
* Se používá k povolení přístupu k vaší spravované domény secure LDAP přes internet.
* Otevření prostřednictvím skupina NSG na tento port je volitelné. Otevřete port pouze v případě, že máte přístup protokolu secure LDAP přes internet povolena.
* Příchozí přístup můžete omezit na tento port na zdrojové IP adresy, ze kterých plánujete připojit přes protokol secure LDAP.


## <a name="network-security-groups"></a>Network Security Groups (Skupiny zabezpečení sítě)
A [skupina zabezpečení sítě (NSG)](../virtual-network/virtual-networks-nsg.md) obsahuje seznam pravidel seznamu řízení přístupu (ACL), která povolují nebo odpírají síťový provoz do vašich instancí virtuálních počítačů ve virtuální síti. Skupiny NSG můžou být přidružené buď k podsítím, nebo k jednotlivým instancím virtuálních počítačů v této podsíti. Pokud je skupina zabezpečení sítě přidružená k podsíti, pravidla seznamu ACL platí pro všechny instance virtuálních počítačů v této podsíti. Kromě toho provoz do konkrétního virtuálního počítače je možné omezit další tím, že přidružíte skupinu NSG přímo k tomuto virtuálnímu počítači.

### <a name="sample-nsg-for-virtual-networks-with-azure-ad-domain-services"></a>Ukázková skupina zabezpečení sítě pro virtuální sítě pomocí Azure AD Domain Services
Následující tabulka uvádí ukázku skupiny zabezpečení sítě můžete nakonfigurovat pro virtuální síť s spravované domény služby Azure AD Domain Services. Toto pravidlo umožňuje příchozí provoz přes požadované porty, aby vaše spravovaná doména zůstanou opravit, aktualizovat a je možné monitorovat společností Microsoft. Výchozí pravidlo "DenyAll" platí pro všechny další příchozí provoz z Internetu.

Kromě toho NSG také ukazuje, jak uzamknout protokolu Secure LDAP přes internet. Toto pravidlo přeskočte, pokud jste ještě nepovolili přístup protokolu secure LDAP k vaší spravované doméně přes internet. Skupiny zabezpečení sítě obsahuje sadu pravidel, které umožňují přístup protokolu LDAPS pro příchozí připojení přes port TCP 636 pouze ze zadaných IP adres. Pravidla NSG pro povolení protokolu LDAPS přístup přes internet ze zadaných IP adres má vyšší prioritu než pravidla DenyAll NSG.

![Ukázka skupiny NSG k zabezpečení přístupu k protokolu LDAPS přes internet](./media/active-directory-domain-services-alerts/default-nsg.png)

**Další informace o** - [vytvořte skupinu zabezpečení sítě](../virtual-network/manage-network-security-group.md).


## <a name="network-connectivity"></a>Připojení k síti
Spravované domény služby Azure AD Domain Services je možné povolit jenom v rámci jedné virtuální sítě v Azure.

### <a name="scenarios-for-connecting-azure-networks"></a>Scénáře pro propojení sítí Azure
Připojení virtuální sítě Azure používat spravovanou doménu v některém z těchto scénářích nasazení:

#### <a name="use-the-managed-domain-in-more-than-one-azure-virtual-network"></a>Používat spravovanou doménu ve více než jednu virtuální síť Azure
Jiným virtuálním sítím Azure můžete připojit k virtuální síti Azure, ve kterém jste povolili službu Azure AD Domain Services. Toto připojení s partnerským vztahem VPN/virtuální síť umožňuje používat spravovanou doménu pomocí úlohy nasazení v jiné virtuální sítě.

![Připojení klasické virtuální sítě](./media/active-directory-domain-services-design-guide/classic-vnet-connectivity.png)

#### <a name="use-the-managed-domain-in-a-resource-manager-based-virtual-network"></a>Používat spravovanou doménu ve virtuální síti bázi Resource Manageru
Virtuální sítě založené na Resource Manageru můžete připojit k Azure klasická virtuální síť, ve kterém jste povolili službu Azure AD Domain Services. Toto připojení umožňuje používat spravovanou doménu pomocí vašich úloh nasazených ve virtuální síti bázi Resource Manageru.

![Připojení klasické virtuální sítě Resource Manageru](./media/active-directory-domain-services-design-guide/classic-arm-vnet-connectivity.png)

### <a name="network-connection-options"></a>Možnosti připojení sítě
* **Připojení VNet-to-VNet pomocí virtuální sítě partnerských vztahů**: Partnerské vztahy virtuálních sítí je mechanismus, který spojuje dvě virtuální sítě ve stejné oblasti prostřednictvím páteřní sítě Azure. Po navázání partnerského vztahu se tyto dvě virtuální sítě pro všechny účely připojení jeví jako jedna síť. Jsou i nadále spravovány jako samostatné prostředky, ale virtuální počítače v těchto virtuálních sítích spolu mohou komunikovat přímo pomocí privátních IP adres.

    ![Připojení k virtuální síti pomocí partnerského vztahu](./media/active-directory-domain-services-design-guide/vnet-peering.png)

    [Další informace – virtuální síť vytvoření partnerského vztahu](../virtual-network/virtual-network-peering-overview.md)

* **Připojení VNet-to-VNet pomocí připojení site-to-site VPN**: Propojení virtuální sítě s jinou virtuální sítí (VNet-to-VNet) je podobné propojení virtuální sítě s místním serverem. Oba typy připojení využívají bránu VPN k poskytnutí zabezpečeného tunelového propojení prostřednictvím protokolu IPsec/IKE.

    ![Připojení k virtuální síti pomocí sítě VPN Gateway](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

    [Další informace – propojení virtuálních sítí pomocí VPN gateway](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

<br>

## <a name="related-content"></a>Související obsah
* [Partnerský vztah virtuální sítě Azure](../virtual-network/virtual-network-peering-overview.md)
* [Konfigurace připojení typu VNet-to-VNet pro model nasazení classic](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)
* [Skupiny zabezpečení sítě Azure](../virtual-network/security-overview.md)
* [Vytvořte skupinu zabezpečení sítě](../virtual-network/manage-network-security-group.md)
