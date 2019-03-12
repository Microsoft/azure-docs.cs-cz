---
title: Rozdílové kopírování databází pomocí ovládacího prvku tabulky s Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak přírůstkově kopírovat nové nebo aktualizované řádky pouze z databáze pomocí služby Azure Data Factory pomocí šablony řešení.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/24/2018
ms.openlocfilehash: c32592ce539eeb2dec71792e4a6eb31e7d904eff
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2019
ms.locfileid: "57771153"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>Rozdílové kopírování z databáze s tabulkou ovládacího prvku

Tento článek popisuje šablony, která je k dispozici pro přírůstkové načtení nové nebo aktualizované řádky z tabulky databáze do Azure pomocí externího ovládacího prvku tabulky, který slouží k uložení hodnoty meze.

Tato šablona vyžaduje, že schématu zdrojové databáze obsahuje časové razítko sloupec nebo zvýšení klíč k identifikaci nových nebo aktualizovaných řádků.

>[!NOTE]
> Pokud máte sloupec časového razítka ve zdrojové databázi k identifikaci nových nebo aktualizovaných řádků, ale nechcete, aby k vytvoření externího ovládacího prvku tabulky pro rozdílové kopírování, můžete místo toho použít [nástroj pro kopírování dat Azure Data Factory](copy-data-tool.md) zobrazíte kanálu. Tento nástroj používá aktivační událost naplánované době jako proměnnou ke čtení ze zdrojové databáze nové řádky.

## <a name="about-this-solution-template"></a>O tato šablona řešení

Tato šablona nejdřív načte staré hodnoty meze a porovná ho s aktuální hodnoty meze. Potom zkopíruje pouze změny ze zdrojové databáze založené na porovnání mezi službou dvěma hodnotami mezí. Nakonec uloží novou hodnotu meze do externího ovládacího prvku tabulky pro další čas načítání rozdílová data.

Šablona obsahuje čtyři aktivity:
- **Vyhledávání** načte stará hodnota meze, která je uložena v tabulce externího ovládacího prvku.
- Jiné **vyhledávání** aktivita získá aktuální hodnotu meze ze zdrojové databáze.
- **Kopírování** zkopíruje pouze změny ze zdrojové databáze do cílového úložiště. Dotaz, který identifikuje změny ve zdrojové databázi je podobný "vybrat * z Data_Source_Table kde TIMESTAMP_Column >"poslední meze"a TIMESTAMP_Column < ="aktuální meze"'.
- **SqlServerStoredProcedure** zapíše aktuální hodnotu meze do externího ovládacího prvku tabulky pro rozdílové kopírování při příštím.

Šablona definuje pěti parametrů:
- *Data_Source_Table_Name* je tabulka ve zdrojové databázi, kterou chcete načíst data.
- *Data_Source_WaterMarkColumn* je název sloupce ve zdrojové tabulce, která se používá k identifikaci nových nebo aktualizaci řádků. Typ tohoto sloupce je obvykle *data a času*, *INT*, nebo podobného.
- *Data_Destination_Folder_Path* nebo *Data_Destination_Table_Name* je místem, kde se data zkopírují do za cílového úložiště.
- *Control_Table_Table_Name* je tabulka externího ovládacího prvku, který slouží k uložení hodnoty meze.
- *Control_Table_Column_Name* je sloupec v tabulce externího ovládacího prvku, který slouží k uložení hodnoty meze.

## <a name="how-to-use-this-solution-template"></a>Jak použít tuto šablonu řešení

