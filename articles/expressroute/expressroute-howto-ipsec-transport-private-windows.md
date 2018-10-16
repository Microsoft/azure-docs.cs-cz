---
title: Konfigurovat režim přenosu protokolu IPsec pro hostitele Windows - privátního partnerského vztahu Azure ExpressRoute | Dokumentace Microsoftu
description: Jak povolit režim přenosu protokolu IPsec mezi virtuálními počítači Windows Azure a místní hostitele Windows prostřednictvím ExpressRoute soukromého partnerského vztahu pomocí objektů zásad skupiny a organizační jednotky.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: cherylmc
ms.openlocfilehash: 4ac0753c3f663aad6a1393e633ff81abb89579ef
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49346693"
---
# <a name="configure-ipsec-transport-mode-for-expressroute-private-peering"></a>Konfigurovat režim přenosu protokolu IPsec pro soukromý partnerský vztah ExpressRoute

Tento článek vám pomůže vytvořit tunely IPsec v režimu přenosu přes ExpressRoute privátní partnerský vztah mezi virtuálními počítači Azure s Windows a místní hostitele Windows. Kroky v tomto článku vytvořit tuto konfiguraci pomocí objektů zásad skupiny. I když je možné k vytvoření této konfigurace bez použití organizačních jednotek (OU) a seskupit objekty zásad (GPO), kombinací organizačních jednotek a objektů zásad skupiny pomůže zjednodušit práci ovládacího prvku zásad zabezpečení a umožňuje rychle škálovat nahoru. Kroky v tomto článku předpokládají, že už máte konfiguraci služby Active Directory a že máte zkušenosti s použitím organizačních jednotek a objektů zásad skupiny.

## <a name="about-this-configuration"></a>O této konfiguraci

Konfigurace v následujících krocích pomocí privátního partnerského vztahu ExpressRoute jednu virtuální síť Azure (VNet). Nicméně tato konfigurace může zahrnovat více virtuálními sítěmi Azure a místními sítěmi. Tento článek vám pomůže definovat zásady šifrování protokolem IPsec a použijte ji pro skupinu virtuálních počítačů Azure a hostitelé v místním, které jsou součástí stejné organizační jednotce. Konfigurace šifrování mezi virtuálními počítači Azure (vm1 a vm2) a místní host1 pouze pro přenosy pomocí protokolu HTTP s cílový port 8080. Různé typy je možné vytvořit zásady protokolu IPSec na základě vašich požadavků.

### <a name="working-with-ous"></a>Práce s organizační jednotky 

Zásady zabezpečení přidružené k organizační jednotce se vloží do počítačů pomocí objektu zásad skupiny. Má několik výhod pomocí organizační jednotky, místo používání zásad pro jednoho hostitele, jsou:

* Přidružení zásady s organizační Jednotkou zaručuje, že počítače, které patří do stejné organizační jednotce získat stejné zásady.
* Změna zásad zabezpečení přidružené k organizační jednotce se projeví změny na všechny hostitele v organizační jednotce.

### <a name="diagrams"></a>Diagramy

Následující diagram znázorňuje propojení a přiřazeného adresního prostoru IP adres. Virtuální počítače Azure a místního hostitele se systémem Windows 2016. Virtuální počítače Azure a místní host1 jsou součástí stejné domény. Virtuální počítače Azure a místní hostitele může překládat názvy správně pomocí DNS.

[![1]][1]

Tento diagram znázorňuje tunelových propojení IPsec na cestě v privátního partnerského vztahu ExpressRoute.

[![4]][4]

### <a name="working-with-ipsec-policy"></a>Práce s zásad systému IPsec

Ve Windows je šifrování přidružené k zásadám protokolu IPsec. Zásady protokolu IPsec Určuje, jaký provoz IP je zabezpečená a mechanismus zabezpečení, který se použije IP paketech.
**Zásady protokolu IPSec** se skládá z následujících položek: **seznamy filtrů**, **filtr akce**, a **pravidla zabezpečení**.

Při konfiguraci zásad protokolu IPsec, je důležité, abyste rozuměli technologiím následující zásady IPsec:

