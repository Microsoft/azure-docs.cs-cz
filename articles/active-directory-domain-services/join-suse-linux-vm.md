---
title: Připojte virtuální počítač SLE k Azure AD Domain Services | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat a připojit virtuální počítač s SUSE Linux Enterprise k Azure AD Domain Services spravované doméně.
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 08/12/2020
ms.author: joflore
ms.openlocfilehash: 5d89f1a3d6028afb3450e0112a6081c9c706775b
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91962458"
---
# <a name="join-a-suse-linux-enterprise-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Připojení virtuálního počítače s SUSE Linux Enterprise k Azure Active Directory Domain Services spravované doméně

Pokud chcete umožnit uživatelům přihlašovat se k virtuálním počítačům v Azure pomocí jediné sady přihlašovacích údajů, můžete připojit virtuální počítače k spravované doméně Azure Active Directory Domain Services (Azure služba AD DS). Když připojíte virtuální počítač k spravované doméně Azure služba AD DS, můžete k přihlášení a správě serverů použít uživatelské účty a přihlašovací údaje z domény. Členství ve skupinách ze spravované domény se používá také k řízení přístupu k souborům nebo službám na virtuálním počítači.

V tomto článku se dozvíte, jak připojit virtuální počítač s SUSE Linux Enterprise (SLE) ke spravované doméně.

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Musíte mít aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
* Ve vašem tenantovi Azure AD je povolená a nakonfigurovaná spravovaná doména Azure Active Directory Domain Services.
    * V případě potřeby se v prvním kurzu [vytvoří a nakonfiguruje Azure Active Directory Domain Services spravovaná doména][create-azure-ad-ds-instance].
* Uživatelský účet, který je součástí spravované domény.

## <a name="create-and-connect-to-a-sle-linux-vm"></a>Vytvoření virtuálního počítače se systémem SLE Linux a připojení k němu

