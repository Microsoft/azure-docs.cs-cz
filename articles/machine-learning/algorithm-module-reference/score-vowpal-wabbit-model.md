---
title: Hodnocení modelu Vowpal Wabbit
titleSuffix: Azure Machine Learning
description: Naučte se používat modul skore pro dostupné model ke generování skóre pro sadu vstupních dat pomocí existujícího modelu pro dostupné.
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/02/2020
ms.openlocfilehash: 23dfee7b78f2606c54525391e1260af69a9b0779
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90898397"
---
# <a name="score-vowpal-wabbit-model"></a>Hodnocení modelu Vowpal Wabbit
Tento článek popisuje, jak pomocí modulu **pro (skore dostupné model** ) v Návrháři Azure Machine Learning vytvořit skóre pro sadu vstupních dat pomocí existujícího modelu pro dostupné.  

Tento modul poskytuje nejnovější verzi rozhraní pro dostupné Framework verze 8.8.1. Tento modul použijte k určení skóre dat pomocí výukového modelu uloženého ve formátu VW verze 8.  

## <a name="how-to-configure-score-vowpal-wabbit-model"></a>Postup konfigurace modelu skóre pro dostupné

1.  Do experimentu přidejte modul **pro dostupné pro hodnocení skóre** .  
  
2.  Přidejte vyškolený model dostupné pro a připojte ho k portu pro vstup na levé straně. Můžete použít vyškolený model vytvořený ve stejném experimentu nebo vyhledat uložený model v kategorii **datové sady** v levém navigačním podokně návrháře. Model však musí být k dispozici v Návrháři Azure Machine Learning.  
  
    > [!NOTE]
    > Podporují se jenom modely 8.8.1 pro dostupné. uložené modely, které byly vyškolené pomocí jiných algoritmů, se nedají připojit.
  
3.  Přidejte testovací datovou sadu a připojte ji ke vstupnímu portu na pravé straně. Pokud je testovací datová sada adresář, který obsahuje soubor testovacích dat, zadejte název souboru testovacích dat s **názvem souboru testovacích dat**. Pokud je testovací datová sada jedním souborem, ponechte **název souboru testovacích dat** prázdný.

4. Do textového pole **argumenty VW** zadejte sadu platných argumentů příkazového řádku pro spustitelný soubor pro dostupné.  

    Informace o tom, které argumenty pro dostupné jsou v Azure Machine Learning podporované a nepodporované, najdete v části [technické poznámky](#technical-notes) .  

5.  **Název souboru testovacích dat**: zadejte název souboru, který obsahuje vstupní data. Tento argument se používá pouze v případě, že je testovací datová sada adresář.

6. **Zadejte typ souboru**: Určete, který formát vaše školicí data používá. Pro dostupné podporuje tyto dva formáty vstupních souborů:  

   - **VW** představuje interní formát používaný pro dostupné. Podrobnosti najdete na [stránce wiki pro dostupné](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format) . 
   - **SVMLight** je formát používaný některými jinými nástroji pro strojové učení. 

7. Vyberte možnost a **přidejte sloupec navíc obsahující popisky**, pokud chcete výstupní popisky spolu s výsledky.  

   Obvykle při zpracování textových dat pro dostupné nevyžaduje popisky a vrátí pouze skóre pro každý řádek dat.  

8. Tuto možnost vyberte, pokud chcete výstupovat nezpracované výsledky společně s výsledky, **Zahrňte sloupec navíc obsahující nezpracovaná skóre**.  

9. Odešlete kanál.

## <a name="results"></a>Výsledky

Po dokončení školení:

+ Chcete-li vizualizovat výsledky, klikněte pravým tlačítkem na výstup modulu [skore pro dostupné model](score-vowpal-wabbit-model.md) . Výstup označuje skóre předpovědi normalizované z 0 na 1. 

+ Pro vyhodnocení výsledků by měla výstupní datová sada obsahovat konkrétní názvy sloupců skóre, které splňují požadavky na modul pro vyhodnocení modelu.

  + Pro úlohu regrese musí mít datová sada k vyhodnocení jeden sloupec s názvem `Regression Scored Labels` , který představuje popisky skóre.
  + Pro úlohu binární klasifikace musí mít datová sada, která se má vyhodnotit, dva sloupce s názvem `Binary Class Scored Labels` , `Binary Class Scored Probabilities` , které představují popisky s skóre a pravděpodobnosti v uvedeném pořadí.
  + Pro úlohu více klasifikací musí mít datová sada, která se má vyhodnotit, jeden sloupec s názvem `Multi Class Scored Labels` , který představuje popisky skóre.

  Všimněte si, že není možné vyhodnotit výsledky modulu pro dostupné modelu skóre přímo. Před vyhodnocením je nutné datovou sadu upravit podle výše uvedených požadavků.

##  <a name="technical-notes"></a>Technické poznámky

Tato část obsahuje podrobné informace o implementaci, tipy a odpovědi na nejčastější dotazy.

### <a name="parameters"></a>Parametry

Pro dostupné má mnoho možností příkazového řádku pro výběr a ladění algoritmů. Úplnou diskusi o těchto možnostech tady není možné; doporučujeme zobrazit [stránku wikiwebu pro dostupné](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments).  

Následující parametry nejsou podporovány v Azure Machine Learning Studio (Classic).  

-   Možnosti vstupu a výstupu zadané v [https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)  
  
     Tyto vlastnosti jsou již modulem konfigurovány automaticky.  
  
-   Kromě toho je zakázána jakákoli možnost, která generuje více výstupů nebo přijímá více vstupů. Mezi ně patří *`--cbt`* , *`--lda`* a *`--wap`* .  
  
-   Podporují se jenom kontrolní algoritmy pro učení pod dohledem. Tato možnost nepovoluje tyto možnosti: *`–active`* , `--rank` , *`--search`* atd.  

Všechny argumenty kromě těch, které jsou popsané výše, jsou povoleny.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 