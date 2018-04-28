---
title: Vytvoření Azure SSIS integrace runtime v Azure Data Factory | Microsoft Docs
description: Naučte se vytvářet modulu runtime integrace Azure SSIS tak, aby balíčku služby SSIS můžete spustit v cloudu Azure.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2018
ms.author: douglasl
ms.openlocfilehash: e0217080963502094800a8b62de9c781817aec61
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Vytvoření modulu runtime integrace Azure SSIS v Azure Data Factory
Tento článek popisuje kroky pro zřizování modulu runtime integrace Azure SSIS v Azure Data Factory. Následně můžete pomocí SQL Server Data Tools (SSDT) nebo aplikace SQL Server Management Studio (SSMS) do tohoto modulu runtime v Azure nasadit balíčky SSIS (SQL Server Integration Services).

Kurz: [kurz: nasadit balíčky SQL Server Integration Services (služby SSIS) do Azure](tutorial-create-azure-ssis-runtime-portal.md) ukázal, jak vytvořit modul Runtime integrace Azure SSIS (IR) pomocí Azure SQL Database jako úložiště pro katalog služby SSIS. Tento článek se rozšíří na kurz a ukazuje, jak provést následující akce: 

- Použijte spravované Instance Azure SQL (Preview) pro hostování katalog služby SSIS (databáze SSISDB).
- Připojení Azure SSIS Reakcí do virtuální sítě Azure (VNet). Koncepční informace o připojení Azure SSIS IR k virtuální síti a konfiguraci virtuální sítě na portálu Azure najdete v tématu [připojení k Azure SSIS Reakcí do virtuální sítě](join-azure-ssis-integration-runtime-virtual-network.md). 

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), prostudujte si [dokumentaci služby Data Factory verze 1](v1/data-factory-introduction.md).


