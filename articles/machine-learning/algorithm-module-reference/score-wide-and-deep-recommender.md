---
title: Použití & hloubkového modulu pro velký počet skóre
titleSuffix: Azure Machine Learning
description: Naučte se, jak používat & hloubkový modul pro velký počet, v Azure Machine Learning na předpovědi doporučení pro skóre pro datovou sadu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/12/2020
ms.openlocfilehash: 9a1a3892e6a47aabd9b5129ca551900494616bc8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90905163"
---
# <a name="score-wide-and-deep-recommender"></a>Určení skóre doporučovacího systému Wide and Deep

Tento článek popisuje, jak v Návrháři Azure Machine Learning použít modul pro **velký počet a hloubku pro hodnocení a** vytváření předpovědi založených na modelu doporučených doporučení na základě široké & hloubkového učení od společnosti Google.

V rámci širokého a hloubkového doporučení můžete generovat dva různé druhy předpovědi:

- [Předpověď hodnocení pro daného uživatele a položku](#predict-ratings)

- [Doporučit položky pro daného uživatele](#recommend-items)


Při vytváření druhého typu předpovědi můžete pracovat v režimu *produkčního* prostředí nebo v *režimu vyhodnocení*.

- **Provozní režim** bere v úvahu všechny uživatele nebo položky a obvykle se používá ve webové službě. Můžete vytvářet hodnocení pro nové uživatele, nikoli jenom uživatele, kteří se během školení viděli. 

- **Zkušební režim** pracuje na omezené sadě uživatelů nebo položek, které lze vyhodnotit, a obvykle se používá během experimentování.

Další podrobnosti o celém a hloubkovém doporučení a jeho základní teoretické oblasti najdete v příslušném výzkumném dokumentu:  [široké & hlubokou výuku pro doporučované systémy](https://arxiv.org/pdf/1606.07792.pdf).  

## <a name="how-to-configure-score-wide-and-deep-recommender"></a>Jak nakonfigurovat hodnocení v širokém a podrobném doporučeném

Tento modul podporuje různé typy doporučení, z nichž každá má různé požadavky. Klikněte na odkaz pro požadovaný typ dat a typ doporučení, které chcete vytvořit.

+ [Odhadnout hodnocení](#predict-ratings)
+ [Doporučit položky](#recommend-items)

### <a name="predict-ratings"></a>Odhadnout hodnocení

Při předpovědi hodnocení vyhodnotí tento model, jak bude daný uživatel reagovat na konkrétní položku s ohledem na data školení. Vstupní data pro bodování proto musí poskytnout uživateli i položku, která se má ohodnotit.

1. Přidejte do experimentu výukový model širokého doporučení pro & s hloubkou a připojte ho k **škole širokého a podrobného modelu doporučení**.  Model je nutné vytvořit pomocí [širokého a podrobného doporučení pro analýzu](train-wide-and-deep-recommender.md).

2. **Doporučený druh předpovědi**: vyberte **předpověď hodnocení**. Nejsou vyžadovány žádné další parametry.

3. Přidejte data, pro která chcete vytvořit předpovědi, a připojte je k **datové sadě ke skóre**.

    Aby bylo možné předpovědět hodnocení, musí vstupní datová sada obsahovat páry uživatelských položek.

    Datová sada může obsahovat volitelný třetí sloupec hodnocení pro pár uživatel-položka v prvním a druhém sloupci, ale třetí sloupec se během předpovědi ignoruje.

4.  (Volitelné). Pokud máte datovou sadu uživatelských funkcí, připojte ji k **uživatelským funkcím**.

    Datová sada uživatelských funkcí by měla obsahovat identifikátor uživatele v prvním sloupci. Zbývající sloupce by měly obsahovat hodnoty, které charakterizují uživatele, například jejich pohlaví, předvolby, umístění atd.
  
    Funkce uživatelů, kteří mají hodnocené položky v datové sadě školení, se ignorují v **rámci širokého a podrobného doporučení**, protože se už dozvěděly během školení. Proto vyfiltrujte datovou sadu předem, aby zahrnovala pouze *studené uživatele* nebo uživatele, kteří nehodnotili žádné položky.

    > [!WARNING]
    > Pokud byl model vyškolený bez použití uživatelských funkcí, nelze během bodování začlenit uživatelské funkce.

5. Máte-li datovou sadu funkcí položky, můžete ji připojit k **funkcím položek**.

    Datová sada funkcí položky musí v prvním sloupci obsahovat identifikátor položky. Zbývající sloupce by měly obsahovat hodnoty, které charakterizují položky.

    Funkce hodnocených položek v datové sadě školení se ignorují v **rámci široké škály a s hloubkovým doporučením** , protože už byly během školení zjištěny. Proto omezte datovou sadu bodování na *položky ze studeného* startu nebo na položky, které nebyly ohodnoceny žádnými uživateli.

    > [!WARNING]
    > Pokud byl model vyškolený bez použití funkcí položky, nemůžete během bodování zavádět funkce položek.

7. Spusťte experiment.

### <a name="results-for-rating-predictions"></a>Výsledky pro hodnocení předpovědi 

Výstupní datová sada obsahuje tři sloupce, které obsahují uživatele, položku a předpokládané hodnocení pro každého vstupního uživatele a položku.

Při bodování se navíc aplikují tyto změny:

-  Pro sloupec funkce číselného uživatele nebo položky se chybějící hodnoty automaticky nahradí **průměrem** nechybějících hodnot sady školení. U funkce kategorií chybí hodnoty nahrazené stejnou kategorií hodnotou než všechny možné hodnoty této funkce.
-  Pro hodnoty funkcí není použit žádný překlad, aby bylo možné zachovat jejich řídkosti.

### <a name="recommend-items"></a>Doporučit položky

Chcete-li doporučit položky pro uživatele, zadejte seznam uživatelů a položek jako vstup. Z těchto dat model používá své znalosti o stávajících položkách a uživatelích, aby vygeneroval seznam položek s pravděpodobným odvoláním pro jednotlivé uživatele. Můžete přizpůsobit počet vrácených doporučení a nastavit prahovou hodnotu pro počet předchozích doporučení, která jsou vyžadována pro vygenerování doporučení.

1. Přidejte do experimentu školený model širokého a podrobného doporučení a připojte ho k **škole širokého a podrobného modelu doporučení**.  Model je nutné vytvořit pomocí [širokého a podrobného doporučení pro analýzu](train-wide-and-deep-recommender.md).

2. Chcete-li doporučit položky pro určitý seznam uživatelů, nastavte **druh předpovědi** doporučení na **položku doporučení**.

3. **Doporučený výběr položky**: Určete, jestli používáte modul bodování v produkčním prostředí, nebo pro vyhodnocení modelu volbou jedné z těchto hodnot:

    - **Z hodnocených položek (pro vyhodnocení modelu)**: tuto možnost vyberte, pokud vyvíjíte nebo testujete model. Tato možnost povolí **režim vyhodnocování** a modul vytváří doporučení pouze z těch položek ve vstupní datové sadě, které byly ohodnoceny.
    - **Ze všech položek**: tuto možnost vyberte, Pokud nastavujete experiment pro použití v rámci webové služby nebo výroby.  Tato možnost umožňuje **režim produkčního** prostředí a modul dává doporučení ze všech položek, které se během školení zobrazily.
    - Z nezpracovaných **položek (pro návrh nových položek uživatelům)**: tuto možnost vyberte, pokud chcete, aby modul provedl doporučení jenom z těch položek v datové sadě školení, které nebyly ohodnocené. 
4. Přidejte datovou sadu, pro kterou chcete vytvořit předpovědi, a připojte ji k **datové sadě ke skóre**.

    - Zvolíte **-li možnost, musí** být vstupní datová sada tvořena jedním a pouze jedním sloupcem obsahujícím identifikátory uživatelů, pro které chcete doporučení vytvořit.

        Datová sada může obsahovat další dva sloupce identifikátorů a hodnocení položek, ale tyto dva sloupce jsou ignorovány. 

    - Pokud zvolíte možnost, **od hodnocených položek (pro vyhodnocení modelu)**, vstupní datová sada by měla obsahovat **páry uživatelských položek**. První sloupec by měl obsahovat identifikátor **uživatele** . Druhý sloupec by měl obsahovat odpovídající identifikátory **položek** .

        Datová sada může zahrnovat třetí sloupec hodnocení položky uživatele, ale tento sloupec je ignorován.
        
    - V případě **z nehodnocených položek (pro návrh nových položek uživatelům)** by měla vstupní datová sada obsahovat páry uživatelských položek. První sloupec by měl obsahovat identifikátor uživatele. Druhý sloupec by měl obsahovat odpovídající identifikátory položek.

        Datová sada může zahrnovat třetí sloupec hodnocení položky uživatele, ale tento sloupec je ignorován.

5. (Volitelné). Pokud máte datovou sadu **uživatelských funkcí**, připojte ji k **uživatelským funkcím**.

    První sloupec v datové sadě uživatelských funkcí by měl obsahovat identifikátor uživatele. Zbývající sloupce by měly obsahovat hodnoty, které charakterizují uživatele, například jejich pohlaví, předvolby, umístění atd.

    Funkce uživatelů, kteří mají hodnocené položky, se ignorují v **rámci široké škály a podle hloubkového doporučení**, protože tyto funkce již byly během školení zjištěny. Proto můžete datovou sadu předem vyfiltrovat, aby zahrnovala pouze *studené uživatele* nebo uživatele, kteří nehodnotili žádné položky.

    > [!WARNING]
    >  Pokud byl model vyškolený bez použití uživatelských funkcí, nelze při vyhodnocování použít funkci použít.

6. Volitelné Máte-li datovou sadu **funkcí položky**, můžete ji připojit k **funkcím položek**.

    První sloupec v datové sadě funkcí položky musí obsahovat identifikátor položky. Zbývající sloupce by měly obsahovat hodnoty, které charakterizují položky.

    Funkce hodnocených položek se ignorují v **rámci širokého a podrobného doporučení**, protože tyto funkce se už během školení dozvěděly. Proto můžete omezit datovou sadu bodování na *položky pro studený start* nebo položky, které nebyly ohodnoceny žádnými uživateli.

    > [!WARNING]
    >  Pokud byl model vyškolený bez použití funkcí položky, při bodování nepoužívejte funkce položek.  

7. **Maximální počet položek, které je potřeba doporučit pro uživatele**: zadejte počet položek, které se mají vrátit pro každého uživatele. Ve výchozím nastavení se doporučuje 5 položek.

8. **Minimální velikost fondu doporučení na uživatele**: zadejte hodnotu, která určuje, kolik předchozích doporučení je potřeba.  Ve výchozím nastavení je tento parametr nastaven na hodnotu 2, což znamená, že položka musí být doporučena alespoň dvěma dalšími uživateli.

    Tato možnost by se měla používat jenom v případě, že vyhodnocujete režim vyhodnocování. Možnost není k dispozici, pokud vyberete možnost **ze všech položek** nebo **z nehodnocených položek (pro návrh nových položek uživatelům)**.

9. V případě z nezpracovaných **položek (pro návrh nových položek uživatelům)** použijte třetí vstupní port s názvem **školicí data** pro odebrání položek, které již byly ohodnoceny z výsledků předpovědi.

    Chcete-li použít tento filtr, připojte původní datovou sadu školení ke vstupnímu portu.

10. Spusťte experiment.
### <a name="results-of-item-recommendation"></a>Výsledky doporučení položky

Vyhodnocená datová sada vrácená funkcí pro **velké množství a hloubkový doporučuje** seznam doporučených položek pro každého uživatele.

- První sloupec obsahuje identifikátory uživatelů.
- Vygeneruje se počet dalších sloupců v závislosti na hodnotě nastavené pro **maximální počet položek, které se doporučují pro uživatele**. Každý sloupec obsahuje doporučenou položku (podle identifikátoru). Doporučení jsou seřazená podle spřažení položek a uživatele, přičemž položka s největším spřažením je vložena do sloupce, **Položka 1**.

##  <a name="technical-notes"></a>Technické poznámky

V této části najdete odpovědi na některé běžné dotazy týkající se používání širokého &ho vhodného předpovědiu pro vytvoření.  

###  <a name="cold-start-users-and-recommendations"></a>Uživatelé a doporučení pro studený start

Pro vytváření doporučení se obvykle vyžaduje, aby modul **hodnocení širokého a hloubkového** modulu měl stejné vstupy, které jste použili při výuce modelu, včetně ID uživatele. Důvodem je to, že algoritmus potřebuje zjistit, jestli se během školení dozvěděl o tomto uživateli něco. 

Pro nové uživatele ale možná nemáte ID uživatele, jenom některé uživatelské funkce, jako je třeba stáří, pohlaví a tak dále.

Můžete přesto vytvořit doporučení pro uživatele, kteří jsou ve vašem systému noviní, a to tak, že je zařadíte jako *Uživatelé z chladírenského startu*. U takových uživatelů algoritmus doporučení nepoužívá minulou historii ani předchozí hodnocení, jenom uživatelské funkce.

Pro účely předpovědi je uživatel se studeným startem definovaný jako uživatel s ID, který se pro školení nepoužil. Chcete-li zajistit, že ID se neshodují s ID používanými při výuce, můžete vytvořit nové identifikátory. Můžete například vygenerovat náhodná ID v zadaném rozsahu nebo přidělit sérii identifikátorů předem pro uživatele studeného startu.

Pokud ale nemáte žádná data pro filtrování spolupráce, jako je například vektor uživatelských funkcí, je lepší používat informace o klasifikaci nebo regresi.

###  <a name="production-use-of-the-wide-and-deep-recommender"></a>Produkční použití širokého a podrobného doporučení

Pokud jste se dozkoušeli na široké a hloubkové doporučení a pak model přesunete do produkčního prostředí, pamatujte na tyto klíčové rozdíly při použití doporučení v režimu vyhodnocování a v provozním režimu:

- Vyhodnocení, podle definice, vyžaduje předpovědi, které lze ověřit proti *zemi* v sadě testů. Proto při vyhodnocení doporučení, musí předpovědět pouze položky, které byly ohodnoceny v sadě testů. To nutně omezuje možné hodnoty, které jsou předpovězeny.

    Když však model zprovozněníte, obvykle změníte režim předpovědi tak, aby vytvářely doporučení na základě všech možných položek, aby bylo možné získat nejlepší předpovědi. Pro mnohé z těchto předpovědi neexistuje žádná odpovídající země, takže přesnost doporučení nelze ověřit stejným způsobem jako při experimentování.

- Pokud neposkytnete ID uživatele v produkčním prostředí a zadáte jenom vektor funkce, můžete jako odpověď získat seznam všech doporučení pro všechny možné uživatele. Nezapomeňte zadat ID uživatele.

    Chcete-li omezit počet vrácených doporučení, můžete také určit maximální počet vrácených položek na uživatele. 



## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) Azure Machine Learning. 
