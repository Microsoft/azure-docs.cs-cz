---
title: Konfigurovat časový interval pro správu a údržbu (Preview)
description: Naučte se, jak nastavit čas, kdy se má plánovaná údržba provádět ve vašich databázích SQL Azure, elastických fondech a databázích spravované instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/23/2021
ms.openlocfilehash: 8688458d85084f3d3dab4678fa91ed827a337739
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047347"
---
# <a name="configure-maintenance-window-preview"></a>Konfigurovat časový interval pro správu a údržbu (Preview)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


Nakonfigurujte časové [období údržby (Preview)](maintenance-window.md) pro databázi SQL Azure, elastický fond nebo databázi spravované instance SQL Azure během vytváření prostředků nebo kdykoli po vytvoření prostředku. 

Výchozí časové období údržby *systému* je 17:00 8:00 denně (místní čas oblasti Azure, ve které je prostředek umístěný), aby se předešlo výpadkům v pracovní době špičky. Pokud výchozí časové období údržby *systému* není nejlepší čas, vyberte jedno z dalších dostupných časových období údržby.

Možnost změny v jiném časovém intervalu pro správu a údržbu není k dispozici pro každou úroveň služby nebo v každé oblasti. Podrobnosti o dostupnosti najdete v tématu [dostupnost okna údržby](maintenance-window.md#availability).

> [!Important]
> Konfigurace časového období údržby je dlouhodobě spuštěná asynchronní operace, podobně jako změna úrovně služby prostředku SQL Azure. Prostředek je k dispozici během operace s výjimkou krátké rekonfigurace, která se na konci operace stane, a obvykle trvá až 8 sekund i v případě přerušených dlouhotrvajících transakcí. Chcete-li minimalizovat dopad rekonfigurace, měli byste provést operaci mimo špičku.

## <a name="configure-maintenance-window-during-database-creation"></a>Konfigurovat časové období údržby během vytváření databáze 

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Chcete-li nakonfigurovat časové období údržby při vytváření databáze, elastického fondu nebo spravované instance, nastavte požadované **okno údržby** na stránce **Další nastavení** . 

## <a name="set-the-maintenance-window-while-creating-a-single-database-or-elastic-pool"></a>Nastavení časového období údržby při vytváření jedné databáze nebo elastického fondu

Podrobné informace o vytvoření nové databáze nebo fondu najdete v tématu [vytvoření Azure SQL Database izolované databáze](single-database-create-quickstart.md).

   :::image type="content" source="media/maintenance-window-configure/additional-settings.png" alt-text="Vytvořit další kartu nastavení databáze":::


## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Nastavení časového období údržby při vytváření spravované instance

Podrobné informace o vytvoření nové spravované instance najdete v tématu [Vytvoření spravované instance Azure SQL](../managed-instance/instance-create-quickstart.md).

   :::image type="content" source="media/maintenance-window-configure/additional-settings-mi.png" alt-text="Vytvořit další kartu nastavení spravované instance":::




# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Následující příklady ukazují, jak nakonfigurovat okno údržby pomocí Azure PowerShell. Můžete [nainstalovat Azure PowerShell](/powershell/azure/install-az-ps)nebo použít Azure Cloud Shell.

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče tak, že přejdete na [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.


## <a name="discover-available-maintenance-windows"></a>Zjistit dostupná časová období údržby

Při nastavování časového intervalu pro správu a údržbu má každá oblast vlastní možnosti okna údržby, které odpovídají časovému pásmu pro oblast, ve které je databáze nebo fond umístěný. 

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>Zjišťování oken údržby SQL Database a elastického fondu 

Následující příklad vrátí dostupná okna údržby pro oblast *eastus2* pomocí rutiny [Get-AzMaintenancePublicConfiguration](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) . Pro databáze a elastické fondy nastavte `MaintenanceScope` na `SQLDB` .

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLDB"}
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>Vyhledat okna údržby spravované instance SQL 

Následující příklad vrátí dostupná okna údržby pro oblast *eastus2* pomocí rutiny [Get-AzMaintenancePublicConfiguration](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) . U spravovaných instancí nastavte `MaintenanceScope` na `SQLManagedInstance` .

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLManagedInstance"}
   ```


## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>Nastavení časového období údržby při vytváření izolované databáze

Následující příklad vytvoří novou databázi a nastaví časové období údržby pomocí rutiny [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) . `-MaintenanceConfigurationId`Hodnota musí být nastavena na platnou hodnotu pro oblast vaší databáze. Pokud chcete získat platné hodnoty pro vaši oblast, přečtěte si téma [zjišťování dostupných oken údržby](#discover-available-maintenance-windows).


   ```powershell-interactive
    # Set variables for your database
    $resourceGroupName = "your_resource_group_name"
    $serverName = "your_server_name"
    $databaseName = "your_db_name"
    
    # Set selected maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_1"

    Write-host "Creating a gen5 2 vCore database with maintenance window ${maintenanceConfig} ..."
    $database = New-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -ComputeGeneration Gen5 `
      -VCore 2 `
      -MaintenanceConfigurationId $maintenanceConfig
    $database
   ```



## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>Nastavení časového období údržby při vytváření elastického fondu

Následující příklad vytvoří nový elastický fond a nastaví časové období údržby pomocí rutiny [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) . Časové období údržby je nastavené v elastickém fondu, takže všechny databáze ve fondu mají plán časového intervalu pro správu fondu. `-MaintenanceConfigurationId`Hodnota musí být nastavena na platnou hodnotu pro oblast fondu. Pokud chcete získat platné hodnoty pro vaši oblast, přečtěte si téma [zjišťování dostupných oken údržby](#discover-available-maintenance-windows).


   ```powershell-interactive
    # Set variables for your pool
    $resourceGroupName = "your_resource_group_name"
    $serverName = "your_server_name"
    $poolName = "your_pool_name"
    
    # Set selected maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_2"

    Write-host "Creating a Standard 50 pool with maintenance window ${maintenanceConfig} ..."
    $pool = New-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName `
      -Edition "Standard" `
      -Dtu 50 `
      -DatabaseDtuMin 10 `
      -DatabaseDtuMax 20 `
      -MaintenanceConfigurationId $maintenanceConfig
    $pool
   ```

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Nastavení časového období údržby při vytváření spravované instance

Následující příklad vytvoří novou spravovanou instanci a nastaví časové období údržby pomocí rutiny [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) . Časové období údržby je nastaveno na instanci, takže všechny databáze v instanci mají plán časového intervalu pro správu a údržbu instance. `-MaintenanceConfigurationId`V případě musí být *MaintenanceConfigName* platnou hodnotou pro oblast vaší instance. Pokud chcete získat platné hodnoty pro vaši oblast, přečtěte si téma [zjišťování dostupných oken údržby](#discover-available-maintenance-windows).


   ```powershell
   New-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -Location "your_mi_location" `
     -SubnetId /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/azure-cli)

Následující příklady ukazují, jak nakonfigurovat okno údržby pomocí Azure CLI. Můžete [nainstalovat rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli)nebo použít Azure Cloud Shell. 

Konfigurace časového intervalu pro správu a údržbu pomocí Azure CLI je dostupná jenom pro spravovanou instanci SQL.

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. 

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče tak, že přejdete na [https://shell.azure.com/cli](https://shell.azure.com/cli) . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

## <a name="discover-available-maintenance-windows"></a>Zjistit dostupná časová období údržby

Při nastavování časového intervalu pro správu a údržbu má každá oblast vlastní možnosti okna údržby, které odpovídají časovému pásmu pro oblast, ve které je databáze nebo fond umístěný.

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>Zjišťování oken údržby SQL Database a elastického fondu

Následující příklad vrátí dostupná časová období údržby pro oblast *eastus2* pomocí příkazu [AZ Maintenance Public-Configuration list](/cli/azure/ext/maintenance/maintenance/public-configuration#ext_maintenance_az_maintenance_public_configuration_list) . Pro databáze a elastické fondy nastavte `maintenanceScope` na `SQLDB` .

   ```azurecli
   location="eastus2"

   az maintenance public-configuration list --query "[?location=='$location'&&contains(maintenanceScope,'SQLDB')]"
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>Vyhledat okna údržby spravované instance SQL

Následující příklad vrátí dostupná časová období údržby pro oblast *eastus2* pomocí příkazu [AZ Maintenance Public-Configuration list](/cli/azure/ext/maintenance/maintenance/public-configuration#ext_maintenance_az_maintenance_public_configuration_list) . U spravovaných instancí nastavte `maintenanceScope` na `SQLManagedInstance` .

   ```azurecli
   az maintenance public-configuration list --query "[?location=='eastus2'&&contains(maintenanceScope,'SQLManagedInstance')]"
   ```

## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>Nastavení časového období údržby při vytváření izolované databáze

Následující příklad vytvoří novou databázi a nastaví časové období údržby pomocí příkazu [AZ SQL DB Create](/cli/azure/sql/db#az_sql_db_create) . `--maint-config-id`Hodnota (nebo `-m` ) musí být nastavena na platnou hodnotu pro oblast databáze. Pokud chcete získat platné hodnoty pro vaši oblast, přečtěte si téma [zjišťování dostupných oken údržby](#discover-available-maintenance-windows).


   ```azurecli
    # Set variables for your database
    resourceGroupName="your_resource_group_name"
    serverName="your_server_name"
    databaseName="your_db_name"

    # Set selected maintenance window
    maintenanceConfig="SQL_EastUS2_DB_1"

    # Create database
    az sql db create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2 \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>Nastavení časového období údržby při vytváření elastického fondu

Následující příklad vytvoří nový elastický fond a nastaví časové období údržby pomocí rutiny [AZ SQL elastický fond Create](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create) . Časové období údržby je nastavené v elastickém fondu, takže všechny databáze ve fondu mají plán časového intervalu pro správu fondu. `--maint-config-id`Hodnota (nebo `-m` ) musí být nastavena na platnou hodnotu pro oblast fondu. Pokud chcete získat platné hodnoty pro vaši oblast, přečtěte si téma [zjišťování dostupných oken údržby](#discover-available-maintenance-windows).


   ```azurecli
    # Set variables for your pool
    resourceGroupName="your_resource_group_name"
    serverName="your_server_name"
    poolName="your_pool_name"

    # Set selected maintenance window
    maintenanceConfig="SQL_EastUS2_DB_2"

    # Create elastic pool
    az sql elastic-pool create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2 \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Nastavení časového období údržby při vytváření spravované instance

Následující příklad vytvoří novou spravovanou instanci a nastaví časové období údržby pomocí [AZ SQL mi Create](/cli/azure/sql/mi#az_sql_mi_create). Časové období údržby je nastaveno na instanci, takže všechny databáze v instanci mají plán časového intervalu pro správu a údržbu instance. *MaintenanceConfigName* musí mít platnou hodnotu pro oblast vaší instance. Pokud chcete získat platné hodnoty pro vaši oblast, přečtěte si téma [zjišťování dostupných oken údržby](#discover-available-maintenance-windows).

   ```azurecli
   az sql mi create -g mygroup -n myinstance -l mylocation -i -u myusername -p mypassword --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}
   ```

-----

## <a name="configure-maintenance-window-for-existing-databases"></a>Konfigurovat časový interval pro správu a údržbu pro existující databáze


Když použijete výběr časového období údržby pro databázi, může být v některých případech k době krátká Změna konfigurace (několik sekund), protože Azure aplikuje požadované změny.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Následující postup nastaví časové období údržby existující databáze, elastického fondu nebo spravované instance pomocí Azure Portal:


## <a name="set-the-maintenance-window-for-an-existing-database-or-elastic-pool"></a>Nastavení časového období údržby pro existující databázi nebo elastický fond

1. Přejděte do databáze SQL nebo elastického fondu, pro který chcete nastavit časové intervaly pro správu a údržbu.
1. V nabídce **Nastavení** vyberte **Údržba** a pak vyberte požadované okno údržby.

   :::image type="content" source="media/maintenance-window-configure/maintenance.png" alt-text="Stránka údržby databáze SQL":::


## <a name="set-the-maintenance-window-for-an-existing-managed-instance"></a>Nastavení časového intervalu pro správu a údržbu existující spravované instance

1. Přejděte do spravované instance, pro kterou chcete nastavit časový interval pro správu a údržbu.
1. V nabídce **Nastavení** vyberte **Údržba** a pak vyberte požadované okno údržby.

   :::image type="content" source="media/maintenance-window-configure/maintenance-mi.png" alt-text="Stránka údržby spravované instance SQL":::



# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="set-the-maintenance-window-for-an-existing-database"></a>Nastavení časového období údržby pro existující databázi

Následující příklad nastaví časové období údržby pro existující databázi pomocí rutiny [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) . `-MaintenanceConfigurationId`Hodnota musí být nastavena na platnou hodnotu pro oblast vaší databáze. Pokud chcete získat platné hodnoty pro vaši oblast, přečtěte si téma [zjišťování dostupných oken údržby](#discover-available-maintenance-windows).

   ```powershell-interactive
    # Select different maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_2"

    Write-host "Changing database maintenance window to ${maintenanceConfig} ..."
    $database = Set-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -MaintenanceConfigurationId $maintenanceConfig
    $database
   ```

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>Nastavení časového období údržby pro existující elastický fond

Následující příklad nastaví časové období údržby pro existující elastický fond pomocí rutiny [set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool) . Je důležité, abyste se ujistili, že `$maintenanceConfig` hodnota je platnou hodnotou pro oblast fondu.  Chcete-li získat platné hodnoty pro oblast, přečtěte si téma [zjišťování dostupných oken údržby](#discover-available-maintenance-windows).

   ```powershell-interactive
    # Select different maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_1"
    
    Write-host "Changing pool maintenance window to ${maintenanceConfig} ..."
    $pool = Set-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName `
      -MaintenanceConfigurationId $maintenanceConfig
    $pool
   ```



## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>Nastavení časového období údržby existující spravované instance

Následující příklad nastaví časové období údržby existující spravované instance pomocí rutiny [set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance) . Je důležité, abyste se ujistili, že `$maintenanceConfig` hodnota musí být platnou hodnotou pro oblast vaší instance.  Chcete-li získat platné hodnoty pro oblast, přečtěte si téma [zjišťování dostupných oken údržby](#discover-available-maintenance-windows).


   ```powershell-interactive
   Set-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/azure-cli)

Následující příklady ukazují, jak nakonfigurovat okno údržby pomocí Azure CLI. Můžete [nainstalovat rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli)nebo použít Azure Cloud Shell.

## <a name="set-the-maintenance-window-for-an-existing-database"></a>Nastavení časového období údržby pro existující databázi

Následující příklad nastaví časové období údržby existující databáze pomocí příkazu [AZ SQL DB Update](/cli/azure/sql/db#az_sql_db_update) . `--maint-config-id`Hodnota (nebo `-m` ) musí být nastavena na platnou hodnotu pro oblast databáze. Pokud chcete získat platné hodnoty pro vaši oblast, přečtěte si téma [zjišťování dostupných oken údržby](#discover-available-maintenance-windows).

   ```azurecli
    # Select different maintenance window
    maintenanceConfig="SQL_EastUS2_DB_2"

    # Update database
    az sql db update \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>Nastavení časového období údržby pro existující elastický fond

Následující příklad nastaví okno údržby pro existující elastický fond pomocí příkazu [AZ SQL elastický fond Update](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) . Je důležité, abyste se ujistili, že `maintenanceConfig` hodnota je platnou hodnotou pro oblast fondu.  Chcete-li získat platné hodnoty pro oblast, přečtěte si téma [zjišťování dostupných oken údržby](#discover-available-maintenance-windows).

   ```azurecli
    # Select different maintenance window
    maintenanceConfig="SQL_EastUS2_DB_1"

    # Update pool
    az sql elastic-pool update \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>Nastavení časového období údržby existující spravované instance

Následující příklad nastaví časové období údržby pomocí [AZ SQL mi Update](/cli/azure/sql/mi#az_sql_mi_update). Časové období údržby je nastaveno na instanci, takže všechny databáze v instanci mají plán časového intervalu pro správu a údržbu instance. `-MaintenanceConfigurationId`V případě musí být *MaintenanceConfigName* platnou hodnotou pro oblast vaší instance. Pokud chcete získat platné hodnoty pro vaši oblast, přečtěte si téma [zjišťování dostupných oken údržby](#discover-available-maintenance-windows).

   ```azurecli
   az sql mi update -g mygroup  -n myinstance -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MainteanceConfigName}
   ```

-----

## <a name="cleanup-resources"></a>Vyčištění prostředků

Nezapomeňte odstranit nepotřebné prostředky, až je dokončíte, abyste se vyhnuli zbytečným poplatkům.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Přejděte do databáze SQL nebo elastického fondu, který již nepotřebujete.
1. V nabídce **Přehled** vyberte možnost odstranění prostředku.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   # Delete database
   Remove-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName

   # Delete elastic pool
   Remove-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName
   ```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/azure-cli)

   ```azurecli
   az sql db delete \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName

   az sql elastic-pool delete \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName
   ```

-----

## <a name="next-steps"></a>Další kroky

- Další informace o okně údržby najdete v tématu časové [období údržby (Preview)](maintenance-window.md).
- Další informace najdete v tématu [Nejčastější dotazy týkající se okna údržby](maintenance-window-faq.yml).
- Další informace o optimalizaci výkonu najdete [v tématu sledování a ladění výkonu v Azure SQL Database a v Azure SQL Managed instance](monitor-tune-overview.md).
