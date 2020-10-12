---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 03/10/2020
ms.author: mathoma
ms.reviewer: vanto
ms.openlocfilehash: ad6e444f9672fbe521e9c6963649d250830154d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84046135"
---
V tomto kroku vytvoříte [logický SQL Server](../database/logical-servers.md) a samostatnou [databázi](../database/single-database-overview.md) , která používá ukázková data AdventureWorksLT. Databázi můžete vytvořit pomocí nabídek a obrazovek Azure Portal nebo pomocí skriptu Azure CLI nebo PowerShellu v Azure Cloud Shell.

Všechny metody zahrnují nastavení pravidla brány firewall na úrovni serveru, které umožní veřejnou IP adresu počítače, který používáte pro přístup k serveru. Další informace o vytváření pravidel brány firewall na úrovni serveru najdete v tématu [Vytvoření brány firewall na úrovni serveru](../database/firewall-create-server-level-portal-quickstart.md). Můžete také nastavit pravidla brány firewall na úrovni databáze. Viz [Vytvoření pravidla brány firewall na úrovni databáze](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database).

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Postup vytvoření skupiny prostředků, serveru a izolované databáze v Azure Portal:

1. Přihlaste se k [portálu](https://portal.azure.com).
1. Na panelu hledání vyhledejte a vyberte **Azure SQL**.
1. Na stránce **Azure SQL** vyberte **Přidat**.

   ![Přidat do Azure SQL](./media/sql-database-create-single-database/sqldbportal.png)

1. Na stránce **vybrat možnost nasazení SQL** vyberte dlaždici **databáze SQL** s izolovanou **databází** v části **typ prostředku**. Další informace o různých databázích můžete zobrazit výběrem možnosti **Zobrazit podrobnosti**.
1. Vyberte **Vytvořit**.

   ![Vytvoření izolované databáze](./media/sql-database-create-single-database/create-single-database.png)

1. Na kartě **základy** ve formuláři **vytvořit databázi SQL** v části **Project Details (podrobnosti projektu**) vyberte správné **předplatné** Azure, pokud ještě není vybrané.
1. V části **Skupina prostředků**vyberte **vytvořit novou**, zadejte *myResourceGroup*a vyberte **OK**.
1. V části **Podrobnosti databáze**zadejte do **pole název databáze** *mySampleDatabase*.
1. Pro možnost **Server**vyberte **vytvořit novou**a vyplňte formulář **nového serveru** následujícím způsobem:
   - **Název serveru**: zadejte *MySQLServer*a některé znaky pro jedinečnost.
   - **Přihlašovací jméno správce serveru**: zadejte *azureuser*.
   - **Heslo**: zadejte heslo, které splňuje požadavky, a znovu ho zadejte do pole **Potvrdit heslo** .
   - **Umístění**: rozevírací seznam a vyberte umístění, například **(US) východní USA**.

   Vyberte **OK**.

   ![Nový server](./media/sql-database-create-single-database/new-server.png)

   Zaznamenejte přihlašovací jméno a heslo správce serveru, abyste se mohli přihlásit k serveru a jeho databázím. Pokud zapomenete přihlašovací jméno nebo heslo, můžete po vytvoření databáze získat přihlašovací jméno nebo resetovat heslo na stránce **SQL serveru** . Chcete-li otevřít stránku **systému SQL Server** , vyberte název serveru na stránce **Přehled** databáze.

1. Pokud chcete překonfigurovat výchozí hodnoty, vyberte v části **COMPUTE + úložiště**možnost **Konfigurovat databázi**.

   Na stránce **Konfigurace** můžete volitelně:
   - Změňte **výpočetní vrstvu** ze **zřízené** na bez **serveru**.
   - Zkontrolujte a změňte nastavení pro **virtuální jádra** a **maximální velikost dat**.
   - Vyberte **změnit konfiguraci** pro změnu generování hardwaru.

   Po provedení změn vyberte **použít**.

1. V dolní části stránky vyberte možnost **Další: sítě** .

   ![Nová databáze SQL – karta Basic](./media/sql-database-create-single-database/new-sql-database-basics.png)

1. Na kartě **sítě** v části **způsob připojení**vyberte **veřejný koncový bod**.
1. V části **pravidla brány firewall**nastavte **Přidat aktuální IP adresu klienta** na **Ano**.
1. Vyberte **Další: Další nastavení** v dolní části stránky.

   ![Karta sítě](./media/sql-database-create-single-database/networking.png)
  
   Další informace o nastavení brány firewall najdete v tématu [Povolení služeb a prostředků Azure pro přístup k tomuto serveru](../database/network-access-controls-overview.md) a [Přidání privátního koncového bodu](../database/private-endpoint-overview.md).

1. Na kartě **Další nastavení** v části **zdroj dat** pro možnost **použít existující data**vyberte **Ukázka**.
1. V dolní části stránky vyberte **zkontrolovat + vytvořit** .

   ![Karta Další nastavení](./media/sql-database-create-single-database/additional-settings.png)

1. Po zkontrolování nastavení vyberte **vytvořit**.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pomocí rozhraní příkazového řádku Azure (Azure CLI) můžete vytvořit skupinu prostředků Azure, server a samostatnou databázi. Pokud nechcete používat Azure Cloud Shell, nainstalujte na svém počítači rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli) .

