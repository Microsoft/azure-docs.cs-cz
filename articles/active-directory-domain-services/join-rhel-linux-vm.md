---
title: Připojte virtuální počítač RHEL k Azure AD Domain Services | Microsoft Docs
description: Naučte se konfigurovat virtuální počítač Red Hat Enterprise Linux a připojit se k Azure AD Domain Services spravované doméně.
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.author: joflore
ms.openlocfilehash: ffde2317ea66a15936df75c4fe2455f627ea6e98
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91962118"
---
# <a name="join-a-red-hat-enterprise-linux-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Připojení virtuálního počítače s Red Hat Enterprise Linux k spravované doméně Azure Active Directory Domain Services

Pokud chcete umožnit uživatelům přihlašovat se k virtuálním počítačům v Azure pomocí jediné sady přihlašovacích údajů, můžete připojit virtuální počítače k spravované doméně Azure Active Directory Domain Services (Azure služba AD DS). Když připojíte virtuální počítač k spravované doméně Azure služba AD DS, můžete k přihlášení a správě serverů použít uživatelské účty a přihlašovací údaje z domény. Členství ve skupinách ze spravované domény se používá také k řízení přístupu k souborům nebo službám na virtuálním počítači.

V tomto článku se dozvíte, jak připojit virtuální počítač Red Hat Enterprise Linux (RHEL) ke spravované doméně.

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Musíte mít aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
* Ve vašem tenantovi Azure AD je povolená a nakonfigurovaná spravovaná doména Azure Active Directory Domain Services.
    * V případě potřeby se v prvním kurzu [vytvoří a nakonfiguruje Azure Active Directory Domain Services spravovaná doména][create-azure-ad-ds-instance].
* Uživatelský účet, který je součástí spravované domény.

## <a name="create-and-connect-to-a-rhel-linux-vm"></a>Vytvoření virtuálního počítače se systémem RHEL Linux a připojení k němu

