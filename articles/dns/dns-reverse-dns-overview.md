---
title: Přehled reverzních DNS v Azure – Azure DNS
description: V tomto studijním programu se můžete začít učit, jak funguje reverzní DNS a jak ho lze použít v Azure.
services: dns
documentationcenter: na
author: rohinkoul
manager: KumuD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: bf3da62e989f0e029efdc8e9c70f5f45e0ddd765
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76932301"
---
# <a name="overview-of-reverse-dns-and-support-in-azure"></a>Přehled reverzního DNS a podpory v Azure

Tento článek poskytuje přehled o tom, jak funguje reverzní DNS a reverzní scénáře DNS podporované v Azure.

## <a name="what-is-reverse-dns"></a>Co je reverzní DNS?

Konvenční záznamy DNS umožňují mapování z názvu DNS (například "www.contoso.com") na ADRESU IP (například 64.4.6.100).  Reverzní DNS umožňuje překlad IP adresy (64.4.6.100) zpět na jméno ('www.contoso.com').

Reverzní ZÁZNAMY DNS se používají v různých situacích. Například reverzní záznamy DNS jsou široce používány v boji proti nevyžádané poště e-mail ověřením odesílatele e-mailové zprávy.  Přijímající poštovní server načte reverzní záznam DNS adresy IP odesílajícího serveru a ověří, zda je tento hostitel oprávněn odesílat e-maily z původní domény. 

## <a name="how-reverse-dns-works"></a>Jak funguje reverzní DNS

Reverzní záznamy DNS jsou hostovány ve zvláštních zónách DNS, známých jako zóny ARPA.  Tyto zóny tvoří samostatnou hierarchii DNS paralelně s normální hierarchií hostitelských domén, jako je například "contoso.com".

Například záznam DNS "www.contoso.com" je implementován pomocí záznamu DNS "A" s názvem "www" v zóně "contoso.com".  Tento záznam A odkazuje na odpovídající IP adresu, v tomto případě 64.4.6.100.  Zpětné vyhledávání je implementováno samostatně pomocí záznamu PTR s názvem "100" v zóně "6.4.64.in-addr.arpa" (všimněte si, že IP adresy jsou obráceny v zónách ARPA.)  Tento záznam PTR, pokud byl správně nakonfigurován, odkazuje na název "www.contoso.com".

Když je organizaci přiřazen blok IP adres, získají také právo spravovat odpovídající zónu ARPA. Zóny ARPA odpovídající blokům IP adres používaným Azure hostuje a spravuje Microsoft. Poskytovatel služeb Služeb LŽmůže být hostitelem zóny ARPA pro vaše vlastní IP adresy nebo vám může umožnit hostovat zónu ARPA ve službě DNS podle vašeho výběru, jako je Azure DNS.

> [!NOTE]
> Dopředná vyhledávání DNS a reverzní vyhledávání DNS jsou implementována v samostatných paralelních hierarchiích DNS. Zpětné vyhledávání pro 'www.contoso.com' **není** hostováno v zóně "contoso.com", spíše je hostováno v zóně ARPA pro odpovídající blok IP adres. Samostatné zóny se používají pro bloky adres IPv4 a IPv6.

### <a name="ipv4"></a>IPv4

Název zóny zpětného vyhledávání IPv4 by měl být `<IPv4 network prefix in reverse order>.in-addr.arpa`v následujícím formátu: .

Například při vytváření reverzní zóny pro hostitele záznamů pro hostitele s IP adresami, které jsou v předponě 192.0.2.0/24, název zóny by byl vytvořen izolováním síťové předpony adresy (192.0.2) `.in-addr.arpa`a následným obrácením pořadí (2.0.192) a přidáním přípony .

|Třída podsítě|Předpona sítě  |Obrácená předpona sítě  |Standardní přípona  |Název reverzní zóny |
|-------|----------------|------------|-----------------|---------------------------|
|Třída A|203.0.0.0/8     | 203        | .in-addr.arpa   | `203.in-addr.arpa`        |
|Třída B|198.51.0.0/16   | 51.198     | .in-addr.arpa   | `51.198.in-addr.arpa`     |
|Třída C|192.0.2.0/24    | 2.0.192    | .in-addr.arpa   | `2.0.192.in-addr.arpa`    |

### <a name="classless-ipv4-delegation"></a>Beztřídní delegace IPv4

V některých případech je rozsah IP přidělený organizaci menší než rozsah třídy C (/24). V tomto případě rozsah IP nespadá na hranici `.in-addr.arpa` zóny v hierarchii zóny, a proto nemůže být delegována jako podřízená zóna.

Místo toho se používá jiný mechanismus k přenosu řízení jednotlivých záznamů zpětného vyhledávání (PTR) do vyhrazené zóny DNS. Tento mechanismus deleguje podřízenou zónu pro každý rozsah IP adres a pak mapuje každou ADRESU IP v rozsahu jednotlivě na tuto podřízenou zónu pomocí záznamů CNAME.

