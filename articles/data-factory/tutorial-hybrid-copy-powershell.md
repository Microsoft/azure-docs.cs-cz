---
title: Kopírování dat z SQL Server do úložiště objektů BLOB pomocí PowerShellu
description: Zjistěte, jak kopírovat data z místního úložiště dat do cloudu Azure s využitím místního prostředí Integration Runtime ve službě Azure Data Factory.
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019, devx-track-azurepowershell
ms.date: 02/18/2021
ms.openlocfilehash: 33c3138013c43062465cce16189e6c19d22ae4d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101724927"
---
# <a name="tutorial-copy-data-from-a-sql-server-database-to-azure-blob-storage"></a>Kurz: kopírování dat z databáze SQL Server do úložiště objektů BLOB v Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

V tomto kurzu použijete Azure PowerShell k vytvoření kanálu datové továrny, který kopíruje data z databáze SQL Server do úložiště objektů BLOB v Azure. Vytvoříte a použijete místní prostředí Integration Runtime, které přesouvá data mezi místním a cloudovým úložištěm dat.

> [!NOTE]
> Tento článek neposkytuje podrobný úvod do služby Data Factory. Další informace najdete v tématu [Úvod do Azure Data Factory](introduction.md).

V tomto kurzu budete provádět následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvořte místní prostředí Integration Runtime.
> * Vytvoření propojených služeb SQL Server a Azure Storage
> * Vytvoření datových sad SQL Serveru a Azure Blob
> * Vytvoření kanálu s aktivitou kopírování pro přesun dat
> * Zahajte spuštění kanálu.
> * Monitorování spuštění kanálu

## <a name="prerequisites"></a>Požadavky
### <a name="azure-subscription"></a>Předplatné Azure
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

### <a name="azure-roles"></a>Role Azure
Pro vytvoření instancí služby Data Factory musí být uživatelský účet, který použijete pro přihlášení k Azure, přiřazený k roli *Přispěvatel* nebo *vlastník* nebo musí být *správcem* předplatného Azure.

Pokud chcete zobrazit oprávnění, která v předplatném máte, přejděte na web Azure Portal, v pravém horním rohu vyberte své uživatelské jméno a pak vyberte **Oprávnění**. Pokud máte přístup k několika předplatným, vyberte odpovídající předplatné. Ukázku pokynů pro přidání uživatele k roli najdete v tématu [přiřazení rolí Azure pomocí Azure Portal](../role-based-access-control/role-assignments-portal.md) článku.

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 a 2017
V tomto kurzu použijete databázi SQL Server jako *zdrojové* úložiště dat. Kanál v datové továrně, který vytvoříte v tomto kurzu, kopíruje data z této SQL Server databáze (zdroj) do úložiště objektů BLOB v Azure (jímka). Ve své databázi SQL Serveru pak vytvoříte tabulku **emp** a vložíte do ní několik ukázkových záznamů.

