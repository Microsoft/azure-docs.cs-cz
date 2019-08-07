---
title: 'Azure Active Directory Domain Services: Připojení virtuálního počítače s CentOS ke spravované doméně | Microsoft Docs'
description: Připojte se k virtuálnímu počítači s CentOS Linux a Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 7b3159b6b963cf422442ee7c04253b8172e8f3e9
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2019
ms.locfileid: "68773135"
---
# <a name="join-a-centos-linux-virtual-machine-to-a-managed-domain"></a>Připojení virtuálního počítače se systémem CentOS Linux ke spravované doméně
V tomto článku se dozvíte, jak připojit virtuální počítač s CentOS Linux v Azure k spravované doméně Azure AD Domain Services.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Před zahájením
K provedení úkolů uvedených v tomto článku budete potřebovat:
1. Platné **předplatné Azure**.
2. **Adresář služby Azure AD** – buď synchronizovaný s místním adresářem, nebo s adresářem jenom pro Cloud.
3. **Azure AD Domain Services** musí být povolený pro adresář služby Azure AD. Pokud jste to ještě neudělali, postupujte podle všech úkolů popsaných v [průvodci Začínáme](create-instance.md).
4. Ujistěte se, že jste nakonfigurovali IP adresy spravované domény jako servery DNS pro virtuální síť. Další informace najdete v tématu [Postup aktualizace nastavení DNS pro virtuální síť Azure](active-directory-ds-getting-started-dns.md) .
5. Dokončete kroky potřebné k [synchronizaci hesel s Azure AD Domain Services spravované doméně](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-a-centos-linux-virtual-machine"></a>Zřízení virtuálního počítače s CentOS Linux
Vytvořte virtuální počítač s CentOS v Azure pomocí kterékoli z následujících metod:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Nasaďte virtuální počítač do **stejné virtuální sítě, ve které jste povolili Azure AD Domain Services**.
> * Vyberte **jinou podsíť** , než kterou jste povolili Azure AD Domain Services.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Vzdálené připojení k nově zřízenému virtuálnímu počítači se systémem Linux
Virtuální počítač CentOS se zřídil v Azure. Dalším úkolem je vzdálené připojení k virtuálnímu počítači pomocí účtu místního správce vytvořeného při zřizování virtuálního počítače.

Postupujte podle pokynů v článku [jak se přihlásit k virtuálnímu počítači se systémem Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Konfigurace souboru hostitelů na virtuálním počítači se systémem Linux
V terminálu SSH upravte soubor/etc/hosts a aktualizujte IP adresu počítače a název hostitele.

```console
sudo vi /etc/hosts
```

V souboru Hosts zadejte následující hodnotu:

```console
127.0.0.1 contoso-centos.contoso100.com contoso-centos
```

Tady je "contoso100.com" název domény DNS vaší spravované domény. contoso-CentOS je název hostitele virtuálního počítače s CentOS, ke kterému se připojujete, ke spravované doméně.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Nainstalovat požadované balíčky na virtuálním počítači se systémem Linux
Dále nainstalujte balíčky požadované pro připojení k doméně na virtuálním počítači. Do terminálu SSH zadejte následující příkaz, který nainstaluje požadované balíčky:

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Připojit se k virtuálnímu počítači Linux ke spravované doméně
Teď, když jsou na virtuálním počítači se systémem Linux nainstalované požadované balíčky, je dalším úkolem připojit se k virtuálnímu počítači do spravované domény.

1. Seznamte se se spravovanou doménou služby AAD Domain Services. Do terminálu SSH zadejte následující příkaz:

    ```console
    sudo realm discover CONTOSO100.COM
    ```

   > [!NOTE]
   > **Při** Pokud *Zjišťování sféry* nemůže najít spravovanou doménu:  
   >    * Zajistěte, aby byla doména dosažitelná z virtuálního počítače (zkuste použít test pomocí testu).  
   >    * Ověřte, zda je virtuální počítač skutečně nasazený do stejné virtuální sítě, ve které je dostupná spravovaná doména.
   >    * Zkontrolujte, jestli jste aktualizovali nastavení serveru DNS pro virtuální síť tak, aby odkazovala na řadiče domény spravované domény.  

2. Inicializujte Kerberos. Do terminálu SSH zadejte následující příkaz:

    > [!TIP]
    > * Zadejte uživatele, který patří do skupiny Správci AAD DC.
    > * Zadejte název domény velkými písmeny, jinak kinit neproběhne.

    ```console
    kinit bob@CONTOSO100.COM
    ```

3. Připojte počítač k doméně. Do terminálu SSH zadejte následující příkaz:

    > [!TIP]
    > Použijte stejný uživatelský účet, který jste zadali v předchozím kroku (' kinit ').
    >
    > Pokud se váš virtuální počítač nemůže připojit k doméně, ujistěte se, že skupina zabezpečení sítě virtuálního počítače umožňuje odchozí přenosy protokolu Kerberos na portu TCP + UDP 464 do podsítě virtuální sítě pro spravovanou doménu Azure služba AD DS.

    ```console
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM'
    ```

Pokud se počítač úspěšně připojí ke spravované doméně, měla by se zobrazit zpráva ("úspěšně zaregistrovaný počítač ve sféře").


## <a name="verify-domain-join"></a>Ověření připojení k doméně
Ověřte, jestli se počítač úspěšně připojil ke spravované doméně. Připojte se k virtuálnímu počítači CentOS připojenému k doméně pomocí jiného připojení SSH. Použijte účet uživatele domény a potom zkontrolujte, jestli je uživatelský účet správně vyřešený.

1. Do terminálu SSH zadejte následující příkaz, který se připojí k doméně připojené k doméně CentOS pomocí SSH. Použijte doménový účet, který patří do spravované domény (napříkladbob@CONTOSO100.COMv tomto případě).
    
    ```console
    ssh -l bob@CONTOSO100.COM contoso-centos.contoso100.com
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
* [Instalace protokolu Kerberos](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)
* [Red Hat Enterprise Linux 7 – Průvodce integrací Windows](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)
