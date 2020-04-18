---
title: Řešení potíží se změnami na virtuálním počítači Azure | Dokumentace Microsoftu
description: Potíže se změnami na virtuálním počítači Azure můžete řešit pomocí řešení Change Tracking.
services: automation
ms.subservice: change-inventory-management
keywords: change, tracking, automation
ms.date: 12/05/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 89f5e00c75b6b85c9a14de02504136907cde62b5
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604693"
---
# <a name="troubleshoot-changes-in-your-environment"></a>Řešení potíží se změnami ve vašem prostředí

V tomto kurzu zjistíte, jak řešit potíže se změnami na virtuálním počítači Azure. Povolením funkce Sledování změn můžete sledovat změny v softwaru, souborech, daemonech Linuxu, službách systému Windows a klíčích registru systému Windows v počítačích.
Identifikace těchto změn konfigurace vám může pomoct přesně určit provozní problémy v celém prostředí.

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Připojení virtuálního počítače k řešení Change Tracking a Inventory
> * Prohledávání protokolů změn pro zastavené služby
> * Konfigurace sledování změn
> * Povolení připojení protokolu aktivit
> * Aktivace události
> * Zobrazení změn
> * Konfigurace upozornění

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](automation-offering-get-started.md), který bude obsahovat sledovací proces, runbooky akcí a úlohu sledovacího procesu.
* [Virtuální počítač](../virtual-machines/windows/quick-create-portal.md) pro připojení.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Povolení řešení Change Tracking a Inventory

Nejprve je třeba povolit sledování změn a inventář pro váš virtuální počítač pro tento kurz. Pokud jste už dříve pro virtuální počítač povolili jiné řešení automatizace, tento krok není nezbytný.

1. V levé nabídce vyberte **Virtuální počítače** a vyberte virtuální počítač ze seznamu.
1. V levé nabídce vyberte **položku Zásoby v** části **Operace**. Otevře se stránka Zásoby.

![Povolit změnu](./media/automation-tutorial-troubleshoot-changes/enableinventory.png)

Nakonfigurujte umístění, pracovní prostor analýzy protokolů a účet Automatizace, který chcete použít, a klepněte na tlačítko **Povolit**. Pokud se pole zobrazují šedě, znamená to, že pro daný virtuální počítač je povolené jiné řešení automatizace a musí se použít stejný pracovní prostor a účet Automation.

Pracovní prostor [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) slouží ke shromažďování dat generovaných funkcemi a službami, jako je řešení Inventory.
Tento pracovní prostor poskytuje možnost kontroly a analýzy dat z několika zdrojů na jednom místě.

Během registrace se virtuální počítač zřizován s agentem Log Analytics pro Windows a hybridním pracovníkem runbooku.
Agent se používá ke komunikaci s virtuálním počítačem a získání informací o nainstalovaném softwaru.

Povolení řešení může trvat až 15 minut. Během této doby byste neměli zavírat okno prohlížeče.
Po povolení řešení se informace o nainstalovaném softwaru a změnách na virtuálním počítači točtou do protokolů Azure Monitoru.
Zpřístupnění dat pro analýzu může trvat 30 minut až 6 hodin.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="using-change-tracking-in-azure-monitor-logs"></a>Použití sledování změn v protokolech Azure Monitoru

Sledování změn generuje data protokolu, která se odesílá do protokolů Azure Monitor.
Chcete-li prohledávat protokoly spuštěním dotazů, vyberte **Log Analytics** v horní části stránky Sledování změn.
Data sledování změn jsou `ConfigurationChange`uložena pod typem .
Následující ukázka dotazu Log Analytics vrátí všechny zastavené služby systému Windows.

```loganalytics
ConfigurationChange
| where ConfigChangeType == "WindowsServices" and SvcState == "Stopped"
```

Další informace o spouštění a prohledávání souborů protokolu v protokolech Azure Monitoru najdete v [tématu protokoly Azure Monitor .](../azure-monitor/log-query/log-query-overview.md)

## <a name="configure-change-tracking"></a>Konfigurace řešení Change Tracking

Change Tracking poskytuje možnost sledovat změny konfigurace na vašem virtuálním počítači. Následující kroky ukazují, jak nakonfigurovat sledování klíčů registru a souborů.

Pokud chcete zvolit, jaké soubory a klíče registru se mají shromažďovat a sledovat, vyberte **Upravit nastavení** v horní části stránky Change Tracking.

> [!NOTE]
> Řešení Inventory i Change Tracking používají stejná nastavení shromažďování a nastavení se konfigurují na úrovni pracovního prostoru.

Na stránce Konfigurace pracovního prostoru přidejte klíče registru systému Windows, soubory systému Windows nebo soubory Linuxu, které chcete sledovat, jak je uvedeno v následujících třech částech.

### <a name="add-a-windows-registry-key"></a>Přidání klíče registru systému Windows

1. Na kartě **Registr systému Windows** vyberte **Přidat**. 

1. Na stránce Přidat registr systému Windows pro sledování změn zadejte informace o klíči ke sledování a klepněte na tlačítko **Uložit.**

