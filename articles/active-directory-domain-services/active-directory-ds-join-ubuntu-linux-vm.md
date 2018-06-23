---
title: 'Azure Active Directory Domain Services: Připojení virtuálního počítače s Ubuntu ke spravované doméně | Microsoft Docs'
description: Připojení virtuálního počítače Ubuntu Linux do Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: d9f4dc0883ced599dd13d0c5d52ff865e03b73ed
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36332907"
---
# <a name="join-an-ubuntu-virtual-machine-in-azure-to-a-managed-domain"></a>Připojení virtuálního počítače Ubuntu v Azure ke spravované doméně
Tento článek ukazuje, jak propojit virtuálního počítače Ubuntu Linux k spravované doméně služby Azure AD Domain Services.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Než začnete
Chcete-li provést úkoly vypsané v tomto článku, je třeba:  
1. Platná **předplatné**.
2. **Adresář Azure AD** – buď synchronizovány s místní adresář nebo výhradně cloudový adresář.
3. **Azure AD Domain Services** musí být povolen pro adresář Azure AD. Pokud jste tak dosud neučinili, postupujte podle všechny úkoly popsané v [příručce Začínáme](active-directory-ds-getting-started.md).
4. Ujistěte se, že jste nakonfigurovali IP adresy spravované doméně jako servery DNS pro virtuální síť. Další informace najdete v tématu [postup aktualizace nastavení DNS pro virtuální síť Azure](active-directory-ds-getting-started-dns.md)
5. Dokončete kroky potřebné k [synchronizovat hesla k spravované doméně služby Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-an-ubuntu-linux-virtual-machine"></a>Zřízení virtuálního počítače Ubuntu Linux
Zřízení virtuálního počítače Ubuntu Linux v Azure, pomocí kteréhokoli z následujících metod:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Nasaďte virtuální počítač do **stejnou virtuální síť, ve kterém jste povolili službu Azure AD Domain Services**.
> * Vyberte **jiné podsíti** než ten, ve kterém jste povolili službu Azure AD Domain Services.
>


## <a name="connect-remotely-to-the-ubuntu-linux-virtual-machine"></a>Vzdálené připojení k virtuálnímu počítači Ubuntu Linux
Virtuální počítač Ubuntu zřízená v Azure. Dalším krokem je vzdáleně připojit k virtuálnímu počítači pomocí účtu místního správce, který je vytvořen při zřizování virtuálního počítače.

Postupujte podle pokynů v článku [přihlášení do virtuálního počítače se systémem Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Konfigurace souboru hostitelů na virtuální počítač Linux
V terminálu SSH upravte soubor/etc/hosts a aktualizovat IP adresa počítače a název hostitele.

```
sudo vi /etc/hosts
```

V souboru hostitele zadejte následující hodnotu:

```
127.0.0.1 contoso-ubuntu.contoso100.com contoso-ubuntu
```
"Contoso100.com" následuje název domény DNS vaší spravované domény. "contoso-ubuntu, je název hostitele virtuálního počítače Ubuntu, ke které se připojujete k spravované doméně.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Nainstalujte požadované balíčky Linux virtuálního počítače
V dalším kroku instalovat balíčky požadované pro připojení k doméně na virtuálním počítači. Proveďte následující kroky:

1.  V terminálu SSH zadejte následující příkaz ke stažení balíčku seznamy z úložiště. Tento příkaz aktualizuje balíček zobrazí informace o nejnovější verze balíčků a jejich závislosti.

    ```
    sudo apt-get update
    ```

2. Zadejte následující příkaz pro instalaci požadovaných balíčků.
    ```
      sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
    ```

3. Při instalaci protokolu Kerberos najdete v části růžový obrazovky. Instalace balíčku krb5 uživatele vyzve k zadání pro název sféry (na všech velká písmena). Instalace zapíše [sféry] a [domain_realm] části /etc/krb5.conf.

    > [!TIP]
    > Pokud je název vaší spravované domény contoso100.com, zadejte CONTOSO100.COM jako sféru. Mějte na paměti, musí být zadán název sféry velkými písmeny.
    >
    >


## <a name="configure-the-ntp-network-time-protocol-settings-on-the-linux-virtual-machine"></a>Nakonfigurujte nastavení NTP (Network Time Protocol) na virtuální počítač Linux
Datum a čas vašeho virtuálního počítače s Ubuntu musí synchronizovat s spravované domény. Přidejte název vaší spravované domény NTP hostitele v souboru /etc/ntp.conf.

```
sudo vi /etc/ntp.conf
```

V souboru ntp.conf zadejte následující hodnotu a uložte soubor:

```
server contoso100.com
```
"Contoso100.com" následuje název domény DNS vaší spravované domény.

Teď synchronizovat data a času s serveru NTP Ubuntu Virtuálního počítače a potom spusťte službu NTP:

```
sudo systemctl stop ntp
sudo ntpdate contoso100.com
sudo systemctl start ntp
```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Připojit virtuální počítač Linux k spravované doméně
Teď, když požadované balíčky jsou nainstalovány na virtuální počítač Linux, dalším úkolem je připojení virtuálního počítače k spravované doméně.

1. Zjistit spravované doméně služby AAD Domain Services. V terminálu SSH zadejte následující příkaz:

    ```
    sudo realm discover CONTOSO100.COM
    ```

   > [!NOTE]
   > **Řešení potíží:** Pokud *zjišťování sféry* se nepodařilo najít vaší spravované domény:
     * Ujistěte se, že doména je dostupný z virtuálního počítače (zkuste ping).
     * Zkontrolujte, že virtuální počítač skutečně byla nasazena do stejné virtuální síti, ve kterém je k dispozici spravované domény.
     * Zkontrolujte, zda jste aktualizovali nastavení serveru DNS virtuální sítě tak, aby odkazoval na řadičích domény, spravované domény.
   >

2. Inicializace protokolu Kerberos. V terminálu SSH zadejte následující příkaz:

    > [!TIP]
    > * Ujistěte se, že zadáváte uživatel, který patří do skupiny "Administrators AAD řadič domény.
    > * Zadejte název domény velkými písmeny, else kinit selže.
    >

    ```
    kinit bob@CONTOSO100.COM
    ```

3. Připojení počítače k doméně. V terminálu SSH zadejte následující příkaz:

    > [!TIP]
    > Pomocí stejného uživatelského účtu, který jste zadali v předchozím kroku (kinit).
    >

    ```
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM' --install=/
    ```

Měli byste obdržet zprávu ("úspěšně zaregistrované počítače ve sféře") Pokud je počítač úspěšně připojený k spravované doméně.


## <a name="update-the-sssd-configuration-and-restart-the-service"></a>Aktualizujte konfiguraci SSSD a restartujte službu
1. V terminálu SSH zadejte následující příkaz. Otevřete soubor sssd.conf a proveďte následující změny
    ```
    sudo vi /etc/sssd/sssd.conf
    ```

2. Komentář řádku **use_fully_qualified_names = True** a soubor uložte.
    ```
    # use_fully_qualified_names = True
    ```

3. Restartujte službu SSSD.
    ```
    sudo service sssd restart
    ```


## <a name="configure-automatic-home-directory-creation"></a>Konfigurace vytváření automatické domovský adresář
Pokud chcete povolit automatické vytváření domovský adresář po přihlášení uživatele, zadejte následující příkazy v PuTTY terminálu:
```
sudo vi /etc/pam.d/common-session
```

Přidejte následující řádek v tomto souboru pod čarou 'volitelné pam_sss.so relace a uložte jej:
```
session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
```


## <a name="verify-domain-join"></a>Ověřte připojení k doméně
Ověřte, zda je počítač byl úspěšně připojen k spravované doméně. Připojení k doméně virtuálního počítače s Ubuntu pomocí jiné připojení SSH. Použijte účet uživatele domény a potom zkontrolujte, zda uživatelský účet vyřešen správně.

1. Vaše SSH terminálu zadejte následující příkaz pro připojení k doméně připojený Ubuntu virtuálního počítače pomocí protokolu SSH. Použít účet domény, který patří k spravované doméně (například "bob@CONTOSO100.COM' v takovém případě.)
    ```
    ssh -l bob@CONTOSO100.COM contoso-ubuntu.contoso100.com
    ```

2. V terminálu SSH zadejte následující příkaz, pokud chcete zobrazit, pokud byla správně inicializována domovský adresář.
    ```
    pwd
    ```

3. V terminálu SSH pomocí následujícího příkazu zobrazíte, pokud členství ve skupinách jsou řešeny správně.
    ```
    id
    ```


## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Udělení oprávnění sudo skupiny "Správci AAD řadič domény.
Můžete udělit pro členy, správci AAD řadič domény, skupiny oprávnění ke správě Ubuntu virtuálního počítače. Je umístěn v /etc/sudoers soubor sudo. Sudo mohou provést členové skupin AD v sudoers přidána.

1. V terminálu vaší SSH zkontrolujte, zda že jste se přihlásili oprávnění superuživatele. Můžete vytvořit účet místního správce, který jste zadali při vytváření virtuálního počítače. Spusťte následující příkaz:
    ```
    sudo vi /etc/sudoers
    ```

2. Přidejte následující položku do souboru /etc/sudoers a uložte jej:
    ```
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

3. Můžete teď přihlásit jako člen skupiny 'Administrators AAD řadič domény a musí mít oprávnění správce ve virtuálním počítači.


## <a name="troubleshooting-domain-join"></a>Řešení potíží s připojení k doméně
Odkazovat [připojení k doméně Poradce při potížích s](active-directory-ds-admin-guide-join-windows-vm-portal.md#troubleshoot-joining-a-domain) článku.


## <a name="related-content"></a>Související obsah
* [Azure AD Domain Services – Příručka Začínáme](active-directory-ds-getting-started.md)
* [Připojení virtuálního počítače s Windows serverem k spravované doméně služby Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Jak se přihlásit do virtuálního počítače se systémem Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
