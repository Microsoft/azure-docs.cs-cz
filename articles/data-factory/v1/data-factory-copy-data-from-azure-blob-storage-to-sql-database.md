---
title: Kopírování dat z úložiště objektů Blob do SQL Database – Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak pomocí aktivity kopírování v kanál služby Azure Data Factory ke zkopírování dat z úložiště objektů Blob do databáze SQL.
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
ms.openlocfilehash: 6575a68423df97d7727401b79ded44545a43afca
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621101"
---
# <a name="tutorial-copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Kurz: Kopírování dat z úložiště objektů Blob do SQL Database pomocí objektu pro vytváření dat
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
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verzi 2 služby Data Factory, který je ve verzi Preview, přečtěte si [kurz aktivity kopírování v dokumentaci verze 2](../quickstart-create-data-factory-dot-net.md). 

V tomto kurzu vytvoříte objekt pro vytváření dat kanál ke zkopírování dat z úložiště objektů Blob do databáze SQL.

Aktivita kopírování provádí přesun dat ve službě Azure Data Factory. Používá globálně dostupnou službu, která může kopírovat data mezi různými úložišti dat zabezpečeným, spolehlivým a škálovatelným způsobem. Podrobnosti o aktivitě kopírování najdete v článku [Aktivity přesunu dat](data-factory-data-movement-activities.md).  

> [!NOTE]
> Podrobný přehled služby Data Factory najdete [Úvod do Azure Data Factory](data-factory-introduction.md) článku.
>
>

## <a name="prerequisites-for-the-tutorial"></a>Předpoklady pro kurz
Je nutné, abyste před zahájením tohoto kurzu splňovali následující požadavky:

* **Předplatné Azure**.  Pokud nemáte předplatné, můžete si během několika minut bezplatně vytvořit zkušební účet. Najdete v článku [bezplatné zkušební verze](http://azure.microsoft.com/pricing/free-trial/) článku.
* **Účet úložiště Azure**. Použít jako úložiště objektů blob **zdroj** úložiště dat v tomto kurzu. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-create-storage-account.md#create-a-storage-account), kde najdete kroky pro jeho vytvoření.
* **Azure SQL Database**. Použít databázi Azure SQL jako **cílové** úložiště dat v tomto kurzu. Pokud nemáte Azure SQL database, můžete použít v tomto kurzu, najdete v tématu [jak vytvořit a nakonfigurovat Azure SQL Database](../../sql-database/sql-database-get-started.md) k jeho vytvoření.
* **SQL Server 2012 nebo 2014 nebo Visual Studio 2013**. Používáte SQL Server Management Studio nebo Visual Studio k vytvoření ukázkové databáze a k zobrazení výsledných dat v databázi.  

## <a name="collect-blob-storage-account-name-and-key"></a>Shromažďovat název účtu úložiště objektů blob a klíč
Potřebujete název účtu a klíč účtu vašeho účtu úložiště Azure uděláte v tomto kurzu. Poznamenejte si **název účtu** a **klíč účtu** pro váš účet úložiště Azure.

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).
2. Klikněte na tlačítko **všechny služby** v levé nabídce a vyberte **účty úložiště**.

    ![Procházet - účty úložiště](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png)
3. V **účty úložiště** okně, vyberte **účtu úložiště Azure** , kterou chcete použít v tomto kurzu.
4. Vyberte **přístupové klíče** v části **nastavení**.
5. Klikněte na tlačítko **kopie** tlačítko (obrázek) vedle **název účtu úložiště** text pole a uložit a vložte ji někam (například: do textového souboru).
6. Opakujte předchozí krok zkopírovat nebo poznamenejte **key1**.

    ![Přístupový klíč k úložišti](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png)
7. Zavřete všechna okna kliknutím **X**.

## <a name="collect-sql-server-database-user-names"></a>Shromažďování systému SQL server, databáze, uživatelská jména
Názvy serveru Azure SQL, databáze a uživatel mohl tohoto kurzu potřebujete. Zapište názvy **server**, **databáze**, a **uživatele** pro vaši databázi Azure SQL.

1. V **portál Azure**, klikněte na tlačítko **všechny služby** na levé straně a vyberte **databází SQL**.
2. V **okna databáze SQL**, vyberte **databáze** , kterou chcete použít v tomto kurzu. Poznamenejte si **název databáze**.  
3. V **databáze SQL** okně klikněte na tlačítko **vlastnosti** pod **nastavení**.
4. Zapište hodnoty **název serveru** a **přihlašovací jméno správce serveru**.
5. Zavřete všechna okna kliknutím **X**.

## <a name="allow-azure-services-to-access-sql-server"></a>Povolit službám Azure přístup k systému SQL server
Ujistěte se, že **povolit přístup ke službám Azure** nastavení zapnuté **ON** pro server Azure SQL tak, aby služba Data Factory přístup k serveru Azure SQL. Pokud chcete toto nastavení ověřit a zapnout, proveďte následující kroky:

1. Klikněte na tlačítko **všechny služby** rozbočovače na levé straně a klikněte na **servery SQL**.
2. Vyberte svůj server a v části **NASTAVENÍ** klikněte na **Brána firewall**.
3. V okně **Nastavení brány firewall** klikněte na **ZAPNUTO** u možnosti **Povolit přístup ke službám Azure**.
4. Zavřete všechna okna kliknutím **X**.

## <a name="prepare-blob-storage-and-sql-database"></a>Příprava úložiště objektů Blob a databáze SQL
Nyní Příprava úložiště objektů blob v Azure a Azure SQL database pro tento kurz provedením následujících kroků:  

1. Spusťte Poznámkový blok. Zkopírujte následující text a uložte ho jako **emp.txt** k **C:\ADFGetStarted** složky na pevném disku.

    ```
    John, Doe
    Jane, Doe
    ```
2. Pomocí nástrojů, jako je [Azure Storage Explorer](http://storageexplorer.com/), vytvořte kontejner **adftutorial** a odešlete soubor **emp.txt** do tohoto kontejneru.

    ![Azure Storage Explorer. Kopírování dat z úložiště objektů Blob do databáze SQL](./media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/getstarted-storage-explorer.png)
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

    **Pokud máte SQL Server 2012 nebo 2014 v počítači nainstalována:** postupujte podle pokynů v [Správa Azure SQL Database pomocí SQL Server Management Studio](../../sql-database/sql-database-manage-azure-ssms.md) pro připojení k serveru Azure SQL a spusťte skript SQL. 

    Pokud klient nemá povolený přístup ke službě Azure SQL Server, budete muset nakonfigurovat bránu firewall pro Azure SQL Server tak, aby povolovala přístup z vašeho počítače (IP adresa). Postup konfigurace brány firewall pro server SQL Azure najdete v [tomto článku](../../sql-database/sql-database-configure-firewall-settings.md).

## <a name="create-a-data-factory"></a>Vytvoření datové továrny
Dokončili jste požadavky. Můžete vytvořit objekt pro vytváření dat pomocí jedné z následujících způsobů. Klikněte na jednu z možností v rozevíracím seznamu v horní části nebo následující odkazy k provedení tohoto kurzu.     

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
