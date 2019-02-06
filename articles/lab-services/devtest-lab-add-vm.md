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
ms.date: 01/25/2019
ms.author: spelluru
ms.openlocfilehash: 69c0ce73fa5c29a2d0e49d9c4bb15a855fadc75b
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55746780"
---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Přidání virtuálního počítače do testovacího prostředí ve službě Azure DevTest Labs
Pokud už máte [vytvoření vašeho prvního virtuálního počítače](tutorial-create-custom-lab.md#add-a-vm-to-the-lab), pravděpodobně jste to v předem načtené [image z marketplace](devtest-lab-configure-marketplace-images.md). Nyní, pokud chcete přidat další virtuální počítače do testovacího prostředí, můžete také *základní* , který je buď [vlastní image](devtest-lab-create-template.md) nebo [vzorec](devtest-lab-manage-formulas.md). Tento kurz vás provede Přidání virtuálního počítače do testovacího prostředí v DevTest Labs pomocí webu Azure portal.

Tento článek také ukazuje, jak spravovat artefakty pro virtuální počítač ve vaší laboratoři.

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Postup přidání virtuálního počítače do testovacího prostředí ve službě Azure DevTest Labs
1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **všechny služby**a pak vyberte **DevTest Labs** v **DEVOPS** oddílu. Pokud vyberete * (hvězdičkami) vedle položky **DevTest Labs** v **DEVOPS** oddílu. Tato akce přidá **DevTest Labs** do levé navigační nabídce tak, aby k němu máte přístup snadno dalším. Pak můžete vybrat **DevTest Labs** v navigační nabídce vlevo.

    ![Vyberte všechny služby – DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
1. V seznamu testovacích prostředí vyberte prostředí, ve kterém chcete vytvořit virtuální počítač.  
2. Cvičení **přehled** stránce **+ přidat**.  

    ![Přidání tlačítka virtuálního počítače](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)
1. Na **vyberte bázi** vyberte marketplace image pro virtuální počítač.
1. Na **základní nastavení** karty **virtuálního počítače** stránce, proveďte následující akce: 
    1. Zadejte název pro virtuální počítač v **název virtuálního počítače** textového pole. Do textového pole je pro vás předem vyplněný s jedinečným názvem automaticky generované. Název odpovídá uživatelské jméno v e-mailovou adresu, za nímž následuje jedinečný 3 číslice. Tato funkce vám ušetří čas přemýšlení o název počítače a zadávat pokaždé, když vytváříte počítač. Pokud budete chtít, můžete přepsat toto pole automaticky vyplněný s názvem podle vašeho výběru. Pokud chcete přepsat automaticky vyplněný název pro virtuální počítač, zadejte název do **název virtuálního počítače** textového pole.
    2. Zadejte **uživatelské jméno** , která jsou udělena oprávnění správce na virtuálním počítači. **Uživatelské jméno** pro počítač je předem vyplněná jedinečný automaticky generovaný název. Název odpovídá uživatelské jméno v e-mailovou adresu. Tato funkce vám ušetří čas při rozhodování o uživatelského jména pokaždé, když vytvoříte nový počítač. Pokud chcete znovu, můžete přepsat toto pole automaticky vyplněný s uživatelským jménem podle svého výběru. Chcete-li přepsat automaticky vyplněné hodnoty pro uživatelské jméno, zadejte hodnotu v **uživatelské jméno** textového pole. Tomuto uživateli je udělen **správce** oprávnění na virtuálním počítači.
    3. Pokud vytváříte první virtuální počítač v testovacím prostředí, zadejte **heslo** pro daného uživatele. Chcete-li uložit toto heslo jako výchozí heslo ve službě Azure key vault související s testovacím prostředí, vyberte **uložit jako výchozí heslo**. Výchozí heslo se uloží ve službě key vault s názvem: **VmPassword**. Při pokusu o vytvoření dalších virtuálních počítačů v testovacím prostředí, **VmPassword** je automaticky vybrána pro **heslo**. Chcete-li přepsat hodnotu, zrušte **použít uložený tajný kód** zaškrtněte políčko a zadejte heslo. 

        ![Výběr základní image](./media/tutorial-create-custom-lab/new-virtual-machine.png)

        Můžete také tajné kódy ve službě key vault nejdříve uložte a pak použít při vytváření virtuálního počítače v testovacím prostředí. Další informace najdete v tématu [Store tajné kódy ve službě key vault](devtest-lab-store-secrets-in-key-vault.md). Chcete-li použít heslo uložené ve službě key vault, vyberte **použít uložený tajný kód**a zadat hodnotu klíče, který odpovídá váš tajný klíč (heslo).
    4. V **další možnosti** vyberte **změnit velikost**. Vyberte jednu z předdefinovaných položek, které určují jader procesoru, velikosti paměti RAM a velikost pevného disku virtuálního počítače k vytvoření.
    5. Vyberte **přidat nebo odebrat artefakty**. Vyberte a nakonfigurujte artefakty, které chcete přidat do základní image.
    **Poznámka:** Pokud začínáte s DevTest Labs začínáte nebo konfigurace artefakty, vyhledejte [k virtuálnímu počítači přidat existující artefakt](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) části a pak se sem vraťte po dokončení.
2. Přepněte **Upřesnit nastavení** kartě v horní části a proveďte následující akce:
    1. Chcete-li změnit virtuální síť, virtuální počítač, vyberte **změnit virtuální síť**. 
    2. Pokud chcete změnit podsíť, vyberte **změnit podsíť**. 
    3. Určete, jestli je IP adresa virtuálního počítače **veřejné, privátní nebo sdílené**. 
    4. Pokud chcete automaticky odstranit virtuální počítač, zadejte **datum a čas vypršení**. 
    5. Chcete-li virtuální počítač nárokovatelných uživatelem testovacího prostředí, vyberte **Ano** pro **nastavit tento počítač nárokovatelných** možnost. 
    6. Zadejte počet **instancí virtuálních počítačů** chcete zpřístupnit uživatelům testovacího prostředí. 

        ![Výběr základní image](./media/tutorial-create-custom-lab/new-vm-advanced-settings.png)
1. Vyberte **vytvořit** zadaný virtuální počítač přidat do testovacího prostředí.

   Na stránce testovacího prostředí se zobrazuje stav vytvoření Virtuálního počítače – nejprve jako **vytváření**, pak jako **systémem** po spuštění virtuálního počítače.

    ![Stav vytváření virtuálního počítače](./media/tutorial-create-custom-lab/vm-creation-status.png)

## <a name="add-an-existing-artifact-to-a-vm"></a>Přidat existující artefakt do virtuálního počítače
Při vytváření virtuálního počítače, můžete přidat existující artefakty. Pro každé testovací prostředí obsahuje artefakty z veřejného úložiště artefaktů DevTest Labs, stejně jako artefakty, které jste vytvořili a přidání úložiště artefaktů.

* Azure DevTest Labs *artefakty* vám umožní zadat *akce* prováděných při zřízení virtuálního počítače, jako je například spouštění skriptů Windows Powershellu, příkazů Bash a instalace softwaru.
* Artefakt *parametry* umožňují přizpůsobit artefakt pro váš konkrétní scénář

Chcete zjistit, jak vytvořit artefakty, najdete v článku, [informace o vytváření vlastních artefaktů pro použití s DevTest Labs](devtest-lab-artifact-author.md).

1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
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

1. Na **virtuálního počítače** vyberte **zobrazení šablony Azure Resource Manageru**.
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
