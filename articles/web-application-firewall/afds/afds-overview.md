---
title: Co je firewall webových aplikací Azure na frontách Azure?
description: Přečtěte si, jak Firewall webových aplikací Azure na službě Azure front-dveří chrání vaše webové aplikace před škodlivými útoky.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 05/22/2020
ms.author: victorh
ms.openlocfilehash: 0e3073c491b251a1dbb505d4656de9c6a1e0ac7f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048489"
---
# <a name="azure-web-application-firewall-on-azure-front-door"></a>Firewall webových aplikací Azure na frontách Azure

Firewall webových aplikací Azure (WAF) na frontách Azure poskytuje centralizovanou ochranu pro vaše webové aplikace. WAF chrání vaše webové služby před běžnými zneužitími a chybami zabezpečení. Udržuje vaše služba vysoce dostupná pro vaše uživatele a pomáhá splnit požadavky na dodržování předpisů.

WAF na předních dveřích je globální a centralizované řešení. Je nasazená na hraničních místech sítě Azure po celém světě. Webové aplikace s povolenou službou WAF kontrolují všechny příchozí žádosti doručené přes dvířka v hraniční síti. 

WAF zabraňuje škodlivým útokům blízko zdrojů útoku předtím, než vstoupí do vaší virtuální sítě. Globální ochranu získáte škálováním bez obětování výkonu. Zásady WAF jednoduše odkazují na libovolný profil front-dveří v rámci vašeho předplatného. Nová pravidla se dají nasadit během několika minut, takže můžete rychle reagovat na měnící se vzory hrozeb.

![Firewall webových aplikací Azure](../media/overview/wafoverview.png)

