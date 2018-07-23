---
title: Přehled reverzních záznamů DNS v Azure | Dokumentace Microsoftu
description: Zjistěte, jak reverzní DNS funguje a jak je možné v Azure
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: victorh
ms.openlocfilehash: fa3798a35804998936e0ac166fceff02b01231a0
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171506"
---
# <a name="overview-of-reverse-dns-and-support-in-azure"></a>Přehled reverzního DNS a podporu v Azure

Tento článek obsahuje přehled jak reverzní DNS funguje a reverzní DNS scénáře podporované v Azure.

## <a name="what-is-reverse-dns"></a>Co je reverzních záznamů DNS?

Konvenční záznamy DNS na IP adresu (například 64.4.6.100) povolit mapování z názvu DNS (např 'www.contoso.com).  Reverzní DNS umožňuje překlad IP adresy (64.4.6.100) zpět do názvu ("www.contoso.com").

Reverzních záznamů DNS se používají v nejrůznějších situacích. Reverzních záznamů DNS se často používá v boji proti spamu e-mailu tak, že ověříte odesílatele e-mailové zprávy.  Přijímající poštovní server načte zpětný záznam DNS server odesílající IP adresy a ověří, zda, které hostují autorizaci k odesílání e-mailů z původní domény. 

## <a name="how-reverse-dns-works"></a>Funguje jak reverzní DNS

Reverzních záznamů DNS jsou hostované v speciální zóny DNS, známé jako "ARPA" zóny.  Tyto zóny formuláře samostatné hierarchie DNS souběžně s normální hierarchie hostování domény, jako je například "contoso.com".

Například DNS záznamů "www.contoso.com" je implementováno pomocí záznamu DNS "A" s názvem "www" v zóně "contoso.com".  Tento záznam A odkazuje na příslušnou IP adresou v tomto případě 64.4.6.100.  Zpětné vyhledávání je implementováno samostatně pomocí "PTR' záznam s názvem"100"v zóně"6.4.64.in-addr.arpa"(Všimněte si, že IP adresy se vrátit zpět do zóny ARPA.)  Tento záznam PTR, pokud byla nakonfigurována správně, odkazuje na název "www.contoso.com".

Když organizace přiřadí blok IP adres, získají také právo spravovat odpovídající zónu ARPA. Zóny ARPA odpovídající bloky IP adres Azure používá jsou hostované a spravované microsoftem. Poskytovatel může hostovat zónu ARPA pro vlastní IP adresy pro vás nebo vám umožňuje hostovat zónu ARPA ve službě DNS podle vašeho výběru, jako je Azure DNS.

> [!NOTE]
> Dopředné vyhledávání DNS a reverzního vyhledávání DNS jsou implementovány v samostatné, paralelní hierarchií DNS. Zpětné vyhledávání pro "www.contoso.com" je **není** hostovaná v zóně "contoso.com", spíše je hostován v zónu ARPA pro odpovídající blok IP adres. Samostatné zóny jsou používány pro bloky adres IPv4 a IPv6.

### <a name="ipv4"></a>IPv4

Název zóně zpětného vyhledávání IPv4 by měl být v následujícím formátu: `<IPv4 network prefix in reverse order>.in-addr.arpa`.

Například při vytváření zóny zpětného vyhledávání na záznamy hostitele pro hostitele s IP adresami, které jsou v 192.0.2.0/24 předponu, název zóny by se vytvořily pomocí izolace sítě předponu adresy (192.0.2) a potom obráceným pořadím (2.0.192) a přidání přípony `.in-addr.arpa`.

|Třída podsítě|Předpona sítě  |Předpona obrácený sítě  |Standardní příponu  |Název zóny zpětného vyhledávání |
|-------|----------------|------------|-----------------|---------------------------|
|Třída A|203.0.0.0/8     | 203        | .in-addr.arpa   | `203.in-addr.arpa`        |
|Třída B|198.51.0.0/16   | 51.198     | .in-addr.arpa   | `51.198.in-addr.arpa`     |
|Třída C|192.0.2.0/24    | 2.0.192    | .in-addr.arpa   | `2.0.192.in-addr.arpa`    |

### <a name="classless-ipv4-delegation"></a>Delegování Classless IPv4

V některých případech je menší než třídy C rozsah IP adres přidělené organizace (/ 24) rozsahu. V takovém případě rozsah IP adres nespadá na hranici zóny v rámci `.in-addr.arpa` zóna hierarchii a proto není možné delegovat jako podřízenou zónu.

Místo toho jiný mechanismus slouží k řízení záznamů jednotlivých zpětného vyhledávání (PTR) je převedeno na vyhrazené zóny DNS. Tento mechanismus podřízenou zónu pro každý rozsah IP adres deleguje potom mapuje jednotlivě každou IP adresu v rozsahu tohoto podřízenou zónu pomocí záznamů CNAME.

Předpokládejme například, že organizace udělují 192.0.2.128/26 rozsah IP jeho poskytovatele internetových služeb. To představuje 64 IP adresy, od 192.0.2.128 k 192.0.2.191. Reverzních záznamů DNS pro tento rozsah je implementován takto:
- Organizace vytvoří zónu zpětného vyhledávání s názvem 128-26.2.0.192.in-addr.arpa. Představuje segmentu sítě přiřazená k organizaci v rámci třídy C předpona "128-26' (/ 24) rozsahu.
- Poskytovatel internetových služeb vytvoří záznamy NS pro nastavení delegování DNS v nadřazené zóně třídy C výše uvedené zóny. Také vytvoří záznamy CNAME v nadřazené (třídy C) zóny zpětného vyhledávání, mapování každou IP adresu v rozsahu IP adres do nové zóny vytvořené organizaci:

```
$ORIGIN 2.0.192.in-addr.arpa
; Delegate child zone
128-26    NS       <name server 1 for 128-26.2.0.192.in-addr.arpa>
128-26    NS       <name server 2 for 128-26.2.0.192.in-addr.arpa>
; CNAME records for each IP address
129       CNAME    129.128-26.2.0.192.in-addr.arpa
130       CNAME    130.128-26.2.0.192.in-addr.arpa
131       CNAME    131.128-26.2.0.192.in-addr.arpa
; etc
```
- Organizace pak spravuje jednotlivých záznamů PTR v rámci jejich podřízené zóny.

```
$ORIGIN 128-26.2.0.192.in-addr.arpa
; PTR records for each UIP address. Names match CNAME targets in parent zone
129      PTR    www.contoso.com
130      PTR    mail.contoso.com
131      PTR    partners.contoso.com
; etc
```
Pro dotazy IP adresa "192.0.2.129" pro záznam PTR zpětného vyhledávání s názvem "129.2.0.192.in-addr.arpa". Tento dotaz se přeloží pomocí záznamu CNAME v nadřazenou zónu a záznam PTR v podřízené zóně.

### <a name="ipv6"></a>IPv6

Název zóny zpětného vyhledávání IPv6 by měl být ve tvaru: `<IPv6 network prefix in reverse order>.ip6.arpa`

Například. Při vytváření zóny zpětného vyhledávání na záznamy hostitele pro hostitele s IP adresami, které jsou 2001:db8:1000:abdc:: / 64 předponu, název zóny, by se vytvořil pomocí izolace sítě předponu adresy (2001:db8:abdc::). Dále rozbalte předponu IPv6 sítě odebrat [nula komprese](https://technet.microsoft.com/library/cc781672(v=ws.10).aspx), pokud se používá ke zkrácení předponu adresy protokolu IPv6 (2001:0db8:abdc:0000::). Pořadí použití tečku jako oddělovač mezi každou šestnáctkové číslo v předponu, předponu obrácený sítě vytvářet (`0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2`) a přidejte příponu `.ip6.arpa`.


|Předpona sítě  |Předpona rozšířené a obrácený sítě |Standardní příponu |Název zóny zpětného vyhledávání  |
|---------|---------|---------|---------|
|2001:DB8:abdc:: / 64    | 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2        | . ip6.arpa naleznete        | `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa`       |
|2001:DB8:1000:9102:: / 64    | 2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2        | . ip6.arpa naleznete        | `2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2.ip6.arpa`        |


## <a name="azure-support-for-reverse-dns"></a>Podpora Azure pro reverzních záznamů DNS

Azure podporuje dva samostatné scénáře týkající se reverzní DNS:

**Hostování zóny zpětného vyhledávání odpovídající vaší blok IP adres.**
Azure DNS je možné použít k [hostování zón zpětného vyhledávání a Správa záznamů PTR pro každý zpětného vyhledávání DNS](dns-reverse-dns-hosting.md), pro protokol IPv4 i IPv6.  Proces vytváření zóny zpětného vyhledávání (ARPA), nastavení delegování a konfiguraci záznamů PTR je stejná jako regulární zóny DNS.  Pouze rozdíly jsou, delegování musí být konfigurováno prostřednictvím svého poskytovatele internetových služeb spíše než vašeho registrátora DNS a by měla sloužit pouze PTR typu záznamu.

**Nakonfigurujte zpětný záznam DNS pro IP adresu přiřazenou k vaší službě Azure.** Azure vám umožní [konfigurace reverzního vyhledávání IP adres, přiděleny ke službě Azure](dns-reverse-dns-for-azure-services.md).  Tato zpětného vyhledávání je nakonfigurovaný v Azure jako záznamů PTR v odpovídající zóny ARPA.  Tyto zóny ARPA odpovídající všechny rozsahy IP adres, které Azure používá jsou hostované společnosti Microsoft

## <a name="next-steps"></a>Další postup

Další informace o reverzních záznamů DNS najdete v tématu [zpětného vyhledávání DNS v encyklopedii Wikipedia](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Zjistěte, jak [hostování zóny zpětného vyhledávání pro váš rozsah IP přiřazené poskytovatele internetových služeb v Azure DNS](dns-reverse-dns-for-azure-services.md).
<br>
Zjistěte, jak [Správa reverzních záznamů DNS pro služby Azure](dns-reverse-dns-for-azure-services.md).

