---
title: Postup plánování Azure-SSIS Integration Runtime
description: Tento článek popisuje, jak naplánovat spouštění a zastavování Azure-SSIS Integration Runtime pomocí Azure Data Factory.
ms.service: data-factory
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/09/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 40c3b8ab228c93fd4c681281d89d16f88ddf30f1
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384363"
---
# <a name="how-to-start-and-stop-azure-ssis-integration-runtime-on-a-schedule"></a>Plánované spouštění a zastavování prostředí Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek popisuje, jak naplánovat spouštění a zastavování Azure-SSIS Integration Runtime (IR) pomocí Azure Data Factory (ADF). Azure-SSIS IR je výpočetní prostředek ADF vyhrazený pro spouštění balíčků služba SSIS (SQL Server Integration Services) (SSIS). Spuštění Azure-SSIS IR má k sobě přiřazené náklady. Proto obvykle budete chtít spustit IR pouze v případě, že budete potřebovat spouštět balíčky SSIS v Azure a zastavit technologii IR, když ji ještě nepotřebujete. K [ručnímu spuštění nebo zastavení prostředí IR](manage-azure-ssis-integration-runtime.md)můžete použít uživatelské rozhraní (UI) ADF (/App) nebo Azure PowerShell.

Alternativně můžete vytvořit aktivity webu v kanálech ADF a spustit nebo zastavit infračervený vývoj podle plánu, například začít od ráno před spuštěním denních úloh ETL a Zastavte ho po skončení odpoledne.  Můžete také zřetězit aktivitu spustit SSIS balíčku mezi dvěma aktivitami webu, které spouštějí a zastavují INFRAČERVENé prostředí, takže se IR na vyžádání spustí nebo zastaví, a to v čase před/po provedení balíčku. Další informace o aktivitě SSIS balíčku najdete v článku [spuštění balíčku SSIS pomocí aktivity spustit balíček SSIS v článku kanál ADF](how-to-invoke-ssis-package-ssis-activity.md) .

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky
Pokud jste už Azure-SSIS IR ještě nezřídili, zřiďte ho podle pokynů v tomto [kurzu](./tutorial-deploy-ssis-packages-azure.md). 

## <a name="create-and-schedule-adf-pipelines-that-start-and-or-stop-azure-ssis-ir"></a>Vytvářejte a naplánujte kanály ADF, které začínají a nebo zastavují Azure-SSIS IR
V této části se dozvíte, jak používat aktivity webu v kanálech ADF ke spuštění nebo zastavení Azure-SSIS IR v plánu nebo spuštění & zastavení na vyžádání. Průvodce vám umožní vytvořit tři kanály: 

1. První kanál obsahuje aktivitu webu, která spouští Azure-SSIS IR. 
2. Druhý kanál obsahuje aktivitu webu, která zastaví vaše Azure-SSIS IR.
3. Třetí kanál obsahuje aktivitu spustit SSIS balíčku zřetězenou mezi dvěma webovými aktivitami, které spouští/zastavují vaše Azure-SSIS IR. 

Po vytvoření a otestování těchto kanálů můžete vytvořit aktivační událost plánovače a přidružit ji k jakémukoli kanálu. Aktivační událost plánovače definuje plán pro spuštění přidruženého kanálu. 

Můžete například vytvořit dvě triggery, přičemž první z nich je naplánována na každý den v rozmezí od 6 do 9:00 a přidružená k prvnímu kanálu, zatímco druhý z nich má naplánováno každodenní spuštění v 6 ODP a přidruženo k druhému kanálu.  Tímto způsobem máte po dobu od 6 do 6 hodin každý den v době, kdy je spuštěný IR, připravený k provedení denních úloh ETL.  

Pokud vytvoříte třetí Trigger, u kterého je naplánováno každodenní spuštění na půlnoci a přidruženo k třetímu kanálu, tento kanál se spustí každý den o půlnoci, který se před provedením balíčku zahájí, a následně se spustí a okamžitě se zastaví, a to tak, aby váš IR neběžel idly.

### <a name="create-your-adf"></a>Vytvoření ADF

