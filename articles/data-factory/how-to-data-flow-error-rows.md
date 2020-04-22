---
title: Zpracování chybových řádků s mapováním toků dat v Azure Data Factory
description: Zjistěte, jak zpracovat chyby zkrácení SQL v Azure Data Factory pomocí toků dat mapování.
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: makromer
ms.openlocfilehash: 8225143bb75118620b45c2520bb62ea30501a617
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732686"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>Zpracování řádků chyb zkrácení SQL v tocích dat mapování datové továrny dat

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Běžným scénářem v Datové továrně při použití mapování toků dat je zápis transformovaných dat do databáze Azure SQL. V tomto scénáři je možné zkrácení sloupce běžné chybové podmínky, které je nutné zabránit proti. Pomocí těchto kroků můžete zajistit protokolování sloupců, které se nevejdou do sloupce cílového řetězce, což umožní, aby tok dat v těchto scénářích pokračoval.

## <a name="scenario"></a>Scénář

1. Máme cílovou databázovou tabulku Azure ```nvarchar(5)``` SQL, která má sloupec s názvem "name".

2. Uvnitř našeho toku dat chceme mapovat názvy filmů z našeho dřezu do tohoto cílového sloupce "jméno".

    ![Tok filmových dat 1](media/data-flow/error4.png)
    
3. Problém je v tom, že název filmu se nevejde do sloupce jímky, který pojme pouze 5 znaků. Při spuštění tohoto toku dat se zobrazí chyba, jako je tato:```"Job failed due to reason: DF-SYS-01 at Sink 'WriteToDatabase': java.sql.BatchUpdateException: String or binary data would be truncated. java.sql.BatchUpdateException: String or binary data would be truncated."```

Toto video vás provede příkladem nastavení logiky zpracování chyb ve vašem toku dat:
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4uOHj]

## <a name="how-to-design-around-this-condition"></a>Jak navrhnout kolem této podmínky

1. V tomto scénáři maximální délka sloupce "název" je pět znaků. Takže přidáme podmíněné rozdělení transformace, která nám umožní protokolovat řádky s "tituly", které jsou delší než pět znaků a zároveň umožňuje zbytek řádků, které se vejdou do tohoto prostoru pro zápis do databáze.

    ![podmíněné rozdělení](media/data-flow/error1.png)

2. Tato podmíněná rozdělení transformace definuje maximální délku "title" být pět. Všechny řádky, které je menší nebo rovno pět přejde do datového ```GoodRows``` proudu. Každý řádek, který je větší ```BadRows``` než pět přejde do datového proudu.

3. Nyní musíme protokolovat řádky, které se nezdařily. Přidejte transformace jímky do datového ```BadRows``` proudu pro protokolování. Zde budeme "auto-mapovat" všechna pole tak, abychom zaznamenali kompletní záznam transakce. Toto je výstup souboru CSV odděleného textem pro jeden soubor v úložišti objektů Blob. Budeme volat soubor protokolu "badrows.csv".

    ![Chybné řádky](media/data-flow/error3.png)
    
4. Dokončený tok dat je uveden níže. Nyní jsme schopni oddělit chybové řádky, abychom se vyhnuli chybám zkrácení SQL a vložili tyto položky do souboru protokolu. Mezitím úspěšné řádky můžete pokračovat v zápisu do naší cílové databáze.

    ![kompletní tok dat](media/data-flow/error2.png)

## <a name="next-steps"></a>Další kroky

* Vytvořte zbytek logiky toku dat pomocí mapování [transformace](concepts-data-flow-overview.md)toků dat .
