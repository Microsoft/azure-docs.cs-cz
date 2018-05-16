---
title: Zjišťování, jaký software je nainstalovaný na počítačích, pomocí Azure Automation | Dokumentace Microsoftu
description: Využijte řešení Inventory ke zjišťování, jaký software je nainstalovaný na počítačích napříč prostředím.
services: automation
keywords: inventory, automation, change, tracking
author: jennyhunter-msft
ms.author: jehunte
ms.date: 04/11/2018
ms.topic: tutorial
ms.service: automation
ms.component: change-inventory-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: cb876a8d8019f5a2a7232c3093c6f64a7b2730e1
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="discover-what-software-is-installed-on-your-azure-and-non-azure-machines"></a>Zjišťování, jaký software je nainstalovaný na počítačích Azure a jiných počítačích než Azure

V tomto kurzu se naučíte zjistit, jaký software je nainstalovaný ve vašem prostředí. Můžete shromažďovat a zobrazovat inventář softwaru, souborů, linuxových procesů démon, služeb systému Windows a klíčů registru Windows na vašich počítačích. Sledování konfigurací vašich počítačů vám může pomoci přesně identifikovat provozní problémy napříč prostředím a lépe porozumět stavu vašich počítačů.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Povolení řešení
> * Připojení virtuálního počítače Azure
> * Připojení virtuálního počítače mimo Azure
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

Pro účely tohoto kurzu je nejprve potřeba povolit řešení Change Tracking a Inventory. Pokud jste už dříve povolili řešení **Change Tracking**, tento krok není nezbytný.

Přejděte do svého účtu Automation a v části **SPRÁVA KONFIGURACE** vyberte **Inventory**.

Zvolte pracovní prostor Log Analytics a účet Automation a kliknutím na **Povolit** povolte řešení. Povolení řešení trvá přibližně 15 minut.

![Banner konfigurace připojení k řešení Inventory](./media/automation-tutorial-installed-software/enableinventory.png)

Pokud chcete řešení povolit, nakonfigurujte umístění, pracovní prostor Log Analytics a účet Automation, které se mají použít, a klikněte na **Povolit**. Pokud se pole zobrazují šedě, znamená to, že pro daný virtuální počítač je povolené jiné řešení automatizace a musí se použít stejný pracovní prostor a účet Automation.

Pracovní prostor [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) slouží ke shromažďování dat generovaných funkcemi a službami, jako je řešení Inventory.
Tento pracovní prostor poskytuje možnost kontroly a analýzy dat z několika zdrojů na jednom místě.

Povolení řešení může trvat až 15 minut. Během této doby byste neměli zavírat okno prohlížeče.
Po povolení řešení začnou do Log Analytics proudit informace o nainstalovaném softwaru a změnách na virtuálních počítačích.
Zpřístupnění dat pro analýzu může trvat 30 minut až 6 hodin.

## <a name="onboard-a-vm"></a>Připojení virtuálního počítače

Ve svém účtu Automation přejděte do části **Inventory** v části **SPRÁVA KONFIGURACE**.

Vyberte **+ Přidat virtuální počítač Azure**. Otevře se stránka **Virtuální počítače**, na které můžete ze seznamu vybrat stávající virtuální počítače. Vyberte virtuální počítač, který chcete připojit. Na stránce, která se otevře, kliknutím na **Povolit** povolte řešení na virtuálním počítači. Na virtuální počítač se nasadí agent Microsoft Management Agent, který se nakonfiguruje ke komunikaci s pracovním prostorem Log Analytics, který jste nakonfigurovali při povolování řešení. Dokončení připojování může trvat několik minut. V tuto chvíli můžete ze seznamu vybrat nový virtuální počítač a připojit další virtuální počítač.

## <a name="onboard-a-non-azure-machine"></a>Připojení počítače mimo Azure

