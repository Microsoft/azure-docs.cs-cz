---
title: Zřízení edice Enterprise pro runtime integrace Azure-SSIS
description: Tento článek popisuje funkce Enterprise Edition pro Azure-SSIS Integration Runtime a jak ji zřídit
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/13/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: anandsub
ms.openlocfilehash: a0e35a309fd30a36263b498b6a8e74a312004a99
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606159"
---
# <a name="provision-enterprise-edition-for-the-azure-ssis-integration-runtime"></a>Zřízení edice Enterprise pro runtime integrace Azure-SSIS

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Enterprise Edition prostředí Azure-SSIS Integration Runtime umožňuje používat následující pokročilé a prémiové funkce:
-   Změnit součásti sběru dat (CDC)
-   Konektory Oracle, Teradata a SAP BW
-   Konektory a transformace služby SQL Server Analysis Services (SSAS) a Služby Azure Analysis Services (AAS)
-   Transformace fuzzy seskupení a fuzzy vyhledávání
-   Transformace extrakce termínu a vyhledávání termínů

Některé z těchto funkcí vyžadují instalaci dalších součástí pro přizpůsobení azure-SSIS IR. Další informace o instalaci dalších součástí najdete [v tématu Vlastní nastavení pro prostředí integrace Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="enterprise-features"></a>Funkce Enterprise

| **Podnikové funkce** | **Popisy** |
|---|---|
| Komponenty CDC | Zdroj CDC, řídicí úloha a transformace splitteru jsou předinstalovány v počítačích Azure-SSIS IR Enterprise Edition. Chcete-li se připojit k řešení Oracle, je také nutné nainstalovat návrháře a službu CDC do jiného počítače. |
| Konektory Oracle | Oracle Connection Manager, Source a Destination jsou předinstalovány v azure-SSIS IR Enterprise Edition. Je také nutné nainstalovat ovladač Oracle Call Interface (OCI) a v případě potřeby nakonfigurovat substrát oracle transportové sítě (TNS) v zařízení Azure-SSIS IR. Další informace najdete v tématu [Vlastní nastavení pro prostředí Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Teradata konektory | Je třeba nainstalovat Teradata Connection Manager, zdroj a cíl, stejně jako Teradata Parallel Transporter (TPT) API a Teradata ODBC ovladač, na Azure-SSIS IR Enterprise Edition. Další informace najdete v tématu [Vlastní nastavení pro prostředí Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Konektory SAP BW | Sap BW Connection Manager, source a destination jsou předinstalovány na Azure-SSIS IR Enterprise Edition. Je také nutné nainstalovat ovladač SAP BW na Azure-SSIS IR. Tyto konektory podporují SAP BW 7.0 nebo starší verze. Chcete-li se připojit k novějším verzím SAP BW nebo jiných produktů SAP, můžete zakoupit a nainstalovat konektory SAP od neinstalačních techniků třetích stran na infračerveném zařízení Azure-SSIS. Další informace o instalaci dalších součástí najdete [v tématu Vlastní nastavení pro prostředí integrace Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Součásti analysis services               | Cíl školení modelu dolování dat, cíl zpracování dimenze a cíl zpracování oddílu, stejně jako transformace dotazu dolování dat, jsou předinstalovány na Azure-SSIS IR Enterprise Edition. Všechny tyto součásti podporují sql server analysis services (SSAS), ale pouze cíl zpracování oddílu podporuje Služby Azure Analysis Services (AAS). Chcete-li se připojit k systému SSAS, je také nutné [nakonfigurovat pověření ověřování systému Windows v databázi SSISDB](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth). Kromě těchto součástí jsou úloha DDL služby Analysis Services, úloha zpracování služby Analysis Services a úloha dotazu dolování dat také předinstalovány v azure-SSIS IR Standard/Enterprise Edition. |
| Transformace fuzzy seskupení a fuzzy vyhledávání  | Transformace fuzzy seskupení a fuzzy vyhledávání jsou předinstalovány v Azure-SSIS IR Enterprise Edition. Tyto součásti podporují SQL Server a Azure SQL Database pro ukládání referenčních dat. |
| Transformace extrakce termínu a vyhledávání termínů | Transformace extrakce termínu a vyhledávání termínů jsou předinstalovány v azure-Ssis IR Enterprise Edition. Tyto součásti podporují SQL Server a Azure SQL Database pro ukládání referenčních dat. |

## <a name="instructions"></a>Pokyny

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1.  Stáhněte a nainstalujte [Azure PowerShell](/powershell/azure/install-az-ps).

2.  Když zřídíte nebo překonfigurujete infračervený přenos Azure-SSIS pomocí prostředí PowerShell, spusťte `Set-AzDataFactoryV2IntegrationRuntime` **enterprise** jako hodnotu parametru **Edition** před spuštěním infračerveného serveru Azure-SSIS. Zde je ukázkový skript:

    ```powershell
    $MyAzureSsisIrEdition = "Enterprise"

    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                               -Name $MyAzureSsisIrName
                                               -ResourceGroupName $MyResourceGroupName
                                               -Edition $MyAzureSsisIrEdition

    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                                 -Name $MyAzureSsisIrName
                                                 -ResourceGroupName $MyResourceGroupName
    ```

## <a name="next-steps"></a>Další kroky

-   [Vlastní nastavení pro runtime integrace Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Jak vyvíjet placené nebo licencované vlastní komponenty pro runtime integrace Azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md)
