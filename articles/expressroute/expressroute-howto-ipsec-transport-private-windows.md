---
title: 'Privátní partnerský vztah Azure ExpressRoute: Konfigurace režimu přenosu Protokolu IPsec – hostitelé systému Windows'
description: Jak povolit režim přenosu IPsec mezi virtuálními počítači Azure windows a místními hostiteli Windows prostřednictvím soukromého partnerského vztahu ExpressRoute pomocí objektů zásad skupiny a ouslužeb.
services: expressroute
author: fabferri
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: fabferri
ms.custom: seodec18
ms.openlocfilehash: 1bc33047d31262af443cddc418853fbacd88aec1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022009"
---
# <a name="configure-ipsec-transport-mode-for-expressroute-private-peering"></a>Konfigurace režimu přenosu protokolu IPsec pro soukromý partnerský vztah ExpressRoute

Tento článek vám pomůže vytvořit tunely IPsec v režimu přenosu přes expressroute privátní partnerský vztah mezi virtuálními počítači Azure se systémem Windows a místními hostiteli Windows. Kroky v tomto článku vytvořit tuto konfiguraci pomocí objektů zásad skupiny. I když je možné vytvořit tuto konfiguraci bez použití organizačníjednotky (OU) a objekty zásad skupiny (GPO), kombinace organizačních jednotek a objektů zásad skupiny pomůže zjednodušit řízení zásad zabezpečení a umožňuje rychle vertikálně navýšit kapacitu. Kroky v tomto článku předpokládají, že již máte konfiguraci služby Active Directory a že jste obeznámeni s použitím ounu a objektů zásad skupiny.

## <a name="about-this-configuration"></a>O této konfiguraci

Konfigurace v následujících krocích používá jednu virtuální síť Azure (VNet) s privátním partnerského vztahu ExpressRoute. Tato konfigurace však může span více virtuálních sítí Azure a místních sítí. Tento článek vám pomůže definovat zásady šifrování IPsec a použít je na skupinu virtuálních počítačích Azure a místních hostitelů, které jsou součástí stejné jednotky. Šifrování mezi virtuálními počítači Azure (vm1 a vm2) a místním hostitelem1 nakonfigurujete pouze pro přenosy HTTP s cílovým portem 8080. Různé typy zásad IPsec lze vytvořit na základě vašich požadavků.

### <a name="working-with-ous"></a>Práce s vněmi s ous 

Zásady zabezpečení přidružené k ou ce jsou do počítačů zasínuty prostřednictvím objektu zásad skupiny. Několik výhod použití vou, spíše než použití zásad pro jednoho hostitele, jsou:

* Připodonořování zásad y s ou zaručuje, že počítače, které patří do stejné ou, získají stejné zásady.
* Změna zásad zabezpečení přidružených k hlavní výužné ale souzásadě bude změny uplatňovat u všech hostitelů v hlavní výužce.

### <a name="diagrams"></a>Diagramy

Následující diagram znázorňuje propojení a přiřazený adresní prostor IP. Virtuální počítače Azure a místní hostitel používají Windows 2016. Virtuální počítače Azure a místní hostitel1 jsou součástí stejné domény. Virtuální počítače Azure a místní hostitelé můžou správně přeložit názvy pomocí DNS.

[![1]][1 1 1 1.]

Tento diagram znázorňuje tunely IPsec při přenosu v soukromém partnerském vztahu ExpressRoute.

[![4]][4.]

### <a name="working-with-ipsec-policy"></a>Práce se zásadami IPsec

V systému Windows je šifrování přidruženo k zásadám IPsec. Zásady protokolu IPsec určují, který přenos IP je zabezpečen a mechanismus zabezpečení použitý pro pakety IP.
**Zásady protokolu IPSec** se skládají z následujících položek: **Seznamy filtrů**, **Akce filtrů**a **Pravidla zabezpečení**.

Při konfiguraci zásad iPsec je důležité porozumět následující terminologii zásad IPsec:

* **Zásady iPsec:** Kolekce pravidel. V určitém okamžiku může být aktivní pouze jedna zásada ("přiřazena"). Každá zásada může mít jedno nebo více pravidel, z nichž všechny mohou být aktivní současně. Počítači lze v daném okamžiku přiřadit pouze jednu aktivní zásadu IPsec. V rámci zásad iPsec však můžete definovat více akcí, které mohou být přijata v různých situacích. Každá sada pravidel IPsec je přidružena k seznamu filtrů, který ovlivňuje typ síťového provozu, na který se pravidlo vztahuje.

