---
title: Použití SQL Databasech balíčků DAC a úloh Stream Analytics s Azure SQL Database Edge | Microsoft Docs
description: Informace o použití Stream Analytics úloh v SQL Database Edge
keywords: SQL Database Edge, Stream Analytics, SqlPackage
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 21a8bb6953fd879b17816361f536596571678697
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384163"
---
# <a name="using-sql-database-dac-packages-and-stream-analytics-jobs-with-sql-database-edge"></a>Použití SQL Databasech balíčků DAC a úloh Stream Analytics s SQL Database Edge

Azure SQL Database Edge Preview je optimalizovaný relační databázový stroj pro nasazení IoT a Edge. Je postaven na nejnovějších verzích Microsoft SQL Server databázového stroje, který poskytuje špičkové možnosti pro výkon, zabezpečení a zpracování dotazů. Spolu s špičkovými možnostmi správy relačních databází SQL Server nabízí Azure SQL Database Edge vestavěnou funkci streamování pro analýzy v reálném čase a složité zpracování událostí.

Azure SQL Database Edge taky poskytuje nativní implementaci SqlPackage. exe, která umožňuje nasadit [SQL Database DAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) Package během nasazování SQL Database Edge.

Azure SQL Database Edge zpřístupňuje dva volitelné parametry pomocí možnosti `module twin's desired properties` modulu IoT Edge:

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
| SqlPackage | Identifikátor URI úložiště objektů BLOB v Azure pro soubor *. zip, který obsahuje SQL Database balíček DAC.
| ASAJobInfo | Identifikátor URI úložiště objektů BLOB v Azure pro hraniční úlohu ASA Další informace najdete v tématu [publikování hraničních úloh ASA pro SQL Database Edge](/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge).

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>Použití SQL Databasech balíčků DAC s SQL Database Edge

Pokud chcete použít SQL Database balíček DAC (*. DACPAC) s SQL Database Edge, proveďte tyto kroky:

1. Vytvoření nebo extrakce SQL Database balíčku DAC Informace o tom, jak vygenerovat balíček DAC pro existující SQL Server databázi, najdete v tématu [extrakce DAC z databáze](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) .

