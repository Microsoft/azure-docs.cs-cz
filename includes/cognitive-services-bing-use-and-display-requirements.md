---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: MikeDodaro
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 04/19/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 174af83686eba665a729246be7a477b9a5054f30
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "35343917"
---
# <a name="bing-search-api-use-and-display-requirements"></a>Použít rozhraní API pro Bing hledání a zobrazit požadavky

Použití a zobrazení požadavky platí pro žádnou implementaci obsah a související informace. Například požadavky platí pro relace, metadat a jinými signály. To může být k dispozici prostřednictvím volání rozhraní API pro následující:

- Vlastní vyhledávání Bingu
- Vyhledávání entit Bingu
- Vyhledávání obrázků Bingu
- Vyhledávání zpráv Bingu
- Vyhledávání videí Bingu
- Vizuální vyhledávání Bingu
- Vyhledávání na webu Bingu
- Kontrola pravopisu Bingu
- Automatické návrhy Bingu

Můžete najít podrobnosti implementace související s těmito požadavky v dokumentaci pro konkrétní funkce a výsledky.     

## <a name="bing-spell-check-and-bing-autosuggest-apis"></a>Kontrola pravopisu v Bingu a Bing pro automatické návrhy rozhraní API

Ne:

- Kopírování, uložení nebo mezipaměti žádná data, které jste získali od kontrola pravopisu Bing nebo rozhraní API pro automatické návrhy v Bingu.
- Pomocí dat získaných ze služby Bing kontrola pravopisu nebo rozhraní API pro automatické návrhy v Bingu v rámci machine learning či podobné algoritmické aktivity. Nepoužívejte tato data pro trénování, vyhodnocení nebo vylepšení nový nebo existující služby, které může nabídnout vám či třetích stran.

## <a name="definitions"></a>Definice

- *Odpověď* odkazuje na kategorii výsledky vrácené v odpovědi. Například na odpověď od rozhraní API služby Bing webové Search můžete zahrnout odpovědi do kategorie výsledky webovou stránku, image, video, visual a zprávy.   
- *Odpověď* znamená všechny odpovědi a související data přijatá v reakci na jednoho volání rozhraní API pro vyhledávání.
- *Výsledek* odkazuje na položku informací v odpovědi na dotaz. Například sada dat, které jsou připojené pomocí jednoho aktuální článek je výsledek v odpovědi na zprávy.
- *Rozhraní API pro vyhledávání* znamená souhrnně, Bing vlastní vyhledávání, Entity vyhledávání, hledání bitové kopie, zprávy vyhledávání, hledání Video, Visual vyhledávání a webové rozhraní API pro vyhledávání. 


## <a name="search-apis"></a>Rozhraní API pro vyhledávání

Požadavky v této části se vztahují na rozhraní API pro vyhledávání. Rozhraní API pro vyhledávání nezahrnují kontrola pravopisu Bing nebo automatické návrhy v Bingu. Požadavky pro tyto dvě rozhraní API jsou popsané v předchozí části.

### <a name="internet-search-experience"></a>Práci vyhledávání v Internetu

Všechna data v odpovědi může použít pouze v prostředí Internetu vyhledávání. Hledání Internetu znamená obsah zobrazit, a to: 
- Je důležité a citlivé přímý dotaz koncového uživatele nebo jiné údaj o zájmu hledat uživatele a záměr (například uživatele uvedené vyhledávací dotaz). 
- Pomáhají uživatelům najít a přejděte do zdroje dat (například zadané adresy URL jsou implementované jako hypertextové odkazy, tak, aby obsah nebo porušení prokliknutelný odkaz zřetelně zobrazeny s daty). Nebo, pokud používáte rozhraní API služby Bing Entity Search, viditelně propojit vyhledávače bing.com adresy URL poskytnuté v odpovědi, která umožňuje uživatelům přejděte na výsledky hledání relevantní dotaz na vyhledávače bing.com.
- Obsahuje více výsledků pro uživatele k výběru (například několik výsledky z odpovědí zprávy se zobrazují, nebo jsou vráceny všechny výsledky, pokud je to méně než několik). 
- Je omezený na dobu příslušné hledání účel (například obrázek, který miniatur jsou miniaturu proměnlivé velikosti v poměru k zobrazení uživatele). 
- Zahrnuje ukázala pro uživatele, že obsah je výsledky hledání v Internetu (třeba příkaz, že obsah je "z webu").
- Zahrnuje jinou kombinaci opatření odpovídající Ujistěte se, že používání data přijatá z rozhraní API pro vyhledávání nejsou porušení libovolných příslušných zákonů a práva třetích stran. Například pokud se spoléháte na licenci, licence Creative Commons, budete jednat v souladu s příslušných licenčních podmínek. Prohlédněte, které vaše právní poradci, chcete-li zjistit, co mohou být opatření vhodné.
Jedinou výjimkou požadavek rozhraní vyhledávání na Internetu je zjišťování adresy URL, jak je popsáno dále v tomto článku. 

