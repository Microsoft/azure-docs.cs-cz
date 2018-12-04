---
title: Řešení potíží se změnami na virtuálním počítači Azure | Dokumentace Microsoftu
description: Potíže se změnami na virtuálním počítači Azure můžete řešit pomocí řešení Change Tracking.
services: automation
ms.service: automation
ms.component: change-inventory-management
keywords: change, tracking, automation
author: jennyhunter-msft
ms.author: jehunte
ms.date: 11/01/2018
ms.topic: tutorial
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: e4ea8f92a562ea4bc90df98d6e459377b9886777
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52844902"
---
# <a name="troubleshoot-changes-in-your-environment"></a>Řešení potíží se změnami ve vašem prostředí

V tomto kurzu zjistíte, jak řešit potíže se změnami na virtuálním počítači Azure. Když povolíte řešení Change Tracking, můžete sledovat změny softwaru, souborů, linuxových procesů démon, služeb systému Windows a klíčů registru Windows na vašich počítačích.
Identifikace těchto změn konfigurace vám může pomoct přesně určit provozní problémy v celém prostředí.

V tomto kurzu se naučíte:

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

Pro účely tohoto kurzu je nejprve potřeba povolit pro váš virtuální počítač řešení Change Tracking a Inventory. Pokud jste už dříve pro virtuální počítač povolili jiné řešení automatizace, tento krok není nezbytný.

1. V nabídce vlevo vyberte **Virtuální počítače** a ze seznamu vyberte virtuální počítač.
1. V nabídce vlevo v části **OPERACE** klikněte na **Inventory**. Otevře se stránka řešení **Change Tracking**.

![Povolení změny](./media/automation-tutorial-troubleshoot-changes/enableinventory.png) Otevře se obrazovka řešení **Change Tracking**. Nakonfigurujte umístění, pracovní prostor Log Analytics a účet Automation, které se mají použít, a klikněte na **Povolit**. Pokud se pole zobrazují šedě, znamená to, že pro daný virtuální počítač je povolené jiné řešení automatizace a musí se použít stejný pracovní prostor a účet Automation.

Pracovní prostor [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) slouží ke shromažďování dat generovaných funkcemi a službami, jako je řešení Inventory.
Tento pracovní prostor poskytuje možnost kontroly a analýzy dat z několika zdrojů na jednom místě.

Během připojování se virtuální počítač zřídí s agentem Microsoft Monitoring Agent (MMA) a procesem hybrid worker.
Agent slouží ke komunikaci s virtuálním počítačem a získávání informací o nainstalovaném softwaru.

Povolení řešení může trvat až 15 minut. Během této doby byste neměli zavírat okno prohlížeče.
Po povolení řešení začnou do Log Analytics proudit informace o nainstalovaném softwaru a změnách na virtuálních počítačích.
Zpřístupnění dat pro analýzu může trvat 30 minut až 6 hodin.

## <a name="using-change-tracking-in-log-analytics"></a>Použití řešení Change Tracking v Log Analytics

Řešení Change Tracking generuje data protokolu, která se odesílají do Log Analytics.
Pokud chcete v protokolech hledat spouštěním dotazů, v horní části okna **Change Tracking** vyberte **Log Analytics**.
Data řešení Change Tracking se ukládají jako typ **ConfigurationChange** (Změna konfigurace).
Následující ukázka dotazu Log Analytics vrátí všechny zastavené služby systému Windows.

```loganalytics
ConfigurationChange
| where ConfigChangeType == "WindowsServices" and SvcState == "Stopped"
```

Další informace o provozu a prohledávání souborů protokolů v Log Analytics najdete na stránce [Azure Log Analytics](../azure-monitor/log-query/log-query-overview.md).

## <a name="configure-change-tracking"></a>Konfigurace řešení Change Tracking

Change Tracking poskytuje možnost sledovat změny konfigurace na vašem virtuálním počítači. Následující kroky ukazují, jak nakonfigurovat sledování klíčů registru a souborů.

Pokud chcete zvolit, jaké soubory a klíče registru se mají shromažďovat a sledovat, vyberte **Upravit nastavení** v horní části stránky **Change Tracking**.

> [!NOTE]
> Řešení Inventory i Change Tracking používají stejná nastavení shromažďování a nastavení se konfigurují na úrovni pracovního prostoru.

V okně **Konfigurace pracovního prostoru** přidejte klíče registru systému Windows, soubory Windows nebo soubory Linuxu, které chcete sledovat, jak je uvedeno v dalších třech částech.

### <a name="add-a-windows-registry-key"></a>Přidání klíče registru systému Windows

1. Na kartě **Registr systému Windows** vyberte **Přidat**.
    Otevře se okno **Přidat registr systému Windows pro řešení Change Tracking**.