## <a name="overview"></a>Přehled
Tento článek ukazuje různé způsoby zřizování Azure SSIS IR:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Šablona Azure Resource Manageru](#azure-resource-manager-template)

Při vytváření služby Azure SSIS IR objekt pro vytváření dat se připojuje k vaší databázi SQL Azure Příprava databáze katalogu služby SSIS (SSISDB). Skript také nakonfiguruje oprávnění a nastavení vaší virtuální sítě, pokud je zadáte, a připojí k této virtuální síti novou instanci prostředí Azure SSIS Integration Runtime.

Když zřizujete instanci Azure-SSIS IR, nainstaluje se také Azure Feature Pack for SSIS a Access Redistributable. Tyto komponenty nabízejí mimo připojení k datovým zdrojům podporovaným integrovanými komponentami navíc možnosti připojení k souborům aplikací Excel a Access a různým datovým zdrojům Azure. Můžete také nainstalovat další součásti. Další informace najdete v tématu [vlastní nastavení pro modul runtime integrace Azure SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure**. Pokud předplatné nemáte, můžete si vytvořit [bezplatný zkušební](http://azure.microsoft.com/pricing/free-trial/) účet.
- **Server Azure SQL Database** nebo **spravované Instance systému SQL Server (Preview)**. Pokud ještě nemáte databázový server, vytvořte si ho na webu Azure Portal před tím, než začnete. Tento server hostuje databázi katalogu služby SSIS (SSISDB). Doporučujeme vytvořit databázový server ve stejné oblasti Azure jako prostředí Integration Runtime. Tato konfigurace umožňuje prostředí Integration Runtime zapisovat protokoly spuštění do databáze SSISDB bez přecházení mezi oblastmi Azure. Poznamenejte si cenové úrovně se server Azure SQL. Seznam podporovaných cenové úrovně pro databázi SQL Azure najdete v tématu [limitů prostředků databáze SQL](../sql-database/sql-database-resource-limits.md).

    Potvrďte, že serveru Azure SQL Database nebo spravované Instance systému SQL Server (Preview) nemá katalog služby SSIS (SSIDB databáze). Zřízení Azure-SSIS IR nepodporuje používání existujícího katalogu služby SSIS.
- **Classic nebo Azure Resource Manager virtuální Network(VNet) (volitelné)**. Virtuální síť Azure musíte mít v případě, že platí alespoň jedna z následujících podmínek:
    - Jsou hostiteli databáze služby SSIS katalogu na spravované Instance systému SQL Server (Preview), který je součástí virtuální sítě.
    - Z balíčků SSIS spuštěných v prostředí Azure SSIS Integration Runtime se chcete připojit k místním úložištím dat.
- **Azure PowerShell**. Postupujte podle pokynů v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/install-azurerm-ps). PowerShell použijete ke spuštění skriptu, který zřídí prostředí Azure SSIS Integration Runtime spouštějící balíčky SSIS v cloudu. 

> [!NOTE]
> - Datovou továrnu verze 2 můžete vytvořit v následujících oblastech: USA – východ, USA – východ 2, Jihovýchodní Asie a Západní Evropa. 
> - Můžete vytvořit Reakcí Azure SSIS v následujících oblastech: Východ USA, Východ USA 2, střed USA, západní USA 2, Severní Evropa, západní Evropa, Spojené království – jih a Austrálie – východ.

## <a name="azure-portal"></a>Azure Portal
V této části použijete portál Azure, konkrétně Data Factory uživatelského rozhraní, k vytvoření služby Azure SSIS infračerveného signálu. 

### <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
2. Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).    
3. V nabídce vlevo klikněte na **Nový**, klikněte na **Data + analýzy** a pak na **Data Factory**. 
   
   ![Nový -> Objekt pro vytváření dat](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. Na stránce **Nová datová továrna** jako **název** zadejte **MyAzureSsisDataFactory**. 
      
     ![Stránka Nová datová továrna](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Název objektu pro vytváření dat Azure musí být **globálně jedinečný**. Pokud se zobrazí následující chyba, změňte název datové továrny (například na vaše_jméno_MyAzureSsisDataFactory) a zkuste to znovu. Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](naming-rules.md).
  
       `Data factory name “MyAzureSsisDataFactory” is not available`

3. Vyberte své **předplatné** Azure, ve kterém chcete vytvořit datovou továrnu. 
4. Pro **Skupinu prostředků** proveďte jeden z následujících kroků:
     
      - Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků. 
      - Vyberte **Vytvořit novou** a zadejte název skupiny prostředků.   
         
      Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).  
4. Jako **verzi** vyberte **V2 (Preview)**.
5. Vyberte **umístění** pro objekt pro vytváření dat. V seznamu se zobrazí pouze podporovaná umístění pro vytváření datových továren.
6. Zaškrtněte **Připnout na řídicí panel**.     
7. Klikněte na možnost **Vytvořit**.
8. Na řídicím panelu vidíte následující dlaždice se statusem: **Nasazování datové továrny**. 

    ![nasazování dlaždice datové továrny](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. Po vytvoření se zobrazí stránka **Datová továrna**, jak je znázorněno na obrázku.
   
   ![Domovská stránka objektu pro vytváření dat](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. Kliknutím na dlaždici **Vytvořit a monitorovat** otevřete na samostatné kartě uživatelské rozhraní služby Data Factory. 

### <a name="provision-an-azure-ssis-integration-runtime"></a>Zřízení prostředí Azure SSIS Integration Runtime

1. Na stránce Začínáme klikněte na dlaždici **Konfigurace prostředí SSIS Integration Runtime**. 

   ![Dlaždice Konfigurace prostředí SSIS Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. Na stránce **Obecná nastavení** v okně **Instalace prostředí Integration Runtime** proveďte následující kroky: 

   ![Obecná nastavení](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    1. Zadejte **název** prostředí Integration Runtime.
    2. Zadejte **umístění** prostředí Integration Runtime. Zobrazí se pouze podporovaná umístění.
    3. Vyberte **velikost uzlu**, která se má pro modul runtime služby SSIS nakonfigurovat.
    4. Zadejte **počet uzlů** v clusteru.
    5. Klikněte na **Další**. 
1. V **Nastavení SQL** proveďte následující kroky: 

    ![Nastavení SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    1. Zadejte **předplatné** Azure, které obsahuje server SQL Azure. 
    2. Jako **Koncový bod databázového serveru katalogu** vyberte váš server SQL Azure.
    3. Zadejte uživatelské jméno **správce**.
    4. Zadejte **heslo** pro tohoto správce.  
    5. Vyberte **úroveň služby** pro databázi SSISDB. Výchozí hodnota je Basic.
    6. Klikněte na **Další**. 
1.  Na stránce **Upřesnit nastavení** vyberte hodnotu pro **Maximální počet paralelních zpracování na uzel**.   

    ![Upřesnit nastavení](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
5. Tento krok je **volitelný**. Pokud máte virtuální síť (Classic nebo Azure Resource Manager), které chcete runtime integrace se chcete připojit, vyberte **vyberte virtuální síť pro vaše runtime integrace Azure SSIS připojit a povolit službám Azure ke konfiguraci oprávnění nebo nastavení sítě VNet**možnost a proveďte následující kroky: 

    ![Upřesnit nastavení s virtuální sítí](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

    1. Pro **předplatné**, zadejte **předplatné** s virtuální sítí. 
    2. Pro typ, zadejte **typ** sítě vnet (klasickou virtuální síť nebo virtuální sítě Azure Resource Manager). 
    3. Pro **název virtuální sítě**, vyberte název vaší **VNet**. 
    4. Pro **název podsítě**, vyberte název **podsíť** ve virtuální síti.
1. Kliknutím na **Dokončit** zahajte vytváření prostředí Azure SSIS Integration Runtime. 

    > [!IMPORTANT]
    > - Tento proces trvá přibližně 20 minut.
    > - Služba Data Factory se připojí k vaší službě Azure SQL Database a připraví databázi katalogu služby SSIS (SSISDB). Skript také nakonfiguruje oprávnění a nastavení vaší virtuální sítě, pokud je zadáte, a připojí k této virtuální síti novou instanci prostředí Azure SSIS Integration Runtime.
7. V okně **Připojení** v případě potřeby přepněte na **Prostředí Integration Runtime**. Kliknutím na **Aktualizovat** aktualizujte stav. 

    ![Stav vytváření](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. Použijte odkazy v části **akce** sloupec, který se zastavit a spustit, upravit nebo odstranit integrace modulu runtime. Pomocí posledního odkazu zobrazíte kód JSON pro prostředí Integration Runtime. Tlačítka pro úpravu a odstranění jsou aktivní, pouze když je prostředí IR zastavené. 

    ![Azure SSIS IR – akce](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Prostředí Azure SSIS Integration Runtime na portálu

1. V uživatelském prostředí služby Azure Data Factory přepněte na kartu **Upravit**, klikněte na **Připojení** a pak přepněte zpět na kartu **Prostředí Integration Runtime**, kde se zobrazí existující prostředí Integration Runtime ve vaší datové továrně. 
    ![Zobrazení existujících IR](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
1. Kliknutím na **Nový** vytvořte nové prostředí Azure-SSIS IR. 

    ![Prostředí Integration Runtime prostřednictvím nabídky](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
2. Pokud chcete vytvořit prostředí Azure-SSIS Integration Runtime, klikněte na **Nový**, jak je znázorněno na obrázku. 
3. V okně Instalace prostředí Integration Runtime vyberte **Migrovat metodou „lift and shift“ existující balíčky služby SSIS ke spuštění v Azure** a pak klikněte na **Další**.

    ![Zadání typu prostředí Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. Zbývající kroky k nastavení prostředí Azure-SSIS IR najdete v části [Zřízení prostředí Azure SSIS Integration Runtime](#provision-an-azure-ssis-integration-runtime).

## <a name="azure-powershell"></a>Azure PowerShell
V této části použijte prostředí Azure PowerShell k vytvoření služby Azure SSIS infračerveného signálu.

### <a name="create-variables"></a>Vytvoření proměnných
Definujte proměnné, které se použijí ve skriptech v tomto kurzu:

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# You can create a data factory of version 2 in the following regions: East US, East US 2, Southeast Asia, and West Europe. 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS integration runtime name]"
$AzureSSISDescription = "This is my Azure-SSIS integration runtime"
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Enterprise Edition supports advanced/premium features

# You can create an Azure-SSIS IR in the following regions: East US, East US 2, Central US, West US 2, North Europe, West Europe, UK South, and Australia East.
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL: SAS URI of blob container where your custom setup script and its associated files are stored

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or your Azure SQL Managed Instance (Preview) server endpoint]"
$SSISDBServerAdminUserName = "[your server admin username]"
$SSISDBServerAdminPassword = "[your server admin password]"

# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (Preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "[your Azure SQL Database pricing tier. Examples: Basic, S0, S1, S2, S3, etc.]"

## These two parameters apply if you are using a VNet and an Azure SQL Managed Instance (Preview) 
# Specify information about your classic or Azure Resource Manager virtual network (VNet). 
$VnetId = "[your VNet resource ID or leave it empty]" 
$SubnetName = "[your subnet name or leave it empty]" 

```
### <a name="log-in-and-select-subscription"></a>Přihlášení a výběr předplatného
Přidejte následující kód skriptu se přihlaste a vyberte předplatné Azure: 

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database"></a>Ověření připojení k databázi
Přidejte následující skript, který chcete ověřit vaše server.database.windows.net serveru Azure SQL Database nebo váš koncový bod serveru spravované Instance Azure SQL (Preview). 

```powershell
$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID="+ $SSISDBServerAdminUserName +";Password="+ $SSISDBServerAdminPassword
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL DB logical server/Azure SQL MI server, exception: $_"  ;
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}
```

### <a name="configure-virtual-network"></a>Konfigurace virtuální sítě
Přidejte následující skript, který automaticky nakonfiguruje oprávnění a nastavení virtuální sítě, ke které se má vaše prostředí Azure SSIS Integration Runtime připojit.

```powershell
# Register to Azure Batch resource provider
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Vytvořte [skupinu prostředků Azure](../azure-resource-manager/resource-group-overview.md) pomocí příkazu [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky jako skupina. Následující příklad vytvoří skupinu prostředků s názvem `myResourceGroup` v umístění `westeurope`.

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Vytvoření datové továrny
Spuštěním následujícího příkazu vytvořte datovou továrnu:

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Vytvoření prostředí Integration Runtime
Spusťte následující příkaz k vytvoření modulu runtime integrace Azure SSIS, který běží v Azure balíčky SSIS: pomocí tohoto skriptu z části na základě typu databáze (Azure SQL Database vs. Azure SQL spravované Instance (Preview)) používáte. 

#### <a name="azure-sql-database-to-host-the-ssisdb-database-ssis-catalog"></a>Databáze SQL Azure k hostování databáze SSISDB (SSIS katalog) 

```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -CatalogPricingTier $SSISDBPricingTier `
                                            -Description $AzureSSISDescription `
                                            -Edition $AzureSSISEdition ` 
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                            -SetupScriptContainerSasUri $SetupScriptContainerSasUri
```

Nemusíte předat hodnoty pro VNetId a podsíť, pokud budete potřebovat přístup k datům místně, tedy máte cíle zdroje dat na místě ve vašich balíčcích SSIS. Je nutné předat hodnotu pro parametr CatalogPricingTier. Seznam podporovaných cenové úrovně pro databázi SQL Azure najdete v tématu [limitů prostředků databáze SQL](../sql-database/sql-database-resource-limits.md).

#### <a name="azure-sql-managed-instance-preview-to-host-the-ssisdb-database"></a>Azure spravované Instance systému SQL (Preview) k hostování databáze SSISDB

```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -Description $AzureSSISDescription `
                                            -Edition $AzureSSISEdition ` 
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                            -SetupScriptContainerSasUri $SetupScriptContainerSasUri `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

Je třeba předat hodnoty parametrů VnetId a podsíť s Azure spravované Instance SQL (Preview) které připojí virtuální sítě. Parametr CatalogPricingTier nelze použít pro spravované Instance Azure SQL (Preview). 

### <a name="start-integration-runtime"></a>Spuštění prostředí Integration Runtime
Spuštěním následujícího příkazu spusťte prostředí Azure SSIS Integration Runtime: 

```powershell
write-host("##### Starting #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```
Dokončení tohoto příkazu trvá **20 až 30 minut**. 


### <a name="full-script"></a>Celý skript
Zde je úplná skript, který vytvoří Azure SSIS reakcí na Incidenty a připojí k virtuální síti. Tento skript předpokládá, že používáte Azure spravované Instance SQL (Preview) pro hostování katalogu služby SSIS. 

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS (IR)>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Enterprise Edition supports advanced/premium features

$AzureSSISLocation = "EastUS" 
 # In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL: SAS URI of blob container where your custom setup script and its associated files are stored

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (Preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>" 

$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID="+ $SSISDBServerAdminUserName +";Password="+ $SSISDBServerAdminPassword
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL DB logical server/Azure SQL MI server, exception: $_"  ;
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}

Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                        -Location $DataFactoryLocation `
                        -Name $DataFactoryName

$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -CatalogPricingTier $SSISDBPricingTier `
                                            -Description $AzureSSISDescription `
                                            -Edition $AzureSSISEdition ` 
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                            -SetupScriptContainerSasUri $SetupScriptContainerSasUri

write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru
V této části použijete k vytvoření modulu runtime integrace Azure SSIS šablonu Azure Resource Manager. Zde je ukázka návod: 

1. Vytvořte soubor JSON pomocí následující šablony Resource Manageru. Nahraďte vlastními hodnotami hodnoty v lomené závorky (zástupného). 

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2017-09-01-preview",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for the Azure SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D1_v2",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 1
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL server>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL user",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```
2. Pokud chcete nasadit šablonu Resource Manager, spuštěním příkazu New-AzureRmResourceGroupDeployment jak je znázorněno v následujícím exmaple. V tomto příkladu je ADFTutorialResourceGroup název skupiny prostředků. C:\adfgetstarted je soubor, který obsahuje definici JSON pro vytváření dat a infračerveného signálu Azure SSIS. 

    ```powershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Tento příkaz vytvoří objekt pro vytváření dat a vytvoří Azure SSIS IR v něm, ale nespustí infračerveného signálu. 
3. Spusťte IR Azure SSIS, spusťte příkaz Start-AzureRmDataFactoryV2IntegrationRuntime: 

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name> `
                                             -DataFactoryName <Data Factory Name> `
                                             -Name <Azure SSIS IR Name> `
                                             -Force
    ``` 

## <a name="deploy-ssis-packages"></a>Nasazení balíčků SSIS
Teď použijte SQL Server Data Tools (SSDT) nebo aplikaci SQL Server Management Studio (SSMS) k nasazení vašich balíčků SSIS do Azure. Připojte se k serveru SQL Azure, který hostuje katalog služby SSIS (SSISDB). Název serveru SQL Azure je ve formátu &lt;název_serveru&gt;.database.windows.net (pro službu Azure SQL Database). Pokyny najdete v článku věnovaném [nasazení balíčků](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

## <a name="next-steps"></a>Další postup
Najdete v dalších tématech IR Azure SSIS v této dokumentaci:

- [Modul Runtime integrace Azure SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Tento článek obsahuje koncepční informace o integraci runtimes obecně včetně infračerveného signálu Azure SSIS. 
- [Kurz: Nasazení balíčků SSIS do Azure](tutorial-create-azure-ssis-runtime-portal.md) Tento článek obsahuje podrobné pokyny pro vytvoření Azure-SSIS IR a využívá databázi Azure SQL k hostování katalogu SSIS. 
- [Monitorování Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Tento článek ukazuje, jak načíst informace o Azure-SSIS IR a popisy stavů ve vrácených informacích. 
- [Správa Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Tento článek ukazuje, jak zastavit, spustit nebo odebrat Azure-SSIS IR. Ukazuje také postup horizontálního navýšení kapacity Azure-SSIS IR přidáním více uzlů. 
- [Připojení Azure-SSIS IR k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md) Tento článek obsahuje koncepční informace o připojení Azure-SSIS IR k virtuální síti Azure (VNet). Poskytuje také kroky pro využití webu Azure Portal ke konfiguraci virtuální sítě, aby se k ní prostředí Azure-SSIS IR mohlo připojit. 
