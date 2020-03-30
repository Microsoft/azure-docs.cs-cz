---
title: Kopírování dat z úložiště objektů blob do databáze SQL – Azure
description: Tento kurz ukazuje, jak pomocí kopírovat aktivitu v kanálu Azure Data Factory ke kopírování dat z úložiště objektů Blob do databáze SQL.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: e4035060-93bf-4e8d-bf35-35e2d15c51e0
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: cc2f0a513219a671dd8a75ee00af4fc9d4c6a68a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979724"
---
# <a name="tutorial-copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Kurz: Kopírování dat z úložiště objektů blob do databáze SQL pomocí datové továrny
> [!div class="op_single_selector"]
> * [Přehled a požadavky](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Šablona Azure Resource Manageru](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [ROZHRANÍ API PRO ODPOČINEK](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi této služby, podívejte se na [kurz o aktivitě kopírování](../quickstart-create-data-factory-dot-net.md).

V tomto kurzu vytvoříte továrnu dat s kanálem pro kopírování dat z úložiště objektů Blob do databáze SQL.

Aktivita kopírování provádí přesun dat ve službě Azure Data Factory. Používá globálně dostupnou službu, která může kopírovat data mezi různými úložišti dat zabezpečeným, spolehlivým a škálovatelným způsobem. Podrobnosti o aktivitě kopírování najdete v článku [Aktivity přesunu dat](data-factory-data-movement-activities.md).  

> [!NOTE]
> Podrobný přehled služby Data Factory najdete v článku [Úvod do Azure Data Factory.](data-factory-introduction.md)
>
>

## <a name="prerequisites-for-the-tutorial"></a>Předpoklady pro výuku
Je nutné, abyste před zahájením tohoto kurzu splňovali následující požadavky:

* **Předplatné Azure**.  Pokud nemáte předplatné, můžete si během několika minut bezplatně vytvořit zkušební účet. Podrobnosti najdete v článku [o bezplatné zkušební verzi.](https://azure.microsoft.com/pricing/free-trial/)
* **Účet úložiště Azure**. Úložiště objektů blob se používá jako **zdrojové** úložiště dat v tomto kurzu. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md), kde najdete kroky pro jeho vytvoření.
* **Azure SQL Database**. V tomto kurzu se jako cílové úložiště dat používáte jako **cílové** úložiště dat. Pokud nemáte databázi Azure SQL, kterou můžete použít v kurzu, přečtěte [si, jak vytvořit a nakonfigurovat Azure SQL Database](../../sql-database/sql-database-get-started.md) k vytvoření.
* **SQL Server 2012/2014 nebo Visual Studio 2013**. Sql Server Management Studio nebo Visual Studio k vytvoření ukázkové databáze a zobrazení dat výsledků v databázi.  

## <a name="collect-blob-storage-account-name-and-key"></a>Shromažďování názvu účtu úložiště objektů blob a klíče
K tomuto kurzu potřebujete název účtu a klíč účtu vašeho účtu úložiště Azure. Poznamenejte si **název účtu** a **klíč účtu** pro váš účet úložiště Azure.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. V levé nabídce klepněte na **Všechny služby** a vyberte **položku Účty úložiště**.

    ![Procházet – účty úložiště](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png)
3. V okně **Účty úložiště** vyberte účet **úložiště Azure,** který chcete použít v tomto kurzu.
4. V části **NASTAVENÍ**vyberte odkaz **Přístupové klávesy** .
5. Klikněte na tlačítko **Kopírovat** (obrázek) vedle textového pole **Název účtu úložiště** a někam ho uložte (například: do textového souboru).
6. Opakováním předchozího kroku zkopírujte nebo poznamenejte **si klávesu 1**.

    ![Přístupový klíč úložiště](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png)
7. Zavřete všechny nože klepnutím na **tlačítko X**.

## <a name="collect-sql-server-database-user-names"></a>Shromažďujte SQL server, databázi, uživatelská jména
K tomuto kurzu potřebujete názvy Serveru Azure SQL, databáze a uživatele. Poznamenejte si názvy **serverů**, **databáze**a **uživatele** pro databázi Azure SQL.

1. Na **webu Azure Portal**klikněte na **Všechny služby** vlevo a vyberte **databáze SQL**.
2. V **okně databáze SQL**vyberte **databázi,** kterou chcete použít v tomto kurzu. Poznamenejte si **název databáze**.  
3. V okně **databáze SQL** klepněte na **položku Vlastnosti** v části **NASTAVENÍ**.
4. Poznamenejte si hodnoty pro **NÁZEV SERVERU** a PŘIHLÁŠENÍ **SPRÁVCE SERVERU**.
5. Zavřete všechny nože klepnutím na **tlačítko X**.

## <a name="allow-azure-services-to-access-sql-server"></a>Povolení přístupu ke službám Azure serveru SQL
Ujistěte se, že je pro váš server Azure SQL **zapnutý** přístup **ke službám Azure,** aby služba Data Factory mohla přistupovat k vašemu serveru Azure SQL. Pokud chcete toto nastavení ověřit a zapnout, proveďte následující kroky:

1. Vlevo klikněte na **Centrum všechny služby** a na **servery SQL**.
2. Vyberte svůj server a v části **NASTAVENÍ** klikněte na **Brána firewall**.
3. V okně **Nastavení brány firewall** klikněte na **ZAPNUTO** u možnosti **Povolit přístup ke službám Azure**.
4. Zavřete všechny nože klepnutím na **tlačítko X**.

## <a name="prepare-blob-storage-and-sql-database"></a>Příprava úložiště objektů blob a databáze SQL
Teď připravte úložiště objektů blob Azure a azure SQL databáze pro kurz provedením následujících kroků:  

1. Spusťte Poznámkový blok. Zkopírujte následující text a uložte jej jako **soubor emp.txt** do složky **C:\ADFGetStarted** na pevném disku.

    ```
    John, Doe
    Jane, Doe
    ```
2. Pomocí nástrojů, jako je [Azure Storage Explorer](https://storageexplorer.com/), vytvořte kontejner **adftutorial** a odešlete soubor **emp.txt** do tohoto kontejneru.

3. Pomocí následujícího skriptu SQL vytvořte tabulku **emp** v Azure SQL Database.  

    ```SQL
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

    **Pokud máte v počítači nainstalovaný SQL Server 2012/2014:** [postupujte](../../sql-database/sql-database-manage-azure-ssms.md) podle pokynů ze správy Azure SQL Database pomocí SQL Server Management Studio pro připojení k serveru Azure SQL a spuštění skriptu SQL.

    Pokud klient nemá povolený přístup ke službě Azure SQL Server, budete muset nakonfigurovat bránu firewall pro Azure SQL Server tak, aby povolovala přístup z vašeho počítače (IP adresa). Postup konfigurace brány firewall pro server SQL Azure najdete v [tomto článku](../../sql-database/sql-database-configure-firewall-settings.md).

## <a name="create-a-data-factory"></a>Vytvoření datové továrny
Dokončili jste požadavky. Můžete vytvořit data factory pomocí jednoho z následujících způsobů. Chcete-li kurz provést, klepněte na jednu z možností v rozevíracím seznamu v horní části nebo na následující odkazy.     

* [Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md)
* [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
* [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
* [Šablona Azure Resource Manageru](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [ROZHRANÍ API PRO ODPOČINEK](data-factory-copy-activity-tutorial-using-rest-api.md)
* [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Datový kanál v tomto kurzu kopíruje data ze zdrojového úložiště dat do cílového úložiště dat. Neprovádí transformaci vstupních dat, aby vytvořil výstupní data. Kurz předvádějící způsoby transformace dat pomocí Azure Data Factory najdete v tématu popisujícím [kurz vytvoření prvního kanálu, který umožňuje transformovat data pomocí clusteru Hadoop](data-factory-build-your-first-pipeline.md).
>
> Dvě aktivity můžete zřetězit (spustit jednu aktivitu po druhé) nastavením výstupní datové sady jedné aktivity jako vstupní datové sady druhé aktivity. Podrobné informace najdete v tématu s popisem [plánování a provádění ve službě Data Factory](data-factory-scheduling-and-execution.md).
