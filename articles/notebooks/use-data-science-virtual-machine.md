---
title: Použití Virtual Machines pro datové vědy v Azure
description: Přečtěte si, jak se připojit k Azure Data Science Virtual Machine (DSVM), abyste rozšířili výpočetní výkon dostupný pro Azure Notebooks Preview.
author: getroyer
manager: andneil
ms.author: getroyer
ms.topic: how-to
ms.date: 06/13/2019
ms.openlocfilehash: 7d65a39c1a1d302e565b59db1644af2c08befb6b
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843158"
---
# <a name="use-azure-data-science-virtual-machines"></a>Použití Virtual Machines pro datové vědy v Azure

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Ve výchozím nastavení jsou projekty spouštěny na **bezplatné výpočetní** úrovni, což je omezeno na 4 GB paměti a 1 GB dat, aby nedocházelo k zneužití. Tato omezení můžete obejít tak, že použijete jiný virtuální počítač, který jste zřídili v rámci předplatného Azure. Pro tento účel je nejlepší volbou Azure Data Science Virtual Machine (DSVM) pomocí Image **Data Science Virtual Machine pro Linux (Ubuntu)** . Takový DSVM je předem nakonfigurovaný se všemi potřebnými pro Azure Notebooks a automaticky se zobrazuje v rozevíracím seznamu **Spustit** v Azure Notebooks.

> [!Note]
> Azure Notebooks se podporuje jenom v DSVMs vytvořených pomocí Image Ubuntu pro Linux. Poznámkové bloky nejsou podporované v imagí Windows 2012, Windows 2016 nebo Linux CentOS.

## <a name="create-a-dsvm-instance"></a>Vytvoření instance DSVM

Pokud chcete vytvořit novou instanci DSVM, postupujte podle pokynů v tématu [Vytvoření Ubuntu Data Science VM](../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md). Další informace, včetně podrobností o cenách, najdete v článku [Virtual Machines pro datové vědy](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="connect-to-the-dsvm"></a>Připojení k DSVM

Po vytvoření DSVM vyberte rozevírací seznam **Spustit** na řídicím panelu projektu Azure Notebooks a vyberte příslušnou instanci DSVM. V rozevíracím seznamu se zobrazí instance DSVM, pokud jsou splněné následující podmínky:

- Jste přihlášení k Azure Notebooks pomocí účtu, který používá Azure Active Directory (AAD), jako je například účet společnosti.
- Váš účet je připojený k předplatnému Azure.
- Máte jeden nebo více virtuálních počítačů v tomto předplatném, s alespoň přístupem Čtenář, který používá Image Data Science Virtual Machine pro Linux (Ubuntu).)

![Instance Data Science Virtual Machine v rozevíracím seznamu na řídicím panelu projekt](media/project-compute-tier-dsvm.png)

Když vyberete instanci DSVM, může Azure Notebooks zobrazit výzvu k zadání přihlašovacích údajů pro konkrétní počítač, které jste použili při vytváření virtuálního počítače.

> [!Important]
> Pro použití s JupyterHub musí mít uživatelské jméno malými písmeny.

Pokud se některá z podmínek nesplní, můžete se ke službě DSVM připojit i nadále. V rozevíracím seznamu vyberte možnost **přímé výpočty** , která vás vyzve k zadání názvu (k zobrazení v seznamu), IP adresy a portu virtuálního počítače (obvykle 8000, výchozího portu, na který JupyterHub naslouchá), a přihlašovacích údajů k virtuálnímu počítači:

![Dotázat se na shromažďování informací o serveru pro možnost přímé COMPUTE](media/project-compute-tier-direct.png)

Tyto hodnoty získáte ze stránky DSVM v Azure Portal.

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>Přístup k souborům Azure Notebooks z DSVM

Přístup k systému souborů je podporován pro DSVM verze 19.06.15 nebo novější. Pokud chcete zjistit verzi, nejdřív se připojte k DSVM přes SSH a pak spusťte následující příkaz: `curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2018-10-01"` (musíte použít přesnou IP adresu uvedenou tady). Číslo verze se zobrazí ve výstupu pro "Version".

Aby se zachovala parita cest k souborům s **bezplatnou výpočetní** vrstvou, můžete na DSVM otevřít jenom jeden projekt. Chcete-li otevřít nový projekt, je třeba nejprve ukončit otevřený projekt.

Když je projekt spuštěn na virtuálním počítači, soubory jsou připojeny do kořenového adresáře serveru Jupyter (adresář zobrazený v JupyterHub) a nahradí výchozí Azure Notebooks soubory. Když virtuální počítač vypnete pomocí tlačítka **vypnout** v uživatelském rozhraní poznámkového bloku, Azure Notebooks obnoví výchozí soubory.

![Tlačítko vypnout v Azure Notebooks](media/shutdown.png)

## <a name="create-new-dsvm-users"></a>Vytvoření nových uživatelů DSVM

Pokud DSVM sdílí více uživatelů, můžete se vyhnout blokování tím, že vytvoříte a použijete uživatele DSVM pro každého uživatele poznámkového bloku:

1. Na [Azure Portal](https://portal.azure.com)přejděte na svůj virtuální počítač.
1. V části **Podpora a řešení potíží** na levém okraji vyberte **resetovat heslo**.
1. Zadejte nové **uživatelské jméno**. Pro použití s JupyterHub musí mít uživatelské jméno malými písmeny. Zadejte heslo. Pak vyberte **aktualizovat**. (Stávající uživatelská jména nejsou ovlivněna.)
1. Opakujte předchozí krok pro všechny další uživatele.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o DSVMs o [Seznámení s Azure Data věda Virtual Machines](../machine-learning/data-science-virtual-machine/overview.md).