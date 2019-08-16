---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 06/19/2019
ms.author: mathoma
ms.openlocfilehash: eff121cfaf4473607110de4553a9bb8021990caf
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "68444679"
---
V tomto kroku vytvoříte skupinu prostředků a Azure SQL Database izolovanou databázi. 

> [!IMPORTANT]
> Nezapomeňte nastavit pravidla brány firewall tak, aby používala veřejnou IP adresu počítače, na kterém provedete kroky v tomto článku. Pravidla brány firewall na úrovni databáze se automaticky replikují na sekundární server.
>
> Informace najdete v tématu vytvoření [pravidla brány firewall na úrovni databáze](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) nebo určení IP adresy používané pro pravidlo brány firewall na úrovni serveru pro váš počítač v tématu [Vytvoření brány firewall na úrovni serveru](../articles/sql-database/sql-database-server-level-firewall-rule.md).  

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Vytvořte skupinu prostředků a jedinou databázi pomocí Azure Portal. 

1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek**.
2. Vyberte **databáze** a pak výběrem **SQL Database** otevřete stránku **vytvořit SQL Database** .

   ![Vytvoření izolované databáze](../articles/sql-database/media/sql-database-get-started-portal/create-database-1.png)

3. Na kartě **základy** v části **Project Details (podrobnosti projektu** ) zadejte nebo vyberte následující hodnoty:

   - **Předplatné**: Rozevírací seznam a vyberte správné předplatné, pokud se nezobrazí.
   - **Skupina prostředků**: Vyberte **vytvořit nový**, zadejte `myResourceGroup`a vyberte **OK**.

     ![Nová databáze SQL – karta Basic](../articles/sql-database/media/sql-database-get-started-portal/new-sql-database-basics.png)

4. V části **Podrobnosti databáze** zadejte nebo vyberte následující hodnoty:

   - **Název databáze**: Zadejte `mySampleDatabase`.
   - **Server**: Vyberte **vytvořit novou** a zadejte následující hodnoty a pak vyberte **Vybrat**.
       - **Název serveru**: Typ `mysqlserver`; spolu s některými čísly pro jedinečnost.
       - **Přihlašovací jméno správce serveru**: Zadejte `azureuser`.
       - **Heslo**: Zadejte komplexní heslo, které splňuje požadavky na heslo.
       - **Umístění**: V rozevíracím seznamu vyberte umístění, například `West US 2`.

         ![Nový server](../articles/sql-database/media/sql-database-get-started-portal/new-server.png)

      > [!IMPORTANT]
      > Nezapomeňte si zaznamenat přihlašovací jméno a heslo správce serveru, abyste se mohli přihlásit k serveru a databázím pro toto a další rychlé starty. Pokud zapomenete přihlašovací jméno nebo heslo, můžete získat přihlašovací jméno nebo resetovat heslo na stránce **SQL serveru** . Stránku **SQL serveru** otevřete tak, že po vytvoření databáze vyberete název serveru na stránce **Přehled** databáze.

        ![Podrobnosti SQL Database](../articles/sql-database/media/sql-database-get-started-portal/sql-db-basic-db-details.png)

   - **Chcete použít elastický fond SQL**: Vyberte možnost **ne** .
   - **Výpočty a úložiště**: Vyberte možnost **Konfigurovat databázi** a pro tento rychlý Start vyberte **možnosti nákupu na základě Vcore**

     ![Možnosti nákupu na základě virtuálních jader](../articles/sql-database/media/sql-database-get-started-portal/create-database-vcore.png)

   - Vyberte **zřízené** a **COMPUTE GEN4 –** .

     ![výpočetní vrstva bez serveru](../articles/sql-database/media/sql-database-get-started-portal/create-database-serverless.png)

   - Zkontrolujte nastavení pro **Maximum virtuální jádra**, **min virtuální jádra**, **zpoždění automatického pozastavení**a **maximální velikosti dat**. Změňte je podle potřeby.
   - Přijměte podmínky verze Preview a klikněte na **OK**.
   - Vyberte **Použít**.

