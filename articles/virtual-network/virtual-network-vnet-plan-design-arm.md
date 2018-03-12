---
title: "Průvodce návrhem a plán virtuální sítě Azure (VNet) | Microsoft Docs"
description: "Informace o plánování a návrh virtuálních sítí v Azure na základě požadavků vaší izolace, připojení a umístění."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: tysonn
ms.assetid: 3a4a9aea-7608-4d2e-bb3c-40de2e537200
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/08/2016
ms.author: jdial
ms.openlocfilehash: ecdc3a847821fd83718f9cfc42308667460feabc
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="plan-and-design-azure-virtual-networks"></a>Naplánujte a navrhněte Azure Virtual Network
Vytvoření virtuální sítě a experimentovat s je dostatečně snadno, ale pravděpodobné, budete nasazovat více virtuálních sítí v čase pro podporu provozním potřebám vaší organizace. S některými plánování a návrhu bude možné nasadit virtuální sítě a připojení prostředků, které potřebujete efektivněji. Pokud nejste obeznámeni s virtuální sítě, se doporučuje, můžete [Další informace o virtuálních sítí](virtual-networks-overview.md) a [nasazení](quick-create-portal.md) jeden než budete pokračovat.

## <a name="plan"></a>Plánování
Je nutné pro úspěch důkladné znalosti týkající se předplatná Azure, oblasti a síťovým prostředkům. Seznam důležitých informací níže můžete použít jako počáteční bod. Jakmile porozumíte tyto aspekty, můžete definovat požadavky na návrh vaší sítě.

### <a name="considerations"></a>Požadavky
Předtím, než když si odpovíte plánování otázky, které se dole, zvažte následující:

