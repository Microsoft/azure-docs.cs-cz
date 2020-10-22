---
title: 'Soukromý partnerský vztah Azure ExpressRoute: konfigurace režimu přenosu IPsec – hostitelé Windows'
description: Jak povolit transportní režim protokolu IPsec mezi virtuálními počítači Azure a místními hostiteli Windows prostřednictvím privátního partnerského vztahu ExpressRoute pomocí objektů zásad skupiny a organizačních jednotek
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 10/17/2018
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 2dcb8489d94b9afc3ae4df829b37dd9785383d85
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92208239"
---
# <a name="configure-ipsec-transport-mode-for-expressroute-private-peering"></a>Konfigurace režimu přenosu IPsec pro privátní partnerské vztahy ExpressRoute

Tento článek vám pomůže vytvořit tunely IPsec v režimu přenosu přes privátní partnerský vztah ExpressRoute mezi virtuálními počítači Azure s Windows a místními hostiteli Windows. Kroky v tomto článku vytvářejí tuto konfiguraci pomocí objektů zásad skupiny. I když je možné vytvořit tuto konfiguraci bez použití organizačních jednotek (OU) a objektů zásad skupiny (GPO), kombinace organizačních jednotek a objektů zásad skupiny vám pomůže zjednodušit řízení zásad zabezpečení a umožní vám rychlé škálování. V krocích v tomto článku se předpokládá, že už máte konfiguraci služby Active Directory a že máte zkušenosti s používáním organizačních jednotek a objektů zásad skupiny.

## <a name="about-this-configuration"></a>O této konfiguraci

Konfigurace v následujících krocích používá jednu virtuální síť Azure s privátním partnerským vztahem ExpressRoute. Tato konfigurace ale může zahrnovat víc virtuální sítě a místních sítí Azure. Tento článek vám pomůže definovat zásady šifrování protokolu IPsec a použít ho pro skupinu virtuálních počítačů Azure a hostitele v místním prostředí, které jsou součástí stejné organizační jednotky. Nakonfigurujete šifrování mezi virtuálními počítači Azure (VM1 a VM2) a místním Hostitel1 jenom pro přenosy HTTP s cílovým portem 8080. Na základě vašich požadavků je možné vytvořit různé typy zásad protokolu IPsec.

### <a name="working-with-ous"></a>Práce s organizačními jednotkami 

Zásady zabezpečení přidružené k organizační jednotce se odesílají do počítačů prostřednictvím objektu zásad skupiny. Několik výhod používání organizačních jednotek místo toho, aby bylo možné použít zásady na jediného hostitele, jsou tyto:

* Přidružení zásady k organizační jednotce zaručuje, že počítače, které patří do stejné organizační jednotky, získají stejné zásady.
* Změna zásad zabezpečení přidružených k organizační jednotce použije změny na všechny hostitele v organizační jednotce.

### <a name="diagrams"></a>Diagram

Následující diagram znázorňuje propojení a přiřazený adresní prostor IP adres. Virtuální počítače Azure a místní hostitel používají systém Windows 2016. Virtuální počítače Azure a místní Hostitel1 jsou součástí stejné domény. Virtuální počítače Azure a místní hostitelé můžou názvy správně přeložit pomocí DNS.

[![1]][1]

Tento diagram zobrazuje tunely IPsec při přenosu v privátním partnerském vztahu ExpressRoute.

[![4]][4]

### <a name="working-with-ipsec-policy"></a>Práce se zásadami IPsec

V systému Windows je k zásadám IPsec přidruženo šifrování. Zásady protokolu IPsec určují, který provoz IP je zabezpečený, a mechanismus zabezpečení, který se použije pro pakety protokolu IP.
**Zásady protokolu IPSec** se skládají z následujících položek: **filtrovat seznamy**, **filtrovat akce**a **pravidla zabezpečení**.

Při konfiguraci zásad protokolu IPsec je důležité pochopit následující terminologii zásad IPsec:

* **Zásada protokolu IPSec:** Kolekce pravidel V jednom okamžiku může být aktivní jenom jedna zásada (přiřazená). Každá zásada může mít jedno nebo více pravidel, z nichž každá může být aktivní současně. V daném okamžiku může být počítači přiřazena pouze jedna aktivní zásada protokolu IPsec. V rámci zásad protokolu IPsec však můžete definovat více akcí, které mohou být provedeny v různých situacích. Každá sada pravidel IPsec je přidružená k seznamu filtrů, který má vliv na typ síťového provozu, na který se pravidlo vztahuje.

