---
title: Změna konfigurace prostředí Azure-SSIS integration runtime | Dokumentace Microsoftu
description: Zjistěte, jak změnit konfiguraci prostředí Azure-SSIS integration runtime ve službě Azure Data Factory, jakmile už ho máte zřízená.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 3c1178a20debc36fbdbbd374eaf9adb6005a93a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "66152353"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Změna konfigurace prostředí Azure-SSIS integration runtime
Tento článek popisuje, jak překonfigurovat existující Azure-SSIS integration runtime. Vytvoření prostředí Azure-SSIS integration runtime (IR) ve službě Azure Data Factory najdete v tématu [vytvořit prostředí Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md).  

## <a name="data-factory-ui"></a>Uživatelské rozhraní Data Factory 
Uživatelské rozhraní služby Data Factory můžete použít k zastavení, úpravy/změna konfigurace nebo odstranění Azure-SSIS IR. 

1. V **uživatelské rozhraní služby Data Factory**, přepněte **upravit** kartu. Chcete-li spustit uživatelské rozhraní služby Data Factory, klikněte na tlačítko **vytvořit a monitorovat** na domovské stránce vaší datové továrně.
2. V levém podokně klikněte na tlačítko **připojení**.
3. V pravém podokně, přepněte **prostředí Integration runtime**. 
4. Můžete pomocí tlačítek ve sloupci Akce **Zastavit**, **upravit**, nebo **odstranit** prostředí integration runtime. **Kód** tlačítko **akce** sloupec umožňuje zobrazit definice JSON přidružený k modulu integration runtime.  
    
    ![Akce pro prostředí Azure SSIS IR](./media/manage-azure-ssis-integration-runtime/actions-for-azure-ssis-ir.png)

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Změna konfigurace prostředí Azure-SSIS IR
1. Zastavit prostředí integration runtime kliknutím **Zastavit** v **akce** sloupce. Chcete-li obnovit zobrazení seznamu, klikněte na tlačítko **aktualizovat** na panelu nástrojů. Po zastavení prostředí IR uvidíte, že první akci umožňuje spuštění IR. 

    ![Akce pro prostředí Azure SSIS IR – po zastavení](./media/manage-azure-ssis-integration-runtime/actions-after-ssis-ir-stopped.png)
2. Upravit/reconfigure reakcí na Incidenty kliknutím **upravit** tlačítko **akce** sloupce. V **instalace prostředí Integration Runtime** okna, změnit nastavení (například velikost uzlu, počet uzlů, nebo maximální paralelních úloh na uzlu). 
3. Restartujte IR, klikněte na tlačítko **Start** tlačítko **akce** sloupce.     

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Po zřízení a spuštění instance prostředí Azure-SSIS integration runtime, můžete ji překonfigurovat spuštěním sekvence `Stop`  -  `Set`  -  `Start` rutin prostředí PowerShell postupně. Například následující příkaz powershellu změní z počtu uzlů, které jsou přiděleny pro instanci prostředí Azure-SSIS integration runtime na pět.

### <a name="reconfigure-an-azure-ssis-ir"></a>Změna konfigurace prostředí Azure-SSIS IR

1. Nejprve zastavit pomocí prostředí Azure-SSIS integration runtime [Stop-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/stop-Azdatafactoryv2integrationruntime) rutiny. Tento příkaz uvolní všechny jeho uzly a zastaví fakturaci.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. V dalším kroku znovu nakonfigurovat prostředí Azure-SSIS IR s použitím [Set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) rutiny. Následující ukázkový příkaz kolísá prostředí Azure-SSIS integration runtime na pět uzlů.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. Spusťte prostředí Azure-SSIS integration runtime s použitím [Start AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) rutiny. Tento příkaz přidělí všechny jeho uzly pro spouštění balíčků služby SSIS.   

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

### <a name="delete-an-azure-ssis-ir"></a>Odstranit prostředí Azure-SSIS IR
1. Nejprve vypište všechny existujících IR služby SSIS Azure v rámci vaší datové továrně.

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
    ```
2. V dalším kroku zastavte všechny stávající Azure SSIS IR ve službě data factory.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
3. V dalším kroku odeberte všechny existující Azure SSIS IR ve vaší datové továrně jeden po druhém.

    ```powershell
    Remove-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
4. Nakonec odeberte svou datovou továrnu.

    ```powershell
    Remove-AzDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
    ```
5. Pokud jste vytvořili novou skupinu prostředků, odeberte skupinu prostředků.

    ```powershell
    Remove-AzResourceGroup -Name $ResourceGroupName -Force 
    ```

## <a name="next-steps"></a>Další postup
Další informace o modulu runtime Azure-SSIS najdete v následujících tématech: 

- [Prostředí Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Tento článek obsahuje koncepční informace o prostředí integration runtime obecně včetně Azure-SSIS IR. 
- [Kurz: Nasazení balíčků SSIS do Azure](tutorial-create-azure-ssis-runtime-portal.md) Tento článek obsahuje podrobné pokyny pro vytvoření Azure-SSIS IR a využívá databázi Azure SQL k hostování katalogu SSIS. 
- [Postup: Vytvoření prostředí Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md). Tento článek dál navazuje na tento kurz a obsahuje pokyny k používání Azure SQL Database Managed Instance a připojení IR k virtuální síti. 
- [Připojení Azure-SSIS IR k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md). Tento článek obsahuje koncepční informace o připojení Azure-SSIS IR k virtuální síti Azure. Poskytuje také kroky pro využití webu Azure Portal ke konfiguraci virtuální sítě, aby se k ní prostředí Azure-SSIS IR mohlo připojit. 
- [Monitorování Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Tento článek ukazuje, jak načíst informace o Azure-SSIS IR a popisy stavů ve vrácených informacích. 
 
