---
title: Správa vzorců v Azure DevTest Labs k vytváření virtuálních počítačů | Microsoft Docs
description: Tento článek ukazuje, jak vytvořit vzorec buď ze základní (vlastní image, image Marketplace, nebo jiného vzorce), nebo existujícího virtuálního počítače.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: f012a8c59a8e938334c3e1abc4c7b3ccd0e48d3e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91308890"
---
# <a name="manage-azure-devtest-labs-formulas"></a>Správa vzorců Azure DevTest Labs

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

Tento článek ukazuje, jak vytvořit vzorec buď ze základní (vlastní image, image Marketplace, nebo jiného vzorce), nebo existujícího virtuálního počítače. Tento článek vás také provede správou stávajících vzorců.

## <a name="create-a-formula"></a>Vytvoří vzorec.
Kdokoli, kdo má oprávnění *uživatelů* DevTest Labs, může vytvořit virtuální počítače pomocí vzorce jako základní. Existují dva způsoby, jak vytvořit vzorce: 

* Ze základního použití, pokud chcete definovat všechny charakteristiky vzorce.
* Z existujícího virtuálního počítače testovacího prostředí – použijte, když chcete vytvořit vzorec na základě nastavení existujícího virtuálního počítače.

Další informace o přidání uživatelů a oprávnění najdete v tématu [Přidání vlastníků a uživatelů v Azure DevTest Labs](./devtest-lab-add-devtest-user.md).

### <a name="create-a-formula-from-a-base"></a>Vytvoření vzorce ze základu
Následující kroky vás provedou procesem vytvoření vzorce z vlastní image, obrázku na webu Marketplace nebo jiného vzorce.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

2. Vyberte **všechny služby** a v seznamu vyberte **DevTest Labs** .

3. V seznamu cvičení vyberte požadované testovací prostředí.  

4. Na stránce testovacího prostředí vyberte **vzorce (opakovaně použitelné báze)** v nabídce vlevo.
5. Na stránce **vzorce** vyberte **+ Přidat**.
   
    ![Přidat vzorec](./media/devtest-lab-create-formulas/add-formula.png)
6. Na stránce **Zvolte základní** stránku vyberte základní (vlastní image, obrázek Marketplace nebo obrázek Galerie sdílených imagí), ze kterého chcete vzorec vytvořit.

    :::image type="content" source="./media/devtest-lab-create-formulas/select-base.png" alt-text="Vyberte základní image.":::
1. Na kartě **základní nastavení** na stránce **vytvořit vzorec** zadejte následující hodnoty:
   
    * **Název vzorce** – zadejte název vzorce. Tato hodnota se zobrazí v seznamu základních imagí při vytváření virtuálního počítače. Název se ověří při jeho psaní a pokud není platný, zpráva indikuje požadavky na platný název.
    - Zadejte volitelný **Popis** vzorce. 
    * **Uživatelské jméno** – zadejte uživatelské jméno, kterému je uděleno oprávnění správce.
    * **Heslo** – zadejte-nebo vyberte z rozevíracího seznamu – hodnota, která je přidružená ke tajnému klíči (heslo), které chcete použít pro zadaného uživatele. Další informace o ukládání tajných klíčů v trezoru klíčů a jejich použití při vytváření prostředků testovacího prostředí najdete [v tématu uložení tajných kódů v Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md).

        Vyberte možnost **použít uložený tajný klíč** , pokud chcete použít tajný klíč z Azure Key Vault místo používání hesla. 
    * **Velikost virtuálního počítače** – vyberte **změnit velikost** a změňte velikost virtuálního počítače. 
    - **Typ disku s operačním systémem** – vyberte typ disku, který chcete použít (HDD úrovně Standard, SSD úrovně Standard nebo SSD úrovně Premium).
    * **Artefakty** – vyberte stránku **Přidat nebo odebrat artefakty** , ve kterých vyberete a nakonfigurujete artefakty, které chcete přidat do základní image. Další informace o artefaktech najdete v tématu [Vytvoření vlastních artefaktů pro váš virtuální počítač s Azure DevTest Labs](devtest-lab-artifact-author.md).

        ![Základní stránka nastavení](./media/devtest-lab-create-formulas/basic-settings.png)