1. Spusťte aplikaci SQL Server Management Studio. Pokud na vašem počítači ještě není nainstalovaná, přejděte na stránku pro [stažení aplikace SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

1. Pomocí svých přihlašovacích údajů se přihlaste ke své instanci SQL Serveru.

1. Vytvořte ukázkovou databázi. Ve stromovém zobrazení klikněte pravým tlačítkem na **Databáze** a pak vyberte **Nová databáze**.

1. V okně **Nová databáze** zadejte název databáze a pak vyberte **OK**.

1. Vytvořte tabulku **emp** a vložte do ní nějaká ukázková data spuštěním následujícího skriptu dotazu proti databázi. Ve stromovém zobrazení klikněte pravým tlačítkem na databázi, kterou jste vytvořili, a pak vyberte **Nový dotaz**.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
    ```


### <a name="azure-storage-account"></a>Účet služby Azure Storage
V tomto kurzu použijete účet úložiště Azure (konkrétně úložiště objektů blob v Azure) pro obecné účely jako cílové úložiště dat nebo úložiště dat jímky. Pokud nemáte účet úložiště Azure pro obecné účely, přečtěte si téma [Vytvoření účtu úložiště](../storage/common/storage-account-create.md). Kanál v datové továrně, který vytvoříte v tomto kurzu, kopíruje data z databáze SQL Server (zdroj) do tohoto úložiště objektů BLOB v Azure (jímka). 

#### <a name="get-storage-account-name-and-account-key"></a>Získání názvu a klíče účtu úložiště
V tomto kurzu použijete název a klíč svého účtu úložiště Azure. Získejte název a klíč vašeho účtu úložiště pomocí následujícího postupu:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí svého uživatelského jména a hesla Azure.

1. V levém podokně vyberte **Další služby**, proveďte filtrování pomocí klíčového slova **úložiště** a pak vyberte **Účty úložiště**.

    ![Vyhledání účtu úložiště](media/doc-common-process/search-storage-account.png)

1. V seznamu účtů úložiště vyfiltrujte váš účet úložiště (pokud je to potřeba) a pak vyberte váš účet úložiště.

1. V okně **Účet úložiště** vyberte **Přístupové klíče**.

1. Zkopírujte hodnoty polí **Název účtu úložiště** a **klíč1** a vložte je do Poznámkového bloku nebo jiného editoru pro pozdější použití v rámci kurzu.

#### <a name="create-the-adftutorial-container"></a>Vytvoření kontejneru adftutorial
V této části vytvoříte ve svém úložišti objektů BLOB v Azure kontejner objektů BLOB s názvem **adftutorial** .

1. V okně **Účet úložiště** přepněte na **Přehled** a pak vyberte **Objekty blob**.

    ![Výběr možnosti Objekty blob](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. V okně **Blob service** vyberte **Kontejner**.

1. V okně **Nový kontejner** do pole **Název** zadejte **adftutorial** a pak vyberte **OK**.

    ![Zadání názvu kontejneru](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

1. V seznamu kontejnerů vyberte **adftutorial**.  

1. Nechejte okno **kontejneru** pro **adftutorial** otevřené. Použijete ji k ověření výstupu na konci tohoto kurzu. Data Factory v tomto kontejneru vytvoří výstupní složku automaticky, takže ji nemusíte vytvářet.


### <a name="windows-powershell"></a>Windows PowerShell

#### <a name="install-azure-powershell"></a>Instalace prostředí Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pokud jej ve svém počítači ještě nemáte, nainstalujte nejnovější verzi Azure PowerShellu. Podrobné pokyny najdete v tématu [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/install-Az-ps).

#### <a name="log-in-to-powershell"></a>Přihlášení do PowerShellu

1. Spusťte na svém počítači PowerShell a nechte ho otevřený do konce tohoto kurzu Rychlý start. Pokud ho zavřete a znovu otevřete, tyto příkazy budete muset spustit znovu.

1. Spusťte následující příkaz a pak zadejte uživatelské jméno a heslo Azure, které používáte pro přihlášení k webu Azure Portal:

    ```powershell
    Connect-AzAccount
    ```        

1. Pokud máte několik předplatných Azure, spuštěním následujícího příkazu vyberte předplatné, se kterým chcete pracovat. Nahraďte **SubscriptionId** číslem ID vašeho předplatného Azure:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"    
    ```

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Definujte proměnnou pro název skupiny prostředků, kterou použijete později v příkazech PowerShellu. Zkopírujte do PowerShellu následující příkaz, zadejte název [skupiny prostředků Azure](../azure-resource-manager/management/overview.md) (v uvozovkách, například `"adfrg"`) a pak příkaz spusťte. 
   
    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ```

1. Pokud chcete vytvořit skupinu prostředků Azure, spusťte následující příkaz:

    ```powershell
    New-AzResourceGroup $resourceGroupName -location 'East US'
    ```

    Pokud již skupina prostředků existuje, nepřepisujte ji. Přiřaďte proměnné `$resourceGroupName` jinou hodnotu a spusťte tento příkaz znovu.

1. Definujte proměnnou pro název datové továrny, kterou můžete později použít v příkazech prostředí PowerShell. Název musí začínat písmenem nebo číslicí a může obsahovat pouze písmena, číslice a spojovníky (-).

    > [!IMPORTANT]
    >  Aktualizujte název datové továrny tak, aby byl globálně jedinečný. Například ADFTutorialFactorySP1127.

    ```powershell
    $dataFactoryName = "ADFTutorialFactory"
    ```

1. Definujte proměnnou pro umístění datové továrny:

    ```powershell
    $location = "East US"
    ```  

1. Vytvořte datovou továrnu spuštěním následující rutiny `Set-AzDataFactoryV2`:

    ```powershell       
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName
    ```

> [!NOTE]
>
> * Název datové továrny musí být globálně jedinečný. Pokud se zobrazí následující chyba, změňte název a zkuste to znovu.
>    ```
>    The specified data factory name 'ADFv2TutorialDataFactory' is already in use. Data factory names must be globally unique.
>    ```
> * Pro vytvoření instancí datové továrny musí mít uživatelský účet, který použijete pro přihlášení k Azure, přiřazenou roli *přispěvatel* nebo *vlastník* předplatného Azure nebo musí být jeho *správcem*.
> * Pokud chcete zobrazit seznam oblastí Azure, ve kterých je služba Data Factory aktuálně dostupná, na následující stránce vyberte oblasti, které vás zajímají, pak rozbalte **Analýza** a vyhledejte **Data Factory:**[Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/). Úložiště dat (Azure Storage, Azure SQL Database atd.) a výpočetní prostředí (Azure HDInsight atd.) používané datovou továrnou můžou být v jiných oblastech.
>
>

## <a name="create-a-self-hosted-integration-runtime"></a>Vytvoření místního prostředí Integration Runtime

V této části vytvoříte místní prostředí Integration Runtime a přidružíte ho k místnímu počítači s databází SQL Serveru. Místní prostředí Integration Runtime je komponenta, která kopíruje data z databáze SQL Serveru ve vašem počítači do úložiště objektů blob v Azure.

1. Vytvořte proměnnou pro název prostředí Integration Runtime. Použijte jedinečný název, který si poznamenejte. Použijete ho později v tomto kurzu.

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```

1. Vytvořte místní prostředí Integration Runtime.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $integrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```

    Zde je ukázkový výstup:

    ```console
    Name              : ADFTutorialIR
    Type              : SelfHosted
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Description       : selfhosted IR description
    Id                : /subscriptions/<subscription ID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/integrationruntimes/<integrationRuntimeName>
    ```

1. Pokud chcete načíst stav vytvořeného prostředí Integration Runtime, spusťte následující příkaz:

    ```powershell
   Get-AzDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
    ```

    Zde je ukázkový výstup:

    ```console
    State                     : NeedRegistration
    Version                   :
    CreateTime                : 9/10/2019 3:24:09 AM
    AutoUpdate                : On
    ScheduledUpdateDate       :
    UpdateDelayOffset         :
    LocalTimeZoneOffset       :
    InternalChannelEncryption :
    Capabilities              : {}
    ServiceUrls               : {eu.frontend.clouddatahub.net}
    Nodes                     : {}
    Links                     : {}
    Name                      : <Integration Runtime name>
    Type                      : SelfHosted
    ResourceGroupName         : <resourceGroup name>
    DataFactoryName           : <dataFactory name>
    Description               : selfhosted IR description
    Id                        : /subscriptions/<subscription ID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/integrationruntimes/<integrationRuntimeName>
    ```

1. Spuštěním následujícího příkazu načtěte *ověřovací klíče* pro registraci místního prostředí Integration Runtime ve službě Data Factory v cloudu. Pro registraci místního prostředí Integration Runtime, které nainstalujete na počítači v dalším kroku, zkopírujte jeden z klíčů (bez uvozovek).

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
    ```

    Zde je ukázkový výstup:

    ```json
    {
        "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
        "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
    }
    ```

## <a name="install-the-integration-runtime"></a>Instalace prostředí Integration Runtime
1. Na místním počítači s Windows stáhněte [prostředí Integration Runtime služby Azure Data Factory](https://www.microsoft.com/download/details.aspx?id=39717) a spusťte instalaci.

1. V průvodci **Vítá vás instalace prostředí Microsoft Integration Runtime** vyberte **Další**.  

1. V okně **Licenční smlouva s koncovým uživatelem (EULA)** přijměte podmínky a licenční smlouvu a vyberte **Další**.

1. V okně **Cílová složka** vyberte **Další**.

1. V okně **Připraveno k instalaci prostředí Microsoft Integration Runtime** vyberte **Nainstalovat**.

1. V průvodci **Dokončení instalace prostředí Microsoft Integration Runtime** vyberte **Dokončit**.

1. V okně **Registrace prostředí Integration Runtime (v místním prostředí)** vložte klíč, který jste uložili v předchozí části, a pak vyberte **Zaregistrovat**.

    ![Registrace modulu runtime integrace](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)

1. V okně **nový Integration runtime (v místním prostředí) uzel** vyberte **Dokončit**.

    ![Okno Nový uzel prostředí Integration Runtime](media/tutorial-hybrid-copy-powershell/new-integration-runtime-node-page.png)

 1. Po úspěšném dokončení registrace místního prostředí Integration Runtime se zobrazí následující zpráva:

    ![Úspěšně zaregistrováno](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

1. V okně **Registrace prostředí Integration Runtime (v místním prostředí)** vyberte **Spustit Správce konfigurace**.

1. Jakmile se uzel připojí ke cloudové službě, zobrazí se následující zpráva:

    ![Uzel je připojen](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

1. Pomocí následujícího postupu otestujte připojení k databázi SQL Serveru:

    a. V okně **Configuration Manager** přepněte na kartu **Diagnostika** .

    b. V poli **Typ zdroje dat** vyberte **SqlServer**.

    c. Zadejte název serveru.

    d. Zadejte název databáze.

    e. Vyberte režim ověřování.

    f. Zadejte uživatelské jméno.

    například Zadejte heslo přidružené k tomuto uživatelskému jménu.

    h. Pokud chcete potvrdit, že se prostředí Integration Runtime může připojit k SQL Serveru, klikněte na **Test**.  
    ![Připojení bylo úspěšné.](media/tutorial-hybrid-copy-powershell/config-manager-diagnostics-tab.png)

    Pokud je připojení úspěšné, zobrazí se zelená ikona zaškrtnutí. V opačném případě se zobrazí chybová zpráva přidružená k tomuto selhání. Opravte všechny problémy a ověřte, že se prostředí Integration Runtime může připojit k vaší instanci SQL Serveru.

    Všechny předchozí hodnoty si poznamenejte pro pozdější použití v rámci tohoto kurzu.

## <a name="create-linked-services"></a>Vytvoření propojených služeb
Vytvořte v datové továrně propojené služby, abyste svá úložiště dat a výpočetní služby propojili s datovou továrnou. V tomto kurzu propojíte účet služby Azure Storage a instanci SQL Server s úložištěm dat. Propojené služby mají informace o připojení, které služba Data Factory používá pro připojení za běhu.

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Vytvoření propojené služby Azure Storage (cíl/jímka)
V tomto kroku s datovou továrnou propojíte svůj účet úložiště Azure.

1. Ve složce *C:\ADFv2Tutorial* vytvořte soubor JSON s názvem *AzureStorageLinkedService.json* s následujícím kódem. Pokud složka *ADFv2Tutorial* neexistuje, vytvořte ji.  

    > [!IMPORTANT]
    > Než soubor uložíte, nahraďte \<accountName> a \<accountKey> názvem a klíčem účtu úložiště Azure. Tyto hodnoty jste si poznamenali v části [Požadavky](#get-storage-account-name-and-account-key).

   ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "annotations": [],
            "type": "AzureBlobStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net"
            }
        }
    }
   ```

1. V PowerShellu přejděte do složky *C:\ADFv2Tutorial*.
   ```powershell
   Set-Location 'C:\ADFv2Tutorial'    
   ```

1. Spuštěním následující rutiny `Set-AzDataFactoryV2LinkedService` vytvořte propojenou službu AzureStorageLinkedService:

   ```powershell
   Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
   ```

   Zde je ukázkový výstup:

    ```console
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroup name>
    DataFactoryName   : <dataFactory name>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobStorageLinkedService
    ```

    Pokud se zobrazí chyba Soubor nenalezen, spusťte příkaz `dir` a ověřte, že soubor existuje. Pokud má název souboru příponu *.txt* (například AzureStorageLinkedService.json.txt), odeberte ji a pak spusťte příkaz PowerShellu znovu.

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>Vytvoření a šifrování propojené služby SQL Serveru (zdroj)
V tomto kroku propojíte instanci SQL Server s datovou továrnou.

1. Ve složce *C:\ADFv2Tutorial* vytvořte soubor JSON s názvem *SQLServerLinkedService.json* s následujícím kódem:

    > [!IMPORTANT]
    > Vyberte odpovídající část na základě ověřování, které používáte pro připojení k SQL Serveru.

    **Ověřování SQL (sa):**

    ```json
    {  
        "name":"SqlServerLinkedService",
        "type":"Microsoft.DataFactory/factories/linkedservices",
        "properties":{  
            "annotations":[  

            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=False;data source=<serverName>;initial catalog=<databaseName>;user id=<userName>;password=<password>"
            },
            "connectVia":{  
                "referenceName":"<integration runtime name> ",
                "type":"IntegrationRuntimeReference"
            }
        }
    }
   ```    

    **Ověřování systému Windows:**

    ```json
    {  
        "name":"SqlServerLinkedService",
        "type":"Microsoft.DataFactory/factories/linkedservices",
        "properties":{  
            "annotations":[  

            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=True;data source=<serverName>;initial catalog=<databaseName>",
                "userName":"<username> or <domain>\\<username>",
                "password":{  
                    "type":"SecureString",
                    "value":"<password>"
                }
            },
            "connectVia":{  
                "referenceName":"<integration runtime name>",
                "type":"IntegrationRuntimeReference"
            }
        }
    }
    ```

    > [!IMPORTANT]
    > - Vyberte odpovídající část na základě ověřování, které používáte pro připojení k vaší instanci SQL Serveru.
    > - Nahraďte  **\<integration runtime name>** názvem vašeho prostředí Integration runtime.
    > - Před uložením souboru, nahraďte **\<servername>** , **\<databasename>** , **\<username>** a **\<password>** s hodnotami vaší instance SQL Server.
    > - Pokud v názvu uživatelského účtu nebo serveru potřebujete použít zpětné lomítko (\\), vložte před něj řídicí znak (\\). Použijte například *MyDomain \\ \\ MyUser*.

1. Pokud chcete šifrovat citlivá data (uživatelské jméno, heslo atd.), spusťte rutinu `New-AzDataFactoryV2LinkedServiceEncryptedCredential`.  
    Toto šifrování zajišťuje šifrování přihlašovacích údajů pomocí rozhraní Data Protection API. Zašifrované přihlašovací údaje jsou uložené místně v uzlu místního prostředí Integration Runtime (místní počítač). Výstupní datovou část je možné přesměrovat do jiného souboru JSON (v tomto případě *encryptedLinkedService.json*), který obsahuje zašifrované přihlašovací údaje.

   ```powershell
   New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

1. Spusťte následující příkaz, který vytvoří EncryptedSqlServerLinkedService:

   ```powershell
   Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>Vytvoření datových sad
V tomto kroku vytvoříte vstupní a výstupní datové sady. Představují vstupní a výstupní data pro operaci kopírování, která kopíruje data z databáze SQL Server do úložiště objektů BLOB v Azure.

### <a name="create-a-dataset-for-the-source-sql-server-database"></a>Vytvoření datové sady pro zdrojovou databázi SQL Serveru
V tomto kroku definujete datovou sadu, která představuje data v instanci databáze SQL Serveru. Tato datová sada je typu SqlServerTable. Odkazuje na propojenou službu SQL Serveru, kterou jste vytvořili v předchozím kroku. Propojená služba má informace o připojení, které služba Data Factory používá pro připojení k vaší instanci SQL Serveru za běhu. Tato datová sada určuje tabulku SQL v databázi, která obsahuje data. V tomto kurzu zdrojová data obsahuje tabulka **emp**.

1. Ve složce *C:\ADFv2Tutorial* vytvořte soubor JSON s názvem *SqlServerDataset.json* s následujícím kódem:  
    ```json
    {  
        "name":"SqlServerDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"EncryptedSqlServerLinkedService",
                "type":"LinkedServiceReference"
            },
            "annotations":[  

            ],
            "type":"SqlServerTable",
            "schema":[  

            ],
            "typeProperties":{  
                "schema":"dbo",
                "table":"emp"
            }
        }
    }
    ```

1. Datovou sadu SqlServerDataset vytvoříte spuštěním rutiny `Set-AzDataFactoryV2Dataset`.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File ".\SqlServerDataset.json"
    ```

    Zde je ukázkový výstup:

    ```console
    DatasetName       : SqlServerDataset
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-dataset-for-azure-blob-storage-sink"></a>Vytvoření datové sady pro úložiště objektů blob v Azure (jímka)
V tomto kroku definujete datovou sadu představující data, která se mají zkopírovat do úložiště objektů blob v Azure. Tato datová sada je typu AzureBlob. Odkazuje na propojenou službu Azure Storage, kterou jste vytvořili dříve v tomto kurzu.

Propojená služba má informace o připojení, které datová továrna používá pro připojení k vašemu účtu úložiště Azure za běhu. Tato datová sada určuje složku v úložišti Azure, do které se kopírují data z databáze SQL Serveru. V tomto kurzu je touto složkou *adftutorial/fromonprem*, kde `adftutorial` je kontejner objektů blob a `fromonprem` je složka.

1. Ve složce *C:\ADFv2Tutorial* vytvořte soubor JSON s názvem *AzureBlobDataset.json* s následujícím kódem:

    ```json
    {  
        "name":"AzureBlobDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"AzureStorageLinkedService",
                "type":"LinkedServiceReference"
            },
            "annotations":[  

            ],
            "type":"DelimitedText",
            "typeProperties":{  
                "location":{  
                    "type":"AzureBlobStorageLocation",
                    "folderPath":"fromonprem",
                    "container":"adftutorial"
                },
                "columnDelimiter":",",
                "escapeChar":"\\",
                "quoteChar":"\""
            },
            "schema":[  

            ]
        },
        "type":"Microsoft.DataFactory/factories/datasets"
    }
    ```

1. Datovou sadu AzureBlobDataset vytvoříte spuštěním rutiny `Set-AzDataFactoryV2Dataset`.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File ".\AzureBlobDataset.json"
    ```

    Zde je ukázkový výstup:

    ```console
    DatasetName       : AzureBlobDataset
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.DelimitedTextDataset
    ```

