---
title: Šifrování disků s operačním systémem pomocí klíčů spravovaných zákazníkem v Azure DevTest Labs
description: Naučte se šifrovat disky operačního systému (OS) pomocí klíčů spravovaných zákazníkem v Azure DevTest Labs.
ms.topic: article
ms.date: 09/01/2020
ms.openlocfilehash: ddc29f8cf924bca6c757843ae8b7062757ff61bf
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92329538"
---
# <a name="encrypt-operating-system-os-disks-using-customer-managed-keys-in-azure-devtest-labs"></a>Šifrování disků operačního systému (OS) pomocí klíčů spravovaných zákazníkem v Azure DevTest Labs
Šifrování na straně serveru (SSE) chrání vaše data a pomáhá splnit závazky zabezpečení a dodržování předpisů vaší organizace. SSE automaticky šifruje vaše data uložená na spravovaných discích v Azure (s operačním systémem a datovými disky) ve výchozím nastavení, když je trvale ukládá do cloudu. Přečtěte si další informace o [šifrování disků](../virtual-machines/windows/disk-encryption.md) v Azure. 

V rámci DevTest Labs se všechny disky s operačním systémem a datové disky vytvořené jako součást testovacího prostředí šifrují pomocí klíčů spravovaných platformou. Jako vlastník testovacího prostředí se ale můžete rozhodnout pro šifrování disků s operačním systémem virtuálního počítače testovacího prostředí pomocí vlastních klíčů. Pokud se rozhodnete spravovat šifrování pomocí vlastních klíčů, můžete zadat **klíč spravovaný zákazníkem** , který se použije k šifrování dat v testovacích discích s operačním systémem. Další informace o šifrování na straně serveru (SSE) pomocí klíčů spravovaných zákazníkem a dalších typů šifrování spravovaného disku najdete v tématu [klíče spravované zákazníkem](../virtual-machines/windows/disk-encryption.md#customer-managed-keys). Viz také [omezení s použitím klíčů spravovaných zákazníkem](../virtual-machines/disks-enable-customer-managed-keys-portal.md#restrictions).

> [!NOTE]
> - Aktuálně se šifrování disku s klíčem spravovaným zákazníkem podporuje jenom pro disky s operačním systémem v DevTest Labs. 
> 
> - Toto nastavení se vztahuje na nově vytvořené disky s operačním systémem v testovacím prostředí. Pokud se rozhodnete, že v určitém bodě budete sadu šifrování disků chtít změnit, starší disky v testovacím prostředí zůstanou zašifrované pomocí předchozí sady šifrování disku. 

V následující části se dozvíte, jak může vlastník testovacího prostředí nastavit šifrování pomocí klíče spravovaného zákazníkem.

## <a name="pre-requisites"></a>Požadavky

1. Pokud nemáte sadu šifrování disku nastavenou, postupujte podle pokynů v tomto článku [a nastavte Key Vault a sadu šifrování disku](../virtual-machines/disks-enable-customer-managed-keys-portal.md). Všimněte si následujících požadavků pro sadu Disk Encryption: 

    - Sada Encryption disk musí být **ve stejné oblasti a předplatném jako vaše testovací prostředí**. 
    - Ujistěte se, že (vlastník testovacího prostředí) má alespoň **přístup na úrovni čtenáře** ke sadě Encryption disk, která bude použita k šifrování disků testovacího prostředí. 
1. Pro laboratoře vytvořené před 8/1/2020 se vlastník testovacího prostředí bude muset ujistit, že je povolená identita přiřazená testovacímu systému. Pokud to chcete udělat, může vlastník testovacího prostředí přejít ke své laboratoři, kliknout na **Konfigurace a zásady**, kliknout na okno **identita (Preview)** , změnit **stav** identity přiřazené systémem na **zapnuto** a kliknout na **Uložit**. Pro novou laboratoř vytvořenou po identitě přiřazenou systémem 8/1/2020 testovacího prostředí bude ve výchozím nastavení povolená. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/managed-keys.png" alt-text="Spravované klíče":::
1. Aby testovací prostředí mohlo zpracovávat šifrování pro všechny disky s operačním systémem, musí vlastník testovacího prostředí explicitně udělit roli pro správce identit v testovacím **systému** v sadě šifrování disku a také roli Přispěvatel virtuálních počítačů na základním předplatném Azure. Vlastník testovacího prostředí to může udělat provedením následujících kroků:

   
    1. Ujistěte se, že jste členem [role správce přístupu uživatele](../role-based-access-control/built-in-roles.md#user-access-administrator) na úrovni předplatného Azure, abyste mohli spravovat přístup uživatelů k prostředkům Azure. 
    1. Na stránce **sada šifrování disku** vyberte v nabídce vlevo možnost **řízení přístupu (IAM)** . 
    1. Na panelu nástrojů vyberte **+ Přidat** a vyberte **Přidat přiřazení role**.  

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/add-role-management-menu.png" alt-text="Spravované klíče":::
    1. Na stránce **Přidat přiřazení role** vyberte roli **Čtenář** nebo roli, která umožňuje další přístup. 
    1. Zadejte název testovacího prostředí, pro který se použije sada pro šifrování disků, a v rozevíracím seznamu vyberte název testovacího prostředí (identitu přiřazenou systémem pro testovací prostředí). 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/select-lab.png" alt-text="Spravované klíče":::        
    1. Na panelu nástrojů vyberte **Uložit**. 

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/save-role-assignment.png" alt-text="Spravované klíče":::
3. Přidejte **identitu přiřazenou systémem** testovacího prostředí do role **Přispěvatel virtuálních počítačů** pomocí stránky řízení přístupu **předplatného**  ->  **(IAM)** . Postup se podobá těm, které jsou v předchozích krocích. 

    
    1. Přejděte na stránku **předplatného** v Azure Portal. 
    1. Vyberte **Řízení přístupu (IAM)** . 
    1. Na panelu nástrojů vyberte **+ Přidat** a vyberte **Přidat přiřazení role**. 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/subscription-access-control-page.png" alt-text="Spravované klíče":::
    1. Na stránce **Přidat přiřazení role** vyberte u této role možnost **Přispěvatel virtuálního počítače** .
    1. Zadejte název testovacího prostředí a v rozevíracím seznamu vyberte **název testovacího prostředí** (identita přiřazený systémem pro testovací prostředí). 
    1. Na panelu nástrojů vyberte **Uložit**. 

## <a name="encrypt-lab-os-disks-with-a-customer-managed-key"></a>Šifrování disků s operačním systémem testovacího prostředí pomocí klíče spravovaného zákazníkem 

1. Na domovské stránce testovacího prostředí v Azure Portal v nabídce vlevo vyberte **Konfigurace a zásady** . 
1. Na stránce **Konfigurace a zásady** vyberte v části **šifrování** možnost **disky (Preview)** . Ve výchozím nastavení je **typ šifrování** nastavený na místní **šifrování pomocí klíče spravovaného platformou**.

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disks-page.png" alt-text="Spravované klíče":::
1. V části **typ šifrování**vyberte v rozevíracím seznamu možnost místní **šifrování pomocí spravovaného klíče zákazníka** . 
1. V části **nastavení šifrování disku**vyberte sadu šifrování disku, kterou jste vytvořili dříve. Je to stejná sada pro šifrování disků, ke které má přístup k systémové identitě přiřazena systémová identita testovacího prostředí.
1. Na panelu nástrojů vyberte **Uložit**. 

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disk-encryption-set.png" alt-text="Spravované klíče":::
1. V okně se zprávou s následujícím textem: *Toto nastavení se použije u nově vytvořených počítačů v testovacím prostředí. Starý disk s operačním systémem zůstane zašifrovaný se starou sadou Disk Encryption*, vyberte **OK**. 

    Po nakonfigurování se disky s operačním systémem testovacího prostředí zašifrují pomocí klíče spravovaného zákazníkem, který je k dispozici pomocí sady šifrování disku. 
   
## <a name="how-to-validate-if-disks-are-being-encrypted"></a>Jak ověřit, jestli jsou disky šifrované

1. Přejít na virtuální počítač testovacího prostředí vytvořený po povolení šifrování disku pomocí spravovaného klíče zákazníka v testovacím prostředí.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/enabled-encryption-vm.png" alt-text="Spravované klíče":::
1. Klikněte na skupinu prostředků virtuálního počítače a pak klikněte na disk s operačním systémem.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/vm-resource-group.png" alt-text="Spravované klíče":::
1. V části šifrování a ověřit, jestli je šifrování nastavené na spravovaný klíč zákazníka se sadou pro šifrování disku, kterou jste vybrali.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/validate-encryption.png" alt-text="Spravované klíče":::
  
## <a name="next-steps"></a>Další kroky

Viz následující články: 

- [Azure Disk Encryption](../virtual-machines/windows/disk-encryption.md). 
- [Klíče spravované zákazníkem](../virtual-machines/windows/disk-encryption.md#customer-managed-keys) 
