---
title: Vytvoření vašeho prvního virtuálního počítače v testovacím prostředí ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit svůj první virtuální počítač v testovacím prostředí ve službě Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: fbc5a438-6e02-4952-b654-b8fa7322ae5f
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: c72e10991e27f7d616703e635ee6e1a18122afc5
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55078065"
---
# <a name="create-your-first-vm-in-a-lab-in-azure-devtest-labs"></a>Vytvoření vašeho prvního virtuálního počítače v testovacím prostředí ve službě Azure DevTest Labs

Při počátečním přístup ke službě DevTest Labs a chcete vytvořit svůj první virtuální počítač, bude pravděpodobně uděláte pomocí předem načtené [image z marketplace základní](devtest-lab-configure-marketplace-images.md). Později také budete mít na výběr [vlastní image a vzorec](devtest-lab-add-vm.md) při vytváření více virtuálních počítačů. 

Tento kurz vás provede přidání vašeho prvního virtuálního počítače do testovacího prostředí v DevTest Labs pomocí webu Azure portal.

## <a name="steps-to-add-your-first-vm-to-a-lab-in-azure-devtest-labs"></a>Postup přidání vašeho prvního virtuálního počítače do testovacího prostředí ve službě Azure DevTest Labs
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
3. Vyberte **vytvořit** zadaný virtuální počítač přidat do testovacího prostředí.

   Na stránce testovacího prostředí se zobrazuje stav vytvoření Virtuálního počítače – nejprve jako **vytváření**, pak jako **systémem** po spuštění virtuálního počítače.

## <a name="next-steps"></a>Další postup
* Po vytvoření virtuálního počítače může připojit k virtuálnímu počítači tak, že vyberete **připojit** na stránce Virtuálního počítače.
* Podívejte se na [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md) úplnější informace o přidávání dalších virtuálních počítačů ve vaší laboratoři.
* Prozkoumejte [Galerie šablon QuickStart pro Azure Resource Manageru DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates).