* Všechny objekty, které vytvoříte v Azure se skládá z jedné nebo více prostředků. Virtuální počítač (VM) je prostředek, síťového adaptéru rozhraní (NIC) používá virtuální počítač je prostředek, veřejnou IP adresu použitou adaptérem je prostředek, síťový adaptér je připojen k virtuální síti je prostředek.
* Vytvořit prostředky v rámci [oblast Azure](https://azure.microsoft.com/regions/#services) a předplatné. Prostředky lze připojit pouze k virtuální síti, která existuje ve stejné oblasti a předplatné prostředku je.
* Virtuální sítě můžete připojit k sobě navzájem pomocí:
    * **[Partnerský vztah virtuální sítě](virtual-network-peering-overview.md)**: virtuální sítě musí být ve stejné oblasti Azure. Šířka pásma mezi prostředky v peered virtuální sítě je stejný, jako kdyby byly prostředky připojené ke stejné virtuální síti.
    * **Azure [brány VPN](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)**: virtuální sítě může existovat v oblastech Azure stejný nebo jiný. Šířku pásma mezi prostředky ve virtuálních sítích, které se připojují prostřednictvím brány VPN je omezena šířka pásma brány VPN.
* Virtuální sítě můžete připojit k místní síti pomocí jedné z [možnosti připojení](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti) v Azure k dispozici.
* Různých prostředků můžete seskupit do [skupiny prostředků](../azure-resource-manager/resource-group-overview.md#resource-groups), což usnadňuje správu prostředků jako jednotku. Skupiny prostředků může obsahovat prostředky z několika oblastí, tak dlouho, dokud prostředky patří do stejného předplatného.

### <a name="define-requirements"></a>Definujte požadavky
Použijte na otázky níže jako výchozí bod pro návrh vaší sítě Azure.    

1. Umístění, ve kterých Azure budou používat hostiteli virtuální sítě?
2. Je třeba zadat komunikace mezi těmito Azure místy?
3. Je třeba zadat komunikace mezi vaší VNet(s) Azure a vašich datových centrech místní?
4. Kolik infrastruktury jako služby (IaaS) VMs, cloudových služeb rolí a proveďte webové aplikace, které potřebujete pro vaše řešení?
5. Potřebujete k izolaci provozu na základě skupin virtuální počítače (tj. front-endu webové servery a servery databáze back-end)?
6. Je třeba řízení toku provozu pomocí virtuální zařízení?
7. Mají uživatelé různé sady oprávnění k různými prostředky Azure?

### <a name="understand-vnet-and-subnet-properties"></a>Pochopení virtuálních sítí a podsítí vlastnosti
Virtuální sítě a podsítě prostředky pomohl definovat hranici zabezpečení pro úlohy běžící v Azure. Virtuální síť je charakterizovaná kolekce adresní prostory, které jsou definované jako bloků CIDR.

> [!NOTE]
> Správci sítě se seznámíte s notaci CIDR. Pokud nejste obeznámeni s CIDR, [Další informace o](http://whatismyipaddress.com/cidr).
>
>

Virtuální sítě obsahují následující vlastnosti.

| Vlastnost | Popis | Omezení |
| --- | --- | --- |
| **name** |Název virtuální sítě |Řetězec až 80 znaků. Může obsahovat písmena, číslice, podtržítka, tečky a pomlčky. Musí začínat písmenem nebo číslicí. Musí končit písmenem, číslicí nebo podtržítkem. Můžete obsahuje velká nebo malá písmena. |
| **location** |Umístění Azure (označované také jako oblast). |Musí mít jednu z platných umístění Azure. |
| **addressSpace** |Kolekce předpon adres, které tvoří virtuální sítě v zápisu CIDR. |Musí být pole platné bloky adres CIDR, včetně rozsahů veřejných IP adres. |
| **Podsítě** |Kolekce podsítě, které tvoří virtuální sítě |viz následující tabulka vlastnosti podsítě. |
| **dhcpOptions** |Objekt, který obsahuje jeden požadovaná vlastnost s názvem **dnsServers**. | |
| **dnsServers** |Pole serverů DNS používaných virtuální sítě. Pokud není zadaný žádný server, se používá Azure interní překlad adres. |Musí být pole až 10 serverů DNS, podle IP adresy. |

Podsíť je prostředkem podřízené virtuální sítě, a pomáhá definovat segmenty adresní prostory v rámci blok CIDR pomocí předpony IP adres. Síťové adaptéry můžete přidat do podsítí a připojení k virtuálním počítačům, poskytuje připojení pro různé úlohy.

Podsítě obsahují následující vlastnosti.

| Vlastnost | Popis | Omezení |
| --- | --- | --- |
| **name** |Název podsítě |Řetězec až 80 znaků. Může obsahovat písmena, číslice, podtržítka, tečky a pomlčky. Musí začínat písmenem nebo číslicí. Musí končit písmenem, číslicí nebo podtržítkem. Můžete obsahuje velká nebo malá písmena. |
| **location** |Umístění Azure (označované také jako oblast). |Musí mít jednu z platných umístění Azure. |
| **addressPrefix** |Jedna adresa předponu, která tvoří podsíť v notaci CIDR |Musí být jeden blok CIDR, která je součástí jednoho virtuální síť adresní prostory. |
| **networkSecurityGroup** |Skupina NSG použije na podsíť | |
| **routeTable** |Směrovací tabulka použije na podsíť | |
| **Konfigurace IP adresy** |Kolekce objektů konfigurace IP používané síťové adaptéry připojené k podsíti | |

### <a name="name-resolution"></a>Překlad adres
Ve výchozím nastavení, virtuální sítě používá [Azure překlad](virtual-networks-name-resolution-for-vms-and-role-instances.md) překládat názvy uvnitř virtuální sítě a na veřejného Internetu. Ale pokud vaše virtuální sítě se připojit k vaší místní datových center, potřebujete poskytovat [serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md) překládat názvy mezi vaší sítí.  

### <a name="limits"></a>Omezení
Zkontrolujte síťové omezení v [Azure omezuje](../azure-subscription-service-limits.md#networking-limits) článek a ujistěte se, že váš návrh není v konfliktu s žádným z omezení. Některá omezení je možné zvýšit otevřením lístku podpory.

### <a name="role-based-access-control-rbac"></a>Řízení přístupu na základě role (RBAC)
Můžete použít [Azure RBAC](../active-directory/role-based-access-built-in-roles.md) řídit úroveň přístupu k prostředkům v Azure může mít různé uživatele. Tímto způsobem můžete oddělit práci váš tým podle svých potřeb.

Pokud jde o virtuální sítě jsou problémem, uživatelé v **Přispěvatel sítě** role mít plnou kontrolu nad prostředky virtuální sítě Azure Resource Manager. Podobně, uživatelé v **Classic Přispěvatel sítě** role mít plnou kontrolu nad klasické virtuální síťové prostředky.

> [!NOTE]
> Můžete také [vytvářet vlastní role](../active-directory/role-based-access-control-configure.md) jednotlivé požadavky na správu.
>
>

## <a name="design"></a>Návrh
Pokud znáte odpovědi na otázky v [plánování](#Plan) , projděte si následující před definováním vaší virtuální sítě.

### <a name="number-of-subscriptions-and-vnets"></a>Počet odběrů a virtuální sítě
Měli byste zvážit vytvoření více virtuálních sítí v následujících scénářích:

* **Virtuální počítače, které musí být umístěny v různých umístěních Azure**. Virtuální sítě v Azure jsou místní. Jejich nemůžou zahrnovat umístění. Proto musíte mít alespoň jeden virtuální sítě pro každé umístění Azure, kterou chcete hostitele virtuálních počítačů v.
* **Úlohy, které musí být naprosto izolované od sebe navzájem**. Můžete vytvořit samostatné virtuální sítě, který i pomocí stejné adresní prostory IP adres, různých zátěží od sebe navzájem izolovat.

Mějte na paměti, které jsou omezení, které vidíte výše na oblast na předplatné. To znamená, že používáte více předplatných o zvýšení limitu prostředků, které je možné uchovávat v Azure. Síť site-to-site VPN nebo okruhem ExpressRoute slouží k propojení virtuálních sítí v různých předplatných.

### <a name="subscription-and-vnet-design-patterns"></a>Předplatné a vzory návrhu virtuální sítě
Následující tabulka uvádí některé běžné vzory návrhu pro použití odběry a virtuální sítě.

| Scénář | Diagram | Odborníci na | Nevýhody |
| --- | --- | --- | --- |
| Jednomu předplatnému, dvě virtuální sítě na aplikace |![Jedno předplatné](./media/virtual-network-vnet-plan-design-arm/figure1.png) |Jenom jedno předplatné pro správu. |Maximální počet virtuálních sítí na oblast Azure. Potom musíte více odběrů. Zkontrolujte [Azure omezuje](../azure-subscription-service-limits.md#networking-limits) článku. |
| S jedním odběrem na aplikaci, dvě virtuální sítě na aplikace |![Jedno předplatné](./media/virtual-network-vnet-plan-design-arm/figure2.png) |Využívá jenom dva virtuální sítě na jedno předplatné. |Těžší ke správě, pokud jsou moc velký počet aplikací. |
| S jedním odběrem na organizační jednotku, dvě virtuální sítě, jednu aplikaci. |![Jedno předplatné](./media/virtual-network-vnet-plan-design-arm/figure3.png) |Vyrovnávat mezi počet odběrů a virtuální sítě. |Maximální počet virtuálních sítí na organizační jednotku (předplatné). Zkontrolujte [Azure omezuje](../azure-subscription-service-limits.md#networking-limits) článku. |
| S jedním odběrem na organizační jednotku, dvě virtuální sítě na skupinu aplikací. |![Jedno předplatné](./media/virtual-network-vnet-plan-design-arm/figure4.png) |Vyrovnávat mezi počet odběrů a virtuální sítě. |Aplikace musí být izolovat pomocí podsítě a skupin Nsg. |

### <a name="number-of-subnets"></a>Počet podsítí
Je třeba zvážit několik podsítí ve virtuální síti v následujících scénářích:

* **Není dostatek privátních IP adres pro všechny síťové adaptéry v podsíti**. Pokud váš adresní prostor podsítě neobsahuje dost IP adres pro počet síťových adaptérů v podsíti, musíte vytvořit několik podsítí. Mějte na paměti, který Azure si vyhrazuje 5 soukromé IP adresy z každé podsítě, která se nedá použít: první a poslední adresy adresní prostor (pro adresu podsítě a vícesměrové vysílání) a 3 adres používat interně (pro účely DHCP a DNS).
* **Zabezpečení.** Můžete oddělit skupiny virtuálních počítačů od sebe navzájem pro úlohy, které mají více vrstvu struktury a použít jiné podsítě [skupin zabezpečení (Nsg) sítě](virtual-networks-nsg.md#subnets) pro tyto podsítě.
* **Hybridní připojení**. Můžete použít brány sítě VPN a okruhy ExpressRoute do [připojit](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti) vaší virtuální sítě na sebe navzájem a center(s) vaše místní data. Okruhy ExpressRoute a bran VPN vyžadují podsíť vlastní, který se má vytvořit.
* **Virtuální zařízení**. Virtuální zařízení, jako jsou brány firewall, akcelerátorů WAN nebo brány sítě VPN můžete použít ve službě Azure VNet. Pokud tak učiníte, budete muset [směrování provozu](virtual-networks-udr-overview.md) do těchto zařízení a jejich zjištění ve vlastní podsíti.

### <a name="subnet-and-nsg-design-patterns"></a>Podsíť a NSG vzory návrhu
Následující tabulka uvádí některé běžné vzory návrhu pro používání podsítě.

| Scénář | Diagram | Odborníci na | Nevýhody |
| --- | --- | --- | --- |
| Jedna podsíť, počet skupin Nsg na aplikační vrstvu, jednotlivé aplikace |![Jedna podsíť](./media/virtual-network-vnet-plan-design-arm/figure5.png) |Jenom jednu podsíť pro správu. |Více skupin Nsg nutné izolovat každou aplikaci. |
| Jednu podsíť jednu aplikaci, počet skupin Nsg na aplikační vrstvu |![Podsíť jednotlivé aplikace](./media/virtual-network-vnet-plan-design-arm/figure6.png) |Menší počet skupin Nsg pro správu. |Několik podsítí ke správě. |
| Jednu podsíť pro aplikační vrstvu, počet skupin Nsg na aplikaci. |![Podsíť jednu vrstvu](./media/virtual-network-vnet-plan-design-arm/figure7.png) |Rovnováha mezi Nsg a počet podsítí. |Maximální počet skupin Nsg na jedno předplatné. Zkontrolujte [Azure omezuje](../azure-subscription-service-limits.md#networking-limits) článku. |
| Jednu podsíť pro aplikační vrstvu, jednu aplikaci, počet skupin Nsg na podsítě |![Podsíť jednu vrstvu jednotlivé aplikace](./media/virtual-network-vnet-plan-design-arm/figure8.png) |Případně zmenšete počet skupin Nsg. |Několik podsítí ke správě. |

## <a name="sample-design"></a>Ukázka návrhu
Pro ilustraci použití informací obsažených v tomto článku, zvažte následující scénář.

Pracujete pro společnost, která má 2 datových centrech v Severní Americe a dvě datových centrech Evropa. Jste identifikovali 6 aplikace přístupných různých zákazníků, které se spravuje pomocí 2 jiné organizační jednotky, které chcete migrovat do Azure jako pilotní nasazení. Základní architekturu pro aplikace jsou následující:

* App1, počítači App2, App3 a App4 jsou webové aplikace hostované na serverech Linux s Ubuntu. Každá aplikace připojuje k serveru samostatné aplikace, který je hostitelem služby RESTful na servery se systémem Linux. Služeb RESTful připojení k databázi MySQL back-end.
* App5 a App6 jsou webové aplikace hostované na serverech Windows se systémem Windows Server 2012 R2. Každá aplikace připojí k databázi systému SQL Server back-end.
* Všechny aplikace jsou teď hostované v jedné z firemních datových centrech v Severní Americe.
* Místní datová centra použít 10.0.0.0/8 adresní prostor.

Je třeba navrhnout řešení virtuální sítě, které splňuje následující požadavky:

* Jednotlivé obchodní jednotky by neměla mít vliv spotřeby prostředků z jiných organizačních jednotek.
* Měli byste minimalizovat objem virtuální sítě a podsítě, které usnadňují správu.
* Jednotlivé obchodní jednotky by měl mít jeden testovacím/vývojovém virtuální sítě používá pro všechny aplikace.
* Každá aplikace je hostitelem 2 různých datových center Azure na kontinentě (Severní Americe a Evropě).
* Každá aplikace je naprosto izolované od sebe navzájem.
* Každá aplikace přístupná zákazníkům přes Internet pomocí protokolu HTTP.
* Každá aplikace přístupná pomocí uživatelé připojení k místní datových centrech pomocí tunelového propojení šifrované.
* Připojení k místní datových centrech měli používat existující zařízení VPN.
* Firemní sítě skupiny musí mít plnou kontrolu nad konfiguraci virtuální sítě.
* Vývojáři v jednotlivé obchodní jednotky byste měli mít jenom k nasazení virtuálních počítačů do existující podsítí.
* Všechny aplikace bude migrovat, protože se jedná o do Azure (navýšení a shift).
* Databáze v každém umístění musí replikovat do jiných umístění Azure jednou denně.
* Každá aplikace by měl použít 5 front-endu webové servery, 2 aplikační servery (v případě potřeby) a 2 databázovým serverům.

### <a name="plan"></a>Plánování
Měli byste začít návrhu, plánování odpovědi na otázky v [definovat požadavky](#Define-requirements) části, jak je uvedeno níže.

1. Umístění, ve kterých Azure budou používat hostiteli virtuální sítě?

    2 umístění v Severní Americe a 2 umístění v Evropě. Měli byste vybrat algoritmů založených na fyzické umístění existující místní datacentrech. Tímto způsobem připojení ze fyzickými umístěními do Azure, budou mít lepší latence.
2. Je třeba zadat komunikace mezi těmito Azure místy?

    Ano. Vzhledem k tomu, že databáze musí být replikována do všech umístění.
3. Je třeba zadat komunikace mezi vaší VNet(s) Azure a místními center(s) dat?

    Ano. Vzhledem k tomu, že uživatelé připojeni k místní datových centrech musí být schopen přístup k aplikacím přes tunelové propojení šifrované.
4. Tom, kolik virtuálních počítačů IaaS potřebujete pro vaše řešení?

    200 virtuálních počítačů IaaS. App1, počítači App2, App3 a App4 vyžadují 5 webové servery každé, 2 aplikace každý server a 2 databázovým serverům. Je celkem 9 virtuálních počítačů IaaS na aplikaci nebo 36 virtuální počítače IaaS. App5 a App6 vyžadují 5 webové servery a 2 databázovým serverům. Je celkem 7 virtuální počítače IaaS na aplikaci nebo 14 virtuální počítače IaaS. Proto musíte 50 virtuální počítače IaaS pro všechny aplikace v každé oblasti Azure. Vzhledem k tomu, že je potřeba použít 4 oblasti, budou existovat 200 virtuálních počítačů IaaS.

    Musíte také zadat servery DNS v každé virtuální síť, nebo v datových centrech vaše místní přeložit název mezi virtuální počítače Azure IaaS a v místní síti.
5. Potřebujete k izolaci provozu na základě skupin virtuální počítače (tj. front-endu webové servery a servery databáze back-end)?

    Ano. Každá aplikace musí být vzájemně zcela izolované a každý aplikační vrstvu navíc by měl mít izolované.
6. Je třeba řízení toku provozu pomocí virtuální zařízení?

    Ne. Virtuální zařízení slouží k poskytuje větší kontrolu nad tok přenosů, včetně podrobnější protokolování roviny data.
7. Mají uživatelé různé sady oprávnění k různými prostředky Azure?

    Ano. Sítě tým musí úplné řízení na nastavení virtuální sítě, zatímco vývojáři mají jenom moct nasadit své virtuální počítače do již existující podsítí.

### <a name="design"></a>Návrh
Postupujte podle návrhu odběry, virtuální sítě, podsítě a skupin Nsg. Zde probereme skupin Nsg, ale měli další informace o [skupiny Nsg](virtual-networks-nsg.md) před dokončením návrhu.

**Počet odběrů a virtuální sítě**

Odběry a virtuální sítě se vztahují následující požadavky:

* Jednotlivé obchodní jednotky by neměla mít vliv spotřeby prostředků z jiných organizačních jednotek.
* Měli byste minimalizovat objem virtuální sítě a podsítě.
* Jednotlivé obchodní jednotky by měl mít jeden testovacím/vývojovém virtuální sítě používá pro všechny aplikace.
* Každá aplikace je hostitelem 2 různých datových center Azure na kontinentě (Severní Americe a Evropě).

Na základě těchto požadavků, potřebujete předplatné pro jednotlivé obchodní jednotky. Tímto způsobem, spotřebu prostředků z organizační jednotky nebude započítávat limity pro jiné organizační jednotky. A vzhledem k tomu, že chcete minimalizovat počet virtuálních sítí, měli byste zvážit použití **s jedním odběrem na organizační jednotku, dvě virtuální sítě na skupinu aplikací** vzor, jak vidíte níže.

![Jedno předplatné](./media/virtual-network-vnet-plan-design-arm/figure9.png)

Budete taky muset zadat adresní prostor pro každý virtuální síť. Vzhledem k tomu, že potřebujete centra pro připojení mezi místní data a oblasti, adresní prostor, použít pro virtuální sítě Azure nelze kolidovat s místní sítí a adresní prostor používá každý virtuální sítě nesmí kolidovat s jiné existující virtuální sítě. Splňovat tyto požadavky můžete použít adresní prostory v následující tabulce.  

| **Předplatné** | **VNet** | **Oblast Azure** | **Adresní prostor** |
| --- | --- | --- | --- |
| BU1 |ProdBU1US1 |Západní USA |172.16.0.0/16 |
| BU1 |ProdBU1US2 |Východ USA |172.17.0.0/16 |
| BU1 |ProdBU1EU1 |Severní Evropa |172.18.0.0/16 |
| BU1 |ProdBU1EU2 |Západní Evropa |172.19.0.0/16 |
| BU1 |TestDevBU1 |Západní USA |172.20.0.0/16 |
| BU2 |TestDevBU2 |Západní USA |172.21.0.0/16 |
| BU2 |ProdBU2US1 |Západní USA |172.22.0.0/16 |
| BU2 |ProdBU2US2 |Východ USA |172.23.0.0/16 |
| BU2 |ProdBU2EU1 |Severní Evropa |172.24.0.0/16 |
| BU2 |ProdBU2EU2 |Západní Evropa |172.25.0.0/16 |

**Počet skupin Nsg a podsítí**

Tyto požadavky se týkají skupiny Nsg a podsítě:

* Měli byste minimalizovat objem virtuální sítě a podsítě.
* Každá aplikace je naprosto izolované od sebe navzájem.
* Každá aplikace přístupná zákazníkům přes Internet pomocí protokolu HTTP.
* Každá aplikace přístupná pomocí uživatelé připojení k místní datových centrech pomocí tunelového propojení šifrované.
* Připojení k místní datových centrech měli používat existující zařízení VPN.
* Databáze v každém umístění musí replikovat do jiných umístění Azure jednou denně.

Na základě těchto požadavků, můžete použít jednu podsíť pro aplikační vrstvu a používáním skupin Nsg k filtrování provozu na aplikaci. Tímto způsobem můžete mít pouze 3 podsítě v každé virtuální sítě (front-endu, aplikační vrstvu a datová vrstva) a jedna skupina NSG na aplikaci na podsíť. V takovém případě měli byste zvážit použití **jednu podsíť pro aplikační vrstvu, počet skupin Nsg na aplikaci** vzoru návrhu. Následující obrázek ukazuje použití představující vzoru návrhu **ProdBU1US1** virtuální sítě.

![Jednu podsíť jednu vrstvu, jedna skupina NSG na aplikaci na vrstvu](./media/virtual-network-vnet-plan-design-arm/figure11.png)

Však také musíte vytvořit podsíť navíc pro připojení VPN mezi virtuální sítě a vaše místní datových centrech. A je třeba zadat adresní prostor pro každou podsíť. Následující obrázek znázorňuje ukázkové řešení pro **ProdBU1US1** virtuální sítě. Tento scénář by replikovat pro každý virtuální síť. Každá barva představuje jinou aplikaci.

![Ukázka virtuální sítě](./media/virtual-network-vnet-plan-design-arm/figure10.png)

**Řízení přístupu**

K řízení přístupu se vztahují následující požadavky:

* Firemní sítě skupiny musí mít plnou kontrolu nad konfiguraci virtuální sítě.
* Vývojáři v jednotlivé obchodní jednotky byste měli mít jenom k nasazení virtuálních počítačů do existující podsítí.

Na základě těchto požadavků, můžete přidat uživatele od týmu pro sítě do vestavěné **Přispěvatel sítě** role v každém předplatném; a vytvořit vlastní roli pro vývojáře aplikací v každém předplatném, bude mít práva k přidání virtuálních počítačů do existující podsítí.

## <a name="next-steps"></a>Další postup
* [Nasadit virtuální síť](virtual-networks-create-vnet-arm-template-click.md) závislosti na scénáři.
* Pochopit, jak [vyrovnávat zatížení](../load-balancer/load-balancer-overview.md) virtuální počítače IaaS a [spravovat směrování nad několika oblastmi Azure](../traffic-manager/traffic-manager-overview.md).
* Další informace o [skupiny Nsg a jak naplánovat a navrhnout](virtual-networks-nsg.md) řešení s NSG.
* Další informace o vaší [mezi různými místy a možnosti připojení virtuální sítě](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti).
