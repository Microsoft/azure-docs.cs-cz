---
title: "Přehled zabezpečení sítě Azure | Dokumentace Microsoftu"
description: "Seznamte se s možnostmi zabezpečení pro řízení toku síťového provozu mezi prostředky Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: jdial
ms.openlocfilehash: fbf0556cc47bc08a71fcf050b43c2dbbe5d27184
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2018
---
# <a name="network-security"></a>Zabezpečení sítě

Pomocí skupiny zabezpečení sítě můžete omezit síťový provoz směřující do prostředků ve virtuální síti. Skupina zabezpečení sítě obsahuje seznam pravidel zabezpečení, která povolují nebo odepírají příchozí nebo odchozí síťový provoz v závislosti na zdrojové nebo cílové IP adrese, portu a protokolu. 

## <a name="network-security-groups"></a>Skupiny zabezpečení sítě

Každé síťové rozhraní má přidruženou jednu nebo žádnou skupinu zabezpečení sítě. Každé síťové rozhraní existuje v podsíti [virtuální sítě](virtual-networks-overview.md). Podsíť také může mít přidruženou jednu nebo žádnou skupinu zabezpečení sítě. 

Při použití pravidel zabezpečení na podsíť se pravidla zabezpečení použijí na všechny prostředky v této podsíti. Kromě síťových rozhraní můžete mít v podsíti nasazené instance dalších služeb Azure, například HDInsight, škálovací sady virtuálních počítačů a prostředí aplikačních služeb.

V případě přidružení skupiny zabezpečení sítě k síťovému rozhraní i podsíti, ve které se síťové rozhraní nachází, se skupiny zabezpečení sítě použijí následujícím způsobem:

- **Příchozí provoz:** Skupina zabezpečení sítě přidružená k podsíti, ve které se síťové rozhraní nachází, se vyhodnocuje jako první. Veškerý provoz, kterému je povolený průchod přes skupinu zabezpečení sítě přidruženou k podsíti, se pak vyhodnocuje skupinou zabezpečení sítě přidruženou k síťovému rozhraní. Můžete například požadovat příchozí přístup z internetu k virtuálnímu počítači přes port 80. Pokud přidružíte skupinu zabezpečení sítě k síťovému rozhraní i podsíti, ve které se síťové rozhraní nachází, musí tato skupina zabezpečení sítě povolovat port 80. Pokud jste port 80 povolili pouze prostřednictvím skupiny zabezpečení sítě přidružené k podsíti nebo síťovému rozhraní, ve kterém se podsíť nachází, kvůli výchozím pravidlům zabezpečení komunikace selže. Podrobnosti najdete v části [Výchozí pravidla zabezpečení](#default-security-rules). Pokud jste skupinu zabezpečení použili pouze na podsíť nebo síťové rozhraní a pokud obsahovala pravidlo povolující příchozí provoz například přes port 80, komunikace bude úspěšná. 
- **Odchozí provoz:** Skupina zabezpečení sítě přidružená k síťovému rozhraní se vyhodnocuje jako první. Veškerý provoz, kterému je povolený průchod přes skupinu zabezpečení sítě přidruženou k síťovému rozhraní, se pak vyhodnocuje skupinou zabezpečení sítě přidruženou k podsíti.

Ne vždy musíte mít přehled o použití skupin zabezpečení sítě na síťové rozhraní i podsíť. Agregovaná pravidla použitá na síťové rozhraní můžete snadno zobrazit v [platných pravidlech zabezpečení](virtual-network-manage-nsg-arm-portal.md) pro síťové rozhraní. Pomocí funkce [Ověření toku protokolu IP](../network-watcher/network-watcher-check-ip-flow-verify-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) v nástroji Azure Network Watcher můžete také určit, jestli je povolená komunikace směřující do síťového rozhraní nebo z něj. Tento nástroj vám řekne, jestli je povolená komunikace, a které pravidlo zabezpečení sítě povoluje nebo odepírá provoz.
 
> [!NOTE]
> Skupiny zabezpečení sítě se nepřidružují k síťovým rozhraním v modelu nasazení Resource Manager, ale k podsítím nebo virtuálním počítačům a cloudovým službám nasazeným pomocí modelu nasazení Classic. Další informace o modelech nasazení Azure najdete v článku [Vysvětlení modelů nasazení Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Stejnou skupinu zabezpečení sítě můžete použít na libovolný počet jednotlivých síťových rozhraní a podsítí.

## <a name="security-rules"></a>Pravidla zabezpečení

Skupina zabezpečení sítě nemusí obsahovat žádná pravidla nebo může podle potřeby obsahovat libovolný počet pravidel v rámci [omezení](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) předplatného Azure. Každé pravidlo určuje následující vlastnosti:

|Vlastnost  |Vysvětlení  |
|---------|---------|
|Název|Jedinečný název v rámci skupiny zabezpečení sítě.|
|Priorita | Číslo v rozsahu od 100 do 4096. Pravidla se zpracovávají v pořadí podle priority, přičemž nižší čísla, která mají vyšší prioritu, se zpracovávají před vyššími čísly. Jakmile provoz odpovídá pravidlu, zpracování se zastaví. V důsledku toho se nezpracují žádná existující pravidla s nižší prioritou (vyšší čísla), která mají stejné atributy jako pravidla s vyšší prioritou.|
|Zdroj nebo cíl| Všechny nebo určitá IP adresa, blok CIDR (například 10.0.0.0/24), značka služby nebo skupina zabezpečení aplikace. Další informace o [značkách služeb](#service-tags) a [skupinách zabezpečení aplikací](#application-security-groups). Zadání rozsahu, značky služby nebo skupiny zabezpečení aplikace umožňuje vytvářet méně pravidel zabezpečení. Možnost zadat v pravidlu několik jednotlivých IP adres a rozsahů (není možné zadat více značek služeb ani skupin aplikací) se označuje jako rozšířená pravidla zabezpečení. Přečtěte si o [rozšířených pravidlech zabezpečení](#augmented-security-rules) víc. Rozšířená pravidla zabezpečení je možné vytvářet pouze ve skupinách zabezpečení sítě vytvořených prostřednictvím modelu nasazení Resource Manager. Ve skupinách zabezpečení sítě vytvořených prostřednictvím modelu nasazení Classic není možné zadat více IP adres ani rozsahů IP adres.|
|Protocol (Protokol)     | TCP, UDP nebo Všechny, což zahrnuje protokoly TCP, UDP a ICMP. Samotný protokol ICMP nejde zadat, takže pokud potřebujete protokol ICMP, musíte použít možnost Všechny. |
|Směr| Určuje, jestli se pravidlo vztahuje na příchozí nebo odchozí provoz.|
|Rozsah portů     |Můžete zadat určitý port nebo rozsah portů. Můžete zadat například 80 nebo 10000-10005. Zadání rozsahů umožňuje vytvářet méně pravidel zabezpečení. Rozšířená pravidla zabezpečení je možné vytvářet pouze ve skupinách zabezpečení sítě vytvořených prostřednictvím modelu nasazení Resource Manager. Ve skupinách zabezpečení sítě vytvořených prostřednictvím modelu nasazení Classic není možné zadat několik portů ani rozsahů portů ve stejném pravidlu zabezpečení.   |
|Akce     | Povolení nebo odepření.        |

Pravidla zabezpečení jsou stavová. Pokud zadáte odchozí pravidlo zabezpečení pro všechny adresy například přes port 80, není potřeba zadávat příchozí pravidlo zabezpečení pro reakci na odchozí provoz. Příchozí pravidlo zabezpečení je potřeba zadat pouze v případě, že se komunikace zahajuje externě. Opačně to platí také. Pokud je přes port povolený příchozí provoz, není potřeba zadávat odchozí pravidlo zabezpečení pro reakci na provoz přes tento port. Informace o omezeních při vytváření pravidel zabezpečení najdete v článku o [omezeních Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="augmented-security-rules"></a>Rozšířená pravidla zabezpečení

Rozšířená pravidla zjednodušují definici zabezpečení pro virtuální počítače tím, že umožňují definovat větší a složitější zásady zabezpečení sítě při použití menšího počtu pravidel. Můžete zkombinovat více portů a explicitních IP adres, značky služeb a skupiny zabezpečení aplikací do jediného, snadno pochopitelného pravidla zabezpečení. Rozšířená pravidla používejte v polích pravidla pro zdroj, cíl a port. Při vytváření pravidla můžete zadat několik explicitních IP adres, rozsahů CIDR a portů. Pokud chcete zjednodušit údržbu definice pravidla zabezpečení, zkombinujte rozšířená pravidla zabezpečení se značkami služeb nebo skupinami zabezpečení aplikací. 

Informace o omezeních při vytváření rozšířených pravidel zabezpečení najdete v článku o [omezeních Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="default-security-rules"></a>Výchozí pravidla zabezpečení

Pokud k podsíti nebo síťovému rozhraní není přidružena skupina zabezpečení sítě, je povolený veškerý příchozí i odchozí provoz do i z podsítě nebo síťového rozhraní. Jakmile se vytvoří skupina zabezpečení sítě, Azure v rámci této skupiny zabezpečení sítě vytvoří následující výchozí pravidla:

### <a name="inbound"></a>Příchozí

#### <a name="allowvnetinbound"></a>AllowVNetInBound

|Priorita|Zdroj|Zdrojové porty|Cíl|Cílové porty|Protocol (Protokol)|Access|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|Vše|Povolit|

#### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Priorita|Zdroj|Zdrojové porty|Cíl|Cílové porty|Protocol (Protokol)|Access|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|Vše|Povolit|

#### <a name="denyallinbound"></a>DenyAllInbound

|Priorita|Zdroj|Zdrojové porty|Cíl|Cílové porty|Protocol (Protokol)|Access|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|Vše|Odepřít|

### <a name="outbound"></a>Odchozí

#### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Priorita|Zdroj|Zdrojové porty| Cíl | Cílové porty | Protocol (Protokol) | Access |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | Vše | Povolit |

#### <a name="allowinternetoutbound"></a>AllowVnetOutBound

|Priorita|Zdroj|Zdrojové porty| Cíl | Cílové porty | Protocol (Protokol) | Access |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | Vše | Povolit |

#### <a name="denyalloutbound"></a>DenyAllOutBound

|Priorita|Zdroj|Zdrojové porty| Cíl | Cílové porty | Protocol (Protokol) | Access |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | Vše | Odepřít |

Ve sloupcích **Zdroj** a **Cíl** jsou hodnoty *VirtualNetwork*, *AzureLoadBalancer* a *Internet* [značky služeb](#tags), a nikoli IP adresy. Hodnota **Všechny** ve sloupci Protokol zahrnuje protokoly TCP, UDP a ICMP. Při vytváření pravidla můžete zadat protokol TCP, UDP nebo Všechny, ale nemůžete zadat samotný protokol ICMP. Proto pokud vaše pravidlo vyžaduje protokol ICMP, musíte jako protokol vybrat hodnotu *Všechny*. Hodnota *0.0.0.0/0* ve sloupcích **Zdroj** a **Cíl** představuje všechny adresy.
 
Výchozí pravidla nemůžete odebrat, ale můžete je přepsat vytvořením pravidel s vyšší prioritou.

## <a name="service-tags"></a>Značky služeb

 Značka služby představuje skupinu předpon IP adres a tím pomáhá minimalizovat složitost vytváření pravidla zabezpečení. Nemůžete vytvořit vlastní značku služby ani určit, které IP adresy jsou ve značce zahrnuté. Předpony adres zahrnuté ve značce služby spravuje Microsoft, a pokud se adresy změní, automaticky značku služby aktualizuje. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Pro použití v definici pravidla zabezpečení jsou k dispozici následující značky služeb. Jejich názvy se mírně liší mezi [modely nasazení Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

* **VirtualNetwork** (Resource Manager) (**VIRTUAL_NETWORK** v případě klasického modelu): Tato značka zahrnuje adresní prostor virtuální sítě (všechny rozsahy CIDR definované pro virtuální síť), všechny připojené místní adresní prostory a [partnerské](virtual-network-peering-overview.md) virtuální sítě nebo virtuální sítě připojené k [bráně virtuální sítě](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* **AzureLoadBalancer** (Resource Manager) (**AZURE_LOADBALANCER** v případě klasického modelu): Tato značka označuje nástroj pro vyrovnávání zatížení infrastruktury Azure. Značka se přeloží na [IP adresu datacentra Azure](https://www.microsoft.com/download/details.aspx?id=41653), kde mají původ testy stavu Azure. Pokud nepoužíváte nástroj pro vyrovnávání zatížení Azure, můžete toto pravidlo přepsat.
* **Internet** (Resource Manager) (**INTERNET** v případě klasického modelu): Tato značka označuje adresní prostor IP adres, který se nachází mimo virtuální síť a je dostupný prostřednictvím veřejného internetu. Rozsah adres zahrnuje [veřejný adresní prostor IP adres vlastněný Azure](https://www.microsoft.com/download/details.aspx?id=41653).
* **AzureTrafficManager** (pouze Resource Manager): Tato značka označuje adresní prostor IP adres sondy pro Azure Traffic Manager. Další informace o IP adresách sondy pro Traffic Manager najdete v tématu [Azure Traffic Manager – nejčastější dotazy](https://docs.microsoft.com/en-us/azure/traffic-manager/traffic-manager-faqs).
* **Storage** (pouze Resource Manager): Tato značka označuje adresní prostor IP adres pro službu Azure Storage. Pokud jako hodnotu zadáte *Storage*, provoz směřující do úložiště se povolí nebo zakáže. Pokud chcete povolit přístup k úložišti pouze v konkrétní [oblasti](https://azure.microsoft.com/regions), můžete zadat tuto oblast. Pokud například chcete povolit přístup pouze ke službě Azure Storage v oblasti Východní USA, můžete jako značku služby zadat *Storage.EastUS*. Značka představuje službu, ale ne konkrétní instance služby. Značka například představuje službu Azure Storage, ale ne konkrétní účet služby Azure Storage.
* **Sql** (pouze Resource Manager): Tato značka označuje předpony adres služeb Azure SQL Database a Azure SQL Data Warehouse. Pokud jako hodnotu zadáte *SQL*, provoz směřující do SQL se povolí nebo zakáže. Pokud chcete povolit přístup k SQL jenom v konkrétní [oblasti](https://azure.microsoft.com/regions), můžete zadat tuto oblast. Pokud například chcete povolit přístup pouze ke službě Azure SQL Database v oblasti Východní USA, můžete jako značku služby zadat *Storage.EastUS*. Značka představuje službu, ale ne konkrétní instance služby. Značka například představuje službu Azure SQL Database, ale ne konkrétní server nebo databázi SQL.

> [!NOTE]
> Pokud implementujete [koncový bod služby virtuální sítě](virtual-network-service-endpoints-overview.md) pro službu, jako je Azure Storage nebo Azure SQL Database, Azure pro tuto službu přidá trasu k podsíti virtuální sítě. Předpony adres pro trasu jsou stejné předpony adres nebo rozsahy CIDR jako u odpovídající značky služby.

## <a name="application-security-groups"></a>Skupiny zabezpečení aplikací

Skupiny zabezpečení aplikací umožňují konfigurovat zabezpečení sítě jako přirozené rozšíření struktury aplikace. Můžete seskupovat virtuální počítače a na základě těchto skupin definovat zásady zabezpečení sítě. Tato funkce umožňuje opakovaně používat škálované zásady zabezpečení bez potřeby ruční údržby explicitních IP adres. O složitost explicitních IP adres a několika skupin pravidel se stará platforma a vy se tak můžete zaměřit na obchodní logiku.

Skupinu zabezpečení aplikace můžete zadat jako zdroj a cíl v pravidlu zabezpečení. Po definování zásady zabezpečení můžete vytvořit virtuální počítače a přiřadit jejich síťová rozhraní ke skupině zabezpečení aplikace. Zásada se použije na základě členství jednotlivých síťových rozhraní virtuálního počítače ve skupině zabezpečení aplikace. Následující příklad ukazuje, jak můžete použít skupinu zabezpečení aplikace pro všechny webové servery ve vašem předplatném:

1. Vytvořte skupinu zabezpečení aplikace *WebServers*.
2. Vytvořte skupinu zabezpečení sítě *MyNSG*.
3. Ve skupině zabezpečení sítě vytvořte příchozí pravidlo zabezpečení, přičemž jako zdrojovou adresu zadejte značku služby *Internet*, jako cílovou adresu zadejte skupinu zabezpečení aplikace *WebServers* a povolte porty 80 a 443.
4. Nasaďte virtuální počítač s aplikací webového serveru. Určete, že síťové rozhraní ve virtuálním počítači je členem skupiny zabezpečení aplikace *WebServers*. Do virtuálního počítače se pak povolí porty 80 a 443. Tyto porty jsou povolené také do dalších webových serverů, které vytvoříte a učiníte členy skupiny zabezpečení aplikace *WebServers*. 

Pokud jste vytvořili jiná pravidla a jako cíl jste zadali jiné skupiny zabezpečení aplikací, taková pravidla se na webové servery z předchozího příkladu nepoužijí. Pravidla určující skupinu zabezpečení aplikace se použijí pouze na síťová rozhraní, která jsou členy příslušné skupiny zabezpečení aplikace. Skupiny zabezpečení aplikací v kombinaci s rozšířenými pravidly zabezpečení a značkami služeb umožňují vytvářet pro účely správy zabezpečení sítě v rámci předplatného minimální počet skupin zabezpečení sítě.
 
Další informace o omezeních při vytváření skupin zabezpečení aplikací a jejich zadávání v pravidlech zabezpečení najdete v článku o [omezeních Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

Skupiny zabezpečení aplikací jsou dostupné ve verzi Preview. Funkce ve verzi Preview nedosahují stejné úrovně dostupnosti a spolehlivosti jako služby v obecné verzi. Před použitím skupin zabezpečení aplikací je potřeba se nejdřív zaregistrovat k jejich použití dokončením kroků 1–5 v částech tématu [Vytvoření skupiny zabezpečení sítě s použitím skupin zabezpečení aplikací](create-network-security-group-preview.md) věnovaných Azure nebo PowerShellu. Pro skupiny zabezpečení aplikací platí následující omezení:

-   Všechna síťová rozhraní v rámci skupiny zabezpečení aplikace musí existovat ve stejné virtuální síti. Do stejné skupiny zabezpečení aplikace nemůžete přidat síťová rozhraní z různých virtuálních sítí. Virtuální síť, ve které je první síťové rozhraní přiřazené ke skupině zabezpečení aplikace, definuje, ve které virtuální síti musí existovat všechna další přiřazená síťová rozhraní.
- Pokud zadáte skupiny zabezpečení aplikací jako zdroj a cíl v pravidle zabezpečení, síťová rozhraní v obou skupinách zabezpečení aplikací musí existovat ve stejné virtuální síti. Kdyby například skupina ASG1 obsahovala síťová rozhraní z virtuální sítě VNet1 a skupina ASG2 obsahovala síťová rozhraní z virtuální sítě VNet2, nemohli byste v pravidle přiřadit skupinu ASG1 jako zdroj a skupinu ASG2 jako cíl, ale všechna síťová rozhraní by musela existovat ve virtuální síti VNet1.

## <a name="azure-platform-considerations"></a>Důležité informace o platformě Azure

- **Virtuální IP adresa uzlu hostitele:** Základní služby infrastruktury, například DHCP, DNS a sledování stavu, jsou poskytované prostřednictvím virtualizovaných IP adres hostitele 168.63.129.16 a 169.254.169.254. Tyto veřejné IP adresy patří společnosti Microsoft a jsou to jediné virtualizované IP adresy používané pro tento účel ve všech oblastech. Adresy se mapují na fyzickou IP adresu počítače serveru (uzel hostitele) hostujícího virtuální počítač. Uzel hostitele funguje jako přenos DHCP, rekurzivní překladač DNS a zdroj testů pro test stavu a zdroj testu pro test stavu nástroje pro vyrovnávání zatížení a test stavu počítače. Komunikace směřující na tyto IP adresy nepředstavuje útok. Pokud zablokujete provoz směřující na tyto IP adresy nebo z nich, virtuální počítač možná nebude fungovat správně.
- **Licencování (Služba správy klíčů):** Image Windows spuštěné na virtuálních počítačích musí být licencované. Aby se zajistilo licencování, odesílají se žádosti o licenci na hostitelské servery Služby správy klíčů, které takové dotazy zpracovávají. Požadavek odchází přes port 1688. Pro nasazení využívající konfiguraci [výchozí trasy 0.0.0.0/0](virtual-networks-udr-overview.md#default-route) bude toto pravidlo platformy zakázané.
- **Virtuální počítače ve fondech s vyrovnáváním zatížení:** Použitý zdrojový port a rozsah adres odpovídá zdrojovému počítači, a nikoli nástroji pro vyrovnávání zatížení. Cílový port a rozsah adres odpovídá cílovému počítači, a nikoli nástroji pro vyrovnávání zatížení.
- **Instance služeb Azure:** V podsítích virtuální sítě jsou nasazené instance několika služeb Azure, například HDInsight, prostředí aplikačních služeb a škálovací sady virtuálních počítačů. Úplný seznam služeb, které můžete nasadit do virtuální sítě, najdete v tématu [Virtuální síť pro služby Azure](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Před použitím skupiny zabezpečení sítě na podsíť, ve které je nasazený prostředek, se ujistěte, že znáte požadavky jednotlivých služeb na porty. Pokud odepřete porty, které služba vyžaduje, nebude správně fungovat.
- **Odesílání odchozích e-mailů:** Microsoft doporučuje k odesílání e-mailů ze služby Azure Virtual Machines využívat služby pro přenos přes ověřený protokol SMTP (obvykle připojené přes port TCP 587, ale často i jiný). Služby pro přenos přes protokol SMTP se specializují na reputaci odesílatele, aby se minimalizovala možnost odmítnutí zpráv poskytovateli e-mailu třetích stran. Mezi takové služby pro přenos přes protokol SMTP patří mimo jiné Exchange Online Protection a SendGrid. Používání služeb pro přenos přes protokol SMTP v Azure není nijak omezeno, a to bez ohledu na typ předplatného. 

  Pokud jste své předplatné Azure vytvořili před 15. listopadem 2017, kromě možnosti používat služby pro přenos přes protokol SMTP můžete e-maily odesílat také přímo přes port TCP 25. Pokud jste své předplatné vytvořili po 15. listopadu 2017, možná nebudete moci odesílat e-maily přímo přes port 25. Chování odchozí komunikace přes port 25 závisí na typu vašeho předplatného, a to následujícím způsobem:

     - **Smlouva Enterprise:** Odchozí komunikace přes port 25 je povolená. Odchozí emaily můžete z virtuálních počítačů odesílat přímo externím poskytovatelům e-mailu bez jakýchkoli omezení platformy Azure. 
     - **Průběžné platby:** Odchozí komunikace přes port 25 ze všech prostředků je blokovaná. Pokud potřebujete odesílat e-maily z virtuálního počítače přímo externím poskytovatelům e-mailu (bez použití přenosu přes zabezpečený protokol SMTP), můžete vytvořit žádost o odebrání tohoto omezení. Žádosti se posuzují a schvalují na základě vlastního uvážení Microsoftu a vyhoví se jim pouze po provedení kontrol v souvislosti s možnými podvody. Pokud chcete vytvořit žádost, otevřete případ podpory s typem problému *Technický*, *Možnosti připojení k virtuální síti*, *Nelze odesílat e-maily (SMTP/port 25)*. Do případu podpory zahrňte podrobnosti o tom, proč vaše předplatné potřebuje odesílat e-maily přímo poskytovatelům e-mailu místo používání přenosu přes ověřený protokol SMTP. Pokud má vaše předplatné výjimku, odchozí komunikace přes port 25 jsou schopné pouze virtuální počítače vytvořené po datu udělení výjimky.
     - **Poskytovatel cloudových služeb (CSP), MSDN, Azure Pass, Azure v rámci licenčního programu Open License, Azure ve vzdělávání, BizSpark a bezplatná zkušební verze:** Odchozí komunikace přes port 25 ze všech prostředků je blokovaná. Není možné vytvořit žádost o odebrání omezení, protože takovým žádostem se nevyhovuje. Pokud z virtuálního počítače potřebujete odesílat e-maily, musíte k tomu použít službu pro přenos přes protokol SMTP.

  Pokud vám Azure povolí odesílat e-maily přes port 25, Microsoft nemůže zaručit přijetí příchozích e-mailů z vašeho virtuálního počítače poskytovateli e-mailu. Pokud konkrétní poskytovatel odmítá e-maily z vašeho virtuálního počítače, musíte spolupracovat přímo s daným poskytovatelem a vyřešit případné problémy s doručováním zpráv nebo filtrováním nevyžádané pošty, nebo použít službu pro přenos přes ověřený protokol SMTP. 


## <a name="next-steps"></a>Další kroky

* Dokončení kurzu [Vytvoření skupiny zabezpečení sítě](virtual-networks-create-nsg-arm-pportal.md)
* Dokončení kurzu [Vytvoření skupiny zabezpečení sítě s použitím skupin zabezpečení aplikací](create-network-security-group-preview.md)