* **Zásady protokolu IPsec:** kolekce pravidel. Pouze jedna zásada může být aktivní ("přiřazeno") v určitém čase. Každá zásada může mít jedno nebo více pravidel, které může být aktivní současně. Počítač je možné přiřadit pouze jeden aktivní zásady protokolu IPsec v zadaný čas. V rámci zásad protokolu IPsec, ale můžete definovat více akcí, které mohou být přijata v různých situacích. Každá sada pravidel IPsec je přidružený seznam filtrů, které má vliv na typ síťového provozu, na které se pravidlo vztahuje.

* **Filtrovat seznamy:** seznamy filtrů jsou sady jeden nebo více filtrů. Jeden seznam může obsahovat více filtrů. Filtr definuje, jestli komunikace je povolený, zabezpečené nebo blokovaný, podle toho, rozsahy adres IP, protokoly nebo dokonce určitý protokol portů. Každý filtr hledá shodu ve určitou sadu podmínek. například pakety odeslané z konkrétní podsítě pro konkrétní počítač, na konkrétní cílový port. Když stav sítě odpovídají nejméně jeden z těchto filtrů, se aktivuje filtr seznamu. Každý filtr je definována uvnitř konkrétní filtr seznamu. Filtry se nedají sdílet mezi seznamy filtrů. Daná filtrovací seznamu však lze začlenit do několik zásad protokolu IPsec. 

* **Akce filtru:** metodu zabezpečení definuje sadu algoritmů zabezpečení, protokoly, a klíče počítače nabízí během vyjednávání protokolu IKE. Filtr akce jsou seznamy metody zabezpečení, seřazeny v pořadí podle priority.  Když počítač vyjedná relaci protokolu IPsec, přijímá nebo odesílá návrhy na základě nastavení zabezpečení, uložená v seznamu filtrů akce.

* **Pravidla zabezpečení:** pravidla určují, jak a kdy zásady protokolu IPsec, chrání komunikaci. Používá **seznam filtrů** a **akce filtru** vytvoří pravidlo IPsec k vytvoření připojení IPsec. Každá zásada může mít jedno nebo více pravidel, které může být aktivní současně. Jednotlivá pravidla obsahují seznam filtrů IP adres a kolekce zabezpečení akcí, které provedou po shodu s seznamu filtru:
  * Akce filtru IP
  * Metody ověřování
  * Nastavení IP adresy tunelového propojení
  * Typy připojení

[![5]][5]

## <a name="before-you-begin"></a>Než začnete

Ujistěte se, že splňujete následující požadavky:

* Musíte mít funkční konfigurace služby Active Directory, který vám pomůže implementovat nastavení zásad skupiny. Další informace o objekty zásad skupiny najdete v tématu [objekty zásad skupiny](https://msdn.microsoft.com/library/windows/desktop/aa374162(v=vs.85).aspx).

* Musí mít aktivní okruh ExpressRoute.
  * Informace o vytvoření okruhu ExpressRoute najdete v tématu [vytvořit okruh ExpressRoute](expressroute-howto-circuit-arm.md). 
  * Povolte je okruh poskytovatelem připojení. 
  * Ověřte, že máte soukromého partnerského vztahu Azure nakonfigurovaný pro váš okruh. Zobrazit [konfigurace směrování](expressroute-howto-routing-arm.md) najdete pokyny pro směrování. 
  * Ověřte, že máte virtuální síť a Brána virtuální sítě vytvořené a plně zřízený. Postupujte podle pokynů a [vytvořit bránu virtuální sítě pro ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Brána virtuální sítě pro ExpressRoute používá GatewayType "ExpressRoute", ne VPN.

* Brána virtuální sítě ExpressRoute, musíte být připojeni k okruhu ExpressRoute. Další informace najdete v tématu [připojení virtuální sítě k okruhu ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Ověřte, že jsou nasazené virtuální počítače Windows Azure k virtuální síti.

* Ověřte, zda existuje připojení mezi místní hostitele a virtuální počítače Azure.

* Ověřte, že se virtuální počítače Windows Azure a místní hostitele pomocí služby DNS správně překlad názvů.

### <a name="workflow"></a>Pracovní postup

1. Vytvořit objekt zásad skupiny a přidružíte ho s organizační Jednotkou.
2. Definice protokolu IPsec **akce filtru**.
3. Definice protokolu IPsec **vyfiltrujete**.
4. Vytvoření zásad IPsec s **pravidla zabezpečení**.
5. Přiřadíte organizační jednotky IPsec objektu zásad skupiny.

### <a name="example-values"></a>Příklady hodnot

* **Název domény:** ipsectest.com

* **Organizační jednotky:** IPSecOU

* **V místním počítači Windows:** host1

* **Virtuální počítače s Windows Azure:** vm1, vm2

## <a name="creategpo"></a>1. Vytvořit objekt zásad skupiny

1. Pokud chcete vytvořit nový objekt zásad skupiny propojené s organizační Jednotkou, otevřete modul snap-in Správa zásad skupiny a vyhledejte organizační jednotku, na který se propojí se objekt zásad skupiny. V tomto příkladu má název organizační jednotky **IPSecOU**. 

  [![9]][9]
2. V modulu snap-in Správa zásad skupiny vyberte organizační jednotku a klikněte pravým tlačítkem myši. V rozevíracím seznamu, klikněte na tlačítko "**vytvořit objekt zásad skupiny v této doméně a propojit jej sem …** ".

  [![10]][10]
3. Název objektu zásad skupiny intuitivní název tak, aby ji mohli později snadno najít. Klikněte na tlačítko **OK** k vytvoření a propojení objektu zásad skupiny.

  [![11]][11]

## <a name="enablelink"></a>2. Povolí odkaz na objekt zásad skupiny

Chcete-li použít objekt zásad skupiny pro organizační jednotku, nesmí být objekt zásad skupiny propojené jenom s organizační jednotku, ale odkaz musí být také povolena.

1. Vyhledejte objekt zásad skupiny, kterou jste vytvořili, klikněte pravým tlačítkem a vyberte **upravit** z rozevíracího seznamu.
2. Chcete-li použít objekt zásad skupiny pro organizační jednotku, vyberte **propojení povoleno**.

  [![12]][12]

## <a name="filteraction"></a>3. Definovat akce filtru IP

1. Z rozevíracího seznamu, klikněte pravým tlačítkem na **zásady zabezpečení protokolu IP v Active Directory**a potom klikněte na tlačítko **Správa IP seznamy filtrů a filtrů akce...** .

  [![15]][15]
2. Na "**spravovat filtr akce**" klikněte na tlačítko **přidat**.

  [![16]][16]

3. Na **akce zabezpečení filtru IP průvodce**, klikněte na tlačítko **Další**.

  [![17]][17]
4. Název filtru akce intuitivní název tak, aby ji mohli později najít. V tomto příkladu je název akce filtru **myEncryption**. Můžete také přidat popis. Pak klikněte na **Další**.

  [![18]][18]
5. **Vyjednávání zabezpečení** umožňuje definovat chování, pokud nejde navázat protokolu IPsec s jiným počítačem. Vyberte **vyjednávání zabezpečení**, pak klikněte na tlačítko **Další**.

  [![19]][19]
6. Na **Communicating u počítačů, které nepodporují protokol IPsec** stránce **Nepovolovat nezabezpečenou komunikaci**, pak klikněte na tlačítko **Další**.

  [![20]][20]
7. Na **přenosy a zabezpečení** stránce **vlastní**, pak klikněte na tlačítko **nastavení...** .

  [![21]][21]
8. Na **vlastní nastavení metody zabezpečení** stránce **integritu dat a šifrování (ESP): SHA1, 3DES**. Potom klikněte na **OK**.

  [![22]][22]
9. Na **spravovat akce filtru** stránky, můžete zobrazit, který **myEncryption** filtru se úspěšně přidal. Klikněte na **Zavřít**.

  [![23]][23]

## <a name="filterlist1"></a>4. Definování seznamu filtru IP

Vytvoření seznamu filtru, který určuje šifrovaný provoz protokolu HTTP s cílový port 8080.

1. K určení, jaké typy provozu musí být zašifrován, použijte **seznam filtrů IP**. V **Správa seznamů filtru IP** klikněte na tlačítko **přidat** přidat nový seznam filtrů IP.

  [![24]][24]
2. V **název:** pole, zadejte název pro váš seznam filtrů IP. Například **azure. místní HTTP8080**. Potom klikněte na **přidat**.

  [![25]][25]
3. Na **vlastnost Popis filtru IP a zrcadlený** stránce **zrcadlený**. Zrcadlené nastavení odpovídá paketů do toho pustit v obou směrech, které umožňuje obousměrnou komunikaci. Pak klikněte na tlačítko **Další**.

  [![26]][26]
4. Na **zdroje přenosů IP** stránky, od **zdrojová adresa:** rozevíracím seznamu zvolte **konkrétní IP adresu nebo podsíť**. 

  [![27]][27]
5. Zadejte zdrojovou adresu **IP adresy nebo podsítě:** IP provozu a pak klikněte na tlačítko **Další**.

  [![28]][28]
6. Zadejte **cílovou adresu:** IP adresy nebo podsítě. Pak klikněte na **Další**.

  [![29]][29]
7. Na **typ protokolu IP** stránce **TCP**. Pak klikněte na **Další**.

  [![30]][30]
8. Na **portu protokolu IP** stránce **z jakéhokoli portu** a **na tento port:**. Typ **8080** v textovém poli. Tato nastavení určují, že budou se šifrovat jenom provozu HTTP na cílový port 8080. Pak klikněte na **Další**.

  [![do 31]][do 31]
9. Zobrazte seznam filtrů IP.  Konfigurace seznamu filtru IP **azure. místní HTTP8080** aktivuje šifrování pro veškeré přenosy, které splňují následující kritéria:

  * Všechny zdrojové adresy v 10.0.1.0/24 (Azure Subnet2)
  * Žádné cílové adresy v 10.2.27.0/25 (místní podsítě)
  * Protokolu TCP
  * Cílový port 8080

  [![32]][32]

## <a name="filterlist2"></a>5. Upravit seznam filtru IP

K šifrování stejný typ provozu v opačném směru (z místního hostitele pro virtuální počítač Azure), budete potřebovat druhý filtr IP. Proces zřízení nového filtru je stejný proces, který jste použili k nastavení prvního filtru IP. Pouze rozdíly jsou podsítě zdrojové a cílové podsíti.

1. Chcete-li přidat nový filtr IP do seznamu filtru IP, **upravit**.

  [![33]][33]
2. Na **seznam filtrů IP** klikněte na **přidat**.

  [![34]][34]
3. Vytvořte druhý filtr IP pomocí nastavení v následujícím příkladu:

  [![35]][35]
4. Po vytvoření druhý filtr IP seznamu filtru IP bude vypadat například takto:

  [![36]][36]

Pokud se vyžaduje mezi místním umístěním a Azure podsíť k ochraně aplikace, místo úprava existující seznam filtrů IP šifrování je můžete přidat nový seznam filtrů IP. Přidružení 2 IP seznamy filtrů do stejných zásad IPsec poskytuje dá větší flexibilitu protože konkrétního seznamu filtru IP můžete změnit ani odebrat kdykoli, aniž by to ovlivnilo ostatní seznamy filtrů IP.

## <a name="ipsecpolicy"></a>6. Vytvoření zásady zabezpečení protokolu IPsec 

Vytvoření zásad IPsec s pravidla zabezpečení.

1. Vyberte **IPSecurity zásady na základě Active directory** přidružený k organizační jednotce. Klikněte pravým tlačítkem a vyberte **vytvořit zásady zabezpečení protokolu IP**.

  [![37]][37]
2. Název zásady zabezpečení. Například **zásad azure místní**. Pak klikněte na **Další**.

  [![38]][38]
3. Klikněte na tlačítko **Další** zaškrtnutí políčka.

  [![39]][39]
4. Ověřte, že **upravit vlastnosti** zaškrtávací políčko zaškrtnuto a potom klikněte na **Dokončit**.

  [![40]][40]

## <a name="editipsec"></a>7. Upravit zásady zabezpečení protokolu IPsec

Přidat do zásad IPsec **seznam filtrů IP** a **filtr akce** , který jste dříve nakonfigurovali.

1. V této zásadě HTTP vlastnosti **pravidla** klikněte na tlačítko **přidat**.

  [![41]][41]
2. Na úvodní stránce klikněte na tlačítko **Další**.

  [![42]][42]
3. Pravidlo obsahuje možnost definovat režim protokolu IPsec: režimu tunelového propojení nebo režim přenosu.

  * V režimu tunelového propojení původní paket se zapouzdří takto sadou záhlaví IP. Režim tunelového propojení chrání interní informace o směrování tím, že šifruje hlavičku protokolu IP z původního paketu. Režim tunelového propojení se implementuje široce mezi bránami ve scénářích VPN typu site-to-site. Režim tunelového propojení je ve většině případů použít pro šifrování začátku do konce mezi hostiteli.

  * Režim přenosu šifruje jenom datové části a ESP; Hlavička protokolu IP z původního paketu nejsou šifrována. V režimu přenosu IP zdroj a cíl IP pakety jsou beze změny.

  Vyberte **toto pravidlo neurčuje tunel**a potom klikněte na tlačítko **Další**.

  [![43]][43]
4. **Typ sítě** definuje, která síťová připojení přidruží se zásadami zabezpečení. Vyberte **všechna síťová připojení**a potom klikněte na tlačítko **Další**.

  [![44]][44]
5. Vyberte seznam filtrů IP, kterou jste vytvořili dříve, **azure. místní HTTP8080**a potom klikněte na tlačítko **Další**.

  [![45]][45]
6. Vyberte existující akci filtru **myEncryption** , kterou jste vytvořili dříve.

  [![46]][46]
7. Windows podporuje čtyři různé typy ověřování: protokolu Kerberos, certifikáty, NTLMv2 a předsdílený klíč. Protože Pracujeme s hostiteli připojených k doméně, vyberte **výchozí služby Active Directory (protokol Kerberos v. 5)** a potom klikněte na tlačítko **Další**.

  [![47]][47]
8. Nová zásada se vytvoří pravidlo zabezpečení: **azure. místní HTTP8080**. Klikněte na **OK**.

  [![48]][48]

Zásady protokolu IPsec vyžaduje všechna připojení protokolu HTTP na cílový port 8080 použít režim přenosu protokolu IPsec. Protože HTTP je protokol prostý text, zásady zabezpečení povolené zajistí, že data se šifrují, když se přenáší prostřednictvím ExpressRoute privátní partnerský vztah. Zásady zabezpečení protokolu IP pro službu Active Directory je složitější konfigurace než Windows Firewall s pokročilým zabezpečením, ale umožní pro větší míru přizpůsobení připojení IPsec.

## <a name="assigngpo"></a>8. Přiřadit organizační jednotky IPsec objektu zásad skupiny

1. Zobrazte zásady. Zásady skupiny zabezpečení je definována, ale ještě nebyly přiřazeny.

  [![49]][49]
2. Přiřazení zásad skupiny zabezpečení s organizační Jednotkou **IPSecOU**, klikněte pravým tlačítkem na zásady zabezpečení a zvolili **přiřadit**.
Každý počítač vazby patří do organizační jednotky, bude mít přiřazené zásady skupiny zabezpečení.

  [![50]][50]

## <a name="checktraffic"></a>Kontrola šifrování přenosů

Abyste šifrování objektu zásad skupiny použito na organizační jednotku, instalace služby IIS na všech virtuálních počítačích Azure a host1. Každý IIS upravit tak, aby odpovědět na požadavky HTTP na portu 8080.
Pokud chcete ověřit šifrování, můžete nainstalovat program sítě (třeba Wireshark) ve všech počítačích v organizační jednotce.
Skript prostředí powershell funguje jako klient HTTP k vygenerování požadavky HTTP na portu 8080:

```powershell
$url = "http://10.0.1.20:8080"
while ($true) {
try {
[net.httpWebRequest]
$req = [net.webRequest]::create($url)
$req.method = "GET"
$req.ContentType = "application/x-www-form-urlencoded"
$req.TimeOut = 60000

$start = get-date
[net.httpWebResponse] $res = $req.getResponse()
$timetaken = ((get-date) - $start).TotalMilliseconds

Write-Output $res.Content
Write-Output ("{0} {1} {2}" -f (get-date), $res.StatusCode.value__, $timetaken)
$req = $null
$res.Close()
$res = $null
} catch [Exception] {
Write-Output ("{0} {1}" -f (get-date), $_.ToString())
}
$req = $null

# uncomment the line below and change the wait time to add a pause between requests
#Start-Sleep -Seconds 1
}

```
Následující snímek sítě ukazuje, že se že zobrazí výsledky pro místní host1 s ESP filtr tak, aby odpovídaly pouze šifrovaný provoz:

[![51]][51]

Při spuštění prostředí powershell skriptu na premisies (klienta HTTP), v zachytávání sítě na virtuálním počítači Azure zobrazí podobné trasování.

## <a name="next-steps"></a>Další postup

Další informace o ExpressRoute najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).

<!--Image References-->

[1]: ./media/expressroute-howto-ipsec-transport-private-windows/network-diagram.png "režim přenosu protokolu IPsec Diagram sítě prostřednictvím ExpressRoute"
[4]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-interesting-traffic.png "zajímavé provoz protokolu IPsec"
[5]: ./media/expressroute-howto-ipsec-transport-private-windows/windows-ipsec.png "zásady protokolu IPsec pro Windows"
[9]: ./media/expressroute-howto-ipsec-transport-private-windows/ou.png "organizační jednotku v zásadách skupiny"
[10]: ./media/expressroute-howto-ipsec-transport-private-windows/create-gpo-ou.png "vytvořit objekt zásad skupiny přidružený k organizační jednotky"
[11]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-name.png "přiřaďte název objektu zásad skupiny přidružený k organizační jednotky"
[12]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-gpo.png "upravit objekt zásad skupiny"
[15]: ./media/expressroute-howto-ipsec-transport-private-windows/manage-ip-filter-list-filter-actions.png "spravovat seznamy filtrů IP a filtrovat akce"
[16]: ./media/expressroute-howto-ipsec-transport-private-windows/add-filter-action.png "přidat filtr akce"
[17]: ./media/expressroute-howto-ipsec-transport-private-windows/action-wizard.png "akce průvodce"
[18]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-name.png "název filtru akce"
[19]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action.png "akce filtru"
[20]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-no-ipsec.png "určit chování navázat nezabezpečené připojení"
[21]: ./media/expressroute-howto-ipsec-transport-private-windows/security-method.png "mechanismus zabezpečení"
[22]: ./media/expressroute-howto-ipsec-transport-private-windows/custom-security-method.png "vlastní zabezpečení – Metoda"
[23]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-actions-list.png "seznam filtrů Akce"
[24]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-ip-filter.png "přidat nový seznam filtrů IP"
[25]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-http-traffic.png "provoz protokolu HTTP přidat do filtru IP"
[26]: ./media/expressroute-howto-ipsec-transport-private-windows/match-both-direction.png "paketu shoda v obou směrech"
[27]: ./media/expressroute-howto-ipsec-transport-private-windows/source-address.png "výběr zdrojová podsíť"
[28]: ./media/expressroute-howto-ipsec-transport-private-windows/source-network.png "zdrojové sítě"
[29]: ./media/expressroute-howto-ipsec-transport-private-windows/destination-network.png "cílové sítě"
[30]: ./media/expressroute-howto-ipsec-transport-private-windows/protocol.png "protokolu"
[do 31]: ./media/expressroute-howto-ipsec-transport-private-windows/source-port-and-destination-port.png "zdrojový port a cílový port"
[32]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list.png "seznam filtrů"
[33]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-for-http.png "seznam filtrů IP s přenosy HTTP"
[34]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-add-second-entry.png "přidává druhý filtr IP"
[35]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-second-entry.png "položka seznamu druhý filtr IP"
[36]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list-2entries.png "položka seznamu druhý filtr IP"
[37]: ./media/expressroute-howto-ipsec-transport-private-windows/create-ip-security-policy.png "vytvořit zásady zabezpečení protokolu IP"
[38]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-policy-name.png "název zásad systému IPsec"
[39]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-wizard.png "Průvodce zásadami protokolu IPsec"
[40]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-security-policy.png "upravit zásady protokolu IPsec"
[41]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-rule.png "přidat nové pravidlo zabezpečení pro zásady protokolu IPsec"
[42]: ./media/expressroute-howto-ipsec-transport-private-windows/create-security-rule.png "vytvořit nové pravidlo zabezpečení"
[43]: ./media/expressroute-howto-ipsec-transport-private-windows/transport-mode.png "režim přenosu"
[44]: ./media/expressroute-howto-ipsec-transport-private-windows/network-type.png "typ sítě"
[45]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-list.png "výběr existujícího seznamu filtru IP"
[46]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-action.png "výběr existující akci filtru"
[47]: ./media/expressroute-howto-ipsec-transport-private-windows/authentication-method.png "výběru metody ověřování"
[48]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-completed.png "konci procesu vytvoření zásad zabezpečení"
[49]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-not-assigned.png "zásady IPsec propojen se objekt zásad skupiny, ale Nepřiřazeno"
[50]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-assigned.png "přiřazené objekt zásad skupiny zásady protokolu IPsec"
[51]: ./media/expressroute-howto-ipsec-transport-private-windows/encrypted-traffic.png "zachycení IPsec šifrovaného přenosu"
