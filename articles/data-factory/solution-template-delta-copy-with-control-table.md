---
title: Rozdílová kopie z databáze pomocí řídicí tabulky
description: Naučte se používat šablonu řešení pro přírůstkové kopírování nových nebo aktualizovaných řádků z databáze pomocí Azure Data Factory.
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
ms.date: 12/09/2020
ms.openlocfilehash: 3f15ffd6f7ed784cce398d419e013a69c60289d8
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96921797"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>Rozdílová kopie z databáze s řídicí tabulkou

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek popisuje šablonu, která je k dispozici pro přírůstkové načtení nových nebo aktualizovaných řádků z databázové tabulky do Azure pomocí externí tabulky ovládacích prvků, která ukládá hodnotu s vysokým limitem.

Tato šablona vyžaduje, aby schéma zdrojové databáze obsahovalo sloupec časového razítka nebo přírůstek klíče pro identifikaci nových nebo aktualizovaných řádků.

>[!NOTE]
> Pokud ve zdrojové databázi máte sloupec časového razítka k identifikaci nových nebo aktualizovaných řádků, ale nechcete vytvořit tabulku externího ovládacího prvku pro použití rozdílové kopie, můžete místo toho použít [nástroj Azure Data Factory kopírování dat](copy-data-tool.md) a získat tak kanál. Tento nástroj používá čas naplánovaný triggerem jako proměnnou pro čtení nových řádků ze zdrojové databáze.

## <a name="about-this-solution-template"></a>O této šabloně řešení

Tato šablona nejprve načte starou hodnotu meze a porovná ji s aktuální hodnotou meze. Poté zkopíruje pouze změny ze zdrojové databáze na základě porovnání dvou hodnot meze. Nakonec uloží novou hodnotu horní meze do tabulky externích ovládacích prvků pro další rozdílová data načítání.

Šablona obsahuje čtyři aktivity:
- **Vyhledávání** načte starou hodnotu horní meze, která je uložena v externí tabulce ovládacích prvků.
- Jiná aktivita **vyhledávání** načte aktuální hodnotu horní meze ze zdrojové databáze.
- **Kopírovat** kopíruje pouze změny ze zdrojové databáze do cílového úložiště. Dotaz, který identifikuje změny ve zdrojové databázi, je podobný příkazu SELECT * FROM Data_Source_Table, kde TIMESTAMP_Column > "poslední horní mez" a TIMESTAMP_Column <= "Current High-meze" ".
- **SqlServerStoredProcedure** zapisuje aktuální hodnotu horní meze do tabulky externích ovládacích prvků pro rozdílovou kopii v dalším čase.

Šablona definuje následující parametry:
- *Data_Source_Table_Name* je tabulka ve zdrojové databázi, ze které chcete načíst data.
- *Data_Source_WaterMarkColumn* je název sloupce ve zdrojové tabulce, který se používá k identifikaci nových nebo aktualizovaných řádků. Typ tohoto sloupce je obvykle *DateTime*, *int* nebo podobný.
- *Data_Destination_Container* je kořenová cesta k místu, kam se data zkopírují do cílového úložiště.
- *Data_Destination_Directory* je cesta k adresáři v kořenovém adresáři místa, kam se zkopírují data do cílového úložiště.
- *Data_Destination_Table_Name* je místo, kam se data zkopírují do cílového úložiště (platí v případě, že je jako cíl pro data vybraná možnost Azure synapse Analytics).
- *Data_Destination_Folder_Path* je místo, kam se data zkopírují do cílového úložiště (platí v případě, že je jako cíl pro data vybraná možnost "systém souborů" nebo "Azure Data Lake Storage Gen1").
- *Control_Table_Table_Name* je tabulka externích ovládacích prvků, která ukládá hodnotu horní meze.
- *Control_Table_Column_Name* je sloupec v tabulce externího ovládacího prvku, který ukládá hodnotu s horním limitem.

