---
title: Konfigurace prostředí Azure-SSIS Integration runtime pro převzetí služeb při selhání SQL Database
description: Tento článek popisuje, jak nakonfigurovat prostředí Azure-SSIS Integration runtime pomocí Azure SQL Database geografické replikace a převzetí služeb při selhání pro databázi SSISDB.
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
ms.date: 11/06/2020
ms.openlocfilehash: 6b37a0df994546762abbcf3452d8e7b52dec6847
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "94331409"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-sql-database-geo-replication-and-failover"></a>Konfigurace prostředí Azure-SSIS Integration runtime pomocí SQL Database geografické replikace a převzetí služeb při selhání

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Tento článek popisuje, jak nakonfigurovat prostředí Azure-SSIS Integration runtime (IR) s Azure SQL Database geografickou replikaci pro databázi SSISDB. Když dojde k převzetí služeb při selhání, můžete zajistit, aby Azure-SSIS IR dál pracoval se sekundární databází.

Další informace o geografické replikaci a převzetí služeb při selhání pro SQL Database najdete v tématu [Přehled: Aktivní geografická replikace a skupiny automatického převzetí služeb při selhání](../azure-sql/database/auto-failover-group-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-a-sql-managed-instance"></a>Azure-SSIS IR převzetí služeb při selhání pomocí spravované instance SQL

### <a name="prerequisites"></a>Požadavky

Spravovaná instance Azure SQL používá k zabezpečení dat, přihlašovacích údajů a informací o připojení uložených v databázi *hlavní klíč databáze (DMK)* . Chcete-li povolit automatické dešifrování DMK, je kopie klíče šifrována prostřednictvím *hlavního klíče serveru (SMK)*. 

SMK není replikována ve skupině převzetí služeb při selhání. Po převzetí služeb při selhání musíte do primární i sekundární instance přidat heslo pro dešifrování DMK.

1. Spusťte následující příkaz pro SSISDB na primární instanci. Tento krok přidá nové šifrovací heslo.

   ```sql
   ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'password'
   ```

2. Vytvořte skupinu převzetí služeb při selhání na spravované instanci SQL.

3. Spusťte **sp_control_dbmasterkey_password** na sekundární instanci pomocí nového šifrovacího hesla.

   ```sql
   EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB', @password = N'<password>', @action = N'add';  
   GO
   ```

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>Scénář 1: Azure-SSIS IR odkazuje na koncový bod naslouchacího procesu pro čtení a zápis

Pokud chcete, aby Azure-SSIS IR odkazovala na koncový bod naslouchacího procesu pro čtení a zápis, musíte nejdřív nasměrovat na koncový bod primárního serveru. Po umístění SSISDB do skupiny převzetí služeb při selhání můžete Azure-SSIS IR zastavit, změnit tak, aby odkazovaly na koncový bod naslouchacího procesu čtení/zápisu pomocí Azure PowerShell a znovu ho restartujte.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -CatalogServerEndpoint "Azure SQL Managed Instance read/write listener endpoint"
```

#### <a name="solution"></a>Řešení

Pokud dojde k převzetí služeb při selhání, proveďte následující kroky:

1. Zastavte Azure-SSIS IR v primární oblasti.

2. Upravte Azure-SSIS IR s informacemi o nové oblasti, virtuální síti a identifikátoru URI sdíleného přístupového podpisu (SAS) pro vlastní instalaci v sekundární instanci. Vzhledem k tomu, že Azure-SSIS IR odkazuje na naslouchací proces pro čtení a zápis a koncový bod je pro Azure-SSIS IR transparentní, nemusíte tento koncový bod upravovat.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
      -VNetId "new VNet" `
      -Subnet "new subnet" `
      -SetupScriptContainerSasUri "new custom setup SAS URI"
   ```

3. Restartujte Azure-SSIS IR.

### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>Scénář 2: Azure-SSIS IR odkazuje na koncový bod primárního serveru

Tento scénář je vhodný, pokud Azure-SSIS IR odkazuje na koncový bod primárního serveru.

#### <a name="solution"></a>Řešení

Pokud dojde k převzetí služeb při selhání, proveďte následující kroky:

1. Zastavte Azure-SSIS IR v primární oblasti.

2. Upravte Azure-SSIS IR o novou oblast, koncový bod a informace o virtuální síti pro sekundární instanci.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
      -CatalogServerEndpoint "Azure SQL Database endpoint" `
      -CatalogAdminCredential "Azure SQL Database admin credentials" `
      -VNetId "new VNet" `
      -Subnet "new subnet" `
      -SetupScriptContainerSasUri "new custom setup SAS URI"
   ```

3. Restartujte Azure-SSIS IR.

### <a name="scenario-3-azure-ssis-ir-is-pointing-to-a-public-endpoint-of-a-sql-managed-instance"></a>Scénář 3: Azure-SSIS IR odkazuje na veřejný koncový bod spravované instance SQL

Tento scénář je vhodný, pokud Azure-SSIS IR odkazuje na veřejný koncový bod spravované instance SQL Azure a nepřipojí se k virtuální síti. Jediným rozdílem ze scénáře 2 je, že nemusíte upravovat informace o virtuální síti pro Azure-SSIS IR po převzetí služeb při selhání.

#### <a name="solution"></a>Řešení

Pokud dojde k převzetí služeb při selhání, proveďte následující kroky:

1. Zastavte Azure-SSIS IR v primární oblasti.

2. Upravte Azure-SSIS IR s informacemi o nové oblasti a koncových bodech pro sekundární instanci.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
      -CatalogServerEndpoint "Azure SQL Database server endpoint" `
      -CatalogAdminCredential "Azure SQL Database server admin credentials" `
      -SetupScriptContainerSasUri "new custom setup SAS URI"
   ```

3. Restartujte Azure-SSIS IR.

### <a name="scenario-4-attach-an-existing-ssisdb-instance-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scénář 4: připojení existující instance SSISDB (SSIS Catalog) k novému Azure-SSIS IR

Tento scénář je vhodný, pokud chcete, aby SSISDB pracovat s novým Azure-SSIS IR v nové oblasti, když dojde k Azure Data Factory nebo Azure-SSIS IR havárie v aktuální oblasti.

#### <a name="solution"></a>Řešení

Pokud dojde k převzetí služeb při selhání, proveďte následující kroky.

> [!NOTE]
> Použijte PowerShell pro krok 4 (vytvoření IR). Pokud to neuděláte, Azure Portal oznámí chybu, která říká, že SSISDB už existuje.

1. Zastavte Azure-SSIS IR v primární oblasti.

2. Spusťte uloženou proceduru, která aktualizuje metadata v SSISDB, aby přijímala připojení z **\<new_data_factory_name\>** a **\<new_integration_runtime_name\>** .
   
   ```sql
   EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

3. Vytvořte novou datovou továrnu s názvem **\<new_data_factory_name\>** v nové oblasti.

   ```powershell
   Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
      -Location "new region"`
      -Name "<new_data_factory_name>"
   ```
   
   Další informace o tomto příkazu PowerShellu najdete v tématu [Vytvoření datové továrny Azure pomocí PowerShellu](quickstart-create-data-factory-powershell.md).

4. Vytvoří nový Azure-SSIS IR s názvem **\<new_integration_runtime_name\>** v nové oblasti pomocí Azure PowerShell.

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
   
   Další informace o tomto příkazu PowerShellu najdete [v tématu Vytvoření prostředí Azure-SSIS Integration runtime v Azure Data Factory](create-azure-ssis-integration-runtime.md).

## <a name="azure-ssis-ir-failover-with-sql-database"></a>Azure-SSIS IR převzetí služeb při selhání s SQL Database

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>Scénář 1: Azure-SSIS IR odkazuje na koncový bod naslouchacího procesu pro čtení a zápis

Tento scénář je vhodný v těchto případech:

- Azure-SSIS IR odkazuje na koncový bod naslouchacího procesu pro čtení/zápis skupiny převzetí služeb při selhání.
- SQL Database Server *není nakonfigurován s* pravidlem pro koncový bod služby virtuální sítě.

Pokud chcete, aby Azure-SSIS IR odkazovala na koncový bod naslouchacího procesu pro čtení a zápis, musíte nejdřív nasměrovat na koncový bod primárního serveru. Po umístění SSISDB do skupiny převzetí služeb při selhání můžete Azure-SSIS IR zastavit, změnit tak, aby odkazovaly na koncový bod naslouchacího procesu čtení/zápisu pomocí Azure PowerShell a znovu ho restartujte.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -CatalogServerEndpoint "Azure SQL Database read/write listener endpoint"
```

#### <a name="solution"></a>Řešení

Pokud dojde k převzetí služeb při selhání, je pro Azure-SSIS IR transparentní. Azure-SSIS IR se automaticky připojí k nové primární skupině převzetí služeb při selhání. 

Pokud chcete aktualizovat oblast nebo jiné informace v Azure-SSIS IR, můžete je zastavit, upravit a restartovat.


### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>Scénář 2: Azure-SSIS IR odkazuje na koncový bod primárního serveru

Tento scénář je vhodný, pokud Azure-SSIS IR odkazuje na koncový bod primárního serveru.

#### <a name="solution"></a>Řešení

Pokud dojde k převzetí služeb při selhání, proveďte následující kroky:

1. Zastavte Azure-SSIS IR v primární oblasti.

2. Upravte Azure-SSIS IR o novou oblast, koncový bod a informace o virtuální síti pro sekundární instanci.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
      -CatalogServerEndpoint "Azure SQL Database endpoint" `
      -CatalogAdminCredential "Azure SQL Database admin credentials" `
      -VNetId "new VNet" `
      -Subnet "new subnet" `
      -SetupScriptContainerSasUri "new custom setup SAS URI"
   ```

3. Restartujte Azure-SSIS IR.

### <a name="scenario-3-attach-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scénář 3: připojení existujícího katalogu SSISDB (SSIS) k novému Azure-SSIS IR

Tento scénář je vhodný, pokud chcete zřídit nový Azure-SSIS IR v sekundární oblasti. To je vhodné také v případě, že chcete, aby vaše SSISDB v nové oblasti pracovaly s novou Azure-SSIS IR v případě, že dojde k Azure Data Factory nebo Azure-SSIS IR havárie v aktuální oblasti.

#### <a name="solution"></a>Řešení

Pokud dojde k převzetí služeb při selhání, proveďte následující kroky.

> [!NOTE]
> Použijte PowerShell pro krok 4 (vytvoření IR). Pokud to neuděláte, Azure Portal oznámí chybu, která říká, že SSISDB už existuje.

1. Zastavte Azure-SSIS IR v primární oblasti.

2. Spusťte uloženou proceduru, která aktualizuje metadata v SSISDB, aby přijímala připojení z **\<new_data_factory_name\>** a **\<new_integration_runtime_name\>** .
   
   ```sql
   EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

3. Vytvořte novou datovou továrnu s názvem **\<new_data_factory_name\>** v nové oblasti.

   ```powershell
   Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
      -Location "new region"`
      -Name "<new_data_factory_name>"
   ```
   
   Další informace o tomto příkazu PowerShellu najdete v tématu [Vytvoření datové továrny Azure pomocí PowerShellu](quickstart-create-data-factory-powershell.md).

4. Vytvoří nový Azure-SSIS IR s názvem **\<new_integration_runtime_name\>** v nové oblasti pomocí Azure PowerShell.

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

   Další informace o tomto příkazu PowerShellu najdete [v tématu Vytvoření prostředí Azure-SSIS Integration runtime v Azure Data Factory](create-azure-ssis-integration-runtime.md).

## <a name="next-steps"></a>Další kroky

Vezměte v úvahu tyto další možnosti konfigurace Azure-SSIS IR:

- [Konfigurace prostředí Azure-SSIS Integration runtime pro vysoký výkon](configure-azure-ssis-integration-runtime-performance.md)

- [Přizpůsobení nastavení pro Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Zřízení Enterprise Edition pro prostředí Azure-SSIS Integration runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
