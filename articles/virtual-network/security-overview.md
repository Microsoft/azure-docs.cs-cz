---
title: Přehled skupin zabezpečení sítě Azure
titlesuffix: Azure Virtual Network
description: Seznamte se se skupinami zabezpečení sítě. Skupiny zabezpečení sítě vám pomůžou filtrovat síťový provoz mezi prostředky Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 968cc9ed9d938bb04d1243102855c134147ddf3b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81269869"
---
# <a name="network-security-groups"></a>Skupiny zabezpečení sítě
<a name="network-security-groups"></a>

Skupinu zabezpečení sítě Azure můžete použít k filtrování síťového provozu do a z prostředků Azure ve službě Azure Virtual Network. Skupina zabezpečení sítě obsahuje [pravidla zabezpečení](#security-rules) , která povolují nebo odmítají příchozí síťový provoz nebo odchozí síťový provoz z několika typů prostředků Azure. Pro každé pravidlo můžete určit zdroj a cíl, port a protokol.
Tento článek popisuje vlastnosti pravidla skupiny zabezpečení sítě, použitých [výchozích pravidel zabezpečení](#default-security-rules) a vlastností pravidla, které můžete upravit a vytvořit tak [Rozšířená pravidla zabezpečení](#augmented-security-rules).

## <a name="security-rules"></a><a name="security-rules"></a>Pravidla zabezpečení

Skupina zabezpečení sítě nemusí obsahovat žádná pravidla nebo může podle potřeby obsahovat libovolný počet pravidel v rámci [omezení](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) předplatného Azure. Každé pravidlo určuje následující vlastnosti:

|Vlastnost  |Vysvětlení  |
|---------|---------|
|Název|Jedinečný název v rámci skupiny zabezpečení sítě.|
|Priorita | Číslo v rozsahu od 100 do 4096. Pravidla se zpracovávají v pořadí podle priority, přičemž nižší čísla, která mají vyšší prioritu, se zpracovávají před vyššími čísly. Jakmile provoz odpovídá pravidlu, zpracování se zastaví. V důsledku toho se nezpracují žádná existující pravidla s nižší prioritou (vyšší čísla), která mají stejné atributy jako pravidla s vyšší prioritou.|
|Zdroj nebo cíl| Všechny nebo určitá IP adresa, blok CIDR (například 10.0.0.0/24), [značka služby](service-tags-overview.md) nebo [skupina zabezpečení aplikace](#application-security-groups). Pokud zadáváte adresu prostředku Azure, zadejte privátní IP adresu přiřazenou k tomuto prostředku. Skupiny zabezpečení sítě se zpracovávají poté, co Azure přeloží veřejnou IP adresu na privátní IP adresu pro příchozí provoz, a před tím, než Azure přeloží privátní IP adresu na veřejnou IP adresu pro odchozí provoz. Další informace o [IP adresách](virtual-network-ip-addresses-overview-arm.md) Azure. Zadání rozsahu, značky služby nebo skupiny zabezpečení aplikace umožňuje vytvářet méně pravidel zabezpečení. Možnost zadat několik jednotlivých IP adres a rozsahů (v pravidle nemůžete zadat více značek služeb ani skupin aplikací), které jsou označovány jako [Rozšířená pravidla zabezpečení](#augmented-security-rules). Rozšířená pravidla zabezpečení je možné vytvářet pouze ve skupinách zabezpečení sítě vytvořených prostřednictvím modelu nasazení Resource Manager. Ve skupinách zabezpečení sítě vytvořených prostřednictvím modelu nasazení Classic není možné zadat více IP adres ani rozsahů IP adres. Další informace o [modelech nasazení Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
|Protocol (Protokol)     | TCP, UDP, ICMP nebo Any.|
|Směr| Určuje, jestli se pravidlo vztahuje na příchozí nebo odchozí provoz.|
|Rozsah portů     |Můžete zadat určitý port nebo rozsah portů. Můžete zadat například 80 nebo 10000-10005. Zadání rozsahů umožňuje vytvářet méně pravidel zabezpečení. Rozšířená pravidla zabezpečení je možné vytvářet pouze ve skupinách zabezpečení sítě vytvořených prostřednictvím modelu nasazení Resource Manager. Ve skupinách zabezpečení sítě vytvořených prostřednictvím modelu nasazení Classic není možné zadat několik portů ani rozsahů portů ve stejném pravidlu zabezpečení.   |
|Akce     | Povolení nebo odepření.        |

Pravidla zabezpečení skupin zabezpečení sítě se vyhodnocují podle priority s použitím informací o řazené kolekci 5 členů (zdroj, zdrojový port, cíl, cílový port a protokol) a určují, jestli se má provoz povolit nebo odepřít. Pro stávající připojení se vytvoří záznam toku. Komunikace se povoluje nebo odepírá na základě stavu připojení v záznamu toku. Záznam toku umožňuje, aby skupina zabezpečení sítě byla stavová. Pokud zadáte odchozí pravidlo zabezpečení pro všechny adresy například přes port 80, není potřeba zadávat příchozí pravidlo zabezpečení pro reakci na odchozí provoz. Příchozí pravidlo zabezpečení je potřeba zadat pouze v případě, že se komunikace zahajuje externě. Opačně to platí také. Pokud je přes port povolený příchozí provoz, není potřeba zadávat odchozí pravidlo zabezpečení pro reakci na provoz přes tento port.
Pokud odeberete pravidlo zabezpečení, které povolilo tok, nesmí se přerušit žádné stávající připojení. Toky provozu se přeruší v případě zastavení připojení a toku provozu oběma směry po dobu alespoň několika minut.

Počet pravidel zabezpečení, která můžete ve skupině zabezpečení sítě vytvořit, je omezený. Podrobnosti najdete v tématu věnovaném [omezením Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

### <a name="default-security-rules"></a><a name="default-security-rules"></a>Výchozí pravidla zabezpečení

Azure v každé skupině zabezpečení sítě, kterou vytvoříte, vytvoří následující výchozí pravidla:

#### <a name="inbound"></a>Příchozí

##### <a name="allowvnetinbound"></a>AllowVNetInBound

|Priorita|Zdroj|Zdrojové porty|Cíl|Cílové porty|Protocol (Protokol)|Access|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|Všechny|Povolit|

##### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Priorita|Zdroj|Zdrojové porty|Cíl|Cílové porty|Protocol (Protokol)|Access|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|Všechny|Povolit|

##### <a name="denyallinbound"></a>DenyAllInbound

|Priorita|Zdroj|Zdrojové porty|Cíl|Cílové porty|Protocol (Protokol)|Access|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|Všechny|Odepřít|

#### <a name="outbound"></a>Odchozí

##### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Priorita|Zdroj|Zdrojové porty| Cíl | Cílové porty | Protocol (Protokol) | Access |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | Všechny | Povolit |

##### <a name="allowinternetoutbound"></a>AllowVnetOutBound

|Priorita|Zdroj|Zdrojové porty| Cíl | Cílové porty | Protocol (Protokol) | Access |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | Všechny | Povolit |

##### <a name="denyalloutbound"></a>DenyAllOutBound

|Priorita|Zdroj|Zdrojové porty| Cíl | Cílové porty | Protocol (Protokol) | Access |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | Všechny | Odepřít |

Ve sloupcích **Zdroj** a **Cíl** jsou hodnoty *VirtualNetwork*, *AzureLoadBalancer* a *Internet*[značky služeb](service-tags-overview.md), a nikoli IP adresy. Ve sloupci Protocol zahrnuje **všechny** protokoly TCP, UDP a ICMP. Při vytváření pravidla můžete zadat TCP, UDP, ICMP nebo Any. Hodnota *0.0.0.0/0* ve sloupcích **Zdroj** a **Cíl** představuje všechny adresy. Klienti, jako je Azure Portal, Azure CLI nebo PowerShell, můžou pro tento výraz použít * nebo Any.
 
Výchozí pravidla nemůžete odebrat, ale můžete je přepsat vytvořením pravidel s vyšší prioritou.

### <a name="augmented-security-rules"></a><a name="augmented-security-rules"></a>Rozšířená pravidla zabezpečení

Rozšířená pravidla zabezpečení zjednodušují definici zabezpečení pro virtuální sítě tím, že umožňují definovat větší a složitější zásady zabezpečení sítě při použití menšího počtu pravidel. Můžete zkombinovat více portů a explicitních IP adres a rozsahů do jediného, snadno pochopitelného pravidla zabezpečení. Rozšířená pravidla používejte v polích pravidla pro zdroj, cíl a port. Chcete-li zjednodušit údržbu definice pravidla zabezpečení, zkombinujte Rozšířená pravidla zabezpečení s [značkami služby](service-tags-overview.md) nebo [skupinami zabezpečení aplikací](#application-security-groups). Existují omezení počtu adres, rozsahů a portů, které lze zadat v pravidle. Podrobnosti najdete v tématu věnovaném [omezením Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

#### <a name="service-tags"></a>Značky služeb

Značka služby představuje skupinu předpon IP adres z dané služby Azure. Pomáhá minimalizovat složitost častých aktualizací pravidel zabezpečení sítě.

Další informace najdete v tématu [značky služeb Azure](service-tags-overview.md). Příklad použití značky služby úložiště k omezení přístupu k síti najdete v tématu [omezení síťového přístupu k prostředkům PaaS](tutorial-restrict-network-access-to-resources.md).

#### <a name="application-security-groups"></a>Skupiny zabezpečení aplikací

Skupiny zabezpečení aplikací umožňují konfigurovat zabezpečení sítě jako přirozené rozšíření struktury aplikace. Můžete seskupovat virtuální počítače a na základě těchto skupin definovat zásady zabezpečení sítě. Zásady zabezpečení můžete opakovaně používat ve velkém měřítku bez potřeby ruční údržby explicitních IP adres. Další informace najdete v tématu [skupiny zabezpečení aplikací](application-security-groups.md).

## <a name="how-traffic-is-evaluated"></a>Způsob vyhodnocování provozu

Do virtuální sítě Azure můžete nasadit prostředky z několika služeb Azure. Úplný seznam najdete v tématu popisujícím [služby, které je možné nasadit do virtuální sítě](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Ke každé [podsíti](virtual-network-manage-subnet.md#change-subnet-settings) virtuální sítě a každému [síťovému rozhraní](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group) na virtuálním počítači můžete přiřadit jednu nebo žádnou skupinu zabezpečení sítě. Stejnou skupinu zabezpečení sítě můžete přidružit k libovolnému počtu podsítí a síťových rozhraní.

Následující obrázek ukazuje různé scénáře nasazení skupin zabezpečení sítě pro povolení síťového provozu do a z internetu na portu TCP 80:

![Zpracování NSG](./media/security-groups/nsg-interaction.png)

Předchozí obrázek společně s následujícím textem vám pomůže porozumět způsobu, jakým Azure zpracovává příchozí a odchozí pravidla pro skupiny zabezpečení sítě:

### <a name="inbound-traffic"></a>Příchozí provoz

V případě příchozího provozu zpracuje Azure nejprve pravidla ve skupině zabezpečení sítě přidružené k příslušné podsíti, pokud taková skupina existuje, a pak pravidla ve skupině zabezpečení sítě přidružené k síťovému rozhraní, pokud taková skupina existuje.

- **VM1:** Zpracují se pravidla ve skupině *NSG1*, protože je přidružená k podsíti *Subnet1* a virtuální počítač *VM1* se nachází v podsíti *Subnet1*. Pokud jste nevytvořili pravidlo povolující příchozí provoz na portu 80, výchozí pravidlo zabezpečení [DenyAllInbound](#denyallinbound) provoz odepře a skupina *NSG2* ho nikdy nevyhodnotí, protože skupina *NSG2* je přidružená k síťovému rozhraní. Pokud skupina *NSG1* obsahuje pravidlo zabezpečení povolující port 80, provoz se pak zpracuje skupinou *NSG2*. Pokud pro virtuální počítač chcete povolit příchozí provoz přes port 80, skupina *NSG1* i *NSG2* musí obsahovat pravidlo povolující příchozí provoz přes port 80 z internetu.
- **VM2:** Zpracují se pravidla ve skupině *NSG1*, protože virtuální počítač *VM2* se také nachází v podsíti *Subnet1*. Vzhledem k tomu, že k síťovému rozhraní virtuálního počítače *VM2* není přidružená žádná skupina zabezpečení sítě, přijme toto rozhraní veškerý provoz povolený skupinou *NSG1* nebo se mu odepře veškerý provoz zakázaný skupinou *NSG1*. Pokud je skupina zabezpečení sítě přidružená k podsíti, povolí se nebo se odepře provoz do všech prostředků ve stejné podsíti.
- **VM3:** Vzhledem k tomu, že k podsíti *Subnet2* není přidružená žádná skupina zabezpečení sítě, povolí se příchozí provoz do této podsítě a zpracuje se skupinou *NSG2*, protože skupina *NSG2* je přidružená k síťovému rozhraní připojenému k virtuálnímu počítači *VM3*.
- **VM4:** Provoz do virtuálního počítače *VM4* se povolí, protože k podsíti *Subnet3* ani k síťovému rozhraní na virtuálním počítači není přidružená žádná skupina zabezpečení sítě. Pokud k podsíti a síťovému rozhraní není přidružená žádná skupina zabezpečení sítě, povolí se přes ně průchod veškerého síťového provozu.

### <a name="outbound-traffic"></a>Odchozí provoz

V případě odchozího provozu zpracuje Azure nejprve pravidla ve skupině zabezpečení sítě přidružené k příslušnému síťovému rozhraní, pokud taková skupina existuje, a pak pravidla ve skupině zabezpečení sítě přidružené k podsíti, pokud taková skupina existuje.

- **VM1:** Zpracují se pravidla zabezpečení ve skupině *NSG2*. Pokud nevytvoříte pravidlo zabezpečení zakazující odchozí provoz přes port 80 do internetu, výchozí pravidlo zabezpečení [AllowInternetOutbound](#allowinternetoutbound) ve skupině *NSG1* i *NSG2* provoz povolí. Pokud skupina *NSG2* obsahuje pravidlo zabezpečení zakazující port 80, provoz se odepře a skupina *NSG1* ho nikdy nevyhodnotí. Pokud chcete na virtuálním počítači zakázat odchozí provoz přes port 80, jedna ze skupin zabezpečení sítě nebo obě musí obsahovat pravidlo zakazující odchozí provoz přes port 80 do internetu.
- **VM2:** Veškerý provoz se odešle přes síťové rozhraní do podsítě, protože k síťovému rozhraní připojenému k virtuálnímu počítači *VM2* není přidružená žádná skupina zabezpečení sítě. Zpracují se pravidla ve skupině *NSG1*.
- **VM3:** Pokud skupina *NSG2* obsahuje pravidlo zabezpečení zakazující port 80, provoz se odepře. Pokud skupina *NSG2* obsahuje pravidlo zabezpečení povolující port 80, povolí se odchozí provoz přes port 80 do internetu, protože k podsíti *Subnet2* není přidružená žádná skupina zabezpečení sítě.
- **VM4:** Veškerý odchozí provoz z virtuálního počítače *VM4* se povolí, protože k síťovému rozhraní připojenému k virtuálnímu počítači ani k podsíti *Subnet3* není přidružená žádná skupina zabezpečení sítě.


### <a name="intra-subnet-traffic"></a>Provoz uvnitř podsítě

Je důležité si uvědomit, že pravidla zabezpečení v NSG přidružené k podsíti můžou mít vliv na připojení mezi virtuálním počítačem v rámci tohoto virtuálního počítače. Například pokud je do *NSG1* přidáno pravidlo, které zakazuje veškerý příchozí a odchozí provoz, *VM1* a *VM2* už nebudou moct vzájemně komunikovat. Další pravidlo by se muselo přidat konkrétně, aby to bylo možné. 



Agregovaná pravidla použitá na síťové rozhraní můžete snadno zobrazit v [platných pravidlech zabezpečení](virtual-network-network-interface.md#view-effective-security-rules) pro síťové rozhraní. Pomocí funkce [Ověření toku protokolu IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) v nástroji Azure Network Watcher můžete také určit, jestli je povolená komunikace směřující do síťového rozhraní nebo z něj. Ověření toku protokolu IP vám řekne, jestli je povolená nebo zakázaná komunikace, a které pravidlo zabezpečení sítě povoluje nebo odepírá provoz.

> [!NOTE]
> Skupiny zabezpečení sítě jsou přidruženy k podsítím nebo virtuálním počítačům a cloudovým službám nasazeným v modelu nasazení Classic a k podsítím nebo síťovým rozhraním v modelu nasazení Správce prostředků. Další informace o modelech nasazení Azure najdete v článku [Vysvětlení modelů nasazení Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

> [!TIP]
> Pokud k tomu nemáte konkrétní důvod, doporučujeme přidružit skupinu zabezpečení sítě k podsíti nebo k síťovému rozhraní, ale ne k oběma. Vzhledem k tomu, že může docházet ke konfliktům mezi pravidly ve skupině zabezpečení sítě přidružené k podsíti a pravidly ve skupině zabezpečení sítě přidružené k síťovému rozhraní, můžou nastat neočekávané problémy s komunikací vyžadující řešení.

## <a name="azure-platform-considerations"></a>Důležité informace o platformě Azure

- **Virtuální IP adresa uzlu hostitele**: základní služby infrastruktury, jako jsou DHCP, DNS, IMDS a sledování stavu, se poskytují prostřednictvím virtualizované IP adresy hostitele 168.63.129.16 a 169.254.169.254. Tyto IP adresy patří společnosti Microsoft a jsou jediné virtualizované IP adresy, které se používají ve všech oblastech pro tento účel. Platná pravidla zabezpečení a efektivní trasy nebudou zahrnovat tato pravidla platformy. K přepsání této základní komunikace infrastruktury můžete vytvořit pravidlo zabezpečení pro odepření provozu pomocí následujících [značek služby](service-tags-overview.md) v pravidlech skupiny zabezpečení sítě: AzurePlatformDNS, AzurePlatformIMDS, AzurePlatformLKM. Naučte se [diagnostikovat filtrování síťového provozu](diagnose-network-traffic-filter-problem.md) a [diagnostikovat síťové směrování](diagnose-network-routing-problem.md).
- **Licencování (Služba správy klíčů):** Image Windows spuštěné na virtuálních počítačích musí být licencované. Aby se zajistilo licencování, odesílají se žádosti o licenci na hostitelské servery Služby správy klíčů, které takové dotazy zpracovávají. Požadavek odchází přes port 1688. Pro nasazení využívající konfiguraci [výchozí trasy 0.0.0.0/0](virtual-networks-udr-overview.md#default-route) bude toto pravidlo platformy zakázané.
- **Virtuální počítače ve fondech s vyrovnáváním zatížení:** Použitý zdrojový port a rozsah adres odpovídá zdrojovému počítači, a nikoli nástroji pro vyrovnávání zatížení. Cílový port a rozsah adres odpovídá cílovému počítači, a nikoli nástroji pro vyrovnávání zatížení.
- **Instance služeb Azure:** V podsítích virtuální sítě jsou nasazené instance několika služeb Azure, například HDInsight, prostředí aplikačních služeb a škálovací sady virtuálních počítačů. Úplný seznam služeb, které můžete nasadit do virtuální sítě, najdete v tématu [Virtuální síť pro služby Azure](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Před použitím skupiny zabezpečení sítě na podsíť, ve které je nasazený prostředek, se ujistěte, že znáte požadavky jednotlivých služeb na porty. Pokud odepřete porty, které služba vyžaduje, nebude správně fungovat.
- **Odesílání odchozích e-mailů:** Microsoft doporučuje k odesílání e-mailů ze služby Azure Virtual Machines využívat služby pro přenos přes ověřený protokol SMTP (obvykle připojené přes port TCP 587, ale často i jiný). Služby pro přenos přes protokol SMTP se specializují na reputaci odesílatele, aby se minimalizovala možnost odmítnutí zpráv poskytovateli e-mailu třetích stran. Mezi takové služby pro přenos přes protokol SMTP patří mimo jiné Exchange Online Protection a SendGrid. Používání služeb pro přenos přes protokol SMTP v Azure není nijak omezeno, a to bez ohledu na typ předplatného. 

  Pokud jste své předplatné Azure vytvořili před 15. listopadem 2017, kromě možnosti používat služby pro přenos přes protokol SMTP můžete e-maily odesílat také přímo přes port TCP 25. Pokud jste své předplatné vytvořili po 15. listopadu 2017, možná nebudete moci odesílat e-maily přímo přes port 25. Chování odchozí komunikace přes port 25 závisí na typu vašeho předplatného, a to následujícím způsobem:

     - **Smlouva Enterprise:** Odchozí komunikace přes port 25 je povolená. Odchozí emaily můžete z virtuálních počítačů odesílat přímo externím poskytovatelům e-mailu bez jakýchkoli omezení platformy Azure. 
     - **Průběžné platby:** Odchozí komunikace přes port 25 ze všech prostředků je blokovaná. Pokud potřebujete odesílat e-maily z virtuálního počítače přímo externím poskytovatelům e-mailu (bez použití přenosu přes zabezpečený protokol SMTP), můžete vytvořit žádost o odebrání tohoto omezení. Žádosti se posuzují a schvalují na základě vlastního uvážení Microsoftu a vyhoví se jim pouze po provedení kontrol v souvislosti s možnými podvody. Pokud chcete vytvořit žádost, otevřete případ podpory s typem problému *Technický*, *Možnosti připojení k virtuální síti*, *Nelze odesílat e-maily (SMTP/port 25)*. Do případu podpory zahrňte podrobnosti o tom, proč vaše předplatné potřebuje odesílat e-maily přímo poskytovatelům e-mailu místo používání přenosu přes ověřený protokol SMTP. Pokud má vaše předplatné výjimku, odchozí komunikace přes port 25 jsou schopné pouze virtuální počítače vytvořené po datu udělení výjimky.
     - **MSDN, Azure Pass, Azure v rámci licenčního programu Open License, Azure ve vzdělávání, BizSpark a bezplatná zkušební verze:** Odchozí komunikace přes port 25 ze všech prostředků je blokovaná. Není možné vytvořit žádost o odebrání omezení, protože takovým žádostem se nevyhovuje. Pokud z virtuálního počítače potřebujete odesílat e-maily, musíte k tomu použít službu pro přenos přes protokol SMTP.
     - **Poskytovatel cloudových služeb:** Zákazníci, kteří využívají prostředky Azure prostřednictvím poskytovatele cloudových služeb, si mohou u poskytovatele cloudových služeb vytvořit případ podpory a požádat ho, aby v jejich zastoupení vytvořil případ odblokování, pokud se nedá použít zabezpečený přenos SMTP.

  Pokud vám Azure povolí odesílat e-maily přes port 25, Microsoft nemůže zaručit přijetí příchozích e-mailů z vašeho virtuálního počítače poskytovateli e-mailu. Pokud konkrétní poskytovatel odmítá e-maily z vašeho virtuálního počítače, spolupracujte přímo s daným poskytovatelem a vyřešte případné problémy s doručováním zpráv nebo filtrováním nevyžádané pošty, nebo použijte službu pro přenos přes ověřený protokol SMTP.

## <a name="next-steps"></a>Další kroky

* Další informace o tom, které prostředky Azure je možné nasadit do virtuální sítě a které mají přidružené skupiny zabezpečení sítě, najdete v tématu [Integrace virtuální sítě pro služby Azure](virtual-network-for-azure-services.md) .
* Pokud jste ještě nikdy skupinu zabezpečení sítě nevytvářeli, můžete si projít rychlý [kurz](tutorial-filter-network-traffic.md), ve kterém se seznámíte s jejím vytvořením. 
* Pokud už skupiny zabezpečení sítě znáte a potřebujete je spravovat, přečtěte si téma [Správa skupiny zabezpečení sítě](manage-network-security-group.md). 
* Pokud máte problémy s komunikací a potřebujete řešit potíže se skupinami zabezpečení sítě, přečtěte si téma [Diagnostika potíží s filtrováním síťového provozu virtuálních počítačů](diagnose-network-traffic-filter-problem.md). 
* Naučte se, jak povolit [protokoly toku skupin zabezpečení sítě](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) k analýze síťového provozu do a z prostředků, které mají přidruženou skupinu zabezpečení sítě.
