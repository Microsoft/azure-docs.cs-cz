---
title: Správa grafu toku dat mapování
description: Jak efektivně spravovat a upravovat Graf toku dat mapování
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2020
ms.openlocfilehash: 0cdad47123d69ca7cee468c5bb0cea3268d73bfe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89420078"
---
# <a name="managing-the-mapping-data-flow-graph"></a>Správa grafu toku dat mapování

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Mapování toků dat se vytváří pomocí návrhové plochy, která je v grafu toku dat. V grafu je logika transformace sestavena zleva doprava a další datové proudy jsou přidány shora dolů. Chcete-li přidat novou transformaci, vyberte znaménko plus na pravé straně existující transformace.

![Plátno](media/data-flow/canvas2.png "Plátno")

Díky následujícím mechanismům můžete efektivně Procházet a spravovat Graf toku dat, protože vaše toky dat jsou složitější. 

## <a name="moving-transformations"></a>Přesun transformací

V části mapování toků dat se sada propojených transformačních logiky označuje jako **datový proud**. V poli **příchozí datový** proud se určí, který datový proud zaznamená aktuální transformaci. Každá transformace má jeden nebo dva příchozí datové proudy v závislosti na její funkci a představuje výstupní datový proud. Výstupní schéma příchozích datových proudů určuje, která metadata sloupce mohou být odkazována aktuální transformací.

![Přesunout uzel](media/data-flow/move-nodes.png "přesunout uzel")

Na rozdíl od plátna kanálu se transformace toku dat neupravují pomocí modelu přetažení. Chcete-li změnit příchozí datový proud nebo přesunout transformaci, vyberte jinou hodnotu z rozevíracího seznamu **příchozí datový proud** . Když to uděláte, všechny transformace s dalšími možnostmi se přesunou společně s upravenou transformací. Graf se automaticky aktualizuje a zobrazí se nový logický tok. Pokud příchozí datový proud změníte na transformaci, která už má transformaci po podmnožině dat, vytvoří se nová větev nebo paralelní datový proud. Přečtěte si další informace o [nových větvích v mapování toku dat](data-flow-new-branch.md).

## <a name="hide-graph-and-show-graph"></a>Skrýt graf a zobrazit graf

Při úpravách transformace můžete panel konfigurace rozbalit, aby se zabralo celé plátno, a graf se skryje. Klikněte na dvojitou šipku vzhůru umístěnou na pravé straně plátna.

![Skrýt graf](media/data-flow/hide-graph.png "Skrýt graf")

Když je graf skrytý, můžete se pohybovat mezi transformacemi v rámci datového proudu kliknutím na tlačítko **Další** nebo **předchozí**. Kliknutím na dvojitou šipku směřující dolů zobrazíte graf.

![Zobrazit graf](media/data-flow/show-graph.png "Zobrazit graf")

## <a name="searching-for-transformations"></a>Hledání transformací

Pokud chcete rychle najít transformaci v grafu, klikněte na ikonu **hledání** nad nastavením přiblížení.

![Hledání](media/data-flow/search-1.png "Vyhledat graf")

Můžete vyhledat transformaci podle názvu nebo popisu transformace.

![Hledání](media/data-flow/search-2.png "Vyhledat graf")

## <a name="hide-reference-nodes"></a>Skrýt uzly odkazů

Pokud má váš datový tok jakékoli transformace, vyhledávání, zobrazení nebo sjednocení, tok dat zobrazuje referenční uzly pro všechny příchozí streamy. Pokud chcete minimalizovat velikost svislého místa, můžete své referenční uzly minimalizovat. Provedete to tak, že kliknete pravým tlačítkem na plátno a vyberete **Skrýt referenční uzly**.

![Skrýt uzly odkazů](media/data-flow/hide-reference-nodes.png "Skrýt uzly odkazů")

## <a name="next-steps"></a>Další kroky

Po dokončení logiky toku dat zapněte [režim ladění](concepts-data-flow-debug-mode.md) a otestujte ho v náhledu dat.
