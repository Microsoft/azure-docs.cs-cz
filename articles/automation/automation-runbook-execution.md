---
title: Spuštění sady Runbook ve službě Azure Automation
description: Popisuje podrobnosti o způsobu zpracování sady runbook ve službě Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 05/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ff58e22f8b9b837ec272cd2cd6193da80a7b718e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="runbook-execution-in-azure-automation"></a>Spuštění sady Runbook ve službě Azure Automation

Při spuštění sady runbook ve službě Azure Automation se vytvoří úloha. Úloha je instance jednoho spuštění sady runbook. Ke spuštění Každá úloha je přiřazen pracovního procesu automatizace Azure. Když zaměstnanci jsou sdíleny více účtů Azure, úlohy z různých účtů Automation jsou izolované od sebe navzájem. Můžete mít není řídit, přes které worker služby žádost o úlohu. Jedné sady runbook může mít několik úloh spuštěných současně. Prostředí pro spuštění úloh ze stejného účtu Automation může být znovu použita. Při zobrazení seznamu sad runbook na portálu Azure, zobrazí stav všech úloh, které byly zahájeny pro každou sadu runbook. Chcete-li sledovat stav každého z nich můžete zobrazit seznam úloh pro každou sadu runbook. Popis stavy různé úlohy [stavy úlohy](#job-statuses).

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../includes/gdpr-dsr-and-stp-note.md)]

