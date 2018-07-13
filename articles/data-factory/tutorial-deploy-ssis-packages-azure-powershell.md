---
title: Zřízení prostředí Azure-SSIS Integration Runtime pomocí PowerShellu | Microsoft Docs
description: Zjistěte, jak pomocí PowerShellu zřídit prostředí Azure-SSIS Integration Runtime ve službě Azure Data Factory, abyste v Azure mohli nasazovat a spouštět balíčky SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: hero-article
ms.date: 06/27/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 8dc41eb3507368bb810c30a7680b73d0d1f26408
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2018
ms.locfileid: "37085403"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory-with-powershell"></a>Zřízení prostředí Azure-SSIS Integration Runtime ve službě Azure Data Factory pomocí PowerShellu
Tento kurz obsahuje postup pro zřízení prostředí Azure-SSIS Integration Runtime (IR) ve službě Azure Data Factory. Následně můžete pomocí SQL Server Data Tools (SSDT) nebo aplikace SQL Server Management Studio (SSMS) v tomto modulu runtime v Azure nasazovat a spouštět balíčky SSIS (SQL Server Integration Services). V tomto kurzu provedete následující kroky:

> [!NOTE]
> Tento článek používá Azure PowerShell ke zřízení Azure SSIS IR. Informace o zřízení Azure SSIS IR pomocí uživatelského rozhraní Data Factory najdete v tématu [Kurz: Vytvoření modulu runtime integrace Azure SSIS](tutorial-create-azure-ssis-runtime-portal.md). 

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření prostředí Azure-SSIS Integration Runtime
> * Spuštění prostředí Azure-SSIS Integration Runtime
> * Nasazení balíčků SSIS
> * Kontrola celého skriptu

## <a name="prerequisites"></a>Požadavky
- **Předplatné Azure**. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete. Koncepční informace o Azure-SSIS IR najdete v [přehledu prostředí Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). 
- **Server služby Azure SQL Database**. Pokud ještě nemáte databázový server, vytvořte si ho na webu Azure Portal před tím, než začnete. Tento server hostuje databázi katalogu služby SSIS (SSISDB). Doporučujeme vytvořit databázový server ve stejné oblasti Azure jako prostředí Integration Runtime. Tato konfigurace umožňuje prostředí Integration Runtime zapisovat protokoly spuštění do databáze SSISDB bez přecházení mezi oblastmi Azure. 
    - V závislosti na vybraném databázovém serveru je možné databázi SSISDB vytvořit vaším jménem jako jednu databázi, součást elastického fondu nebo v MI (Preview) a zpřístupnit ji ve veřejné síti nebo prostřednictvím připojení k virtuální síti. Pokyny pro výběr typu databázového serveru pro hostování SSISDB najdete ve [srovnání SQL Database a Spravované instance (Preview)](create-azure-ssis-integration-runtime.md#compare-sql-database-and-managed-instance-preview). Pokud k hostování databáze SSISDB používáte službu Azure SQL Database s koncovými body služby virtuální sítě nebo MI (Preview) nebo pokud vyžadujete přístup k místním datům, musíte prostředí Azure-SSIS IR připojit k virtuální síti. Další informace najdete v tématu [Vytvoření prostředí Azure-SSIS IR ve virtuální síti](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime). 
    - Ujistěte se, že nastavení **Povolit přístup ke službám Azure** je pro databázový server **zapnuté**. Toto nastavení neplatí, pokud k hostování databáze SSISDB používáte službu Azure SQL Database s koncovými body služby virtuální sítě nebo MI (Preview). Další informace najdete v tématu [Zabezpečení databáze SQL Azure](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal). Pokud chcete toto nastavení povolit pomocí PowerShellu, přečtěte si téma věnované rutině [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1). 
    - Přidejte IP adresu klientského počítače nebo rozsah IP adres, který obsahuje IP adresu klientského počítače, do seznamu IP adres v nastavení brány firewall pro databázový server. Další informace najdete v tématu [Pravidla brány firewall na úrovni serveru a databáze služby Azure SQL Database](../sql-database/sql-database-firewall-configure.md). 
    - K databázovému serveru se můžete připojit prostřednictvím ověřování SQL s použitím přihlašovacích údajů správce serveru nebo prostřednictvím ověřování Azure Active Directory (AAD) s použitím identity spravované služby (MSI) Azure Data Factory.  U druhé možnosti je potřeba přidat MSI služby Data Factory do skupiny AAD s přístupovými oprávněními k databázovému serveru. Další informace najdete v tématu [Vytvoření prostředí Azure-SSIS IR s ověřováním AAD](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime). 
    - Ověřte si, že váš server Azure SQL Database nemá katalog služby SSIS (databázi SSIDB). Zřízení Azure-SSIS IR nepodporuje používání existujícího katalogu služby SSIS. 
- **Azure PowerShell**. Postupujte podle pokynů v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/install-azurerm-ps). PowerShell použijete ke spuštění skriptu, který zřídí prostředí Azure SSIS Integration Runtime spouštějící balíčky SSIS v cloudu. 

