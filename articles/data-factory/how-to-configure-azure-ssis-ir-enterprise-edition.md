---
title: Zřízení Enterprise Edition pro prostředí Azure-SSIS Integration Runtime | Dokumentace Microsoftu
description: Tento článek popisuje funkce Enterprise Edition pro prostředí Azure-SSIS Integration Runtime a tom, jak zřídit
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
ms.openlocfilehash: c475a9c820e577a321491b87d806ed08ddff8693
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54013873"
---
# <a name="provision-enterprise-edition-for-the-azure-ssis-integration-runtime"></a>Zřízení Enterprise Edition pro prostředí Azure-SSIS Integration Runtime

Azure-SSIS Integration Runtime ve verzi Enterprise umožňuje použití, které tyto pokročilé a prémiové funkce:
-   Změnit Data Capture (CDC) komponenty
-   Konektory SAP BW, Oracle a Teradata
-   Konektory SQL Server Analysis Services (SSAS) a Azure Analysis Services (AAS) a transformace
-   Přibližné seskupení a vyhledávání s fuzzy logikou transformace
-   Extrakce termínu a období vyhledávání transformace

Některé z těchto funkcí vyžadovat instalaci dalších komponent pro přizpůsobení prostředí Azure-SSIS IR. Další informace o tom, jak nainstalovat další komponenty, naleznete v tématu [vlastní nastavení pro prostředí Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="enterprise-features"></a>Podnikové funkce

| **Podnikové funkce** | **Popisy** |
|---|---|
| CDC komponenty | CDC zdroje, ovládací prvek úkolu a rozdělovač transformace jsou předinstalovány v prostředí Azure-SSIS IR Enterprise Edition. Pro připojení k Oracle, budete také muset nainstalovat CDC designeru a služby na jiném počítači. |
| Konektory Oracle | Správce připojení Oracle, zdroj a cíl jsou předinstalovány v prostředí Azure-SSIS IR Enterprise Edition. Také je třeba nainstalovat ovladače Oracle volání rozhraní (OCI) a v případě potřeby nakonfigurujte Oracle přenosu sítě podložky (TNS), v prostředí Azure-SSIS IR. Další informace najdete v tématu [Vlastní nastavení pro prostředí Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Konektory Teradata | Je potřeba nainstalovat Správce připojení Teradata, zdroj a cíl, jakož i ovladač Teradata paralelní přepravce (TPT) rozhraní API a rozhraní ODBC Teradata v prostředí Azure-SSIS IR Enterprise Edition. Další informace najdete v tématu [Vlastní nastavení pro prostředí Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Konektory SAP BW | Správce připojení k SAP BW, zdroj a cíl jsou předinstalovány v prostředí Azure-SSIS IR Enterprise Edition. Je také potřeba nainstalovat ovladač SAP BW v prostředí Azure-SSIS IR. Tyto konektory podporují SAP BW 7.0 nebo dřívější verze. Pro připojení k novější verze SAP BW nebo jiné produkty SAP, můžete zakoupit a nainstalovat konektory SAP ze softwaru třetích stran v prostředí Azure-SSIS IR. Další informace o tom, jak nainstalovat další komponenty, naleznete v tématu [vlastní nastavení pro prostředí Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Součásti služby analýzy               | Cílové školení Data Mining Model, cíl zpracování dimenze a cíl zpracování oddílu, jakož i dotazu transformace dolování dat, jsou předinstalovány v prostředí Azure-SSIS IR Enterprise Edition. Všechny tyto součásti podporuje SQL Server Analysis Services (SSAS), ale pouze oddílu zpracování cíl podporuje Azure Analysis Services (AAS). Pro připojení k službě SSAS, budete také muset [nakonfigurovat přihlašovací údaje pro ověřování Windows v SSISDB](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth). Kromě těchto součástí úkol DDL provést Analysis Services, Analysis Services zpracování úloh a úlohy dotaz dolování dat se také jako předinstalované na prostředí Azure-SSIS IR Standard nebo Enterprise Edition. |
| Přibližné seskupení a vyhledávání s fuzzy logikou transformace  | Seskupení přibližných shod a vyhledávání s fuzzy logikou transformace jsou předinstalovány v prostředí Azure-SSIS IR Enterprise Edition. Tyto součásti podporu systému SQL Server a Azure SQL Database pro ukládání referenční data. |
| Extrakce termínu a období vyhledávání transformace | Termín extrakce a vyhledávací termín transformace jsou předinstalovány v prostředí Azure-SSIS IR Enterprise Edition. Tyto součásti podporu systému SQL Server a Azure SQL Database pro ukládání referenční data. |

## <a name="instructions"></a>Pokyny

1.  Stáhněte a nainstalujte [prostředí Azure PowerShell (verze 5.4 nebo novější)](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018).

2.  Při zřízení nebo změnit konfiguraci prostředí Azure-SSIS IR pomocí prostředí PowerShell, spusťte `Set-AzureRmDataFactoryV2IntegrationRuntime` s **Enterprise** hodnotu **Edition** parametr před zahájením prostředí Azure-SSIS IR. Tady je ukázkový skript:

    ```powershell
    $MyAzureSsisIrEdition = "Enterprise"

    Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                               -Name $MyAzureSsisIrName
                                               -ResourceGroupName $MyResourceGroupName
                                               -Edition $MyAzureSsisIrEdition

    Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                                 -Name $MyAzureSsisIrName
                                                 -ResourceGroupName $MyResourceGroupName
    ```

## <a name="next-steps"></a>Další postup

-   [Vlastní nastavení pro prostředí Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Jak vyvíjet placené nebo licenci vlastní komponenty prostředí Azure-SSIS integration runtime](how-to-develop-azure-ssis-ir-licensed-components.md)
