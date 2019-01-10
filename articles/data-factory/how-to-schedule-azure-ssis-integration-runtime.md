---
title: Jak plánovat prostředí Azure-SSIS Integration Runtime | Dokumentace Microsoftu
description: Tento článek popisuje, jak naplánovat spuštění a zastavení prostředí Azure-SSIS Integration runtime pomocí služby Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 1/9/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 9f1ee309156a39078ffdfeed2c75d86476ac8b48
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54158648"
---
# <a name="how-to-start-and-stop-azure-ssis-integration-runtime-on-a-schedule"></a>Jak spustit a zastavit prostředí Azure-SSIS Integration Runtime podle plánu
Tento článek popisuje, jak naplánovat spuštění a zastavení prostředí Azure-SSIS Integration Runtime (IR) pomocí Azure Data Factory (ADF). Prostředí Azure-SSIS IR je ADF výpočetní prostředky, které jsou vyhrazené pro spouštění balíčků SQL Server Integration Services (SSIS). Spuštění prostředí Azure-SSIS IR s náklady s ním spojená. Proto je obvykle chcete spustit prostředí IR jenom v případě, že budete muset spouštění balíčků služby SSIS v Azure a zastavit prostředí IR, když ho už není nutné. Můžete použít ADF uživatelské rozhraní (UI) / aplikaci nebo prostředí Azure PowerShell potřeba [ručně spustit nebo zastavit prostředí IR](manage-azure-ssis-integration-runtime.md)).

Alternativně můžete vytvořit aktivity webu v ADF kanály, abyste mohli spustit/zastavit prostředí IR podle plánu, třeba to začne ráno před spouštěním vaše každodenní úlohy ETL a zastavuje odpoledne poté, co se dokončí.  Můžete také zřetězit aktivity spuštění balíčku služby SSIS mezi dvě aktivity webu spouštějících a zastavujících vaše reakcí na Incidenty, takže prostředí IR se spustit/zastavit na vyžádání za běhu před nebo za spouštění vašeho balíčku. Další informace o aktivitě spuštění balíčku služby SSIS najdete v tématu [spouštění balíčků služby SSIS pomocí aktivity spustit balíčků služby SSIS v kanálu ADF](how-to-invoke-ssis-package-ssis-activity.md) článku.

