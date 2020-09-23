---
title: Použití SQL Database DACPAC a BACPAC balíčků – Azure SQL Edge
description: Další informace o použití DACPACs a bacpacs ve službě Azure SQL Edge
keywords: SQL Edge, SqlPackage
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 6c8be6e67b1d7b919d6ea221c473c8975e559658
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90887487"
---
# <a name="sql-database-dacpac-and-bacpac-packages-in-sql-edge"></a>SQL Database DACPAC a BACPAC balíčky v SQL Edge

Azure SQL Edge je optimalizovaný relační databázový stroj pro nasazování do IoT a na hraniční zařízení. Je postaven na nejnovějších verzích modulu Microsoft SQL Database Engine, který poskytuje špičkové možnosti pro výkon, zabezpečení a zpracování dotazů. Spolu s špičkovými možnostmi správy relačních databází SQL Server poskytuje Azure SQL Edge integrované možnosti streamování pro analýzy v reálném čase a složité zpracování událostí.

Azure SQL Edge taky poskytuje nativní implementaci SqlPackage.exe, která vám umožní nasadit [SQL Database DACPAC a balíček BacPac](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) během nasazování SQL Edge. 

Balíčky SQL Database DACPAC a BacPac lze nasadit do SQL Edge pomocí `MSSQL_PACKAGE` proměnné prostředí. Proměnná prostředí se dá nakonfigurovat pomocí kterékoli z následujících možností.  
- Umístění místní složky v rámci kontejneru SQL obsahujícího soubory DACPAC a BacPac. Tuto složku lze namapovat na svazek hostitele pomocí přípojných bodů nebo kontejnerů datových svazků. 
- Místní cesta k souboru v mapování kontejneru SQL k souboru DACPAC nebo BacPac. Tato cesta k souboru se dá namapovat na svazek hostitele pomocí přípojných bodů nebo kontejnerů datových svazků. 
- Místní cesta k souboru v mapování kontejneru SQL k souboru zip, který obsahuje soubory DACPAC nebo BacPac. Tato cesta k souboru se dá namapovat na svazek hostitele pomocí přípojných bodů nebo kontejnerů datových svazků. 
- Adresa URL SAS objektu blob Azure do souboru zip, který obsahuje soubory DACPAC a BacPac.
- Adresa URL SAS objektu blob Azure na DACPAC nebo soubor BacPac. 

## <a name="use-a-sql-database-dac-package-with-sql-edge"></a>Použití SQL Databaseho balíčku DAC s okrajem SQL

Pokud chcete nasadit (nebo importovat) SQL Database balíček DAC `(*.dacpac)` nebo soubor BacPac `(*.bacpac)` pomocí služby Azure Blob Storage a souboru zip, postupujte podle následujících kroků. 

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

    5. Na stránce **nastavit moduly** a klikněte na modul Azure SQL Edge.

    6. V podokně **aktualizovat IoT Edge modul** vyberte **proměnné prostředí**. Přidejte `MSSQL_PACKAGE` proměnnou prostředí a zadejte adresu URL SAS vygenerovanou v kroku 3 výše jako hodnotu proměnné prostředí. 

    7. Vyberte **Aktualizovat**.

    8. Na stránce **nastavit moduly** vyberte **zkontrolovat + vytvořit**.

    9. Na stránce **nastavit moduly** vyberte **vytvořit**.

5. Po aktualizaci modulu se soubory balíčku stáhnou, rozbalí a nasadí proti instanci SQL Edge.

Při každém restartování kontejneru Azure SQL Edge se aplikace SQL Edge pokusí stáhnout balíček souborů zip a vyhodnotit změny. Pokud dojde k nové verzi souboru DACPAC, změny se nasadí do databáze v SQL Edge.

## <a name="next-steps"></a>Další kroky

- [Nasazení okraje SQL Edge prostřednictvím Azure Portal](deploy-portal.md).
- [Streamování dat](stream-data.md)
- [Machine Learning a AI s ONNX v SQL Edge](onnx-overview.md)
