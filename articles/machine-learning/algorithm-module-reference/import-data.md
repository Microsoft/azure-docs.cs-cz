---
title: 'Import dat: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Import ovat data v Azure Machine Learning načíst data do kanálu strojového učení z existujících cloudových datových služeb.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: d124fdc15bd34743b237985a66cc35625f5d9a4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456195"
---
# <a name="import-data-module"></a>Modul Importovat data

Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

Tento modul slouží k načtení dat do kanálu strojového učení z existujících cloudových datových služeb. 

> [!Note]
> Všechny funkce poskytované tímto modulem lze provádět **úložištěm dat** a **datovými sadami** na vstupní stránce worksapce. Doporučujeme používat **úložiště dat** a **datovou sadu,** která obsahuje další funkce, jako je monitorování dat. Další informace najdete v článku [Jak získat přístup k datům](../how-to-access-data.md) a Jak [zaregistrovat datové sady.](../how-to-create-register-datasets.md)
> Po registraci datové sady ji najdete v kategorii **Datové sady** -> **Moje datové sady** v rozhraní návrháře. Tento modul je vyhrazen pro uživatele studia (klasické) pro známé prostředí. 
>

Modul **Import dat** podporuje čtená data z následujících zdrojů:

- ADRESA URL přes HTTP
- Cloudová úložiště Azure prostřednictvím [**datových úložišť**](../how-to-access-data.md))
    - Kontejner objektů blob Azure
    - Sdílená složka Azure
    - Azure Data Lake
    - Azure Data Lake Gen2
    - Azure SQL Database
    - Azure PostgreSQL    

Před použitím cloudového úložiště je třeba nejprve zaregistrovat úložiště dat v pracovním prostoru Azure Machine Learning. Další informace naleznete v [tématu Jak získat přístup k datům](../how-to-access-data.md). 

Po definování požadovaných dat a připojení ke zdroji **[importdat](./import-data.md)** odvodí datový typ každého sloupce na základě hodnot, které obsahuje, a načte data do kanálu návrháře. Výstup **importu dat** je datová sada, kterou lze použít s libovolným kanálem návrháře.

Pokud se zdrojová data změní, můžete aktualizovat datovou sadu a přidat nová data opětovným spuštěním [importu dat](./import-data.md).

## <a name="how-to-configure-import-data"></a>Konfigurace importu dat

1. Přidejte do kanálu modul **Importdat.** Tento modul najdete v kategorii **Vstup a Výstup dat** v návrháři.

1. Výběrem modulu otevřete pravé podokno.

1. Vyberte **Zdroj dat**a zvolte typ zdroje dat. Může to být HTTP nebo úložiště dat.

    Pokud zvolíte úložiště dat, můžete vybrat existující úložiště dat, která se už zaregistrovala do pracovního prostoru Azure Machine Learning, nebo vytvořit nové úložiště dat. Pak definujte cestu k importu dat v úložišti dat. Cestu můžete snadno procházet kliknutím na **procházet cestu** ![import-data-path](media/module/import-data-path.png)

1. Vyberte schéma náhledu, které chcete filtrovat sloupce, které chcete zahrnout. Můžete také definovat upřesňující nastavení, jako je oddělovač v možnostech analýzy.

    ![import-data-preview](media/module/import-data.png)

1. Zaškrtávací políčko **Regenerate output**rozhodne, zda má být modul spuštěn za běhu. 

    Ve výchozím nastavení není vybrán, což znamená, že pokud byl modul dříve proveden se stejnými parametry, systém znovu použije výstup z posledního spuštění, aby zkrátil dobu běhu. 

    Pokud je vybrána, systém modul znovu spustí, aby se znovu vygeneroval výstup. Takže tuto možnost vyberte, když jsou podkladová data v úložišti aktualizována, může pomoci získat nejnovější data.


1. Odešlete potrubí.

    Když import dat načte data do návrháře, odvodí datový typ každého sloupce na základě hodnot, které obsahuje, číselné nebo kategorické.

    Pokud je záhlaví k dispozici, záhlaví se používá k pojmenování sloupců výstupní datové sady.

    Pokud v datech nejsou žádná existující záhlaví sloupců, budou nové názvy sloupců generovány pomocí formátu col1, col2,... , coln*.

## <a name="results"></a>Výsledky

Po dokončení importu klepněte na výstupní datovou sadu a vyberte **Visualize, abyste zjistili,** zda byla data úspěšně importována.

Pokud chcete data uložit k opakovanému použití, nikoli importovat novou sadu dat při každém spuštění kanálu, vyberte ikonu **Registrovat datovou sadu** na kartě **Výstupy** v pravém panelu modulu. Zvolte název datové sady. Uložená datová sada zachová data v době uložení, datová sada se při opětovném spuštění kanálu neaktualizuje, a to ani v případě, že se změní datová sada v kanálu. To může být užitečné pro pořizování snímků dat.

Po importu dat může potřebovat další přípravy pro modelování a analýzu:

- Pomocí [funkce Upravit metadata](./edit-metadata.md) můžete měnit názvy sloupců, zpracovávat sloupec jako jiný datový typ nebo označovat, že některé sloupce jsou popisky nebo prvky.

- Pomocí [příkazu Vybrat sloupce v datové sadě](./select-columns-in-dataset.md) vyberte podmnožinu sloupců, které chcete transformovat nebo použít v modelování. Transformované nebo odebrané sloupce lze snadno připojit k původní datové sadě pomocí modulu [Přidat sloupce.](./add-columns.md)  

- Pomocí [oddílu a ukázky](./partition-and-sample.md) rozdělte datovou sadu, proveďte vzorkování nebo získejte horní n řádků.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
