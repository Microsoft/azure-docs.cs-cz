---
title: Přidat virtuální počítač do testovacího prostředí v Azure DevTest Labs | Microsoft Docs
description: Zjistěte, jak přidat virtuální počítač do testovacího prostředí v Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 5f953cd6f33e5d46098566740efbf83a5fd80799
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Přidat virtuální počítač do testovacího prostředí v Azure DevTest Labs
Pokud už máte [vytvoření vaší první virtuální počítač](devtest-lab-create-first-vm.md), pravděpodobně jste to v předem načtený [marketplace image](devtest-lab-configure-marketplace-images.md). Nyní, pokud chcete přidat další virtuální počítače do testovacího prostředí, můžete také *základní* to znamená buď [vlastní image](devtest-lab-create-template.md) nebo [vzorec](devtest-lab-manage-formulas.md). Tento kurz vás provede procesem přidání virtuálního počítače do testovacího prostředí v DevTest Labs pomocí portálu Azure.

Tento článek také ukazuje, jak spravovat artefaktů pro virtuální počítač ve svém testovacím prostředí.

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Postup pro přidání virtuálního počítače do testovacího prostředí v Azure DevTest Labs
1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **všechny služby**a potom vyberte **DevTest Labs** ze seznamu.
1. Ze seznamu labs vyberte testovací prostředí, ve kterém chcete vytvořit virtuální počítač.  
1. V tomto prostředí **přehled** podokně, vyberte **+ přidat**.  

    ![Virtuální počítač tlačítko Přidat](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. Na **zvolte na základní** podokně, vyberte základ pro virtuální počítač.
1. Na **virtuálního počítače** podokně, zadejte název pro nový virtuální počítač **název virtuálního počítače** textového pole.

    ![Virtuální počítač podokně testovacího prostředí](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. Zadejte **uživatelské jméno** , jsou udělena oprávnění správce na virtuálním počítači.  
1. Pokud chcete použít heslo uložené v vaše [tajný úložiště](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store), vyberte **použít uložené tajný klíč**a zadejte hodnotu klíče, která odpovídá váš tajný klíč (heslo). Jinak, zadejte heslo do textového pole s názvem bez přípony **zadejte hodnotu**.
1. **Typ disku virtuálního počítače** určuje disku typů úložiště, které u virtuálních počítačů v testovacím prostředí.
1. Vyberte **velikost virtuálního počítače** a vyberte jednu z předdefinovaných položky, které zadejte jader procesoru, velikosti paměti RAM a velikost pevného disku pro vytvoření virtuálního počítače.
1. Vyberte **artefakty** a - ze seznamu artefaktů - vyberte a nakonfigurujte artefaktů, které chcete přidat do základní bitové kopie.
    **Poznámka:** Pokud nepracovali DevTest Labs nebo konfigurace artefaktů, vyhledejte [přidat existující artefaktů pro virtuální počítač](#add-an-existing-artifact-to-a-vm) části a vraťte se sem po dokončení.
1. Vyberte **upřesňující nastavení** nakonfigurovat možnosti sítě a vypršení platnosti možnosti Virtuálního počítače. 

   Chcete-li nastavit možnost vypršení platnosti, zvolte ikonu Kalendář zadat datum, na kterém virtuální počítač se automaticky odstraní.  Ve výchozím nastavení virtuální počítač nikdy nevyprší. 
1. Pokud chcete zobrazit nebo zkopírování šablony Azure Resource Manager, přečtěte si [šablony uložit Azure Resource Manageru](#save-azure-resource-manager-template) části a sem vraťte po dokončení.
1. Vyberte **vytvořit** přidat zadaný virtuální počítač do testovacího prostředí.
1. V podokně testovacího prostředí se zobrazuje stav vytvoření Virtuálního počítače – nejprve jako **vytváření**, pak jako **systémem** po spuštění virtuálního počítače.

> [!NOTE]
> [Přidat virtuální počítač vymahatelných](devtest-lab-add-claimable-vm.md) ukazuje, jak zajistit vymahatelných virtuálního počítače, takže je k dispozici pro použití jakéhokoli uživatele v testovacím prostředí.
>
>

## <a name="add-an-existing-artifact-to-a-vm"></a>Přidat existující artefaktů pro virtuální počítač
Při vytváření virtuálního počítače, můžete přidat existující artefakty. Každý laboratoř zahrnuje artefakty z veřejného úložiště artefaktů DevTest Labs, jakož i artefaktů, které jste vytvořili a přidat do úložiště artefaktů.

* Azure DevTest Labs *artefakty* umožňují zadat *akce* , se provádí při zřizování virtuálních počítačů, jako je například spouštění skriptů prostředí Windows PowerShell, Bash příkazy a instalaci softwaru.
* Artefaktů *parametry* umožňují přizpůsobit artefaktů pro konkrétní scénář

Chcete zjistit, jak vytvořit artefakty, najdete v článku [Naučte se vytvářet vlastní artefaktů pro použití s DevTest Labs](devtest-lab-artifact-author.md).

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **všechny služby**a potom vyberte **DevTest Labs** ze seznamu.
1. Ze seznamu labs vyberte testovací prostředí obsahující počítač, se kterým chcete pracovat.  
1. Vyberte **Můj virtuální počítače**.
1. Vyberte požadovaný virtuální počítač.
1. Vyberte **spravovat artefakty**. 
1. Vyberte **použít artefakty**.
1. Na **použít artefakty** podokně vyberte artefaktů, které chcete přidat do virtuálního počítače.
1. Na **přidat artefaktů** podokně, zadejte hodnoty požadovaných parametrů a všechny volitelné parametry, které potřebujete.  
1. Vyberte **přidat** přidat artefaktu a vrátíte se do **použít artefakty** podokně.
1. Pokračujte v přidávání artefakty potřebné pro virtuální počítač.
1. Po přidání artefakty, můžete [změnit pořadí, ve kterém jsou spuštěny artefakty](#change-the-order-in-which-artifacts-are-run). Můžete také přejít zpět na [zobrazit nebo upravit artefakt](#view-or-modify-an-artifact).
1. Po dokončení přidávání artefakty, vyberte **použít**

## <a name="change-the-order-in-which-artifacts-are-run"></a>Změňte pořadí, ve kterém jsou spuštěné artefaktů
Ve výchozím nastavení akcí artefakty jsou spouštěny v pořadí, ve kterém budou přidány do virtuálního počítače. Následující kroky ukazují, jak chcete-li změnit pořadí, ve kterém jsou spuštěny artefakty.

1. V horní části **použít artefakty** podokně, vyberte odkaz určující počet artefaktů, které byly přidány do virtuálního počítače.
   
    ![Počet artefaktů přidat k virtuálnímu počítači](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Na **vybrané artefakty** podokně přetažení artefakty do požadované pořadí. **Poznámka:** Pokud máte potíže při přetahování artefaktu, ujistěte se, že přetáhnete z levé strany artefaktu. 
1. Až to budete mít, vyberte **OK**.  

## <a name="view-or-modify-an-artifact"></a>Zobrazit nebo upravit artefakt
Následující kroky ukazují, jak lze zobrazit nebo upravit parametry artefakt:

1. V horní části **použít artefakty** podokně, vyberte odkaz určující počet artefaktů, které byly přidány do virtuálního počítače.
   
    ![Počet artefaktů přidat k virtuálnímu počítači](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Na **vybrané artefakty** podokně vyberte artefaktu, který chcete zobrazit nebo upravit.  
1. Na **přidat artefaktů** podokně, ujistěte se, všechny potřebné změny a vyberte **OK** zavřete **přidat artefaktů** podokně.
1. Vyberte **OK** zavřete **vybrané artefakty** podokně.

## <a name="save-azure-resource-manager-template"></a>Uložení šablony Azure Resource Manageru
Šablonu Azure Resource Manager nabízí deklarativní způsob, jak definovat opakovatelných nasazení. Následující kroky popisují, jak uložit šablony Azure Resource Manageru pro vytváření virtuálních počítačů.
Po uložení, můžete použít šablonu Azure Resource Manager [nasadit nové virtuální počítače v prostředí Azure PowerShell](../azure-resource-manager/resource-group-overview.md#template-deployment).

1. Na **virtuálního počítače** podokně, vyberte **šablony ARM zobrazení**.
2. Na **šablony zobrazení Azure Resource Manageru** podokně, vyberte text, šablonu.
3. Kopírovat vybraný text do schránky.
4. Vyberte **OK** zavřete **šablony Azure Resource Manageru zobrazení podokna**.
5. Otevřete textový editor.
6. Vložte text šablony ze schránky.
7. Uložte soubor pro pozdější použití.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další postup
* Po vytvoření virtuálního počítače, můžete připojit k virtuálnímu počítači tak, že vyberete **Connect** v podokně Virtuálního počítače.
* Zjistěte, jak [vytvoření vlastních artefaktů pro virtuální počítač DevTest Labs](devtest-lab-artifact-author.md).
* Prozkoumejte [Galerie šablon DevTest Labs Azure Resource Manager QuickStart](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