2. Zip *. DACPAC a nahrajte ho do účtu služby Azure Blob Storage. Další informace o nahrávání souborů do úložiště objektů BLOB v Azure najdete v tématu [nahrání, stažení a výpis objektů BLOB pomocí Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Vygenerujte sdílený přístupový podpis pro soubor ZIP pomocí Azure Portal. Další informace najdete v tématu [delegování přístupu pomocí podpisů sdíleného přístupu (SAS)](../storage/common/storage-sas-overview.md).

4. Aktualizujte konfiguraci modulu SQL Database Edge tak, aby zahrnovala identifikátor URI sdíleného přístupu pro balíček DAC. Pokud chcete aktualizovat modul SQL Database Edge, proveďte tyto kroky:

    1. V Azure Portal pokračujte na nasazení IoT Hub.

    2. V levém podokně vyberte **IoT Edge**.

    3. Na stránce **IoT Edge** vyhledejte a vyberte IoT Edge, kde je nasazený modul SQL Database Edge.

    4. Na stránce zařízení **IoT Edge zařízení** vyberte **nastavit modul**.

    5. Na stránce **nastavit moduly** vyberte **konfigurovat** pro modul SQL Database Edge.

    6. V podokně **IoT Edge vlastní moduly** vyberte **nastavit požadované vlastnosti nevlákenných**modulů. Aktualizujte požadované vlastnosti tak, aby zahrnovaly identifikátor URI pro možnost `SQLPackage`, jak je znázorněno v následujícím příkladu.

        > [!NOTE]
        > Identifikátor URI SAS v následujícím formátu JSON je pouze příklad. Nahraďte identifikátor URI skutečným identifikátorem URI z vašeho nasazení.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. Vyberte **Uložit**.

    8. Na stránce **nastavit moduly** vyberte **Další**.

    9. Na stránce **nastavit moduly** vyberte **Další** a pak **Odeslat**.

5. Po aktualizaci modulu se soubor balíčku DAC stáhne, rozbalí a nasadí do instance SQL Database Edge.

## <a name="using-streaming-jobs-with-sql-database-edge"></a>Použití úloh streamování s SQL Database Edge

Azure SQL Database Edge má nativní implementaci modulu Stream Analytics runtime. Tato implementace umožňuje vytvořit úlohu Azure Stream Analytics Edge a nasazovat tuto úlohu jako úlohu streamování SQL Database Edge. Pokud chcete vytvořit úlohu Stream Analytics Edge, proveďte tyto kroky:

1. Pomocí [adresy URL](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true)náhledu přejdete na Azure Portal. Tato adresa URL Preview umožňuje nakonfigurovat SQL Database výstup pro úlohu Stream Analytics Edge.

2. Vytvoří novou **Azure Stream Analytics na IoT Edge** úlohu. Vyberte hostující prostředí, které cílí na **Edge**.

3. Definujte vstup a výstup pro úlohu Azure Stream Analytics. Každý výstup SQL, který tady nastavíte, je vázaný na jednu tabulku v databázi. Pokud potřebujete streamovat data do více tabulek, budete muset vytvořit několik výstupů SQL Database. Můžete nakonfigurovat výstupy SQL tak, aby odkazovaly na různé databáze.

    **Vstup**. Jako vstup pro úlohu Edge vyberte EdgeHub a zadejte informace o prostředku.

    **Výstup**. Jako výstup vyberte SQL Database. Vyberte **zadat SQL Database nastavení ručně**. Zadejte podrobnosti o konfiguraci databáze a tabulky.

    |Pole      | Popis |
    |---------------|-------------|
    |Alias pro výstup | Název aliasu výstupu.|
    |Databáze | Název databáze SQL. Musí se jednat o platný název databáze, která existuje v instanci SQL Database Edge.|
    |Název serveru | Název (nebo IP adresa) a podrobnosti o číslech portů pro instanci SQL. Pro nasazení SQL Database Edge můžete pro název serveru použít **protokol TCP:., 1433** .|
    |Uživatelské jméno | Přihlašovací účet SQL, který má čtečku dat a přístup k datům zapisovače dat k databázi, kterou jste zadali dříve.|
    |Heslo | Heslo pro přihlašovací účet SQL, který jste zadali dříve.|
    |Table | Název tabulky, která bude výstupem pro úlohu streamování.|
    |Zdědit dělení| Povoluje dědění schématu dělení předchozího kroku dotazu nebo vstupu. Když je tato možnost povolená, můžete očekávat lepší propustnost při psaní do tabulky založené na disku a mít plně paralelní topologii pro vaši úlohu.|
    |Velikost dávky| Maximální počet záznamů, které jsou odeslány při každé transakci hromadného vložení.|

    Zde je ukázka vstupní/výstupní konfigurace:

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
    > Další informace o výstupním adaptéru SQL pro Azure Stream Analytics najdete v tématu [Azure Stream Analytics výstup do Azure SQL Database](../stream-analytics/stream-analytics-sql-output-perf.md).

4. Definujte dotaz na úlohu ASA pro úlohu Edge. Tento dotaz by měl používat definované aliasy vstupu a výstupu jako vstupní a výstupní názvy v dotazu. Další informace najdete v tématu [Referenční dokumentace jazyka Stream Analytics dotazů](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

5. Nastavte nastavení účtu úložiště pro úlohu Edge. Účet úložiště se používá jako cíl publikování pro úlohu Edge.

6. V části **Konfigurovat**vyberte **publikovat**a pak vyberte tlačítko **publikovat** . Uložte identifikátor URI SAS pro použití s modulem SQL Database Edge.

### <a name="deploy-the-stream-analytics-edge-job-to-sql-database-edge"></a>Nasaďte úlohu Stream Analytics Edge do SQL Database Edge.

Pokud chcete nasadit úlohu streamování do modulu SQL Database Edge, aktualizujte konfiguraci modulu SQL Database Edge tak, aby zahrnovala identifikátor URI SAS pro úlohu streamování z předchozího kroku. Aktualizace modulu SQL Database Edge:

1. V Azure Portal pokračujte na nasazení IoT Hub.

2. V levém podokně vyberte **IoT Edge**.

3. Na stránce **IoT Edge** vyhledejte a vyberte IoT Edge, kde je nasazený modul SQL Database Edge.

4. Na stránce zařízení **IoT Edge zařízení** vyberte **nastavit modul**.

5. Na stránce **nastavit moduly** vyberte **konfigurovat** pro modul SQL Database Edge.

6. V podokně **IoT Edge vlastní moduly** vyberte **nastavit požadované vlastnosti nevlákenných**modulů. Aktualizujte požadované vlastnosti tak, aby zahrnovaly identifikátor URI pro možnost `ASAJobInfo`, jak je znázorněno v následujícím příkladu.

    > [!NOTE]
    > Identifikátor URI SAS v následujícím formátu JSON je pouze příklad. Nahraďte identifikátor URI skutečným identifikátorem URI z vašeho nasazení.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. Vyberte **Uložit**.

8. Na stránce **nastavit moduly** vyberte **Další**.

9. Na stránce **nastavit moduly** vyberte **Další** a pak **Odeslat**.

10. Po aktualizaci modulu se soubor úlohy Stream Analytics stáhne, odzip a nasadí na SQL Databaseou instanci Edge.

## <a name="next-steps"></a>Další kroky

- Podrobnosti o cenách a dostupnosti najdete v tématu [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).
- Vyžádejte si povolení Azure SQL Database Edge pro vaše předplatné.
- Informace o tom, jak začít, najdete v tématu [nasazení SQL Database Edge přes Azure Portal](deploy-portal.md).
