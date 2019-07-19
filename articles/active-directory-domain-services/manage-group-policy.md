---
title: 'Azure Active Directory Domain Services: Spravovat Zásady skupiny | Microsoft Docs'
description: Správa Zásady skupiny ve Azure Active Directory Domain Services spravovaných doménách
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: iainfou
ms.openlocfilehash: c7b32885fdb3cf4f3e584c916d6b234fff54bfc4
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234029"
---
# <a name="administer-group-policy-on-an-azure-ad-domain-services-managed-domain"></a>Správa Zásady skupiny ve Azure AD Domain Services spravované doméně
Azure Active Directory Domain Services obsahuje integrované objekty Zásady skupiny (GPO) pro kontejnery AADDC Users a AADDC Computers. Tyto integrované objekty zásad skupiny můžete přizpůsobit pro konfiguraci Zásady skupiny ve spravované doméně. Členové skupiny Správci AAD DC mohou navíc vytvořit vlastní organizační jednotky ve spravované doméně. Můžou také vytvářet vlastní objekty zásad skupiny a propojit je s těmito vlastními organizačními jednotkami. Uživatelům patřícím do skupiny Správci AAD DC se udělují Zásady skupiny oprávnění ke správě ve spravované doméně.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Před zahájením
K provedení úkolů uvedených v tomto článku budete potřebovat:

1. Platné **předplatné Azure**.
2. **Adresář služby Azure AD** – buď synchronizovaný s místním adresářem, nebo s adresářem jenom pro Cloud.
3. **Azure AD Domain Services** musí být povolený pro adresář služby Azure AD. Pokud jste to ještě neudělali, postupujte podle všech úkolů popsaných v [průvodci Začínáme](create-instance.md).
4. **Virtuální počítač připojený k doméně** , ze kterého spravujete Azure AD Domain Services spravovanou doménu. Pokud tento virtuální počítač nemáte, postupujte podle všech úkolů uvedených v článku s názvem [připojení virtuálního počítače s Windows ke spravované doméně](active-directory-ds-admin-guide-join-windows-vm.md).
5. Abyste mohli spravovat Zásady skupiny pro spravovanou doménu, potřebujete přihlašovací údaje **uživatelského účtu patřící do skupiny Správci AAD DC** v adresáři.

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-group-policy-for-the-managed-domain"></a>Úloha 1 – zřízení virtuálního počítače připojeného k doméně pro vzdálenou správu Zásady skupiny pro spravovanou doménu
Spravované domény Azure AD Domain Services je možné spravovat vzdáleně pomocí známých nástrojů pro správu služby Active Directory, jako je Centrum správy služby Active Directory (ADAC) nebo prostředí AD PowerShell. Podobně Zásady skupiny pro spravovanou doménu je možné spravovat vzdáleně pomocí nástrojů pro správu Zásady skupiny.

Správci v adresáři Azure AD nemají oprávnění pro připojení k řadičům domény ve spravované doméně prostřednictvím vzdálené plochy. Členové skupiny Správci AAD DC mohou spravovat Zásady skupiny pro spravované domény vzdáleně. Můžou používat Zásady skupiny nástroje na Windows serveru nebo klientském počítači připojeném ke spravované doméně. Zásady skupiny nástroje lze nainstalovat jako součást volitelné funkce Správa Zásady skupiny v systému Windows Server a klientských počítačích připojených ke spravované doméně.

Prvním úkolem je zřídit virtuální počítač s Windows serverem, který je připojený ke spravované doméně. Pokyny najdete v článku s názvem [připojení virtuálního počítače s Windows serverem k Azure AD Domain Services spravované doméně](active-directory-ds-admin-guide-join-windows-vm.md).

## <a name="task-2---install-group-policy-tools-on-the-virtual-machine"></a>Úloha 2 – instalace Zásady skupiny nástrojů na virtuálním počítači
Provedením následujících kroků nainstalujete nástroje pro správu Zásady skupiny na virtuální počítač připojený k doméně.

