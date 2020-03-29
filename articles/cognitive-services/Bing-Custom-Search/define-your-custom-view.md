---
title: Konfigurace vlastního vyhledávání Bingu | Dokumenty společnosti Microsoft
titleSuffix: Azure Cognitive Services
description: Portál umožňuje vytvořit instanci hledání, která určuje řezy webu; domény, podstránky a webové stránky.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: c14376cc80373371ec5fcb8f22a00584a6b2f714
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220212"
---
# <a name="configure-your-bing-custom-search-experience"></a>Konfigurace vlastního vyhledávání Bingu

Instance vlastního vyhledávání umožňuje přizpůsobit prostředí vyhledávání tak, aby zahrnovalo obsah pouze z webů, na kterých uživatelům záleží. Místo vyhledávání na webu bing vyhledává pouze části webu, které vás zajímají. Pokud chcete vytvořit vlastní zobrazení webu, použijte [portál](https://customsearch.ai) Vlastní vyhledávání Bingu.

Portál umožňuje vytvořit instanci hledání, která určuje řezy webu: domény, podstránky a webové stránky, které má Bing prohledávat, a ty, které nechcete prohledávat. Portál může také navrhnout obsah, který můžete chtít zahrnout.

Při definování řezů webu použijte následující:

| Název řezu | Popis                                                                                                                                                                                                                                                                                                |
|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Domain (Doména)     | Řez domény obsahuje veškerý obsah nalezený v internetové doméně. Například, `www.microsoft.com`. Vynechání `www.` způsobí, že Bing také prohledávat subdomény domény. Pokud například zadáte `microsoft.com`, bing také `support.microsoft.com` `technet.microsoft.com`vrátí výsledky z nebo . |
| Podstránku    | Podstránka obsahuje veškerý obsah nalezený v podstránce a cesty pod ní. V cestě můžete zadat maximálně dvě podstránky. Například `www.microsoft.com/en-us/windows/`.                                                                                                                       |
| Webové stránky    | Řez webové stránky může obsahovat pouze tuto webovou stránku do vlastního vyhledávání. Volitelně můžete určit, zda chcete zahrnout podstránky.                                                                                                                                                                                  |

> [!IMPORTANT]
> Všechny domény, podstránky a webové stránky, které zadáte, musí být veřejné a indexovány bingem. Pokud vlastníte veřejný web, který chcete zahrnout do hledání, a Bing ho neindexoval, přečtěte si [podrobnosti](https://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a) o tom, jak bing indexovat. Podrobnosti o tom, jak bing aktualizovat procházený web, pokud je index zastaralý, najdete v dokumentaci pro webmastery.

## <a name="add-slices-of-the-web-to-your-custom-search-instance"></a>Přidání řezů webu do vlastní instance hledání

Při vytváření vlastní instance hledání můžete určit řezy webu: domény, podstránky a webové stránky, které chcete zahrnout nebo zablokovat ve výsledcích hledání. 

Pokud znáte řezy, které chcete zahrnout do vlastní instance hledání, přidejte je do **aktivního** seznamu instance. 

Pokud si nejste jisti, které řezy zahrnout, můžete odesílat vyhledávací dotazy bingu v podokně **Náhled** a vybrat požadované řezy. Použijte následující postup: 

1. Vyberte "Bing" z rozevíracího seznamu v podokně Náhled a zadejte vyhledávací dotaz

2. Klikněte na **Přidat web** vedle výsledku, který chcete zahrnout. Pak klikněte na OK.

>[!NOTE]
> [!INCLUDE[publish or revert](./includes/publish-revert.md)]

<a name="active-and-blocked-lists"></a>

### <a name="customize-your-search-experience-with-active-and-blocked-lists"></a>Přizpůsobení prostředí vyhledávání pomocí aktivních a blokovaných seznamů 

K seznamu aktivních a blokovaných řezů můžete získat přístup kliknutím na karty **Aktivní** a **Blokované** ve vlastní instanci vyhledávání. Řezy přidané do aktivního seznamu budou zahrnuty do vlastního hledání. Blokované řezy nebudou prohledány a ve výsledcích vyhledávání se nezobrazí.

Chcete-li určit řezy webu, které má Bing prohledávat, klikněte na kartu **Aktivní** a přidejte jednu nebo více adres URL. Chcete-li adresy URL upravit nebo odstranit, použijte možnosti ve sloupci **Ovládací prvky.** 

Při přidávání adres URL do **seznamu Aktivní** můžete přidat jednu adresu URL nebo více adres URL najednou nahráním textového souboru pomocí ikony nahrávání.

![Karta Aktivní vlastní hledání Bingu](media/file-upload-icon.png)

Chcete-li nahrát soubor, vytvořte textový soubor a určete jednu doménu, podstránku nebo webovou stránku na řádek. Soubor bude odmítnut, pokud není správně formátován.

> [!NOTE]
> * Soubor můžete nahrát pouze do seznamu **Aktivní.** Nelze ji použít k přidání řezů do seznamu **Blokováno.**  
> * Pokud seznam **Blokováno** obsahuje doménu, podstránku nebo webovou stránku, kterou jste zadali v souboru pro nahrávání, bude odebrán ze seznamu **Blokováno** a přidán do seznamu **Aktivní.**
> * Vlastní vyhledávání Bingu ignoruje duplicitní položky v nahraném souboru. 

### <a name="get-website-suggestions-for-your-search-experience"></a>Získejte návrhy webových stránek pro vyhledávání

Po přidání webových řezů do seznamu **Aktivní** vygeneruje portál vlastního vyhledávání Bingu návrhy webů a podstránek v dolní části karty. Jedná se o řezy, které si vlastní vyhledávání Bingu myslí, že je budete chtít zahrnout. Kliknutím na **Aktualizovat** získáte aktualizované návrhy po aktualizaci nastavení vlastní instance vyhledávání. Tato část je viditelná pouze v případě, že jsou k dispozici návrhy.

## <a name="search-for-images-and-videos"></a>Hledání obrázků a videí

Obrázky a videa můžete vyhledávat podobně jako webový obsah pomocí [rozhraní API pro vlastní vyhledávání obrázků Bingu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) nebo rozhraní API pro vlastní vyhledávání [videa Bingu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference). Tyto výsledky můžete zobrazit pomocí [hostovaného ui](hosted-ui.md)nebo api. 

Tato rozhraní API jsou podobná nevlastním rozhraním API [pro vyhledávání obrázků bingu](../Bing-Image-Search/overview.md) a vyhledávání `customConfig` videí [Bingu,](../Bing-Video-Search/search-the-web.md) ale prohledávají celý web a nevyžadují parametr dotazu. Další informace o práci s obrázky a videi najdete v těchto sadách dokumentace. 

## <a name="test-your-search-instance-with-the-preview-pane"></a>Testování instance hledání pomocí podokna Náhled

Instanci vyhledávání můžete otestovat pomocí podokna náhledu na pravé straně portálu a odeslat vyhledávací dotazy a zobrazit výsledky. 

1. Pod vyhledávacím polem vyberte **Moje instance**. Výsledky vyhledávání můžete porovnat se službou Bing výběrem možnosti **Bing**. 
2. Vyberte filtr bezpečného vyhledávání a trh, který chcete hledat (viz [Parametry dotazu](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)).
3. Zadejte dotaz a stisknutím klávesy Enter nebo kliknutím na ikonu hledání zobrazíte výsledky z aktuální konfigurace. Chcete-li získat odpovídající výsledky, můžete změnit typ hledání, který provádíte, klepnutím na **položku Web**, **Obrázek**nebo **Video.** 

<a name="adjustrank"></a>

## <a name="adjust-the-rank-of-specific-search-results"></a>Úprava pořadí konkrétních výsledků vyhledávání

Portál umožňuje upravit pořadí vyhledávání obsahu z konkrétních domén, podstránek a webových stránek. Po odeslání vyhledávacího dotazu v podokně náhledu obsahuje každý výsledek hledání seznam úprav, které pro něj můžete provést:  

|            |                                                                                                                                                                      |
|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Blok      | Přesune doménu, podstránku nebo webovou stránku do seznamu Blokováno. Bing vyloučí obsah z vybraného webu z zobrazování ve výsledcích hledání.                    |
| Zvýšit      | Zvýší obsah z domény nebo podstránky tak, aby byl ve výsledcích hledání vyšší.                                                                                        |
| Degradování     | Sníží úroveň obsahu z domény nebo podstránky níže ve výsledcích hledání. Můžete vybrat, zda chcete snížit úroveň obsahu z domény nebo podstránky, do které webová stránka patří. |
| Připnout na začátek | Přesune doménu, podstránku nebo webovou stránku do seznamu **Připnutý.** To vynutí, aby se webová stránka zobrazila jako nejlepší výsledek hledání daného vyhledávacího dotazu.                   |

Nastavení pořadí není k dispozici pro vyhledávání obrázků nebo videí.

### <a name="boosting-and-demoting-search-results"></a>Zvýšení a rozsvícení výsledků vyhledávání

Můžete super propagovat, propagovat nebo snížit úroveň libovolné domény nebo podstránky v seznamu **Aktivní.** Ve výchozím nastavení jsou všechny řezy přidány bez úprav pořadí. Řezy na webu, které jsou super propagované nebo propagované, jsou ve výsledcích vyhledávání vyšší (s hodnocením super boost vyšší než boost). Položky, které jsou sníženy, jsou ve výsledcích hledání seřazeny níže.

Položky můžete super propagovat, propagovat nebo suše tažné položky pomocí ovládacích prvků **Úpravy pořadí** v seznamu **Aktivní** nebo pomocí ovládacích prvků Boost a Degradte v podokně Náhled. Služba přidá řez do seznamu Aktivní a odpovídajícím způsobem upraví pořadí.

> [!NOTE] 
> Zvýšení a rozdroby domén a podstránek je jednou z mnoha metod, které vlastní vyhledávání Bing používá k určení pořadí výsledků hledání. Vzhledem k dalším faktorům ovlivňujícím pořadí různého webového obsahu se mohou účinky úpravy pořadí lišit. Podokno Náhled slouží k testování účinků úpravy pořadí výsledků hledání. 

Super zvýšení, zvýšení a snížení úrovně nejsou k dispozici pro vyhledávání obrázků a videí.

## <a name="pin-slices-to-the-top-of-search-results"></a>Připnutí řezů na začátek výsledků hledání

Portál také umožňuje připnout adresy URL k horní části výsledků hledání pro konkrétní hledané výrazy pomocí **karty Připnuté.** Zadejte adresu URL a dotaz pro určení webové stránky, která se zobrazí jako nejlepší výsledek. Všimněte si, že můžete připnout maximálně jednu webovou stránku na vyhledávací dotaz a při vyhledávání se zobrazí pouze indexované webové stránky. Připnutí výsledků není k dispozici pro vyhledávání obrázků nebo videí.

Webovou stránku můžete připnout nahoru dvěma způsoby:

* Na kartě **Připnuté** zadejte adresu URL webové stránky, kterou chcete připnout nahoru, a odpovídající dotaz.

* V podokně **Náhled** zadejte vyhledávací dotaz a klikněte na Hledat. Najděte webovou stránku, kterou chcete připnout k dotazu, a klikněte **na Připnout nahoru**. webová stránka a dotaz budou přidány do seznamu **Připnutý.**

### <a name="specify-the-pins-match-condition"></a>Určení podmínky shody špendlíku

Ve výchozím nastavení jsou webové stránky připnuty k hornímu výsledku hledání pouze v případě, že řetězec dotazu uživatele přesně odpovídá řetězci uvedenému v **seznamu Připnutý.** Toto chování můžete změnit zadáním jedné z následujících podmínek shody:

> [!NOTE]
> Všechna porovnání mezi vyhledávacím dotazem uživatele a vyhledávacím dotazem špendlíku jsou malá a velká písmena.

| Hodnota | Popis                                                                          |
|---------------|----------------------------------------------------------------------------------|
| Začíná na | Pin se shoduje, pokud řetězec dotazu uživatele začíná řetězcem dotazu špendlíku. |
| Končí   | Pin se shoduje, pokud řetězec dotazu uživatele končí řetězcem dotazu špendlíku.  |
| Contains    | Pin se shoduje, pokud řetězec dotazu uživatele obsahuje řetězec dotazu špendlíku.   |


Chcete-li změnit podmínku shody špendlíku, klikněte na ikonu úprav špendlíku. Ve sloupci **Podmínka shody dotazu** klikněte na rozevírací seznam a vyberte novou podmínku, kterou chcete použít. Potom kliknutím na ikonu uložit uložte změnu.

### <a name="change-the-order-of-your-pinned-sites"></a>Změna pořadí připnutých webů

Chcete-li změnit pořadí špendlíků, můžete je přetáhnout a upravit jejich pořadové číslo kliknutím na ikonu "upravit" ve **sloupci Ovládací prvky** v seznamu **Připnutý.**

Pokud více kolíků splňuje podmínku shody, použije vlastní vyhledávání Bingu nejvyšší v seznamu.

## <a name="view-statistics"></a>Zobrazit statistiky

Pokud jste se přihlásili k odběru vlastního vyhledávání na příslušné úrovni (viz [stránky s cenami),](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)přidá se do produkčních instancí karta **Statistika.** Na kartě Statistiky jsou uvedeny podrobnosti o tom, jak se vaše koncové body vlastního vyhledávání používají, včetně objemu hovorů, hlavních dotazů, geografické distribuce, kódů odpovědí a bezpečného vyhledávání. Podrobnosti můžete filtrovat pomocí zadaných ovládacích prvků.

## <a name="usage-guidelines"></a>Pokyny k použití

- Pro každou vlastní instanci vyhledávání je maximální počet úprav hodnocení, které můžete provést u **aktivních** a **blokovaných** řezů, omezen na 400.
- Přidání řezu na karty Aktivní nebo Blokované se počítá jako jedna úprava hodnocení.
- Zvýšení a rozsvícení se počítá jako dvě úpravy pořadí.
- Pro každou vlastní instanci vyhledávání je maximální počet pinů, které můžete provést, omezen na 200.

## <a name="next-steps"></a>Další kroky

- [Volání vlastního vyhledávání](./search-your-custom-view.md)
- [Konfigurace prostředí pro hostované uživatelské rozhraní](./hosted-ui.md)
- [Zvýraznění textu pomocí dekoračních značek](../bing-web-search/hit-highlighting.md)
- [Stránkování webových stránek](./page-webpages.md)