Přední dvířka Azure jsou [ve verzi Preview dvě nové SKU](../../frontdoor/standard-premium/overview.md): přední dveře Standard a SKU front-dveří Premium. WAF je nativně integrovaná s SKU front-dveří Premium s úplnými možnostmi. Pro skladovou jednotku Standard Standard je podporována pouze [vlastní pravidla](#custom-authored-rules) .

## <a name="waf-policy-and-rules"></a>Zásady a pravidla WAF

Můžete nakonfigurovat [zásady WAF](waf-front-door-create-portal.md) a přidružit tuto zásadu k jedné nebo více front-endu front-endy pro ochranu. Zásady WAF se skládají ze dvou typů pravidel zabezpečení:

- vlastní pravidla, která jsou autorem zákazníka.

- spravované sady pravidel, které jsou kolekcí předem nakonfigurovaných sad pravidel spravovaných Azure.

Pokud jsou přítomny obě, před zpracováním pravidel v sadě spravovaných pravidel se zpracují vlastní pravidla. Pravidlo se skládá z podmínky shody, priority a akce. Podporované typy akcí: ALLOW, BLOCK, LOG a REDIRECT. Můžete vytvořit plně přizpůsobené zásady, které vyhovují konkrétním požadavkům na ochranu aplikací, a to kombinováním spravovaných a vlastních pravidel.

Pravidla v rámci zásad jsou zpracovávána v pořadí podle priority. Priorita je jedinečné celé číslo, které definuje pořadí pravidel pro zpracování. Menší celočíselná hodnota znamená vyšší prioritu a tato pravidla se vyhodnocují před pravidly s vyšší celočíselnou hodnotou. Po porovnání pravidla se na žádost aplikuje odpovídající akce, která byla definována v pravidle. Po zpracování této shody se pravidla s nižšími prioritami nezpracují dále.

K webové aplikaci dodávané přes dvířka může být v jednu chvíli přidružená jenom jedna zásada WAF. Můžete ale mít konfiguraci front-dveří bez jakýchkoli zásad WAF, které jsou k němu přidružené. Pokud je přítomná zásada WAF, replikuje se na všechna naše hraniční umístění, aby se zajistila konzistence zásad zabezpečení po celém světě.

## <a name="waf-modes"></a>WAF režimy

Zásady WAF je možné nakonfigurovat tak, aby běžely v následujících dvou režimech:

- **Detekční režim:** Při spuštění v režimu detekce neprovádí WAF žádné jiné jiné akce než monitory a zaznamená požadavek a odpovídající WAF pravidlo do protokolů WAF. Diagnostiku protokolování můžete zapnout pro přední dveře. Když použijete portál, přejdete do části **Diagnostika** .

- **Režim prevence:** V režimu prevence převezme WAF zadanou akci, pokud požadavek odpovídá pravidlu. Pokud je nalezena shoda, nejsou vyhodnocována žádná další pravidla s nižší prioritou. V protokolech WAF se zaznamenávají i všechny odpovídající požadavky.

## <a name="waf-actions"></a>Akce WAF

WAF zákazníci můžou zvolit spuštění z jedné z akcí, když požadavek odpovídá podmínkám pravidla:

- **Povolení:**  Požadavek projde přes WAF a předává se back-end. Tato žádost nemůže blokovat žádná další pravidla s nižší prioritou.
- **Blok:** Požadavek je zablokován a WAF odešle odpověď klientovi bez předání žádosti do back-endu.
- **Protokol:**  Požadavek se zaznamená do protokolů WAF a WAF pokračuje v hodnocení pravidel s nižší prioritou.
- **Přesměrování:** WAF přesměruje požadavek na zadaný identifikátor URI. Zadaný identifikátor URI je nastavení úrovně zásad. Po nakonfigurování se do tohoto identifikátoru URI odešlou všechny požadavky, které odpovídají akci **přesměrování** .

## <a name="waf-rules"></a>Pravidla WAF

Zásady WAF můžou sestávat ze dvou typů pravidel zabezpečení – vlastní pravidla vytvořená zákazníkem a spravovaným rulesetsem, předem nakonfigurovanou sadou pravidel spravovaných pomocí Azure.

### <a name="custom-authored-rules"></a>Vlastní vytvořená pravidla

Vlastní pravidla WAF můžete nakonfigurovat následujícím způsobem:

- Seznam **povolených a blokovaných IP adres:** Můžete řídit přístup k webovým aplikacím na základě seznamu IP adres klientů nebo rozsahů IP adres. Podporují se typy adres IPv4 i IPv6. Tento seznam je možné nakonfigurovat tak, aby buď blokoval, nebo povolil tyto požadavky, kde zdrojová IP adresa odpovídá IP adrese v seznamu.

- **Geografické řízení přístupu:** Přístup k webovým aplikacím můžete řídit na základě kódu země, který je přidružený k IP adrese klienta.

- **Řízení přístupu na základě parametrů http:** Pro řetězcové shody v parametrech požadavků HTTP/HTTPS můžete základní pravidla.  Například řetězce dotazů, POST argumenty, identifikátor URI požadavku, Hlavička požadavku a text žádosti.

- **Řízení přístupu na základě metody požadavku:** Pravidla založená na metodě žádosti HTTP žádosti. Například GET, PUT nebo HEAD.

- **Omezení velikosti:** Pravidla můžete založit na délce určitých částí požadavku, jako je řetězec dotazu, identifikátor URI nebo text žádosti.

- **Pravidla omezující rychlost:** Pravidlem řízení sazeb je omezit neobvykle vysoký provoz z jakékoli IP adresy klienta. Prahovou hodnotu můžete nakonfigurovat pro počet webových požadavků povolených z IP adresy klienta během doby trvání 1 minuty. Toto pravidlo se liší od vlastního pravidla povolit/zablokování založeného na seznamu IP adres, které buď umožňuje všem žádostem z IP adresy klienta nebo blokovat všechny požadavky. Omezení přenosové rychlosti lze kombinovat s dalšími podmínkami shody, jako je například shoda parametru HTTP (S) pro detailní řízení míry.

### <a name="azure-managed-rule-sets"></a>Sady pravidel spravované v Azure

Sady pravidel spravované pomocí Azure poskytují snadný způsob, jak nasadit ochranu proti běžné sadě bezpečnostních hrozeb. Vzhledem k tomu, že tato RuleSets spravuje Azure, pravidla se aktualizují podle potřeby k ochraně před novými signaturami útoku. Sada výchozích pravidel spravovaná v Azure zahrnuje pravidla pro následující kategorie hrozeb:

- Skriptování mezi weby
- Útoky Java
- Zahrnutí místních souborů
- Útoky injektáže PHP
- Vzdálené spuštění příkazu
- Zahrnutí vzdáleného souboru
- Fixace relace
- Ochrana před útoky prostřednictvím injektáže SQL.
- Útočníci protokolu

Číslo verze výchozích sad pravidel se zvýší, když jsou do sady pravidel přidány nové podpisy útoků.
Výchozí sada pravidel je ve výchozím nastavení povolená v režimu detekce v zásadách WAF. Můžete zakázat nebo povolit jednotlivá pravidla v rámci výchozího pravidla nastavená tak, aby splňovala požadavky vaší aplikace. Můžete také nastavit konkrétní akce (povolení/blokování/přesměrování/protokol) na pravidlo.

Někdy možná budete muset vynechat určité atributy žádosti z WAF vyhodnocení. Běžným příkladem jsou vložené tokeny vložené službou Active Directory, které se používají pro ověřování. Můžete nakonfigurovat seznam vyloučení pro spravované pravidlo, skupinu pravidel nebo pro celou sadu pravidel.  

Výchozí akce je zablokovat. Vlastní pravidla se navíc dají nakonfigurovat ve stejných zásadách WAF, pokud chcete vynechat některá z předkonfigurovaných pravidel v sadě výchozích pravidel.

Vlastní pravidla se vždycky aplikují předtím, než se vyhodnotí pravidla v sadě výchozích pravidel. Pokud požadavek odpovídá vlastnímu pravidlu, použije se odpovídající akce pravidla. Požadavek je buď zablokován, nebo předán do back-endu. Nezpracují se žádná další vlastní pravidla ani pravidla v sadě výchozích pravidel. Můžete taky odebrat výchozí sadu pravidel ze zásad WAF.

### <a name="bot-protection-rule-set-preview"></a>Sada pravidel ochrany robota (Preview)

Můžete povolit spravovanou sadu pravidel ochrany robotů k přijetí vlastních akcí na žádostech ze známých kategorií bot. 

Podporují se tři kategorie bot: chybné, dobré a neznámé. Signatury robota jsou spravované a dynamicky aktualizované platformou WAF.

Chybný roboty zahrnuje roboty ze škodlivých IP adres a roboty, které si zazfalšována své identity. Škodlivé IP adresy se naúčtují z informačního kanálu Microsoft Threat Intelligence a aktualizují se každou hodinu. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) využívá Microsoft Threat Intelligence a používá ho víc služeb, včetně Azure Security Center.