1. Prozkoumejte zdroje tabulky, který chcete načíst a definovat meze sloupec, který slouží k identifikaci nových nebo aktualizovaných řádků. Typ tohoto sloupce může být *data a času*, *INT*, nebo podobného. V tomto sloupci Hodnota se zvyšuje při přidání nových řádků. Z následující ukázka zdrojovou tabulku (data_source_table), můžeme použít *LastModifytime* sloupec jako sloupec meze.

    ```sql
            PersonID    Name    LastModifytime
            1   aaaa    2017-09-01 00:56:00.000
            2   bbbb    2017-09-02 05:23:00.000
            3   cccc    2017-09-03 02:36:00.000
            4   dddd    2017-09-04 03:21:00.000
            5   eeee    2017-09-05 08:06:00.000
            6   fffffff 2017-09-06 02:23:00.000
            7   gggg    2017-09-07 09:01:00.000
            8   hhhh    2017-09-08 09:01:00.000
            9   iiiiiiiii   2017-09-09 09:01:00.000
    ```
    
2. V systému SQL Server nebo databázi SQL Azure pro ukládání hodnoty horní meze pro rozdílové načítání dat vytvořte tabulku ovládacího prvku. V následujícím příkladu je název ovládacího prvku tabulky *watermarktable*. V této tabulce *WatermarkValue* je sloupec, který slouží k uložení hodnoty meze, a její typ je *data a času*.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Vytvořte uloženou proceduru ve stejné instanci systému SQL Server nebo databázi SQL Azure, který jste použili k vytvoření tabulky ovládacího prvku. Uložená procedura se používá k zápisu novou hodnotu meze do externího ovládacího prvku tabulky pro další čas načítání rozdílová data.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. Přejděte **rozdílové kopírování z databáze** šablony. Vytvoření **nový** připojení, který chcete kopírovat data ze zdrojové databáze.

    ![Vytvoření nového připojení ke zdrojové tabulky](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. Vytvoření **nový** připojení do cílového úložiště dat, který chcete zkopírovat data.

    ![Vytvořit nové připojení do cílové tabulky](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. Vytvoření **nový** připojení externího ovládacího prvku tabulky a uložené procedury, kterou jste vytvořili v kroku 2 a 3.

    ![Vytvořit nové připojení k úložišti ovládacího prvku tabulky dat](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. Vyberte **pomocí této šablony**.

     ![Použít tuto šablonu](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable7.png)
    
8. Zobrazí se dostupné kanál, jak je znázorněno v následujícím příkladu:

     ![Zkontrolujte kanálu](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. Vyberte **uloženou proceduru**. Pro **název uložené procedury**, zvolte **[update_watermark]**. Vyberte **importovat parametr**a pak vyberte **Přidat dynamický obsah**.  

     ![Nastavte aktivitu uložené procedury](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png) 

10. Zapsat obsah  **\@{activity('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}** a pak vyberte **Dokončit**.  

     ![Zapsat obsah pro parametry uložené procedury](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)      
     
11. Vyberte **ladění**, zadejte **parametry**a pak vyberte **Dokončit**.

    ![Vyberte ** ladění **](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. Výsledek podobný následujícím příkladu se zobrazí:

    ![Zkontrolujte výsledky](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. Můžete vytvořit nové řádky ve zdrojové tabulce. Tady je ukázka jazyka SQL k vytvoření nových řádků:

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```
14. Opětovné spuštění kanálu, vyberte **ladění**, zadejte **parametry**a pak vyberte **Dokončit**.

    ![Vyberte ** ladění **](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

    Uvidíte, že byly zkopírovány pouze nové řádky do cíle.

15. (Volitelné:) Pokud vyberete jako cíl dat SQL Data Warehouse, musíte také zadat připojení k úložišti objektů Blob v Azure pro přípravu, kterou vyžaduje SQL Data Warehouse Polybase. Ujistěte se, že kontejner již byl vytvořen v úložišti objektů Blob.
    
    ![Konfigurace funkce Polybase](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>Další postup

- [Hromadné kopírování databází pomocí ovládacího prvku tabulky s Azure Data Factory](solution-template-bulk-copy-with-control-table.md)
- [Kopírování souborů z několika kontejnerů pomocí Azure Data Factory](solution-template-copy-files-multiple-containers.md)