> [!NOTE]
> - Pokud chcete zobrazit seznam oblastí Azure, ve kterých je služba Data Factory aktuálně dostupná, na následující stránce vyberte oblasti, které vás zajímají, pak rozbalte **Analýza** a vyhledejte **Data Factory:** [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/). 
> - Pokud chcete zobrazit seznam oblastí Azure, ve kterých je prostředí Azure-SSIS Integration Runtime aktuálně dostupné, na následující stránce vyberte oblasti, které vás zajímají, pak rozbalte **Analýza** a vyhledejte **SSIS Integration Runtime:** [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/).

## <a name="launch-windows-powershell-ise"></a>Spuštění integrovaného skriptovacího prostředí (ISE) v prostředí Windows PowerShell
Spusťte **Integrované skriptovací prostředí (ISE) v prostředí Windows PowerShell** s oprávněními správce. 

## <a name="create-variables"></a>Vytvoření proměnných
Zkopírujte a vložte následující skript a zadejte hodnoty pro proměnné. Seznam podporovaných **cenových úrovní** pro službu Azure SQL Database najdete v tématu [Omezení prostředků služby SQL Database](../sql-database/sql-database-resource-limits.md).

```powershell
# Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
$SubscriptionName = "[Azure subscription name]"
$ResourceGroupName = "[Azure resource group name]"
# Data factory name. Must be globally unique
$DataFactoryName = "[Data factory name]"
$DataFactoryLocation = "EastUS"

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[Specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[Specify a description for your Azure-SSIS IR]"
$AzureSSISLocation = "EastUS"
# In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D4_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B". For standard/premium/Elastic Pool tiers, specify "S0", "S1", "S2", "S3", etc.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>)]"
```

## <a name="validate-the-connection-to-database"></a>Ověření připojení k databázi
Přidejte následující skript pro ověření vašeho serveru služby Azure SQL Database – `<servername>.database.windows.net`. 

```powershell
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
```

Pokud chcete vytvořit databázi SQL Azure jako součást skriptu, podívejte se na následující příklad: 

Nastavte hodnoty pro proměnné, které ještě nejsou definované. Příklad: SSISDBServerName, FirewallIPAddress. 

```powershell
New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -Location $DataFactoryLocation `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SSISDBServerAdminUserName, $(ConvertTo-SecureString -String $SSISDBServerAdminPassword -AsPlainText -Force))    

New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -FirewallRuleName "ClientIPAddress_$today" -StartIpAddress $FirewallIPAddress -EndIpAddress $FirewallIPAddress

New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $SSISDBServerName -AllowAllAzureIPs
```

## <a name="log-in-and-select-subscription"></a>Přihlášení a výběr předplatného
Přidejte do skriptu následující kód pro přihlášení a výběr vašeho předplatného Azure: 

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Vytvořte [skupinu prostředků Azure](../azure-resource-manager/resource-group-overview.md) pomocí příkazu [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky jako skupina. Následující příklad vytvoří skupinu prostředků s názvem `myResourceGroup` v umístění `westeurope`.

Pokud vaše skupina prostředků už existuje, nekopírujte tento kód do vašeho skriptu. 

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

## <a name="create-a-data-factory"></a>Vytvoření datové továrny
Spuštěním následujícího příkazu vytvořte datovou továrnu:

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

## <a name="create-an-integration-runtime"></a>Vytvoření prostředí Integration Runtime
Spuštěním následujícího příkazu vytvořte prostředí Azure SSIS Integration Runtime, které spouští balíčky SSIS v Azure: 

```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
  
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -SetupScriptContainerSasUri $SetupScriptContainerSasUri `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogAdminCredential $serverCreds `
                                           -CatalogPricingTier $SSISDBPricingTier
