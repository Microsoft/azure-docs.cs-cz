---
title: Seznamte se s tím, jaký software je nainstalovaný na virtuálních počítačích pomocí Azure Automation | Microsoft Docs
description: Tento článek popisuje software nainstalovaný na virtuálních počítačích ve vašem prostředí.
services: automation
keywords: inventář, automatizace, sledování změn
ms.date: 04/11/2018
ms.topic: tutorial
ms.subservice: change-inventory-management
ms.custom: mvc
ms.openlocfilehash: 8451067ae86e95269c0c2f22554e1654ac64c1e4
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593773"
---
# <a name="discover-what-software-is-installed-on-your-vms"></a>Zjistěte, jaký software je nainstalovaný na vašich virtuálních počítačích.

V tomto kurzu se naučíte, jak pomocí funkce Azure Automation Change Tracking a inventarizace zjistit, který software se ve vašem prostředí nainstaloval. Na svých počítačích můžete shromažďovat a zobrazovat inventář softwaru, souborů, procesů démonů Linux, služeb systému Windows a klíčů registru Windows. Sledování konfigurací vašich počítačů vám může pomoci přesně identifikovat provozní problémy napříč prostředím a lépe porozumět stavu vašich počítačů.

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Povolení řešení Change Tracking a Inventory
> * Povolení virtuálního počítače Azure
> * Povolení virtuálního počítače mimo Azure
> * Zobrazení nainstalovaného softwaru
> * Vyhledávání nainstalovaného softwaru v protokolech inventáře

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](./index.yml) , který bude obsahovat sledovací proces a runbooky akcí a úlohu sledovacího procesu.
* [Virtuální počítač](../virtual-machines/windows/quick-create-portal.md) , který má být pro funkci povolen.

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Povolení řešení Change Tracking a Inventory

Pro účely tohoto kurzu je nejprve potřeba povolit řešení Change Tracking a Inventory. Pokud jste tuto funkci dříve povolili, tento krok není nezbytný.

>[!NOTE]
>Pokud jsou pole šedá, pro virtuální počítač je povolena jiná funkce automatizace a je nutné použít stejný pracovní prostor a účet Automation.

1. Přejděte do svého účtu Automation a v části **Správa konfigurace** vyberte **inventarizace** nebo **sledování změn** .

2. Vyberte pracovní prostor [Log Analytics](../azure-monitor/logs/log-query-overview.md) . Tento pracovní prostor shromažďuje data generovaná funkcemi, jako jsou Change Tracking a inventář. Tento pracovní prostor poskytuje možnost kontroly a analýzy dat z několika zdrojů na jednom místě.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

3. Vyberte účet Automation, který se má použít.

4. Nakonfigurujte umístění pro nasazení.

5. Kliknutím na **Povolit** nasadíte funkci pro svůj virtuální počítač. 

    ![Banner Konfigurace inventáře](./media/automation-tutorial-installed-software/enableinventory.png)

Během instalace se virtuální počítač zřídí s agentem Log Analytics pro Windows a [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md). Povolení Change Tracking a inventáře může trvat až 15 minut. Během této doby byste neměli zavírat okno prohlížeče.

Po povolení funkce budou informace o nainstalovaném softwaru a změnách na virtuálním počítači toky Azure Monitor protokoly. Zpřístupnění dat pro analýzu může trvat 30 minut až 6 hodin.

## <a name="add-an-azure-vm-to-change-tracking-and-inventory"></a>Přidání virtuálního počítače Azure do Change Tracking a inventáře

1. Ve svém účtu Automation v části **Správa konfigurace** přejděte na **inventář** nebo **sledování změn** .

2. Vyberte **+ Přidat virtuální počítač Azure**.

3. Ze seznamu virtuálních počítačů vyberte svůj virtuální počítač. 

4. Kliknutím na **Povolit** povolte na virtuálním počítači Change Tracking a inventář. Agent Log Analytics pro Windows je nasazený na virtuálním počítači a nakonfiguruje virtuální počítač tak, aby komunikoval s pracovním prostorem Log Analytics. Operace nastavení může trvat několik minut. 

5. V tomto okamžiku můžete v případě potřeby vybrat nový virtuální počítač ze seznamu, který chcete funkci povolit.

## <a name="add-a-non-azure-machine-to-change-tracking-and-inventory"></a>Přidání počítače mimo Azure do Change Tracking a inventáře

Povolení počítačů mimo Azure pro funkci:

1. V závislosti na operačním systému nainstalujte [agenta Log Analytics pro Windows](../azure-monitor/agents/agent-windows.md) nebo [agenta Log Analytics pro systém Linux](automation-linux-hrw-install.md). 

2. Přejděte do svého účtu Automation a v části **Správa konfigurace** přejděte na **inventář** nebo **sledování změn** . 

3. Klikněte na **spravovat počítače**. Zobrazí se seznam počítačů, které hlásí do vašeho pracovního prostoru Log Analytics, u kterých není povolený Change Tracking a inventář. Vyberte vhodnou možnost pro vaše prostředí:

    * **Povolit na všech dostupných počítačích** – Tato možnost povolí funkci na všech počítačích, které v tuto chvíli nahlásí do vašeho pracovního prostoru Log Analytics.
    * **Povolit na všech dostupných počítačích a v budoucích počítačích** – Tato možnost povolí funkci na všech počítačích, které se hlásí do vašeho pracovního prostoru Log Analytics a následně do všech budoucích počítačů přidaných do pracovního prostoru.
    * **Povolit na vybraných počítačích** – Tato možnost povolí funkci jenom na počítačích, které jste vybrali.

    ![Správa počítačů](./media/automation-tutorial-installed-software/manage-machines.png)

## <a name="view-installed-software"></a>Zobrazení nainstalovaného softwaru

Jakmile je funkce Change Tracking a inventáře povolená, můžete zobrazit výsledky na stránce inventáře.

1. Ve svém účtu Automation v části **Správa konfigurace** vyberte **inventarizace** .

2. Na stránce Inventory klikněte na kartu **Software**.

3. Všimněte si tabulky obsahující seznam zjištěného softwaru. Software se seskupuje podle názvu a verze. V tabulce jsou zobrazené základní podrobnosti o jednotlivých záznamech softwaru. Mezi tyto podrobnosti patří název softwaru, verze, vydavatel, čas poslední aktualizace (nejnovější čas aktualizace hlášený počítačem ve skupině) a počítače (počet počítačů s tímto softwarem).

    ![Inventář softwaru](./media/automation-tutorial-installed-software/inventory-software.png)

4. Kliknutím na nějaký řádek zobrazíte vlastnosti záznamu softwaru a názvy počítačů s tímto softwarem.

5. Pokud hledáte konkrétní software nebo skupinu softwaru, můžete je vyhledat v textovém poli přímo nad seznamem softwaru.
Filtr umožňuje vyhledávat podle názvu softwaru, verze nebo vydavatele. Například vyhledávání **Contoso** vrátí veškerý software s názvem, vydavatelem nebo verzí obsahující **Contoso**.

## <a name="search-inventory-logs-for-installed-software"></a>Vyhledávání nainstalovaného softwaru v protokolech inventáře

Change Tracking a inventář generuje data protokolu, která se odesílají do protokolů Azure Monitor. Pokud chcete v protokolech Hledat spouštěním dotazů, vyberte **Log Analytics** v horní části stránky inventáře. Data inventáře se ukládají pod typ `ConfigurationData` .

Následující příklad Log Analytics dotazu vrátí výsledky inventáře pro vydavatele Microsoft Corporation.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| where Publisher == "Microsoft Corporation"
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
```

Další informace o spouštění a hledání souborů protokolu v protokolech Azure Monitor najdete v tématu [protokoly Azure monitor](../azure-monitor/logs/log-query-overview.md).

## <a name="see-the-software-inventory-for-a-single-machine"></a>Zobrazit inventář softwaru pro jeden počítač

Pokud chcete zobrazit inventář softwaru pro jeden počítač, můžete získat přístup k inventáři na stránce prostředku virtuálního počítače Azure nebo použít protokoly Azure Monitor k filtrování dolů na odpovídající počítač. Následující příklad Log Analytics dotazu vrátí seznam softwaru pro počítač s názvem **ContosoVM**.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
| summarize by Publisher, SoftwareName
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak zobrazit inventář softwaru:

> [!div class="checklist"]
> * Povolení řešení Change Tracking a Inventory
> * Povolení virtuálního počítače Azure
> * Povolení virtuálního počítače mimo Azure
> * Zobrazení nainstalovaného softwaru
> * Vyhledávání nainstalovaného softwaru v protokolech inventáře

Pokračujte na Přehled funkce Change Tracking a inventáře a získejte další informace o této funkci.

> [!div class="nextstepaction"]
> [Přehled Change Tracking a inventáře](change-tracking/overview.md)
