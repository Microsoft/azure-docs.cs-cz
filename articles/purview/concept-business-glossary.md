---
title: Principy funkcí podnikového glosáře v Azure dosah (Preview)
description: Tento článek vysvětluje, co je obchodní Glosář v Azure dosah.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 8b391438d8d6605e7ef493a6552af634db840ad5
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553255"
---
# <a name="understand-business-glossary-features-in-azure-purview"></a>Principy funkcí podnikového glosáře v Azure dosah

Tento článek poskytuje přehled funkce obchodní Glosář v Azure dosah. 

## <a name="business-glossary"></a>Obchodní Glosář

Glosář poskytuje slovník pro obchodní uživatele.  Skládá se z obchodních podmínek, které mohou být vzájemně propojené a umožňují jejich kategorizaci do kategorií, aby je bylo možné chápat v různých kontextech. Tyto výrazy se pak dají namapovat na prostředky, jako je databáze, tabulky, sloupce atd. To pomáhá při abstrakci technických žargonu přidružených k úložištím dat a umožňuje firemnímu uživateli zjistit a pracovat s daty ve slovním, které jsou pro ně lépe obeznámené.


Obchodní Glosář je kolekce termínů. Každý termín představuje objekt v organizaci a je velmi pravděpodobný, že stejný objekt představuje více výrazů. Zákazník by taky mohl označovat jako klienta, nákupčího nebo nákupčího. Tato vícenásobná slova mají vzájemnou relaci. Relace mezi těmito podmínkami by mohla být jedna z následujících:

- synonyma – různé výrazy se stejnou definicí
- související – jiný název s podobnou definicí

Stejný termín může také znamenat více obchodních objektů. Je důležité, aby byl každý výraz jasně definovaný a jasně srozumitelný v rámci organizace.

## <a name="custom-attributes"></a>Vlastní atributy

Azure dosah podporuje osm předem připravených atributů pro jakékoli obchodní glosáře:
- Název
- Definice
- Stewards dat
- Odborníky na data
- Akronym
- Synonyma
- Související výrazy
- Zdroje a prostředky

Tyto atributy nelze upravovat ani odstraňovat. Tyto atributy ale nestačí k úplnému definování termínu v organizaci. Pro vyřešení tohoto problému poskytuje dosah funkci, kde můžete definovat vlastní atributy pro Glosář.

## <a name="term-templates"></a>Šablony termínů

Šablony termínů nabízí, aby se vlastní atributy glosáře logicky seskupují v katalogu. Tato funkce umožňuje seskupit všechny relevantní vlastní atributy společně v šabloně a pak šablonu použít při vytváření glosářového termínu. Například všechny vlastní atributy související s financemi, jako je nákladové středisko, ziskové centrum, účetní kód může být seskupena v šabloně finance šablony termínu a šablona finance se dá použít k vytvoření podmínek finančního glosáře.

Všechny standardní atributy se seskupují do výchozí systémové šablony. Každá šablona termínu, kterou vytvoříte, bude obsahovat tyto atributy spolu s dalšími vlastními atributy vytvořenými jako součást procesu vytvoření šablony.

## <a name="glossary-vs-classification-vs-sensitivity-labels"></a>Glosář vs klasifikace a citlivostní popisky

I když jsou glosáře pojmem, klasifikace a popisky jsou poznámky k datovému assetu, každá z nich má v kontextu katalogu jiný význam. 

### <a name="glossary"></a>Glosář

Jak je uvedeno výše, pojem obchodní glosář definuje obchodní slovník pro organizaci a pomáhá přemostění mezer mezi různými odděleními ve vaší společnosti.

### <a name="classifications"></a>Klasifikace

Klasifikace jsou poznámky, které je možné přiřadit k entitám. Flexibilita klasifikací umožňuje jejich použití pro více scénářů, jako jsou například:

- Princip charakteru dat uložených v datových prostředcích
- definování zásad řízení přístupu

Dosah má ještě více než 100 systémových klasifikátorů a v katalogu můžete definovat vlastní klasifikátory. V rámci procesu skenování tyto klasifikace automaticky zjišťujeme a aplikujeme na datové assety a schémata. Kdykoli je však můžete kdykoli přepsat. Při automatických kontrolách se přepisy člověka nikdy nenahrazují.

### <a name="sensitivity-labels"></a>Popisky citlivosti

Popisky citlivosti představují typ anotace, který umožňuje klasifikovat a chránit data vaší organizace bez nutnosti bránit produktivitě a spolupráci. Popisky citlivosti se používají k identifikaci kategorií typů klasifikace v rámci dat vaší organizace a k seskupení zásad, které chcete použít u jednotlivých kategorií. Dosah využívá stejné typy citlivých informací jako Microsoft 365, což vám umožní roztáhnout stávající zásady zabezpečení a jejich ochranu napříč celým obsahem a datovou podstatou. Stejné popisky je možné sdílet mezi systém Microsoft Officemi produkty a datovými assety v dosah.

## <a name="next-steps"></a>Další kroky

- [Správa šablon termínů](how-to-manage-term-templates.md)
- [Procházení katalogu Data Catalog v Azure dosah](how-to-browse-catalog.md)
