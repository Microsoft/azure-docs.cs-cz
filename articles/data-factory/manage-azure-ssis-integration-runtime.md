---
title: Změna konfigurace modulu Azure-SSIS Integration Runtime
description: Zjistěte, jak překonfigurovat runtime integrace Azure-SSIS v Azure Data Factory poté, co jste ji už zřídili.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931398"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Změna konfigurace modulu Azure-SSIS Integration Runtime
Tento článek popisuje, jak překonfigurovat existující prostředí integrace Azure-SSIS. Pokud chcete vytvořit runtime integrace Azure-SSIS (IR) v Azure Data Factory, přečtěte si informace [o vytvoření runtime integrace Azure-SSIS](create-azure-ssis-integration-runtime.md).  

## <a name="data-factory-ui"></a>Uživatelské rozhraní Data Factory 
Pomocí uzdu Factory můžete zastavit, upravit nebo překonfigurovat nebo odstranit Azure-SSIS Ir. 

1. V **uzdu Datové továrny**přepněte na kartu **Úpravy.** Pokud chcete spustit uzonování Datové továrny, klikněte na domovské stránce datové továrny na **& monitor autora.**
2. V levém podokně klepněte na **položku Připojení**.
3. V pravém podokně přepněte na **integrační runtimes**. 
4. Pomocí tlačítek ve sloupci Akce můžete **zastavit**, **upravit**nebo **odstranit** integrační runtime. Tlačítko **Kód** ve sloupci **Akce** umožňuje zobrazit definici JSON přidruženou k integračnímu běhu.  
    
    ![Akce pro Azure SSIS IR](./media/manage-azure-ssis-integration-runtime/actions-for-azure-ssis-ir.png)

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Změna konfigurace infračerveného serveru Azure-SSIS
1. Zastavte integrační běh kliknutím na **Zastavit** ve sloupci **Akce.** Chcete-li aktualizovat zobrazení seznamu, klepněte na tlačítko **Aktualizovat** na panelu nástrojů. Po zastavení infračerveného přenosového provozu uvidíte, že první akce umožňuje spustit infračervený přenos. 

    ![Akce pro Azure SSIS IR - po zastavení](./media/manage-azure-ssis-integration-runtime/actions-after-ssis-ir-stopped.png)
2. Upravte nebo změňte konfiguraci infračerveného počítače kliknutím na tlačítko **Upravit** ve sloupci **Akce.** V okně **Instalace prostředí Integration Runtime** změňte nastavení (například velikost uzlu, počet uzlů nebo maximální paralelní spuštění na uzel). 
3. Chcete-li infračervený přenos restartovat, klepněte ve sloupci **Akce** na tlačítko **Start.**     

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Po zřízení a spuštění instance runtime integrace Azure-SSIS, můžete ji překonfigurovat spuštěním posloupnosti `Stop`  -  `Set`  -  `Start` rutin prostředí PowerShell po sobě. Například následující skript prostředí PowerShell změní počet uzlů přidělených pro instanci runtime integrace Azure-SSIS na pět.

### <a name="reconfigure-an-azure-ssis-ir"></a>Změna konfigurace infračerveného serveru Azure-SSIS

1. Nejprve zastavte za běhu integrace Azure-SSIS pomocí rutiny [Stop-AzDataFactoryV2IntegrationRuntime.](/powershell/module/az.datafactory/stop-Azdatafactoryv2integrationruntime) Tento příkaz uvolní všechny jeho uzly a zastaví fakturaci.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. Dále překonfigurujte azure-SSIS IR pomocí rutiny [Set-AzDataFactoryV2IntegrationRuntime.](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) Následující ukázkový příkaz škáluje zaběhu integrace Azure-SSIS na pět uzlů.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. Potom spusťte runtime integrace Azure-SSIS pomocí rutiny [Start-AzDataFactoryV2IntegrationRuntime.](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) Tento příkaz přiděluje všechny své uzly pro spouštění balíčků SSIS.   

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

### <a name="delete-an-azure-ssis-ir"></a>Odstranění infračerveného počítače Azure-SSIS
1. Nejprve uveďte všechny existující irs Azure SSIS v rámci vaší datové továrny.

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
    ```
2. Dále zastavte všechny existující Azure SSIS IRs ve vaší datové továrně.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
3. Dále odeberte všechny existující IRs Azure SSIS v datové továrně jeden po druhém.

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
Další informace o běhu Azure-SSIS najdete v následujících tématech: 

- [Azure-SSIS Integrace Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Tento článek obsahuje rámcové informace o integračních časech runtimes obecně, včetně Azure-SSIS IR. 
- [Kurz: Nasazení balíčků SSIS do Azure](tutorial-create-azure-ssis-runtime-portal.md) Tento článek obsahuje podrobné pokyny pro vytvoření Azure-SSIS IR a využívá databázi Azure SQL k hostování katalogu SSIS. 
- [Postup: Vytvoření prostředí Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md) Tento článek rozšiřuje kurz a obsahuje pokyny k použití Azure SQL Database Spravované instance a připojení infračerveného počítače k virtuální síti. 
- [Připojení Azure-SSIS IR k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md). Tento článek obsahuje koncepční informace o připojení Azure-SSIS IR k virtuální síti Azure. Poskytuje také kroky pro využití webu Azure Portal ke konfiguraci virtuální sítě, aby se k ní prostředí Azure-SSIS IR mohlo připojit. 
- [Monitorování Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Tento článek ukazuje, jak načíst informace o Azure-SSIS IR a popisy stavů ve vrácených informacích. 
 
