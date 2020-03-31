---
title: Rozdílová kopie z databáze pomocí řídicí tabulky
description: Zjistěte, jak pomocí šablony řešení postupně kopírovat nové nebo aktualizované řádky jenom z databáze pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/24/2018
ms.openlocfilehash: 3c077e2c04cae94d2e1a2a84ccd7d09c7a0829b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75439717"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>Rozdílová kopie z databáze s řídicí tabulkou

Tento článek popisuje šablonu, která je k dispozici pro postupné načítání nových nebo aktualizovaných řádků z databázové tabulky do Azure pomocí tabulky externího ovládacího prvku, která ukládá hodnotu vysokého vodoznaku.

Tato šablona vyžaduje, aby schéma zdrojové databáze obsahovalo sloupec časového razítka nebo obnovovací klíč k identifikaci nových nebo aktualizovaných řádků.

>[!NOTE]
> Pokud máte sloupec časového razítka ve zdrojové databázi k identifikaci nových nebo aktualizovaných řádků, ale nechcete vytvořit tabulku externího ovládacího prvku, která se použije pro rozdílovou kopii, můžete místo toho použít [nástroj Azure Data Factory Copy Data](copy-data-tool.md) k získání kanálu. Tento nástroj používá aktivační událost naplánovaný čas jako proměnnou ke čtení nových řádků ze zdrojové databáze.

## <a name="about-this-solution-template"></a>O této šabloně řešení

Tato šablona nejprve načte starou hodnotu vodoznaku a porovná ji s aktuální hodnotou vodoznaku. Poté zkopíruje pouze změny ze zdrojové databáze na základě porovnání mezi dvěma hodnotami vodoznaku. Nakonec ukládá novou hodnotu vysokého vodoznaku do tabulky externího ovládacího prvku pro načítání delta dat příště.

Šablona obsahuje čtyři aktivity:
- **Vyhledávání načte** starou hodnotu vysokého vodoznaku, která je uložena v tabulce externího ovládacího prvku.
- Další **vyhledávací** aktivita načte aktuální hodnotu vysokého vodoznaku ze zdrojové databáze.
- **Zkopírujte** pouze kopie změn ze zdrojové databáze do cílového úložiště. Dotaz, který identifikuje změny ve zdrojové databázi je podobný "SELECT * FROM Data_Source_Table KDE TIMESTAMP_Column > "poslední high-vodoznak" a TIMESTAMP_Column <= "aktuální high-vodoznak".
- **SqlServerStoredProcedure** zapíše aktuální hodnotu vysokého vodoznaku do tabulky externího ovládacího prvku pro další rozdílovou kopii.

Šablona definuje následující parametry:
- *Data_Source_Table_Name* je tabulka ve zdrojové databázi, ze které chcete načíst data.
- *Data_Source_WaterMarkColumn* je název sloupce ve zdrojové tabulce, který se používá k identifikaci nových nebo aktualizovaných řádků. Typ tohoto sloupce je obvykle *datetime*, *INT*nebo podobné.
- *Data_Destination_Container* je kořenová cesta místa, kam jsou data zkopírována v cílovém úložišti.
- *Data_Destination_Directory* je cesta k adresáři pod kořenem místa, kam jsou data zkopírována v cílovém úložišti.
- *Data_Destination_Table_Name* je místo, kde se data zkopírují do cílového úložiště (použitelné při "Azure Synapse Analytics (dříve SQL DW)" je vybrán jako cíl dat).
- *Data_Destination_Folder_Path* je místo, kam se data zkopírují v cílovém úložišti (platí, když je jako cíl dat vybrán "Systém souborů" nebo "Azure Data Lake Storage Gen1").
- *Control_Table_Table_Name* je tabulka externího ovládacího prvku, která ukládá hodnotu vysokého vodoznaku.
- *Control_Table_Column_Name* je sloupec v tabulce externího ovládacího prvku, ve který je uložena hodnota vysokého vodoznaku.

