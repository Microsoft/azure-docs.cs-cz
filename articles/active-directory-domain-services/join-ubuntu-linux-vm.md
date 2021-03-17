---
title: Připojte virtuální počítač Ubuntu k Azure AD Domain Services | Microsoft Docs
description: Naučte se konfigurovat virtuální počítač Ubuntu Linux a připojit se k Azure AD Domain Services spravované doméně.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.author: justinha
ms.custom: fasttrack-edit
ms.openlocfilehash: 8b6d022b9a1f3be70f69943a53754c9dd909ca99
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619483"
---
# <a name="join-an-ubuntu-linux-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Připojení virtuálního počítače s Ubuntu Linux k spravované doméně Azure Active Directory Domain Services

Pokud chcete umožnit uživatelům přihlašovat se k virtuálním počítačům v Azure pomocí jediné sady přihlašovacích údajů, můžete připojit virtuální počítače k spravované doméně Azure Active Directory Domain Services (Azure služba AD DS). Když připojíte virtuální počítač k spravované doméně Azure služba AD DS, můžete k přihlášení a správě serverů použít uživatelské účty a přihlašovací údaje z domény. Členství ve skupinách ze spravované domény se používá také k řízení přístupu k souborům nebo službám na virtuálním počítači.

V tomto článku se dozvíte, jak připojit virtuální počítač Ubuntu Linux ke spravované doméně.

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Musíte mít aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
* Ve vašem tenantovi Azure AD je povolená a nakonfigurovaná spravovaná doména Azure Active Directory Domain Services.
    * V případě potřeby se v prvním kurzu [vytvoří a nakonfiguruje Azure Active Directory Domain Services spravovaná doména][create-azure-ad-ds-instance].
* Uživatelský účet, který je součástí spravované domény.

## <a name="create-and-connect-to-an-ubuntu-linux-vm"></a>Vytvoření virtuálního počítače s Ubuntu Linux a připojení k němu

