---
title: Vytvoření izolované databáze
description: Vytvořte v Azure SQL Database izolovanou databázi pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/03/2020
ms.openlocfilehash: cb7a441a6d86cd64847300d1e597cf6f86067282
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/23/2020
ms.locfileid: "97740942"
---
# <a name="quickstart-create-an-azure-sql-database-single-database"></a>Rychlý Start: vytvoření samostatné databáze Azure SQL Database

V tomto rychlém startu vytvoříte v Azure SQL Database [jednu databázi](single-database-overview.md) pomocí Azure Portal, skriptu PowerShellu nebo skriptu Azure CLI. Pak Dotazujte databázi pomocí **Editoru dotazů** v Azure Portal.



## <a name="prerequisite"></a>Požadavek

- Musíte mít aktivní předplatné Azure. Pokud žádné nemáte, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/).

## <a name="create-a-single-database"></a>Vytvoření izolované databáze

V tomto rychlém startu se vytvoří jedna databáze na [výpočetní úrovni bez serveru](serverless-tier-overview.md).

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Pokud chcete v Azure Portal vytvořit izolovanou databázi, spustí se v tomto rychlém startu na stránce Azure SQL.

1. Přejděte na stránku [možnost vybrat nasazení systému SQL](https://portal.azure.com/#create/Microsoft.AzureSQL) .
1. V části **databáze SQL** ponechte **typ prostředku** nastavený na izolovaná **databáze** a vyberte **vytvořit**.

   ![Přidat do Azure SQL](./media/single-database-create-quickstart/select-deployment.png)

1. Na kartě **základy** formuláře **vytvořit SQL Database** v části **Project Details (podrobnosti projektu**) vyberte požadované **předplatné** Azure.
1. V případě **skupiny prostředků** vyberte **vytvořit novou**, zadejte *myResourceGroup* a vyberte **OK**.
1. Jako **název databáze** zadejte *mySampleDatabase*.
1. Pro možnost **Server** vyberte **vytvořit novou** a vyplňte formulář **nového serveru** následujícími hodnotami:
   - **Název serveru**: zadejte *MySQLServer* a přidejte některé znaky pro jedinečnost. Nemůžeme zadat přesný název serveru, protože názvy serverů musí být globálně jedinečné pro všechny servery v Azure, ne jen jedinečné v rámci předplatného. Zadejte tak něco jako mysqlserver12345 a portál vám umožní zjistit, jestli je k dispozici, nebo ne.
   - **Přihlašovací jméno správce serveru**: zadejte *azureuser*.
   - **Heslo**: zadejte heslo, které splňuje požadavky, a znovu ho zadejte do pole **Potvrdit heslo** .
   - **Umístění**: v rozevíracím seznamu vyberte umístění.

   Vyberte **OK**.

1. Nechte **použít elastický fond SQL** nastavený na **ne**.
1. V části **Výpočty + úložiště** vyberte **Konfigurovat databázi**.
1. V tomto rychlém startu se používá databáze bez serveru, vyberte možnost bez **serveru** a pak vyberte **použít**. 

      ![Konfigurace databáze bez serveru](./media/single-database-create-quickstart/configure-database.png)

1. V dolní části stránky vyberte možnost **Další: sítě** .

   ![Nová databáze SQL – karta Basic](./media/single-database-create-quickstart/new-sql-database-basics.png)

1. Na kartě **sítě** pro **metodu připojení** vyberte **veřejný koncový bod**.
1. Pro **pravidla brány firewall** nastavte **Přidat aktuální IP adresu klienta** na **Ano**. Nechejte **službám a prostředkům Azure přístup k tomuto serveru** nastavenému na **ne**.
1. Vyberte **Další: Další nastavení** v dolní části stránky.

   ![Karta sítě](./media/single-database-create-quickstart/networking.png)
  

1. Na kartě **Další nastavení** v části **zdroj dat** pro možnost **použít existující data** vyberte **Ukázka**. Tím se vytvoří ukázková databáze AdventureWorksLT, aby byly k dispozici některé tabulky a data pro dotazování a experimentování s, a to na rozdíl od prázdné prázdné databáze.
1. V dolní části stránky vyberte **zkontrolovat + vytvořit** :

   ![Karta Další nastavení](./media/single-database-create-quickstart/additional-settings.png)

1. Po kontrole vyberte na stránce **Revize + vytvořit** možnost **vytvořit**.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče tak, že přejdete na [https://shell.azure.com](https://shell.azure.com) . Vyberte **Kopírovat** pro zkopírování bloků kódu, vložení do Cloud Shell a stisknutím klávesy **ENTER** ji spusťte.

## <a name="set-parameter-values"></a>Nastavení hodnot parametrů

Následující hodnoty se používají v dalších příkazech k vytvoření databáze a požadovaných prostředků. Názvy serverů musí být globálně jedinečné napříč všemi verzemi Azure, takže se k vytvoření názvu serveru používá funkce $RANDOM. Nahraďte hodnoty 0.0.0.0 v rozsahu IP adres tak, aby odpovídaly vašemu konkrétnímu prostředí.

```azurecli-interactive
# Set the resource group name and location for your server
resourceGroupName=myResourceGroup
location=eastus

# Set an admin login and password for your database
adminlogin=azureuser
password=Azure1234567!

# Set a server name that is unique to Azure DNS (<server_name>.database.windows.net)
serverName=server-$RANDOM

# Set the ip address range that can access your database
startip=0.0.0.0
endip=0.0.0.0
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus* :

```azurecli-interactive
az group create --name $resourceGroupName --location $location
```

## <a name="create-a-server"></a>Vytvoření serveru

Vytvořte server pomocí příkazu [AZ SQL Server Create](/cli/azure/sql/server) .

```azurecli-interactive
az sql server create \
    --name $serverName \
    --resource-group $resourceGroupName \
    --location $location  \
    --admin-user $adminlogin \
    --admin-password $password
```


## <a name="configure-a-firewall-rule-for-the-server"></a>Konfigurace pravidla brány firewall pro server

Pomocí příkazu [AZ SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule) vytvořte pravidlo brány firewall.

```azurecli-interactive
az sql server firewall-rule create \
    --resource-group $resourceGroupName \
    --server $serverName \
    -n AllowYourIp \
    --start-ip-address $startip \
    --end-ip-address $endip
```


## <a name="create-a-single-database-with-azure-cli"></a>Vytvoření izolované databáze pomocí Azure CLI

Vytvořte databázi pomocí příkazu [AZ SQL DB Create](/cli/azure/sql/db) . Následující kód vytvoří


```azurecli-interactive
az sql db create \
    --resource-group $resourceGroupName \
    --server $serverName \
    --name mySampleDatabase \
    --sample-name AdventureWorksLT \
    --edition GeneralPurpose \
    --compute-model Serverless \
    --family Gen5 \
    --capacity 2
```


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pomocí Windows PowerShellu můžete vytvořit skupinu prostředků, server a samostatnou databázi.

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče tak, že přejdete na [https://shell.azure.com](https://shell.azure.com) . Vyberte **Kopírovat** pro zkopírování bloků kódu, vložení do Cloud Shell a stisknutím klávesy **ENTER** ji spusťte.

## <a name="set-parameter-values"></a>Nastavení hodnot parametrů

Následující hodnoty se používají v dalších příkazech k vytvoření databáze a požadovaných prostředků. Názvy serverů musí být globálně jedinečné napříč všemi Azure, aby se k vytvoření názvu serveru použila rutina Get-Random. Nahraďte hodnoty 0.0.0.0 v rozsahu IP adres tak, aby odpovídaly vašemu konkrétnímu prostředí.

```azurepowershell-interactive
   # Set variables for your server and database
   $resourceGroupName = "myResourceGroup"
   $location = "eastus"
   $adminLogin = "azureuser"
   $password = "Azure1234567!"
   $serverName = "mysqlserver-$(Get-Random)"
   $databaseName = "mySampleDatabase"

   # The ip address range that you want to allow to access your server
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Server name is" $serverName
```


## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

```azurepowershell-interactive
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup
```


## <a name="create-a-server"></a>Vytvoření serveru

Vytvořte server pomocí rutiny [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) .

```azurepowershell-interactive
  Write-host "Creating primary server..."
   $server = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -Location $location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
      -ArgumentList $adminLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $server
```

## <a name="create-a-firewall-rule"></a>Vytvoření pravidla brány firewall

Vytvořte pravidlo brány firewall serveru pomocí rutiny [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) .

```azurepowershell-interactive
   Write-host "Configuring server firewall rule..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule
```


## <a name="create-a-single-database-with-powershell"></a>Vytvoření izolované databáze pomocí PowerShellu

Vytvořte izolovanou databázi pomocí rutiny [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) .

```azurepowershell-interactive
   Write-host "Creating a gen5 2 vCore serverless database..."
   $database = New-AzSqlDatabase  -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -ComputeModel Serverless `
      -ComputeGeneration Gen5 `
      -VCore 2 `
      -MinimumCapacity 2 `
      -SampleName "AdventureWorksLT"
   $database
```

---



## <a name="query-the-database"></a>Dotazování databáze

Po vytvoření databáze můžete použít **Editor dotazů (Preview)** v Azure Portal pro připojení k databázi a dotazování na data.

1. Na portálu vyhledejte a vyberte **databáze SQL** a pak ze seznamu vyberte svou databázi.
1. Na stránce databáze v levé nabídce vyberte **Editor dotazů (Preview)** .
1. Zadejte přihlašovací údaje správce serveru a vyberte **OK**.

   ![Přihlášení k editoru dotazů](./media/single-database-create-quickstart/query-editor-login.png)

1. Do podokna **Editoru dotazů** zadejte následující dotaz.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

1. Vyberte **Spustit** a potom v podokně **Výsledky** zkontrolujte výsledky dotazu.

   ![Výsledky editoru dotazů](./media/single-database-create-quickstart/query-editor-results.png)

1. Zavřete stránku **Editor dotazů** a po zobrazení výzvy k zahození neuložených úprav vyberte **OK** .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Ponechte skupinu prostředků, server a izolovanou databázi, abyste přešli na další kroky, a Naučte se, jak se připojit a dotazovat se na databázi pomocí různých metod.

Po dokončení používání těchto prostředků můžete odstranit vytvořenou skupinu prostředků, která také odstraní Server a samostatnou databázi.

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Chcete-li odstranit **myResourceGroup** a všechny jeho prostředky pomocí Azure Portal:

1. Na portálu vyhledejte a vyberte **skupiny prostředků** a v seznamu vyberte **myResourceGroup** .
1. Na stránce skupina prostředků vyberte **Odstranit skupinu prostředků**.
1. V části **Zadejte název skupiny prostředků** zadejte *myResourceGroup* a pak vyberte **Odstranit**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete odstranit skupinu prostředků a všechny její prostředky, spusťte následující příkaz rozhraní příkazového řádku Azure a použijte název vaší skupiny prostředků:

```azurecli-interactive
az group delete --name $resourceGroupName
```

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete odstranit skupinu prostředků a všechny její prostředky, spusťte následující rutinu PowerShellu s použitím názvu vaší skupiny prostředků:

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
```

---

## <a name="next-steps"></a>Další kroky

[Připojení a dotazování](connect-query-content-reference-guide.md) databáze pomocí různých nástrojů a jazyků:
> [!div class="nextstepaction"]
> [Připojení a dotazování pomocí SQL Server Management Studia](connect-query-ssms.md)
>
> [Připojení a dotazování pomocí Azure Data Studia](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)

Chcete optimalizovat a uložit své útraty do cloudu?

> [!div class="nextstepaction"]
> [Zahájení analýzy nákladů pomocí Cost Management](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)