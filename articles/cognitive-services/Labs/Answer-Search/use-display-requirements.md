---
title: Projektu použití odpovědí vyhledávání a zobrazení požadavků - kognitivní služby Microsoft | Microsoft Docs
description: Použijte a zobrazí požadavky pro koncový bod hledání odpovědí projektu.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 6e8eaaaa2c83a1420f2de86b23e15f4f19f7a565
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343282"
---
# <a name="project-answer-search-use-and-display-requirements"></a>Vyhledání odpovědí projektu používat a zobrazit požadavky

Použití a zobrazení požadavky platí pro žádnou implementaci obsah a přidružené informace, například relace, metadat a jinými signály, k dispozici prostřednictvím volání Bing znalostní báze vyhledávání, hledání vlastní Bing, Entity vyhledávání, hledání bitové kopie, Příspěvky vyhledávání, hledání videa, Visual hledání, prohledávání webu, kontrola pravopisu a rozhraní API pro automatické návrhy. Podrobnosti implementace týkající se těchto požadavků naleznete v dokumentaci pro konkrétní funkce a výsledky.

## <a name="1-bing-spell-check-and-bing-autosuggest-api"></a>1. Kontrola pravopisu v Bingu a Bing pro automatické návrhy rozhraní API.

Ne:

- kopírování, uložení nebo mezipaměti žádná data, které jste získali od kontrola pravopisu Bing nebo rozhraní API pro automatické návrhy v Bingu
- pomocí dat získaných ze služby Bing kontrola pravopisu nebo rozhraní API pro automatické návrhy v Bingu v rámci machine learning či podobné algoritmické aktivity pro trénování, vyhodnocení nebo vylepšení nových nebo existujících služeb, které může nabídnout vám či třetích stran.

## <a name="2-definitions"></a>2. Definice

- "odpověď" odkazuje na kategorii výsledky vrácené v odpovědi. Například může odpověď z rozhraní API služby Bing webové Search zahrnout odpovědi do kategorie výsledky webovou stránku, bitové kopie, videa a zprávy;
- "odpověď" se rozumí všechny odpovědi a související data přijatá v reakci na jednoho volání rozhraní API Search;
- "výsledek" odkazuje na položku informací v odpovědi na dotaz. Například sada dat, které jsou připojené pomocí jednoho aktuální článek je výsledek v odpovědi na zprávy.
- "Rozhraní API pro vyhledávání" znamená souhrnně, Bing vlastní vyhledávání, Entity vyhledávání, hledání bitové kopie, zprávy vyhledávání, hledání Video, Visual vyhledávání a webové rozhraní API pro vyhledávání. 


## <a name="3-search-apis"></a>3. Rozhraní API pro vyhledávání

Požadavky v této části 3 platí pro rozhraní API pro vyhledávání.

**A. Hledání prostředí Internetu.** Všechna data v odpovědi může použít pouze v prostředí Internetu vyhledávání. Hledání Internetu znamená obsah zobrazit, a to: 
- je důležité a citlivé přímý dotaz koncového uživatele nebo jiné údaj o zájmu hledat uživatele a záměr (například uživatele uvedené vyhledávací dotaz); 
- pomáhají uživatelům najít a přejděte do zdroje dat (například zadané adresy URL jsou implementované jako hypertextové odkazy tak, aby obsah nebo porušení prokliknutelný odkaz zřetelně zobrazeny s daty); nebo, pokud z rozhraní API vyhledávání Entity Bing, viditelně odkaz vyhledávače bing.com adresy URL poskytnuté v odpovědi, která umožňuje uživatelům přejděte na výsledky hledání relevantní dotaz na vyhledávače bing.com;
- obsahuje více výsledky pro koncového uživatele k výběru (například několik výsledky z odpovědí zprávy se zobrazují, nebo jsou vráceny všechny výsledky, pokud je to méně než několik); 
- je omezený na dobu příslušné hledání účel (například obrázek, který miniatur jsou miniaturu proměnlivé velikosti v poměru k zobrazení uživatele); 
- zahrnuje ukázala pro koncového uživatele, že je obsah výsledky hledání v Internetu (například prohlášení, že obsah je "z webu"); a
- zahrnuje jinou kombinaci opatření odpovídající Ujistěte se, že používání data přijatá z rozhraní API pro vyhledávání není porušení libovolných příslušných zákonů a práva třetí strany (Pokud například spoléhat na licence Creative Commons licence, které jsou v souladu s příslušných licenčních podmínky). Prohlédněte, které vaše právní poradci, chcete-li zjistit, co mohou být opatření vhodné.
Jedinou výjimkou požadavek rozhraní vyhledávání na Internetu je pro zjišťování adresy URL, jak je popsáno v části 3E (Non zobrazovaná adresa URL zjišťování) následující. 

