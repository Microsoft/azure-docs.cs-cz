---
title: Zpracování chyb v grafických runboocích Azure Automation
description: Tento článek popisuje, jak implementovat logiku zpracování chyb v grafických runboocích Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: f1aa605b3e6f32b260ea4a9eee9c056277fcd12d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367070"
---
# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Zpracování chyb v grafických runboocích Azure Automation

Princip klíčového návrhu, který je třeba zvážit pro grafickou runbook Azure Automation, je identifikace problémů, které může dojít při spuštění sady Runbook. Tyto problémy mohou zahrnovat úspěch, očekávané chybové stavy a neočekávané chybové podmínky.

Často pokud dojde k neukončující chybě, ke které dochází s aktivitou runbooku, prostředí Windows PowerShell zpracovává aktivitu zpracováním jakékoli aktivity, která následuje, bez ohledu na chybu. Chyba pravděpodobně vygeneruje výjimku, ale přesto je umožněno spuštění další aktivity.

Grafické runbook by měl obsahovat kód zpracování chyb pro řešení problémů s prováděním. Chcete-li ověřit výstup aktivity nebo zpracovat chybu, můžete použít aktivitu kódu prostředí PowerShell, definovat podmíněnou logiku na výstupní matné vazbě aktivity nebo použít jinou metodu.

Grafické runbooky Azure Automation byly vylepšeny o možnost zahrnovat zpracování chyb. Nyní můžete přepnout výjimky na neukončující chyby a vytvořit chybová propojení mezi aktivitami. Vylepšený proces umožňuje vaší runbooku zachytit chyby a spravovat realizované nebo neočekávané podmínky. 

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md).

## <a name="powershell-error-types"></a>Typy chyb prostředí PowerShell

Typy chyb prostředí PowerShell, ke kterým může dojít během spuštění runbooku, jsou ukončující chyby a neukončující chyby.
 
### <a name="terminating-error"></a>Ukončující chyba

Ukončující chyba je závažná chyba během provádění, která zcela zastaví spuštění příkazu nebo skriptu. Mezi příklady patří neexistující rutiny, syntaktické chyby, které brání spuštění rutiny, a další závažné chyby.

### <a name="non-terminating-error"></a>Neukončující chyba

Neukončující chyba je nezávažná chyba, která umožňuje pokračovat v provádění i přes chybový stav. Příklady zahrnují provozní chyby, jako jsou chyby souboru, které nebyly nalezeny, a problémy s oprávněními.

## <a name="when-to-use-error-handling"></a>Kdy použít zpracování chyb

Použití zpracování chyb v aplikaci Runbook při kritické aktivity vyvolá chybu nebo výjimku. Je důležité zabránit zpracování další aktivity v runbooku a správně zpracovat chybu. Zpracování chyby je obzvláště důležité, když vaše sady Runbook podporují obchodní nebo servisní operace.

Pro každou aktivitu, která může způsobit chybu, můžete přidat odkaz na chybu odkaz ukazující na jinou aktivitu. Cílová aktivita může být libovolného typu, včetně aktivity kódu, vyvolání rutiny, vyvolání jiného runbooku a tak dále. Cílová aktivita může mít také odchozí odkazy, a to buď pravidelné nebo chybové odkazy. Odkazy umožňují runbook implementovat komplexní logiku zpracování chyb bez uchýlení se k aktivitě kódu.

Doporučeným postupem je vytvoření vyhrazeného runbooku pro zpracování chyb se společnými funkcemi, ale tento postup není povinný. Zvažte například runbook, který se pokusí spustit virtuální počítač a nainstalovat do něj aplikaci. Pokud se virtuální virtuální zařízení nespustí správně, tak:

1. Odešle oznámení o tomto problému.
2. Spustí jiný runbook, který místo toho automaticky zřídí nový virtuální ms.

Jedním z řešení je mít odkaz na chybu v runbooku ukazující na aktivitu, která zpracovává krok jedna. Například runbook můžete připojit `Write-Warning` rutinu k aktivitě pro krok dva, jako je například [Rutina Start-AzAutomationRunbook.](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0)

Toto chování můžete také zobecnit pro použití v mnoha sadách Runbook tím, že tyto dvě aktivity vložíte do samostatné sady Runbook pro zpracování chyb. Před původní runbook volá tuto chybu zpracování runbook, může vytvořit vlastní zprávu z jeho dat a pak ji předat jako parametr pro zpracování chyb runbook.

## <a name="how-to-use-error-handling"></a>Jak použít zpracování chyb

Každá aktivita v aplikaci Runbook má nastavení konfigurace, které změní výjimky na neukončující chyby. Standardně je toto nastavení zakázáno. Doporučujeme povolit toto nastavení pro všechny aktivity, kde vaše runbook zpracovává chyby. Toto nastavení zajišťuje, že runbook zpracovává ukončující i neukončující chyby v aktivitě jako neukončující chyby pomocí propojení chyb.  

Po povolení nastavení konfigurace, aby vaše runbook vytvořit aktivitu, která zpracovává chybu. Pokud aktivita způsobí nějakou chybu, budou sledovány odchozí chybové odkazy. Pravidelné odkazy nejsou dodržovány, a to i v případě, že aktivita vytváří pravidelný výstup stejně.<br><br> ![Příklad chybového propojení v runbooku Automation](media/automation-runbook-graphical-error-handling/error-link-example.png)

V následujícím příkladu načte runbook proměnnou, která obsahuje název počítače virtuálního počítače. Potom se pokusí spustit virtuální ho s další aktivitou.<br><br> ![Příklad zpracování chyb runbooku automatizace](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

Aktivita `Get-AutomationVariable` a [rutina Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) jsou nakonfigurovány tak, aby převáděly výjimky na chyby. Pokud existují problémy se získáním proměnné nebo spuštěním virtuálního počítače, kód generuje chyby.<br><br> ![Nastavení](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)aktivit zpracování chyb runbooku automatizace .

Chybové odkazy toku z `error management` těchto aktivit na jeden kód aktivity. Tato aktivita je nakonfigurován s jednoduchým výrazem Prostředí PowerShell, který používá `throw` klíčové slovo k zastavení zpracování, spolu s `$Error.Exception.Message` získat zprávu, která popisuje aktuální výjimku.<br><br> ![Příklad kódu zpracování chyb runbooku automatizace](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)

## <a name="next-steps"></a>Další kroky

* Další informace o propojeních a typech propojení v grafických runboocích najdete v tématu [Grafické vytváření obsahu v Azure Automation](automation-graphical-authoring-intro.md#links-and-workflow).

* Další informace o spouštění runbooků, monitorování úloh sady Runbook a další technické podrobnosti najdete [v tématu spuštění sady Runbook v Azure Automation](automation-runbook-execution.md).