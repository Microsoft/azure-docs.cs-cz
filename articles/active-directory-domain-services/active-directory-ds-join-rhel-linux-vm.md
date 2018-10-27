---
title: 'Azure Active Directory Domain Services: Připojit virtuální počítač s RHEL ke spravované doméně | Dokumentace Microsoftu'
description: Připojení virtuálního počítače s Red Hat Enterprise Linux do Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: d76ae997-2279-46dd-bfc5-c0ee29718096
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: bfad3b8c8347b885fd713d30c620d6c86377fab3
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50155222"
---
# <a name="join-a-red-hat-enterprise-linux-7-virtual-machine-to-a-managed-domain"></a>Připojení virtuálního počítače Red Hat Enterprise Linux 7 ke spravované doméně
V tomto článku se dozvíte, jak propojit virtuální počítače s Red Hat Enterprise Linux (RHEL) 7 ke spravované doméně Azure AD Domain Services.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Než začnete
K provádění úkolů uvedených v tomto článku, budete potřebovat:  
1. Platný **předplatného Azure**.
2. **Adresář Azure AD** – buď synchronizaci s místním adresářem nebo výhradně cloudový adresář.
3. **Azure AD Domain Services** musí být povolené pro adresář Azure AD. Pokud jste neudělali, postupujte podle všechny úkoly popsané v [příručce Začínáme](active-directory-ds-getting-started.md).
4. Ujistěte se, že jste nakonfigurovali IP adres spravované domény jako servery DNS pro virtuální síť. Další informace najdete v tématu [postup aktualizace nastavení DNS pro virtuální síť Azure](active-directory-ds-getting-started-dns.md)
5. Dokončete kroky potřebné k [synchronizace hesel do spravované domény služby Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-a-red-hat-enterprise-linux-virtual-machine"></a>Zřízení virtuálního počítače s Red Hat Enterprise Linux
Zřízení virtuálního počítače s RHEL 7 v Azure pomocí kteréhokoli z následujících metod:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Nasazení virtuálního počítače do **stejné virtuální síti, ve kterém jste povolili službu Azure AD Domain Services**.
> * Vyberte **jinou podsíť** než ten, ve kterém jste povolili službu Azure AD Domain Services.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Vzdálené připojení na nově zřízeného virtuálního počítač s Linuxem
Zřízení virtuálního počítače RHEL 7.2 v Azure. Další úlohou je vzdáleně připojit k virtuálnímu počítači pomocí účtu místního správce vytvořené při zřizování virtuálních počítačů.

Postupujte podle pokynů v článku [jak se přihlásit k virtuálnímu počítači s Linuxem](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Konfigurace souboru hostitelů na virtuálním počítači s Linuxem
V terminálu SSH upravte soubor/etc/hosts a aktualizovat IP adresu vašeho počítače a název hostitele.

```
sudo vi /etc/hosts
```

V souboru hostitele zadejte následující hodnotu:

```
127.0.0.1 contoso-rhel.contoso100.com contoso-rhel
```
"Contoso100.com" následuje název domény DNS vaší spravované domény. "contoso-rhel" je název hostitele virtuálního počítače RHEL, ke které se připojujete ke spravované doméně.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Instalace požadovaných balíčků na virtuálním počítači s Linuxem
Dále nainstalujte balíčky požadované pro připojení k doméně na virtuálním počítači. V terminálu SSH zadejte následující příkaz k instalaci požadovaných balíčků:

    ```
    sudo yum install realmd sssd krb5-workstation krb5-libs samba-common-tools
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
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM'
    ```

Měli byste získat zprávu ("úspěšně zaregistrované počítače ve sféře") Pokud je počítač úspěšně připojen ke spravované doméně.


## <a name="verify-domain-join"></a>Ověřte připojení k doméně
Ověřte, zda je počítač byl úspěšně připojen ke spravované doméně. Připojení k doméně pomocí jiné připojení SSH virtuální počítač s RHEL. Použít účet uživatele domény a pak zaškrtněte, pokud chcete zobrazit, pokud je uživatelský účet správně přeložit.

1. Vaše SSH terminálu zadejte následující příkaz pro připojení k doméně připojený RHEL virtuálnímu počítači pomocí SSH. Použít účet domény, který patří ke spravované doméně (například "bob@CONTOSO100.COM" v tomto případě.)
    ```
    ssh -l bob@CONTOSO100.COM contoso-rhel.contoso100.com
    ```

2. V terminálu SSH zadejte následující příkaz zobrazíte, pokud domovský adresář byl správně inicializován.
    ```
    pwd
    ```

3. V terminálu SSH zadejte následující příkaz, pokud chcete zobrazit, pokud členství ve skupinách jsou řešeny správně.
    ```
    id
    ```


## <a name="troubleshooting-domain-join"></a>Řešení potíží s připojení k doméně
Odkazovat [připojení k doméně Poradce při potížích s](active-directory-ds-admin-guide-join-windows-vm-portal.md#troubleshoot-joining-a-domain) článku.

## <a name="related-content"></a>Související obsah
* [Azure AD Domain Services – Příručka Začínáme](active-directory-ds-getting-started.md)
* [Připojte se k virtuálnímu počítači s Windows serverem do spravované domény služby Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Jak se přihlásit k virtuálnímu počítači s Linuxem](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Instalace protokolu Kerberos](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)
* [Red Hat Enterprise Linux 7 – příručka integrace Windows](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)
