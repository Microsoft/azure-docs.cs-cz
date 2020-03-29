---
title: Diagnostika selhání artefaktů ve virtuálním počítači Azure DevTest Labs
description: DevTest Labs poskytují informace, které můžete použít k diagnostice selhání artefaktu. Tento článek ukazuje, jak řešit chyby artefaktů.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 115e0086-3293-4adf-8738-9f639f31f918
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 7229f1ee4061eb38b7c6da09df21102ab302ab42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760313"
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Diagnostika selhání artefaktů v laboratoři 
Po vytvoření artefaktu můžete zkontrolovat, zda byl úspěšný nebo neúspěšný. Protokoly artefaktů v azure devtest labs poskytují informace, které můžete použít k diagnostice selhání artefaktu. Máte několik možností pro zobrazení informací protokolu artefaktů pro virtuální hod systému Windows:

* Na webu Azure Portal
* Ve virtuálním min

> [!NOTE]
> Chcete-li zajistit, že chyby jsou správně identifikovány a vysvětleny, je důležité, aby artefakt má správnou strukturu. Informace o tom, jak správně vytvořit artefakt, naleznete [v tématu Vytvoření vlastních artefaktů](devtest-lab-artifact-author.md). Chcete-li zobrazit příklad správně strukturované artefakt, podívejte se [na test typy parametrů](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes) artefakt.

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Poradce při potížích s chybami artefaktů pomocí portálu Azure

1. Na webu Azure Portal vseznamu prostředků vyberte testovací prostředí.
2. Zvolte virtuální ms systému Windows, který obsahuje artefakt, který chcete prozkoumat.
3. V levém panelu v části **OBECNÉ**vyberte **artefakty**. Zobrazí se seznam artefaktů přidružených k tomuto virtuálnímu počítače. Název artefaktu a stav artefaktu jsou uvedeny.

   ![Stav artefaktu](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. Vyberte artefakt, který zobrazuje **stav Selhání.** Artefakt se otevírá. Zobrazí se zpráva rozšíření, která obsahuje podrobnosti o selhání artefaktu.

   ![Chybová zpráva artefaktu](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>Poradce při potížích s chybami artefaktů z virtuálního počítače

1. Přihlaste se k virtuálnímu virtuálnímu mísu, který obsahuje artefakt, který chcete diagnostikovat.
2. Přejděte na C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\\*1.9*\Status, kde *1.9* je číslo verze rozšíření Azure Script.

   ![Stavový soubor](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. Otevřete **stavový** soubor.

Pokyny k hledání souborů protokolu na virtuálním počítači **SIF** najdete v následujícím článku: [Použití rozšíření Azure Custom Script Extension Version 2 s virtuálními počítači s Linuxem](../virtual-machines/extensions/custom-script-linux.md#troubleshooting)


## <a name="related-blog-posts"></a>Související příspěvky blogu
* [Připojení virtuálního počítače k existující doméně služby Active Directory pomocí šablony Správce prostředků v devTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Další kroky
* Přečtěte si, jak [přidat úložiště Git do testovacího prostředí](devtest-lab-add-artifact-repo.md).

