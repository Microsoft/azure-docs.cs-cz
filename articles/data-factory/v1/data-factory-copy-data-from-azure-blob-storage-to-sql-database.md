---
title: Kopírování dat z Blob Storage do SQL Database – Azure | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak použít aktivitu kopírování v kanálu Azure Data Factory pro kopírování dat z úložiště objektů Blob do služby SQL database.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: e4035060-93bf-4e8d-bf35-35e2d15c51e0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: a9f76b38139cccedb97c6026f0e0efa14d0dbc8c
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2018
ms.locfileid: "42060119"
---
# <a name="tutorial-copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Kurz: Kopírování dat z úložiště objektů Blob do služby SQL Database pomocí služby Data Factory
> [!div class="op_single_selector"]
> * [Přehled a požadavky](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md)
> * [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Šablona Azure Resource Manageru](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi této služby, podívejte se na [kurz o aktivitě kopírování](../quickstart-create-data-factory-dot-net.md). 

V tomto kurzu vytvoříte datovou továrnu s kanálu pro kopírování dat z úložiště objektů Blob do služby SQL database.

Aktivita kopírování provádí přesun dat ve službě Azure Data Factory. Používá globálně dostupnou službu, která může kopírovat data mezi různými úložišti dat zabezpečeným, spolehlivým a škálovatelným způsobem. Podrobnosti o aktivitě kopírování najdete v článku [Aktivity přesunu dat](data-factory-data-movement-activities.md).  

> [!NOTE]
> Podrobný přehled o službě Data Factory najdete v článku [Úvod do služby Azure Data Factory](data-factory-introduction.md) článku.
>
>

## <a name="prerequisites-for-the-tutorial"></a>Předpoklady pro kurz
Je nutné, abyste před zahájením tohoto kurzu splňovali následující požadavky:

* **Předplatné Azure**.  Pokud nemáte předplatné, můžete si během několika minut bezplatně vytvořit zkušební účet. Zobrazit [bezplatnou zkušební verzi](http://azure.microsoft.com/pricing/free-trial/) , kde najdete podrobnosti.
* **Účet úložiště Azure**. Použijete jako úložiště objektů blob **zdroj** úložiště dat v tomto kurzu. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-quickstart-create-account.md), kde najdete kroky pro jeho vytvoření.
* **Azure SQL Database**. Použití služby Azure SQL database jako **cílové** úložiště dat v tomto kurzu. Pokud nemáte Azure SQL database, můžete použít v tomto kurzu, najdete v tématu [jak vytvořit a nakonfigurovat službu Azure SQL Database](../../sql-database/sql-database-get-started.md) k jejímu vytvoření.
* **SQL Server 2012 nebo 2014 nebo Visual Studio 2013**. Použijete SQL Server Management Studio nebo Visual Studio k vytvoření ukázkové databáze a zobrazíte Výsledná data v databázi.  

## <a name="collect-blob-storage-account-name-and-key"></a>Shromažďovat název účtu služby blob storage a klíč
Budete potřebovat název účtu a klíč svého účtu Azure storage provedete v tomto kurzu. Poznamenejte si **název účtu** a **klíč účtu** pro svůj účet úložiště Azure.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Klikněte na tlačítko **všechny služby** v levé nabídce a vyberte **účty úložiště**.

    ![Procházet – účty úložiště](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png)
3. V **účty úložiště** okno, vyberte **účtu služby Azure storage** , kterou chcete použít v tomto kurzu.
4. Vyberte **přístupové klíče** odkaz pod **nastavení**.
5. Klikněte na **kopírování** (image) tlačítko vedle **název účtu úložiště** textové pole a uložení a vložte ho někam (například: do textového souboru).
6. Opakujte předchozí krok pro kopírování nebo poznamenejte **key1**.

    ![Přístupový klíč k úložišti](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png)
7. Zavřete všechna okna kliknutím **X**.

## <a name="collect-sql-server-database-user-names"></a>Shromažďování systému SQL server, databáze, uživatelská jména
Budete potřebovat názvy serveru Azure SQL, databáze a uživatele v tomto kurzu. Poznamenejte si názvy **server**, **databáze**, a **uživatele** pro vaši databázi Azure SQL.

1. V **webu Azure portal**, klikněte na tlačítko **všechny služby** na levé straně a vyberte **databází SQL**.
2. V **okno databáze SQL**, vyberte **databáze** , kterou chcete použít v tomto kurzu. Poznamenejte si **název_databáze**.  
3. V **SQL database** okna, klikněte na tlačítko **vlastnosti** pod **nastavení**.
4. Poznamenejte si hodnoty pro **název serveru** a **přihlašovací jméno správce serveru**.
5. Zavřete všechna okna kliknutím **X**.

## <a name="allow-azure-services-to-access-sql-server"></a>Povolit službám Azure přístup k systému SQL server
Ujistěte se, že **povolit přístup ke službám Azure** nastavení obrácené **ON** pro váš server Azure SQL tak, aby služba Data Factory můžete přístup k vašemu serveru Azure SQL. Pokud chcete toto nastavení ověřit a zapnout, proveďte následující kroky:

1. Klikněte na tlačítko **všechny služby** na levé straně a klikněte na straně **SQL servery**.
2. Vyberte svůj server a v části **NASTAVENÍ** klikněte na **Brána firewall**.
3. V okně **Nastavení brány firewall** klikněte na **ZAPNUTO** u možnosti **Povolit přístup ke službám Azure**.
4. Zavřete všechna okna kliknutím **X**.

## <a name="prepare-blob-storage-and-sql-database"></a>Připravili Blob Storage a SQL Database
Teď připravte úložiště objektů blob v Azure a Azure SQL database pro tento kurz provedením následující kroků:  

1. Spusťte Poznámkový blok. Zkopírujte následující text a uložte ho jako **emp.txt** k **C:\ADFGetStarted** složky na pevném disku.

    ```
    John, Doe
    Jane, Doe
    ```
2. Pomocí nástrojů, jako je [Azure Storage Explorer](http://storageexplorer.com/), vytvořte kontejner **adftutorial** a odešlete soubor **emp.txt** do tohoto kontejneru.

    ![Průzkumník služby Azure Storage. Kopírování dat z úložiště objektů Blob do služby SQL database](./media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/getstarted-storage-explorer.png)
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

    **Pokud máte SQL Server 2012/2014 v počítači nainstalovanou:** postupujte podle pokynů v [Správa Azure SQL Database pomocí aplikace SQL Server Management Studio](../../sql-database/sql-database-manage-azure-ssms.md) pro připojení k serveru Azure SQL a spusťte skript SQL. 

    Pokud klient nemá povolený přístup ke službě Azure SQL Server, budete muset nakonfigurovat bránu firewall pro Azure SQL Server tak, aby povolovala přístup z vašeho počítače (IP adresa). Postup konfigurace brány firewall pro server SQL Azure najdete v [tomto článku](../../sql-database/sql-database-configure-firewall-settings.md).

## <a name="create-a-data-factory"></a>Vytvoření datové továrny
Dokončili jste požadavky. Můžete vytvářet datové továrny pomocí jedné z následujících způsobů. Klikněte na jednu z možností v rozevíracím seznamu v horní části nebo získat prostřednictvím následujících odkazů k provedení tohoto kurzu.     

* [Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md)
* [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
* [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
* [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
* [Šablona Azure Resource Manageru](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
* [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Datový kanál v tomto kurzu kopíruje data ze zdrojového úložiště dat do cílového úložiště dat. Neprovádí transformaci vstupních dat, aby vytvořil výstupní data. Kurz předvádějící způsoby transformace dat pomocí Azure Data Factory najdete v tématu popisujícím [kurz vytvoření prvního kanálu, který umožňuje transformovat data pomocí clusteru Hadoop](data-factory-build-your-first-pipeline.md).
> 
> Dvě aktivity můžete zřetězit (spustit jednu aktivitu po druhé) nastavením výstupní datové sady jedné aktivity jako vstupní datové sady druhé aktivity. Podrobné informace najdete v tématu s popisem [plánování a provádění ve službě Data Factory](data-factory-scheduling-and-execution.md). 
