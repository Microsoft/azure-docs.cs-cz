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
ms.openlocfilehash: f166ceac1ae848565f861a94781ce0500c24747e
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51289151"
---
Použití a zobrazit požadavky platí pro žádnou implementaci obsah a související informace. Například požadavky platí pro relace, metadata a dalšími signály. Toto může být k dispozici prostřednictvím volání rozhraní API pro následující:

- Vlastní vyhledávání Bingu
- Vyhledávání entit Bingu
- Vyhledávání obrázků Bingu
- Vyhledávání zpráv Bingu
- Vyhledávání videí Bingu
- Vizuální vyhledávání Bingu
- Webové vyhledávání Bingu
- Kontrola pravopisu Bingu
- Automatické návrhy Bingu

Můžete najít podrobnosti o implementaci souvisejících s těmito požadavky v dokumentaci pro konkrétní funkce a výsledky.     

## <a name="bing-spell-check-and-bing-autosuggest-apis"></a>Rozhraní API pro automatické návrhy Bingu pro kontrolu pravopisu a Bingu

Ne:

- Kopírování, uložení nebo mezipaměti všechna data, která jste dostali z kontrolu pravopisu Bingu nebo rozhraní API pro automatické návrhy Bingu.
- Pomocí dat získaných z kontrolu pravopisu Bingu nebo rozhraní API pro automatické návrhy Bingu jako součást machine learning nebo podobné aktivitě vylepšením. Nepoužívejte tato data k trénování, vyhodnocení nebo vylepšení nového nebo existujícího služeb, které může nabídnout vy nebo třetími stranami.

## <a name="definitions"></a>Definice

- *Odpověď* odkazuje na kategorie výsledků vrácená v odpovědi. Například odpověď z rozhraní API Bingu pro vyhledávání webu může obsahovat odpovědi v kategoriích webovou stránku výsledků, image, video, vizuál a zprávy.   
- *Odpověď* znamená, že všechny odpovědi a přidružená data přijatá v odpovědi na jednoho volání rozhraní API pro vyhledávání.
- *Výsledek* odkazuje na položku informací v odpovědi. Například sadu data připojená přes jeden příspěvek je výsledek v odpovědi na zprávy.
- *Hledat rozhraní API* znamená společně, vlastní vyhledávání Bingu, vyhledávání entit, vyhledávání obrázků, vyhledávání zpráv, vyhledávání videí, pro vizuální vyhledávání a webové rozhraní API pro vyhledávání. 


## <a name="search-apis"></a>Rozhraní API pro vyhledávání

Požadavky uvedené v této části platí pro rozhraní API pro vyhledávání. Rozhraní API pro vyhledávání neobsahují kontrolu pravopisu Bingu nebo pro automatické návrhy Bingu. Požadavky pro tyto dvě rozhraní API jsou popsané v předchozí části.

### <a name="internet-search-experience"></a>Možnosti vyhledávání Internet

Všechna data vrácená v odpovědi může použít jenom v Internetu vyhledávací prostředí. Vyhledávání na Internetu znamená, že obsah, zobrazí se odpovídající: 
- Je důležité a reagovat na koncového uživatele přímých dotazů nebo jiné označení zájmu hledat uživatele a záměr (například uživatel uveden vyhledávací dotaz). 
- Pomáhá uživatelům vyhledávat a přejděte do zdroje dat (například zadané adresy URL jsou implementovány jako hypertextové odkazy, takže obsah nebo attribution prokliknutelný odkaz zřetelně zobrazí s daty). Nebo pokud používáte rozhraní API pro vyhledávání entit Bingu, viditelně odkaz na adresu URL bing.com k dispozici v odpovědi, která umožňuje uživateli přejít na výsledky hledání pro odpovídající dotaz na adrese bing.com.
- Zahrnuje více výsledků pro uživatele můžete vybírat z (například se zobrazí několik výsledky z odpovědí zprávy, nebo jsou vráceny všechny výsledky, pokud je to méně než několik). 
- Je omezený na dobu odpovídající účel vyhledávání (například obrázkům miniatur jsou miniaturní výkonový zisk zobrazení uživatele). 
- Obsahuje vizuální indikaci uživateli, že obsah je výsledky hledání v Internetu (třeba příkaz, že obsah je "z webu").
- Zahrnuje kombinaci opatření vhodné zajistit, že využití data přijatá z rozhraní API pro vyhledávání nebudou porušovat libovolných příslušných zákonů a práva třetí strany. Například pokud se spoléháte na licenci typu Creative Commons, budete jednat v souladu s příslušných licenčních podmínkách. Informace, které vaše právní poradci a určí, co opatření může být vhodné.
Jedinou výjimkou požadavek prostředí pro vyhledávání na Internetu je adresa URL zjišťování, jak je popsáno dále v tomto článku. 

### <a name="restrictions"></a>Omezení

