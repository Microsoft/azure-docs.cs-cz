---
title: Shromažďování dat Log Analytics pomocí sady runbook ve službě Azure Automation | Dokumentace Microsoftu
description: Podrobný kurz, který provede procesem vytvoření sady runbook ve službě Azure Automation ke shromažďování dat do úložiště pro analýzu pomocí Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: monitoring
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/27/2017
ms.author: bwren
ms.openlocfilehash: 1472e6cf05485b942c93f632c147fcb0b0311c61
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2018
ms.locfileid: "51828209"
---
# <a name="collect-data-in-log-analytics-with-an-azure-automation-runbook"></a>Shromažďování dat v Log Analytics runbooku Azure Automation
Významné množství dat v Log Analytics může shromažďovat z nejrůznějších zdrojů, včetně [zdroje dat](../../log-analytics/log-analytics-data-sources.md) na agentech a také [shromažďovat data z Azure](../../log-analytics/log-analytics-azure-storage.md).  Potřebujete-li shromažďovat data, která není přístupná prostřednictvím těchto zdrojů na standardní existují scénáře.  V těchto případech můžete použít [rozhraní API kolekce dat HTTP](../../log-analytics/log-analytics-data-collector-api.md) zapsat data do Log Analytics z jakéhokoli klienta REST API.  Běžnou metodou k provedení této kolekce dat používá sady runbook ve službě Azure Automation.   

Tento kurz vás provede procesem vytvoření a plánování runbooku ve službě Azure Automation k zápisu dat do Log Analytics.


## <a name="prerequisites"></a>Požadavky
Tento scénář vyžaduje následující prostředcích nakonfigurovaných ve vašem předplatném Azure.  Může být bezplatný účet.

