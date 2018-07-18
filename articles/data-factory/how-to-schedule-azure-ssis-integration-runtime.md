---
title: Jak plánovat prostředí Azure SSIS integration runtime | Dokumentace Microsoftu
description: Tento článek popisuje, jak naplánovat spuštění a zastavení z prostředí Azure SSIS integration runtime pomocí Azure Automation a Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/16/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: f83715d2a382db271686210d9df285c255c09216
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2018
ms.locfileid: "39113973"
---
# <a name="how-to-start-and-stop-the-azure-ssis-integration-runtime-on-a-schedule"></a>Jak spustit a zastavit prostředí Azure SSIS integration runtime podle plánu
Tento článek popisuje, jak naplánovat spuštění a zastavení z prostředí Azure SSIS integration runtime (IR) pomocí služby Azure Automation a Azure Data Factory. Spuštění prostředí Azure SSIS (SQL Server Integration Services) integration runtime (IR) má svou cenu s ním spojená. Proto je obvykle chcete spustit prostředí IR jenom v případě, že budete muset spouštění balíčků služby SSIS v Azure a zastavit prostředí IR, když ho nepotřebujete. Můžete použít uživatelské rozhraní služby Data Factory nebo prostředí Azure PowerShell potřeba [ručně spustit nebo zastavit prostředí Azure SSIS IR](manage-azure-ssis-integration-runtime.md)).

Můžete například vytvořit aktivity webu pomocí webhooků do runbooku Azure Automation, PowerShell a řetězit aktivity spuštění balíčku služby SSIS mezi nimi. Aktivity webu můžete spustit a zastavit prostředí Azure-SSIS IR za běhu před a po spuštění vašeho balíčku. Další informace o aktivitě spuštění balíčku služby SSIS najdete v tématu [spouštění balíčků služby SSIS pomocí aktivity SSIS v Azure Data Factory](how-to-invoke-ssis-package-ssis-activity.md).

## <a name="overview-of-the-steps"></a>Přehled kroků

Tady jsou hlavní kroky popsané v tomto článku:

1. **Vytvoření a testování runbooku Azure Automation.** V tomto kroku vytvořte Powershellový runbook se skriptem, který spuštění nebo zastavení Azure-SSIS IR. Potom runbook otestovat ve scénářích spouštění a ZASTAVOVÁNÍ a potvrďte, že reakcí na Incidenty, spuštění nebo zastavení. 
2. **Vytvořte dva plány pro sadu runbook.** Pro první plán nakonfigurujete sadu runbook s START jako operaci. Pro druhý plán nakonfigurujte sadu runbook se jako operaci. Oba plány zadejte tempo spuštění sady runbook. Například můžete naplánovat první z nich ke spuštění v 8: 00, každý den a druhou pro spuštění v každý den 23: 00. První sada runbook běží, spustí Azure SSIS IR. Pokud druhá sada runbook běží, zastaví se Azure SSIS IR. 
3. **Vytvoření dvou webhook pro runbook**, jeden pro operace spuštění a druhou pro operaci zastavení. Adresy URL tyto webhooky použijete při konfiguraci webové aktivity v kanálu služby Data Factory. 
4. **Vytvoření kanálu Data Factory**. Kanál, který vytvoříte skládá ze tří činností. První **webové** aktivita vyvolá první webhooku spustit Azure SSIS IR. **Uložená procedura** aktivita spouští skript SQL, na kterém běží balíčků služby SSIS. Druhá **webové** aktivity zastaví Azure SSIS IR. Další informace o volání z kanálu Data Factory balíčku SSIS pomocí aktivity uložených procedur, naleznete v tématu [vyvolání balíčků SSIS](how-to-invoke-ssis-package-stored-procedure-activity.md). Pak vytvoříte aktivační událost plánovače naplánování kanálu běžet frekvencí, kterou zadáte.

