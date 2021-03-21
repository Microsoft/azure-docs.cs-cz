---
title: Změna konfigurace modulu Azure-SSIS Integration Runtime
description: Naučte se, jak znovu nakonfigurovat prostředí Azure-SSIS Integration runtime v Azure Data Factory poté, co jste ho již zřídili.
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/03/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 724411dc12654aec1614230c943923062b334cd2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100370678"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Změna konfigurace modulu Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek popisuje, jak znovu nakonfigurovat existující prostředí Azure-SSIS Integration runtime. Postup vytvoření prostředí Azure-SSIS Integration runtime (IR) v Azure Data Factory najdete v tématu [vytvoření prostředí Azure-SSIS Integration runtime](create-azure-ssis-integration-runtime.md).  

## <a name="data-factory-ui"></a>Uživatelské rozhraní Data Factory 
K zastavení, úpravám a překonfigurování nebo odstranění Azure-SSIS IR můžete použít Data Factory uživatelské rozhraní. 

1. Otevřete Data Factory uživatelské rozhraní tak, že na domovské stránce objektu pro vytváření dat vyberete dlaždici pro **monitorování autora &** .
2. Kliknutím na  centra pro správu **Domů**, **Upravit** a **monitorovat** zobrazíte podokno **připojení** .

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Postup překonfigurování Azure-SSIS IR
V podokně **připojení** v části **Spravovat** centrum přepněte na stránku **prostředí Integration runtime** a vyberte **aktualizovat**. 

   ![Podokno připojení](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   Azure-SSIS IR můžete upravit nebo překonfigurovat tak, že vyberete jeho název. Můžete také vybrat relevantní tlačítka pro monitorování, spouštění, zastavování a odstraňování Azure-SSIS IR, automatické vygenerování kanálu ADF s aktivitou spustit balíček SSIS ke spuštění v Azure-SSIS IR a zobrazení kódu JSON nebo datové části vašeho Azure-SSIS IR.  Úpravy a odstranění Azure-SSIS IR lze provést pouze v případě, že je zastaveno.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Po zřízení a spuštění instance prostředí Azure-SSIS Integration runtime je můžete překonfigurovat tak, že postupně spustíte posloupnost `Stop`  -  `Set`  -  `Start` rutin PowerShellu. Například následující skript prostředí PowerShell změní počet uzlů přidělených pro instanci prostředí Azure-SSIS Integration runtime na pět.

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
- [Kurz: Nasazení balíčků SSIS do Azure](./tutorial-deploy-ssis-packages-azure.md) Tento článek obsahuje podrobné pokyny k vytvoření Azure-SSIS IR a používá Azure SQL Database k hostování katalogu SSIS. 
- [Postup: Vytvoření prostředí Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md) Tento článek se rozbalí v tomto kurzu a poskytne pokyny k použití spravované instance Azure SQL a připojení IR k virtuální síti. 
- [Připojení Azure-SSIS IR k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md). Tento článek obsahuje koncepční informace o připojení Azure-SSIS IR k virtuální síti Azure. Poskytuje také kroky pro využití webu Azure Portal ke konfiguraci virtuální sítě, aby se k ní prostředí Azure-SSIS IR mohlo připojit. 
- [Monitorování Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Tento článek ukazuje, jak načíst informace o Azure-SSIS IR a popisy stavů ve vrácených informacích.