Chcete-li spustit následující ukázku kódu v Azure Cloud Shell, vyberte možnost **vyzkoušet** v záhlaví ukázka kódu. Po otevření Cloud Shell v záhlaví ukázka kódu vyberte možnost **Kopírovat** a vložte ukázku kódu do okna Cloud Shell. V kódu nahraďte `<Subscription ID>` ID předplatného Azure a pro `$startip` a `$endip` nahraďte `0.0.0.0` veřejnou IP adresou počítače, který používáte.

Postupujte podle pokynů na obrazovce a přihlaste se k Azure a spusťte kód.

Azure Cloud Shell můžete použít také z Azure Portal tak, že vyberete ikonu Cloud Shell na horním panelu.

   ![Azure Cloud Shell](./media/sql-database-create-single-database/cloudshell.png)

Při prvním použití Cloud Shell na portálu vyberte v **uvítacím** dialogovém okně **bash** . Následné relace budou používat rozhraní příkazového řádku Azure CLI v prostředí bash nebo můžete vybrat **bash** z ovládacího panelu Cloud Shell.

Následující kód Azure CLI vytvoří skupinu prostředků, server, izolovanou databázi a pravidlo brány firewall IP na úrovni serveru pro přístup k serveru. Ujistěte se, že jste vygenerovali vygenerovanou skupinu prostředků a názvy serverů, abyste tyto prostředky mohli spravovat později.

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

# Set a server name that is unique to Azure DNS (<server_name>.database.windows.net)
servername=server-$RANDOM

# Set the ip address range that can access your database
startip=0.0.0.0
endip=0.0.0.0

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a server in the resource group
az sql server create \
    --name $servername \
    --resource-group $resourceGroupName \
    --location $location  \
    --admin-user $adminlogin \
    --admin-password $password

# Configure a server-level firewall rule for the server
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

Předchozí kód používá tyto příkazy rozhraní příkazového řádku Azure CLI:

| Příkaz | Popis |
|---|---|
| [AZ Account set](/cli/azure/account?view=azure-cli-latest#az-account-set) | Nastaví předplatné jako aktuální aktivní předplatné. |
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Vytvoří server, který je hostitelem databází a elastických fondů. |
| [AZ SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-create) | Vytvoří pravidlo brány firewall na úrovni serveru. |
| [az sql db create](/cli/azure/sql/db#az-sql-db-create?view=azure-cli-latest) | Vytvoří databázi. |

Další Azure SQL Database ukázek rozhraní příkazového řádku Azure najdete v tématu [ukázky Azure CLI](../database/az-cli-script-samples-content-guide.md).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pomocí Windows PowerShellu můžete vytvořit skupinu prostředků, server a samostatnou databázi. Pokud nechcete použít Azure Cloud Shell, [nainstalujte modul Azure PowerShell](/powershell/azure/install-az-ps).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Chcete-li spustit následující ukázku kódu v Azure Cloud Shell, vyberte možnost **vyzkoušet** v záhlaví kódu. Po otevření Cloud Shell v záhlaví ukázka kódu vyberte možnost **Kopírovat** a vložte ukázku kódu do okna Cloud Shell. V kódu nahraďte `<Subscription ID>` ID předplatného Azure a pro `$startIp` a `$endIp` nahraďte `0.0.0.0` veřejnou IP adresou počítače, který používáte.

Postupujte podle pokynů na obrazovce a přihlaste se k Azure a spusťte kód.

Azure Cloud Shell můžete z Azure Portal použít také tak, že na horním panelu vyberete ikonu Cloud Shell.

   ![Azure Cloud Shell](./media/sql-database-create-single-database/cloudshell.png)

Při prvním použití Cloud Shell z portálu vyberte v **uvítacím** dialogovém okně **PowerShell** . Následné relace budou používat PowerShell, nebo je můžete vybrat z ovládacího panelu Cloud Shell.

Následující kód PowerShellu vytvoří skupinu prostředků Azure, server, izolovanou databázi a pravidlo brány firewall pro přístup k serveru. Ujistěte se, že jste vygenerovali vygenerovanou skupinu prostředků a názvy serverů, abyste tyto prostředky mohli spravovat později.

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
   Write-host "Creating primary server..."
   $server = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -Location $location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
      -ArgumentList $adminLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $server

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for primary server..."
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

Předchozí kód používá tyto rutiny PowerShellu:

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Vytvoří server, který je hostitelem databází a elastických fondů. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Vytvoří pravidlo brány firewall na úrovni serveru pro server. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Vytvoří databázi. |

Další Azure SQL Database ukázek PowerShellu najdete v tématu [Azure PowerShell Samples](../database/powershell-script-content-guide.md).

---
