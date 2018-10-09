---
title: Použít a zobrazit požadavky - hledání odpovědí projektu
titlesuffix: Azure Cognitive Services
description: Použít a zobrazit požadavky pro koncový bod hledání odpovědí projektu.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: project-answer-search
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: 0869833ec83c40f061bebf1aeb1267f44dcbb048
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48868229"
---
# <a name="project-answer-search-use-and-display-requirements"></a>Hledání odpovědí projektu použít a zobrazit požadavky

Použití a zobrazit požadavky platí pro žádnou implementaci obsah a přidružené informace, například relace, metadata a dalšími signály, k dispozici prostřednictvím volání do znalostní báze Bingu pro vyhledávání, vlastní vyhledávání Bingu, vyhledávání entit, vyhledávání obrázků Vyhledávání zpráv, vyhledávání videí, vizuálního vyhledávání, webové vyhledávání, kontrola pravopisu a rozhraní API pro automatické návrhy. Podrobnosti implementace související s tyto požadavky najdete v dokumentaci pro konkrétní funkce a výsledky.

## <a name="1-bing-spell-check-and-bing-autosuggest-api"></a>1. Pro kontrolu pravopisu Bingu a Bing pro automatické návrhy rozhraní API.

Ne:

- kopírování, uložení nebo mezipaměti všechna data, která jste dostali z kontrolu pravopisu Bingu nebo rozhraní API pro automatické návrhy Bingu
- pomocí dat získaných z kontrolu pravopisu Bingu nebo rozhraní API pro automatické návrhy Bingu jako součást machine learning nebo podobné aktivitě vylepšením pro trénování, vyhodnocení nebo vylepšení nové nebo existující služby, které může nabídnout vy nebo třetími stranami.

## <a name="2-definitions"></a>2. Definice

- "odpověď" odkazuje na kategorie výsledků vrácená v odpovědi. Například může zahrnovat odpovědi v kategoriích webovou stránku výsledků, image, video a zprávy; odpověď z rozhraní API webové vyhledávání Bingu
- "odpověď" znamená, že všechny odpovědi a přidružená data přijatá v odpovědi na jediné volání rozhraní API pro vyhledávání;
- "výsledek" odkazuje na položku informací v odpovědi. Například sadu data připojená přes jeden příspěvek je výsledek v odpovědi na zprávy.
- "Rozhraní API pro vyhledávání" znamená společně, vlastní vyhledávání Bingu, vyhledávání entit, vyhledávání obrázků, vyhledávání zpráv, vyhledávání videí, pro vizuální vyhledávání a webové rozhraní API pro vyhledávání. 


## <a name="3-search-apis"></a>3. Rozhraní API pro vyhledávání

Platí požadavky uvedené v této části 3 pro rozhraní API pro vyhledávání.

**A. Možnosti vyhledávání Internet.** Všechna data vrácená v odpovědi může použít jenom v Internetu vyhledávací prostředí. Vyhledávání na Internetu znamená, že obsah, zobrazí se odpovídající: 
- je důležité a reagovat na koncového uživatele přímých dotazů nebo jiné označení zájmu hledat uživatele a záměr (například uživatel uveden vyhledávací dotaz); 
- pomáhá uživatelům vyhledávat a přejděte do zdroje dat (například zadané adresy URL jsou implementovány jako hypertextové odkazy, obsah nebo attribution je prokliknutelný odkaz zřetelně zobrazí s daty); nebo pokud aplikace API pro vyhledávání entit Bingu, viditelně odkazují na bing.com adresy URL poskytnuté v odpovědi, která umožňuje uživateli přejít na výsledky hledání pro odpovídající dotaz na adrese bing.com;
- zahrnuje více výsledků pro koncového uživatele můžete vybírat z (například se zobrazí několik výsledky z odpovědí zprávy, nebo jsou vráceny všechny výsledky, pokud je to méně než několik); 
- je omezený na dobu odpovídající účel vyhledávání (například obrázkům miniatur jsou miniaturní výkonový zisk zobrazení uživatele); 
- zahrnuje by pro koncového uživatele, že obsah je výsledky hledání v Internetu (třeba příkaz, že je "z webu") a
- zahrnuje libovolnou kombinaci opatření vhodné zajistit, že všechny platné zákony nebo práva třetí strany (například, pokud spoléhat na licenci typu Creative Commons dodržování příslušných licenčních nebudou porušovat používání dat přijatých z rozhraní API pro vyhledávání podmínky). Informace, které vaše právní poradci a určí, co opatření může být vhodné.
Jedinou výjimkou požadavek prostředí pro vyhledávání na Internetu je adresa URL zjišťování, jak je popsáno v následující části 3E (Non zobrazovaná adresa URL zjišťování). 

