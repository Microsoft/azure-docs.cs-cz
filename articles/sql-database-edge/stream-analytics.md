---
title: Použití balíčků DAC databáze SQL a úloh Stream Analytics s Azure SQL Database Edge | Dokumenty společnosti Microsoft
description: Informace o používání úloh Stream Analytics v SQL Database Edge
keywords: sql database edge, analýza datového proudu, sqlpackage
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 21a8bb6953fd879b17816361f536596571678697
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74384163"
---
# <a name="using-sql-database-dac-packages-and-stream-analytics-jobs-with-sql-database-edge"></a>Použití balíčků DAC databáze SQL a úloh Stream Analytics s sql database edge

Azure SQL Database Edge Preview je optimalizovaný relační databázový stroj zaměřený na nasazení IoT a hraničních zařízení. Je postaven na nejnovějších verzích microsoft SQL Server Database Engine, který poskytuje špičkové funkce výkonu, zabezpečení a zpracování dotazů. Azure SQL Database Edge poskytuje spolu s špičkovými možnostmi správy relačních databází sql serveru vestavěnou funkci streamování pro analýzy v reálném čase a komplexní zpracování událostí.

Azure SQL Database Edge také poskytuje nativní implementaci sqlpackage.exe, která umožňuje nasadit balíček [DAC databáze SQL](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) během nasazení SQL Database Edge.

Azure SQL Database Edge zveřejňuje dva `module twin's desired properties` volitelné parametry prostřednictvím možnosti modulu IoT Edge:

```json
{
    "properties.desired":
    {
        "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
        "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
    }
}
```

|Pole | Popis |
|------|-------------|
| SqlPackage | Identifikátor URI úložiště objektů blob Azure pro soubor *.zip, který obsahuje balíček DAC databáze SQL.
| ASAJobInfo | Identifikátor URI úložiště objektů blob Azure pro úlohu ASA Edge. Další informace naleznete [v tématu Publikování úlohy ASA Edge pro sql database edge](/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge).

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>Použití balíčků DAC databáze SQL s okrajem SQL Database Edge

Chcete-li použít balíček DAC databáze SQL (*.dacpac) s sql database edge, postupujte takto:

1. Vytvořte nebo extrahujte balíček DAC databáze SQL. Informace o tom, jak generovat balíček DAC pro existující databázi serveru SQL Server, naleznete [v tématu Extrahování dac z databáze.](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/)

