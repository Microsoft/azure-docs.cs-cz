---
title: Omezení velikosti pro požadavek webové aplikace brány firewall a seznamy vyloučení ve službě Azure Application Gateway – Azure portal
description: Tento článek obsahuje informace o omezení velikosti pro požadavek webové aplikace brány firewall a seznamy vyloučení konfigurace ve službě Application Gateway pomocí webu Azure portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 5/15/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 5ddcdeca41e2f21fa27db25f7e0721c7ef87e491
ms.sourcegitcommit: 3675daec6c6efa3f2d2bf65279e36ca06ecefb41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65620276"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Omezení velikosti pro požadavek webové aplikace brány firewall a seznamy vyloučení

Firewall webových aplikací (WAF) Azure Application Gateway chrání webové aplikace. Tento článek popisuje WAF žádost o velikosti omezení a vyloučení jsou uvedeny konfigurace.

## <a name="waf-request-size-limits"></a>Omezení velikosti žádostí o WAF

![Požádat o omezení velikosti](media/application-gateway-waf-configuration/waf-requestsizelimit.png)

Firewall webových aplikací umožňuje konfigurovat omezení velikosti požadavku v rámci dolní a horní hranice. K dispozici jsou následující dvě velikost omezení konfigurace:

- Pole velikost textu maximální požadavku je zadán v kilobajtech a ovládací prvky, které celkový limit velikosti žádosti o vyloučení všechny soubory nahraje. Toto pole musí být v rozsahu 1 KB minimální do maximální hodnota 128 KB. Výchozí hodnota pro velikost textu požadavku je 128 KB.
- Limit pole pro uložení souborů se uvádí v MB a řídí maximální velikost povolenou nahrávání. Toto pole může mít minimální hodnotu 1 MB a maximálně 500 MB pro velké SKU instance SKU médium obsahuje maximálně 100 MB. Výchozí hodnota pro limitu pro nahrávání souborů je 100 MB.

WAF také nabízí Konfigurovatelný ovladače k zapnutí nebo vypnutí kontroly těla požadavku. Ve výchozím nastavení povolení kontroly těla požadavku. Pokud kontrola tělo žádosti je vypnutý, WAF nevyhodnocuje obsah zprávy HTTP. V takových případech WAF nadále vynucovat pravidla firewallu webových aplikací na záhlaví, soubory cookie a identifikátor URI. Pokud kontrola tělo žádosti je vypnutý, maximální žádost subjektu velikost pole nelze použít a nelze nastavit. Vypnutí kontroly tělo požadavku umožňuje zprávy větší než 128 KB k odeslání do WAF, ale tělo zprávy není zkontroloval ohrožení zabezpečení.

## <a name="waf-exclusion-lists"></a>Seznamy vyloučení WAF

![waf-exclusion.png](media/application-gateway-waf-configuration/waf-exclusion.png)

Seznamy vyloučení WAF umožňují vynechat určité atributy žádosti ze zkušební verze WAF. Běžným příkladem je že vložen tokeny, které se používají pro ověřování nebo pole s heslem služby Active Directory. Tyto atributy jsou náchylné k obsahovat speciální znaky, které můžou aktivovat falešně pozitivní z pravidla firewallu webových aplikací. Po přidání atributu do seznamu vyloučení WAF se nepovažuje za žádným pravidlem nakonfigurovaná a aktivní WAF. V oboru jsou globální seznamy vyloučení.

Následující atributy mohou být přidány do seznamu vyloučení:

* Hlavičky žádosti
* Soubory cookie požadavků
* Název atributu požadavku (argumenty)

   * Vícedílný dat formuláře
   * XML
   * JSON
   * Adresa URL dotazu argumentů

Můžete určit přesné požadavek záhlaví, textu, soubor cookie nebo atributu shodu řetězce dotazu.  Nebo můžete volitelně zadat částečné shody. Vyloučení je vždycky aktivní pole hlavičky, nikdy ne na jeho hodnotu. Pravidla vyloučení jsou globální v oboru a platí pro všechny stránky a všechna pravidla.

Tady jsou operátory porovnání podporovaných kritéria:

- **Se rovná**:  Tento operátor se používá pro přesnou shodu. Jako příklad pro výběr záhlaví s názvem **bearerToken**, operátoru rovná pomocí modulu pro výběr nastavit jako **bearerToken**.
- **Začíná**: Tento operátor odpovídá všechna pole, která začínají hodnotou zadaný selektor.
- **Končí**:  Tento operátor odpovídá všechna pole žádosti, které končí hodnotu zadaný selektor.
- **Obsahuje**: Tento operátor odpovídá všechna pole žádosti, které obsahují hodnotu zadaný selektor.
- **Rovná se žádné**: Tento operátor odpovídá všechna pole požadavku. * bude hodnota selektor.

Ve všech případech vyhledávání nejsou rozlišována velká a malá písmena a regulárního výrazu nejsou povoleny jako selektorů.

### <a name="examples"></a>Příklady

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Následující příklady ukazují použití vyloučení.

### <a name="example-1"></a>Příklad 1

V tomto příkladu budete chtít vyloučit záhlaví user-agent. Hlavičky uživatelského agenta žádosti obsahuje charakteristické řetězec, který umožňuje síť partnerské uzly protokolu k identifikaci typu aplikace, operační systém, výrobce softwaru nebo verze softwaru agenta uživatele žádost o software. Další informace najdete v tématu [User-Agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent).

Může existovat libovolný počet důvodů, proč zakázat vyhodnocení této hlavičky. Je možné řetězec, který WAF se zobrazí a předpokládá, že se zlými úmysly. Například klasické útoku SQL "x = x" v řetězci. V některých případech to může být legitimní provoz. Proto budete muset vyloučit tuto hlavičku ze zkušební verze WAF.

Následující rutiny Azure Powershellu vyloučí záhlaví user-agent ze zkušební verze:

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```

### <a name="example-2"></a>Příklad 2

Tento příklad vylučuje hodnoty v *uživatele* parametr, který je předán v požadavku prostřednictvím adresy URL. Řekněme například, že je běžné v prostředí pro uživatele pole obsahuje řetězec, který WAF zobrazení jako škodlivý obsah, tak se zablokuje.  Tento parametr můžete v tomto případě vyloučit tak, aby WAF nevyhodnocuje cokoli, co je v poli.

Následující rutiny Azure Powershellu nezahrnuje tento parametr se ze zkušební verze:

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "user"
```
Takže když adresa URL **http://www.contoso.com/?user=fdafdasfda** je předán do WAF, nevyhodnotí řetězec **fdafdasfda**.

## <a name="next-steps"></a>Další postup

Po dokončení konfigurace nastavení WAF se dozvíte, jak si chcete zobrazit protokoly WAF. Další informace najdete v tématu [diagnostice služby Application Gateway](application-gateway-diagnostics.md#diagnostic-logging).