* **Seznamy filtru:** Seznamy filtrů jsou sady jednoho nebo více filtrů. Jeden seznam může obsahovat více filtrů. Filtr definuje, jestli je komunikace povolená, zabezpečená nebo blokovaná, podle rozsahů IP adres, protokolů nebo dokonce specifických portů protokolu. Každý filtr odpovídá konkrétní sadě podmínek; například pakety odeslané z konkrétní podsítě do konkrétního počítače na určitém cílovém portu. Pokud se síťové podmínky shodují s jedním nebo více z těchto filtrů, je seznam filtru aktivovaný. Každý filtr je definován v konkrétním seznamu filtrů. Filtry nelze sdílet mezi seznamy filtrů. Daný seznam filtrů však lze začlenit do několika zásad protokolu IPsec. 

* **Akce filtru:** Metoda zabezpečení definuje sadu algoritmů zabezpečení, protokolů a klíče, které počítač nabízí během vyjednávání IKE. Akce filtru jsou seznamy metod zabezpečení, které jsou seřazené v pořadí podle priority.  Když počítač vyjedná relaci protokolu IPsec, přijme nebo pošle návrhy na základě nastavení zabezpečení uloženého v seznamu akcí filtru.

* **Pravidla zabezpečení:** Pravidla určují, jak a kdy zásada IPsec chrání komunikaci. K vytvoření pravidla IPsec pro sestavení připojení IPsec používá **seznam filtrů** a **akce filtru** . Každá zásada může mít jedno nebo více pravidel, z nichž každá může být aktivní současně. Každé pravidlo obsahuje seznam filtrů IP adres a kolekci akcí zabezpečení, které se provádějí při shodě s tímto seznamem filtrů:
  * Akce filtru IP
  * Metody ověřování
  * Nastavení tunelu IP
  * Typy připojení

[![5]][5]

## <a name="before-you-begin"></a>Než začnete

Ujistěte se, že splňujete následující požadavky:

* Musíte mít funkční konfiguraci služby Active Directory, kterou můžete použít k implementaci Zásady skupiny nastavení. Další informace o objektech zásad skupiny najdete v tématu [Zásady skupiny objektů](/previous-versions/windows/desktop/Policy/group-policy-objects).

