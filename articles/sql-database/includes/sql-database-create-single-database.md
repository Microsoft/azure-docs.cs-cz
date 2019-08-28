---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 07/31/2019
ms.author: mathoma
ms.openlocfilehash: d4c426c5fe31f8fc2bfaf4697c05456124cafcb1
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098995"
---
V tomto kroku vytvoříte Azure SQL Database jedinou databázi. 

> [!IMPORTANT]
> Nezapomeňte nastavit pravidla brány firewall, která budou používat veřejnou IP adresu počítače, který používáte k dokončení tohoto článku.
>
> Informace najdete v tématech [Vytvoření pravidla brány firewall na úrovni databáze](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) nebo určení IP adresy používané pro pravidlo brány firewall na úrovni serveru pro váš počítač najdete v tématu [Vytvoření brány firewall na úrovni serveru](../sql-database-server-level-firewall-rule.md).  

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

Vytvořte skupinu prostředků a jedinou databázi pomocí Azure Portal.

1. V nabídce na levé straně [Azure Portal](https://portal.azure.com)vyberte **Azure SQL** . Pokud **Azure SQL** není v seznamu, vyberte **všechny služby**a do vyhledávacího pole zadejte *Azure SQL* . Volitelné Vyberte hvězdičku vedle **Azure SQL** , kterou chcete oblíbenou, a přidejte ji jako položku v levém navigačním panelu. 
2. Výběrem **+ Přidat** otevřete stránku **vybrat možnost nasazení SQL** . Další informace o různých databázích můžete zobrazit tak, že na dlaždici **databáze** vyberete **Zobrazit podrobnosti** .
3. Vyberte **vytvořit**:

   ![Vytvoření izolované databáze](../media/sql-database-get-started-portal/create-single-database.png)

3. Na kartě **základy** v části **Project Details (podrobnosti projektu** ) zadejte nebo vyberte následující hodnoty:

   - **Předplatné:** Rozevírací seznam a vyberte správné předplatné, pokud se nezobrazí.
   - **Skupina prostředků**: Vyberte **vytvořit nový**, zadejte `myResourceGroup`a vyberte **OK**.

     ![Nová databáze SQL – karta Basic](../media/sql-database-get-started-portal/new-sql-database-basics.png)

4. V části **Podrobnosti databáze** zadejte nebo vyberte následující hodnoty:

   - **Název databáze**: Zadejte `mySampleDatabase`.
   - **Server**: Vyberte **vytvořit nový**, zadejte následující hodnoty a pak vyberte **Vybrat**.
       - **Název serveru**: Typ `mysqlserver`; spolu s některými čísly pro jedinečnost.
       - **Přihlašovací jméno správce serveru**: Zadejte `azureuser`.
       - **Heslo**: Zadejte komplexní heslo, které splňuje požadavky na heslo.
       - **Umístění**: V rozevíracím seznamu vyberte umístění, například `West US`.

         ![Nový server](../media/sql-database-get-started-portal/new-server.png)

      > [!IMPORTANT]
      > Nezapomeňte si zaznamenat přihlašovací jméno a heslo správce serveru, abyste se mohli přihlásit k serveru a databázím pro toto a další rychlé starty. Pokud zapomenete přihlašovací jméno nebo heslo, můžete získat přihlašovací jméno nebo resetovat heslo na stránce **SQL serveru** . Stránku **SQL serveru** otevřete tak, že po vytvoření databáze vyberete název serveru na stránce **Přehled** databáze.

   - **Chcete použít elastický fond SQL**: Vyberte možnost **ne** .
   - **Výpočty a úložiště**: Vyberte **Konfigurovat databázi**. 

     ![Podrobnosti SQL Database](../media/sql-database-get-started-portal/sql-db-basic-db-details.png)

   - Vyberte **zřízené** a **Gen5**.

     ![Zřízené COMPUTE GEN4 –](../media/sql-database-get-started-portal/create-database-provisioned.png)

   - Zkontrolujte nastavení maximálního **počtu virtuální jádra**, **min virtuální jádra**, **zpoždění**při autopauzu a **maximální velikosti dat**. Změňte je podle potřeby.
   - Přijměte podmínky verze Preview a klikněte na **OK**.
   - Vyberte **Použít**.

5. Vyberte kartu **Další nastavení** . 
6. V části **zdroj dat** v části **použít existující data**vyberte `Sample`.

   ![Další nastavení databáze SQL](../media/sql-database-get-started-portal/create-sql-database-additional-settings.png)

   > [!IMPORTANT]
   > Ujistěte se, že jste vybrali ukázková data **(AdventureWorksLT)** , abyste se mohli snadno řídit tímto a dalšími Azure SQL Database rychlými starty, které používají tato data.

7. Zbývající hodnoty ponechte jako výchozí a v dolní části formuláře vyberte **zkontrolovat + vytvořit** .
8. Zkontrolujte poslední nastavení a vyberte **vytvořit**.

9. Na formuláři **SQL Database** vyberte **vytvořit** a nasaďte a zřiďte skupinu prostředků, server a databázi.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Vytvořte skupinu prostředků a izolovanou databázi pomocí prostředí PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   $subscriptionId = '<SubscriptionID>'
   $resourceGroupName = "myResourceGroup-$(Get-Random)"
   $location = "West US"
   $adminLogin = "azureuser"
   $password = "PWD27!"+(New-Guid).Guid
   $serverName = "mysqlserver-$(Get-Random)"
   $databaseName = "mySampleDatabase"

   # The ip address range that you want to allow to access your server 
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName 
   Write-host "Password is" $password  
   Write-host "Server name is" $serverName 

   # Connect to Azure
   Connect-AzAccount

   # Set subscription ID
   Set-AzContext -SubscriptionId $subscriptionId 

   # Create a resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup

   # Create a server with a system wide unique server name
   Write-host "Creating primary logical server..."
   $server = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -Location $location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
      -ArgumentList $adminLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $server

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for primary logical server..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule

   # Create General Purpose Gen4 database with 1 vCore
   Write-host "Creating a gen5 2 vCore database..."
   $database = New-AzSqlDatabase  -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -VCore 2 `
      -ComputeGeneration Gen5 `
      -MinimumCapacity 2 `
      -SampleName "AdventureWorksLT"
   $database
   ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Vytvořte skupinu prostředků a samostatnou databázi pomocí AZ CLI.

   ```azurecli-interactive
   #!/bin/bash
   # Set variables
   subscriptionID=<SubscriptionID>
   resourceGroupName=myResourceGroup-$RANDOM
   location=SouthCentralUS
   adminLogin=azureuser
   password="PWD27!"+`openssl rand -base64 18`
   serverName=mysqlserver-$RANDOM
   databaseName=mySampleDatabase
   drLocation=NorthEurope
   drServerName=mysqlsecondary-$RANDOM
   failoverGroupName=failovergrouptutorial-$RANDOM

   # The ip address range that you want to allow to access your DB. 
   # Leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB
   startip=0.0.0.0
   endip=0.0.0.0
  
   # Connect to Azure
   az login

   # Set the subscription context for the Azure account
   az account set -s $subscriptionID

   # Create a resource group
   echo "Creating resource group..."
   az group create \
      --name $resourceGroupName \
      --location $location \
      --tags Owner[=SQLDB-Samples]

   # Create a logical server in the resource group
   echo "Creating primary logical server..."
   az sql server create \
      --name $serverName \
      --resource-group $resourceGroupName \
      --location $location  \
      --admin-user $adminLogin \
      --admin-password $password

   # Configure a firewall rule for the server
   echo "Configuring firewall..."
   az sql server firewall-rule create \
      --resource-group $resourceGroupName \
      --server $serverName \
      -n AllowYourIp \
      --start-ip-address $startip \
      --end-ip-address $endip

   # Create a gen5 1vCore database in the server 
   echo "Creating a gen5 2 vCore database..."
   az sql db create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --sample-name AdventureWorksLT \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2
   ```

---