## <a name="create-a-pipeline"></a>Vytvoření kanálu
V tomto kurzu pomocí aktivity kopírování vytvoříte kanál. Aktivita kopírování používá SqlServerDataset jako vstupní datovou sadu a AzureBlobDataset jako výstupní datovou sadu. Typ zdroje je nastavený na *SqlSource* a typ jímky je nastavený na *BlobSink*.

1. Ve složce *C:\ADFv2Tutorial* vytvořte soubor JSON s názvem *SqlServerToBlobPipeline.json* s následujícím kódem:

    ```json
    {  
        "name":"SqlServerToBlobPipeline",
        "properties":{  
            "activities":[  
                {  
                    "name":"CopySqlServerToAzureBlobActivity",
                    "type":"Copy",
                    "dependsOn":[  

                    ],
                    "policy":{  
                        "timeout":"7.00:00:00",
                        "retry":0,
                        "retryIntervalInSeconds":30,
                        "secureOutput":false,
                        "secureInput":false
                    },
                    "userProperties":[  

                    ],
                    "typeProperties":{  
                        "source":{  
                            "type":"SqlServerSource"
                        },
                        "sink":{  
                            "type":"DelimitedTextSink",
                            "storeSettings":{  
                                "type":"AzureBlobStorageWriteSettings"
                            },
                            "formatSettings":{  
                                "type":"DelimitedTextWriteSettings",
                                "quoteAllText":true,
                                "fileExtension":".txt"
                            }
                        },
                        "enableStaging":false
                    },
                    "inputs":[  
                        {  
                            "referenceName":"SqlServerDataset",
                            "type":"DatasetReference"
                        }
                    ],
                    "outputs":[  
                        {  
                            "referenceName":"AzureBlobDataset",
                            "type":"DatasetReference"
                        }
                    ]
                }
            ],
            "annotations":[  

            ]
        }
    }
    ```

