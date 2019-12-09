---
title: Zřízení Azure-SSIS Integration Runtime pomocí prostředí PowerShell
description: Zjistěte, jak pomocí PowerShellu zřídit prostředí Azure-SSIS Integration Runtime ve službě Azure Data Factory, abyste v Azure mohli nasazovat a spouštět balíčky SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 06/26/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: anandsub
ms.openlocfilehash: ff40867bc1e2778ec6f21f479360866b50d0c184
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926513"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory-with-powershell"></a>Zřízení prostředí Azure-SSIS Integration Runtime ve službě Azure Data Factory pomocí PowerShellu

V tomto kurzu najdete postup, jak zřídit Azure-služba SSIS (SQL Server Integration Services) (SSIS) Integration Runtime (IR) v Azure Data Factory (ADF). Azure-SSIS IR podporuje spouštění balíčků nasazených do katalogu SSIS (SSISDB) hostovaných pomocí Azure SQL Database serveru/spravované instance (model nasazení projektu) a ty, které jsou nasazené do systémů souborů/sdílené složky/soubory Azure (model nasazení balíčku). Po zřízení Azure-SSIS IR pak můžete pomocí známých nástrojů, jako je například `dtinstall`/`dtutil`/`dtexec`pro nasazení a spouštění balíčků v Azure, použít známé nástroje Management Studio SQL Server, jako je například. V tomto kurzu provedete následující kroky:

