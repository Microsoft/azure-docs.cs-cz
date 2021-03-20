---
title: Zpracování řádků chyb s mapováním toků dat v Azure Data Factory
description: Naučte se zpracovávat chyby zkracování SQL v Azure Data Factory pomocí mapování toků dat.
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/22/2020
ms.author: makromer
ms.openlocfilehash: a7a03ff1a58f50f16ebefce48b9e2772a16a011a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100386335"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>Zpracování řádků chyb zkracování SQL v Data Factory datových toků mapování

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Běžným scénářem v Data Factory při použití toků dat mapování je psaní transformovaných dat do databáze v Azure SQL Database. V tomto scénáři je běžný chybový stav, který je třeba zabránit proti, možné zkrátit sloupec.

Existují dvě primární metody pro řádné zpracování chyb při zápisu dat do jímky databáze v datových tocích ADF:

* Při zpracování dat databáze nastavte [zpracování řádků chyb](./connector-azure-sql-database.md#error-row-handling) jímky na "pokračovat při chybě". Toto je automatizovaná metoda catch-All, která nevyžaduje vlastní logiku v toku dat.
* Případně postupujte podle následujících kroků a poskytněte protokolování sloupců, které se nevejdou do sloupce cílového řetězce, což umožňuje, aby tok dat pokračoval.

> [!NOTE]
> Při povolování automatického zpracování řádků chyb na rozdíl od metody uvedené níže při psaní vlastní logiky zpracování chyb dojde k malému snížení výkonu a k provedení dvou fází, které se provádí pomocí ADF, provede v rámci dvoufázové operace zachycení chyb.

## <a name="scenario"></a>Scenario

1. Máme cílovou databázovou tabulku, která má ```nvarchar(5)``` sloupec s názvem "název".

2. V našem toku dat chceme namapovat názvy filmů z naší jímky do cílového sloupce "název".

    ![Tok filmových dat 1](media/data-flow/error4.png)
    
3. Problémem je to, že název filmu se nevejde do sloupce jímky, který může obsahovat pouze 5 znaků. Při spuštění tohoto toku dat se zobrazí chybová zpráva podobná této: ```"Job failed due to reason: DF-SYS-01 at Sink 'WriteToDatabase': java.sql.BatchUpdateException: String or binary data would be truncated. java.sql.BatchUpdateException: String or binary data would be truncated."```

V tomto videu se seznámíte s příkladem logiky zpracování řádků chyb při vytváření datového toku:
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4uOHj]

## <a name="how-to-design-around-this-condition"></a>Postup pro návrh kolem této podmínky

1. V tomto scénáři je maximální délka sloupce "název" pět znaků. Pojďme tedy přidat transformaci podmíněného rozdělení, která nám umožní protokolovat řádky s "tituly", které jsou delší než pět znaků, a zároveň povolit zbývající část řádků, které se vejdou do tohoto prostoru pro zápis do databáze.

    ![podmíněné rozdělení](media/data-flow/error1.png)

2. Tato transformace podmíněného rozdělení definuje maximální délku "title" na pět. Libovolný řádek, který je menší nebo roven pěti, bude přecházet do ```GoodRows``` datového proudu. Libovolný řádek, který je větší než pět, přejde do ```BadRows``` datového proudu.

3. Teď potřebujeme protokolovat řádky, které selhaly. Přidejte do datového proudu transformaci jímky ```BadRows``` pro protokolování. Tady budeme "automaticky namapovat" všechna pole, aby bylo protokolování kompletního záznamu transakce. Toto je výstup souboru CSV s oddělovači do jednoho souboru v Blob Storage. Zavoláme soubor protokolu "badrows.csv".

    ![Chybné řádky](media/data-flow/error3.png)
    
4. Dokončený tok dat je uvedený níže. Nyní je možné rozdělit řádky chyb, aby se předešlo chybám při zkracování SQL a vložením těchto záznamů do souboru protokolu. Mezitím úspěšné řádky můžou pokračovat v zápisu do naší cílové databáze.

    ![dokončení toku dat](media/data-flow/error2.png)

5. Pokud zvolíte možnost zpracování řádků chyby v transformaci jímky a nastavíte "řádky s chybou výstupu", bude ADF automaticky vygenerovat výstup souboru CSV pro data řádku spolu s chybovými zprávami ovladače. Tuto logiku nemusíte do toku dat přidat ručně pomocí této alternativní možnosti. Tato možnost má za sníženou účinnost, aby ADF mohla implementovat 2 fáze metodologie pro zachycení chyb a jejich protokolování.

    ![dokončení toku dat s chybovými řádky](media/data-flow/error-row-3.png)

## <a name="next-steps"></a>Další kroky

* Sestavte zbývající část logiky toku dat pomocí [transformací](concepts-data-flow-overview.md)mapování toků dat.