---
title: Připojení k virtuálnímu počítači Ubuntu ke službě Azure AD Domain Services | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nakonfigurovat a připojit virtuální počítač Ubuntu Linux ke spravované doméně služby Azure AD Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: 74af841b777494744c72ed219bacd3b3835d41ac
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617553"
---
# <a name="join-an-ubuntu-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Připojení virtuálního počítače Ubuntu Linux ke spravované doméně služby Azure AD Domain Services

Pokud chcete uživatelům uvolit přihlášení k virtuálním počítačům (VM) v Azure pomocí jediné sady přihlašovacích údajů, můžete virtuální počítače připojit ke spravované doméně služby Azure Active Directory Domain Services (AD DS). Když připojíte virtuální počítač ke spravované doméně Azure AD DS, uživatelské účty a přihlašovací údaje z domény se dají použít k přihlášení a správě serverů. Členství ve skupinách ze spravované domény Azure AD DS se také používají, abyste měli řídit přístup k souborům nebo službám na virtuálním počítači.

Tento článek ukazuje, jak se připojit k virtuálnímu počítači Ubuntu Linux do spravované domény Azure AD DS.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte si účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, synchronizovaný s místním adresářem nebo s adresářem pouze pro cloud.
    * V případě potřeby [vytvořte klienta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure ke svému účtu][associate-azure-ad-tenant].
