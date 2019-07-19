---
title: Omezení velikosti požadavků firewallu webových aplikací a seznamy vyloučení v Azure Application Gateway-Azure Portal
description: Tento článek obsahuje informace o omezeních velikosti požadavků firewallu webových aplikací a seznamech vyloučení v Application Gateway s Azure Portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 7/17/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 9e9472fbcd01cf40204063174b159638369d7429
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326672"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Omezení velikosti a seznamů vyloučení požadavků firewallu webových aplikací

Firewall webových aplikací (WAF) služby Azure Application Gateway poskytuje ochranu pro webové aplikace. Tento článek popisuje WAF omezení velikosti požadavků a seznam vyloučení.

## <a name="waf-request-size-limits"></a>Omezení velikosti žádosti WAF

![Omezení velikosti požadavku](media/application-gateway-waf-configuration/waf-requestsizelimit.png)

Firewall webových aplikací umožňuje konfigurovat omezení velikosti požadavků v rámci dolních a horních mezí. K dispozici jsou následující dvě konfigurace omezení velikosti:

- Pole Maximální velikost textu požadavku je zadáno v kilobajtech a řídí celkové omezení velikosti požadavku bez jakýchkoli nahrávání souborů. Toto pole může být v rozsahu od 1 do KB minimálně až 128-maximální hodnota KB. Výchozí hodnota velikosti textu požadavku je 128 KB.
- Pole limit pro nahrání souboru je zadané v MB a určuje maximální povolenou velikost nahrávání souborů. V tomto poli může být minimální hodnota 1 MB a maximálně 500 MB pro instance velkých SKU, přičemž střední SKU má maximálně 100 MB. Výchozí hodnota pro limit nahrávání souborů je 100 MB.

WAF také nabízí konfigurovatelný ovladač pro zapnutí nebo vypnutí kontroly textu žádosti. Ve výchozím nastavení je kontrola textu žádosti povolena. Pokud je kontrola těla požadavku vypnutá, WAF nevyhodnotí obsah zprávy HTTP. V takových případech WAF nadále vynutil pravidla WAF u hlaviček, souborů cookie a identifikátorů URI. Pokud je kontrola těla žádosti vypnutá, pole Maximální velikost textu požadavku se nedá použít a nedá se nastavit. Vypnutí kontroly textu požadavku umožňuje odeslat zprávy větší než 128 KB do WAF, ale tělo zprávy není zkontrolováno pro ohrožení zabezpečení.

## <a name="waf-exclusion-lists"></a>Seznamy vyloučení WAF

![waf-exclusion.png](media/application-gateway-waf-configuration/waf-exclusion.png)

Seznamy vyloučení WAF umožňují vynechat určité atributy žádostí z vyhodnocení WAF. Běžným příkladem jsou vložené tokeny služby Active Directory, které se používají pro pole ověřování nebo hesla. Tyto atributy jsou náchylné k zahrnutí speciálních znaků, které mohou aktivovat falešně pozitivní hodnoty z pravidel WAF. Po přidání atributu do seznamu vyloučení WAF se tento atribut nepovažuje za žádné nakonfigurované a aktivní WAF pravidlo. Seznamy vyloučení jsou globální v oboru.

Následující atributy lze přidat do seznamů vyloučení podle názvu. Hodnoty zvoleného pole nejsou vyhodnoceny proti pravidlům WAF, ale jejich názvy jsou stále (viz příklad 1 níže, hodnota hlavičky uživatelského agenta je vyloučena z vyhodnocení WAF). Seznamy vyloučení odstraňují kontrolu hodnoty pole.

* Hlavičky požadavku
* Soubory cookie požadavků
* Název atributu žádosti (args) se dá přidat jako element vyloučení, třeba:

   * Název pole formuláře
   * Entita XML
   * Entita JSON
   * Argumenty řetězce dotazu adresy URL

Můžete zadat přesně takovou hlavičku požadavku, tělo, soubor cookie nebo atribut řetězce dotazu.  Případně můžete volitelně zadat částečné shody. Pravidla vyloučení jsou globální v oboru a platí pro všechny stránky a všechna pravidla.

Následují podporované operátory kritérií shody:

- **Se rovná**:  Tento operátor se používá pro přesnou shodu. Jako příklad pro výběr záhlaví s názvem **bearerToken**použijte operátor Equals se sadou selektoru jako **bearerToken**.
- **Začíná**: Tento operátor odpovídá všem polím, která začínají zadanou hodnotou selektoru.
- **Končí**:  Tento operátor odpovídá všem polím žádosti, která končí zadanou hodnotou selektoru.
- **Obsahuje**: Tento operátor odpovídá všem polím požadavku, která obsahují zadanou hodnotu selektoru.
- **Je rovno**: Tento operátor odpovídá všem polím žádosti. * bude hodnota selektoru.

Ve všech případech porovnávání nerozlišuje velká a malá písmena a regulární výraz nejsou povoleny jako selektory.

> [!NOTE]
> Další informace a nápovědu k řešení potíží najdete v tématu [řešení potíží s WAF](web-application-firewall-troubleshoot.md).

### <a name="examples"></a>Příklady

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Následující příklady ukazují použití vyloučení.

### <a name="example-1"></a>Příklad 1

V tomto příkladu chcete vyloučit hlavičku User-Agent. Hlavička požadavku User-Agent obsahuje charakteristický řetězec, který umožňuje partnerům síťového protokolu identifikovat typ aplikace, operační systém, výrobce softwaru nebo verzi softwaru žádajícího softwarového agenta. Další informace najdete v tématu [User-Agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent).

Je možné zakázat vyhodnocování této hlavičky z libovolného počtu důvodů. Může se jednat o řetězec, který WAF uvidí a předpokládá, že je škodlivý. Například klasický útok SQL "x = x" v řetězci. V některých případech to může být legitimní provoz. Proto může být nutné vyřadit tuto hlavičku z WAF vyhodnocení.

Následující rutina Azure PowerShell vyloučí hlavičku User-Agent z vyhodnocení:

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```

### <a name="example-2"></a>Příklad 2

Tento příklad vylučuje hodnotu v *uživatelském* parametru, který se předává v požadavku přes adresu URL. Řekněme například, že ve vašem prostředí je běžné, že pole pro uživatele obsahuje řetězec, který WAF zobrazí jako škodlivý obsah, takže ho zablokuje.  V tomto případě můžete v tomto případě vyloučit parametr uživatele tak, aby WAF nevyhodnotil cokoli v poli.

Následující rutina Azure PowerShell vyloučí parametr uživatele z vyhodnocení:

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "StartsWith" `
   -Selector "user"
```
Takže pokud je adresa **http://www.contoso.com/?user%281%29=fdafdasfda** URL předána do WAF, nevyhodnotí se řetězec **fdafdasfda**, ale přesto bude vyhodnocen uživatel s názvem parametru **% 281% 29**. 

## <a name="next-steps"></a>Další kroky

Po nakonfigurování nastavení WAF se můžete dozvědět, jak zobrazit protokoly WAF. Další informace najdete v tématu [diagnostika Application Gateway](application-gateway-diagnostics.md#diagnostic-logging).
