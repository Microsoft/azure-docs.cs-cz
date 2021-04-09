---
title: Kopírování dat z Blob Storage do SQL Database – Azure
description: V tomto kurzu se dozvíte, jak pomocí aktivity kopírování v kanálu Azure Data Factory kopírovat data z úložiště objektů blob do služby SQL Database.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 24cedc6a1e0be66e9a924a50e25257f18b7f96a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376883"
---
# <a name="tutorial-copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Kurz: kopírování dat z Blob Storage pro SQL Database pomocí Data Factory
> [!div class="op_single_selector"]
> * [Přehled a požadavky](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Šablona Azure Resource Manageru](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi této služby, podívejte se na [kurz o aktivitě kopírování](../quickstart-create-data-factory-dot-net.md).

V tomto kurzu vytvoříte datovou továrnu s kanálem, který kopíruje data z úložiště objektů blob do SQL Database.

Aktivita kopírování provádí přesun dat ve službě Azure Data Factory. Používá globálně dostupnou službu, která může kopírovat data mezi různými úložišti dat zabezpečeným, spolehlivým a škálovatelným způsobem. Podrobnosti o aktivitě kopírování najdete v článku [Aktivity přesunu dat](data-factory-data-movement-activities.md).  

> [!NOTE]
> Podrobný přehled služby Data Factory najdete v článku [Úvod do Azure Data Factory](data-factory-introduction.md) .
>
>

## <a name="prerequisites-for-the-tutorial"></a>Předpoklady pro tento kurz
Je nutné, abyste před zahájením tohoto kurzu splňovali následující požadavky:

* **Předplatné Azure**.  Pokud nemáte předplatné, můžete si během několika minut bezplatně vytvořit zkušební účet. Podrobnosti najdete v článku [bezplatná zkušební verze](https://azure.microsoft.com/pricing/free-trial/) .
* **Účet Azure Storage**. V tomto kurzu použijete úložiště objektů BLOB jako **zdrojové** úložiště dat. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md), kde najdete kroky pro jeho vytvoření.
* **Azure SQL Database**. V tomto kurzu použijete Azure SQL Database jako **cílové** úložiště dat. Pokud v Azure SQL Database nepoužíváte databázi, kterou můžete použít v tomto kurzu, přečtěte si téma [jak vytvořit a nakonfigurovat databázi v Azure SQL Database](../../azure-sql/database/single-database-create-quickstart.md) a vytvořit si ji.
* **SQL Server 2012/2014 nebo Visual Studio 2013**. Pomocí SQL Server Management Studio nebo sady Visual Studio můžete vytvořit ukázkovou databázi a zobrazit výsledná data v databázi.  

## <a name="collect-blob-storage-account-name-and-key"></a>Shromáždit název a klíč účtu úložiště objektů BLOB
K provedení tohoto kurzu potřebujete název účtu a klíč účtu účtu úložiště Azure. Poznamenejte si **název účtu** a **klíč účtu** pro svůj účet úložiště Azure.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/).
2. V levé nabídce klikněte na **všechny služby** a vyberte **účty úložiště**.

    ![Procházení – účty úložiště](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png)
3. V okně **účty úložiště** vyberte **účet úložiště Azure** , který chcete v tomto kurzu použít.
4. V části **Nastavení** vyberte **přístupové klíče** .
5. Klikněte na tlačítko **Kopírovat** (obrázek) vedle pole **název účtu úložiště** a uložte ho nebo vložte jinam (například: v textovém souboru).
6. Opakujte předchozí krok a zkopírujte nebo Poznamenejte si **klíč1**.

    ![Přístupový klíč k úložišti](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png)
7. Zavřete všechna okna kliknutím na **X**.

## <a name="collect-sql-server-database-user-names"></a>Shromažďování SQL serveru, databáze, uživatelských jmen
K provedení tohoto kurzu potřebujete názvy logického serveru SQL, databáze a uživatele. Poznamenejte si názvy **serverů**, **databáze** a **uživatele** pro Azure SQL Database.

1. V **Azure Portal** klikněte na **všechny služby** na levé straně a vyberte **databáze SQL**.
2. V okně **databáze SQL** vyberte **databázi** , kterou chcete v tomto kurzu použít. Poznamenejte si **název databáze**.  
3. V okně **databáze SQL** klikněte v části **Nastavení** na **vlastnosti** .
4. Poznamenejte si hodnoty pro **název serveru** a **přihlašovací jméno správce serveru**.
5. Zavřete všechna okna kliknutím na **X**.

## <a name="allow-azure-services-to-access-sql-server"></a>Povolení přístupu služeb Azure k SQL serveru
Ujistěte se, že nastavení **Povolit přístup ke službám Azure** je pro váš **Server zapnuté** , aby služba Data Factory mohla přistupovat k vašemu serveru. Pokud chcete toto nastavení ověřit a zapnout, proveďte následující kroky:

1. Na levé straně klikněte na **všechny služby** centra a pak klikněte na **servery SQL**.
2. Vyberte svůj server a v části **NASTAVENÍ** klikněte na **Brána firewall**.
3. V okně **Nastavení brány firewall** klikněte na **ZAPNUTO** u možnosti **Povolit přístup ke službám Azure**.
4. Zavřete všechna okna kliknutím na **X**.

## <a name="prepare-blob-storage-and-sql-database"></a>Příprava Blob Storage a SQL Database
Teď Připravte Azure Blob Storage a Azure SQL Database pro kurz provedením následujících kroků:  

1. Spusťte Poznámkový blok. Zkopírujte následující text a uložte ho jako **emp.txt** do složky **C:\ADFGetStarted** na pevném disku.

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

    **Pokud máte na počítači nainstalovanou SQL Server 2012/2014:** postupujte podle pokynů ke [správě Azure SQL Database pomocí SQL Server Management Studio](../../azure-sql/database/single-database-manage.md) pro připojení k vašemu serveru a spuštění skriptu SQL.

    Pokud klient nemá povolený přístup k logickému serveru SQL, je nutné nakonfigurovat bránu firewall pro váš server, aby povolovala přístup z vašeho počítače (IP adresa). Postup konfigurace brány firewall pro Server najdete v [tomto článku](../../azure-sql/database/firewall-configure.md) .

## <a name="create-a-data-factory"></a>Vytvoření datové továrny
Dokončili jste požadavky. Datovou továrnu můžete vytvořit pomocí některého z následujících způsobů. Klikněte na jednu z možností v rozevíracím seznamu v horní části nebo na následující odkazy a proveďte tento kurz.     

* [Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md)
* [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
* [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
* [Šablona Azure Resource Manageru](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
* [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Datový kanál v tomto kurzu kopíruje data ze zdrojového úložiště dat do cílového úložiště dat. Neprovádí transformaci vstupních dat, aby vytvořil výstupní data. Kurz předvádějící způsoby transformace dat pomocí Azure Data Factory najdete v tématu popisujícím [kurz vytvoření prvního kanálu, který umožňuje transformovat data pomocí clusteru Hadoop](data-factory-build-your-first-pipeline.md).
>
> Dvě aktivity můžete zřetězit (spustit jednu aktivitu po druhé) nastavením výstupní datové sady jedné aktivity jako vstupní datové sady druhé aktivity. Podrobné informace najdete v tématu s popisem [plánování a provádění ve službě Data Factory](data-factory-scheduling-and-execution.md).