**B. Omezení.** Ne:

- kopírování, uložení nebo mezipaměti žádná data z odpovědi (s výjimkou uchovávání rozsahu povoleném následující části "Kontinuity služby"); 
- Použijte data přijatá z rozhraní API pro vyhledávání v rámci žádného strojové učení nebo podobné aktivitě algoritmické cvičení, vyhodnocení nebo zlepšení nových nebo existujících služeb, které může nabídnout vám či třetích stran.
- Upravit obsah výsledků (jiné než chcete přeformátujte je způsobem, který není v rozporu s ostatními požadavky), pokud to vyžaduje zákon nebo ustanovení společnost Microsoft. 
- vynechte uvedení a adresy URL přidružené výsledek obsahu;
- změnit pořadí, třeba tak, že opomenutí, výsledky v odpověď zobrazí, když je k dispozici objednávku nebo hodnocení (rozhraní API služby Bing vlastní vyhledávání toto pravidlo se nevztahuje změna implementovaná prostřednictvím portálu customsearch.ai), pokud to vyžaduje zákon nebo ustanovení společností Microsoft ;
- Zobrazte další obsah v rámci všech součástí odpověď způsobem, který by vést koncový uživatel se domnívat, že jiný obsah je součástí odpovědi; 
- Zobrazte reklamy, který není od Microsoftu na libovolné stránce, který zobrazuje všechny odpovědi; -zobrazení jakémukoliv inzerování s odpovědí (i) z bitové kopie Bingu, zprávy nebo rozhraní API pro vyhledávání Video; nebo (ii), jsou filtrovány nebo jsou omezena především (nebo výhradně) do bitové kopie, zprávy nebo video výsledky.

**C. Oznámení a brandingu.** 

- Výrazně zahrnují funkční hypertextový odkaz na prohlášení Microsoft, k dispozici na https://go.microsoft.com/fwlink/?LinkId=521839, téměř každý bod v uživatelském rozhraní (UX), které nabízí možnost vyhledávací dotaz vstup uživatele. Označení hyperlink "Prohlášení o ochraně osobních údajů Microsoft".
- Výrazně zobrazí Bing branding konzistentní s pokyny, které jsou k dispozici na https://go.microsoft.com/fwlink/?linkid=833278, téměř každý bod v činnosti koncového uživatele, který nabízí možnost vyhledávací dotaz vstup uživatele.  Takové branding musí jasně označují uživateli, aby Microsoft je pohánějící Možnosti Internetu vyhledávání.
- Může atribut každé odpovědi (nebo její části odpověď), zobrazí z webové služby Bing, Image, novinky a rozhraní API Video společnosti Microsoft, jak je popsáno v https://go.microsoft.com/fwlink/?linkid=833278, pokud Microsoft neurčí jinak zápis pro vaše použití. 
- Není atribut odpovědí (nebo její části odpovědí) zobrazí z rozhraní API služby Bing vlastní Search společnosti Microsoft, pokud Microsoft jinak zápis pro vaše konkrétní použití.


**D. Přenášení odpovědi.** Pokud povolíte uživatelům přenosu odpověď z rozhraní API pro vyhledávání na jiného uživatele, například prostřednictvím zasílání zpráv aplikace nebo sociálních příspěvků, následujících podmínek: 
- Musí být přenášená odpovědí:
  - Skládá z obsahu, který je beze změny z obsahu odpovědi přenášení uživateli zobrazí (změny formátování jsou přípustné);
  - Nezahrnovat žádná data v podobě metadat;
  - Pro odpovědi z webové služby Bing, Image, novinky a rozhraní API Video, jazyk zobrazení, která určuje odpověď získanou prostřednictvím Internetu vyhledávání prostředí používá technologii Bing (například "Zapnuté podle Bing," "Další informace o tuto bitovou kopii na Bing", nebo pomocí Bingu logo);
  - Pro odpovědi z rozhraní API služby Bing vlastní Search jazyk zobrazení, která určuje odpověď získanou prostřednictvím Internetu vyhledávání prostředí (například "Další informace o výsledek vyhledávání");
  - Výrazně zobrazte celý dotaz sloužící ke generování odpovědi; a
  - Zahrnout viditelného odkaz nebo podobné uvedení na podkladový zdroj odpověď, a to buď přímo nebo prostřednictvím vyhledávacího webu (vyhledávače bing.com, m.bing.com nebo vlastní vyhledávací službě, podle vhodnosti).
