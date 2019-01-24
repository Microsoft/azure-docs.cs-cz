---
title: 'Azure Active Directory Domain Services: Správa spravované domény | Dokumentace Microsoftu'
description: Správa spravované domény Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: d4fdbc75-3e6b-4e20-8494-5dcc3bf2220a
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: 81e7eb6a95dea5bf68af62e4eeae20f958ec4790
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54844802"
---
# <a name="administer-an-azure-active-directory-domain-services-managed-domain"></a>Správa spravované domény služby Azure Active Directory Domain Services
Tento článek ukazuje, jak Správa spravované domény služby Azure Active Directory (AD) Domain Services.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Před zahájením
K dokončení úkolů uvedených v tomto článku, budete potřebovat:

1. Platný **předplatného Azure**.
2. **Adresář Azure AD** – buď synchronizaci s místním adresářem nebo výhradně cloudový adresář.
3. **Azure AD Domain Services** musí být povolené pro adresář Azure AD. Pokud jste neudělali, postupujte podle všechny úkoly popsané v [příručce Začínáme](active-directory-ds-getting-started.md).
4. A **virtuální počítač připojený k doméně** ze kterého budete spravovat spravované doméně služby Azure AD Domain Services. Pokud nemáte virtuální počítač, proveďte všechny úkoly popsané v článku s názvem [připojení virtuálního počítače s Windows k spravované doméně](active-directory-ds-admin-guide-join-windows-vm.md).
5. Potřebujete přihlašovací údaje **uživatelský účet patřící do skupiny "Správci AAD DC"** ve vašem adresáři, ke správě vaší spravované domény.

<br>

## <a name="administrative-tasks-you-can-perform-on-a-managed-domain"></a>Úlohy správy, které můžete provést ve spravované doméně
Členové skupiny "Správci AAD DC" jsou udělena oprávnění ve spravované doméně, které umožňují provádět úkoly, jako je:

* Připojení počítače k spravované doméně.
* Konfigurace integrovaného objektu zásad skupiny pro kontejnery Počítače AADDC a Uživatelé AADDC ve spravované doméně.
* Správa DNS ve spravované doméně.
* Vytvořit a spravovat vlastní organizační jednotky (OU) ve spravované doméně.
* Získání přístupu pro správu k počítačům připojeným ke spravované doméně.

## <a name="administrative-privileges-you-do-not-have-on-a-managed-domain"></a>Oprávnění správce, který ve spravované doméně nemáte
Doména je spravováno společností Microsoft, včetně aktivity, například opravy, monitorování a provádění zálohování. Doména je uzamčen a nemáte oprávnění provést určité úlohy správy v doméně. Níže jsou příklady úloh, které nelze provést.

* Nemáte oprávnění správce domény nebo správce podnikové sítě pro spravovanou doménu.
* Nelze rozšířit schéma této spravované doméně.
* Nemůžete se připojit k řadičům domény pro spravovanou doménu pomocí vzdálené plochy.
* Řadiče domény nelze přidat do spravované domény.

## <a name="task-1---create-a-domain-joined-windows-server-virtual-machine-to-remotely-administer-the-managed-domain"></a>Úloha 1: vytvoření připojeného k doméně systému Windows Server virtuálního počítače pro vzdálenou správu spravované domény
Spravované domény služby Azure AD Domain Services můžete spravovat pomocí známých nástrojů pro správu služby Active Directory jako je správu Center Active Directory (ADAC) nebo Powershellu AD. Správci klientů nemají oprávnění k připojení k řadiči domény ve spravované doméně přes vzdálenou plochu. Členové skupiny "Správci AAD DC" můžete spravovat vzdáleně pomocí nástroje pro správu AD z počítače serveru/klientu Windows, který je připojený ke spravované doméně spravované domény. Nástroje pro správu služby AD může být nainstalována jako součást volitelné funkce vzdálenou správu serveru (RSAT) na Windows Server a klientské počítače připojené k spravované doméně.

Prvním krokem je nastavení virtuálního počítače s Windows serverem, který je připojený ke spravované doméně. Pokyny najdete v článku s názvem [připojení virtuálního počítače s Windows serverem do spravované domény služby Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md).

### <a name="remotely-administer-the-managed-domain-from-a-client-computer-for-example-windows-10"></a>Vzdálená správa spravované domény z klientského počítače (třeba Windows 10)
Pokyny v tomto článku využívají virtuální počítače s Windows serverem pro správu adresářové služby AAD spravované domény. Ale můžete také používat k tomu virtuálního počítače Windows klienta (například Windows 10).

