---
title: Co je firewall webových aplikací Azure na frontách Azure?
description: Přečtěte si, jak Firewall webových aplikací Azure na službě Azure front-dveří chrání vaše webové aplikace před škodlivými útoky.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: overview
ms.date: 11/23/2019
ms.author: victorh
ms.openlocfilehash: b646035f6a952f679059abab86d94179f447f9ff
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406209"
---
# <a name="azure-web-application-firewall-on-azure-front-door"></a>Firewall webových aplikací Azure na frontách Azure

Firewall webových aplikací (WAF) Azure na frontách Azure poskytuje centralizovanou ochranu pro vaše webové aplikace, které jsou globálně dodávány pomocí služby Azure front-dveří. Je navržený a provozovaný tak, aby chránil vaše webové služby před běžným zneužitím a ohrožením zabezpečení, udržoval vaše služby vysoce dostupné pro uživatele a navíc pomáhal splnit požadavky na dodržování předpisů.

WAF na předních dveřích je globální a centralizované řešení. Je nasazená na hraničních místech sítě Azure po celém světě a každý příchozí požadavek na webovou aplikaci s podporou WAF, která se dodává přes dveře, se kontroluje v hraniční síti. To umožňuje WAF zabránit škodlivým útokům blízko zdrojů útoků, než vstoupí do vaší virtuální sítě, a nabízí globální ochranu ve velkém měřítku, aniž by došlo k obětování výkonu. Zásady WAF můžete snadno propojit s libovolným profilem front-dveří v rámci předplatného a nová pravidla je možné nasadit během několika minut. můžete tak rychle reagovat na měnící se vzory hrozeb.

![Firewall webových aplikací Azure](../media/overview/wafoverview.png)

## <a name="waf-policy-and-rules"></a>Zásady a pravidla WAF

Můžete nakonfigurovat zásady WAF a přidružit tuto zásadu k jedné nebo více front-endu front-endy pro ochranu. Zásady WAF se skládají ze dvou typů pravidel zabezpečení:

- vlastní pravidla, která jsou autorem zákazníka.

- spravované sady pravidel, které jsou kolekcí předem nakonfigurovaných sad pravidel spravovaných Azure.

Pokud jsou přítomny obě, před zpracováním pravidel v sadě spravovaných pravidel se zpracují vlastní pravidla. Pravidlo se skládá z podmínky shody, priority a akce. Podporované typy akcí: ALLOW, BLOCK, LOG a REDIRECT. Můžete vytvořit plně přizpůsobené zásady, které vyhovují konkrétním požadavkům na ochranu aplikací, a to kombinováním spravovaných a vlastních pravidel.

Pravidla v rámci zásad se zpracovávají v pořadí podle priority, kde priorita je jedinečné celé číslo, které definuje pořadí zpracovávaných pravidel. Menší celočíselná hodnota znamená vyšší prioritu a ta se vyhodnotí před pravidly s vyšší celočíselnou hodnotou. Po porovnání pravidla se na žádost aplikuje odpovídající akce, která byla definována v pravidle. Po zpracování této shody se pravidla s nižšími prioritami nezpracují dále.

K webové aplikaci dodávané přes dvířka může být v jednu chvíli přidružená jenom jedna zásada WAF. Můžete ale mít konfiguraci front-dveří bez jakýchkoli zásad WAF, které jsou k němu přidružené. Pokud jsou přítomné zásady WAF, replikují se do všech našich hraničních umístění, aby se zajistila konzistence v zásadách zabezpečení po celém světě.

## <a name="waf-modes"></a>WAF režimy

Zásady WAF je možné nakonfigurovat tak, aby běžely v následujících dvou režimech:

- **Detekční režim:** Při spuštění v režimu detekce neprovádí WAF žádné jiné jiné akce než monitory a zaznamená požadavek a odpovídající WAF pravidlo na WAF protokoly. Diagnostiku protokolování pro přední dvířka můžete zapnout (Pokud používáte portál, můžete to dosáhnout tak, že v Azure Portal v části **Diagnostika** ).

- **Režim prevence:** Když je konfigurace spuštěna v režimu prevence, WAF převezme zadanou akci, pokud požadavek odpovídá pravidlu a pokud je nalezena shoda, nevyhodnotí se žádná další pravidla s nižší prioritou. V protokolech WAF se zaznamenávají i všechny odpovídající požadavky.

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

- Seznam **povolených a blokovaných IP adres:** Můžete nakonfigurovat vlastní pravidla pro řízení přístupu k webovým aplikacím na základě seznamu IP adres klientů nebo rozsahů IP adres. Podporují se typy adres IPv4 i IPv6. Tento seznam je možné nakonfigurovat tak, aby buď blokoval, nebo povolil tyto požadavky, kde zdrojová IP adresa odpovídá IP adrese v seznamu.

- **Geografické řízení přístupu:** Můžete nakonfigurovat vlastní pravidla pro řízení přístupu k webovým aplikacím na základě kódu země přidruženého k IP adrese klienta.

