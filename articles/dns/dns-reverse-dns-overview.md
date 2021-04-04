---
title: Přehled reverzního DNS v Azure – Azure DNS
description: V tomto výukovém postupu se naučíte, jak reverzní služba DNS funguje a jak se dá používat v Azure.
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
ms.openlocfilehash: 8af9549efc3e8dab54f55dd404346d87201dee2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94965608"
---
# <a name="overview-of-reverse-dns-and-support-in-azure"></a>Přehled reverzních DNS a podpory v Azure

Tento článek poskytuje přehled o tom, jak reverzní DNS funguje, a scénáře reverzních DNS podporované v Azure.

## <a name="what-is-reverse-dns"></a>Co je reverzní DNS?

Konvenční záznamy DNS umožňují mapování z názvu DNS (například ' www.contoso.com ') na IP adresu (třeba na 64.4.6.100).  Reverzní DNS umožňuje překlad IP adresy (64.4.6.100) zpět na název (' www.contoso.com ').

Reverzní záznamy DNS se používají v různých situacích. Například reverzní záznamy DNS se často používají v boji proti nevyžádané poště tím, že ověřují odesílatele e-mailové zprávy.  Přijímající poštovní server načte reverzní záznam DNS IP adresy odesílajícího serveru a ověří, jestli je tento hostitel autorizovaný k odesílání e-mailů z původní domény. 

## <a name="how-reverse-dns-works"></a>Jak reverzní služba DNS funguje

Reverzní záznamy DNS se hostují ve zvláštních zónách DNS, označovaných jako zóny "ARPA".  Tyto zóny tvoří samostatnou hierarchii DNS paralelně s normálními hostujícími doménami hierarchie, jako je například ' contoso.com '.

Například záznam DNS ' www.contoso.com ' je implementován pomocí záznamu DNS ' A ' s názvem ' www ' v zóně ' contoso.com '.  Tento záznam odkazuje na odpovídající IP adresu v tomto případě 64.4.6.100.  Zpětné vyhledávání se implementuje samostatně pomocí záznamu PTR s názvem 100 v zóně 6.4.64.in-addr. arpa (Všimněte si, že IP adresy se přesměrují v zónách ARPA.)  Tento záznam PTR, pokud byl správně nakonfigurován, odkazuje na název ' www.contoso.com '.

Když je organizaci přiřazen blok IP adres, získá taky právo spravovat odpovídající zónu ARPA. Zóny ARPA odpovídající blokům IP adres používaným v Azure jsou hostované a spravované Microsoftem. Poskytovatel internetových služeb může hostovat zónu ARPA pro vaše vlastní IP adresy, nebo vám může umožnit hostovat zónu ARPA ve službě DNS podle vašeho výběru, například Azure DNS.

> [!NOTE]
> Dopředné vyhledávání DNS a reverzní vyhledávání DNS se implementují v samostatných paralelních hierarchiích DNS. Zpětné vyhledávání pro ' www.contoso.com ' není hostováno v zóně ' contoso.com ', spíše **je hostováno** v zóně arpa pro odpovídající blok IP adres. Pro bloky adres IPv4 a IPv6 se používají samostatné zóny.

### <a name="ipv4"></a>IPv4

Název zóny zpětného vyhledávání IPv4 by měl být v následujícím formátu: `<IPv4 network prefix in reverse order>.in-addr.arpa` .

Například při vytváření reverzní zóny k záznamům hostitelů pro hostitele s IP adresami, které jsou v předponě 192.0.2.0/24, se název zóny vytvoří tak, že se zaizoluje síťová předpona adresy (192.0.2) a pak se přesměruje objednávka (2.0.192) a přidání přípony `.in-addr.arpa` .

|Třída podsítě|Předpona sítě  |Předpona reverzní sítě  |Standardní přípona  |Název reverzní zóny |
|-------|----------------|------------|-----------------|---------------------------|
|Třída A|203.0.0.0/8     | 203        | . in-addr. arpa   | `203.in-addr.arpa`        |
|Třída B|198.51.0.0/16   | 51,198     | . in-addr. arpa   | `51.198.in-addr.arpa`     |
|Třída C|192.0.2.0/24    | 2.0.192    | . in-addr. arpa   | `2.0.192.in-addr.arpa`    |

### <a name="classless-ipv4-delegation"></a>Delegování beztřídou IPv4

V některých případech je rozsah IP adres přidělený organizaci menší než rozsah třídy C (/24). V takovém případě rozsah IP adres nespadají do hranice zóny v rámci `.in-addr.arpa` hierarchie zóny, a proto nemůže být delegovaný jako podřízená zóna.

