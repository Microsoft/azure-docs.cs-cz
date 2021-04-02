---
title: Připojte virtuální počítač CentOS k Azure AD Domain Services | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat virtuální počítač s CentOS Linux a připojit ho k Azure Active Directory Domain Services spravované doméně.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.author: justinha
ms.openlocfilehash: 76fc11384b55337f581a74239d4a40b90b284f32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619653"
---
# <a name="join-a-centos-linux-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Připojení virtuálního počítače se systémem CentOS Linux k Azure Active Directory Domain Services spravované doméně

Pokud chcete umožnit uživatelům přihlašovat se k virtuálním počítačům v Azure pomocí jediné sady přihlašovacích údajů, můžete připojit virtuální počítače k spravované doméně Azure Active Directory Domain Services (Azure služba AD DS). Když připojíte virtuální počítač k spravované doméně Azure služba AD DS, můžete k přihlášení a správě serverů použít uživatelské účty a přihlašovací údaje z domény. Členství ve skupinách ze spravované domény se používá také k řízení přístupu k souborům nebo službám na virtuálním počítači.

V tomto článku se dozvíte, jak připojit virtuální počítač s CentOS Linux ke spravované doméně.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Musíte mít aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
* Ve vašem tenantovi Azure AD je povolená a nakonfigurovaná spravovaná doména Azure Active Directory Domain Services.
    * V případě potřeby se v prvním kurzu [vytvoří a nakonfiguruje Azure Active Directory Domain Services spravovaná doména][create-azure-ad-ds-instance].
* Uživatelský účet, který je součástí spravované domény.

## <a name="create-and-connect-to-a-centos-linux-vm"></a>Vytvoření virtuálního počítače se systémem CentOS Linux a připojení k němu