**B. Omezení.** Ne:

- kopírování, uložení nebo mezipaměti všechna data z odpovědi (s výjimkou uchovávání rozsahu povoleném následující část "Kontinuity podnikových procesů služba"); 
- data přijatá z rozhraní API pro vyhledávání v rámci jakékoli machine learning nebo podobné aktivitě vylepšením použijte k trénování, vyhodnocení nebo vylepšení nového nebo existujícího služeb, které může nabídnout vy nebo třetími stranami.
- Upravit obsah výsledků (jiné než se jejich přeformátování tak, aby všechny požadavky nebudou porušovat), pokud to vyžaduje zákon nebo ustanovení společnost Microsoft. 
- vynechte attribution a adresy URL přidružené k výsledku obsahu;
- přesunout v pořadí, třeba tak, že vynechání, výsledky zobrazené v odpovědi, když je k dispozici objednávku nebo pořadí (pro API Bingu pro vlastní vyhledávání, toto pravidlo se nevztahují na změnu pořadí implementované pomocí portálu customsearch.ai), pokud to vyžaduje zákon nebo ustanovení společností Microsoft ;
- Zobrazte další obsah v rámci všech součástí odpovědi způsobem, který by mohlo dojít koncový uživatel se domnívat, že další obsah je součástí odpovědi; 
- Zobrazte reklamy, který není k dispozici společností Microsoft na libovolné stránce, která zobrazuje všechny odpovědi; – zobrazení jakémukoliv inzerování s odpovědí (i) z obrázky Bingu, zprávy nebo rozhraní API pro Video Search; nebo (ii), která jsou filtrovaná nebo omezené hlavně (nebo výhradně) k výsledkům, zprávy a/nebo video.

**C. Oznámení a značky.** 

- Funkční hypertextový odkaz na prohlášení, k dispozici na výrazném místě zahrnují https://go.microsoft.com/fwlink/?LinkId=521839, téměř každý bod v uživatelské prostředí (UX), který uživateli umožňuje zadání vyhledávacího dotazu. Popisek hypertextový odkaz "Prohlášení o ochraně osobních údajů Microsoft".
- Výrazně zobrazit značky, konzistentní s pokyny k dispozici na Bingu https://go.microsoft.com/fwlink/?linkid=833278, téměř každý bod v uživatelském prostředí, který uživateli umožňuje zadání vyhledávacího dotazu.  Tyto značky musí jasně označují uživateli, že Microsoft je provozování vyhledáváním na Internetu.
- Může atribut každé odpovědi (nebo část odpověď), zobrazí z Bingu Web, Image, zprávy a rozhraní API pro Video na Microsoft, jak je popsáno v https://go.microsoft.com/fwlink/?linkid=833278, pokud Microsoft neurčí jinak písemně používat. 
- Není atribut odpovědi (ani jeho části odpovědí) zobrazí z rozhraní API pro vlastní vyhledávání Bingu společnosti Microsoft, pokud Microsoft neurčí jinak písemně pro konkrétní použití.


**D. Přenos odpovědi.** Pokud povolíte uživatele k přenosu odpověď z rozhraní API pro vyhledávání na jiného uživatele, například prostřednictvím zasílání zpráv aplikace nebo účtování sociálních médií, následujících podmínek: 
- Přenesené odpovědi musí:
  - Skládat z obsahu, který je z obsahu odpovědi přenášení uživateli zobrazí bez úprav (změny formátování jsou přípustné);
  - Nezahrnuje všechna data v podobě metadat
  - Pro odpovědi z Bingu Web, Image, zprávy a rozhraní API pro Video, jazyk zobrazení označující odpovědi získaný prostřednictvím Internetu prostředí vyhledávání Bing používá technologii (například "S využitím službou Bing," "Další informace o této bitové kopie na Bingu", nebo pomocí Bing logo);
  - Pro odpovědi z rozhraní API pro vlastní vyhledávání Bingu jazyk zobrazení označující odpovědi získaný prostřednictvím možnosti vyhledávání na Internetu (například "Další informace o tento výsledek hledání");
  - Výrazně zobrazte celý dotaz sloužící ke generování odpovědi; a
  - Zahrnout viditelného odkaz nebo podobné attribution do podkladového zdroje odpověď, a to buď přímo nebo prostřednictvím vyhledávací web (bing.com, m.bing.com nebo vaše vlastní vyhledávací služba, podle vhodnosti).
