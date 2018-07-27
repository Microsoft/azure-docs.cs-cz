---
title: Obchodní kontinuity podnikových procesů a po havárii doporučení pro zotavení (BCDR) pro Azure-SSIS Integration Runtime | Dokumentace Microsoftu
description: Tento článek popisuje, jak obchodní kontinuity podnikových procesů a po havárii doporučení pro zotavení pro Azure-SSIS Integration Runtime.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/26/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 37347df2d543116085f52fed76c692b60fac2ad6
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285705"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-recommendations-for-azure-ssis-integration-runtime"></a>Obchodní kontinuity podnikových procesů a po havárii doporučení pro zotavení (BCDR) pro Azure-SSIS Integration Runtime

Pro účely zotavení po havárii můžete zastavit prostředí Azure-SSIS integration runtime v oblasti, ve kterém je aktuálně spuštěna a přepněte do jiné oblasti spusťte znovu. Doporučujeme, abyste použili [spárované oblasti Azure](../best-practices-availability-paired-regions.md) pro tento účel.

## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že jste povolili zotavení po havárii pro váš server Azure SQL Database v případě, že má server ve stejnou dobu výpadku. Další informace najdete v tématu [přehled kontinuity obchodních procesů ve službě Azure SQL Database](../sql-database/sql-database-business-continuity.md).

- Pokud používáte virtuální sítě v aktuální oblasti, budete muset použít jinou virtuální sítí v nové oblasti pro připojení prostředí Azure-SSIS integration runtime. Další informace najdete v tématu [připojit k prostředí Azure-SSIS integration runtime k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md).

- Pokud použijete vlastní nastavení, budete muset připravit jiném identifikátoru URI SAS pro kontejner objektů blob, který ukládá vlastní instalační skript a přidružené soubory, takže ho dál dostupná během výpadků. Další informace najdete v tématu [nakonfigurovat vlastní nastavení v prostředí Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="steps"></a>Kroky

Použijte následující postup zastavit prostředí Azure-SSIS IR, přepnout prostředí IR do nové oblasti a znovu spustit.

1. Zastavte prostředí IR v oblasti původní.

2. Zavoláním následujícího příkazu v Powershellu aktualizovat prostředí IR

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
