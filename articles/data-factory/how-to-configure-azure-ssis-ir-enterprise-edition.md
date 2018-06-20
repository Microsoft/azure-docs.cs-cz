---
title: Zřídit Enterprise Edition pro modul Runtime integrace Azure SSIS | Microsoft Docs
description: Tento článek popisuje funkce Enterprise Edition pro modul Runtime integrace Azure SSIS a jak zřídit
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/13/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 73997345895bc54f54db1d66c0c6c24c24153dd2
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268105"
---
# <a name="provision-enterprise-edition-for-the-azure-ssis-integration-runtime"></a>Zřízení Enterprise Edition pro modul Runtime integrace Azure SSIS

Enterprise Edition modulu runtime integrace Azure SSIS vám umožní používat následující advanced a prémiové funkce:
-   Změnit součásti Data Capture (CDC)
-   Konektory SAP BW, Oracle a Teradata
-   SQL Server Analysis Services (SSAS) a Azure Analysis Services (AAS) konektory a transformace
-   Přibližné seskupení a vyhledávání s fuzzy logikou transformace
-   Extrakce podmínek a podmínek vyhledávání transformace

Některé z těchto funkcí vyžadují, abyste instalaci dalších součástí přizpůsobit infračerveného signálu Azure SSIS. Další informace o tom, jak nainstalovat další součásti najdete v tématu [vlastní nastavení pro modul runtime integrace Azure SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="enterprise-features"></a>Funkce Enterprise

| **Funkce Enterprise** | **Popisy** |
|---|---|
| Součásti CDC | Zdroj CDC, řízení úloh a transformaci rozdělovače jsou předinstalován v Azure SSIS IR Enterprise Edition. Pro připojení k Oracle, musíte také nainstalovat CDC Designer a služby v jiném počítači. |
| Konektory Oracle | Správce připojení Oracle, zdroj a cíl je předinstalován v Azure SSIS IR Enterprise Edition. Také je potřeba nainstalovat ovladač Oracle volání rozhraní (OCI) a v případě potřeby nakonfigurujte Oracle přenosu sítě podložky (TNS), na infračerveného signálu Azure SSIS. Další informace najdete v tématu [Vlastní nastavení pro prostředí Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Konektory Teradata | Musíte nainstalovat Správce připojení Teradata, zdroje a určení, jakož i Teradata paralelní přepravce (TPT) rozhraní API a rozhraní ODBC Teradata ovladače, na edici Enterprise Azure SSIS reakcí na Incidenty. Další informace najdete v tématu [Vlastní nastavení pro prostředí Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Konektory SAP BW | Správce připojení SAP BW, zdroj a cíl jsou předinstalován v Azure SSIS IR Enterprise Edition. Musíte taky nainstalovat ovladač SAP BW na infračerveného signálu Azure SSIS. Tyto konektory podporovat SAP BW 7.0 nebo dřívější verzí. Pro připojení k novější verze SAP BW nebo jiné produkty SAP, můžete zakoupit a nainstalovat SAP konektory z nezávislí výrobci softwaru třetích stran na infračerveného signálu Azure SSIS. Další informace o tom, jak nainstalovat další součásti najdete v tématu [vlastní nastavení pro modul runtime integrace Azure SSIS](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Součásti Analysis Services               | Cílové školení Data Mining Model, cílový zpracování dimenze a cíl zpracování oddílu, jakož i dotazu transformaci dolování dat, jsou předinstalován v Azure SSIS IR Enterprise Edition. Všechny tyto součásti podpory SQL Server Analysis Services (SSAS), avšak pouze oddílu zpracování cíl podporuje Azure Analysis Services (AAS). Pokud chcete připojit ke službě SSAS, budete také muset [konfigurace pověření pro ověřování systému Windows v SSISDB](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth). Kromě těchto součástí úloh DDL provést Analysis Services, Analysis Services zpracování úlohy a úlohy dotaz dolování dat jsou také předinstalovat Azure SSIS IR Standard nebo Enterprise Edition. |
| Přibližné seskupení a vyhledávání s fuzzy logikou transformace  | Transformace přibližné seskupení a vyhledávání s fuzzy logikou jsou předinstalován v Azure SSIS IR Enterprise Edition. Tyto komponenty podporují systém SQL Server a databáze SQL Azure pro ukládání referenční data. |
| Extrakce podmínek a podmínek vyhledávání transformace | Extrakce podmínek a podmínek vyhledávání transformace jsou předinstalován v Azure SSIS IR Enterprise Edition. Tyto komponenty podporují systém SQL Server a databáze SQL Azure pro ukládání referenční data. |

## <a name="instructions"></a>Pokyny

1.  Stáhněte a nainstalujte [prostředí Azure PowerShell (verze 5.4 nebo novější)](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018).

2.  Při zřízení nebo překonfigurujte IR Azure SSIS pomocí prostředí PowerShell, spusťte `Set-AzureRmDataFactoryV2IntegrationRuntime` s **Enterprise** hodnotu **edice** parametr před zahájením infračerveného signálu Azure SSIS. Tady je ukázkový skript:

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

-   [Vlastní instalace pro modul runtime integrace Azure SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Jak vyvíjet placené nebo licencovaná vlastní komponenty pro modul runtime integrace Azure SSIS](how-to-develop-azure-ssis-ir-licensed-components.md)
