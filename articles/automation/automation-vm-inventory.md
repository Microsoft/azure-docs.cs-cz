---
title: Správa virtuálního počítače Azure se shromažďováním dat pro inventarizaci | Dokumentace Microsoftu
description: Správa virtuálního počítače se shromažďováním dat pro inventarizaci
services: automation
ms.subservice: change-inventory-management
keywords: inventory, automation, change, tracking
ms.date: 01/28/2020
ms.topic: conceptual
ms.openlocfilehash: d0324038b8a38d7eba84e5472b8f90439b0322c1
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844815"
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Správa virtuálního počítače Azure se shromažďováním dat pro inventarizaci

Sledování inventáře pro virtuální počítač Azure můžete povolit na stránce prostředků příslušného virtuálního počítače. V počítačích můžete shromažďovat a zobrazovat tyto informace o inventáři:

- Software systému Windows (aplikace systému Windows a aktualizace systému Windows), služby, soubory a klíče registru
- Procesy démonů a soubory Linux softwaru (balíčky)

Tato metoda poskytuje uživatelské rozhraní v prohlížeči pro nastavení a konfiguraci shromažďování dat pro inventarizaci.

## <a name="before-you-begin"></a>Než začnete

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/).

V tomto článku se předpokládá, že máte virtuální počítač, na kterém chcete řešení nakonfigurovat. Pokud ještě nemáte virtuální počítač Azure, [vytvořte si virtuální počítač](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na web [Azure Portal](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Povolení shromažďování dat pro inventarizaci na stránce prostředků virtuálního počítače

1. V levém podokně webu Azure Portal vyberte **Virtuální počítače**.
2. V seznamu virtuálních počítačů vyberte virtuální počítač.
3. V nabídce **prostředek** v části **operace**vyberte **inventarizace**.
4. Vyberte Log Analytics pracovní prostor pro ukládání datových protokolů.
    Pokud v dané oblasti nemáte k dispozici žádný pracovní prostor, zobrazí se výzva k vytvoření výchozího pracovního prostoru a účtu Automation.
5. Pokud chcete zahájit připojování vašeho počítače, vyberte **Povolit**.

   ![Zobrazení možností připojení](./media/automation-vm-inventory/inventory-onboarding-options.png)

    Stavový řádek vás bude informovat o povolování řešení. Tento proces může trvat až 15 minut. Během této doby můžete okno zavřít nebo ho můžete nechat otevřené a upozorní vás, když je řešení povolené. Stav nasazení můžete monitorovat v podokně oznámení.

   ![Zobrazení řešení inventarizace ihned po připojení](./media/automation-vm-inventory/inventory-onboarded.png)

Po dokončení nasazení stavový řádek zmizí. Systém stále shromažďuje data inventáře, ale data ještě nemusí být viditelná. Úplné shromáždění dat může trvat až 24 hodin.

## <a name="configure-your-inventory-settings"></a>Konfigurace nastavení inventarizace

Ve výchozím nastavení jsou pro shromažďování nakonfigurovány software, služby Windows a linuxové procesy démon. Pokud chcete shromažďovat data pro inventarizaci registru a souborů systému Windows, nakonfigurujte nastavení shromažďování dat pro inventarizaci.

1. V zobrazení **inventáře** vyberte tlačítko **Upravit nastavení** v horní části okna.
2. Pokud chcete přidat nové nastavení shromažďování, výběrem karet **Registr systému Windows**, **Soubory Windows** a **Soubory Linuxu** přejděte do kategorie nastavení, které chcete přidat.
3. Vyberte příslušnou kategorii a v horní části okna klikněte na **Přidat** .

Následující tabulky obsahují informace o jednotlivých vlastnostech, které lze nakonfigurovat pro různé kategorie.

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

### <a name="linux-files"></a>Soubory Linux

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

## <a name="manage-machine-groups"></a>Správa skupin počítačů

Inventarizace umožňuje vytvářet a zobrazovat skupiny počítačů v protokolech Azure Monitor. Skupiny počítačů jsou kolekce počítačů definované dotazem v protokolech Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Pokud chcete zobrazit skupiny počítačů, vyberte na stránce inventář kartu **skupiny počítačů** .

![Zobrazení skupin počítačů na stránce inventáře](./media/automation-vm-inventory/inventory-machine-groups.png)

Výběrem skupiny počítačů ze seznamu se otevře stránka skupiny počítačů. Tato stránka zobrazuje podrobnosti o skupině počítačů. Mezi tyto podrobnosti patří dotaz Log Analytics, který se používá k definování skupiny. V dolní části stránky je seznam počítačů, které jsou součástí této skupiny.

![Zobrazit stránku skupiny počítačů](./media/automation-vm-inventory/machine-group-page.png)

Kliknutím na tlačítko **+ klonovat** naklonujte skupinu počítačů. Tady musíte dát skupině Nový název a alias pro skupinu. Definici lze v tomto okamžiku změnit. Po změně dotazu stisknutím tlačítka **ověřit dotaz** zobrazíte náhled počítačů, které budou vybrány. Když jste spokojeni se skupinou, klikněte na **vytvořit** a vytvořte skupinu počítačů.

Pokud chcete vytvořit novou skupinu počítačů, vyberte **+ vytvořit skupinu počítačů**. Tímto tlačítkem otevřete **stránku vytvořit skupinu počítačů** , kde můžete definovat novou skupinu. Kliknutím na **vytvořit** vytvořte skupinu.

![Vytvořit novou skupinu počítačů](./media/automation-vm-inventory/create-new-group.png)

## <a name="disconnect-your-virtual-machine-from-management"></a>Odpojení virtuálního počítače od správy

Odebrání virtuálního počítače ze správy inventáře:

1. V levém podokně webu Azure Portal vyberte **Log Analytics** a pak vyberte pracovní prostor, který jste použili při připojování vašeho virtuálního počítače.
2. V okně **Log Analytics** v nabídce **Prostředek** v kategorii **Zdroje dat pracovního prostoru** vyberte **Virtuální počítače**.
3. V seznamu vyberte virtuální počítač, který chcete odpojit. U virtuálního počítače se zobrazí zelené zaškrtnutí vedle textu **Tento pracovní prostor** ve sloupci **Připojení OMS**.

   >[!NOTE]
   >OMS se teď označuje jako protokoly Azure Monitor.
   
4. V horní části další stránky vyberte **Odpojit**.
5. V potvrzovacím okně vyberte **Ano**.
    Tato akce odpojí počítač od správy.

## <a name="next-steps"></a>Další kroky

* Informace o správě změn nastavení souborů a registru na virtuálních počítačích najdete v tématu [Sledování změn softwaru v prostředí pomocí řešení Change Tracking](../log-analytics/log-analytics-change-tracking.md).
* Další informace o správě aktualizací pro Windows a balíčky na virtuálních počítačích najdete v tématu [řešení Update Management v Azure](../operations-management-suite/oms-solution-update-management.md).

