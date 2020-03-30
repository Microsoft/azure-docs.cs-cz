---
title: Co je brána firewall webových aplikací Azure na Azure Front Door?
description: Zjistěte, jak brána firewall webových aplikací Azure ve službě Azure Front Door chrání vaše webové aplikace před škodlivými útoky.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: overview
ms.date: 02/01/2020
ms.author: victorh
ms.openlocfilehash: c8ff1849668d5effe15b6c25d00f3965a17b8e3e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77915635"
---
# <a name="azure-web-application-firewall-on-azure-front-door"></a>Brána firewall webových aplikací Azure na předních dveřích Azure

Azure Web Application Firewall (WAF) na Azure Front Door poskytuje centralizovanou ochranu pro vaše webové aplikace. WAF chrání vaše webové služby před běžnými zneužitími a chybami zabezpečení. Udržuje vaši službu vysoce dostupnou pro vaše uživatele a pomáhá vám splnit požadavky na dodržování předpisů.

WAF na předních dveřích je globální a centralizované řešení. Nasadí se na okrajových místech Azure po celém světě. Webové aplikace s podporou WAF kontrolují každý příchozí požadavek dodaný společností Front Door na okraji sítě. 

WAF zabraňuje škodlivým útokům v blízkosti zdrojů útoku, než vstoupí do vaší virtuální sítě. Získáte globální ochranu ve velkém měřítku, aniž byste obětovali výkon. Zásady WAF snadno odkazují na libovolný profil předních dveří ve vašem předplatném. Nová pravidla lze nasadit během několika minut, takže můžete rychle reagovat na měnící se vzorce hrozeb.

![Brána firewall pro webové aplikace Azure](../media/overview/wafoverview.png)

## <a name="waf-policy-and-rules"></a>Zásady a pravidla WAF

Můžete nakonfigurovat zásady WAF a přidružit tuto zásadu k jednomu nebo více front-endům předních dveří pro ochranu. Zásada WAF se skládá ze dvou typů pravidel zabezpečení:

- vlastní pravidla, která jsou vytvořena zákazníkem.

- spravované sady pravidel, které jsou kolekcí předem nakonfigurované sady pravidel spravované azurem.

Pokud jsou k dispozici obě, vlastní pravidla jsou zpracovány před zpracováním pravidel ve spravované sadě pravidel. Pravidlo je tvořeno podmínkou shody, prioritou a akcí. Podporované typy akcí jsou: POVOLIT, BLOKOVAT, PROTOKOLOVAT a PŘESMĚROVAT. Kombinací spravovaných a vlastních pravidel můžete vytvořit plně přizpůsobenou zásadu, která splňuje vaše specifické požadavky na ochranu aplikací.

Pravidla v rámci zásady jsou zpracována v pořadí podle priority. Priorita je jedinečné celé číslo, které definuje pořadí pravidel ke zpracování. Menší celá hodnota označuje vyšší prioritu a tato pravidla jsou vyhodnocována před pravidly s vyšší celá hodnota. Jakmile je pravidlo spárováno, odpovídající akce, která byla definována v pravidle, se použije na požadavek. Jakmile je taková shoda zpracována, pravidla s nižšími prioritami nejsou dále zpracována.

Webová aplikace dodaná společností Front Door může mít současně přidruženou pouze jednu zásadu WAF. Můžete však mít konfiguraci předních dveří bez jakýchkoli zásad WAF, které jsou s ní spojeny. Pokud je k dispozici zásada WAF, je replikována do všech našich hraničních umístění, aby byla zajištěna konzistentní zásady zabezpečení po celém světě.

## <a name="waf-modes"></a>Režimy WAF

Zásady WAF lze nakonfigurovat tak, aby běžely v následujících dvou režimech:

- **Detekční režim:** Při spuštění v režimu detekce waf nebere žádné jiné akce než monitoruje a protokoluje požadavek a jeho odpovídající WAF pravidlo waf protokoly. Můžete zapnout diagnostiku protokolování pro přední dveře. Při použití portálu přejděte do části **Diagnostika.**

