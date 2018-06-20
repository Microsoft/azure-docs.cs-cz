---
title: 'Azure Active Directory Domain Services: Správa DNS v doménách spravovaných | Microsoft Docs'
description: Spravovat DNS v doménách spravovaných Azure Active Directory Domain Services
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
ms.date: 09/23/2017
ms.author: maheshu
ms.openlocfilehash: 20f6c95deb78c04668c484a20abcf41a58130847
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36211362"
---
# <a name="administer-dns-on-an-azure-ad-domain-services-managed-domain"></a>Spravovat DNS na spravované doméně služby Azure AD Domain Services
Azure Active Directory Domain Services zahrnuje server DNS (překlad názvu domény), který poskytuje překlad názvů DNS pro spravovanou doménu. V některých případech může musíte nakonfigurovat DNS na spravované domény. Musíte vytvořit záznamy DNS pro počítače, které nejsou připojené k doméně, nakonfigurujte virtuální IP adresy pro vyrovnávání zatížení nebo nastavit externí servery DNS pro předávání. Z toho důvodu jsou uživatelé, kteří patří do skupiny "Administrators AAD řadič domény, udělena oprávnění správy DNS na spravované domény.

## <a name="before-you-begin"></a>Než začnete
Chcete-li provést úkoly vypsané v tomto článku, je třeba:

1. Platná **předplatné**.
2. **Adresář Azure AD** – buď synchronizovány s místní adresář nebo výhradně cloudový adresář.
3. **Azure AD Domain Services** musí být povolen pro adresář Azure AD. Pokud jste tak dosud neučinili, postupujte podle všechny úkoly popsané v [příručce Začínáme](active-directory-ds-getting-started.md).
4. A **virtuální počítač připojený k doméně** ze kterého můžete spravovat spravované doméně služby Azure AD Domain Services. Pokud nemáte virtuálního počítače, postupujte podle všechny úkoly popsané v článku s názvem [připojení virtuálního počítače s Windows k spravované doméně](active-directory-ds-admin-guide-join-windows-vm.md).
5. Potřebujete přihlašovací údaje **uživatelský účet patří do skupiny "Správci AAD řadič domény,** ve vašem adresáři, ke správě vaší spravované domény DNS.

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-dns-for-the-managed-domain"></a>Úloha 1 – zřídit virtuální počítač připojený k doméně ke vzdálené správě DNS pro spravovanou doménu
Spravované domény služby Azure AD Domain Services můžete spravovat vzdáleně pomocí známých nástrojů pro správu služby Active Directory jako správy Center Active Directory (ADAC) nebo AD PowerShell. Podobně DNS pro spravovanou doménu lze spravovat vzdáleně pomocí nástrojů pro správu serveru DNS.

Správci v adresáři služby Azure AD nemají oprávnění pro připojení k řadiče domény na spravované domény přes vzdálenou plochu. Členové skupiny 'Administrators AAD řadič domény, mohou spravovat DNS pro spravované domény vzdáleně pomocí nástrojů DNS serveru z počítače systému Windows Server nebo klienta, který je připojen k spravované doméně. Jako součást volitelná funkce vzdálenou správu serveru (RSAT) na Windows serveru a klientské počítače připojené k spravované doméně může být nainstalované nástroje serveru DNS.

Prvním krokem je zřízení virtuálního počítače s Windows Server, který je připojen k spravované doméně. Pokyny naleznete v článku s názvem [připojení virtuálního počítače s Windows serverem k spravované doméně služby Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md).

## <a name="task-2---install-dns-server-tools-on-the-virtual-machine"></a>Úloha 2 – nástroje pro instalaci serveru DNS na virtuálním počítači
Proveďte následující kroky k instalaci nástroje pro správu DNS na virtuální počítač připojený k doméně. Další informace o [instalaci a použití nástrojů pro vzdálenou správu serveru](https://technet.microsoft.com/library/hh831501.aspx), najdete v článku webu Technet.

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
9. Na **funkce** stránky, klikněte na tlačítko rozšířit **nástrojů pro vzdálenou správu serveru** uzel a potom kliknutím rozbalte položku **nástroje pro správu rolí** uzlu. Vyberte **nástroje serveru DNS** funkci ze seznamu nástroje pro správu rolí.

    ![Stránka funkce](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)
10. Na **potvrzení** klikněte na tlačítko **nainstalovat** k instalaci funkce nástroje pro DNS Server na virtuálním počítači. Po úspěšném dokončení instalace funkce klikněte na tlačítko **Zavřít** ukončíte **přidat role a funkce** průvodce.

    ![Stránka potvrzení](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-confirmation.png)

## <a name="task-3---launch-the-dns-management-console-to-administer-dns"></a>Úloha 3: Spusťte konzolu pro správu služby pro správu DNS
Teď, když funkce nástroje serveru DNS nainstalovaná v doméně virtuálního počítače, nástroje DNS jsme můžete použít ke správě DNS na spravované domény.

> [!NOTE]
> Musíte být členem skupiny 'správci AAD řadič domény, ke správě DNS na spravované domény.
>
>

1. Na obrazovce Start klikněte na tlačítko **nástroje pro správu**. Měli byste vidět **DNS** konzola nainstalovaná na virtuálním počítači.

    ![Nástroje pro správu - konzolu DNS](./media/active-directory-domain-services-admin-guide/install-rsat-dns-tools-installed.png)
2. Klikněte na tlačítko **DNS** spusťte konzolu pro správu DNS.
3. V **připojit k serveru DNS** dialogové okno, klikněte na možnost s názvem **následující počítače**a zadejte název domény DNS spravované domény (například "contoso100.com").

    ![Konzolu DNS - připojit k doméně](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)
4. Připojí se k spravované doméně konzolu DNS.

    ![Konzolu DNS - spravovat domény](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)
5. Teď můžete konzolu DNS přidat záznamy DNS pro počítače v rámci virtuální sítě, ve kterém jste povolili služby AAD Domain Services.

> [!WARNING]
> Buďte opatrní při správě DNS pro spravované doméně pomocí nástroje pro správu DNS. Ujistěte se, že jste **odstranit nebo upravit integrované záznamy DNS, které jsou používané službami domény v doméně**. Předdefinované záznamy DNS zahrnují záznamy DNS domény, záznamy názvového serveru a další záznamy použít pro umístění řadiče domény. Pokud upravíte tyto záznamy, dojde k narušení služby domain services ve virtuální síti.
>
>

Najdete v článku [nástrojů DNS článku na webu Technet](https://technet.microsoft.com/library/cc753579.aspx) pro další informace o správě DNS.

## <a name="related-content"></a>Související obsah
* [Azure AD Domain Services – Příručka Začínáme](active-directory-ds-getting-started.md)
* [Připojení virtuálního počítače s Windows serverem k spravované doméně služby Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Správa spravované domény služby Azure AD Domain Services](active-directory-ds-admin-guide-administer-domain.md)
* [Nástroje pro správu DNS](https://technet.microsoft.com/library/cc753579.aspx)
