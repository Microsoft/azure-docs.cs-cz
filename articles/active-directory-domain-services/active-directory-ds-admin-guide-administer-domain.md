---
title: 'Azure Active Directory Domain Services: Správa spravované domény | Microsoft Docs'
description: Správa spravované domény Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: d4fdbc75-3e6b-4e20-8494-5dcc3bf2220a
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: maheshu
ms.openlocfilehash: 6a4e25dd3f819ad4f0fee7846e87df2202f5227f
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36210614"
---
# <a name="administer-an-azure-active-directory-domain-services-managed-domain"></a>Správa spravované domény služby Azure Active Directory Domain Services
Tento článek ukazuje, jak spravovat spravované doméně služby Azure Active Directory (AD) Domain Services.

## <a name="before-you-begin"></a>Než začnete
Chcete-li provést úkoly vypsané v tomto článku, je třeba:

1. Platná **předplatné**.
2. **Adresář Azure AD** – buď synchronizovány s místní adresář nebo výhradně cloudový adresář.
3. **Azure AD Domain Services** musí být povolen pro adresář Azure AD. Pokud jste tak dosud neučinili, postupujte podle všechny úkoly popsané v [příručce Začínáme](active-directory-ds-getting-started.md).
4. A **virtuální počítač připojený k doméně** ze kterého můžete spravovat spravované doméně služby Azure AD Domain Services. Pokud nemáte virtuálního počítače, postupujte podle všechny úkoly popsané v článku s názvem [připojení virtuálního počítače s Windows k spravované doméně](active-directory-ds-admin-guide-join-windows-vm.md).
5. Potřebujete přihlašovací údaje **uživatelský účet patří do skupiny "Správci AAD řadič domény,** ve vašem adresáři, ke správě vaší spravované domény.

<br>

## <a name="administrative-tasks-you-can-perform-on-a-managed-domain"></a>Úlohy správy, které můžete provádět na spravované doméně
Členové skupiny "Správci AAD řadič domény, jsou udělena oprávnění na spravované domény, které umožňují provádět úlohy, jako je:

* Připojení počítače k spravované doméně.
* Konfigurace integrovaného objektu zásad skupiny pro kontejnery Počítače AADDC a Uživatelé AADDC ve spravované doméně.
* Správa DNS ve spravované doméně.
* Vytvořit a spravovat vlastní organizačních jednotek (OU) na spravované domény.
* Získání přístupu pro správu k počítačům připojeným ke spravované doméně.

## <a name="administrative-privileges-you-do-not-have-on-a-managed-domain"></a>Oprávnění správce nemáte na spravované doméně
Domény spravuje Microsoft, včetně aktivit, jako je například opravy, monitorování a provádění zálohování. Proto je uzamčené domény a nemáte oprávnění k provádění určitých úloh správy v doméně. Níže jsou příklady úloh, které nelze provést.

* Nejsou udělena oprávnění správce domény nebo správce podnikové sítě pro spravovanou doménu.
* Nelze rozšířit schéma spravované domény.
* Nemůžete se připojit k řadičům domény k spravované doméně pomocí vzdálené plochy.
* Řadiče domény nelze přidat k spravované doméně.

## <a name="task-1---provision-a-domain-joined-windows-server-virtual-machine-to-remotely-administer-the-managed-domain"></a>Úloha 1 - zřídit virtuální počítač připojený k doméně systému Windows Server pro vzdálenou správu spravované doméně
Spravované domény služby Azure AD Domain Services můžete spravovat pomocí známých nástrojů pro správu služby Active Directory jako správy Center Active Directory (ADAC) nebo AD PowerShell. Správci klientů nemají oprávnění pro připojení k řadiče domény na spravované domény přes vzdálenou plochu. Členové skupiny 'Administrators AAD řadič domény, proto mohou spravovat spravovaných domén vzdáleně pomocí nástroje pro správu AD z počítače systému Windows Server nebo klienta, který je připojen k spravované doméně. Nástroje pro správu služby AD může být nainstalován jako součást volitelné funkce vzdálenou správu serveru (RSAT) na Windows Server a klientských počítačů, které jsou připojené k spravované doméně.

Prvním krokem je nastavení virtuálního počítače Windows serveru, který je připojen k spravované doméně. Pokyny naleznete v článku s názvem [připojení virtuálního počítače s Windows serverem k spravované doméně služby Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md).

