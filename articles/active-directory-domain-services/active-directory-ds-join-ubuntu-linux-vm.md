---
title: 'Azure Active Directory Domain Services: Připojení virtuálního počítače s Ubuntu k spravované doméně | Dokumentace Microsoftu'
description: Připojení virtuálního počítače s Ubuntu Linuxem do Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: 35b37a5c50346dc116b2f36394feecb82a0d88fd
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54845747"
---
# <a name="join-an-ubuntu-virtual-machine-in-azure-to-a-managed-domain"></a>Připojení virtuálního počítače s Ubuntu v Azure k spravované doméně
V tomto článku se dozvíte, jak propojit virtuální počítače s Ubuntu Linux k spravované doméně služby Azure AD Domain Services.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Před zahájením
K provádění úkolů uvedených v tomto článku, budete potřebovat:  
1. Platný **předplatného Azure**.
2. **Adresář Azure AD** – buď synchronizaci s místním adresářem nebo výhradně cloudový adresář.
3. **Azure AD Domain Services** musí být povolené pro adresář Azure AD. Pokud jste neudělali, postupujte podle všechny úkoly popsané v [příručce Začínáme](active-directory-ds-getting-started.md).
4. Ujistěte se, že jste nakonfigurovali IP adres spravované domény jako servery DNS pro virtuální síť. Další informace najdete v tématu [postup aktualizace nastavení DNS pro virtuální síť Azure](active-directory-ds-getting-started-dns.md)
5. Dokončete kroky potřebné k [synchronizace hesel do spravované domény služby Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-an-ubuntu-linux-virtual-machine"></a>Zřízení virtuálního počítače s Ubuntu Linuxem
Zřízení virtuálního počítače s Ubuntu Linuxem v Azure pomocí kteréhokoli z následujících metod:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Nasazení virtuálního počítače do **stejné virtuální síti, ve kterém jste povolili službu Azure AD Domain Services**.
> * Vyberte **jinou podsíť** než ten, ve kterém jste povolili službu Azure AD Domain Services.
>


## <a name="connect-remotely-to-the-ubuntu-linux-virtual-machine"></a>Vzdálené připojení k virtuálním počítači s Ubuntu Linuxem
Zřízení virtuálního počítače se systémem Ubuntu v Azure. Další úlohou je vzdáleně připojit k virtuálnímu počítači pomocí účtu místního správce vytvořené při zřizování virtuálních počítačů.

Postupujte podle pokynů v článku [jak se přihlásit k virtuálnímu počítači s Linuxem](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Konfigurace souboru hostitelů na virtuálním počítači s Linuxem
V terminálu SSH upravte soubor/etc/hosts a aktualizovat IP adresu vašeho počítače a název hostitele.

```
sudo vi /etc/hosts
```

V souboru hostitele zadejte následující hodnotu:

```
127.0.0.1 contoso-ubuntu.contoso100.com contoso-ubuntu
```
"Contoso100.com" následuje název domény DNS vaší spravované domény. "contoso-ubuntu" je název hostitele virtuálního počítače se systémem Ubuntu, které se připojujete ke spravované doméně.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Instalace požadovaných balíčků na virtuálním počítači s Linuxem
Dále nainstalujte balíčky požadované pro připojení k doméně na virtuálním počítači. Proveďte následující kroky:

1.  V terminálu SSH zadejte následující příkaz stahovat seznamy balíčků z úložiště. Tento příkaz aktualizuje balíček seznamy, které chcete získat informace o nejnovější verze balíčků a jejich závislosti.

    ```
    sudo apt-get update
    ```

2. Zadejte pomocí následujícího příkazu nainstalujte požadované balíčky.
    ```
      sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
    ```

3. Při instalaci protokolu Kerberos objeví se obrazovka růžový. Instalace balíčku krb5 uživatele vyzve k zadání název sféry (na všechna velká písmena). Instalace zapíše [sféry] a [domain_realm] části /etc/krb5.conf.

    > [!TIP]
    > Pokud je název vaší spravované domény contoso100.com, zadejte CONTOSO100.COM jako sféru. Mějte na paměti, musí být zadán název sféry, velkými písmeny.
    >
    >


## <a name="configure-the-ntp-network-time-protocol-settings-on-the-linux-virtual-machine"></a>Konfigurace nastavení NTP (Network Time Protocol) na virtuálním počítači s Linuxem
Datum a čas vašeho virtuálního počítače se systémem Ubuntu musí synchronizovat s spravované domény. Přidáte název hostitele NTP vaší spravované domény v souboru /etc/ntp.conf.

```
sudo vi /etc/ntp.conf
```

V souboru ntp.conf zadejte následující hodnoty a soubor uložte:

```
server contoso100.com
```
"Contoso100.com" následuje název domény DNS vaší spravované domény.

Teď synchronizovat data a času se serverem NTP virtuálního počítače se systémem Ubuntu a poté spusťte službu NTP:

```
sudo systemctl stop ntp
sudo ntpdate contoso100.com
sudo systemctl start ntp
```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Připojení virtuálního počítače Linux ke spravované doméně
Teď, když na virtuálním počítači s Linuxem nainstalované požadované balíčky, dalším krokem je připojení virtuálního počítače do spravované domény.

1. Objevte spravované domény služby AAD Domain Services. V terminálu SSH zadejte následující příkaz:

    ```
    sudo realm discover CONTOSO100.COM
    ```

   > [!NOTE]
   > **Řešení potíží:** Pokud *zjišťování sféry* nemůže najít vaši spravovanou doménu:
     * Ujistěte se, že doménu je dostupný z virtuálního počítače (zkuste příkaz ping).
     * Zkontrolujte, že virtuální počítač skutečně byla nasazena do stejné virtuální síti, ve kterém je spravovaná doména k dispozici.
     * Zaškrtněte, pokud chcete zobrazit, když jste aktualizovali nastavení serveru DNS pro virtuální síť tak, aby odkazoval na řadiče domény spravované domény.
   >

2. Inicializace protokolu Kerberos. V terminálu SSH zadejte následující příkaz:

    > [!TIP]
    > * Ujistěte se, že zadáte uživatele, který patří do skupiny "Správci AAD DC".
    > * Zadejte název domény velkými písmeny, else kinit selže.
    >

    ```
    kinit bob@CONTOSO100.COM
    ```

3. Připojte počítač k doméně. V terminálu SSH zadejte následující příkaz:

    > [!TIP]
    > Pomocí stejného uživatelského účtu, který jste zadali v předchozím kroku (kinit).
    >

    ```
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM' --install=/
    ```

Měli byste získat zprávu ("úspěšně zaregistrované počítače ve sféře") Pokud je počítač úspěšně připojen ke spravované doméně.


## <a name="update-the-sssd-configuration-and-restart-the-service"></a>Aktualizujte konfiguraci SSSD a restartovat službu
1. V terminálu SSH zadejte následující příkaz. Otevřete soubor sssd.conf a proveďte následující změnu
    ```
    sudo vi /etc/sssd/sssd.conf
    ```

2. Odkomentujte řádek **use_fully_qualified_names = True** a soubor uložte.
    ```
    # use_fully_qualified_names = True
    ```

3. Restartujte službu SSSD.
    ```
    sudo service sssd restart
    ```


## <a name="configure-automatic-home-directory-creation"></a>Konfigurace vytváření automatické domovského adresáře
Pokud chcete povolit automatické vytváření domovský adresář po přihlášení uživatele, zadejte následující příkazy v PuTTY terminálu:
```
sudo vi /etc/pam.d/common-session
```

Přidejte následující řádek v tomto souboru pod řádkem "volitelné pam_sss.so relace" a uložte ho:
```
session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
```


## <a name="verify-domain-join"></a>Ověřte připojení k doméně
Ověřte, zda je počítač byl úspěšně připojen ke spravované doméně. Připojení k doméně virtuálního počítače s Ubuntu pomocí jiné připojení SSH. Použít účet uživatele domény a pak zaškrtněte, pokud chcete zobrazit, pokud je uživatelský účet správně přeložit.

1. Vaše SSH terminálu zadejte následující příkaz pro připojení k doméně připojený virtuální počítač s Ubuntu pomocí protokolu SSH. Použít účet domény, který patří ke spravované doméně (například "bob@CONTOSO100.COM" v tomto případě.)
    ```
    ssh -l bob@CONTOSO100.COM contoso-ubuntu.contoso100.com
    ```

2. V terminálu SSH zadejte následující příkaz zobrazíte, pokud domovský adresář byl správně inicializován.
    ```
    pwd
    ```

3. V terminálu SSH zadejte následující příkaz, pokud chcete zobrazit, pokud členství ve skupinách jsou řešeny správně.
    ```
    id
    ```


## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Udělení oprávnění sudo skupiny "Správci AAD DC.
Můžete poskytnout členům skupiny pro správu oprávnění skupiny "Správci AAD DC' na virtuálním počítači se systémem Ubuntu. Je umístěn na /etc/sudoers soubor sudo. Členové skupiny AD přidá sudoers mohou provádět sudo.

1. V terminálu vaše SSH Ujistěte se, že jste přihlášeni oprávnění superuživatele. Můžete použít účet místního správce, který jste zadali při vytváření virtuálního počítače. Spusťte následující příkaz:
    ```
    sudo vi /etc/sudoers
    ```

2. Přidejte následující položku do souboru /etc/sudoers a uložte ho:
    ```
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

3. Můžete teď přihlásit jako člen skupiny "Správci AAD DC" a by měl mít oprávnění správce na virtuálním počítači.


## <a name="troubleshooting-domain-join"></a>Řešení potíží s připojení k doméně
Odkazovat [připojení k doméně Poradce při potížích s](active-directory-ds-admin-guide-join-windows-vm-portal.md#troubleshoot-joining-a-domain) článku.


## <a name="related-content"></a>Související obsah
* [Azure AD Domain Services – Příručka Začínáme](active-directory-ds-getting-started.md)
* [Připojte se k virtuálnímu počítači s Windows serverem do spravované domény služby Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Jak se přihlásit k virtuálnímu počítači s Linuxem](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
