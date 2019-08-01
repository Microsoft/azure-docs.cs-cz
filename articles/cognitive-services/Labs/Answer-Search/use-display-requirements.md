---
title: Použití a zobrazení požadavků – hledání odpovědí na projekt
titlesuffix: Azure Cognitive Services
description: Použijte a zobrazte požadavky na koncový bod pro hledání odpovědí projektu.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: 2b42d61fd887f166a08b78510d5eaacb8a7cdcb8
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706705"
---
# <a name="project-answer-search-use-and-display-requirements"></a>Hledání odpovědí na projekt a podmínky zobrazení

Požadavky na používání a zobrazení se vztahují na jakoukoli implementaci obsahu a přidružených informací, například na relace, metadata a jiné signály, které jsou dostupné prostřednictvím volání vyhledávání znalostí Bingu Vlastní vyhledávání Bingu, Vyhledávání entit Vyhledávání obrázků. Rozhraní API pro Vyhledávání zpráv, Vyhledávání videí, Vizuální vyhledávání, Vyhledávání na webu, Kontrola pravopisu a autonávrh. Podrobnosti implementace související s těmito požadavky najdete v dokumentaci pro konkrétní funkce a výsledky.

## <a name="1-bing-spell-check-and-bing-autosuggest-api"></a>1. Kontrola pravopisu Bingu a rozhraní API pro automatické návrhy Bingu.

Ne:

- kopírovat, ukládat nebo ukládat veškerá data, která obdržíte z Kontrola pravopisu Bingu nebo rozhraní API Automatické návrhy Bingu
- data, která obdržíte z Kontrola pravopisu Bingu nebo Automatické návrhy Bingu rozhraní API, můžete využívat jako součást jakékoli strojové učení nebo podobného algoritmu, který umožňuje výuku, vyhodnocení nebo vylepšení nových nebo stávajících služeb, které vám nebo mohou nabídnout třetí strany.

## <a name="2-definitions"></a>2. Definice

- "Answer" odkazuje na kategorii výsledků vrácených v odpovědi. Například odpověď z rozhraní API Bingu pro vyhledávání na webu může obsahovat odpovědi v kategoriích výsledků webové stránky, obrázku, videa a novinkách;
- "odezva" znamená všechny odpovědi a přidružená data přijatá v reakci na jedno volání rozhraní API pro vyhledávání;
- "výsledek" odkazuje na položku informací v odpovědi. Například sada dat propojená s jedním článkem novinek je výsledkem odpovědi na zprávy.
- Rozhraní API pro vyhledávání znamená souhrnně Vlastní vyhledávání Bingu, Vyhledávání entit, Vyhledávání obrázků, Vyhledávání zpráv, Vyhledávání videí, Vizuální vyhledávání a Vyhledávání na webu rozhraní API. 


## <a name="3-search-apis"></a>3. Rozhraní API pro vyhledávání

Požadavky v této části 3 se vztahují na rozhraní API pro hledání.

**A. Možnosti vyhledávání v Internetu.** Všechna data vrácená v odpovědích se dají použít jenom v prostředích pro hledání v Internetu. Prostředí pro vyhledávání v Internetu znamená, že se zobrazuje obsah, jak je možné: 
- má význam a reagovat na přímý dotaz koncového uživatele nebo na jiný náznak zájmu a záměru hledání uživatele (například uživatelem zadaný vyhledávací dotaz); 
- pomáhá uživatelům najít zdroje dat a přejít na ně (například zadané adresy URL jsou implementovány jako hypertextové odkazy, takže obsah nebo označení je odkaz s možnostmi, který je viditelně zobrazen s daty); nebo, pokud je rozhraní API Bingu pro vyhledávání entit, viditelně odkaz na adresu URL bing.com, která je k dispozici v odpovědi, která umožňuje uživateli přejít na výsledky hledání pro příslušný dotaz na bing.com;
- obsahuje několik výsledků, ze kterých se má koncový uživatel vybírat (například několik výsledků z odpovědi na zprávy, nebo všechny výsledky, pokud je vráceno méně než několik výsledků); 
- je omezen na částku, která je vhodná k tomu, aby sloužila účelu hledání (například miniatury obrázků jsou v poměru k zobrazení uživatele). 
- obsahuje viditelné označení koncovému uživateli, že obsah je ve výsledku hledání na internetu (například příkaz, který je obsahem "z webu"); ani
- zahrnuje všechny další kombinace měr, které jsou vhodné k zajištění toho, aby používání dat přijatých z rozhraní API pro vyhledávání neporušilo žádné platné zákony nebo práva třetích stran (například pokud se spoléhá na licenci Creative-v, která odpovídá příslušné licenci. podmínek). Pokud chcete zjistit, jaké míry můžou být vhodné, Projděte si právní poradce.
Jedinou výjimkou je požadavek na možnosti vyhledávání na internetu, která je určena pro zjišťování adresy URL, jak je popsáno v části 3E (nezobrazuje se zjišťování adresy URL). 

