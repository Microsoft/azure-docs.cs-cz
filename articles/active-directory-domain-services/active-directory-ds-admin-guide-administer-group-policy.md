---
title: 'Azure Active Directory Domain Services: Správa zásad skupiny na spravovaných domén | Microsoft Docs'
description: Zásady skupiny Správa v Azure Active Directory Domain Services spravované domény
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: maheshu
ms.openlocfilehash: 3f49e4ac0073c81a6e55e6653acc7c6531989379
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36212236"
---
# <a name="administer-group-policy-on-an-azure-ad-domain-services-managed-domain"></a>Správa zásad skupiny na spravované doméně služby Azure AD Domain Services
Azure Active Directory Domain Services zahrnuje předdefinované objekty zásad skupiny (GPO) pro kontejnery 'AADDC uživatele' a "AADDC počítače". Můžete přizpůsobit těchto předdefinovaných objektů zásad skupiny ke konfigurování zásad skupiny na spravované domény. Kromě toho členy skupiny "Správci AAD řadič domény, můžete vytvořit své vlastní vlastní organizačních jednotek ve spravované doméně. Můžete také vytvořit vlastní objekty zásad skupiny a propojit je s těmito vlastní organizační jednotky. Uživatelé, kteří patří do skupiny "Administrators AAD řadič domény, jsou udělena oprávnění správy zásad skupiny na spravované domény.

## <a name="before-you-begin"></a>Než začnete
Chcete-li provést úkoly vypsané v tomto článku, je třeba:

1. Platná **předplatné**.
2. **Adresář Azure AD** – buď synchronizovány s místní adresář nebo výhradně cloudový adresář.
3. **Azure AD Domain Services** musí být povolen pro adresář Azure AD. Pokud jste tak dosud neučinili, postupujte podle všechny úkoly popsané v [příručce Začínáme](active-directory-ds-getting-started.md).
4. A **virtuální počítač připojený k doméně** ze kterého můžete spravovat spravované doméně služby Azure AD Domain Services. Pokud nemáte virtuálního počítače, postupujte podle všechny úkoly popsané v článku s názvem [připojení virtuálního počítače s Windows k spravované doméně](active-directory-ds-admin-guide-join-windows-vm.md).
5. Potřebujete přihlašovací údaje **uživatelský účet patří do skupiny "Správci AAD řadič domény,** ve vašem adresáři, ke správě zásad skupiny pro vaší spravované domény.

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-group-policy-for-the-managed-domain"></a>Úloha 1 – zřídit virtuální počítač připojený k doméně pro vzdálenou správu zásad skupiny pro spravované doméně
Spravované domény služby Azure AD Domain Services můžete spravovat vzdáleně pomocí známých nástrojů pro správu služby Active Directory jako správy Center Active Directory (ADAC) nebo AD PowerShell. Podobně zásad skupiny pro spravovanou doménu lze spravovat vzdáleně pomocí nástrojů pro správu zásad skupiny.

Správci v adresáři služby Azure AD nemají oprávnění pro připojení k řadiče domény na spravované domény přes vzdálenou plochu. Členové skupiny 'Administrators AAD řadič domény, mohou spravovat zásady skupiny pro spravované domény vzdáleně. Používání nástrojů zásad skupiny na Windows Server nebo klienta počítač připojen k spravované doméně. Nástroje zásad skupiny můžete nainstalovat jako součást volitelné funkce Správa zásad skupiny v systému Windows Server a klientských počítačů, které jsou připojené k spravované doméně.

Prvním krokem je zřízení virtuálního počítače s Windows Server, který je připojen k spravované doméně. Pokyny naleznete v článku s názvem [připojení virtuálního počítače s Windows serverem k spravované doméně služby Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md).

## <a name="task-2---install-group-policy-tools-on-the-virtual-machine"></a>Úloha 2 – zásad skupiny instalace nástroje na virtuálním počítači
Proveďte následující kroky k instalaci nástroje pro správu zásad skupiny na virtuální počítač připojený k doméně.

