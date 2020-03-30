---
title: 'Skóre SVD Doporučující: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak použít modul Doporučení skóre SVD v Azure Machine Learning k skóre předpovědi doporučení pro datovou sadu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/10/2020
ms.openlocfilehash: 82c3454ad4c8db3a9b19084f5b6ece988cc86b9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455974"
---
# <a name="score-svd-recommender"></a>Určení skóre doporučovacího systému SVD

Tento článek popisuje, jak používat modul Doporučení skóre SVD v návrháři Azure Machine Learning (preview). Tento modul slouží k vytvoření předpovědi pomocí trénovaného modelu doporučení na základě algoritmu rozkladu jedné hodnoty (SVD).

SVD doporučující může generovat dva různé druhy předpovědi:

- [Předvídání hodnocení pro daného uživatele a položku](#prediction-of-ratings)
- [Doporučit položky uživateli](#recommendations-for-users)

Při vytváření druhého typu předpovědí můžete pracovat v jednom z těchto režimů:

- **Produkční režim** bere v úvahu všechny uživatele nebo položky. Obvykle se používá ve webové službě.

  Můžete vytvořit skóre pro nové uživatele, nejen pro uživatele, kteří se viděli během školení. Další informace naleznete v [technických poznámkách](#technical-notes). 

- **Režim hodnocení** pracuje se sníženou sadou uživatelů nebo položek, které lze vyhodnotit. Obvykle se používá během operací kanálu.

Další informace o algoritmu svd doporučující, viz výzkum papír [Matice faktorizace techniky pro doporučující systémy](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).

## <a name="how-to-configure-score-svd-recommender"></a>Jak nakonfigurovat skóre SVD Recommender

Tento modul podporuje dva typy předpovědí, každý s různými požadavky. 

###  <a name="prediction-of-ratings"></a>Predikce hodnocení

Když předpovídáte hodnocení, model vypočítá, jak bude uživatel reagovat na konkrétní položku vzhledem k trénovacím datům. Vstupní data pro vyhodnocování musí poskytnout uživatele i položku, kterou chcete hodnotit.

1. Přidejte do kanálu trénovaný model doporučení a připojte jej k **trénovaného doporučujícího zařízení SVD**. Model je nutné vytvořit pomocí modulu [Train SVD Recommender.](train-SVD-recommender.md)

2. U **druhu předpověď doporučení**vyberte možnost **Predikce hodnocení**. Nejsou vyžadovány žádné další parametry.

3. Přidejte data, pro která chcete provést předpovědi, a připojte je k **datové sadě, abyste získali skóre**.

   Aby model předpověděl hodnocení, musí vstupní datová sada obsahovat dvojice uživatelských položek.

   Datová sada může obsahovat volitelný třetí sloupec hodnocení pro dvojici uživatelských položek v prvním a druhém sloupci. Ale třetí sloupec bude ignorován během předpovědi.

4. Odešlete potrubí.

### <a name="results-for-rating-predictions"></a>Výsledky pro předpovědi hodnocení 

Výstupní datová sada obsahuje tři sloupce: uživatele, položky a předpokládané hodnocení pro každého vstupního uživatele a položku.

###  <a name="recommendations-for-users"></a>Doporučení pro uživatele 

Chcete-li doporučit položky pro uživatele, zadejte seznam uživatelů a položek jako vstup. Z těchto dat model využívá své znalosti o existujících položkách a uživatelích ke generování seznamu položek s pravděpodobným odvoláním pro každého uživatele. Můžete přizpůsobit počet vrácených doporučení. A můžete nastavit prahovou hodnotu pro počet předchozích doporučení, které jsou nutné ke generování doporučení.

1. Přidejte do kanálu trénovaný model doporučení a připojte jej k **trénovaného doporučujícího zařízení SVD**.  Model je nutné vytvořit pomocí modulu [Train SVD Recommender.](train-svd-recommender.md)

2. Chcete-li doporučit položky pro seznam uživatelů, nastavte **typ předpovědi doporučení doporučujícího uživatele** na **položku Doporučení**.

3. U **doporučeného výběru položek**označte, zda používáte modul hodnocení ve výrobě nebo pro vyhodnocení modelu. Zvolte jednu z těchto hodnot:

    - **Ze všech položek**: Tuto možnost vyberte, pokud nastavujete kanál pro použití ve webové službě nebo v produkčním prostředí.  Tato možnost povolí *produkční režim*. Modul poskytuje doporučení ze všech položek, které byly pozorovány během tréninku.

    - **Z hodnocené položky (pro vyhodnocení modelu)**: Tuto možnost vyberte, pokud vyvíjíte nebo testujete model. Tato možnost umožňuje *režim vyhodnocení*. Modul poskytuje doporučení pouze z těch položek ve vstupní datové sadě, které byly hodnoceny.
    
    - **Z nehodnocených položek (navrhnout nové položky uživatelům)**: Tuto možnost vyberte, pokud chcete, aby modul doporučení pouze z těch položek v datové sadě školení, které nebyly hodnoceny. 

4. Přidejte datovou sadu, pro kterou chcete provést předpovědi, a připojte ji k **datové sadě, abyste získali skóre**.

    - Pro **ze všech položek**by se vstupní datová sada měla skládat z jednoho sloupce. Obsahuje identifikátory uživatelů, pro které chcete doporučení.

      Datová sada může obsahovat další dva sloupce identifikátorů a hodnocení položek, ale tyto dva sloupce jsou ignorovány. 

    - Pro **z hodnocené položky (pro vyhodnocení modelu)**, vstupní datová sada by měla obsahovat dvojice uživatelských položek. První sloupec by měl obsahovat identifikátor uživatele. Druhý sloupec by měl obsahovat odpovídající identifikátory položek.

      Datová sada může obsahovat třetí sloupec hodnocení uživatelských položek, ale tento sloupec je ignorován.

    - Pro **From Unrated Items (chcete-li navrhnout nové položky uživatelům)**, vstupní datová sada by měla obsahovat dvojice uživatelských položek. První sloupec by měl obsahovat identifikátor uživatele. Druhý sloupec by měl obsahovat odpovídající identifikátory položek.

     Datová sada může obsahovat třetí sloupec hodnocení uživatelských položek, ale tento sloupec je ignorován.

5. **Maximální počet položek, které chcete doporučit uživateli**: Zadejte počet položek, které se mají vrátit pro každého uživatele. Ve výchozím nastavení modul doporučuje pět položek.

6. **Minimální velikost fondu doporučení na uživatele**: Zadejte hodnotu, která označuje, kolik předchozích doporučení je vyžadováno. Ve výchozím nastavení je tento parametr nastaven na 2, což znamená, že položku doporučili alespoň dva další uživatelé.

   Tuto možnost použijte pouze v případě, že vyhodnocujete v režimu hodnocení. Tato možnost není k dispozici, pokud vyberete **možnost Ze všech položek** nebo Z položek bez hodnocení **(chcete-li uživatelům navrhnout nové položky).**

7.  Pro **z nehodnocených položek (navrhnout nové položky pro uživatele)**, použijte třetí vstupní port s názvem **Trénovací data**, chcete-li odebrat položky, které již byly hodnoceny z výsledků předpovědi.

    Chcete-li použít tento filtr, připojte původní trénovací datovou sadu ke vstupnímu portu.

8. Odešlete potrubí.

### <a name="results-of-item-recommendation"></a>Výsledky doporučení položky

Datová sada s hodnocením vrácená pomocí funkce Score SVD Recommender uvádí doporučené položky pro každého uživatele:

- První sloupec obsahuje identifikátory uživatele.
- V závislosti na hodnotě nastavené pro **maximální počet položek, které chcete uživateli doporučit,** je generováno několik dalších sloupců. Každý sloupec obsahuje doporučenou položku (podle identifikátoru). Doporučení jsou seřazeny podle spřažení uživatelských položek. Položka s nejvyšší spřažení je uvedena ve sloupci **Položka 1**.

> [!WARNING]
> Tuto datovou sadu s hodnocením nelze vyhodnotit pomocí modulu [Vyhodnotit doporučení.](evaluate-recommender.md)


##  <a name="technical-notes"></a>Technické poznámky

Pokud máte kanál s svd doporučující a přesunout model do produkčního prostředí, uvědomte si, že existují klíčové rozdíly mezi použitím doporučující v režimu hodnocení a jeho použití v produkčním režimu.

Hodnocení, podle definice, vyžaduje předpovědi, které lze ověřit proti *zemi pravdu* v testovací sadě. Při vyhodnocování doporučující, musí předpovědět pouze položky, které byly hodnoceny v testovací sadě. Tím se omezí možné hodnoty, které jsou předpovězeny.

Při zprovoznění modelu, obvykle změnit režim předpověď, aby doporučení na základě všech možných položek, chcete-li získat nejlepší předpovědi. Pro mnohé z těchto předpovědí neexistuje žádná odpovídající pozemní pravda. Takže přesnost doporučení nelze ověřit stejným způsobem jako během operací kanálu.


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