Dobrá roboty zahrnovat ověřené vyhledávací stroje. Mezi neznámé kategorie patří další skupiny robotů, které se identifikovaly jako roboty. Například analyzátor trhu, načítající informační kanály a agenti sběru dat. 

Neznámý roboty jsou klasifikovány prostřednictvím publikovaných uživatelských agentů bez dalšího ověření. Můžete nastavit vlastní akce pro blokování, povolení, protokolování nebo přesměrování pro různé typy roboty.

![Sada pravidel ochrany bot](../media/afds-overview/botprotect2.png)

> [!IMPORTANT]
> Sada pravidel ochrany robota je aktuálně ve verzi Public Preview a poskytuje verzi Preview smlouvy o úrovni služeb. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti.  Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pokud je zapnutá ochrana robota, příchozí požadavky, které odpovídají pravidlům robota, se protokolují do protokolu FrontdoorWebApplicationFirewallLog. K protokolům WAF můžete přistupovat z účtu úložiště, centra událostí nebo Log Analytics.

## <a name="configuration"></a>Konfigurace

Všechny typy pravidel WAF můžete nakonfigurovat a nasadit pomocí Azure Portal, rozhraní REST API, šablon Azure Resource Manager a Azure PowerShell.

## <a name="monitoring"></a>Monitorování

Monitorování pro WAF ve front-dveřích je integrované s Azure Monitor pro sledování výstrah a snadné monitorování trendů provozu.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [bráně firewall webových aplikací v Azure Application Gateway](../ag/ag-overview.md)