1. Kanál SQLServerToBlobPipeline vytvoříte spuštěním následující rutiny `Set-AzDataFactoryV2Pipeline`.

    ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SQLServerToBlobPipeline" -File ".\SQLServerToBlobPipeline.json"
    ```

    Zde je ukázkový výstup:

    ```console
    PipelineName      : SQLServerToBlobPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopySqlServerToAzureBlobActivity}
    Parameters        :  
    ```

## <a name="create-a-pipeline-run"></a>Vytvoření spuštění kanálu
Zahajte spuštění pro kanál SQLServerToBlobPipeline a zaznamenejte ID spuštění kanálu pro budoucí monitorování.

```powershell
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
```

## <a name="monitor-the-pipeline-run"></a>Monitorování spuštění kanálu

1. Pokud chcete nepřetržitě kontrolovat stav spuštění kanálu SQLServerToBlobPipeline, spusťte v PowerShellu následující skript a vytiskne konečný výsledek:

    ```powershell
    while ($True) {
        $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
            Start-Sleep -Seconds 30
        }
        else {
            Write-Host "Pipeline 'SQLServerToBlobPipeline' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
    }
    ```

    Zde je výstup tohoto ukázkového spuštění:

    ```console
    ResourceGroupName    : <resourceGroupName>
    DataFactoryName      : <dataFactoryName>
    ActivityRunId        : 24af7cf6-efca-4a95-931d-067c5c921c25
    ActivityName         : CopySqlServerToAzureBlobActivity
    ActivityType         : Copy
    PipelineRunId        : 7b538846-fd4e-409c-99ef-2475329f5729
    PipelineName         : SQLServerToBlobPipeline
    Input                : {source, sink, enableStaging}
    Output               : {dataRead, dataWritten, filesWritten, sourcePeakConnections...}
    LinkedServiceName    :
    ActivityRunStart     : 9/11/2019 7:10:37 AM
    ActivityRunEnd       : 9/11/2019 7:10:58 AM
    DurationInMs         : 21094
    Status               : Succeeded
    Error                : {errorCode, message, failureType, target}
    AdditionalProperties : {[retryAttempt, ], [iterationHash, ], [userProperties, {}], [recoveryStatus, None]...}
    ```

1. Spuštěním následujícího příkazu můžete získat ID spuštění kanálu SQLServerToBlobPipeline a zkontrolovat podrobné výsledky spuštění aktivit:

    ```powershell
    Write-Host "Pipeline 'SQLServerToBlobPipeline' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "CopySqlServerToAzureBlobActivity"}).Output.ToString()
    ```

    Zde je výstup tohoto ukázkového spuštění:

    ```json
    {  
        "dataRead":36,
        "dataWritten":32,
        "filesWritten":1,
        "sourcePeakConnections":1,
        "sinkPeakConnections":1,
        "rowsRead":2,
        "rowsCopied":2,
        "copyDuration":18,
        "throughput":0.01,
        "errors":[  

        ],
        "effectiveIntegrationRuntime":"ADFTutorialIR",
        "usedParallelCopies":1,
        "executionDetails":[  
            {  
                "source":{  
                    "type":"SqlServer"
                },
                "sink":{  
                    "type":"AzureBlobStorage",
                    "region":"CentralUS"
                },
                "status":"Succeeded",
                "start":"2019-09-11T07:10:38.2342905Z",
                "duration":18,
                "usedParallelCopies":1,
                "detailedDurations":{  
                    "queuingDuration":6,
                    "timeToFirstByte":0,
                    "transferDuration":5
                }
            }
        ]
    }
    ```

## <a name="verify-the-output"></a>Ověření výstupu
Kanál v kontejneru objektů blob `adftutorial` automaticky vytvoří výstupní složku *fromonprem*. Zkontrolujte, že výstupní složka obsahuje soubor *dbo.emp.txt*.

1. Na webu Azure Portal v okně kontejneru **adftutorial** vyberte **Obnovit**. Zobrazí se výstupní složka.
1. V seznamu složek vyberte `fromonprem`.
1. Potvrďte, že se zobrazuje soubor s názvem `dbo.emp.txt`.

    ![Výstupní soubor](media/tutorial-hybrid-copy-powershell/fromonprem-file.png)


## <a name="next-steps"></a>Další kroky
Kanál v této ukázce kopíruje data z jednoho umístění do jiného v úložišti objektů blob v Azure. Naučili jste se:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvořte místní prostředí Integration Runtime.
> * Vytvoření propojených služeb SQL Server a Azure Storage
> * Vytvoření datových sad SQL Serveru a Azure Blob
> * Vytvoření kanálu s aktivitou kopírování pro přesun dat
> * Zahajte spuštění kanálu.
> * Monitorování spuštění kanálu

Seznam úložišť dat podporovaných nástrojem Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).

Pokud se chcete dozvědět víc o hromadném kopírování dat ze zdroje do cíle, přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Hromadné kopírování dat](tutorial-bulk-copy.md)