> [!NOTE]
> Tento článek používá Azure PowerShell ke zřízení Azure-SSIS IR. Pokud chcete použít aplikaci Azure Portal/ADF ke zřízení Azure-SSIS IR, přečtěte si téma [kurz: zřizování Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md). 

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření prostředí Azure-SSIS Integration Runtime
> * Spuštění prostředí Azure-SSIS Integration Runtime
> * Kontrola celého skriptu
> * Nasazení balíčků SSIS

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Předplatné Azure**. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete. Koncepční informace o Azure-SSIS IR najdete v [přehledu prostředí Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).
- **Server Azure SQL Database (volitelné)** . Pokud ještě nemáte databázový server, vytvořte ho v Azure Portal před tím, než začnete. ADF pak na tomto databázovém serveru vytvoří SSISDB. Doporučujeme vytvořit databázový server ve stejné oblasti Azure jako prostředí Integration Runtime. Tato konfigurace umožňuje prostředí Integration runtime zapisovat protokoly spouštění do SSISDB bez přechodu do oblastí Azure. 
    - V závislosti na vybraném databázovém serveru je možné databázi SSISDB vytvořit vaším jménem jako jednoúčelovou databázi, součást elastického fondu nebo ve spravované instanci a zpřístupnit ji ve veřejné síti nebo prostřednictvím připojení k virtuální síti. Pokyny k výběru typu databázového serveru pro hostování SSISDB najdete v tématu [porovnání Azure SQL Database izolované databáze, elastického fondu a spravované instance](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance). Pokud používáte Azure SQL Database Server s koncovými body služby virtuální sítě/spravovanou instancí ve virtuální síti k hostování SSISDB nebo vyžadují přístup k místním datům, musíte se připojit k Azure-SSIS IR k virtuální síti, přečtěte si téma [vytvoření Azure-SSIS IR ve virtuální síti](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Ujistěte se, že má databázový server povolené nastavení **Povolit přístup ke službám Azure**. Tato možnost se nevztahuje na použití serveru Azure SQL Database s koncovými body služby virtuální sítě/spravovanou instancí ve virtuální síti pro hostování SSISDB. Další informace najdete v tématu [Zabezpečení databáze Azure SQL](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Pokud chcete toto nastavení povolit pomocí PowerShellu, přečtěte si článek [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
    - Přidejte IP adresu klientského počítače nebo rozsah IP adres, který obsahuje IP adresu klientského počítače, do seznamu IP adres klienta v nastavení brány firewall pro databázový server. Další informace najdete v tématu [Pravidla brány firewall na úrovni serveru a databáze služby Azure SQL Database](../sql-database/sql-database-firewall-configure.md).
    - K databázovému serveru se můžete připojit pomocí ověřování SQL s přihlašovacími údaji správce serveru nebo pomocí ověřování Azure Active Directory (AAD) se spravovanou identitou pro ADF.  U druhé možnosti je potřeba přidat spravovanou identitu služby ADF do skupiny AAD s přístupovými oprávněními k databázovému serveru. Další informace najdete v tématu [Vytvoření prostředí Azure-SSIS IR s ověřováním AAD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Ověřte, že váš databázový server ještě nemá SSISDB. Zřizování Azure-SSIS IR nepodporuje používání existující SSISDB.
- **Azure PowerShell**. Postupujte podle pokynů v tématu [instalace a konfigurace Azure PowerShell](/powershell/azure/install-Az-ps), pokud chcete ke zřízení Azure-SSIS IR spustit skript prostředí PowerShell.

> [!NOTE]
> - Seznam oblastí Azure, ve kterých jsou v současnosti k dispozici ADF a Azure-SSIS IR, najdete v článku [o dostupnosti ADF + SSIS IR v oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="launch-windows-powershell-ise"></a>Spuštění integrovaného skriptovacího prostředí (ISE) v prostředí Windows PowerShell

Spusťte **Integrované skriptovací prostředí (ISE) v prostředí Windows PowerShell** s oprávněními správce. 

## <a name="create-variables"></a>Vytvoření proměnných

Zkopírujte a vložte následující skript – zadejte hodnoty pro proměnné. 

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

Přidejte následující kód pro přihlášení a vyberte své předplatné Azure.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

## <a name="validate-the-connection-to-database-server"></a>Ověření připojení k databázovému serveru

Přidejte následující skript pro ověření serveru Azure SQL Database. 

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

Chcete-li vytvořit Azure SQL Database jako součást skriptu, přečtěte si následující příklad: 

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

Vytvořte [skupinu prostředků Azure](../azure-resource-manager/resource-group-overview.md) pomocí příkazu [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky jako skupina.

Pokud vaše skupina prostředků už existuje, nekopírujte do skriptu tento kód. 

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

Spuštěním následujících příkazů vytvořte prostředí Azure-SSIS Integration runtime, které spouští balíčky SSIS v Azure.

Pokud nepoužíváte SSISDB, můžete parametry CatalogServerEndpoint, CatalogPricingTier a CatalogAdminCredential vynechat.

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

Spuštěním následujících příkazů spusťte prostředí Azure-SSIS Integration runtime.

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
> S výjimkou času vlastní instalace by tento proces měl být dokončen do 5 minut.
>
> Pokud používáte SSISDB, služba ADF se připojí k vašemu databázovému serveru a připraví SSISDB. 
> 
> Když zřizujete Azure-SSIS IR, nainstaluje se také Distribuovatelný balíček a sada Azure Feature Pack pro SSIS. Tyto komponenty poskytují kromě zdrojů dat, které už jsou podporované integrovanými součástmi, možnosti připojení k souborům Excelu a Accessu a různým zdrojům dat Azure. Můžete také nainstalovat další součásti, viz [vlastní nastavení pro Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="full-script"></a>Celý skript

Skript prostředí PowerShell v této části nakonfiguruje instanci Azure-SSIS IR, která spouští balíčky SSIS. Po úspěšném spuštění tohoto skriptu můžete nasadit a spustit balíčky SSIS v Azure.

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

- [Azure-SSIS IR monitorování](monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Správa Azure-SSIS IR](manage-azure-ssis-integration-runtime.md)

## <a name="deploy-ssis-packages"></a>Nasazení balíčků SSIS

Pokud používáte SSISDB, můžete do něj nasadit balíčky a spouštět je v Azure-SSIS IR pomocí nástrojů SSDT/SSMS, které se připojují k vašemu databázovému serveru prostřednictvím koncového bodu serveru. U Azure SQL Database serveru/spravované instance s veřejným koncovým bodem je formát koncového bodu serveru `<server name>.database.windows.net`/`<server name>.public.<dns prefix>.database.windows.net,3342`v uvedeném pořadí. Pokud SSISDB nepoužíváte, můžete balíčky nasadit do systémů souborů/sdílených složek nebo souborů Azure a spouštět je v Azure-SSIS IR pomocí `dtinstall`/`dtutil`/`dtexec` nástrojů příkazového řádku. Další informace najdete v tématu [nasazení balíčků SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). V obou případech můžete také spouštět nasazené balíčky na Azure-SSIS IR pomocí aktivity spustit balíček SSIS v kanálech ADF, viz téma [vyvolání spuštění balíčku SSIS jako aktivity první třídy ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

Viz také následující články z dokumentace ke službě SSIS: 

- [Nasazení, spuštění a monitorování balíčků SSIS v Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Připojení k SSISDB v Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Plánování spouštění balíčků v Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
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