1. Přejděte na Azure Portal. Klikněte na **všechny prostředky** na levém panelu. Vyhledejte a klikněte na virtuální počítač, který jste vytvořili v úloze 1.
2. Klikněte na tlačítko **připojit** na kartě Přehled. Vytvoří a stáhne se soubor protokol RDP (Remote Desktop Protocol) (. RDP).

    ![Připojení k virtuálnímu počítači s Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Chcete-li se připojit k virtuálnímu počítači, otevřete stažený soubor protokolu RDP. Pokud se zobrazí výzva, klikněte na **Připojit**. Na příkazovém řádku pro přihlášení použijte přihlašovací údaje uživatele, který patří do skupiny AAD DC Administrators. V našem případě používámebob@domainservicespreview.onmicrosoft.comnapříklad "". Během procesu přihlášení se může zobrazit upozornění certifikátu. Klikněte na Ano nebo pokračovat a pokračujte v připojování.
4. Z obrazovky Start otevřete **Správce serveru**. V centrálním podokně okna Správce serveru klikněte na **Přidat role a funkce** .

    ![Spustit Správce serveru na virtuálním počítači](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. Na stránce **než začnete** v **Průvodci přidáním rolí a funkcí**klikněte na **Další**.

    ![Než začnete stránku](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. Na stránce **typ instalace** ponechte zaškrtnutou možnost instalace na základě **rolí nebo na základě funkcí** a klikněte na **Další**.

    ![Stránka typ instalace](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. Na stránce **Výběr serveru** vyberte aktuální virtuální počítač z fondu serverů a klikněte na **Další**.

    ![Stránka Výběr serveru](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. Na stránce **role serveru** klikněte na **Další**. Tuto stránku přeskočíme, protože na server neinstalujeme žádné role.
9. Na stránce **funkce** vyberte funkci **správy Zásady skupiny** .

    ![Stránka funkcí](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)
10. Na stránce **potvrzení** klikněte na **nainstalovat** a nainstalujte na virtuální počítač funkci správy Zásady skupiny. Po úspěšném dokončení instalace funkce kliknutím na **Zavřít** zavřete průvodce **přidáním rolí a funkcí** .

    ![Stránka potvrzení](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management-confirmation.png)

## <a name="task-3---launch-the-group-policy-management-console-to-administer-group-policy"></a>Úloha 3 – spuštění konzoly pro správu Zásady skupiny pro správu Zásady skupiny
Ke správě Zásady skupiny ve spravované doméně můžete použít konzolu pro správu Zásady skupiny na virtuálním počítači připojeném k doméně.

> [!NOTE]
> Aby bylo možné spravovat Zásady skupiny ve spravované doméně, musíte být členem skupiny AAD DC Administrators.
>
>

1. Na obrazovce Start klikněte na **Nástroje pro správu**. Na virtuálním počítači by se měla zobrazit Konzola **pro správu Zásady skupiny** .

    ![Spustit správu Zásady skupiny](./media/active-directory-domain-services-admin-guide/gp-management-installed.png)
2. Kliknutím na **správa zásady skupiny** spusťte konzolu pro správu Zásady skupiny.

    ![Konzola Zásady skupiny](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

## <a name="task-4---customize-built-in-group-policy-objects"></a>Úloha 4: přizpůsobení předdefinovaných Zásady skupiny objektů
Existují dva předdefinované Zásady skupiny objekty (GPO) – jednu pro všechny pro kontejnery AADDC Computers a AADDC Users ve vaší spravované doméně. Tyto objekty zásad skupiny můžete přizpůsobit pro konfiguraci zásad skupiny ve spravované doméně.

1. V konzole pro **správu Zásady skupiny** kliknutím rozbalte uzly doménová **struktura: contoso100.com** a **domény** , aby se zobrazily zásady skupiny pro spravovanou doménu.

    ![Předdefinované objekty zásad skupiny](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)
2. Tyto integrované objekty zásad skupiny můžete přizpůsobit pro konfiguraci zásad skupiny ve spravované doméně. Klikněte pravým tlačítkem na objekt zásad skupiny a klikněte na **Upravit...** a přizpůsobte vestavěný objekt zásad skupiny. Nástroj Zásady skupiny Configuration Editor umožňuje přizpůsobení objektu zásad skupiny.

    ![Upravit vestavěný objekt zásad skupiny](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)
3. Nyní můžete použít konzolu **Editor pro správu zásad skupiny** k úpravě předdefinovaného objektu zásad skupiny. Například následující snímek obrazovky ukazuje, jak přizpůsobit vestavěný objekt zásad skupiny ' AADDC počítače '.

    ![Přizpůsobení objektu zásad skupiny](./media/active-directory-domain-services-admin-guide/gp-editor.png)

## <a name="task-5---create-a-custom-group-policy-object-gpo"></a>Úloha 5 – Vytvoření vlastního objektu Zásady skupiny (GPO)
Můžete vytvářet nebo importovat vlastní objekty zásad skupiny. Vlastní objekty zásad skupiny můžete propojit taky s vlastní organizační jednotkou vytvořenou ve spravované doméně. Další informace o vytváření vlastních organizačních jednotek najdete v tématu [Vytvoření vlastní organizační jednotky ve spravované doméně](create-ou.md).

> [!NOTE]
> Aby bylo možné spravovat Zásady skupiny ve spravované doméně, musíte být členem skupiny AAD DC Administrators.
>
>

1. V konzole **pro správu Zásady skupiny** kliknutím vyberte svoji vlastní organizační jednotku (OU). Klikněte pravým tlačítkem na organizační jednotku a pak klikněte na **vytvořit objekt zásad skupiny v této doméně a připojit ho sem...** .

    ![Vytvoření vlastního objektu zásad skupiny](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)
2. Zadejte název nového objektu zásad skupiny a klikněte na tlačítko **OK**.

    ![Zadejte název objektu zásad skupiny.](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)
3. Vytvoří se nový objekt zásad skupiny, který se propojí s vlastní organizační jednotkou. Pravým tlačítkem myši klikněte na objekt zásad skupiny a v nabídce klikněte na **Upravit...** .

    ![Nově vytvořený objekt zásad skupiny](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)
4. Nově vytvořený objekt zásad skupiny můžete přizpůsobit pomocí **Editor pro správu zásad skupiny**.

    ![Přizpůsobení nového objektu zásad skupiny](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)


Další informace o používání [Konzola pro správu zásad skupiny](https://technet.microsoft.com/library/cc753298.aspx) najdete na webu TechNet.

## <a name="related-content"></a>Související obsah
* [Azure AD Domain Services – Průvodce Začínáme](create-instance.md)
* [Připojení virtuálního počítače s Windows serverem k spravované doméně Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Správa domény Azure AD Domain Services](manage-domain.md)
* [Konzola pro správu zásad skupiny](https://technet.microsoft.com/library/cc753298.aspx)