- Nelze automatizovat přenos odpovědi. Převod musí být iniciovány akcí uživatele jasně prokazující záměru přenést odpověď.
- Můžete povolit pouze uživatele k přenosu odpovědi, které byly zobrazeny v reakci na dotaz přenášení uživatele.

**E. Kontinuitu poskytování služeb.** Kopírování, uložení nebo všechna data z rozhraní API pro hledání odpovědí do mezipaměti. Povolit kontinuity podnikových procesů přístupu ke službám a vykreslování dat, ale může uchovávat výsledky pouze za následujících podmínek:

**Zařízení.** Můžete povolit koncovým uživatelům zachovat výsledky na zařízení pro menší než délka (i) 24 hodin od okamžiku dotazu nebo (ii) dokud koncový uživatel odešle jiný dotaz pro aktualizované výsledky, za předpokladu, že uchovanou výsledky mohou být použity pouze:

- Chcete-li povolit koncový uživatel pro přístup k dříve vrátil pro tento koncový uživatel na tomto zařízení (například v případě přerušení služby); výsledky nebo
- k uložení výsledků vrácených pro dotaz proaktivní individuální čekat na případná koncový uživatel musí na základě signálů koncovým uživatelem (například v případě přerušení očekávané služby).

**Server.** Může zachovat výsledky, které jsou specifické pro jeden koncový uživatel bezpečně na serveru pod kontrolou a můžete zobrazit pouze uchovanou výsledky:

- Chcete-li povolit koncový uživatel pro přístup k historické sestavy výsledků dříve vrátí pro tohoto uživatele ve vašem řešení, za předpokladu, že výsledky nemusí být (i) uchovávají po dobu více než 21 dní od počátečního dotazu koncového uživatele a (ii) zobrazí v reakci na skončí u nové nebo opakované dotazu ivatele; nebo
- k uložení výsledků vrácených pro proaktivní dotaz individuální čekat na případná potřebám koncového uživatele na základě signálů koncového uživatele pro menší než délka (i) 24 hodin od času dotazu nebo (ii) dokud koncový uživatel odešle jiný dotaz pro aktualizované výsledky.

Pokaždé, když se uchovávají, výsledky pro konkrétního uživatele nelze commingled s výsledky pro jiného uživatele, to znamená, výsledky jednotlivých uživatelů musí být zachovány a nabízeli samostatně.

**Obecné.** Pro všechny presentation uchovaná výsledků:

- Zahrnout viditelné a jasně řečeno oznámení o čas, kdy byla vyslána dotaz,
- prezentovat tlačítko nebo podobně jako způsob, jak znovu odeslán dotaz a získat aktualizované výsledky 
- zachovat Bingu značky v prezentaci výsledků, a
- Odstranit (a aktualizuje s použitím nového dotazu v případě potřeby) uložených výsledků v rámci časové rámce zadán.

**F. Adresa URL zjišťování mimo zobrazení.** Odpovědi na vyhledávání na může použít pouze v prostředí Internetu jiných hledání pouze za účelem zjišťování adresy URL zdroje informací interaktivní dotaz z vašich uživatelů a zákazníků. Můžete kopírovat tyto adresy URL v sestavě nebo podobné odpovědi, které zadáte (i) pouze pro tohoto uživatele nebo zákazníka, odpověď na tento dotaz a (ii), který zahrnuje výrazné další hodnotný obsah relevantní pro dotaz. Požadavky uvedené v části 3A 3E z těchto požadavků použití a zobrazení se nedá použít s tímto použitím jiných zobrazení, s výjimkou: 

- Nebude ukládat do mezipaměti, kopírovat ani ukládat veškerá data, nebo z obsahu, odvozený z reakce na vyhledávání, než je omezená kopírování adresy URL, které jsou popsány dříve;
- Ujistěte se, že všechny platné zákony nebo práva třetí strany, nebudou porušovat používání (včetně adresy URL) dat přijatých z rozhraní API pro vyhledávání a
- (Včetně adresy URL) dat přijatých z rozhraní API pro vyhledávání jako součást indexu vyhledávání nebo strojové učení nebo podobné aktivitě vylepšením nebudeme používat k vytváření trénování, vyhodnocení a vylepšení služeb, které může nabídnout vy nebo třetími stranami.

## <a name="next-steps"></a>Další postup
[Přehled hledání odpovědí](overview.md)
