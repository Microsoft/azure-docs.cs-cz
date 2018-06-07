---
title: 'Azure Active Directory Domain Services: Připojení virtuálního počítače Windows serveru ke spravované doméně | Microsoft Docs'
description: Připojení k virtuálnímu počítači s Windows serverem ve službě Azure AD DS
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 29316313-c76c-4fb9-8954-5fa5ec82609e
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: maheshu
ms.openlocfilehash: dadc20cdee68730fa1d81dd86b3ffa0b0022a5b1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34586949"
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Připojení virtuálního počítače Windows Server ke spravované doméně
Tento článek ukazuje, jak nasazení virtuálního počítače s Windows serverem pomocí portálu Azure. Potom ukazuje, jak připojit virtuální počítač k spravované doméně služby Azure Active Directory Domain Services (Azure AD DS).

## <a name="step-1-create-a-windows-server-virtual-machine"></a>Krok 1: Vytvoření virtuálního počítače s Windows serverem
K vytvoření virtuálního počítače s Windows, který je připojen k virtuální síti, ve kterém jste povolili službu Azure AD DS, postupujte takto:

1. Přihlaste se k [portálu Azure](http://portal.azure.com).
2. V horní části levého podokna, vyberte **nový**.
3. Vyberte **Compute** a potom vyberte **Windows Server 2016 Datacenter**.

    ![Windows Server 2016 Datacenter odkaz](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)

4. V **Základy** podokně průvodce, nakonfigurovali základní nastavení pro virtuální počítač.

    ![V podokně základy](./media/active-directory-domain-services-admin-guide/create-windows-vm-basics.png)

    > [!TIP]
    > Uživatelské jméno a heslo, které tady zadáte, jsou pro účet místního správce, který se používá k přihlášení k virtuálnímu počítači. Vyberte silné heslo k ochraně virtuálního počítače před útoky hrubou silou heslo. Nezadávejte zde přihlašovací údaje pro účet uživatele domény.
    >

5. Vyberte **velikost** pro virtuální počítač. Chcete-li zobrazit další velikosti, vyberte **zobrazit všechny** nebo změňte **podporován typ disku** filtru.

    ![V podokně "Zvolte velikost"](./media/active-directory-domain-services-admin-guide/create-windows-vm-size.png)

6. V **nastavení** podokně, vyberte virtuální síť, ve kterém je nasazený vaší doméně Azure AD DS spravovat. Vyberte jinou podsíť než ten, který je nasazený vaší spravované domény do. U ostatních nastavení, ponechejte výchozí hodnoty a potom vyberte **OK**.

    ![Nastavení virtuální sítě pro virtuální počítač](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [!TIP]
    > **Vyberte správnou virtuální síť a podsíť.**
    >
    > Vyberte buď virtuální síť, ve kterém je nasazený vaší spravované domény nebo virtuální síť, která je připojený ke svému pomocí virtuální sítě, partnerský vztah. Pokud vyberete bez připojení virtuální sítě, nemůže připojit k virtuální počítač k spravované doméně.
    >
    > Doporučujeme, abyste nasazení vaší spravované domény do vyhrazené podsíť. Proto není vyberte podsíť, ve kterém jste povolili vaší spravované domény.

7. U ostatních nastavení, ponechejte výchozí hodnoty a potom vyberte **OK**.
8. Na **nákupu** , zkontrolujte nastavení a pak vyberte **OK** pro nasazení virtuálního počítače.
9. Nasazení virtuálního počítače je připnuli k řídicímu panelu portálu Azure.

    ![Hotovo](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)
10. Po dokončení nasazení můžete zobrazit informace o virtuálním počítači na **přehled** stránky.


## <a name="step-2-connect-to-the-windows-server-virtual-machine-by-using-the-local-administrator-account"></a>Krok 2: Připojení k virtuálnímu počítači Windows serveru pomocí účtu místního správce
V dalším kroku připojte k nově vytvořený virtuální počítač Windows serveru pro připojení k doméně. Použijte přihlašovací údaje místního správce, kterou jste zadali při vytváření virtuálního počítače.

Pro připojení k virtuálnímu počítači, postupujte takto:

1. V **přehled** podokně, vyberte **Connect**.  
    Soubor Remote Desktop Protocol (.rdp) je vytvořen a stáhli.

    ![Připojení k systému Windows virtuálního počítače](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. Chcete-li se připojit k virtuálnímu počítači, otevřete stažený soubor protokolu RDP. Pokud se zobrazí výzva, vyberte **Connect**.
3. V okně pro přihlášení, zadejte vaše **přihlašovací údaje místního správce**, který jste zadali při vytváření virtuálního počítače (například *localhost\mahesh*).
4. Pokud se zobrazí varování týkající se certifikátu během procesu přihlášení, pokračovat v připojení výběrem **Ano** nebo **pokračovat**.

V tomto okamžiku jste by měl být přihlášeni na nově vytvořený virtuální počítač Windows pomocí svých přihlašovacích údajů místního správce. Dalším krokem je připojení virtuálního počítače k doméně.


## <a name="step-3-join-the-windows-server-virtual-machine-to-the-azure-ad-ds-managed-domain"></a>Krok 3: Připojení virtuálního počítače Windows serveru do Azure AD DS spravovat domény
Pokud chcete připojit k Azure AD DS spravované domény virtuálního počítače Windows serveru, postupujte takto:

1. Připojit k virtuální počítač Windows serveru, jak je znázorněno v "Krok 2." Na **spustit** obrazovce otevřete **správce serveru**.
2. V levém podokně **správce serveru** vyberte **místní Server**.

    ![Okna Správce serveru na virtuálním počítači](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)

3. V části **vlastnosti**, vyberte **pracovní skupiny**. 
4. V **vlastnosti systému** vyberte **změnu** pro připojení k doméně.

    ![Okno Vlastnosti systému](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)

5. V **domény** , zadejte název vaší služby Azure AD DS spravované domény a pak vyberte **OK**.

    ![Určení domény pro připojení](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)

6. Zobrazí se výzva k zadání pověření pro připojení k doméně. Zadejte přihlašovací údaje *uživatele, který patří do skupiny správců Azure řadič domény AD*. Pouze členové této skupiny mají oprávnění k připojení počítačů k spravované doméně.

    ![Okno zabezpečení systému Windows pro zadání přihlašovacích údajů](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)

7. Můžete zadat přihlašovací údaje v některém z následujících způsobů:

   * **Formát UPN**: (doporučeno) zadejte hlavní název uživatele (UPN) příponu pro uživatelský účet, jak nakonfigurovat ve službě Azure AD. V tomto příkladu příponu UPN uživatele *bob* je *bob@domainservicespreview.onmicrosoft.com*.

   * **Formát SAMAccountName**: můžete zadat název účtu ve formátu SAMAccountName. V tomto příkladu uživatel *bob* by bylo potřeba zadejte *CONTOSO100\bob*.

     > [!TIP]
     > **Doporučujeme použít formát UPN a zadejte přihlašovací údaje.**
     >
     > Pokud uživatele (UPN) předpona je příliš dlouhý (například *joehasareallylongname*), SAMAccountName může být automaticky generovaný. Pokud máte více uživatelů stejnou předponou hlavní název uživatele (například *bob*) v klientovi služby Azure AD může být automaticky vygenerované službou jejich SAMAccountName formátu. V těchto případech formát UPN lze spolehlivě se přihlásit k doméně.
     >

8. Po úspěšném připojení k doméně, se následující zpráva vás vítá do domény.

    ![Vítejte v doméně](./media/active-directory-domain-services-admin-guide/join-domain-done.png)

9. Pokud chcete provést připojení k doméně, restartujte virtuální počítač.

## <a name="troubleshoot-joining-a-domain"></a>Řešení potíží s připojením k doméně
### <a name="connectivity-issues"></a>Problémy s připojením
Pokud je virtuální počítač se nepodařilo najít doménu, zkuste jeden nebo více následujících akcí:

* Ujistěte se, že virtuální počítač je připojený ke stejné virtuální síti jako ten, který jste povolili službu Azure AD DS v. Pokud není připojený, virtuální počítač se nemůže připojit k doméně a proto se nepodařilo připojit k doméně.

* Zajistěte, aby byl virtuální počítač na virtuální síť, která je pak připojen k virtuální síti, ve kterém jste povolili službu Azure AD DS.

* Zkuste příkazem ping otestovat doménu pomocí názvu domény spravované domény (například *příkaz ping contoso100.com*). Pokud jste to možné, zkuste příkazem ping otestovat IP adresy pro doménu, která se zobrazí na stránce, kde jste povolili službu Azure AD DS (například *příkaz ping 10.0.0.4*). Pokud budete moct odeslat příkaz ping IP adresu, ale nikoli domény, můžou být nesprávně nakonfigurované DNS. Zkontrolujte, zda jsou IP adresy domény nakonfigurovány jako servery DNS pro virtuální síť.

* Zkuste to, abyste vyprázdnili mezipaměť Překladač DNS na virtuálním počítači (*ipconfig/flushdns*).

Pokud se zobrazí okno, která požaduje zadání pověření pro připojení k doméně, nemáte problémy s připojením.

### <a name="credentials-related-issues"></a>Problémy související s přihlašovací údaje
Pokud máte problémy s přihlašovacími údaji a nelze k připojení k doméně, zkuste jeden nebo více následujících akcí:

* Použijte formát UPN a zadejte přihlašovací údaje. Pokud existuje více uživatelů se stejnou předponou UPN v klientovi nebo pokud vaši předponu UPN je příliš dlouhý, může být automaticky generovaný SAMAccountName pro váš účet. Proto SAMAccountName formát pro váš účet může lišit od co můžete očekávat, nebo použít v místní doméně.

* Zkuste použít přihlašovací údaje uživatelského účtu, který patří do *AAD řadič domény správci* skupiny.

* Ujistěte se, že máte [povolena synchronizace hesel](active-directory-ds-getting-started-password-sync.md) podle kroků uvedených v Průvodci získávání spuštěna.

* Zkontrolujte, jestli hlavní název uživatele uživatele používají jako nakonfigurovaný v Azure AD (například *bob@domainservicespreview.onmicrosoft.com*) pro přihlášení.

* Ujistěte se, že jste čekali dost dlouho kvůli synchronizaci hesel dokončeno, jak je uvedeno v Příručka Začínáme.

## <a name="related-content"></a>Související obsah
* [Azure AD DS Příručka Začínáme](active-directory-ds-getting-started.md)
* [Spravovat domény služby Azure AD DS spravovat](active-directory-ds-admin-guide-administer-domain.md)