- **Režim prevence:** V režimu prevence waf provede zadanou akci, pokud požadavek odpovídá pravidlu. Pokud je nalezena shoda, jsou vyhodnocována žádná další pravidla s nižší prioritou. Všechny odpovídající požadavky jsou také zaznamenány v protokolech WAF.

## <a name="waf-actions"></a>Akce WAF

Zákazníci WAF se mohou rozhodnout spustit jednu z akcí, pokud požadavek odpovídá podmínkám pravidla:

- **Povolit:**  Požadavek prochází WAF a je předán back-end. Žádné další nižší priority pravidla mohou blokovat tento požadavek.
- **Blok:** Požadavek je blokován a WAF odešle odpověď klientovi bez předání požadavku back-endu.
- **Protokol:**  Požadavek je zaznamenán v protokolech WAF a WAF pokračuje v hodnocení pravidel s nižší prioritou.
- **Přesměrování:** WAF přesměruje požadavek na zadaný identifikátor URI. Zadaný identifikátor URI je nastavení úrovně zásad. Po nakonfigurování budou do tohoto identifikátoru URI odeslány všechny požadavky, které odpovídají akci **Přesměrování.**

## <a name="waf-rules"></a>Pravidla WAF

Zásada WAF se může skládat ze dvou typů pravidel zabezpečení – vlastnípravidla vytvořená zákazníkem a spravovanými pravidly, předem nakonfigurovaná sada pravidel spravovaná v Azure.

### <a name="custom-authored-rules"></a>Vlastní pravidla vytvořená

Vlastní pravidla WAF můžete nakonfigurovat následujícím způsobem:

- **Seznam povolených ip a blokovaných položek:** Přístup k webovým aplikacím můžete řídit na základě seznamu ip adres klientů nebo rozsahů IP adres. Podporovány jsou typy adres IPv4 i IPv6. Tento seznam lze nakonfigurovat tak, aby tyto požadavky blokoval nebo povoloval, pokud zdrojová ADRESA IP odpovídá adrese IP v seznamu.

- **Geografické řízení přístupu:** Přístup k webovým aplikacím můžete řídit na základě kódu země, který je přidružen k IP adrese klienta.

- **Řízení přístupu založené na parametrech PROTOKOLU HTTP:** Pravidla pro shody řetězců můžete založit v parametrech požadavku HTTP/HTTPS.  Například řetězce dotazu, POST args, Identifikátor URI požadavku, Hlavička požadavku a Tělo požadavku.

- **Řízení přístupu založené na metodách:** Pravidla založená na metodě požadavku HTTP požadavku. Například GET, PUT nebo HEAD.

- **Omezení velikosti:** Pravidla můžete založit na délkách určitých částí požadavku, jako je řetězec dotazu, Identifikátor Uri nebo tělo požadavku.

- **Pravidla pro omezení rychlosti:** Pravidlo řízení rychlosti je omezit abnormální vysoký provoz z libovolné IP adresy klienta. Můžete nakonfigurovat prahovou hodnotu na počet webových požadavků povolených z IP klienta během jedné minuty trvání. Toto pravidlo se liší od vlastního pravidla povolit a blokovat seznam ip adres, které umožňuje všechny nebo blokuje všechny požadavky z IP adresy klienta. Omezení rychlosti lze kombinovat s dalšími podmínkami shody, jako jsou shody parametrů HTTP(S) pro granulární řízení rychlosti.

### <a name="azure-managed-rule-sets"></a>Sady pravidel spravované Azure

Sady pravidel spravované Azure poskytují snadný způsob nasazení ochrany proti společné sadě bezpečnostních hrozeb. Vzhledem k tomu, že tyto sady pravidel jsou spravovány azure, pravidla jsou aktualizovány podle potřeby k ochraně proti nové podpisy útoku. Výchozí sada pravidel spravovaná Azure obsahuje pravidla proti následujícím kategoriím hrozeb:

- Skriptování mezi weby
- Java útoky
- Zahrnutí místního souboru
- ÚTOKY PHP injekce
- Vzdálené spuštění příkazu
- Zahrnutí vzdáleného souboru
- Fixace relace
- Ochrana před útoky prostřednictvím injektáže SQL.
- Útočníci protokolu

Číslo verze výchozí sady pravidel se při přidávání nových podpisů útoku do sady pravidel.
Výchozí sada pravidel je ve výchozím nastavení povolena v režimu zjišťování v zásadách WAF. Můžete zakázat nebo povolit jednotlivá pravidla v rámci výchozí sady pravidel ke splnění požadavků aplikace. Můžete také nastavit konkrétní akce (ALLOW/BLOCK/REDIRECT/LOG) podle pravidla.

Někdy může být nutné vynechat určité atributy požadavku z hodnocení WAF. Běžným příkladem jsou tokeny vložené službou Active Directory, které se používají k ověřování. Seznam vyloučení můžete nakonfigurovat pro spravované pravidlo, skupinu pravidel nebo pro celou sadu pravidel.  

Výchozí akce je BLOKOVAT. Kromě toho mohou být vlastní pravidla nakonfigurována ve stejné zásadě WAF, pokud chcete obejít některá z předem nakonfigurovaných pravidel ve výchozí sadě pravidel.

Vlastní pravidla jsou vždy použita před vyhodnocením pravidel ve výchozí sadě pravidel. Pokud požadavek odpovídá vlastnímu pravidlu, použije se odpovídající akce pravidla. Požadavek je blokován nebo předán back-endu. Nejsou zpracována žádná další vlastní pravidla ani pravidla ve výchozí sadě pravidel. Můžete také odebrat výchozí sadu pravidel ze zásad WAF.

### <a name="bot-protection-rule-set-preview"></a>Sada pravidel ochrany botů (náhled)

Můžete povolit sadu pravidel ochrany spravovaného robota, která umožňuje provádět vlastní akce na základě požadavků ze známých kategorií robotů. 

Podporovány jsou tři kategorie robotů: Špatné, Dobré a Neznámé. Podpisy botů jsou spravovány a dynamicky aktualizovány platformou WAF.

Špatné roboty patří roboty ze škodlivých IP adres a roboty, které zfalšované jejich identity. Škodlivé IP adresy jsou získávány ze zdroje Microsoft Threat Intelligence a aktualizovány každou hodinu. [Inteligentní graf zabezpečení](https://www.microsoft.com/security/operations/intelligence) pohání Microsoft Threat Intelligence a používá ho několik služeb, včetně Azure Security Center.

Dobrý Roboti patří ověřené vyhledávače. Neznámé kategorie zahrnují další skupiny robotů, které se identifikovaly jako roboty. Například analyzátor trhu, nakladače informačních kanálů a agenti sběru dat. 

Neznámí roboti jsou klasifikováni prostřednictvím publikovaných uživatelských agentů bez dalšího ověření. Můžete nastavit vlastní akce pro blokování, povolení, protokolování nebo přesměrování pro různé typy robotů.

![Sada pravidel ochrany botů](../media/afds-overview/botprotect2.png)

> [!IMPORTANT]
> Sada pravidel ochrany botů je aktuálně ve verzi Public Preview a je vybavena předběžnou smlouvou o úrovni služeb. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti.  Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pokud je povolena ochrana robota, příchozí požadavky, které odpovídají pravidlům robota, jsou zaznamenány v protokolu FrontdoorWebApplicationFirewallLog. K protokolům WAF můžete přistupovat z účtu úložiště, centra událostí nebo analýzy protokolů.

## <a name="configuration"></a>Konfigurace

Všechny typy pravidel WAF můžete nakonfigurovat a nasadit pomocí portálu Azure, rozhraní API REST, šablon Azure Resource Manager a Azure PowerShellu.

## <a name="monitoring"></a>Monitorování

Monitorování WAF u předních dveří je integrované s Azure Monitorem pro sledování výstrah a snadné sledování trendů provozu.

## <a name="next-steps"></a>Další kroky

- Informace o [bráně firewall webových aplikací v bráně aplikací Azure](../ag/ag-overview.md)