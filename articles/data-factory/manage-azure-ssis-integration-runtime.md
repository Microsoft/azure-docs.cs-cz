---
title: Překonfigurujte prostředí Azure-SSIS Integration runtime
description: Naučte se, jak znovu nakonfigurovat prostředí Azure-SSIS Integration runtime v Azure Data Factory poté, co jste ho již zřídili.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: anandsub
ms.openlocfilehash: fbac52d65433f2137d565ca60fcf754e49199640
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931398"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Překonfigurujte prostředí Azure-SSIS Integration runtime
Tento článek popisuje, jak znovu nakonfigurovat existující prostředí Azure-SSIS Integration runtime. Postup vytvoření prostředí Azure-SSIS Integration runtime (IR) v Azure Data Factory najdete v tématu [vytvoření prostředí Azure-SSIS Integration runtime](create-azure-ssis-integration-runtime.md).  

## <a name="data-factory-ui"></a>Uživatelské rozhraní Data Factory 
K zastavení, úpravám a překonfigurování nebo odstranění Azure-SSIS IR můžete použít Data Factory uživatelské rozhraní. 

1. V **uživatelském rozhraní Data Factory**přepněte na kartu **Upravit** . Pokud chcete spustit Data Factory uživatelské rozhraní, klikněte na tlačítko **vytvořit & monitor** na domovské stránce objektu pro vytváření dat.
2. V levém podokně klikněte na možnost **připojení**.
3. V pravém podokně přepněte na **modul runtime integrace**. 
4. Pomocí tlačítek ve sloupci akce můžete **zastavit**, **Upravit**nebo **Odstranit** modul runtime integrace. Tlačítko **Code (kód** ) ve sloupci **Actions (akce** ) umožňuje zobrazit definici JSON přidruženou k prostředí Integration runtime.  
    
    ![Akce pro Azure SSIS IR](./media/manage-azure-ssis-integration-runtime/actions-for-azure-ssis-ir.png)

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Postup překonfigurování Azure-SSIS IR
1. Zastavte prostředí Integration runtime kliknutím na **zastavit** ve sloupci **Actions (akce** ). Chcete-li aktualizovat zobrazení seznamu, klikněte na tlačítko **aktualizovat** na panelu nástrojů. Po zastavení prostředí IR vidíte, že první akce vám umožní spustit IR. 

    ![Akce pro Azure SSIS IR – po zastavení](./media/manage-azure-ssis-integration-runtime/actions-after-ssis-ir-stopped.png)
2. Kliknutím na tlačítko **Upravit** ve sloupci **Actions (akce** ) upravte nebo překonfigurujte IR. V okně **nastavení Integration runtime** změňte nastavení (například velikost uzlu, počet uzlů nebo maximální paralelní spouštění na uzel). 
3. Chcete-li restartovat prostředí IR, klikněte ve sloupci **Akce** na tlačítko **Start** .     

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Po zřízení a spuštění instance prostředí Azure-SSIS Integration runtime ji můžete znovu nakonfigurovat spuštěním sekvence `Stop` - `Set``Start`  - rutiny prostředí PowerShell po sobě. Například následující skript prostředí PowerShell změní počet uzlů přidělených pro instanci prostředí Azure-SSIS Integration runtime na pět.

### <a name="reconfigure-an-azure-ssis-ir"></a>Překonfigurujte Azure-SSIS IR

1. Nejprve zastavte prostředí Azure-SSIS Integration runtime pomocí rutiny [stop-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/stop-Azdatafactoryv2integrationruntime) . Tento příkaz uvolní všechny jeho uzly a zastaví fakturaci.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. Dále znovu nakonfigurujte Azure-SSIS IR pomocí rutiny [set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) . Následující vzorový příkaz škáluje prostředí Azure-SSIS Integration runtime na pět uzlů.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. Pak spusťte prostředí Azure-SSIS Integration runtime pomocí rutiny [Start-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) . Tento příkaz přiděluje všechny jeho uzly pro spouštění balíčků SSIS.   

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

### <a name="delete-an-azure-ssis-ir"></a>Odstranit Azure-SSIS IR
1. Nejprve vypíšete seznam všech existujících úřadů Azure SSIS v rámci vaší datové továrny.

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
    ```
2. Dál zastavte všechny existující finanční služby Azure SSIS ve vaší datové továrně.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
3. V dalším kroku odeberte všechny stávající finanční služby Azure SSIS ve vaší datové továrně jeden po jednom.

    ```powershell
    Remove-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
4. Nakonec odeberte datovou továrnu.

    ```powershell
    Remove-AzDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
    ```
5. Pokud jste vytvořili novou skupinu prostředků, odeberte skupinu prostředků.

    ```powershell
    Remove-AzResourceGroup -Name $ResourceGroupName -Force 
    ```

## <a name="next-steps"></a>Další kroky
Další informace o modulu runtime Azure-SSIS najdete v následujících tématech: 

- [Azure-SSIS Integration runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Tento článek obsahuje koncepční informace o integračních modulech Integration obecně včetně Azure-SSIS IR. 
- [Kurz: Nasazení balíčků SSIS do Azure](tutorial-create-azure-ssis-runtime-portal.md) Tento článek obsahuje podrobné pokyny pro vytvoření Azure-SSIS IR a využívá databázi Azure SQL k hostování katalogu SSIS. 
- [Postup: Vytvoření prostředí Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md) Tento článek se rozbalí v tomto kurzu a poskytne pokyny k používání Azure SQL Database spravované instance a připojení IR k virtuální síti. 
- [Připojení Azure-SSIS IR k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md). Tento článek obsahuje koncepční informace o připojení Azure-SSIS IR k virtuální síti Azure. Poskytuje také kroky pro využití webu Azure Portal ke konfiguraci virtuální sítě, aby se k ní prostředí Azure-SSIS IR mohlo připojit. 
- [Monitorování Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Tento článek ukazuje, jak načíst informace o Azure-SSIS IR a popisy stavů ve vrácených informacích. 
 
