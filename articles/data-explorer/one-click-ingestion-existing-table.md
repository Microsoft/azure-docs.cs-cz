---
title: Použití ingestování jedním kliknutím k ingestování dat do existující tabulky Průzkumníka dat Azure
description: Ingestování (načítání) dat do existující tabulky Průzkumníka dat Azure jednoduše pomocí ingestování jedním kliknutím.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: a7fdab66394c132bcd9134669b841d178c559f28
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546209"
---
# <a name="use-one-click-ingestion-to-ingest-data-to-an-existing-table-in-azure-data-explorer"></a>Použití ingestování jedním kliknutím k ingestování dat do existující tabulky v Průzkumníku dat Azure

Požití jedním kliknutím umožňuje rychlé ingestování dat v JSON, CSV a dalších formátech do tabulky. Pomocí webového uživatelského rozhraní Průzkumníka dat Azure můžete ingestovat data z úložiště, z místního souboru nebo z kontejneru. 

Tento dokument popisuje použití intuitivního průvodce jedním kliknutím k ingestování dat JSON ze souboru do existující tabulky. Potom můžete upravit tabulku a spouštět dotazy pomocí webového uživatelského rozhraní Průzkumníka dat Azure.

Přijetí jedním kliknutím je užitečné zejména při prvním ingestování dat nebo při tom, že schéma dat není pro vás známé. 

Přehled požití jedním kliknutím a seznam požadavků naleznete v tématu [Ingestion jedním kliknutím](ingest-data-one-click.md).
Informace o ingestování dat do nové tabulky v Průzkumníku dat Azure najdete v [tématu ingestování jedním kliknutím do nové tabulky.](one-click-ingestion-new-table.md)

## <a name="ingest-new-data"></a>Ingestování nových dat

1. V levé nabídce webového uživatelského prostředí klepněte pravým tlačítkem myši na *databázi* nebo *tabulku* a vyberte **příkaz Ingestovat nová data (náhled).**

    ![Výběr ingestování jedním kliknutím v uživatelském uživatelském počítači webu](media/one-click-ingestion-existing-table/one-click-ingestion-in-webui.png)   
 
1. V okně **Ingestovat nová data (náhled)** je automaticky vybrána karta **Zdroj.**

1. Pokud pole **Tabulka** není vyplněno automaticky, vyberte z rozevírací nabídky existující název tabulky.
    > [!TIP]
    > Pokud na řádku *tabulky* vyberete **Možnost Ingestovat nová data (náhled),** zobrazí se vybraný název tabulky v **podrobnostech projektu**.

[!INCLUDE [data-explorer-one-click-ingestion-types](../../includes/data-explorer-one-click-ingestion-types.md)]
    
Vyberte **Upravit schéma,** chcete-li zobrazit a upravit konfiguraci sloupce tabulky.

## <a name="edit-the-schema"></a>Úprava schématu

1. Otevře se dialogové okno **Mapovat sloupce** a můžete mapovat sloupce zdrojových dat na cílové sloupce tabulky. 
    * Do polí **Zdrojové sloupce** zadejte názvy sloupců, které chcete mapovat **pomocí cílových sloupců**.
    * Chcete-li mapování odstranit, vyberte ikonu koše.

    ![Okno Sloupce mapy](media/one-click-ingestion-existing-table/map-columns.png)

1. Vyberte **Aktualizovat**.
1. Na kartě **Schéma:**
    1. Vyberte **typ komprese**a pak vyberte **Nekomprimovaný** nebo **GZip**.

        [!INCLUDE [data-explorer-one-click-ingestion-edit-schema](../../includes/data-explorer-one-click-ingestion-edit-schema.md)]
        
    1. Pokud vyberete **JSON**, musíte také vybrat **úrovně JSON**, od 1 do 10. Úrovně ovlivňují zobrazení dat sloupce tabulky.

    ![Vybrat úrovně JSON](media/one-click-ingestion-existing-table/json-levels.png)

    * Pokud vyberete jiný formát než JSON, můžete zaškrtnout políčko **Zahrnout názvy sloupců** a ignorovat řádek záhlaví souboru.
        
    ![Vybrat Zahrnout názvy sloupců](media/one-click-ingestion-existing-table/non-json-format.png)

    > [!Note]
    > Tabulkové formáty můžou ingestovat data sloupců do jenom do jednoho sloupce v tabulce Průzkumníka dat Azure. 

    * Nová mapování jsou nastavena automaticky, ale můžete je změnit tak, aby se používalo existující mapování. 
    * Výběrem **možnosti Mapovat sloupce** otevřete okno **Sloupce mapy.**

## <a name="copy-and-paste-queries"></a>Kopírování a vkládání dotazů

1. Nad podoknem **Editor** vyberte tlačítko **v** pro otevření editoru. V editoru můžete zobrazit a zkopírovat automatické příkazy generované z vašich vstupů. 
1. V tabulce: 
    * Výběrem nových záhlaví sloupců chcete-li přidat **nový sloupec**, **Odstranit sloupec**, **Seřadit vzestupně**nebo **Seřadit sestupně**. V existujících sloupcích je k dispozici pouze řazení dat.

    > [!Note]
    > * Název a datový typ pro existující tabulky nelze aktualizovat.
    > * Příkazy přetažení vrátí pouze změny provedené jeho tokem přiing (nové rozsahy a sloupce). Nic jiného nebude upuštěno.

[![](media/one-click-ingestion-existing-table/edit-view.png "One-click ingestion edit view")](media/one-click-ingestion-existing-table/edit-view.png#lightbox) 

## <a name="start-ingestion"></a>Zahájení požití

Vyberte **Zahájit požití,** chcete-li vytvořit tabulku a mapování a zahájit přijímdat data.

![Zahájení požití](media/one-click-ingestion-existing-table/start-ingestion.png)

## <a name="data-ingestion-completed"></a>Požití dat dokončeno

V okně **dokončení ingestování dat** budou všechny tři kroky označeny zelenými značkami zaškrtnutí, pokud je požití dat úspěšně dokončeno.
 
![Požití dat jedním kliknutím dokončeno](media/one-click-ingestion-existing-table/one-click-data-ingestion-complete.png)

[!INCLUDE [data-explorer-one-click-ingestion-query-data](../../includes/data-explorer-one-click-ingestion-query-data.md)]

## <a name="next-steps"></a>Další kroky

* [Dotazovat se na data ve webovém uživatelském rozhraní Průzkumníka dat Azure](/azure/data-explorer/web-query-data)
* [Psaní dotazů pro Azure Data Explorer pomocí kusto dotazovacího jazyka](/azure/data-explorer/write-queries)