8. Přepněte na kartu **Upřesnit nastavení** a zadejte následující hodnoty:
    - **Virtuální síť** – Chcete-li změnit virtuální síť, vyberte možnost **změnit virtuální** síť. 
    - **Podsíť** – Chcete-li změnit podsíť, vyberte možnost **změnit podsíť**. 
    - **Konfigurace IP adresy** – určete, jestli chcete použít veřejné, privátní nebo sdílené IP adresy. Další informace o sdílených IP adresách najdete v tématu [Principy sdílených IP adres v Azure DevTest Labs](./devtest-lab-shared-ip.md).
    - **Datum a čas vypršení platnosti** – toto pole nemůžete upravit. 
    - **Nastavit tento počítač jako vynucený** – vytvoření počítače s nárokem na vynucené znamená, že se mu nebude přiřazovat vlastnictví v době vytvoření. Místo toho budou moci uživatelé testovacího prostředí převzít vlastnictví ("deklarace") počítače na stránce testovacího prostředí.  

        ![Snímek obrazovky s rozšířeným nastavením stránky vytvořit vzorec (opakovaně použitelný základ)](./media/devtest-lab-create-formulas/advanced-settings.png)
    - Pokud jste jako základ vybrali Image Galerie sdílených imagí, zobrazí se také pole **verze bitové kopie** , které umožňuje vybrat verzi obrázku z Galerie, kterou chcete použít jako základ. 

        ![Stránka Upřesnit nastavení](./media/devtest-lab-create-formulas/advanced-settings-shared-image-gallery.png)
8. Vyberte **Odeslat** a vytvořte vzorec.

9. Po vytvoření vzorce se zobrazí v seznamu na stránce **vzorce** .

### <a name="create-a-formula-from-a-vm"></a>Vytvoření vzorce z virtuálního počítače
Následující kroky vás provedou procesem vytvoření vzorce založeného na stávajícím virtuálním počítači. 

> [!NOTE]
> K vytvoření vzorce z virtuálního počítače se musí virtuální počítač vytvořit po 30. březnu 2016. 
> 
> 

1. Přihlaste se na [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Vyberte **všechny služby** a v seznamu vyberte **DevTest Labs** .
3. V seznamu cvičení vyberte požadované testovací prostředí.  
4. Na stránce **Přehled** testovacího prostředí vyberte virtuální počítač, ze kterého chcete vzorec vytvořit.
   
    ![Virtuální počítače Labs](./media/devtest-lab-create-formulas/my-vms.png)
5. Na stránce virtuálního počítače vyberte **vytvořit vzorec (opakovaně použitelný základ)**.
   
    ![Vytvořit vzorec](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. Na stránce **vytvořit vzorec** zadejte **název** a **Popis** nového vzorce.
   
    ![Vytvořit stránku vzorců](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. Vyberte **OK** a vytvořte vzorec.

## <a name="modify-a-formula"></a>Úprava vzorce
Chcete-li upravit vzorec, postupujte podle následujících kroků:

1. Přihlaste se na [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Vyberte **všechny služby** a v seznamu vyberte **DevTest Labs** .
3. V seznamu cvičení vyberte požadované testovací prostředí.  
4. Na stránce testovacího prostředí vyberte **vzorce (opakovaně použitelné báze)**.
   
    ![Snímek obrazovky zobrazující stránku testovacího prostředí se zvolenými vzorci (opakovaně použitelnými základy)](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Na stránce **vzorce testovacího prostředí** vyberte vzorec, který chcete upravit.
6. Na stránce **aktualizovat vzorec** proveďte požadované úpravy a vyberte **aktualizovat**.

## <a name="delete-a-formula"></a>Odstranění vzorce
Vzorec odstraníte pomocí následujících kroků:

1. Přihlaste se na [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Vyberte **všechny služby** a v seznamu vyberte **DevTest Labs** .
3. V seznamu cvičení vyberte požadované testovací prostředí.  
4. Na stránce **Nastavení** testovacího prostředí vyberte **vzorce**.
   
    ![Nabídka vzorce](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Na stránce **vzorce testovacího prostředí** vyberte tři tečky vpravo od vzorce, který chcete odstranit.
   
    ![Snímek obrazovky zobrazující stránku vzorce testovacího prostředí se třemi tečkami pro zvýrazněné možnosti vzorce](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. V kontextové nabídce vzorce vyberte **Odstranit**.
   
    ![Místní nabídka vzorce](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. V dialogovém okně pro potvrzení odstranění vyberte **Ano** .

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Související blogové příspěvky
* [Vlastní obrázky nebo vzorce?](devtest-lab-faq.md#what-is-the-difference-between-a-custom-image-and-a-formula)

## <a name="next-steps"></a>Další kroky
Po vytvoření vzorce pro použití při vytváření virtuálního počítače je dalším krokem [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md).

