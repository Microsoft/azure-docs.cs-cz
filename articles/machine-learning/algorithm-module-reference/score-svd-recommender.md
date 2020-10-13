---
title: 'Doporučení pro skóre SVD: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se, jak pomocí modulu skóre SVD doporučit v Azure Machine Learning k doporučení předpovědi pro skóre pro datovou sadu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/10/2020
ms.openlocfilehash: bf62fa995724b8e1fff757e89945cc39db3d9842
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90893714"
---
# <a name="score-svd-recommender"></a>Určení skóre doporučovacího systému SVD

Tento článek popisuje, jak v Návrháři Azure Machine Learning použít modul doporučení skóre SVD. Tento modul použijte k vytvoření předpovědi pomocí vyučeného modelu doporučení založeného na algoritmu SVD (Single Value rekompozice).

Doporučení SVD může generovat dva různé druhy předpovědi:

- [Předpověď hodnocení pro daného uživatele a položku](#prediction-of-ratings)
- [Doporučit položky uživateli](#recommendations-for-users)

Když vytváříte druhý typ předpovědi, můžete pracovat v jednom z těchto režimů:

- **Provozní režim** bere v úvahu všechny uživatele nebo položky. Obvykle se používá ve webové službě.

  Můžete vytvářet hodnocení pro nové uživatele, nikoli jenom uživatele, kteří se během školení viděli. Další informace najdete v tématu [technické poznámky](#technical-notes). 

- **Režim vyhodnocení** funguje na omezené sadě uživatelů nebo položek, které lze vyhodnotit. Obvykle se používá během operací kanálu.

Další informace o algoritmu doporučeného pro SVD najdete v tématu techniky vytváření [matrice pro zdroje informací pro doporučované systémy](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).

## <a name="how-to-configure-score-svd-recommender"></a>Jak nakonfigurovat doporučení SVD skóre

Tento modul podporuje dva typy předpovědi, z nichž každá má různé požadavky. 

###  <a name="prediction-of-ratings"></a>Předpověď hodnocení

Když odhadnete hodnocení, model vypočítá, jak bude uživatel reagovat na konkrétní položku, a to s ohledem na školicí data. Vstupní data pro bodování musí poskytovat rychlost uživateli i položku.

1. Přidejte do svého kanálu školený model doporučení a připojte ho k **škole SVD doporučení**. Model je potřeba vytvořit pomocí modulu SVD, který je [doporučený pro vlak](train-SVD-recommender.md) .

2. V případě **doporučeného druhu odhadu**vyberte **předpověď hodnocení**. Nejsou vyžadovány žádné další parametry.

3. Přidejte data, pro která chcete vytvořit předpovědi, a připojte je k **datové sadě ke skóre**.

   Pro model pro předpověď hodnocení musí vstupní datová sada obsahovat páry uživatelských položek.

   Datová sada může obsahovat volitelný třetí sloupec hodnocení pro pár uživatel-položka v prvním a druhém sloupci. Ale třetí sloupec se během předpovědi ignoruje.

4. Odešlete kanál.

### <a name="results-for-rating-predictions"></a>Výsledky pro hodnocení předpovědi 

Výstupní datová sada obsahuje tři sloupce: uživatelé, položky a předpokládané hodnocení pro každého vstupního uživatele a položku.

###  <a name="recommendations-for-users"></a>Doporučení pro uživatele 

Chcete-li doporučit položky pro uživatele, zadejte seznam uživatelů a položek jako vstup. Z těchto dat model používá své znalosti o stávajících položkách a uživatelích, aby vygeneroval seznam položek s pravděpodobným odvoláním pro jednotlivé uživatele. Můžete přizpůsobit počet vrácených doporučení. A můžete nastavit prahovou hodnotu pro počet předchozích doporučení, která jsou nutná k vygenerování doporučení.

1. Přidejte do svého kanálu školený model doporučení a připojte ho k **škole SVD doporučení**.  Model je potřeba vytvořit pomocí modulu SVD, který je [doporučený pro vlak](train-svd-recommender.md) .

2. Chcete-li doporučit položky pro seznam uživatelů, nastavte **druh předpovědi** doporučení na **položku doporučení**.

3. Pro **Výběr Doporučené položky**uveďte, zda používáte modul bodování v produkčním prostředí nebo pro vyhodnocení modelu. Vyberte jednu z těchto hodnot:

    - **Ze všech položek**: tuto možnost vyberte, Pokud nastavujete kanál pro použití ve webové službě nebo v produkčním prostředí.  Tato možnost povolí *režim výroby*. Modul vytváří doporučení ze všech položek zobrazených během školení.

    - **Z hodnocených položek (pro vyhodnocení modelu)**: tuto možnost vyberte, pokud vyvíjíte nebo testujete model. Tato možnost povolí *režim vyhodnocování*. Modul vytváří doporučení pouze z těch položek ve vstupní datové sadě, které byly ohodnoceny.
    
    - Z nezpracovaných **položek (pro návrh nových položek uživatelům)**: tuto možnost vyberte, pokud chcete, aby modul provedl doporučení jenom z těch položek v datové sadě školení, které nebyly ohodnocené. 

4. Přidejte datovou sadu, pro kterou chcete vytvořit předpovědi, a připojte ji k **datové sadě ke skóre**.

    - Pro **ze všech položek**by vstupní datová sada měla sestávat z jednoho sloupce. Obsahuje identifikátory uživatelů, pro které chcete vytvořit doporučení.

      Datová sada může obsahovat další dva sloupce identifikátorů a hodnocení položek, ale tyto dva sloupce jsou ignorovány. 

    - V případě **z hodnocených položek (pro vyhodnocení modelu)** by měla vstupní datová sada obsahovat páry uživatelských položek. První sloupec by měl obsahovat identifikátor uživatele. Druhý sloupec by měl obsahovat odpovídající identifikátory položek.

      Datová sada může zahrnovat třetí sloupec hodnocení položky uživatele, ale tento sloupec je ignorován.

    - V případě **z nehodnocených položek (pro návrh nových položek uživatelům)** by měla vstupní datová sada obsahovat páry uživatelských položek. První sloupec by měl obsahovat identifikátor uživatele. Druhý sloupec by měl obsahovat odpovídající identifikátory položek.

     Datová sada může zahrnovat třetí sloupec hodnocení položky uživatele, ale tento sloupec je ignorován.

5. **Maximální počet položek, které je potřeba doporučit pro uživatele**: zadejte počet položek, které se mají vrátit pro každého uživatele. Ve výchozím nastavení modul doporučuje pět položek.

6. **Minimální velikost fondu doporučení na uživatele**: zadejte hodnotu, která určuje, kolik předchozích doporučení je potřeba. Ve výchozím nastavení je tento parametr nastaven na hodnotu 2, což znamená, že položka doporučila aspoň dva další uživatelé.

   Tuto možnost použijte pouze v případě, že vyhodnocujete režim vyhodnocování. Možnost není k dispozici, pokud vyberete možnost **ze všech položek** nebo **z nehodnocených položek (pro návrh nových položek uživatelům)**.

7.  V případě z nezpracovaných **položek (pro návrh nových položek uživatelům)** použijte třetí vstupní port s názvem **školicí data**pro odebrání položek, které již byly ohodnoceny z výsledků předpovědi.

    Chcete-li použít tento filtr, připojte původní datovou sadu školení ke vstupnímu portu.

8. Odešlete kanál.

### <a name="results-of-item-recommendation"></a>Výsledky doporučení položky

Vyhodnocená datová sada vrácená funkcí skore SVD doporučuje, aby vypisuje Doporučené položky pro každého uživatele:

- První sloupec obsahuje identifikátory uživatelů.
- Vygeneruje se počet dalších sloupců v závislosti na hodnotě, kterou jste nastavili pro **maximální počet položek, které se doporučují pro uživatele**. Každý sloupec obsahuje doporučenou položku (podle identifikátoru). Doporučení jsou seřazená podle spřažení uživatelem a položky. Položka s největším spřažením je vložena do **položky sloupce 1**.


##  <a name="technical-notes"></a>Technické poznámky

Pokud máte kanál s doporučeným nástrojem SVD a přesouváte model do produkčního prostředí, je potřeba si uvědomit, že v režimu vyhodnocování se doporučuje použít klíčové rozdíly a použít je v provozním režimu.

Vyhodnocení, podle definice, vyžaduje předpovědi, které lze ověřit proti *zemi* v sadě testů. Když vyhodnotili doporučení, musí předpovědět pouze položky, které byly ohodnoceny v sadě testů. Tím se omezí možné hodnoty, které jsou předpovězeny.

Když model zprovozněníte, obvykle změníte režim předpovědi tak, aby vytvářely doporučení na základě všech možných položek, aby bylo možné získat nejlepší předpovědi. Pro mnohé z těchto předpovědi neexistuje žádná odpovídající země. Přesnost doporučení proto nelze ověřit stejným způsobem jako při operacích kanálu.


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