## <a name="prerequisites"></a>Požadavky
Pokud jste ještě prostředí Azure SSIS integration runtime už zřídili, zřízení podle pokynů v [kurzu](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="create-and-test-an-azure-automation-runbook"></a>Vytvoření a otestování runbooku Azure Automation
V této části provedete následující kroky: 

1. Vytvoření účtu Azure Automation.
2. Vytvořte Powershellový runbook v účtu Azure Automation. Skript Powershellu, které jsou přidružené k sadě runbook spustí nebo zastaví prostředí Azure SSIS IR založené na příkazu, který zadáte pro parametr operace. 
3. Otestovat sadu runbook v obou po spuštění a zastavení scénáře ověřte, že funguje. 

### <a name="create-an-azure-automation-account"></a>Vytvoření účtu Azure Automation
Pokud nemáte účet Azure Automation, vytvořte si ho podle pokynů v tomto kroku. Podrobné pokyny najdete v článku [vytvořit účet Azure Automation](../automation/automation-quickstart-create-account.md). Jako součást v tomto kroku vytvoříte **spustit jako pro Azure** účtu (instanční objekt ve službě Azure Active Directory) a přidejte ho do **Přispěvatel** role vašeho předplatného Azure. Ujistěte se, že je stejné jako předplatné, které obsahuje služby data factory, který má Azure SSIS IR. Tento účet služby Azure Automation používá k ověření na Azure Resource Manager a pracovat s prostředky. 

1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
2. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).    
3. Vyberte **nový** v nabídce vlevo vyberte **monitorování a správa**a vyberte **automatizace**. 

    ![Nový -> monitorování + Správa -> Automatizace](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
2. V **přidat účet Automation** okno, proveďte následující kroky: 

    1. Zadejte **název** pro účet automation. 
    2. Vyberte **předplatné** , který má datovou továrnu s Azure SSIS IR. 
    3. Pro **skupiny prostředků**vyberte **vytvořit nový** a vytvořte novou skupinu prostředků nebo vyberte **použít existující** vyberte existující skupinu prostředků. 
    4. Vyberte **umístění** pro účet automation. 
    5. Ujistěte se, že **vytvořit účet Spustit jako** je nastavena na **Ano**. Instanční objekt služby se vytvoří ve službě Azure Active Directory. Přidá se do **Přispěvatel** role vašeho předplatného Azure
    6. Vyberte **připnout na řídicí panel** tak, aby se zobrazí na řídicím panelu portálu. 
    7. Vyberte **Vytvořit**. 

        ![Nový -> monitorování + Správa -> Automatizace](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
3. Zobrazí **stav nasazení** na řídicím panelu a v oznámeních. 
    
    ![Nasazení služby automation](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
4. Po úspěšném vytvoření se zobrazí domovská stránka účtu automation. 

    ![Domovská stránka služby Automation](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-data-factory-modules"></a>Import modulů služby Data Factory

1. Vyberte **moduly** v **SDÍLENÉ prostředky** části v nabídce vlevo a ověřte, jestli máte **AzureRM.Profile** a **AzureRM.DataFactoryV2** v seznamu modulů.

    ![Ověřte požadované moduly.](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  Přejděte na galerii prostředí PowerShell pro [AzureRM.DataFactoryV2 modulu](https://www.powershellgallery.com/packages/AzureRM.DataFactoryV2/)vyberte **nasadit do Azure Automation**, vyberte svůj účet Automation a pak vyberte **OK**. Přejděte zpět na zobrazení **moduly** v **SDÍLENÉ prostředky** části v nabídce vlevo a počkejte, dokud **stav** z **AzureRM.DataFactoryV2** modulu změnu **dostupné**.

    ![Ověření modulu služby Data Factory](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  Přejděte na galerii prostředí PowerShell pro [modulu AzureRM.Profile](https://www.powershellgallery.com/packages/AzureRM.profile/), klikněte na **nasadit do Azure Automation**, vyberte svůj účet Automation a pak vyberte **OK**. Přejděte zpět na zobrazení **moduly** v **SDÍLENÉ prostředky** části v nabídce vlevo a počkejte, dokud **stav** z **AzureRM.Profile**modulu změnu **dostupné**.

    ![Ověření modulu profilu](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-a-powershell-runbook"></a>Vytvoření runbooku v PowerShellu
Následující postup předvádí kroky k vytvoření runbooku v Powershellu. Skript přidružené k sadě runbook buď spustí/zastaví založené na příkazu zadáte pro prostředí Azure SSIS IR **operace** parametru. Tato část neposkytuje všechny podrobnosti pro vytvoření sady runbook. Další informace najdete v tématu [vytvoření sady runbook](../automation/automation-quickstart-create-runbook.md) článku.

1. Přepněte **sady Runbook** kartu a vyberte **+ přidat runbook** z panelu nástrojů. 

    ![Přidejte tlačítko sady runbook](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
2. Vyberte **vytvořit nový runbook**a proveďte následující kroky: 

    1. Pro **název**, typ **StartStopAzureSsisRuntime**.
    2. Pro **typ Runbooku**vyberte **Powershellu**.
    3. Vyberte **Vytvořit**.
    
        ![Přidejte tlačítko sady runbook](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
3. Kopírování a vkládání následující skript do okna skript sady runbook. Uložit a potom publikovat sadu runbook pomocí **Uložit** a **publikovat** tlačítka na panelu nástrojů. 

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
5. Otestovat sadu runbook tak, že vyberete **Start** tlačítko na panelu nástrojů. 

    ![Sada runbook tlačítko Start](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
6. V **spustit Runbook** okno, proveďte následující kroky: 

    1. Pro **název skupiny prostředků**, zadejte název skupiny prostředků pomocí služby data factory, který má Azure SSIS IR. 
    2. Pro **název datové TOVÁRNY**, zadejte název objektu pro vytváření dat, která má Azure SSIS IR. 
    3. Pro **AZURESSISNAME**, zadejte název Azure SSIS IR. 
    4. Pro **operace**, zadejte **START**. 
    5. Vyberte **OK**.  

        ![Sada runbook časový interval pro spuštění](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
7. V okně úlohy vyberte **výstup** dlaždici. V okně výstupu úlohy, počkejte, až se zobrazí zpráva **### dokončeno ###** po zobrazení **### počáteční ###**. Spuštění prostředí Azure SSIS IR trvá přibližně 20 minut. Zavřít **úlohy** okna a potom **Runbook** okna.

    ![Azure SSIS IR – zahájeno](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
8.  Opakujte předchozí dva kroky, ale pomocí **Zastavit** hodnotu **operace**. Znovu spusťte runbook kliknutím na položku **Start** tlačítko na panelu nástrojů. Zadejte název skupiny prostředků, název datové továrny a prostředí Azure SSIS IR název. Pro **operace**, zadejte **Zastavit**. 

    V okně výstupu úlohy, počkejte, až se zobrazí zpráva **### dokončeno ###** po zobrazení **### zastavení ###**. Zastavení prostředí Azure SSIS IR nepřijímá až od Azure SSIS IR. Zavřít **úlohy** okna a potom **Runbook** okna.

## <a name="create-schedules-for-the-runbook-to-startstop-the-azure-ssis-ir"></a>Vytváření plánů pro sady runbook spustit/zastavit prostředí Azure SSIS IR
V předchozí části jste vytvořili runbooku Azure Automation, který můžete spustit nebo zastavit Azure-SSIS IR. V této části vytvoříte dvě plány pro sadu runbook. Při konfiguraci prvního plánu, zadejte START pro parametr operace. Podobně při konfiguraci druhý plánu, zadejte místo pro OPERACI. Podrobné pokyny pro vytváření plánů, naleznete v tématu [vytvořit plán](../automation/automation-schedules.md#creating-a-schedule).

1. V **Runbook** okně **plány**a vyberte **+ přidat plán** na panelu nástrojů. 

    ![Azure SSIS IR – zahájeno](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
2. V **plán Runbook** okno, proveďte následující kroky: 

    1. Vyberte **připojení plánu k runbooku**. 
    2. Vyberte **vytvoření nového plánu**.
    3. V **nový plán** okno, zadejte **Start IR denně** pro **název**. 
    4. V **zahájí oddíl**, může určit čas pár minut po aktuálním čase. 
    5. Pro **opakování**vyberte **periodický**. 
    6. V **opakovat každých** vyberte **den**. 
    7. Vyberte **Vytvořit**. 

        ![Plán pro spuštění prostředí Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
3. Přepněte **nastavení parametrů a běhu** kartu. Zadejte název skupiny prostředků, název datové továrny a prostředí Azure SSIS IR název. Pro **operace**, zadejte **START**. Vyberte **OK**. Vyberte **OK** znovu, abyste viděli plán na **plány** stránky sady runbook. 

    ![Plán pro rovnou začít prostředí Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
4. Opakujte předchozí dva kroky k vytvoření plánu s názvem **zastavit prostředí IR denně**. Tentokrát, zadejte čas aspoň 30 minut od chvíle, kdy jste zadali pro **Start IR denně** plánu. Pro **operace**, zadejte **Zastavit**. 
5. V **Runbook** okně **úlohy** v nabídce vlevo. Měli byste vidět úloh vytvořených produktem plány v zadaném časy a jejich stavy. Můžete zobrazit podrobnosti o úlohy, například jeho výstup podobný co jste viděli při testování runbooku. 

    ![Plán pro rovnou začít prostředí Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
6. Po dokončení testování, upravovat a výběrem zakázat plány **ne** pro **povoleno**. Vyberte **plány** v nabídce vlevo vyberte **spustit prostředí IR denní/Stop reakcí na Incidenty denně**a vyberte **ne** pro **povoleno**. 

## <a name="create-webhooks-to-start-and-stop-the-azure-ssis-ir"></a>Vytváření webhooků, spuštění a zastavení prostředí Azure SSIS IR
Postupujte podle pokynů v [vytvořit webhook](../automation/automation-webhooks.md#creating-a-webhook) k vytvoření dvou webhooky pro sadu runbook. Pro první z nich zadejte START jako OPERACI a pro druhý, zadejte jako OPERACI zastavení. Uložte si adresy URL pro webhooky někde (například poznámkového bloku Onenotu nebo textového souboru). Tyto adresy URL použijete při konfiguraci webové aktivity v kanálu služby Data Factory. Příklad vytvoření webhooku, který se spustí prostředí Azure SSIS IR je znázorněno na následujícím obrázku:

1. V **Runbook** okně **Webhooky** v levé nabídce a vyberte **+ přidat Webhook** na panelu nástrojů. 

    ![Webhooky -> Přidat Webhook](./media/how-to-schedule-azure-ssis-integration-runtime/add-web-hook-menu.png)
2. V **přidat Webhook** okně **vytvořit nový webhook**, a proveďte následující akce: 

    1. Pro **název**, zadejte **StartAzureSsisIR**. 
    2. Ujistěte se, že **povoleno** je nastavena na **Ano**. 
    3. Kopírovat **URL** a uložte ho někam. Tento krok je důležitý. Adresa URL nezobrazí později. 
    4. Vyberte **OK**. 

        ![Okno Nový Webhook](./media/how-to-schedule-azure-ssis-integration-runtime/new-web-hook-window.png)
3. Přepněte **nastavení parametrů a běhu** kartu. Zadejte název skupiny prostředků, název datové továrny a název prostředí Azure SSIS IR. Pro **operace**, zadejte **START**. Klikněte na **OK**. Poté klikněte na možnost **Vytvořit**. 

    ![Webhook – nastavení parametrů a běhu](./media/how-to-schedule-azure-ssis-integration-runtime/webhook-parameters.png)
4. Opakujte předchozí tři kroky k vytvoření jiného webhooku s názvem **StopAzureSsisIR**. Nezapomeňte si zkopírovat adresu URL. Při zadávání parametrů a parametrů spuštění, zadejte **Zastavit** pro **operace**. 

Byste měli mít dvě adresy URL. jednu pro **StartAzureSsisIR** webhook a druhou pro **StopAzureSsisIR** webhooku. Můžete odeslat požadavek HTTP POST na tyto adresy URL pro spuštění/zastavení vaše Azure SSIS IR. 

## <a name="create-and-schedule-a-data-factory-pipeline-that-startsstops-the-ir"></a>Vytvoření a naplánování kanálu služby Data Factory, který spustí/zastaví prostředí IR
V této části ukazuje, jak používat aktivitu webu k vyvolání webhooky, které jste vytvořili v předchozí části.

Kanál, který vytvoříte skládá ze tří činností. 

1. První **webové** aktivita vyvolá první webhooku spustit Azure SSIS IR. 
2. **Spuštění balíčku služby SSIS** aktivity nebo **uložená procedura** aktivita spustí balíček služby SSIS.
3. Druhá **webové** aktivity volá webhook, aby zastavit Azure SSIS IR. 

Po vytvoření a testování kanálu, vytvoříte aktivační událost plánovače a přidružit kanálu. Aktivační událost plánovače definuje plán pro kanál. Předpokládejme, že vytvoříte aktivační událost, která je naplánované spouštění každý den ve 23: 00. Aktivační událost spouští kanál ve 23: 00 každý den. Kanál spuštění prostředí Azure SSIS IR, spustí balíček služby SSIS a poté se zastaví Azure SSIS IR. 

### <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).    
2. V nabídce vlevo klikněte na **Nový**, klikněte na **Data + analýzy** a pak na **Data Factory**. 
   
   ![Nový -> Datová továrna](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. Na stránce **Nová datová továrna** jako **název** zadejte **MyAzureSsisDataFactory**. 
      
     ![Stránka Nová datová továrna](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Název datové továrny Azure musí být **globálně jedinečný**. Pokud se zobrazí následující chyba, změňte název datové továrny (například na vaše_jméno_MyAzureSsisDataFactory) a zkuste to znovu. Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](naming-rules.md).
  
       `Data factory name �MyAzureSsisDataFactory� is not available`
3. Vyberte své **předplatné** Azure, ve kterém chcete vytvořit datovou továrnu. 
4. Pro **Skupinu prostředků** proveďte jeden z následujících kroků:
     
      - Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků. 
      - Vyberte **Vytvořit novou** a zadejte název skupiny prostředků.   
         
      Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).  
4. Jako **verzi** vyberte **V2**.
5. Vyberte **umístění** pro datovou továrnu. V seznamu se zobrazí pouze podporovaná umístění pro vytváření datových továren.
6. Zaškrtněte **Připnout na řídicí panel**.     
7. Klikněte na možnost **Vytvořit**.
8. Na řídicím panelu vidíte následující dlaždice se statusem: **Nasazování datové továrny**. 

    ![nasazování dlaždice datové továrny](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. Po vytvoření se zobrazí stránka **Datová továrna**, jak je znázorněno na obrázku.
   
   ![Domovská stránka datové továrny](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. Kliknutím na dlaždici **Vytvořit a monitorovat** otevřete na samostatné kartě uživatelské rozhraní služby Data Factory.

### <a name="create-a-pipeline"></a>Vytvoření kanálu

1. V **Začínáme** stránce **vytvořit kanál**. 

   ![Stránka Začínáme](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
2. V **aktivity** sady nástrojů, rozbalte **Obecné**, přetáhněte **webové** aktivity na plochu návrháře kanálu. V **Obecné** karty **vlastnosti** okna, změňte název aktivity na **StartIR**.

   ![První aktivity webu – karta Obecné](./media/how-to-schedule-azure-ssis-integration-runtime/first-web-activity-general-tab.png)
3. Přepněte **nastavení** kartu **vlastnosti** okna, a proveďte následující akce: 

    1. Pro **URL**, vložte adresu URL webhooku, která spustí Azure SSIS IR. 
    2. Pro **metoda**vyberte **příspěvek**. 
    3. Pro **tělo**, zadejte `{"message":"hello world"}`. 
   
        ![První aktivity webu – karta nastavení](./media/how-to-schedule-azure-ssis-integration-runtime/first-web-activity-settnigs-tab.png)

4. Přetažení aktivity spuštění balíčku služby SSIS nebo aktivity uložené procedury z **Obecné** část **aktivity** sady nástrojů. Nastavte název aktivity na **RunSSISPackage**. 

5. Pokud vyberete aktivity spuštění balíčku služby SSIS, postupujte podle pokynů v [spouštění balíčků služby SSIS pomocí aktivity SSIS v Azure Data Factory](how-to-invoke-ssis-package-ssis-activity.md) nezbytných k dokončení vytvoření aktivity.  Ujistěte se, že zadáte dostatečný počet opakovaných pokusů, které jsou dostatečně častý na čekání na dostupnost prostředí Azure-SSIS IR, protože trvá až 30 minut spuštění. 

    ![Nastavení opakování](media/how-to-schedule-azure-ssis-integration-runtime/retry-settings.png)

6. Pokud vyberete aktivity uložené procedury, postupujte podle pokynů v [vyvolání balíčků SSIS pomocí aktivity uložených procedur ve službě Azure Data Factory](how-to-invoke-ssis-package-stored-procedure-activity.md) nezbytných k dokončení vytvoření aktivity. Ujistěte se, že vložení skript Transact-SQL, který čeká na dostupnost prostředí Azure-SSIS IR, protože trvá až 30 minut spuštění.
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

7. Připojení **webové** aktivitu **spuštění balíčku služby SSIS** nebo **uložená procedura** aktivity. 

    ![Propojení aktivit Web a uložená procedura](./media/how-to-schedule-azure-ssis-integration-runtime/connect-web-sproc.png)

8. Přetáhnout myší jiného **webové** aktivity napravo od **spuštění balíčku služby SSIS** aktivity nebo **uložená procedura** aktivity. Nastavte název aktivity na **StopIR**. 
9. Přepněte **nastavení** kartu **vlastnosti** okna, a proveďte následující akce: 

    1. Pro **URL**, vložte adresu URL webhooku, která ukončí Azure SSIS IR. 
    2. Pro **metoda**vyberte **příspěvek**. 
    3. Pro **tělo**, zadejte `{"message":"hello world"}`.  
10. Připojení **spuštění balíčku služby SSIS** aktivity nebo **uložená procedura** aktivity na poslední **webové** aktivity.

    ![Úplný kanál](./media/how-to-schedule-azure-ssis-integration-runtime/full-pipeline.png)
11. Kliknutím na ověřit nastavení kanálu **ověřit** na panelu nástrojů. Zavřít **sestava ověření kanálu** kliknutím **>>** tlačítko. 

    ![Ověření kanálu](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-the-pipeline"></a>Testovací spuštění kanálu

1. Vyberte **testovací běh** na panelu nástrojů pro kanál. Najdete ve výstupu v **výstup** okno v dolním podokně. 

    ![Testovací běh](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
2. V **Runbook** stránku vašeho účtu Azure Automation můžete ověřit, že úlohy proběhly spuštění a zastavení Azure SSIS IR. 

    ![Úlohy sady Runbook](./media/how-to-schedule-azure-ssis-integration-runtime/runbook-jobs.png)
3. Spusťte SQL Server Management Studio. V **připojit k serveru** okno, proveďte následující akce: 

    1. Pro **název serveru**, zadejte  **&lt;Azure SQL database&gt;. database.windows.net**.
    2. Vyberte **možnosti >>**.
    3. Pro **připojit k databázi**vyberte **SSISDB**.
    4. Vyberte **Connect** (Připojit). 
    5. Rozbalte **integrace služby katalogy** -> **SSISDB** -> složky -> **projekty** -> Projekt vaše služby SSIS -> **balíčky** . 
    6. Klikněte pravým tlačítkem na váš balíček služby SSIS a vyberte **sestavy** -> **standardních sestav** -> **všech provedení**. 
    7. Ověřte, zda byl spuštěn balíčků služby SSIS. 

        ![Ověřte spuštění balíčku služby SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/verfiy-ssis-package-run.png)

### <a name="schedule-the-pipeline"></a>Naplánovat kanál 
Kanál pracuje, jak jste očekávali, můžete vytvořit aktivační událost pro spuštění kanálu v zadané tempo. Podrobnosti o přiřazení aktivační událost plánovače s kanálem, najdete v článku [aktivace kanálu podle plánu](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

1. Na panelu nástrojů pro kanál vyberte **aktivační událost**a vyberte **nová/upravit**. 

    ![Aktivační událost -> Nový/upravit](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)
2. V **přidat aktivační události** okně **+ nová**.

    ![Přidat aktivační události – nový](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)
3. V **Nová aktivační událost**, proveďte následující akce: 

    1. Pro **název**, zadejte název pro aktivační událost. V následujícím příkladu **každodenní spouštění** je název triggeru. 
    2. Pro **typ**vyberte **plán**. 
    3. Pro **datum zahájení**, vyberte počáteční datum a čas. 
    4. Pro **opakování**, zadejte tempo aktivační události. V následujícím příkladu denní jednou. 
    5. Pro **End**, můžete určit datum a čas tak, že vyberete **k datu** možnost. 
    6. Vyberte **aktivovat**. Aktivační událost se aktivuje okamžitě po publikování řešení do služby Data Factory. 
    7. Vyberte **Další**.

        ![Aktivační událost -> Nový/upravit](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
4. V **parametry spuštění aktivační události** stránky, přečtěte si upozornění a vyberte **Dokončit**. 
5. Publikování řešení do služby Data Factory tak, že vyberete **Publikovat vše** v levém podokně. 

    ![Publikovat vše](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)

### <a name="monitor-the-pipeline-and-trigger-in-the-azure-portal"></a>Monitorování kanálu a aktivační události na webu Azure Portal

1. Pokud chcete monitorovat spuštění aktivační události a spuštění kanálu, použijte **monitorování** karty na levé straně. Podrobné pokyny najdete v článku [monitorování kanálu](quickstart-create-data-factory-portal.md#monitor-the-pipeline).

    ![Spuštění kanálu](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)
2. Pokud chcete zobrazit spuštění aktivit související se spuštěním kanálu, vyberte první odkaz (**zobrazit spuštění aktivit**) v **akce** sloupce. Zobrazit spuštění aktivit tři spojené s každou aktivitu v kanálu (první webová aktivita, aktivita uložené procedury a druhé aktivity webu). Pokud chcete přepnout zpět na zobrazení spuštění kanálu, vyberte **kanály** odkazu v horní části.

    ![Spuštění aktivit](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)
3. Spuštění aktivační události můžete zobrazit také tak, že vyberete **spouštět testy** z rozevíracího seznamu vedle **spuštění kanálu** v horní části. 

    ![Spuštění aktivačních událostí](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

### <a name="monitor-the-pipeline-and-trigger-with-powershell"></a>Monitorování kanálu a aktivační události s využitím Powershellu

Použití skriptů, jako jsou následující příklady k monitorování kanálu a aktivační události.

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

## <a name="next-steps"></a>Další postup
Najdete v následujícím blogovém příspěvku:
-   [Modernizace a Rozšiřte své pracovní postupy ETL/ELT pomocí služby SSIS aktivit v kanálech ADF](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)

Projděte si následující články v dokumentaci ke službě SSIS: 

- [Nasazení, spuštění a monitorování balíčku SSIS v Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Připojení ke katalogu SSIS v Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Plánování spouštění balíčku v Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Připojení k místním zdrojům dat s využitím ověřování systému Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)
