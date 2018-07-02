---
title: Kopírování dat z SQL Serveru do úložiště objektů blob pomocí Azure Data Factory | Microsoft Docs
description: Zjistěte, jak kopírovat data z místního úložiště dat do cloudu Azure s využitím místního prostředí Integration Runtime ve službě Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: cfbd7080ff365daa15618b93dcfb9fd2d07aacff
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37046574"
---
# <a name="tutorial-copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>Kurz: Kopírování dat z místní databáze SQL Serveru do úložiště objektů blob v Azure
V tomto kurzu použijete Azure PowerShell k vytvoření kanálu datové továrny, který kopíruje data z místní databáze SQL Serveru do úložiště objektů blob v Azure. Vytvoříte a použijete místní prostředí Integration Runtime, které přesouvá data mezi místním a cloudovým úložištěm dat. 

> [!NOTE]
> Tento článek neposkytuje podrobný úvod do služby Data Factory. Další informace najdete v tématu [Úvod do Azure Data Factory](introduction.md). 

V tomto kurzu budete provádět následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvořte místní prostředí Integration Runtime.
> * Vytvoření propojených služeb SQL Server a Azure Storage 
> * Vytvoření datových sad SQL Serveru a Azure Blob
> * Vytvoření kanálu s aktivitou kopírování pro přesun dat
> * Zahájení spuštění kanálu
> * Monitorování spuštění kanálu

## <a name="prerequisites"></a>Požadavky
### <a name="azure-subscription"></a>Předplatné Azure
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

### <a name="azure-roles"></a>Role Azure
Pro vytvoření instancí datové továrny musí mít uživatelský účet, který použijete pro přihlášení k Azure, přiřazenou roli *přispěvatel* nebo *vlastník* předplatného Azure nebo musí být jeho *správcem*. 

