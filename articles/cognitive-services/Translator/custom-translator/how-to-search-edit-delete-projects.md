---
title: Jak hledat, upravovat a odstraňovat projekty – vlastní Překladatel
titleSuffix: Azure Cognitive Services
description: Vlastní Překladatel nabízí různé způsoby, jak efektivně spravovat projekty. Můžete vytvořit více projektů, vyhledávat podle svých kritérií, upravovat své projekty. Odstranění projektu je možné také ve vlastním překladateli.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 308ca25d35011c67ded7300177149cd590462952
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896439"
---
# <a name="search-edit-and-delete-projects"></a>Hledání, úpravy a odstraňování projektů

Vlastní Překladatel nabízí několik způsobů, jak můžete své projekty spravovat účinným způsobem. Můžete vytvořit mnoho projektů, vyhledávat podle vašich kritérií a upravovat projekty. Odstranění projektu je možné také ve vlastním překladateli.  

## <a name="search-and-filter-projects"></a>Hledání a filtrování projektů

Nástroj filtru umožňuje vyhledávat projekty podle různých podmínek filtrování. Filtruje jako název projektu, stav, zdrojový a cílový jazyk a kategorii projektu.

1. Klikněte na tlačítko filtr.

    ![Hledat projekt](media/how-to/how-to-search-project.png)

2. Můžete filtrovat podle všech (nebo všech) následujících polí: název projektu, jazyk zdrojového kódu, cílový jazyk, kategorie a dostupnost projektu.

3. Klikněte na použít.

    ![Vyhledat možnosti filtru projektu](media/how-to/how-to-search-project-filters.png)

4. Pokud chcete zobrazit všechny vaše projekty klepnutím na Vymazat, vymažte filtr.

## <a name="edit-a-project"></a>Upravit projekt

Vlastní Překladatel vám dává možnost upravit název a popis projektu. Jiná metadata projektu, jako je kategorie, zdrojový jazyk a cílový jazyk, nejsou k dispozici pro úpravy. Následující postup popisuje, jak upravit projekt.

1. Klikněte na ikonu tužky, která se zobrazí při najetí myší na projekt.

    ![Upravit projekt](media/how-to/how-to-edit-project.png)

2. V dialogovém okně můžete upravit název projektu, popis projektu, Popis kategorie a popisek projektu, pokud není nasazen žádný model. Po vytvoření projektu nelze změnit dvojici kategorie nebo jazyka.

    ![Dialogové okno Upravit projekt](media/how-to/how-to-edit-project-dialog.png)

3. Klikněte na tlačítko Uložit.

## <a name="delete-a-project"></a>Odstranit projekt

Projekt můžete odstranit, pokud ho již nepotřebujete. Nastavte, aby projekt nemá modely v aktivním stavu, jako je například nasazené, školení odesláno, zpracování dat, nasazování atd., jinak operace odstranění selže. Následující kroky popisují, jak odstranit projekt.

1. Najeďte myší na libovolný záznam projektu a klikněte na ikonu koše.

   ![Odstranit projekt](media/how-to/how-to-delete-project.png)

2. Potvrďte odstranění. Odstraněním projektu dojde k odstranění všech modelů, které byly vytvořeny v rámci daného projektu. Odstranění projektu nebude mít vliv na vaše dokumenty.

   ![Potvrzovací dialog pro odstranění](media/how-to/how-to-delete-project-confirm.png)

## <a name="next-steps"></a>Další kroky

- [Nahrajte dokumenty](how-to-upload-document.md) a začněte vytvářet vlastní model překladu.