|Vlastnost  |Popis  |
|---------|---------|
|Povoleno     | Určuje, jestli se nastavení používá.        |
|Název položky     | Popisný název souboru, který se má sledovat        |
|Skupina     | Název skupiny pro logické seskupení souborů        |
|Klíč registru systému Windows   | Cesta, ve které se má soubor hledat. Například: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup      |

### <a name="add-a-windows-file"></a>Přidání souboru Windows

1. Na kartě **Soubory Windows** vyberte **Přidat**. 

1. Na stránce Přidat soubor systému Windows pro sledování změn zadejte informace o souboru nebo adresáři, který chcete sledovat, a klepněte na **tlačítko Uložit.**

|Vlastnost  |Popis  |
|---------|---------|
|Povoleno     | Určuje, jestli se nastavení používá.        |
|Název položky     | Popisný název souboru, který se má sledovat        |
|Skupina     | Název skupiny pro logické seskupení souborů        |
|Zadat cestu     | Cesta, ve které se má soubor hledat, například: c:\temp\\\*.txt.<br>Můžete použít také proměnnou prostředí, například %winDir%\System32\\\*.*.         |
|Rekurze     | Určuje, jestli se při hledání položky, která se má sledovat, používá rekurze.        |
|Nahrát obsah souboru pro všechna nastavení| Zapne nebo vypne u sledovaných změn nahrávání obsahu souboru. Dostupné možnosti: **True** nebo **False**.|

### <a name="add-a-linux-file"></a>Přidání souboru Linuxu

1. Na kartě **Soubory Linuxu** vyberte **Přidat**. 

1. Na stránce Přidat linuxový soubor pro sledování změn zadejte informace o souboru nebo adresáři, který chcete sledovat, a klepněte na tlačítko **Uložit**.