Pokud chcete zobrazit oprávnění, která v předplatném máte, přejděte na web Azure Portal, v pravém horním rohu vyberte své uživatelské jméno a pak vyberte **Oprávnění**. Pokud máte přístup k několika předplatným, vyberte odpovídající předplatné. Ukázku pokynů pro přidání uživatele k roli najdete v článku věnovaném [přidání rolí](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 a 2017
V tomto kurzu použijete místní databázi SQL Serveru jako *zdrojové* úložiště dat. Kanál v datové továrně, který vytvoříte v tomto kurzu, kopíruje data z této místní databáze SQL Serveru (zdroj) do úložiště objektů blob v Azure (jímka). Ve své databázi SQL Serveru pak vytvoříte tabulku **emp** a vložíte do ní několik ukázkových záznamů. 

1. Spusťte aplikaci SQL Server Management Studio. Pokud na vašem počítači ještě není nainstalovaná, přejděte na stránku pro [stažení aplikace SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). 

2. Pomocí svých přihlašovacích údajů se přihlaste ke své instanci SQL Serveru. 

3. Vytvořte ukázkovou databázi. Ve stromovém zobrazení klikněte pravým tlačítkem na **Databáze** a pak vyberte **Nová databáze**. 
 
4. V okně **Nová databáze** zadejte název databáze a pak vyberte **OK**. 

5. Vytvořte tabulku **emp** a vložte do ní nějaká ukázková data spuštěním následujícího skriptu dotazu proti databázi:

   ```
       INSERT INTO emp VALUES ('John', 'Doe')
       INSERT INTO emp VALUES ('Jane', 'Doe')
       GO
   ```

6. Ve stromovém zobrazení klikněte pravým tlačítkem na databázi, kterou jste vytvořili, a pak vyberte **Nový dotaz**.

### <a name="azure-storage-account"></a>Účet služby Azure Storage
V tomto kurzu použijete účet úložiště Azure (konkrétně úložiště objektů blob v Azure) pro obecné účely jako cílové úložiště dat nebo úložiště dat jímky. Pokud nemáte účet úložiště Azure pro obecné účely, přečtěte si téma [Vytvoření účtu úložiště](../storage/common/storage-create-storage-account.md#create-a-storage-account). Kanál v datové továrně, který vytvoříte v tomto kurzu, kopíruje data z místní databáze SQL Serveru (zdroj) do tohoto úložiště objektů blob v Azure (jímka). 

#### <a name="get-storage-account-name-and-account-key"></a>Získání názvu a klíče účtu úložiště
V tomto kurzu použijete název a klíč svého účtu úložiště Azure. Získejte název a klíč vašeho účtu úložiště pomocí následujícího postupu: 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí svého uživatelského jména a hesla Azure. 

2. V levém podokně vyberte **Další služby**, proveďte filtrování pomocí klíčového slova **úložiště** a pak vyberte **Účty úložiště**.

    ![Vyhledání účtu úložiště](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

3. V seznamu účtů úložiště vyfiltrujte váš účet úložiště (pokud je to potřeba) a pak vyberte váš účet úložiště. 

4. V okně **Účet úložiště** vyberte **Přístupové klíče**.

    ![Získání názvu a klíče účtu úložiště](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

5. Zkopírujte hodnoty polí **Název účtu úložiště** a **klíč1** a vložte je do Poznámkového bloku nebo jiného editoru pro pozdější použití v rámci kurzu. 

#### <a name="create-the-adftutorial-container"></a>Vytvoření kontejneru adftutorial 
V této části vytvoříte ve svém úložišti objektů blob v Azure kontejner objektů blob s názvem **adftutorial**. 

1. V okně **Účet úložiště** přepněte na **Přehled** a pak vyberte **Objekty blob**. 

    ![Výběr možnosti Objekty blob](media/tutorial-hybrid-copy-powershell/select-blobs.png)

2. V okně **Blob service** vyberte **Kontejner**. 

    ![Tlačítko pro přidání kontejneru](media/tutorial-hybrid-copy-powershell/add-container-button.png)

3. V okně **Nový kontejner** do pole **Název** zadejte **adftutorial** a pak vyberte **OK**. 

    ![Zadání názvu kontejneru](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

4. V seznamu kontejnerů vyberte **adftutorial**.  

    ![Výběr kontejneru](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

5. Okno **Kontejner** pro **adftutorial** nechte otevřené. Použijete ji k ověření výstupu na konci tohoto kurzu. Data Factory v tomto kontejneru vytvoří výstupní složku automaticky, takže ji nemusíte vytvářet.

    ![Okno Kontejner](media/tutorial-hybrid-copy-powershell/container-page.png)

### <a name="windows-powershell"></a>Windows PowerShell

#### <a name="install-azure-powershell"></a>Instalace prostředí Azure PowerShell
Pokud jej ve svém počítači ještě nemáte, nainstalujte nejnovější verzi Azure PowerShellu. 

1. Přejděte na stránku [Sady Azure SDK ke stažení](https://azure.microsoft.com/downloads/). 

2. V části **Nástroje příkazového řádku** > **PowerShell** vyberte **Instalace pro Windows**. 

3. Pokud chcete nainstalovat Azure PowerShell, spusťte soubor MSI. 

Podrobné pokyny najdete v tématu [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-powershell"></a>Přihlášení do PowerShellu

1. Spusťte na svém počítači PowerShell a nechte ho otevřený do konce tohoto kurzu Rychlý start. Pokud ho zavřete a znovu otevřete, tyto příkazy budete muset spustit znovu.

    ![Spuštění PowerShellu](media/tutorial-hybrid-copy-powershell/search-powershell.png)

2. Spusťte následující příkaz a pak zadejte uživatelské jméno a heslo Azure, které používáte pro přihlášení k webu Azure Portal:
       
    ```powershell
    Connect-AzureRmAccount
    ```        

3. Pokud máte několik předplatných Azure, spuštěním následujícího příkazu vyberte předplatné, se kterým chcete pracovat. Místo **SubscriptionId** použijte ID vašeho předplatného Azure:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Definujte proměnnou pro název skupiny prostředků, kterou použijete později v příkazech PowerShellu. Zkopírujte do PowerShellu následující příkaz, zadejte název [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md) (v uvozovkách, například `"adfrg"`) a pak příkaz spusťte. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ```

2. Pokud chcete vytvořit skupinu prostředků Azure, spusťte následující příkaz: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 

    Pokud již skupina prostředků existuje, nepřepisujte ji. Přiřaďte proměnné `$resourceGroupName` jinou hodnotu a spusťte tento příkaz znovu.

3. Definujte proměnnou pro název datové továrny, kterou můžete později použít v příkazech prostředí PowerShell. Název musí začínat písmenem nebo číslicí a může obsahovat pouze písmena, číslice a spojovníky (-).

    > [!IMPORTANT]
    >  Aktualizujte název datové továrny tak, aby byl globálně jedinečný. Například ADFTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFTutorialFactory"
    ```

4. Definujte proměnnou pro umístění datové továrny: 

    ```powershell
    $location = "East US"
    ```  

5. Vytvořte datovou továrnu spuštěním následující rutiny `Set-AzureRmDataFactoryV2`: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

> [!NOTE]
> 
> * Název datové továrny musí být globálně jedinečný. Pokud se zobrazí následující chyba, změňte název a zkuste to znovu.
>    ```
>    The specified data factory name 'ADFv2TutorialDataFactory' is already in use. Data factory names must be globally unique.
>    ```
> * Pro vytvoření instancí datové továrny musí mít uživatelský účet, který použijete pro přihlášení k Azure, přiřazenou roli *přispěvatel* nebo *vlastník* předplatného Azure nebo musí být jeho *správcem*.
> * V současné době můžete vytvářet datové továrny jenom v oblastech Východní USA, Východní USA 2 a Západní Evropa. Úložiště dat (Azure Storage, Azure SQL Database atd.) a výpočetní prostředí (Azure HDInsight atd.) používané datovou továrnou můžou být v jiných oblastech.
> 
> 

## <a name="create-a-self-hosted-integration-runtime"></a>Vytvoření místního prostředí Integration Runtime

V této části vytvoříte místní prostředí Integration Runtime a přidružíte ho k místnímu počítači s databází SQL Serveru. Místní prostředí Integration Runtime je komponenta, která kopíruje data z databáze SQL Serveru ve vašem počítači do úložiště objektů blob v Azure. 

1. Vytvořte proměnnou pro název prostředí Integration Runtime. Použijte jedinečný název, který si poznamenejte. Použijete ho později v tomto kurzu. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```

2. Vytvořte místní prostředí Integration Runtime. 

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $integrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ``` 
    Zde je ukázkový výstup:

    ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       : selfhosted IR description
    ```

3. Pokud chcete načíst stav vytvořeného prostředí Integration Runtime, spusťte následující příkaz:

    ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
    ```

    Zde je ukázkový výstup:
    
    ```json
    Nodes                     : {}
    CreateTime                : 9/14/2017 10:01:21 AM
    InternalChannelEncryption :
    Version                   :
    Capabilities              : {}
    ScheduledUpdateDate       :
    UpdateDelayOffset         :
    LocalTimeZoneOffset       :
    AutoUpdate                :
    ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
    ResourceGroupName         : <ResourceGroup name>
    DataFactoryName           : <DataFactory name>
    Name                      : <Integration Runtime name>
    State                     : NeedRegistration
    ```

4. Spuštěním následujícího příkazu načtěte *ověřovací klíče* pro registraci místního prostředí Integration Runtime ve službě Data Factory v cloudu. Pro registraci místního prostředí Integration Runtime, které nainstalujete na počítači v dalším kroku, zkopírujte jeden z klíčů (bez uvozovek). 

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
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

2. V průvodci **Vítá vás instalace prostředí Microsoft Integration Runtime** vyberte **Další**.  

3. V okně **Licenční smlouva s koncovým uživatelem (EULA)** přijměte podmínky a licenční smlouvu a vyberte **Další**. 

4. V okně **Cílová složka** vyberte **Další**. 

5. V okně **Připraveno k instalaci prostředí Microsoft Integration Runtime** vyberte **Nainstalovat**. 

6. Pokud se zobrazí zpráva upozorňující na konfiguraci přechodu počítače do režimu spánku nebo hibernace, když se zrovna nepoužívá, vyberte **OK**. 

7. Pokud se zobrazí okno **Možnosti napájení**, zavřete ho a přepněte do okna nastavení. 

8. V průvodci **Dokončení instalace prostředí Microsoft Integration Runtime** vyberte **Dokončit**.

9. V okně **Registrace prostředí Integration Runtime (v místním prostředí)** vložte klíč, který jste uložili v předchozí části, a pak vyberte **Zaregistrovat**. 

    ![Registrace prostředí Integration Runtime](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)

    Po úspěšném dokončení registrace místního prostředí Integration Runtime se zobrazí následující zpráva: 

    ![Úspěšně zaregistrováno](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

10. V okně **Nový uzel prostředí Integration Runtime (v místním prostředí)** vyberte **Další**. 

    ![Okno Nový uzel prostředí Integration Runtime](media/tutorial-hybrid-copy-powershell/new-integration-runtime-node-page.png)

11. V okně **Komunikační kanál intranetu** vyberte **Přeskočit**.  
    V prostředí Integration Runtime s více uzly můžete pro zabezpečení komunikace v rámci uzlů vybrat certifikaci TLS/SSL.

    ![Okno Komunikační kanál intranetu](media/tutorial-hybrid-copy-powershell/intranet-communication-channel-page.png)

12. V okně **Registrace prostředí Integration Runtime (v místním prostředí)** vyberte **Spustit Správce konfigurace**. 

13. Jakmile se uzel připojí ke cloudové službě, zobrazí se následující zpráva:

    ![Uzel je připojen](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

14. Pomocí následujícího postupu otestujte připojení k databázi SQL Serveru:

    ![Karta Diagnostika](media/tutorial-hybrid-copy-powershell/config-manager-diagnostics-tab.png)   

    a. V okně **Správce konfigurace** přepněte na kartu **Diagnostika**.

    b. V poli **Typ zdroje dat** vyberte **SqlServer**.

    c. Zadejte název serveru.

    d. Zadejte název databáze. 

    e. Vyberte režim ověřování. 

    f. Zadejte uživatelské jméno. 

    g. Zadejte heslo přidružené k tomuto uživatelskému jménu.

    h. Pokud chcete potvrdit, že se prostředí Integration Runtime může připojit k SQL Serveru, klikněte na **Test**.  
    Pokud je připojení úspěšné, zobrazí se zelená ikona zaškrtnutí. V opačném případě se zobrazí chybová zpráva přidružená k tomuto selhání. Opravte všechny problémy a ověřte, že se prostředí Integration Runtime může připojit k vaší instanci SQL Serveru.

    Všechny předchozí hodnoty si poznamenejte pro pozdější použití v rámci tohoto kurzu.
    
## <a name="create-linked-services"></a>Vytvoření propojených služeb
Vytvořte v datové továrně propojené služby, abyste svá úložiště dat a výpočetní služby propojili s datovou továrnou. V tomto kurzu propojíte s úložištěm dat svůj účet úložiště Azure a místní instanci SQL Serveru. Propojené služby mají informace o připojení, které služba Data Factory používá pro připojení za běhu. 

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Vytvoření propojené služby Azure Storage (cíl/jímka)
V tomto kroku s datovou továrnou propojíte svůj účet úložiště Azure.

1. Ve složce *C:\ADFv2Tutorial* vytvořte soubor JSON s názvem *AzureStorageLinkedService.json* s následujícím kódem. Pokud složka *ADFv2Tutorial* neexistuje, vytvořte ji.  

    > [!IMPORTANT]
    > Než soubor uložíte, položky \<accountName> a \<accountKey> nahraďte názvem svého účtu úložiště Azure a jeho klíčem. Tyto hodnoty jste si poznamenali v části [Požadavky](#get-storage-account-name-and-account-key).

   ```json
    {
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>;EndpointSuffix=core.windows.net"
                }
            }
        },
        "name": "AzureStorageLinkedService"
    }
   ```

2. V PowerShellu přejděte do složky *C:\ADFv2Tutorial*.

3. Spuštěním následující rutiny `Set-AzureRmDataFactoryV2LinkedService` vytvořte propojenou službu AzureStorageLinkedService: 

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
   ```

   Zde je ukázkový výstup:

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

    Pokud se zobrazí chyba Soubor nenalezen, spusťte příkaz `dir` a ověřte, že soubor existuje. Pokud má název souboru příponu *.txt* (například AzureStorageLinkedService.json.txt), odeberte ji a pak spusťte příkaz PowerShellu znovu. 

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>Vytvoření a šifrování propojené služby SQL Serveru (zdroj)
V tomto kroku s datovou továrnou propojíte místní instanci SQL Serveru.

1. Ve složce *C:\ADFv2Tutorial* vytvořte soubor JSON s názvem *SQLServerLinkedService.json* s následujícím kódem:

    > [!IMPORTANT]
    > Vyberte odpovídající část na základě ověřování, které používáte pro připojení k SQL Serveru.

    **Ověřování SQL (sa):**

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }
   ```    

    **Ověřování systému Windows:**

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<server>;Database=<database>;Integrated Security=True"
                },
                "userName": "<user> or <domain>\\<user>",
                "password": {
                    "type": "SecureString",
                    "value": "<password>"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }    
    ```

    > [!IMPORTANT]
    > - Vyberte odpovídající část na základě ověřování, které používáte pro připojení k vaší instanci SQL Serveru.
    > - Položku **\<integration runtime name>** nahraďte názvem vašeho prostředí Integration Runtime.
    > - Než soubor uložíte, položky **\<servername>**, **\<databasename>**, **\<username>**, a **\<password>** nahraďte odpovídajícími hodnotami pro vaši instanci SQL Serveru.
    > - Pokud v názvu uživatelského účtu nebo serveru potřebujete použít zpětné lomítko (\\), vložte před něj řídicí znak (\\). Použijte například *mydomain\\\\myuser*. 

2. Pokud chcete šifrovat citlivá data (uživatelské jméno, heslo atd.), spusťte rutinu `New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential`.  
    Toto šifrování zajišťuje šifrování přihlašovacích údajů pomocí rozhraní Data Protection API. Zašifrované přihlašovací údaje jsou uložené místně v uzlu místního prostředí Integration Runtime (místní počítač). Výstupní datovou část je možné přesměrovat do jiného souboru JSON (v tomto případě *encryptedLinkedService.json*), který obsahuje zašifrované přihlašovací údaje.
    
   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. Spusťte následující příkaz, který vytvoří EncryptedSqlServerLinkedService:

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>Vytvoření datových sad
V tomto kroku vytvoříte vstupní a výstupní datové sady. Ty představují vstupní a výstupní data pro operaci kopírování, která kopíruje data z místní databáze SQL Serveru do úložiště objektů blob v Azure.

### <a name="create-a-dataset-for-the-source-sql-server-database"></a>Vytvoření datové sady pro zdrojovou databázi SQL Serveru
V tomto kroku definujete datovou sadu, která představuje data v instanci databáze SQL Serveru. Tato datová sada je typu SqlServerTable. Odkazuje na propojenou službu SQL Serveru, kterou jste vytvořili v předchozím kroku. Propojená služba má informace o připojení, které služba Data Factory používá pro připojení k vaší instanci SQL Serveru za běhu. Tato datová sada určuje tabulku SQL v databázi, která obsahuje data. V tomto kurzu zdrojová data obsahuje tabulka **emp**. 

1. Ve složce *C:\ADFv2Tutorial* vytvořte soubor JSON s názvem *SqlServerDataset.json* s následujícím kódem:  

    ```json
    {
       "properties": {
            "type": "SqlServerTable",
            "typeProperties": {
                "tableName": "dbo.emp"
            },
            "structure": [
                 {
                    "name": "ID",
                    "type": "String"
                },
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "linkedServiceName": {
                "referenceName": "EncryptedSqlServerLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "SqlServerDataset"
    }
    ```

2. Datovou sadu SqlServerDataset vytvoříte spuštěním rutiny `Set-AzureRmDataFactoryV2Dataset`.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File ".\SqlServerDataset.json"
    ```

    Zde je ukázkový výstup:

    ```json
    DatasetName       : SqlServerDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         : {"name": "ID" "type": "String", "name": "FirstName" "type": "String", "name": "LastName" "type": "String"}
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-dataset-for-azure-blob-storage-sink"></a>Vytvoření datové sady pro úložiště objektů blob v Azure (jímka)
V tomto kroku definujete datovou sadu představující data, která se mají zkopírovat do úložiště objektů blob v Azure. Tato datová sada je typu AzureBlob. Odkazuje na propojenou službu Azure Storage, kterou jste vytvořili dříve v tomto kurzu. 

Propojená služba má informace o připojení, které datová továrna používá pro připojení k vašemu účtu úložiště Azure za běhu. Tato datová sada určuje složku v úložišti Azure, do které se kopírují data z databáze SQL Serveru. V tomto kurzu je touto složkou *adftutorial/fromonprem*, kde `adftutorial` je kontejner objektů blob a `fromonprem` je složka. 

1. Ve složce *C:\ADFv2Tutorial* vytvořte soubor JSON s názvem *AzureBlobDataset.json* s následujícím kódem:

    ```json
    {
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/fromonprem",
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "AzureBlobDataset"
    }
    ```

2. Datovou sadu AzureBlobDataset vytvoříte spuštěním rutiny `Set-AzureRmDataFactoryV2Dataset`.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File ".\AzureBlobDataset.json"
    ```

    Zde je ukázkový výstup:

    ```json
    DatasetName       : AzureBlobDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-a-pipeline"></a>Vytvoření kanálu
V tomto kurzu pomocí aktivity kopírování vytvoříte kanál. Aktivita kopírování používá SqlServerDataset jako vstupní datovou sadu a AzureBlobDataset jako výstupní datovou sadu. Typ zdroje je nastavený na *SqlSource* a typ jímky je nastavený na *BlobSink*.

1. Ve složce *C:\ADFv2Tutorial* vytvořte soubor JSON s názvem *SqlServerToBlobPipeline.json* s následujícím kódem:

    ```json
    {
       "name": "SQLServerToBlobPipeline",
        "properties": {
            "activities": [       
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource"
                        },
                        "sink": {
                            "type":"BlobSink"
                        }
                    },
                    "name": "CopySqlServerToAzureBlobActivity",
                    "inputs": [
                        {
                            "referenceName": "SqlServerDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "AzureBlobDataset",
                            "type": "DatasetReference"
                        }
                    ]
                }
            ]
        }
    }
    ```

2. Kanál SQLServerToBlobPipeline vytvoříte spuštěním následující rutiny `Set-AzureRmDataFactoryV2Pipeline`.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SQLServerToBlobPipeline" -File ".\SQLServerToBlobPipeline.json"
    ```

    Zde je ukázkový výstup:

    ```json
    PipelineName      : SQLServerToBlobPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Activities        : {CopySqlServerToAzureBlobActivity}
    Parameters        :  
    ```

## <a name="create-a-pipeline-run"></a>Vytvoření spuštění kanálu
Zahajte spuštění pro kanál SQLServerToBlobPipeline a zaznamenejte ID spuštění kanálu pro budoucí monitorování.

```powershell
$runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
```

## <a name="monitor-the-pipeline-run"></a>Monitorování spuštění kanálu

1. Pokud chcete nepřetržitě kontrolovat stav spuštění kanálu SQLServerToBlobPipeline, spusťte v PowerShellu následující skript a vytiskne konečný výsledek:

    ```powershell
    while ($True) {
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

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

    ```jdon
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : copy
    PipelineRunId     : 4ec8980c-62f6-466f-92fa-e69b10f33640
    PipelineName      : SQLServerToBlobPipeline
    Input             :  
    Output            :  
    LinkedServiceName :
    ActivityRunStart  : 9/13/2017 1:35:22 PM
    ActivityRunEnd    : 9/13/2017 1:35:42 PM
    DurationInMs      : 20824
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

2. Spuštěním následujícího příkazu můžete získat ID spuštění kanálu SQLServerToBlobPipeline a zkontrolovat podrobné výsledky spuštění aktivit: 

    ```powershell
    Write-Host "Pipeline 'SQLServerToBlobPipeline' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "CopySqlServerToAzureBlobActivity"}).Output.ToString()
    ```

    Zde je výstup tohoto ukázkového spuštění:

    ```json
    {
      "dataRead": 36,
      "dataWritten": 24,
      "rowsCopied": 2,
      "copyDuration": 3,
      "throughput": 0.01171875,
      "errors": [],
      "effectiveIntegrationRuntime": "MyIntegrationRuntime",
      "billedDuration": 3
    }
    ```

## <a name="verify-the-output"></a>Ověření výstupu
Kanál v kontejneru objektů blob `adftutorial` automaticky vytvoří výstupní složku *fromonprem*. Zkontrolujte, že výstupní složka obsahuje soubor *dbo.emp.txt*. 

1. Na webu Azure Portal v okně kontejneru **adftutorial** vyberte **Obnovit**. Zobrazí se výstupní složka.

    ![Vytvořená výstupní složka](media/tutorial-hybrid-copy-powershell/fromonprem-folder.png)
2. V seznamu složek vyberte `fromonprem`. 
3. Potvrďte, že se zobrazuje soubor s názvem `dbo.emp.txt`.

    ![Výstupní soubor](media/tutorial-hybrid-copy-powershell/fromonprem-file.png)


## <a name="next-steps"></a>Další kroky
Kanál v této ukázce kopíruje data z jednoho umístění do jiného v úložišti objektů blob v Azure. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvořte místní prostředí Integration Runtime.
> * Vytvoření propojených služeb SQL Server a Azure Storage 
> * Vytvoření datových sad SQL Serveru a Azure Blob
> * Vytvoření kanálu s aktivitou kopírování pro přesun dat
> * Zahájení spuštění kanálu
> * Monitorování spuštění kanálu

Seznam úložišť dat podporovaných službou Data Factory najdete v tématu popisujícím [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).

Pokud se chcete dozvědět víc o hromadném kopírování dat ze zdroje do cíle, přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Hromadné kopírování dat](tutorial-bulk-copy.md)