* **Seznamy filtrů:** Seznamy filtrů jsou součástí jednoho nebo více filtrů. Jeden seznam může obsahovat více filtrů. Filtr definuje, zda je komunikace povolena, zabezpečena nebo blokována podle rozsahů IP adres, protokolů nebo dokonce specifických portů protokolu. Každý filtr odpovídá určité sadě podmínek; například pakety odeslané z určité podsítě do určitého počítače na určitém cílovém portu. Pokud se podmínky sítě shodují s jedním nebo více z těchto filtrů, aktivuje se seznam filtrů. Každý filtr je definován uvnitř určitého seznamu filtrů. Filtry nelze sdílet mezi seznamy filtrů. Daný seznam filtrů však může být začleněn do několika zásad IPsec. 

* **Akce filtru:** Metoda zabezpečení definuje sadu algoritmů zabezpečení, protokolů a klíče, které počítač nabízí během vyjednávání protokolu IKE. Akce filtru jsou seznamy metod zabezpečení seřazených podle priority.  Pokud počítač vyjedná relaci protokolu IPsec, přijme nebo odešle návrhy na základě nastavení zabezpečení uloženého v seznamu akcí filtru.

* **Bezpečnostní pravidla:** Pravidla upravují, jak a kdy zásady IPsec chrání komunikaci. Používá **seznam filtrů** a **akce filtru** k vytvoření pravidla IPsec k vytvoření připojení IPsec. Každá zásada může mít jedno nebo více pravidel, z nichž všechny mohou být aktivní současně. Každé pravidlo obsahuje seznam filtrů IP a kolekci akcí zabezpečení, které se uskuteční při shodě s tímto seznamem filtrů:
  * Akce filtru IP
  * Metody ověřování
  * Nastavení tunelového propojení IP
  * Typy připojení

[![5]][5 5.]

## <a name="before-you-begin"></a>Než začnete

Ujistěte se, že splňujete následující požadavky:

