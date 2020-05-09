---
title: Konfigurace Azure-SSIS Integration Runtime pro převzetí služeb při selhání SQL Database
description: Tento článek popisuje, jak nakonfigurovat Azure-SSIS Integration Runtime Azure SQL Database geografickou replikaci a převzetí služeb při selhání pro databázi SSISDB.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/09/2020
ms.openlocfilehash: b4b679b15092531ff9553d221f23d7f030fc1def
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2020
ms.locfileid: "82592084"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Konfigurace Azure-SSIS Integration Runtime s využitím Azure SQL Database geografické replikace a převzetí služeb při selhání

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Tento článek popisuje, jak nakonfigurovat Azure-SSIS Integration Runtime s Azure SQL Database geografickou replikaci pro databázi SSISDB. Když dojde k převzetí služeb při selhání, můžete zajistit, aby Azure-SSIS IR dál pracoval se sekundární databází.

Další informace o geografické replikaci a převzetí služeb při selhání pro SQL Database najdete v tématu [Přehled: Aktivní geografická replikace a skupiny automatického převzetí služeb při selhání](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-azure-sql-database-managed-instance"></a>Azure-SSIS IR převzetí služeb při selhání pomocí spravované instance Azure SQL Database

### <a name="prerequisites"></a>Požadavky

Azure SQL Database spravovaná instance používá k zabezpečení dat, přihlašovacích údajů a informací o připojeních uložených v databázi **hlavní klíč databáze (DMK)** . Pokud chcete povolit automatické dešifrování DMK, je kopie klíče zašifrovaná pomocí **hlavního klíče serveru (SMK)**. Ale SMK se nereplikuje do skupiny převzetí služeb při selhání, takže při převzetí služeb při selhání je potřeba přidat další heslo na primární i sekundární instance pro dešifrování DMK.

1. Příkaz spustit pod příkazem SSISDB u primární instance. Tento krok přidává nové šifrovací heslo.

    ```sql
    ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'password'
    ```

2. Vytvořte skupinu převzetí služeb při selhání na Azure SQL Database Managed instance.

3. Spusťte **sp_control_dbmasterkey_password** na sekundární instanci pomocí nového šifrovacího hesla.

    ```sql
    EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB',   
        @password = N'<password>', @action = N'add';  
    GO
    ```

### <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Scénář 1 – Azure-SSIS IR odkazuje na koncový bod naslouchacího procesu pro čtení a zápis

Pokud chcete Azure-SSIS IR Point na koncový bod naslouchacího procesu pro čtení a zápis, musíte nejdřív nasměrovat na koncový bod primárního serveru. Po vložení SSISDB do skupiny převzetí služeb při selhání můžete přejít na koncový bod naslouchacího procesu čtení i zápisu a restartovat Azure-SSIS IR.

#### <a name="solution"></a>Řešení

Pokud dojde k převzetí služeb při selhání, musíte provést následující akce:

1. Zastavit Azure-SSIS IR v primární oblasti

2. Upravte Azure-SSIS IR s využitím nové oblasti, virtuální sítě a vlastní nastavení informace identifikátoru URI SAS sekundární instance. Jak Azure-SSIS IR odkazuje na naslouchací proces pro čtení i zápis a koncový bod je pro Azure-SSIS IR transparentní, nemusíte upravovat koncový bod.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Restartujte Azure-SSIS IR.

### <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Scénář 2 – Azure-SSIS IR odkazuje na koncový bod primárního serveru

Scénář je vhodný, pokud Azure-SSIS IR odkazuje na koncový bod primárního serveru.

#### <a name="solution"></a>Řešení

Pokud dojde k převzetí služeb při selhání, musíte provést následující akce:

1. Zastavit Azure-SSIS IR v primární oblasti

2. Upravte Azure-SSIS IR s informacemi o nové oblasti, koncovém bodu a virtuální síti sekundární instance.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Restartujte Azure-SSIS IR.

### <a name="scenario-3---azure-ssis-ir-is-pointing-to-public-endpoint-of-azure-sql-database-managed-instance"></a>Scénář 3 – Azure-SSIS IR odkazuje na veřejný koncový bod Azure SQL Database spravované instance

Scénář je vhodný, pokud Azure-SSIS IR odkazuje na veřejný koncový bod Azure SQL Database spravované instance a nepřipojí se k virtuální síti. Jediný rozdíl mezi scénáři 2 a těmito scénáři spočívá v tom, že po převzetí služeb při selhání nemusíte upravovat informace o virtuální síti Azure-SSIS IR.

#### <a name="solution"></a>Řešení

Pokud dojde k převzetí služeb při selhání, musíte provést následující akce:

1. Zastavit Azure-SSIS IR v primární oblasti

2. Upravit Azure-SSIS IR s informacemi o nové oblasti a koncových bodech sekundární instance.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Restartujte Azure-SSIS IR.

### <a name="scenario-4---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scénář 4 – připojení existujícího katalogu SSISDB (SSIS Catalog) k novému Azure-SSIS IR

Tento scénář je vhodný, pokud chcete zřídit novou Azure-SSIS IR v sekundární oblasti nebo pokud chcete, aby vaše SSISDB pracovaly s novým Azure-SSIS IR v nové oblasti, když dojde k selhání ADF nebo Azure-SSIS IR havárie v aktuální oblasti.

#### <a name="solution"></a>Řešení

Pokud dojde k převzetí služeb při selhání, musíte provést následující akce:

> [!NOTE]
> Krok 4 (vytváření IR) se musí provádět přes PowerShell. Azure Portal nahlásí chybu informující o tom, že SSISDB už existuje.

1. Zastavit Azure-SSIS IR v primární oblasti

2. Spusťte uloženou proceduru, která aktualizuje metadata v SSISDB, aby přijímala připojení z ** \<new_data_factory_name\> ** a ** \<new_integration_runtime_name\>**.
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. Vytvořte novou datovou továrnu s názvem ** \<new_data_factory_name\> ** v nové oblasti. Další informace najdete v tématu Vytvoření datové továrny.

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                      -Location "new region"`
                      -Name "<new_data_factory_name>"
    ```
    
    Další informace o tomto příkazu PowerShellu najdete v tématu [Vytvoření datové továrny Azure pomocí PowerShellu](quickstart-create-data-factory-powershell.md) .

4. Vytvořte novou Azure-SSIS IR s názvem ** \<new_integration_runtime_name\> ** v nové oblasti pomocí Azure PowerShell.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
    ```

    Další informace o tomto příkazu PowerShellu najdete [v tématu Vytvoření prostředí Azure-SSIS Integration runtime v tématu Azure Data Factory](create-azure-ssis-integration-runtime.md)



## <a name="azure-ssis-ir-failover-with-azure-sql-database"></a>Azure-SSIS IR převzetí služeb při selhání s Azure SQL Database

### <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Scénář 1 – Azure-SSIS IR odkazuje na koncový bod naslouchacího procesu pro čtení a zápis

Tento scénář je vhodný Azure-SSIS IR odkazuje na koncový bod naslouchacího procesu pro čtení i zápis ve skupině převzetí služeb při selhání a SQL Database Server *není nakonfigurovaný s* pravidlem koncového bodu služby virtuální sítě. Pokud chcete Azure-SSIS IR Point na koncový bod naslouchacího procesu pro čtení a zápis, musíte nejdřív nasměrovat na koncový bod primárního serveru. Po vložení SSISDB do skupiny převzetí služeb při selhání můžete přejít na koncový bod naslouchacího procesu čtení i zápisu a restartovat Azure-SSIS IR.

#### <a name="solution"></a>Řešení

Pokud dojde k převzetí služeb při selhání, je transparentní pro Azure-SSIS IR. Azure-SSIS IR se automaticky připojí k nové primární skupině převzetí služeb při selhání. Pokud chcete oblast nebo jiné informace v Azure-SSIS IR aktualizovat, můžete ji zastavit, upravit a restartovat.


### <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Scénář 2 – Azure-SSIS IR odkazuje na koncový bod primárního serveru

Scénář je vhodný, pokud Azure-SSIS IR odkazuje na koncový bod primárního serveru.

#### <a name="solution"></a>Řešení

Pokud dojde k převzetí služeb při selhání, musíte provést následující akce:

1. Zastavit Azure-SSIS IR v primární oblasti

2. Upravte Azure-SSIS IR s informacemi o nové oblasti, koncovém bodu a virtuální síti sekundární instance.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Restartujte Azure-SSIS IR.

### <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scénář 3 – připojení existujícího katalogu SSISDB (SSIS) k novému Azure-SSIS IR

Tento scénář je vhodný, pokud chcete zřídit novou Azure-SSIS IR v sekundární oblasti nebo pokud chcete, aby vaše SSISDB pracovaly s novým Azure-SSIS IR v nové oblasti, když dojde k selhání ADF nebo Azure-SSIS IR havárie v aktuální oblasti.

#### <a name="solution"></a>Řešení

> [!NOTE]
> Krok 4 (vytváření IR) se musí provádět přes PowerShell. Azure Portal nahlásí chybu informující o tom, že SSISDB už existuje.

1. Zastavit Azure-SSIS IR v primární oblasti

2. Spusťte uloženou proceduru, která aktualizuje metadata v SSISDB, aby přijímala připojení z ** \<new_data_factory_name\> ** a ** \<new_integration_runtime_name\>**.
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. Vytvořte novou datovou továrnu s názvem ** \<new_data_factory_name\> ** v nové oblasti. Další informace najdete v tématu Vytvoření datové továrny.

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
    ```
    
    Další informace o tomto příkazu PowerShellu najdete v tématu [Vytvoření datové továrny Azure pomocí PowerShellu](quickstart-create-data-factory-powershell.md) .

4. Vytvořte novou Azure-SSIS IR s názvem ** \<new_integration_runtime_name\> ** v nové oblasti pomocí Azure PowerShell.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
    ```

    Další informace o tomto příkazu PowerShellu najdete [v tématu Vytvoření prostředí Azure-SSIS Integration runtime v tématu Azure Data Factory](create-azure-ssis-integration-runtime.md)


## <a name="next-steps"></a>Další kroky

Vezměte v úvahu tyto další možnosti konfigurace Azure-SSIS IR:

- [Konfigurace Azure-SSIS Integration Runtime pro vysoký výkon](configure-azure-ssis-integration-runtime-performance.md)

- [Přizpůsobení nastavení pro Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Zřídit Enterprise Edition pro Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