Předpokládejme například, že organizaci je udělen rozsah IP adres 192.0.2.128/26 jejím isp. To představuje 64 IP adres, od 192.0.2.128 do 192.0.2.191. Reverzní DNS pro tento rozsah je implementována takto:
- Organizace vytvoří zónu zpětného vyhledávání nazvanou 128-26.2.0.192.in-addr.arpa. Předpona "128-26" představuje síťový segment přiřazený organizaci v rozsahu třídy C (/24).
- Isp vytvoří záznamy NS nastavit delegování DNS pro výše uvedenou zónu z nadřazené zóny třídy C. Vytvoří také záznamy CNAME v nadřazené zóně zpětného vyhledávání (třída C) a mapuje každou adresu IP v rozsahu IP na novou zónu vytvořenou organizací:

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
- Organizace pak spravuje jednotlivé záznamy PTR v rámci své podřízené zóny.

```
$ORIGIN 128-26.2.0.192.in-addr.arpa
; PTR records for each UIP address. Names match CNAME targets in parent zone
129      PTR    www.contoso.com
130      PTR    mail.contoso.com
131      PTR    partners.contoso.com
; etc
```
Zpětné vyhledávání dotazů ip adresy '192.0.2.129' pro záznam PTR s názvem "129.2.0.192.in-addr.arpa". Tento dotaz řeší prostřednictvím CNAME v nadřazené zóně na záznam PTR v podřízené zóně.

### <a name="ipv6"></a>IPv6

Název zóny zpětného vyhledávání IPv6 by měl být v následujícím formuláři:`<IPv6 network prefix in reverse order>.ip6.arpa`

Například,. Při vytváření reverzní zóny pro hostitele záznamů pro hostitele s IP adresami, které jsou v předponě 2001:db8:1000:abdc::/64, bude název zóny vytvořen izolováním síťové předpony adresy (2001:db8:abdc::). Dále rozbalte předponu sítě IPv6 a odeberte [nulovou kompresi](https://technet.microsoft.com/library/cc781672(v=ws.10).aspx), pokud byla použita ke zkrácení předpony adresy IPv6 (2001:0db8:abdc:0000::). Stornováním pořadí pomocí tečky jako oddělovače mezi jednotlivými šestnáctkovým číslem v předponě vytvořte obrácenou předponu sítě (`0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2`) a přidejte příponu `.ip6.arpa`.


|Předpona sítě  |Rozšířená a obrácená předpona sítě |Standardní přípona |Název reverzní zóny  |
|---------|---------|---------|---------|
|2001:db8:abdc::/64    | 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2        | .ip6.arpa        | `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa`       |
|2001:db8:1000:9102::/64    | 2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2        | .ip6.arpa        | `2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2.ip6.arpa`        |


## <a name="azure-support-for-reverse-dns"></a>Podpora Azure pro reverzní DNS

Azure podporuje dva samostatné scénáře týkající se reverznídns:

**Hostování zóny zpětného vyhledávání odpovídající bloku IP adresy.**
Azure DNS lze použít k [hostování reverzní vyhledávací zóny a spravovat záznamy PTR pro každé zpětné vyhledávání DNS](dns-reverse-dns-hosting.md), pro IPv4 a IPv6.  Proces vytváření zóny zpětného vyhledávání (ARPA), nastavení delegování a konfigurace záznamů PTR je stejný jako u běžných zón DNS.  Jedinýmrozdílem je, že delegování musí být nakonfigurováno prostřednictvím poskytovatele služeb Internetu, nikoli prostřednictvím registrátora DNS, a měl by být použit pouze typ záznamu PTR.

**Nakonfigurujte reverzní záznam DNS pro IP adresu přiřazenou vaší službě Azure.** Azure umožňuje [konfigurovat zpětné vyhledávání IP adres přidělených vaší službě Azure](dns-reverse-dns-for-azure-services.md).  Toto zpětné vyhledávání je konfigurováno azure jako záznam PTR v odpovídající zóně ARPA.  Tyto zóny ARPA, které odpovídají všem rozsahům IP adres používaných v Azure, jsou hostovány společností Microsoft

## <a name="next-steps"></a>Další kroky

Další informace o reverzním DNS naleznete v [tématu reverzní vyhledávání DNS na Wikipedii](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Přečtěte si, jak [hostovat zónu zpětného vyhledávání pro rozsah IP adres přiřazený k isp v Azure DNS](dns-reverse-dns-for-azure-services.md).
<br>
Přečtěte si, jak [spravovat reverzní záznamy DNS pro vaše služby Azure](dns-reverse-dns-for-azure-services.md).

