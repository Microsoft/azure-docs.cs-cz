---
title: Diagnostikování selhání artefaktů ve virtuálním počítači s Azure DevTest Labs | Dokumentace Microsoftu
description: Informace o řešení potíží se selháním artefaktů ve službě Azure DevTest Labs.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38697268"
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Diagnostikování selhání artefaktů v testovacím prostředí 
Po vytvoření artefakt je můžete zkontrolovat, zda bylo úspěšné nebo neúspěšné. Protokoly artefaktů ve službě Azure DevTest Labs obsahují informace, které vám umožní diagnostikovat selhání artefaktů. Máte několik možností pro zobrazení informací o protokolu artefaktů pro virtuální počítač s Windows:

* Na webu Azure Portal
* Ve virtuálním počítači

> [!NOTE]
> Zajistíte, že chyby jsou správně identifikovat a vysvětlení, je důležité, že artefakt má správnou strukturu. Informace o tom, jak správně vytvořit artefakt najdete v tématu [při vytváření vlastních artefaktů](devtest-lab-artifact-author.md). K prohlédnutí příkladu správně strukturovaný artefaktu, podívejte se [testování typy parametrů](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes) artefaktů.

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Řešení potíží se selháním artefaktů pomocí webu Azure portal

1. Na webu Azure Portal, v seznamu prostředků vyberte testovacího prostředí.
2. Vyberte virtuální počítač Windows, který obsahuje artefakt, který chcete prozkoumat.
3. V levém panelu klikněte v části **Obecné**vyberte **artefakty**. Zobrazí se seznam artefakty přidružené k tomuto virtuálnímu počítači. Název artefaktu a stav artefaktů jsou uvedeny.

   ![Stav artefaktu](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. Vyberte artefakt, který ukazuje **neúspěšné** stav. Otevře se artefakt. Zobrazí se zpráva rozšíření, která obsahuje podrobnosti o chybě artefaktu.

   ![Chybová zpráva artefaktu](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>Řešení potíží se selháním artefaktů z v rámci virtuálního počítače

1. Přihlaste se k virtuálnímu počítači, který obsahuje artefakt, který chcete diagnostikovat.
2. Přejděte na C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\\*1.9*\Status, kde *1.9* je číslo verze rozšíření vlastních skriptů Azure.

   ![Stav souboru](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. Otevřít **stav** souboru.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Související blogové příspěvky
* [Připojte se k virtuální počítač do existující domény služby Active Directory pomocí šablony Resource Manageru ve službě DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Další postup
* Zjistěte, jak [přidání úložiště Git do testovacího prostředí](devtest-lab-add-artifact-repo.md).

