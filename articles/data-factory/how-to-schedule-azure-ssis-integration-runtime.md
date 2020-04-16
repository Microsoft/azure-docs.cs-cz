---
title: Jak naplánovat runtime integrace Azure-SSIS
description: Tento článek popisuje, jak naplánovat spuštění a zastavení prostředí integrace Azure-SSIS pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 8/2/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: anandsub
ms.openlocfilehash: 39f758b779e7c4935feab2424be16b829db8e46b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399516"
---
# <a name="how-to-start-and-stop-azure-ssis-integration-runtime-on-a-schedule"></a>Plánované spouštění a zastavování prostředí Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek popisuje, jak naplánovat spuštění a zastavení Azure-SSIS Integrace Runtime (IR) pomocí Azure Data Factory (ADF). Azure-SSIS IR je výpočetní prostředek ADF určený pro provádění balíčků služby SQL Server Integration Services (SSIS). Spuštění azure-SSIS IR má náklady s ním spojené. Proto obvykle chcete spustit infračervený přenos pouze v případě, že potřebujete spustit balíčky SSIS v Azure a zastavit infračervený přenos, když už ho nepotřebujete. Pomocí uživatelského rozhraní (UI) (ADF) /app nebo Azure PowerShell [můžete ručně spustit nebo zastavit infračervený přenos).](manage-azure-ssis-integration-runtime.md)

