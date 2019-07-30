---
title: Párování a zarovnání vět – vlastní Překladatel
titleSuffix: Azure Cognitive Services
description: Během provádění školení jsou věty přítomné v paralelních dokumentech spárovány nebo zarovnány. Vlastní Překladatel se učí překlady po jednotlivých větách, a to tak, že si přečte větu a překládá tuto větu. Pak zarovnává slova a fráze v těchto dvou větách sobě navzájem.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: e9bc5c876da6bd2be1b22b389b819e51330b2e50
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595469"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Párování a zarovnání vět v paralelních dokumentech

V průběhu školení jsou věty přítomné v paralelních dokumentech spárovány nebo zarovnány. Vlastní Překladatel oznamuje počet vět, které byly v každé sadě dat spárovány jako zarovnaných vět.

## <a name="pairing-and-alignment-process"></a>Párování a proces zarovnání

Vlastní Překladatel se učí překlady vět v jednom okamžiku. Čte ze zdroje větu a pak překládá tuto větu z cíle. Pak zarovnává slova a fráze v těchto dvou větách sobě navzájem. Tento proces umožňuje IT vytvořit mapu slov a frází v jedné větě na ekvivalentní slova a fráze v překladu této věty. Zarovnání se snaží zajistit, aby systém vlakových a vět navzájem přecházejí.

## <a name="pre-aligned-documents"></a>Předem zarovnané dokumenty

Pokud víte, že máte paralelní dokumenty, můžete zarovnání věty přepsat zadáním předem zarovnaných textových souborů. Můžete extrahovat všechny věty z obou dokumentů do textového souboru, uspořádat jednu větu na řádek a nahrát s `.align` příponou. `.align` Rozšíření signalizuje vlastní překladateli, že by měl přeskočit zarovnání věty.

Pro dosažení co nejlepších výsledků se ujistěte, že máte v souborech jednu větu na řádek. Ve větě nemusíte mít znaky nového řádku, protože by to způsobilo špatné zarovnání.

## <a name="suggested-minimum-number-of-extracted-and-aligned-sentences"></a>Navrhovaný minimální počet vět, které byly extrahovány a zarovnány

Pro úspěšné školení zobrazuje tabulka níže minimální počet extrahovaných vět a zarovnané věty vyžadované v každé sadě dat. Navrhovaný minimální počet vět je mnohem vyšší než navrhovaný minimální počet vět zarovnaných, aby se zohlednila skutečnost, že zarovnání věty nemusí být schopné úspěšně zarovnat všechny extrahované věty.

| Sada dat   | Navrhovaný minimální počet extrahovaných vět | Navrhovaný minimální počet vět pro zarovnání | Maximální počet vět zarovnaných |
|------------|--------------------------------------------|------------------------------------------|--------------------------------|
| Školení   | 10,000                                     | 2 000                                    | Bez horní meze                 |
| Ladění     | 2 000                                      | 500                                      | 2,500                          |
| Testování    | 2 000                                      | 500                                      | 2,500                          |
| Slovník | 0                                          | 0                                        | Bez horní meze                 |

## <a name="next-steps"></a>Další postup

- Naučte se používat [slovník](what-is-dictionary.md) ve vlastním překladateli.
