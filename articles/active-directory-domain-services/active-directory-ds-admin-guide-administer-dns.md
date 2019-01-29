---
title: 'Azure Active Directory Domain Services: Správa DNS ve spravované domény | Dokumentace Microsoftu'
description: Správa DNS ve spravované domény Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: d6705f9f7e324c915c38d01c54bdf16826c62380
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55180190"
---
# <a name="administer-dns-on-an-azure-ad-domain-services-managed-domain"></a>Správa DNS ve spravované doméně služby Azure AD Domain Services
Azure Active Directory Domain Services obsahuje server DNS (překlad názvů domén), který zajišťuje překlad názvů DNS pro spravovanou doménu. V některých případech budete muset nakonfigurovat DNS ve spravované doméně. Budete muset vytvořit záznamy DNS pro počítače, které nejsou připojené k doméně, nakonfigurujte virtuální IP adresy nástroje pro vyrovnávání zatížení nebo nastavit externí servery DNS pro předávání. Z tohoto důvodu jsou uživatelé, kteří patří do skupiny "Správci AAD DC" udělit oprávnění pro správu DNS ve spravované doméně.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Před zahájením
K dokončení úkolů uvedených v tomto článku, budete potřebovat:

1. Platný **předplatného Azure**.
2. **Adresář Azure AD** – buď synchronizaci s místním adresářem nebo výhradně cloudový adresář.
3. **Azure AD Domain Services** musí být povolené pro adresář Azure AD. Pokud jste neudělali, postupujte podle všechny úkoly popsané v [příručce Začínáme](active-directory-ds-getting-started.md).
4. A **virtuální počítač připojený k doméně** ze kterého budete spravovat spravované doméně služby Azure AD Domain Services. Pokud nemáte virtuální počítač, proveďte všechny úkoly popsané v článku s názvem [připojení virtuálního počítače s Windows k spravované doméně](active-directory-ds-admin-guide-join-windows-vm.md).
5. Potřebujete přihlašovací údaje **uživatelský účet patřící do skupiny "Správci AAD DC"** ve vašem adresáři, ke správě DNS vaší spravované domény.

<br>

## <a name="task-1---create-a-domain-joined-virtual-machine-to-remotely-administer-dns-for-the-managed-domain"></a>Úloha 1 – Vytvoření virtuálního počítače připojené k doméně pro vzdálenou správu DNS pro spravovanou doménu
Spravované domény služby Azure AD Domain Services je možné spravovat vzdáleně pomocí známých nástrojů pro správu služby Active Directory jako je správu Center Active Directory (ADAC) nebo Powershellu AD. Obdobně DNS pro spravovanou doménu je možné spravovat vzdáleně pomocí nástrojů pro správu serveru DNS.

Správce v adresáři služby Azure AD nemá oprávnění k připojení k řadiči domény ve spravované doméně přes vzdálenou plochu. Členové skupiny "Správci AAD DC" můžete spravovat DNS pro spravované domény vzdáleně pomocí nástrojů DNS serveru z počítače serveru/klientu Windows, který je připojený ke spravované doméně. Nástroje serveru DNS jsou součástí volitelná funkce vzdálenou správu serveru (RSAT).

První úloha je vytvoření virtuálního počítače s Windows serverem, který je připojený ke spravované doméně. Pokyny najdete v článku s názvem [připojení virtuálního počítače s Windows serverem do spravované domény služby Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md).