* Musí mít aktivní okruh ExpressRoute.
  * Informace o vytvoření okruhu ExpressRoute najdete v tématu [vytvoření okruhu ExpressRoute](expressroute-howto-circuit-arm.md). 
  * Ověřte, jestli je okruh povolený vaším poskytovatelem připojení. 
  * Ověřte, že máte pro váš okruh nakonfigurovaný privátní partnerský vztah Azure. Pokyny k směrování najdete v článku věnovaném [konfiguraci směrování](expressroute-howto-routing-arm.md) . 
  * Ověřte, že máte virtuální síť a Brána virtuální sítě se vytvořila a kompletně zřídila. Postupujte podle pokynů a [vytvořte bránu virtuální sítě pro ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Brána virtuální sítě pro ExpressRoute používá GatewayType "ExpressRoute", ne VPN.

* Brána virtuální sítě ExpressRoute musí být připojená k okruhu ExpressRoute. Další informace najdete v tématu [připojení virtuální sítě k okruhu ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Ověřte, že jsou virtuální počítače Azure s Windows nasazené do virtuální sítě.

* Ověřte, jestli existuje připojení mezi místními hostiteli a virtuálními počítači Azure.

* Ověřte, že virtuální počítače Azure a místní hostitelé můžou k správnému překladu názvů použít DNS.

### <a name="workflow"></a>Pracovní postup

1. Vytvořte objekt zásad skupiny a přidružte ho k organizační jednotce.
2. Definujte **akci filtru**protokolu IPSec.
3. Definujte **seznam filtru**IPSec.
4. Vytvořte zásadu IPsec s **pravidly zabezpečení**.
5. Přiřaďte objekt zásad skupiny IPsec k organizační jednotce.

### <a name="example-values"></a>Příklady hodnot

* **Název domény:** ipsectest.com

* **Organizační jednotka:** IPSecOU

* **Místní počítač se systémem Windows:** Hostitel1

* **Virtuální počítače Azure s Windows:** VM1, VM2

## <a name="1-create-a-gpo"></a><a name="creategpo"></a>1. vytvoření objektu zásad skupiny

1. Chcete-li vytvořit nový objekt zásad skupiny propojený s organizační jednotkou, otevřete modul snap-in Správa Zásady skupiny a vyhledejte organizační jednotku, do které bude objekt zásad skupiny propojený. V příkladu má organizační jednotka název **IPSecOU**. 

   [![9]][9]
2. V modulu snap-in Správa Zásady skupiny vyberte organizační jednotku a klikněte pravým tlačítkem myši. V rozevíracím seznamu klikněte na**vytvořit objekt zásad skupiny v této doméně a připojit ho sem**...

   [![10]][10]
3. Pojmenujte objekt zásad skupiny intuitivním názvem, abyste ho později mohli snadno najít. Kliknutím na tlačítko **OK** vytvořte objekt zásad skupiny a propojte ho.

   [![11]][11]

## <a name="2-enable-the-gpo-link"></a><a name="enablelink"></a>2. povolení odkazu na objekt zásad skupiny

Pokud chcete objekt zásad skupiny použít pro organizační jednotku, nesmí být objekt zásad skupiny propojený jenom s organizační jednotkou, musí ale taky být povolený odkaz.

1. Vyhledejte objekt zásad skupiny, který jste vytvořili, klikněte pravým tlačítkem myši a v rozevíracím seznamu vyberte možnost **Upravit** .
2. Pokud chcete objekt zásad skupiny použít pro organizační jednotku, vyberte **odkaz povoleno**.

   [![12]][12]

## <a name="3-define-the-ip-filter-action"></a><a name="filteraction"></a>3. Definujte akci filtru IP.

1. V rozevíracím seznamu klikněte pravým tlačítkem na **zásady zabezpečení protokolu IP v Active Directory**a pak klikněte na **Spravovat seznamy filtrů IP adres a filtrovat akce...**.

   [![15]][15]
2. Na kartě**Spravovat akce filtru**klikněte na **Přidat**.

   [![16]][16]

3. V **Průvodci akcí filtru zabezpečení protokolu IP**klikněte na tlačítko **Další**.

   [![17]][17]
4. Pojmenujte tento filtr intuitivní název, abyste ho mohli najít později. V tomto příkladu má akce filtru název **myEncryption**. Můžete také přidat popis. Pak klikněte na tlačítko **Další**.

   [![18]][18]
5. **Vyjednávání zabezpečení** umožňuje definovat chování, pokud nelze protokol IPSec vytvořit s jiným počítačem. Vyberte **Vyjednat zabezpečení**a pak klikněte na **Další**.

   [![19]][19]
6. Na stránce **komunikace s počítači, které nepodporují protokol IPSec** vyberte možnost **Nepovolit nezabezpečenou komunikaci**a pak klikněte na tlačítko **Další**.

   [![20]][20]
7. Na stránce **provoz a zabezpečení protokolu IP** vyberte **vlastní**a pak klikněte na **nastavení...**.

   [![21]][21]
8. Na stránce **vlastní nastavení metody zabezpečení** vyberte možnost **Integrita dat a šifrování (ESP): SHA1, 3DES**. Pak klikněte na **OK**.

   [![22]][22]
9. Na stránce **Správa akcí filtru** vidíte, že byl filtr **myEncryption** úspěšně přidán. Klikněte na **Zavřít**.

   [![23]][23]

## <a name="4-define-an-ip-filter-list"></a><a name="filterlist1"></a>4. definování seznamu filtrů IP adres

Vytvořte seznam filtrů, který určuje šifrovaný provoz HTTP s cílovým portem 8080.

1. K získání informací o tom, které typy přenosů musí být šifrované, použijte **seznam filtru IP adres**. Na kartě **Spravovat seznamy filtrů IP** klikněte na **Přidat** a přidejte nový seznam filtrů IP.

   [![24]][24]
2. Do pole **Název:** zadejte název vašeho seznamu filtru IP adres. Například **Azure-premises-HTTP8080**. Pak klikněte na tlačítko **Přidat**.

   [![25]][25]
3. Na stránce s **popisem filtru IP a zrcadlenou vlastností** vyberte možnost **Zrcadleno**. Zrcadlení nastavení odpovídá paketům v obou směrech, což umožňuje obousměrnou komunikaci. Potom klikněte na **Další**.

   [![26]][26]
4. Na stránce **Zdroj přenosu IP** adres v rozevíracím seznamu **zdrojová adresa:** vyberte **konkrétní IP adresu nebo podsíť**. 

   [![27]][27]
5. Zadejte **IP adresu nebo podsíť** zdrojové adresy IP a potom klikněte na **Další**.

   [![28]][28]
6. Zadejte **cílovou adresu:** IP adresa nebo podsíť. Pak klikněte na tlačítko **Další**.

   [![29]][29]
7. Na stránce **typ protokolu IP** vyberte **TCP**. Pak klikněte na tlačítko **Další**.

   [![30]][30]
8. Na stránce **port protokolu IP** vyberte **libovolný port** a **na tento port:**. Do textového pole zadejte **8080** . Tato nastavení určují pouze přenosy HTTP na cílovém portu 8080 budou zašifrovány. Pak klikněte na tlačítko **Další**.

   [![31]][31]
9. Zobrazit seznam filtrů IP adres.  Konfigurace seznamu filtru IP adres **Azure-HTTP8080** spustí šifrování pro veškerý provoz, který odpovídá následujícím kritériím:

   * Libovolná zdrojová adresa v 10.0.1.0/24 (Azure podsíť subnet2)
   * Libovolná cílová adresa v 10.2.27.0/25 (místní podsíť)
   * Protokol TCP
   * Cílový port 8080

   [![32]][32]

## <a name="5-edit-the-ip-filter-list"></a><a name="filterlist2"></a>5. Upravte seznam filtru IP adres.

Pokud chcete zašifrovat stejný typ provozu v opačném směru (z místního hostitele na virtuální počítač Azure), budete potřebovat druhý filtr IP adres. Proces nastavení nového filtru je stejný jako proces, který jste použili k nastavení prvního filtru IP adres. Jediným rozdílem je zdrojová podsíť a cílová podsíť.

1. Pokud chcete přidat nový filtr IP adres do seznamu filtru IP adres, vyberte **Upravit**.

   [![33]][33]
2. Na stránce **seznam filtrů IP** klikněte na **Přidat**.

   [![34]][34]
3. Vytvořte druhý filtr IP adres pomocí nastavení v následujícím příkladu:

   [![35]][35]
4. Po vytvoření druhého filtru IP adres bude seznam filtru IP vypadat takto:

   [![36]][36]

Pokud se vyžaduje šifrování mezi místním umístěním a podsítí Azure k ochraně aplikace, místo úprav stávajícího seznamu filtrů IP adres můžete místo toho přidat nový seznam filtrů IP adres. Přidružení dvou seznamů filtrů IP ke stejné zásadě IPsec poskytuje lepší flexibilitu, protože konkrétní seznam filtrů IP se dá kdykoli upravit nebo odebrat, aniž by to ovlivnilo ostatní seznamy filtrů IP adres.

## <a name="6-create-an-ipsec-security-policy"></a><a name="ipsecpolicy"></a>6. vytvoření zásady zabezpečení protokolu IPsec 

Vytvořte zásadu IPsec s pravidly zabezpečení.

1. Vyberte **zásady ipSecurity ve službě Active Directory** , která je přidružená k organizační jednotce. Klikněte pravým tlačítkem a vyberte **vytvořit zásadu zabezpečení protokolu IP**.

   [![37]][37]
2. Pojmenujte zásady zabezpečení. Například **zásada – Azure-místní**. Pak klikněte na tlačítko **Další**.

   [![38]][38]
3. Klikněte na tlačítko **Další** bez zaškrtnutí políčka.

   [![39]][39]
4. Ověřte, zda je zaškrtnuto políčko **Upravit vlastnosti** , a potom klikněte na tlačítko **Dokončit**.

   [![40]][40]

## <a name="7-edit-the-ipsec-security-policy"></a><a name="editipsec"></a>7. upravte zásady zabezpečení IPsec.

Přidejte do zásady protokolu IPsec **seznam filtrů IP** a **akci filtru** , kterou jste předtím nakonfigurovali.

1. Na kartě **pravidla** vlastností zásad protokolu HTTP klikněte na **Přidat**.

   [![41]][41]
2. Na úvodní stránce klikněte na **Další**.

   [![42]][42]
3. Pravidlo nabízí možnost definovat režim protokolu IPsec: režim tunelového propojení nebo Přenosový režim.

   * V režimu tunelového propojení je původní paket zapouzdřený sadou hlaviček protokolu IP. Režim tunelového propojení chrání interní informace o směrování tím, že šifruje hlavičku protokolu IP původního paketu. Režim tunelového propojení je v rámci scénářů sítě Site-to-Site VPN široce implementován mezi bránami. Režim tunelového propojení je ve většině případů, který se používá pro komplexní šifrování mezi hostiteli.

   * Transportní režim šifruje jenom datovou část a přípojné vozidlo ESP; Hlavička protokolu IP původního paketu není šifrovaná. V transportního režimu se IP adresa a cíl IP paketů nemění.

   Vyberte **Toto pravidlo neurčuje tunel**a potom klikněte na tlačítko **Další**.

   [![43]][43]
4. **Typ sítě** definuje, které síťové připojení přidruží k zásadám zabezpečení. Vyberte **všechna síťová připojení**a pak klikněte na **Další**.

   [![44]][44]
5. Vyberte seznam filtrů IP, který jste předtím vytvořili,  **Azure-premises-HTTP8080**, a pak klikněte na **Další**.

   [![45]][45]
6. Vyberte existující akci filtru **myEncryption** , kterou jste vytvořili dříve.

   [![46]][46]
7. Windows podporuje čtyři různé typy ověřování: Kerberos, certifikáty, NTLMv2 a předsdílený klíč. Vzhledem k tomu, že pracujete s hostiteli připojeným k doméně, vyberte možnost **výchozí nastavení služby Active Directory (protokol Kerberos V5)** a poté klikněte na tlačítko **Další**.

   [![47]][47]
8. Nové zásady vytvoří pravidlo zabezpečení: **Azure-premises-HTTP8080**. Klikněte na **OK**.

   [![48]][48]

Zásada IPsec vyžaduje, aby všechna připojení HTTP na cílovém portu 8080 používala režim přenosu IPsec. Vzhledem k tomu, že HTTP je protokol nešifrovaného textu, má povolenou zásadu zabezpečení, která zajišťuje, aby byla data při přenosu prostřednictvím privátního partnerského vztahu ExpressRoute zašifrovaná. Zásady zabezpečení protokolu IP pro službu Active Directory jsou složitější pro konfiguraci než brána Windows Firewall s pokročilým zabezpečením, ale umožňuje další přizpůsobení připojení IPsec.

## <a name="8-assign-the-ipsec-gpo-to-the-ou"></a><a name="assigngpo"></a>8. Přiřaďte objekt zásad skupiny IPsec k organizační jednotce.

1. Podívejte se na zásadu. Zásady skupiny zabezpečení jsou definované, ale ještě nejsou přiřazené.

   [![49]][49]
2. Zásadu skupiny zabezpečení přiřadíte **IPSecOU**organizační jednotce tak, že kliknete pravým tlačítkem na zásadu zabezpečení a zvolíte **přiřadit**.
   Každý počítač THT patří do organizační jednotky, bude mít přiřazené zásady skupiny zabezpečení.

   [![50]][50]

## <a name="check-traffic-encryption"></a><a name="checktraffic"></a>Ověřit šifrování provozu

Pokud chcete zjistit objekt zásad skupiny, který je v organizační jednotce použit, nainstalujte službu IIS na všechny virtuální počítače Azure a v Hostitel1. Každá služba IIS je přizpůsobená tak, aby odpovídala požadavkům HTTP na portu 8080.
Chcete-li ověřit šifrování, můžete na všech počítačích v organizační jednotce nainstalovat síťový Sniffer (například Wireshark).
Powershellový skript funguje jako klient HTTP, který generuje požadavky HTTP na portu 8080:

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
Následující záznam v síti zobrazuje výsledky pro místní Hostitel1 s filtrováním protokolu ESP, aby odpovídal pouze zašifrovanému provozu:

[![51]][51]

Pokud spustíte powershellový skript v místním prostředí (klient HTTP), zachytávání sítě na virtuálním počítači Azure zobrazuje podobné trasování.

## <a name="next-steps"></a>Další kroky

Další informace o ExpressRoute najdete v tématu [ExpressRoute – Nejčastější dotazy](expressroute-faqs.md).

<!--Image References-->

[1]: ./media/expressroute-howto-ipsec-transport-private-windows/network-diagram.png "režim přenosu IPSec v diagramu sítě přes ExpressRoute"
[4]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-interesting-traffic.png "zajímavé přenosy IPSec"
[5]: ./media/expressroute-howto-ipsec-transport-private-windows/windows-ipsec.png "zásady IPSec systému Windows"
[9]: ./media/expressroute-howto-ipsec-transport-private-windows/ou.png "organizační jednotky v Zásady skupiny"
[10]: ./media/expressroute-howto-ipsec-transport-private-windows/create-gpo-ou.png "vytvoření objektu zásad skupiny PŘIDRUŽENÉHO k organizační jednotce"
[11]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-name.png "přiřazení názvu k objektu zásad skupiny PŘIDRUŽENÉMU k organizační jednotce"
[12]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-gpo.png "Úprava objektu zásad skupiny"
[15]: ./media/expressroute-howto-ipsec-transport-private-windows/manage-ip-filter-list-filter-actions.png "Správa seznamů filtrů IP a akcí filtru"
[16]: ./media/expressroute-howto-ipsec-transport-private-windows/add-filter-action.png. "akce přidání filtru"
[17]: ./media/expressroute-howto-ipsec-transport-private-windows/action-wizard.png "Průvodce akcemi" 17
[18]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-name.png "název akce filtru"
[19]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action.png "akce filtru"
[20]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-no-ipsec.png "Určení chování je nezabezpečené připojení navázáno" .
[21]: ./media/expressroute-howto-ipsec-transport-private-windows/security-method.png "mechanismus zabezpečení"
[22]: ./media/expressroute-howto-ipsec-transport-private-windows/custom-security-method.png "vlastní metoda zabezpečení"
[23]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-actions-list.png "seznam akcí filtru"
[24]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-ip-filter.png "Přidání nového seznamu filtru IP adres"
[25]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-http-traffic.png "Přidání provozu http do filtru IP"
[26]: ./media/expressroute-howto-ipsec-transport-private-windows/match-both-direction.png "paketů v obou směrech"
[27]: ./media/expressroute-howto-ipsec-transport-private-windows/source-address.png "výběr zdrojové podsítě"
[28]: ./media/expressroute-howto-ipsec-transport-private-windows/source-network.png "zdrojová síť"
[29]: ./media/expressroute-howto-ipsec-transport-private-windows/destination-network.png "cílových sítí"
[30]: ./media/expressroute-howto-ipsec-transport-private-windows/protocol.png "protokol"
[31]: ./media/expressroute-howto-ipsec-transport-private-windows/source-port-and-destination-port.png "zdrojový port a cílový port"
[32]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list.png "seznam filtru" 32
[33]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-for-http.png "seznam filtrů IP adres s přenosy HTTP"
[34]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-add-second-entry.png "Přidání druhého filtru IP adres"
[35]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-second-entry.png "seznam filtru IP adres 35 – sekundová položka"
[36]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list-2entries.png "seznam filtru IP adres 36 – sekundová položka"
[37]: ./media/expressroute-howto-ipsec-transport-private-windows/create-ip-security-policy.png "Vytvoření zásady zabezpečení protokolu IP"
[38]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-policy-name.png "název zásady protokolu IPSec"
[39]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-wizard.png "Průvodce zásadami protokolu IPSec" 39
[40]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-security-policy.png "úpravy zásady protokolu IPSec"
[41]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-rule.png "Přidání nového pravidla zabezpečení do zásady protokolu IPSec"
[42]: ./media/expressroute-howto-ipsec-transport-private-windows/create-security-rule.png "vytvořit nové pravidlo zabezpečení"
[43]: ./media/expressroute-howto-ipsec-transport-private-windows/transport-mode.png "přenosový režim" 43
[44]: ./media/expressroute-howto-ipsec-transport-private-windows/network-type.png "typ sítě" 44
[45]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-list.png "Výběr existujícího seznamu filtrů IP adres"
[46]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-action.png "Výběr stávající akce filtru"
[47]: ./media/expressroute-howto-ipsec-transport-private-windows/authentication-method.png "Výběr metody ověřování"
[48]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-completed.png "Konec procesu vytváření zásad zabezpečení"
[49]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-not-assigned.png "zásada IPSec propojená s objektem zásad skupiny, ale není přiřazeno"
[50]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-assigned.png "zásada IPSec přiřazená k objektu zásad skupiny"
[51]: ./media/expressroute-howto-ipsec-transport-private-windows/encrypted-traffic.png "zachycení přenosu zašifrovaného protokolem IPSec"