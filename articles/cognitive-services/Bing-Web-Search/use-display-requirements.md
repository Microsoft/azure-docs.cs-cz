---
title: Použití a zobrazení požadavků pro rozhraní API pro vyhledávání Bingu
titleSuffix: Azure Cognitive Services
description: Požadavky na zobrazení výsledků hledání z rozhraní API pro vyhledávání Bingu ve vašich aplikacích.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: 5575668f164b97142e7c4b2ddb2608c3173426a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "60499881"
---
# <a name="bing-search-api-use-and-display-requirements"></a>Požadavky rozhraní API pro vyhledávání Bingu na zobrazení a použití

Tyto požadavky na použití a zobrazení se vztahují na veškerou implementaci obsahu a související informace z následujících rozhraní API pro vyhledávání Bingu, včetně vztahů, metadat a dalších signálů.

- Vlastní vyhledávání Bingu
- Vyhledávání entit Bingu
- Vyhledávání obrázků Bingu
- Vyhledávání zpráv Bingu
- Vyhledávání videí Bingu
- Vizuální vyhledávání Bingu
- Vyhledávání na webu Bingu
- Kontrola pravopisu Bingu
- Automatické návrhy Bingu

## <a name="definitions"></a>Definice


|Období  |Popis  |
|---------|---------|
|Odpověď     | Odpověď vrátila kategorii výsledků. Odpověď z rozhraní API Bingu pro vyhledávání na webu například může obsahovat odpovědi v kategoriích výsledků webové stránky, obrázku, videa, vizuálu a novinkách. |
|Odpověď     | Jakékoli a všechny odpovědi a přidružená data přijatá v reakci na jedno volání rozhraní API pro hledání. |
|Výsledek    | Položka informací v odpovědi. Například sada dat propojená s jedním článkem novinek je výsledkem odpovědi na zprávy. |
|Rozhraní API pro vyhledávání    | souhrnně Vlastní vyhledávání Bingu, Vyhledávání entit, Vyhledávání obrázků, Vyhledávání zpráv, Vyhledávání videí, Vizuální vyhledávání, místní hledání firmy a Vyhledávání na webu rozhraní API. |

## <a name="bing-spell-check-and-bing-autosuggest-api-restrictions"></a>Omezení Kontrola pravopisu Bingu a rozhraní API pro automatické návrhy Bingu

Ne:

- Data, která obdržíte z Kontrola pravopisu Bingu nebo Automatické návrhy Bingu API, můžete kopírovat, ukládat nebo ukládat do mezipaměti.
- Data, která obdržíte z Kontrola pravopisu Bingu nebo Automatické návrhy Bingu rozhraní API, můžete použít jako součást všech strojových kurzů nebo podobných neinteraktivních aktivit. Nepoužívejte tato data ke školení, hodnocení nebo vylepšení nových nebo stávajících služeb, které vám nebo mohou nabídnout třetí strany.

## <a name="bing-search-apis"></a>Rozhraní API pro vyhledávání Bingu

> [!NOTE]
> Požadavky v této části se vztahují jenom na rozhraní API pro vyhledávání, která nezahrnují Kontrola pravopisu Bingu ani Automatické návrhy Bingu. 

### <a name="internet-search-experience-requirements"></a>Požadavky na hledání na internetu

Všechna data vrácená v odpovědích se dají použít jenom v prostředích pro hledání v Internetu. Prostředí pro vyhledávání v Internetu znamená zobrazený obsah: 

- Má význam a reagovat na přímý dotaz koncového uživatele nebo jiné označení jejich zájmu a záměru hledání (například uživatelem zadaný vyhledávací dotaz). 

- Pomáhá uživatelům najít zdroje dat odpovědi a přejít na ně. Zadejte například odkazy na odkaz z odkazů v odpovědi.

- Obsahuje více výsledků pro uživatele, kteří mají vybrat. 

- Existují v umístění, které umožňuje uživatelům vyhledávání.

- Obsahuje viditelnou indikaci, že obsah je výsledkem hledání v Internetu. Například příkaz, který je obsahem "z webu".

- Zahrnuje jakékoli další vhodné míry, které zajistí, aby vaše data Vyhledávání Bingu API neporušila žádné platné zákony ani práva třetích stran. Pokud chcete zjistit, jaké míry můžou být vhodné, Projděte si právní poradce.

Jedinou výjimkou jsou tyto požadavky na vyhledávání na internetu pro zjišťování adresy URL, jak je popsáno dále v tomto článku. 

### <a name="restrictions"></a>Omezení

Ne:

- Kopírovat, ukládat nebo ukládat veškerá data z odpovědí (s výjimkou uchování v rozsahu povoleném [kontinuitou služeb](#continuity-of-service). 

- Data přijatá z rozhraní API pro hledání použijte jako součást všech strojových kurzů nebo podobných aktivit. Nepoužívejte tato data ke školení, hodnocení nebo vylepšení nových nebo stávajících služeb, které vám nebo mohou nabídnout třetí strany.

- Úprava obsahu výsledků (jiné než jejich přeformátování způsobem, který porušuje žádné jiné požadavky), pokud to není vyžadováno zákonem nebo schváleným společností Microsoft. 

- Vynechejte informace o přidělení a adresy URL přidružené k obsahu výsledku.

- Změna pořadí, včetně vynechání, výsledků zobrazených v odpovědi v případě, že je poskytnuta objednávka nebo hodnocení, pokud to není vyžadováno zákonem nebo schváleným společností Microsoft. 

    > [!NOTE]
    > Tento požadavek neplatí pro přeřazení implementovaná prostřednictvím portálu pro rozhraní API pro vlastní vyhledávání Bingu.

- Zobrazit další obsah v rámci libovolné části reakce způsobem, který by pomohlo, aby se uživatel domnívat, že je druhý obsah součástí odpovědi. 

- Zobrazit reklamu, kterou společnost Microsoft neposkytuje, na jakékoli stránce, která zobrazuje jakoukoli část odpovědi. 

- Zobrazit reklamu na stránkách s odpověďmi:
    - Z image Bingu, Vyhledávání zpráv, Vyhledávání videí nebo rozhraní API Vizuální vyhledávání
    - Které jsou filtrovány nebo omezeny primárně (nebo výhradně) na výsledky obrázků, zpráv a videa nebo vizuálních výsledků hledání.

### <a name="notices-and-branding"></a>Oznámení a branding 
Postup

- Výrazně obsahuje funkční hypertextový odkaz na [prohlášení o zásadách ochrany osobních údajů společnosti Microsoft](https://go.microsoft.com/fwlink/?LinkId=521839)poblíž každého bodu uživatelského prostředí (UX), které uživateli nabízí možnost zadat vyhledávací dotaz. Označte hypertextový odkaz **prohlášení Microsoftu o zásadách ochrany osobních údajů**.

- Výrazným zobrazením brandingu Bingu, který je v souladu s [pokyny pro použití ochranné známky Bingu](https://go.microsoft.com/fwlink/?linkid=833278), poblíž každého místa v uživatelském rozhraní, které nabízí uživateli možnost zadat vyhledávací dotaz. Tato značka musí jasně uvádět uživatele, že Microsoft provádí vyhledávání v Internetu.

- Každou odpověď (nebo část odpovědi), která se zobrazuje z Vyhledávání na webu Bingu, Vyhledávání obrázků, Vyhledávání zpráv, Vyhledávání videí a Vizuální vyhledávání rozhraní API pro Microsoft, můžete vytvořit sami, pokud společnost Microsoft neurčí jinak v zápisu pro vaše použití. Tento postup je popsaný v tématu [pokyny k používání ochranné známky Bingu](https://go.microsoft.com/fwlink/?linkid=833278). 

Ne:

- Odpovědi na atributy (nebo části odpovědí) zobrazené z rozhraní API pro vlastní vyhledávání Bingu společnosti Microsoft, pokud společnost Microsoft neurčí jinak v zápisu pro konkrétní použití.

### <a name="transferring-responses"></a>Přenášení odpovědí

Pokud uživateli povolíte přenos odpovědi z rozhraní API pro hledání na jiného uživatele, například prostřednictvím aplikace pro zasílání zpráv nebo příspěvků na sociálních médiích, platí následující: 

- Přenesené odpovědi musí:
  - Skládá se z obsahu, který se nezměnil z obsahu odpovědí zobrazených pro předávajícího uživatele. Jsou přípustné změny formátování.
  - Do formuláře metadat nezahrnujte žádná data.
  - V případě odpovědí z webu Bingu, obrázku, zpráv, videa a vizuálních rozhraní API Bingu se zobrazí jazyk zobrazení, který indikuje, že byla odpověď získaná prostřednictvím vyhledávacího prostředí pro Internet, které využívá Bing. Můžete například zobrazit jazyk, jako je například "napájeno pomocí Bingu" nebo "získat další informace o tomto obrázku v Bingu" nebo můžete použít logo Bingu.
  - V případě odpovědí z rozhraní API pro vlastní vyhledávání Bingu se zobrazuje jazyk zobrazení, který indikuje, že odpověď byla získána prostřednictvím internetového hledání. Můžete například zobrazit jazyk, například "Další informace o tomto výsledku hledání".
  - Výrazně zobrazí úplný dotaz použitý k vygenerování odpovědi.
  - Do podkladového zdroje odpovědi zahrňte výrazný odkaz nebo podobné označení, a to buď přímo, nebo prostřednictvím vyhledávacího modulu (bing.com, m.bing.com nebo vlastní vyhledávací služby podle potřeby).
- Nemůžete automatizovat přenos odpovědí. Přenos musí být zahájen akcí uživatele jasně evidencing záměrem přenést odpověď.
- Uživateli můžete povolit přenos odpovědí, které se zobrazily v reakci na dotaz předávajícího uživatele.

### <a name="continuity-of-service"></a>Kontinuita provozu 

Nekopírujte, neukládejte ani neukládají žádná data z odpovědí rozhraní API pro hledání. Chcete-li však povolit kontinuitu přístupu ke službě a vykreslování dat, můžete výsledky uchovávat pouze za následujících podmínek:

#### <a name="device"></a>Zařízení

Uživateli může být umožněno uchovat výsledky na zařízení za méně než 24 hodin od času dotazu nebo (II), dokud uživatel neodešle jiný dotaz na aktualizované výsledky za předpokladu, že se zachované výsledky můžou použít jenom:

- Chcete-li uživateli povolit přístup k výsledkům, které již byly vráceny tomuto uživateli na daném zařízení (například v případě přerušení služby).
- Pro uložení výsledků vrácených pro přizpůsobení proaktivního dotazu při předvídání požadavků uživatele, a to na základě signálů tohoto uživatele (například v případě předpokládaného přerušení služby).

#### <a name="server"></a>Server

Můžete uchovávat výsledky specifické pro jednoho uživatele bezpečně na serveru, který ovládáte, a zobrazit pouze zachované výsledky:

- Chcete-li uživateli povolit přístup k historické sestavě výsledků, které dříve vrátili tomuto uživateli ve vašem řešení. Výsledky nemusí být (i) zachované po dobu delší než 21 dní od počátečního dotazu koncového uživatele a (II) zobrazené v reakci na nový nebo opakovaný dotaz uživatele.
- Pro uložení výsledků vrácených pro vlastní přizpůsobené dotazování při předvídání požadavků uživatelů na základě signálů tohoto uživatele. Tyto výsledky můžete uložit na méně než 24 hodin od času dotazu, nebo (II), dokud uživatel neodešle jiný dotaz na aktualizované výsledky.

Kdykoli je zachováno, výsledky pro konkrétního uživatele nelze commingled s výsledky pro jiného uživatele. To znamená, že výsledky každého uživatele musí být zachovány a dodávány samostatně.

### <a name="general"></a>Obecné 

Pro všechny prezentace uchovávaných výsledků:

- Zahrňte jasné a viditelné oznámení o tom, kdy byl dotaz odeslán.
- Prezentujte uživateli tlačítko nebo podobným způsobem, jak znovu dotazovat a získávat aktualizované výsledky. 
- Zachovejte značky Bingu v prezentaci výsledků.
- V případě potřeby odstraňte (a aktualizujte nový dotaz) uložené výsledky v rámci zadaných časových období.

### <a name="non-display-url-discovery"></a>Zjišťování adresy URL bez zobrazení 

Odpovědi na hledání můžete použít jenom v prostředí vyhledávání v Internetu, abyste mohli jenom zjistit, jaké adresy URL zdrojů informací reagují na dotaz od uživatele nebo zákazníka. Tyto adresy URL můžete zkopírovat do sestavy nebo podobné reakce, kterou zadáte:

- Pouze pro tohoto uživatele nebo zákazníka v reakci na tento dotaz.
- Pouze v případě, že obsahuje významný další cenný obsah, který je relevantní pro dotaz.

Předchozí části použití a zobrazení požadavků rozhraní API pro vyhledávání se nevztahují na toto nezobrazované použití, s výjimkou následujících: 

- Neukládat do mezipaměti, kopírovat ani ukládat žádná data nebo obsah z, nebo z, z odpovědi na vyhledávání, kromě dříve popsaných souborů s omezením.
- Zajistěte, aby vaše používání dat (včetně adres URL) přijatých z rozhraní API pro hledání neporušilo žádné platné zákony ani práva třetích stran.
- Nepoužívejte data (včetně adres URL) přijatých z rozhraní API pro hledání jako součást libovolného indexu hledání nebo strojového učení nebo podobného algoritmu. Tato data Nepoužívejte k vytváření výukových, vyhodnocování nebo zlepšování služeb, které mohou nabídnout třetí strany.

## <a name="gdpr-compliance"></a>Dodržování předpisů GDPR  

V souvislosti se všemi osobními údaji, které jsou předmětem Obecné nařízení o ochraně osobních údajů Evropské unie (GDPR) a které se zpracovávají v souvislosti s voláními rozhraní API pro vyhledávání, rozhraní API Bingu pro kontrolu pravopisu nebo rozhraní API pro automatické návrhy Bingu, rozumíte tomu, že jste vy a Microsoft jsou na GDPR nezávislé řadiče dat. Nezávisle zodpovídáte za vaše dodržování předpisů pomocí GDPR.  

