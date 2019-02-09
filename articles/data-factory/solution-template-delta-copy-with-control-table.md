---
title: Rozdílové kopírování z databáze s tabulkou ovládacího prvku s Azure Data Factory | Dokumentace Microsoftu
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
ms.openlocfilehash: 23e1255013cd5e52166fe0e59a8931dd9ecd81a0
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967167"
---
# <a name="delta-copy-from-database-with-control-table"></a>Rozdílové kopírování databází pomocí ovládacího prvku tabulka

Pokud chcete přírůstkové načtení změn (nové nebo aktualizované řádky) pouze z tabulky v databázi do Azure s tabulkou externího ovládacího prvku uložení hodnoty meze.  K dispozici šablona je určena pro tento případ. 

Tato šablona vyžaduje že schématu zdrojové databáze musí obsahující časové razítko sloupec nebo zvýšení klíč k identifikaci nových nebo aktualizovaných řádků.

Pokud máte sloupec časového razítka ve zdrojové databázi k identifikaci nových nebo aktualizovaných řádků, ale nechcete, aby k vytvoření externího ovládacího prvku tabulky povolit rozdílové kopírování, můžete použít nástroj pro kopírování dat zobrazíte kanál, který používá aktivační událost naplánované době jako proměnné pro čtení nové řádky pouze ze zdrojové databáze.

## <a name="about-this-solution-template"></a>O tato šablona řešení

Tato šablona vždy nejprve načte staré hodnoty meze a porovná ho s aktuální hodnoty meze. Potom zkopíruje pouze změny ze zdrojové databáze na základě porovnání mezi 2 hodnoty meze.  Jakmile budete hotovi, uloží novou hodnotu meze do externího ovládacího prvku tabulky pro další čas načítání rozdílová data.

Šablona obsahuje čtyři aktivity:
-   A **vyhledávání** aktivity k načtení původní hodnoty meze uložené v tabulce externího ovládacího prvku.
-   A **vyhledávání** aktivitu k získání aktuální hodnoty meze ze zdrojové databáze.
-   A **kopírování** aktivity kopírování změn pouze ze zdrojové databáze do cílového úložiště. Dotaz používá k identifikaci změny ze zdrojové databáze v aktivitě kopírování je podobné jako "vybrat * z Data_Source_Table kde TIMESTAMP_Column >"poslední meze"a TIMESTAMP_Column < ="aktuální meze"'.
-   A **SqlServerStoredProcedure** aktivity zapsat aktuální hodnotu meze do externího ovládacího prvku tabulky pro rozdílové kopírování při příštím.

Šablona definuje pěti parametrů:
-   Parametr *Data_Source_Table_Name* je název tabulky ze zdrojové databáze, ve které chcete načíst data.
-   Parametr *Data_Source_WaterMarkColumn* název sloupce ve zdrojové tabulce, který slouží k identifikaci nových nebo aktualizovaných řádků. Za normálních okolností typ tohoto sloupce může být datum a čas nebo INT atd.
-   Parametr *Data_Destination_Folder_Path* nebo *Data_Destination_Table_Name* je místem, kde se data kopírují do cílového úložiště.
-   Parametr *Control_Table_Table_Name* je název tabulky externího ovládacího prvku pro ukládání hodnoty horní meze.
-   Parametr *Control_Table_Column_Name* název sloupce v tabulce externího ovládacího prvku pro ukládání hodnoty horní meze.

## <a name="how-to-use-this-solution-template"></a>Jak použít tuto šablonu řešení

1. Prozkoumat zdrojová tabulka, kterou chcete načíst a definovat meze sloupec, který můžete použít k rozlišení nových nebo aktualizovaných řádků. Za normálních okolností typ tohoto sloupce může být datum a čas nebo INT atd. a jeho data udržování zvyšuje při přidání nových řádků.  Z ukázkové tabulky zdroje (název tabulky: data_source_table) níže, můžete použít sloupec *LastModifytime* jako sloupec meze.

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
    
2. Vytvoření ovládacího prvku tabulky v SQL server nebo SQL Azure pro ukládání hodnoty horní meze pro rozdílové načítání dat. V následujícím příkladu můžete zobrazit název ovládacího prvku tabulky je *watermarktable*. V něm, je název sloupce pro ukládání hodnoty horní meze *WatermarkValue* a jejím typem je *data a času*.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Vytvořit uloženou proceduru na stejném serveru SQL nebo SQL Azure použité k vytvoření ovládacího prvku tabulky. Uložená procedura se používá k zápisu do externího ovládacího prvku tabulky pro další čas načítání rozdílových dat nová hodnota meze.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. Přejděte do šablony **rozdílové kopírování z databáze**a vytvořit **nové připojení** k kde kopírování dat ze zdrojové databáze.

    ![Vytvoření nového připojení ke zdrojové tabulky](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. Vytvoření **nové připojení** na vaše cílové úložiště dat, ve kterém kopírování dat do.

    ![Vytvořit nové připojení do cílové tabulky](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. Vytvoření **nové připojení** externího ovládacího prvku tabulky a uložené procedury.  Jde o připojení k databázi, ve kterém jste vytvořili tabulku ovládacího prvku a uložené procedury v kroku #2 a #3.

    ![Vytvořit nové připojení k úložišti ovládacího prvku tabulky dat](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. Klikněte na tlačítko **pomocí této šablony**.

     ![Použít tuto šablonu](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable7.png)
    
8. K dispozici v panelu kanálu uvidíte, jak je znázorněno v následujícím příkladu:

     ![Kontrola kanálu](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. Klikněte na aktivitu uložená procedura, vyberte **název uložené procedury**, klikněte na tlačítko **parametr importu** a klikněte na tlačítko **Přidat dynamický obsah**.  

     ![Aktivita uložená procedura Set](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png) 

10. Zapsat obsah **@{activity('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}** a klikněte na tlačítko **Dokončit**.  

     ![Zapsat obsah pro parametr pro uloženou proceduru](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)      
     
11. Klikněte na tlačítko **ladění**, vstupní parametry a klikněte na tlačítko **Dokončit**.

    ![Klikněte na tlačítko ladění](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. K dispozici na panelu výsledků uvidíte, jak je znázorněno v následujícím příkladu:

    ![Zkontrolujte výsledky](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. Můžete vytvořit nové řádky ve zdrojové tabulce.  Ukázka sql k vytvoření nových řádků může být jako následující:

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```
13. Opětovné spuštění kanálu kliknutím **ladění**, vstupní parametry a klikněte na tlačítko **Dokončit**.

    ![Klikněte na tlačítko ladění](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

14. Zobrazí se pouze nové řádky, které byly zkopírovány do cíle.

15. (Volitelné) Pokud vyberete jako cíl dat SQL Data Warehouse, musíte také vstupní připojení služby Azure blob storage jako pracovní, která vyžaduje SQL Data Warehouse Polybase.  Ujistěte se prosím, že již vytvořeno kontejneru v úložišti objektů blob.  
    
    ![Konfigurace funkce Polybase](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>Další postup

- [Úvod do Azure Data Factory](introduction.md)