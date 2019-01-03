---
title: Konfigurace prostředí pro vlastní vyhledávání Bingu | Dokumentace Microsoftu
titlesuffix: Azure Cognitive Services
description: Popisuje, jak vytvořit web a vertikální vyhledávací služby
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: aahi
ms.openlocfilehash: ac5ba80740c47dd71a30bb20aab4a54829eac822
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53597796"
---
# <a name="configure-your-bing-custom-search-experience"></a>Konfigurace prostředí pro vlastní vyhledávání Bingu

Instanci vlastního vyhledávání umožňuje přizpůsobit možnosti vyhledávání zahrnout obsah jenom z webů, které vaši uživatelé záleží. Místo prohledávání celé webové vyhledává Bing pouze řezy webu, které vás zajímají. Pokud chcete vytvořit vlastní zobrazení webu, použijte [portál](https://customsearch.ai) Vlastní vyhledávání Bingu.

Na portálu umožňuje vytvořit instanci hledání, který určuje řezy webu: domény, podstránky a webové stránky, které chcete Bingu pro vyhledávání, a ty, které nechcete, aby ji k prohledávání. Na portálu můžete také navrhnout obsah, který chcete zahrnout.

Při definování řezy z webu, použijte následující:

| Název řezu | Popis                                                                                                                                                                                                                                                                                                |
|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Domain (Doména)     | Řez domény zahrnuje veškerý obsah v rámci rozsahu internetové domény nebyl nalezen. Například, `www.microsoft.com`. Vynechání `www.` způsobí, že Bingu také hledat subdomény doméně. Pokud zadáte například `microsoft.com`, Bing také vrátí výsledky z `support.microsoft.com` nebo `technet.microsoft.com`. |
| Podstránku    | Podstránku řez zahrnuje veškerý obsah v podstránku a cesty pod ní. V cestě můžete zadat maximálně dvě podstránky. Například `www.microsoft.com/en-us/windows/`.                                                                                                                       |
| Webová stránka    | Řez webová stránka může obsahovat pouze webové stránce Vlastní vyhledávání. Volitelně můžete zadat, jestli se má zahrnout podstránky.                                                                                                                                                                                  |

> [!IMPORTANT]
> Všechny domény, podstránky a webové stránky, které zadáte, musí být veřejné a indexované bingem. Pokud máte licence, kterou chcete do hledání zahrnout veřejné lokality a Bing ještě indexovat jej, najdete v článku Bing [správce webového serveru dokumentaci](https://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a) podrobnosti o tom, jak Bingu se. Podrobnosti o tom, jak Bingu aktualizovat procházené lokalitu, pokud je aktuální index také v dokumentaci správce webového serveru.

## <a name="add-slices-of-the-web-to-your-custom-search-instance"></a>Přidání části webu do vaší instance vlastního vyhledávání

Při vytváření vaší instance vlastního hledání můžete určit řezy webu: domény, podstránky a webové stránky, které chcete zahrnout nebo blokován z výsledků vyhledávání. 

Pokud víte, že řezy, které chcete zahrnout do vaší instance vlastního hledání, je přidat k vaší instanci **aktivní** seznamu. 

Pokud si nejste jistí, řezy, které chcete zahrnout, můžete poslat dotazy vyhledávání Bingu ve **ve verzi Preview** podokně a vyberte řezy, které chcete. Použijte následující postup: 

1. Vyberte z rozevíracího seznamu v podokně náhledu "Bingu" a zadejte vyhledávací dotaz

2. Klikněte na tlačítko **přidat web** vedle výsledků, které chcete zahrnout. Pak klikněte na OK.

>[!NOTE]
> [!INCLUDE[publish or revert](./includes/publish-revert.md)]

<a name="active-and-blocked-lists"></a>

### <a name="customize-your-search-experience-with-active-and-blocked-lists"></a>Přizpůsobit možnosti vyhledávání s aktivní a uzavřeno seznamy 

Seznam aktivních a zablokování řezů se zpřístupní po kliknutí na **aktivní** a **blokováno** karty ve vaší instance vlastního hledání. Kolekce obsahuje nějaké řezy, které jsou přidány do seznamu aktivní bude součástí vašeho vlastního hledání. Blokované kolekce obsahuje nějaké řezy nebudou vyhledávána a nezobrazí se ve výsledcích hledání.

K určení řezy webu má Bingu pro vyhledávání, klikněte na tlačítko **aktivní** karta a přidat jeden nebo více adres URL. Upravit nebo odstranit adresy URL, pomocí možností v části **ovládací prvky** sloupce. 

Při přidávání adresy URL **aktivní** seznamu můžete přidat adresy URL jednoho nebo více adres URL najednou tak, že nahrajete do textového souboru pomocí nahrát ikonu.

![Bing vlastní vyhledávání aktivní kartě](media/file-upload-icon.png)

Pokud chcete nahrát soubor, vytvořte textový soubor a zadejte jednu doménu, podstránku nebo webové stránky na řádek. Váš soubor odmítne, pokud není správně naformátovaná.

> [!NOTE]
> * Můžete nahrát jenom soubor, který chcete **aktivní** seznamu. Nelze ho použít k přidání řezy, aby **blokováno** seznamu.  
> * Pokud **blokováno** seznam obsahuje doménu, podstránku nebo webovou stránku, která jste zadali v souboru k odeslání, se odebere z **blokováno** seznamu a přidat do **aktivní** seznamu .
> * Duplicitní položky v nahrání souboru se bude ignorovat ve vlastní vyhledávání Bingu. 

### <a name="get-website-suggestions-for-your-search-experience"></a>Získat návrhy webu pro vaše prostředí hledání

Po přidání oblasti web slice k **aktivní** seznamu, vlastní vyhledávání Bingu portálu vygeneruje návrhy webu a podstránku v dolní části karty. Toto jsou datové řezy, které vlastní vyhledávání Bingu předpokládá, že budete chtít zahrnout. Klikněte na tlačítko **aktualizovat** získat návrhy aktualizované po aktualizaci nastavení vaší instance vlastního hledání. Tato část je viditelné pouze pokud jsou k dispozici návrhy.

## <a name="search-for-images-and-videos"></a>Vyhledávání obrázků a videa

Můžete vyhledat obrázky a videa podobně k webovému obsahu pomocí [API pro vyhledávání obrázků Bingu vlastní](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference) nebo [API pro vyhledávání videí Bingu vlastní](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-videos-api-v7-reference). Můžete zobrazit tyto výsledky s [hostované uživatelského rozhraní](hosted-ui.md), nebo rozhraní API. 

Tato rozhraní API se podobně jako jiné vlastní [Bingu pro vyhledávání obrázků](../Bing-Image-Search/overview.md) a [Bingu pro vyhledávání videí](../Bing-Video-Search/search-the-web.md) rozhraní API, ale hledat celého webu a nevyžadují, aby `customConfig` parametr dotazu. Najdete v těchto sad dokumentace pro další informace o práci s obrázky a videa. 

## <a name="test-your-search-instance-with-the-preview-pane"></a>Testovat vaše hledání instance s podokno náhledu

Vaše hledání instance můžete otestovat pomocí podokna ve verzi preview na pravé straně na portálu posílat vyhledávací dotazy a zobrazit výsledky. 

1. Pod vyhledávacím polem vyberte **Moje Instance**. Můžete porovnat výsledky z prostředí pro vyhledávání na Bingu, tak, že vyberete **Bingu**. 
2. Výběr filtru bezpečného hledání a které uvedení na trh pro vyhledávání (viz [parametry dotazu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters)).
3. Zadejte dotaz a stiskněte enter nebo klikněte na ikonu Hledat a zobrazit výsledky z aktuální konfiguraci. Můžete změnit typ hledání provedete kliknutím **webové**, **Image**, nebo **Video** zobrazíte odpovídající výsledky. 

<a name="adjustrank"></a>

## <a name="adjust-the-rank-of-specific-search-results"></a>Upravit pořadí výsledky hledání

Na portálu můžete upravit pořadí hledání obsahu z konkrétní domény, podstránky a webové stránky. Po odeslání vyhledávacího dotazu v podokně náhledu, každý výsledek hledání obsahuje seznam úpravy provedené pro něj:  

|            |                                                                                                                                                                      |
|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Zablokovat      | Přesune do seznamu blokovaných domény, podstránku nebo webové stránky. Bing vyloučí obsah z vybraného webu z zobrazování ve výsledcích hledání.                    |
| Zvýšení skóre      | Zvyšuje obsah z domény nebo podstránku vyšší ve výsledcích hledání.                                                                                        |
| Snížení úrovně     | Sníží úroveň obsah z domény nebo podstránku nižší ve výsledcích hledání. Vyberte, jestli se má obsah z domény degradovat nebo podstránky, že webová stránka patří. |
| Připnout nahoru | Přesune domény, podstránku nebo webové stránky **Pinned** seznamu. To přinutí webová stránka se zobrazí jako výsledek Nejvyhledávanější zadanému vyhledávacímu dotazu.                   |

Úprava pořadí není k dispozici pro obrázek nebo video hledání.

### <a name="boosting-and-demoting-search-results"></a>Zvýšení a snížení výsledky hledání

Můžete mimořádně zvýšit, zvýšit, nebo snížení úrovně do libovolné domény nebo podstránky v **aktivní** seznamu. Ve výchozím nastavení se přidají všechny řezy bez úprav hodnocení. Kolekce obsahuje nějaké řezy z webu, které jsou mimořádně boosted nebo Boosted jsou řazeny výše ve výsledcích hledání (s pořadím super boost vyšší než boost). Ve výsledcích hledání jsou hodnoceny nižší položky, které jsou interpretovány.

Můžete mimořádně zvýšit, zvýšení nebo snížení úrovně položek s použitím **hodnocení upravit** ovládacích prvků v **aktivní** seznamu, nebo tím, že pomocí zvýšení a snížení úrovně ovládací prvky v podokně náhledu. Služba přidá do seznamu Active řezu a upraví hodnocení odpovídajícím způsobem.

> [!NOTE] 
> Zvýšení a snížení úrovně domén a podstránky je jednou z mnoha metod, které vlastní vyhledávání Bingu se používá k určení pořadí výsledky hledání. Z důvodu jiné faktory ovlivňující hodnocení jiný webový obsah se mohou lišit důsledcích nastavení pořadí. Podokno náhledu testovat účinky nastavení řazení výsledků hledání. 

Mimořádně zvýšit, zvýšení a snížení úrovně nejsou k dispozici pro image a videa hledání.

## <a name="pin-slices-to-the-top-of-search-results"></a>Kolekce obsahuje nějaké řezy PIN kód do horní části výsledků vyhledávání

Na portálu také umožňuje Připnutí adresy URL do horní části výsledků vyhledávání pro konkrétní hledané výrazy pomocí **Pinned** kartu. Zadejte adresu URL a dotazů zadejte webovou stránku, která se zobrazí jako nejlepší výsledek. Všimněte si, že můžete připnout lze maximálně jedna webová stránka na vyhledávací dotaz a zobrazí se pouze indexovaná webové stránky v hledání. Připnutí výsledky není k dispozici pro obrázek nebo video hledání.

Webová stránka na začátek si můžete připnout dvěma způsoby:

* V **Pinned** kartu, zadejte adresu URL webové stránky pro Připnutí na horní a jeho odpovídající dotaz.

* V **ve verzi Preview** podokně, zadejte vyhledávací dotaz a klikněte na tlačítko Hledat. Webová stránka, kterou chcete připnout pro dotaz a klikněte na tlačítko Najít **PIN kód na začátek**. webové stránky a dotazů se přidají do **Pinned** seznamu.

### <a name="specify-the-pins-match-condition"></a>Zadejte podmínku shody PIN kód

Ve výchozím nastavení, se webové stránky pouze připnuté k hornímu okraji výsledky hledání při řetězce dotazu přesně odpovídal některé uvedené v **Pinned** seznamu. Toto chování můžete změnit zadáním jedné z následujících podmínek shody:

> [!NOTE]
> Všechna porovnání mezi uživatele vyhledávací dotaz a vyhledávání kódu pin jsou malá a velká písmena.

| Hodnota | Popis                                                                          |
|---------------|----------------------------------------------------------------------------------|
| Začíná na | Kód pin je nalezena shoda, pokud řetězec dotazu začíná řetězec dotazu PIN kódu |
| Končí na   | Kód pin je nalezena shoda, pokud řetězec dotazu končí řetězcem dotazu PIN kódu.  |
| Contains    | Kód pin je nalezena shoda, pokud řetězec dotazu obsahuje řetězec dotazu PIN kódu.   |


Podmínka shody PIN kód změnit, klikněte na ikonu úprav PIN kódu. V **podmínce shody dotazu** sloupce, klikněte na rozevírací seznam a vyberte nové podmínky použití. Potom klikněte na uložit ikonu a uložte změny.

### <a name="change-the-order-of-your-pinned-sites"></a>Změna pořadí připnutých webech

Chcete-li změnit pořadí váš PIN kódy, můžete na ně přetažení myší, nebo upravit pořadového čísla kliknutím na ikonu pro "úpravy" v **ovládací prvky** sloupec **Pinned** seznamu.

Pokud víc PIN kódů splňují podmínky shody, bude používat vlastní vyhledávání Bingu je nejvyšší v seznamu.

## <a name="view-statistics"></a>Statistiky sledovanosti

Pokud jste přihlášení k odběru vlastního vyhledávání na příslušné úrovni (najdete v článku [stránkách s cenami](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), **statistiky** kartu se přidá do produkčních instancí. Statistiky karta zobrazuje podrobnosti o vašich koncových bodů vlastního vyhledávání používání, včetně objemy volání, nejčastější dotazy, geografické distribuce, kódy odpovědí a bezpečného hledání. Můžete filtrovat podrobně popisuje použití zadaný ovládací prvky.

## <a name="usage-guidelines"></a>Pokyny k používání

- Pro každou instanci vlastního vyhledávání, maximální počet hodnocení úpravy, které může provádět **aktivní** a **blokováno** řezy je omezená na 400.
- Přidání řezu na aktivní nebo Uzavřeno karty se počítá jako jeden hodnocení úpravy.
- Zvýšení a snížení počtu jako dvě pořadí úpravy.
- Pro každou instanci vlastního vyhledávání je maximální počet kódů PIN, které může být omezená na 200.

## <a name="next-steps"></a>Další postup

- [Volání vlastního vyhledávání](./search-your-custom-view.md)
- [Konfigurace prostředí pro hostované uživatelské rozhraní](./hosted-ui.md)
- [Zvýraznění textu pomocí dekoračních značek](./hit-highlighting.md)
- [Stránkování webových stránek](./page-webpages.md)
