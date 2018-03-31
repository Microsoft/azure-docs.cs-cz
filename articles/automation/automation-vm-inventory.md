---
title: Správa virtuálního počítače Azure se shromažďováním dat pro inventarizaci | Dokumentace Microsoftu
description: Správa virtuálního počítače se shromažďováním dat pro inventarizaci
services: automation
keywords: inventory, automation, change, tracking
author: jennyhunter-msft
ms.author: jehunte
ms.date: 09/13/2017
ms.topic: article
manager: carmonm
ms.openlocfilehash: b23132f6e5693f5d731bf044ac5c2544a9308ee1
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Správa virtuálního počítače Azure se shromažďováním dat pro inventarizaci

Sledování inventáře pro virtuální počítač Azure můžete povolit na stránce prostředků příslušného virtuálního počítače. Tato metoda poskytuje uživatelské rozhraní v prohlížeči pro nastavení a konfiguraci shromažďování dat pro inventarizaci.

## <a name="before-you-begin"></a>Než začnete
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/).
Pokud ještě nemáte virtuální počítač Azure, [vytvořte si virtuální počítač](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal
Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Povolení shromažďování dat pro inventarizaci na stránce prostředků virtuálního počítače

1. V levém podokně webu Azure Portal vyberte **Virtuální počítače**.
2. V seznamu virtuálních počítačů vyberte virtuální počítač.
3. V nabídce **Prostředek** v části **Operace** vyberte **Inventarizace (Preview)**.  
    V horní části okna se zobrazí banner s oznámením, že řešení není povoleno. 
4. Pokud chcete řešení povolit, klikněte na banner.
5. Vyberte pracovní prostor Log Analytics, ve kterém se budou ukládat vaše protokoly dat.  
    Pokud v dané oblasti nemáte k dispozici žádný pracovní prostor, zobrazí se výzva k vytvoření výchozího pracovního prostoru a účtu Automation. 
6. Pokud chcete zahájit připojování vašeho počítače, vyberte **Povolit**.

   ![Zobrazení možností připojení](./media/automation-vm-inventory/inventory-onboarding-options.png)  
    Stavový řádek vás bude informovat o povolování řešení. Tento proces může trvat až 15 minut. Během této doby můžete okno zavřít nebo ho nechat otevřené a jakmile bude řešení povoleno, zobrazí se v něm oznámení. Stav nasazení můžete monitorovat v podokně oznámení.

   ![Zobrazení řešení inventarizace ihned po připojení](./media/automation-vm-inventory/inventory-onboarded.png)

Po dokončení nasazení stavový řádek zmizí. Systém stále shromažďuje data inventáře, ale data ještě nemusí být viditelná. Úplné shromáždění dat může trvat až 24 hodin.

## <a name="configure-your-inventory-settings"></a>Konfigurace nastavení inventarizace

Ve výchozím nastavení jsou pro shromažďování nakonfigurovány software, služby Windows a linuxové procesy démon. Pokud chcete shromažďovat data pro inventarizaci registru a souborů systému Windows, nakonfigurujte nastavení shromažďování dat pro inventarizaci.

1. V zobrazení **Inventarizace (Preview)** vyberte tlačítko **Upravit nastavení** v horní části okna.
2. Pokud chcete přidat nové nastavení shromažďování, výběrem karet **Registr systému Windows**, **Soubory Windows** a **Soubory Linuxu** přejděte do kategorie nastavení, které chcete přidat. 
3. V horní části okna vyberte **Přidat**.
4. Pokud chcete zobrazit podrobné informace o všech vlastnostech nastavení a jejich popisy, přejděte na [stránku dokumentace k inventarizaci](https://aka.ms/configinventorydocs).

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
* Další informace o správě systému Windows a aktualizace balíčků na virtuálních počítačích najdete v tématu [řešení správy aktualizací v Azure](../operations-management-suite/oms-solution-update-management.md).