Ne:

- Kopírování, uložení nebo všechna data z odpovědi (s výjimkou uchovávání rozsahu povoleném v části "Kontinuity podnikových procesů služba" dále v tomto článku) do mezipaměti. 
- Použití dat přijatých z rozhraní API pro vyhledávání v rámci jakékoli machine learning nebo podobné aktivitě vylepšením. Nepoužívejte tato data k trénování, vyhodnocení nebo vylepšení nového nebo existujícího služeb, které může nabídnout vy nebo třetími stranami.
- Upravte obsah výsledky (jiné než se jejich přeformátování tak, aby všechny požadavky nebudou porušovat), pokud to vyžaduje zákon nebo ustanovení společností Microsoft. 
- Vynechte attribution a adresy URL přidružené k výsledku obsah.
- Změnit pořadí, třeba tak, že vynechání, výsledky zobrazené v odpovědi, pokud je zadaná, pokud to vyžaduje zákon nebo písemně uzavřené smlouvy Microsoft objednávku nebo hodnocení. (Pro API Bingu pro vlastní vyhledávání, toto pravidlo se nevztahují na změnu pořadí implementované pomocí portálu customsearch.ai.)
- Zobrazte další obsah v rámci všech součástí odpovědi způsobem, který by mohlo dojít uživatele se domnívat, že je další obsah do odpovědi. 
- Zobrazit reklamy, která není k dispozici společností Microsoft na libovolné stránce, která zobrazuje všechny odpovědi. 
- Zobrazit jakémukoliv inzerování s odpovědí (i) z obrázky Bingu, vyhledávání zpráv, vyhledávání videí nebo vizuální rozhraní API pro vyhledávání; nebo (ii), která jsou filtrovaná nebo omezené hlavně (nebo výhradně) do bitové kopie, zprávy a/nebo video nebo visual výsledky.

### <a name="notices-and-branding"></a>Oznámení a značky 

