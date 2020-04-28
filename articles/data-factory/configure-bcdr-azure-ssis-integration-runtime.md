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
ms.openlocfilehash: 39d55d4372f03a1625bb04d8377ed6533401e281
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188718"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Konfigurace Azure-SSIS Integration Runtime s využitím Azure SQL Database geografické replikace a převzetí služeb při selhání

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak nakonfigurovat Azure-SSIS Integration Runtime s Azure SQL Database geografickou replikaci pro databázi SSISDB. Když dojde k převzetí služeb při selhání, můžete zajistit, aby Azure-SSIS IR dál pracoval se sekundární databází.

Další informace o geografické replikaci a převzetí služeb při selhání pro SQL Database najdete v tématu [Přehled: Aktivní geografická replikace a skupiny automatického převzetí služeb při selhání](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-azure-sql-database-managed-instance"></a>Azure-SSIS IR převzetí služeb při selhání pomocí spravované instance Azure SQL Database

### <a name="prerequisites"></a>Požadavky
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

### <a name="solution"></a>Řešení
Pokud dojde k převzetí služeb při selhání, pokud chcete použít existující Azure-SSIS IR v primární oblasti:
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

4. Změňte název **serveru v sestavení** balíčků SSIS s názvem sekundární instance serveru a pak tyto balíčky znovu nasaďte a spusťte.

Pokud chcete zřídit novou Azure-SSIS IR v sekundární oblasti:
> [!NOTE]
> Krok 4 (vytváření IR) se musí provádět přes PowerShell. Azure Portal nahlásí chybu informující o tom, že SSISDB už existuje.
1. Zastavit Azure-SSIS IR v primární oblasti

2. Spusťte uloženou proceduru, která aktualizuje metadata v SSISDB, aby přijímala připojení z ** \<new_data_factory_name\> ** a ** \<new_integration_runtime_name\>**.
   
```SQL
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

5. Změňte název **serveru v sestavení** balíčků SSIS s názvem sekundární instance serveru a pak tyto balíčky znovu nasaďte a spusťte.



## <a name="azure-ssis-ir-failover-with-azure-sql-database"></a>Azure-SSIS IR převzetí služeb při selhání s Azure SQL Database

### <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Scénář 1 – Azure-SSIS IR odkazuje na koncový bod naslouchacího procesu pro čtení a zápis

#### <a name="conditions"></a>Podmínky

Tato část platí v případě, že jsou splněné následující podmínky:

- Azure-SSIS IR odkazuje na koncový bod naslouchacího procesu pro čtení i zápis ve skupině převzetí služeb při selhání.

  AND

- SQL Database Server *není nakonfigurován s* pravidlem koncového bodu služby virtuální sítě.

#### <a name="solution"></a>Řešení

Pokud dojde k převzetí služeb při selhání, je transparentní pro Azure-SSIS IR. Azure-SSIS IR se automaticky připojí k nové primární skupině převzetí služeb při selhání.


### <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Scénář 2 – Azure-SSIS IR odkazuje na koncový bod primárního serveru

#### <a name="conditions"></a>Podmínky

Tato část se vztahuje, pokud je splněna jedna z následujících podmínek:

- Azure-SSIS IR odkazuje na koncový bod primárního serveru skupiny převzetí služeb při selhání. Tento koncový bod se při převzetí služeb při selhání změní.

  NEBO

- Azure SQL Database Server je nakonfigurován s pravidlem koncového bodu služby virtuální sítě.


#### <a name="solution"></a>Řešení

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

4. Změňte název **serveru v sestavení** balíčků SSIS s názvem sekundární instance serveru a pak tyto balíčky znovu nasaďte a spusťte.


### <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scénář 3 – připojení existujícího katalogu SSISDB (SSIS) k novému Azure-SSIS IR

Když dojde k selhání ADF nebo Azure-SSIS IR v aktuální oblasti, můžete SSISDB pracovat s novým Azure-SSIS IR v nové oblasti.

#### <a name="solution"></a>Řešení

> [!NOTE]
> Krok 4 (vytváření IR) se musí provádět přes PowerShell. Azure Portal nahlásí chybu informující o tom, že SSISDB už existuje.

1. Zastavit Azure-SSIS IR v primární oblasti

2. Spusťte uloženou proceduru, která aktualizuje metadata v SSISDB, aby přijímala připojení z ** \<new_data_factory_name\> ** a ** \<new_integration_runtime_name\>**.
   
```SQL
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

5. Změňte název **serveru v sestavení** balíčků SSIS s názvem sekundární instance serveru a pak tyto balíčky znovu nasaďte a spusťte.


## <a name="next-steps"></a>Další kroky

Vezměte v úvahu tyto další možnosti konfigurace Azure-SSIS IR:

- [Konfigurace Azure-SSIS Integration Runtime pro vysoký výkon](configure-azure-ssis-integration-runtime-performance.md)

- [Přizpůsobení nastavení pro Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Zřídit Enterprise Edition pro Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
