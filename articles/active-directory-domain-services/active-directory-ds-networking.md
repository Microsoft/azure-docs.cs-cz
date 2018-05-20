---
title: 'Azure AD Domain Services: Pokyny pro sítě | Microsoft Docs'
description: Požadavky sítě pro Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 23a857a5-2720-400a-ab9b-1ba61e7b145a
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: maheshu
ms.openlocfilehash: b40aa0e105c0e9fac9c9cab63a5b0a2a6116c4c9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="networking-considerations-for-azure-ad-domain-services"></a>Požadavky sítě pro Azure AD Domain Services
## <a name="how-to-select-an-azure-virtual-network"></a>Jak vybrat virtuální síť Azure
Následující pokyny vám pomohou vybrat virtuální sítě pro použití se službou Azure AD Domain Services.

### <a name="type-of-azure-virtual-network"></a>Typ virtuální sítě Azure
* **Virtuální sítě Resource Manager**: Azure AD Domain Services se dají povolit v virtuální sítě vytvořené pomocí Azure Resource Manager.
* Nelze povolit Azure AD Domain Services ve klasické virtuální síti Azure.
* Další virtuální sítě můžete připojit k virtuální síti, ve které je povolili službu Azure AD Domain Services. Další informace najdete v tématu [připojení k síti](active-directory-ds-networking.md#network-connectivity) části.

### <a name="azure-region-for-the-virtual-network"></a>Oblast Azure pro virtuální síť
* Vaše Azure AD Domain Services spravované domény je nasazená ve stejné oblasti Azure jako virtuální síť rozhodnete povolit služby.
* Vyberte virtuální síť v oblasti Azure podporované službou Azure AD Domain Services.
* Na stránce [Služby Azure podle oblasti](https://azure.microsoft.com/regions/#services/) se dozvíte, pro jaké oblasti je dostupná služba Azure AD Domain Services.

### <a name="requirements-for-the-virtual-network"></a>Požadavky pro virtuální síť
* **Blízkosti Azure úlohy**: vyberte virtuální síť, které aktuálně hostitelem virtuálních počítačů, kteří potřebují přístup k Azure AD Domain Services. Pokud vaše úlohy jsou nasazeny v jiné virtuální síti než spravované doméně, můžete také propojit virtuální sítě.
* **Vlastní nebo přineste vlastní servery DNS**: Ujistěte se, že neexistují žádné vlastní servery DNS, které jsou nakonfigurované pro virtuální síť. Příkladem vlastního serveru DNS je instance virtuálního počítače na serveru Windows, který jste nasadili ve virtuální síti a systémem Windows Server DNS. Služba Azure AD Domain Services nelze integrovat do žádné vlastní servery DNS, které jsou nasazené v rámci virtuální sítě.
* **Stávajících doménách se stejným názvem domény**: Ujistěte se, že nemáte existující doménu se stejným názvem domény k dispozici ve virtuální síti. Například předpokládejme, že již máte doménu „contoso.com“ k dispozici na vybrané virtuální síti. Později Zkuste povolit spravované doméně služby Azure AD Domain Services s se stejným názvem domény (to znamená "contoso.com") ve virtuální síti. Dojde k chybě při pokusu o povolení služby Azure AD Domain Services. Toto selhání je z důvodu konfliktu názvů pro název domény ve virtuální síti. V této situaci musíte použít jiný název pro nastavení domény spravované službou Azure AD Domain Services. Případně můžete zrušit existující doménu a poté pokračovat v povolení služby Azure AD Domain Services.

> [!WARNING]
> Domain Services nelze přesunout do jiné virtuální síti, poté, co povolíte službu.
>
>


## <a name="guidelines-for-choosing-a-subnet"></a>Pokyny pro výběr podsíť

![Doporučené podsíť návrhu](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

* Nasazení Azure AD Domain Services **oddělit vyhrazené podsíť** v rámci virtuální sítě Azure.
* Neplatí skupiny Nsg k podsíti vyhrazené vaší spravované domény. Pokud musíte použít skupiny Nsg na vyhrazené podsíť, ujistěte se, můžete **nezadávejte blokovat porty vyžadované pro služby a spravovat vaše doména**.
* Neomezují zbytečně počet IP adres, které jsou k dispozici v rámci podsítě vyhrazené vaší spravované domény. Toto omezení zabrání službu zpřístupnění dva řadiče domény vaší spravované domény.
* **Nepovolujte Azure AD Domain Services v podsíti brány** virtuální sítě.
* Neblokují odchozí přístup z podsítě, ve kterém je povolená vaší spravované domény.

> [!WARNING]
> Pokud přidružíte skupinu NSG s podsítí, ve kterém služba Azure AD Domain Services je povoleno, mohou narušit možnost služeb a spravovat doménu společnosti Microsoft. Kromě toho synchronizace mezi vašeho klienta Azure AD a vaší spravované domény dojde k narušení. **Smlouva SLA se nevztahuje na nasazení, kde skupinu NSG použil blokující Azure AD Domain Services z aktualizace a Správa domény.**
>
>

## <a name="ports-required-for-azure-ad-domain-services"></a>Porty vyžadované pro Azure AD Domain Services
Následující porty jsou povinné pro Azure AD Domain Services do služby a údržbu vaší spravované domény. Zajistěte, aby tyto porty nejsou blokováno pro podsíť, ve kterém jste povolili vaší spravované domény.

| Číslo portu | Povinné? | Účel |
| --- | --- | --- |
| 443 | Povinné |Synchronizace se klientovi služby Azure AD |
| 5986 | Povinné | Správa vaší domény |
| 3389 | Nepovinné | Správa vaší domény |
| 636 | Nepovinné | Zabezpečený LDAP (LDAPS) přístup k spravované doméně |

**Port 443 (synchronizaci se službou Azure AD)**
* Slouží k synchronizaci adresáře Azure AD s vaší spravované domény.
* Je nutné povolit přístup na tento port ve vaší skupině. Bez přístupu na tento port není synchronizována s adresáře Azure AD vaší spravované domény. Uživatelé nebudou moci přihlásit, protože nejsou synchronizovány změny hesla k vaší spravované domény.
* Příchozí přístup můžete omezit na tento port na IP adresy, které patří do rozsahu Azure IP adres. Všimněte si, že rozsah adres Azure IP jiný rozsah než je rozsah prostředí PowerShell vidět v pravidle níže.

**Port 5986 (Vzdálená komunikace prostředí PowerShell)**
* Slouží k provádění úloh správy pomocí vzdálenou komunikaci prostředí PowerShell na vaší spravované domény.
* Je nutné povolit přístup pomocí tohoto portu ve vaší skupině. Bez přístupu na tento port vaší spravované domény nemůže být aktualizované, nakonfigurované, zálohované nebo monitorovaných.
* Příchozí přístup můžete omezit na tento port pro následující zdrojové IP adresy: 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161
* Řadiče domény pro vaší spravované domény Nenaslouchat obvykle na tento port. Služba otevře tento port na spravovaných řadičích domény se jenom v případě, že operace údržby nebo správy je nutné provést pro spravovanou doménu. Při dokončení operace vypne službu tento port na spravovaných řadičích domény.

**Port 3389 (Vzdálená plocha)**
* Používá se pro připojení ke vzdálené ploše na řadiče domény vaší spravované domény.
* Otevírání tohoto portu prostřednictvím vaší NSG je volitelný.
* Tento port je také zůstane na vaší spravované domény z velké části vypnuty. Tento mechanismus není průběžně použít, protože pro správu a sledování úkolů se provádí pomocí vzdálenou komunikaci prostředí PowerShell. Tento port se používá pouze v výjimečná událost, která Microsoft potřebuje vzdáleně připojit k vaší spravované domény pro pokročilé řešení problémů. Port je zavřený, při řešení potíží operace se dokončila.

**Port 636 zabezpečený LDAP)**
* Slouží k povolení zabezpečeného přístupu LDAP k vaší spravované domény přes internet.
* Otevírání tohoto portu prostřednictvím vaší NSG je volitelný. Otevřete port pouze v případě, že máte zabezpečený LDAP přístup přes internet povoleno.
* Příchozí přístup můžete omezit na tento port na zdrojové IP adresy, ze kterých plánujete připojit přes zabezpečený LDAP.

**Odchozí přístup** služby AAD Domain Services potřebuje odchozí přístup k různým jiných služeb systému Azure, aby bylo možné spravovat, zálohování a monitorování vaší spravované domény. Neblokují odchozí přístup z vyhrazených podsítě, ve kterém je povolená vaší spravované domény.


## <a name="network-security-groups"></a>Network Security Groups (Skupiny zabezpečení sítě)
A [skupina zabezpečení sítě (NSG)](../virtual-network/security-overview.md) obsahuje seznam pravidel seznamu řízení přístupu (ACL), která povolují nebo odpírají síťový provoz instancím virtuálních počítačů ve virtuální síti. Skupiny NSG můžou být přidružené buď k podsítím, nebo k jednotlivým instancím virtuálních počítačů v této podsíti. Pokud je skupina zabezpečení sítě přidružená k podsíti, pravidla seznamu ACL platí pro všechny instance virtuálních počítačů v této podsíti. Kromě toho je možné omezit provoz do konkrétního virtuálního počítače další tím, že přidružíte skupinu NSG přímo do tohoto virtuálního počítače.

### <a name="sample-nsg-for-virtual-networks-with-azure-ad-domain-services"></a>Ukázka NSG pro virtuální sítě s Azure AD Domain Services
Následující tabulka znázorňuje ukázku NSG můžete nakonfigurovat pro virtuální síť s spravované doméně služby Azure AD Domain Services. Toto pravidlo umožňuje příchozí provoz přes požadované porty pro zajištění vaší spravované domény zůstane opravit, aktualizovat a společnost Microsoft se dá sledovat. Výchozí pravidlo, DenyAll, platí pro všechny ostatní příchozí přenosy z Internetu.

Kromě toho NSG také ukazuje, jak zamknout zabezpečený LDAP přístup přes internet. Přeskočte toto pravidlo, pokud jste nepovolili zabezpečený LDAP přístup k vaší spravované domény přes internet. NSG obsahuje sadu pravidel, která povolí příchozí LDAPS přístup přes port TCP 636 pouze ze zadané sady IP adres. Pravidla NSG pro povolení LDAPS přístupu přes internet ze zadaných IP adres má vyšší prioritu než skupina NSG DenyAll pravidlo.

![Ukázka skupiny NSG k zabezpečení přístupu k LDAPS přes internet](.\media\active-directory-domain-services-alerts\default-nsg.png)

**Další informace** - [vytvořit skupinu zabezpečení sítě](../virtual-network/virtual-networks-create-nsg-arm-pportal.md).


## <a name="network-connectivity"></a>Připojení k síti
Spravované doméně služby Azure AD Domain Services lze povolit pouze v rámci jedné virtuální sítě v Azure.

### <a name="scenarios-for-connecting-azure-networks"></a>Scénáře pro připojení sítě Azure
Připojte virtuální sítě Azure používat spravované doméně v některém z těchto scénářích nasazení:

#### <a name="use-the-managed-domain-in-more-than-one-azure-virtual-network"></a>Používat spravované domény ve více než jednu virtuální síť Azure
Další virtuální sítě Azure můžete připojit k virtuální síti Azure, ve kterém jste povolili službu Azure AD Domain Services. Toto připojení VPN/VNet partnerského vztahu můžete spravované doméně pomocí úlohy nasazené v jiných virtuálních sítí.

![Připojení klasické virtuální sítě](./media/active-directory-domain-services-design-guide/classic-vnet-connectivity.png)

#### <a name="use-the-managed-domain-in-a-resource-manager-based-virtual-network"></a>Používat spravované domény ve virtuální síti na základě Resource Manager
Virtuální síť využívající Resource Manager můžete připojit k Azure klasickou virtuální síť, ve kterém jste povolili službu Azure AD Domain Services. Toto připojení umožňuje spravované doméně pomocí úlohy nasazené ve virtuální síti na základě Resource Manager.

![Správce prostředků klasickou virtuální síť připojení](./media/active-directory-domain-services-design-guide/classic-arm-vnet-connectivity.png)

### <a name="network-connection-options"></a>Možnosti připojení sítě
* **Připojení VNet-to-VNet pomocí virtuální sítě, partnerský vztah**: partnerský vztah virtuální sítě je mechanismus, který se připojuje prostřednictvím Azure páteřní síti dvou virtuálních sítí ve stejné oblasti. Po navázání partnerského vztahu se tyto dvě virtuální sítě pro všechny účely připojení jeví jako jedna síť. Jsou i nadále spravovány jako samostatné prostředky, ale virtuální počítače v těchto virtuálních sítích spolu mohou komunikovat přímo pomocí privátních IP adres.

    ![Připojení k virtuální síti pomocí partnerského vztahu](./media/active-directory-domain-services-design-guide/vnet-peering.png)

    [Další informace - virtuální sítě partnerského vztahu](../virtual-network/virtual-network-peering-overview.md)

* **Připojení VNet-to-VNet pomocí připojení site-to-site VPN**: propojení virtuální sítě s jinou virtuální sítí (VNet-to-VNet) je podobné propojení virtuální sítě do umístění místního webu. Oba typy připojení využívají bránu VPN k poskytnutí zabezpečeného tunelového propojení prostřednictvím protokolu IPsec/IKE.

    ![Připojení k virtuální síti s použitím brány VPN](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

    [Další informace - připojení virtuální sítě pomocí brány sítě VPN](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

<br>

## <a name="related-content"></a>Související obsah
* [Partnerský vztah virtuální síť Azure](../virtual-network/virtual-network-peering-overview.md)
* [Nakonfigurujte připojení VNet-to-VNet pro model nasazení classic](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)
* [Skupiny zabezpečení sítě Azure](../virtual-network/security-overview.md)
* [Vytvořit skupinu zabezpečení sítě](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)