Případně můžete vytvořit webové aktivity v kanálech ADF pro spuštění nebo zastavení infračerveného serveru podle plánu, například spuštění v dopoledních hodinách před spuštěním denníúlohy ETL a zastavení v odpoledních hodinách po dokončení.  Můžete také zřetězit aktivitu balíčku SSIS mezi dvěma webovými aktivitami, které spouštějí a zastavují infračervený přenos, takže infračervený přenos se spustí nebo zastaví na vyžádání, právě včas před nebo po spuštění balíčku. Další informace o spuštění aktivity balíčku SSIS naleznete [v tématu Spuštění balíčku SSIS pomocí spuštění aktivity balíčku SSIS v článku kanálu ADF.](how-to-invoke-ssis-package-ssis-activity.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky
Pokud jste ještě nezpracovali azure-SSIS IR, zřazujte ji podle pokynů v [kurzu](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="create-and-schedule-adf-pipelines-that-start-and-or-stop-azure-ssis-ir"></a>Vytvoření a naplánování kanálů ADF, které spouštějí nebo zastavují službu Azure-SSIS IR
Tato část ukazuje, jak používat webové aktivity v kanálech ADF spustit nebo zastavit azure-SSIS IR podle plánu nebo spustit & zastavit na vyžádání. Provedeme vás vytvořením tří kanálů: 

1. První kanál obsahuje webovou aktivitu, která spustí infračervený přenos Azure-SSIS. 
2. Druhý kanál obsahuje webovou aktivitu, která zastaví infračervený přenos Azure-SSIS.
3. Třetí kanál obsahuje aktivitu spouštění balíčku SSIS zřetězenou mezi dvěma webovými aktivitami, které spouštějí a zastavují infračervený přenos Azure-SSIS. 

Po vytvoření a testování těchto kanálů můžete vytvořit aktivační událost plánu a přidružit ji k libovolnému kanálu. Aktivační událost plánu definuje plán pro spuštění přidruženého kanálu. 

Můžete například vytvořit dvě aktivační události, první je naplánováno spuštění denně v 6:00 a přidružené k první kanál, zatímco druhý je naplánováno spustit denně v 6 hodin a přidružené k druhému kanálu.  Tímto způsobem máte období mezi 6:00 a 18:00 každý den, kdy je spuštěna infračervená infračervená, připravena ke spuštění denní úlohy ETL.  

Pokud vytvoříte třetí aktivační událost, která je naplánována na spuštění denně o půlnoci a přidružená ke třetímu kanálu, bude tento kanál spuštěn každý den o půlnoci, spustí infračervený přenos těsně před spuštěním balíčku, následně spustí váš balíček a okamžitě zastaví infračervený přenos těsně po spuštění balíčku, takže infračervený přenos nebude spuštěn idly.

### <a name="create-your-adf"></a>Vytvořte si ADF

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).    
2. V nabídce vlevo klikněte na **Nový**, klikněte na **Data + analýzy** a pak na **Data Factory**. 
   
   ![Nový -> Objekt pro vytváření dat](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
   
3. Na stránce **Nová továrna dat** zadejte **MyAzureSsisDataFactory** pro **Název**. 
      
   ![Stránka Nová datová továrna](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Název podavače ADF musí být globálně jedinečný. Pokud se zobrazí následující chyba, změňte název adf (např. yournameMyAzureSsisDataFactory) a zkuste jej vytvořit znovu. Informace o pravidlech pojmenování artefaktů ADF najdete v článku Data Factory – článek [Pravidla pojmenování.](naming-rules.md)
  
   `Data factory name MyAzureSsisDataFactory is not available`
      
4. Vyberte **předplatné Azure,** pod kterým chcete vytvořit apodad. 
5. V části **Skupina prostředků** proveďte jeden z následujících kroků:
     
   - Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků. 
   - Vyberte **Vytvořit nový**a zadejte název nové skupiny prostředků.   
         
   Další informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure.](../azure-resource-manager/management/overview.md)
   
6. V **popřípadě verze**vyberte **v2** .
7. V **části Umístění**vyberte z rozevíracího seznamu jedno z umístění podporovaných pro vytváření podavačů ADF.
8. Zaškrtněte **Připnout na řídicí panel**.     
9. Klikněte na **Vytvořit**.
10. Na řídicím panelu Azure se zobrazí následující dlaždice se stavem: **Nasazení data factory**. 

    ![nasazování dlaždice datové továrny](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
   
11. Po dokončení vytváření můžete zobrazit stránku ADF, jak je znázorněno níže.
   
    ![Domovská stránka objektu pro vytváření dat](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
   
12. Kliknutím na **Author & Monitor** spusťte ui./aplikaci ADF na samostatné kartě.

### <a name="create-your-pipelines"></a>Vytvoření kanálů

1. Na stránce **Začínáme** vyberte **Vytvořit kanál**. 

   ![Stránka Začínáme](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
   
2. V panelu nástrojů **Aktivity** rozbalte **obecnou** nabídku a přetáhněte & **přetáhněte webovou** aktivitu na povrch návrháře kanálu. Na kartě **Obecné** v okně vlastností aktivity změňte název aktivity na **startMyIR**. Přepněte na kartu **Nastavení** a proveďte následující akce.

    1. Pro **adresu URL**zadejte následující adresu URL rozhraní REST API, `{subscriptionId}` `{resourceGroupName}`která `{factoryName}`spouští službu Azure-SSIS IR a nahrazuje , , a `{integrationRuntimeName}` skutečné hodnoty pro infračervený přenos: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/start?api-version=2018-06-01` Případně můžete také zkopírovat & ID prostředku infračerveného zařízení z jeho monitorovací stránky v rozhraní ADF UI/app a nahradit tak následující část výše uvedené adresy URL:`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}`
    
       ![ID infračerveného prostředku SSIS systému ADF](./media/how-to-schedule-azure-ssis-integration-runtime/adf-ssis-ir-resource-id.png)
  
    2. V **popřípadě Metoda**vyberte **možnost POST**. 
    3. Do **části** `{"message":"Start my IR"}`Tělo zadejte . 
    4. V **části Ověřování**vyberte **msi,** chcete-li použít spravovanou identitu pro váš podavač ADF, další informace najdete v článku [Spravovaná identita pro datafactory.](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)
    5. Do **skupiny Zdroj**zadejte . `https://management.azure.com/`
    
       ![Plán webových aktivit služby ADF SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-schedule-ssis-ir.png)
  
3. Klonovat první kanál vytvořit druhý, změna názvu aktivity **stopMyIR** a nahrazení následující vlastnosti.

    1. Pro **adresu URL**zadejte následující adresu URL pro rozhraní REST `{subscriptionId}`API, která zastaví infračervený přenos Azure-SSIS a nahradí `{resourceGroupName}`, `{factoryName}`a `{integrationRuntimeName}` skutečné hodnoty infračerveného zařízení:`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/stop?api-version=2018-06-01`
    
    2. Do **části** `{"message":"Stop my IR"}`Tělo zadejte . 

4. Vytvořte třetí kanál, přetáhněte & **aktivitu spouštět balíček SSIS** z **panelu** nástrojů Aktivity na povrch návrháře kanálu a nakonfigurujte ji podle pokynů v [příkazech Vyvolat balíček SSIS pomocí spuštění aktivity balíčku SSIS v článku ADF.](how-to-invoke-ssis-package-ssis-activity.md)  Případně můžete místo toho použít **aktivitu uloženou proceduru** a nakonfigurovat ji podle pokynů v [příkazech vyvolat balíček SSIS pomocí aktivity uložené procedury v článku ADF.](how-to-invoke-ssis-package-stored-procedure-activity.md)  Dále zřetězení spustit balíček SSIS/uložené procedury aktivity mezi dvěma webovými aktivitami, které spuštění nebo zastavení infračerveného přenosu, podobně jako ty webové aktivity v první/druhé kanály.

   ![ADF webová aktivita na vyžádání SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-on-demand-ssis-ir.png)

5. Přiřaďte spravovanou identitu pro roli Přispěvatel a **ADF** k sobě, takže webové aktivity v jeho kanálech můžete volat rozhraní REST API pro spuštění nebo zastavení Azure-SSIS IRs zřízené v něm.  Na stránce ADF na webu Azure Portal klikněte na **Řízení přístupu (IAM),** klikněte na **+ Přidat přiřazení role**a potom v okně Přidat přiřazení **role** proveďte následující akce.

    1. V **části Role**vyberte **přispěvatele**. 
    2. **Chcete-li přiřadit přístup k**, vyberte **uživatele, skupinu nebo instanční objekt Služby Azure AD**. 
    3. V **pole Vybrat**vyhledejte název automatického podavače ADF a vyberte jej. 
    4. Klikněte na **Uložit**.
    
   ![Přiřazení role spravované identity adf](./media/how-to-schedule-azure-ssis-integration-runtime/adf-managed-identity-role-assignment.png)

6. Ověřte své podavač a všechna nastavení kanálu kliknutím na **ověřit vše/Ověřit** na panelu nástrojů factory/pipeline. Zavřete **výstup ověření továrny/kanálu** klepnutím na **>>** tlačítko.  

   ![Ověření kanálu](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-your-pipelines"></a>Testování spuštění kanálů

1. Na panelu nástrojů pro každý kanál vyberte **Testovat spuštění** a v dolním podokně vyberte **Okno Výstup.** 

   ![Zkušební běh](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
    
2. Chcete-li otestovat třetí kanál, spusťte SQL Server Management Studio (SSMS). V okně **Připojit k serveru** proveďte následující akce. 

    1. Do **pole Název serveru**zadejte ** &lt;&gt;název serveru Azure SQL Database .database.windows.net**.
    2. Vyberte **možnosti >>**.
    3. V **popřípadě Připojit k databázi**vyberte **položku SSISDB**.
    4. Vyberte **Connect** (Připojit). 
    5. Rozbalte **katalogy integračních** -> služeb**SSISDB** -> složka -> **projekty** -> váš projekt SSIS -> **balíčky**. 
    6. Chcete-li spustit, klepněte pravým tlačítkem myši na zadaný balíček SSIS a vyberte možnost **Sestavy** -> **standardních sestav** -> **všech spuštění**. 
    7. Ověřte, zda byl spuštěn. 

   ![Ověření spuštění balíčku SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/verify-ssis-package-run.png)

### <a name="schedule-your-pipelines"></a>Plánování kanálů

Teď, když vaše kanály fungují podle očekávání, můžete vytvořit aktivační události pro jejich spuštění na zadaných kadenci. Podrobnosti o připojování aktivačních událostí s kanály naleznete [v tématu Aktivace kanálu v](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule) článku plánu.

1. Na panelu nástrojů kanálu vyberte **Aktivovat** a vyberte **Nový/Upravit**. 

   ![Aktivační událost -> Nové/Upravit](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)

2. V podokně **Přidat aktivační události** vyberte + **Nový**.

   ![Přidat aktivační události - nové](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)

3. V podokně **Nová aktivační událost** proveďte následující akce: 

    1. Do **pole Název**zadejte název aktivační události. V následujícím příkladu spustit **denně** je název aktivační události. 
    2. V **pole Typ**vyberte možnost **Plán**. 
    3. Do **položky Počáteční datum (UTC)** zadejte počáteční datum a čas v čase UTC. 
    4. V **části Opakování**zadejte kadenci aktivační události. V následujícím příkladu je **jednou Daily.** 
    5. V **části Konec**vyberte možnost Žádný **konec** nebo zadejte koncové datum a čas po výběru **možnosti Datum**. 
    6. Výběrem **možnosti Aktivována** aktivujete aktivační událost ihned po publikování celého nastavení ADF. 
    7. Vyberte **Další**.

   ![Aktivační událost -> Nové/Upravit](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
    
4. Na stránce **Parametry spuštění aktivační události** zkontrolujte všechna upozornění a vyberte **Dokončit**. 
5. Publikujte celé nastavení ADF výběrem **možnosti Publikovat vše** v panelu nástrojů výroby. 

   ![Publikovat vše](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)

### <a name="monitor-your-pipelines-and-triggers-in-azure-portal"></a>Monitorování kanálů a aktivačních událostí na webu Azure Portal

1. Chcete-li sledovat spuštění aktivačních událostí a spuštění kanálu, použijte kartu **Monitor** na levé straně ui/aplikace ADF. Podrobné kroky najdete v článku [sledování kanálu.](quickstart-create-data-factory-portal.md#monitor-the-pipeline)

   ![Spuštění kanálu](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)

2. Chcete-li zobrazit spuštění aktivity přidružené ke spuštění kanálu, vyberte první odkaz (**Zobrazit spuštění aktivity**) ve **sloupci Akce.** Pro třetí kanál uvidíte tři spuštění aktivity, jeden pro každou zřetězenou aktivitu v kanálu (webová aktivita pro spuštění infračerveného přenosu, aktivita uložené procedury pro spuštění balíčku a webová aktivita k zastavení infračerveného přenosu). Chcete-li znovu zobrazit spuštění kanálu, vyberte nahoře propojení **Potrubí.**

   ![Spuštění aktivit](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)

3. Chcete-li zobrazit spuštění aktivační události, vyberte **spuštění aktivační události** v rozevíracím seznamu v části Spuštění **kanálu** v horní části. 

   ![Spuštění aktivačních událostí](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

### <a name="monitor-your-pipelines-and-triggers-with-powershell"></a>Sledování kanálů a aktivačních událostí pomocí PowerShellu

Skripty, jako jsou následující příklady, slouží ke sledování kanálů a aktivačních událostí.

1. Získejte stav spuštění kanálu.

   ```powershell
   Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $myPipelineRun
   ```

2. Získejte informace o aktivační události.

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name  "myTrigger"
   ```

3. Získejte stav spuštění aktivační události.

   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "myTrigger" -TriggerRunStartedAfter "2018-07-15" -TriggerRunStartedBefore "2018-07-16"
   ```

## <a name="create-and-schedule-azure-automation-runbook-that-startsstops-azure-ssis-ir"></a>Vytvoření a naplánování runbooku Azure Automation, který spustí nebo zastaví Azure-SSIS IR

V této části se naučíte vytvářet runbook Azure Automation, který spouští skript prostředí PowerShell a spouští nebo zastavuje infračervený přenos Azure-SSIS podle plánu.  To je užitečné, pokud chcete spustit další skripty před / po spuštění / zastavení infračerveného zařízení pro před-post-zpracování.

### <a name="create-your-azure-automation-account"></a>Vytvoření účtu Azure Automation

Pokud ještě nemáte účet Azure Automation, vytvořte ho podle pokynů v tomto kroku. Podrobné kroky najdete v článku [Vytvoření účtu Azure Automation.](../automation/automation-quickstart-create-account.md) V rámci tohoto kroku vytvoříte účet **Azure Run As** (instanční objekt ve službě Azure Active Directory) a přiřadíte mu roli **přispěvatele** ve vašem předplatném Azure. Ujistěte se, že se jedná o stejné předplatné, které obsahuje váš ADF s Azure SSIS IR. Azure Automation použije tento účet k ověření ve Správci prostředků Azure a k provozu s vašimi prostředky. 

1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. V současné době je uživatelské rozhraní ADF/aplikace podporováno pouze ve webových prohlížečích Microsoft Edge a Google Chrome.
2. Přihlaste se k [portálu Azure](https://portal.azure.com/).    
3. V levé nabídce vyberte **Nový,** vyberte **Sledování + Správa**a vyberte **Automatizace**. 

   ![Nové monitorování > + automatizace správy ->](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
    
2. V podokně **Přidat účet automatizace** proveďte následující akce.

    1. Do **pole Název**zadejte název účtu Azure Automation. 
    2. V **části Předplatné**vyberte předplatné, které má váš podavač ADF pomocí služby Azure-SSIS IR. 
    3. Ve **skupině Prostředků**vyberte vytvořit **nový** a vytvořte novou skupinu prostředků nebo Vyberte existující skupinu **prostředků.** 
    4. V **aplikaci Umístění**vyberte umístění pro svůj účet Azure Automation. 
    5. Potvrďte **vytvořit účet Azure Run As** jako **Ano**. Instanční objekt se vytvoří ve vašem Azure Active Directory a přiřadí roli **přispěvatele** ve vašem předplatném Azure.
    6. Vyberte **Připnout na řídicí panel,** abyste ho trvale zobrazili na řídicím panelu Azure. 
    7. Vyberte **Vytvořit**. 

   ![Nové monitorování > + automatizace správy ->](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
   
3. Stav nasazení vašeho účtu Azure Automation se zobrazí na řídicím panelu Azure a oznámení. 
    
   ![Nasazení automatizace](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
    
4. Po úspěšném vytvoření se zobrazí domovská stránka vašeho účtu Azure Automation. 

   ![Domovská stránka automatizace](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-adf-modules"></a>Import modulů ADF

1. V levé nabídce vyberte **Moduly** v části **SDÍLENÉ ZDROJE** a ověřte, zda máte v seznamu modulů **Az.DataFactory** + **Az.Profile.**

   ![Ověření požadovaných modulů](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  Pokud **az.DataFactory**nemáte , přejděte do galerie PowerShell u [modulu Az.DataFactory](https://www.powershellgallery.com/packages/Az.DataFactory/), vyberte **Nasadit do Azure Automation**, vyberte účet Azure Automation a pak vyberte **OK**. Vraťte se k zobrazení **modulů** v části **SDÍLENÉ ZDROJE** v levé nabídce a počkejte, až se zobrazí **stav** modulu **Az.DataFactory** změněn na **K dispozici**.

    ![Ověření modulu Data Factory](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  Pokud nemáte **Az.Profile**, přejděte do modulu PowerShell Gallery for [Az.Profile](https://www.powershellgallery.com/packages/Az.profile/), vyberte **Deploy to Azure Automation**, vyberte účet Azure Automation a pak vyberte **OK**. Vraťte se k zobrazení **modulů** v části **SDÍLENÉ ZDROJE** v levé nabídce a počkejte, až se zobrazí **stav** modulu **Az.Profile** změněn na **K dispozici**.

    ![Ověření modulu Profil](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-your-powershell-runbook"></a>Vytvoření runbooku Prostředí PowerShell

Následující část obsahuje postup pro vytvoření runbooku prostředí PowerShell. Skript přidružený k runbooku buď spustí nebo zastaví Azure-SSIS IR na základě příkazu, který zadáte pro parametr **OPERATION.** Tato část neposkytuje úplné podrobnosti pro vytvoření runbooku. Další informace najdete v tématu Vytvoření článku [v runbooku.](../automation/automation-quickstart-create-runbook.md)

1. Přepněte na kartu **Runbook** a vyberte **+ Přidat runbook** z panelu nástrojů. 

   ![Tlačítko Přidat runbook](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
   
2. Vyberte **Vytvořit novou runbook** a proveďte následující akce: 

    1. Do **pole Název**zadejte příkaz **StartStopAzureSsisRuntime**.
    2. V **popřípadě Typ runbooku**vyberte **PowerShell**.
    3. Vyberte **Vytvořit**.
    
   ![Tlačítko Přidat runbook](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
   
3. Zkopírujte & vložte následující skript prostředí PowerShell do okna skriptu runbooku. Uložte a publikujte sadu Runbook pomocí tlačítek **Uložit** a **Publikovat** na panelu nástrojů. 

    ```powershell
    Param
    (
          [Parameter (Mandatory= $true)]
          [String] $ResourceGroupName,
    
          [Parameter (Mandatory= $true)]
          [String] $DataFactoryName,
    
          [Parameter (Mandatory= $true)]
          [String] $AzureSSISName,
    
          [Parameter (Mandatory= $true)]
          [String] $Operation
    )
    
    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
    
        "Logging in to Azure..."
        Connect-AzAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
        if (!$servicePrincipalConnection)
        {
            $ErrorMessage = "Connection $connectionName not found."
            throw $ErrorMessage
        } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
        }
    }
    
    if($Operation -eq "START" -or $operation -eq "start")
    {
        "##### Starting #####"
        Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $AzureSSISName -Force
    }
    elseif($Operation -eq "STOP" -or $operation -eq "stop")
    {
        "##### Stopping #####"
        Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    }  
    "##### Completed #####"    
    ```

   ![Úprava runbooku PowerShellu](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
    
4. Otestujte sadu Runbook výběrem tlačítka **Start** na panelu nástrojů. 

   ![Tlačítko Spustit runbook](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
    
5. V podokně **Spustit runbook** proveďte následující akce: 

    1. V **části NÁZEV SKUPINY PROSTŘEDKŮ**zadejte název skupiny prostředků, která má váš podavač ADF pomocí služby Azure-SSIS Ir. 
    2. V **části NÁZEV DATA FACTORY**zadejte název automatického podavače ADF pomocí zařízení Azure-SSIS IR. 
    3. V **části AZURESSISNAME**zadejte název infračerveného serveru Azure-SSIS. 
    4. Pro **operaci**zadejte **START**. 
    5. Vyberte **OK**.  

   ![Spustit okno runbooku](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
   
6. V okně úlohy vyberte **Výstupní** dlaždice. Ve výstupním okně počkejte na zprávu **##### Dokončeno #####** po zobrazení **##### Spuštění #####**. Spuštění azure-SSIS IR trvá přibližně 20 minut. Zavřete okno **úlohy** a vraťte se do okna **Runbooku.**

   ![Azure SSIS IR – spuštěno](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
    
7. Opakujte předchozí dva kroky pomocí **STOP** jako hodnota pro **OPERACI**. Znovu spusťte sadu Runbook výběrem tlačítka **Start** na panelu nástrojů. Zadejte názvy skupin prostředků, podavače a souborů ADF a indičních zařízení Azure-SSIS. Pro **operaci**zadejte **STOP**. Ve výstupním okně počkejte na zprávu **##### Dokončeno #####** po zobrazení **##### Zastavení #####**. Zastavení Azure-SSIS IR netrvá tak dlouho, jak ji spuštění. Zavřete okno **úlohy** a vraťte se do okna **Runbooku.**

8. Runbook můžete také spustit pomocí webového háku, který lze vytvořit výběrem položky nabídky **Webhooks** nebo podle plánu, který lze vytvořit výběrem položky nabídky **Plány,** jak je uvedeno níže.  

## <a name="create-schedules-for-your-runbook-to-startstop-azure-ssis-ir"></a>Vytvoření plánů pro váš runbook pro spuštění a zastavení azure-SSIS IR

V předchozí části jste vytvořili runbook Azure Automation, který můžete spustit nebo zastavit Azure-SSIS IR. V této části vytvoříte dva plány pro váš runbook. Při konfiguraci prvního plánu zadáte **spustit** pro **operaci**. Podobně při konfiguraci druhého zadáte **STOP** pro **OPERACI**. Podrobné kroky k vytvoření plánů najdete v článku [Vytvoření plánu.](../automation/shared-resources/schedules.md#creating-a-schedule)

1. V okně **sady Runbook** vyberte **Plány**a vyberte **+ Přidat plán** na panelu nástrojů. 

   ![Azure SSIS IR – spuštěno](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
   
2. V podokně **Spustit seznam plán** proveďte následující akce: 

    1. Vyberte **Propojit plán se souborem Runbook**. 
    2. Vyberte **možnost Vytvořit nový plán**.
    3. V podokně **Nový plán** zadejte **spustit infračervený přenos denně** pro **název**. 
    4. Do **spouští**, zadejte čas, který je několik minut po aktuálním čase. 
    5. V **popřípadě opakování**vyberte **možnost Opakovat**. 
    6. Do **pole Opakovat každý**, zadejte **hodnotu 1** a vyberte možnost **Den**. 
    7. Vyberte **Vytvořit**. 

   ![Plán spuštění služby Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
    
3. Přepněte na **kartu Parametry a spusťte nastavení.** Zadejte názvy skupin prostředků, podavače a souborů Azure-SSIS. V **popřípadě OPERACE**zadejte **START** a vyberte **OK**. Výběrem **možnosti OK** znovu zobrazíte plán na stránce **Plány** v aplikaci Runbook. 

   ![Plán pro zírání na azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
    
4. Opakováním předchozích dvou kroků vytvořte plán s názvem **Zastavit infračervený přenos denně**. Zadejte čas, který je nejméně 30 minut po čase, který jste zadali pro **spuštění infračerveného denního** plánu. V **režimu OPERACE**zadejte **příkaz STOP** a vyberte **ok**. Výběrem **možnosti OK** znovu zobrazíte plán na stránce **Plány** v aplikaci Runbook. 

5. V okně **Runbook** vyberte **úlohy** v levé nabídce. Měli byste vidět úlohy vytvořené vaše plány v určených časech a jejich stavy. Můžete zobrazit podrobnosti o úloze, jako je například jeho výstup, podobně jako to, co jste viděli po otestování sady Runbook. 

   ![Plán pro zírání na azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
    
6. Po dokončení testování zakažte plány jejich úpravou. V levé nabídce vyberte **Možnost Plány,** vyberte **Spustit infračervený přenos denně/Zastavit infračervený přenos denně**a vyberte **ne** pro **povoleno**. 

## <a name="next-steps"></a>Další kroky
Podívejte se na následující příspěvek na blogu:
-   [Modernizujte a rozšiřte své pracovní postupy ETL/ELT o aktivity SSIS v kanálech ADF](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)

Projděte si následující články v dokumentaci ke službě SSIS: 

- [Nasazení, spuštění a monitorování balíčku SSIS v Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Připojení ke katalogu SSIS v Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Plánování spouštění balíčku v Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Připojení k místním zdrojům dat s využitím ověřování systému Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)
