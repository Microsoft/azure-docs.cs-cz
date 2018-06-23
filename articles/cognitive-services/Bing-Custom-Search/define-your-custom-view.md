---
title: 'Hledání vlastní Bing: Definovat vlastní zobrazení | Microsoft Docs'
description: Popisuje postup vytvoření lokality a služby svislé vyhledávání
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 8ffe3087df398d6310828e41d0c6992199fafbed
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342872"
---
# <a name="configure-your-custom-search-experience"></a>Konfigurovat možnosti vlastní vyhledávání
Vlastní hledání instance umožňuje přizpůsobit možnosti vyhledávání zahrnout obsah jenom z webů, které vaši uživatelé zajímají. Místo prohledávání celé webové služby Bing vyhledá pouze řezy webu, které vás zajímají.
Pokud chcete vytvořit vlastní zobrazení webu, použijte funkci vyhledávání vlastní Bing [portál](https://customsearch.ai). Informace o přihlášení k portálu najdete v tématu [vytvoření vaší první instance Bing vlastní vyhledávání](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/quick-start). Na portálu umožňuje vytvářet hledání instance, které určuje domén, podstránky a webové stránky, které chcete Bingu pro vyhledávání a ty, které nechcete, aby ji k prohledávání. Kromě určení adresy URL obsahu, který víte o, můžete také požádat portálu navrhovat obsah, který chcete přidat do zobrazení. Toto jsou způsoby, můžete definovat řez webu: 

1.  Domény. Řez domény zahrnuje veškerý obsah v rámci internetové domény. Například www.microsoft.com. Vynechání "www" způsobí, že Bing taky hledání subdomény domény. Například pokud zadáte microsoft.com, Bing taky vrátí výsledky z webu support.microsoft.com nebo technet.microsoft.com.
2.  Podstránku. Řez podstránku zahrnuje veškerý obsah v podstránku a cesty pod ním. V cestě můžete určit maximálně dvě podstránky. Například www.microsoft.com/en-us/windows/ 
3.  Webová stránka. Řez webová stránka může obsahovat pouze webová stránka ve vlastní vyhledávání. Můžete volitelně zadat, jestli se mají zahrnout podstránky.

Všechny domény, podstránky a webové stránky, které zadáte, musí být veřejné a indexované podle Bing. Pokud jste vlastníkem veřejný web, který chcete zahrnout do vyhledávání a Bing není indexované ho, najdete v části Bing [správce webového serveru dokumentace](https://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a) podrobnosti o získání Bing indexování ho. Také naleznete v dokumentaci správce webového serveru podrobnosti o získání Bing aktualizovat prohledávaný web, pokud index je zastaralý.

## <a name="adding-slices-to-your-custom-search"></a>Přidávání řezy do vlastní hledání
Když definujete vlastní vyhledávání instanci, zadejte aktivní i Blokované domény, podstránky a webové stránky, které chcete vyhledat nebo není vyhledávání.  

- Aktivní: Seznam domén, podstránky nebo webové stránky, které chcete zahrnout do hledání. 
- Blokované: Seznam domény, podstránky nebo webové stránky, které chcete vyloučit z hledání. Položky, které jste bloku musí být obsahu v doménách a podstránky uvedena v seznamu aktivní.

Přístup každý seznamu, klikněte na kartě aktivní a blokováno v instanci vlastní vyhledávání. 

<a name="active-and-blocked-lists"></a>
## <a name="active-and-blocked-lists"></a>Aktivní a seznamy blokováno 
Chcete-li zadat řez webu, které chcete Bingu pro vyhledávání, klikněte na tlačítko **Active** kartě a seznam domén, podstránky a webové stránky pro vyhledávání. Můžete přidat řez přímo do seznamu nebo přidat více než jeden řez tím, že nahrajete textový soubor pomocí ikony nahrávání.

Podrobnosti o nahrání souboru: 

- Nahrávání souborů je k dispozici pouze pro přidávání řezy do seznamu Active, nelze ho použít k přidání řezy do seznamu blokovaných. 
- Vytvoření textového souboru a zadejte jednu doménu, podstránku nebo webové stránky na každý řádek. Celý nahrávání byl odmítnut, pokud dojde k chybě. 
- Pokud v seznamu blokovaných obsahuje doménu, podstránku nebo webovou stránku, který jste zadali v souboru k odeslání, služba odstraní ji ze seznamu blokovaných a přidá do seznamu aktivní. 
- Služba ignoruje duplicitní položky v souboru k odeslání.

Chcete-li upravit nebo odstranit řezů, použijte možnosti ve sloupci ovládací prvky. 

Podobně můžete přidat řezy do seznamu blokovaných (s výjimkou soubor nahrávání nelze použít k určení řezy).

## <a name="pinned-list"></a>Definovaného seznamu
Na portálu umožňuje taky připnout konkrétní webové stránky do horní části výsledek hledání, pokud uživatel zadá konkrétní hledaný termín. **Pinned** karta obsahuje seznam termínů a webová stránka párů dotazu, které zadat webovou stránku, který se zobrazí v horní důsledku pro konkrétní dotaz. Termín dotazu uživatele musí přesně shodovat termín definovaného dotazu.
Informace o Připnutí výsledků najdete v tématu [upravit pořadí](#adjustrank).

Připnutí výsledků není k dispozici pro možnosti vyhledávání bitové kopie.

## <a name="site-suggestions"></a>Návrhy lokality
Po přidání do seznamu Active řezů, generuje tato služba lokality a podstránku návrhy, které chcete přidat do hledání. **Můžete chtít přidat** část obsahuje návrhy. Stránka nastavení instance zahrnuje v této části pouze v případě, že doporučení jsou k dispozici. 

Chcete-li přidat do seznamu Active návrhy, klikněte na tlačítko + ikonu.  Protože služba generuje návrhy na základě svého nastavení, nezapomeňte klikněte na tlačítko **aktualizovat** po přidání návrhy. 

## <a name="preview-pane"></a>Podokno pro náhled
Na vaše hledání instance v podokně náhledu na pravé straně odesíláte vyhledávací dotazy a zobrazení výsledků můžete otestovat. Vyberte **Instance Moje**, vyberte filtr bezpečné vyhledávání a co na trhu pro vyhledávání (viz [parametry dotazu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters). Zadejte dotaz a stiskněte enter nebo klikněte na ikonu hledání, pokud chcete zobrazit výsledky z aktuální konfigurace. Zobrazíte výsledky web, klikněte na tlačítko **webové**, chcete-li zobrazit výsledky bitovou kopii, klikněte na tlačítko **Image**. 

 V podokně náhledu, můžete také zkontrolovat Bing výsledky výběrem **Bing** místo **Instance Moje**. To může být užitečné k porovnání výsledků z možnosti vyhledávání do výsledků vrácených Bing.

<a name="adjustrank"></a>
## <a name="adjust-rank"></a>Upravit pořadí
Na portálu umožňuje nastavit řazení k manipulaci s výsledky, které vrátí Bing. V podokně náhledu zadejte hledaný termín a spusťte dotaz. V podokně náhledu seznam výsledků hledání pro dotaz. Napravo od každý výsledek je seznam úpravy, můžete provést. 

- Blok. Přesune do seznamu blokovaných domény, podstránku nebo webové stránky. Vyberete úroveň blokování. Bing vyloučí obsah z vybraného webu ve výsledcích hledání. 
- Zvýšení. Zvyšuje obsah z domény nebo podstránku vyšší ve výsledcích hledání. Jestli se má zvýšit obsah z domény nebo podstránky, že webová stránka patří do vyberete.
- Snížení úrovně. Sníží úroveň obsahu z domény nebo podstránku nižší ve výsledcích hledání. Můžete vybrat, jestli se pro snížení úrovně obsah z domény nebo podstránky, že webová stránka patří do. 
- PIN kód nahoru. Zadejte webovou stránku, která se zobrazí v horní části výsledky, pokud se dotaz uživatele, že termín přesně odpovídá termín dotazu, můžete použít. Seznam aktivních nemusí obsahovat webové stránky můžete připnout. 

Úprava pořadí není k dispozici pro možnosti vyhledávání bitové kopie.

## <a name="boosting-and-demoting"></a>Zvyšovat skóre a snížení úrovně
Můžete super zvýšit, zvýšit, nebo snížení úrovně do libovolné domény nebo podstránky ve vašem Active seznamu. Ve výchozím nastavení jsou všechny řezy přidán se stejnou váhou. Položky, které jsou naprosto boosted nebo Boosted jsou řazeny výše ve výsledcích hledání (s pořadím super nárůst vyšší než nárůst). Položky, které jsou k degradaci, jsou seřazeny nižší ve výsledcích hledání.

Je důležité si uvědomit, který super zvýšit, zvýšení a snížení úrovně udělení příslušných váhy variant domén nebo podstránky. Toto je pouze jedna z mnoha signály, které používají ranker k určení pořadí výsledky. To znamená, že jejich platnost pro konkrétní dotaz není zaručena jako mnoho dalších faktorů, můžou ovlivnit celkovou řazení výsledků webové.  Pro ověření účinnosti zásad možné tímto zvýšení nebo snížení úrovně nemá na ranker, test možnosti vyhledávání v podokně náhledu.

Můžete super zvýšit, zvýšit, nebo snížit úroveň položky, pomocí klasifikace upravit ovládacích prvků v aktivním seznamu nebo pomocí nárůst a snížení úrovně ovládací prvky v podokně náhledu. Služba řez přidá do seznamu aktivní a upraví pořadí odpovídajícím způsobem.

Super zvýšit, zvýšení a snížení úrovně automaticky uloží změny a odráží okamžitě vaše vlastní vyhledávání koncový bod. 

Super zvýšit, zvýšení a snížení úrovně nejsou k dispozici pro možnosti vyhledávání bitové kopie.

## <a name="pin-to-top"></a>Připnout na začátek
Připnutí webové stránky do horní části výsledky hledání specifického dotazu, vyberte jednu z následujících možností:

1.  Na kartě Pinned zadejte adresu URL webové stránky chcete připnout na začátek výsledky a přesný dotaz, který se aktivuje připojení. 
2.  V podokně náhledu zadejte termín dotazu a klikněte na tlačítko Hledat. V dalším kroku Identifikujte webové stránky v výsledky, které chcete připnout na začátek výsledky, pokud uživatel zadá stejný dotaz. Potom klikněte na připnout na nejvyšší. Služba přidá do seznamu Pinned webové stránky a dotazu. 

Můžete sledovat na kartě Pinned váš PIN. Zadané kódy PIN jsou uvedeny jako\<dotazu\>, \<webová stránka\>' páry. 

Webová stránka je připnutá pouze v případě, že se dotaz uživatele přesně odpovídá dotazu. 

Pro konkrétní dotaz budete moct připnout maximálně jednu webovou stránku do horní části výsledky.

Kódy PIN nejsou k dispozici pro možnosti vyhledávání bitové kopie.

## <a name="use-bing-to-specify-slices"></a>Použijte k určení řezy Bing
Existuje několik způsobů určení řezy webu, které tvoří vlastní hledání. Pokud znáte řezy, které chcete zahrnout do vaší instanci, přidejte do seznamu Active vaší instance. Informace o přidání položky do seznamu Active sami, najdete v části [aktivní a blokováno seznamy](#active-and-blocked-lists).
Ale pokud si nejste jistí, řezy, které chcete zahrnout, můžete spouštět dotazy Bing v podokně náhledu a zjistit, co vrátí Bing. Pak můžete vybrat datové řezy, které chcete zahrnout do vlastní hledání. Pravděpodobně budete muset spustit několika vyhledávacích dotazů a ujistěte se, že identifikujete všechny datové řezy, které chcete použít pro vaše instance. 

Použijte následující postup slouží k přidání řezy k vaší vlastní vyhledávání instanci Bing. 
1.  Přihlaste se k hledání vlastní Bing [portál](https://customsearch.ai).
2.  Vytvoření instance nebo vyberte instanci aktualizovat.
3.  V podokně náhledu na pravé straně vyberte z rozevíracího seznamu Bing.
4.  Do vyhledávacího pole zadejte termín dotazu, které jsou důležité pro vaše instance.
5.  Klikněte na tlačítko **přidat lokality** vedle výsledků, které chcete zahrnout.
6.  Klikněte na tlačítko **OK**.

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

## <a name="view-statistics"></a>Zobrazení statistiky
Pokud odebíráte vlastní vyhledávání na příslušné úrovni (najdete v článku [ceny stránky](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), **statistiky** karta je přidané do instancí vaše produkční. Na kartě statistiky obsahuje podrobnosti o tom, jak se používají vyhledávání vlastní koncové body, včetně volání na technickou podporu, nejčastějších dotazů, zeměpisném rozložení, kódy odpovědí a bezpečné vyhledávání. Podrobnosti o použití zadané ovládacích prvků můžete filtrovat.

## <a name="understanding-quota"></a>Principy kvóty
- Pro každou instanci vlastní vyhledávání, maximální počet hodnocení úpravy, které může provést na **Active** a **blokováno** řezy je omezený na 400.
- Přidání řez do karty aktivní nebo blokované počítá jako jedna hodnocení úpravu.
- Zvyšovat skóre a snížení počtu jako dvě úpravy hodnocení.
- Pro každou instanci vlastní vyhledávání je maximální počet kódů PIN, které může být omezen na 200.

## <a name="next-steps"></a>Další postup

- [Volání vlastní hledání](./search-your-custom-view.md)
- [Konfigurace prostředí hostované uživatelského rozhraní](./hosted-ui.md)
- [Použití decoration značky zvýraznění textu](./hit-highlighting.md)
- [Stránka webové stránky](./page-webpages.md)