- **Řízení přístupu na základě parametrů http:** Můžete nakonfigurovat vlastní pravidla na základě řetězců, které odpovídají parametrům požadavků HTTP/HTTPS, jako jsou řetězce dotazů, POST argumenty, identifikátor URI požadavku, Hlavička požadavku a text žádosti.

- **Řízení přístupu na základě metody požadavku:** Vlastní pravidla můžete nakonfigurovat na základě metody požadavku HTTP, jako je například GET, PUT nebo HEAD.

- **Omezení velikosti:** Můžete nakonfigurovat vlastní pravidla na základě délek specifických částí požadavku, jako je řetězec dotazu, identifikátor URI nebo text žádosti.

- **Pravidla omezující rychlost:** Pravidlem řízení sazeb je omezit neobvykle vysoký provoz z jakékoli IP adresy klienta. Prahovou hodnotu můžete nakonfigurovat pro počet webových požadavků povolených z IP adresy klienta během doby trvání 1 minuty. To se liší od vlastního pravidla povolit/zablokování založeného na seznamu IP adres, které buď povolí všechny požadavky nebo zablokuje všechny žádosti z IP adresy klienta. Omezení rychlosti lze kombinovat s dalšími podmínkami shody, jako jsou například parametry HTTP (S) odpovídající pro detailní řízení sazeb.

### <a name="azure-managed-rule-sets"></a>Sady pravidel spravované v Azure

Sady pravidel spravované pomocí Azure poskytují snadný způsob, jak nasadit ochranu proti běžné sadě bezpečnostních hrozeb. Vzhledem k tomu, že tato RuleSets spravuje Azure, pravidla se aktualizují podle potřeby k ochraně před novými signaturami útoku. Ve verzi Public Preview sada výchozích pravidel spravovaná v Azure zahrnuje pravidla proti těmto kategoriím hrozeb:

- Skriptování mezi weby
- Útoky Java
- Zahrnutí místních souborů
- Útoky injektáže PHP
- Vzdálené spuštění příkazu
- Zahrnutí vzdálených souborů
- Fixace relace
- Ochrana před útoky prostřednictvím injektáže SQL.
- Útočníci protokolu

Číslo verze výchozí sady pravidel se zvýší, když budou do sady pravidel přidány nové podpisy útoků.
Výchozí sada pravidel je ve výchozím nastavení povolená v režimu detekce v zásadách WAF. Můžete zakázat nebo povolit jednotlivá pravidla v rámci výchozího pravidla nastavená tak, aby splňovala požadavky vaší aplikace. Můžete také nastavit konkrétní akce (povolení/blokování/přesměrování/protokol) na pravidlo. Výchozí akce je zablokovat. Vlastní pravidla se navíc dají nakonfigurovat ve stejné zásadě WAF, pokud chcete vynechat některá z předkonfigurovaných pravidel v sadě výchozích pravidel.
Vlastní pravidla se vždycky aplikují předtím, než se vyhodnotí pravidla v sadě výchozích pravidel. Pokud požadavek odpovídá vlastnímu pravidlu, použije se odpovídající akce pravidla a požadavek se buď zablokuje, nebo projde back-endu bez vyvolání jakýchkoli dalších vlastních pravidel nebo pravidel v sadě výchozích pravidel. Kromě toho máte možnost odebrat ze zásad WAF výchozí sadu pravidel.


### <a name="bot-protection-rule-set-preview"></a>Sada pravidel ochrany robota (Preview)

Sada pravidel spravované ochrany robotů může být povolená pro WAF, aby mohla přijímat vlastní akce na žádostech ze známých kategorií bot. Podporují se tři kategorie bot: Bad roboty, dobrý roboty a neznámý roboty. Signatury robota jsou spravované a dynamicky aktualizované platformou WAF. Škodlivé IP adresy pro chybně roboty jsou zdroje z kanálu Microsoft Threat Intelligence. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) využívá Microsoft Threat Intelligence a používá ho víc služeb, včetně Azure Security Center. Dobrá roboty zahrnovat ověřené vyhledávací stroje. Mezi neznámé kategorie patří další skupiny robotů. U různých typů roboty můžete nastavit vlastní akce pro blokování, povolení, protokolování nebo přesměrování.

![Sada pravidel ochrany bot](../media/afds-overview/botprotect2.png)

> [!IMPORTANT]
> Sada pravidel ochrany robota je aktuálně ve verzi Public Preview a poskytuje verzi Preview smlouvy o úrovni služeb. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti.  Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pokud je zapnutá ochrana robota, příchozí požadavky, které odpovídají pravidlům robota, se protokolují do protokolu FrontdoorWebApplicationFirewallLog. K protokolům WAF můžete přistupovat z účtu úložiště, centra událostí nebo Log Analytics.

## <a name="configuration"></a>Konfigurace

Konfigurace a nasazení všech typů pravidel WAF je plně podporované pomocí Azure Portal, rozhraní REST API, šablon Azure Resource Manager a Azure PowerShell.

## <a name="monitoring"></a>Monitorování

Monitorování pro WAF ve front-dveřích je integrované s Azure Monitor pro sledování výstrah a snadné monitorování trendů provozu.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [bráně firewall webových aplikací v Azure Application Gateway](../ag/ag-overview.md)
