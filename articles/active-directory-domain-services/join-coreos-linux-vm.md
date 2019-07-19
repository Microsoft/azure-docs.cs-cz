---
title: 'Azure Active Directory Domain Services: Připojení k virtuálnímu počítači s CoreOS Linux | Microsoft Docs'
description: Připojte se k virtuálnímu počítači s CoreOS Linux a Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 78a6c5262cd6668712beac1e041fa4f25c05a724
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234062"
---
# <a name="join-a-coreos-linux-virtual-machine-to-a-managed-domain"></a>Připojení virtuálního počítače se systémem CoreOS Linux ke spravované doméně
V tomto článku se dozvíte, jak připojit virtuální počítač s CoreOS Linux v Azure k spravované doméně Azure AD Domain Services.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Před zahájením
K provedení úkolů uvedených v tomto článku budete potřebovat:
1. Platné **předplatné Azure**.
2. **Adresář služby Azure AD** – buď synchronizovaný s místním adresářem, nebo s adresářem jenom pro Cloud.
3. **Azure AD Domain Services** musí být povolený pro adresář služby Azure AD. Pokud jste to ještě neudělali, postupujte podle všech úkolů popsaných v [průvodci Začínáme](create-instance.md).
4. Ujistěte se, že jste nakonfigurovali IP adresy spravované domény jako servery DNS pro virtuální síť. Další informace najdete v tématu [Postup aktualizace nastavení DNS pro virtuální síť Azure](active-directory-ds-getting-started-dns.md) .
5. Dokončete kroky potřebné k [synchronizaci hesel s Azure AD Domain Services spravované doméně](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-a-coreos-linux-virtual-machine"></a>Zřízení virtuálního počítače s CoreOS Linux
Vytvořte virtuální počítač s CoreOS v Azure pomocí kterékoli z následujících metod:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Tento článek používá bitovou kopii virtuálního počítače se **systémem CoreOS Linux (stabilní)** v Azure.

> [!IMPORTANT]
> * Nasaďte virtuální počítač do **stejné virtuální sítě, ve které jste povolili Azure AD Domain Services**.
> * Vyberte **jinou podsíť** , než kterou jste povolili Azure AD Domain Services.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Vzdálené připojení k nově zřízenému virtuálnímu počítači se systémem Linux
Virtuální počítač CoreOS se zřídil v Azure. Dalším úkolem je vzdálené připojení k virtuálnímu počítači pomocí účtu místního správce vytvořeného při zřizování virtuálního počítače.

Postupujte podle pokynů v článku [jak se přihlásit k virtuálnímu počítači se systémem Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Konfigurace souboru hostitelů na virtuálním počítači se systémem Linux
V terminálu SSH upravte soubor/etc/hosts a aktualizujte IP adresu počítače a název hostitele.

```console
sudo vi /etc/hosts
```

V souboru Hosts zadejte následující hodnotu:

```console
127.0.0.1 contoso-coreos.contoso100.com contoso-coreos
```

Tady je "contoso100.com" název domény DNS vaší spravované domény. contoso-CoreOS je název hostitele virtuálního počítače s CoreOS, ke kterému se připojujete, ke spravované doméně.


## <a name="configure-the-sssd-service-on-the-linux-virtual-machine"></a>Konfigurace služby SSSD na virtuálním počítači se systémem Linux
Dále aktualizujte konfigurační soubor SSSD v ('/etc/SSSD/SSSD.conf ') tak, aby odpovídal následující ukázce:

```console
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

Nahraďte ' CONTOSO100 '. COM ' s názvem domény DNS vaší spravované domény. Nezapomeňte zadat název domény v souboru conf velkými písmeny.


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Připojit se k virtuálnímu počítači Linux ke spravované doméně
Teď, když jsou na virtuálním počítači se systémem Linux nainstalované požadované balíčky, je dalším úkolem připojit se k virtuálnímu počítači do spravované domény.

```console
sudo adcli join -D CONTOSO100.COM -U bob@CONTOSO100.COM -K /etc/krb5.keytab -H contoso-coreos.contoso100.com -N coreos
```


> [!NOTE]
> **Při** Pokud *adcli* nemůže najít spravovanou doménu:
>   * Zajistěte, aby byla doména dosažitelná z virtuálního počítače (zkuste použít test pomocí testu).
>   * Ověřte, zda je virtuální počítač skutečně nasazený do stejné virtuální sítě, ve které je dostupná spravovaná doména.
>   * Zkontrolujte, jestli jste aktualizovali nastavení serveru DNS pro virtuální síť tak, aby odkazovala na řadiče domény spravované domény.

Spusťte službu SSSD. Do terminálu SSH zadejte následující příkaz:
  
```console
sudo systemctl start sssd.service
```


## <a name="verify-domain-join"></a>Ověření připojení k doméně
Ověřte, jestli se počítač úspěšně připojil ke spravované doméně. Připojte se k virtuálnímu počítači CoreOS připojenému k doméně pomocí jiného připojení SSH. Použijte účet uživatele domény a potom zkontrolujte, jestli je uživatelský účet správně vyřešený.

1. Do terminálu SSH zadejte následující příkaz, který se připojí k doméně připojené k doméně CoreOS pomocí SSH. Použijte doménový účet, který patří do spravované domény (napříkladbob@CONTOSO100.COMv tomto případě).
    
    ```console
    ssh -l bob@CONTOSO100.COM contoso-coreos.contoso100.com
    ```

2. V terminálu SSH zadejte následující příkaz, který zjistí, zda byl domovský adresář správně inicializován.
    
    ```console
    pwd
    ```

3. V terminálu SSH zadejte následující příkaz, abyste viděli, jestli se členství ve skupině správně řeší.
   
    ```console
    id
    ```


## <a name="troubleshooting-domain-join"></a>Řešení potíží s připojením k doméně
Informace najdete v článku [věnovaném řešení potíží s připojením k doméně](join-windows-vm.md#troubleshoot-joining-a-domain) .

## <a name="related-content"></a>Související obsah
* [Azure AD Domain Services – Průvodce Začínáme](create-instance.md)
* [Připojení virtuálního počítače s Windows serverem k spravované doméně Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Jak se přihlásit k virtuálnímu počítači se systémem Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