2. Zapněte *.dacpac a nahrajte ho do účtu úložiště objektů Blob Azure. Další informace o nahrávání souborů do úložiště objektů Blob Azure najdete [v tématu Nahrávání, stahování a seznam objektů BLOB s portálem Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Vygenerujte sdílený přístupový podpis pro soubor ZIP pomocí portálu Azure. Další informace naleznete [v tématu Delegování přístupu se sdílenými přístupovými podpisy (SAS).](../storage/common/storage-sas-overview.md)

4. Aktualizujte konfiguraci modulu SQL Database Edge tak, aby zahrnovala identifikátor URI pro sdílený přístup pro balíček DAC. Chcete-li aktualizovat modul SQL Database Edge, postupujte takto:

    1. Na webu Azure Portal přejděte na nasazení služby IoT Hub.

    2. V levém podokně vyberte **IoT Edge**.

    3. Na stránce **IoT Edge** najděte a vyberte okraj IoT, kde se nasazuje modul SQL Database Edge.

    4. Na stránce **Zařízení IoT Edge** vyberte **Nastavit modul**.

    5. Na stránce **Nastavit moduly** vyberte **Konfigurovat** podle modulu SQL Database Edge.

    6. V podokně **Vlastní moduly IoT Edge** vyberte **Nastavit požadované vlastnosti dvojčete modulu**. Aktualizujte požadované vlastnosti zahrnout `SQLPackage` URI pro možnost, jak je znázorněno v následujícím příkladu.

        > [!NOTE]
        > Identifikátor URI SAS v následujícím obrázku JSON je pouze příkladem. Nahraďte identifikátor URI skutečným identifikátorem URI z vašeho nasazení.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. Vyberte **Uložit**.

    8. Na stránce **Nastavit moduly** vyberte **Další**.

    9. Na stránce **Nastavit moduly** vyberte **Další** a potom **Odeslat**.

5. Po aktualizaci modulu je soubor balíčku DAC stažen, rozbalen a nasazen proti instanci SQL Database Edge.

## <a name="using-streaming-jobs-with-sql-database-edge"></a>Použití úloh streamování s sql database edge

Azure SQL Database Edge má nativní implementaci modulu runtime analýzy datového proudu. Tato implementace umožňuje vytvořit hraniční úlohu Azure Stream Analytics a nasadit tuto úlohu jako úlohu streamování SQL Database Edge. Chcete-li vytvořit hraniční úlohu Služby Stream Analytics, proveďte tyto kroky:

1. Přejděte na portál Azure pomocí [adresy URL](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true)náhledu . Tato adresa URL náhledu umožňuje nakonfigurovat výstup databáze SQL pro hraniční úlohu Stream Analytics.

2. Vytvořte novou **Azure Stream Analytics na ioT edge** úlohy. Zvolte hostitelské prostředí, které cílí **na Edge**.

3. Definujte vstup a výstup pro úlohu Azure Stream Analytics. Každý výstup SQL, který zde nastavíte, je vázán na jednu tabulku v databázi. Pokud potřebujete streamovat data do více tabulek, budete muset vytvořit více výstupů databáze SQL. Výstupy SQL můžete nakonfigurovat tak, aby ukazovaly na různé databáze.

    **Vstup**. Zvolte EdgeHub jako vstup pro hraniční úlohu a zadejte informace o prostředku.

    **Výstup**. Vyberte databázi SQL jako výstup. Vyberte **ručně poskytněte možnost Poskytnout nastavení databáze SQL**. Zadejte podrobnosti konfigurace databáze a tabulky.

    |Pole      | Popis |
    |---------------|-------------|
    |Alias pro výstup | Název výstupního aliasu.|
    |Databáze | Název databáze SQL. Musí se jedná o platný název databáze, která existuje v instanci SQL Database Edge.|
    |Název serveru | Název (nebo IP adresa) a podrobnosti o čísle portu pro instanci SQL. Pro nasazení SQL Database Edge můžete pro název serveru použít **tcp:.,1433.**|
    |Uživatelské jméno | Přihlašovací účet SQL, který má čtečku dat a zápis dat přístup k databázi, kterou jste zadali dříve.|
    |Heslo | Heslo pro přihlašovací účet SQL, který jste zadali dříve.|
    |Table | Název tabulky, která bude výstupem pro úlohu streamování.|
    |Dědit dělení| Umožňuje dědění schéma dělení předchozí krok dotazu nebo vstup. Pokud je tato možnost povolena, můžete očekávat lepší propustnost při zápisu do tabulky založené na disku a mají plně paralelní topologie pro vaši úlohu.|
    |Velikost dávky| Maximální počet záznamů odeslaných s každou transakcí hromadného vložení.|

    Zde je ukázková konfigurace vstupu a výstupu:

    ```txt
        Input:
            Input from EdgeHub
            Input alias: input
            Event serialization format: JSON
            Encoding: UTF-8
            Event compression type: None

        Output:
            Output alias: output
            Database:  MeasurementsDB
            Server name: tcp:.,1433
            Username: sa
            Password: <Password>
            Table: TemperatureMeasurements
            Inherit Partitioning: Merge all input partitions into a single writer
            Max batch count: 10000
    ```

    > [!NOTE]
    > Další informace o výstupním adaptéru SQL pro Azure Stream Analytics najdete v článku [výstup Azure Stream Analytics do Azure SQL Database](../stream-analytics/stream-analytics-sql-output-perf.md).

4. Definujte dotaz úlohy ASA pro hraniční úlohu. Tento dotaz by měl používat definované vstupní a výstupní aliasy jako vstupní a výstupní názvy v dotazu. Další informace naleznete v [tématu Odkaz na dotaz ový jazyk Služby Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

5. Nastavte nastavení účtu úložiště pro hraniční úlohu. Účet úložiště se používá jako cíl publikování pro hraniční úlohu.

6. V části **Konfigurovat**vyberte **Publikovat**a pak vyberte tlačítko **Publikovat.** Uložte identifikátor URI SAS pro použití s modulem SQL Database Edge.

### <a name="deploy-the-stream-analytics-edge-job-to-sql-database-edge"></a>Nasazení hraniční úlohy Stream Analytics do sql database edge

Chcete-li nasadit úlohu streamování do modulu SQL Database Edge, aktualizujte konfiguraci modulu SQL Database Edge tak, aby zahrnovala identifikátor URI SAS pro úlohu streamování z předchozího kroku. Aktualizace modulu SQL Database Edge:

1. Na webu Azure Portal přejděte na nasazení služby IoT Hub.

2. V levém podokně vyberte **IoT Edge**.

3. Na stránce **IoT Edge** najděte a vyberte okraj IoT, kde se nasazuje modul SQL Database Edge.

4. Na stránce **Zařízení IoT Edge** vyberte **Nastavit modul**.

5. Na stránce **Nastavit moduly** vyberte **Konfigurovat** podle modulu SQL Database Edge.

6. V podokně **Vlastní moduly IoT Edge** vyberte **Nastavit požadované vlastnosti dvojčete modulu**. Aktualizujte požadované vlastnosti zahrnout `ASAJobInfo` URI pro možnost, jak je znázorněno v následujícím příkladu.

    > [!NOTE]
    > Identifikátor URI SAS v následujícím obrázku JSON je pouze příkladem. Nahraďte identifikátor URI skutečným identifikátorem URI z vašeho nasazení.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. Vyberte **Uložit**.

8. Na stránce **Nastavit moduly** vyberte **Další**.

9. Na stránce **Nastavit moduly** vyberte **Další** a potom **Odeslat**.

10. Po aktualizaci modulu je soubor úlohy analýzy datového proudu stažen, rozbalen a nasazen proti instanci SQL Database Edge.

## <a name="next-steps"></a>Další kroky

- Podrobnosti o cenách a dostupnosti najdete v [tématu Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).
- Požádejte o povolení Azure SQL Database Edge pro vaše předplatné.
- Další informace najdete v [tématu Nasazení sql database edge prostřednictvím portálu Azure](deploy-portal.md).
