---
title: Řešení potíží se změnami na virtuálním počítači Azure v Azure Automation | Microsoft Docs
description: Tento článek popisuje, jak řešit potíže se změnami na virtuálním počítači Azure.
services: automation
ms.subservice: change-inventory-management
keywords: Změna, sledování, sledování změn, inventář, automatizace
ms.date: 03/21/2021
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 980740d387f9e953d1ea764327c8aa13f8650948
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775798"
---
# <a name="troubleshoot-changes-on-an-azure-vm"></a>Řešení potíží se změnami na virtuálním počítači Azure

V tomto kurzu zjistíte, jak řešit potíže se změnami na virtuálním počítači Azure. Povolením Change Tracking a inventáře můžete sledovat změny softwaru, souborů, démonů systému Linux, služeb systému Windows a klíčů registru Windows na vašich počítačích.
Identifikace těchto změn konfigurace vám může pomoct přesně určit provozní problémy v celém prostředí.

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Povolení Change Tracking a inventáře pro virtuální počítač
> * Prohledávání protokolů změn pro zastavené služby
> * Konfigurace sledování změn
> * Povolení připojení protokolu aktivit
> * Aktivace události
> * Zobrazení změn
> * Konfigurace upozornění

## <a name="prerequisites"></a>Předpoklady

Pro absolvování tohoto kurzu potřebujete:

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](./index.yml) , který bude obsahovat sledovací proces a runbooky akcí a úlohu sledovacího procesu.
* [Virtuální počítač](../virtual-machines/windows/quick-create-portal.md) , který má být pro funkci povolen.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Povolení řešení Change Tracking a Inventory

Nejdřív musíte pro tento kurz povolit Change Tracking a inventář. Pokud jste tuto funkci dříve povolili, tento krok není nezbytný.

>[!NOTE]
>Pokud jsou pole šedá, pro virtuální počítač je povolena jiná funkce automatizace a je nutné použít stejný pracovní prostor a účet Automation.

1. Vyberte **virtuální počítače** a ze seznamu vyberte virtuální počítač.
2. V nabídce vlevo vyberte **inventarizace** v části **operace**. Otevře se stránka inventář.

    ![Povolit změnu](./media/automation-tutorial-troubleshoot-changes/enableinventory.png)