### <a name="remotely-administer-the-managed-domain-from-a-client-computer-for-example-windows-10"></a>Vzdálená správa spravované domény z klientského počítače (například Windows 10)
Pokyny v tomto článku virtuálního počítače s Windows serverem pro správu služby DS AAD spravované domény. Však také můžete k tomu použít klienta (například Windows 10) virtuálního počítače s Windows.

Můžete [nainstalovat vzdálenou správu serveru (RSAT)](http://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx) na virtuálním počítači klienta Windows podle pokynů na webu TechNet.

## <a name="task-2---install-active-directory-administration-tools-on-the-virtual-machine"></a>Úloha 2 – nástroje pro správu instalace služby Active Directory na virtuálním počítači
Proveďte následující kroky k instalaci nástrojů pro správu služby Active Directory na virtuální počítač připojený k doméně. Další informace najdete v článku Technet [informace o instalaci a použití nástrojů pro vzdálenou správu serveru](https://technet.microsoft.com/library/hh831501.aspx).

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
9. Na **funkce** stránky, klikněte na tlačítko rozšířit **nástrojů pro vzdálenou správu serveru** uzel a potom kliknutím rozbalte položku **nástroje pro správu rolí** uzlu. Vyberte **služby AD DS a AD LDS nástroje** funkci ze seznamu nástroje pro správu rolí.

    ![Stránka funkce](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-ad-tools.png)
10. Na **potvrzení** klikněte na tlačítko **nainstalovat** instalace služby AD a funkce nástroje služby AD LDS na virtuálním počítači. Po úspěšném dokončení instalace funkce klikněte na tlačítko **Zavřít** ukončíte **přidat role a funkce** průvodce.

    ![Stránka potvrzení](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-confirmation.png)

## <a name="task-3---connect-to-and-explore-the-managed-domain"></a>Úloha 3 – připojit a zkoumat spravované doméně
Teď, když jsou nainstalovány nástroje pro správu AD v doméně virtuálního počítače, jsme můžete pomocí těchto nástrojů můžete prozkoumat a správa spravované domény.

> [!NOTE]
> Musíte být členem skupiny 'správci AAD řadič domény, ke správě spravované domény.
>
>

1. Na obrazovce Start klikněte na tlačítko **nástroje pro správu**. Měli byste vidět nástroje pro správu AD nainstalované na virtuálním počítači.

    ![Nástroje pro správu nainstalován na serveru](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. Klikněte na tlačítko **Centrum správy služby Active Directory**.

    ![Centrum správy služby Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. Pokud chcete prozkoumat domény, klikněte na název domény v levém podokně (například "contoso100.com"). Všimněte si dvě kontejnery označované jako 'AADDC počítače' a 'AADDC uživatele'.

    ![Centrum ADAC - zobrazení domény](./media/active-directory-domain-services-admin-guide/adac-domain-view.png)
4. Klikněte na kontejneru názvem **AADDC uživatelé** zobrazíte všechny uživatele a skupiny, které patří k spravované doméně. Měli byste vidět uživatelských účtů a skupin ze služby Azure AD klienta zobrazit v tomto kontejneru. Všimněte si v tomto příkladu, uživatelský účet pro uživatele názvem "bob" a skupinu s názvem "Správci AAD řadič domény, jsou k dispozici v tomto kontejneru.

    ![Centrum ADAC - uživatelé domény](./media/active-directory-domain-services-admin-guide/adac-aaddc-users.png)
5. Klikněte na kontejneru názvem **AADDC počítače** a zobrazit počítače připojené k této spravované domény. Měli byste vidět položku pro aktuální virtuální počítač, který je připojený k doméně. Účty počítačů pro všechny počítače, které jsou připojeny k spravované doméně služby Azure AD Domain Services jsou uloženy v tomto kontejneru "AADDC počítače".

    ![Centrum ADAC - počítačů připojených k doméně](./media/active-directory-domain-services-admin-guide/adac-aaddc-computers.png)

<br>

## <a name="related-content"></a>Související obsah
* [Azure AD Domain Services – Příručka Začínáme](active-directory-ds-getting-started.md)
* [Připojení virtuálního počítače s Windows serverem k spravované doméně služby Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Nasazení nástroje pro správu vzdáleného serveru](https://technet.microsoft.com/library/hh831501.aspx)