Pokud chcete přidat počítače mimo Azure, nainstalujte agenta pro [Windows](../log-analytics/log-analytics-agent-windows.md) nebo [Linux](automation-linux-hrw-install.md) v závislosti na vašem operačním systému. Jakmile bude agent nainstalovaný, přejděte ve svém účtu Automation do části **Inventory** v části **SPRÁVA KONFIGURACE**. Po kliknutí na **Spravovat počítače** se zobrazí seznam počítačů, které se hlásí do vašeho pracovního prostoru Log Analytics a které nemají řešení povolené. Vyberte odpovídající možnost pro vaše prostředí.

* **Povolit na všech dostupných počítačích** – Tato možnost povolí řešení na všech počítačích, které se aktuálně hlásí do vašeho pracovního prostoru Log Analytics.
* **Povolit na všech dostupných i budoucích počítačích** – Tato možnost povolí řešení na všech počítačích, které se hlásí do vašeho pracovního prostoru Log Analytics, a následně i na všech počítačích, které se do pracovního prostoru přidají v budoucnu.
* **Povolit na vybraných počítačích** – Tato možnost povolí řešení pouze na vybraných počítačích.

![Správa počítačů](./media/automation-tutorial-installed-software/manage-machines.png)

## <a name="view-installed-software"></a>Zobrazení nainstalovaného softwaru

Jakmile budou řešení Change Tracking a Inventory povolená, můžete zobrazit výsledky na stránce **Inventory**.

Ve svém účtu Automation v části **SPRÁVA KONFIGURACE** vyberte **Inventory**.

Na stránce **Inventory** klikněte na kartu **Software**.

Na kartě **Software** je tabulka obsahující seznam nalezeného softwaru. Software se seskupuje podle názvu a verze.

V tabulce jsou zobrazené základní podrobnosti o jednotlivých záznamech softwaru. Mezi tyto podrobnosti patří název softwaru, verze, vydavatel, čas poslední aktualizace (nejnovější čas aktualizace hlášený počítačem ve skupině) a počítače (počet počítačů s tímto softwarem).

![Inventář softwaru](./media/automation-tutorial-installed-software/inventory-software.png)

Kliknutím na nějaký řádek zobrazíte vlastnosti záznamu softwaru a názvy počítačů s tímto softwarem.

Pokud hledáte konkrétní software nebo skupinu softwaru, můžete je vyhledat v textovém poli přímo nad seznamem softwaru.
Filtr umožňuje vyhledávat podle názvu softwaru, verze nebo vydavatele.

Pokud například vyhledáte Contoso, vrátí se veškerý software, jehož název, vydavatel nebo verze obsahuje text Contoso.

## <a name="search-inventory-logs-for-installed-software"></a>Vyhledávání nainstalovaného softwaru v protokolech inventáře

Inventarizace generuje data protokolu, která se odesílají do Log Analytics. Pokud chcete v protokolech hledat spouštěním dotazů, v horní části okna **Inventory** vyberte **Log Analytics**.

Data řešení Inventory se ukládají jako typ **ConfigurationData** (Konfigurační data).
Následující ukázkový dotaz Log Analytics vrátí výsledky inventáře, kde se Publisher (Vydavatel) rovná Microsoft Corporation.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| where Publisher == "Microsoft Corporation"
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
```

Další informace o provozu a prohledávání souborů protokolů v Log Analytics najdete na stránce [Azure Log Analytics](https://docs.loganalytics.io/index).

### <a name="single-machine-inventory"></a>Inventarizace jediného počítače

Pokud chcete zobrazit inventář softwaru pro jediný počítač, můžete ze stránky prostředku virtuálního počítače Azure přejít k inventáři nebo pomocí Log Analytics vyfiltrovat odpovídající počítač.
Následující příklad dotazu Log Analytics vrátí seznam softwaru pro počítač ContosoVM.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
| summarize by Publisher, SoftwareName
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak zobrazovat inventář softwaru, a například jste se naučili:

> [!div class="checklist"]
> * Povolení řešení
> * Připojení virtuálního počítače Azure
> * Připojení virtuálního počítače mimo Azure
> * Zobrazení nainstalovaného softwaru
> * Vyhledávání nainstalovaného softwaru v protokolech inventáře

Další informace najdete v přehledu řešení Change Tracking a Inventory.

> [!div class="nextstepaction"]
> [Řešení Change Tracking a Inventory](automation-change-tracking.md)