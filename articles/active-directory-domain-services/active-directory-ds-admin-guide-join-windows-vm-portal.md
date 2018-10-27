---
title: 'Azure Active Directory Domain Services: Připojení virtuálního počítače s Windows serverem do spravované domény | Dokumentace Microsoftu'
description: Připojte se k virtuálnímu počítači s Windows serverem do Azure AD DS
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 29316313-c76c-4fb9-8954-5fa5ec82609e
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/21/2018
ms.author: ergreenl
ms.openlocfilehash: 753818526ad637db0baa13c92e92d2725b19dbf8
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50158314"
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Připojení virtuálního počítače Windows Server ke spravované doméně
Tento článek popisuje postup nasazení virtuálního počítače s Windows serverem pomocí webu Azure portal. Následně ukazuje, jak připojit virtuální počítač k spravované doméně služby Azure Active Directory Domain Services (Azure AD DS).

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="step-1-create-a-windows-server-virtual-machine"></a>Krok 1: Vytvoření virtuálního počítače s Windows serverem
K vytvoření virtuálního počítače s Windows, který je připojený k virtuální síti, ve kterém jste povolili službu Azure AD DS, proveďte následující kroky:

1. Přihlaste se k webu [Azure Portal](http://portal.azure.com).
2. V horní části v levém podokně vyberte **nový**.
3. Vyberte **Compute** a potom vyberte **Windows Server 2016 Datacenter**.

    ![Odkaz na Windows Server 2016 Datacenter](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)

4. V **Základy** podokně Průvodce konfigurace základního nastavení pro virtuální počítač.

    ![V podokně základy](./media/active-directory-domain-services-admin-guide/create-windows-vm-basics.png)

    > [!TIP]
    > Uživatelské jméno a heslo, které tady zadáte, jsou pro účet místního správce, který se používá k přihlášení k virtuálnímu počítači. Můžete si vyberte silné heslo k ochraně virtuálního počítače před útoky na hesla hrubou silou. Nezadávejte zde přihlašovací údaje pro účet uživatele domény.
    >

5. Vyberte **velikost** pro virtuální počítač. Chcete-li zobrazit další velikosti, vyberte **zobrazit všechny** nebo změnit **podporovaný typ disku** filtru.

    ![V podokně "Zvolte velikost"](./media/active-directory-domain-services-admin-guide/create-windows-vm-size.png)

6. V **nastavení** podokně, vyberte virtuální síť, ve kterém je nasazen vaší služby Azure AD DS spravované domény. Vyberte jinou podsíť. než ty, které vaše spravovaná doména se nasazuje do. U ostatních nastavení nechejte výchozí hodnoty a pak vyberte **OK**.

    ![Nastavení virtuální sítě pro virtuální počítač](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [!TIP]
    > **Vyberte správnou virtuální síť a podsíť.**
    >
    > Vyberte buď virtuální síť, ve kterém je nasazený vaší spravované domény nebo virtuální síť, která se připojí k němu přes virtuální síť vytvoření partnerského vztahu. Pokud vyberete nepřipojené virtuální sítě, nelze připojit virtuální počítač do spravované domény.
    >
    > Doporučujeme nasadit spravované domény do vyhrazenou podsíť. Proto není vyberte podsíť, ve kterém jste povolili vaší spravované domény.

7. U ostatních nastavení nechejte výchozí hodnoty a pak vyberte **OK**.
8. Na **nákupní** stránky, zkontrolujte nastavení a pak vyberte **OK** nasazení virtuálního počítače.
9. Nasazení virtuálního počítače je připnutá na řídicím panelu portálu Azure.

    ![Hotovo](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)
10. Po dokončení nasazení můžete zobrazit informace o virtuálním počítači na **přehled** stránky.


## <a name="step-2-connect-to-the-windows-server-virtual-machine-by-using-the-local-administrator-account"></a>Krok 2: Připojení k virtuálnímu počítači Windows serveru pomocí účtu místního správce
Dále se připojte k nově vytvořenému virtuálnímu počítači Windows serveru pro připojení k doméně. Použijte přihlašovací údaje místního správce, které jste zadali při vytváření virtuálního počítače.

Pro připojení k virtuálnímu počítači, proveďte následující kroky:

1. V **přehled** vyberte **připojit**.  
    Vytvoří a stáhne se soubor Remote Desktop Protocol (RDP).

    ![Připojení k virtuálnímu počítači Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. Chcete-li se připojit k virtuálnímu počítači, otevřete stažený soubor protokolu RDP. Pokud se zobrazí výzva, vyberte **Připojit**.
3. Zadejte vaše **přihlašovací údaje místního správce**, které jste zadali při vytváření virtuálního počítače (například *localhost\mahesh*).
4. Pokud se zobrazí upozornění certifikátu během procesu přihlašování, vyberte **Ano** nebo **pokračovat** pro připojení.

V tomto okamžiku jste by měl být přihlášeni k nově vytvořenému virtuálnímu počítači Windows pomocí přihlašovacích údajů místního správce. Dalším krokem je připojení virtuálního počítače k doméně.


## <a name="step-3-join-the-windows-server-virtual-machine-to-the-azure-ad-ds-managed-domain"></a>Krok 3: Připojení virtuálních počítačů s Windows serverem do Azure AD DS spravované domény
K připojení virtuálních počítačů s Windows serverem do Azure AD DS spravovaných domén, proveďte následující kroky:

1. Připojení k virtuálnímu počítači Windows serveru, jak je znázorněno v "Krok 2." Na **Start** obrazovce otevřete **správce serveru**.
2. V levém podokně **správce serveru** okně **místní Server**.

    ![Okna Správce serveru na virtuálním počítači](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)

3. V části **vlastnosti**vyberte **pracovní skupiny**.
4. V **vlastnosti systému** okně **změnu** připojit se k doméně.

    ![V okně Vlastnosti systému](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)

5. V **domény** , zadejte název vaší služby Azure AD DS spravované domény a potom vyberte **OK**.

    ![Zadejte doménu, který se má spojit](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)

6. Budete vyzváni k zadání přihlašovacích údajů k připojení k doméně. Použijte přihlašovací údaje *uživatele, který patří do skupiny administrators řadič domény Azure AD*. Pouze členové této skupiny mají oprávnění k připojení počítačů ke spravované doméně.

    ![V okně zabezpečení Windows zadáním přihlašovacích údajů](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)

7. Zadejte přihlašovací údaje v některém z následujících způsobů:

   * **Formát UPN**: (doporučeno) zadejte hlavní název uživatele (UPN) příponu pro uživatelský účet, protože nakonfigurovaná ve službě Azure AD. V tomto příkladu přípona UPN uživatele *bob* je *bob@domainservicespreview.onmicrosoft.com*.

   * **Formátu SAMAccountName**: můžete zadat název účtu ve formátu SAMAccountName. V tomto příkladu uživatel *bob* by bylo potřeba zadat *CONTOSO100\bob*.

     > [!TIP]
     > **Doporučujeme použít formát hlavního názvu uživatele k zadání přihlašovacích údajů.**
     >
     > Pokud předponu uživatele (UPN) je příliš dlouhý (například *joehasareallylongname*), SAMAccountName pravděpodobně automaticky generovanou. Pokud máte více uživatelů stejnou předponu hlavní název uživatele (například *bob*) ve vašem tenantovi Azure AD může být automaticky vygenerované službou jejich formátu SAMAccountName. V těchto případech formát UPN je možné spolehlivě pro přihlášení k doméně.
     >

8. Po úspěšném připojení k doméně, tato zpráva vás vítá doméně.

    ![Vítá vás doména](./media/active-directory-domain-services-admin-guide/join-domain-done.png)

9. Abyste mohli dokončit připojení k doméně, restartujte virtuální počítač.

## <a name="troubleshoot-joining-a-domain"></a>Řešení potíží s připojením k doméně
### <a name="connectivity-issues"></a>Problémy s připojením
Pokud virtuální počítač se nepodařilo najít doménu, zkuste následující kroky:

* Ověřte, že virtuální počítač je připojený ke stejné virtuální síti Azure AD DS je povolena v. V opačném případě je virtuální počítač nemůže připojit k nebo připojení k doméně.

* Ověřte, že je virtuální počítač ve virtuální síti, který je zase připojený k virtuální síti Azure AD DS je povolena v.

* Zkuste příkaz ping název domény DNS pro spravovanou doménu (například *odešlete zprávu ping contoso100.com*). Pokud jste to možné, zkuste příkaz ping IP adresy pro doménu, ke které se zobrazí na stránce, kde jste povolili službu Azure AD DS (například *odešlete zprávu ping 10.0.0.4*). Pokud IP adresa, ale nikoli domény může otestovat příkazem ping, pravděpodobně nesprávně nakonfigurována DNS. Zkontrolujte, zda IP adresy řadičů domény jsou nakonfigurovány jako servery DNS pro virtuální síť.

* Zkuste vyprazdňování cache Překladač DNS na virtuálním počítači (*ipconfig/flushdns*).

Pokud se zobrazí okno, které vyzve k zadání přihlašovacích údajů k připojení k doméně, není nutné problémy s připojením.

### <a name="credentials-related-issues"></a>Problémy související s přihlašovacími údaji
Pokud máte potíže s přihlašovacími údaji a se nám připojit se k doméně, vyzkoušejte následující kroky:

* Zkuste použít formát hlavního názvu uživatele k zadání přihlašovacích údajů. Pokud existuje mnoho uživatelů se stejnou předponou hlavní název uživatele ve vašem tenantovi nebo předponu hlavní název uživatele je příliš dlouhý, může být automaticky generované SAMAccountName pro váš účet. V těchto případech formátu SAMAccountName pro váš účet může lišit od co můžete očekávat, nebo použít v místní doméně.

* Zkuste použít přihlašovací údaje uživatelského účtu, který patří do *správci AAD DC* skupiny.

* Zkontrolujte, jestli máte [povolili synchronizaci hesel](active-directory-ds-getting-started-password-sync.md) do spravované domény.

* Zkontrolujte, že jste použili (UPN) uživatele, jak nakonfigurovat ve službě Azure AD (například *bob@domainservicespreview.onmicrosoft.com*) pro přihlášení.

* Počkejte dostatečně dlouho synchronizace hesel do dokončení, jak je uvedeno v získávání Příručka Začínáme.

## <a name="related-content"></a>Související obsah
* [Azure AD DS – Příručka Začínáme](active-directory-ds-getting-started.md)
* [Správa DS spravované domény služby Azure AD](active-directory-ds-admin-guide-administer-domain.md)
