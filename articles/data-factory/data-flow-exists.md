---
title: Transformace toku dat mapování Azure Data Factory existuje
description: Postup kontroly existujících řádků pomocí toků dat mapování služby Data Factory s transformací Exists
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 8b488a079b2da1bcf0dd064025ed251a1dc25213
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029383"
---
# <a name="mapping-data-flow-exists-transformation"></a>Transformace toku dat mapování existuje



Transformace EXISTS je transformace, která při filtrování řádků zastaví nebo umožňuje procházet řádky ve vašich datech. Transformace EXISTS je podobná ```SQL WHERE EXISTS``` a ```SQL WHERE NOT EXISTS```. Po transformaci Exists budou výsledné řádky z datového proudu zahrnovat všechny řádky, ve kterých ve zdroji 2 existují hodnoty sloupců ze zdroje 1 nebo neexistují ve zdroji 2.

![Existuje nastavení](media/data-flow/exists.png "1") .

Vyberte druhý zdroj pro existující, aby tok dat mohl porovnat hodnoty z streamu 1 proti streamu 2.

Vyberte sloupec ze zdroje 1 a ze zdrojového kódu 2, u kterého chcete, aby se hodnoty, které chcete kontrolovat, existuje nebo neexistují.

## <a name="multiple-exists-conditions"></a>Existuje několik podmínek.

U každého řádku v podmínkách sloupce pro existuje, když najedete myší na řádek dosáhnete, najdete symbol +. To vám umožní přidat více řádků pro podmínky Exists. Každá další podmínka je "a".

## <a name="custom-expression"></a>Vlastní výraz

Vlastní ![Nastavení]existuje(media/data-flow/exists1.png "vlastní")

Můžete kliknout na vlastní výraz a místo toho vytvořit výraz volné formy jako podmínku Exists nebo NOT EXISTS. Zaškrtnutím tohoto políčka umožníte zadat vlastní výraz jako podmínku.

## <a name="next-steps"></a>Další kroky

Podobné transformace jsou [Lookup](data-flow-lookup.md) a [Join](data-flow-join.md).
