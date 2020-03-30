---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 03/10/2020
ms.author: mathoma
ms.reviewer: vanto
ms.openlocfilehash: c1ca87b6e7b8afb50522e73107707e15782a0a91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79199634"
---
V tomto kroku vytvoříte server Azure SQL Database a jednu databázi, která používá ukázková data AdventureWorksLT. Databázi můžete vytvořit pomocí nabídek a obrazovek portálu Azure nebo pomocí skriptu Azure CLI nebo PowerShellu v Prostředí Azure Cloud Shell.

Všechny metody zahrnují nastavení pravidla brány firewall na úrovni serveru, které umožní veřejnou IP adresu počítače, který používáte pro přístup k serveru. Další informace o vytváření pravidel brány firewall serveru naleznete [v tématu Vytvoření brány firewall na úrovni serveru](../sql-database-server-level-firewall-rule.md). Můžete také nastavit pravidla brány firewall na úrovni databáze. Viz [Vytvoření pravidla brány firewall na úrovni databáze](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database). 

# <a name="portal"></a>[Portál](#tab/azure-portal)

Vytvoření skupiny prostředků, serveru SQL a jedné databáze na webu Azure Portal:

1. Přihlaste se k [portálu](https://portal.azure.com).
1. Na panelu Hledání vyhledejte a vyberte **Azure SQL**.
1. Na stránce **Azure SQL** vyberte **Přidat**. 
   
   ![Přidání do Azure SQL](../media/sql-database-single-database-get-started/sqldbportal.png)
   
1. Na stránce **možnosti Vybrat nasazení SQL** vyberte dlaždici **databáze SQL** s **položkou Jedna databáze** v části Typ **prostředku**. Další informace o různých databázích můžete zobrazit výběrem **možnosti Zobrazit podrobnosti**.
1. Vyberte **Vytvořit**.
   
   ![Vytvoření izolované databáze](../media/sql-database-single-database-get-started/create-single-database.png)
   
1. Na kartě **Základy** formuláře **Vytvořit databázi SQL** vyberte v části **Podrobnosti projektu**správné **předplatné** Azure, pokud ještě není vybrané.
1. V části **Skupina prostředků**vyberte **Vytvořit nový**, zadejte *myResourceGroup*a vyberte **OK**.
1. V části **Podrobnosti databáze**zadejte **název databáze** do *databáze mySampleDatabase*.
1. V **části Server**vyberte Vytvořit **nový**a vyplňte formulář Nový **server** následujícím způsobem:
   - **Název serveru**: Zadejte *mysqlserver*a některé znaky pro jedinečnost.
   - **Přihlášení správce serveru**: Zadejte *azureuser*.
   - **Heslo**: Zadejte heslo, které splňuje požadavky, a znovu ho zadejte do pole **Potvrdit heslo.**
   - **Umístění**: Rozevírací nabídky a zvolte umístění, například **(USA) – východ USA**.
   
   Vyberte **OK**.
   
   ![Nový server](../media/sql-database-single-database-get-started/new-server.png)
   
   Zaznamenejte přihlašovací jméno a heslo správce serveru, abyste se mohli přihlásit k serveru a databázím. Pokud zapomenete své přihlašovací jméno nebo heslo, můžete získat přihlašovací jméno nebo obnovit heslo na stránce **serveru SQL** po vytvoření databáze. Chcete-li otevřít stránku **serveru SQL,** vyberte název serveru na stránce **Přehled** databáze.
   
1. Pokud chcete v části **Compute + storage**změnit konfiguraci výchozích hodnot, vyberte Konfigurovat **databázi**.
   
   Na stránce **Konfigurovat** můžete volitelně:
   - Změňte **výpočetní úroveň** z **zřízená** na **bezserveru**.
   - Zkontrolujte a změňte nastavení **virtuálních jader** a **maximální velikosti dat**.
   - Chcete-li změnit generování hardwaru, vyberte **změnit konfiguraci.**
   
   Po provedení jakýchkoli změn vyberte **Použít**.
   
1. Vyberte **Další: Vytváření sítí** v dolní části stránky.
   
   ![Nová databáze SQL – základní karta](../media/sql-database-single-database-get-started/new-sql-database-basics.png)
   
1. Na kartě **Síť** vyberte v části **Connectivity metodu** **možnost Veřejný koncový bod**. 
1. V části **Pravidla brány firewall**nastavte přidat aktuální ip adresu **klienta** do **yes**.
1. Vyberte **Další: Další nastavení** v dolní části stránky.
   
   ![Karta Síť](../media/sql-database-single-database-get-started/networking.png)
   
   Další informace o nastavení brány firewall najdete v [tématu Povolení přístupu ke službám a prostředkům Azure pro přístup k tomuto serveru](../sql-database-networkaccess-overview.md) a [přidání privátního koncového bodu](../../private-link/private-endpoint-overview.md).
   
1. Na kartě **Další nastavení** v části **Zdroj dat** v části **Použití existujících dat**vyberte **ukázku**.
1. V dolní části stránky vyberte **Zkontrolovat + vytvořit.**
   
   ![Karta Další nastavení](../media/sql-database-single-database-get-started/additional-settings.png)
   
1. Po kontrole nastavení vyberte **Vytvořit**.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pomocí rozhraní příkazového řádku Azure (Azure CLI) můžete vytvořit skupinu prostředků Azure, server SQL a jednu databázi. Pokud nechcete používat Azure Cloud Shell, [nainstalujte azure cli](/cli/azure/install-azure-cli) na vašem počítači.

Pokud chcete spustit následující ukázku kódu v Prostředí Azure Cloud, vyberte **Vyzkoušet** v záhlaví ukázkového kódu. Když se cloudové prostředí otevře, vyberte **Kopírovat** v záhlaví ukázkového kódu a vložte ukázku kódu do okna Cloud Shell. V kódu `<Subscription ID>` nahraďte ID předplatného Azure `$startip` `$endip`a `0.0.0.0` pro a , nahraďte veřejnou IP adresou počítače, který používáte.

Postupujte podle pokynů na obrazovce, abyste se přihlásili do Azure a spusťte kód. 

Azure Cloud Shell můžete taky použít z portálu Azure, když vyberete ikonu Cloud Shell na horním panelu. 
   
   ![Azure Cloud Shell](../media/sql-database-single-database-get-started/cloudshell.png)
   
Při prvním použití cloudového prostředí na portálu **vyberte** bash v **úvodním** dialogovém okně. Následné relace budou používat Azure CLI v prostředí Bash, nebo můžete vybrat **Bash** z ovládacího panelu cloudového prostředí. 

Následující kód Azure CLI vytvoří skupinu prostředků Azure, SQL server, jednu databázi a pravidlo brány firewall pro přístup k serveru. Nezapomeňte zaznamenat vygenerované názvy skupin prostředků a serverů, abyste tyto prostředky mohli spravovat později.

```azurecli-interactive
#!/bin/bash

# Sign in to Azure and set execution context (if necessary)
az login
az account set --subscription <Subscription ID>

# Set the resource group name and location for your server
resourceGroupName=myResourceGroup-$RANDOM
location=westus2

# Set an admin login and password for your database
adminlogin=azureuser
password=Azure1234567

# Set a logical server name that is unique in the system
servername=server-$RANDOM

# Set the ip address range that can access your database
startip=0.0.0.0
endip=0.0.0.0

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a logical server in the resource group
az sql server create \
    --name $servername \
    --resource-group $resourceGroupName \
    --location $location  \
    --admin-user $adminlogin \
    --admin-password $password

# Configure a firewall rule for the server
az sql server firewall-rule create \
    --resource-group $resourceGroupName \
    --server $servername \
    -n AllowYourIp \
    --start-ip-address $startip \
    --end-ip-address $endip

# Create a gen5 2 vCore database in the server
az sql db create \
    --resource-group $resourceGroupName \
    --server $servername \
    --name mySampleDatabase \
    --sample-name AdventureWorksLT \
    --edition GeneralPurpose \
    --family Gen5 \
    --capacity 2 \
```

Předchozí kód používá tyto příkazy Azure CLI:

| Příkaz | Popis |
|---|---|
| [az účet nastaven](/cli/azure/account?view=azure-cli-latest#az-account-set) | Nastaví odběr jako aktuální aktivní předplatné. | 
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Vytvoří databázový server SQL, který hostuje jednotlivé databáze a elastické fondy. |
| [Vytvoření pravidla brány firewall serveru az SQL Server](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-create) | Vytvoří pravidla brány firewall serveru. | 
| [az sql db create](/cli/azure/sql/db#az-sql-db-create?view=azure-cli-latest) | Vytvoří databázi. | 

Další ukázky nastavení příkazového příkazu Azure k Azure v Azure najdete v [tématu ukázky nastavení příkazového příkazu k aplikaci Azure](../sql-database-cli-samples.md).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pomocí prostředí Windows PowerShell můžete vytvořit skupinu prostředků Azure, server SQL a jednu databázi. Pokud nechcete používat Azure Cloud Shell, [nainstalujte modul Azure PowerShell](/powershell/azure/install-az-ps).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pokud chcete spustit následující ukázku kódu v prostředí Azure Cloud Shell, vyberte **Vyzkoušet** v záhlaví kódu. Když se cloudové prostředí otevře, vyberte **Kopírovat** v záhlaví ukázkového kódu a vložte ukázku kódu do okna Cloud Shell. V kódu `<Subscription ID>` nahraďte ID předplatného Azure `$startIp` `$endIp`a `0.0.0.0` pro a , nahraďte veřejnou IP adresou počítače, který používáte. 

Postupujte podle pokynů na obrazovce, abyste se přihlásili do Azure a spusťte kód. 

Azure Cloud Shell můžete taky použít z portálu Azure, když vyberete ikonu Cloud Shell na horním panelu. 
   
   ![Azure Cloud Shell](../media/sql-database-single-database-get-started/cloudshell.png)
   
Při prvním použití cloudového prostředí z portálu vyberte v **úvodním** dialogovém okně **možnost PowerShell.** Následující relace budou používat PowerShell, nebo ho můžete vybrat z ovládacího panelu Cloud Shell. 

Následující kód Prostředí PowerShell vytvoří skupinu prostředků Azure, SQL server, jednu databázi a pravidlo brány firewall pro přístup k serveru. Nezapomeňte zaznamenat vygenerované názvy skupin prostředků a serverů, abyste tyto prostředky mohli spravovat později.

   ```powershell-interactive
   # Set variables for your server and database
   $subscriptionId = '<SubscriptionID>'
   $resourceGroupName = "myResourceGroup-$(Get-Random)"
   $location = "West US"
   $adminLogin = "azureuser"
   $password = "Azure1234567"
   $serverName = "mysqlserver-$(Get-Random)"
   $databaseName = "mySampleDatabase"

   # The ip address range that you want to allow to access your server 
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName 
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

Předchozí kód používá tyto rutiny prostředí PowerShell:

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [Nový-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Vytvoří databázový server SQL, který hostuje jednotlivé databáze a elastické fondy. |
| [Nové pravidlo azsqlserverfirewallfirewall](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Vytvoří pravidlo brány firewall pro logický server. | 
| [Nová databáze AzSql](/powershell/module/az.sql/new-azsqldatabase) | Vytvoří jednu databázi Azure SQL Database. | 

Další ukázky Prostředí PowerShell azure SQL Database najdete v [tématu ukázky Prostředí Azure PowerShell](../sql-database-powershell-samples.md).

---
