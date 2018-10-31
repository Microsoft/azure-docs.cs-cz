---
title: Spuštění Runbooku ve službě Azure Automation
description: Popisuje podrobnosti o zpracování sady runbook ve službě Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 10/30/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bb6236203a1165361505c8699ba94bff54e41c2a
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50247346"
---
# <a name="runbook-execution-in-azure-automation"></a>Spuštění Runbooku ve službě Azure Automation

Při spuštění runbooku ve službě Azure Automation, se vytvoří úloha. Úloha je instance jednoho spuštění sady runbook. Pracovník s Azure Automation se přiřadí ke spuštění každé úlohy. Když zaměstnanci sdílí mnoho účtů Azure, úlohy z různých účtů Automation jsou od sebe. Nemáte máte kontrolu, nad kterou pracovního procesu služby žádost pro vaši úlohu. Jeden runbook může mít mnoho úloh spuštěných současně. Spouštěcí prostředí pro úlohy ve stejném účtu Automation může znovu použít. Další úlohy spuštění ve stejnou dobu, tím častěji může odesílat do stejného izolovaného prostoru. Úlohy spuštěné ve stejném izolovaném prostoru procesu může ovlivnit mezi sebou, jedním z příkladů je spuštěna `Disconnect-AzureRMAccount` rutiny. Spuštění této rutiny by odpojit každé úlohy runbooku ve sdílené izolovaného prostoru procesu. Při zobrazení seznamu sad runbook na portálu Azure portal, zobrazí stav všech úloh, které byly spuštěny pro každou sadu runbook. Zobrazí se seznam úloh pro každou sadu runbook, chcete-li sledovat stav každého. Protokoly úlohy jsou uloženy maximálně 30 dnů. Popis stavy různé úlohy [stavy úlohy](#job-statuses).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

Následující diagram znázorňuje životní cyklus úlohy runbooku pro [grafické runbooky](automation-runbook-types.md#graphical-runbooks) a [runbooky pracovních postupů Powershellu](automation-runbook-types.md#powershell-workflow-runbooks).

![Stavy úlohy - pracovního postupu Powershellu](./media/automation-runbook-execution/job-statuses.png)

Následující diagram znázorňuje životní cyklus úlohy runbooku pro [Powershellové runbooky](automation-runbook-types.md#powershell-runbooks).

![Stavy úlohy - skript prostředí PowerShell](./media/automation-runbook-execution/job-statuses-script.png)

Úlohy mají přístup k prostředkům Azure tím, že připojení k předplatnému Azure. Pouze mají přístup k prostředkům ve vašem datovém centru Pokud tyto prostředky jsou přístupné z veřejného cloudu.

## <a name="job-statuses"></a>Stavy úlohy

Následující tabulka popisuje různé stavy, které jsou u úlohy nastat. Prostředí PowerShell má dva typy chyb, ukončujícími a neukončujícími chybami. Chyby se ukončuje nastavit stav sady runbook **neúspěšné** Pokud k nim dojde. Neukončující chyby povolit skriptu pokračovat i po jejich výskytu. Příklad neukončující chyba používá `Get-ChildItem` rutiny s cestou, která neexistuje. Prostředí PowerShell vidí, že cesta neexistuje, vyvolá chybu a pokračuje do další složky. Tato chyba nebude nastavit stav sady runbook **neúspěšné** a může být označen jako **dokončeno**. Chcete-li vynutit sady runbook a zastavit na neukončující chybě, můžete použít `-ErrorAction Stop` na rutiny.

| Status | Popis |
|:--- |:--- |
| Dokončeno |Úloha byla úspěšně dokončena. |
| Selhalo |Pro [grafické runbooky pracovních postupů Powershellu](automation-runbook-types.md), sada runbook se nepodařilo zkompilovat. Pro [Powershellový skript sady runbook](automation-runbook-types.md), se nepodařilo spustit sadu runbook nebo úloha měla výjimku. |
| Se nezdařilo, čekání na prostředky |Úloha se nezdařila, protože bylo dosaženo [spravedlivé sdílení](#fair-share) omezit třikrát a spustit pokaždé, když ze stejného kontrolního bodu nebo od začátku runbooku. |
| Ve frontě |Úloha čeká pro prostředky služby Automation zpřístupnění pracovního procesu tak, aby jeho spuštění. |
| Spouštění |Úloha byla přiřazena k pracovnímu procesu a spouštění systému. |
| Obnovení |Systém obnovuje úlohy poté, co bylo pozastaveno. |
| Spuštěno |Úloha je spuštěna. |
| Spuštění, čekání na prostředky |Úloha byla uvolněna, protože bylo dosaženo [spravedlivé sdílení](#fair-share) limit. Bude pokračovat i za chvíli od svého posledního kontrolního bodu. |
| Zastaveno |Úloha se zastavila tímto uživatelem, než se provedl. |
| Zastavování |V systému Probíhá zastavování úlohy. |
| Pozastaveno |Úlohu pozastavil uživatel, systém nebo příkaz v runbooku. Pokud sada runbook neobsahuje kontrolní bod, začne od začátku runbooku. Pokud má kontrolní bod, můžete začít znovu a pokračovat od svého posledního kontrolního bodu. Sada runbook je pouze pozastaveno systémem, když dojde k výjimce. Ve výchozím nastavení, ErrorActionPreference nastavená na **pokračovat**, což znamená, že úloha běžel na chybu. Pokud je tato předvolba proměnné nastavená **Zastavit**, pak v případě chyby pozastaví úlohu. Platí pro [grafické runbooky pracovních postupů Powershellu](automation-runbook-types.md) pouze. |
| Pozastavování |Systém se pokouší pozastavit úlohu na žádost uživatele. Sada runbook dosažení následujícího kontrolního bodu předtím, než je možné ho pozastavit. Pokud už uplynulé svého posledního kontrolního bodu, pak dokončí předtím, než je možné ho pozastavit. Platí pro [grafické runbooky pracovních postupů Powershellu](automation-runbook-types.md) pouze. |

## <a name="viewing-job-status-from-the-azure-portal"></a>Zobrazení stavu úlohy na webu Azure Portal

Můžete zobrazit souhrnný stav všech úloh sady runbook nebo přejdete na podrobnosti o konkrétní runbooku na portálu Azure portal. Můžete také konfigurace integrace s pracovního prostoru Log Analytics předávat datové proudy úlohy stavu a úlohu runbooku. Další informace o integraci se službou Log Analytics najdete v tématu [předávání stavu úlohy a datové proudy úlohy ze služby Automation do Log Analytics](automation-manage-send-joblogs-log-analytics.md).

### <a name="automation-runbook-jobs-summary"></a>Souhrn úlohy runbooků služby Automation

Na pravé straně vybraný účet Automation, můžete prohlédnout souhrnné informace o všech úloh sad runbook v rámci **statistiky úlohy** dlaždici.

![Dlaždice statistiky úlohy](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Na této dlaždici se zobrazuje počet a grafické vyjádření stavu úlohy pro všechny úlohy provést.

Kliknutím na soubor představuje **úlohy** stránky, která obsahuje souhrnný seznam všech úloh provedeny. Tato stránka zobrazuje stav, časy zahájení a dokončení časy.

![Stránka úlohy účtu Automation](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

Seznam úloh můžete filtrovat výběrem **filtrování úloh** a jsou filtrované na konkrétní sadu runbook, stav úloh, nebo z rozevíracího seznamu a časový rozsah má hledat.

![Filtr stavu úlohy](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Alternativně můžete zobrazit podrobnosti souhrnu úlohy pro konkrétní sadu runbook tak, že vyberete dané sady runbook z **sady Runbook** stránky ve vašem účtu Automation a pak vyberte **úlohy** dlaždici. Tato akce představuje **úlohy** stránky, a z něj můžete kliknout na úlohu záznam, který chcete zobrazit podrobnosti a výstup.

![Stránka úlohy účtu Automation](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>Souhrn úlohy

Můžete zobrazit seznam všech úloh, které byly vytvořeny pro konkrétní sadu runbook a jejich aktuální stav. Můžete filtrovat tento seznam podle stavu úlohy a rozsahu dat poslední změny úlohy. Chcete-li zobrazit podrobné informace a výstup, klikněte na název úlohy. V podrobném zobrazení úlohy obsahuje hodnoty parametrů runbooku poskytnuté pro tuto úlohu.

Chcete-li zobrazit úlohy pro sady runbook můžete použít následující kroky.

1. Na webu Azure Portal, vyberte **automatizace** a potom vyberte název účtu Automation.
2. Z centra, vyberte **sady Runbook** a pak na **sady Runbook** stránky ze seznamu vybrat sadu runbook.
3. Na stránce pro vybranou sadu runbook, klikněte na tlačítko **úlohy** dlaždici.
4. Klikněte na jednu z úloh v seznamu a na stránce podrobností úlohy runbooku můžete zobrazit podrobnosti a výstup.

## <a name="retrieving-job-status-using-windows-powershell"></a>Načtení stavu úlohy pomocí prostředí Windows PowerShell

Můžete použít [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) můžete načíst úlohy vytvořené pro runbook a podrobnosti konkrétní úlohy. Pokud runbook spustíte s Windows Powershellem pomocí [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook), vrátí se výsledná úloha. Použití [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) výstupu úlohy.

Následující ukázkové příkazy z poslední úlohu ukázkového runbooku načíst a zobrazit její stav, hodnot poskytnutých parametrů runbooku a výstup z úlohy.

```azurepowershell-interactive
$job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

Následující příklad načte výstup pro konkrétní úlohu a vrátí všechny záznamy. V případě, že došlo k výjimce pro jeden ze záznamů, výjimka je zapsán místo hodnoty. Toto chování je užitečné jako výjimky může poskytovat doplňující informace, které nemusejí být zaznamenány obvykle při výstupu.

```azurepowershell-interactive
$output = Get-AzureRmAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzureRmAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
    if ($fullRecord.Type -eq "Error")
    {
        $fullRecord.Value.Exception
    }
    else
    {
    $fullRecord.Value
    }
}
```

## <a name="get-details-from-activity-log"></a>Získejte informace od protokolů aktivit

Další podrobnosti, jako je osoba nebo účet, který spustil sadu runbook můžete získat z protokolu aktivit pro účet automation. Následující příklad Powershellu poskytuje poslední uživatel ke spuštění sady runbook dotyčný:

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$rg = "ResourceGroup01"
$AutomationAccount = "MyAutomationAccount"
$RunbookName = "Test-Runbook"
$JobResourceID = "/subscriptions/$subid/resourcegroups/$rg/providers/Microsoft.Automation/automationAccounts/$AutomationAccount/jobs"

Get-AzureRmLog -ResourceId $JobResourceID -MaxRecord 1 | Select Caller
```

## <a name="fair-share"></a>Spravedlivé sdílení

Pokud chcete sdílení prostředků mezi všechny runbooky v cloudu, služby Azure Automation bude dočasně uvolnit nebo zastavit všechny úlohy, který byl spuštěný déle než tři hodiny. Úlohy pro [sady runbook pomocí prostředí PowerShell](automation-runbook-types.md#powershell-runbooks) a [runbooky Python](automation-runbook-types.md#python-runbooks) se zastaví a ne restartuje, a stav úlohy zobrazuje zastaveno.

Pro dlouho běžící úlohy se doporučuje použít funkci [Hybrid Runbook Worker](automation-hrw-run-runbooks.md#job-behavior). Procesy hybrid Runbook Worker není omezena spravedlivé sdílení a nemají omezení můžete spustit na jak dlouho sady runbook. Další úlohy [omezení](../azure-subscription-service-limits.md#automation-limits) platí pro Azure karantény a procesy Hybrid Runbook Worker. Zatímco proces Hybrid Runbook Worker nejsou omezeny limit spravedlivé sdílení 3 hodiny, sady runbook spustil na nich by měl být stále vyvinutý za účelem podpory chování restartování z problémů s neočekávaným místní infrastrukturou.

Další možností je Optimalizujte sady runbook pomocí runbooků. Pokud vaše sada runbook prochází stejnou funkci na celé řadě zdrojů, jako je například databázová operace na několik databází, můžete přesunout na tuto funkci [podřízeného runbooku](automation-child-runbooks.md) a volání s [ Start-AzureRMAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) rutiny. Každý z těchto podřízených runbooků se bude provádět paralelně v samostatném procesu a tím se sníží celková doba zpracování nadřazeného runbooku. Můžete použít [Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/Get-AzureRmAutomationJob) rutina v sadě runbook, a zkontrolujte stav úlohy pro každou podřízenou, pokud operace, které je třeba provést po dokončení podřízeného runbooku.

## <a name="next-steps"></a>Další postup

* Další informace o různých metodách, které můžete použít ke spuštění sady runbook ve službě Azure Automation najdete v tématu [spuštění runbooku ve službě Azure Automation](automation-starting-a-runbook.md)
