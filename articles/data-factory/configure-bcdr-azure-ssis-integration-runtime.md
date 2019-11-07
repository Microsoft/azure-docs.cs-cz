---
title: Konfigurace Azure-SSIS Integration Runtime pro převzetí služeb při selhání SQL Database
description: Tento článek popisuje, jak nakonfigurovat Azure-SSIS Integration Runtime Azure SQL Database geografickou replikaci a převzetí služeb při selhání pro databázi SSISDB.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 08/14/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 6e709a25c6c33a1fc80a110435035b1473d92681
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681393"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Konfigurace Azure-SSIS Integration Runtime s využitím Azure SQL Database geografické replikace a převzetí služeb při selhání

Tento článek popisuje, jak nakonfigurovat Azure-SSIS Integration Runtime s Azure SQL Database geografickou replikaci pro databázi SSISDB. Když dojde k převzetí služeb při selhání, můžete zajistit, aby Azure-SSIS IR dál pracoval se sekundární databází.

Další informace o geografické replikaci a převzetí služeb při selhání pro SQL Database najdete v tématu [Přehled: Aktivní geografická replikace a skupiny automatického převzetí služeb při selhání](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Scénář 1 – Azure-SSIS IR odkazuje na koncový bod naslouchacího procesu pro čtení a zápis

### <a name="conditions"></a>Podmínky

Tato část platí v případě, že jsou splněné následující podmínky:

- Azure-SSIS IR odkazuje na koncový bod naslouchacího procesu pro čtení i zápis ve skupině převzetí služeb při selhání.

  ANI

- SQL Database Server *není nakonfigurován s* pravidlem koncového bodu služby virtuální sítě.

### <a name="solution"></a>Řešení

Pokud dojde k převzetí služeb při selhání, je transparentní pro Azure-SSIS IR. Azure-SSIS IR se automaticky připojí k nové primární skupině převzetí služeb při selhání.

## <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Scénář 2 – Azure-SSIS IR odkazuje na koncový bod primárního serveru

### <a name="conditions"></a>Podmínky

Tato část se vztahuje, pokud je splněna jedna z následujících podmínek:

- Azure-SSIS IR odkazuje na koncový bod primárního serveru skupiny převzetí služeb při selhání. Tento koncový bod se při převzetí služeb při selhání změní.

  NEBO

- Azure SQL Database Server je nakonfigurován s pravidlem koncového bodu služby virtuální sítě.

  NEBO

- Databázový server je SQL Database spravovaná instance nakonfigurovaná pomocí virtuální sítě.

### <a name="solution"></a>Řešení

Pokud dojde k převzetí služeb při selhání, musíte provést následující akce:

1. Zastavte Azure-SSIS IR.

2. Překonfigurujte IR tak, aby odkazovala na nový primární koncový bod a na virtuální síť v nové oblasti.

3. Restartujte IR.

Následující části popisují tyto kroky podrobněji.

### <a name="prerequisites"></a>Požadavky

- Ujistěte se, že jste povolili zotavení po havárii pro Azure SQL Database Server v případě výpadku serveru ve stejnou dobu. Další informace najdete v tématu [Přehled provozní kontinuity pomocí Azure SQL Database](../sql-database/sql-database-business-continuity.md).

- Pokud používáte virtuální síť v aktuální oblasti, musíte k připojení prostředí Azure-SSIS Integration runtime použít jinou virtuální síť v nové oblasti. Další informace najdete v tématu [připojení prostředí Azure-SSIS Integration runtime k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md).

- Pokud používáte vlastní instalaci, může být nutné připravit jiný identifikátor URI SAS pro kontejner objektů blob, který ukládá vlastní instalační skript a přidružené soubory, takže bude během výpadku nadále přístupný. Další informace najdete v tématu [Konfigurace vlastního nastavení v prostředí Azure-SSIS Integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Kroky

Postupujte podle těchto kroků a zastavte Azure-SSIS IR, přepněte IR na novou oblast a znovu ji spusťte.

1. Zastavte infračervený přenos v původní oblasti.

2. Voláním následujícího příkazu v PowerShellu aktualizujte IR s novým nastavením.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    Další informace o tomto příkazu PowerShellu najdete [v tématu Vytvoření prostředí Azure-SSIS Integration runtime v tématu Azure Data Factory](create-azure-ssis-integration-runtime.md)

3. Znovu spusťte IR.

## <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scénář 3 – připojení existujícího katalogu SSISDB (SSIS) k novému Azure-SSIS IR

Když dojde k selhání ADF nebo Azure-SSIS IR v aktuální oblasti, můžete SSISDB pracovat s novým Azure-SSIS IR v nové oblasti.

### <a name="prerequisites"></a>Požadavky

- Pokud používáte virtuální síť v aktuální oblasti, musíte k připojení prostředí Azure-SSIS Integration runtime použít jinou virtuální síť v nové oblasti. Další informace najdete v tématu [připojení prostředí Azure-SSIS Integration runtime k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md).

- Pokud používáte vlastní instalaci, může být nutné připravit jiný identifikátor URI SAS pro kontejner objektů blob, který ukládá vlastní instalační skript a přidružené soubory, takže bude během výpadku nadále přístupný. Další informace najdete v tématu [Konfigurace vlastního nastavení v prostředí Azure-SSIS Integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Kroky

Postupujte podle těchto kroků a zastavte Azure-SSIS IR, přepněte IR na novou oblast a znovu ji spusťte.

1. Spusťte uloženou proceduru, aby se SSISDB připojil k **\<new_data_factory_name\>** nebo **\<new_integration_runtime_name\>** .
   
  ```SQL
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

2. Vytvořte novou datovou továrnu s názvem **\<new_data_factory_name\>** v nové oblasti. Další informace najdete v tématu Vytvoření datové továrny.

     ```powershell
     Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
     ```
    Další informace o tomto příkazu PowerShellu najdete v tématu [Vytvoření datové továrny Azure pomocí PowerShellu](quickstart-create-data-factory-powershell.md) .

3. Pomocí Azure PowerShell vytvořte v nové oblasti novou Azure-SSIS IR s názvem **\<new_integration_runtime_name\>** .

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

4. Znovu spusťte IR.

## <a name="next-steps"></a>Další kroky

Vezměte v úvahu tyto další možnosti konfigurace Azure-SSIS IR:

- [Konfigurace Azure-SSIS Integration Runtime pro vysoký výkon](configure-azure-ssis-integration-runtime-performance.md)

- [Přizpůsobení nastavení pro Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Zřídit Enterprise Edition pro Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
