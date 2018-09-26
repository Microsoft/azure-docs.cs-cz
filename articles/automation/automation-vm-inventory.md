---
title: Správa virtuálního počítače Azure se shromažďováním dat pro inventarizaci | Dokumentace Microsoftu
description: Správa virtuálního počítače se shromažďováním dat pro inventarizaci
services: automation
ms.service: automation
ms.component: change-inventory-management
keywords: inventory, automation, change, tracking
author: jennyhunter-msft
ms.author: jehunte
ms.date: 03/30/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 30569c3a89de320769d433b5b3a4af9cf4e08e66
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47091402"
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Správa virtuálního počítače Azure se shromažďováním dat pro inventarizaci

Sledování inventáře pro virtuální počítač Azure můžete povolit na stránce prostředků příslušného virtuálního počítače. Tato metoda poskytuje uživatelské rozhraní v prohlížeči pro nastavení a konfiguraci shromažďování dat pro inventarizaci.

## <a name="before-you-begin"></a>Než začnete

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/).

Tento článek předpokládá, že máte ke konfigurování řešení, na virtuálním počítači. Pokud ještě nemáte virtuální počítač Azure, [vytvořte si virtuální počítač](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Povolení shromažďování dat pro inventarizaci na stránce prostředků virtuálního počítače

1. V levém podokně webu Azure Portal vyberte **Virtuální počítače**.
2. V seznamu virtuálních počítačů vyberte virtuální počítač.
3. Na **prostředků** nabídky v části **operace**vyberte **inventáře**.
4. Vyberte pracovní prostor Log Analytics, ve kterém se budou ukládat vaše protokoly dat.
    Pokud v dané oblasti nemáte k dispozici žádný pracovní prostor, zobrazí se výzva k vytvoření výchozího pracovního prostoru a účtu Automation.
5. Pokud chcete zahájit připojování vašeho počítače, vyberte **Povolit**.

   ![Zobrazení možností připojení](./media/automation-vm-inventory/inventory-onboarding-options.png)

    Stavový řádek vás bude informovat o povolování řešení. Tento proces může trvat až 15 minut. Během této doby můžete okno zavřít nebo ho nechat otevřené a jeho vás upozorní, jakmile bude řešení povoleno. Stav nasazení můžete monitorovat v podokně oznámení.

   ![Zobrazení řešení inventarizace ihned po připojení](./media/automation-vm-inventory/inventory-onboarded.png)

Po dokončení nasazení stavový řádek zmizí. Systém stále shromažďuje data inventáře, ale data ještě nemusí být viditelná. Úplné shromáždění dat může trvat až 24 hodin.

## <a name="configure-your-inventory-settings"></a>Konfigurace nastavení inventarizace

Ve výchozím nastavení jsou pro shromažďování nakonfigurovány software, služby Windows a linuxové procesy démon. Pokud chcete shromažďovat data pro inventarizaci registru a souborů systému Windows, nakonfigurujte nastavení shromažďování dat pro inventarizaci.

1. V **inventáře** zobrazení, vyberte **upravit nastavení** tlačítko v horní části okna.
2. Pokud chcete přidat nové nastavení shromažďování, výběrem karet **Registr systému Windows**, **Soubory Windows** a **Soubory Linuxu** přejděte do kategorie nastavení, které chcete přidat.
3. Vyberte odpovídající kategorii a klikněte na tlačítko **přidat** v horní části okna.

Následující tabulka obsahuje informace o jednotlivých vlastnostech, které je možné nakonfigurovat pro různé kategorie.

### <a name="windows-registry"></a>Registr Windows

|Vlastnost  |Popis  |
|---------|---------|
|Povoleno     | Určuje, jestli se nastavení používá.        |
|Název položky     | Popisný název souboru, který se má sledovat        |
|Skupina     | Název skupiny pro logické seskupení souborů        |
|Klíč registru systému Windows   | Cesta, ve které se má soubor hledat. Například: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup      |

### <a name="windows-files"></a>Soubory Windows

|Vlastnost  |Popis  |
|---------|---------|
|Povoleno     | Určuje, jestli se nastavení používá.        |
|Název položky     | Popisný název souboru, který se má sledovat        |
|Skupina     | Název skupiny pro logické seskupení souborů        |
|Zadat cestu     | Cesta, ve které se má soubor hledat. Například: c:\temp\myfile.txt

### <a name="linux-files"></a>Soubory Linuxu

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

## <a name="manage-machine-groups"></a>Spravovat skupiny počítačů

Inventář můžete vytvořit a zobrazit skupiny počítačů v Log Analytics. Skupiny počítačů jsou kolekce počítačů, které jsou definované v dotazu v Log Analytics.

Zobrazení skupin vyberte váš počítač **Machine skupiny** karty na stránce inventáře.

![Skupiny počítačů zobrazení na stránce inventáře](./media/automation-vm-inventory/inventory-machine-groups.png)

Výběr skupiny počítačů ze seznamu otevře stránku skupiny počítačů. Tato stránka zobrazuje podrobnosti o skupině počítačů. Mezi tyto podrobnosti patří dotazu log analytics, který se používá k definování skupiny. V dolní části stránky je stránkovaného seznam počítačů, které jsou součástí této skupiny.

![Zobrazit stránku skupiny počítačů](./media/automation-vm-inventory/machine-group-page.png)

Klikněte na tlačítko **+ klonování** tlačítko a klonování skupiny počítačů. Zde je třeba zadat skupiny nový název a alias pro skupinu. V tuto chvíli můžete změnit definici. Po změně stiskněte dotazu **ověřit dotaz** do počítače, které by být vybrán ve verzi preview. Až budete spokojení s skupiny klikněte na tlačítko **vytvořit** vytvořit skupinu počítačů

Pokud chcete vytvořit novou skupinu mchine, vyberte **+ vytvořit skupinu počítačů**. Toto tlačítko otevře **vytvořit stránku skupiny počítačů** Tady můžete definovat nové skupiny. Klikněte na tlačítko **vytvořit** vytvořit skupinu.

![Vytvořit novou skupinu počítačů](./media/automation-vm-inventory/create-new-group.png)

## <a name="disconnect-your-virtual-machine-from-management"></a>Odpojení virtuálního počítače od správy

Odebrání virtuálního počítače ze správy inventáře:

1. V levém podokně webu Azure Portal vyberte **Log Analytics** a pak vyberte pracovní prostor, který jste použili při připojování vašeho virtuálního počítače.
2. V okně **Log Analytics** v nabídce **Prostředek** v kategorii **Zdroje dat pracovního prostoru** vyberte **Virtuální počítače**.
3. V seznamu vyberte virtuální počítač, který chcete odpojit. U virtuálního počítače se zobrazí zelené zaškrtnutí vedle textu **Tento pracovní prostor** ve sloupci **Připojení OMS**.
4. V horní části další stránky vyberte **Odpojit**.
5. V potvrzovacím okně vyberte **Ano**.
    Tato akce odpojí počítač od správy.

## <a name="next-steps"></a>Další postup

* Informace o správě změn nastavení souborů a registru na virtuálních počítačích najdete v tématu [Sledování změn softwaru v prostředí pomocí řešení Change Tracking](../log-analytics/log-analytics-change-tracking.md).
* Další informace o správě Windows a aktualizace balíčků na virtuálních počítačích, najdete v článku [řešení Update Management v Azure](../operations-management-suite/oms-solution-update-management.md).