1. Přihlaste se k [Azure Portal](https://portal.azure.com/).    
2. V nabídce vlevo klikněte na **Nový**, klikněte na **Data + analýzy** a pak na **Data Factory**. 
   
   ![Nový -> Objekt pro vytváření dat](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
   
3. Na stránce **Nová datová továrna** jako **název** zadejte **MyAzureSsisDataFactory** . 
      
   ![Stránka Nová datová továrna](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Název vašeho ADF musí být globálně jedinečný. Pokud se zobrazí následující chyba, změňte název ADF (např. yournameMyAzureSsisDataFactory) a zkuste ho vytvořit znovu. Další informace o pravidlech pojmenovávání pro artefakty ADF najdete v článku [pravidla pro Pojmenovávání Data Factory](naming-rules.md) .
  
   `Data factory name MyAzureSsisDataFactory is not available`
      
4. Vyberte své **předplatné** Azure, ve kterém chcete vytvořit ADF. 
5. V části **Skupina prostředků** proveďte jeden z následujících kroků:
     
   - Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků. 
   - Vyberte **vytvořit novou** a zadejte název nové skupiny prostředků.   
         
   Další informace o skupinách prostředků najdete v článku [použití skupin prostředků ke správě vašich prostředků Azure](../azure-resource-manager/management/overview.md) .
   
6. V případě **verze** vyberte **v2** .
7. V poli **umístění** vyberte jedno z umístění podporovaného pro vytvoření ADF z rozevíracího seznamu.
8. Zaškrtněte **Připnout na řídicí panel**.     
9. Klikněte na **Vytvořit**.
10. Na řídicím panelu Azure se zobrazí následující dlaždice se stavem: **nasazení Data Factory**. 

    ![nasazování dlaždice datové továrny](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
   
11. Po vytvoření se vám zobrazí stránka ADF, jak je vidět níže.
   
    ![Domovská stránka objektu pro vytváření dat](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
   
12. Klikněte na tlačítko **autor & monitorovat** a spusťte na samostatné kartě uživatelské rozhraní nebo aplikaci ADF.

### <a name="create-your-pipelines"></a>Vytváření kanálů

1. Na stránce **Začínáme** vyberte **vytvořit kanál**. 

   ![Stránka Začínáme](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
   
2. Na panelu nástrojů **aktivity** rozbalte nabídku **obecné** a přetáhněte & přetáhněte aktivitu **webu** na plochu návrháře kanálu. Na kartě **Obecné** v okně vlastnosti aktivity změňte název aktivity na **startMyIR**. Přepněte na kartu **Nastavení** a proveďte následující akce.

    1. Do pole **Adresa URL** zadejte následující adresu URL pro REST API, která začíná Azure-SSIS IR, nahrazuje `{subscriptionId}` , `{resourceGroupName}` , `{factoryName}` a `{integrationRuntimeName}` skutečnými hodnotami pro infračervený zápis: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/start?api-version=2018-06-01` případně můžete také zkopírovat & ID prostředku IR ze své stránky monitorování v uživatelském rozhraní ADF nebo aplikaci nahradit následující část výše uvedené adresy URL: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}`
    
       ![ID prostředku IR SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/adf-ssis-ir-resource-id.png)
  
    2. V případě **metody** vyberte **post**. 
    3. V části **text** zadejte `{"message":"Start my IR"}` . 
    4. Pro **ověřování** vyberte **MSI** , aby se pro něj používala spravovaná identita. Další informace najdete v článku [spravovaná identita pro Data Factory](./data-factory-service-identity.md) .
    5. V případě **prostředku** zadejte `https://management.azure.com/` .
    
       ![Plán webové aktivity ADF SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-schedule-ssis-ir.png)
  
3. Naklonujte první kanál tak, aby se vytvořil druhý, a to tak, že změníte název aktivity na **stopMyIR** a nahradíte následující vlastnosti.

    1. Do pole **Adresa URL** zadejte následující adresu URL pro REST API, která zastaví Azure-SSIS IR, nahradí `{subscriptionId}` , a `{resourceGroupName}` `{factoryName}` a `{integrationRuntimeName}` skutečnými hodnotami pro váš infračervený přenos: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/stop?api-version=2018-06-01`
    
    2. V části **text** zadejte `{"message":"Stop my IR"}` . 

4. Vytvořte třetí kanál, přetáhněte & aktivitu **spustit balíček SSIS** z panelu nástrojů **aktivity** na plochu návrháře kanálu a nakonfigurujte ji podle pokynů v tématu [vyvolání balíčku SSIS pomocí aktivity spustit balíček SSIS v článku ADF](how-to-invoke-ssis-package-ssis-activity.md) .  Alternativně můžete místo toho použít aktivitu **uložené procedury** a nakonfigurovat ji podle pokynů v tématu [vyvolání balíčku SSIS pomocí aktivity uložená procedura v rámci ADF](how-to-invoke-ssis-package-stored-procedure-activity.md) .  Dále řetězte aktivitu spustit SSIS balíček/uloženou proceduru mezi dvěma webovými aktivitami, které spouští nebo zastavuje infračervený přenos, podobně jako tyto webové aktivity v kanálech prvních/sekund.

   ![Webové aktivity ADF na vyžádání SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-on-demand-ssis-ir.png)

5. Přiřaďte spravovanou identitu pro samoobslužnou úlohu ADF roli **přispěvatele** sám sobě, takže webové aktivity v jejich kanálech můžou volat REST API pro spuštění nebo zastavení Azure-SSIS finančního úřadu.  Na stránce ADF v Azure Portal klikněte na **řízení přístupu (IAM)**, klikněte na **+ Přidat přiřazení role** a potom v okně **Přidat přiřazení role** proveďte následující akce.

    1. Jako **role** vyberte **Přispěvatel**. 
    2. V případě **přiřazení přístupu k** vyberte možnost **uživatel, skupina nebo instanční objekt služby Azure AD**. 
    3. Pro **možnost vybrat** vyhledejte název ADF a vyberte ho. 
    4. Klikněte na **Uložit**.
    
   ![Přiřazení role identity spravovaného ADF](./media/how-to-schedule-azure-ssis-integration-runtime/adf-managed-identity-role-assignment.png)

6. Kliknutím na **ověřit vše/ověřit** na panelu nástrojů továrny nebo kanálu ověřte nastavení ADF a všechna nastavení kanálu. Kliknutím na tlačítko zavřete **výstup ověřování továrny/kanálu** **>>** .  

   ![Ověření kanálu](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-your-pipelines"></a>Testovací běh vašich kanálů

1. Pro každý kanál vyberte **testovací běh** na panelu nástrojů a v dolním podokně se podívejte na okno **výstup** . 

   ![Testovací běh](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
    
2. Pokud chcete otestovat třetí kanál, spusťte SQL Server Management Studio (SSMS). V okně **připojit k serveru** proveďte následující akce. 

    1. Jako **název serveru** zadejte **&lt; název vašeho serveru &gt; . Database.Windows.NET**.
    2. Vyberte **možnosti >>**.
    3. Pro **připojení k databázi** vyberte **SSISDB**.
    4. Vyberte **Connect** (Připojit). 
    5. Rozbalte položku **katalogy integračních služeb**  ->  **SSISDB** -> vaše složka – > **projekty** – > **balíčky**> projektu SSIS. 
    6. Klikněte pravým tlačítkem na zadaný SSIS balíček, který se spustí, a vyberte **sestavy**  ->  **standardní sestavy**  ->  **všechna spuštění**. 
    7. Ověřte, zda bylo spuštěno. 

   ![Ověřit běh balíčku SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/verify-ssis-package-run.png)

### <a name="schedule-your-pipelines"></a>Plánování kanálů

Teď, když vaše kanály pracují podle očekávání, můžete vytvořit triggery, které je budou spouštět v zadaných tempem. Podrobnosti o přidružení triggerů k kanálům najdete v článku [Aktivace kanálu podle plánu](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule) .

1. Na panelu nástrojů kanálu vyberte **aktivační událost** a vyberte **Nová/upravit**. 

   ![Snímek obrazovky, který zvýrazní možnost nabídky pro nové/úpravy > triggeru](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)

2. V podokně **Přidat aktivační události** vyberte **+ Nová**.

   ![Přidat triggery – nové](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)

3. V podokně **Nová aktivační událost** proveďte následující akce: 

    1. Do pole **název** zadejte název aktivační události. V následujícím příkladu je **příkaz run Daily** názvem triggeru. 
    2. Jako **typ** vyberte **plán**. 
    3. Do **počátečního data (UTC)** zadejte počáteční datum a čas ve standardu UTC. 
    4. Jako **opakování** zadejte tempo pro aktivační událost. V následujícím příkladu je jednou **denně** . 
    5. Pro možnost **End** vyberte možnost **bez ukončení** nebo zadejte koncové datum a čas po výběru **data**. 
    6. Vyberte **aktivovat** , pokud chcete aktivovat Trigger hned po publikování celého nastavení ADF. 
    7. Vyberte **Další**.

   ![Trigger-> nový/upravit](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
    
4. Na stránce **parametry spuštění aktivační události** Zkontrolujte všechna upozornění a vyberte **Dokončit**. 
5. Všechna nastavení ADF publikujte výběrem možnosti **publikovat vše** na panelu nástrojů Factory. 

   ![Publikovat vše](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)

### <a name="monitor-your-pipelines-and-triggers-in-azure-portal"></a>Monitorujte své kanály a triggery v Azure Portal

1. Pokud chcete monitorovat spuštění aktivačních událostí a spuštění kanálů, použijte kartu **monitorování** na levé straně uživatelského rozhraní a aplikace ADF. Podrobný postup najdete v článku [monitorování kanálu](quickstart-create-data-factory-portal.md#monitor-the-pipeline) .

   ![Spuštění kanálu](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)

2. Pokud chcete zobrazit spuštění aktivit související se spuštěním kanálu, vyberte první odkaz (**zobrazení spuštění aktivit**) ve sloupci **Actions (akce** ). U třetího kanálu se zobrazí tři spuštění aktivit, jedno pro každou zřetězenou aktivitu v kanálu (aktivita webu, která spustí aktivitu IR, uloženou proceduru pro spuštění balíčku a aktivitu webu pro zastavení IR). Pokud chcete znovu zobrazit spuštění kanálu, vyberte odkaz **kanály** v horní části.

   ![Spuštění aktivit](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)

3. Pokud chcete zobrazit spuštění triggeru, vyberte v rozevíracím seznamu Spustit **aktivační událost** v části **spuštění kanálu** v horní části. 

   ![Spuštění aktivačních událostí](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

### <a name="monitor-your-pipelines-and-triggers-with-powershell"></a>Monitorování kanálů a triggerů pomocí PowerShellu

Pomocí skriptů, jako jsou následující příklady, Sledujte své kanály a triggery.

1. Získejte stav spuštění kanálu.

   ```powershell
   Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $myPipelineRun
   ```

2. Získat informace o triggeru

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name  "myTrigger"
   ```

3. Získejte stav spuštění triggeru.

   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "myTrigger" -TriggerRunStartedAfter "2018-07-15" -TriggerRunStartedBefore "2018-07-16"
   ```

## <a name="create-and-schedule-azure-automation-runbook-that-startsstops-azure-ssis-ir"></a>Vytvoření a naplánování runbooku Azure Automation, který spustí nebo zastaví Azure-SSIS IR

V této části se naučíte vytvořit Azure Automation sadu Runbook, která spustí skript prostředí PowerShell, spuštění nebo zastavení Azure-SSIS IR podle plánu.  To je užitečné v případě, že chcete před spuštěním/po spuštění nebo zastavením IR spustit další skripty pro předběžné nebo následné zpracování.

### <a name="create-your-azure-automation-account"></a>Vytvoření účtu Azure Automation

Pokud účet Azure Automation ještě nemáte, vytvořte ho podle pokynů v tomto kroku. Podrobný postup najdete v článku [Vytvoření účtu Azure Automation](../automation/automation-quickstart-create-account.md) . V rámci tohoto kroku vytvoříte účet **Spustit v Azure jako** (instanční objekt ve vašem Azure Active Directory) a přiřadíte mu roli **přispěvatele** v předplatném Azure. Ujistěte se, že se jedná o stejné předplatné, které obsahuje váš ADF s Azure SSIS IR. Azure Automation tento účet použije k ověření, aby Azure Resource Manager a pracoval na svých prostředcích. 

1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. V současné době se uživatelské rozhraní a aplikace ADF podporují jenom ve webových prohlížečích Microsoft Edge a Google Chrome.
2. Přihlaste se k [Azure Portal](https://portal.azure.com/).    
3. V nabídce vlevo vyberte **Nový** , vyberte **monitorování a Správa** a pak vyberte **Automation**. 

   ![Snímek obrazovky, který zvýrazní možnost Monitorování a správa > Automation.](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
    
2. V podokně **Přidat účet Automation** proveďte následující akce.

    1. Jako **název** zadejte název účtu Azure Automation. 
    2. V části **předplatné** vyberte předplatné, které má váš ADF s Azure-SSIS IR. 
    3. V případě **skupiny prostředků** vyberte **vytvořit novou** a vytvořte novou skupinu prostředků, nebo **použijte existující** a vyberte existující. 
    4. Jako **umístění** vyberte umístění pro váš účet Azure Automation. 
    5. Potvrďte volbu **vytvořit účet Spustit jako pro Azure** jako **Ano**. V Azure Active Directory se vytvoří instanční objekt a v předplatném Azure se přiřadí role **Přispěvatel** .
    6. Vyberte **Připnout na řídicí panel** , abyste ho mohli trvale zobrazit na řídicím panelu Azure. 
    7. Vyberte **Vytvořit**. 

   ![Automatizace > Monitorování a správa >](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
   
3. Na řídicím panelu Azure a oznámení se zobrazí stav nasazení účtu Azure Automation. 
    
   ![Nasazení automatizace](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
    
4. Po úspěšném vytvoření účtu Azure Automation se zobrazí jeho Domovská stránka. 

   ![Domovská stránka automatizace](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-adf-modules"></a>Importovat moduly ADF

1. V nabídce vlevo vyberte v části **sdílené prostředky** možnost **moduly** a ověřte, jestli máte v seznamu modulů **AZ. DataFactory**  +  **AZ. Profile** .

   ![Ověřte požadované moduly.](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  Pokud nemáte **AZ. DataFactory**, vyhledejte modul Galerie prostředí PowerShell pro [AZ. DataFactory](https://www.powershellgallery.com/packages/Az.DataFactory/), vyberte **nasadit do Azure Automation**, vyberte účet Azure Automation a pak vyberte **OK**. V levé nabídce v části zobrazit **moduly v modulech** **sdílené prostředky** a počkejte, dokud neuvidíte **stav** typu **AZ. DataFactory** , který je **k dispozici**.

    ![Ověření modulu Data Factory](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  Pokud nepoužíváte **AZ. Profile**, v modulu Galerie prostředí PowerShell pro [AZ. Profile](https://www.powershellgallery.com/packages/Az.profile/)vyberte **nasadit a Azure Automation**, vyberte účet Azure Automation a pak vyberte **OK**. V levé nabídce přejděte zpět do části zobrazit **moduly** v části **sdílené prostředky** a počkejte, dokud neuvidíte **stav** modulu **AZ. Profile** , který se změnil na **k dispozici**.

    ![Ověření modulu Profile](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-your-powershell-runbook"></a>Vytvoření Runbooku PowerShellu

V následující části najdete postup vytvoření Runbooku PowerShellu. Skript přidružený k sadě Runbook spustí nebo zastaví Azure-SSIS IR na základě příkazu, který zadáte pro parametr **operace** . V této části nejsou uvedeny úplné podrobnosti o vytvoření sady Runbook. Další informace najdete v článku [Vytvoření Runbooku](../automation/automation-quickstart-create-runbook.md) .

1. Přepněte na kartu **Runbooky** a na panelu nástrojů vyberte **+ Přidat Runbook** . 

   ![Snímek obrazovky, který zvýrazní tlačítko + Přidat Runbook](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
   
2. Vyberte **vytvořit novou sadu Runbook** a proveďte následující akce: 

    1. Jako **název** zadejte **StartStopAzureSsisRuntime**.
    2. Jako **typ Runbooku** vyberte **PowerShell**.
    3. Vyberte **Vytvořit**.
    
   ![Přidat Runbook – tlačítko](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
   
3. Zkopírujte & do okna skriptu Runbooku vložte následující skript PowerShellu. Uložte a publikujte Runbook pomocí tlačítek **Uložit** a **publikovat** na panelu nástrojů. 

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

   ![Upravit PowerShellový Runbook](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
    
4. Otestujte svůj Runbook tak, že na panelu nástrojů vyberete tlačítko **Start** . 

   ![Tlačítko spustit Runbook](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
    
5. V podokně **Spustit sadu Runbook** proveďte následující akce: 

    1. Do pole **název skupiny prostředků** zadejte název skupiny prostředků, která má váš ADF s Azure-SSIS IR. 
    2. Do pole **název datové továrny** zadejte název svého ADF s Azure-SSIS IR. 
    3. Pro **AZURESSISNAME** zadejte název Azure-SSIS IR. 
    4. V případě **operace** zadejte **Start**. 
    5. Vyberte **OK**.  

   ![Spustit okno Runbooku](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
   
6. V okně úlohy vyberte možnost **výstupní** dlaždice. V okně výstup po zobrazení # # # # # od # # **#**# # počkejte na zprávu **# # # # # dokončeno #** # # # #. Spuštění Azure-SSIS IR trvá přibližně 20 minut. Zavřete okno **úlohy** a vraťte se do okna **Runbooku** .

   ![Snímek obrazovky, který zvýrazní dlaždici výstup.](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
    
7. Opakujte předchozí dva kroky pomocí **stop** jako hodnoty pro **operaci**. Znovu spusťte Runbook tak, že na panelu nástrojů vyberete tlačítko **Start** . Zadejte svoji skupinu prostředků, ADF a názvy Azure-SSIS IR. V případě **operace** zadejte **stop**. V okně výstup počkejte na **dokončení zprávy # # #** # # po zobrazení **# # # # # zastavování # # # # #**. Zastavení Azure-SSIS IR netrvá tak dlouho, dokud ho nespustíte. Zavřete okno **úlohy** a vraťte se do okna **Runbooku** .

8. Můžete také aktivovat Runbook pomocí Webhooku, který se dá vytvořit výběrem položky nabídky **webhooks** nebo podle plánu, který se dá vytvořit, a to tak, že vyberete položku nabídky **plány** , jak je uvedeno níže.  

## <a name="create-schedules-for-your-runbook-to-startstop-azure-ssis-ir"></a>Vytvořit plány pro Runbook pro spuštění/zastavení Azure-SSIS IR

V předchozí části jste vytvořili Azure Automation sadu Runbook, která může buď spustit nebo zastavit Azure-SSIS IR. V této části vytvoříte dva plány pro sadu Runbook. Při konfiguraci prvního plánu zadáte **operaci** **začátek** pro. Podobně při konfiguraci druhého **nastavování operace zastavit** pro **operaci**. Podrobné pokyny k vytvoření plánů najdete v článku [o vytvoření plánu](../automation/shared-resources/schedules.md#create-a-schedule) .

1. V okně **Runbook** vyberte **plány** a na panelu nástrojů vyberte **+ Přidat plán** . 

   ![Azure SSIS IR – spuštěno](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
   
2. V podokně **naplánovat Runbook** proveďte následující akce: 

    1. Vyberte **připojit plán k sadě Runbook**. 
    2. Vyberte **vytvořit nový plán**.
    3. V podokně **Nový plán** zadejte pro **název** **začátek IR – den** . 
    4. V části **začátek** zadejte čas, který je několik minut po aktuálním čase. 
    5. V případě **opakování** vyberte možnost **opakující** se. 
    6. U **každého opakování** zadejte **1** a vyberte **den**. 
    7. Vyberte **Vytvořit**. 

   ![Plán pro Azure SSIS – začátek IR](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
    
3. Přepněte na **parametry a spusťte kartu nastavení** . Zadejte svoji skupinu prostředků, ADF a názvy Azure-SSIS IR. V případě **operace** zadejte **Start** a vyberte **OK**. Opětovným výběrem **OK** zobrazíte **stránku plán na plánování sady** Runbook. 

   ![Snímek obrazovky, který zvýrazní pole operace](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
    
4. Zopakováním předchozích dvou kroků vytvořte plán s názvem **zastavit IR denně**. Zadejte čas, který je nejméně 30 minut po zadané době pro **spuštění IR denního** plánu. V případě **operace** zadejte **stop** a vyberte **OK**. Opětovným výběrem **OK** zobrazíte **stránku plán na plánování sady** Runbook. 

5. V okně **Runbooku** v nabídce vlevo vyberte **úlohy** . Měli byste vidět úlohy vytvořené v plánech v zadaných časech a jejich stavech. Můžete si prohlédnout podrobnosti o úloze, jako je výstup, podobně jako na základě toho, co jste viděli po otestování Runbooku. 

   ![Plán pro hvězdování prostředí Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
    
6. Po dokončení testování zakažte své plány jejich úpravou. V nabídce vlevo vyberte **plány** , vyberte **Spustit IR denní/zastavit IR denně** a pro **povolené** vyberte **ne** . 

## <a name="next-steps"></a>Další kroky
Podívejte se na tento příspěvek na blogu:
-   [Modernizovat a rozšíří pracovní postupy ETL/ELT s aktivitami SSIS v kanálech ADF.](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)

Projděte si následující články v dokumentaci ke službě SSIS: 

- [Nasazení, spuštění a monitorování balíčku SSIS v Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Připojení ke katalogu SSIS v Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Plánování spouštění balíčku v Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Připojení k místním zdrojům dat s využitím ověřování systému Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)