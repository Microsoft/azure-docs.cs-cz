---
title: Použití úloh Azure Stream Analytics Edge s Azure SQL Edge (Preview)
description: Informace o použití úloh Stream Analytics ve službě Azure SQL Edge (Preview)
keywords: SQL Edge, Stream Analytics,
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 3b57cb8cae80381a6c2cd88358dd9284ba56c919
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597314"
---
# <a name="using-azure-stream-analytics-jobs-with-sql-edge"></a>Použití úloh Azure Stream Analytics s okrajem SQL

Azure SQL Edge (Preview) je optimalizovaný relační databázový stroj, který je optimalizovaný pro nasazení v IoT a hraničních zařízeních. Je postaven na nejnovějších verzích Microsoft SQL Server databázového stroje, který poskytuje špičkové možnosti pro výkon, zabezpečení a zpracování dotazů. Spolu s špičkovými možnostmi správy relačních databází SQL Server poskytuje Azure SQL Edge integrované možnosti streamování pro analýzy v reálném čase a složité zpracování událostí.

Azure SQL Edge má nativní implementaci modulu Stream Analytics runtime. Tato implementace umožňuje vytvořit úlohu Azure Stream Analytics Edge a nasazovat tuto úlohu jako úlohu streamování Edge SQL. Azure Stream Analytics úlohy je možné nasadit do SQL Edge pomocí parametru ASAJobInfo, který je vystavený prostřednictvím `module twin's desired properties` Možnosti modulu SQL Edge:

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
| ASAJobInfo | Identifikátor URI úložiště objektů BLOB v Azure pro hraniční úlohu ASA

## <a name="create-an-azure-stream-analytics-edge-job"></a>Vytvořit úlohu Azure Stream Analytics Edge

1. Přejděte na web Azure Portal.

2. Vytvoří novou **Azure Stream Analytics na IoT Edge** úlohu. Vyberte hostující prostředí, které cílí na **Edge**.

3. Definujte vstup a výstup pro úlohu Azure Stream Analytics. Každý výstup SQL, který tady nastavíte, je vázaný na jednu tabulku v databázi. Pokud potřebujete streamovat data do více tabulek, budete muset vytvořit několik výstupů SQL Database. Můžete nakonfigurovat výstupy SQL tak, aby odkazovaly na různé databáze.

    **Vstup**. Jako vstup pro úlohu Edge vyberte EdgeHub a zadejte informace o prostředku.

    **Výstup**. Jako výstup vyberte SQL Database. Vyberte **zadat SQL Database nastavení ručně**. Zadejte podrobnosti o konfiguraci databáze a tabulky.

    |Pole      | Popis |
    |---------------|-------------|
    |Alias pro výstup | Název aliasu výstupu.|
    |databáze | Název databáze SQL. Musí se jednat o platný název databáze, která existuje v instanci SQL Edge.|
    |Název serveru | Název (nebo IP adresa) a podrobnosti o číslech portů pro instanci SQL. Pro nasazení SQL Edge můžete pro název serveru použít **TCP:., 1433** .|
    |Uživatelské jméno | Přihlašovací účet SQL, který má čtečku dat a přístup k datům zapisovače dat k databázi, kterou jste zadali dříve.|
    |Heslo | Heslo pro přihlašovací účet SQL, který jste zadali dříve.|
    |Tabulka | Název tabulky, která bude výstupem pro úlohu streamování.|
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

6. V části **Konfigurovat**vyberte **publikovat**a pak vyberte tlačítko **publikovat** . Uložte identifikátor URI SAS pro použití s modulem SQL Edge.

## <a name="deploy-azure-stream-analytics-edge-job-to-sql-edge"></a>Nasazení úlohy Azure Stream Analytics Edge do okraje SQL

Pokud chcete nasadit úlohu streamování do modulu SQL Edge, aktualizujte konfiguraci modulu SQL Edge tak, aby zahrnovala identifikátor URI SAS pro úlohu streamování z předchozího kroku. Aktualizace modulu SQL Edge:

1. V Azure Portal pokračujte na nasazení IoT Hub.

2. V levém podokně vyberte **IoT Edge**.

3. Na stránce **IoT Edge** vyhledejte a vyberte IoT Edge, kde je nasazen modul SQL Edge.

4. Na stránce zařízení **IoT Edge zařízení** vyberte **nastavit modul**.

5. Na stránce **nastavit moduly** vyberte **Konfigurovat** pro modul SQL Edge.

6. V podokně **IoT Edge vlastní moduly** vyberte **nastavit požadované vlastnosti nevlákenných**modulů. Aktualizujte požadované vlastnosti tak, aby zahrnovaly identifikátor URI pro `ASAJobInfo` možnost, jak je znázorněno v následujícím příkladu.

    > [!NOTE]
    > Identifikátor URI SAS v následujícím formátu JSON je pouze příklad. Nahraďte identifikátor URI skutečným identifikátorem URI z vašeho nasazení.

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "<<<SAS URL For the published ASA Edge Job>>>>"
            }
        }
    ```

7. Vyberte **Uložit**.

8. Na stránce **nastavit moduly** vyberte **Další**.

9. Na stránce **nastavit moduly** vyberte **Další** a pak **Odeslat**.

10. Po aktualizaci modulu se soubor úlohy Stream Analytics stáhne, odzip a nasadí na instanci SQL Edge.

## <a name="next-steps"></a>Další kroky

- [Nasazení okraje SQL Edge prostřednictvím Azure Portal](deploy-portal.md).

- [Streamování dat](stream-data.md)

- [Machine Learning a AI s ONNXem v SQL Edge (Preview)](onnx-overview.md)