|Vlastnost  |Popis  |
|---------|---------|
|Povoleno     | Určuje, jestli se nastavení používá.        |
|Název položky     | Popisný název souboru, který se má sledovat        |
|Skupina     | Název skupiny pro logické seskupení souborů        |
|Zadat cestu     | Cesta, ve které se má soubor hledat. Například: /etc/*.conf       |
|Typ cesty     | Typ položky, která se má sledovat. Možné hodnoty jsou Soubor a Adresář.        |
|Rekurze     | Určuje, jestli se při hledání položky, která se má sledovat, používá rekurze.        |
|Použít sudo     | Toto nastavení určuje, jestli se při kontrole položky používá sudo.         |
|Odkazy     | Toto nastavení určuje, jak se při procházení adresářů zpracovávají symbolické odkazy.<br> **Ignorovat** – Ignoruje symbolické odkazy a nezahrnuje odkazované soubory ani adresáře.<br>**Sledovat** – Během rekurze sleduje symbolické odkazy a zahrnuje i odkazované soubory a adresáře.<br>**Spravovat** – Sleduje symbolické odkazy a umožňuje zpracování vráceného obsahu.      |
|Nahrát obsah souboru pro všechna nastavení| Zapne nebo vypne u sledovaných změn nahrávání obsahu souboru. Dostupné možnosti: True nebo False.|

   > [!NOTE]
   > Možnost **Spravovat odkazy** se nedoporučuje. Načítání obsahu souborů se nepodporuje.

## <a name="enable-activity-log-connection"></a>Povolení připojení protokolu aktivit

Na stránce Change Tracking na vašem virtuálním počítači vyberte **Správa připojení protokolu aktivit**. Tato úloha otevře stránku Protokol aktivit Azure. Kliknutím na **Připojit** se připojíte ke sledování změn do protokolu aktivit Azure pro váš virtuální počítač.

Když je toto nastavení povolené, přejděte na stránku Přehled vašeho virtuálního počítače a výběrem **Zastavit** virtuální počítač zastavte. Po zobrazení výzvy vyberte **Ano** a zastavte virtuální počítač. Až bude přidělení vašeho virtuálního počítače zrušeno, vyberte **Spustit** a restartujte ho.

Zastavení a spuštění virtuálního počítače zapíše tuto událost do jeho protokolu aktivit Vraťte se na stránku Change Tracking. Vyberte **Události** v dolní části stránky. Po chvíli se události zobrazí v grafu a tabulce. Podobně jako v předchozím kroku je možné každou událost vybrat a zobrazit o ní podrobné informace.

![Zobrazení podrobnosti o změnách na portálu](./media/automation-tutorial-troubleshoot-changes/viewevents.png)

## <a name="view-changes"></a>Zobrazení změn

Jakmile budou řešení Change Tracking a Inventory povolená, můžete zobrazit výsledky na stránce Change Tracking.

Ve vašem virtuálním počítači v části **OPERACE** vyberte **Change Tracking**.

![Snímek obrazovky se seznamem změn virtuálního počítače](./media/automation-tutorial-troubleshoot-changes/change-tracking-list.png)

Tento graf ukazuje změny, ke kterým došlo v průběhu času.
Po přidání připojení protokolu aktivit zobrazuje čára grafu úplně nahoře události protokolu aktivit Azure.
Jednotlivé řádky grafu reprezentují různé typy sledovatelných změn.
Tyto typy jsou linuxové procesy démon, soubory, klíče registru systému Windows, software a služby pro Windows.
Karta Změny zobrazuje podrobnosti o změnách znázorněných ve vizualizaci v sestupném pořadí podle času, kdy ke změně došlo (nejnovější je první).
Na kartě **Události** zobrazuje tabulka připojené události protokolu aktivit a související podrobnosti, přičemž nejnovější události jsou uvedené jako první.

Ve výsledcích vidíte, že došlo k několika změnám systému, včetně změn služeb a softwaru. Pomocí filtrů v horní části stránky můžete výsledky filtrovat podle **typu změny** nebo časového rozsahu.

Vyberte změnu **služby WindowsServices.** Tento výběr otevře stránku Změnit podrobnosti zobrazující podrobnosti o změně a hodnotách před a po změně. V tomto případě se služba Ochrana softwaru zastavila.

![Zobrazení podrobnosti o změnách na portálu](./media/automation-tutorial-troubleshoot-changes/change-details.png)

## <a name="configure-alerts"></a>Konfigurace upozornění

Zobrazení změn na webu Azure Portal může být užitečné, ale užitečnější je možnost dostávat upozornění, když dojde ke změně, jako je například zastavení služby.

Pokud chcete přidat upozornění na zastavení služby, přejděte na webu Azure Portal do části **Monitorování**. Pak v části **Sdílené služby** vyberte **Upozornění** a klikněte na **+ Nové pravidlo upozornění**.

Chcete-li vybrat zdroj, klepněte na **tlačítko Vybrat.** Na stránce Vybrat prostředek vyberte **Log Analytics** z rozevírací nabídky **Filtr podle typu prostředku.** Vyberte váš pracovní prostor služby Log Analytics a pak vyberte **Hotovo**.

![Výběr prostředku](./media/automation-tutorial-troubleshoot-changes/select-a-resource.png)

Klepněte na tlačítko **Přidat podmínku**, na stránce Konfigurovat logiku signálu v tabulce vyberte **vlastní hledání protokolu**. Do textového pole Vyhledávací dotaz zadejte následující dotaz:

```loganalytics
ConfigurationChange | where ConfigChangeType == "WindowsServices" and SvcName == "W3SVC" and SvcState == "Stopped" | summarize by Computer
```

Tento dotaz vrátí počítače, na kterých se v zadaném období zastavila služba W3SVC.

V části **Logika upozornění** jako **Prahová hodnota** zadejte **0**. Jakmile budete hotovi, vyberte **Hotovo**.

![Konfigurace logiky signálů](./media/automation-tutorial-troubleshoot-changes/configure-signal-logic.png)

V části **Skupiny akcí**vyberte **Vytvořit nový**. Skupina akcí se skládá z akcí, které můžete použít ve více upozorněních. Mezi akce můžou patřit mimo jiné e-mailová oznámení, runbooky, webhooky a řada dalších. Další informace o skupinách akcí najdete v [tématu Vytváření a správa skupin akcí](../azure-monitor/platform/action-groups.md).

V části **Podrobnosti výstrahy**zadejte název a popis výstrahy. Nastavte **Závažnost** na **Informativní (záv. 2)**, **Upozornění (záv. 1)** nebo **Kritické (záv. 0)**.

Do pole **Název skupiny akcí** zadejte název a krátký název upozornění. Krátký název se použije místo úplného názvu skupiny akcí při odesílání oznámení pomocí této skupiny.

V části **Akce** zadejte název akce, například **Odeslání e-mailu správcům**. V části **TYP AKCE** vyberte **E-mailové/SMS/nabízené/hlasové oznámení**. V části **PODROBNOSTI** vyberte **Upravit podrobnosti**.

![Přidání skupiny akcí](./media/automation-tutorial-troubleshoot-changes/add-action-group.png)

V podokně E-mailové/SMS/nabízené/hlasové oznámení zadejte název. Zaškrtněte políčko **E-mail** a zadejte platnou e-mailovou adresu. V podokně klikněte na **OK** a potom na stránce Přidat skupinu akcí klikněte na **OK.**

Pokud chcete upravit předmět e-mailového upozornění, na stránce **Vytvořit pravidlo** v části **Přizpůsobit akce** vyberte **Předmět e-mailu**. Jakmile budete hotovi, vyberte **Vytvořit pravidlo upozornění**. Pravidlo vás upozorní na úspěšné nasazení aktualizací a poskytne informace o tom, které počítače byly součástí dané hromadné postupné aktualizace.

Následující obrázek ukazuje příklad e-mailu přijatého po zastavení služby W3SVC.

![e-mail](./media/automation-tutorial-troubleshoot-changes/email.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Připojení virtuálního počítače k řešení Change Tracking a Inventory
> * Prohledávání protokolů změn pro zastavené služby
> * Konfigurace sledování změn
> * Povolení připojení protokolu aktivit
> * Aktivace události
> * Zobrazení změn
> * Konfigurace upozornění

Další informace najdete v přehledu řešení Change Tracking a Inventory.

> [!div class="nextstepaction"]
> [Řešení Change Tracking a Inventory](automation-change-tracking.md)

