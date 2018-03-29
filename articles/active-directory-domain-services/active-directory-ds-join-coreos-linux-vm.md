---
title: 'Azure Active Directory Domain Services: Připojení virtuálního počítače s CoreOS Linux k spravované doméně | Microsoft Docs'
description: Virtuální počítač s Linuxem CoreOS připojení k Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: maheshu
ms.openlocfilehash: 6c7290db73645a4e1e745142f266aabc6df735df
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="join-a-coreos-linux-virtual-machine-to-a-managed-domain"></a>Připojit virtuální počítač jádro operačního systému Linux k spravované doméně
Tento článek ukazuje, jak připojit virtuální počítač s Linuxem CoreOS v Azure k spravované doméně služby Azure AD Domain Services.

## <a name="before-you-begin"></a>Než začnete
Chcete-li provést úkoly vypsané v tomto článku, je třeba:
1. Platná **předplatné**.
2. **Adresář Azure AD** – buď synchronizovány s místní adresář nebo výhradně cloudový adresář.
3. **Azure AD Domain Services** musí být povolen pro adresář Azure AD. Pokud jste tak dosud neučinili, postupujte podle všechny úkoly popsané v [příručce Začínáme](active-directory-ds-getting-started.md).
4. Ujistěte se, že jste nakonfigurovali IP adresy spravované doméně jako servery DNS pro virtuální síť. Další informace najdete v tématu [postup aktualizace nastavení DNS pro virtuální síť Azure](active-directory-ds-getting-started-dns.md)
5. Dokončete kroky potřebné k [synchronizovat hesla k spravované doméně služby Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-a-coreos-linux-virtual-machine"></a>Zřízení virtuálního počítače s CoreOS Linux
Zřízení virtuálního počítače s CoreOS v Azure, pomocí kteréhokoli z následujících metod:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Tento článek používá **jádro operačního systému Linux (stabilní)** bitovou kopii virtuálního počítače v Azure.

> [!IMPORTANT]
> * Nasaďte virtuální počítač do **stejnou virtuální síť, ve kterém jste povolili službu Azure AD Domain Services**.
> * Vyberte **jiné podsíti** než ten, ve kterém jste povolili službu Azure AD Domain Services.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Vzdáleně připojit k nově zřízeného virtuálního počítače systému Linux
Virtuální počítač CoreOS zřízená v Azure. Dalším krokem je vzdáleně připojit k virtuálnímu počítači pomocí účtu místního správce, který je vytvořen při zřizování virtuálního počítače.

Postupujte podle pokynů v článku [přihlášení do virtuálního počítače se systémem Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Konfigurace souboru hostitelů na virtuální počítač Linux
V terminálu SSH upravte soubor/etc/hosts a aktualizovat IP adresa počítače a název hostitele.

```
sudo vi /etc/hosts
```

V souboru hostitele zadejte následující hodnotu:

```
127.0.0.1 contoso-coreos.contoso100.com contoso-coreos
```
"Contoso100.com" následuje název domény DNS vaší spravované domény. "contoso-coreos, je název hostitele virtuálního počítače CoreOS, ke které se připojujete k spravované doméně.


## <a name="configure-the-sssd-service-on-the-linux-virtual-machine"></a>Konfigurovat službu SSSD na virtuální počítač Linux
Potom aktualizujte v konfiguračním souboru SSSD ("/ etc/sssd/sssd.conf') tak, aby odpovídala následující ukázka:

 ```
 [sssd]
 config_file_version = 2
 services = nss, pam
 domains = CONTOSO100.COM

 [domain/CONTOSO100.COM]
 id_provider = ad
 auth_provider = ad
 chpass_provider = ad

 ldap_uri = ldap://contoso100.com
 ldap_search_base = dc=contoso100,dc=com
 ldap_schema = rfc2307bis
 ldap_sasl_mech = GSSAPI
 ldap_user_object_class = user
 ldap_group_object_class = group
 ldap_user_home_directory = unixHomeDirectory
 ldap_user_principal = userPrincipalName
 ldap_account_expire_policy = ad
 ldap_force_upper_case_realm = true
 fallback_homedir = /home/%d/%u

 krb5_server = contoso100.com
 krb5_realm = CONTOSO100.COM
 ```
Nahraďte "CONTOSO100. COM "s název domény DNS vaší spravované domény. Zajistěte, aby že v případě kapitálové v souboru conf zadáte název domény.


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Připojit virtuální počítač Linux k spravované doméně
Teď, když požadované balíčky jsou nainstalovány na virtuální počítač Linux, dalším úkolem je připojení virtuálního počítače k spravované doméně.

```
sudo adcli join -D CONTOSO100.COM -U bob@CONTOSO100.COM -K /etc/krb5.keytab -H contoso-coreos.contoso100.com -N coreos
```


> [!NOTE]
> **Řešení potíží:** Pokud *adcli* se nepodařilo najít vaší spravované domény:
  * Ujistěte se, že doména je dostupný z virtuálního počítače (zkuste ping).
  * Zkontrolujte, že virtuální počítač skutečně byla nasazena do stejné virtuální síti, ve kterém je k dispozici spravované domény.
  * Zkontrolujte, zda jste aktualizovali nastavení serveru DNS virtuální sítě tak, aby odkazoval na řadičích domény, spravované domény.
>

Spusťte službu SSSD. V terminálu SSH zadejte následující příkaz:
  ```
  sudo systemctl start sssd.service
  ```


## <a name="verify-domain-join"></a>Ověřte připojení k doméně
Ověřte, zda je počítač byl úspěšně připojen k spravované doméně. Připojení k doméně počítače s CoreOS pomocí jiné připojení SSH. Použijte účet uživatele domény a potom zkontrolujte, zda uživatelský účet vyřešen správně.

1. Vaše SSH terminálu zadejte následující příkaz pro připojení k doméně připojený CoreOS virtuálního počítače pomocí protokolu SSH. Použít účet domény, který patří k spravované doméně (například "bob@CONTOSO100.COM' v takovém případě.)
    ```
    ssh -l bob@CONTOSO100.COM contoso-coreos.contoso100.com
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
