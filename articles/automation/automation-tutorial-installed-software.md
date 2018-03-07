---
title: "Zjišťování, jaký software je nainstalovaný na počítačích, pomocí Azure Automation | Dokumentace Microsoftu"
description: "Využijte řešení Inventory ke zjišťování, jaký software je nainstalovaný na počítačích napříč prostředím."
services: automation
keywords: inventory, automation, change, tracking
author: jennyhunter-msft
ms.author: jehunte
ms.date: 02/28/2018
ms.topic: tutorial
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 97cd2c91ca2c70b044518c43d49356918202d5ff
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2018
---
# <a name="discover-what-software-is-installed-on-your-azure-and-non-azure-machines"></a>Zjišťování, jaký software je nainstalovaný na počítačích Azure a jiných počítačích než Azure

V tomto kurzu se naučíte zjistit, jaký software je nainstalovaný ve vašem prostředí. Můžete shromažďovat a zobrazovat inventář softwaru, souborů, linuxových procesů démon, služeb systému Windows a klíčů registru Windows na vašich počítačích. Sledování konfigurací vašich počítačů vám může pomoci přesně identifikovat provozní problémy napříč prostředím a lépe porozumět stavu vašich počítačů.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připojení virtuálního počítače k řešení Change Tracking a Inventory
> * Zobrazení nainstalovaného softwaru
> * Vyhledávání nainstalovaného softwaru v protokolech inventáře

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Předplatné Azure. Pokud ještě žádné nemáte, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Účet Automation](automation-offering-get-started.md), který bude obsahovat sledovací proces, runbooky akcí a úlohu sledovacího procesu.
* [Virtuální počítač](../virtual-machines/windows/quick-create-portal.md) pro připojení.

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese http://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Povolení řešení Change Tracking a Inventory

Pro účely tohoto kurzu je nejprve potřeba povolit pro váš virtuální počítač řešení Change Tracking a Inventory. Pokud jste už dříve pro virtuální počítač povolili řešení **Change Tracking**, tento krok není nezbytný.

1. V nabídce vlevo vyberte **Virtuální počítače** a ze seznamu vyberte virtuální počítač.
2. V nabídce vlevo v části **Operace** klikněte na **Inventory**. Otevře se stránka **Povolit řešení Change Tracking a Inventory**.

![Banner konfigurace připojení k řešení Inventory](./media/automation-tutorial-installed-software/enableinventory.png)

Pokud chcete řešení povolit, nakonfigurujte umístění, pracovní prostor Log Analytics a účet Automation, které se mají použít, a klikněte na **Povolit**. Pokud se pole zobrazují šedě, znamená to, že pro daný virtuální počítač je povolené jiné řešení automatizace a musí se použít stejný pracovní prostor a účet Automation.

Pracovní prostor [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) slouží ke shromažďování dat generovaných funkcemi a službami, jako je řešení Inventory.
Tento pracovní prostor poskytuje možnost kontroly a analýzy dat z několika zdrojů na jednom místě.

Povolení řešení může trvat až 15 minut. Během této doby byste neměli zavírat okno prohlížeče.
Po povolení řešení začnou do Log Analytics proudit informace o nainstalovaném softwaru a změnách na virtuálních počítačích.
Zpřístupnění dat pro analýzu může trvat 30 minut až 6 hodin.

## <a name="view-installed-software"></a>Zobrazení nainstalovaného softwaru

Jakmile budou řešení Change Tracking a Inventory povolená, můžete zobrazit výsledky na stránce **Inventory**.

Ve vašem virtuálním počítači v části **OPERACE** vyberte **Inventory**.

Na stránce **Inventory** klikněte na kartu **Software**.

Na kartě **Software** je tabulkový seznam nalezeného softwaru. Software se seskupuje podle názvu a verze.

V tabulce jsou zobrazené základní podrobnosti o jednotlivých záznamech softwaru. Mezi tyto podrobnosti patří název softwaru, verze, vydavatel, čas poslední aktualizace (nejnovější čas aktualizace hlášený počítačem ve skupině) a počítače (počet počítačů s tímto softwarem).

![Inventář softwaru](./media/automation-tutorial-installed-software/inventory-software.png)

Kliknutím na nějaký řádek zobrazíte vlastnosti záznamu softwaru a názvy počítačů s tímto softwarem.

Pokud hledáte konkrétní software nebo skupinu softwaru, můžete je vyhledat v textovém poli přímo nad seznamem softwaru.
Filtr umožňuje vyhledávat podle názvu softwaru, verze nebo vydavatele.

Pokud například vyhledáte Contoso, vrátí se veškerý software, jehož název, vydavatel nebo verze obsahuje text Contoso.

## <a name="search-inventory-logs-for-installed-software"></a>Vyhledávání nainstalovaného softwaru v protokolech inventáře

Inventarizace generuje data protokolu, která se odesílají do Log Analytics. Pokud chcete v protokolech hledat spouštěním dotazů, v horní části okna **Inventory** vyberte **Log Analytics**.

Data řešení Inventory se ukládají jako typ **ConfigurationData** (Konfigurační data).
Následující ukázkový dotaz Log Analytics vrátí vydavatele, kteří obsahují text Microsoft, a počet záznamů softwaru (seskupených podle názvu softwaru nebo počítače) pro jednotlivé vydavatele.

```
ConfigurationData
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
| where ConfigDataType == "Software"
| search Publisher:"Microsoft"
| summarize count() by Publisher
```

Další informace o provozu a prohledávání souborů protokolů v Log Analytics najdete na stránce [Azure Log Analytics](https://docs.loganalytics.io/index).

### <a name="single-machine-inventory"></a>Inventarizace jediného počítače

Pokud chcete zobrazit inventář softwaru pro jediný počítač, můžete ze stránky prostředku virtuálního počítače Azure přejít k inventáři nebo pomocí Log Analytics vyfiltrovat odpovídající počítač. Následující příklad dotazu Log Analytics vrátí seznam softwaru pro počítač ContosoVM.

```
ConfigurationData
| where ConfigDataType == "Software" 
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak zobrazovat inventář softwaru, a například jste se naučili:

> [!div class="checklist"]
> * Připojení virtuálního počítače k řešení Change Tracking a Inventory
> * Zobrazení nainstalovaného softwaru
> * Vyhledávání nainstalovaného softwaru v protokolech inventáře

Další informace najdete v přehledu řešení Change Tracking a Inventory.

> [!div class="nextstepaction"]
> [Řešení Change Tracking a Inventory](automation-change-tracking.md)