## <a name="how-to-use-this-solution-template"></a>Jak používat tuto šablonu řešení

1. Prozkoumejte zdrojovou tabulku, kterou chcete načíst, a definujte sloupec s vysokou mezí, který se dá použít k identifikaci nových nebo aktualizovaných řádků. Typ tohoto sloupce může být *DateTime*, *int* nebo podobný. Hodnota tohoto sloupce se zvětšuje, když se přidají nové řádky. Z následující ukázkové zdrojové tabulky (data_source_table) můžeme použít sloupec *LastModifytime* jako sloupec s vysokou mezí.

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
    
2. Vytvořte tabulku ovládacího prvku v SQL Server nebo Azure SQL Database k uložení hodnoty horní meze pro načítání rozdílových dat. V následujícím příkladu je název řídicí tabulky tabulkou *vodotisk*. V této tabulce je *WatermarkValue* sloupec, který ukládá hodnotu s horním limitem, a jeho typ je *DateTime*.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Vytvořte uloženou proceduru ve stejné SQL Server nebo Azure SQL Database instanci, kterou jste použili k vytvoření řídicí tabulky. Uložená procedura se používá k zápisu nové hodnoty s vysokou mezí do tabulky externích ovládacích prvků pro další rozdílová data načítání.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. Přejít na **rozdílovou kopii z šablony databáze** . Vytvořte **nové** připojení ke zdrojové databázi, ze které chcete kopírovat data.

    ![Vytvoří nové připojení ke zdrojové tabulce.](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. Vytvořte **nové** připojení k cílovému úložišti dat, do kterého chcete data zkopírovat.

    ![Vytvoří nové připojení k cílové tabulce.](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. Vytvoří **nové** připojení k tabulce externích ovládacích prvků a uložené proceduře, kterou jste vytvořili v krocích 2 a 3.

    ![Vytvoří nové připojení k úložišti dat řídicí tabulky.](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. Vyberte **Použít tuto šablonu**.
    
8. Zobrazí se dostupný kanál, jak je znázorněno v následujícím příkladu:
  
    ![Kontrola kanálu](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. Vyberte **uloženou proceduru**. Jako **název uložené procedury** vyberte **[dbo]. [ update_watermark]**. Vyberte **importovat parametr** a pak vyberte **Přidat dynamický obsah**.  

    ![Nastavení aktivity uložené procedury](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png)  

10. Zapište obsah **\@ {Activity (' LookupCurrentWaterMark '). Output. FIRSTROW. NewWatermarkValue}** a pak vyberte **Dokončit**.  

    ![Zapsat obsah pro parametry uložené procedury](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)       
     
11. Vyberte **ladit**, zadejte **parametry** a pak vyberte **Dokončit**.

    ![Vybrat * * ladit * *](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. Zobrazí se výsledky podobné následujícímu příkladu:

    ![Kontrola výsledku](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. Ve zdrojové tabulce můžete vytvořit nové řádky. Tady je ukázkový jazyk SQL pro vytváření nových řádků:

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```

14. Pokud chcete znovu spustit kanál, vyberte **ladit**, zadejte **parametry** a pak vyberte **Dokončit**.

    Uvidíte, že do cílového umístění se zkopírovaly jenom nové řádky.

15. Volitelné Pokud jako cíl dat vyberete Azure synapse Analytics, musíte taky pro přípravu zadat připojení k úložišti objektů BLOB v Azure, které vyžaduje základ služby Azure synapse Analytics. Šablona vygeneruje cestu kontejneru. Po spuštění kanálu ověřte, jestli se kontejner vytvořil v úložišti objektů BLOB.
    
    ![Konfigurovat základ](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>Další kroky

- [Hromadné kopírování z databáze pomocí řídicí tabulky s Azure Data Factory](solution-template-bulk-copy-with-control-table.md)
- [Kopírování souborů z více kontejnerů pomocí Azure Data Factory](solution-template-copy-files-multiple-containers.md)
