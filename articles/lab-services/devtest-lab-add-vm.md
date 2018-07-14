---
title: Přidání virtuálního počítače do testovacího prostředí ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak přidat virtuální počítač do testovacího prostředí ve službě Azure DevTest Labs
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
ms.date: 07/11/2018
ms.author: spelluru
ms.openlocfilehash: 9ddf44ef933270c08b42f67387866cd7a3b34719
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39004075"
---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Přidání virtuálního počítače do testovacího prostředí ve službě Azure DevTest Labs
Pokud už máte [vytvoření vašeho prvního virtuálního počítače](devtest-lab-create-first-vm.md), pravděpodobně jste to v předem načtené [image z marketplace](devtest-lab-configure-marketplace-images.md). Nyní, pokud chcete přidat další virtuální počítače do testovacího prostředí, můžete také *základní* , který je buď [vlastní image](devtest-lab-create-template.md) nebo [vzorec](devtest-lab-manage-formulas.md). Tento kurz vás provede Přidání virtuálního počítače do testovacího prostředí v DevTest Labs pomocí webu Azure portal.

Tento článek také ukazuje, jak spravovat artefakty pro virtuální počítač ve vaší laboratoři.

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Postup přidání virtuálního počítače do testovacího prostředí ve službě Azure DevTest Labs
1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu.
1. V seznamu testovacích prostředí vyberte prostředí, ve kterém chcete vytvořit virtuální počítač.  
1. Cvičení **přehled** vyberte **+ přidat**.  

    ![Přidání tlačítka virtuálního počítače](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. Na **vyberte bázi** podokně, vyberte základ pro virtuální počítač.
1. Na **virtuálního počítače** podokně, zadejte název pro nový virtuální počítač v **název virtuálního počítače** textového pole.

    ![Podokno virtuálního počítače testovacího prostředí](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. Zadejte **uživatelské jméno** , která jsou udělena oprávnění správce na virtuálním počítači.  
1. Pokud chcete použít heslo uložené v [službou Azure key vault](devtest-lab-store-secrets-in-key-vault.md)vyberte **použít uložený tajný kód**a zadat hodnotu klíče, který odpovídá váš tajný klíč (heslo). V opačném případě zadejte heslo do textového pole s popiskem **zadejte hodnotu**. Další informace o ukládání tajných klíčů v trezoru klíčů a jejich používání při vytváření prostředků testovacího prostředí, najdete v článku [Store tajné kódy ve službě Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md).
1. **Typ disku virtuálního počítače** Určuje, jaký typ disku úložiště je povolený pro virtuální počítače v testovacím prostředí.
2. Vyberte **velikost virtuálního počítače** a vyberte jednu z předdefinovaných položek, které určují jader procesoru, velikosti paměti RAM a velikost pevného disku virtuálního počítače k vytvoření.
3. Vyberte **artefakty** a – z seznam artefakty - vybrat a nakonfigurovat artefakty, které chcete přidat do základní image.
    **Poznámka:** Pokud začínáte s DevTest Labs začínáte nebo konfigurace artefakty, vyhledejte [k virtuálnímu počítači přidat existující artefakt](#add-an-existing-artifact-to-a-vm) části a pak se sem vraťte po dokončení.
4. Vyberte **upřesňující nastavení** konfigurovat možnosti sítě Virtuálního počítače a možností vypršení platnosti. 

   Chcete-li nastavit možnost vypršení platnosti, zvolte ikonu kalendáře pro výběr data, na kterém virtuální počítač se automaticky odstraní.  Ve výchozím nastavení virtuální počítač nikdy nevyprší. 
1. Pokud chcete zobrazit nebo zkopírujte šablony Azure Resource Manageru, přečtěte si [šablonu uložte Azure Resource Manageru](#save-azure-resource-manager-template) části a vraťte se sem po dokončení.
1. Vyberte **vytvořit** zadaný virtuální počítač přidat do testovacího prostředí.
1. Podokno testovací prostředí se zobrazuje stav vytvoření Virtuálního počítače – nejprve jako **vytváření**, pak jako **systémem** po spuštění virtuálního počítače.

> [!NOTE]
> [Přidat nárokovatelných virtuálních počítačů](devtest-lab-add-claimable-vm.md) ukazuje, jak nastavit nárokovatelných virtuální počítač tak, aby byly k dispozici pro všechny uživatele v testovacím prostředí.
>
>

## <a name="add-an-existing-artifact-to-a-vm"></a>Přidat existující artefakt do virtuálního počítače
Při vytváření virtuálního počítače, můžete přidat existující artefakty. Pro každé testovací prostředí obsahuje artefakty z veřejného úložiště artefaktů DevTest Labs, stejně jako artefakty, které jste vytvořili a přidání úložiště artefaktů.

* Azure DevTest Labs *artefakty* vám umožní zadat *akce* prováděných při zřízení virtuálního počítače, jako je například spouštění skriptů Windows Powershellu, příkazů Bash a instalace softwaru.
* Artefakt *parametry* umožňují přizpůsobit artefakt pro váš konkrétní scénář

Chcete zjistit, jak vytvořit artefakty, najdete v článku, [informace o vytváření vlastních artefaktů pro použití s DevTest Labs](devtest-lab-artifact-author.md).

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu.
1. V seznamu testovacích prostředí vyberte prostředí obsahující tento virtuální počítač, se kterým chcete pracovat.  
1. Vyberte **Moje virtual machines**.
1. Vyberte požadovaný virtuální počítač.
1. Vyberte **spravovat artefakty**. 
1. Vyberte **použít artefakty**.
1. Na **použít artefakty** podokně, vyberte prosím artefakt, který chcete přidat do virtuálního počítače.
1. Na **přidání artefaktu** podokně, zadejte hodnoty požadovaných parametrů a všechny volitelné parametry, které potřebujete.  
1. Vyberte **přidat** přidat artefakt a vraťte **použít artefakty** podokně.
1. Pokračujte v přidávání artefaktů podle potřeby pro váš virtuální počítač.
1. Po přidání artefaktů můžete [změnit pořadí, ve kterém jsou spuštěny artefakty](#change-the-order-in-which-artifacts-are-run). Můžete také přejít zpět k [zobrazit nebo upravit artefakt](#view-or-modify-an-artifact).
1. Při přidávání artefaktů budete mít, vyberte **použít**

## <a name="change-the-order-in-which-artifacts-are-run"></a>Změnit pořadí, ve kterém jsou spuštěny artefaktů
Ve výchozím nastavení akcí artefakty provádějí v pořadí, ve kterém byly přidány k virtuálnímu počítači. Následující postup ukazuje, jak změnit pořadí, ve kterém jsou spuštěny artefakty.

1. V horní části **použít artefakty** podokně, vyberte odkaz, která udává počet artefaktů, které byly přidány k virtuálnímu počítači.
   
    ![Počet artefaktů, které jsou přidány do virtuálního počítače](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Na **vybrané artefakty** podokně přetáhněte a umístěte artefakty do požadovaného pořadí. **Poznámka:** Pokud máte potíže při přetahování artefaktu, ujistěte se, že přetáhnete na levé straně artefaktu. 
1. Až to budete mít, vyberte **OK**.  

## <a name="view-or-modify-an-artifact"></a>Zobrazit nebo upravit artefakt
Následující kroky ukazují, jak zobrazit nebo upravit parametry artefaktu:

1. V horní části **použít artefakty** podokně, vyberte odkaz, která udává počet artefaktů, které byly přidány k virtuálnímu počítači.
   
    ![Počet artefaktů, které jsou přidány do virtuálního počítače](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Na **vybrané artefakty** podokně, vyberte prosím artefakt, který chcete zobrazit nebo upravit.  
1. Na **přidání artefaktu** podokno, zkontrolujte všechny potřebné změny a vyberte **OK** zavřete **přidání artefaktu** podokně.
1. Vyberte **OK** zavřete **vybrané artefakty** podokně.

## <a name="save-azure-resource-manager-template"></a>Uložit šablonu Azure Resource Manageru
Šablony Azure Resource Manageru poskytuje deklarativní způsob, jak definovat opakovatelné nasazování. Následující postup vysvětluje, jak uložit šablonu Azure Resource Manageru pro virtuální počítač vytváří.
Po uložení, můžete použít šablony Azure Resource Manageru k [nasazení nových virtuálních počítačů pomocí Azure Powershellu](../azure-resource-manager/resource-group-overview.md#template-deployment).

1. Na **virtuálního počítače** vyberte **zobrazit šablonu ARM**.
2. Na **zobrazení Azure Resource Manageru šablony** podokně, vyberte text šablony.
3. Zkopírování vybraného textu do schránky.
4. Vyberte **OK** zavřete **šablony Azure Resource Manageru zobrazit podokno**.
5. Otevřete textový editor.
6. Vložte text šablony ze schránky.
7. Uložte pro pozdější použití.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další postup
* Po vytvoření virtuálního počítače může připojit k virtuálnímu počítači tak, že vyberete **připojit** v podokně Virtuálního počítače.
* Zjistěte, jak [při vytváření vlastních artefaktů pro virtuální počítač DevTest Labs](devtest-lab-artifact-author.md).
* Prozkoumejte [Galerie šablon QuickStart pro Azure Resource Manageru DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
