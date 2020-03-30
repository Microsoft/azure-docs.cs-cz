---
title: Omezení velikosti a seznamy vyloučení brány firewall webových aplikací v Azure Application Gateway – portál Azure
description: Tento článek obsahuje informace o omezení velikosti webových aplikací firewall a vyloučení seznamy konfigurace v application gateway s portálem Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/20/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 7244788bbc7431c7f26363b2852babb72d5697e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77526786"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Omezení velikosti a seznamy vyloučení brány firewall webových aplikací

Brána webových aplikací Azure Application Gateway (WAF) poskytuje ochranu webových aplikací. Tento článek popisuje omezení velikosti požadavku WAF a konfiguraci seznamů vyloučení. Tato nastavení jsou umístěna v zásadách WAF přidružených k bráně aplikace. Další informace o zásadách WAF najdete v [tématu Azure Web Application Firewall v bráně aplikací Azure](ag-overview.md) a Vytvoření zásad brány brány [webových aplikací pro aplikační bránu.](create-waf-policy-ag.md)

## <a name="waf-exclusion-lists"></a>Seznamy vyloučení WAF

![Požadavky na omezení velikosti](../media/application-gateway-waf-configuration/waf-policy.png)

Seznamy vyloučení WAF umožňují vynechat určité atributy požadavku z vyhodnocení WAF. Běžným příkladem jsou vložené tokeny služby Active Directory, které se používají pro pole ověřování nebo hesla. Tyto atributy jsou náchylné k obsahovat speciální znaky, které mohou vyvolat falešně pozitivní z pravidel WAF. Jakmile je atribut přidán do seznamu vyloučení WAF, nepovažuje jej žádné nakonfigurované a aktivní pravidlo WAF. Seznamy vyloučení mají globální rozsah.

Následující atributy lze přidat do seznamů vyloučení podle názvu. Hodnoty zvoleného pole nejsou vyhodnoceny podle pravidel WAF, ale jejich názvy stále jsou (viz příklad 1 níže, hodnota hlavičky User-Agent je vyloučena z vyhodnocení WAF). Seznamy vyloučení odeberou kontrolu hodnoty pole.

* Hlavičky požadavku
* Žádost o soubory cookie
* Název atributu požadavku (args) lze přidat jako prvek vyloučení, například:

   * Název pole formuláře
   * Entita XML
   * Entita JSON
   * Řetězec dotazu URL args

Můžete zadat přesnou shodu záhlaví, těla, souboru cookie nebo řetězce dotazu.  Nebo můžete volitelně zadat částečné shody. Pravidla vyloučení mají globální rozsah a vztahují se na všechny stránky a všechna pravidla.

Následují podporované operátory kritérií shody:

- **Rovná se**: Tento operátor se používá pro přesnou shodu. Jako příklad pro výběr hlavičky s názvem **bearerToken**použijte operátor equals s voličem nastaveným jako **bearerToken**.
- **Začíná na**: Tento operátor odpovídá všem polím, která začínají zadanou hodnotou voliče.
- **Končí :** Tento operátor porovnává všechna pole požadavku, která končí se zadanou hodnotou voliče.
- **Obsahuje**: Tento operátor odpovídá všem polím požadavku, která obsahují zadanou hodnotu voliče.
- **Rovná se všem**: Tento operátor odpovídá všem polím požadavku. * bude hodnota voliče.

Ve všech případech je porovnávání malá a velká písmena a regulární výraz není povolen jako voliče.

> [!NOTE]
> Další informace a nápovědu k řešení potíží naleznete v [tématu WAF troubleshooting](web-application-firewall-troubleshoot.md).

### <a name="examples"></a>Příklady

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Následující příklady ukazují použití vyloučení.

#### <a name="example-1"></a>Příklad 1

V tomto příkladu chcete vyloučit hlavičku uživatelského agenta. Hlavička požadavku uživatelského agenta obsahuje charakteristický řetězec, který umožňuje partnerům síťového protokolu identifikovat typ aplikace, operační systém, dodavatele softwaru nebo verzi softwaru ožádajíného softwarového uživatelského agenta. Další informace naleznete [v tématu User-Agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent).

Může existovat libovolný počet důvodů zakázat vyhodnocení této hlavičky. Tam by mohl být řetězec, který WAF vidí a předpokládá, že je škodlivý. Například klasický útok SQL "x=x" v řetězci. V některých případech to může být legitimní provoz. Takže možná budete muset vyloučit tuto hlavičku z hodnocení WAF.

Následující rutina prostředí Azure PowerShell vyloučí hlavičku uživatelského agenta z vyhodnocení:

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```
#### <a name="example-2"></a>Příklad 2

Tento příklad vylučuje hodnotu v *parametru uživatele,* který je předán v požadavku prostřednictvím adresy URL. Řekněme například, že je ve vašem prostředí běžné, že uživatelské pole obsahuje řetězec, který waf zobrazí jako škodlivý obsah, takže jej blokuje.  V tomto případě můžete vyloučit parametr uživatele, aby WAF nevyhodnotil nic v poli.

Následující rutina prostředí Azure PowerShell vyloučí uživatelský parametr z vyhodnocení:

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "StartsWith" `
   -Selector "user"
```
Takže pokud `http://www.contoso.com/?user%281%29=fdafdasfda` je adresa URL předána WAF, nebude hodnotit řetězec **fdafdasfda**, ale bude stále hodnotit název parametru **uživatele%281%29**. 

## <a name="waf-request-size-limits"></a>Omezení velikosti požadavku WAF



Brána firewall webových aplikací umožňuje konfigurovat omezení velikosti požadavků v dolní a horní meze. K dispozici jsou následující dvě konfigurace omezení velikosti:

- Maximální velikost těla požadavku pole je určena v kilobajtech a řídí celkový limit velikosti požadavku bez nahrání souborů. Toto pole může být v rozsahu od minimální hodnoty 1 KB do 128 KB. Výchozí hodnota pro velikost těla požadavku je 128 KB.
- Pole limitu pro odeslání souboru je určeno v MB a určuje maximální povolenou velikost odesílání souborů. Toto pole může mít minimální hodnotu 1 MB a následující maxima:

   - 100 MB pro brány v1 Medium WAF
   - 500 MB pro v1 Velké WAF brány
   - 750 MB pro brány v2 WAF 

 Výchozí hodnota pro limit pro odesílání souborů je 100 MB.

WAF také nabízí konfigurovatelný knoflík pro zapnutí nebo vypnutí kontroly těla požadavku. Ve výchozím nastavení je povolena kontrola těla požadavku. Pokud je kontrola těla požadavku vypnutá, WAF nevyhodnocuje obsah textu zprávy HTTP. V takových případech WAF nadále vynucuje pravidla WAF pro záhlaví, soubory cookie a identifikátor URI. Pokud je kontrola těla požadavku vypnutá, pole maximální velikosti těla požadavku není použitelné a nelze je nastavit. Vypnutí kontroly těla požadavku umožňuje zprávy větší než 128 KB, které mají být odeslány do WAF, ale tělo zprávy není kontrolována na chyby zabezpečení.

## <a name="next-steps"></a>Další kroky

Po konfiguraci nastavení WAF se můžete dozvědět, jak zobrazit protokoly WAF. Další informace naleznete v tématu [Diagnostika aplikační brány](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).
