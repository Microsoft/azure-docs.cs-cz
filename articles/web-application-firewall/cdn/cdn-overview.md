---
title: Co je brána firewall webových aplikací Azure na Azure CDN?
description: Zjistěte, jak brána firewall webových aplikací Azure ve službě Azure CDN chrání vaše webové aplikace před škodlivými útoky.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: overview
ms.date: 03/18/2020
ms.author: victorh
ms.openlocfilehash: 28cf8d9fd60cc6fc158812aa0a1dff3a4b0dced1
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754298"
---
# <a name="azure-web-application-firewall-on-azure-content-delivery-network"></a>Brána firewall webových aplikací Azure v síti pro doručování obsahu Azure

Azure Web Application Firewall (WAF) v Síti pro doručování obsahu Azure (CDN) od Microsoftu poskytuje centralizovanou ochranu webového obsahu. WAF chrání vaše webové služby před běžnými zneužitími a chybami zabezpečení. Udržuje vaši službu vysoce dostupnou pro vaše uživatele a pomáhá vám splnit požadavky na dodržování předpisů.

> [!IMPORTANT]
> WAF na Azure CDN od Microsoftu je aktuálně ve verzi Public Preview a je k dispozici předběžná smlouva o úrovni služeb. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti.  Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

WAF na Azure CDN je globální a centralizované řešení. Nasadí se na okrajových místech Azure po celém světě. WAF zastaví škodlivé útoky v blízkosti zdrojů útoku, než se dostanou do vašeho původu. Získáte globální ochranu ve velkém měřítku, aniž byste obětovali výkon. 

Zásady WAF snadno odkazy na libovolný koncový bod CDN ve vašem předplatném. Nová pravidla lze nasadit během několika minut, takže můžete rychle reagovat na měnící se vzorce hrozeb.

![Brána firewall pro webové aplikace Azure](../media/cdn-overview/waf-cdn-overview.png)

## <a name="waf-policy-and-rules"></a>Zásady a pravidla WAF

Můžete nakonfigurovat zásady WAF a přidružit tuto zásadu k jednomu nebo více koncovým bodům CDN pro ochranu. Zásada WAF se skládá ze dvou typů pravidel zabezpečení:

- vlastní pravidla, která můžete vytvořit.

- spravované sady pravidel, které jsou kolekcí předem nakonfigurovaných pravidel Azure.

Pokud jsou k dispozici obě, vlastní pravidla jsou zpracovány před zpracováním pravidel ve spravované sadě pravidel. Pravidlo je tvořeno podmínkou shody, prioritou a akcí. Podporované typy akcí jsou: *POVOLIT*, *BLOKOVAT*, *PROTOKOLA*A *PŘESMĚROVÁNÍ*. Kombinací spravovaných a vlastních pravidel můžete vytvořit plně přizpůsobenou zásadu, která splňuje vaše specifické požadavky na ochranu aplikací.

Pravidla v rámci zásady jsou zpracována v pořadí podle priority. Priorita je jedinečné číslo, které definuje pořadí pravidel ke zpracování. Menší čísla mají vyšší prioritu a tato pravidla jsou vyhodnocována před pravidly s vyšší hodnotou. Jakmile je pravidlo spárováno, odpovídající akce, která byla definována v pravidle, se použije na požadavek. Jakmile je taková shoda zpracována, pravidla s nižšími prioritami nejsou dále zpracována.

Webová aplikace hostovaná v Azure CDN může mít současně přidruženou jenom jednu zásadu WAF. Můžete však mít koncový bod CDN bez jakýchkoli zásad WAF s ním spojené. Pokud je k dispozici zásada WAF, je replikována do všech našich hraničních umístění, aby byla zajištěna konzistentní zásady zabezpečení po celém světě.

## <a name="waf-modes"></a>Režimy WAF

Zásady WAF lze nakonfigurovat tak, aby běžely v následujících dvou režimech:

- *Režim detekce*: Při spuštění v režimu detekce waf nebere žádné jiné akce než monitoruje a protokoluje požadavek a jeho odpovídající WAF pravidlo waf protokoly. Můžete zapnout diagnostiku protokolování pro přední dveře. Při použití portálu přejděte do části **Diagnostika.**

- *Režim prevence*: V režimu prevence provede WAF zadanou akci, pokud požadavek odpovídá pravidlu. Pokud je nalezena shoda, jsou vyhodnocována žádná další pravidla s nižší prioritou. Všechny odpovídající požadavky jsou také zaznamenány v protokolech WAF.

## <a name="waf-actions"></a>Akce WAF

Můžete zvolit jednu z následujících akcí, pokud požadavek odpovídá podmínkám pravidla:

- *Povolit*: Požadavek prochází WAF a je předán back-end. Žádné další nižší priority pravidla mohou blokovat tento požadavek.
- *Blok*: Požadavek je blokován a WAF odešle odpověď klientovi bez předání požadavku back-endu.
- *Protokol*: Požadavek je zaznamenán v protokolech WAF a WAF pokračuje v hodnocení pravidel s nižší prioritou.
- *Přesměrování*: WAF přesměruje požadavek na zadaný identifikátor URI. Zadaný identifikátor URI je nastavení úrovně zásad. Po nakonfigurování jsou do tohoto identifikátoru URI odeslány všechny požadavky, které odpovídají akci *Přesměrování.*

## <a name="waf-rules"></a>Pravidla WAF

Zásada WAF se může skládat ze dvou typů pravidel zabezpečení:

- *vlastní pravidla*: pravidla, která sami vytvoříte 
- *spravované sady pravidel*: Předem nakonfigurovaná sada pravidel spravované Azure

### <a name="custom-rules"></a>Vlastní pravidla

Vlastní pravidla mohou mít pravidla shody a pravidla řízení sazeb.

Můžete nakonfigurovat následující vlastní pravidla shody:

- *Seznam povolených ip adres a seznam blokování*: Můžete řídit přístup k webovým aplikacím na základě seznamu IP adres klientů nebo rozsahů IP adres. Podporovány jsou typy adres IPv4 i IPv6. Tento seznam lze nakonfigurovat tak, aby tyto požadavky blokoval nebo povoloval, pokud zdrojová ADRESA IP odpovídá adrese IP v seznamu.

- *Geografické řízení přístupu*: Přístup k webovým aplikacím můžete řídit na základě kódu země, který je přidružen k IP adrese klienta.

- *Řízení přístupu založené na parametrech HTTP*: Pravidla pro shody řetězců můžete založit v parametrech požadavku HTTP/HTTPS.  Například řetězce dotazu, POST args, Identifikátor URI požadavku, Hlavička požadavku a Tělo požadavku.

- *Řízení přístupu založené na metodě*: Pravidla jsou založena na metodě požadavku HTTP požadavku. Například GET, PUT nebo HEAD.

- *Omezení velikosti*: Můžete založit pravidla na délkách určitých částí požadavku, jako je řetězec dotazu, Uri nebo tělo požadavku.

Pravidlo řízení rychlosti omezuje abnormálně vysoký provoz z libovolné ip adresy klienta.

- *Pravidla omezení rychlosti*: Můžete nakonfigurovat prahovou hodnotu počtu webových požadavků povolených z IP adresy klienta během jedné minuty trvání. Toto pravidlo se liší od vlastního pravidla povolit a blokovat seznam ip adres, které umožňuje všechny požadavky z adresy IP klienta nebo blokuje všechny požadavky. Omezení rychlosti lze kombinovat s dalšími podmínkami shody, jako jsou shody parametrů HTTP(S) pro granulární řízení rychlosti.

### <a name="azure-managed-rule-sets"></a>Sady pravidel spravované Azure

Sady pravidel spravované Azure poskytují snadný způsob nasazení ochrany proti společné sadě bezpečnostních hrozeb. Vzhledem k tomu, že tyto sady pravidel jsou spravovány Azure, pravidla jsou aktualizovány podle potřeby k ochraně proti nové podpisy útoku. Výchozí sada pravidel spravované Azure obsahuje pravidla proti následujícím kategoriím hrozeb:

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
Výchozí sada pravidel je ve výchozím nastavení povolena v režimu *zjišťování* v zásadách WAF. Můžete zakázat nebo povolit jednotlivá pravidla v rámci výchozí sady pravidel ke splnění požadavků aplikace. Můžete také nastavit konkrétní akce (ALLOW/BLOCK/REDIRECT/LOG) podle pravidla. Výchozí akce pro spravovanou výchozí sadu pravidel je *Blok*.

Vlastní pravidla jsou vždy použita před vyhodnocením pravidel ve výchozí sadě pravidel. Pokud požadavek odpovídá vlastnímu pravidlu, použije se odpovídající akce pravidla. Požadavek je blokován nebo předán back-endu. Nejsou zpracována žádná další vlastní pravidla ani pravidla ve výchozí sadě pravidel. Můžete také odebrat výchozí sadu pravidel ze zásad WAF.

## <a name="configuration"></a>Konfigurace

Všechny typy pravidel WAF můžete nakonfigurovat a nasadit pomocí portálu Azure, rozhraní API REST, šablon Azure Resource Manager a Azure PowerShellu.

## <a name="monitoring"></a>Monitorování

Monitorování WAF s CDN je integrované s Azure Monitor sledovat výstrahy a snadno sledovat trendy provozu.

## <a name="next-steps"></a>Další kroky

- [Kurz: Vytvoření zásadwaf s Azure CDN pomocí portálu Azure](waf-cdn-create-portal.md)