5. Vyberte kartu **Další nastavení** . 
6. V části **zdroj dat** v části **použít existující data**vyberte `Sample`. 

   ![Další nastavení databáze SQL](../articles/sql-database/media/sql-database-get-started-portal/create-sql-database-additional-settings.png)

   > [!IMPORTANT]
   > Ujistěte se, že jste vybrali ukázková data **(AdventureWorksLT)** , abyste se mohli snadno řídit tímto a dalšími Azure SQL Database rychlými starty, které používají tato data.

7. Zbývající hodnoty ponechte jako výchozí a v dolní části formuláře vyberte **zkontrolovat + vytvořit** .
8. Zkontrolujte poslední nastavení a vyberte **vytvořit**.

9. Na formuláři **SQL Database** vyberte **vytvořit** a nasaďte a zřiďte skupinu prostředků, server a databázi.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

[!INCLUDE [updated-for-az](updated-for-az.md)]

Vytvořte skupinu prostředků a izolovanou databázi pomocí prostředí PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   $SubscriptionId = '<Your Azure subscription ID>'
   $ResourceGroupName = "myResourceGroup" # to randomize: "myResourceGroup-$(Get-Random)"
   $Location = "westus2"
   $AdminLogin = "azureuser"
   $Password = "ChangeYourAdminPassword1"
   $ServerName = "mysqlserver" # to randomize: "mysqlserver-$(Get-Random)"
   $DatabaseName = "mySampleDatabase"
   
   
   # The ip address range that you want to allow to access your server 
   #(leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"
   
   # Connect to Azure
   Connect-AzAccount

   # Set subscription ID
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create a resource group
   $resourceGroup = New-AzResourceGroup -Name $ResourceGroupName -Location $Location
   
   # Create a server with a system wide unique server name
   $server = New-AzSqlServer -ResourceGroupName $ResourceGroupName `
      -ServerName $ServerName `
      -Location $Location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $AdminLogin, $(ConvertTo-SecureString -String $Password -AsPlainText -Force))
   
   # Create a server firewall rule that allows access from the specified IP range
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
      -ServerName $ServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   
   # Create General Purpose Gen4 database with 1 vCore
   $database = New-AzSqlDatabase  -ResourceGroupName $ResourceGroupName `
      -ServerName $ServerName `
      -DatabaseName $DatabaseName `
      -Edition GeneralPurpose `
      -VCore 1 `
      -ComputeGeneration Gen4  `
      -MinimumCapacity 1 `
      -SampleName "AdventureWorksLT"
   ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)
Vytvořte skupinu prostředků a samostatnou databázi pomocí AZ CLI. 


   ```azurecli-interactive
   #!/bin/bash
   # Set variables
   export subscriptionID=<Your Subscription ID>
   export ResourceGroupName=myResourceGroup # to randomize: myResourceGroup-$RANDOM
   export Location=WestUS2
   export AdminLogin=azureuser
   export Password=ChangeYourAdminPassword1
   export ServerName="mysqlserver" # to randomize: mysqlserver-$RANDOM
   export DatabaseName=mySampleDatabase
   
   # The ip address range that you want to allow to access your DB. 
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   export startip=0.0.0.0
   export endip=0.0.0.0
  
   # Connect to Azure
   az login

   $ Set subscription ID
   az account set --subscription $SubscriptionID
   
   # Create a resource group
   az group create \
      --name $ResourceGroupName \
      --location $Location
   
   # Create a logical server in the resource group
   az sql server create \
      --name $ServerName \
      --resource-group $ResourceGroupName \
      --location $Location  \
      --admin-user $AdminLogin\
      --admin-password $Password
   
   # Configure a firewall rule for the server
   az sql server firewall-rule create \
      --resource-group $ResourceGroupName \
      --server $ServerName \
      -n AllowYourIp \
      --start-ip-address $startip \
      --end-ip-address $endip
   
   # Create a database in the server 
   az sql db create \
      --resource-group $ResourceGroupName \
      --server $ServerName \
      --name $DatabaseName \
      --sample-name AdventureWorksLT \
      --edition GeneralPurpose \
      --family Gen4 \
      --capacity 1 \
   ```

---