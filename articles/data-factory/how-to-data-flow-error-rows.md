---
title: Zpracování řádků chyb s mapováním toků dat v Azure Data Factory
description: Naučte se zpracovávat chyby zkracování SQL v Azure Data Factory pomocí mapování toků dat.
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: makromer
ms.openlocfilehash: 3fe3403ad06d82ba5ccd33d2718bf0e5eff64490
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73166535"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>Zpracování řádků chyb zkracování SQL v Data Factory datových toků mapování

Běžným scénářem v Data Factory při použití toků mapování dat je zápis transformovaných dat do databáze SQL Azure. V tomto scénáři je běžný chybový stav, který je třeba zabránit proti, možné zkrátit sloupec. Pomocí těchto kroků můžete zadat protokolování sloupců, které se nevejdou do sloupce cílového řetězce, což umožňuje, aby tok dat v těchto scénářích pokračoval.

## <a name="scenario"></a>Scénář

1. Máme cílovou tabulku Azure SQL Database, která má sloupec ```nvarchar(5)``` s názvem "Name".

2. V našem toku dat chceme namapovat názvy filmů z naší jímky do cílového sloupce "název".

    ![Tok filmových dat 1](media/data-flow/error4.png)
    
3. Problémem je to, že název filmu se nevejde do sloupce jímky, který může obsahovat pouze 5 znaků. Při spuštění tohoto toku dat se zobrazí chybová zpráva, jako je tato: ```"Job failed due to reason: DF-SYS-01 at Sink 'WriteToDatabase': java.sql.BatchUpdateException: String or binary data would be truncated. java.sql.BatchUpdateException: String or binary data would be truncated."```

## <a name="how-to-design-around-this-condition"></a>Postup pro návrh kolem této podmínky

1. V tomto scénáři je maximální délka sloupce "název" pět znaků. Pojďme tedy přidat transformaci podmíněného rozdělení, která nám umožní protokolovat řádky s "tituly", které jsou delší než pět znaků, a zároveň povolit zbývající část řádků, které se vejdou do tohoto prostoru pro zápis do databáze.

    ![podmíněné rozdělení](media/data-flow/error1.png)

2. Tato transformace podmíněného rozdělení definuje maximální délku "title" na pět. Libovolný řádek, který je menší nebo roven pěti, přejde do ```GoodRows```ového streamu. Libovolný řádek, který je větší než pět, přejde do datového proudu ```BadRows```.

3. Teď potřebujeme protokolovat řádky, které selhaly. Přidejte transformaci jímky do ```BadRows```ového datového proudu pro protokolování. Tady budeme "automaticky namapovat" všechna pole, aby bylo protokolování kompletního záznamu transakce. Toto je výstup souboru CSV s oddělovači do jednoho souboru v Blob Storage. Zavoláme soubor protokolu "badrows. csv".

    ![Chybné řádky](media/data-flow/error3.png)
    
4. Dokončený tok dat je uvedený níže. Nyní je možné rozdělit řádky chyb, aby se předešlo chybám při zkracování SQL a vložením těchto záznamů do souboru protokolu. Mezitím úspěšné řádky můžou pokračovat v zápisu do naší cílové databáze.

    ![dokončení toku dat](media/data-flow/error2.png)

## <a name="next-steps"></a>Další kroky

* Sestavte zbývající část logiky toku dat pomocí [transformací](concepts-data-flow-overview.md)mapování toků dat.
