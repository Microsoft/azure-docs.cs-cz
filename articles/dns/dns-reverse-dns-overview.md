---
title: Přehled zpětné DNS v Azure | Microsoft Docs
description: Zjistěte, jak zpětné funguje DNS a jeho použití v Azure
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: kumud
ms.openlocfilehash: 1ce14360d0f62a01172a8003e1d78a45885166f6
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
ms.locfileid: "32772394"
---
# <a name="overview-of-reverse-dns-and-support-in-azure"></a>Přehled zpětné DNS a podpory v Azure

Tento článek přináší přehled funguje jak zpětné DNS a zpětného DNS scénáře podporované v Azure.

## <a name="what-is-reverse-dns"></a>Co je zpětné DNS?

Konvenční záznamy DNS povolíte mapování z názvu DNS (např 'www.contoso.com) na IP adresu (například 64.4.6.100).  Zpětné DNS umožňuje překlad IP adresy (64.4.6.100) zpět do název (www.contoso.com).

Zpětné záznamy DNS se používají v různých situacích. Zpětné záznamy DNS se často používá v boje proti spamu e-mailu kontrolou odesílatele e-mailové zprávy.  Přijímající poštovního serveru načte zpětné záznam DNS serveru odesílání IP adresy a ověřuje, zda tohoto hostitele je oprávněn k odesílání e-mailu z původní doméně. 

## <a name="how-reverse-dns-works"></a>Jak zpětné funguje DNS

Zpětné záznamy DNS jsou hostované v speciální zóny DNS, označuje jako 'ARPA' zóny.  Tyto zóny formuláři samostatné hierarchie DNS paralelně s normální hierarchii hostování domény, jako je například "contoso.com".

Například DNS záznam "www.contoso.com" je implementovaná pomocí záznam DNS "A" s název "www" v zóně "contoso.com".  Tento záznam A odkazuje na odpovídající IP adresu v tomto případě 64.4.6.100.  Zpětného vyhledávání je implementovaná samostatně pomocí záznamu, PTR, s názvem '100' v zóně '6.4.64.in-addr.arpa' (Upozorňujeme, že jsou v zóny ARPA obrácený IP adresy.)  Tento záznam PTR, pokud byla nakonfigurována správně, odkazuje na název www.contoso.com.

Pokud organizace přiřazen blok IP adres, budou také získat právo spravovat odpovídající zóny ARPA. Zóny ARPA odpovídající bloky IP adres, používají v Azure jsou hostované a spravované společností Microsoft. Poskytovatel internetových služeb může hostitel zóny ARPA pro vlastní IP adresy pro vás nebo vám jako hostitel zóny ARPA ve službě DNS podle vaší volby, například Azure DNS.

> [!NOTE]
> Dopředného vyhledávání DNS a zpětného vyhledávání DNS jsou implementované v samostatné, paralelní hierarchiích DNS. Zpětné vyhledávání pro "www.contoso.com" je **není** hostované v zóně "contoso.com", spíše je hostovaná v zóny ARPA pro odpovídající blok IP adres. Samostatné zóny jsou používány pro bloky adres IPv4 a IPv6.

### <a name="ipv4"></a>IPv4

Název zóny zpětného vyhledávání IPv4 musí být v následujícím formátu: `<IPv4 network prefix in reverse order>.in-addr.arpa`.

Například při vytváření zóny zpětného vyhledávání pro záznamy hostitele pro hostitele pomocí IP adresy, které jsou v předponu 192.0.2.0/24, název zóny by se vytvořily izolace sítě předponu adresy (192.0.2) a pak obrácení směru (2.0.192) a přidáním přípona `.in-addr.arpa`.

|Podsíť – třída|Předpona sítě  |Předpona odstínech sítě  |Standardní příponu  |Název zóny zpětného vyhledávání |
|-------|----------------|------------|-----------------|---------------------------|
|Třída A|203.0.0.0/8     | 203        | .in-addr.arpa   | `203.in-addr.arpa`        |
|Třída B|198.51.0.0/16   | 51.198     | .in-addr.arpa   | `51.198.in-addr.arpa`     |
|Třída C|192.0.2.0/24    | 2.0.192    | .in-addr.arpa   | `2.0.192.in-addr.arpa`    |

### <a name="classless-ipv4-delegation"></a>Notace delegování IPv4

V některých případech je menší než třídy C rozsah IP adres přidělené organizace (/ 24) rozsahu. V takovém případě rozsah IP adres nespadá na hranici zóny v rámci `.in-addr.arpa` zón hierarchii a proto není možné delegovat jako podřízenou zónu.

Místo toho jiný mechanismus se používá k přenosu řízení záznamů jednotlivých zpětného vyhledávání (PTR) vyhrazené zóny DNS. Tento mechanismus deleguje podřízenou zónu pro každý rozsah IP adres a potom mapuje jednotlivě každou IP adresu v rozsahu tohoto podřízenou zónu pomocí záznamů CNAME.

Předpokládejme například, že organizace je udělit 192.0.2.128/26 rozsah IP pomocí jeho poskytovatele internetových služeb. To představuje 64 IP adresy, od 192.0.2.128 k 192.0.2.191. Zpětné DNS pro tento rozsah je implementována následujícím způsobem:
- Organizace vytvoří zónu zpětného vyhledávání názvem 128-26.2.0.192.in-addr.arpa. Předpona se 128-26' představuje segmentu sítě přiřazená k organizaci v rámci třídy C (/ 24) rozsahu.
- Poskytovatel internetových služeb vytvoří záznamy NS nastavit delegování DNS pro výše uvedené zónu ze třídy C nadřazené zóny. Vytvoří také záznamy CNAME v zóně zpětného vyhledávání nadřazené (třída C), mapování každou IP adresu v rozsahu IP do nové zóny vytvořené v organizaci:

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
Zpětné vyhledávání pro IP adresu, 192.0.2.129' dotazy pro záznam PTR s názvem '129.2.0.192.in-addr.arpa'. Tento dotaz přeloží prostřednictvím CNAME v nadřazené zóně záznam PTR v podřízené zóně.

### <a name="ipv6"></a>IPv6

Název zóny zpětného vyhledávání IPv6 by měl být ve tvaru: `<IPv6 network prefix in reverse order>.ip6.arpa`

Například. Při vytvoření zóny zpětného vyhledávání pro záznamy hostitele pro hostitele pomocí IP adresy, jsou 2001:db8:1000:abdc:: / 64 předpona, název zóny, by se vytvořily s izolace sítě předponu adresy (2001:db8:abdc::). Potom rozbalte předponu IPv6 sítě odebrat [nula komprese](https://technet.microsoft.com/library/cc781672(v=ws.10).aspx), pokud byl použit tak, aby zkrátil předpona adresy IPv6 (2001:0db8:abdc:0000::). Pořadí, pomocí dobou jako oddělovače mezi každou hexadecimální číslo předponu, k vytvoření předponu odstínech sítě (`0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2`) a přidat příponu `.ip6.arpa`.


|Předpona sítě  |Předpona rozšířené a odstínech sítě |Standardní příponu |Název zóny zpětného vyhledávání  |
|---------|---------|---------|---------|
|2001:DB8:abdc:: / 64    | 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2        | . ip6.arpa naleznete        | `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa`       |
|2001:DB8:1000:9102:: / 64    | 2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2        | . ip6.arpa naleznete        | `2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2.ip6.arpa`        |


## <a name="azure-support-for-reverse-dns"></a>Podpora Azure pro zpětné DNS

Azure podporuje dva samostatné scénáře týkající se reverse DNS:

**Hostuje zóny zpětného vyhledávání odpovídající vaší blok IP adres.**
Azure DNS lze provádět [hostování vaší zóny zpětného vyhledávání a spravovat záznamy PTR pro každý zpětného vyhledávání DNS](dns-reverse-dns-hosting.md)pro oba protokoly IPv4 a IPv6.  Proces vytváření zóny zpětného vyhledávání (ARPA), nastavení delegování a konfiguraci záznamů PTR je stejné jako regulární zóny DNS.  Pouze rozdíly jsou delegování musí být nakonfigurované přes vašeho poskytovatele internetových služeb, nikoli registrátora DNS, a slouží pouze záznamů typu PTR.

**Konfigurace zpětné záznam DNS pro adresu IP přiřazenou k vaší službě Azure.** Azure vám umožní [konfigurace zpětného vyhledávání pro IP adresy přidělené k službě Azure](dns-reverse-dns-for-azure-services.md).  Tato zpětného vyhledávání je nakonfigurovaný v Azure jako záznam PTR v odpovídající zóny ARPA.  Tyto zóny ARPA odpovídající všechny rozsahy IP používají v Azure, jsou hostovaná společností Microsoft

## <a name="next-steps"></a>Další postup

Další informace o zpětné DNS najdete v tématu [zpětného vyhledávání DNS na webu Wikipedia](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Zjistěte, jak [hostitel zóny zpětného vyhledávání pro váš rozsah poskytovatele internetových služeb přiřadit IP v Azure DNS](dns-reverse-dns-for-azure-services.md).
<br>
Zjistěte, jak [zpětné záznamy DNS pro služeb Azure spravovat](dns-reverse-dns-for-azure-services.md).