## <a name="prerequisites"></a>Požadavky
Pokud jste ještě už zřídili prostředí Azure-SSIS IR, zřízení podle pokynů v [kurzu](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="create-and-schedule-adf-pipelines-that-start-and-or-stop-azure-ssis-ir"></a>Vytvoření a naplánování ADF kanály, které spustit a zastavit prostředí Azure-SSIS IR
V této části se dozvíte, jak pomocí aktivity webu v kanálech ADF operací spustit/zastavit prostředí Azure-SSIS IR podle plánu nebo spuštění a zastavení na vyžádání. Můžeme vás povedou při vytvoření tří kanály: 

1. První kanál obsahuje aktivitu webu, který se spustí vaše prostředí Azure-SSIS IR. 
2. Druhý kanál obsahuje webová aktivita, která ukončí vaše prostředí Azure-SSIS IR.
3. Třetí kanál obsahuje aktivity spuštění balíčku služby SSIS zřetězené mezi dvě aktivity webu, které operací spustit/zastavit Azure-SSIS IR. 

Po vytvoření a testování tyto kanály, můžete vytvořit aktivační událost plánovače a přidružte jej k libovolného kanálu. Aktivační událost plánovače definuje plánu pro spuštěnou přidruženého kanálu. 

Například můžete vytvořit dvě aktivační události, první z nich je naplánované spouštění každý den v 6: 00 a související s první kanál, zatímco druhá je naplánováno spuštění každý den na 18: 00 a přidružené druhého kanálu.  Tímto způsobem mají období mezi 6: 00 do 18 hodin každý den, kdy prostředí IR běží, připravené ke spuštění vaše každodenní úlohy ETL.  

Pokud vytvoříte třetí aktivační událost, která je naplánované spouštění každý den o půlnoci a související se třetí kanálu, tento kanál se spustí každý den o půlnoci spouštění prostředí IR těsně před spuštěním balíčku, následně provádění vašeho balíčku a ihned zastavení prostředí IR bezprostředně po spuštění balíčku, takže prostředí IR nebude spuštěn idly.

### <a name="create-your-adf"></a>Vytvoření vašeho ADF

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).    
2. V nabídce vlevo klikněte na **Nový**, klikněte na **Data + analýzy** a pak na **Data Factory**. 
   
   ![Nový -> Datová továrna](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
   
3. V **nová datová továrna** zadejte **MyAzureSsisDataFactory** pro **název**. 
      
   ![Stránka Nová datová továrna](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Název vašeho ADF musí být globálně jedinečný. Pokud se zobrazí následující chyba, změňte název vaší ADF (například na Vaše_jméno_myazuressisdatafactory) a zkuste vytvořit znova. Zobrazit [služby Data Factory – pravidla pojmenování](naming-rules.md) článku se dozvíte o pravidla pojmenování artefaktů ADF.
  
   `Data factory name MyAzureSsisDataFactory is not available`
      
4. Vyberte si Azure **předplatné** pod kterou chcete vytvořit váš ADF. 
5. V části **Skupina prostředků** proveďte jeden z následujících kroků:
     
   - Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků. 
   - Vyberte **vytvořit nový**a zadejte název nové skupiny prostředků.   
         
   Další informace o skupinách prostředků najdete v tématu [použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md) článku.
   
6. Pro **verze**vyberte **V2** .
7. Pro **umístění**, vyberte jednu z podporovaných pro ADF vytváření z rozevíracího seznamu umístění.
8. Zaškrtněte **Připnout na řídicí panel**.     
9. Klikněte na možnost **Vytvořit**.
10. Na řídicím panelu Azure se zobrazí následující dlaždice se statusem: **Nasazování datové továrny**. 

   ![nasazování dlaždice datové továrny](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
   
11. Po vytvoření se zobrazí, uvidíte stránku ADF, jak je znázorněno níže.
   
   ![Domovská stránka datové továrny](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
   
12. Klikněte na tlačítko **vytvořit a monitorovat** pro spuštění na samostatné kartě uživatelské rozhraní ADF/aplikace.

### <a name="create-your-pipelines"></a>Vytváření kanálů

1. V **pusťme se do práce** stránce **vytvořit kanál**. 

   ![Stránka Začínáme](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
   
2. V **aktivity** sady nástrojů, rozbalte **Obecné** nabídky a přetáhnout **webové** aktivity na plochu návrháře kanálu. V **Obecné** kartě okna vlastnosti aktivity, změňte název aktivity na **startMyIR**. Přepnout na **nastavení** kartu, a proveďte následující akce.

    1. Pro **URL**, zadejte následující adresu URL pro rozhraní REST API, který se spustí prostředí Azure-SSIS IR, nahrazení `{subscriptionId}`, `{resourceGroupName}`, `{factoryName}`, a `{integrationRuntimeName}` skutečnými hodnotami pro vaše prostředí IR: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/start?api-version=2018-06-01` Alternativně může zkopírujte a vložte ID prostředku prostředí IR z jeho monitorování stránky na ADF uživatelského rozhraní nebo aplikaci nahradit následující část výše zobrazenou adresu URL: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}`
    
       ![ID prostředku prostředí IR služby SSIS ADF](./media/how-to-schedule-azure-ssis-integration-runtime/adf-ssis-ir-resource-id.png)
  
    2. Pro **metoda**vyberte **příspěvek**. 
    3. Pro **tělo**, zadejte `{"message":"Start my IR"}`. 
    4. Pro **ověřování**vyberte **MSI** pomocí spravované identity pro vaše ADF naleznete v části [identitu služby Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) najdete další informace.
    5. Pro **prostředků**, zadejte `https://management.azure.com/`.
    
       ![Plán ADF webové aktivity SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-schedule-ssis-ir.png)
  
3. Klonovat první kanál vytvořit druhý certifikát, mění se název aktivity na **stopMyIR** a nahraďte následující vlastnosti.

    1. Pro **URL**, zadejte následující adresu URL pro rozhraní REST API, která ukončí prostředí Azure-SSIS IR, nahrazení `{subscriptionId}`, `{resourceGroupName}`, `{factoryName}`, a `{integrationRuntimeName}` skutečnými hodnotami pro vaše prostředí IR: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/stop?api-version=2018-06-01`
    
    2. Pro **tělo**, zadejte `{"message":"Stop my IR"}`. 

4. Vytvoření kanálu třetí, přetažením **spuštění balíčku služby SSIS** aktivita z **aktivity** nástrojů do návrháře kanálu plochu a nakonfigurujte ho podle pokynů v [ Vyvolání balíčků SSIS pomocí aktivity spustit balíčků služby SSIS ve službě ADF](how-to-invoke-ssis-package-ssis-activity.md) článku.  Alternativně můžete použít **uložená procedura** aktivity místo a nakonfigurujte ho podle pokynů v [vyvolání balíčků SSIS pomocí aktivity uložených procedur ve službě ADF](how-to-invoke-ssis-package-stored-procedure-activity.md) článku.  V dalším kroku řetězit aktivity spuštění služby SSIS balíčku/uložené procedury mezi dvě aktivity webu, které prostředí IR, podobně jako tyto aktivity webu v kanálech první nebo druhé spuštění/zastavení.

   ![Aktivita webu ADF na vyžádání prostředí IR služby SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-on-demand-ssis-ir.png)

5. Přiřazení spravovaných identit pro vaše ADF **Přispěvatel** rolí na sebe sama, takže aktivity webu v její kanály můžete volat rozhraní REST API se spustit nebo zastavit prostředí Azure-SSIS IR zřízené v ní.  Na stránce ADF na webu Azure portal, klikněte na tlačítko **řízení přístupu (IAM)**, klikněte na tlačítko **+ přidat přiřazení role**a pak na **přidat přiřazení role** okno, proveďte následující akce.

    1. Pro **Role**vyberte **Přispěvatel**. 
    2. Pro **přiřadit přístup k**vyberte **uživatele, skupinu nebo instanční objekt služby Azure AD**. 
    3. Pro **vyberte**, vyhledejte své jméno ADF a vyberte ho. 
    4. Klikněte na **Uložit**.
    
   ![ADF spravované Identity přiřazení Role](./media/how-to-schedule-azure-ssis-integration-runtime/adf-managed-identity-role-assignment.png)

6. Ověřit vaše ADF a všechna nastavení kanálu kliknutím **ověřit, zda všechny / Validate** na panelu nástrojů továrny a kanálu. Zavřít **výstup ověření kanálu Factory/** kliknutím **>>** tlačítko.  

   ![Ověření kanálu](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-your-pipelines"></a>Testovací běh vašich kanálů

1. Vyberte **testovací běh** na panelu nástrojů pro každý kanál a viz **výstup** okno v dolním podokně. 

   ![Testovací běh](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
    
2. Třetí kanálu otestovat, spusťte SQL Server Management Studio (SSMS). V **připojit k serveru** okno, proveďte následující akce. 

    1. Pro **název serveru**, zadejte  **&lt;název vašeho serveru Azure SQL Database&gt;. database.windows.net**.
    2. Vyberte **možnosti >>**.
    3. Pro **připojit k databázi**vyberte **SSISDB**.
    4. Vyberte **Connect** (Připojit). 
    5. Rozbalte **integrace služby katalogy** -> **SSISDB** -> složky -> **projekty** -> Projekt vaše služby SSIS -> **balíčky** . 
    6. Klikněte pravým tlačítkem na zadaného balíčku služby SSIS ke spuštění a vyberte **sestavy** -> **standardních sestav** -> **všech provedení**. 
    7. Ověřte, zda byl spuštěn. 

   ![Ověřte spuštění balíčku služby SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/verify-ssis-package-run.png)

### <a name="schedule-your-pipelines"></a>Plánování kanálů

Teď, když vaše kanály fungovat jako jste očekávali, můžete vytvořit aktivační události pro spuštění v zadané cadences. Podrobnosti o přidružování aktivační události kanálů najdete v tématu [aktivace kanálu podle plánu](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule) článku.

1. Na panelu nástrojů kanálu vyberte **aktivační událost** a vyberte **nová/upravit**. 

   ![Aktivační událost -> Nový/upravit](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)

2. V **přidat aktivační události** vyberte **+ nová**.

   ![Přidat aktivační události – nový](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)

3. V **Nová aktivační událost** podokno, proveďte následující akce: 

    1. Pro **název**, zadejte název pro aktivační událost. V následujícím příkladu **každodenní spouštění** je název aktivační události. 
    2. Pro **typ**vyberte **plán**. 
    3. Pro **Start datum (UTC)**, zadejte počáteční datum a čas ve standardu UTC. 
    4. Pro **opakování**, zadejte tempo aktivační události. V následujícím příkladu je **denní** po. 
    5. Pro **End**vyberte **bez ukončení** nebo zadejte koncové datum a čas, po výběru **k datu**. 
    6. Vyberte **aktivováno** aktivační okamžitě po publikování celé nastavení ADF. 
    7. Vyberte **Další**.

   ![Aktivační událost -> Nový/upravit](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
    
4. V **parametry spuštění aktivační události** stránce zkontrolujte všechna upozornění a vyberte **Dokončit**. 
5. Celý ADF nastavení publikování tak, že vyberete **Publikovat vše** na panelu nástrojů objekt pro vytváření. 

   ![Publikovat vše](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)

### <a name="monitor-your-pipelines-and-triggers-in-azure-portal"></a>Sledujte své kanály a aktivační události na webu Azure portal

1. Pokud chcete monitorovat spuštění aktivační události a spuštění kanálu, použijte **monitorování** karty na levé straně ADF uživatelského rozhraní nebo aplikaci. Podrobné pokyny najdete v článku [monitorování kanálu](quickstart-create-data-factory-portal.md#monitor-the-pipeline) článku.

   ![Spuštění kanálu](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)

2. Pokud chcete zobrazit spuštění aktivit související se spuštěním kanálu, vyberte první odkaz (**zobrazit spuštění aktivit**) v **akce** sloupce. Třetí kanálu uvidíte tři spuštění aktivit, jeden pro každou zřetězené aktivitu v kanálu (webové aktivity pro spuštění prostředí IR, aktivita uložené procedury ke spouštění vašeho balíčku, webová aktivita zastavit prostředí IR). Chcete-li zobrazit kanál spustí znovu, vyberte **kanály** odkazu v horní části.

   ![Spuštění aktivit](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)

3. Pokud chcete zobrazit spuštění aktivační události vyberte **aktivační událost spouští** z rozevíracího seznamu v části **spuštění kanálu** v horní části. 

   ![Spuštění aktivačních událostí](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

### <a name="monitor-your-pipelines-and-triggers-with-powershell"></a>Sledujte své kanály a aktivační události s využitím Powershellu

Sledujte své kanály a aktivační události pomocí skriptů jako v následujících příkladech.

1. Získáte stav spuštění kanálu.

  ```powershell
  Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $myPipelineRun
  ```

2. Získáte informace o aktivační události.

  ```powershell
  Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name  "myTrigger"
  ```

3. Načíst stav spuštění aktivační události.

  ```powershell
  Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "myTrigger" -TriggerRunStartedAfter "2018-07-15" -TriggerRunStartedBefore "2018-07-16"
  ```

## <a name="create-and-schedule-azure-automation-runbook-that-startsstops-azure-ssis-ir"></a>Vytvoření a naplánování runbooku Azure Automation, který spustí/zastaví prostředí Azure-SSIS IR

V této části se zjistíte, jak vytvořit runbook Azure Automation, který se spustí skript Powershellu, spouštění/zastavování prostředí Azure-SSIS IR podle plánu.  To je užitečné, pokud chcete spustit další skripty před a po spuštění/zastavení prostředí IR pro pre, post zpracování.

### <a name="create-your-azure-automation-account"></a>Vytvoření účtu Azure Automation.

Pokud ještě nemáte účet Azure Automation, vytvořte ho podle pokynů v tomto kroku. Podrobné pokyny najdete v článku [vytvořit účet Azure Automation](../automation/automation-quickstart-create-account.md) článku. Jako součást v tomto kroku vytvoříte **spustit jako pro Azure** účtu (instanční objekt ve službě Azure Active Directory) a přiřaďte ho **Přispěvatel** role ve vašem předplatném Azure. Ujistěte se, že se jedná o předplatným, které obsahuje váš ADF s Azure SSIS IR. Azure Automation bude tento účet používat k ověření na Azure Resource Manager a pracovat s prostředky. 

1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. V současné době ADF uživatelského rozhraní nebo aplikací je podporována pouze v Microsoft Edge a webové prohlížeče Google Chrome.
2. Přihlaste se k [portálu Azure](https://portal.azure.com/).    
3. Vyberte **nový** v nabídce vlevo vyberte **monitorování a správa**a vyberte **automatizace**. 

   ![Nový -> monitorování + Správa -> Automatizace](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
    
2. V **přidat účet Automation** podokno, proveďte následující akce.

    1. Pro **název**, zadejte název účtu služby Azure Automation. 
    2. Pro **předplatné**, vyberte předplatné, které má vaše ADF pomocí prostředí Azure-SSIS IR. 
    3. Pro **skupiny prostředků**vyberte **vytvořit nový** vytvořit novou skupinu prostředků nebo **použít existující** vyberte nějaký existující. 
    4. Pro **umístění**, vyberte umístění pro váš účet Azure Automation. 
    5. Potvrďte **vytvoření účtu Azure spustit jako** jako **Ano**. Instanční objekt služby bude vytvořena ve službě Azure Active Directory a přiřadit **Přispěvatel** role ve vašem předplatném Azure.
    6. Vyberte **připnout na řídicí panel** trvale zobrazí v řídicím panelu Azure. 
    7. Vyberte **Vytvořit**. 

   ![Nový -> monitorování + Správa -> Automatizace](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
   
3. Zobrazí se stav nasazení vašeho účtu Azure Automation v řídicím panelu Azure a oznámení. 
    
   ![Nasazení služby automation](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
    
4. Po úspěšném vytvoření se zobrazí na domovské stránce vašeho účtu Azure Automation. 

   ![Domovská stránka služby Automation](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-adf-modules"></a>Import modulů ADF

1. Vyberte **moduly** v **SDÍLENÉ prostředky** v nabídce vlevo a ověřte, zda máte **AzureRM.DataFactoryV2**  +   **AzureRM.Profile** v seznamu modulů.

   ![Ověřte požadované moduly.](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  Pokud nemáte **AzureRM.DataFactoryV2**, přejděte na galerii prostředí PowerShell pro [AzureRM.DataFactoryV2 modulu](https://www.powershellgallery.com/packages/AzureRM.DataFactoryV2/)vyberte **nasadit do Azure Automation**, vyberte Azure Účet Automation a pak vyberte **OK**. Přejděte zpět na zobrazení **moduly** v **SDÍLENÉ prostředky** části v nabídce vlevo a počkejte, dokud **stav** z **AzureRM.DataFactoryV2** modul se změnil na **dostupné**.

    ![Ověření modulu služby Data Factory](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  Pokud nemáte **AzureRM.Profile**, přejděte na galerii prostředí PowerShell pro [modulu AzureRM.Profile](https://www.powershellgallery.com/packages/AzureRM.profile/)vyberte **nasadit do Azure Automation**, vyberte Azure Automation účet a potom vyberte **OK**. Přejděte zpět na zobrazení **moduly** v **SDÍLENÉ prostředky** části v nabídce vlevo a počkejte, dokud **stav** z **AzureRM.Profile** modul se změnil na **dostupné**.

    ![Ověření modulu profilu](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-your-powershell-runbook"></a>Vytvoření Powershellového runbooku

Následující část obsahuje postup pro vytvoření sady runbook Powershellu. Skript přidružené k sadě runbook buď spustí/zastaví založené na příkazu zadáte pro Azure-SSIS IR **operace** parametru. Tento oddíl neposkytuje kompletní informace pro vytvoření sady runbook. Další informace najdete v tématu [vytvoření sady runbook](../automation/automation-quickstart-create-runbook.md) článku.

1. Přepnout na **sady Runbook** kartě a vyberte **+ přidat runbook** z panelu nástrojů. 

   ![Přidejte tlačítko sady runbook](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
   
2. Vyberte **vytvořit nový runbook** a proveďte následující akce: 

    1. Pro **název**, zadejte **StartStopAzureSsisRuntime**.
    2. Pro **typ Runbooku**vyberte **Powershellu**.
    3. Vyberte **Vytvořit**.
    
   ![Přidejte tlačítko sady runbook](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
   
3. Zkopírujte a vložte následující skript Powershellu pro okno skript sady runbook. Uložit a potom publikovat runbook pomocí **Uložit** a **publikovat** tlačítka na panelu nástrojů. 

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
        Connect-AzureRmAccount `
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
        Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $AzureSSISName -Force
    }
    elseif($Operation -eq "STOP" -or $operation -eq "stop")
    {
        "##### Stopping #####"
        Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    }  
    "##### Completed #####"    
    ```

   ![Upravit runbook Powershellu](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
    
4. Otestovat sadu runbook tak, že vyberete **Start** tlačítko na panelu nástrojů. 

   ![Sada runbook tlačítko Start](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
    
5. V **spustit Runbook** podokno, proveďte následující ations: 

    1. Pro **název skupiny prostředků**, zadejte název skupiny prostředků, která obsahuje vaše ADF pomocí prostředí Azure-SSIS IR. 
    2. Pro **název datové TOVÁRNY**, zadejte název vaší ADF pomocí prostředí Azure-SSIS IR. 
    3. Pro **AZURESSISNAME**, zadejte název prostředí Azure-SSIS IR. 
    4. Pro **operace**, zadejte **START**. 
    5. Vyberte **OK**.  

   ![Sada runbook časový interval pro spuštění](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
   
6. V okně úlohy vyberte **výstup** dlaždici. V okně výstupu čekat na zprávu **### dokončeno ###** po zobrazení **### počáteční ###**. Spuštění prostředí Azure-SSIS IR trvá přibližně 20 minut. Zavřít **úlohy** okno a získat zpět do **Runbook** okna.

   ![Azure SSIS IR – zahájeno](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
    
7. Opakujte předchozí dva kroky pomocí **Zastavit** hodnotu **operace**. Znovu spusťte runbook kliknutím na položku **Start** tlačítko na panelu nástrojů. Zadejte skupinu prostředků, ADF a Azure-SSIS IR názvy. Pro **operace**, zadejte **Zastavit**. V okně výstupu čekat na zprávu **### dokončeno ###** po zobrazení **### zastavení ###**. Zastavení prostředí Azure-SSIS IR nepřijímá až potom ji spustit. Zavřít **úlohy** okno a získat zpět do **Runbook** okna.

## <a name="create-schedules-for-your-runbook-to-startstop-azure-ssis-ir"></a>Vytváření plánů pro sady runbook a operací spustit/zastavit prostředí Azure-SSIS IR

V předchozí části jste vytvořili runbooku Azure Automation, který můžete spustit nebo zastavit prostředí Azure-SSIS IR. V této části vytvoříte dvě plány pro své sadě runbook. Při konfiguraci prvního plánu, zadejte **START** pro **operace**. Podobně při konfiguraci je druhý řádek, zadáte **Zastavit** pro **operace**. Podrobný postup vytváření plánů najdete v tématu [vytvořit plán](../automation/automation-schedules.md#creating-a-schedule) článku.

1. V **Runbook** okně **plány**a vyberte **+ přidat plán** na panelu nástrojů. 

   ![Azure SSIS IR – zahájeno](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
   
2. V **plán Runbook** podokno, proveďte následující akce: 

    1. Vyberte **připojení plánu k runbooku**. 
    2. Vyberte **vytvoření nového plánu**.
    3. V **nový plán** podokně zadejte **Start IR denně** pro **název**. 
    4. Pro **spustí**, zadejte čas, který je pár minut po aktuálním čase. 
    5. Pro **opakování**vyberte **periodický**. 
    6. Pro **opakovat každých**, zadejte **1** a vyberte **den**. 
    7. Vyberte **Vytvořit**. 

   ![Plán pro spuštění prostředí Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
    
3. Přepnout na **nastavení parametrů a běhu** kartu. Zadejte skupinu prostředků, ADF a Azure-SSIS IR názvy. Pro **operace**, zadejte **START** a vyberte **OK**. Vyberte **OK** znovu, abyste viděli plán na **plány** stránky sady runbook. 

   ![Plán pro rovnou začít prostředí Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
    
4. Opakujte předchozí dva kroky k vytvoření plánu s názvem **zastavit prostředí IR denně**. Zadejte čas, který je nejméně 30 minut od chvíle, kdy jste zadali pro **Start IR denně** plánu. Pro **operace**, zadejte **Zastavit** a vyberte **OK**. Vyberte **OK** znovu, abyste viděli plán na **plány** stránky sady runbook. 

5. V **Runbook** okně **úlohy** v nabídce vlevo. Měli byste vidět úloh vytvořených produktem schémata v zadaný čas a jejich stavy. Zobrazí podrobnosti úlohy, jako je například jeho výstup, podobný co jste viděli po vás vaše sada runbook otestovat. 

   ![Plán pro rovnou začít prostředí Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
    
6. Po dokončení testování, zakažte plánům jejich úpravou. Vyberte **plány** v nabídce vlevo vyberte **spustit prostředí IR denní/Stop reakcí na Incidenty denně**a vyberte **ne** pro **povoleno**. 

## <a name="next-steps"></a>Další postup
Najdete v následujícím blogovém příspěvku:
-   [Modernizace a Rozšiřte své pracovní postupy ETL/ELT pomocí služby SSIS aktivit v kanálech ADF](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)

Projděte si následující články v dokumentaci ke službě SSIS: 

- [Nasazení, spuštění a monitorování balíčku SSIS v Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Připojení ke katalogu SSIS v Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Plánování spouštění balíčku v Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Připojení k místním zdrojům dat s využitím ověřování systému Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)
