---
title: 'Zřídit Enterprise Edition pro Azure-SSIS Integration Runtime '
description: Tento článek popisuje funkce edice Enterprise pro Azure-SSIS Integration Runtime a jejich zřízení.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/13/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 8c9db18d416d508dd5a98cbacc14b7db6c075a55
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73673953"
---
# <a name="provision-enterprise-edition-for-the-azure-ssis-integration-runtime"></a>Zřídit Enterprise Edition pro Azure-SSIS Integration Runtime

Edice Enterprise sady Azure-SSIS Integration Runtime vám umožňuje používat následující funkce Advanced a Premium:
-   Součásti Change Data Capture (CDC)
-   Konektory Oracle, Teradata a SAP BW
-   Konektory a transformace v SQL Server Analysis Services (SSAS) a Azure Analysis Services (AAS)
-   Fuzzy seskupení a transformace vyhledávání s fuzzy logikou
-   Extrakce termínů a transformace termínu vyhledávání

Některé z těchto funkcí vyžadují instalaci dalších komponent pro přizpůsobení Azure-SSIS IR. Další informace o tom, jak nainstalovat další součásti, najdete v tématu [vlastní nastavení pro prostředí Azure-SSIS Integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="enterprise-features"></a>Podnikové funkce

| **Podnikové funkce** | **Označení** |
|---|---|
| Komponenty CDC | V edici Azure-SSIS IR Enterprise je předinstalována předinstalace zdroje CDC, kontrolní úkol a rozdělovací transformace. Abyste se mohli připojit k Oracle, musíte taky nainstalovat návrháře a službu CDC v jiném počítači. |
| Konektory Oracle | Správce připojení Oracle, zdroj a cíl jsou předem nainstalovány v edici Azure-SSIS IR Enterprise. Je také potřeba nainstalovat ovladač Oracle Call Interface (OCI) a v případě potřeby nakonfigurovat TNS (Oracle Transport Network substrát) na Azure-SSIS IR. Další informace najdete v tématu [Vlastní nastavení pro prostředí Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Konektory Teradata | V edici Azure-SSIS IR Enterprise je nutné nainstalovat Správce připojení Teradata, zdroj a cíl i rozhraní API Teradata Parallel Transporter (TPT) a Teradata ODBC Driver. Další informace najdete v tématu [Vlastní nastavení pro prostředí Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Konektory SAP BW | SAP BW Správce připojení, zdroj a cíl jsou předem nainstalovány v Azure-SSIS IR Enterprise Edition. Také je nutné na Azure-SSIS IR nainstalovat ovladač SAP BW. Tyto konektory podporují SAP BW 7,0 nebo starší verze. Pokud se chcete připojit k novějším verzím SAP BW nebo jiným produktům SAP, můžete si v Azure-SSIS IR koupit a nainstalovat konektory SAP od jiných nezávislých výrobců softwaru. Další informace o tom, jak nainstalovat další součásti, najdete v tématu [vlastní nastavení pro prostředí Azure-SSIS Integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Analysis Services komponenty               | Verze školení modelu dolování dat, cíl zpracování dimenze a cíl zpracování oddílu a také transformace dotazu dolování dat jsou předem nainstalovány v edici Azure-SSIS IR Enterprise. Všechny tyto komponenty podporují SQL Server Analysis Services (SSAS), ale pouze cíl zpracování oddílu podporuje Azure Analysis Services (AAS). Pokud se chcete připojit k SSAS, musíte taky [nakonfigurovat přihlašovací údaje pro ověřování Windows v SSISDB](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth). Kromě těchto součástí jsou také předinstalovány úlohy Analysis Services spustit DDL, úloha zpracování Analysis Services a úloha dotaz dolování dat, a to i v edici Azure-SSIS IR Standard/Enterprise. |
| Fuzzy seskupení a transformace vyhledávání s fuzzy logikou  | Nepřibližná seskupení a transformace pro vyhledávání s fuzzy logikou jsou předem nainstalovány v edici Azure-SSIS IR Enterprise. Tyto komponenty podporují SQL Server i Azure SQL Database pro ukládání referenčních dat. |
| Extrakce termínů a transformace termínu vyhledávání | V Azure-SSIS IR Enterprise Edition jsou předem nainstalovány výrazy pro extrakci a vyhledávání termínů. Tyto komponenty podporují SQL Server i Azure SQL Database pro ukládání referenčních dat. |

## <a name="instructions"></a>Pokyny

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1.  Stáhněte a nainstalujte [Azure PowerShell](/powershell/azure/install-az-ps).

2.  Při zřizování nebo překonfigurování Azure-SSIS IR pomocí prostředí PowerShell spusťte před spuštěním Azure-SSIS IR `Set-AzDataFactoryV2IntegrationRuntime` s parametrem **Enterprise** jako hodnotu parametru **Edition** . Tady je ukázkový skript:

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

-   [Vlastní nastavení pro prostředí Azure-SSIS Integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Jak vyvíjet placené nebo licencované vlastní komponenty pro prostředí Azure-SSIS Integration runtime](how-to-develop-azure-ssis-ir-licensed-components.md)