**B. Podléhající.** Ne:

- kopírovat, ukládat nebo ukládat veškerá data z odpovědí (s výjimkou uchování v rozsahu povoleném v části "kontinuita služby"); 
- data přijatá z rozhraní API pro hledání se používají jako součást jakékoli strojové učení nebo podobného algoritmu, který umožňuje vyškolit, vyhodnocovat nebo zdokonalit nové nebo existující služby, které vám nebo třetí strany mohou nabídnout.
- Úprava obsahu výsledků (jiné než jejich přeformátování způsobem, který porušuje žádné jiné požadavky), pokud to není vyžadováno zákonem nebo schváleným společností Microsoft; 
- vynechat přidělení a adresy URL přidružené k obsahu výsledku;
- Změna pořadí, včetně opomenutí, výsledků zobrazených v odpovědi při zadání objednávky nebo hodnocení (pro rozhraní API pro vlastní vyhledávání Bingu se toto pravidlo nevztahuje na změnu pořadí implementované prostřednictvím portálu customsearch.ai), pokud to není vyžadováno zákonem nebo schváleným společností Microsoft ;
- Zobrazit další obsah v rámci libovolné části reakce způsobem, který by vedlo koncovému uživateli k názoru, že je druhý obsah součástí odpovědi; 
- Zobrazit reklamu, kterou společnost Microsoft neposkytuje, na jakékoli stránce, která zobrazuje jakoukoli část odpovědi; – Zobrazit veškerou reklamu pomocí odpovědí (i) z image Bingu, zpráv nebo rozhraní API pro Vyhledávání videí; nebo (II), které jsou filtrovány nebo omezeny primárně (nebo výhradně) na výsledky obrázků, zpráv a videa.

**C. Oznámení a branding.** 

- Výrazně obsahuje funkční hypertextový odkaz na prohlášení o zásadách ochrany osobních údajů společnosti https://go.microsoft.com/fwlink/?LinkId=521839 Microsoft, které jsou k dispozici v blízkosti každého bodu uživatelského prostředí (UX), který uživateli nabízí možnost zadat vyhledávací dotaz. Označte hypertextový odkaz prohlášení Microsoftu o zásadách ochrany osobních údajů.
- Výrazně se zobrazuje branding Bingu, který je v souladu s pokyny https://go.microsoft.com/fwlink/?linkid=833278 dostupnými v blízkosti každého místa v uživatelském rozhraní, které nabízí uživateli možnost zadat vyhledávací dotaz.  Tato značka musí jasně znamenat uživateli, že Microsoft provádí vyhledávání v Internetu.
- Můžete považovat každou odpověď (nebo část odpovědi), která se zobrazuje z rozhraní API webu Bingu, image, News a videa společnosti Microsoft, jak https://go.microsoft.com/fwlink/?linkid=833278 je popsáno v tématu, pokud společnost Microsoft neurčí jinak v zápisu pro vaše použití. 
- Nepoužívejte odpovědi atributů (ani části odpovědí), které se zobrazují z rozhraní API pro vlastní vyhledávání Bingu společnosti Microsoft, pokud společnost Microsoft neurčí jinak v zápisu pro konkrétní použití.


**D. Přenášení odpovědí.** Pokud uživateli povolíte přenos odpovědi z rozhraní API pro hledání na jiného uživatele, například prostřednictvím aplikace pro zasílání zpráv nebo příspěvků na sociálních médiích, platí následující: 
- Přenesené odpovědi musí:
  - Skládá se z obsahu, který se nezměnil z obsahu odpovědí zobrazených na předávajícího uživatele (jsou přípustné změny formátování).
  - Nezahrnovat žádná data do formuláře metadat;
  - V případě odpovědí z rozhraní API webu Bing web, image, News a video můžete zobrazit jazyk, který indikuje, že byla odpověď získaná prostřednictvím služby vyhledávání v Internetu, kterou využívá Bing (například "napájený pomocí Bingu"). Další informace o této imagi najdete v Bingu nebo pomocí loga Bingu.
  - V případě odpovědí z rozhraní API pro vlastní vyhledávání Bingu se zobrazuje jazyk zobrazení, který indikuje, že odpověď byla získána prostřednictvím internetového hledání (například "Další informace o tomto výsledku hledání");
  - Výrazně zobrazí úplný dotaz použitý ke generování odpovědi. ani
  - Do podkladového zdroje odpovědi zahrňte výrazný odkaz nebo podobné označení, a to buď přímo, nebo prostřednictvím vyhledávacího modulu (bing.com, m.bing.com nebo vlastní vyhledávací služby podle potřeby).
