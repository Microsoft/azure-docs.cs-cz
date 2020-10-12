---
title: Zpracování chyb v Azure Automation grafické Runbooky
description: Tento článek popisuje, jak implementovat logiku zpracování chyb v grafických sadách Runbook.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 8af64f2189625bcff5271855d6c0102551d1a535
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185955"
---
# <a name="handle-errors-in-graphical-runbooks"></a>Zpracování chyb v grafických runboocích

Klíčovým návrhem, který je třeba zvážit pro Azure Automation Grafická sada Runbook, je identifikace problémů, které může sada Runbook způsobit během provádění. Tyto problémy mohou zahrnovat úspěch, očekávané chybové stavy a neočekávané chybové podmínky.

Pokud dojde k neukončující chybě, která se vyskytuje v aktivitě sady Runbook, aplikace Windows PowerShell zpracovává aktivitu zpracováním jakékoli aktivity, která následuje, bez ohledu na chybu. Chyba pravděpodobně vygeneruje výjimku, ale přesto je umožněno spuštění další aktivity.

Váš grafický Runbook by měl zahrnovat kód pro zpracování chyb, který by mohl řešit problémy s prováděním. Chcete-li ověřit výstup aktivity nebo zpracovat chybu, můžete použít aktivitu kódu prostředí PowerShell, definovat podmíněný Logic na výstupním odkazu aktivity nebo použít jinou metodu.

Grafické runbooky Azure Automation byly vylepšeny o možnost zahrnovat zpracování chyb. Nyní můžete přepnout výjimky na neukončující chyby a vytvořit chybová propojení mezi aktivitami. Vylepšený proces umožňuje Runbooku zachytit chyby a spravovat realizované nebo neočekávané podmínky. 

## <a name="powershell-error-types"></a>Typy chyb prostředí PowerShell

Typy chyb prostředí PowerShell, které mohou nastat během provádění sady Runbook, ukončující chyby a neukončující chyby.
 
### <a name="terminating-error"></a>Ukončující chyba

Ukončující chyba je závažná chyba během provádění, která zablokuje úplné spuštění příkazu nebo skriptu. Mezi příklady patří neexistující rutiny, chyby syntaxe, které brání spuštění rutiny a další závažné chyby.

### <a name="non-terminating-error"></a>Neukončující chyba

Neukončující chyba je nezávažná chyba, která umožňuje pokračovat v provádění bez ohledu na chybovou podmínku. Mezi příklady patří provozní chyby, jako jsou chyby nenalezení souborů a problémy s oprávněními.

## <a name="when-to-use-error-handling"></a>Kdy použít zpracování chyb

V sadě Runbook použijte zpracování chyb, když kritická aktivita vyvolá chybu nebo výjimku. Je důležité zabránit zpracování další aktivity v sadě Runbook a odpovídajícím způsobem zpracovat chybu. Zpracování této chyby je obzvláště důležité, pokud vaše Runbooky podporují proces podnikání nebo provozu.

## <a name="add-error-links"></a>Přidat odkazy na chyby

Pro každou aktivitu, která může vytvořit chybu, můžete přidat chybové propojení odkazující na jakoukoli jinou aktivitu. Cílová aktivita může být libovolného typu, včetně aktivity kódu, vyvolání rutiny, vyvolání jiné sady Runbook atd. Cílová aktivita může mít také odchozí propojení, a to buď pravidelné, nebo chybové odkazy. Odkazy umožňují sadě Runbook implementovat komplexní logiku zpracování chyb bez nutnosti používat aktivitu kódu.

Doporučeným postupem je vytvoření vyhrazené sady Runbook pro zpracování chyb s běžnými funkcemi, ale tento postup není povinný. Představte si třeba sadu Runbook, která se pokusí spustit virtuální počítač a nainstalovat na něj aplikaci. Pokud se virtuální počítač nespustí správně, postupujte takto:

1. Pošle oznámení o tomto problému.
2. Spustí další sadu Runbook, která místo toho automaticky zřídí nový virtuální počítač.

Jedním z řešení je, aby v Runbooku bylo chybné propojení odkazující na aktivitu, která zpracovává krok 1. Sada Runbook může například připojit `Write-Warning` rutinu k aktivitě pro krok 2, například rutinu [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0) .

Toto chování můžete také zobecnit pro použití v mnoha sadách Runbook tím, že tyto dvě aktivity vložíte do samostatného Runbooku pro zpracování chyb. Předtím, než původní sada Runbook zavolá tuto sadu Runbook pro zpracování chyb, může vytvořit vlastní zprávu z jejich dat a pak ji předat jako parametr do sady Runbook pro zpracování chyb.

## <a name="turn-exceptions-into-non-terminating-errors"></a>Vypnout výjimky na neukončující chyby

Každá aktivita v sadě Runbook má nastavení konfigurace, které zapíná výjimky na neukončující chyby. Standardně je toto nastavení zakázáno. Toto nastavení doporučujeme povolit u všech aktivit, kde sada Runbook zpracovává chyby. Toto nastavení zajišťuje, že sada Runbook zpracovává ukončovací i neukončující chyby v aktivitě jako neukončující chyby pomocí odkazu na chybu.  

Po povolení nastavení konfigurace budou mít vaše sada Runbook vytvořenou aktivitu, která zpracovává chybu. Pokud aktivita vyvolá jakoukoli chybu, následují odchozí odkazy na chyby. Nejsou následovány běžné odkazy, a to ani v případě, že aktivita vytváří i pravidelný výstup.<br><br> ![Příklad chybového propojení v runbooku Automation](media/automation-runbook-graphical-error-handling/error-link-example.png)

V následujícím příkladu sada Runbook načte proměnnou obsahující název virtuálního počítače. Pak se pokusí spustit virtuální počítač s další aktivitou.<br><br> ![Příklad zpracování chyb v Runbooku Automation](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

`Get-AutomationVariable`Aktivita a rutina [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) jsou nakonfigurovány na převod výjimek na chyby. Pokud dojde k potížím s získáním proměnné nebo spuštěním virtuálního počítače, kód generuje chyby.<br><br> ![Nastavení aktivity pro zpracování chyb v Runbooku Automation ](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)

Chyby odkazují z těchto aktivit na aktivitu jediného `error management` kódu. Tato aktivita je nakonfigurována pomocí jednoduchého výrazu prostředí PowerShell, který používá `throw` klíčové slovo k zastavení zpracování, a `$Error.Exception.Message` k získání zprávy s popisem aktuální výjimky.<br><br> ![Příklad kódu pro zpracování chyb v Runbooku Automation](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)

## <a name="next-steps"></a>Další kroky

* Informace o řešení chyb grafického Runbooku najdete v tématu [řešení potíží](troubleshoot/runbooks.md)se sadou Runbook.
