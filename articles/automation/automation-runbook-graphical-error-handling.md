---
title: Zpracování chyb v grafických runboocích Azure Automation
description: Tento článek popisuje, jak implementovat logiku zpracování chyb v grafických runboocích Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 14112a9a9f64f20540a8f7e1d37cd31017238e59
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34192706"
---
# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Zpracování chyb v grafických runboocích Azure Automation

Klíčovým aspektem návrhu runbooků, na který je třeba pamatovat, je identifikace různých problémů, se kterými se runbook může setkat. Tyto problémy mohou zahrnovat úspěch, očekávané chybové stavy a neočekávané chybové podmínky.

Runbooky by měly zahrnovat zpracování chyb. K ověření výstupu aktivity nebo zpracování chyb v grafických runboocích můžete použít aktivitu s kódem PowerShellu, definovat podmíněnou logiku pro výstupní propojení aktivity nebo použít jinou metodu.          

Často se stává, že pokud v aktivitě runbooku dojde k neukončující chybě, zpracují se i následující aktivity bez ohledu na chybu. Chyba pravděpodobně vygeneruje výjimku, ale přesto je umožněno spuštění další aktivity. Takto je v PowerShellu navrženo zpracování chyb.    

Během provádění se mohou objevit dva typy chyb PowerShellu – ukončující nebo neukončující. Rozdíly mezi ukončujícími a neukončujícími chybami jsou následující:

* **Ukončující chyba:** závažná chyba během provádění, která úplně zastaví příkaz (nebo provádění skriptu). Příkladem jsou neexistující rutiny, chyby syntaxe znemožňující spuštění rutiny nebo jiné závažné chyby.

* **Neukončující chyba:** méně závažná chyba, která umožňuje pokračovat v provádění bez ohledu na selhání. Jedná se například o operační chyby, jako je nenalezení souboru nebo problémy s oprávněním.

Grafické runbooky Azure Automation byly vylepšeny o možnost zahrnovat zpracování chyb. Nyní můžete přepnout výjimky na neukončující chyby a vytvořit chybová propojení mezi aktivitami. Díky tomu může autor runbooku zachytávat chyby a spravovat očekávané i neočekávané situace.  

## <a name="when-to-use-error-handling"></a>Kdy použít zpracování chyb

Kdykoli nějaká důležitá aktivita vyvolá chybu nebo výjimku, je důležité zabránit zpracování další aktivity v runbooku a odpovídajícím způsobem chybu zpracovat. To je obzvláště důležité v případě, kdy na vašich runboocích závisí podnikový proces nebo proces servisních operací.

Pro každou aktivitu, která může vést k chybě, může autor runbooku přidat chybové propojení odkazující na jinou aktivitu. Cílová aktivita může být libovolného typu, včetně aktivity s kódem, aktivity vyvolávající rutinu, aktivity vyvolávající jiný runbook atd.

Kromě toho může cílová aktivita také obsahovat odchozí propojení. Může se jednat o běžná propojení nebo chybová propojení. To znamená, že autor runbooku může implementovat komplexní logiku zpracování chyb bez nutnosti uchýlit se k aktivitě s kódem. Doporučeným postupem je vytvoření vyhrazeného runbooku s běžnými funkcemi pro zpracování chyb, ale není to nutné. Logika zpracování chyb v aktivitě s kódem PowerShellu není jedinou možností.  

Představte si například runbook, který se pokusí spustit virtuální počítač a nainstalovat v něm aplikaci. Pokud se virtuální počítač nespustí správně, provede runbook dvě akce:

1. Odešle oznámení o tomto problému.
2. Spustí jiný runbook, který automaticky zřídí nový virtuální počítač.

Jedním z řešení je vytvořit chybové propojení odkazující na aktivitu, která zpracuje krok 1. Můžete například propojit rutinu **Write-Warning** s aktivitou pro krok 2, například rutinou **Start-AzureRmAutomationRunbook**.

Toto chování můžete také zobecnit pro použití v mnoha runboocích tím, že obě aktivity umístíte do samostatného runbooku pro zpracování chyb podle výše navrhovaných pokynů. Před voláním tohoto runbooku pro zpracování chyb můžete z dat v původním runbooku vytvořit vlastní zprávu a předat ji jako parametr do runbooku pro zpracování chyb.

## <a name="how-to-use-error-handling"></a>Jak použít zpracování chyb

V nastavení konfigurace každé aktivity je možnost přepnout výjimky na neukončující chyby. Standardně je toto nastavení zakázáno. Doporučujeme toto nastavení povolit u všech aktivit, ve kterých chcete zpracovávat chyby.  

Povolením této konfigurace zajistíte, že se s ukončujícími i neukončujícími chybami v aktivitě bude zacházet jako s neukončujícími chybami, které můžete zpracovat pomocí chybového propojení.  

Po konfiguraci tohoto nastavení vytvoříte aktivitu, která bude zpracovávat chyby. Pokud v aktivitě dojde k jakékoli chybě, použijí se odchozí chybová propojení a nikoli běžná propojení; běžná propojení se nepoužijí ani v případě, že aktivita vygeneruje kromě chyby i běžný výstup.<br><br> ![Příklad chybového propojení v runbooku Automation](media/automation-runbook-graphical-error-handling/error-link-example.png)

V následujícím příkladu runbook načte proměnnou obsahující název virtuálního počítače. Následně se pokusí spustit virtuální počítač pomocí další aktivity.<br><br> ![Příklad zpracování chyb v runbooku Automation](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

Aktivity **Get-AutomationVariable** a **Start-AzureRmVm** jsou nakonfigurované, aby převáděly výjimky na chyby. Pokud dojde k problémům se získáním proměnné nebo spuštěním virtuálního počítače, vygenerují se chyby.<br><br> ![Nastavení aktivity pro zpracování chyb v runbooku Automation](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)

Chybová propojení vedou z těchto aktivit do jediné aktivity **Error Management** (aktivita s kódem). V této aktivitě je nakonfigurovaný jednoduchý výraz PowerShellu, který pomocí klíčového slova *Throw* zastaví zpracování a prostřednictvím *$Error.Exception.Message* získá zprávu s popisem aktuální výjimky.<br><br> ![Příklad kódu pro zpracování chyb v runbooku Automation](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)


## <a name="next-steps"></a>Další postup

* Další informace o propojeních a typech propojení v grafických runboocích najdete v tématu [Grafické vytváření obsahu v Azure Automation](automation-graphical-authoring-intro.md#links-and-workflow).

* Další informace o spouštění runbooků, postupy při monitorování úloh runbooků a další technické podrobnosti najdete v článku [Sledování úlohy runbooku](automation-runbook-execution.md).