* Spravovaná doména Služby Azure Active Directory Domain Services povolená a nakonfigurovaná ve vašem tenantovi Azure AD.
    * V případě potřeby první kurz [vytvoří a nakonfiguruje instanci služby Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Uživatelský účet, který je součástí spravované domény Azure AD DS.

## <a name="create-and-connect-to-an-ubuntu-linux-vm"></a>Vytvoření virtuálního počítače Ubuntu Linux a připojení k němu

Pokud máte v Azure existující virtuální počítač Ubuntu Linux, připojte se k němu pomocí SSH a pokračujte dalším krokem a [začněte konfigurovat virtuální počítač](#configure-the-hosts-file).

Pokud potřebujete vytvořit virtuální počítač Ubuntu Linux nebo chcete vytvořit testovací virtuální počítač pro použití s tímto článkem, můžete použít jednu z následujících metod:

* [portál Azure](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Při vytváření virtuálního počítače věnujte pozornost nastavení virtuální sítě a ujistěte se, že virtuální počítač může komunikovat se spravovanou doménou Azure AD DS:

* Nasaďte virtuální počítač do stejné nebo partnerské virtuální sítě, ve které jste povolili služby Azure AD Domain Services.
* Nasazení virtuálního počítače do jiné podsítě, než je vaše instance služby Azure AD Domain Services.

Po nasazení virtuálního virtuálního zařízení postupujte podle pokynů pro připojení k virtuálnímu virtuálnímu zařízení pomocí SSH.

## <a name="configure-the-hosts-file"></a>Konfigurace souboru hosts

Chcete-li se ujistit, že je název hostitele virtuálního počítače správně nakonfigurován pro spravovanou doménu, upravte soubor */etc/hosts* a nastavte název hostitele:

```console
sudo vi /etc/hosts
```

V souboru *hosts* aktualizujte adresu *localhost.* V následujícím příkladu:

* *aaddscontoso.com* je název domény DNS spravované domény Azure AD DS.
* *ubuntu* je název hostitele vašeho Ubuntu VM, který se připojujete ke spravované doméně.

Aktualizujte tyto názvy vlastními hodnotami:

```console
127.0.0.1 ubuntu.aaddscontoso.com ubuntu
```

Po dokončení uložte a ukončete soubor *hosts* pomocí `:wq` příkazu editoru.

## <a name="install-required-packages"></a>Instalace požadovaných balíčků

Virtuální počítač potřebuje některé další balíčky pro připojení virtuálního počítače ke spravované doméně Azure AD DS. Chcete-li nainstalovat a nakonfigurovat tyto balíčky, aktualizujte a nainstalujte nástroje pro připojení k doméně pomocí`apt-get`

Během instalace protokolu Kerberos *krb5-user* balíček vyzve k názvu sféry v části VELKÁ PÍSMENA. Pokud je například název spravované domény Azure AD DS *aaddscontoso.com*, zadejte jako sféru *AADDSCONTOSO.COM.* Instalace zapíše `[realm]` `[domain_realm]` oddíly a do konfiguračního souboru */etc/krb5.conf.* Ujistěte se, že zadáte sféru a VELKÁ PÍSMENA:

```console
sudo apt-get update
sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
```

## <a name="configure-network-time-protocol-ntp"></a>Konfigurace protokolu NTP (Network Time Protocol)

Aby komunikace s doménami fungovala správně, musí se datum a čas virtuálního počítače Ubuntu synchronizovat se spravovanou doménou Azure AD DS. Přidejte název hostitele NTP spravované domény služby Azure AD DS do souboru */etc/ntp.conf.*

1. Otevřete soubor *ntp.conf* s editorem:

    ```console
    sudo vi /etc/ntp.conf
    ```

1. V souboru *ntp.conf* vytvořte řádek pro přidání názvu DNS spravované domény služby Azure AD DS. V následujícím příkladu je přidána položka pro *aaddscontoso.com.* Použijte vlastní název DNS:

    ```console
    server aaddscontoso.com
    ```

    Po dokončení uložte a ukončete `:wq` soubor *ntp.conf* pomocí příkazu editoru.

1. Chcete-li se ujistit, že je virtuální počítač synchronizován se spravovanou doménou Azure AD DS, jsou potřeba následující kroky:

    * Zastavení serveru NTP
    * Aktualizace data a času ze spravované domény
    * Spuštění služby NTP

    Chcete-li provést tyto kroky, spusťte následující příkazy. Použijte vlastní název DNS `ntpdate` s příkazem:

    ```console
    sudo systemctl stop ntp
    sudo ntpdate aaddscontoso.com
    sudo systemctl start ntp
    ```

## <a name="join-vm-to-the-managed-domain"></a>Připojení virtuálního virtuálního montovaka ke spravované doméně

Teď, když jsou požadované balíčky nainstalované na virtuálním počítači a NTP je nakonfigurovaný, připojte virtuální počítač ke spravované doméně Azure AD DS.

1. Pomocí `realm discover` příkazu zjišťujte spravovanou doménu Azure AD DS. Následující příklad zjišťuje *AADDSCONTOSO.COM*sféry . Zadejte svůj vlastní název spravované domény Azure AD DS ve všech velkých písmen:

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   Pokud `realm discover` příkaz nemůže najít vaši spravovanou doménu Azure AD DS, přečtěte si následující kroky řešení potíží:

    * Ujistěte se, že doména je dosažitelná z virtuálního virtuálního soudu. Pokuste `ping aaddscontoso.com` se zjistit, zda je vrácena kladná odpověď.
    * Zkontrolujte, že virtuální počítač je nasazený do stejné nebo partnerské virtuální sítě, ve které je dostupná spravovaná doména Azure AD DS.
    * Zkontrolujte, zda bylo nastavení serveru DNS pro virtuální síť aktualizováno tak, aby ukazovalo na řadiče domény spravované spravované doménou Azure AD DS.

1. Nyní inicializovat Kerberos pomocí příkazu. `kinit` Zadejte uživatele, který je součástí spravované domény Azure AD DS. V případě potřeby [přidejte uživatelský účet do skupiny ve službě Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Znovu název spravované domény Azure AD DS musí být zadán ve všech velkých písmen. V následujícím příkladu se `contosoadmin@aaddscontoso.com` účet s názvem používá k inicializaci protokolu Kerberos. Zadejte svůj vlastní uživatelský účet, který je součástí spravované domény Azure AD DS:

    ```console
    kinit contosoadmin@AADDSCONTOSO.COM
    ```

1. Nakonec připojte počítač ke spravované doméně Azure AD DS pomocí příkazu. `realm join` Použijte stejný uživatelský účet, který je součástí spravované domény Služby Azure AD DS, kterou jste zadali v předchozím `kinit` příkazu, například `contosoadmin@AADDSCONTOSO.COM`:

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM' --install=/
    ```

Připojení virtuálního počítače ke spravované doméně Azure AD DS trvá několik okamžiků. Následující příklad výstupu ukazuje, že virtuální počítač se úspěšně připojil ke spravované doméně Azure AD DS:

```output
Successfully enrolled machine in realm
```

Pokud váš virtuální počítač nemůže úspěšně dokončit proces připojení k doméně, ujistěte se, že skupina zabezpečení sítě virtuálního počítače umožňuje odchozí přenosprotokolu Kerberos na portu TCP + UDP 464 do podsítě virtuální sítě pro spravovanou doménu Azure AD DS.

Pokud jste obdrželi chybu *Nespecifikované selhání GSS.  Menší kód může poskytnout další informace (Server nebyl nalezen v databázi Kerberos)*, otevřete `[libdefaults]` soubor */etc/krb5.conf* a přidejte následující kód do sekce a zkuste to znovu:

```console
rdns=false
```

## <a name="update-the-sssd-configuration"></a>Aktualizace konfigurace SSSD

Jeden z balíčků nainstalovaných v předchozím kroku byl pro Daemon systémových bezpečnostních služeb (SSSD). Když se uživatel pokusí přihlásit k virtuálnímu virtuálnímu mísu pomocí pověření domény, SSSD předá požadavek poskytovateli ověřování. V tomto scénáři SSSD používá Azure AD DS k ověření požadavku.

1. Otevřete soubor *sssd.conf* pomocí editoru:

    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

1. Zakomentujte řádek pro *use_fully_qualified_names* takto:

    ```console
    # use_fully_qualified_names = True
    ```

    Po dokončení uložte a ukončete soubor `:wq` *sssd.conf* pomocí příkazu editoru.

1. Chcete-li změnu použít, restartujte službu SSSD:

    ```console
    sudo service sssd restart
    ```

## <a name="configure-user-account-and-group-settings"></a>Konfigurace nastavení uživatelského účtu a skupiny

S virtuálním počítačem připojen ke spravované doméně Azure AD DS a nakonfigurované pro ověřování, existuje několik možností konfigurace uživatele k dokončení. Tyto změny konfigurace zahrnují povolení ověřování na základě hesla a automatické vytváření domovských adresářů na místním virtuálním počítači při prvním přihlášení uživatelů domény.

### <a name="allow-password-authentication-for-ssh"></a>Povolit ověřování hesla pro SSH

Ve výchozím nastavení se uživatelé můžou k virtuálnímu počítačůmu přihlašovat jenom pomocí ověřování pomocí veřejného klíče SSH. Ověřování pomocí hesla se nezdaří. Když připojíte virtuální počítač ke spravované doméně Azure AD DS, musí tyto účty domény používat ověřování založené na heslech. Aktualizujte konfiguraci SSH takto, aby bylo možné ověřování pomocí hesel.

1. Otevřete *soubor sshd_conf* pomocí editoru:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. Aktualizujte řádek pro *passwordauthentication* na *ano*:

    ```console
    PasswordAuthentication yes
    ```

    Po dokončení uložte a ukončete *soubor sshd_conf* pomocí `:wq` příkazu editoru.

1. Chcete-li použít změny a uvolit uživatelům přihlášení pomocí hesla, restartujte službu SSH:

    ```console
    sudo systemctl restart ssh
    ```

### <a name="configure-automatic-home-directory-creation"></a>Konfigurace automatického vytváření domovského adresáře

Chcete-li povolit automatické vytvoření domovského adresáře při prvním přihlášení uživatele, proveďte následující kroky:

1. Otevřete soubor */etc/pam.d/common-session* v editoru:

    ```console
    sudo vi /etc/pam.d/common-session
    ```

1. Přidejte následující řádek v tomto `session optional pam_sss.so`souboru pod řádek :

    ```console
    session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
    ```

    Po dokončení uložte a ukončete `:wq` soubor běžné *relace* pomocí příkazu editoru.

### <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Udělení oprávnění skupiny sudo správců řadiče AAD DC

Chcete-li udělit členům *skupiny AAD DC Administrators* oprávnění správce na virtuálním počítači Ubuntu, přidejte položku */etc/sudoers*. Po přidání mohou členové *skupiny AAD DC Administrators* použít `sudo` příkaz na virtuálním počítači Ubuntu.

1. Otevřete soubor *sudoers* pro úpravy:

    ```console
    sudo visudo
    ```

1. Přidejte následující položku na konec *souboru /etc/sudoers:*

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

    Po dokončení uložte a ukončete editor pomocí příkazu. `Ctrl-X`

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Přihlášení k virtuálnímu virtuálnímu účtu pomocí účtu domény

Chcete-li ověřit, že virtuální počítač byl úspěšně připojen ke spravované doméně Azure AD DS, spusťte nové připojení SSH pomocí uživatelského účtu domény. Zkontrolujte, zda byl vytvořen domovský adresář a zda je použito členství ve skupině z domény.

1. Vytvořte nové připojení SSH z konzole. Pomocí `ssh -l` příkazu použijte účet domény, který patří `contosoadmin@aaddscontoso.com` do spravované domény, například adresu virtuálního počítače, například *ubuntu.aaddscontoso.com*. Pokud používáte Azure Cloud Shell, použijte veřejnou IP adresu virtuálního počítače, nikoli interní název DNS.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com ubuntu.aaddscontoso.com
    ```

1. Po úspěšném připojení k virtuálnímu virtuálnímu serveru ověřte, že domácí adresář byl inicializován správně:

    ```console
    pwd
    ```

    Měli byste být v adresáři */home* s vlastním adresářem, který odpovídá uživatelskému účtu.

1. Nyní zkontrolujte, zda jsou členství ve skupinách správně vyřešena:

    ```console
    id
    ```

    Měli byste vidět vaše členství ve skupinách ze spravované domény Azure AD DS.

1. Pokud jste se k virtuálnímu virtuálnímu soudu přihlásili jako člen *skupiny Správci řadiče domény AAD,* zkontrolujte, zda můžete `sudo` příkaz správně použít:

    ```console
    sudo apt-get update
    ```

## <a name="next-steps"></a>Další kroky

Pokud máte problémy s připojením virtuálního počítače ke spravované doméně Azure AD DS nebo při přihlášení pomocí účtu domény, [přečtěte si článek Poradce při potížích s připojením domény](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
