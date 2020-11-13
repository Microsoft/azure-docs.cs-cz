---
title: Konfigurace prostředí pro Vlastní vyhledávání Bingu | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Portál umožňuje vytvořit instanci hledání, která určuje řezy webu. domény, podstránky a webové stránky.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: e8747b822916ba2c9ef7b45cc3cbbaa88d16dcb1
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565785"
---
# <a name="configure-your-bing-custom-search-experience"></a>Konfigurace prostředí Vlastní vyhledávání Bingu

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

Vlastní instance vyhledávání umožňuje přizpůsobit možnosti vyhledávání tak, aby zahrnovaly obsah jenom z webů, o kterých se uživatelé setkávají. Místo provádění vyhledávání v rámci webu Bing vyhledává pouze řezy webu, které vás zajímají. Pokud chcete vytvořit vlastní zobrazení webu, použijte [portál](https://www.customsearch.ai) Vlastní vyhledávání Bingu.

Portál umožňuje vytvořit instanci hledání, která určuje řezy webu: domény, podstránky a webové stránky, které má Bing Hledat, a ty, které nechcete hledat. Portál může také navrhnout obsah, který budete chtít zahrnout.

Při definování řezů webu použijte následující:

| Název řezu | Popis                                                                                                                                                                                                                                                                                                |
|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Doména     | Řez domény zahrnuje veškerý obsah nacházející se v doméně Internet. Například, `www.microsoft.com`. Vynechání `www.` způsobí, že Bing bude prohledávat i subdomény domény. Pokud například zadáte `microsoft.com` , Bing také vrátí výsledky z `support.microsoft.com` nebo `technet.microsoft.com` . |
| Podstránku    | Řez podstránky obsahuje veškerý obsah nacházející se na podstránce a v cestách pod ním. V cestě můžete zadat maximálně dvě podstránky. Například `www.microsoft.com/en-us/windows/`.                                                                                                                       |
| Stránku    | Řez webové stránky může obsahovat jenom tuto webovou stránku ve vlastním hledání. Volitelně můžete určit, zda chcete zahrnout podstránky.                                                                                                                                                                                  |

> [!IMPORTANT]
> Všechny domény, podstránky a webové stránky, které zadáte, musí být veřejné a indexované pomocí Bingu. Pokud vlastníte veřejný web, který chcete zahrnout do hledání, a Bing ho neindexoval, přečtěte si téma [dokumentace správce](https://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a) Bingu, kde najdete podrobnosti o tom, jak Bingu indexovat. Další informace o tom, jak zjistit, jestli je index neaktuální, najdete v dokumentaci správce webového serveru.

## <a name="add-slices-of-the-web-to-your-custom-search-instance"></a>Přidání řezů webu do instance vlastního vyhledávání

Při vytváření vlastní instance hledání můžete určit řezy pro web: domény, podstránky a webové stránky, které chcete zahrnout do výsledků hledání nebo zablokovat. 

Pokud znáte řezy, které chcete zahrnout do vlastní instance hledání, přidejte je do **aktivního** seznamu vaší instance. 

Pokud si nejste jistí, které řezy zahrnout, můžete odeslat vyhledávací dotazy do Bingu v podokně **náhledu** a vybrat požadované řezy. Použijte následující postup: 

1. v rozevíracím seznamu v podokně náhledu vyberte Bing a zadejte vyhledávací dotaz.

2. Klikněte na tlačítko **Přidat web** vedle výsledku, který chcete zahrnout. Pak klikněte na OK.

>[!NOTE]
> [!INCLUDE[publish or revert](./includes/publish-revert.md)]

<a name="active-and-blocked-lists"></a>

### <a name="customize-your-search-experience-with-active-and-blocked-lists"></a>Přizpůsobení možností vyhledávání pomocí aktivních a blokovaných seznamů 

Seznam aktivních a blokovaných řezů získáte tak, že ve vlastní instanci hledání kliknete na karty **aktivní** a **blokované** . Do vlastního vyhledávání budou zahrnuty řezy přidané do aktivního seznamu. Blokované řezy se nevyhledají a ve výsledcích hledání se nezobrazí.

Chcete-li určit řezy webu, které má Bing prohledávat, klikněte na kartu **aktivní** a přidejte jednu nebo více adres URL. Chcete-li upravit nebo odstranit adresy URL, použijte možnosti ve sloupci **ovládací prvky** . 

Při přidávání adres URL do **aktivního** seznamu můžete přidat jednotlivé adresy URL nebo více adres URL najednou tak, že textový soubor nahrajete pomocí ikony nahrát.

![Karta Vlastní vyhledávání Bingu aktivní](media/file-upload-icon.png)

Pokud chcete nahrát soubor, vytvořte textový soubor a zadejte jednu doménu, podstránku nebo webovou stránku na řádek. Soubor bude odmítnut, pokud není správně naformátován.

> [!NOTE]
> * Soubor můžete nahrát pouze do **aktivního** seznamu. Nemůžete ho použít k přidání řezů do seznamu **blokovaných** .  
> * Pokud seznam **blokovaných** součástí obsahuje doménu, podstránku nebo webovou stránku, kterou jste zadali v souboru odeslání, bude odebrána ze seznamu **blokovaných** a přidána do seznamu **aktivních** .
> * Duplicitní položky v souboru pro nahrání budou Vlastní vyhledávání Bingu ignorovány. 

### <a name="get-website-suggestions-for-your-search-experience"></a>Získání návrhů webů pro vyhledávání

Po přidání webových řezů do **aktivního** seznamu vlastní vyhledávání Bingu portál vygeneruje návrhy na webové stránky a podstránky v dolní části karty. Jedná se o řezy, které Vlastní vyhledávání Bingu považovat za vhodné zahrnutí. Kliknutím na **aktualizovat** získáte aktualizované návrhy po aktualizaci nastavení vlastní instance hledání. Tato část je viditelná pouze v případě, že jsou k dispozici návrhy.

## <a name="search-for-images-and-videos"></a>Hledání obrázků a videí

Obrázky a videa můžete hledat Podobně jako webový obsah pomocí [rozhraní API pro vlastní vyhledávání obrázků Bingu](/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) nebo [vlastního rozhraní API bingu pro vyhledávání videí](/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference). Tyto výsledky můžete zobrazit pomocí [hostovaného uživatelského rozhraní](hosted-ui.md)nebo rozhraní API. 

Tato rozhraní API jsou podobná rozhraním API, která nejsou vlastní [vyhledávání obrázků Bingu](../Bing-Image-Search/overview.md) a [Vvyhledávání videí Bingu](../bing-video-search/overview.md) , ale hledají celý web a nevyžadují `customConfig` parametr dotazu. Další informace o práci s obrázky a videem najdete v následujících sadách dokumentace. 

## <a name="test-your-search-instance-with-the-preview-pane"></a>Otestování instance hledání pomocí podokna náhledu

Můžete testovat instanci hledání pomocí podokna náhledu na pravé straně portálu pro odeslání vyhledávacích dotazů a zobrazení výsledků. 

1. Pod vyhledávacím polem vyberte **Moje instance**. Výsledky hledání můžete porovnat s vyhledáváním v Bingu tak, že vyberete **Bing**. 
2. Vyberte zabezpečený filtr hledání, který bude na trhu Hledat (viz [parametry dotazu](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)).
3. Zadejte dotaz a stisknutím klávesy ENTER nebo kliknutím na ikonu hledání zobrazte výsledky z aktuální konfigurace. Můžete změnit typ hledání, který provedete kliknutím na **Web** , **Obrázek** nebo **video** , abyste získali odpovídající výsledky. 

<a name="adjustrank"></a>

## <a name="adjust-the-rank-of-specific-search-results"></a>Úprava pořadí konkrétních výsledků hledání

Portál umožňuje upravit pořadí hledání obsahu z konkrétních domén, podstránek a webových stránek. Po odeslání vyhledávacího dotazu v podokně náhledu obsahuje každý výsledek hledání seznam úprav, které můžete pro něj provést:  

| Úpravu | Popis |
|------------|-------------|
| Blok      | Přesune doménu, podstránku nebo webovou stránku do seznamu blokovaných. Bing bude při zobrazení výsledků hledání vyloučit obsah z vybrané lokality.                    |
| Boost      | Zvyšuje obsah z domény nebo podstránky tak, aby byl ve výsledcích hledání vyšší.                                                                                        |
| Degradování     | Sníží úroveň obsahu z domény nebo podstránky ve výsledcích hledání. Můžete vybrat, jestli se má snížit úroveň obsahu z domény nebo podstránky, do které webová stránka patří. |
| Připnout na začátek | Přesune doménu, podstránku nebo webovou stránku na **připnutý** seznam. To vynutí, aby se webová stránka zobrazila jako výsledek hledání v daném vyhledávacím dotazu.                   |

Úprava pořadí není k dispozici pro hledání obrázků a videí.

### <a name="boosting-and-demoting-search-results"></a>Zvýšení a snížení úrovně výsledků hledání

V **aktivním** seznamu můžete zvýšit, zvýšit nebo snížit úroveň všech domén nebo podstránek. Ve výchozím nastavení jsou všechny řezy přidány bez úprav hodnocení. Řezy webu, které mají vysoký nárůst nebo zvýšení úrovně, jsou ve výsledcích hledání seřazené výše (s větším hodnocením zvýšení úrovně Super než zvýšení). Položky, které jsou ve výsledcích hledání nižší úrovně, jsou seřazené níže.

Můžete zvýšit, zvýšit nebo snížit úroveň položek pomocí **hodnocení upravit** ovládací prvky v **aktivním** seznamu nebo pomocí ovládacích prvků zvýšení a snížení úrovně v podokně náhledu. Služba přidá řez do aktivního seznamu a odpovídajícím způsobem upraví pořadí.

> [!NOTE] 
> Zvýšení a snížení úrovně domén a podstránek je jednou z mnoha metod, Vlastní vyhledávání Bingu používá k určení pořadí výsledků hledání. Vzhledem k jiným faktorům, které ovlivňují pořadí různého webového obsahu, se může lišit vliv úpravy pořadí. Pomocí podokna náhledu můžete testovat účinek úprav pořadí výsledků hledání. 

Pro hledání obrázků a videí nejsou k dispozici zvýšení úrovně super, zvýšení a snížení úrovně.

## <a name="pin-slices-to-the-top-of-search-results"></a>Připnout řezy na začátek výsledků hledání

Portál také umožňuje připnout adresy URL na začátek výsledků hledání konkrétních hledaných výrazů pomocí **připnutých** karet. Zadejte adresu URL a dotaz pro určení webové stránky, která se zobrazí jako nejvyšší výsledek. Všimněte si, že můžete připnout maximálně jednu webovou stránku na vyhledávací dotaz a v hledání budou zobrazeny pouze indexované webové stránky. Výsledky připnutí nejsou k dispozici pro hledání obrázků a videí.

Webovou stránku můžete připnout na začátek dvěma způsoby:

* Na **připnuté** kartě zadejte adresu URL webové stránky, kterou chcete připnout k hornímu, a odpovídající dotaz.

* V podokně **náhledu** zadejte vyhledávací dotaz a klikněte na tlačítko Hledat. Vyhledejte webovou stránku, kterou chcete pro svůj dotaz připnout, a klikněte na **Připnout na začátek**. Webová stránka a dotaz budou přidány do **připnutého** seznamu.

### <a name="specify-the-pins-match-condition"></a>Zadejte podmínku shody PIN kódu.

Ve výchozím nastavení jsou webové stránky připnuté pouze na začátek výsledků hledání, když řetězec dotazu uživatele přesně odpovídá jednomu, který je uveden v seznamu **připnuté** . Toto chování můžete změnit zadáním jedné z následujících podmínek shody:

> [!NOTE]
> Všechna porovnání mezi vyhledávacím dotazem uživatele a vyhledávacím dotazem kódu PIN jsou nerozlišovat velká a malá písmena.

| Hodnota | Popis                                                                          |
|---------------|----------------------------------------------------------------------------------|
| Začíná na | PIN je shoda, pokud řetězec dotazu uživatele začíná řetězcem dotazu PIN. |
| Končí na   | PIN je shoda, pokud řetězec dotazu uživatele končí řetězcem dotazu PIN.  |
| Contains    | PIN je shoda, pokud řetězec dotazu uživatele obsahuje řetězec dotazu PIN.   |


Pokud chcete změnit podmínku shody PIN kódu, klikněte na ikonu pro úpravy kódu PIN. Ve sloupci **Podmínka shody dotazů** klikněte na rozevírací seznam a vyberte novou podmínku, kterou chcete použít. Potom kliknutím na ikonu Uložit uložte změnu.

### <a name="change-the-order-of-your-pinned-sites"></a>Změna pořadí připnutéch webů

Chcete-li změnit pořadí kódů PIN, můžete je přetáhnout nebo upravit jejich číslo, a to kliknutím na ikonu Upravit ve sloupci **ovládací prvky** **připnutého** seznamu.

Pokud více kódů PIN vyhovuje podmínce shody, Vlastní vyhledávání Bingu použije v seznamu jeden nejvyšší.

## <a name="view-statistics"></a>Zobrazit statistiku

Pokud jste se přihlásili k odběru vlastního vyhledávání na příslušné úrovni (viz [stránky s cenami](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), přidá se do vaší provozní instance karta **Statistika** . Na kartě statistika se zobrazují podrobnosti o tom, jak se používají vaše vlastní koncové body hledání, včetně objemu volání, hlavních dotazů, geografické distribuce, kódů odpovědí a bezpečného vyhledávání. Podrobnosti můžete filtrovat pomocí zadaných ovládacích prvků.

## <a name="usage-guidelines"></a>Pokyny k používání

- Pro každou vlastní instanci vyhledávání je maximální počet úprav řazení, které lze provést **aktivní** a **blokované** řezy, omezen na 400.
- Přidání řezu na aktivní nebo blokované karty se počítá jako jedna úprava řazení.
- Zvyšování a snižování úrovně jako dvou úprav řazení.
- U každé vlastní instance vyhledávání je maximální počet kódů PIN, které lze provést, omezen na 200.

## <a name="next-steps"></a>Další kroky

- [Volání vlastního vyhledávání](./search-your-custom-view.md)
- [Konfigurace prostředí pro hostované uživatelské rozhraní](./hosted-ui.md)
- [Zvýraznění textu pomocí dekoračních značek](../bing-web-search/hit-highlighting.md)
- [Stránkování webových stránek](../bing-web-search/paging-search-results.md)