Pokud máte v Azure existující virtuální počítač Ubuntu Linux, připojte se k němu pomocí SSH a pak pokračujte k dalšímu kroku, abyste mohli [začít s konfigurací virtuálního počítače](#configure-the-hosts-file).

Pokud potřebujete vytvořit Ubuntu Linux virtuální počítač nebo chcete vytvořit testovací virtuální počítač pro použití s tímto článkem, můžete použít jednu z následujících metod:

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
* *Ubuntu* je název hostitele vašeho virtuálního počítače s Ubuntu, ke kterému se připojujete do spravované domény.

Aktualizujte tyto názvy vlastními hodnotami:

```console
127.0.0.1 ubuntu.aaddscontoso.com ubuntu
```

Po dokončení uložte a zavřete soubor *hosts* pomocí `:wq` příkazu editoru.

## <a name="install-required-packages"></a>Instalace požadovaných balíčků

Virtuální počítač potřebuje nějaké další balíčky, aby se připojil k virtuálnímu počítači do spravované domény. Pokud chcete tyto balíčky nainstalovat a nakonfigurovat, aktualizujte a nainstalujte nástroje pro připojení k doméně pomocí `apt-get`

Během instalace protokolu Kerberos se balíček *krb5-User* vyzve k zadání názvu sféry velkými písmeny. Například pokud je název spravované domény *aaddscontoso.com*, jako sféru zadejte *AADDSCONTOSO.com* . Instalace zapisuje `[realm]` oddíly a `[domain_realm]` v konfiguračním souboru */etc/krb5.conf* . Ujistěte se, že jste zadali celou sféru velkými PÍSMENy:

```console
sudo apt-get update
sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
```

## <a name="configure-network-time-protocol-ntp"></a>Konfigurace protokolu NTP (Network Time Protocol)

Aby mohla doménová komunikace správně fungovat, musí se datum a čas vašeho virtuálního počítače s Ubuntu synchronizovat se spravovanou doménou. Do souboru */etc/NTP.conf* přidejte název hostitele NTP spravované domény.

1. Otevřete soubor *NTP. conf* pomocí editoru:

    ```console
    sudo vi /etc/ntp.conf
    ```

1. V souboru *NTP. conf* vytvořte řádek, který přidá název DNS spravované domény. V následujícím příkladu je přidána položka pro *aaddscontoso.com* . Použijte vlastní název DNS:

    ```console
    server aaddscontoso.com
    ```

    Po dokončení uložte a zavřete soubor *NTP. conf* pomocí `:wq` příkazu v editoru.

1. Abyste se ujistili, že je virtuální počítač synchronizovaný se spravovanou doménou, je potřeba provést tyto kroky:

    * Zastavení serveru NTP
    * Aktualizuje datum a čas ze spravované domény.
    * Spuštění služby NTP

    Spusťte následující příkazy a proveďte tyto kroky. Pomocí příkazu použijte vlastní název DNS `ntpdate` :

    ```console
    sudo systemctl stop ntp
    sudo ntpdate aaddscontoso.com
    sudo systemctl start ntp
    ```

## <a name="join-vm-to-the-managed-domain"></a>Připojit virtuální počítač ke spravované doméně

Teď, když jsou na virtuálním počítači nainstalované požadované balíčky a je nakonfigurovaný protokol NTP, připojte virtuální počítač ke spravované doméně.

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
    kinit -V contosoadmin@AADDSCONTOSO.COM
    ```

1. Nakonec připojte virtuální počítač ke spravované doméně pomocí `realm join` příkazu. Použijte stejný uživatelský účet, který je součástí spravované domény, kterou jste zadali v předchozím `kinit` příkazu, například `contosoadmin@AADDSCONTOSO.COM` :

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM' --install=/
    ```

Připojení virtuálního počítače ke spravované doméně chvíli trvá. Následující příklad výstupu ukazuje, že se virtuální počítač úspěšně připojil ke spravované doméně:

```output
Successfully enrolled machine in realm
```

Pokud váš virtuální počítač nemůže úspěšně dokončit proces připojení k doméně, ujistěte se, že skupina zabezpečení sítě virtuálního počítače umožňuje odchozí přenosy protokolu Kerberos na portu TCP + UDP 464 do podsítě virtuální sítě pro spravovanou doménu.

Pokud se vám zobrazila chyba *Nespecifikovaná chyba služby GSS  Vedlejší kód může poskytovat další informace (Server nebyl nalezen v databázi Kerberos)*, otevřít soubor */etc/krb5.conf* a přidat následující kód do `[libdefaults]` části a akci opakujte:

```console
rdns=false
```

## <a name="update-the-sssd-configuration"></a>Aktualizace konfigurace SSSD

Jeden z balíčků nainstalovaných v předchozím kroku byl pro démona služby System Security Services (SSSD). Když se uživatel pokusí přihlásit k virtuálnímu počítači pomocí přihlašovacích údajů domény, SSSD přenáší požadavek poskytovateli ověřování. V tomto scénáři používá SSSD k ověření žádosti Azure služba AD DS.

1. Otevřete soubor *SSSD. conf* pomocí editoru:

    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

1. Odkomentujte řádek pro *use_fully_qualified_names* následujícím způsobem:

    ```console
    # use_fully_qualified_names = True
    ```

    Po dokončení uložte a zavřete soubor *SSSD. conf* pomocí `:wq` příkazu v editoru.

1. Chcete-li použít změnu, restartujte službu SSSD:

    ```console
    sudo systemctl restart sssd
    ```

## <a name="configure-user-account-and-group-settings"></a>Konfigurace uživatelského účtu a nastavení skupiny

Když je virtuální počítač připojený ke spravované doméně a nakonfigurovaný pro ověřování, je potřeba provést několik možností konfigurace uživatelů. Tyto změny konfigurace zahrnují povolení ověřování na základě hesla a automatické vytváření domovských adresářů na místním virtuálním počítači při prvním přihlášení uživatele domény.

### <a name="allow-password-authentication-for-ssh"></a>Povolení ověřování hesla pro SSH

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
    sudo systemctl restart ssh
    ```

### <a name="configure-automatic-home-directory-creation"></a>Konfigurace automatického vytvoření domovského adresáře

Pokud chcete povolit automatické vytváření domovského adresáře, když se uživatel poprvé přihlásí, proveďte následující kroky:

1. Otevřete soubor */etc/pam.d/Common-Session* v editoru:

    ```console
    sudo vi /etc/pam.d/common-session
    ```

1. Do tohoto souboru přidejte následující řádek pod řádkem `session optional pam_sss.so` :

    ```console
    session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
    ```

    Až budete hotovi, uložte a zavřete soubor *běžné relace* pomocí `:wq` příkazu editoru.

### <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Udělte skupině AAD DC Administrators oprávnění sudo

Pokud chcete členům skupiny *AAD DC Administrators* udělit na virtuálním počítači Ubuntu oprávnění správce, přidejte položku do */etc/sudoers*. Po přidání můžou členové skupiny *Správci AAD DC* použít `sudo` příkaz na virtuálním počítači s Ubuntu.

1. Otevřete soubor *sudoers* pro úpravy:

    ```console
    sudo visudo
    ```

1. Do konce souboru */etc/sudoers* přidejte následující položku:

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

    Až budete hotovi, uložte a ukončete Editor pomocí `Ctrl-X` příkazu.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Přihlaste se k virtuálnímu počítači pomocí účtu domény.

Chcete-li ověřit, zda byl virtuální počítač úspěšně připojen ke spravované doméně, spusťte nové připojení SSH pomocí účtu uživatele domény. Potvrďte, že byl vytvořen domovský adresář a že je použito členství ve skupině z domény.

1. Vytvořte nové připojení SSH z konzoly. Pomocí příkazu použijte doménový účet, který patří do spravované domény `ssh -l` , `contosoadmin@aaddscontoso.com` a pak zadejte adresu vašeho virtuálního počítače, třeba *Ubuntu.aaddscontoso.com*. Pokud používáte Azure Cloud Shell, použijte veřejnou IP adresu virtuálního počítače místo interního názvu DNS.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com ubuntu.aaddscontoso.com
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
    sudo apt-get update
    ```

## <a name="next-steps"></a>Další kroky

Pokud máte problémy s připojením k virtuálnímu počítači ke spravované doméně nebo přihlášením pomocí účtu domény, přečtěte si téma [řešení potíží s připojením k doméně](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