- Výrazně zahrnují funkční hypertextový odkaz [prohlášení o ochraně osobních údajů Microsoft](https://go.microsoft.com/fwlink/?LinkId=521839), téměř každý bod v uživatelské prostředí (UX), který uživateli umožňuje zadání vyhledávacího dotazu. Popisek hypertextový odkaz **prohlášení o ochraně osobních údajů Microsoft**.
- Výrazně zobrazit Bingu branding, konzistentní s [pokyny k používání ochranná známka Bingu](https://go.microsoft.com/fwlink/?linkid=833278), téměř každý bod v uživatelském prostředí, který uživateli umožňuje zadání vyhledávacího dotazu. Tyto značky musí jasně označují uživateli, že Microsoft je provozování vyhledáváním na Internetu.
- Každá odpověď (nebo část odpověď), zobrazí z Bingu pro vyhledávání na webu, vyhledávání obrázků, vyhledávání zpráv, vyhledávání videí a rozhraní API pro vizuální vyhledávání společnosti Microsoft, můžete atribut, pokud Microsoft neurčí jinak písemně používat. To je popsáno v [pokyny k používání ochranná známka Bingu](https://go.microsoft.com/fwlink/?linkid=833278). 
- Není atribut odpovědi (ani jeho části odpovědí) zobrazí z rozhraní API pro vlastní vyhledávání Bingu společnosti Microsoft, pokud Microsoft neurčí jinak písemně pro konkrétní použití.

### <a name="transferring-responses"></a>Přenos odpovědi

Pokud povolíte uživatele k přenosu odpověď z rozhraní API pro vyhledávání na jiného uživatele, například prostřednictvím zasílání zpráv aplikace nebo účtování sociálních médií, následujících podmínek: 
- Přenesené odpovědi musí:
  - Se skládají z obsahu, který je z obsahu odpovědi přenášení uživateli zobrazí bez úprav. Změny formátování jsou přípustné.
  - Nezahrnuje všechna data v podobě metadat.
  - Pro odpovědi z Bingu Web, Image, zprávy, videa a vizuální rozhraní API jazyk zobrazení označující odpovědi získaný prostřednictvím Internetu prostředí vyhledávání Bing používá technologii. Například jazyka, jako je "S využitím pomocí Bingu" nebo "Informace" Další informace o tomto obrázku můžete zobrazit v Bingu, nebo můžete použít Bing logo.
  - Pro odpovědi z rozhraní API pro vlastní vyhledávání Bingu jazyk zobrazení označující odpovědi získaný prostřednictvím Internetu vyhledávací prostředí. Například můžete zobrazit jazyka, jako je "Další informace o tomto výsledku hledání."
  - Výrazně zobrazte celý dotaz sloužící ke generování odpovědi.
  - Zahrnout viditelného odkaz nebo podobné attribution do podkladového zdroje odpověď, a to buď přímo nebo prostřednictvím vyhledávací web (bing.com, m.bing.com nebo vaše vlastní vyhledávací služba, podle vhodnosti).
- Nelze automatizovat přenos odpovědi. Převod musí být iniciovány akcí uživatele jasně prokazující záměru přenést odpověď.
- Můžete povolit pouze uživatele k přenosu odpovědi, které byly zobrazeny v reakci na dotaz přenášení uživatele.

### <a name="continuity-of-service"></a>Kontinuitu poskytování služeb 

Kopírování, uložení nebo všechna data z rozhraní API pro hledání odpovědí do mezipaměti. Povolit kontinuity podnikových procesů přístupu ke službám a vykreslování dat, ale může uchovávat výsledky pouze za následujících podmínek:

**Zařízení.** Můžete povolit uživateli zachovat výsledky na zařízení pro menší než délka (i) 24 hodin od okamžiku dotazu, nebo (ii) dokud uživatel odešle jiný dotaz pro aktualizované výsledky, za předpokladu, že uchovanou výsledky mohou být použity pouze:

- Povolit uživateli přístup k výsledky dříve pro tohoto uživatele na tomto zařízení (například v případě přerušení služby).
- K uložení výsledků vrácených pro dotaz proaktivní individuální čekat na případná uživatele, které potřebujete, na základě signálů, uživatele (například v případě přerušení očekávané služby).

**Server.** Lze zachovat výsledky, které jsou specifické pro jednoho uživatele na serveru, které sami kontrolujete a zobrazit pouze uchovanou výsledky:

- Povolit uživateli přístup k historické sestavy dříve výsledky pro tohoto uživatele ve vašem řešení. Výsledky nemusí (i) uchovávají po dobu více než 21 dní od počátečního dotazu koncového uživatele a (ii) zobrazí v reakci na nové nebo opakované dotaz uživatele.
- Ukládat výsledky dotazu proaktivní individuální čekat na případná uživatele, které potřebujete, na základě signálů, tento uživatel. Je možné uložit tyto výsledky pro menší než délka (i) 24 hodin od okamžiku dotazu, nebo (ii) dokud uživatel odešle jiný dotaz pro aktualizované výsledky.

Pokaždé, když se uchovávají, výsledky pro konkrétního uživatele nelze commingled s výsledky pro jiného uživatele. To znamená výsledky každý uživatel musí uchovávat a nabízeli samostatně.

### <a name="general"></a>Obecné 

Pro všechny presentation uchovaná výsledků:

- Zahrnout viditelné a jasně řečeno oznámení o čas, kdy byla vyslána dotazu.
- K dispozici, znamená, že uživatel s tlačítkem nebo podobné znovu odeslán dotaz a získat aktualizované výsledky. 
- Zachovat Bingu značky v prezentaci výsledků.
- Odstranit (a aktualizuje s použitím nového dotazu v případě potřeby) uložených výsledků v rámci časové rámce zadán.

### <a name="non-display-url-discovery"></a>Adresa URL zjišťování mimo zobrazení 

Odpovědi na vyhledávání na může použít pouze v prostředí Internetu jiných hledání pouze za účelem zjišťování adresy URL zdroje informací interaktivní dotaz z vašich uživatelů a zákazníků. V sestavě nebo podobné odpovědi, které zadáte, může zkopírovat tyto adresy URL:

- Pouze pro uživatele nebo zákazníka, odpověď na daný dotaz.
- Pouze v případě, že obsahuje významné další hodnotný obsah, relevantní pro dotaz.

Použijte v předchozích částech rozhraní API pro vyhledávání a požadavky na zobrazení se nedá použít s tímto použitím jiných zobrazení, kromě následujících: 

- Ukládat do mezipaměti, zkopírovat nebo ukládání všech dat nebo obsahu, nebo odvozený od reakce na vyhledávání, než je omezená kopírování adresy URL je popsáno výše.
- Zkontrolujte, že používání (včetně adresy URL) dat přijatých z rozhraní API pro vyhledávání nebudou porušovat libovolných příslušných zákonů a práva třetí strany.
- Nepoužívejte (včetně adresy URL) dat přijatých z rozhraní API pro vyhledávání v rámci jakékoli hledání index nebo machine learning nebo podobné aktivitě vylepšením. Nepoužívejte tato data k vytvoření trénování, vyhodnocení nebo vylepšení služeb, které může nabídnout vy nebo třetími stranami.

## <a name="gdpr-compliance"></a>Dodržování předpisů GDPR  

S ohledem všechny osobní údaje v souladu s Evropské unie obecné Data Protection Regulation (GDPR) a že se zpracovává souvislosti volání rozhraní API pro vyhledávání, API kontrola pravopisu Bingu a rozhraní API pro automatické návrhy Bingu víte, že se vám a společnosti Microsoft nezávislý datový řadiče v GDPR. Zodpovídáte nezávisle na sobě za dodržování GDPR.  

