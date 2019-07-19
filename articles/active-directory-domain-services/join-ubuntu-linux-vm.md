---
title: 'Azure Active Directory Domain Services: Připojení virtuálního počítače s Ubuntu ke spravované doméně | Microsoft Docs'
description: Připojení virtuálního počítače s Ubuntu Linux k Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 29a6cb69a818ed11e5f20dddd7299c01fbefbf47
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234023"
---
# <a name="join-an-ubuntu-virtual-machine-in-azure-to-a-managed-domain"></a>Připojení virtuálního počítače s Ubuntu v Azure ke spravované doméně
V tomto článku se dozvíte, jak připojit Ubuntu Linux virtuální počítač k Azure AD Domain Services spravované doméně.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Před zahájením
K provedení úkolů uvedených v tomto článku budete potřebovat:  
1. Platné **předplatné Azure**.
2. **Adresář služby Azure AD** – buď synchronizovaný s místním adresářem, nebo s adresářem jenom pro Cloud.
3. **Azure AD Domain Services** musí být povolený pro adresář služby Azure AD. Pokud jste to ještě neudělali, postupujte podle všech úkolů popsaných v [průvodci Začínáme](create-instance.md).
4. Ujistěte se, že jste nakonfigurovali IP adresy spravované domény jako servery DNS pro virtuální síť. Další informace najdete v tématu [Postup aktualizace nastavení DNS pro virtuální síť Azure](active-directory-ds-getting-started-dns.md) .
5. Dokončete kroky potřebné k [synchronizaci hesel s Azure AD Domain Services spravované doméně](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-an-ubuntu-linux-virtual-machine"></a>Zřízení virtuálního počítače s Ubuntu Linux
Pomocí kterékoli z následujících metod zřídíte Ubuntu Linux virtuální počítač v Azure:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Nasaďte virtuální počítač do **stejné virtuální sítě, ve které jste povolili Azure AD Domain Services**.
> * Vyberte **jinou podsíť** , než kterou jste povolili Azure AD Domain Services.
>


## <a name="connect-remotely-to-the-ubuntu-linux-virtual-machine"></a>Vzdálené připojení k Ubuntu Linuxmu virtuálnímu počítači
Virtuální počítač Ubuntu se zřídil v Azure. Dalším úkolem je vzdálené připojení k virtuálnímu počítači pomocí účtu místního správce vytvořeného při zřizování virtuálního počítače.

Postupujte podle pokynů v článku [jak se přihlásit k virtuálnímu počítači se systémem Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Konfigurace souboru hostitelů na virtuálním počítači se systémem Linux
V terminálu SSH upravte soubor/etc/hosts a aktualizujte IP adresu počítače a název hostitele.

```console
sudo vi /etc/hosts
```

V souboru Hosts zadejte následující hodnotu:

```console
127.0.0.1 contoso-ubuntu.contoso100.com contoso-ubuntu
```

Tady je "contoso100.com" název domény DNS vaší spravované domény. contoso-Ubuntu je název hostitele virtuálního počítače s Ubuntu, ke kterému se připojujete, ke spravované doméně.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Nainstalovat požadované balíčky na virtuálním počítači se systémem Linux
Dále nainstalujte balíčky požadované pro připojení k doméně na virtuálním počítači. Proveďte následující kroky:

1.  V terminálu SSH zadejte následující příkaz pro stažení seznamů balíčků z úložišť. Tento příkaz aktualizuje seznamy balíčků, aby získal informace o nejnovějších verzích balíčků a jejich závislostech.

    ```console
    sudo apt-get update
    ```

2. Zadáním následujícího příkazu nainstalujte požadované balíčky.

    ```console
      sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
    ```

3. Během instalace protokolu Kerberos se zobrazí růžová obrazovka. Instalace balíčku krb5-User se vyzve k zadání názvu sféry (všechna velká písmena). Instalace zapisuje oddíly [Realm] a [domain_realm] v/etc/krb5.conf.

    > [!TIP]
    > Pokud je název spravované domény contoso100.com, jako sféru zadejte CONTOSO100.COM. Nezapomeňte, že název sféry musí být zadaný velkými PÍSMENy.


## <a name="configure-the-ntp-network-time-protocol-settings-on-the-linux-virtual-machine"></a>Konfigurace nastavení protokolu NTP (Network Time Protocol) na virtuálním počítači se systémem Linux
Datum a čas vašeho virtuálního počítače s Ubuntu se musí synchronizovat se spravovanou doménou. Do souboru/etc/NTP.conf přidejte název hostitele NTP spravované domény.

```console
sudo vi /etc/ntp.conf
```

Do souboru NTP. conf zadejte následující hodnotu a soubor uložte:

```console
server contoso100.com
```

Tady je "contoso100.com" název domény DNS vaší spravované domény.

Teď synchronizujete datum a čas virtuálního počítače Ubuntu se serverem NTP a potom spusťte službu NTP:

```console
sudo systemctl stop ntp
sudo ntpdate contoso100.com
sudo systemctl start ntp
```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Připojit se k virtuálnímu počítači Linux ke spravované doméně
Teď, když jsou na virtuálním počítači se systémem Linux nainstalované požadované balíčky, je dalším úkolem připojit se k virtuálnímu počítači do spravované domény.

1. Seznamte se se spravovanou doménou služby AAD Domain Services. Do terminálu SSH zadejte následující příkaz:

    ```console
    sudo realm discover CONTOSO100.COM
    ```

   > [!NOTE]
   > **Při** Pokud *Zjišťování sféry* nemůže najít spravovanou doménu:
   >   * Zajistěte, aby byla doména dosažitelná z virtuálního počítače (zkuste použít test pomocí testu).
   >   * Ověřte, zda je virtuální počítač skutečně nasazený do stejné virtuální sítě, ve které je dostupná spravovaná doména.
   >   * Zkontrolujte, jestli jste aktualizovali nastavení serveru DNS pro virtuální síť tak, aby odkazovala na řadiče domény spravované domény.

2. Inicializujte Kerberos. Do terminálu SSH zadejte následující příkaz:

    > [!TIP]
    > * Ujistěte se, že zadáváte uživatele, který patří do skupiny AAD DC Administrators.
    > * Zadejte název domény velkými písmeny, jinak kinit neproběhne.
    >

    ```console
    kinit bob@CONTOSO100.COM
    ```

3. Připojte počítač k doméně. Do terminálu SSH zadejte následující příkaz:

    > [!TIP]
    > Použijte stejný uživatelský účet, který jste zadali v předchozím kroku (' kinit ').

    ```console
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM' --install=/
    ```

Pokud se počítač úspěšně připojí ke spravované doméně, měla by se zobrazit zpráva ("úspěšně zaregistrovaný počítač ve sféře").


## <a name="update-the-sssd-configuration-and-restart-the-service"></a>Aktualizujte konfiguraci SSSD a restartujte službu.
1. Do terminálu SSH zadejte následující příkaz. Otevřete soubor SSSD. conf a proveďte následující změnu.
    
    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

2. Odkomentujte řádek **use_fully_qualified_names = true** a uložte soubor.
    
    ```console
    # use_fully_qualified_names = True
    ```

3. Restartujte službu SSSD.
    
    ```console
    sudo service sssd restart
    ```


## <a name="configure-automatic-home-directory-creation"></a>Konfigurace automatického vytvoření domovského adresáře
Pokud chcete po přihlášení uživatelů Povolit automatické vytváření domovského adresáře, zadejte v terminálu pro výstup následující příkazy:

```console
sudo vi /etc/pam.d/common-session
```

Do tohoto souboru přidejte následující řádek pod řádkem "session Optional pam_sss. so" a uložte ho:

```console
session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
```


## <a name="verify-domain-join"></a>Ověření připojení k doméně
Ověřte, jestli se počítač úspěšně připojil ke spravované doméně. Připojte se k virtuálnímu počítači Ubuntu připojenému k doméně pomocí jiného připojení SSH. Použijte účet uživatele domény a potom zkontrolujte, jestli je uživatelský účet správně vyřešený.

1. Do terminálu SSH zadejte následující příkaz, který se připojí k doméně připojené k doméně Ubuntu pomocí SSH. Použijte doménový účet, který patří do spravované domény (napříkladbob@CONTOSO100.COMv tomto případě).
    
    ```console
    ssh -l bob@CONTOSO100.COM contoso-ubuntu.contoso100.com
    ```

2. V terminálu SSH zadejte následující příkaz, který zjistí, zda byl domovský adresář správně inicializován.
    
    ```console
    pwd
    ```

3. V terminálu SSH zadejte následující příkaz, abyste viděli, jestli se členství ve skupině správně řeší.
    
    ```console
    id
    ```


## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Udělte skupině AAD DC Administrators oprávnění sudo
Na virtuálním počítači s Ubuntu můžete členům skupiny AAD DC Administrators udělit oprávnění správce. Soubor sudo se nachází na adrese/etc/sudoers. Členové skupin AD přidaní v sudoers mohou provádět sudo.

1. V terminálu SSH se ujistěte, že jste přihlášeni pomocí oprávnění uživatele. Můžete použít účet místního správce, který jste zadali při vytváření virtuálního počítače. Spusťte následující příkaz:
    
    ```console
    sudo vi /etc/sudoers
    ```

2. Do souboru/etc/sudoers přidejte následující položku a uložte ji:
    
    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

3. Nyní se můžete přihlásit jako člen skupiny AAD DC Administrators a mít oprávnění správce na virtuálním počítači.


## <a name="troubleshooting-domain-join"></a>Řešení potíží s připojením k doméně
Informace najdete v článku [věnovaném řešení potíží s připojením k doméně](join-windows-vm.md#troubleshoot-joining-a-domain) .


## <a name="related-content"></a>Související obsah
* [Azure AD Domain Services – Průvodce Začínáme](create-instance.md)
* [Připojení virtuálního počítače s Windows serverem k spravované doméně Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Jak se přihlásit k virtuálnímu počítači se systémem Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
