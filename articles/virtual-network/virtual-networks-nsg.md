---
title: Skupiny zabezpečení sítě v Azure | Dokumentace Microsoftu
description: Zjistěte, jak izolovat a řídit tok provozu ve virtuálních sítích pomocí distribuované brány firewall v Azure používající skupiny zabezpečení sítě.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: tysonn
ms.assetid: 20e850fc-6456-4b5f-9a3f-a8379b052bc9
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/11/2016
ms.author: jdial
ms.openlocfilehash: bd15b7786552d21c8791eeb307aa8c87066b2bcd
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="filter-network-traffic-with-network-security-groups"></a>Filtrování provozu sítě s použitím skupin zabezpečení sítě

Skupina zabezpečení sítě (NSG) obsahuje seznam pravidel zabezpečení, která prostředkům připojeným k virtuálním sítím Azure povolují nebo odpírají síťový provoz. Skupiny zabezpečení sítě můžou být přidružené k podsítím, jednotlivým virtuálním počítačům (klasický model) nebo jednotlivým síťovým rozhraním (síťovým kartám) připojeným k virtuálním počítačům (Resource Manager). Pokud je skupina zabezpečení sítě přidružená k podsíti, pravidla se vztahují na všechny prostředky, které jsou připojené k příslušné podsíti. Provoz se dá dále omezit přidružením skupiny zabezpečení sítě k virtuálnímu počítači nebo síťové kartě.
 
> [!NOTE]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Resource Manager a klasický model](../resource-manager-deployment-model.md). Tento článek popisuje použití obou modelů, ale Microsoft doporučuje, aby většina nových nasazení používala model Resource Manager.

## <a name="nsg-resource"></a>Prostředek NSG
Skupiny zabezpečení sítě obsahují následující vlastnosti:

