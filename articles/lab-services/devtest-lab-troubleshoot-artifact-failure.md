---
title: Diagnostika selhání artefaktů ve virtuálním počítači s Azure DevTest Labs | Microsoft Docs
description: Naučte se řešit potíže s artefakty v Azure DevTest Labs.
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
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 8b7a72c03d41d54aa80505e781b6f6d32cd2a2c0
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991367"
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Diagnostika selhání artefaktů v testovacím prostředí 
Po vytvoření artefaktu můžete zjistit, zda bylo úspěšné nebo neúspěšné. Protokoly artefaktů v Azure DevTest Labs poskytují informace, které můžete použít k diagnostice selhání artefaktu. Máte několik možností, jak zobrazit informace protokolu artefaktu pro virtuální počítač s Windows:

* V Azure Portal
* Ve virtuálním počítači

> [!NOTE]
> Aby bylo zajištěno, že chyby jsou správně identifikovány a vysvětleny, je důležité, aby artefakt měl správnou strukturu. Informace o tom, jak správně sestavit artefakt, najdete v tématu [Vytvoření vlastních artefaktů](devtest-lab-artifact-author.md). Chcete-li zobrazit příklad správně strukturovaného artefaktu, podívejte se na artefakt [typů testovacích parametrů](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes) .

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Řešení chyb artefaktů pomocí Azure Portal

1. V Azure Portal v seznamu prostředků vyberte testovací prostředí.
2. Vyberte virtuální počítač s Windows, který obsahuje artefakt, který chcete prozkoumat.
3. Na levém panelu v části **Obecné**vyberte artefakty. Zobrazí se seznam artefaktů přidružených k tomuto virtuálnímu počítači. Název artefaktu a stav artefaktu jsou označeny.

   ![Stav artefaktu](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. Vyberte artefakt, který zobrazuje stav **selhání** . Spustí se artefakt. Zobrazí se zpráva o rozšíření, která obsahuje podrobnosti o selhání artefaktu.

   ![Chybová zpráva artefaktu](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>Řešení chyb artefaktů v rámci virtuálního počítače

1. Přihlaste se k virtuálnímu počítači, který obsahuje artefakt, který chcete diagnostikovat.
2. Přejít na C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\\*1,9*\Status, kde *1,9* je číslo verze rozšíření vlastních skriptů Azure.

   ![Stavový soubor](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. Otevřete **stavový** soubor.

Pokyny k hledání souborů protokolu na virtuálním počítači se **systémem Linux** najdete v následujícím článku: [Použití rozšíření vlastních skriptů Azure verze 2 s virtuálními počítači se systémem Linux](../virtual-machines/extensions/custom-script-linux.md#troubleshooting)


## <a name="related-blog-posts"></a>Související blogové příspěvky
* [Připojení virtuálního počítače k existující doméně služby Active Directory pomocí Správce prostředků šablony v DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Další postup
* Naučte se, jak [Přidat úložiště Git do testovacího prostředí](devtest-lab-add-artifact-repo.md).