### <a name="restrictions"></a>Omezení

Ne:

- Kopírování, uložení nebo mezipaměti žádná data z odpovědi (s výjimkou uchování v rozsahu povoleném podle části "Kontinuity služby" později v tomto článku). 
- Použijte data přijatá z rozhraní API pro vyhledávání v rámci machine learning či podobné algoritmické aktivity. Nepoužívejte tato data pro trénování, vyhodnocení nebo vylepšení nový nebo existující služby, které může nabídnout vám či třetích stran.
- Upravit obsah výsledky (jiné než chcete přeformátujte je způsobem, který není v rozporu s ostatními požadavky), pokud to vyžaduje zákon nebo ustanovení společností Microsoft. 
- Vynechte uvedení a adresy URL přidružené výsledek obsahu.
- Změnit pořadí, třeba tak, že opomenutí, výsledky zobrazené v odpověď, pokud je k dispozici, pokud to není vyžadováno zákonem objednávku nebo hodnocení nebo souhlas s microsoftem. (Rozhraní API služby Bing vlastní vyhledávání toto pravidlo nevztahuje na změny pořadí implementovaná prostřednictvím portálu customsearch.ai.)
- Zobrazte další obsah v rámci všech součástí odpověď způsobem, který povede uživatele se domnívat, že jiný obsah je součástí odpovědi. 
- Zobrazit reklamy, který není od Microsoftu na libovolné stránce, který zobrazuje všechny odpovědi. 
- Zobrazit jakémukoliv inzerování s odpovědí (i) z bitové kopie Bingu zprávy vyhledávání, hledání Video nebo Visual rozhraní API pro vyhledávání; nebo (ii), jsou filtrovány nebo jsou omezena především (nebo výhradně) na bitovou kopii, zprávy nebo video nebo visual výsledky.

### <a name="notices-and-branding"></a>Oznámení a značka 