Místo toho se k přenosu řízení jednotlivých záznamů zpětného vyhledávání (PTR) do vyhrazené zóny DNS používá jiný mechanismus. Tento mechanismus deleguje podřízenou zónu pro každý rozsah IP adres a pak mapuje každou IP adresu v rozsahu individuálně na tuto podřízenou zónu pomocí záznamů CNAME.

Předpokládejme například, že organizace má přidělený poskytovatel internetových služeb rozsah IP adres 192.0.2.128/26. To představuje 64 IP adres, od 192.0.2.128 po 192.0.2.191. Reverzní DNS pro tento rozsah je implementován následujícím způsobem:
- Organizace vytvoří zónu zpětného vyhledávání s názvem 128-26.2.0.192.in-addr. arpa. Předpona "128-26" představuje segment sítě přiřazený organizaci v rámci rozsahu třídy C (/24).
- Poskytovatel internetových služeb vytvoří záznamy NS pro nastavení delegování DNS pro výše uvedenou zónu z nadřazené zóny třídy C. Vytvoří také záznamy CNAME v nadřazené zóně zpětného vyhledávání (třída C), přičemž mapování každé IP adresy v rozsahu IP adres na novou zónu vytvořenou organizací:

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
Zpětné vyhledávání pro dotaz 192.0.2.129 IP adresy pro záznam PTR s názvem 129.2.0.192.in-addr. arpa. Tento dotaz překládá přes CNAME v nadřazené zóně na záznam PTR v podřízené zóně.

### <a name="ipv6"></a>IPv6

Název zóny zpětného vyhledávání IPv6 by měl být v následujícím tvaru: `<IPv6 network prefix in reverse order>.ip6.arpa`

Například. Při vytváření reverzní zóny k záznamům hostitelů pro hostitele s IP adresami, které jsou v předponě 2001: db8:1000: ABDC::/64, se vytvoří název zóny tak, že izoluje předponu sítě adresy (2001: db8: ABDC::). Dále rozbalte předponu sítě IPv6 pro odebrání [nulové komprese](/previous-versions/windows/it-pro/windows-server-2003/cc781672(v=ws.10)), pokud se použila k zkrácení předpony adresy IPv6 (2001:0db8: ABDC: 0000::). Obrátí pořadí a pomocí tečky jako oddělovače mezi každým šestnáctkovým číslem v předponě vytvořte reverzní předponu sítě ( `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2` ) a přidejte příponu `.ip6.arpa` .


|Předpona sítě  |Rozšířená a obrácená předpona sítě |Standardní přípona |Název reverzní zóny  |
|---------|---------|---------|---------|
|2001: db8: ABDC::/64    | 0.0.0.0. c. d. b. a. 8. b. d. 0.1.0.0.2        | . ip6. arpa        | `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa`       |
|2001: db8:1000:9102::/64    | 2.0.1.9.0.0.0.1.8. b. d. 0.1.0.0.2        | . ip6. arpa        | `2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2.ip6.arpa`        |


## <a name="azure-support-for-reverse-dns"></a>Podpora Azure pro reverzní DNS

Azure podporuje dva samostatné scénáře týkající se reverzních DNS:

**Hostování zóny zpětného vyhledávání odpovídající vašemu bloku IP adres.**
Azure DNS můžete použít k [hostování zón zpětného vyhledávání a spravovat záznamy PTR pro každé zpětné vyhledávání DNS](dns-reverse-dns-hosting.md)pro IPv4 i IPv6.  Proces vytvoření zóny zpětného vyhledávání (ARPA), nastavení delegování a konfigurace záznamů PTR je stejný jako u standardních zón DNS.  Jediným rozdílem je, že delegování musí být nakonfigurované přes poskytovatele internetových služeb, a ne jako registrátora DNS a mělo by se použít jenom záznam typu PTR.

**Nakonfigurujte reverzní záznam DNS pro IP adresu přiřazenou službě Azure.** Azure umožňuje [nakonfigurovat zpětné vyhledávání pro IP adresy přidělené vaší službě Azure](dns-reverse-dns-for-azure-services.md).  Toto zpětné vyhledávání konfiguruje Azure jako záznam PTR v odpovídající zóně ARPA.  Tyto zóny rozhraní ARPA odpovídající všem rozsahům IP, které Azure používá, jsou hostovány společností Microsoft.

## <a name="next-steps"></a>Další kroky

Další informace o reverzních DNS najdete v tématu [reverzní DNS Lookup v Wikipedii](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Naučte se [hostovat zónu zpětného vyhledávání pro rozsah IP adres přiřazený poskytovateli internetových služeb v Azure DNS](dns-reverse-dns-for-azure-services.md).
<br>
Naučte se [Spravovat reverzní záznamy DNS pro služby Azure](dns-reverse-dns-for-azure-services.md).