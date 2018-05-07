---
title: Naplánování runtime integrace Azure SSIS | Microsoft Docs
description: Tento článek popisuje, jak naplánovat spuštění a zastavení z modulu runtime integrační služby SSIS Azure pomocí Azure Automation a Data Factory.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: article
ms.date: 04/30/2018
ms.author: douglasl
ms.openlocfilehash: 2ccde1a4eaff391dccf1b5f624257479acb263cb
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="how-to-schedule-starting-and-stopping-of-an-azure-ssis-integration-runtime"></a>Naplánování spuštění a zastavení z modulu runtime integrace Azure SSIS 
Spuštění modulu runtime integrace Azure služby SSIS (SQL Server Integration Services) (IR) má poplatků, s ním spojená. Tedy chcete spustit IR pouze v případě potřeby pro spouštění balíčků SSIS v Azure a zastavte ji, pokud tomu tak není. Můžete použít uživatelské rozhraní objektu pro vytváření dat nebo prostředí Azure PowerShell [ruční spuštění nebo zastavení služby SSIS IR Azure](manage-azure-ssis-integration-runtime.md)). Tento článek popisuje, jak naplánovat spuštění a zastavení z modulu runtime integrace Azure služby SSIS (IR) pomocí Azure Automation a Azure Data Factory. Zde jsou základní kroky popsané v tomto článku:

1. **Vytvoření a testování runbooku automatizace Azure.** V tomto kroku vytvoříte runbook Powershellu s skript, který spuštění nebo zastavení služby Azure SSIS infračerveného signálu. Pak test runbooku se ve scénářích spouštění a ZASTAVOVÁNÍ a potvrďte, že IR spuštění nebo zastavení. 
2. **Vytvořte dva plány pro sadu runbook.** Pro první plán nakonfigurujete sady runbook s počáteční jako operaci. Pro druhý plán nakonfigurujte sadu runbook s zastavení jako operaci. U obou plány zadáte cadence spuštění sady runbook. Například můžete naplánovat první z nich pro spuštění v 8: 00 každý den a druhý pro spuštění ve 23: 00 každý den. Když první sada runbook běžet, začne SSIS Azure, infračerveného signálu. Pokud bude druhá sada runbook spuštěna, zastaví se SSIS Azure, infračerveného signálu. 
3. **Vytvořte dva webhooky pro sadu runbook**, jeden pro počáteční operaci a druhou pro operaci UKONČETE. Adresy URL tyto webhooky použijete při konfiguraci webové aktivity v kanálu Data Factory. 
4. **Vytvoření kanálu pro vytváření dat**. Kanál, který vytvoříte, se skládá z tři aktivity. První **webové** aktivity vyvolá webhook první spuštění služby SSIS Azure, infračerveného signálu. **Uloženou proceduru** aktivita se spustí skript SQL, který spouští balíčku služby SSIS. Druhý **webové** aktivity zastaví SSIS Azure, infračerveného signálu. Další informace o vyvolání balíčku služby SSIS z objektu pro vytváření dat kanál pomocí aktivity uložené procedury najdete v tématu [vyvolání balíčku služby SSIS](how-to-invoke-ssis-package-stored-procedure-activity.md). Pak vytvořte plán aktivační událost při plánování kanál ke spuštění s frekvencí, které zadáte.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [balíčky SSIS vyvolání pomocí aktivity uložené procedury v verze 1](v1/how-to-invoke-ssis-package-stored-procedure-activity.md).

 
## <a name="prerequisites"></a>Požadavky
Pokud nebyly zřízené modulu runtime integrace Azure SSIS již, zřídit podle pokynů v [kurzu](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="create-and-test-an-azure-automation-runbook"></a>Vytvoření a otestování runbooku automatizace Azure
V této části provedete následující kroky: 

1. Vytvoření účtu Azure Automation.
2. Vytvoření sady runbook prostředí PowerShell v účtu Azure Automation. Skript prostředí PowerShell, které jsou přidružené k sadě runbook spustí nebo zastaví IR SSIS služby Azure podle příkaz, který jste zadali pro parametr operace. 
3. Otestování sady runbook v obou spuštění a zastavení scénáře potvrďte, že funguje. 

### <a name="create-an-azure-automation-account"></a>Vytvoření účtu Azure Automation
Pokud nemáte účet Azure Automation, vytvořte podle pokynů v tomto kroku. Podrobné pokyny najdete v tématu [vytvoření účtu Azure Automation](../automation/automation-quickstart-create-account.md). V rámci tohoto kroku vytvoříte **spustit v Azure jako** účet (v Azure Active Directory objekt služby) a přidejte ho do **Přispěvatel** role vašeho předplatného Azure. Ujistěte se, že je stejná jako odběr, který obsahuje data factory, který má SSIS Azure, infračerveného signálu. Automatizace Azure používá tento účet k ověření do Azure Resource Manageru a pracovat na vašich prostředků. 

1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
2. Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).    
3. Vyberte **nový** v nabídce vlevo vyberte **monitorování + správu**a vyberte **automatizace**. 

    ![Nový -> sledování a správu -> Automatizace](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
2. V **přidat účet Automation** okno, proveďte následující kroky: 

    1. Zadejte **název** pro účet služby automation. 
    2. Vyberte **předplatné** má objektu pro vytváření dat s Azure SSIS infračerveného signálu. 
    3. Pro **skupiny prostředků**, vyberte **vytvořit nový** vytvořte novou skupinu prostředků nebo vyberte **použít existující** vyberte existující skupinu prostředků. 
    4. Vyberte **umístění** pro účet služby automation. 
    5. Potvrďte, že **vytvořit účet Spustit jako** je nastaven na **Ano**. Objekt služby se vytvoří ve vašem Azure Active Directory. Je přidán do **Přispěvatel** role vašeho předplatného Azure
    6. Vyberte **připnout na řídicí panel** , abyste viděli na řídicím panelu portálu. 
    7. Vyberte **Vytvořit**. 

        ![Nový -> sledování a správu -> Automatizace](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
3. Zobrazí **stav nasazení** na řídicím panelu a v oznámení. 
    
    ![Nasazení automatizace](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
4. Po úspěšném vytvoření se zobrazí domovská stránka pro účet služby automation. 

    ![Automatizace domovské stránky](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-data-factory-modules"></a>Naimportovat moduly objektu pro vytváření dat

1. Vyberte **moduly** v **SDÍLENÉ prostředky** v levé nabídce a ověřte, jestli máte **AzureRM.Profile** a **AzureRM.DataFactoryV2** v seznamu modulů. Pokud nejsou v seznamu, vyberte **procházet galerii** na panelu nástrojů a pokračujte následujícími kroky.

    > [!IMPORTANT]
    > V současné době můžete použít pouze **AzureRM.DataFactoryV2 0.5.2** a **AzureRM.Profile 4.5.0** moduly.

    ![Ověřte požadované moduly](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  Přejděte do Galerie prostředí PowerShell pro [AzureRM.DataFactoryV2 0.5.2 modulu](https://www.powershellgallery.com/packages/AzureRM.DataFactoryV2/0.5.2), vyberte **nasadit do Azure Automation**, vyberte svůj účet Automation a pak vyberte **OK**. Přejděte zpět na zobrazení **moduly** v **SDÍLENÉ prostředky** část v nabídce vlevo a počkejte, až se zobrazí **stav** z **AzureRM.DataFactoryV2 0.5.2**  modulu změnu **dostupné**.

    ![Ověření modulu pro vytváření dat](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  Přejděte do Galerie prostředí PowerShell pro [AzureRM.Profile 4.5.0 modulu](https://www.powershellgallery.com/packages/AzureRM.profile/4.5.0), klikněte na **nasadit do Azure Automation**, vyberte svůj účet Automation a pak vyberte **OK**. Přejděte zpět na zobrazení **moduly** v **SDÍLENÉ prostředky** část v nabídce vlevo a počkejte, až se zobrazí **stav** z **AzureRM.Profile 4.5.0** modulu změnu **dostupné**.

    ![Ověření modulu profilu](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-a-powershell-runbook"></a>Vytvoření runbooku v PowerShellu
Následující postup popisuje kroky pro vytvoření sady runbook prostředí PowerShell. Skript přidružené k sadě runbook buď spustí nebo zastaví IR SSIS služby Azure podle příkaz, který určíte v parametru **operace** parametr. Tato část neposkytuje všechny podrobnosti pro vytvoření sady runbook. Další informace najdete v tématu [vytvoření sady runbook](../automation/automation-quickstart-create-runbook.md) článku.

1. Přepnout **Runbooky** a vyberte **+ přidat runbook** na panelu nástrojů. 

    ![Přidání tlačítka sady runbook](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
2. Vyberte **vytvořit nový runbook**a proveďte následující kroky: 

    1. Pro **název**, typ **StartStopAzureSsisRuntime**.
    2. Pro **typ Runbooku**, vyberte **prostředí PowerShell**.
    3. Vyberte **Vytvořit**.
    
        ![Přidání tlačítka sady runbook](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
3. Zkopírujte a vložte následující skript do okna skript sady runbook. Uložte a pak publikovat sadu runbook pomocí **Uložit** a **publikovat** tlačítek na panelu nástrojů. 

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
5. Otestuje sadu runbook tak, že vyberete **spustit** tlačítka na panelu nástrojů. 

    ![Runbook tlačítko Start](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
6. V **spuštění Runbooku** okno, proveďte následující kroky: 

    1. Pro **název skupiny prostředků**, zadejte název skupiny prostředků pomocí služby data factory, který má SSIS Azure, infračerveného signálu. 
    2. Pro **název objektu pro vytváření dat**, zadejte název objektu pro vytváření dat, který má SSIS Azure, infračerveného signálu. 
    3. Pro **AZURESSISNAME**, zadejte název služby SSIS Azure, infračerveného signálu. 
    4. Pro **operace**, zadejte **spustit**. 
    5. Vyberte **OK**.  

        ![Spusťte okno sady runbook](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
7. V okně úlohy, vyberte **výstup** dlaždici. V okně výstupu úlohy Počkejte, dokud neuvidíte zprávu **### dokončeno ###** po uvidíte **### počáteční ###**. Spouštění služby SSIS IR Azure trvá přibližně 20 minut. Zavřít **úlohy** okně a vrátit k tomu **Runbook** okno.

    ![Azure IR SSIS – spuštění](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
8.  Opakujte předchozí dva kroky, ale pomocí **Zastavit** hodnotu **OPERACI**. Znovu spustit sadu runbook tak, že vyberete **spustit** tlačítka na panelu nástrojů. Zadejte název skupiny prostředků, název objektu pro vytváření dat a název Azure SSIS reakcí na Incidenty. Pro **operace**, zadejte **Zastavit**. 

    V okně výstupu úlohy, počkejte, až se zobrazí zpráva **### dokončeno ###** po uvidíte **### zastavení ###**. Zastavování služby SSIS IR Azure nevyžaduje, pokud jako výchozí SSIS Azure, infračerveného signálu. Zavřít **úlohy** okně a vrátit k tomu **Runbook** okno.

## <a name="create-schedules-for-the-runbook-to-startstop-the-azure-ssis-ir"></a>Vytvořit plány pro sadu runbook pro spuštění a zastavení Azure SSIS IR
V předchozí části můžete vytvořit runbook služby Azure Automation, který můžete spustit nebo zastavit služby Azure SSIS infračerveného signálu. V této části vytvoříte dva plány pro sadu runbook. Při konfiguraci prvního plánu, zadejte počáteční pro parametr OPERACI. Podobně při konfiguraci druhý plánu, zadejte zastavení pro OPERACI. Podrobné pokyny pro vytvoření plány najdete v tématu [vytvořit plán](../automation/automation-schedules.md#creating-a-schedule).

1. V **Runbook** vyberte **plány**a vyberte **+ přidat plán** na panelu nástrojů. 

    ![Azure IR SSIS – spuštění](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
2. V **Runbook plán** okno, proveďte následující kroky: 

    1. Vyberte **propojit plán s runbookem**. 
    2. Vyberte **vytvořte nový plán**.
    3. V **nový plán** zadejte **spustit IR denně** pro **název**. 
    4. V **spustí části**, dobu, zadejte čas pár minut po aktuálním čase. 
    5. Pro **opakování**, vyberte **opakovaná**. 
    6. V **opakování každých** vyberte **den**. 
    7. Vyberte **Vytvořit**. 

        ![Plán pro spuštění služby SSIS IR Azure](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
3. Přepnout **nastavení parametrů a běhu** kartě. Zadejte název skupiny prostředků, název objektu pro vytváření dat a název Azure SSIS reakcí na Incidenty. Pro **operace**, zadejte **spustit**. Vyberte **OK**. Vyberte **OK** znovu zobrazíte plán na **plány** stránky sady runbook. 

    ![Plán pro spuštěním Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
4. Opakujte předchozí dva kroky k vytvoření plánu s názvem **zastavit IR denně**. Tuto chvíli zadejte čas alespoň 30 minut po času jste zadali pro **spustit IR denně** plán. Pro **operace**, zadejte **Zastavit**. 
5. V **Runbook** vyberte **úlohy** v levé nabídce. Měli byste vidět úloh vytvořených produktem plány v zadaném časy a jejich stav. Můžete zobrazit podrobnosti o úloze, jako je jeho výstup podobný co jste viděli při testování runbooku. 

    ![Plán pro spuštěním Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
6. Po dokončení testování, zakažte plány tak, že jim úpravy výběr **ne** pro **povoleno**. Vyberte **plány** v levé nabídce vyberte **spustit IR denně a zastavení IR denně**a vyberte **ne** pro **povoleno**. 

## <a name="create-webhooks-to-start-and-stop-the-azure-ssis-ir"></a>Vytvoření webhooky spuštění a zastavení Azure SSIS IR
Postupujte podle pokynů v [vytvořit webhook, jehož](../automation/automation-webhooks.md#creating-a-webhook) vytvořit dvě webhooky pro sadu runbook. Pro první zadejte počáteční jako OPERACI, a pro druhý, zadejte jako OPERACI UKONČETE. Uložte adresy URL pro webhooků někde (jako textový soubor nebo OneNote Poznámkový blok). Při konfiguraci webové aktivity v kanálu pro vytváření dat použít tyto adresy URL. Následující obrázek ukazuje příklad vytvoření webhooku, která se spouští Azure SSIS IR:

1. V **Runbook** vyberte **Webhooky** z levé nabídce a vyberte **+ přidat Webhooku** na panelu nástrojů. 

    ![Webhooky -> Přidat Webhooku](./media/how-to-schedule-azure-ssis-integration-runtime/add-web-hook-menu.png)
2. V **přidat Webhooku** vyberte **vytvořit nové webhooku**, a proveďte následující akce: 

    1. Pro **název**, zadejte **StartAzureSsisIR**. 
    2. Potvrďte, že **povoleno** je nastaven na **Ano**. 
    3. Kopírování **URL** a uložte ho někde. Tento krok je důležité. Adresa URL nevidíte později. 
    4. Vyberte **OK**. 

        ![Nové okno Webhooku](./media/how-to-schedule-azure-ssis-integration-runtime/new-web-hook-window.png)
3. Přepnout **nastavení parametrů a běhu** kartě. Zadejte název skupiny prostředků, název objektu pro vytváření dat a názvu Azure SSIS reakcí na Incidenty. Pro **operace**, zadejte **spustit**. Klikněte na **OK**. Poté klikněte na možnost **Vytvořit**. 

    ![Webhooku – nastavení parametrů a běhu](./media/how-to-schedule-azure-ssis-integration-runtime/webhook-parameters.png)
4. Opakujte předchozí tři kroky k vytvoření jiné webhooku s názvem **StopAzureSsisIR**. Nezapomeňte si zkopírujte adresu URL. Po určení parametrů a parametrů běhu, zadejte **Zastavit** pro **OPERACI**. 

Měli byste mít dvou adres URL, jeden pro **StartAzureSsisIR** webhooku a druhou pro **StopAzureSsisIR** webhooku. Můžete odeslat požadavek HTTP POST tyto adresy URL pro spuštění a zastavení vaší Azure SSIS infračerveného signálu. 

## <a name="create-and-schedule-a-data-factory-pipeline-that-startsstops-the-ir"></a>Vytvoření a plánování pro vytváření dat kanál, který spustí nebo zastaví reakcí na Incidenty
V této části ukazuje, jak pomocí webové aktivity pro vyvolání webhooků, které jste vytvořili v předchozí části.

Kanál, který vytvoříte, se skládá z tři aktivity. 

1. První **webové** aktivity vyvolá webhook první spuštění služby SSIS Azure, infračerveného signálu. 
2. **Spuštění balíčku služby SSIS** aktivity nebo **uloženou proceduru** balíčku služby SSIS je aktivita spuštěna.
3. Druhý **webové** aktivity vyvolá webhook k zastavení služby SSIS Azure, infračerveného signálu. 

Po vytvoření a testování kanálu, můžete vytvořit aktivační událost plán a přidružit kanálu. Aktivační událost plán definuje plán pro kanál. Předpokládejme, že můžete vytvořit aktivační událost, která je naplánováno spuštění každý den ve 23: 00. Aktivační událost se spustí kanálu ve 23: 00 každý den. Kanál spustí Azure SSIS IR, provede balíčku služby SSIS a poté se zastaví SSIS Azure, infračerveného signálu. 

### <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).    
2. V nabídce vlevo klikněte na **Nový**, klikněte na **Data + analýzy** a pak na **Data Factory**. 
   
   ![Nový -> Objekt pro vytváření dat](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. Na stránce **Nová datová továrna** jako **název** zadejte **MyAzureSsisDataFactory**. 
      
     ![Stránka Nová datová továrna](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Název objektu pro vytváření dat Azure musí být **globálně jedinečný**. Pokud se zobrazí následující chyba, změňte název datové továrny (například na vaše_jméno_MyAzureSsisDataFactory) a zkuste to znovu. Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](naming-rules.md).
  
       `Data factory name “MyAzureSsisDataFactory” is not available`
3. Vyberte své **předplatné** Azure, ve kterém chcete vytvořit datovou továrnu. 
4. Pro **Skupinu prostředků** proveďte jeden z následujících kroků:
     
      - Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků. 
      - Vyberte **Vytvořit novou** a zadejte název skupiny prostředků.   
         
      Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).  
4. Jako **verzi** vyberte **V2 (Preview)**.
5. Vyberte **umístění** pro objekt pro vytváření dat. V seznamu se zobrazí pouze podporovaná umístění pro vytváření datových továren.
6. Zaškrtněte **Připnout na řídicí panel**.     
7. Klikněte na možnost **Vytvořit**.
8. Na řídicím panelu vidíte následující dlaždice se statusem: **Nasazování datové továrny**. 

    ![nasazování dlaždice datové továrny](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. Po vytvoření se zobrazí stránka **Datová továrna**, jak je znázorněno na obrázku.
   
   ![Domovská stránka objektu pro vytváření dat](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. Kliknutím na dlaždici **Vytvořit a monitorovat** otevřete na samostatné kartě uživatelské rozhraní služby Data Factory.

### <a name="create-a-pipeline"></a>Vytvoření kanálu

1. V **Začínáme** vyberte **vytvořit kanál**. 

   ![Stránka Začínáme](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
2. V **aktivity** sada nástrojů, rozbalte položku **Obecné**, přetáhněte **webové** aktivity na plochu návrháře kanálu. V **Obecné** kartě **vlastnosti** okno, změňte název aktivity na **StartIR**.

   ![První aktivita webové – karta Obecné](./media/how-to-schedule-azure-ssis-integration-runtime/first-web-activity-general-tab.png)
3. Přepnout **nastavení** ve **vlastnosti** okno, a proveďte následující akce: 

    1. Pro **URL**, vložte adresu URL webhooku, která se spouští Azure SSIS infračerveného signálu. 
    2. Pro **metoda**, vyberte **POST**. 
    3. Pro **textu**, zadejte `{"message":"hello world"}`. 
   
        ![První aktivita webové – karta nastavení](./media/how-to-schedule-azure-ssis-integration-runtime/first-web-activity-settnigs-tab.png)

4. Přetáhnout myší aktivita spuštění balíčku služby SSIS nebo aktivity uložené procedury z **Obecné** části **aktivity** sady nástrojů. Název aktivity do sady **RunSSISPackage**. 

5. Pokud vyberete spuštění balíčku služby SSIS aktivity, postupujte podle pokynů v [spuštění balíčku služby SSIS pomocí aktivity SSIS v Azure Data Factory](how-to-invoke-ssis-package-ssis-activity.md) vytvoření aktivity.  Ujistěte se, že zadáváte dostatečný počet opakovaných pokusů, které jsou dostatečně častý na čekání na dostupnost služby SSIS Azure IR, vzhledem k tomu, že bude trvat až 30 minut spustit. 

    ![Nastavení opakování](media/how-to-schedule-azure-ssis-integration-runtime/retry-settings.png)

6. Pokud vyberete aktivity uložené procedury, postupujte podle pokynů v [vyvolání balíčku služby SSIS pomocí aktivity uložené procedury v Azure Data Factory](how-to-invoke-ssis-package-stored-procedure-activity.md) vytvoření aktivity. Zajistěte, aby vložení skript Transact-SQL, který čeká na dostupnost služby SSIS Azure IR, vzhledem k tomu, že bude trvat až 30 minut spustit.
    ```sql
    DECLARE @return_value int, @exe_id bigint, @err_msg nvarchar(150)

    -- Wait until Azure-SSIS IR is started
    WHILE NOT EXISTS (SELECT * FROM [SSISDB].[catalog].[worker_agents] WHERE IsEnabled = 1 AND LastOnlineTime > DATEADD(MINUTE, -10, SYSDATETIMEOFFSET()))
    BEGIN
        WAITFOR DELAY '00:00:01';
    END

    EXEC @return_value = [SSISDB].[catalog].[create_execution] @folder_name=N'YourFolder',
        @project_name=N'YourProject', @package_name=N'YourPackage',
        @use32bitruntime=0, @runincluster=1, @useanyworker=1,
        @execution_id=@exe_id OUTPUT 

    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1

    EXEC [SSISDB].[catalog].[start_execution] @execution_id = @exe_id, @retry_count = 0

    -- Raise an error for unsuccessful package execution, check package execution status = created (1)/running (2)/canceled (3)/
    -- failed (4)/pending (5)/ended unexpectedly (6)/succeeded (7)/stopping (8)/completed (9) 
    IF (SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id = @exe_id) <> 7 
    BEGIN
        SET @err_msg=N'Your package execution did not succeed for execution ID: '+ CAST(@execution_id as nvarchar(20))
        RAISERROR(@err_msg, 15, 1)
    END
    ```

7. Připojení **webové** aktivitu **spuštění balíčku služby SSIS** nebo **uloženou proceduru** aktivity. 

    ![Připojení webové a uloženou proceduru aktivity](./media/how-to-schedule-azure-ssis-integration-runtime/connect-web-sproc.png)

8. Přetáhnout myší jiné **webové** napravo od aktivity **spuštění balíčku služby SSIS** aktivity nebo **uloženou proceduru** aktivity. Název aktivity do sady **StopIR**. 
9. Přepnout **nastavení** ve **vlastnosti** okno, a proveďte následující akce: 

    1. Pro **URL**, vložte adresu URL webhooku, která ukončí SSIS Azure, infračerveného signálu. 
    2. Pro **metoda**, vyberte **POST**. 
    3. Pro **textu**, zadejte `{"message":"hello world"}`.  
10. Připojení **spuštění balíčku služby SSIS** aktivity nebo **uloženou proceduru** aktivity na poslední **webové** aktivity.

    ![Úplné kanálu](./media/how-to-schedule-azure-ssis-integration-runtime/full-pipeline.png)
11. Kliknutím na ověřit nastavení kanál **ověřením** na panelu nástrojů. Zavřít **sestavu ověření kanálu** kliknutím **>>** tlačítko. 

    ![Ověření kanálu](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-the-pipeline"></a>Testovací spuštění kanálu

1. Vyberte **spustit Test** na panelu nástrojů pro kanál. Zobrazí se výstup v **výstup** okno v dolním podokně. 

    ![Spouštění testů](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
2. V **Runbook** stránky účtu Azure Automation, můžete ověřit, jestli úlohy spustila spuštění a zastavení služby SSIS Azure, infračerveného signálu. 

    ![Úlohy sady Runbook](./media/how-to-schedule-azure-ssis-integration-runtime/runbook-jobs.png)
3. Spusťte SQL Server Management Studio. V **připojit k serveru** okno, proveďte následující akce: 

    1. Pro **název serveru**, zadejte  **&lt;Azure SQL database&gt;. database.windows.net**.
    2. Vyberte **možnosti >>**.
    3. Pro **připojit k databázi**, vyberte **SSISDB**.
    4. Vyberte **Connect** (Připojit). 
    5. Rozbalte položku **integrační služby katalogů** -> **SSISDB** -> složce -> **projekty** -> vaše SSIS projektu -> **balíčky** . 
    6. Klikněte pravým tlačítkem na váš balíček SSIS a vyberte **sestavy** -> **standardních sestav** -> **všechny spuštěních**. 
    7. Ověřte, zda byl spuštěn balíčku služby SSIS. 

        ![Ověření spuštění balíčku služby SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/verfiy-ssis-package-run.png)

### <a name="schedule-the-pipeline"></a>Plán kanálu 
Teď, když kanál funguje podle očekávání, můžete vytvořit aktivační událost při spouštění tohoto kanálu na zadaný cadence. Podrobnosti o přiřazení plánu aktivační událost se zřetězením příkazů najdete v tématu [aktivovat kanálu podle plánu](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

1. Na panelu nástrojů pro kanál, vyberte **aktivační událost**a vyberte **nové či upravit**. 

    ![Aktivační událost -> nový či upravit](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)
2. V **přidat aktivační události** vyberte **+ nový**.

    ![Přidat nové aktivační události-](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)
3. V **novou aktivační událost**, proveďte následující akce: 

    1. Pro **název**, zadejte název pro aktivační událost. V následujícím příkladu **každodenní spouštění** je název aktivační události. 
    2. Pro **typ**, vyberte **plán**. 
    3. Pro **počáteční datum**, vyberte počáteční datum a čas. 
    4. Pro **opakování**, zadejte cadence pro aktivační událost. V následujícím příkladu denní jednou. 
    5. Pro **End**, můžete určit datum a čas výběrem **na datum** možnost. 
    6. Vyberte **aktivovat**. Aktivační událost je aktivována ihned po publikování řešení služby Data Factory. 
    7. Vyberte **Next** (Další).

        ![Aktivační událost -> nový či upravit](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
4. V **parametry spuštění aktivační události** , přečtěte si upozornění a vyberte **Dokončit**. 
5. Publikování řešení do služby Data Factory výběrem **publikovat všechny** v levém podokně. 

    ![Publikovat všechny](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)
6. Ke sledování aktivační události spuštění a spuštění kanálu, použijte **monitorování** karty na levé straně. Podrobné pokyny najdete v tématu [kanál monitorovat](quickstart-create-data-factory-portal.md#monitor-the-pipeline).

    ![Spuštění kanálu](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)
7. Pokud chcete zobrazit běh aktivit, které jsou přidružené k kanálu spustit, vyberte první odkaz (**zobrazení aktivity spouští**) v **akce** sloupce. Zobrazí spustí tři aktivity spojené s každou aktivitu v kanálu (první webové aktivity, aktivity uložené procedury a druhá aktivita Web). Chcete-li přepnout zpět na zobrazení je spuštěn kanálu, vyberte **kanály** odkaz v horní části.

    ![Spuštění aktivit](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)
8. Spustí aktivační událost můžete také zobrazit výběrem **aktivovat spustí** z rozevíracího seznamu, který má vedle **kanál spustí** v horní části. 

    ![Spuštění aktivačních událostí](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

## <a name="next-steps"></a>Další postup
Projděte si následující články v dokumentaci ke službě SSIS: 

- [Nasazení, spuštění a monitorování balíčku SSIS v Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Připojení ke katalogu SSIS v Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Plánování spouštění balíčku v Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Připojení k místním zdrojům dat s využitím ověřování systému Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)
