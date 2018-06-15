---
title: Shromažďování dat analýzy protokolů se sadou runbook ve službě Azure Automation | Microsoft Docs
description: Podrobný kurz, který provede procesem vytvoření sady runbook ve službě Azure Automation ke shromažďování dat do úložiště OMS pro analýzu podle analýzy protokolů.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: operations-management-suite
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/27/2017
ms.author: bwren
ms.openlocfilehash: 0784e2317fbc98561b486547654ca27bb30e76c3
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
ms.locfileid: "31597363"
---
# <a name="collect-data-in-log-analytics-with-an-azure-automation-runbook"></a>Shromáždit data Log Analytics s runbook služby automatizace Azure
Můžete shromáždit významné množství dat v analýzy protokolů z různých zdrojů včetně [zdroje dat](../log-analytics/log-analytics-data-sources.md) na agentech a také [data shromážděná z Azure](../log-analytics/log-analytics-azure-storage.md).  Když potřebujete-li shromažďovat data, není přístupná prostřednictvím těchto zdrojů je standardní existují scénáře.  V těchto případech můžete použít [rozhraní API sady kolekcí dat protokolu HTTP](../log-analytics/log-analytics-data-collector-api.md) při zápisu dat k analýze protokolů z libovolného klienta REST API.  Běžnou metodou k provedení této kolekce dat používá sady runbook ve službě Azure Automation.   

Tento kurz vás provede proces pro vytvoření a plánování runbooku ve službě Azure Automation k zápisu dat k analýze protokolů.


## <a name="prerequisites"></a>Požadavky
Tento scénář vyžaduje následující prostředky, které jsou nakonfigurované ve vašem předplatném Azure.  Jak může být bezplatný účet.

- [Přihlaste se pracovní prostor analýzy](../log-analytics/log-analytics-get-started.md).
- [Účet Azure automation](../automation/automation-offering-get-started.md).

## <a name="overview-of-scenario"></a>Přehled scénáře
V tomto kurzu napíšete sadu runbook, která shromažďuje informace o automatizaci úloh.  Runbooky ve službě Azure Automation jsou implementované v prostředí PowerShell, spusťte psaní a testování skript v editoru Azure Automation.  Jakmile ověříte, že shromažďujete požadované informace, budete zápisu dat k analýze protokolů a ověřte typ vlastní data.  Nakonec vytvoříte plán, který chcete spouštět sadu runbook v pravidelných intervalech.

> [!NOTE]
> Můžete nakonfigurovat automatizace Azure, aby odesílat informace o úloze k analýze protokolů bez této sady runbook.  Tento scénář se používá hlavně pro podporu tohoto kurzu a doporučuje posílat data do pracovního prostoru testu.  


