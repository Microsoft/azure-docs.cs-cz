---
title: Použití a zobrazení požadavků na vyhledávací api Bingu
titleSuffix: Azure Cognitive Services
description: Požadavky na zobrazení výsledků hledání z vyhledávacích api Bingu ve vašich aplikacích.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: 5575668f164b97142e7c4b2ddb2608c3173426a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "60499881"
---
# <a name="bing-search-api-use-and-display-requirements"></a>Požadavky rozhraní API pro vyhledávání Bingu na zobrazení a použití

Tyto požadavky na použití a zobrazení se vztahují na všechny implementace obsahu a souvisejících informací z následujících vyhledávacích api Bingu, včetně vztahů, metadat a dalších signálů.

- Vlastní vyhledávání Bingu
- Vyhledávání entit Bingu
- Vyhledávání obrázků Bingu
- Vyhledávání zpráv Bingu
- Vyhledávání videí Bingu
- Vizuální vyhledávání Bingu
- Webové vyhledávání Bingu
- Kontrola pravopisu Bingu
- Automatické návrhy Bingu

## <a name="definitions"></a>Definice


|Označení  |Popis  |
|---------|---------|
|Odpověď     | Kategorie výsledků vrácených v odpovědi. Odpověď z rozhraní API pro vyhledávání na webu Bing může například obsahovat odpovědi v kategoriích výsledků webových stránek, obrázků, videí, vizuálů a zpráv. |
|Odpověď     | Všechny odpovědi a související data přijatá v reakci na jedno volání rozhraní API pro vyhledávání. |
|Výsledek    | Informace v odpovědi. Například sada dat spojená s jedním zpravodajským článkem je výsledkem v odpovědi na zprávy. |
|Rozhraní API pro vyhledávání    | společně vlastní vyhledávání Bingu, vyhledávání entit, vyhledávání obrázků, vyhledávání zpráv, vyhledávání videa, vizuální vyhledávání, vyhledávání místních podniků a řešení API pro vyhledávání na webu. |

## <a name="bing-spell-check-and-bing-autosuggest-api-restrictions"></a>Omezení rozhraní API pro kontrolu pravopisu Bingu a automatické návrhy rozhraní API Bing

Ne:

- Zkopírujte, uložte nebo uložte všechna data, která obdržíte z souborů API pro kontrolu pravopisu Bingu nebo automatického návrhu bingu.
- Data, která obdržíte z funkce Kontrola pravopisu Bingu nebo Automaticky navržená api Bingu, můžete použít jako součást jakéhokoli strojového učení nebo podobné algoritmické aktivity. Nepoužívejte tyto údaje k trénování, vyhodnocování nebo vylepšování nových nebo stávajících služeb, které byste vy nebo třetí strany mohli nabídnout.

## <a name="bing-search-apis"></a>Rozhraní API pro vyhledávání Bingu

> [!NOTE]
> Požadavky v této části platí pouze pro vyhledávací api, která nezahrnují kontrolu pravopisu Bingu nebo automatické návrhy Bingu. 

### <a name="internet-search-experience-requirements"></a>Požadavky na prostředí vyhledávání na Internetu

Všechna data vrácená v odpovědích mohou být použita pouze v prostředí vyhledávání na internetu. Prostředí pro vyhledávání na internetu znamená zobrazený obsah: 

- Je relevantní a reaguje na přímý dotaz koncového uživatele nebo jiný údaj o jejich zájmu a záměru vyhledávání (například vyhledávací dotaz označený uživatelem). 

- Pomáhá uživatelům najít zdroje dat odpovědi a přejít k jim. Například poskytnutí odkazů, na které lze kliknout z hypertextových odkazů v odpovědi.

- Zahrnuje více výsledků, ze kterých si uživatel může vybrat. 

- Jsou v umístění, které umožňuje uživatelům vyhledávat.

- Obsahuje viditelné označení, že obsah je výsledek vyhledávání na internetu. Například prohlášení, že obsah je "z webu".

- Zahrnuje jakákoli další vhodná opatření, která zajistí, že vaše data rozhraní API pro vyhledávání Bing nebudou porušovat platné zákony ani práva třetích stran. Poraďte se se svými právními poradci, abyste zjistili, jaká opatření mohou být vhodná.

Jedinou výjimkou z těchto požadavků vyhledávání na internetu je pro zjišťování adres URL, jak je popsáno dále v tomto článku. 

### <a name="restrictions"></a>Omezení

Ne:

- Zkopírujte, uložte nebo uložte všechna data z odpovědí (s výjimkou uchovávání v rozsahu [povoleném kontinuitou služby](#continuity-of-service). 

- Používejte data přijatá z vyhledávacích api jako součást jakéhokoli strojového učení nebo podobné algoritmické aktivity. Nepoužívejte tyto údaje k trénování, vyhodnocování nebo vylepšování nových nebo stávajících služeb, které byste vy nebo třetí strany mohli nabídnout.

- Upravte obsah výsledků (jiným způsobem, než je přeformátujte způsobem, který neporušuje žádný jiný požadavek), pokud to nevyžaduje zákon nebo na tom společnost Microsoft nesouhlasí. 

- Vynechte informace o atribuci a adresy URL související s obsahem výsledků.

- Změna pořadí, včetně vynechání, výsledků zobrazených v odpovědi při zadání objednávky nebo pořadí, pokud to nevyžaduje zákon nebo s tím společnost Microsoft nesouhlasila. 

    > [!NOTE]
    > Tento požadavek se nevztahuje na změnu pořadí implementované prostřednictvím portálu pro rozhraní API pro vlastní vyhledávání Bingu.

- Zobrazte další obsah v rámci libovolné části odpovědi způsobem, který by uživatele vedl k domněnce, že druhý obsah je součástí odpovědi. 

- Zobrazit inzerci, která není poskytována společností Microsoft na žádné stránce, která zobrazuje jakoukoli část odpovědi. 

- Zobrazit jakoukoli reklamu na stránkách s odpověďmi:
    - Z obrázků Bingu, hledání zpráv, vyhledávání videí nebo vizuálních vyhledávacích api
    - Které jsou filtrovány nebo omezeny především (nebo výhradně) na obrázky, zprávy a / nebo video nebo vizuální výsledky vyhledávání.

### <a name="notices-and-branding"></a>Oznámení a značky 
Do:

- Prominentní patří funkční hypertextový odkaz na [prohlášení společnosti Microsoft o zásadách ochrany osobních údajů](https://go.microsoft.com/fwlink/?LinkId=521839)v blízkosti každého bodu uživatelského prostředí (UX), který nabízí uživateli možnost zadat vyhledávací dotaz. Označení hypertextového odkazu **společnosti Microsoft k prohlášení o zásadách ochrany osobních údajů**.

- V blízkosti každého bodu uživatelského prostředí, které uživateli nabízí možnost zadat vyhledávací dotaz, se výrazně zobrazuje značka Bing v souladu s [pokyny pro používání ochranných známek Bingu.](https://go.microsoft.com/fwlink/?linkid=833278) Taková značka musí uživateli jasně uvádět, že společnost Microsoft pohání prostředí pro vyhledávání na internetu.

- Každou odpověď (nebo část odpovědi) zobrazenou z rozhraní API pro vyhledávání na webu Bing, Vyhledávání obrázků, Vyhledávání příspěvků, Vyhledávání videa a Vizuální vyhledávání můžete společnosti Microsoft přiřadit společnosti Microsoft, pokud společnost Microsoft písemně neurčí jinak pro vaše použití. To je popsáno v [pokynech pro používání ochranných známek Bing](https://go.microsoft.com/fwlink/?linkid=833278). 

Ne:

- Odpovědi atributů (nebo části odpovědí) zobrazené v rozhraní API pro vlastní vyhledávání Bingu společnosti Microsoft, pokud společnost Microsoft písemně neurčí jinak pro vaše konkrétní použití.

### <a name="transferring-responses"></a>Přenos odpovědí

Pokud povolíte uživateli přenést odpověď z rozhraní API pro vyhledávání na jiného uživatele, například prostřednictvím aplikace pro zasílání zpráv nebo odesílání na sociálních sítích, platí následující: 

- Přenesené odpovědi musí:
  - Skládá se z obsahu, který není změněn z obsahu odpovědí zobrazených předávajícímu uživateli. Změny formátování jsou přípustné.
  - Nezahrnují žádná data ve formuláři metadat.
  - U odpovědí z rozhraní API bingu, obrázků, zpráv, videa a vizuálních zobrazení označujících, že odpověď byla získána prostřednictvím prostředí pro vyhledávání na internetu využívajícím službu Bing. Můžete například zobrazit jazyk, například "Powered by Bing" nebo "Další informace o tomto obrázku na Bingu", nebo můžete použít logo Bing.
  - U odpovědí z rozhraní API pro vlastní vyhledávání Bingu byl jazyk zobrazení označující odpověď získán prostřednictvím prostředí pro vyhledávání na internetu. Můžete například zobrazit jazyk, například "Další informace o tomto výsledku hledání".
  - Viditelně zobrazí úplný dotaz použitý ke generování odpovědi.
  - Uveďte prominentní odkaz nebo podobné přiřazení k podkladovému zdroji odpovědi, a to buď přímo, nebo prostřednictvím vyhledávače (bing.com, m.bing.com nebo případně vlastní vyhledávací služby).
- Přenos odpovědí nelze automatizovat. Přenos musí být iniciován akcí uživatele, která jasně dolože záměr přenést odpověď.
- Uživateli můžete povolit pouze přenos odpovědí, které byly zobrazeny v reakci na dotaz předávajícího uživatele.

### <a name="continuity-of-service"></a>Kontinuita služeb 

Nekopírujte, neukládejte ani neukládejte žádná data z odpovědí rozhraní API pro vyhledávání. Chcete-li však umožnit kontinuitu přístupu ke službám a vykreslování dat, můžete uchovávat výsledky pouze za následujících podmínek:

#### <a name="device"></a>Zařízení

Můžete uživateli povolit uchovávat výsledky na zařízení po dobu kratší z (i) 24 hodin od okamžiku dotazu nebo (ii) dokud uživatel neodešle jiný dotaz pro aktualizované výsledky za předpokladu, že uchované výsledky mohou být použity pouze:

- Chcete-li umožnit uživateli přístup k výsledkům dříve vrácené tomuto uživateli na tomto zařízení (například v případě přerušení služby).
- Chcete-li uložit výsledky vrácené pro aktivní dotaz přizpůsobený v očekávání potřeb uživatele, na základě signálů tohoto uživatele (například v případě očekávaného přerušení služby).

#### <a name="server"></a>Server

Výsledky specifické pro jednoho uživatele můžete bezpečně zachovat na serveru, který ovládáte, a zobrazit pouze zachované výsledky:

- Chcete-li povolit uživateli přístup k historické sestavy výsledků dříve vrácených tomuto uživateli ve vašem řešení. Výsledky nemusí být (i) uchovávány déle než 21 dní od okamžiku počátečního dotazu koncového uživatele a (ii) zobrazeny v reakci na nový nebo opakovaný dotaz uživatele.
- Chcete-li uložit výsledky vrácené pro aktivní dotaz přizpůsobené v očekávání potřeb uživatele, na základě signálů tohoto uživatele. Tyto výsledky můžete uložit pro menší (i) 24 hodin od okamžiku dotazu nebo (ii), dokud uživatel odešle jiný dotaz pro aktualizované výsledky.

Kdykoli je zachováno, výsledky pro konkrétního uživatele nelze smísit s výsledky pro jiného uživatele. To znamená, že výsledky každého uživatele musí být zachovány a dodány samostatně.

### <a name="general"></a>Obecné 

Pro všechny prezentace uchovávaných výsledků:

- Zahrňte jasné viditelné oznámení o době odeslání dotazu.
- Prezentujte uživateli tlačítko nebo podobné prostředky k opětovnému dotazování a získání aktualizovaných výsledků. 
- Zachovat Bing značky v prezentaci výsledků.
- Odstranit (a aktualizovat s novým dotazem v případě potřeby) uložené výsledky v rámci zadaných časových rámců.

### <a name="non-display-url-discovery"></a>Zjišťování adresy URL bez zobrazení 

Odpovědi na vyhledávání můžete používat pouze v prostředí, které není na internetu, pouze k tomu, aby bylo možné zjistit adresy URL zdrojů informací reagujících na dotaz od uživatele nebo zákazníka. Tyto adresy URL můžete zkopírovat do sestavy nebo podobné odpovědi, kterou zadáte:

- Pouze pro tohoto uživatele nebo zákazníka, v reakci na tento dotaz.
- Pouze v případě, že obsahuje významný další cenný obsah, relevantní pro dotaz.

Předchozí části hledání api použití a požadavky na zobrazení se nevztahují na toto použití bez zobrazení, s výjimkou následujících: 

- Neukládejte do mezipaměti, nekopírujte ani neukládejte žádná data ani obsah z odpovědi vyhledávání ani z ní neodvodíte, s jiným i omezeným kopírováním adres URL popsaným dříve.
- Zajistěte, aby vaše používání dat (včetně adres URL) získaných z vyhledávacích api neporušovalo žádné platné zákony ani práva třetích stran.
- Nepoužívejte data (včetně adres URL) přijatá z vyhledávacích api jako součást indexu vyhledávání nebo strojového učení nebo podobné algoritmické aktivity. Nepoužívejte tato data k vytváření vlakových, vyhodnocovatcích nebo vylepšování služeb, které byste vy nebo třetí strany mohli nabídnout.

## <a name="gdpr-compliance"></a>Dodržování předpisů GDPR  

Pokud jde o jakékoli osobní údaje, na které se vztahuje obecné nařízení Evropské unie o ochraně osobních údajů (GDPR) a které jsou zpracovávány v souvislosti s voláním rozhraní API pro vyhledávání, rozhraní API pro kontrolu pravopisu Bing nebo rozhraní API automatického návrhu služby Bing, nezávislých správců údajů podle GDPR. Nesete nezávislý odpovědnost za dodržování GDPR.  