* Musíte mít funkční konfiguraci služby Active Directory, kterou lze použít k implementaci nastavení zásad skupiny. Další informace o objektech zásad skupiny naleznete v [tématu Objekty zásad skupiny](https://msdn.microsoft.com/library/windows/desktop/aa374162(v=vs.85).aspx).

* Musí mít aktivní okruh ExpressRoute.
  * Informace o vytvoření okruhu ExpressRoute naleznete v [tématu Vytvoření okruhu ExpressRoute](expressroute-howto-circuit-arm.md). 
  * Ověřte, zda je okruh povolen poskytovatelem připojení. 
  * Ověřte, že máte pro okruh nakonfigurovaný privátní partnerský vztah Azure. Pokyny pro směrování naleznete v článku [konfigurace směrování.](expressroute-howto-routing-arm.md) 
  * Ověřte, že máte vytvořenou a plně zřízené virtuální sítě a bránu virtuální sítě. Podle pokynů [vytvořte bránu virtuální sítě pro ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Brána virtuální sítě pro ExpressRoute používá GatewayType 'ExpressRoute', ne VPN.

* Brána virtuální sítě ExpressRoute musí být připojena k okruhu ExpressRoute. Další informace najdete [v tématu Připojení virtuální sítě k okruhu ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Ověřte, že virtuální počítače Azure windows jsou nasazené do virtuální sítě.

* Ověřte, že existuje připojení mezi místními hostiteli a virtuálními počítači Azure.

* Ověřte, že virtuální počítače Azure windows a místní hostitelé jsou schopni používat DNS správně přeložit názvy.

### <a name="workflow"></a>Pracovní postup

1. Vytvořte soubor gpo a přidružte jej k hlavní výmětonové.
2. Definujte akci **filtru**IPsec .
3. Definujte seznam **filtrů**IPsec .
4. Vytvořte zásadu protokolu IPsec s **pravidly zabezpečení**.
5. Přiřaďte příkaz gpo IPsec k řídicí mu.

### <a name="example-values"></a>Příklady hodnot

* **Název domény:** ipsectest.com

* **OU:** IpSecOU

* **Místní počítač se systémem Windows:** host1

* **Virtuální počítače Azure pro Windows:** vm1, vm2

## <a name="1-create-a-gpo"></a><a name="creategpo"></a>1. Vytvoření gpo

1. Chcete-li vytvořit nový objekt zásad skupiny propojený s ouovou skupinou, otevřete modul snap-in Správa zásad skupiny a vyhledejte objekt, se kterým bude objekt zásad skupiny propojen. V příkladu se ou s názvem **IPSecOU**. 

   [![9]][9 9]
2. V modulu snap-in Správa zásad skupiny vyberte hlavní výchop a klikněte pravým tlačítkem myši. V rozevíracím článku klikněte na tlačítko **"Vytvořit v této doméně příkaz GPO" a propojte jej zde...**".

   [![10]][10]
3. Pojmenujte objekt gpo intuitivním názvem, abyste jej později mohli snadno najít. Chcete-li vytvořit a propojit soubor gpo, klepněte na tlačítko **OK.**

   [![11]][11]

## <a name="2-enable-the-gpo-link"></a><a name="enablelink"></a>2. Povolení propojení gpo

Chcete-li použít gpo pro ou, musí být odkaz na ou, ale také povolen.

1. Vyhledejte vytvořený soubor gpo, klikněte pravým tlačítkem myši a v rozevíracím souboru vyberte **Upravit.**
2. Chcete-li na oupoložku použít příkaz gpo, vyberte **možnost Propojení povoleno**.

   [![12]][12]

## <a name="3-define-the-ip-filter-action"></a><a name="filteraction"></a>3. Definujte akci filtru IP

1. V rozevíracím seznamu klepněte pravým tlačítkem myši **na zásady zabezpečení protokolu IP ve službě Active Directory**a potom klepněte na příkaz Spravovat **seznamy filtrů IP a akce filtrů...**.

   [![15]][15.]
2. Na kartě**Spravovat akce filtru**klikněte na **Přidat**.

   [![16]][16.]

3. V **průvodci akcí filtru zabezpečení protokolu IP**klepněte na tlačítko **Další**.

   [![17]][17.]
4. Pojmenujte akci filtru intuitivním názvem, abyste ji později našli. V tomto příkladu je akce filtru **pojmenována myEncryption**. Můžete také přidat popis. Potom klepněte na tlačítko **Další**.

   [![18]][18]
5. **Vyjednat zabezpečení** umožňuje definovat chování, pokud nelze vytvořit iPsec s jiným počítačem. Vyberte **Možnost Vyjednat zabezpečení**a klepněte na tlačítko **Další**.

   [![19]][19.]
6. Na stránce **Komunikace s počítači, které nepodporují aplikaci IPsec,** vyberte **možnost Nepovolit nezabezpečenou komunikaci**a klepněte na tlačítko **Další**.

   [![20]][20.]
7. Na stránce **Provoz a zabezpečení protokolu IP** vyberte **Vlastní**a klikněte na **Nastavení...**.

   [![21]][21.]
8. Na stránce **Vlastní nastavení metody zabezpečení** vyberte **Integrita a šifrování dat (ESP): SHA1, 3DES**. Potom klepněte na tlačítko **OK**.

   [![22]][22]
9. Na stránce **Spravovat akce filtru** uvidíte, že filtr **myEncryption** byl úspěšně přidán. Klikněte na **Zavřít**.

   [![23]][23]

## <a name="4-define-an-ip-filter-list"></a><a name="filterlist1"></a>4. Definování seznamu filtrů IP

Vytvořte seznam filtrů, který určuje šifrovaný provoz HTTP s cílovým portem 8080.

1. Chcete-li zařadit typy přenosů, které musí být šifrovány, použijte **seznam filtrů IP**. Na kartě **Spravovat seznamy filtrů IP** klikněte na **Přidat** a přidejte nový seznam filtrů IP.

   [![24]][24]
2. Do pole **Název:** zadejte název seznamu filtrů IP. Například **azure-onpremises-HTTP8080**. Potom klepněte na tlačítko **Přidat**.

   [![25]][25]
3. Na stránce **Popis filtru IP a Zrcadlené** vyberte **Zrcadlené**. Zrcadlené nastavení odpovídá paketům v obou směrech, což umožňuje obousměrnou komunikaci. Pak klikněte na **Další**.

   [![26]][26.]
4. Na stránce **Zdroj přenosu IP** z rozevíracího přehledu **Zdrojová adresa zvolte** **Určitá adresa IP nebo Podsíť**. 

   [![27]][27.]
5. Zadejte adresu IP zdrojové adresy **nebo podsíť:** přenosy IP a klepněte na tlačítko **Další**.

   [![28]][28]
6. Zadejte **cílovou adresu:** IP adresu nebo podsíť. Potom klepněte na tlačítko **Další**.

   [![29]][29.]
7. Na stránce **Typ protokolu IP** vyberte možnost **TCP**. Potom klepněte na tlačítko **Další**.

   [![30]][30]
8. Na stránce **Port protokolu IP** vyberte z **libovolného portu** a **do tohoto portu:**. Do textového pole zadejte **8080.** Tato nastavení určují, že bude šifrován pouze provoz HTTP na cílovém portu 8080. Potom klepněte na tlačítko **Další**.

   [![31]][31]
9. Zobrazení seznamu filtrů IP.  Konfigurace seznamu filtrů IP **azure-onpremises-HTTP8080** aktivuje šifrování pro veškerý provoz, který odpovídá následujícím kritériím:

   * Libovolná zdrojová adresa v 10.0.1.0/24 (Azure Podnet2)
   * Libovolná cílová adresa v 10.2.27.0/25 (místní podsíť)
   * Protokol TCP
   * Cílový port 8080

   [![32]][32]

## <a name="5-edit-the-ip-filter-list"></a><a name="filterlist2"></a>5. Úprava seznamu filtrů IP

Chcete-li zašifrovat stejný typ provozu v opačném směru (od místního hostitele k virtuálnímu počítači Azure), potřebujete druhý filtr IP. Proces nastavení nového filtru je stejný proces, který jste použili k nastavení prvního filtru IP. Jedinými rozdíly jsou zdrojová a cílová podsíť.

1. Chcete-li přidat nový filtr IP do seznamu filtrů IP, vyberte **možnost Upravit**.

   [![33]][33]
2. Na stránce **Seznam filtrů IP** klepněte na tlačítko **Přidat**.

   [![34]][34]
3. Vytvořte druhý filtr IP pomocí nastavení v následujícím příkladu:

   [![35]][35]
4. Po vytvoření druhého filtru IP bude seznam filtrů IP vypadat takto:

   [![36]][36]

Pokud je vyžadováno šifrování mezi místním umístěním a podsítí Azure k ochraně aplikace, namísto úpravy existujícího seznamu filtrů IP, můžete místo toho přidat nový seznam filtrů IP. Připojování 2 seznamů filtrů IP k stejné zásadě protokolu IPsec poskytuje lepší flexibilitu, protože konkrétní seznam filtrů IP lze kdykoli upravit nebo odebrat, aniž by to mělo vliv na ostatní seznamy filtrů IP.

## <a name="6-create-an-ipsec-security-policy"></a><a name="ipsecpolicy"></a>6. Vytvoření zásad zabezpečení protokolu IPsec 

Vytvořte zásadu protokolu IPsec s pravidly zabezpečení.

1. Vyberte **zásady zabezpečení protokolu IP ve službě Active Directory,** které jsou přidruženy k hlavní výměře. Klepněte pravým tlačítkem myši a vyberte **příkaz Vytvořit zásady zabezpečení protokolu IP**.

   [![37]][37.]
2. Pojmenujte zásady zabezpečení. Například **policy-azure-onpremises**. Potom klepněte na tlačítko **Další**.

   [![38]][38]
3. Bez zaškrtnutí políčka klikněte na **Další.**

   [![39]][39]
4. Ověřte, zda je zaškrtnuté políčko **Upravit vlastnosti,** a klepněte na tlačítko **Dokončit**.

   [![40]][40.]

## <a name="7-edit-the-ipsec-security-policy"></a><a name="editipsec"></a>7. Úprava zásad zabezpečení protokolu IPsec

Přidejte do zásady IPsec **seznam filtrů IP** a **akce filtru,** které jste dříve nakonfigurovali.

1. Na kartě **Pravidla** vlastností zásad HTTP klepněte na tlačítko **Přidat**.

   [![41]][41.]
2. Na úvodní stránce klikněte na **Další**.

   [![42]][42.]
3. Pravidlo poskytuje možnost definovat režim IPsec: režim tunelového propojení nebo režim přenosu.

   * V režimu tunelového propojení je původní paket zapouzdřen sadou hlaviček IP. Režim tunelového propojení chrání interní informace o směrování šifrováním hlavičky PROTOKOLU IP původního paketu. Režim tunelového propojení je široce implementován mezi bránami ve scénářích VPN site-to-site. Režim tunelového propojení se ve většině případů používá pro šifrování mezi hostiteli mezi koncovými body.

   * Transportní režim šifruje pouze užitečné zatížení a přívěs ESP; hlavička IP původního paketu není zašifrována. V režimu přenosu se zdroj IP a cíl IP paketů nezmění.

   Vyberte **Toto pravidlo neurčuje tunelové propojení**a klepněte na tlačítko **Další**.

   [![43]][43.]
4. **Typ sítě definuje,** které síťové připojení je přidruženo k zásadám zabezpečení. Vyberte **Všechna síťová připojení**a klepněte na tlačítko **Další**.

   [![44]][44.]
5. Vyberte seznam filtrů IP, který jste vytvořili dříve, **azure-onpremises-HTTP8080**a klikněte na **další**.

   [![45]][45.]
6. Vyberte existující akci filtru **myEncryption,** kterou jste vytvořili dříve.

   [![46]][46]
7. Systém Windows podporuje čtyři různé typy ověřování: protokol Kerberos, certifikáty, protokol NTLMv2 a předsdílený klíč. Vzhledem k tomu, že spolupracujeme s hostiteli připojenými k doméně, vyberte **výchozí položku služby Active Directory (protokol Kerberos V5)** a klepněte na tlačítko **Další**.

   [![47]][47.]
8. Nová zásada vytvoří pravidlo zabezpečení: **azure-onpremises-HTTP8080**. Klikněte na tlačítko **OK**.

   [![48]][48.]

Zásada Protokolu IPsec vyžaduje, aby všechna připojení HTTP na cílovém portu 8080 používala režim přenosu protokolu IPsec. Vzhledem k tomu, že protokol HTTP je protokol s jasným textem, zajištění povolené zásady zabezpečení zajistí, že data budou při přenosu prostřednictvím soukromého partnerského vztahu ExpressRoute šifrována. Konfigurace služby IP Security pro službu Active Directory je složitější než konfigurace brány Windows Firewall s pokročilým zabezpečením, ale umožňuje další přizpůsobení připojení IPsec.

## <a name="8-assign-the-ipsec-gpo-to-the-ou"></a><a name="assigngpo"></a>8. Přiřazení příkazu gpo IPsec k řídicímu systému

1. Zobrazit zásady. Zásady skupiny zabezpečení jsou definovány, ale ještě nejsou přiřazeny.

   [![49]][49.]
2. Chcete-li přiřadit zásadu skupiny zabezpečení objektu **OU IPSecOU**, klepněte pravým tlačítkem myši na zásady zabezpečení a zvolte **přiřadit**.
   Každému počítači, který patří do hlavní pracovní skupiny, bude přiřazena zásada skupiny zabezpečení.

   [![50]][50.]

## <a name="check-traffic-encryption"></a><a name="checktraffic"></a>Kontrola šifrování provozu

Chcete-li rezervovat zásady zásad zásad šifrování použité na ou, nainstalujte službu IIS na všechny virtuální počítače Azure a v host1. Každá služba IIS je přizpůsobena tak, aby odpovídala na požadavky HTTP na portu 8080.
Chcete-li ověřit šifrování, můžete nainstalovat síť sniffer (jako Wireshark) ve všech počítačích v ou.
Skript prostředí PowerShell funguje jako klient HTTP pro generování požadavků HTTP na portu 8080:

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
Následující síťové zachycení zobrazuje výsledky pro místního hostitele1 s filtrem zobrazení ESP tak, aby odpovídaly pouze šifrovanému provozu:

[![51]][51.]

Pokud spustíte powershellový skript na premisies (http klient), zachytávání sítě v virtuálním počítači Azure zobrazí podobné trasování.

## <a name="next-steps"></a>Další kroky

Další informace o expressroute naleznete v [nejčastějších dotazech k expressroute](expressroute-faqs.md).

<!--Image References-->

[1]: ./media/expressroute-howto-ipsec-transport-private-windows/network-diagram.png "síťový režim přenosu diagramu IPsec přes ExpressRoute"
[4]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-interesting-traffic.png "IPsec zajímavý provoz"
[5]: ./media/expressroute-howto-ipsec-transport-private-windows/windows-ipsec.png "Zásady Windows IPsec"
[9]: ./media/expressroute-howto-ipsec-transport-private-windows/ou.png "Organizační jednotka v zásadách skupiny"
[10]: ./media/expressroute-howto-ipsec-transport-private-windows/create-gpo-ou.png "vytvořit gpo přidružené k ou"
[11]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-name.png "přiřadit název k příkazu gpo přidruženého k ou"
[12]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-gpo.png "úprava gpo"
[15]: ./media/expressroute-howto-ipsec-transport-private-windows/manage-ip-filter-list-filter-actions.png "Správa seznamů filtrů IP a akcí filtrů"
[16]: ./media/expressroute-howto-ipsec-transport-private-windows/add-filter-action.png "přidat akci filtru"
[17]: ./media/expressroute-howto-ipsec-transport-private-windows/action-wizard.png "Průvodce akcí"
[18]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-name.png "Název akce filtru"
[19]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action.png "Akce filtru"
[20]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-no-ipsec.png "určit chování je nezabezpečené připojení je navázán"
[21]: ./media/expressroute-howto-ipsec-transport-private-windows/security-method.png "bezpečnostní mechanismus"
[22]: ./media/expressroute-howto-ipsec-transport-private-windows/custom-security-method.png "Vlastní metoda zabezpečení"
[23]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-actions-list.png "seznam akcí filtru"
[24]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-ip-filter.png "Přidání nového seznamu filtrů IP"
[25]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-http-traffic.png "Přidání přenosů HTTP do filtru IP"
[26]: ./media/expressroute-howto-ipsec-transport-private-windows/match-both-direction.png "paketů v obou směrech"
[27]: ./media/expressroute-howto-ipsec-transport-private-windows/source-address.png "výběr zdrojové podsítě"
[28]: ./media/expressroute-howto-ipsec-transport-private-windows/source-network.png "Zdrojová síť"
[29]: ./media/expressroute-howto-ipsec-transport-private-windows/destination-network.png "Cílová síť"
[Protokol 30]: ./media/expressroute-howto-ipsec-transport-private-windows/protocol.png "Protocol"
[31]: ./media/expressroute-howto-ipsec-transport-private-windows/source-port-and-destination-port.png "zdrojový port a cílový port"
[32]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list.png "seznam filtrů"
[33]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-for-http.png "Seznam filtrů IP s přenosy HTTP"
[34]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-add-second-entry.png "Přidání druhého filtru IP"
[35]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-second-entry.png "Ip Filtr seznam-druhá položka"
[36]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list-2entries.png "Ip filtr seznam-druhá položka"
[37]: ./media/expressroute-howto-ipsec-transport-private-windows/create-ip-security-policy.png "vytvoření zásad zabezpečení protokolu IP"
[38]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-policy-name.png "název zásady IPsec"
[39]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-wizard.png "Průvodce zásadou IPsec"
[40]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-security-policy.png "úprav zásad y IPsec"
[41]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-rule.png "přidání nového pravidla zabezpečení do zásad protokolu IPsec"
[42]: ./media/expressroute-howto-ipsec-transport-private-windows/create-security-rule.png "vytvořit nové pravidlo zabezpečení"
[43]: ./media/expressroute-howto-ipsec-transport-private-windows/transport-mode.png "Způsob dopravy"
[44]: ./media/expressroute-howto-ipsec-transport-private-windows/network-type.png "Typ sítě"
[45]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-list.png "výběr existujícího seznamu filtrů IP"
[46]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-action.png "výběr existující akce filtru"
[47]: ./media/expressroute-howto-ipsec-transport-private-windows/authentication-method.png "výběr metody ověřování"
[48]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-completed.png "ukončení procesu vytváření bezpečnostní politiky"
[49]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-not-assigned.png "Zásady iPsec spojené se zásadou zásad zásad hlavního mocen, ale nepřiřazené"
[50]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-assigned.png "Zásad y IPsec přiřazených k gpo"
[51]: ./media/expressroute-howto-ipsec-transport-private-windows/encrypted-traffic.png "Zachycení šifrovaného provozu IPsec"
