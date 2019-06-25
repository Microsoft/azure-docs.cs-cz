---
title: Azure Data Factory mapování toku existují Data transformace
description: Návod k ověření pro existující řádky pomocí data factory mapovacích dat, toky se transformace Exists
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: b98b7afb21f2f50d44ba93ed793b6efb20f75164
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65235991"
---
# <a name="mapping-data-flow-exists-transformation"></a>Mapování toku dat existuje transformace

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Transformace Exists je řádek transformace, která zastaví nebo povolí řádků ve vašich datech a procházet skrz filtrování. Existuje transformace je podobný ```SQL WHERE EXISTS``` a ```SQL WHERE NOT EXISTS```. Po transformaci existuje buď výsledných řádků z datového proudu zahrne všechny řádky, pokud existují hodnoty sloupce ze zdroje 1 ve zdroji 2 nebo neexistují ve zdroji 2.

![Existuje nastavení](media/data-flow/exists.png "existuje 1")

Zvolte druhý zdroj pro vaše Exists tak, aby tok dat můžete porovnat hodnoty z Stream 1 na Stream 2.

Vyberte sloupec, ze zdroje 1 a 2 zdroj jehož hodnoty, které chcete kontrolovat proti Exists nebo neexistuje.

## <a name="multiple-exists-conditions"></a>Existuje více podmínek

Vedle každý řádek ve svých podmínkách sloupec pro Exists zjistíte + přihlašování k dispozici při najetí myší na dosažení řádek. To vám umožní přidat více řádků Exists podmínky. Všechny další podmínky, je "A".

## <a name="custom-expression"></a>Vlastní výraz

![Neexistuje vlastní nastavení](media/data-flow/exists1.png "existuje vlastní")

Můžete kliknout na "Vlastní výraz" můžete místo toho vytvořit výraz volného tvaru jako vaše neexistuje nebo není existuje podmínka. Zaškrtnutím tohoto políčka vám umožní zadat vlastní výraz jako podmínku.

## <a name="next-steps"></a>Další postup

Jsou podobné transformaci [vyhledávání](data-flow-lookup.md) a [připojit](data-flow-join.md).
