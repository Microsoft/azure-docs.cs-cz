---
title: Použití SQL Databaseho balíčku DAC a úlohy Stream Analytics s Azure SQL Database Edge | Microsoft Docs
description: Informace o použití Stream Analytics úloh v SQL Database Edge
keywords: SQL Database Edge, Stream Analytics, SqlPackage
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: c3ed84e06f693925ed8b484070616e223929e401
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74108742"
---
# <a name="using-sql-database-dac-package-and-stream-analytics-job-with-sql-database-edge"></a>Použití SQL Databaseho balíčku DAC a Stream Analytics úlohy s SQL Database Edge

Azure SQL Database Edge Preview je optimalizovaný relační databázový stroj pro nasazení IoT a Edge. Je postavená na nejnovějších verzích Microsoft SQL Server databázového stroje, který poskytuje špičkové možnosti výkonu, zabezpečení a zpracování dotazů. Spolu s špičkovými možnostmi správy relačních databází SQL Server nabízí Azure SQL Database Edge vestavěnou funkci streamování pro analýzy v reálném čase a složité zpracování událostí.

Azure SQL Database Edge taky poskytuje nativní implementaci SQLPackage. exe, která umožňuje uživatelům nasadit [SQL Database DAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) Package během nasazování SQL Database Edge.

Azure SQL Database Edge zpřístupňuje dva volitelné parametry prostřednictvím možnosti *nevlákenné vlastnosti* modulu IoT Edge modulu.

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
| SQLPackage | Identifikátor URI služby Azure Blob Storage pro soubor *. zip obsahující SQL Database DAC Package.
| ASAJobInfo | Identifikátor URI služby Azure Blob Storage pro hraniční úlohu ASA Další informace o publikování hraničních úloh ASA najdete v tématu [publikování hraničních úloh ASA pro SQL Database Edge](/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge).

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>Použití SQL Databasech balíčků DAC s SQL Database Edge

Pokud chcete použít SQL Database balíček DAC (*. DACPAC) s SQL Database Edge, postupujte prosím podle následujících kroků.

1. Vytvoření nebo extrakce SQL Database balíčku DAC Pomocí konceptů uvedených v části [extrakce DAC z existující databáze](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) můžete vygenerovat DacPac pro existující SQL Database.

