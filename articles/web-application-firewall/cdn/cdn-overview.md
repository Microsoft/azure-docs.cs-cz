---
title: Co je firewall webových aplikací Azure na Azure CDN?
description: Přečtěte si, jak Firewall webových aplikací Azure ve službě Azure CDN chrání vaše webové aplikace před škodlivými útoky.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: victorh
ms.openlocfilehash: cd1f21f150094094a6bf856b04a64a02963f35a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102521709"
---
# <a name="azure-web-application-firewall-on-azure-content-delivery-network-from-microsoft"></a>Firewall webových aplikací Azure v Azure Content Delivery Network od Microsoftu

Firewall webových aplikací Azure (WAF) v Azure Content Delivery Network (CDN) od Microsoftu poskytuje centralizovanou ochranu vašeho webového obsahu. WAF chrání vaše webové služby před běžnými zneužitími a chybami zabezpečení. Udržuje vaše služba vysoce dostupná pro vaše uživatele a pomáhá splnit požadavky na dodržování předpisů.

> [!IMPORTANT]
> Azure CDN WAF od Microsoftu je momentálně ve verzi Public Preview a poskytuje se ve verzi Preview smlouvy o úrovni služeb. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti.  Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

WAF v Azure CDN je globální a centralizované řešení. Je nasazená na hraničních místech sítě Azure po celém světě. WAF zabraňuje škodlivým útokům blízko zdrojů útoku předtím, než dosáhnou svého původu. Globální ochranu získáte škálováním bez obětování výkonu. 

Zásady WAF jednoduše odkazují na libovolný koncový bod CDN v rámci vašeho předplatného. Nová pravidla se dají nasadit během několika minut, takže můžete rychle reagovat na měnící se vzory hrozeb.

![Firewall webových aplikací Azure](../media/cdn-overview/waf-cdn-overview.png)

## <a name="waf-policy-and-rules"></a>Zásady a pravidla WAF

Můžete nakonfigurovat zásady WAF a přidružit tuto zásadu k jednomu nebo více koncovým bodům CDN pro ochranu. Zásady WAF se skládají ze dvou typů pravidel zabezpečení:

- vlastní pravidla, která můžete vytvořit.

- spravované sady pravidel, které jsou kolekcí předem nakonfigurovaných pravidel spravovaných Azure.

Pokud jsou přítomny obě, před zpracováním pravidel v sadě spravovaných pravidel se zpracují vlastní pravidla. Pravidlo se skládá z podmínky shody, priority a akce. Podporované typy akcí: *Allow*, *Block*, *log* a *redirect*. Můžete vytvořit plně přizpůsobené zásady, které vyhovují konkrétním požadavkům na ochranu aplikací, a to kombinováním spravovaných a vlastních pravidel.

Pravidla v rámci zásad jsou zpracovávána v pořadí podle priority. Priorita je jedinečné číslo definující pořadí pravidel, která se mají zpracovat. Menší čísla mají vyšší prioritu a tato pravidla se vyhodnocují před pravidly s větší hodnotou. Po porovnání pravidla se na žádost aplikuje odpovídající akce, která byla definována v pravidle. Po zpracování této shody se pravidla s nižšími prioritami nezpracují dále.

Webová aplikace hostovaná v Azure CDN může k ní být přidružená jenom jedna zásada WAF. Je ale možné, že máte koncový bod CDN bez jakýchkoli zásad WAF, které jsou k němu přidružené. Pokud je přítomná zásada WAF, replikuje se na všechna naše hraniční umístění, aby se zajistila konzistence zásad zabezpečení po celém světě.

## <a name="waf-modes"></a>WAF režimy

Zásady WAF je možné nakonfigurovat tak, aby běžely v následujících dvou režimech:

- *Detekční režim*: při spuštění v režimu detekce neprovádí WAF žádné jiné jiné akce než monitory a zaznamená požadavek a odpovídající WAF pravidlo na WAF protokoly. Diagnostiku protokolování pro CDN můžete zapnout. Když použijete portál, přejdete do části **Diagnostika** .

- *Režim prevence*: v režimu prevence převezme WAF zadanou akci, pokud požadavek odpovídá pravidlu. Pokud je nalezena shoda, nejsou vyhodnocována žádná další pravidla s nižší prioritou. V protokolech WAF se zaznamenávají i všechny odpovídající požadavky.

## <a name="waf-actions"></a>Akce WAF

Když požadavek odpovídá podmínkám pravidla, můžete vybrat jednu z následujících akcí:

- *Allow*: požadavek projde přes WAF a předává se do back-endu. Tato žádost nemůže blokovat žádná další pravidla s nižší prioritou.
- *Blok*: požadavek je ZABLOKOVÁN a WAF odesílá odpověď klientovi bez předání požadavku back-endu.
- *Log*: požadavek se zaznamená do protokolů WAF a WAF pokračuje v hodnocení pravidel s nižší prioritou.
- *Přesměrování*: WAF přesměruje požadavek na zadaný identifikátor URI. Zadaný identifikátor URI je nastavení úrovně zásad. Po nakonfigurování se do tohoto identifikátoru URI odešlou všechny požadavky, které odpovídají akci *přesměrování* .

## <a name="waf-rules"></a>Pravidla WAF

Zásady WAF můžou sestávat ze dvou typů pravidel zabezpečení:

- *vlastní pravidla*: pravidla, která vytvoříte sami 
- *spravované sady pravidel*: spravovaná předem nakonfigurovaná sada pravidel

### <a name="custom-rules"></a>Vlastní pravidla

Vlastní pravidla můžou odpovídat pravidlům pravidel řízení sazeb.

Můžete nakonfigurovat tato vlastní pravidla shody:

- Seznam *povolených a blokovaných IP* adres: můžete řídit přístup k webovým aplikacím na základě seznamu IP adres klientů nebo rozsahů IP adres. Podporují se typy adres IPv4 i IPv6. Tento seznam je možné nakonfigurovat tak, aby buď blokoval, nebo povolil tyto požadavky, kde zdrojová IP adresa odpovídá IP adrese v seznamu.

- *Geografické řízení přístupu*: můžete řídit přístup k webovým aplikacím na základě kódu země, který je PŘIDRUŽENÝ k IP adrese klienta.

- *Řízení přístupu na základě parametrů http*: v parametrech požadavků HTTP/HTTPS můžete použít základní pravidla pro řetězcové shody.  Například řetězce dotazů, POST argumenty, identifikátor URI požadavku, Hlavička požadavku a text žádosti.

- *Řízení přístupu na základě metody požadavku*: základem pro metodu požadavku HTTP požadavku je základní pravidlo. Například GET, PUT nebo HEAD.

- *Omezení velikosti*: můžete odvodit pravidla na délkách určitých částí požadavku, jako je řetězec dotazu, identifikátor URI nebo text žádosti.

Pravidlo řízení sazeb omezuje neobvykle vysoký provoz z jakékoli IP adresy klienta.

- *Pravidla omezení četnosti*: můžete nastavit prahovou hodnotu pro počet webových požadavků povolených z IP adresy klienta během jedné minuty. Toto pravidlo se liší od vlastního pravidla povolit/zablokování založeného na seznamu IP adres, které buď umožňuje všem žádostem z IP adresy klienta nebo blokovat všechny požadavky. Omezení přenosové rychlosti lze kombinovat s dalšími podmínkami shody, jako je například shoda parametru HTTP (S) pro detailní řízení míry.

### <a name="azure-managed-rule-sets"></a>Sady pravidel spravované v Azure

Sady pravidel spravované pomocí Azure poskytují snadný způsob, jak nasadit ochranu proti běžné sadě bezpečnostních hrozeb. Vzhledem k tomu, že tyto RuleSets jsou spravovány v Azure, pravidla se aktualizují podle potřeby k ochraně před novými signaturami útoku. Sada spravovaných výchozích pravidel Azure obsahuje pravidla pro následující kategorie hrozeb:

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
Výchozí sada pravidel je ve výchozím nastavení povolená v režimu *detekce* v zásadách WAF. Můžete zakázat nebo povolit jednotlivá pravidla v rámci výchozího pravidla nastavená tak, aby splňovala požadavky vaší aplikace. Můžete také nastavit konkrétní akce (povolení/blokování/přesměrování/protokol) na pravidlo. Výchozí akce pro sadu spravovaných výchozích pravidel je *zablokovaná*.

Vlastní pravidla se vždycky aplikují předtím, než se vyhodnotí pravidla v sadě výchozích pravidel. Pokud požadavek odpovídá vlastnímu pravidlu, použije se odpovídající akce pravidla. Požadavek je buď zablokován, nebo předán do back-endu. Nezpracují se žádná další vlastní pravidla ani pravidla v sadě výchozích pravidel. Můžete taky odebrat výchozí sadu pravidel ze zásad WAF.

## <a name="configuration"></a>Konfigurace

Všechny typy pravidel WAF můžete nakonfigurovat a nasadit pomocí Azure Portal, rozhraní REST API, šablon Azure Resource Manager a Azure PowerShell.

## <a name="monitoring"></a>Monitorování

Monitorování pro WAF s CDN je integrované s Azure Monitor pro sledování výstrah a snadné monitorování trendů provozu.

## <a name="next-steps"></a>Další kroky

- [Kurz: vytvoření zásady WAF s Azure CDN pomocí Azure Portal](waf-cdn-create-portal.md)