## <a name="1-install-data-collector-api-module"></a>1. Instalace modulu rozhraní API sady kolekcí dat
Každý [žádosti z rozhraní API sady kolekcí dat protokolu HTTP](../log-analytics/log-analytics-data-collector-api.md#create-a-request) musí být správně naformátován a obsahovat hlavičku autorizace.  Můžete to provést ve vašem runbooku, ale můžete snížit množství pomocí modulu, který zjednodušuje tento proces vyžaduje kód.  Jeden modul, který můžete použít se [OMSIngestionAPI modulu](https://www.powershellgallery.com/packages/OMSIngestionAPI) v galerii prostředí PowerShell.

Použít [modulu](../automation/automation-integration-modules.md) v sadě runbook, musí být nainstalován ve vašem účtu Automation.  Všechny sady runbook ve stejném účtu pak můžete použít funkce v modulu.  Můžete nainstalovat nový modul výběrem **prostředky** > **moduly** > **přidat modul** ve vašem účtu Automation.  

Galerie prostředí PowerShell ale nabízí rychlou možnost k nasazení modul přímo na účtu automation, proto tuto možnost můžete použít pro tento kurz.  

![Modul OMSIngestionAPI](media/operations-management-suite-runbook-datacollect/OMSIngestionAPI.png)

1. Přejděte na [Galerie prostředí PowerShell](https://www.powershellgallery.com/).
2. Vyhledejte **OMSIngestionAPI**.
3. Klikněte na **nasadit do Azure Automation** tlačítko.
4. Vyberte svůj účet automation a klikněte na **OK** nainstalovat modul.


## <a name="2-create-automation-variables"></a>2. Vytvoření proměnné služeb automatizace
[Proměnné služeb automatizace](..\automation\automation-variables.md) obsahovat hodnoty, které mohou být využívána všem runbookům v účtu Automation.  Provádění sady runbook flexibilnější tím, že se tyto hodnoty změnit bez úprav skutečné sady runbook. Každý požadavek z rozhraní API sady kolekcí dat protokolu HTTP vyžaduje ID a klíč pracovního prostoru OMS a proměnné prostředky jsou ideální pro ukládání těchto informací.  

![Proměnné](media/operations-management-suite-runbook-datacollect/variables.png)

1. Na portálu Azure přejděte na svůj účet Automation.
2. Vyberte **proměnné** pod **sdílené prostředky**.
2. Klikněte na tlačítko **přidat proměnnou** a vytvořte dvě proměnné, které v následující tabulce.

| Vlastnost | Hodnota ID pracovního prostoru | Hodnota klíče pracovního prostoru |
|:--|:--|:--|
| Název | ID pracovního prostoru | WorkspaceKey |
| Typ | Řetězec | Řetězec |
| Hodnota | Vložte ID pracovního prostoru pracovní prostor analýzy protokolů. | Vkládání pomocí primární nebo sekundární klíč pracovního prostoru analýzy protokolů. |
| Šifrované | Ne | Ano |



## <a name="3-create-runbook"></a>3. Vytvoření runbooku

Automatizace Azure má editoru na portálu, kde můžete upravit a otestujte svůj runbook.  Máte možnost použít editor skriptů pro práci s [prostředí PowerShell přímo](../automation/automation-edit-textual-runbook.md) nebo [vytvořit grafický runbook](../automation/automation-graphical-authoring-intro.md).  V tomto kurzu bude fungovat se skript prostředí PowerShell. 

![Úprava runbooku](media/operations-management-suite-runbook-datacollect/edit-runbook.png)

1. Přejděte na svůj účet Automation.  
2. Klikněte na tlačítko **Runbooky** > **přidat runbook** > **vytvořit nový runbook**.
3. Název sady runbook, zadejte **shromažďování. automatizace úloh**.  Typ runbooku, vyberte **prostředí PowerShell**.
4. Klikněte na tlačítko **vytvořit** vytvořit sadu runbook a spusťte editor.
5. Zkopírujte a vložte následující kód do runbooku.  Komentáře ve skriptu pro vysvětlení kódu odkazovat.
    
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


## <a name="4-test-runbook"></a>4. Služba test runbook
Zahrnuje prostředí do služby Azure Automation [Otestujte svůj runbook](../automation/automation-testing-runbook.md) před publikováním.  Můžou kontrolovat data shromažďovaná společností sady runbook a ověřte ji k analýze protokolů zapíše podle očekávání před publikováním do produkčního prostředí. 
 
![Služba test runbook](media/operations-management-suite-runbook-datacollect/test-runbook.png)

6. Klikněte na tlačítko **Uložit** uložit sady runbook.
1. Klikněte na tlačítko **testovací podokno** otevřete sadu runbook v testovacím prostředí.
3. Vzhledem k tomu, že vaše sada runbook obsahuje parametry, se zobrazí výzva k zadání hodnot pro ně.  Zadejte název skupiny prostředků a automatizace účet, který budete shromažďovat informace o úlohách z.
4. Klikněte na tlačítko **spustit** spuštění sady runbook.
3. Runbook se spustí se stavem **zařazeno ve frontě** předtím, než přejdete do **systémem**.  
3. Sada runbook by měl zobrazit podrobný výstup s úlohami shromážděných ve formátu json.  Pokud nejsou uvedeny žádné úlohy, pak může byly žádné úlohy vytvořené v účtu automation za poslední hodinu.  Pokuste se spustit žádné sady runbook v účtu automation a potom proveďte test znovu.
4. Ujistěte se, že výstup nezobrazí žádné chyby v příkazu post k analýze protokolů.  Měli byste mít zprávu podobný následujícímu.

    ![Výstup POST](media/operations-management-suite-runbook-datacollect/post-output.png)

## <a name="5-verify-records-in-log-analytics"></a>5. Ověřit záznamy v analýzy protokolů
Po sada runbook byla dokončena v testu, a ověřit, že byly úspěšně získány výstup, můžete ověřit, že záznamy byly vytvořené pomocí [hledání protokolů v analýzy protokolů](../log-analytics/log-analytics-log-searches.md).

![Protokolování výstupu](media/operations-management-suite-runbook-datacollect/log-output.png)

1. Na portálu Azure vyberte pracovní prostor analýzy protokolů.
2. Klikněte na **protokolu vyhledávání**.
3. Zadejte následující příkaz `Type=AutomationJob_CL` a klikněte na tlačítko Hledat. Všimněte si, že typ záznamu obsahuje _CL, které není zadané ve skriptu.  Tuto příponu automaticky připojena typ protokolu to znamená, že je typ vlastního protokolu.
4. Měli byste vidět jeden nebo více záznamů vrátil, která určuje, že sada runbook funguje podle očekávání.


## <a name="6-publish-the-runbook"></a>6. Publikovat sadu runbook
Jakmile se ujistíte, že runbook správně funguje, musíte ho publikujete, aby ji můžete spustit v produkčním prostředí.  Můžete upravit a otestování sady runbook beze změny publikovanou verzi.  

![Publikování sady runbook](media/operations-management-suite-runbook-datacollect/publish-runbook.png)

1. Vraťte se k účtu automation.
2. Klikněte na **Runbooky** a vyberte **shromažďování. automatizace úloh**.
3. Klikněte na tlačítko **upravit** a potom **publikování**.
4. Klikněte na tlačítko **Ano** při výzva, abyste ověřili, že chcete přepsat dříve publikovanou verzi.

## <a name="7-set-logging-options"></a>7. Nastavení možností protokolování 
Pro test, bylo možné zobrazit [podrobný výstup](../automation/automation-runbook-output-and-messages.md#message-streams) protože nastavte proměnnou $VerbosePreference ve skriptu.  V produkčním prostředí budete muset nastavit vlastnosti protokolování pro sady runbook, pokud chcete zobrazit podrobný výstup.  Pro sadu runbook v tomto kurzu použít bude se zobrazovat data json odesílány k analýze protokolů.

![Protokolování a trasování](media/operations-management-suite-runbook-datacollect/logging.png)

1. Ve vlastnostech pro vaše sada runbook vyberte **protokolování a trasování** pod **nastavení sady Runbook**.
2. Změna nastavení pro **protokolování podrobných záznamů** k **na**.
3. Klikněte na **Uložit**.

## <a name="8-schedule-runbook"></a>8. Plánování runbooku
Nejběžnější způsob spuštění sady runbook, která shromažďuje data monitorování je naplánovat její automatické spouštění.  To uděláte tak, že vytvoříte [plán ve službě Azure Automation](../automation/automation-schedules.md) a připojíte ho k sadě runbook.

![Plánování runbooku](media/operations-management-suite-runbook-datacollect/schedule-runbook.png)

1. Ve vlastnostech pro vaše sada runbook, zvolte **plány** pod **prostředky**.
2. Klikněte na tlačítko **přidat plán** > **propojit plán s runbookem** > **vytvořte nový plán**.
5. Zadejte následující hodnoty pro plán a klikněte na tlačítko **vytvořit**.

| Vlastnost | Hodnota |
|:--|:--|
| Název | AutomationJobs-každou hodinu |
| Spuštění | Vyberte, kdykoli se alespoň 5 minut po aktuálním čase. |
| Opakování | Opakující se |
| Opakovat každých | 1 hodina |
| Sada vypršení platnosti | Ne |

Po vytvoření plánu, budete muset nastavit hodnoty parametrů, které se použije vždy, když tento plán spuštění sady runbook.

6. Klikněte na tlačítko **nakonfigurovat parametry a nastavení spouštění**.
7. Zadejte hodnoty pro vaše **ResourceGroupName** a **AutomationAccountName**.
8. Klikněte na **OK**. 

## <a name="9-verify-runbook-starts-on-schedule"></a>9. Ověřte, sada runbook spustí podle plánu.
Při spuštění sady runbook [se vytvoří úloha](../automation/automation-runbook-execution.md) a jakéhokoli výstupu protokolována.  Ve skutečnosti jedná se o stejné úlohy, které sada runbook je shromažďování.  Můžete ověřit, že sada runbook spustí podle očekávání kontrolou úlohy pro runbook po uplynutí čas zahájení pro plán.

![Úlohy](media/operations-management-suite-runbook-datacollect/jobs.png)

1. Ve vlastnostech pro vaše sada runbook, zvolte **úlohy** pod **prostředky**.
2. Měli byste vidět v seznamu úloh pro pokaždé, když sada runbook byla spuštěna.
3. Klikněte na jednu z úlohy zobrazíte její podrobnosti.
4. Klikněte na **všechny protokoly** k zobrazení protokolů a výstup z runbooku.
5. Posuňte se dolů, chcete-li položku Najít podobná následující obrázek.<br>![Podrobné](media/operations-management-suite-runbook-datacollect/verbose.png)
6. Kliknutím na tuto položku, chcete-li zobrazit podrobné json data, která byla odeslána k analýze protokolů.



## <a name="next-steps"></a>Další postup
- Použití [Návrhář zobrazení](../log-analytics/log-analytics-view-designer.md) vytvoření zobrazení zobrazení data, která jste shromážděných do úložiště analýzy protokolů.
- Balíček svoji sadu runbook v [řešení pro správu](operations-management-suite-solutions-creating.md) distribuovat zákazníkům.
- Další informace o [analýzy protokolů](https://docs.microsoft.com/azure/log-analytics/).
- Další informace o [Azure Automation](https://docs.microsoft.com/azure/automation/).
- Další informace o [rozhraní API sady kolekcí dat protokolu HTTP](../log-analytics/log-analytics-data-collector-api.md).