1. V okně **Přidat registr systému Windows pro řešení Change Tracking** zadejte informace o klíči, který se má sledovat, a klikněte na **Uložit**.

|Vlastnost  |Popis  |
|---------|---------|
|Povoleno     | Určuje, jestli se nastavení používá.        |
|Název položky     | Popisný název souboru, který se má sledovat        |
|Skupina     | Název skupiny pro logické seskupení souborů        |
|Klíč registru systému Windows   | Cesta, ve které se má soubor hledat. Například: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup      |

### <a name="add-a-windows-file"></a>Přidání souboru Windows

1. Na kartě **Soubory Windows** vyberte **Přidat**. Otevře se okno **Přidat soubor Windows pro řešení Change Tracking**.

1. V okně **Přidat soubor Windows pro řešení Change Tracking** zadejte informace o souboru nebo adresáři, který se má sledovat, a klikněte na **Uložit**.

|Vlastnost  |Popis  |
|---------|---------|
|Povoleno     | Určuje, jestli se nastavení používá.        |
|Název položky     | Popisný název souboru, který se má sledovat        |
|Skupina     | Název skupiny pro logické seskupení souborů        |
|Zadat cestu     | Cesta, ve které se má soubor hledat, například: c:\temp\\\*.txt.<br>Můžete použít také proměnnou prostředí, například %winDir%\System32\\\*.*.         |
|Rekurze     | Určuje, jestli se při hledání položky, která se má sledovat, používá rekurze.        |
|Nahrát obsah souboru pro všechna nastavení| Zapne nebo vypne u sledovaných změn nahrávání obsahu souboru. Dostupné možnosti: **True** nebo **False**.|

### <a name="add-a-linux-file"></a>Přidání souboru Linuxu

1. Na kartě **Soubory Linuxu** vyberte **Přidat**. Otevře se okno **Přidat soubor Linuxu pro řešení Change Tracking**.

1. V okně **Přidat soubor Linuxu pro řešení Change Tracking** zadejte informace o souboru nebo adresáři, který se má sledovat, a klikněte na **Uložit**.

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
|Nahrát obsah souboru pro všechna nastavení| Zapne nebo vypne u sledovaných změn nahrávání obsahu souboru. Dostupné možnosti: **True** nebo **False**.|

   > [!NOTE]
   > Možnost Spravovat se nedoporučuje. Načítání obsahu souborů se nepodporuje.

## <a name="enable-activity-log-connection"></a>Povolení připojení protokolu aktivit

Na stránce **Change Tracking** na vašem virtuálním počítači vyberte **Správa připojení protokolu aktivit**. Tato úloha otevře stránku **Protokol aktivit Azure**. Vyberte **Připojit** a propojte řešení Change Tracking s protokolem aktivit Azure pro váš virtuální počítač.

Když je toto nastavení povolené, přejděte na stránku **Přehled** vašeho virtuálního počítače a výběrem **Zastavit** virtuální počítač zastavte. Po zobrazení výzvy vyberte **Ano** a zastavte virtuální počítač. Až bude přidělení vašeho virtuálního počítače zrušeno, vyberte **Spustit** a restartujte ho.

Zastavení a spuštění virtuálního počítače zapíše tuto událost do jeho protokolu aktivit Vraťte se na stránku **Change Tracking**. Vyberte **Události** v dolní části stránky. Po chvíli se události zobrazí v grafu a tabulce. Podobně jako v předchozím kroku je možné každou událost vybrat a zobrazit o ní podrobné informace.

![Zobrazení podrobnosti o změnách na portálu](./media/automation-tutorial-troubleshoot-changes/viewevents.png)

## <a name="view-changes"></a>Zobrazení změn

Jakmile budou řešení Change Tracking a Inventory povolená, můžete zobrazit výsledky na stránce **Change Tracking**.

Ve vašem virtuálním počítači v části **OPERACE** vyberte **Change Tracking**.

![Snímek obrazovky se seznamem změn virtuálního počítače](./media/automation-tutorial-troubleshoot-changes/change-tracking-list.png)

Tento graf ukazuje změny, ke kterým došlo v průběhu času.
Po přidání připojení protokolu aktivit zobrazuje čára grafu úplně nahoře události protokolu aktivit Azure.
Jednotlivé řádky grafu reprezentují různé typy sledovatelných změn.
Tyto typy jsou linuxové procesy démon, soubory, klíče registru systému Windows, software a služby pro Windows.
Karta Změny zobrazuje podrobnosti o změnách znázorněných ve vizualizaci v sestupném pořadí podle času, kdy ke změně došlo (nejnovější je první).
Na kartě **Události** zobrazuje tabulka připojené události protokolu aktivit a související podrobnosti, přičemž nejnovější události jsou uvedené jako první.

