---
title: 'Azure Active Directory Domain Services: Připojení k virtuálnímu počítači CentOS ke spravované doméně | Microsoft Docs'
description: Připojit virtuální počítač systému CentOS Linux k Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: maheshu
ms.openlocfilehash: 4bf0cfa1fdd1b48233fd4e7bdec0eb72e897b738
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34587054"
---
# <a name="join-a-centos-linux-virtual-machine-to-a-managed-domain"></a>Připojit virtuální počítač systému CentOS Linux k spravované doméně
Tento článek ukazuje, jak propojit systému CentOS Linux virtuálního počítače v Azure k spravované doméně služby Azure AD Domain Services.

## <a name="before-you-begin"></a>Než začnete
Chcete-li provést úkoly vypsané v tomto článku, je třeba:
1. Platná **předplatné**.
2. **Adresář Azure AD** – buď synchronizovány s místní adresář nebo výhradně cloudový adresář.
3. **Azure AD Domain Services** musí být povolen pro adresář Azure AD. Pokud jste tak dosud neučinili, postupujte podle všechny úkoly popsané v [příručce Začínáme](active-directory-ds-getting-started.md).
4. Ujistěte se, že jste nakonfigurovali IP adresy spravované doméně jako servery DNS pro virtuální síť. Další informace najdete v tématu [postup aktualizace nastavení DNS pro virtuální síť Azure](active-directory-ds-getting-started-dns.md)
5. Dokončete kroky potřebné k [synchronizovat hesla k spravované doméně služby Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-a-centos-linux-virtual-machine"></a>Zřízení virtuálního počítače systému CentOS Linux
Zřízení virtuálního počítače s CentOS v Azure, pomocí kteréhokoli z následujících metod:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Nasaďte virtuální počítač do **stejnou virtuální síť, ve kterém jste povolili službu Azure AD Domain Services**.
> * Vyberte **jiné podsíti** než ten, ve kterém jste povolili službu Azure AD Domain Services.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Vzdáleně připojit k nově zřízeného virtuálního počítače systému Linux
Virtuální počítač CentOS zřízená v Azure. Dalším krokem je vzdáleně připojit k virtuálnímu počítači pomocí účtu místního správce, který je vytvořen při zřizování virtuálního počítače.

Postupujte podle pokynů v článku [přihlášení do virtuálního počítače se systémem Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Konfigurace souboru hostitelů na virtuální počítač Linux
V terminálu SSH upravte soubor/etc/hosts a aktualizovat IP adresa počítače a název hostitele.

```
sudo vi /etc/hosts
```

V souboru hostitele zadejte následující hodnotu:

```
127.0.0.1 contoso-centos.contoso100.com contoso-centos
```
"Contoso100.com" následuje název domény DNS vaší spravované domény. "contoso-centos, je název hostitele virtuálního počítače CentOS, ke které se připojujete k spravované doméně.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Nainstalujte požadované balíčky Linux virtuálního počítače
V dalším kroku instalovat balíčky požadované pro připojení k doméně na virtuálním počítači. V terminálu SSH zadejte následující příkaz pro instalaci požadovaných balíčků:

    ```
    sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
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
    > * Zadejte uživatele, který patří do skupiny 'AAD řadič domény správci'.
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
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM'
    ```

Měli byste obdržet zprávu ("úspěšně zaregistrované počítače ve sféře") Pokud je počítač úspěšně připojený k spravované doméně.


## <a name="verify-domain-join"></a>Ověřte připojení k doméně
Ověřte, zda je počítač byl úspěšně připojen k spravované doméně. Připojte k doméně CentOS virtuálního počítače pomocí jiné připojení SSH. Použijte účet uživatele domény a potom zkontrolujte, zda uživatelský účet vyřešen správně.

1. Vaše SSH terminálu zadejte následující příkaz pro připojení k doméně připojený CentOS virtuálního počítače pomocí protokolu SSH. Použít účet domény, který patří k spravované doméně (například "bob@CONTOSO100.COM' v takovém případě.)
    ```
    ssh -l bob@CONTOSO100.COM contoso-centos.contoso100.com
    ```

2. V terminálu SSH zadejte následující příkaz, pokud chcete zobrazit, pokud byla správně inicializována domovský adresář.
    ```
    pwd
    ```

3. V terminálu SSH pomocí následujícího příkazu zobrazíte, pokud členství ve skupinách jsou řešeny správně.
    ```
    id
    ```


## <a name="troubleshooting-domain-join"></a>Řešení potíží s připojení k doméně
Odkazovat [připojení k doméně Poradce při potížích s](active-directory-ds-admin-guide-join-windows-vm-portal.md#troubleshoot-joining-a-domain) článku.

## <a name="related-content"></a>Související obsah
* [Azure AD Domain Services – Příručka Začínáme](active-directory-ds-getting-started.md)
* [Připojení virtuálního počítače s Windows serverem k spravované doméně služby Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Jak se přihlásit do virtuálního počítače se systémem Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Instalaci protokolu Kerberos](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)
* [Red Hat Enterprise Linux 7 – Příručka pro integraci systému Windows](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)
