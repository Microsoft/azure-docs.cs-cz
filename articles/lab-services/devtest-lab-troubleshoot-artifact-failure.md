---
title: Diagnostikování selhání artefaktů na virtuálním počítači Azure DevTest Labs | Microsoft Docs
description: Informace o řešení potíží s artefaktů selhání v Azure DevTest Labs.
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: ebc64215683989ce07f4dd88dc352ecaefe184cd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Diagnostikování selhání artefaktů v testovacím prostředí 
Po vytvoření artefakt můžete můžete zkontrolujte, zda byla úspěšná nebo neúspěšná. Protokoly artefaktů v Azure DevTest Labs poskytují informace, které můžete použít k diagnostikování selhání artefaktů. Máte několik možností pro zobrazení informací protokolu artefaktů pro virtuální počítač s Windows:

* Na portálu Azure
* Ve virtuálním počítači

> [!NOTE]
> Zajistit, že chyby jsou správně identifikovat a vysvětlení, je důležité, aby artefaktu má správnou strukturu. Informace o tom, jak správně vytvořit artefakt najdete v tématu [vytvoření vlastních artefaktů](devtest-lab-artifact-author.md). Pokud chcete zobrazit příklad správně strukturovaný artefaktů, podívejte se [testování typy parametrů](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes) artefaktů.

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Řešení potíží artefaktů selhání pomocí portálu Azure

1. Na portálu Azure v seznamu prostředků vyberte testovacího prostředí.
2. Vyberte virtuální počítač Windows, který zahrnuje artefaktu, který chcete prozkoumat.
3. V levém panelu klikněte v části **Obecné**, vyberte **artefakty**. Zobrazí se seznam artefakty související s tohoto virtuálního počítače. Název artefaktu a stav artefaktů jsou vypsány.

   ![Stav artefaktů](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. Vyberte artefakt, který ukazuje **se nezdařilo** stavu. Otevře se artefakt. Se zobrazí zpráva rozšíření, která obsahuje podrobnosti o selhání artefaktu.

   ![Artefaktů chybová zpráva](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>Řešení potíží s selhání artefaktů z virtuálního počítače

1. Přihlaste se k virtuálním počítači, který obsahuje artefaktů, které chcete diagnostikovat.
2. Přejděte na C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\\*1.9*\Status, kde *1.9* je číslo verze rozšíření vlastních skriptů Azure.

   ![Stav souboru](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. Otevřete **stav** souboru.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Příspěvky blogu související
* [Připojení virtuálního počítače do existující domény služby Active Directory pomocí šablony Resource Manageru v DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Další postup
* Zjistěte, jak [přidejte úložiště Git do testovacího prostředí](devtest-lab-add-artifact-repo.md).