- Nemusí automatizovat přenos odpovědi. Přenos musí být iniciovány akcí uživatele jasně prokazující záměrem přenos odpověď.
- Můžete povolit pouze uživatele k přenosu odpovědi, které byly zobrazeny v odpovědi na dotaz přenášení uživatele.

**E. Kontinuitu poskytování služeb.** Kopírování, uložení nebo žádná data z rozhraní API pro hledání odpovědí do mezipaměti. Pokud chcete povolit kontinuity přístup k službě a vykreslování dat, však může uchovávat výsledky výhradně za následujících podmínek:

**Zařízení.** Povolíte může koncový uživatel chcete zachovat výsledky na zařízení pro nižší úrovně (i) 24 hodin od času dotazu nebo (ii) až koncový uživatel odešle další dotaz pro aktualizované výsledky, za předpokladu, že udržených výsledky mohou být použity pouze:

- Chcete-li pro koncového uživatele pro přístup k výsledky předtím vrátila do tohoto koncového uživatele na tomto zařízení (například v případě přerušení poskytování služeb); nebo
- k ukládání výsledků vrácených pro proaktivní dotazu přizpůsobit v očekávání koncového uživatele je založená na signály koncového uživatele (například v případě přerušení předpokládaného služby).

**Server.** Může uchovávat výsledky, které jsou specifické pro jednoho uživatele end bezpečně na server, řídit a zobrazit pouze zachované výsledky:

- Chcete-li povolit koncový uživatel pro přístup k historická Sestava výsledků dříve vrátí pro tohoto uživatele ve vašem řešení, za předpokladu, že výsledky nemusí být i zachováno déle než 21 dní od počátečního dotazu koncového uživatele a (ii) zobrazí v reakci na element end u Pož na nový nebo opakovaných dotazu; nebo
- k ukládání výsledků vrácených pro proaktivní dotazu přizpůsobit v očekávání koncového uživatele je založená na signály koncového uživatele pro nižší úrovně (i) 24 hodin od času dotazu nebo (ii) až koncový uživatel odešle další dotaz pro aktualizované výsledky.

Vždy, když zachována, výsledky pro konkrétního uživatele nelze commingled s výsledky pro jiného uživatele, tj., výsledky každý uživatel musí být uchována a nabízeli samostatně.

**Obecné.** Pro všechny prezentace zachované výsledků:

- Zrušte zaškrtnutí, viditelné oznámení času, který se odesílá dotaz, patří
- uživatel k dispozici tlačítko nebo podobným způsobem znovu dotaz a získat aktualizované výsledky 
- zachovat Bing brandingu v prezentaci výsledky, a
- Odstranit (a aktualizace se nový dotaz v případě potřeby) uložené výsledky v rámci stanovených časových rámců zadán.

**F. Bez zobrazení zjišťování adresy URL.** Odpovědi na hledání v prostředí Internetu jiných vyhledávání mohou používat pouze k jedinému účelu zjišťování adresy URL zdrojů informací reaguje na dotaz z vašich uživatelů a zákazníků. Může zkopírovat tyto adresy URL v sestavě nebo podobné odpovědi, které poskytnete (i) pouze pro tohoto uživatele nebo zákazníka, v reakci na tento dotaz a (ii), zahrnuje významné další hodnotný obsah relevantní pro dotaz. Na požadavky v části 3A a 3e tyto požadavky na použití a zobrazení není určena k použití této jiné zobrazení, s výjimkou: 

- Nebude ukládat do mezipaměti, kopírovat nebo ukládání dat nebo obsahu z nebo odvozené z hledání odpovědi, než adresa URL je omezený kopírování popsané;
- Ujistěte se, že používání data (včetně adresy URL) přijaté z rozhraní API pro vyhledávání nejsou porušení libovolných příslušných zákonů a práva třetích stran; a
- Získaná z rozhraní API pro vyhledávání v rámci všech indexu vyhledávání nebo strojové učení nebo podobné aktivitě algoritmické data (včetně adresy URL) nebude používat k vytváření train, vyhodnocení a zlepšení služby, které jste či třetích stran můžou nabízet.

## <a name="next-steps"></a>Další postup
[Přehled vyhledávání odpovědí](overview.md)
