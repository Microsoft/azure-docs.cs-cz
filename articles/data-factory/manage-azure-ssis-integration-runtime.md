---
title: Změňte konfiguraci modulu runtime integrace Azure SSIS | Microsoft Docs
description: Zjistěte, jak změnit konfiguraci modulu runtime integrace Azure SSIS v Azure Data Factory po jste ho už zřízené.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: ''
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: douglasl
ms.openlocfilehash: 9932ee862a9cdc7591c62c016e888d9e5d593cf7
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="manage-an-azure-ssis-integration-runtime"></a>Správa modulu runtime integrace Azure SSIS
[Vytvoření modulu runtime integrace Azure SSIS](create-azure-ssis-integration-runtime.md) článek ukazuje, jak vytvořit modulu runtime integrace Azure SSIS (IR) pomocí Azure Data Factory. Tento článek obsahuje informace o změnu konfigurace modulu runtime pro existující integrace Azure SSIS.  

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), prostudujte si [dokumentaci služby Data Factory verze 1](v1/data-factory-introduction.md).


## <a name="data-factory-ui"></a>Uživatelské rozhraní Data Factory 
Objekt pro vytváření dat uživatelského rozhraní můžete použít k zastavení, upravit nebo reconfigure nebo odstranění služby Azure SSIS infračerveného signálu. 

1. V **uživatelského rozhraní objektu pro vytváření dat**, přepnout **upravit** kartě. Chcete-li spustit uživatelské rozhraní objektu pro vytváření dat, klikněte na tlačítko **Autor & monitorování** na domovské stránce objektu pro vytváření vaše data.
2. V levém podokně klikněte na **připojení**.
3. V pravém podokně přepnout **integrační moduly runtime**. 
4. Pomocí tlačítek ve sloupci Akce **Zastavit**, **upravit**, nebo **odstranit** integrace modulu runtime. **Kód** v tlačítko **akce** sloupec umožňuje zobrazit definici JSON související s modulem runtime integrace.  
    
    ![Akce pro Azure SSIS reakcí na Incidenty](./media/manage-azure-ssis-integration-runtime/actions-for-azure-ssis-ir.png)

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Změna konfigurace služby Azure SSIS reakcí na Incidenty
1. Zastavení modulu runtime integrace kliknutím **Zastavit** v **akce** sloupce. Chcete-li aktualizovat zobrazení seznamu, klikněte na tlačítko **aktualizovat** na panelu nástrojů. Po zastavení IR uvidíte, že je první akcí umožňuje spustit infračerveného signálu. 

    ![Akce pro Azure SSIS IR - po zastavení](./media/manage-azure-ssis-integration-runtime/actions-after-ssis-ir-stopped.png)
2. Upravit/reconfigure IR kliknutím **upravit** v tlačítko **akce** sloupec. V **nastavení integrace modulu Runtime** okno, změnit nastavení (například velikost uzlu, počtu uzlů nebo maximální paralelní spuštěních na každém uzlu). 
3. Pokud chcete restartovat reakcí na Incidenty, klikněte na tlačítko **spustit** v tlačítko **akce** sloupec.     

## <a name="azure-powershell"></a>Azure PowerShell
Po zřízení a spuštění instance služby Azure SSIS integrace runtime, můžete ji překonfigurovat spuštěním posloupnost `Stop`  -  `Set`  -  `Start` rutiny prostředí PowerShell za sebou. Například následující skript prostředí PowerShell změní počet uzlů přidělených pro instanci modulu runtime integrace Azure SSIS na pět.

### <a name="reconfigure-an-azure-ssis-ir"></a>Znovu nakonfigurujte Azure SSIS reakcí na Incidenty

1. Nejprve zastavit runtime integrace Azure SSIS pomocí [Stop-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/stop-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1) rutiny. Tento příkaz uvolní všechny jeho uzly a zastaví fakturace.

    ```powershell
    Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. Dále překonfigurujte IR Azure SSIS pomocí [Set-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1) rutiny. Následující ukázkový příkaz horizontálně navýší kapacitu, modulu runtime Azure SSIS integrace do pěti uzlů.

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. Spusťte modul runtime integrace Azure SSIS pomocí [Start-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/start-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1) rutiny. Tento příkaz přiděluje všemi uzly pro spouštění balíčků SSIS.   

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

### <a name="delete-an-azure-ssis-ir"></a>Odstranění služby Azure SSIS reakcí na Incidenty
1. Nejprve seznam všech existujících SSIS finanční úřad Azure v rámci vaší služby data factory.

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
    ```
2. V dalším kroku zastavte všechny existující Azure SSIS finanční úřad v datové továrně.

    ```powershell
    Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
3. Potom odstraňte všechny existující finanční úřad Azure SSIS služby data factory po jednom.

    ```powershell
    Remove-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
4. Nakonec odeberte datovou továrnu.

    ```powershell
    Remove-AzureRmDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
    ```
5. Pokud jste vytvořili novou skupinu prostředků, odeberte skupinu zdrojů.

    ```powershell
    Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force 
    ```

## <a name="next-steps"></a>Další postup
Další informace o běhu Azure SSIS najdete v následujících tématech: 

- [Modul Runtime integrace Azure SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Tento článek obsahuje koncepční informace o integraci runtimes obecně včetně infračerveného signálu Azure SSIS. 
- [Kurz: Nasazení balíčků SSIS do Azure](tutorial-create-azure-ssis-runtime-portal.md) Tento článek obsahuje podrobné pokyny pro vytvoření Azure-SSIS IR a využívá databázi Azure SQL k hostování katalogu SSIS. 
- [Postup: Vytvoření prostředí Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md) Tento článek rozšíří na kurz a poskytuje pokyny o používání spravované Instance Azure SQL (Preview) a připojení k virtuální síti reakcí na Incidenty. 
- [Připojení Azure-SSIS IR k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md) Tento článek obsahuje koncepční informace o připojení Azure-SSIS IR k virtuální síti Azure (VNet). Poskytuje také kroky pro využití webu Azure Portal ke konfiguraci virtuální sítě, aby se k ní prostředí Azure-SSIS IR mohlo připojit. 
- [Monitorování Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Tento článek ukazuje, jak načíst informace o Azure-SSIS IR a popisy stavů ve vrácených informacích. 
 
