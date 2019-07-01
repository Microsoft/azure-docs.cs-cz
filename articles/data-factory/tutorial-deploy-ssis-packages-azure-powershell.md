---
title: Zřízení prostředí Azure-SSIS Integration Runtime pomocí PowerShellu | Microsoft Docs
description: Zjistěte, jak pomocí PowerShellu zřídit prostředí Azure-SSIS Integration Runtime ve službě Azure Data Factory, abyste v Azure mohli nasazovat a spouštět balíčky SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: tutorial
ms.date: 06/26/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: bbbbc45bd050b8f68499febeed6285ad1aa883c4
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484780"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory-with-powershell"></a>Zřízení prostředí Azure-SSIS Integration Runtime ve službě Azure Data Factory pomocí PowerShellu

Tento kurz obsahuje postup pro zřízení Azure – integrační služby SSIS (SQL Server) Integration Runtime (IR) v Azure Data Factory (ADF). Prostředí Azure-SSIS IR podporuje spouštění balíčků, které jsou nasazené do katalogu služby SSIS (SSISDB) hostitelem serveru Azure SQL Database nebo spravované Instance (Model nasazení projektu) a nasazené do systémů soubor soubory sdílené složky Azure (Model nasazení balíčku). Po zřízení Azure-SSIS IR, pak můžete používat známé nástroje, jako je například SQL Server Data Tools (SSDT) nebo SQL Server Management Studio (SSMS) a příkazového řádku nástroje, jako například `dtinstall` / `dtutil` / `dtexec`do nasazení a spouštění balíčků služby v Azure. V tomto kurzu provedete následující kroky:

> [!NOTE]
> Tento článek používá prostředí Azure PowerShell ke zřízení Azure-SSIS IR. Zřízení prostředí Azure-SSIS IR pomocí aplikace Azure portal/ADF, najdete v článku [kurzu: Zřízení prostředí Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md). 

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření prostředí Azure-SSIS Integration Runtime
> * Spuštění prostředí Azure-SSIS Integration Runtime
> * Kontrola celého skriptu
> * Nasazení balíčků SSIS

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Předplatné Azure**. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete. Koncepční informace o Azure-SSIS IR najdete v [přehledu prostředí Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).
- **Server Azure SQL Database (volitelné)** . Pokud ještě nemáte databázový server, vytvořte na webu Azure Portal před zahájením práce. ADF pak vytvoří SSISDB na tomto databázovém serveru. Doporučujeme vytvořit databázový server ve stejné oblasti Azure jako prostředí Integration Runtime. Tato konfigurace umožňuje prostředí integration runtime zapisovat protokoly spuštění do databáze SSISDB bez přecházení mezi oblastmi Azure. 
    - V závislosti na vybraném databázovém serveru je možné databázi SSISDB vytvořit vaším jménem jako jednoúčelovou databázi, součást elastického fondu nebo ve spravované instanci a zpřístupnit ji ve veřejné síti nebo prostřednictvím připojení k virtuální síti. Pokyny k výběru typu databázový server pro hostování služby SSISDB, naleznete v tématu [porovnání Azure SQL Database jeden databáze nebo elastického fondu nebo spravované Instance](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Pokud používáte server Azure SQL Database s virtuální sítě služby koncové body nebo spravované Instance ve virtuální síti k hostování databáze SSISDB nebo vyžadují přístup k místním datům, musíte připojit k prostředí Azure-SSIS IR k virtuální síti najdete v tématu [vytvořit prostředí Azure-SSIS IR ve virtuální síti](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Ujistěte se, že má databázový server povolené nastavení **Povolit přístup ke službám Azure**. Tento parametr nelze použít, pokud používáte server Azure SQL Database pomocí virtuální sítě služby koncové body nebo spravované Instance ve virtuální síti pro hostování služby SSISDB. Další informace najdete v tématu [Zabezpečení databáze Azure SQL](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Toto nastavení povolit pomocí prostředí PowerShell, najdete v článku [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
    - Přidáte IP adresu klientského počítače nebo rozsah IP adres, který obsahuje IP adresu klientského počítače seznamu IP adres v nastavení brány firewall pro databázový server. Další informace najdete v tématu [Pravidla brány firewall na úrovni serveru a databáze služby Azure SQL Database](../sql-database/sql-database-firewall-configure.md).
    - Můžete připojit k databázovému serveru pomocí ověřování SQL pomocí svých přihlašovacích údajů správce serveru nebo ověřování Azure Active Directory (AAD) pomocí spravované identity pro vaše ADF.  U druhé možnosti je potřeba přidat spravovanou identitu služby ADF do skupiny AAD s přístupovými oprávněními k databázovému serveru. Další informace najdete v tématu [Vytvoření prostředí Azure-SSIS IR s ověřováním AAD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Potvrďte, že databázový server nemá žádné SSISDB již. Zřízení prostředí Azure-SSIS IR nepodporuje použití existující databáze SSISDB.
- **Azure PowerShell**. Postupujte podle pokynů v [instalace a konfigurace Azure Powershellu](/powershell/azure/install-Az-ps), pokud chcete spustit skript prostředí PowerShell ke zřízení Azure-SSIS IR.

> [!NOTE]
> - Seznam oblastí Azure, ve kterých jsou aktuálně k dispozici ADF a Azure-SSIS IR najdete v tématu [ADF + SSIS IR dostupnost podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="launch-windows-powershell-ise"></a>Spuštění integrovaného skriptovacího prostředí (ISE) v prostředí Windows PowerShell

Spusťte **Integrované skriptovací prostředí (ISE) v prostředí Windows PowerShell** s oprávněními správce. 

## <a name="create-variables"></a>Vytvoření proměnných

Zkopírujte a vložte následující skript – určuje hodnoty pro proměnné. 

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"
```

## <a name="sign-in-and-select-subscription"></a>Přihlášení a výběr předplatného

Přidejte následující kód skriptu pro přihlášení a výběr vašeho předplatného Azure.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

## <a name="validate-the-connection-to-database-server"></a>Ověření připojení k databázovému serveru

Přidejte následující skript pro ověření vašeho serveru Azure SQL Database. 

```powershell
# Validate only if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
    $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
    Try
    {
        $sqlConnection.Open();
    }
    Catch [System.Data.SqlClient.SqlException]
    {
        Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
        Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
        $yn = Read-Host
        if(!($yn -ieq "Y"))
        {
            Return;
        } 
    }
}
```

K vytvoření služby Azure SQL Database jako součást skriptu, najdete v následujícím příkladu: 

Nastavte hodnoty pro proměnné, které ještě nejsou definované. Příklad: SSISDBServerName, FirewallIPAddress. 

```powershell
New-AzSqlServer -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -Location $DataFactoryLocation `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SSISDBServerAdminUserName, $(ConvertTo-SecureString -String $SSISDBServerAdminPassword -AsPlainText -Force))    

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -FirewallRuleName "ClientIPAddress_$today" -StartIpAddress $FirewallIPAddress -EndIpAddress $FirewallIPAddress

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $SSISDBServerName -AllowAllAzureIPs
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvoření [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md) pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) příkazu. Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky jako skupina.

Pokud vaše skupina prostředků už existuje, nekopírujte tento kód do vašeho skriptu. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

Spuštěním následujícího příkazu vytvořte datovou továrnu:

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

## <a name="create-an-integration-runtime"></a>Vytvoření prostředí Integration Runtime

Spusťte následující příkazy k vytvoření prostředí Azure-SSIS integration runtime spouštějícího balíčky SSIS v Azure.

Pokud nepoužijete SSISDB, můžete vynechat CatalogServerEndpoint CatalogPricingTier a CatalogAdminCredential parametry.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier `
                                               -CatalogAdminCredential $serverCreds
}

# Add SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
```

## <a name="start-integration-runtime"></a>Spuštění prostředí Integration Runtime

Spusťte následující příkazy ke spuštění prostředí Azure-SSIS integration runtime.

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```

> [!NOTE]
> S výjimkou kdykoli vlastní nastavení tento proces by se měl dokončit během 5 minut.
>
> Pokud používáte službu SSISDB, služba ADF se připojí k vašemu databázovému serveru Příprava databáze SSISDB. 
> 
> Když si zřídíte Azure-SSIS IR, jsou nainstalovány také Azure Feature Pack for SSIS a Access Redistributable. Tyto komponenty nabízejí připojení k souborům aplikace Excel a Access a různým datovým zdrojům Azure, spolu se zdroji dat, již podporuje integrované komponenty. Můžete taky nainstalovat další komponenty, naleznete v tématu [vlastní nastavení pro prostředí Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="full-script"></a>Celý skript

Skript Powershellu v této části nakonfiguruje instanci prostředí Azure-SSIS IR, které spouští balíčky SSIS. Po úspěšném spuštění tohoto skriptu můžete nasazovat a spouštění balíčků služby SSIS v Azure.

1. Spusťte integrované skriptovací prostředí (ISE) v prostředí Windows PowerShell.
2. V integrovaném skriptovacím prostředí (ISE) spusťte z příkazového řádku následující příkaz.  

    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```

3. Zkopírujte skript PowerShellu v této části a vložte jej do integrovaného skriptovacího prostředí (ISE).
4. Na začátku skriptu zadejte odpovídající hodnoty pro všechny parametry.
5. Spusťte skript. 

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you want to use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"

### Sign in and select subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database server
# Validate only if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
    $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
    Try
    {
        $sqlConnection.Open();
    }
    Catch [System.Data.SqlClient.SqlException]
    {
        Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
        Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
        $yn = Read-Host
        if(!($yn -ieq "Y"))
        {
            Return;
        } 
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier `
                                               -CatalogAdminCredential $serverCreds
}

# Add SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

### Start integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")   
```

## <a name="monitor-and-manage-azure-ssis-ir"></a>Monitorování a správa Azure-SSIS IR

Podrobné informace o monitorování a správě Azure-SSIS IR najdete v následujících článcích. 

- [Monitorování Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Správa Azure-SSIS IR](manage-azure-ssis-integration-runtime.md)

## <a name="deploy-ssis-packages"></a>Nasazení balíčků SSIS

Pokud používáte službu SSISDB, můžete nasadit své balíčky do ní a spouštět v prostředí Azure-SSIS IR pomocí Nástroje SSDT/SSMS, připojující se ke svému databázovému serveru prostřednictvím její koncový bod serveru. Pro server/spravované instance Azure SQL Database s veřejným koncovým bodem, formát koncový bod serveru je `<server name>.database.windows.net` / `<server name>.public.<dns prefix>.database.windows.net,3342`v uvedeném pořadí. Pokud nepoužijete SSISDB, můžete nasadit své balíčky do souboru systémy nebo k souboru sdílené složky Azure soubory a spouštět v prostředí Azure-SSIS IR pomocí `dtinstall` / `dtutil` / `dtexec` nástroje příkazového řádku. Další informace najdete v tématu [balíčků služby SSIS nasazovat](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). V obou případech se může také spouštění balíčků služby nasazené v prostředí Azure-SSIS IR pomocí aktivity spustit balíčků služby SSIS v kanálech ADF, naleznete v tématu [spouštění jako první třídy aktivity ADF balíčků služby SSIS vyvolat](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

Viz také následující články v dokumentaci ke službě SSIS: 

- [Nasazení, spuštění a monitorování balíčků služby SSIS v Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Připojit do databáze SSISDB v Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Plánování spouštění balíčku v Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Připojení k místním zdrojům dat s využitím ověřování systému Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili: 

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření prostředí Azure-SSIS Integration Runtime
> * Spuštění prostředí Azure-SSIS Integration Runtime
> * Kontrola celého skriptu
> * Nasazení balíčků SSIS

Informace o přizpůsobení prostředí Azure-SSIS Integration Runtime najdete v následujícím článku:

> [!div class="nextstepaction"]
>[Přizpůsobení prostředí Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)