## <a name="task-2---install-dns-server-tools-on-the-virtual-machine"></a>Úloha 2 – nástroje pro instalaci serveru DNS na virtuálním počítači
Proveďte následující kroky k instalaci nástroje pro správu DNS na virtuálním počítači připojené k doméně. Další informace o [instalaci a použití nástrojů pro vzdálenou správu serveru](https://technet.microsoft.com/library/hh831501.aspx), najdete v článku webu Technet.

1. Přejděte na web Azure Portal. Klikněte na tlačítko **všechny prostředky** na panelu vlevo. Vyhledejte a klikněte na virtuální počítač, který jste vytvořili v úloze 1.
2. Klikněte na tlačítko **připojit** tlačítko na kartě Přehled. Vytvoří a stáhne se soubor Remote Desktop Protocol (RDP).

    ![Připojení k virtuálnímu počítači Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Chcete-li se připojit k virtuálnímu počítači, otevřete stažený soubor protokolu RDP. Pokud se zobrazí výzva, klikněte na **Připojit**. Použijte přihlašovací údaje uživatele, které patří do skupiny "Správci AAD DC". Například "bob@domainservicespreview.onmicrosoft.com". Během procesu přihlášení se může zobrazit upozornění certifikátu. Klikněte na tlačítko Ano nebo pokračovat v připojování.

4. Na obrazovce Start otevřete **správce serveru**. Klikněte na tlačítko **přidat role a funkce** ve středovém podokně okna Správce serveru.

    ![Spusťte správce serveru na virtuálním počítači](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. Na **před zahájením** stránku **Průvodce přidání rolí a funkcí**, klikněte na tlačítko **Další**.

    ![Před zahájením stránku](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. Na **typ instalace** ponechte **instalace na základě rolí nebo na základě funkcí** zaškrtnuto políčko a klikněte na tlačítko **Další**.

    ![Stránka typu instalace](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. Na **výběr serveru** stránce vyberte aktuální virtuální počítač z fondu serverů a klikněte na tlačítko **Další**.

    ![Stránka pro výběr serveru](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. Na **role serveru** klikněte na **Další**.
9. Na **funkce** stránky, rozbalte kliknutím **nástroje pro vzdálenou správu serveru** uzel a potom klikněte na rozšířit **nástroje pro správu rolí** uzlu. Vyberte **nástroje serveru DNS** funkce ze seznamu nástroje pro správu rolí.

    ![Funkce stránky](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)
10. Na **potvrzení** klikněte na **nainstalovat** k instalaci funkce nástroje serveru DNS na virtuálním počítači. Po úspěšném dokončení instalace funkce klikněte na tlačítko **Zavřít** ukončíte **přidat role a funkce** průvodce.

    ![Stránka potvrzení](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-confirmation.png)

## <a name="task-3---launch-the-dns-management-console-to-administer-dns"></a>Úloha 3: Spusťte konzolu pro správu DNS pro správu DNS
Teď můžete použít nástroje systému Windows Server DNS, které ke správě DNS ve spravované doméně.

> [!NOTE]
> Musíte být členem skupiny "Správci AAD DC" Správa DNS ve spravované doméně.
>
>

1. Na obrazovce Start klikněte na tlačítko **nástroje pro správu**. Měli byste vidět **DNS** konzola nainstalovaná na virtuálním počítači.

    ![Nástroje pro správu – konzoly DNS](./media/active-directory-domain-services-admin-guide/install-rsat-dns-tools-installed.png)
2. Klikněte na tlačítko **DNS** spustit konzolu pro správu DNS.
3. V **připojit k serveru DNS** dialogového okna, klikněte na tlačítko **následujícího počítače**a zadejte název domény DNS pro spravovanou doménu (například "contoso100.com").

    ![Konzolu DNS – připojení k doméně](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)
4. DNS Konzola se připojuje k spravované doméně.

    ![Konzolu DNS – Správa domény](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)
5. Teď můžete konzolu DNS přidat záznamy DNS pro počítače v rámci virtuální sítě, ve kterém jste povolili služby AAD Domain Services.

> [!WARNING]
> Buďte opatrní při správě DNS pro spravovanou doménu pomocí nástroje pro správu DNS. Ujistěte se, které jste **Nemazat ani neupravovat integrované záznamy DNS, které jsou používané službami domény v doméně**. Integrované záznamy DNS zahrnout další záznamy, použít pro umístění řadiče domény, záznamy názvových serverů a záznamů DNS domény. Pokud změníte tyto záznamy, dojde k narušení služby domain services ve virtuální síti.
>
>

Další informace o správě DNS, najdete v článku [nástroje DNS článek na webu Technet](https://technet.microsoft.com/library/cc753579.aspx).

## <a name="related-content"></a>Související obsah
* [Azure AD Domain Services – Příručka Začínáme](active-directory-ds-getting-started.md)
* [Připojte se k virtuálnímu počítači s Windows serverem do spravované domény služby Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Správa spravované domény služby Azure AD Domain Services](active-directory-ds-admin-guide-administer-domain.md)
* [Nástroje pro správu DNS](https://technet.microsoft.com/library/cc753579.aspx)