| Vlastnost | Popis | Omezení | Požadavky |
| --- | --- | --- | --- |
| Název |Název skupiny NSG |Musí být v rámci oblasti jedinečný.<br/>Může obsahovat písmena, číslice, podtržítka, tečky a pomlčky.<br/>Musí začínat písmenem nebo číslicí.<br/>Musí končit písmenem, číslicí nebo podtržítkem.<br/>Nesmí být delší než 80 znaků. |Pravděpodobně budete vytvářet víc skupin zabezpečení sítě, a proto je vhodné používat takové názvy, které umožní snadno rozpoznat funkci jednotlivých skupin. |
| Oblast |[Oblast](https://azure.microsoft.com/regions) Azure, ve které je skupina zabezpečení sítě hostovaná. |Skupiny zabezpečení sítě lze přidružit pouze k prostředkům v rámci stejné oblasti jako příslušná skupina zabezpečení sítě. |Další informace o tom, kolik skupin zabezpečení sítě můžete mít na každou oblast, najdete v článku o [omezeních Azure](../azure-subscription-service-limits.md#virtual-networking-limits-classic).|
| Skupina prostředků |[Skupina prostředků](../azure-resource-manager/resource-group-overview.md#resource-groups), ve které existuje příslušná skupina zabezpečení sítě. |Ačkoli skupina zabezpečení sítě existuje ve skupině prostředků, dá se přidružit k prostředkům v libovolné skupině prostředků, pokud příslušný prostředek patří do stejné oblasti Azure jako příslušná skupina zabezpečení sítě. |Skupiny prostředků slouží ke společné správě více prostředků jako jednotky nasazení.<br/>Může být vhodné seskupit skupinu zabezpečení sítě s prostředky, ke kterým je přidružená. |
| Pravidla |Pravidla pro příchozí a odchozí provoz, která definují, jaký provoz je povolený nebo odepřený. | |Přečtěte si část [Pravidla NSG](#Nsg-rules) tohoto článku. |

> [!NOTE]
> Seznamy ACL založené na koncových bodech a skupiny zabezpečení sítě nejsou podporované ve stejné instanci virtuálního počítače. Pokud chcete použít skupinu NSG a už máte seznam ACL pro koncové body, nejdřív tento seznam odeberte. Informace o tom, jak odebrat seznam řízení přístupu (ACL), najdete v článku [Správa seznamů řízení přístupu (ACL) pro koncové body pomocí prostředí PowerShell](virtual-networks-acl-powershell.md).
> 

### <a name="nsg-rules"></a>Pravidla NSG
Pravidla NSG obsahují následující vlastnosti:

| Vlastnost | Popis | Omezení | Požadavky |
| --- | --- | --- | --- |
| **Název** |Název pravidla. |Musí být v rámci oblasti jedinečný.<br/>Může obsahovat písmena, číslice, podtržítka, tečky a pomlčky.<br/>Musí začínat písmenem nebo číslicí.<br/>Musí končit písmenem, číslicí nebo podtržítkem.<br/>Nesmí být delší než 80 znaků. |Ve skupině zabezpečení sítě budete mít pravděpodobně několik pravidel, a proto je vhodné používat takové názvy, které umožní rozpoznat funkci pravidla. |
| **Protokol** |Protokol, kterému musí pravidlo odpovídat. |TCP, UDP nebo * |Použití hodnoty * jako protokolu zahrnuje protokol ICMP (pouze provoz typu East-West), UDP a TCP a může snížit počet pravidel, která budete potřebovat.<br/>Použití hodnoty * ale současně může znamenat příliš široký záběr, a proto se doporučuje ověřit, zda je použití hodnoty * skutečně nutné. |
| **Rozsah zdrojových portů** |Rozsah zdrojových portů, kterému musí pravidlo odpovídat. |Jedno číslo portu od 1 do 65535, rozsah portů (např. 1–65535) nebo * (pro všechny porty). |Zdrojové porty můžou být dočasné. Pokud váš klientský program nepoužívá konkrétní port, ve většině případů použijte hodnotu *.<br/>Snažte se co nejvíc používat rozsahy portů, aby nebylo třeba víc pravidel.<br/>Není možné seskupit s použitím čárky více portů nebo rozsahů portů. |
| **Rozsah cílových portů** |Rozsah cílových portů, kterému musí pravidlo odpovídat. |Jedno číslo portu od 1 do 65535, rozsah portů (např. 1–65535) nebo \* (pro všechny porty). |Snažte se co nejvíc používat rozsahy portů, aby nebylo třeba víc pravidel.<br/>Není možné seskupit s použitím čárky více portů nebo rozsahů portů. |
| **Předpona zdrojové adresy** |Předpona zdrojové adresy nebo značka, které musí pravidlo odpovídat. |Jedna IP adresa (např. 10.10.10.10), podsíť IP (např. 192.168.1.0/24), [výchozí značka](#default-tags) nebo * (pro všechny adresy). |Zvažte použití rozsahů, výchozích značek a hodnoty *, abyste snížili počet pravidel. |
| **Předpona cílové adresy** |Předpona cílové adresy nebo značka, které musí pravidlo odpovídat. | Jedna IP adresa (např. 10.10.10.10), podsíť IP (např. 192.168.1.0/24), [výchozí značka](#default-tags) nebo * (pro všechny adresy). |Zvažte použití rozsahů, výchozích značek a hodnoty *, abyste snížili počet pravidel. |
| **Směr** |Směr provozu, kterému musí pravidlo odpovídat. |Příchozí nebo odchozí. |Pravidla pro příchozí a odchozí provoz se zpracovávají odděleně podle směru. |
| **Priorita** |Pravidla se kontrolují v pořadí podle priority. Jakmile se pravidlo aplikuje, u dalších pravidel se už shoda nekontroluje. | Číslo v rozsahu od 100 do 4096. | Doporučujeme u jednotlivých pravidel zadávat priority v krocích po 100, aby zůstal prostor pro vytváření nových pravidel v budoucnu. |
| **Přístup** |Typ přístupu, který se použije v případě odpovídajícího pravidla. | Povolení nebo odepření. | Pamatujte, že pokud se pro paket nenajde pravidlo povolení, paket se zahodí. |

Skupiny zabezpečení sítě obsahují dvě sady pravidel: pro příchozí provoz a pro odchozí provoz. Priorita pravidla musí být v rámci jednotlivých sad jedinečná. 

![Zpracování pravidel NSG](./media/virtual-network-nsg-overview/figure3.png) 

Na obrázku je znázorněno, jak se zpracovávají pravidla skupiny zabezpečení sítě.

### <a name="default-tags"></a>Výchozí značky
Výchozí značky jsou identifikátory poskytnuté systémem, které slouží k adresování určité kategorie IP adres. Výchozí značky můžete použít u libovolného pravidla ve vlastnostech **předpona zdrojové adresy** a **předpona cílové adresy**. Existují tři výchozí značky, které můžete použít:

* **VirtualNetwork** (Resource Manager) (**VIRTUAL_NETWORK** v případě klasického modelu): Tato značka zahrnuje adresní prostor virtuální sítě (rozsahy CIDR definované v Azure) a všechny připojené místní adresní prostory a připojené virtuální sítě Azure (místní sítě).
* **AzureLoadBalancer** (Resource Manager) (**AZURE_LOADBALANCER** v případě klasického modelu): Tato značka označuje nástroj pro vyrovnávání zatížení infrastruktury Azure. Značka se přeloží na IP adresu datacentra Azure, kde mají původ sondy stavu služby Azure Load Balancer.
* **Internet** (Resource Manager) (**INTERNET** v případě klasického modelu): Tato značka označuje adresní prostor IP adres, který se nachází mimo virtuální síť a je dostupný prostřednictvím veřejného internetu. Rozsah zahrnuje [veřejný prostor IP adres vlastněný Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="default-rules"></a>Výchozí pravidla
Všechny skupiny NSG obsahují sadu výchozích pravidel. Výchozí pravidla se nedají odstranit, ale protože je jim přiřazená nejnižší priorita, dají se přepsat pravidly, která vytvoříte. 

Výchozí pravidla povolují a zakazují provoz následujícím způsobem:
- **Virtuální síť:** Provoz směřující z virtuální sítě a do ní je povolený v příchozím i odchozím směru.
- **Internet:** Je povolen odchozí provoz, ale příchozí provoz je blokován.
- **Nástroj pro vyrovnávání zatížení:** Umožňuje službě Azure Load Balancer testovat stav virtuálních počítačů a instancí rolí. Pokud toto pravidlo přepíšete, sondy stavu služby Azure Load Balancer selžou, což může mít vliv na vaši službu.

**Příchozí výchozí pravidla**

| Název | Priorita | Zdrojová IP adresa | Zdrojový port | Cílová IP adresa | Cílový port | Protocol (Protokol) | Access |
| --- | --- | --- | --- | --- | --- | --- | --- |
| AllowVNetInBound |65000 | VirtualNetwork | * | VirtualNetwork | * | * | Povolit |
| AllowAzureLoadBalancerInBound | 65001 | AzureLoadBalancer | * | * | * | * | Povolit |
| DenyAllInBound |65500 | * | * | * | * | * | Odepřít |

**Odchozí výchozí pravidla**

| Název | Priorita | Zdrojová IP adresa | Zdrojový port | Cílová IP adresa | Cílový port | Protocol (Protokol) | Access |
| --- | --- | --- | --- | --- | --- | --- | --- |
| AllowVnetOutBound | 65000 | VirtualNetwork | * | VirtualNetwork | * | * | Povolit |
| AllowVnetOutBound | 65001 | * | * | Internet | * | * | Povolit |
| DenyAllOutBound | 65500 | * | * | * | * | * | Odepřít |

## <a name="associating-nsgs"></a>Přidružení skupin NSG
Skupinu zabezpečení sítě můžete přidružit k virtuálním počítačům, síťovým kartám a podsítím, a to v závislosti na modelu nasazení, který používáte, následujícím způsobem:

* **Virtuální počítač (pouze klasický model):** Pravidla zabezpečení se aplikují na veškerý příchozí/odchozí provoz virtuálního počítače. 
* **Síťová karta (pouze Resource Manager):** Pravidla zabezpečení se aplikují na veškerý příchozí/odchozí provoz síťové karty, ke které je skupina zabezpečení sítě přidružena. V případě virtuálního počítače s více síťovými kartami můžete pro každou síťovou kartu samostatně použít jinou skupinu zabezpečení sítě (nebo tutéž). 
* **Podsíť (Resource Manager a Classic):** Pravidla zabezpečení se aplikují na veškerý příchozí/odchozí provoz prostředků připojených k příslušné podsíti.

K virtuálnímu počítači (nebo síťové kartě, podle modelu nasazení) a podsíti, ke které je síťová karta nebo virtuální počítač vázán, můžete přidružit různé skupiny zabezpečení sítě. Na provoz se aplikují pravidla zabezpečení, a to podle priority v jednotlivých skupinách zabezpečení sítě v následujícím pořadí:

- **Příchozí provoz**

  1. **Skupina zabezpečení sítě použitá na podsíť:** Pokud má skupina zabezpečení sítě pro podsíť odpovídající pravidlo pro odepření provozu, paket se zahodí.

  2. **Skupina zabezpečení sítě použitá na síťovou kartu** (Resource Manager) nebo virtuální počítač (klasický model): Pokud má skupina zabezpečení sítě VM\NIC odpovídající pravidlo pro odepření provozu, pakety se ve skupině VM\NIC zahodí, i když má skupina zabezpečení sítě pro podsíť odpovídající pravidlo pro povolení provozu.

- **Odchozí provoz**

  1. **Skupina zabezpečení sítě použitá na síťovou kartu** (Resource Manager) nebo virtuální počítač (klasický model): Pokud má skupina zabezpečení sítě VM\NIC odpovídající pravidlo pro odepření provozu, pakety se zahodí.

  2. **Skupina zabezpečení sítě použitá na podsíť:** Pokud má skupina zabezpečení sítě pro podsíť odpovídající pravidlo pro odepření provozu, pakety se zahodí, i když má skupina zabezpečení sítě VM/NIC odpovídající pravidlo pro povolení provozu.

> [!NOTE]
> Přestože k podsíti, virtuálnímu počítači nebo síťové kartě se dá přidružit jenom jedna skupina NSG, tutéž skupinu NSG můžete přidružit k libovolnému počtu prostředků.
>

## <a name="implementation"></a>Implementace
Skupiny zabezpečení sítě můžete implementovat v modelu nasazení Resource Manager nebo v klasickém modelu nasazení pomocí následujících nástrojů:

| Nástroj pro nasazení | Classic | Resource Manager |
| --- | --- | --- |
| Azure Portal   | Ne | [Ano](virtual-networks-create-nsg-arm-pportal.md) |
| PowerShell     | [Ano](virtual-networks-create-nsg-classic-ps.md) | [Ano](virtual-networks-create-nsg-arm-ps.md) |
| Rozhraní příkazového řádku Azure CLI **V1**   | [Ano](virtual-networks-create-nsg-classic-cli.md) | [Ano](virtual-networks-create-nsg-arm-cli.md) |
| Rozhraní příkazového řádku Azure CLI **V2**   | Ne | [Ano](virtual-networks-create-nsg-arm-cli.md) |
| Šablona Azure Resource Manageru   | Ne  | [Ano](virtual-networks-create-nsg-arm-template.md) |

## <a name="planning"></a>Plánování
Před implementací skupin NSG je nutné odpovědět na tyto otázky:

1. Pro jaké typy prostředků chcete filtrovat příchozí nebo odchozí provoz? Můžete připojovat prostředky, jako jsou síťové karty (Resource Manager), virtuální počítače (klasický model), cloudové služby, prostředí aplikačních služeb a služba VM Scale Sets. 
2. Jsou prostředky, u kterých chcete filtrovat příchozí/odchozí provoz, připojené k podsítím v existujících virtuálních sítích?

Další informace o plánování zabezpečení sítě v Azure najdete v článku [Zabezpečení sítí a cloudových služeb](../best-practices-network-security.md). 

## <a name="design-considerations"></a>Na co dát pozor při navrhování
Pokud znáte odpovědi na otázky uvedené v části [Plánování](#Planning), projděte si následující části předtím, než budete definovat skupiny zabezpečení sítě:

### <a name="limits"></a>Omezení
Existují omezení pro počet skupin zabezpečení sítě, které můžete mít obsaženy v předplatném, a počet pravidel na jednu skupinu zabezpečení sítě. Další informace o omezeních najdete v článku o [omezeních Azure](../azure-subscription-service-limits.md#networking-limits).

### <a name="vnet-and-subnet-design"></a>Navrhování virtuálních sítí a podsítí
Vzhledem k tomu, že skupiny NSG se dají použít na podsítě, můžete minimalizovat počet skupin NSG tím, že svoje prostředky seskupíte podle podsítí a použijete skupiny NSG na podsítě.  Pokud se rozhodnete použít skupiny NSG na podsítě, možná zjistíte, že vaše stávající virtuální sítě a podsítě byly definované bez zohlednění skupin NSG. Může být potřebné definovat nové virtuální sítě a podsítě, které by podporovaly váš návrh skupin zabezpečení sítě, a nasadit nové prostředky do nových podsítí. Potom můžete definovat strategii migrace k přesunutí stávajících prostředků do nových podsítí. 

### <a name="special-rules"></a>Zvláštní pravidla
Pokud zablokujete provoz povolený následujícími pravidly, infrastruktura nemůže komunikovat se základními službami Azure:

* **Virtuální IP adresa uzlu hostitele:** Základní služby infrastruktury, například DHCP, DNS a sledování stavu, jsou poskytované prostřednictvím virtualizované IP adresy hostitele 168.63.129.16. Tato veřejná IP adresa patří společnosti Microsoft a je to jediná virtualizovaná IP adresa používaná pro tento účel ve všech oblastech. Tato IP adresa se mapuje na fyzickou IP adresu počítače serveru (uzlu hostitele) hostujícího virtuální počítač. Uzel hostitele funguje jako přenos DHCP, rekurzivní překladač DNS a zdroj testů pro test stavu a zdroj testu pro test stavu nástroje pro vyrovnávání zatížení a test stavu počítače. Komunikace směřující na tuto IP adresu nepředstavuje útok.
* **Licencování (Služba správy klíčů):** Image systému Windows spuštěné ve virtuálních počítačích musí být licencované. Aby se zajistilo licencování, odesílají se žádosti o licenci na hostitelské servery Služby správy klíčů, které takové dotazy zpracovávají. Požadavek odchází přes port 1688.

### <a name="icmp-traffic"></a>Provoz protokolu ICMP
Aktuální pravidla NSG povolují pouze protokoly *TCP* nebo *UDP*. Pro *ICMP* neexistuje žádná konkrétní značka. Provoz protokolu ICMP v rámci virtuální sítě je však povolen výchozím pravidlem AllowVNetInBound, které povoluje příchozí a odchozí provoz pro jakýkoli port a protokol v rámci virtuální sítě.

### <a name="subnets"></a>Podsítě
* Zvažte, jaký počet úrovní vaše úlohy vyžadují. Každá úroveň se dá izolovat pomocí podsítě, přičemž skupina NSG se použije na podsíť. 
* Pokud potřebujete implementovat podsíť pro bránu VPN nebo okruh ExpressRoute, **nepoužívejte** pro tuto podsíť skupinu zabezpečení sítě. Pokud byste tak učinili, může dojít k selhání připojení mezi virtuálními sítěmi nebo mezi více místy. 
* Pokud potřebujete implementovat virtuální síťové zařízení (NVA), připojte toto zařízení k jeho vlastní podsíti a vytvořte trasy definované uživatelem (UDR) do tohoto zařízení a z něj. Můžete implementovat skupinu NSG na úrovni podsítě a filtrovat provoz do/z této podsítě. Další informace o trasách definovaných uživatelem najdete v článku [Trasy definované uživatelem](virtual-networks-udr-overview.md).

### <a name="load-balancers"></a>Nástroje pro vyrovnávání zatížení
* Zvažte pravidla pro vyrovnávání zatížení a překlad síťových adres (NAT) pro každý nástroj pro vyrovnávání zatížení používaný jednotlivými úlohami. Pravidla pro překlad síťových adres jsou vázána na fond back-end, který obsahuje síťové karty (Resource Manager) nebo instance rolí virtuálních počítačů či cloudových služeb (klasický model). Zvažte, jestli není vhodné vytvořit skupinu zabezpečení sítě pro každý fond back-end a povolit pouze provoz mapovaný prostřednictvím pravidel implementovaných v nástrojích pro vyrovnávání zatížení. Vytvoření skupiny zabezpečení sítě pro každý fond back-end zaručuje, že se bude filtrovat i provoz, který přichází do fondu back-end přímo (nikoli přes nástroj pro vyrovnávání zatížení).
* V nasazení Classic vytvoříte koncové body, které mapují porty nástroje pro vyrovnávání zatížení do portů virtuálních počítačů nebo instancí rolí. Můžete taky vytvořit vlastní jednotlivý veřejně přístupný nástroj pro vyrovnávání zatížení prostřednictvím Resource Manageru. Cílový port pro příchozí provoz je skutečný port ve virtuálním počítači nebo instanci role, a nikoli port zpřístupněný nástrojem pro vyrovnávání zatížení. Zdrojovým portem a adresou pro připojení k virtuálnímu počítači jsou port a adresa ve vzdáleném počítači v internetu, a nikoli port a adresa zpřístupněné nástrojem pro vyrovnávání zatížení.
* Když vytváříte skupiny zabezpečení sítě pro filtrování provozu procházejícího službou Azure Load Balancer, použitý zdrojový port a rozsah adres odpovídá zdrojovému počítači, a nikoli front-endu nástroje pro vyrovnávání zatížení. Cílový port a rozsah adres odpovídá cílovému počítači, a nikoli front-endu nástroje pro vyrovnávání zatížení.
* Pokud zablokujete značku AzureLoadBalancer, sondy stavu ze služby Azure Load Balancer selžou, což může mít vliv na vaši službu.

### <a name="other"></a>Ostatní
* Seznamy řízení přístupu (ACL) a skupiny zabezpečení sítě založené na koncových bodech nejsou podporované ve stejné instanci virtuálního počítače. Pokud chcete použít skupinu NSG a už máte seznam ACL pro koncové body, nejdřív tento seznam odeberte. Informace o tom, jak odebrat seznam řízení přístupu (ACL) koncového bodu, najdete v článku [Správa seznamů ACL pro koncové body](virtual-networks-acl-powershell.md).
* V případě Resource Manageru můžete u virtuálních počítačů s více síťovými kartami použít skupinu zabezpečení sítě přidruženou k síťové kartě k povolení správy (vzdáleného přístupu) pro jednotlivé síťové karty. Přidružení jedinečných skupin zabezpečení sítě k jednotlivým síťovým kartám umožňuje oddělit mezi síťovými kartami různé typy provozu.
* Podobně, jako tomu je při použití nástrojů pro vyrovnávání zatížení, když filtrujete provoz z jiných virtuálních sítí, je nutné použít zdrojový rozsah adres vzdáleného počítače, nikoli brány připojující tyto virtuální sítě.
* Mnoho služeb Azure není možné připojit k virtuálním sítím. Pokud prostředek Azure není připojený k virtuální síti, nemůžete použít skupinu zabezpečení sítě k filtrování provozu do příslušného prostředku.  Pokud potřebujete zjistit, jestli vámi používané služby jde připojit k virtuální síti, přečtěte si dokumentaci k těmto službám.

## <a name="sample-deployment"></a>Ukázka nasazení
Pro ilustraci použití informací obsažených v tomto článku zvolíme běžný scénář dvouvrstvé aplikace znázorněný na následujícím obrázku:

![Skupiny NSG](./media/virtual-network-nsg-overview/figure1.png)

Jak je vidět v diagramu, virtuální počítače *Web1* a *Web2* jsou připojené k podsíti *FrontEnd* a virtuální počítače *DB1* a *DB2* jsou připojené k podsíti *BackEnd*.  Obě podsítě jsou součástí virtuální sítě *TestVNet*. Jednotlivé komponenty aplikace jsou spuštěny v rámci virtuálního počítače Azure připojeného k virtuální síti. Scénář má následující požadavky:

1. Oddělení provozu mezi servery WEB a DB.
2. Pravidla vyrovnávání zatížení předávají provoz z nástroje pro vyrovnávání zatížení do všech webových serverů na portu 80.
3. Pravidla pro překlad síťových adres nástroje pro vyrovnávání zatížení předávají provoz přicházející do nástroje pro vyrovnávání zatížení na portu 50001 na port 3389 virtuálního počítače WEB1.
4. Z internetu není přístup k virtuálním počítačům v části Front End a Back End, s výjimkou požadavků číslo 2 a 3.
5. Ze serverů WEB a DB není k dispozici odchozí přístup k internetu.
6. Z podsítě FrontEnd je povolen přístup na port 3389 pro každý server WEB.
7. Z podsítě FrontEnd je povolen přístup na port 3389 pro každý server DB.
8. Z podsítě FrontEnd je povolen přístup na port 1433 pro každý server DB.
9. Oddělení provozu správy (port 3389) a provozu databáze (1433) na různých síťových kartách na serverech DB.

Požadavky 1 až 6 (s výjimkou požadavků 3 a 4) jsou omezeny na prostory podsítě. Následující skupiny zabezpečení sítě splňují předchozí požadavky, a současně minimalizují počet požadovaných skupin zabezpečení sítě:

### <a name="frontend"></a>FrontEnd
**Pravidla pro příchozí provoz**

| Pravidlo | Access | Priorita | Zdrojový rozsah adres | Zdrojový port | Cílový rozsah adres | Cílový port | Protocol (Protokol) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Allow-Inbound-HTTP-Internet | Povolit | 100 | Internet | * | * | 80 | TCP |
| Allow-Inbound-RDP-Internet | Povolit | 200 | Internet | * | * | 3389 | TCP |
| Deny-Inbound-All | Odepřít | 300 | Internet | * | * | * | TCP |

**Pravidla pro odchozí provoz**

| Pravidlo | Access | Priorita | Zdrojový rozsah adres | Zdrojový port | Cílový rozsah adres | Cílový port | Protocol (Protokol) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Deny-Internet-All |Odepřít |100 | * | * | Internet | * | * |

### <a name="backend"></a>BackEnd
**Pravidla pro příchozí provoz**

| Pravidlo | Access | Priorita | Zdrojový rozsah adres | Zdrojový port | Cílový rozsah adres | Cílový port | Protocol (Protokol) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Deny-Internet-All | Odepřít | 100 | Internet | * | * | * | * |

**Pravidla pro odchozí provoz**

| Pravidlo | Access | Priorita | Zdrojový rozsah adres | Zdrojový port | Cílový rozsah adres | Cílový port | Protocol (Protokol) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Deny-Internet-All | Odepřít | 100 | * | * | Internet | * | * |

Následující skupiny zabezpečení sítě jsou vytvořeny a přidruženy k síťovým kartám v následujících virtuálních počítačích:

### <a name="web1"></a>WEB1
**Pravidla pro příchozí provoz**

| Pravidlo | Access | Priorita | Zdrojový rozsah adres | Zdrojový port | Cílový rozsah adres | Cílový port | Protocol (Protokol) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Allow-Inbound-RDP-Internet | Povolit | 100 | Internet | * | * | 3389 | TCP |
| Allow-Inbound-HTTP-Internet | Povolit | 200 | Internet | * | * | 80 | TCP |

> [!NOTE]
> Zdrojový rozsah adres pro předchozí pravidla je **Internet**, a nikoli virtuální IP adresa nástroje pro vyrovnávání zatížení. Zdrojový port je *, a nikoli 500001. Pravidla pro překlad síťových adres nástroje pro vyrovnávání zatížení nejsou stejná jako pravidla zabezpečení pro skupinu zabezpečení sítě. Pravidla zabezpečení pro skupinu zabezpečení sítě se vždy vztahují na původní zdroj a konečný cíl provozu, a **nikoli** na nástroj pro vyrovnávání zatížení mezi nimi. Azure Load Balancer vždy zachovává zdrojovou IP adresu a zdrojový port.
> 
> 

### <a name="web2"></a>WEB2
**Pravidla pro příchozí provoz**

| Pravidlo | Access | Priorita | Zdrojový rozsah adres | Zdrojový port | Cílový rozsah adres | Cílový port | Protocol (Protokol) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Deny-Inbound-RDP-Internet | Odepřít | 100 | Internet | * | * | 3389 | TCP |
| Allow-Inbound-HTTP-Internet | Povolit | 200 | Internet | * | * | 80 | TCP |

### <a name="db-servers-management-nic"></a>Servery DB (síťová karta pro správu)
**Pravidla pro příchozí provoz**

| Pravidlo | Access | Priorita | Zdrojový rozsah adres | Zdrojový port | Cílový rozsah adres | Cílový port | Protocol (Protokol) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Allow-Inbound-RDP-Front-end | Povolit | 100 | 192.168.1.0/24 | * | * | 3389 | TCP |

### <a name="db-servers-database-traffic-nic"></a>Servery DB (síťová karta pro provoz databáze)
**Pravidla pro příchozí provoz**

| Pravidlo | Access | Priorita | Zdrojový rozsah adres | Zdrojový port | Cílový rozsah adres | Cílový port | Protocol (Protokol) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Allow-Inbound-SQL-Front-end | Povolit | 100 | 192.168.1.0/24 | * | * | 1433 | TCP |

Vzhledem k tomu, že některé skupiny zabezpečení sítě jsou přidružené k jednotlivým síťovým kartám, jsou pravidla pro prostředky nasazena prostřednictvím Resource Manageru. Pravidla pro podsíť a síťovou kartu se kombinují podle toho, jak jsou přidružena. 

## <a name="next-steps"></a>Další kroky
* [Nasazení skupin zabezpečení sítě (Resource Manager)](virtual-networks-create-nsg-arm-pportal.md).
* [Nasazení skupin zabezpečení sítě (klasický model)](virtual-networks-create-nsg-classic-ps.md).
* [Správa protokolů NSG](virtual-network-nsg-manage-log.md).
* [Řešení potíží týkajících se skupin zabezpečení sítě] (virtual-network-nsg-troubleshoot-portal.md)
