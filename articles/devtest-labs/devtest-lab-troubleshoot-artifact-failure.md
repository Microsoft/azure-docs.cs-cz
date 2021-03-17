---
title: Diagnostika selhání artefaktů ve Azure DevTest Labsm virtuálním počítači
description: DevTest Labs poskytují informace, které můžete použít k diagnostice selhání artefaktu. V tomto článku se dozvíte, jak řešit chyby artefaktů.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 440ce6a537ac8d6a21ae8010bfbb3c38a82bf01e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85480809"
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Diagnostika selhání artefaktů v testovacím prostředí 
Po vytvoření artefaktu můžete zjistit, zda bylo úspěšné nebo neúspěšné. Protokoly artefaktů v Azure DevTest Labs poskytují informace, které můžete použít k diagnostice selhání artefaktu. Máte několik možností, jak zobrazit informace protokolu artefaktu pro virtuální počítač s Windows:

* Na webu Azure Portal
* Ve virtuálním počítači

> [!NOTE]
> Aby bylo zajištěno, že chyby jsou správně identifikovány a vysvětleny, je důležité, aby artefakt měl správnou strukturu. Informace o tom, jak správně sestavit artefakt, najdete v tématu [Vytvoření vlastních artefaktů](devtest-lab-artifact-author.md). Chcete-li zobrazit příklad správně strukturovaného artefaktu, podívejte se na artefakt [typů testovacích parametrů](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes) .

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Řešení chyb artefaktů pomocí Azure Portal

1. V Azure Portal v seznamu prostředků vyberte testovací prostředí.
2. Vyberte virtuální počítač s Windows, který obsahuje artefakt, který chcete prozkoumat.
3. Na levém panelu v části **Obecné**vyberte **artefakty**. Zobrazí se seznam artefaktů přidružených k tomuto virtuálnímu počítači. Název artefaktu a stav artefaktu jsou označeny.

   ![Stav artefaktu](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure-new.png)

4. Vyberte artefakt, který zobrazuje stav **selhání** . Spustí se artefakt. Zobrazí se zpráva o rozšíření, která obsahuje podrobnosti o selhání artefaktu.

   ![Chybová zpráva artefaktu](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>Řešení chyb artefaktů v rámci virtuálního počítače

1. Přihlaste se k virtuálnímu počítači, který obsahuje artefakt, který chcete diagnostikovat.
2. Přejít na C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension \\ *1,9*\Status, kde *1,9* je číslo verze rozšíření vlastních skriptů Azure.

   ![Stavový soubor](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status-new.png)

3. Otevřete **stavový** soubor.

Pokyny k hledání souborů protokolu na virtuálním počítači se **systémem Linux** najdete v následujícím článku: [použití rozšíření Custom Script pro Azure verze 2 s virtuálními počítači se systémem Linux](../virtual-machines/extensions/custom-script-linux.md#troubleshooting)


## <a name="related-blog-posts"></a>Související blogové příspěvky
* [Připojení virtuálního počítače k existující doméně služby Active Directory pomocí Správce prostředků šablony v DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Další kroky
* Naučte se, jak [Přidat úložiště Git do testovacího prostředí](devtest-lab-add-artifact-repo.md).

