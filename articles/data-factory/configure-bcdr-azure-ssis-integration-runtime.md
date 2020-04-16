---
title: Konfigurace modulu runtime integrace Azure-SSIS pro převzetí služeb při selhání databáze SQL
description: Tento článek popisuje, jak nakonfigurovat modul Runtime integrace Azure-SSIS s geografickou replikací azure SQL database a převzetíslužeb při selhání pro databázi SSISDB
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
ms.openlocfilehash: 75dd5a917d718f4ccef034e953a415d575d42bd9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418299"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Konfigurace modulu runtime integrace Azure-SSIS pomocí geografické replikace azure SQL database a převzetí služeb při selhání

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek popisuje, jak nakonfigurovat modul Runtime integrace Azure-SSIS s geografickou replikací Azure SQL Database pro databázi SSISDB. Dojde-li k převzetí služeb při selhání, můžete zajistit, že Azure-SSIS Ir bude i natu dál pracovat se sekundární databází.

Další informace o geografické replikaci a převzetí služeb při selhání pro databázi SQL naleznete v [tématu Přehled: Aktivní skupiny geografické replikace a automatické převzetí služeb při selhání](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Scénář 1 – Azure-SSIS Ir ukazuje na koncový bod posluchače čtení a zápisu

### <a name="conditions"></a>Podmínky

Tato část platí, pokud jsou splněny následující podmínky:

- Azure-SSIS Ir ukazuje na koncový bod posluchače čtení a zápisu skupiny převzetí služeb při selhání.

  AND

- Databázový server SQL *není* nakonfigurován s pravidlem koncového bodu služby virtuální sítě.

### <a name="solution"></a>Řešení

Dojde-li k převzetí služeb při selhání, je transparentní pro Azure-SSIS IR. Azure-SSIS IR se automaticky připojí k nové primární skupiny převzetí služeb při selhání.

## <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Scénář 2 – Azure-SSIS Ir ukazuje na primární koncový bod serveru

### <a name="conditions"></a>Podmínky

Tato část platí, pokud platí jedna z následujících podmínek:

- Azure-SSIS Ir ukazuje na koncový bod primárního serveru skupiny převzetí služeb při selhání. Tento koncový bod se změní, když dojde k převzetí služeb při selhání.

  NEBO

- Databázový server Azure SQL je nakonfigurovaný s pravidlem koncového bodu služby virtuální sítě.

  NEBO

- Databázový server je instance spravované databází SQL nakonfigurované s virtuální sítí.

### <a name="solution"></a>Řešení

Dojde-li k převzetí služeb při selhání, je třeba provést následující akce:

1. Zastavte infračervený přenos služby Azure-SSIS.

2. Překonfigurujte infračervený přenos tak, aby přecšlápl na nový primární koncový bod a na virtuální síť v nové oblasti.

3. Restartujte infračervený přenos.

Následující části popisují tyto kroky podrobněji.

### <a name="prerequisites"></a>Požadavky

- Ujistěte se, že jste povolili zotavení po havárii pro váš server Azure SQL Database v případě, že server má výpadek ve stejnou dobu. Další informace najdete v [tématu Přehled kontinuity podnikání s Azure SQL Database](../sql-database/sql-database-business-continuity.md).

- Pokud používáte virtuální síť v aktuální oblasti, musíte použít jinou virtuální síť v nové oblasti pro připojení runtime integrace Azure-SSIS. Další informace najdete [v tématu Připojení za běhu integrace Azure-SSIS do virtuální sítě](join-azure-ssis-integration-runtime-virtual-network.md).

- Pokud používáte vlastní nastavení, budete muset připravit další identifikátor URI SAS pro kontejner objektů blob, který ukládá vlastní instalační skript a přidružené soubory, takže bude i nadále přístupný během výpadku. Další informace najdete [v tématu Konfigurace vlastního nastavení na prostředí integrace Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Kroky

Podle těchto kroků zastavíte infračervený přenos Azure-SSIS, přepněte infračervený přenos do nové oblasti a spusťte ji znovu.

1. Zastavte infračervený přenos v původní oblasti.

2. Voláním následujícího příkazu v prostředí PowerShell aktualizujte infračervený přenos s novým nastavením.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    Další informace o tomto příkazu PowerShellu najdete [v tématu Vytvoření runtime integrace Azure-SSIS v Azure Data Factory.](create-azure-ssis-integration-runtime.md)

3. Znovu spusťte infračervený přenos.

## <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scénář 3 – připojení existující hospo- databáze SSISDB (katalog SSIS) k novému zařízení Azure-SSIS IR

Dojde-li k havárii služby ADF nebo Azure-SSIS IR v aktuální oblasti, můžete vytvořit, aby vaše SSISDB nadála pracovala s novou službou Azure-SSIS IR v nové oblasti.

### <a name="prerequisites"></a>Požadavky

- Pokud používáte virtuální síť v aktuální oblasti, musíte použít jinou virtuální síť v nové oblasti pro připojení runtime integrace Azure-SSIS. Další informace najdete [v tématu Připojení za běhu integrace Azure-SSIS do virtuální sítě](join-azure-ssis-integration-runtime-virtual-network.md).

- Pokud používáte vlastní nastavení, budete muset připravit další identifikátor URI SAS pro kontejner objektů blob, který ukládá vlastní instalační skript a přidružené soubory, takže bude i nadále přístupný během výpadku. Další informace najdete [v tématu Konfigurace vlastního nastavení na prostředí integrace Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Kroky

Podle těchto kroků přesuňte azure-SSIS IR do nové oblasti.
> [!NOTE]
> Krok 3 (vytvoření infračerveného zařízení) je třeba provést prostřednictvím prostředí PowerShell. Portál Azure ohlásí chybu oznamující, že SSISDB již existuje.

1. Spusťte uloženou proceduru k aktualizaci metadat v SSISDB pro přijetí připojení z ** \<new_data_factory_name\> ** a ** \<new_integration_runtime_name\>**.
   
  ```SQL
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

2. Vytvořte novou datovou továrnu s názvem ** \<new_data_factory_name\> ** v nové oblasti. Další informace najdete v tématu Vytvoření datové továrny.

     ```powershell
     Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
     ```
    Další informace o tomto příkazu PowerShellu najdete [v tématu Vytvoření Azure datové továrny pomocí PowerShellu.](quickstart-create-data-factory-powershell.md)

3. Pomocí Azure PowerShellu vytvořte ** \<\> ** novou infračervený přenos azure-SSIS s názvem new_integration_runtime_name v nové oblasti.

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

    Další informace o tomto příkazu PowerShellu najdete [v tématu Vytvoření runtime integrace Azure-SSIS v Azure Data Factory.](create-azure-ssis-integration-runtime.md)

4. Znovu spusťte infračervený přenos.

## <a name="next-steps"></a>Další kroky

Zvažte tyto další možnosti konfigurace pro Azure-SSIS IR:

- [Konfigurace runtime integrace Azure-SSIS pro vysoký výkon](configure-azure-ssis-integration-runtime-performance.md)

- [Přizpůsobení nastavení pro Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Zřízení edice Enterprise pro runtime integrace Azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)