3. Vyberte pracovní prostor [Log Analytics](../azure-monitor/logs/log-query-overview.md) . Tento pracovní prostor shromažďuje data generovaná funkcemi, jako jsou Change Tracking a inventář. Tento pracovní prostor poskytuje možnost kontroly a analýzy dat z několika zdrojů na jednom místě.

    [!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

4. Vyberte účet Automation, který se má použít.

5. Nakonfigurujte umístění pro nasazení.

5. Kliknutím na **Povolit** nasadíte funkci pro svůj virtuální počítač. 

Během instalace se virtuální počítač zřídí s agentem Log Analytics pro Windows a [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md). Povolení Change Tracking a inventáře může trvat až 15 minut. Během této doby byste neměli zavírat okno prohlížeče.

Po povolení funkce budou informace o nainstalovaném softwaru a změnách na virtuálním počítači toky Azure Monitor protokoly.
Zpřístupnění dat pro analýzu může trvat 30 minut až 6 hodin.

## <a name="use-change-tracking-and-inventory-in-azure-monitor-logs"></a>Použití Change Tracking a inventáře v protokolech Azure Monitor

Change Tracking a inventář generuje data protokolu, která se odesílají do protokolů Azure Monitor. Pokud chcete v protokolech Hledat spouštěním dotazů, vyberte **Log Analytics** v horní části stránky Change Tracking. Data sledování změn jsou uložena v rámci typu `ConfigurationChange` .

Následující příklad Log Analytics dotaz vrátí všechny služby systému Windows, které byly zastaveny.

```loganalytics
ConfigurationChange
| where ConfigChangeType == "WindowsServices" and SvcState == "Stopped"
```

Další informace o spouštění a hledání souborů protokolu v protokolech Azure Monitor najdete v tématu [protokoly Azure monitor](../azure-monitor/logs/log-query-overview.md).

## <a name="configure-change-tracking"></a>Konfigurace sledování změn

Pomocí sledování změn můžete zvolit soubory a klíče registru, které se budou shromažďovat a sledovat pomocí **nastavení úprav** v horní části stránky sledování změn na virtuálním počítači. Můžete přidat klíče registru Windows, soubory Windows nebo soubory systému Linux ke sledování na stránce Konfigurace pracovního prostoru.

> [!NOTE]
> Sledování změn i inventarizace používají stejné nastavení kolekce a nastavení se konfigurují na úrovni pracovního prostoru.

### <a name="add-a-windows-registry-key"></a>Přidat klíč registru Windows

1. Na kartě **Registr systému Windows** vyberte **Přidat**. 

1. Na stránce přidat registr Windows pro Change Tracking zadejte informace pro klíč, který chcete sledovat, a klikněte na **Uložit** .

    |Vlastnost  |Popis  |
    |---------|---------|
    |Povoleno     | Určuje, jestli se nastavení používá.        |
    |Název položky     | Popisný název souboru, který se má sledovat        |
    |Group (Skupina)     | Název skupiny pro logické seskupení souborů        |
    |Klíč registru systému Windows   | Cesta, ve které se má soubor hledat. Například: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup      |

### <a name="add-a-windows-file"></a>Přidání souboru Windows

1. Na kartě **Soubory Windows** vyberte **Přidat**. 

1. Na stránce Přidat soubor Windows pro Change Tracking zadejte informace o souboru nebo adresáři, který se má sledovat, a klikněte na **Uložit** .

    |Vlastnost  |Popis  |
    |---------|---------|
    |Povoleno     | Určuje, jestli se nastavení používá.        |
    |Název položky     | Popisný název souboru, který se má sledovat        |
    |Group (Skupina)     | Název skupiny pro logické seskupení souborů        |
    |Zadat cestu     | Cesta, ve které se má soubor hledat, například: c:\temp\\\*.txt.<br>Můžete použít také proměnnou prostředí, například %winDir%\System32\\\*.*.         |
    |Rekurze     | Určuje, jestli se při hledání položky, která se má sledovat, používá rekurze.        |
    |Nahrát obsah souboru pro všechna nastavení| Zapne nebo vypne u sledovaných změn nahrávání obsahu souboru. Dostupné možnosti: **True** nebo **False**.|

### <a name="add-a-linux-file"></a>Přidání souboru Linuxu

1. Na kartě **Soubory Linuxu** vyberte **Přidat**. 

1. Na stránce Přidat soubor pro Linux pro Change Tracking zadejte informace o souboru nebo adresáři, který se má sledovat, a klikněte na **Uložit**.

    |Vlastnost  |Popis  |
    |---------|---------|
    |Povoleno     | Určuje, jestli se nastavení používá.        |
    |Název položky     | Popisný název souboru, který se má sledovat        |
    |Group (Skupina)     | Název skupiny pro logické seskupení souborů        |
    |Zadat cestu     | Cesta, ve které se má soubor hledat. Například: /etc/*.conf       |
    |Typ cesty     | Typ položky, která se má sledovat. Možné hodnoty jsou Soubor a Adresář.        |
    |Rekurze     | Určuje, jestli se při hledání položky, která se má sledovat, používá rekurze.        |
    |Použít sudo     | Toto nastavení určuje, jestli se při kontrole položky používá sudo.         |
    |Odkazy     | Toto nastavení určuje, jak se při procházení adresářů zpracovávají symbolické odkazy.<br> **Ignorovat** – Ignoruje symbolické odkazy a nezahrnuje odkazované soubory ani adresáře.<br>**Sledovat** – Během rekurze sleduje symbolické odkazy a zahrnuje i odkazované soubory a adresáře.<br>**Spravovat** – Sleduje symbolické odkazy a umožňuje zpracování vráceného obsahu.      |
    |Nahrát obsah souboru pro všechna nastavení| Zapne nebo vypne u sledovaných změn nahrávání obsahu souboru. Dostupné možnosti: True nebo False.|

   > [!NOTE]
   > Není doporučena hodnota **Spravovat** pro vlastnost **odkazy** . Načítání obsahu souborů se nepodporuje.

## <a name="enable-activity-log-connection"></a>Povolení připojení protokolu aktivit

1. Na stránce Change Tracking na vašem virtuálním počítači vyberte **Správa připojení protokolu aktivit**. 

2. Na stránce Protokol aktivit Azure kliknutím na **připojit** připojte Change Tracking a inventář do protokolu aktivit Azure pro váš virtuální počítač.

3. Přejděte na stránku Přehled pro váš virtuální počítač a výběrem **zastavit** zastavte virtuální počítač. 

4. Po zobrazení výzvy vyberte **Ano** a zastavte virtuální počítač. 

5. Až se virtuální počítač oddělí, vyberte **Spustit** a restartujte ho. Zastavení a spuštění virtuálního počítače zapíše událost do protokolu aktivit. 

## <a name="view-changes"></a>Zobrazení změn

1. Přejděte zpět na stránku sledování změn a vyberte kartu **události** v dolní části stránky. 

2. Po chvíli se události sledování změn zobrazují v grafu a tabulce. Tento graf ukazuje změny, ke kterým došlo v průběhu času. Spojnicový graf v horní části zobrazuje události protokolu aktivit Azure. Každý řádek pruhových grafů představuje jiný typ změny, který se může sledovat. Mezi tyto typy patří procesy démonů systému Linux, soubory, klíče registru Windows, software a služby systému Windows. Karta změna zobrazuje podrobnosti zobrazených změn, přičemž Poslední změna se zobrazuje jako první.

    ![Zobrazení událostí na portálu](./media/automation-tutorial-troubleshoot-changes/viewevents.png)

3. Všimněte si, že v systému došlo k několika změnám, včetně změn služeb a softwaru. Pomocí filtrů v horní části stránky můžete výsledky filtrovat podle **typu změny** nebo časového rozsahu.

    ![Seznam změn virtuálního počítače](./media/automation-tutorial-troubleshoot-changes/change-tracking-list.png)

4. Vyberte **WindowsServices** změnu. Tento výběr otevře stránku podrobnosti o změně, která zobrazuje podrobnosti o změně a hodnotách před a po změně. V tomto případě se služba Ochrana softwaru zastavila.

    ![Zobrazení podrobnosti o změnách na portálu](./media/automation-tutorial-troubleshoot-changes/change-details.png)

## <a name="configure-alerts"></a>Konfigurace upozornění

Zobrazení změn na webu Azure Portal může být užitečné, ale užitečnější je možnost dostávat upozornění, když dojde ke změně, jako je například zastavení služby. Pojďme přidat upozornění pro zastavenou službu. 

1. V Azure Portal klikněte na **monitorování**. 

2. V části **sdílené služby** vyberte **výstrahy** a klikněte na **+ nové pravidlo výstrahy**.

3. Pro výběr prostředku klikněte na **Vybrat** . 

4. Na stránce Vybrat prostředek zvolte v rozevírací nabídce **filtrovat podle typu prostředku** možnost **Log Analytics** . 

5. Vyberte pracovní prostor Log Analytics a potom klikněte na **Hotovo**.

    ![Výběr prostředku](./media/automation-tutorial-troubleshoot-changes/select-a-resource.png)

6. Klikněte na **Přidat podmínku**.

7. V tabulce na stránce Konfigurovat logiku signálu vyberte **vlastní prohledávání protokolu**. 

8. Do textového pole vyhledávacího dotazu zadejte následující dotaz:

    ```loganalytics
    ConfigurationChange | where ConfigChangeType == "WindowsServices" and SvcName == "W3SVC" and SvcState == "Stopped" | summarize by Computer
    ```

    Tento dotaz vrátí počítače, na kterých se v zadaném období zastavila služba W3SVC.

9. V **části** **logika výstrahy** zadejte hodnotu **0**. Až skončíte, klikněte na **Hotovo**.

    ![Konfigurace logiky signálů](./media/automation-tutorial-troubleshoot-changes/configure-signal-logic.png)

10. V části **skupiny akcí** vyberte **vytvořit novou** . Skupina akcí se skládá z akcí, které můžete použít ve více upozorněních. Mezi akce můžou patřit mimo jiné e-mailová oznámení, runbooky, webhooky a řada dalších. Další informace o skupinách akcí naleznete v tématu [Create and Manage Action Groups](../azure-monitor/alerts/action-groups.md).

11. V části **Podrobnosti výstrahy** zadejte název a popis výstrahy. 

12. Nastavte **Závažnost** na **Informativní (záv. 2)**, **Upozornění (záv. 1)** nebo **Kritické (záv. 0)**.

13. Do pole **Název skupiny akcí** zadejte název a krátký název upozornění. Krátký název se použije místo úplného názvu skupiny akcí při odesílání oznámení pomocí této skupiny.

14. V části **Akce** zadejte název akce, například **správce e-mailu**. 

15. Jako **typ akce** vyberte **E-mail/zpráva SMS/nabízení/hlas**. 

16. Pokud chcete zobrazit **Podrobnosti**, vyberte **Upravit podrobnosti**.

    :::image type="content" source="./media/automation-tutorial-troubleshoot-changes/add-action-group.png" alt-text="Využití a odhadované náklady." lightbox="./media/automation-tutorial-troubleshoot-changes/add-action-group.png":::

17. V podokně **e-mail/zpráva SMS/oznámení/hlas** zadejte název, zaškrtněte políčko **e-mail** a zadejte platnou e-mailovou adresu. Po dokončení klikněte na tlačítko **OK** v podokně a pak klikněte na tlačítko **OK** na stránce **Přidat skupinu akcí** .

18. Pokud chcete přizpůsobit předmět e-mailu s výstrahou, vyberte **přizpůsobit akce**.

19. V možnosti **vytvořit pravidlo** vyberte **Předmět e-mailu** a pak zvolte **vytvořit pravidlo upozornění**. Pravidlo vás upozorní na úspěšné nasazení aktualizací a poskytne informace o tom, které počítače byly součástí dané hromadné postupné aktualizace. Na následujícím obrázku je příklad e-mailu přijatý při zastavení služby W3SVC.

    ![Snímek obrazovky zobrazuje e-mailové oznámení obdržené, když se zastaví služby W 3 S V C.](./media/automation-tutorial-troubleshoot-changes/email.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Povolení Change Tracking a inventáře pro virtuální počítač
> * Prohledávání protokolů změn pro zastavené služby
> * Konfigurace sledování změn
> * Povolit připojení protokolu aktivit
> * Aktivace události
> * Zobrazení změn
> * Konfigurace upozornění

Pokračujte na Přehled funkce Change Tracking a inventáře a získejte další informace o této funkci.

> [!div class="nextstepaction"]
> [Přehled Change Tracking a inventáře](change-tracking/overview.md)