- Výrazně zahrnují funkční hypertextový odkaz na [prohlášení o ochraně osobních údajů Microsoft](https://go.microsoft.com/fwlink/?LinkId=521839), téměř každý bod v uživatelském rozhraní (UX), které nabízí možnost vyhledávací dotaz vstup uživatele. Označení hypertextový odkaz **prohlášení o ochraně osobních údajů Microsoft**.
- Výrazně zobrazí Bing branding konzistentní s [Bing ochranná známka využití pokyny](https://go.microsoft.com/fwlink/?linkid=833278), téměř každý bod v činnosti koncového uživatele, který nabízí možnost vyhledávací dotaz vstup uživatele. Takové branding musí jasně označují uživateli, aby Microsoft je pohánějící Možnosti Internetu vyhledávání.
- Atribut jednotlivých odpovědi (nebo její části odpověď), zobrazí z hledání webové služby Bing, bitové kopie vyhledávání, hledání zprávy, Video vyhledávání a Visual rozhraní API pro vyhledávání společnosti Microsoft, pokud Microsoft jinak zápis pro vaše použití. To je popsáno v [Bing ochranná známka využití pokyny](https://go.microsoft.com/fwlink/?linkid=833278). 
- Není atribut odpovědí (nebo její části odpovědí) zobrazí z rozhraní API služby Bing vlastní Search společnosti Microsoft, pokud Microsoft jinak zápis pro vaše konkrétní použití.

### <a name="transferring-responses"></a>Přenášení odpovědí

Pokud povolíte uživatelům přenosu odpověď z rozhraní API pro vyhledávání na jiného uživatele, například prostřednictvím zasílání zpráv aplikace nebo sociálních příspěvků, následujících podmínek: 
- Musí být přenášená odpovědí:
  - Obsahovat obsah, který je z obsahu odpovědi přenášení uživateli zobrazí beze změny. Změny formátování jsou přípustné.
  - Nezahrnovat žádná data v podobě metadat.
  - Pro odpovědi z webové služby Bing, Image, zprávy, Video a vizuální rozhraní API jazyk zobrazení, která určuje odpověď získanou prostřednictvím Internetu vyhledávání prostředí používá technologii Bing. Například můžete zobrazit jazyk, jako je například "Zapnuté podle Bing" nebo "Informace" Další informace o tuto bitovou kopii na Bing nebo můžete použít Bing logo.
  - Pro odpovědi z rozhraní API služby Bing vlastní Search jazyk zobrazení, která určuje odpověď získanou rozhraní vyhledávání Internetu. Například můžete zobrazit jazyk, jako je například "Další informace o výsledek vyhledávání."
  - Výrazně zobrazte celý dotaz, použít k vygenerování odpovědi.
  - Zahrnout viditelného odkaz nebo podobné uvedení na podkladový zdroj odpověď, a to buď přímo nebo prostřednictvím vyhledávacího webu (vyhledávače bing.com, m.bing.com nebo vlastní vyhledávací službě, podle vhodnosti).
- Nemusí automatizovat přenos odpovědi. Přenos musí být iniciovány akcí uživatele jasně prokazující záměrem přenos odpověď.
- Můžete povolit pouze uživatele k přenosu odpovědi, které byly zobrazeny v odpovědi na dotaz přenášení uživatele.

### <a name="continuity-of-service"></a>Kontinuitu poskytování služeb 

Kopírování, uložení nebo žádná data z rozhraní API pro hledání odpovědí do mezipaměti. Pokud chcete povolit kontinuity přístup k službě a vykreslování dat, však může uchovávat výsledky výhradně za následujících podmínek:

**zařízení.** Může povolit uživatele, aby zachovaly výsledky na zařízení pro nižší úrovně (i) 24 hodin od času dotazu, nebo (ii) dokud uživatel odešle další dotaz pro aktualizované výsledky, za předpokladu, že udržených výsledky mohou být použity pouze:

- Chcete-li povolit uživatelům přístup k výsledky, které předtím vrátila pro tohoto uživatele na tomto zařízení (například v případě přerušení poskytování služeb).
- K ukládání výsledků vrácených pro proaktivní dotazu přizpůsobit v očekávání uživatele potřeby, podle jeho signály (například v případě přerušení předpokládaného služby).

**Server.** Může zachovaly výsledky, které jsou specifické pro jednoho uživatele bezpečně na serveru, který ovládáte a zobrazit pouze zachované výsledky:

- Chcete-li povolit uživatelům přístup k historická Sestava výsledků předtím vrátila pro tohoto uživatele ve vašem řešení. Výsledky nemusí (i) zachovává déle než 21 dní od počátečního dotazu koncového uživatele a (ii) zobrazí v reakci na nové nebo opakovaných dotaz uživatele.
- K ukládání výsledků vrácených pro proaktivní dotazu přizpůsobit v očekávání uživatele potřeby, podle jeho signály. Můžete uložit tyto výsledky pro nižší úrovně (i) 24 hodin od času dotazu, nebo (ii) dokud uživatel odešle další dotaz pro aktualizované výsledky.

Vždy, když zachována, výsledky pro konkrétního uživatele nelze commingled s výsledky pro jiného uživatele. To znamená musí být výsledky jednotlivých uživatelů uchována a nabízeli samostatně.

### <a name="general"></a>Obecné 

Pro všechny prezentace zachované výsledků:

- Zahrnout jasné, viditelné oznámení o čas odeslání dotazu.
- Aktualizovat přítomen, uživatel s tlačítkem nebo podobné znamená znovu dotaz a získat výsledky. 
- Zachovat Bing brandingu v prezentaci výsledků.
- Odstranit (a aktualizace se nový dotaz v případě potřeby) uložené výsledky v rámci stanovených časových rámců zadán.

### <a name="non-display-url-discovery"></a>Zjišťování adresy URL bez zobrazení 

Odpovědi na hledání v prostředí Internetu jiných vyhledávání mohou používat pouze k jedinému účelu zjišťování adresy URL zdrojů informací reaguje na dotaz z vašich uživatelů a zákazníků. V sestavě nebo podobné odpovědi, které poskytnete může zkopírovat tyto adresy URL:

- Pouze pro tohoto uživatele nebo zákazníka v reakci na tento dotaz.
- Pouze v případě, že obsahuje významné další hodnotný obsah, relevantní pro dotaz.

Použijte v předchozích částech rozhraní API pro vyhledávání a zobrazení požadavky se nevztahují na tuto použijte jiné zobrazení, s těmito výjimkami: 

- Ukládat do mezipaměti, kopírovat nebo ukládání všech dat nebo obsahu, nebo odvozené od hledání odpovědi, než je omezená URL kopírování popsané.
- Zkontrolujte, zda používání data (včetně adresy URL) přijaté z rozhraní API pro vyhledávání nejsou porušení libovolných příslušných zákonů a práva třetích stran.
- Nepoužívejte získaná z rozhraní API pro vyhledávání v rámci hledání index nebo machine learning nebo podobné aktivitě algoritmické data (včetně adresy URL). Nepoužívejte tato data k vytváření train, vyhodnocení a zlepšení služby, které může nabídnout vám či třetích stran.

## <a name="gdpr-compliance"></a>GDPR dodržování předpisů  

S ohledem na souladu Evropské unie obecné nařízení ochrany dat (GDPR) a že je zpracování souvislosti s volání rozhraní API pro vyhledávání, API kontrola pravopisu Bing nebo rozhraní API pro automatické návrhy v Bingu všechna osobní data víte, že jsou jste s Microsoftem nezávislé data řadiče v části GDPR. Jste zodpovědní nezávisle na vaše dodržování předpisů GDPR.  