- [Pracovní prostor log Analytics](../../log-analytics/log-analytics-quick-create-workspace.md).
- [Účet Azure automation](../..//automation/automation-quickstart-create-account.md).

## <a name="overview-of-scenario"></a>Přehled scénáře
Pro účely tohoto kurzu budete psát sadu runbook, která shromažďuje informace o úloh služby Automation.  Runbooky ve službě Azure Automation jsou implementovány pomocí Powershellu, takže začnete psaní a testování skript v editoru Azure Automation.  Jakmile ověříte, že shromažďujete požadované informace, budete zapisovat data do Log Analytics a ověření vlastní datový typ.  Nakonec vytvoříte plán, který chcete spouštět sadu runbook v pravidelných intervalech.

> [!NOTE]
> Můžete nakonfigurovat informace o úlohách odesílat do Log Analytics bez této sady runbook Azure Automation.  Tento scénář se používá především pro podporu tohoto kurzu, a doporučuje odesílat data do pracovního prostoru testu.  


## <a name="1-install-data-collector-api-module"></a>1. Instalace modulu rozhraní API kolekce dat
Každý [žádosti z rozhraní API kolekce dat HTTP](../../log-analytics/log-analytics-data-collector-api.md#create-a-request) musí být správně naformátován a zahrnují také hlavičku ověřování.  Můžete to provést v sadě runbook, ale můžete snížit množství kódu vyžadovaného pomocí modulu, který zjednodušuje tento proces.  Jeden modul, který vám pomůže se [OMSIngestionAPI modulu](https://www.powershellgallery.com/packages/OMSIngestionAPI) v galerii prostředí PowerShell.

Použití [modulu](../../automation/automation-integration-modules.md) v sadě runbook, je třeba nainstalovat ve vašem účtu Automation.  Každý runbook v rámci stejného účtu pak můžete použít funkce v modulu.  Nový modul můžete nainstalovat tak, že vyberete **prostředky** > **moduly** > **přidat modul** ve vašem účtu Automation.  

Galerie prostředí PowerShell ale nabízí rychlou možnost jak nasadit modul přímo do vašeho účtu automation, abyste tuto možnost můžete použít pro účely tohoto kurzu.  

![OMSIngestionAPI modulu](media/runbook-datacollect/OMSIngestionAPI.png)

1. Přejděte na [Galerie prostředí PowerShell](https://www.powershellgallery.com/).
2. Vyhledejte **OMSIngestionAPI**.
3. Klikněte na **nasadit do Azure Automation** tlačítko.
4. Vyberte svůj účet automation a klikněte na tlačítko **OK** nainstalovat modul.


## <a name="2-create-automation-variables"></a>2. Vytvoření proměnné služeb automatizace
[Proměnné služeb automatizace](../../automation/automation-variables.md) uložení hodnoty, které mohou využívat všechny sady runbook ve vašem účtu Automation.  Využívají sady runbook flexibilnější, neboť umožňuje tyto hodnoty změnit bez úprav skutečné sady runbook. Každý požadavek z rozhraní API kolekce dat HTTP vyžaduje ID a klíč pracovního prostoru Log Analytics a proměnných assetů jsou ideální k ukládání příslušných informací.  

![Proměnné](media/runbook-datacollect/variables.png)

1. Na webu Azure Portal přejděte na svůj účet Automation.
2. Vyberte **proměnné** pod **sdílené prostředky**.
2. Klikněte na tlačítko **přidat proměnnou** a vytvořte dvě proměnné v následující tabulce.

| Vlastnost | Hodnota ID pracovního prostoru | Hodnota klíče pracovního prostoru |
|:--|:--|:--|
| Název | ID pracovního prostoru | WorkspaceKey |
| Typ | Řetězec | Řetězec |
| Hodnota | Vložte ID pracovního prostoru z pracovního prostoru Log Analytics. | Vložit pomocí primární nebo sekundární klíč pracovního prostoru Log Analytics. |
| Šifrované | Ne | Ano |



## <a name="3-create-runbook"></a>3. Vytvoření runbooku

Azure Automation obsahuje editor na portálu, kde můžete upravit a otestovat sadu runbook.  Máte možnost používat editor skriptů pro práci s [Powershellu přímo](../../automation/automation-edit-textual-runbook.md) nebo [vytvoření grafického runbooku](../../automation/automation-graphical-authoring-intro.md).  Pro účely tohoto kurzu budete pracovat pomocí skriptu prostředí PowerShell. 

![Úprava runbooku](media/runbook-datacollect/edit-runbook.png)

1. Přejděte na svůj účet Automation.  
2. Klikněte na tlačítko **sady Runbook** > **přidat runbook** > **vytvořit nový runbook**.
3. Název sady runbook, zadejte **úloh služby Automation shromáždit**.  Typ runbooku, vyberte **Powershellu**.
4. Klikněte na tlačítko **vytvořit** k vytvoření sady runbook a spusťte editor.
5. Zkopírujte a vložte následující kód do runbooku.  Přečtěte si komentáře ve skriptu pro vysvětlení kódu.
    
        # Get information required for the automation account from parameter values when the runbook is started.
        Param
        (
            [Parameter(Mandatory = $True)]
            [string]$resourceGroupName,
            [Parameter(Mandatory = $True)]
            [string]$automationAccountName
        )
        
        # Authenticate to the Automation account using the Azure connection created when the Automation account was created.
        # Code copied from the runbook AzureAutomationTutorial.
        $connectionName = "AzureRunAsConnection"
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
        Connect-AzureRmAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
        
        # Set the $VerbosePreference variable so that we get verbose output in test environment.
        $VerbosePreference = "Continue"
        
        # Get information required for Log Analytics workspace from Automation variables.
        $customerId = Get-AutomationVariable -Name 'WorkspaceID'
        $sharedKey = Get-AutomationVariable -Name 'WorkspaceKey'
        
        # Set the name of the record type.
        $logType = "AutomationJob"
        
        # Get the jobs from the past hour.
        $jobs = Get-AzureRmAutomationJob -ResourceGroupName $resourceGroupName -AutomationAccountName $automationAccountName -StartTime (Get-Date).AddHours(-1)
        
        if ($jobs -ne $null) {
            # Convert the job data to json
            $body = $jobs | ConvertTo-Json
        
            # Write the body to verbose output so we can inspect it if verbose logging is on for the runbook.
            Write-Verbose $body
        
            # Send the data to Log Analytics.
            Send-OMSAPIIngestionFile -customerId $customerId -sharedKey $sharedKey -body $body -logType $logType -TimeStampField CreationTime
        }


## <a name="4-test-runbook"></a>4. Test runbooku
Azure Automation obsahuje prostředí tak, aby [otestovat sadu runbook](../../automation/automation-testing-runbook.md) před publikováním.  Můžete kontrolovat data shromažďovaná společností sadu runbook a ověřte, že zapíše do Log Analytics podle očekávání před publikováním do produkčního prostředí. 
 
![Test runbooku](media/runbook-datacollect/test-runbook.png)

6. Klikněte na tlačítko **Uložit** pro sadu runbook uložte.
1. Klikněte na tlačítko **testovací podokno** otevřete sadu runbook v testovacím prostředí.
3. Protože vaše sada runbook obsahuje parametry, budete vyzváni k zadání jejich hodnot.  Zadejte název skupiny prostředků a účet služby automation, která budete shromažďovat informace o úlohách z.
4. Klikněte na tlačítko **Start** pro spuštění sady runbook.
3. Runbook se spustí se stavem **ve frontě** předtím, než se přejde na **systémem**.  
3. Sada runbook zobrazit podrobný výstup s úlohami, které jsou shromážděné ve formátu json.  Pokud nejsou uvedeny žádné úlohy, pak může nebyly žádné úlohy vytvořené v účtu automation za poslední hodinu.  Pokuste se spustit libovolné sady runbook v účtu automation a pak znovu proveďte test.
4. Ujistěte se, že ve výstupu nezobrazí všechny chyby v příkazu post do služby Log Analytics.  Měli byste mít zpráva podobná následující.

    ![Výstup příspěvku](media/runbook-datacollect/post-output.png)

## <a name="5-verify-records-in-log-analytics"></a>5. Zkontrolujte záznamy v Log Analytics
Sada runbook byla dokončena v testu a ověřit, že výstup byl úspěšně přijat, můžete ověřit, že záznamy byly vytvořeny pomocí [prohledávání protokolů v Log Analytics](../../log-analytics/log-analytics-queries.md).

![Výstup protokolu](media/runbook-datacollect/log-output.png)

1. Na webu Azure Portal vyberte pracovní prostor Log Analytics.
2. Klikněte na **prohledávání protokolů**.
3. Zadejte následující příkaz `Type=AutomationJob_CL` a klikněte na tlačítko Hledat. Všimněte si, že typ záznamu obsahuje _CL, které není specifikováno ve skriptu.  Tuto příponu automaticky připojen k typ protokolu k označení, že se jedná o typ vlastního protokolu.
4. Zobrazí se jedné nebo více záznamů vrácených, označující, že sada runbook funguje podle očekávání.


## <a name="6-publish-the-runbook"></a>6. Publikování runbooku
Jakmile ověříte, že runbook správně funguje, musíte ji publikovat, takže je možné spustit v produkčním prostředí.  Můžete nadále upravovat a testovat sady runbook beze změny publikovanou verzi.  

![Publikování runbooku](media/runbook-datacollect/publish-runbook.png)

1. Vraťte se do vašeho účtu automation.
2. Klikněte na **sady Runbook** a vyberte **úloh služby Automation shromáždit**.
3. Klikněte na tlačítko **upravit** a potom **publikovat**.
4. Klikněte na tlačítko **Ano** když se zobrazí výzva k ověření, že chcete přepsat dříve publikované verze.

## <a name="7-set-logging-options"></a>7. Nastavení možností protokolování 
Pro testování, bylo možné zobrazit [podrobný výstup](../../automation/automation-runbook-output-and-messages.md#message-streams) protože nastavte proměnnou $VerbosePreference ve skriptu.  Pro produkční prostředí je nutné nastavit vlastnosti protokolování pro sady runbook, pokud chcete zobrazit podrobné informace.  Sady runbook používanou v tomto kurzu zobrazí se odesílají do Log Analytics data json.

![Protokolování a trasování](media/runbook-datacollect/logging.png)

1. Ve vlastnostech pro vaše sada runbook vyberte **protokolování a trasování** pod **nastavení sady Runbook**.
2. Změna nastavení pro **protokolování podrobných záznamů** k **na**.
3. Klikněte na **Uložit**.

## <a name="8-schedule-runbook"></a>8. Naplánování runbooku
Nejběžnější způsob spuštění sady runbook, která shromažďuje data monitorování je naplánovat její automatické spouštění.  To provedete tak, že vytvoříte [plánu ve službě Azure Automation](../../automation/automation-schedules.md) a připojení k sadě runbook.

![Naplánování runbooku](media/runbook-datacollect/schedule-runbook.png)

1. Ve vlastnostech vaší sady runbook, vyberte **plány** pod **prostředky**.
2. Klikněte na tlačítko **přidat plán** > **připojení plánu k runbooku** > **vytvoření nového plánu**.
5. Zadejte následující hodnoty pro plán a klikněte na **vytvořit**.

| Vlastnost | Hodnota |
|:--|:--|
| Název | AutomationJobs každou hodinu |
| Spuštění | Vyberte možnost kdykoli alespoň 5 minut po aktuálním čase. |
| Opakování | Opakující se |
| Opakovat každých | 1 hodina |
| Nastavit vypršení platnosti | Ne |

Po vytvoření plánu je potřeba nastavit hodnoty parametrů, které se použijí pokaždé, když tento plán spuštění runbooku.

6. Klikněte na tlačítko **nakonfigurovat parametry a nastavení spouštění**.
7. Vyplňte hodnoty pro váš **ResourceGroupName** a **AutomationAccountName**.
8. Klikněte na **OK**. 

## <a name="9-verify-runbook-starts-on-schedule"></a>9. Ověřte spuštění sady runbook podle plánu
Při každém spuštění sady runbook [se vytvoří úloha](../../automation/automation-runbook-execution.md) a jakéhokoli výstupu protokolována.  Ve skutečnosti jedná se o stejné úlohy, které shromažďuje sadu runbook.  Můžete ověřit, že je sada runbook spuštěna dle očekávání kontrolou úloh pro runbook po uplynutí čas zahájení pro plán.

![Úlohy](media/runbook-datacollect/jobs.png)

1. Ve vlastnostech vaší sady runbook, vyberte **úlohy** pod **prostředky**.
2. Měli byste vidět seznam úloh pro při každém spuštění sady runbook.
3. Klikněte na jednotlivé úlohy zobrazíte její podrobnosti.
4. Klikněte na **všechny protokoly** k zobrazení protokolů a výstup z runbooku.
5. Posuňte se dolů na položku najít podobně jako na následujícím obrázku.<br>![Podrobný](media/runbook-datacollect/verbose.png)
6. Kliknutím na tuto položku, chcete-li zobrazit podrobné json data, která se odesílají do Log Analytics.



## <a name="next-steps"></a>Další postup
- Použití [Návrhář zobrazení](../../log-analytics/log-analytics-view-designer.md) vytvořit zobrazení, zobrazení dat shromážděných v úložišti Log Analytics.
- Vaše sada runbook v balíčku [řešení pro správu](../../azure-monitor/insights/solutions-creating.md) distribuovat zákazníkům.
- Další informace o [Log Analytics](https://docs.microsoft.com/azure/log-analytics/).
- Další informace o [Azure Automation](https://docs.microsoft.com/azure/automation/).
- Další informace o [rozhraní API kolekce dat HTTP](../../log-analytics/log-analytics-data-collector-api.md).
