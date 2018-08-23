---
title: Konfigurace prostředí Azure-SSIS Integration Runtime pro převzetí služeb při selhání databáze SQL | Dokumentace Microsoftu
description: Tento článek popisuje, jak nakonfigurovat prostředí Azure-SSIS Integration Runtime s Azure SQL Database pro geografické replikace a převzetí služeb při selhání pro databázi SSISDB
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
ms.openlocfilehash: 2012ccf4d9fd3e62ba248f29f922f868077e4061
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "42060087"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Konfigurace prostředí Azure-SSIS Integration Runtime pomocí Azure SQL Database pro geografické replikace a převzetí služeb při selhání

Tento článek popisuje, jak nakonfigurovat prostředí Azure-SSIS Integration Runtime s Azure SQL Database geografickou replikaci pro databázi SSISDB. Když dojde k selhání, můžete zajistit, že Azure-SSIS IR pořád funguje s sekundární databáze.

Další informace o geografické replikace a převzetí služeb při selhání pro službu SQL Database najdete v tématu [přehled: aktivní geografickou replikaci a automatické převzetí služeb při selhání skupiny](../sql-database/sql-database-geo-replication-overview.md).

## <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Scénář 1 - Azure-SSIS IR je přejdete na koncový bod naslouchacího procesu pro čtení i zápis

### <a name="conditions"></a>Podmínky

Tato část se týká, pokud jsou splněny následující podmínky:

- Prostředí Azure-SSIS IR je přejdete na koncový bod naslouchacího procesu pro čtení i zápis skupiny převzetí služeb při selhání.

  A

- Serveru služby SQL Database je *není* nakonfigurované pravidlo koncový bod služby virtuální sítě.

### <a name="solution"></a>Řešení

Pokud dojde k převzetí služeb při selhání, je transparentní pro prostředí Azure-SSIS IR. Prostředí Azure-SSIS IR se automaticky připojí k nové primární skupině převzetí služeb při selhání.

## <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Scénář 2 - Azure-SSIS IR odkazuje na primární server koncového bodu

### <a name="conditions"></a>Podmínky

Tato část se týká, když je splněna jedna z následujících podmínek:

- Prostředí Azure-SSIS IR odkazuje na primární server koncového bodu skupiny převzetí služeb při selhání. Tento koncový bod se změní, když dojde k převzetí služeb při selhání.

  NEBO

- Server Azure SQL Database má nakonfigurovanou pravidlo koncový bod služby virtuální sítě.

  NEBO

- Databázový server je spravované instanci SQL Database nakonfigurovaná s virtuální sítí.

### <a name="solution"></a>Řešení

Pokud dojde k převzetí služeb při selhání, musíte udělat následující věci:

1. Zastavit prostředí Azure-SSIS IR.

2. Změna konfigurace prostředí IR tak, aby odkazovala na nový primární koncový bod a virtuální sítě v nové oblasti.

3. Restartujte IR.

Následující části popisují postup podrobněji.

### <a name="prerequisites"></a>Požadavky

- Ujistěte se, že jste povolili zotavení po havárii pro váš server Azure SQL Database v případě, že má server ve stejnou dobu výpadku. Další informace najdete v tématu [přehled kontinuity obchodních procesů ve službě Azure SQL Database](../sql-database/sql-database-business-continuity.md).

- Pokud používáte virtuální sítě v aktuální oblasti, budete muset použít jinou virtuální sítí v nové oblasti pro připojení prostředí Azure-SSIS integration runtime. Další informace najdete v tématu [připojit k prostředí Azure-SSIS integration runtime k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md).

- Pokud použijete vlastní nastavení, budete muset připravit jiném identifikátoru URI SAS pro kontejner objektů blob, který ukládá vlastní instalační skript a přidružené soubory, takže ho dál dostupná během výpadků. Další informace najdete v tématu [nakonfigurovat vlastní nastavení v prostředí Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Kroky

Použijte následující postup zastavit prostředí Azure-SSIS IR, přepnout prostředí IR do nové oblasti a znovu spustit.

1. Zastavte prostředí IR v oblasti původní.

2. Zavoláním následujícího příkazu v Powershellu aktualizovat prostředí IR s novým nastavením.

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    Další informace o tomto příkazu prostředí PowerShell najdete v tématu [vytvořit prostředí Azure-SSIS integration runtime ve službě Azure Data Factory](create-azure-ssis-integration-runtime.md)

3. Prostředí IR spusťte znovu.

## <a name="next-steps"></a>Další postup

Vezměte v úvahu tyto další možnosti konfigurace pro prostředí Azure-SSIS IR:

- [Konfigurace prostředí Azure-SSIS Integration Runtime pro vysoký výkon](configure-azure-ssis-integration-runtime-performance.md)

- [Přizpůsobení nastavení pro prostředí Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Zřízení Enterprise Edition pro prostředí Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
