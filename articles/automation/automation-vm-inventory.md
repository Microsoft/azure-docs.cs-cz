---
title: Správa virtuálního počítače Azure se shromažďováním dat pro inventarizaci | Dokumentace Microsoftu
description: Správa virtuálního počítače se shromažďováním dat pro inventarizaci
services: automation
ms.subservice: change-inventory-management
keywords: inventory, automation, change, tracking
ms.date: 01/28/2020
ms.topic: conceptual
ms.openlocfilehash: 0627d2daa70c276535dc43b722e22e1d73b0c8d2
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617379"
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Správa virtuálního počítače Azure se shromažďováním dat pro inventarizaci

Sledování inventáře pro virtuální počítač Azure můžete povolit na stránce prostředků příslušného virtuálního počítače. V počítačích můžete shromažďovat a zobrazovat následující informace o inventáři:

- Software Systému Windows (aplikace systému Windows a aktualizace systému Windows), služby, soubory a klíče registru
- Linux software (balíčky) daemons, a soubory

Tato metoda poskytuje uživatelské rozhraní v prohlížeči pro nastavení a konfiguraci shromažďování dat pro inventarizaci.

## <a name="before-you-begin"></a>Než začnete

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/).

Tento článek předpokládá, že máte virtuální počítač pro konfiguraci řešení na. Pokud ještě nemáte virtuální počítač Azure, [vytvořte si virtuální počítač](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Povolení shromažďování dat pro inventarizaci na stránce prostředků virtuálního počítače

1. V levém podokně webu Azure Portal vyberte **Virtuální počítače**.
2. V seznamu virtuálních počítačů vyberte virtuální počítač.
3. V nabídce **Zdroj** vyberte v části **Operace** **položku Zásoby**.
4. Vyberte pracovní prostor Log Analytics pro ukládání datových protokolů.
    Pokud v dané oblasti nemáte k dispozici žádný pracovní prostor, zobrazí se výzva k vytvoření výchozího pracovního prostoru a účtu Automation.
5. Pokud chcete zahájit připojování vašeho počítače, vyberte **Povolit**.

   ![Zobrazení možností připojení](./media/automation-vm-inventory/inventory-onboarding-options.png)

    Stavový řádek vás bude informovat o povolování řešení. Tento proces může trvat až 15 minut. Během této doby můžete zavřít okno, nebo můžete ponechat otevřené a upozorní vás, když je povoleno řešení. Stav nasazení můžete monitorovat v podokně oznámení.

   ![Zobrazení řešení inventarizace ihned po připojení](./media/automation-vm-inventory/inventory-onboarded.png)

Po dokončení nasazení stavový řádek zmizí. Systém stále shromažďuje data inventáře, ale data ještě nemusí být viditelná. Úplné shromáždění dat může trvat až 24 hodin.

## <a name="configure-your-inventory-settings"></a>Konfigurace nastavení inventarizace

Ve výchozím nastavení jsou pro shromažďování nakonfigurovány software, služby Windows a linuxové procesy démon. Pokud chcete shromažďovat data pro inventarizaci registru a souborů systému Windows, nakonfigurujte nastavení shromažďování dat pro inventarizaci.

1. Na stránce Zásoby klikněte v horní části stránky na **Upravit nastavení.**
2. Chcete-li přidat nové nastavení kolekce, přejděte do kategorie nastavení, kterou chcete přidat, výběrem karty **Registr systému Windows**, **Soubory systému Windows**nebo Soubory **Linuxu.**
3. Vyberte příslušnou kategorii a v horní části stránky klikněte na **Přidat.**

Následující tabulky obsahují informace o každé vlastnosti, kterou lze nakonfigurovat pro různé kategorie.

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
|Povoleno     | True, pokud je použito nastavení a False jinak.        |
|Název položky     | Popisný název souboru, který má být sledován.        |
|Skupina     | Název skupiny pro logické seskupení souborů.       |
|Zadat cestu     | Cesta ke kontrole souboru, například **c:\temp\myfile.txt**.

### <a name="linux-files"></a>Linux ové soubory

|Vlastnost  |Popis  |
|---------|---------|
|Povoleno     | True, pokud je použito nastavení a False jinak.        |
|Název položky     | Popisný název souboru, který má být sledován.        |
|Skupina     | Název skupiny pro logické seskupení souborů.        |
|Zadat cestu     | Cesta ke kontrole souboru, například **/etc/*.conf**.       |
|Typ cesty     | Typ položky, která má být sledována. Hodnoty jsou Soubor a adresář.        |
|Rekurze     | True, pokud rekurze se používá při hledání položky, které mají být sledovány a False jinak.        |
|Použít sudo     | True, pokud sudo se používá při kontrole položky a False jinak.         |
|Odkazy     | Hodnota označující, jak symbolické odkazy jsou řešeny při procházení adresářů. Možné hodnoty: <br> Ignorovat – Ignoruje symbolické odkazy a nezahrnuje odkazované soubory ani adresáře.<br>Sledovat – Během rekurze sleduje symbolické odkazy a zahrnuje i odkazované soubory a adresáře.<br>Spravovat – Sleduje symbolické odkazy a umožňuje zpracování vráceného obsahu.      |

## <a name="manage-machine-groups"></a>Správa skupin strojů

Inventář umožňuje vytvářet a zobrazovat skupiny strojů v protokolech Azure Monitoru. Skupiny počítačů jsou kolekce počítačů definovaných dotazem v protokolech Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Chcete-li zobrazit skupiny strojů, vyberte kartu **Skupiny strojů** na stránce Zásoby.

![Zobrazit skupiny strojů na stránce zásob](./media/automation-vm-inventory/inventory-machine-groups.png)

Výběrem skupiny strojů ze seznamu se otevře stránka Skupiny strojů. Na této stránce jsou uvedeny podrobnosti o skupině strojů. Tyto podrobnosti zahrnují dotaz analýzy protokolu, který se používá k definování skupiny. V dolní části stránky je stránkovaný seznam počítačů, které jsou součástí této skupiny.

![Zobrazit stránku skupiny strojů](./media/automation-vm-inventory/machine-group-page.png)

Chcete-li naklonovat skupinu strojů, klepněte na tlačítko **+ Klonování.** Skupině je nutné dát nový název a alias pro skupinu. Definici lze v tomto okamžiku změnit. Po změně dotazu klikněte na **Ověřit dotaz** a zobrazte náhled počítačů, které by byly vybrány. Až budete se skupinou spokojeni, kliknutím na **Vytvořit** vytvořte skupinu strojů.

Chcete-li vytvořit novou skupinu strojů, klepněte na tlačítko **+ Vytvořit skupinu strojů**. Toto tlačítko otevře **stránku Vytvořit skupinu strojů,** kde můžete definovat novou skupinu. Vytvořte skupinu kliknutím na **Vytvořit**.

![Vytvořit novou skupinu strojů](./media/automation-vm-inventory/create-new-group.png)

## <a name="disconnect-your-virtual-machine-from-management"></a>Odpojení virtuálního počítače od správy

Odebrání virtuálního počítače ze správy inventáře:

1. V levém podokně webu Azure Portal vyberte **Log Analytics** a pak vyberte pracovní prostor, který jste použili při připojování vašeho virtuálního počítače.
2. Na stránce Log Analytics otevřete nabídku **Zdroj.**
3. V části **Zdroje dat pracovního prostoru**vyberte **Virtuální počítače** .
4. V seznamu vyberte virtuální počítač, který chcete odpojit. U virtuálního počítače se zobrazí zelené zaškrtnutí vedle textu **Tento pracovní prostor** ve sloupci **Připojení OMS**.

   >[!NOTE]
   >Operations Management Suite (OMS) se teď označuje jako protokoly Azure Monitor.
   
5. V horní části další stránky klikněte na **Odpojit**.
6. V potvrzovacím okně klepněte na tlačítko **Ano** a odpojte počítač od správy.

## <a name="next-steps"></a>Další kroky

* Informace o správě změn nastavení souborů a registru na virtuálních počítačích najdete v tématu [Sledování změn softwaru v prostředí pomocí řešení Change Tracking](../log-analytics/log-analytics-change-tracking.md).
* Další informace o správě aktualizací Windows a balíčků na virtuálních počítačích najdete [v tématu Řešení správy aktualizací v Azure](../operations-management-suite/oms-solution-update-management.md).