2. Zip * *. DACPAC* a nahrajte ho do účtu služby Azure Blob Storage. Další informace o nahrání souborů do Azure Blob Storage najdete v tématu [nahrání, stažení a výpis objektů BLOB pomocí Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Vygenerujte podpis SAS pro soubor ZIP pomocí Azure Portal. Další informace najdete v tématu [delegování přístupu pomocí podpisů sdíleného přístupu (SAS)](../storage/common/storage-sas-overview.md).

4. Aktualizujte konfiguraci modulu SQL Database Edge tak, aby zahrnoval identifikátor URI SAS pro balíček DAC. Aktualizace modulu SQL Database Edge

    1. V Azure Portal přejděte k nasazení IoT Hub.

    2. V podokně na levé straně klikněte na **IoT Edge**.

    3. Na stránce **IoT Edge** Najděte a klikněte na IoT Edge, kde je nasazený modul SQL Database Edge.

    4. Na stránce zařízení *IoT Edge zařízení* klikněte na **nastavit modul**. 

    5. Na stránce **nastavit moduly** klikněte na *konfigurovat* v modulu SQL Database Edge. 

    6. V podokně **IoT Edge vlastní moduly** vyberte požadované *vlastnosti moduly* a pak aktualizujte požadované vlastnosti tak, aby obsahovaly identifikátor URI pro možnost SQLPackage, jak je znázorněno v následujícím příkladu. 

        > [!NOTE]
        > Identifikátor URI SAS níže je určen pouze pro ilustraci. Nahraďte identifikátor URI skutečným identifikátorem URI z vašeho nasazení.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. Klikněte na možnost **Uložit**.

    8. Na stránce **nastavit moduly** klikněte na *Další*.

    9. Na stránce **nastavit moduly** klikněte na *Další* a pak klikněte na **Odeslat**.

5. Vystavte aktualizaci modulu, soubor DACPAC se stáhne, odzip a nasadí se na SQL Database hraniční instance.

## <a name="using-streaming-jobs-with-sql-database-edge"></a>Použití úloh streamování s SQL Database Edge

Azure SQL Database Edge má nativní implementaci Stream Analytics runtime. To umožňuje uživatelům vytvořit úlohu Azure Stream Analytics Edge a nasazovat tuto úlohu jako úlohu streamování SQL Database Edge. Pokud chcete vytvořit hraniční úlohu Stream Analytics, postupujte podle následujících kroků.

1. Pomocí [adresy URL](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true)náhledu vyhledejte Azure Portal. Tato adresa URL ve verzi Preview umožňuje uživatelům nakonfigurovat SQL Database výstup pro úlohu Stream Analytics na Edge.

2. Vytvořte novou **Azure Stream Analytics na IoT Edge** úlohu a vyberte **hraniční**prostředí cílení.

3. Zadejte *vstupní* a *výstupní* Azure Stream Analytics úlohy. Každý výstup SQL (nakonfigurovaný níže) je vázaný na jednu tabulku v rámci databáze. Pokud je potřeba streamovat data do více tabulek, budete muset vytvořit několik výstupů SQL Database. Výstupy SQL je možné nakonfigurovat tak, aby odkazovaly na různé databáze.

    *Input – vyberte EdgeHub jako vstup pro úlohu Edge a vyplňte informace o prostředku.*

    *Výstup – výběr SQL Database jako výstup, "zadání SQL Database nastavení ručně" a zadání podrobností konfigurace pro databázi a tabulku.*

    |Pole      | Popis |
    |---------------|-------------|
    |Alias pro výstup | Název aliasu výstupu.|
    |Databáze | Název SQL Database. Toto musí být platný název databáze, který existuje v instanci SQL Database Edge.|
    |Název serveru | Název (nebo IP adresa) a podrobnosti o číslech portů pro instanci SQL. Pro nasazení SQL Database Edge můžete jako název serveru použít **protokol TCP:., 1433** .|
    |Uživatelské jméno | Přihlašovací účet SQL s přístupem pro čtení dat a zapisovač dat k databázi uvedené výše.|
    |Heslo | Heslo pro přihlašovací účet SQL zmíněné výše.|
    |Table | Název tabulky, která bude výstupem pro úlohu streamování.|
    |Zdědit dělení| Tato možnost Konfigurace výstupu SQL umožňuje dědění schématu dělení předchozího kroku dotazu nebo vstupu. Když je tato možnost povolená, zapisuje se do tabulky založené na disku a má plně paralelní topologie pro vaši práci. očekává se, že se zobrazí lepší propustnost.|
    |Velikost dávky| Velikost dávky je maximální počet záznamů, které se odesílají při každé transakci hromadného vložení.|

    Ukázková konfigurace vstupu/výstupu níže:

    ```txt
        Input:
            Input from EdgeHub
            Input alias: input
            Event serialization format: JSON
            Encoding: UTF-8
            Event compression type: None

        Output :
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

6. V části konfigurovat vyberte publikovat a klikněte na tlačítko publikovat. Uložte adresu URL SAS pro použití s modulem SQL Database Edge.

### <a name="deploy-the-stream-analytics-edge-job-to-the-sql-database-edge"></a>Nasazení hraniční úlohy Stream Analytics do SQL Database Edge

Pokud chcete nasadit úlohu streamování do modulu SQL Database Edge, aktualizujte konfiguraci modulu SQL Database Edge tak, aby obsahovala identifikátor URI SAS pro úlohu streamování z výše uvedeného kroku. Aktualizace modulu SQL Database Edge

1. V Azure Portal přejděte k nasazení IoT Hub.

2. V podokně na levé straně klikněte na **IoT Edge**.

3. Na stránce **IoT Edge** Najděte a klikněte na IoT Edge, kde je nasazený modul SQL Database Edge.

4. Na stránce zařízení *IoT Edge zařízení* klikněte na **nastavit modul**. 

5. Na stránce **nastavit moduly** klikněte na *konfigurovat* v modulu SQL Database Edge. 

6. V podokně **IoT Edge vlastní moduly** vyberte požadované *vlastnosti moduly* , které mají být v seznamu, a poté aktualizujte požadované vlastnosti tak, aby zahrnovaly identifikátor URI pro možnost ASAJobInfo, jak je znázorněno v následujícím příkladu. 

    > [!NOTE]
    > Identifikátor URI SAS níže je určen pouze pro ilustraci. Nahraďte identifikátor URI skutečným identifikátorem URI z vašeho nasazení.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. Klikněte na možnost **Uložit**.

8. Na stránce **nastavit moduly** klikněte na *Další*.

9. Na stránce **nastavit moduly** klikněte na *Další* a pak klikněte na **Odeslat**.

10. Po publikování aktualizace modulu se soubor úlohy Stream Analytics stáhne, odzip a nasadí na SQL Database Edge.

## <a name="next-steps"></a>Další kroky

- Podrobnosti o cenách a dostupnosti najdete v tématu [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).
- Požadavek na povolení Azure SQL Database Edge pro vaše předplatné.
- Informace o tom, jak začít, najdete v následujících tématech:
  - [Nasazení SQL Database Edge přes Azure Portal](deploy-portal.md)
