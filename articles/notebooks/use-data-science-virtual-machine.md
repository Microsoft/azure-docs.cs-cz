---
title: Použití virtuálních počítačů Azure Data Science
description: Zjistěte, jak se připojit k virtuálnímu počítači Azure Data Science (DSVM) a rozšířit výpočetní výkon dostupný pro Azure Notebooks Preview.
author: getroyer
manager: andneil
ms.author: getroyer
ms.topic: how-to
ms.date: 06/13/2019
ms.openlocfilehash: b4da63b7b2a6da4316215b85a09ca7420745251c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898406"
---
# <a name="use-azure-data-science-virtual-machines"></a>Použití virtuálních počítačů Azure Data Science

Ve výchozím nastavení se projekty spouštějí na úrovni **Free Compute,** která je omezena na 4 GB paměti a 1 GB dat, aby se zabránilo zneužití. Tato omezení můžete obejít pomocí jiného virtuálního počítače, který jste zřídit v předplatném Azure. Pro tento účel je nejlepší volbou virtuální stroj Azure Data Science (DSVM) pomocí image **virtuálního počítače pro virtuální počítače datové vědy pro Linux (Ubuntu).** Takový DSVM je předkonfigurovaný se vším, co potřebujete pro poznámkové bloky Azure a automaticky se zobrazí v rozevíracím seznamu **Spustit** v poznámkových blocích Azure.

> [!Note]
> Poznámkové bloky Azure se podporují jenom na dsvms vytvořených s bitovou kopii Ubuntu na Linuxu. Poznámkové bloky nejsou podporované v ibloucích Windows 2012, Windows 2016 nebo Linux CentOS.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="create-a-dsvm-instance"></a>Vytvoření instance DSVM

Chcete-li vytvořit novou instanci DSVM, postupujte podle pokynů na [vytvoření virtuálního počítače s datovou vědou Ubuntu](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro). Další informace včetně podrobností o cenách naleznete v [tématu Virtuální počítače pro datové vědy](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="connect-to-the-dsvm"></a>Připojení k DSVM

Po vytvoření DSVM vyberte rozevírací seznam **Spustit** na řídicím panelu projektu poznámkových bloků Azure a vyberte příslušnou instanci DSVM. Rozevírací seznam zobrazuje instance DSVM, pokud jsou splněny následující podmínky:

- Jste přihlášení k poznámkovým blokům Azure pomocí účtu, který používá Azure Active Directory (AAD), jako je například firemní účet.
- Váš účet je připojený k předplatnému Azure.
- Máte jeden nebo více virtuálních počítačů v tomto předplatném, s alespoň přístup čtenáře, který používá data science virtual machine pro Linux (Ubuntu) image.)

![Instance virtuálního počítače datové vědy v rozevíracím seznamu na řídicím panelu projektu](media/project-compute-tier-dsvm.png)

Když vyberete instanci DSVM, poznámkové bloky Azure vás mohou vyzvat k zadání konkrétních přihlašovacích údajů počítače, které se používají při vytváření virtuálního počítače.

> [!Important]
> Uživatelské jméno musí být malá písmena pro použití s JupyterHub.

Pokud některá z podmínek nejsou splněny, stále se můžete připojit k DSVM. V rozevíracím seznamu vyberte možnost **Přímé výpočty,** která vás vyzve k zadání názvu (který se zobrazí v seznamu), IP adresy a portu virtuálního počítače (obvykle 8000, výchozí port, na který JupyterHub naslouchá) a pověření virtuálního počítače:

![Výzva ke shromažďování informací o serveru pro možnost Přímé výpočty](media/project-compute-tier-direct.png)

Tyto hodnoty získáte ze stránky DSVM na webu Azure Portal.

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>Přístup k souborům poznámkových bloků Azure z DSVM

Přístup k systému souborů je podporován pro verze DSVM verze 19.06.15 nebo novější. Chcete-li zkontrolovat verzi, nejprve se připojte k DSVM `curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2018-10-01"` přes SSH a pak spusťte následující příkaz: (musíte použít přesnou IP adresu uvedenou zde). Číslo verze je zobrazeno ve výstupu pro "verzi".

Chcete-li zachovat paritu cest souborů s úrovní **Free Compute,** můžete otevřít pouze jeden projekt najednou na DSVM. Chcete-li otevřít nový projekt, musíte nejprve zavřít otevřený projekt.

Při spuštění projektu na virtuálním počítači, soubory se namontují na kořenový adresář serveru Jupyter (adresář zobrazený v JupyterHub), nahradí výchozí soubory poznámkových bloků Azure. Když vypnete virtuální počítač pomocí tlačítka **Vypnutí** v uzu poznámkového bloku, poznámkové bloky Azure obnoví výchozí soubory.

![Tlačítko Vypnutí v poznámkových blocích Azure](media/shutdown.png)

## <a name="create-new-dsvm-users"></a>Vytvoření nových uživatelů DSVM

Pokud službu DSVM sdílí více uživatelů, můžete se vyhnout vzájemnému blokování vytvořením a použitím uživatele DSVM pro každého uživatele poznámkového bloku:

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na svůj virtuální počítač.
1. V části **Podpora + řešení potíží** na levém okraji vyberte Obnovit **heslo**.
1. Zadejte nové **uživatelské jméno**. Uživatelské jméno musí být malá písmena pro použití s JupyterHub. Zadejte heslo. Pak vyberte **Aktualizovat**. (Existující uživatelská jména nejsou ovlivněna.)
1. Opakujte předchozí krok pro všechny další uživatele.

## <a name="next-steps"></a>Další kroky

Další informace o DSVM s [využitím informací o úvodu do virtuálních počítačů Azure Data Science](/azure/machine-learning/data-science-virtual-machine/overview).