```

## <a name="start-integration-runtime"></a>Spuštění prostředí Integration Runtime
Spuštěním následujícího příkazu spusťte prostředí Azure SSIS Integration Runtime: 

```powershell
write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```
Dokončení tohoto příkazu trvá **20 až 30 minut**. 

## <a name="deploy-ssis-packages"></a>Nasazení balíčků SSIS
Teď použijte SQL Server Data Tools (SSDT) nebo aplikaci SQL Server Management Studio (SSMS) k nasazení vašich balíčků SSIS do Azure. Připojte se k serveru SQL Azure, který hostuje katalog služby SSIS (SSISDB). Název serveru služby Azure SQL Database je ve formátu `<servername>.database.windows.net`. 

Projděte si následující články v dokumentaci ke službě SSIS: 

- [Nasazení, spuštění a monitorování balíčku SSIS v Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Připojení ke katalogu SSIS v Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Plánování spouštění balíčku v Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Připojení k místním zdrojům dat s využitím ověřování systému Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="full-script"></a>Celý skript
Skript PowerShellu v této části nakonfiguruje instanci prostředí Azure SSIS Integration Runtime v cloudu, které spouští balíčky SSIS. Po úspěšném spuštění tohoto skriptu můžete nasazovat a spouštět balíčky SSIS v cloudu Microsoft Azure s databází SSISDB hostovanou ve službě Azure SQL Database.

1. Spusťte integrované skriptovací prostředí (ISE) v prostředí Windows PowerShell.
2. V integrovaném skriptovacím prostředí (ISE) spusťte z příkazového řádku následující příkaz.    
    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```
3. Zkopírujte skript PowerShellu v této části a vložte jej do integrovaného skriptovacího prostředí (ISE).
4. Na začátku skriptu zadejte odpovídající hodnoty pro všechny parametry.
5. Spusťte skript. Příkaz `Start-AzureRmDataFactoryV2IntegrationRuntime` u konce skriptu běží **20 až 30 minut**.

> [!NOTE]
> - Skript se připojí k vašemu serveru služby Azure SQL Database a připraví databázi katalogu služby SSIS (SSISDB).

> - Když zřizujete instanci Azure-SSIS IR, nainstaluje se také Azure Feature Pack for SSIS a Access Redistributable. Tyto komponenty nabízejí mimo připojení k datovým zdrojům podporovaným integrovanými komponentami navíc možnosti připojení k souborům aplikací Excel a Access a různým datovým zdrojům Azure. Můžete nainstalovat také další komponenty. Další informace najdete v tématu [Vlastní nastavení pro prostředí Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

Seznam podporovaných **cenových úrovní** pro službu Azure SQL Database najdete v tématu [Omezení prostředků služby SQL Database](../sql-database/sql-database-resource-limits.md). 

Seznam oblastí podporovaných službou Azure Data Factory V2 a prostředím Azure SSIS Integration Runtime najdete v tématu [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/regions/services/). Rozbalením možnosti **Data a analýzy** zobrazíte **Data Factory V2** a **SSIS Integration Runtime**.

```powershell
# Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
$SubscriptionName = "[Azure subscription name]"
$ResourceGroupName = "[Azure resource group name]"
# Data factory name. Must be globally unique
$DataFactoryName = "[Data factory name]"
$DataFactoryLocation = "EastUS"

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[Specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[Specify a description for your Azure-SSIS IR]"
$AzureSSISLocation = "EastUS"
# In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D4_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B". For standard/premium/Elastic Pool tiers, specify "S0", "S1", "S2", "S3", etc.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>)]"

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

Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
    
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
    
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -SetupScriptContainerSasUri $SetupScriptContainerSasUri `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogAdminCredential $serverCreds `
                                           -CatalogPricingTier $SSISDBPricingTier

write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="join-azure-ssis-ir-to-a-virtual-network"></a>Připojení Azure-SSIS IR k virtuální síti
Pokud k hostování databáze SSISDB používáte službu Azure SQL Database s koncovými body služby virtuální sítě nebo MI (Preview) s připojením k virtuální síti, musíte ke stejné virtuální síti připojit také své prostředí Azure-SSIS Integration Runtime. Azure Data Factory umožňuje připojit prostředí Azure-SSIS Integration Runtime k virtuální síti. Další informace najdete v tématu [Připojení prostředí Azure-SSIS Integration Runtime k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md).

Kompletní skript pro vytvoření prostředí Azure-SSIS Integration Runtime pro připojení k virtuální síti najdete v tématu [Vytvoření prostředí Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md).

## <a name="monitor-and-manage-azure-ssis-ir"></a>Monitorování a správa Azure-SSIS IR
Podrobné informace o monitorování a správě Azure-SSIS IR najdete v následujících článcích. 

- [Monitorování prostředí Azure-SSIS Integration Runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Správa prostředí Azure-SSIS Integration Runtime](manage-azure-ssis-integration-runtime.md)

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili: 

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření prostředí Azure-SSIS Integration Runtime
> * Spuštění prostředí Azure-SSIS Integration Runtime
> * Nasazení balíčků SSIS
> * Kontrola celého skriptu

Pokud se chcete dozvědět víc o kopírování dat z místního prostředí do cloudu, přejděte k následujícímu kurzu: 

> [!div class="nextstepaction"]
>[Kopírování dat v cloudu](tutorial-copy-data-dot-net.md)