Pokud máte v Azure existující virtuální počítač s SLE Linux, připojte se k němu pomocí SSH a potom pokračujte dalším krokem a [Spusťte konfiguraci virtuálního počítače](#configure-the-hosts-file).

Pokud potřebujete vytvořit virtuální počítač s SLE Linux nebo chcete vytvořit testovací virtuální počítač pro použití s tímto článkem, můžete použít jednu z následujících metod:

* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Když vytváříte virtuální počítač, věnujte pozornost nastavení virtuální sítě a ujistěte se, že virtuální počítač může komunikovat se spravovanou doménou:

* Nasaďte virtuální počítač do stejné nebo partnerské virtuální sítě, ve které jste povolili Azure AD Domain Services.
* Virtuální počítač nasaďte do jiné podsítě, než jakou má vaše Azure AD Domain Services spravovaná doména.

Až se virtuální počítač nasadí, připojte se k virtuálnímu počítači pomocí SSH podle pokynů.

## <a name="configure-the-hosts-file"></a>Konfigurace souboru hostitelů

Abyste se ujistili, že je název hostitele virtuálního počítače správně nakonfigurovaný pro spravovanou doménu, upravte soubor */etc/hosts* a nastavte název hostitele:

```console
sudo vi /etc/hosts
```

V souboru *hosts* aktualizujte adresu *localhost* . V následujícím příkladu:

* *aaddscontoso.com* je název domény DNS vaší spravované domény.
* *Linux-q2gr* je název hostitele vašeho virtuálního počítače s SLE, ke kterému se připojujete do spravované domény.

Aktualizujte tyto názvy vlastními hodnotami:

```console
127.0.0.1 linux-q2gr linux-q2gr.aaddscontoso.com
```

Po dokončení uložte a zavřete soubor *hosts* pomocí `:wq` příkazu editoru.

## <a name="join-vm-to-the-managed-domain-using-sssd"></a>Připojení virtuálního počítače ke spravované doméně pomocí SSSD

Pokud se chcete připojit ke spravované doméně pomocí **SSSD** a modulu *pro správu přihlášení uživatele* YaST, proveďte následující kroky:

1. Nainstalujte modul YaST *pro správu přihlášení k uživateli* :

    ```bash
    sudo zypper install yast2-auth-client
    ```

1. Otevřete YaST.

1. Chcete-li úspěšně použít automatické zjišťování DNS, nakonfigurujte pro klienta jako názvový server IP adresy spravované domény ( *Server služby Active Directory*).

    V YaST vyberte **nastavení systémové > sítě**.

1. Vyberte kartu *název hostitele/DNS* a potom do textového pole *názvového serveru*zadejte IP adresu (ES) spravované domény. Tyto IP adresy se zobrazí v okně *vlastnosti* v Azure Portal vaší spravované domény, jako je například *10.0.2.4* a *10.0.2.5*.

    Přidejte vlastní IP adresy spravované domény a pak vyberte **OK**.

1. V hlavním okně YaST vyberte *Network Services*  >  *Správa přihlášení k uživateli*.

    Modul se otevře s přehledem o různých vlastnostech sítě vašeho počítače a aktuálně používané metody ověřování, jak je znázorněno v následujícím příkladu obrazovky:

    ![Příklad obrazovky okna pro správu přihlášení uživatele v YaST](./media/join-suse-linux-vm/overview-window.png)

    Chcete-li začít upravovat, vyberte možnost **změnit nastavení**.

Pokud se chcete připojit k virtuálnímu počítači ke spravované doméně, proveďte následující kroky:

1. V dialogovém okně vyberte **Přidat doménu**.

1. Zadejte správný *název domény*, třeba *aaddscontoso.com*, a pak zadejte služby, které se mají použít pro data a ověřování identity. Pro obě vyberte *Microsoft Active Directory* .

    Ujistěte se, že je vybraná možnost *Povolit doménu* .

1. Až budete připraveni, vyberte **OK**.

1. Přijměte výchozí nastavení v následujícím dialogovém okně a pak vyberte **OK**.

1. Virtuální počítač nainstaluje podle potřeby další software a pak zkontroluje, jestli je spravovaná doména k dispozici.

    Pokud je vše správné, zobrazí se následující vzorový dialog, který indikuje, že virtuální počítač zjistil spravovanou doménu, ale ještě nejste *zaregistrovaní*.

    ![Příklad obrazovky okna registrace služby Active Directory v YaST](./media/join-suse-linux-vm/enroll-window.png)

1. V dialogovém okně zadejte *uživatelské jméno* a *heslo* uživatele, který je součástí spravované domény. V případě potřeby [přidejte uživatelský účet do skupiny v Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Pokud chcete mít jistotu, že je aktuální doména povolená pro službu Samba, aktivujte *možnost přepsat konfiguraci služby Samba, aby fungovala s touto službou AD*.

1. Pokud se chcete zaregistrovat, vyberte **OK**.

1. Zobrazí se zpráva s potvrzením, že jste úspěšně zaregistrovali. Pro dokončení vyberte **OK**.

Jakmile je virtuální počítač zaregistrovaný ve spravované doméně, nakonfigurujte klienta pomocí *možnosti Spravovat přihlášení uživatele domény*, jak je znázorněno na následujícím ukázkovém snímku obrazovky:

![Příklad obrazovky okna Správa přihlášení uživatele domény v YaST](./media/join-suse-linux-vm/manage-domain-user-logon-window.png)

1. Pokud chcete přihlašovat pomocí dat poskytovaných spravovanou doménou, zaškrtněte políčko pro možnost *Povolení přihlášení uživatele domény*.

1. Volitelně můžete v části *Povolit zdroj dat domény*kontrolovat další zdroje dat, které jsou potřeba pro vaše prostředí. Tyto možnosti zahrnují, kteří uživatelé smějí používat **sudo** nebo které síťové jednotky jsou k dispozici.

1. Pokud chcete uživatelům ve spravované doméně dovolit, aby měli domovské adresáře na virtuálním počítači, zaškrtněte políčko *vytvořit domovské adresáře*.

1. Na bočním panelu vyberte **Možnosti služby › název přepínače**a pak *Rozšířené možnosti*. Z tohoto okna vyberte buď *fallback_homedir* , nebo *override_homedir*a pak vyberte **Přidat**.

1. Zadejte hodnotu umístění domovského adresáře. Chcete-li mít domovské adresáře, postupujte podle formátu */home/user_name*použijte */Home/%u*. Další informace o možných proměnných naleznete na stránce SSSD. conf Man ( `man 5 sssd.conf` ), část *override_homedir*.

1. Vyberte **OK**.

1. Pokud chcete změny uložit, vyberte **OK**. Pak se ujistěte, že jsou hodnoty zobrazené nyní správné. Pokud chcete dialogové okno opustit, vyberte **Zrušit**.

1. Pokud máte v úmyslu spouštět SSSD a winbind současně (například při připojení prostřednictvím SSSD, ale na serveru se systémem souborů Samba), měla by být *Metoda protokolu Kerberos* možnosti serveru Samba nastavená na *tajné klíče a keytab* v SMB. conf. Možnost SSSD *ad_update_samba_machine_account_password* by měla být také nastavena na *hodnotu true* v SSSD. conf. Tyto možnosti zabrání tomu, aby systém keytab ze synchronizace.

## <a name="join-vm-to-the-managed-domain-using-winbind"></a>Připojení virtuálního počítače ke spravované doméně pomocí winbind

Pokud se chcete připojit ke spravované doméně pomocí **winbind** a modulu *členství v doméně systému Windows* YaST, proveďte následující kroky:

1. V YaST vyberte **Network Services > členství v doméně Windows**.

1. Zadejte doménu, ke které se připojíte v *doméně nebo pracovní skupině* na obrazovce *členství v doméně systému Windows* . Zadejte název spravované domény, například *aaddscontoso.com*.

    ![Příklad obrazovky okna členství v doméně systému Windows v YaST](./media/join-suse-linux-vm/samba-client-window.png)

1. Pokud chcete použít zdroj SMB pro ověřování Linux, podívejte se na možnost *použití informací SMB pro ověřování Linux*.

1. Pro automatické vytvoření místního domovského adresáře pro uživatele spravované domény na virtuálním počítači si Projděte možnost *vytvoření domovského adresáře při přihlášení*.

1. Pokud chcete, aby se uživatelé domény mohli přihlašovat i v případě, že je tato spravovaná doména dočasně nedostupná, ověřte možnost *ověřování v režimu offline* .

1. Pokud chcete změnit rozsahy UID a GID pro uživatele a skupiny služby Samba, vyberte možnost *Expertní nastavení*.

1. Výběrem *Konfigurace NTP*nakonfigurujte synchronizaci času NTP pro spravovanou doménu. Zadejte IP adresy spravované domény. Tyto IP adresy se zobrazí v okně *vlastnosti* v Azure Portal vaší spravované domény, jako je například *10.0.2.4* a *10.0.2.5*.

1. Po zobrazení výzvy vyberte **OK** a potvrďte připojení k doméně.

1. Zadejte heslo pro správce ve spravované doméně a vyberte **OK**.

    ![Ukázkový snímek obrazovky s výzvou k ověření při připojení virtuálního počítače s SLE ke spravované doméně](./media/join-suse-linux-vm/domain-join-authentication-prompt.png)

Až se připojíte ke spravované doméně, můžete se k ní přihlásit z pracovní stanice pomocí Správce zobrazení plochy nebo konzoly.

## <a name="allow-password-authentication-for-ssh"></a>Povolení ověřování hesla pro SSH

Ve výchozím nastavení se uživatelé můžou přihlásit jenom k virtuálnímu počítači pomocí ověřování založeného na veřejných klíčích SSH. Ověřování pomocí hesla se nezdařilo. Když připojíte virtuální počítač ke spravované doméně, musí tyto účty domény používat ověřování pomocí hesla. Aktualizujte konfiguraci SSH tak, aby umožňovala ověřování na základě hesla následujícím způsobem.

1. Otevřete *sshd_conf* soubor pomocí editoru:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. Aktualizujte řádek pro *PasswordAuthentication* na *Ano*:

    ```console
    PasswordAuthentication yes
    ```

    Po dokončení uložte a zavřete soubor *sshd_conf* pomocí `:wq` příkazu editoru.

1. Pokud chcete změny použít a umožnit uživatelům, aby se přihlásili pomocí hesla, restartujte službu SSH:

    ```console
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Udělte skupině AAD DC Administrators oprávnění sudo

Pokud chcete členům skupiny *AAD DC Administrators* udělit na virtuálním počítači SLE oprávnění správce, přidejte položku do */etc/sudoers*. Po přidání můžou členové skupiny *Správci AAD DC* použít `sudo` příkaz na virtuálním počítači s SLE.

1. Otevřete soubor *sudoers* pro úpravy:

    ```console
    sudo visudo
    ```

1. Přidejte následující položku na konec souboru */etc/sudoers* . Skupina *AAD DC Administrators* obsahuje v názvu prázdné znaky, takže v názvu skupiny zahrňte řídicí znak zpětného lomítka. Přidejte vlastní název domény, například *aaddscontoso.com*:

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    Až budete hotovi, uložte a ukončete Editor pomocí `:wq` příkazu editoru.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Přihlaste se k virtuálnímu počítači pomocí účtu domény.

Chcete-li ověřit, zda byl virtuální počítač úspěšně připojen ke spravované doméně, spusťte nové připojení SSH pomocí účtu uživatele domény. Potvrďte, že byl vytvořen domovský adresář a že je použito členství ve skupině z domény.

1. Vytvořte nové připojení SSH z konzoly. Pomocí příkazu použijte doménový účet, který patří do spravované domény `ssh -l` , `contosoadmin@aaddscontoso.com` a pak zadejte adresu vašeho virtuálního počítače, třeba *Linux-q2gr.aaddscontoso.com*. Pokud používáte Azure Cloud Shell, použijte veřejnou IP adresu virtuálního počítače místo interního názvu DNS.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com linux-q2gr.aaddscontoso.com
    ```

2. Po úspěšném připojení k virtuálnímu počítači ověřte, zda byl domovský adresář správně inicializován:

    ```console
    pwd
    ```

    Měli byste být v adresáři */Home* s vlastním adresářem, který odpovídá uživatelskému účtu.

3. Nyní ověřte, zda jsou členství ve skupině správně řešena:

    ```console
    id
    ```

    Měli byste vidět členství ve skupinách ze spravované domény.

4. Pokud jste se k virtuálnímu počítači přihlásili jako člen skupiny *AAD DC Administrators* , ověřte, že můžete správně použít `sudo` příkaz:

    ```console
    sudo zypper update
    ```

## <a name="next-steps"></a>Další kroky

Pokud máte problémy s připojením k virtuálnímu počítači ke spravované doméně nebo přihlášením pomocí účtu domény, přečtěte si téma [řešení potíží s připojením k doméně](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
