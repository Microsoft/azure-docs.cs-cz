---
title: Omezení velikosti požadavků firewallu webových aplikací a seznamy vyloučení v Azure Application Gateway-Azure Portal
description: Tento článek obsahuje informace o omezeních velikosti požadavků firewallu webových aplikací a seznamech vyloučení v Application Gateway s Azure Portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/20/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: ddf631601510e725d77cc391ad41192a47ab0cf1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84752472"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Omezení velikosti a seznamů vyloučení požadavků firewallu webových aplikací

Firewall webových aplikací (WAF) služby Azure Application Gateway poskytuje ochranu pro webové aplikace. Tento článek popisuje WAF omezení velikosti požadavků a seznam vyloučení. Tato nastavení se nacházejí v zásadách WAF přidružených k vašemu Application Gateway. Další informace o zásadách WAF najdete v tématu [Brána Firewall webových aplikací Azure v azure Application Gateway](ag-overview.md) a [vytváření zásad firewallu webových aplikací pro Application Gateway](create-waf-policy-ag.md)

## <a name="waf-exclusion-lists"></a>Seznamy vyloučení WAF

![Omezení velikosti požadavku](../media/application-gateway-waf-configuration/waf-policy.png)

Seznamy vyloučení WAF umožňují vynechat určité atributy žádostí z vyhodnocení WAF. Běžným příkladem jsou vložené tokeny služby Active Directory, které se používají pro pole ověřování nebo hesla. Tyto atributy jsou náchylné k zahrnutí speciálních znaků, které mohou aktivovat falešně pozitivní hodnoty z pravidel WAF. Po přidání atributu do seznamu vyloučení WAF se tento atribut nepovažuje za žádné nakonfigurované a aktivní WAF pravidlo. Seznamy vyloučení jsou globální v oboru.

Následující atributy lze přidat do seznamů vyloučení podle názvu. Hodnoty zvoleného pole nejsou vyhodnocovány proti pravidlům WAF, ale jejich názvy stále jsou (viz příklad 1 níže, hodnota hlavičky User-Agent je vyloučena z vyhodnocení WAF). Seznamy vyloučení odstraňují kontrolu hodnoty pole.

* Hlavičky požadavku
* Soubory cookie požadavků
* Název atributu žádosti (args) se dá přidat jako element vyloučení, třeba:

   * Název pole formuláře
   * Entita JSON
   * Argumenty řetězce dotazu adresy URL

Můžete zadat přesně takovou hlavičku požadavku, tělo, soubor cookie nebo atribut řetězce dotazu.  Případně můžete volitelně zadat částečné shody. Pravidla vyloučení jsou globální v oboru a platí pro všechny stránky a všechna pravidla.

Následují podporované operátory kritérií shody:

- **Equals**: Tento operátor se používá pro přesnou shodu. Jako příklad pro výběr záhlaví s názvem **bearerToken**použijte operátor Equals se sadou selektoru jako **bearerToken**.
- **Začíná**na: Tento operátor odpovídá všem polím, která začínají zadanou hodnotou selektoru.
- **Končí**na: Tento operátor odpovídá všem polím žádosti, která končí zadanou hodnotou selektoru.
- **Obsahuje**: Tento operátor odpovídá všem polím požadavku, která obsahují zadanou hodnotu selektoru.
- **Equals**: Tento operátor odpovídá všem polím žádosti. * bude hodnota selektoru.

Ve všech případech porovnávání nerozlišuje velká a malá písmena a regulární výraz nejsou povoleny jako selektory.

> [!NOTE]
> Další informace a nápovědu k řešení potíží najdete v tématu [řešení potíží s WAF](web-application-firewall-troubleshoot.md).

### <a name="examples"></a>Příklady

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Následující příklady ukazují použití vyloučení.

#### <a name="example-1"></a>Příklad 1

V tomto příkladu chcete vyloučit hlavičku User-Agent. Hlavička požadavku User-Agent obsahuje charakteristický řetězec, který umožňuje partnerům síťového protokolu identifikovat typ aplikace, operační systém, výrobce softwaru nebo verzi softwaru žádajícího softwarového agenta. Další informace najdete v tématu [User-Agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent).

Je možné zakázat vyhodnocování této hlavičky z libovolného počtu důvodů. Může se jednat o řetězec, který WAF uvidí a předpokládá, že je škodlivý. Například klasický útok SQL "x = x" v řetězci. V některých případech to může být legitimní provoz. Proto může být nutné vyřadit tuto hlavičku z WAF vyhodnocení.

Následující rutina Azure PowerShell vyloučí hlavičku User-Agent z vyhodnocení:

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```
#### <a name="example-2"></a>Příklad 2

Tento příklad vylučuje hodnotu v *uživatelském* parametru, který se předává v požadavku přes adresu URL. Řekněme například, že ve vašem prostředí je běžné, že pole pro uživatele obsahuje řetězec, který WAF zobrazí jako škodlivý obsah, takže ho zablokuje.  V tomto případě můžete v tomto případě vyloučit parametr uživatele tak, aby WAF nevyhodnotil cokoli v poli.

Následující rutina Azure PowerShell vyloučí parametr uživatele z vyhodnocení:

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "StartsWith" `
   -Selector "user"
```
Takže pokud je adresa URL `http://www.contoso.com/?user%281%29=fdafdasfda` předána do WAF, nevyhodnotí se řetězec **fdafdasfda**, ale přesto bude vyhodnocen uživatel s názvem parametru **%281 %29**. 

## <a name="waf-request-size-limits"></a>Omezení velikosti žádosti WAF



Firewall webových aplikací umožňuje konfigurovat omezení velikosti požadavků v rámci dolních a horních mezí. K dispozici jsou následující dvě konfigurace omezení velikosti:

- Pole Maximální velikost textu požadavku je zadáno v kilobajtech a řídí celkové omezení velikosti požadavku bez jakýchkoli nahrávání souborů. Toto pole může být v rozsahu od 1 do KB minimálně až 128-maximální hodnota KB. Výchozí hodnota velikosti textu požadavku je 128 KB.
- Pole limit pro nahrání souboru je zadané v MB a určuje maximální povolenou velikost nahrávání souborů. Toto pole může mít minimální hodnotu 1 MB a následující maximum:

   - 100 MB pro brány v1 Medium WAF
   - 500 MB pro velké WAF brány v1
   - 750 MB pro WAF brány v2 

 Výchozí hodnota pro limit nahrávání souborů je 100 MB.

WAF také nabízí konfigurovatelný ovladač pro zapnutí nebo vypnutí kontroly textu žádosti. Ve výchozím nastavení je kontrola textu žádosti povolena. Pokud je kontrola těla požadavku vypnutá, WAF nevyhodnotí obsah zprávy HTTP. V takových případech WAF nadále vynutil pravidla WAF u hlaviček, souborů cookie a identifikátorů URI. Pokud je kontrola těla žádosti vypnutá, pole Maximální velikost textu požadavku se nedá použít a nedá se nastavit. Vypnutí kontroly textu požadavku umožňuje odeslat zprávy větší než 128 KB do WAF, ale tělo zprávy není zkontrolováno pro ohrožení zabezpečení.

## <a name="next-steps"></a>Další kroky

Po nakonfigurování nastavení WAF se můžete dozvědět, jak zobrazit protokoly WAF. Další informace najdete v tématu [diagnostika Application Gateway](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).