- Nemůžete automatizovat přenos odpovědí. Přenos musí být zahájen akcí uživatele jasně evidencing záměrem přenést odpověď.
- Uživateli můžete povolit přenos odpovědí, které se zobrazily v reakci na dotaz předávajícího uživatele.

**E. Kontinuita provozu.** Nekopírujte, neukládejte ani neukládají žádná data z odpovědí rozhraní API pro hledání. Chcete-li však povolit kontinuitu přístupu ke službě a vykreslování dat, můžete výsledky uchovávat pouze za následujících podmínek:

**Zařízení.** Koncovým uživatelům můžete umožnit, aby se výsledky na zařízení zachovaly po kratší dobu (i) 24 hodin od času dotazu nebo (II), dokud koncový uživatel neodešle jiný dotaz na aktualizované výsledky za předpokladu, že se zachované výsledky můžou použít jenom:

- Pokud chcete koncovému uživateli povolit přístup k výsledkům, které se na tomto zařízení vrátily dřív, (třeba v případě přerušení služby); ani
- pro uložení výsledků vrácených pro přizpůsobení proaktivního dotazu při předvídání požadavků koncových uživatelů na základě signálů koncového uživatele (například v případě předpokládaného přerušení služby).

**WebServer.** Výsledky specifické pro jednoho koncového uživatele můžete bezpečně uchovávat na serveru, který ovládáte, a zobrazit jenom zachované výsledky:

- Pokud chcete koncovému uživateli povolit přístup k historické sestavě výsledků, které se dřív vrátily k tomuto uživateli ve vašem řešení, za předpokladu, že výsledky nemusejí být (i) zachované po dobu delší než 21 dní od počátečního dotazu koncového uživatele a (II) zobrazené v reakci na konec u nový nebo opakovaný dotaz na ser; ani
- pro uložení výsledků vrácených pro přizpůsobení proaktivního dotazu při předvídání potřeb koncového uživatele na základě signálů koncového uživatele na méně než 24 hodin od času dotazu nebo (II), dokud koncový uživatel neodešle jiný dotaz na aktualizované výsledky.

Kdykoli je zachováno, výsledky pro konkrétního uživatele nelze commingled s výsledky pro jiného uživatele, tj. výsledky každého uživatele musí být uchovány a dodávány samostatně.

**Všeobecně.** Pro všechny prezentace uchovávaných výsledků:

- Zahrňte jasné a viditelné oznámení o tom, kdy se dotaz odeslal.
- prezentovat uživateli tlačítko nebo podobné prostředky pro opětovné dotazování a získání aktualizovaných výsledků 
- značky Bingu si Zachovejte v prezentaci výsledků a
- v případě potřeby odstraňte (a aktualizujte nový dotaz) uložené výsledky v rámci zadaných časových období.

**F. Zjišťování adresy URL bez zobrazení.** Odpovědi na hledání můžete použít jenom v prostředí vyhledávání v Internetu, abyste mohli jenom zjistit, jaké adresy URL zdrojů informací reagují na dotaz od uživatele nebo zákazníka. Tyto adresy URL můžete zkopírovat v sestavě nebo podobné odpovědi, které zadáte (i) jenom tomuto uživateli nebo zákazníkovi, a to v reakci na tento dotaz a (II), který zahrnuje významný další cenný obsah, který je pro dotaz relevantní. Požadavky v oddílech 3A až 3E těchto použití a požadavky na zobrazení se nevztahují na toto použití bez zobrazení, s výjimkou: 

- Nebudete ukládat do mezipaměti, kopírovat ani ukládat žádná data ani obsah z, ani z nich nemusíte z najít odpověď na vyhledávání, kromě dříve popsaných souborů s omezeným počtem adres URL.
- Zajistěte, aby vaše používání dat (včetně adres URL) přijatých z rozhraní API pro hledání neporušilo žádné platné zákony ani práva třetích stran. ani
- Nebudete používat data (včetně adres URL) přijatých z rozhraní API pro hledání jako součást jakéhokoli indexu vyhledávání nebo strojového učení nebo podobného algoritmu, který umožňuje vytvořit výuku, vyhodnotit nebo zlepšit služby, které vám nebo mohou nabídnout třetí strany.

## <a name="next-steps"></a>Další postup
[Přehled hledání odpovědí](overview.md)