Ve výsledcích vidíte, že došlo k několika změnám systému, včetně změn služeb a softwaru. Pomocí filtrů v horní části stránky můžete výsledky filtrovat podle **typu změny** nebo časového rozsahu.

Vyberte nějakou změnu **WindowsServices** (Služby pro Windows), tím se otevře okno **Podrobnosti o změně**. Okno s podrobnostmi o změně zobrazuje podrobnosti o změně spolu s hodnotami před a po změně. V tomto případě se služba Ochrana softwaru zastavila.

![Zobrazení podrobnosti o změnách na portálu](./media/automation-tutorial-troubleshoot-changes/change-details.png)

## <a name="configure-alerts"></a>Konfigurace upozornění

Zobrazení změn na webu Azure Portal může být užitečné, ale užitečnější je možnost dostávat upozornění, když dojde ke změně, jako je například zastavení služby.

Pokud chcete přidat upozornění na zastavení služby, přejděte na webu Azure Portal do části **Monitorování**. Pak v části **Sdílené služby** vyberte **Upozornění** a klikněte na **+ Nové pravidlo upozornění**.

V části **1. Definujte podmínku upozornění** a klikněte na **+ Vybrat cíl**. V části **Filtrovat podle typu prostředku** vyberte **Log Analytics**. Vyberte váš pracovní prostor Log Analytics a pak vyberte **Hotovo**.

![Výběr prostředku](./media/automation-tutorial-troubleshoot-changes/select-a-resource.png)

Vyberte **+ Přidat kritéria**.
V části **Konfigurovat logiku signálů** vyberte v tabulce **Vlastní prohledávání protokolu**. Do textového pole Vyhledávací dotaz zadejte následující dotaz:

```loganalytics
ConfigurationChange | where ConfigChangeType == "WindowsServices" and SvcName == "W3SVC" and SvcState == "Stopped" | summarize by Computer
```

Tento dotaz vrátí počítače, na kterých se v zadaném období zastavila služba W3SVC.

V části **Logika upozornění** jako **Prahová hodnota** zadejte **0**. Jakmile budete hotovi, vyberte **Hotovo**.

![Konfigurace logiky signálů](./media/automation-tutorial-troubleshoot-changes/configure-signal-logic.png)

V části **2. Definujte podrobnosti upozornění** zadejte název a popis upozornění. Nastavte **Závažnost** na **Informativní (záv. 2)**, **Upozornění (záv. 1)** nebo **Kritické (záv. 0)**.

![Definice podrobností o upozornění](./media/automation-tutorial-troubleshoot-changes/define-alert-details.png)

V části **3. Definujte skupinu akcí** vyberte **Nová skupina akcí**. Skupina akcí se skládá z akcí, které můžete použít ve více upozorněních. Mezi akce můžou patřit mimo jiné e-mailová oznámení, runbooky, webhooky a řada dalších. Další informace o skupinách akcí najdete v tématu [Vytváření a správa skupin akcí](../monitoring-and-diagnostics/monitoring-action-groups.md).

Do pole **Název skupiny akcí** zadejte název a krátký název upozornění. Krátký název se použije místo úplného názvu skupiny akcí při odesílání oznámení pomocí této skupiny.

V části **Akce** zadejte název akce, například **Odeslání e-mailu správcům**. V části **TYP AKCE** vyberte **E-mailové/SMS/nabízené/hlasové oznámení**. V části **PODROBNOSTI** vyberte **Upravit podrobnosti**.

![Přidání skupiny akcí](./media/automation-tutorial-troubleshoot-changes/add-action-group.png)

V podokně **E-mailové/SMS/nabízené/hlasové oznámení** zadejte název. Zaškrtněte políčko **E-mail** a zadejte platnou e-mailovou adresu. Klikněte na **OK** na stránce **E-mailové/SMS/nabízené/hlasové oznámení** a pak klikněte na **OK** na stránce **Přidat skupinu akcí**.

Pokud chcete upravit předmět e-mailového upozornění, na stránce **Vytvořit pravidlo** v části **Přizpůsobit akce** vyberte **Předmět e-mailu**. Jakmile budete hotovi, vyberte **Vytvořit pravidlo upozornění**. Pravidlo vás upozorní na úspěšné nasazení aktualizací a poskytne informace o tom, které počítače byly součástí dané hromadné postupné aktualizace.

Následující obrázek ukazuje příklad e-mailu přijatého po zastavení služby W3SVC.

![e-mail](./media/automation-tutorial-troubleshoot-changes/email.png)

## <a name="next-steps"></a>Další postup

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
