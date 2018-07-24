---
title: Spuštění Runbooku ve službě Azure Automation
description: Popisuje podrobnosti o zpracování sady runbook ve službě Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 05/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3dfe16cc09f0453aef8adf8bf87a00aebd2054bc
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214631"
---
# <a name="runbook-execution-in-azure-automation"></a>Spuštění Runbooku ve službě Azure Automation

Při spuštění runbooku ve službě Azure Automation, se vytvoří úloha. Úloha je instance jednoho spuštění sady runbook. Pracovník s Azure Automation se přiřadí ke spuštění každé úlohy. Když pracovní procesy jsou sdíleny několika účtům Azure, úlohy z různých účtů Automation jsou od sebe. Můžete mít není řídit, přes které pracovního procesu služby žádost pro vaši úlohu. Jeden runbook může mít více úloh spuštěných současně. Spouštěcí prostředí pro úlohy ve stejném účtu Automation může znovu použít. Při zobrazení seznamu sad runbook na portálu Azure portal, zobrazí stav všech úloh, které byly spuštěny pro každou sadu runbook. Aby bylo možné sledovat stav každého můžete zobrazit seznam úloh pro každou sadu runbook. Popis stavy různé úlohy [stavy úlohy](#job-statuses).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

Následující diagram znázorňuje životní cyklus úlohy runbooku pro [grafické runbooky](automation-runbook-types.md#graphical-runbooks) a [runbooky pracovních postupů Powershellu](automation-runbook-types.md#powershell-workflow-runbooks).

![Stavy úlohy - pracovního postupu Powershellu](./media/automation-runbook-execution/job-statuses.png)

Následující diagram znázorňuje životní cyklus úlohy runbooku pro [Powershellové runbooky](automation-runbook-types.md#powershell-runbooks).

![Stavy úlohy - skript prostředí PowerShell](./media/automation-runbook-execution/job-statuses-script.png)

Úlohy mají přístup k prostředkům Azure tím, že připojení k předplatnému Azure. Pouze mají přístup k prostředkům ve vašem datovém centru Pokud tyto prostředky jsou přístupné z veřejného cloudu.

## <a name="job-statuses"></a>Stavy úlohy

Následující tabulka popisuje různé stavy, které jsou u úlohy nastat.

| Status | Popis |
|:--- |:--- |
| Dokončené |Úloha byla úspěšně dokončena. |
| Selhalo |Pro [grafické runbooky pracovních postupů Powershellu](automation-runbook-types.md), sada runbook se nepodařilo zkompilovat. Pro [Powershellový skript sady runbook](automation-runbook-types.md), sada runbook se nepodařilo spustit nebo provádění úlohy došlo k výjimce. |
| Se nezdařilo, čekání na prostředky |Úloha se nezdařila, protože bylo dosaženo [spravedlivé sdílení](#fair-share) omezit třikrát a spustit pokaždé, když ze stejného kontrolního bodu nebo od začátku runbooku. |
| Ve frontě |Úloha čeká pro prostředky služby Automation zpřístupnění pracovního procesu tak, aby jeho spuštění. |
| Spouštění |Úloha byla přiřazena k pracovnímu procesu a v systému probíhá její spouštění. |
| Obnovení |V systému probíhá obnovování dřív pozastavené úlohy. |
| Spuštěno |Úloha je spuštěna. |
| Spuštění, čekání na prostředky |Úloha byla uvolněna, protože bylo dosaženo [spravedlivé sdílení](#fair-share) limit. Bude pokračovat i za chvíli od svého posledního kontrolního bodu. |
| Zastaveno |Úloha se zastavila tímto uživatelem, než se provedl. |
| Zastavování |V systému Probíhá zastavování úlohy. |
| Pozastaveno |Úlohu pozastavil uživatel, systém nebo příkaz v runbooku. Úloha, která je pozastavena dá znova spustit a pokračovat od svého posledního kontrolního bodu nebo od začátku runbooku, pokud nemá kontrolní body. Sada runbook je pouze pozastaveno systémem, když dojde k výjimce. Ve výchozím nastavení, ErrorActionPreference nastavená na **pokračovat**, což znamená, že úloha běžel na chybu. Pokud je tato předvolba proměnné nastavená **Zastavit**, pak v případě chyby pozastaví úlohu. Platí pro [grafické runbooky pracovních postupů Powershellu](automation-runbook-types.md) pouze. |
| Pozastavování |Systém se pokouší pozastavit úlohu na žádost uživatele. Sada runbook dosažení následujícího kontrolního bodu předtím, než je možné ho pozastavit. Pokud už uplynulé svého posledního kontrolního bodu, pak dokončí předtím, než je možné ho pozastavit. Platí pro [grafické runbooky pracovních postupů Powershellu](automation-runbook-types.md) pouze. |

## <a name="viewing-job-status-from-the-azure-portal"></a>Zobrazení stavu úlohy na webu Azure Portal

Můžete zobrazit souhrnný stav všech úloh sady runbook nebo přejdete na podrobnosti o konkrétní runbooku na portálu Azure portal nebo pomocí konfigurace integrace nástroje pracovního prostoru Log Analytics předávat datové proudy úlohy stavu a úlohy runbooku. Další informace o integraci se službou Log Analytics najdete v tématu [předávání stavu úlohy a datové proudy úlohy ze služby Automation do Log Analytics](automation-manage-send-joblogs-log-analytics.md).

### <a name="automation-runbook-jobs-summary"></a>Souhrn úlohy runbooků služby Automation

Na pravé straně vybraný účet Automation, zobrazí se souhrn všech úloh runbooků pro vybraný účet Automation v části **statistiky úlohy** dlaždici.

![Dlaždice statistiky úlohy](./media/automation-runbook-execution/automation-account-job-status-summary.png).

Na této dlaždici se zobrazuje počet a grafické vyjádření stavu úlohy pro všechny úlohy provést.

Kliknutím na soubor představuje **úlohy** okno, které obsahuje souhrnný seznam všech úloh provedeny, se stavem, provádění úlohy a časy spuštění a dokončení.

![Okno úlohy účet služby Automation](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

Seznam úloh můžete filtrovat výběrem **filtrování úloh** a jsou filtrované na konkrétní sadu runbook, stav úloh, nebo z rozevíracího seznamu datový/časový rozsah má hledat.

![Filtr stavu úlohy](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Alternativně můžete zobrazit podrobnosti souhrnu úlohy pro konkrétní sadu runbook tak, že vyberete dané sady runbook z **sady Runbook** okna účtu Automation a pak vyberte **úlohy** dlaždici. To představuje **úlohy** okně, a z něj můžete kliknout na úlohu záznam, který chcete zobrazit podrobnosti a výstup.

![Okno úlohy účet služby Automation](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>Souhrn úlohy

Můžete zobrazit seznam všech úloh, které byly vytvořeny pro konkrétní sadu runbook a jejich aktuální stav. Můžete filtrovat tento seznam podle stavu úlohy a rozsahu dat poslední změny úlohy. Chcete-li zobrazit podrobné informace a výstup, klikněte na název úlohy. V podrobném zobrazení úlohy obsahuje hodnoty parametrů runbooku poskytnuté pro tuto úlohu.

Chcete-li zobrazit úlohy pro sady runbook můžete použít následující kroky.

1. Na webu Azure Portal, vyberte **automatizace** a potom vyberte název účtu Automation.
2. Z centra, vyberte **sady Runbook** a pak na **sady Runbook** okno Vybrat sadu runbook ze seznamu.
3. V okně pro vybranou sadu runbook, klikněte na tlačítko **úlohy** dlaždici.
4. Klikněte na jednu z úloh v seznamu a v okně Podrobnosti úlohy runbooku můžete zobrazit podrobnosti a výstup.

## <a name="retrieving-job-status-using-windows-powershell"></a>Načtení stavu úlohy pomocí prostředí Windows PowerShell

Můžete použít [Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx) můžete načíst úlohy vytvořené pro runbook a podrobnosti konkrétní úlohy. Pokud runbook spustíte s Windows Powershellem pomocí [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx), vrátí se výsledná úloha. Použití [Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx)výstupní výstupu úlohy.

Následující ukázkové příkazy z poslední úlohu ukázkového runbooku načíst a zobrazit její stav, hodnot poskytnutých parametrů runbooku a výstup z úlohy.

```azurepowershell-interactive
$job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

Následující příklad načte výstup pro konkrétní úlohu a vrátí všechny záznamy. V případě, že došlo k výjimce pro jeden ze záznamů, výjimka je zapsán místo hodnoty. To je užitečné, protože výjimky může poskytovat doplňující informace, které nemusejí být zaznamenány obvykle při výstupu.

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

Pokud chcete sdílení prostředků mezi všechny runbooky v cloudu, Azure Automation dočasně uvolní všechny úlohy za běhu pro tři hodiny. Během této doby úlohy pro [sady runbook pomocí prostředí PowerShell](automation-runbook-types.md#powershell-runbooks) se zastaví a nejsou ji restartovat. U úlohy zobrazí stav **Zastaveno**. Tento typ runbooku je vždy restartován od začátku nepodporují kontrolní body.

[Sady runbook pomocí pracovních postupů prostředí PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) nedojde k přerušení od jejich poslední [kontrolního bodu](https://docs.microsoft.com/system-center/sma/overview-powershell-workflows#bk_Checkpoints). Po spuštění tři hodiny, je pozastavené úlohy runbooku ve službě a její stav zobrazí **spuštěna, čekání na prostředky**. Jakmile izolovaném prostoru k dispozici, se automaticky restartuje sadu runbook služby Automation a probuzení z posledního kontrolního bodu. To je normální chování pracovního postupu Powershellu pro pozastavení a restartování. Pokud sada runbook znovu překročí tři hodiny modulu runtime, proces se opakuje, až třikrát. Za třetí restart, pokud je runbook ještě nebyla dokončena v tři hodiny, pak úlohy runbooku se nezdařila a stav úlohy zobrazuje **nebyl úspěšný, čekání na prostředky**. V takovém případě se zobrazí následující výjimku kvůli chybě.

*Úloha nemůže pokračovat se spuštěným protože opakovaně byl vyřazen z stejné kontrolního bodu. Ujistěte se prosím, že vaše sada Runbook neprovádí zdlouhavé činnosti bez zachování stavu.*

Toto je k ochraně služby z runbooky, které běží po neomezenou dobu bez dokončení, nejsou schopna provést do následujícího kontrolního bodu bez uvolňován znovu.

Pokud sada runbook nemá žádné kontrolní body nebo úloha nedosáhla první kontrolní bod před uvolňován, pak restartuje od začátku.

Pro dlouho běžící úlohy, doporučuje se použít [Hybrid Runbook Worker](automation-hrw-run-runbooks.md#job-behavior). Procesy hybrid Runbook Worker není omezena spravedlivé sdílení a nemají omezení můžete spustit na jak dlouho sady runbook.

Pokud použijete runbook pracovního postupu Powershellu v Azure, při vytváření sady runbook, měli byste zajistit, že čas ke spuštění všech aktivit mezi dvěma body obnovení není delší než tři hodiny. Budete muset přidání kontrolních bodů do sady runbook a ujistěte se, že nemá dosažení maximálního počtu 3 hodiny nebo rozdělte dlouhé běžící operace. Vaše sada runbook může například provádět reindex velké databáze SQL. Pokud tento jedné operace nedokončí v rámci limitu spravedlivé sdílení, je tato úloha byla uvolněna a spuštěno znovu od začátku. V takovém případě by měl rozdělte reindex operace do více kroků, jako je například Reindexace jedné tabulky v době a vložte kontrolní bod po každé operaci tak, aby úloha může pokračovat po poslední operaci dokončit.

## <a name="next-steps"></a>Další postup

* Další informace o různých metodách, které můžete použít ke spuštění sady runbook ve službě Azure Automation najdete v tématu [spuštění runbooku ve službě Azure Automation](automation-starting-a-runbook.md)