## <a name="how-to-use-this-solution-template"></a>Použití této šablony řešení

1. Prozkoumejte zdrojovou tabulku, kterou chcete načíst, a definujte sloupec s vysokým vodoznakem, který lze použít k identifikaci nových nebo aktualizovaných řádků. Typ tohoto sloupce může být *datetime*, *INT*nebo podobné. Hodnota tohoto sloupce se zvyšuje při přidávání nových řádků. Z následující zdrojové tabulky vzorku (data_source_table) můžeme jako sloupec s vysokým vodoznakem použít sloupec *LastModifytime.*

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
    
2. Vytvořte tabulku ovládacích prvku v SQL Serveru nebo Azure SQL Database pro uložení hodnoty vysokého vodoznaku pro načítání rozdílových dat. V následujícím příkladu je název řídicí tabulky *vodoznak .* V této tabulce *je Hodnota vodoznaku* sloupec, ve který je uložena hodnota vysokého vodoznaku, a její typ je *datetime*.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Vytvořte uloženou proceduru ve stejné instanci SQL Server nebo Azure SQL Database, kterou jste použili k vytvoření tabulky ovládacích prvku. Uložená procedura se používá k zápisu nové hodnoty vysokého vodoznaku do tabulky externího ovládacího prvku pro další načítání rozdílových dat.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. Přejděte do **rozdílové kopie ze** šablony Databáze. Vytvořte **nové** připojení ke zdrojové databázi, ze které chcete kopírovat data.

    ![Vytvoření nového připojení ke zdrojové tabulce](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. Vytvořte **nové** připojení k cílovému úložišti dat, do kterého chcete data zkopírovat.

    ![Vytvoření nového připojení k cílové tabulce](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. Vytvořte **nové** připojení k tabulce externího ovládacího prvku a uloženou proceduru, kterou jste vytvořili v krocích 2 a 3.

    ![Vytvoření nového připojení k úložišti dat tabulky ovládacích prvkem](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. Vyberte **Použít tuto šablonu**.
    
8. Zobrazí se dostupný kanál, jak je znázorněno v následujícím příkladu:
  
    ![Kontrola kanálu](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. Vyberte **uloženou proceduru**. V **části Název uložené procedury**zvolte **[dbo].[ update_watermark]**. Vyberte **Importovat parametr**a pak **vyberte Přidat dynamický obsah**.  

    ![Nastavení aktivity uložené procedury](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png)  

10. Napište obsah ** \@{activity('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}** a pak vyberte **Dokončit**.  

    ![Napište obsah parametrů uložené procedury](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)       
     
11. Vyberte **Ladit**, zadejte **parametry**a pak vyberte **Dokončit**.

    ![Vybrat **Ladění**](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. Zobrazí se výsledky podobné následujícímu příkladu:

    ![Zkontrolujte výsledek](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. Ve zdrojové tabulce můžete vytvořit nové řádky. Zde je ukázkový jazyk SQL pro vytvoření nových řádků:

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```

14. Chcete-li kanál spustit znovu, vyberte **možnost Ladění**, zadejte **parametry**a pak vyberte **možnost Dokončit**.

    Uvidíte, že do cíle byly zkopírovány pouze nové řádky.

15. (Nepovinné:) Pokud jako cíl dat vyberete Azure Synapse Analytics (dříve SQL DW), musíte také poskytnout připojení k úložišti objektů blob Azure pro pracovní, což vyžaduje SQL Data Warehouse Polybase. Šablona pro vás vygeneruje cestu kontejneru. Po spuštění kanálu zkontrolujte, jestli byl kontejner vytvořen v úložišti objektů Blob.
    
    ![Konfigurovat polybázi](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>Další kroky

- [Hromadná kopírování z databáze pomocí ovládací tabulky s Azure Data Factory](solution-template-bulk-copy-with-control-table.md)
- [Kopírování souborů z více kontejnerů pomocí Azure Data Factory](solution-template-copy-files-multiple-containers.md)