Je možné [vzdálenou správu serveru (RSAT) nainstalujte](https://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx) virtuálnímu počítači Windows klienta podle pokynů uvedených na webu TechNet.

## <a name="task-2---install-active-directory-administration-tools-on-the-virtual-machine"></a>Úloha 2 – nástroje pro správu instalace služby Active Directory na virtuálním počítači
Proveďte následující kroky k instalaci nástroje pro správu služby Active Directory na virtuální počítač k doméně. Další informace najdete v článku Technet [informace o instalaci a používání nástrojů pro vzdálenou správu serveru](https://technet.microsoft.com/library/hh831501.aspx).

1. Přejděte na web Azure Portal. Klikněte na tlačítko **všechny prostředky** na panelu vlevo. Vyhledejte a klikněte na virtuální počítač, který jste vytvořili v úloze 1.
2. Klikněte na tlačítko **připojit** tlačítko na kartě Přehled. Vytvoří a stáhne se soubor Remote Desktop Protocol (RDP).

    ![Připojení k virtuálnímu počítači Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Chcete-li se připojit k virtuálnímu počítači, otevřete stažený soubor protokolu RDP. Pokud se zobrazí výzva, klikněte na **Připojit**. Použijte přihlašovací údaje uživatele, které patří do skupiny "Správci AAD DC". Například "bob@domainservicespreview.onmicrosoft.com". Během procesu přihlášení se může zobrazit upozornění certifikátu. Klikněte na tlačítko Ano nebo dál pokračujte v připojování.
4. Na obrazovce Start otevřete **správce serveru**. Klikněte na tlačítko **přidat role a funkce** ve středovém podokně okna Správce serveru.

    ![Spusťte správce serveru na virtuálním počítači](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. Na **před zahájením** stránku **Průvodce přidání rolí a funkcí**, klikněte na tlačítko **Další**.

    ![Před zahájením stránku](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. Na **typ instalace** ponechte **instalace na základě rolí nebo na základě funkcí** zaškrtnuto políčko a klikněte na tlačítko **Další**.

    ![Stránka typu instalace](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. Na **výběr serveru** stránce vyberte aktuální virtuální počítač z fondu serverů a klikněte na tlačítko **Další**.

    ![Stránka pro výběr serveru](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. Na **role serveru** klikněte na **Další**.
9. Na **funkce** stránky, rozbalte kliknutím **nástroje pro vzdálenou správu serveru** uzel a potom klikněte na rozšířit **nástroje pro správu rolí** uzlu. Vyberte **služby AD DS a nástroje služby AD LDS** funkce ze seznamu nástroje pro správu rolí.

    ![Funkce stránky](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-ad-tools.png)
10. Na **potvrzení** klikněte na **nainstalovat** instalace služby AD a funkce nástroje služby AD LDS na virtuálním počítači. Po úspěšném dokončení instalace funkce klikněte na tlačítko **Zavřít** ukončíte **přidat role a funkce** průvodce.

    ![Stránka potvrzení](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-confirmation.png)

## <a name="task-3---connect-to-and-explore-the-managed-domain"></a>Úloha 3 – připojit k a prozkoumejte spravované domény
Teď můžete použít nástroje pro správu systému Windows Server AD zkoumat a správa spravované domény.

> [!NOTE]
> Musíte být členem skupiny "Správci AAD DC" Správa spravované domény.
>
>

1. Na obrazovce Start klikněte na tlačítko **nástroje pro správu**. Měli byste vidět nástroje pro správu AD nainstalovaný na virtuálním počítači.

    ![Nástroje pro správu nainstalovaný na serveru](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. Klikněte na tlačítko **Centrum správy služby Active Directory**.

    ![Centrum správy služby Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. Prozkoumání domény, klikněte na název domény v levém podokně (například "contoso100.com"). Všimněte si, že dva kontejnery nazývá "Kontejnery počítače AADDC" a "Uživatelé AADDC".

    ![Centrum ADAC – zobrazení domény](./media/active-directory-domain-services-admin-guide/adac-domain-view.png)
4. Klikněte na kontejner volá **uživatelé AADDC** zobrazíte všechny uživatele a skupiny, které patří do spravované domény. Měli byste vidět uživatelských účtů a skupin ze služby Azure AD tenant zobrazit v tomto kontejneru. Všimněte si, že v tomto příkladu, uživatelský účet pro uživatele "bob" a skupinu s názvem "Správci AAD DC' jsou k dispozici v tomto kontejneru.

    ![ADAC - domain users](./media/active-directory-domain-services-admin-guide/adac-aaddc-users.png)
5. Klikněte na kontejner volá **kontejnery počítače AADDC** a zobrazit tak počítače připojené k této spravované doméně. Měli byste vidět položku pro aktuální virtuální počítač, který je připojený k doméně. Účty počítače pro všechny počítače, které jsou připojeny k spravované doméně služby Azure AD Domain Services jsou uložené v tomto kontejneru "Kontejnery počítače AADDC".

    ![Centrum ADAC - počítače připojené k doméně](./media/active-directory-domain-services-admin-guide/adac-aaddc-computers.png)

<br>

## <a name="related-content"></a>Související obsah
* [Azure AD Domain Services – Příručka Začínáme](active-directory-ds-getting-started.md)
* [Připojte se k virtuálnímu počítači s Windows serverem do spravované domény služby Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Nasazení nástroje pro správu vzdáleného serveru](https://technet.microsoft.com/library/hh831501.aspx)