Pokud máte v Azure existující virtuální počítač s CentOS Linux, připojte se k němu pomocí SSH a potom pokračujte dalším krokem a [Spusťte konfiguraci virtuálního počítače](#configure-the-hosts-file).

Pokud potřebujete vytvořit virtuální počítač s CentOS Linux nebo chcete vytvořit testovací virtuální počítač pro použití s tímto článkem, můžete použít jednu z následujících metod:

* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Když vytváříte virtuální počítač, věnujte pozornost nastavení virtuální sítě a ujistěte se, že virtuální počítač může komunikovat se spravovanou doménou:

* Nasaďte virtuální počítač do stejné nebo partnerské virtuální sítě, ve které jste povolili Azure AD Domain Services.
* Virtuální počítač nasaďte do jiné podsítě, než je vaše spravovaná doména.

Až se virtuální počítač nasadí, připojte se k virtuálnímu počítači pomocí SSH podle pokynů.

## <a name="configure-the-hosts-file"></a>Konfigurace souboru hostitelů

Abyste se ujistili, že je název hostitele virtuálního počítače správně nakonfigurovaný pro spravovanou doménu, upravte soubor */etc/hosts* a nastavte název hostitele:

```console
sudo vi /etc/hosts
```

V souboru *hosts* aktualizujte adresu *localhost* . V následujícím příkladu:

* *aaddscontoso.com* je název domény DNS vaší spravované domény.
* *CentOS* je název hostitele vašeho virtuálního počítače s CentOS, ke kterému se připojujete do spravované domény.

Aktualizujte tyto názvy vlastními hodnotami:

```console
127.0.0.1 centos.aaddscontoso.com centos
```

Po dokončení uložte a zavřete soubor *hosts* pomocí `:wq` příkazu editoru.

## <a name="install-required-packages"></a>Instalace požadovaných balíčků

Virtuální počítač potřebuje nějaké další balíčky, aby se připojil k virtuálnímu počítači do spravované domény. Pokud chcete tyto balíčky nainstalovat a nakonfigurovat, aktualizujte a nainstalujte nástroje pro připojení k doméně pomocí těchto kroků `yum` :

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

## <a name="join-vm-to-the-managed-domain"></a>Připojit virtuální počítač ke spravované doméně

Teď, když jsou na virtuálním počítači nainstalované požadované balíčky, připojte virtuální počítač ke spravované doméně.

1. Pomocí `realm discover` příkazu můžete zjistit spravovanou doménu. Následující příklad zjistí sféru *AADDSCONTOSO.com*. Zadejte název vlastní spravované domény, který bude obsahovat všechna velká písmena:

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   Pokud `realm discover` příkaz nemůže najít spravovanou doménu, přečtěte si následující postup řešení potíží:

    * Ujistěte se, že je doména dosažitelná z virtuálního počítače. Zkuste `ping aaddscontoso.com` zjistit, jestli je vrácená kladná odpověď.
    * Ověřte, že je virtuální počítač nasazený do stejné nebo partnerské virtuální sítě, ve které je dostupná spravovaná doména.
    * Ověřte, že nastavení serveru DNS pro virtuální síť se aktualizovala tak, aby odkazovala na řadiče domény spravované domény.

1. Nyní pomocí příkazu inicializujte Kerberos `kinit` . Zadejte uživatele, který je součástí spravované domény. V případě potřeby [přidejte uživatelský účet do skupiny v Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Název spravované domény se znovu musí zadat jenom velkými PÍSMENy. V následujícím příkladu se `contosoadmin@aaddscontoso.com` k inicializaci protokolu Kerberos používá účet s názvem. Zadejte vlastní uživatelský účet, který je součástí spravované domény:

    ```console
    kinit contosoadmin@AADDSCONTOSO.COM
    ```

1. Nakonec připojte virtuální počítač ke spravované doméně pomocí `realm join` příkazu. Použijte stejný uživatelský účet, který je součástí spravované domény, kterou jste zadali v předchozím `kinit` příkazu, například `contosoadmin@AADDSCONTOSO.COM` :

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM'
    ```

Připojení virtuálního počítače ke spravované doméně chvíli trvá. Následující příklad výstupu ukazuje, že se virtuální počítač úspěšně připojil ke spravované doméně:

```output
Successfully enrolled machine in realm
```

Pokud váš virtuální počítač nemůže úspěšně dokončit proces připojení k doméně, ujistěte se, že skupina zabezpečení sítě virtuálního počítače umožňuje odchozí přenosy protokolu Kerberos na portu TCP + UDP 464 do podsítě virtuální sítě pro spravovanou doménu.

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

Pokud chcete členům skupiny *AAD DC Administrators* udělit na virtuálním počítači CentOS oprávnění správce, přidejte položku do */etc/sudoers*. Po přidání můžou členové skupiny *Správci AAD DC* použít `sudo` příkaz na virtuálním počítači s CentOS.

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

1. Vytvořte nové připojení SSH z konzoly. Pomocí příkazu použijte doménový účet, který patří do spravované domény `ssh -l` , `contosoadmin@aaddscontoso.com` a pak zadejte adresu vašeho virtuálního počítače, třeba *CentOS.aaddscontoso.com*. Pokud používáte Azure Cloud Shell, použijte veřejnou IP adresu virtuálního počítače místo interního názvu DNS.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com centos.aaddscontoso.com
    ```

1. Po úspěšném připojení k virtuálnímu počítači ověřte, zda byl domovský adresář správně inicializován:

    ```console
    pwd
    ```

    Měli byste být v adresáři */Home* s vlastním adresářem, který odpovídá uživatelskému účtu.

1. Nyní ověřte, zda jsou členství ve skupině správně řešena:

    ```console
    id
    ```

    Měli byste vidět členství ve skupinách ze spravované domény.

1. Pokud jste se k virtuálnímu počítači přihlásili jako člen skupiny *AAD DC Administrators* , ověřte, že můžete správně použít `sudo` příkaz:

    ```console
    sudo yum update
    ```

## <a name="next-steps"></a>Další kroky

Pokud máte problémy s připojením k virtuálnímu počítači ke spravované doméně nebo přihlášením pomocí účtu domény, přečtěte si téma [řešení potíží s připojením k doméně](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
