---
title: 'Azure PowerShell: Vytvoření databáze SQL | Microsoft Docs'
description: Naučíte se vytvářet logický server služby SQL Database, pravidlo brány firewall na úrovni serveru a databáze na webu Azure Portal.
keywords: kurz k sql database, vytvoření databáze sql
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,DBs & servers
ms.devlang: PowerShell
ms.topic: quickstart
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 91c92de4d7c94cceec69b19647b1fe0bf31915c4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32186024"
---
# <a name="create-a-single-azure-sql-database-using-powershell"></a>Vytvoření izolované databáze SQL Azure pomocí PowerShellu

PowerShell slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. Tento průvodce podrobně uvádí, jak pomocí PowerShellu nasadit databázi SQL Azure ve [skupině prostředků Azure](../azure-resource-manager/resource-group-overview.md) na [logický server Azure SQL Database](sql-database-features.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

Tento kurz vyžaduje modul Azure PowerShell verze 4.0 nebo novější. Verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k předplatnému Azure pomocí příkazu [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) a postupujte podle pokynů na obrazovce.

```powershell
Connect-AzureRmAccount
```

## <a name="create-variables"></a>Vytvoření proměnných

Definujte proměnné, které se použijí ve skriptech v tomto rychlém startu.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The logical server name: Use a random value or replace with your own value (do not capitalize)
$servername = "server-$(Get-Random)"
# Set an admin login and password for your database
# The login information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDatabase"
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte [skupinu prostředků Azure](../azure-resource-manager/resource-group-overview.md) pomocí příkazu [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky jako skupina. Následující příklad vytvoří skupinu prostředků s názvem `myResourceGroup` v umístění `westeurope`.

```powershell
New-AzureRmResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>Vytvoření logického serveru

Vytvořte [logický server Azure SQL Database](sql-database-features.md) pomocí příkazu [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver). Logický server obsahuje soubor databází spravovaných jako skupina. Následující příklad vytvoří ve skupině prostředků náhodně pojmenovaný server s přihlašovacím jménem správce `ServerAdmin` a heslem `ChangeYourAdminPassword1`. Podle potřeby tyto předdefinované hodnoty nahraďte.

```powershell
New-AzureRmSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Konfigurace pravidla brány firewall serveru

Vytvořte [pravidlo brány firewall na úrovni serveru služby Azure SQL Database](sql-database-firewall-configure.md) pomocí příkazu [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule). Pravidlo brány firewall na úrovni serveru umožňuje externí aplikaci, jako je SQL Server Management Studio nebo nástroj SQLCMD, připojení k databázi SQL přes bránu firewall služby SQL Database. V následujícím příkladu je brána firewall otevřená pouze pro ostatní prostředky Azure. Pokud chcete povolit externí připojení, změňte IP adresu na příslušnou adresu pro vaše prostředí. Chcete-li otevřít všechny IP adresy, použijte jako počáteční IP adresu 0.0.0.0 a jako koncovou adresu 255.255.255.255.

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL Database komunikuje přes port 1433. Pokud se pokoušíte připojit z podnikové sítě, nemusí být odchozí provoz přes port 1433 bránou firewall vaší sítě povolený. Pokud je to tak, nebudete se moct připojit k serveru Azure SQL Database, dokud vaše IT oddělení neotevře port 1433.
>

## <a name="create-a-database-in-the-server-with-sample-data"></a>Vytvoření databáze s ukázkovými daty na serveru

Vytvořte na serveru databázi s [úrovní výkonu S0](sql-database-service-tiers-dtu.md) pomocí příkazu [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase). Následující příklad vytvoří databázi s názvem `mySampleDatabase` a načte do této databáze ukázková data AdventureWorksLT. Tyto předdefinované hodnoty nahraďte podle potřeby (další rychlé starty v této kolekci jsou postavené na tomto rychlém startu).

```powershell
New-AzureRmSqlDatabase  -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -SampleName "AdventureWorksLT" `
    -RequestedServiceObjectiveName "S0"
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlý start v této kolekci je postavený na tomto rychlém startu.

> [!TIP]
> Pokud chcete pokračovat v práci s dalším rychlým startem, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí následujících kroků odstraňte všechny prostředky vytvořené tímto rychlým startem na portálu Azure Portal.
>

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Další kroky

- Teď, když máte databázi, můžete se k ní [připojit a provádět dotazování](sql-database-connect-query.md) pomocí některého z vašich oblíbených nástrojů nebo jazyků.
- Informace o návrhu první databáze, vytváření tabulek a vkládání dat najdete v následujících kurzech:
 - [Návrh první databáze SQL Azure pomocí SSMS](sql-database-design-first-database.md)
  - [Návrh databáze SQL Azure SQL database a její připojení pomocí C# a ADO.NET](sql-database-design-first-database-csharp.md)

