---
title: Použití SQL Database DACPAC a BACPAC balíčků – Azure SQL Edge (Preview)
description: Další informace o použití DACPACs a bacpacs ve službě Azure SQL Edge (Preview)
keywords: SQL Edge, SqlPackage
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 52c8e9586d8ee53cdaac28cb1c48d2927d82c2ed
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89462754"
---
# <a name="sql-database-dacpac-and-bacpac-packages-in-sql-edge"></a>SQL Database DACPAC a BACPAC balíčky v SQL Edge

Azure SQL Edge (Preview) je optimalizovaný relační databázový stroj, který je optimalizovaný pro nasazení v IoT a hraničních zařízeních. Je postaven na nejnovějších verzích Microsoft SQL Server databázového stroje, který poskytuje špičkové možnosti pro výkon, zabezpečení a zpracování dotazů. Spolu s špičkovými možnostmi správy relačních databází SQL Server poskytuje Azure SQL Edge integrované možnosti streamování pro analýzy v reálném čase a složité zpracování událostí.

Azure SQL Edge taky poskytuje nativní implementaci SqlPackage.exe, která vám umožní nasadit [SQL Database DACPAC a balíček BacPac](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) během nasazování SQL Edge. SQL Database DACPACs lze nasadit do SQL Edge pomocí parametru SqlPackage vystaveného prostřednictvím `module twin's desired properties` Možnosti modulu SQL Edge:

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
| SqlPackage | Identifikátor URI úložiště objektů BLOB v Azure pro soubor *. zip* , který obsahuje SQL Database balíček DAC nebo BacPac Soubor ZIP může obsahovat více balíčků DAC nebo BacPac souborů.
| ASAJobInfo | Identifikátor URI úložiště objektů BLOB v Azure pro hraniční úlohu ASA

## <a name="use-a-sql-database-dac-package-with-sql-edge"></a>Použití SQL Databaseho balíčku DAC s okrajem SQL

Chcete-li použít SQL Database balíček DAC `(*.dacpac)` nebo soubor BacPac `(*.bacpac)` s okrajem SQL, postupujte podle následujících kroků:

1. Vytvořte nebo rozbalte balíček DAC nebo exportujte soubor BacPac pomocí mechanismu uvedeného níže. 
    - Vytvoření nebo extrakce SQL Database balíčku DAC Informace o tom, jak vygenerovat balíček DAC pro existující SQL Server databázi, najdete v tématu [extrakce DAC z databáze](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) .
    - Exportování nasazeného balíčku DAC nebo databáze. Informace o tom, jak vygenerovat soubor BacPac pro existující databázi SQL Server, najdete v tématu [Export aplikace na datové vrstvě](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application/) .

2. Zip `*.dacpac` `*.bacpac` soubor nebo soubor a nahrajte ho do účtu služby Azure Blob Storage. Další informace o nahrávání souborů do úložiště objektů BLOB v Azure najdete v tématu [nahrání, stažení a výpis objektů BLOB pomocí Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Vygenerujte sdílený přístupový podpis pro soubor ZIP pomocí Azure Portal. Další informace najdete v tématu [delegování přístupu pomocí podpisů sdíleného přístupu (SAS)](../storage/common/storage-sas-overview.md).

4. Aktualizujte konfiguraci modulu SQL Edge tak, aby zahrnovala identifikátor URI sdíleného přístupu pro balíček DAC. Chcete-li aktualizovat modul SQL Edge, proveďte tyto kroky:

    1. V Azure Portal pokračujte na nasazení IoT Hub.

    2. V levém podokně vyberte **IoT Edge**.

    3. Na stránce **IoT Edge** vyhledejte a vyberte IoT Edge, kde je nasazen modul SQL Edge.

    4. Na stránce zařízení **IoT Edge zařízení** vyberte **nastavit modul**.

    5. Na stránce **nastavit moduly** vyberte **Konfigurovat** pro modul SQL Edge.

    6. V podokně **IoT Edge vlastní moduly** vyberte **nastavit požadované vlastnosti nevlákenných**modulů. Aktualizujte požadované vlastnosti tak, aby zahrnovaly identifikátor URI pro `SQLPackage` možnost, jak je znázorněno v následujícím příkladu.

        > [!NOTE]
        > Identifikátor URI SAS v následujícím formátu JSON je pouze příklad. Nahraďte identifikátor URI skutečným identifikátorem URI z vašeho nasazení.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "<<<SAS URL for the *.zip file containing the dacpac and/or the bacpac files",
                }
            }
        ```

    7. Vyberte **Uložit**.

    8. Na stránce **nastavit moduly** vyberte **Další**.

    9. Na stránce **nastavit moduly** vyberte **Další** a pak **Odeslat**.

5. Po aktualizaci modulu se soubor balíčku stáhne, rozbalí a nasadí na instanci SQL Edge.

Při každém restartování kontejneru Azure SQL Edge se `*.dacpac` balíček souborů stáhne a vyhodnotí jeho změny. Pokud dojde k nové verzi souboru DACPAC, změny se nasadí do databáze v SQL Edge. Soubory BacPac 

## <a name="next-steps"></a>Další kroky

- [Nasazení okraje SQL Edge prostřednictvím Azure Portal](deploy-portal.md).
- [Streamování dat](stream-data.md)
- [Machine Learning a AI s ONNXem v SQL Edge (Preview)](onnx-overview.md)