Následující diagram znázorňuje životní cyklus úlohy runbooku pro [grafické runbooky](automation-runbook-types.md#graphical-runbooks) a [runbooky pracovních postupů Powershellu](automation-runbook-types.md#powershell-workflow-runbooks).

![Stavy úlohy – pracovní postup prostředí PowerShell](./media/automation-runbook-execution/job-statuses.png)

Následující diagram znázorňuje životní cyklus úlohy runbooku pro [Powershellové runbooky](automation-runbook-types.md#powershell-runbooks).

![Stavy úlohy - skript prostředí PowerShell](./media/automation-runbook-execution/job-statuses-script.png)

Vaše úlohy mají přístup k prostředkům Azure tak, že připojení k předplatnému Azure. Mají pouze přístup k prostředkům ve vašem datovém centru Pokud tyto prostředky jsou přístupné z veřejného cloudu.

## <a name="job-statuses"></a>Stavy úlohy

Následující tabulka popisuje různé stavy, které jsou u úlohy nastat.

| Status | Popis |
|:--- |:--- |
| Dokončené |Úloha byla úspěšně dokončena. |
| Selhalo |Pro [pracovní postup prostředí PowerShell a grafický runbook](automation-runbook-types.md), sada runbook se nezdařilo zkompilovat. Pro [skript prostředí PowerShell runbooky](automation-runbook-types.md), sada runbook se nepodařilo spustit nebo při provádění úlohy došlo k výjimce. |
| Chyba, čekání na prostředky |Úloha se nezdařila, protože bylo dosaženo [úloha dostatečný podíl](#fair-share) omezit třikrát a spustit z stejné kontrolního bodu nebo od začátku runbooku pokaždé, když. |
| Ve frontě |Úloha čeká prostředky pracovního procesu automatizace připojí k dispozici, tak, aby jeho spuštění. |
| Spouštění |Úloha byla přiřazena k pracovnímu procesu a v systému probíhá její spouštění. |
| Obnovení |V systému probíhá obnovování úlohy po bylo pozastaveno. |
| Spuštěno |Úloha je spuštěna. |
| Spuštění, čekání na prostředky |Úlohy byl odpojen, protože bylo dosaženo [úloha dostatečný podíl](#fair-share) limit. Obnoví krátce od svého posledního kontrolního bodu. |
| Zastaveno |Úloha byla zastavena uživatelem před dokončením. |
| Zastavování |V systému Probíhá zastavování úlohy. |
| Pozastaveno |Úlohu pozastavil uživatel, systém nebo příkaz v runbooku. Úlohu, která je pozastavená lze spustit znovu a pokračovat od svého posledního kontrolního bodu nebo od začátku runbooku, pokud nemá kontrolní body. Sada runbook je pouze pozastaveno systémem, když dojde k výjimce. Ve výchozím nastavení, je hodnotu ErrorActionPreference **pokračovat**, což znamená, že úloha neustále běží na chybu. Pokud je tato předvolba proměnné nastavená **Zastavit**, pak se pozastaví úlohu na chybu. Platí pro [pracovní postup prostředí PowerShell a grafický runbook](automation-runbook-types.md) pouze. |
| Pozastavování |Systém se pokouší pozastavit úlohu na žádost uživatele. Sada runbook dosažení následujícího kontrolního bodu předtím, než se může pozastavit. Pokud již uplynul svého posledního kontrolního bodu, pak dokončí předtím, než se může pozastavit. Platí pro [pracovní postup prostředí PowerShell a grafický runbook](automation-runbook-types.md) pouze. |

## <a name="viewing-job-status-from-the-azure-portal"></a>Zobrazení stavu úlohy z portálu Azure

Můžete zobrazit souhrnnou stav všechny úlohy sady runbook nebo podrobnostem úlohu konkrétní sady runbook na portálu Azure nebo konfigurace integrace s pracovní prostor analýzy protokolů předávat datové proudy úlohy stavu a úlohu runbook. Další informace o integraci s analýzy protokolů najdete v tématu [předávání zpráv o stavu úlohy a datové proudy úlohy ze služby Automation k analýze protokolů](automation-manage-send-joblogs-log-analytics.md).

### <a name="automation-runbook-jobs-summary"></a>Souhrn úlohy sady runbook automatizace

Na pravé straně vybrané účtu Automation, můžete zobrazit souhrn všechny úlohy sady runbook pro vybraný účet automatizace v rámci **Statistika projektu** dlaždici.

![Dlaždice úlohy statistiky](./media/automation-runbook-execution/automation-account-job-status-summary.png).

Tuto dlaždici zobrazí počet a grafické vyjádření stavu úlohy pro všechny úlohy provést.

Kliknutím na dlaždici uvede **úlohy** okno, které obsahuje souhrnný seznam všech úloh provést, se stavem, provádění úlohy a spuštění a dokončení.

![Okno úlohy účet Automation.](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

Můžete filtrovat seznam úloh tak, že vyberete **filtrujte úlohy** a filtr na konkrétní sady runbook, stav úlohy, nebo z rozevíracího seznamu, pro vyhledávání v rámci rozsahu datum a čas.

![Stav úlohy filtru](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Alternativně můžete zobrazit podrobnosti souhrnu úlohy pro konkrétní sadu runbook tak, že vyberete dané sady runbook z **Runbooky** okno v účtu Automation a potom vyberte **úlohy** dlaždici. To představuje **úlohy** okně a z ní můžete kliknutím na záznam úlohy zobrazíte její podrobnosti a výstup.

![Okno úlohy účet Automation.](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>Souhrn úlohy

Můžete zobrazit seznam všech úloh, které byly vytvořeny pro konkrétní runbook a jejich aktuální stav. Můžete filtrovat tento seznam podle stavu úlohy a rozsahu dat poslední změny úlohy. Chcete-li zobrazit její podrobné informace a výstup, klikněte na název úlohy. V podrobném zobrazení úlohy obsahuje hodnoty parametrů runbooku poskytnuté pro tuto úlohu.

Chcete-li zobrazit úlohy pro sady runbook můžete použít následující kroky.

1. Na portálu Azure vyberte **automatizace** a pak vyberte název účtu Automation.
2. Z centra, vyberte **sady Runbook** a pak na **Runbooky** okno Vybrat sadu runbook ze seznamu.
3. V okně pro vybranou sadu runbook, klikněte na **úlohy** dlaždici.
4. Klikněte na jednu z úloh v seznamu a v okně podrobností úlohy runbooku můžete zobrazit podrobnosti a výstup.

## <a name="retrieving-job-status-using-windows-powershell"></a>Načtení stavu úlohy pomocí prostředí Windows PowerShell

Můžete použít [Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx) načíst úlohy vytvořené pro runbook a podrobnosti konkrétní úlohy. Pokud runbook spustíte pomocí prostředí Windows PowerShell [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx), a vrátí se výsledná úloha. Použití [Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx)výstupu výstupu úlohy.

Následující vzorové příkazy načíst poslední úlohu ukázkového runbooku a zobrazit její stav, hodnoty pro parametry runbooku a výstup z úlohy.

```azurepowershell-interactive
$job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

Následující příklad načte výstup pro konkrétní úlohy a vrátí každý záznam. V případě, že došlo k výjimce pro jeden ze záznamů, výjimka je zapsána namísto hodnoty. To je užitečné, jako výjimky může poskytovat doplňující informace, které nemusí být do protokolu obvykle při výstupu.

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

## <a name="get-details-from-activity-log"></a>Získat informace z protokolu aktivit

Další podrobnosti, jako je osoba nebo účet, který spustil sadu runbook mohou být načteny z protokolu aktivit pro účet služby automation. Následující příklad PowerShell poskytuje poslední uživatele k spuštění sady runbook dotyčném:

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$rg = "ResourceGroup01"
$AutomationAccount = "MyAutomationAccount"
$RunbookName = "Test-Runbook"
$JobResourceID = "/subscriptions/$subid/resourcegroups/$rg/providers/Microsoft.Automation/automationAccounts/$AutomationAccount/jobs"

Get-AzureRmLog -ResourceId $JobResourceID -MaxRecord 1 | Select Caller
```

## <a name="fair-share"></a>Úloha dostatečný podíl

Aby bylo možné sdílet prostředky se všechny sady runbook v cloudu, bude Azure Automation dočasně uvolnit všechny úlohy, až po spuštění tři hodiny. Během této doby úlohy pro [sady runbook pomocí prostředí PowerShell](automation-runbook-types.md#powershell-runbooks) se zastaví a nejsou ji restartovat. Zobrazí stav úlohy **Zastaveno**. Tento typ runbooku se vždy restartuje od začátku, vzhledem k tomu, že nepodporují kontrolní body.

[Založené na prostředí PowerShell pracovního postupu sady runbook](automation-runbook-types.md#powershell-workflow-runbooks) nedojde k přerušení od jejich poslední [kontrolního bodu](https://docs.microsoft.com/system-center/sma/overview-powershell-workflows#bk_Checkpoints). Po spuštění tři hodiny, bude úloha sady runbook pozastaví služba a její stav ukazuje **spuštěna, čekání na prostředky**. Jakmile izolovaném prostoru k dispozici, se automaticky restartuje runbooku služby Automation a obnoví z posledního kontrolního bodu. To je normální chování pracovního postupu Powershellu pozastavit nebo restartovat. Pokud sada runbook znovu překročí tři hodiny modulu runtime, postup se opakuje, až třikrát. Po třetím restartování, pokud sada runbook ještě nebyla dokončena v tři hodiny, pak úloha sady runbook se nezdařilo a stav úlohy zobrazuje **se nezdařilo, čekání na prostředky**. V takovém případě se zobrazí následující výjimky s chybou.

*Úlohu nelze pokračovat, systémem, protože bylo opakovaně vyloučeno z stejné kontrolního bodu. Zkontrolujte prosím, že vaše sada Runbook neprovádí bez zachování stavu náročná operace.*

Toto je chránit službu z runbooky, které běží hodně dlouho bez dokončení, protože nejsou možné provádět další kontrolního bodu bez odpojení znovu.

Pokud sada runbook nemá žádné kontrolní body nebo úlohy nedosáhla první kontrolní bod před odpojení, pak znovu od začátku.

Při vytváření sady runbook, měli byste zajistit, že dobu pro spuštění všech aktivit mezi dvěma kontrolních bodů není delší než 3 hodiny. Budete muset přidat kontrolní body do runbooku zajistit, že není dosažení maximálního počtu 3 hodiny nebo rozdělit dlouho spuštěná operace. Vaše sada runbook může například provádět nové indexování velké databáze SQL. Pokud tento jedné operace neskončí v rámci limitu úloha dostatečný podíl, je úloha uvolněna a spuštěno znovu od začátku. V takovém případě by měl rozdělit operaci nové indexování do více kroků, například novou indexaci jedna tabulka současně a vložit kontrolní bod po každé operaci, aby úloha mohly pokračovat po poslední operaci dokončit.

## <a name="next-steps"></a>Další postup

* Další informace o různých metod, které můžete použít ke spuštění sady runbook ve službě Azure Automation najdete v tématu [spuštění sady runbook ve službě Azure Automation](automation-starting-a-runbook.md)