1. Přejděte na portál Azure. Klikněte na tlačítko **všechny prostředky** na levém panelu. Vyhledejte a klikněte na virtuální počítač, který jste vytvořili v úloze 1.
2. Klikněte **Connect** tlačítko na kartě Přehled. Soubor Remote Desktop Protocol (.rdp) je vytvořen a stáhli.

    ![Připojení k systému Windows virtuálního počítače](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Chcete-li se připojit k virtuálnímu počítači, otevřete stažený soubor protokolu RDP. Pokud se zobrazí výzva, klikněte na **Připojit**. Do příkazového řádku přihlášení pomocí přihlašovacích údajů uživatele, které patří do skupiny "Správci AAD řadič domény. Například používáme 'bob@domainservicespreview.onmicrosoft.com' v našem případě. Během procesu přihlášení se může zobrazit upozornění certifikátu. Klikněte na tlačítko Ano nebo můžete dál pokračovat v připojení.
4. Na úvodní obrazovce otevřete **správce serveru**. Klikněte na tlačítko **přidat role a funkce** ve středovém podokně okna Správce serveru.

    ![Spusťte správce serveru na virtuálním počítači](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. Na **než začnete** stránky **Průvodce přidáním rolí a funkcí**, klikněte na tlačítko **Další**.

    ![Před zahájením stránku](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. Na **typ instalace** ponechte **instalace na základě rolí nebo na základě funkcí** zaškrtnuto políčko a klikněte na tlačítko **Další**.

    ![Stránka Typ instalace](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. Na **výběr serveru** vyberte aktuální virtuální počítač z fondu serverů a klikněte na tlačítko **Další**.

    ![Stránka Výběr serveru](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. Na **role serveru** klikněte na tlačítko **Další**. Tuto stránku jsme vynechat, protože jsme se neinstalují žádné role na serveru.
9. Na **funkce** vyberte **Správa zásad skupiny** funkce.

    ![Stránka funkce](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)
10. Na **potvrzení** klikněte na tlačítko **nainstalovat** nainstalovat funkci Správa zásad skupiny na virtuálním počítači. Po úspěšném dokončení instalace funkce klikněte na tlačítko **Zavřít** ukončíte **přidat role a funkce** průvodce.

    ![Stránka potvrzení](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management-confirmation.png)

## <a name="task-3---launch-the-group-policy-management-console-to-administer-group-policy"></a>Úloha 3 – spuštění konzoly pro správu zásad skupiny pro správu zásad skupiny
Konzola pro správu zásad skupiny na virtuálním počítači připojeném k doméně můžete použít ke správě zásad skupiny na spravované domény.

> [!NOTE]
> Musíte být členem skupiny 'správci AAD řadič domény, ke správě zásad skupiny na spravované domény.
>
>

1. Na obrazovce Start klikněte na tlačítko **nástroje pro správu**. Měli byste vidět **Správa zásad skupiny** konzola nainstalovaná na virtuálním počítači.

    ![Správa zásad skupiny spuštění](./media/active-directory-domain-services-admin-guide/gp-management-installed.png)
2. Klikněte na tlačítko **Správa zásad skupiny** ke spuštění konzoly pro správu zásad skupiny.

    ![Konzola zásad skupiny](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

## <a name="task-4---customize-built-in-group-policy-objects"></a>Úloha 4: Přizpůsobení předdefinované objekty zásad skupiny
Existují dvě předdefinované objekty zásad skupiny (GPO) – jeden pro kontejnery 'AADDC počítačů' a 'AADDC uživatele' ve vaší spravované domény. Můžete přizpůsobit tyto objekty zásad skupiny ke konfigurování zásad skupiny na spravované domény.

1. V **Správa zásad skupiny** konzoly, klikněte na tlačítko rozšířit **doménové struktury: contoso100.com** a **domén** uzly zobrazíte zásady skupiny pro vaší spravované domény.

    ![Předdefinované objekty zásad skupiny](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)
2. Můžete přizpůsobit těchto předdefinovaných objektů zásad skupiny nakonfigurovat zásady skupiny na vaší spravované domény. Klikněte pravým tlačítkem na objekt zásad skupiny a klikněte na tlačítko **upravit...**  k přizpůsobení předdefinovaných objektů zásad skupiny. Nástroj Configuration Editor zásad skupiny umožňuje přizpůsobit objekt zásad skupiny.

    ![Upravit integrované objekt zásad skupiny](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)
3. Teď můžete použít **Editor správy zásad skupiny** konzoly upravit integrované objekt zásad skupiny. Například následující snímek obrazovky ukazuje, jak přizpůsobit předdefinované 'AADDC počítače, objekt zásad skupiny.

    ![Přizpůsobení objektu zásad skupiny](./media/active-directory-domain-services-admin-guide/gp-editor.png)

## <a name="task-5---create-a-custom-group-policy-object-gpo"></a>Úloha 5 – vytvoření vlastní objekt zásad skupiny (GPO)
Můžete vytvořit nebo importovat vlastní objekty zásad skupiny vlastní. Vlastní objekty zásad skupiny můžete také propojit vlastní organizační jednotky, které jste vytvořili v vaší spravované domény. Další informace o vytvoření vlastní organizační jednotky, najdete v části [spravované domény vytvořit vlastní](active-directory-ds-admin-guide-create-ou.md).

> [!NOTE]
> Musíte být členem skupiny 'správci AAD řadič domény, ke správě zásad skupiny na spravované domény.
>
>

1. V **Správa zásad skupiny** konzoly, klikněte na tlačítko Vybrat vlastní organizační jednotky (OU). Klikněte pravým tlačítkem na organizační jednotku a klikněte na tlačítko **vytvořit objekt zásad skupiny v této doméně a propojit jej sem...** .

    ![Vytvořit vlastní objekt zásad skupiny](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)
2. Zadejte název pro nový objekt zásad skupiny a klikněte na tlačítko **OK**.

    ![Zadejte název pro objekt zásad skupiny](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)
3. Nový objekt zásad skupiny je vytvořené a připojené k vaší vlastní organizační jednotce. Klikněte pravým tlačítkem na objekt zásad skupiny a klikněte na tlačítko **upravit...**  z nabídky.

    ![Nově vytvořeného objektu zásad skupiny](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)
4. Můžete přizpůsobit nově vytvořený objekt zásad skupiny pomocí **Editor správy zásad skupiny**.

    ![Přizpůsobení nový objekt zásad skupiny](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)


Další informace o používání [konzoly pro správu zásad skupiny](https://technet.microsoft.com/library/cc753298.aspx) je k dispozici na webu Technet.

## <a name="related-content"></a>Související obsah
* [Azure AD Domain Services – Příručka Začínáme](active-directory-ds-getting-started.md)
* [Připojení virtuálního počítače s Windows serverem k spravované doméně služby Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Správa spravované domény služby Azure AD Domain Services](active-directory-ds-admin-guide-administer-domain.md)
* [Konzola pro správu zásad skupiny](https://technet.microsoft.com/library/cc753298.aspx)
