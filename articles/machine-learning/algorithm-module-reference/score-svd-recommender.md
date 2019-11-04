---
title: 'Doporučení pro skóre SVD: odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Naučte se, jak používat modul SVD pro hodnocení skóre ve službě Azure Machine Learning pro hodnocení doporučení předpovědi pro datovou sadu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 25de69873857512a70d6417973d4a85883ac6455
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517914"
---
# <a name="score-svd-recommender"></a>Doporučení pro skóre SVD

Tento článek popisuje, jak v Návrháři Azure Machine Learning použít modul **doporučení skóre SVD** (Preview). Tento modul použijte k vytvoření předpovědi pomocí výukového modelu s doporučenými doporučeními, který je založený na algoritmu SVD (Single Value rekompozice).

Doporučení SVD může generovat dva různé druhy předpovědi:

- [Předpověď hodnocení pro daného uživatele a položku](#predict-ratings)

- [Doporučit položky pro daného uživatele](#recommend)

Při vytváření druhého typu předpovědi můžete pracovat v režimu *produkčního* prostředí nebo v *režimu vyhodnocení*.

- **Provozní režim** bere v úvahu všechny uživatele nebo položky a obvykle se používá ve webové službě.

    Můžete vytvářet hodnocení pro nové uživatele, nikoli jenom uživatele, kteří se během školení viděli. Další informace najdete v [této části](#technical-notes). 

- **Zkušební režim** pracuje na omezené sadě uživatelů nebo položek, které je možné vyhodnotit, a obvykle se používá během kanálu.

Další informace o algoritmu doporučeného pro SVD najdete v tématu věnovaném [technikům vytváření matrice pro doporučované systémy](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).

                                                                                                                                    


## <a name="how-to-configure-score-svd-recommender"></a>Jak nakonfigurovat doporučení SVD skóre

Tento modul podporuje různé typy doporučení, z nichž každá má různé požadavky. Klikněte na odkaz pro požadovaný typ dat a typ doporučení, které chcete vytvořit.

+ [Odhadnout hodnocení](#predict-ratings)
+ [Doporučit položky](#recommend)

###  <a name="predict-ratings"></a>Odhadnout hodnocení

Při předpovědi hodnocení vyhodnotí tento model, jak bude daný uživatel reagovat na konkrétní položku s ohledem na data školení. Vstupní data pro bodování proto musí poskytnout uživateli i položku, která se má ohodnotit.

1. Přidejte do svého kanálu školený model doporučení a připojte ho k **škole SVD doporučení**.  Model je potřeba vytvořit pomocí nástroje pro [školení SVD](train-SVD-recommender.md).

2. **Doporučený druh předpovědi**: vyberte **předpověď hodnocení**. Nejsou vyžadovány žádné další parametry.

3. Přidejte data, pro která chcete vytvořit předpovědi, a připojte je k **datové sadě ke skóre**.

    Aby bylo možné předpovědět hodnocení, musí vstupní datová sada obsahovat páry uživatelských položek.

    Datová sada může obsahovat volitelný třetí sloupec hodnocení pro pár uživatel-položka v prvním a druhém sloupci, ale třetí sloupec se během předpovědi ignoruje.

4. Spuštění kanálu

### <a name="results-for-rating-predictions"></a>Výsledky pro hodnocení předpovědi 

Výstupní datová sada obsahuje tři sloupce, které obsahují uživatele, položku a předpokládané hodnocení pro každého vstupního uživatele a položku.

###  <a name="recommend"></a>Abyste 

Chcete-li doporučit položky pro uživatele, zadejte seznam uživatelů a položek jako vstup. Z těchto dat model používá své znalosti o stávajících položkách a uživatelích, aby vygeneroval seznam položek s pravděpodobným odvoláním pro jednotlivé uživatele. Můžete přizpůsobit počet vrácených doporučení a nastavit prahovou hodnotu pro počet předchozích doporučení, která jsou vyžadována pro vygenerování doporučení.

1. Přidejte do svého kanálu školený model doporučení a připojte ho k **škole SVD doporučení**.  Model je potřeba vytvořit pomocí nástroje pro [školení SVD](train-svd-recommender.md).

2. Chcete-li doporučit položky pro určitý seznam uživatelů, nastavte **druh předpovědi** doporučení na **položku doporučení**.

3. **Doporučený výběr položky**: Určete, jestli používáte modul bodování v produkčním prostředí, nebo pro vyhodnocení modelu volbou jedné z těchto hodnot:

    - **Ze všech položek**: tuto možnost vyberte, Pokud nastavujete kanál pro použití v rámci webové služby nebo výroby.  Tato možnost umožňuje **režim produkčního**prostředí a modul dává doporučení ze všech položek, které se během školení zobrazily.

    - **Z hodnocených položek (pro vyhodnocení modelu)** : tuto možnost vyberte, pokud vyvíjíte nebo testujete model. Tato možnost povolí **režim vyhodnocování**a modul vytváří doporučení pouze z těch položek ve vstupní datové sadě, které byly ohodnoceny.
    
    - Z nezpracovaných **položek (pro návrh nových položek uživatelům)** : tuto možnost vyberte a modul vydává doporučení jenom z těch položek v datové sadě školení, které nebyly ohodnocené. 

4. Přidejte datovou sadu, pro kterou chcete vytvořit předpovědi, a připojte ji k **datové sadě ke skóre**.

    - Pro **ze všech položek**by se vstupní datová sada měla skládat z jednoho sloupce, který obsahuje identifikátory uživatelů, pro které chcete doporučení vytvořit.

        Datová sada může obsahovat další dva sloupce identifikátorů a hodnocení položek, ale tyto dva sloupce jsou ignorovány. 

    - V případě **z hodnocených položek (pro vyhodnocení modelu)** by měla vstupní datová sada obsahovat **páry uživatelských položek**. První sloupec by měl obsahovat identifikátor **uživatele** . Druhý sloupec by měl obsahovat odpovídající identifikátory **položek** .

        Datová sada může zahrnovat třetí sloupec hodnocení položky uživatele, ale tento sloupec je ignorován.

    - V případě **z nehodnocených položek (pro návrh nových položek uživatelům)** by měla vstupní datová sada obsahovat **páry uživatelských položek**. První sloupec by měl obsahovat identifikátor **uživatele** . Druhý sloupec by měl obsahovat odpovídající identifikátory **položek** .

        Datová sada může zahrnovat třetí sloupec hodnocení položky uživatele, ale tento sloupec je ignorován.

5. **Maximální počet položek, které je potřeba doporučit pro uživatele**: zadejte počet položek, které se mají vrátit pro každého uživatele. Ve výchozím nastavení se doporučuje pět položek.

6. **Minimální velikost fondu doporučení na uživatele**: zadejte hodnotu, která určuje, kolik předchozích doporučení je potřeba.  Ve výchozím nastavení je tento parametr nastaven na hodnotu 2, což znamená, že položka musí být doporučena alespoň dvěma dalšími uživateli.

    Tato možnost by se měla používat jenom v případě, že vyhodnocujete režim vyhodnocování. Možnost není k dispozici, pokud vyberete možnost **ze všech položek** nebo **z nehodnocených položek (pro návrh nových položek uživatelům)** .

7.  V případě z nezpracovaných **položek (pro návrh nových položek uživatelům)** použijte třetí vstupní port s názvem **školicí data**pro odebrání položek, které již byly ohodnoceny z výsledků předpovědi.

    Chcete-li použít tento filtr, připojte původní datovou sadu školení ke vstupnímu portu.

8. Spuštění kanálu

### <a name="results-of-item-recommendation"></a>Výsledky doporučení položky

Vyhodnocená datová sada vrácená funkcí **SKORE SVD doporučuje doporučit** seznam doporučených položek pro každého uživatele.

- První sloupec obsahuje identifikátory uživatelů.
- Vygeneruje se počet dalších sloupců v závislosti na hodnotě nastavené pro **maximální počet položek, které se doporučují pro uživatele**. Každý sloupec obsahuje doporučenou položku (podle identifikátoru). Doporučení jsou seřazená podle spřažení položek a uživatele, přičemž položka s největším spřažením je vložena do sloupce, **Položka 1**.

> [!WARNING]
> Tuto datovou sadu s hodnocením nejde vyhodnotit pomocí modulu [vyhodnocení doporučení](evaluate-recommender.md) .


##  <a name="technical-notes"></a>Technické poznámky

V této části najdete odpovědi na některé běžné dotazy týkající se použití doporučení k vytvoření předpovědi.  

###  <a name="production-use-of-the-svd-recommender"></a>Provozní použití doporučení SVD

Pokud máte kanál s doporučeným nástrojem SVD a přesunete model do produkčního prostředí, pamatujte na tyto klíčové rozdíly při použití doporučení v režimu vyhodnocování a v provozním režimu:

- Vyhodnocení, podle definice, vyžaduje předpovědi, které lze ověřit proti *zemi* v sadě testů. Proto při vyhodnocení doporučení, musí předpovědět pouze položky, které byly ohodnoceny v sadě testů. To nutně omezuje možné hodnoty, které jsou předpovězeny.

    Když však model zprovozněníte, obvykle změníte režim předpovědi tak, aby vytvářely doporučení na základě všech možných položek, aby bylo možné získat nejlepší předpovědi. Pro mnohé z těchto předpovědi neexistuje žádná odpovídající země, takže přesnost doporučení nelze ověřit stejným způsobem jako při kanálu.


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning služby. 