Pokud máte v Azure existující virtuální počítač s RHEL Linux, připojte se k němu pomocí SSH a potom pokračujte dalším krokem a [Spusťte konfiguraci virtuálního počítače](#configure-the-hosts-file).

Pokud potřebujete vytvořit virtuální počítač s RHEL Linux nebo chcete vytvořit testovací virtuální počítač pro použití s tímto článkem, můžete použít jednu z následujících metod:

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
* *RHEL* je název hostitele vašeho virtuálního počítače s RHEL, ke kterému se připojujete do spravované domény.

Aktualizujte tyto názvy vlastními hodnotami:

```console
127.0.0.1 rhel rhel.aaddscontoso.com
```

Po dokončení uložte a zavřete soubor *hosts* pomocí `:wq` příkazu editoru.

## <a name="install-required-packages"></a>Instalace požadovaných balíčků

Virtuální počítač potřebuje nějaké další balíčky, aby se připojil k virtuálnímu počítači do spravované domény. Pokud chcete tyto balíčky nainstalovat a nakonfigurovat, aktualizujte a nainstalujte nástroje pro připojení k doméně pomocí `yum` . Mezi RHEL 7. x a RHEL 6. x dochází k nějakým rozdílům, proto použijte příslušné příkazy pro distribuce verzi ve zbývajících částech tohoto článku.

**RHEL 7**

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

**RHEL 6**

```console
sudo yum install adcli sssd authconfig krb5-workstation
```

## <a name="join-vm-to-the-managed-domain"></a>Připojit virtuální počítač ke spravované doméně

Teď, když jsou na virtuálním počítači nainstalované požadované balíčky, připojte virtuální počítač ke spravované doméně. Znovu použijte příslušné kroky pro vaši verzi RHEL distribuce.

### <a name="rhel-7"></a>RHEL 7

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

### <a name="rhel-6"></a>RHEL 6

1. Pomocí `adcli info` příkazu můžete zjistit spravovanou doménu. Následující příklad zjistí sféru *ADDDSCONTOSO.com*. Zadejte název vlastní spravované domény, který bude obsahovat všechna velká písmena:

    ```console
    sudo adcli info aaddscontoso.com
    ```

   Pokud `adcli info` příkaz nemůže najít spravovanou doménu, přečtěte si následující postup řešení potíží:

    * Ujistěte se, že je doména dosažitelná z virtuálního počítače. Zkuste `ping aaddscontoso.com` zjistit, jestli je vrácená kladná odpověď.
    * Ověřte, že je virtuální počítač nasazený do stejné nebo partnerské virtuální sítě, ve které je dostupná spravovaná doména.
    * Ověřte, že nastavení serveru DNS pro virtuální síť se aktualizovala tak, aby odkazovala na řadiče domény spravované domény.

1. Nejprve se připojte k doméně pomocí `adcli join` příkazu, tento příkaz také vytvoří keytab k ověření počítače. Použijte uživatelský účet, který je součástí spravované domény.

    ```console
    sudo adcli join aaddscontoso.com -U contosoadmin
    ```

1. Nyní nakonfigurujte `/ect/krb5.conf` a vytvořte `/etc/sssd/sssd.conf` soubory pro použití `aaddscontoso.com` domény služby Active Directory.
   Ujistěte se, že `AADDSCONTOSO.COM` je nahrazen vlastním názvem domény:

    Otevřete `/ect/krb5.conf` soubor pomocí editoru:

    ```console
    sudo vi /etc/krb5.conf
    ```

    Aktualizujte `krb5.conf` soubor tak, aby odpovídal následující ukázce:

    ```console
    [logging]
     default = FILE:/var/log/krb5libs.log
     kdc = FILE:/var/log/krb5kdc.log
     admin_server = FILE:/var/log/kadmind.log
    
    [libdefaults]
     default_realm = AADDSCONTOSO.COM
     dns_lookup_realm = true
     dns_lookup_kdc = true
     ticket_lifetime = 24h
     renew_lifetime = 7d
     forwardable = true
    
    [realms]
     AADDSCONTOSO.COM = {
     kdc = AADDSCONTOSO.COM
     admin_server = AADDSCONTOSO.COM
     }
    
    [domain_realm]
     .AADDSCONTOSO.COM = AADDSCONTOSO.COM
     AADDSCONTOSO.COM = AADDSCONTOSO.COM
    ```
    
   Vytvořte `/etc/sssd/sssd.conf` soubor:
    
    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

    Aktualizujte `sssd.conf` soubor tak, aby odpovídal následující ukázce:

    ```console
    [sssd]
     services = nss, pam, ssh, autofs
     config_file_version = 2
     domains = AADDSCONTOSO.COM
    
    [domain/AADDSCONTOSO.COM]
    
     id_provider = ad
    ```

1. Ujistěte se, že `/etc/sssd/sssd.conf` oprávnění jsou 600 a jsou vlastněna kořenovým uživatelem:

    ```console
    sudo chmod 600 /etc/sssd/sssd.conf
    sudo chown root:root /etc/sssd/sssd.conf
    ```

1. Použijte `authconfig` k instruování virtuálního počítače o integraci se službou AD Linux:

    ```console
    sudo authconfig --enablesssd --enablesssdauth --update
    ```

1. Spusťte a povolte službu SSSD:

    ```console
    sudo service sssd start
    sudo chkconfig sssd on
    ```

Pokud váš virtuální počítač nemůže úspěšně dokončit proces připojení k doméně, ujistěte se, že skupina zabezpečení sítě virtuálního počítače umožňuje odchozí přenosy protokolu Kerberos na portu TCP + UDP 464 do podsítě virtuální sítě pro spravovanou doménu.

Nyní ověřte, zda se můžete dotazovat na informace o uživateli, pomocí `getent`

```console
sudo getent passwd contosoadmin
```

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

1. Pokud chcete změny použít a umožnit uživatelům, aby se přihlásili pomocí hesla, restartujte službu SSH pro vaši verzi RHEL distribuce:

   **RHEL 7**

    ```console
    sudo systemctl restart sshd
    ```

   **RHEL 6**

    ```console
    sudo service sshd restart
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Udělte skupině AAD DC Administrators oprávnění sudo

Pokud chcete členům skupiny *AAD DC Administrators* udělit na virtuálním počítači RHEL oprávnění správce, přidejte položku do */etc/sudoers*. Po přidání můžou členové skupiny *Správci AAD DC* použít `sudo` příkaz na virtuálním počítači s RHEL.

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

1. Vytvořte nové připojení SSH z konzoly. Pomocí příkazu použijte doménový účet, který patří do spravované domény `ssh -l` , `contosoadmin@aaddscontoso.com` a pak zadejte adresu vašeho virtuálního počítače, třeba *RHEL.aaddscontoso.com*. Pokud používáte Azure Cloud Shell, použijte veřejnou IP adresu virtuálního počítače místo interního názvu DNS.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com rhel.aaddscontoso.com
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
