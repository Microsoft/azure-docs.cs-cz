---
title: Připojení virtuálního počítače CoreOS ke službám Domény Azure AD | Dokumenty společnosti Microsoft
description: Zjistěte, jak nakonfigurovat a připojit virtuální počítač CoreOS ke spravované doméně služby Azure AD Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/23/2020
ms.author: iainfou
ms.openlocfilehash: 63dfe39b986125abc9cacf6c1a6556876bbd3a99
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655199"
---
# <a name="join-a-coreos-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Připojení virtuálního počítače CoreOS ke spravované doméně služby Azure AD Domain Services

Pokud chcete uživatelům uvolit přihlášení k virtuálním počítačům (VM) v Azure pomocí jediné sady přihlašovacích údajů, můžete virtuální počítače připojit ke spravované doméně služby Azure Active Directory Domain Services (AD DS). Když připojíte virtuální počítač ke spravované doméně Azure AD DS, uživatelské účty a přihlašovací údaje z domény se dají použít k přihlášení a správě serverů. Členství ve skupinách ze spravované domény Azure AD DS se také používají, abyste měli řídit přístup k souborům nebo službám na virtuálním počítači.

Tento článek ukazuje, jak připojit virtuální počítač CoreOS ke spravované doméně Azure AD DS.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte si účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, synchronizovaný s místním adresářem nebo s adresářem pouze pro cloud.
    * V případě potřeby [vytvořte klienta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure ke svému účtu][associate-azure-ad-tenant].
* Spravovaná doména Služby Azure Active Directory Domain Services povolená a nakonfigurovaná ve vašem tenantovi Azure AD.
    * V případě potřeby první kurz [vytvoří a nakonfiguruje instanci služby Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Uživatelský účet, který je součástí spravované domény Azure AD DS.

## <a name="create-and-connect-to-a-coreos-linux-vm"></a>Vytvoření virtuálního počítače CoreOS s Linuxem a připojení k němu

Pokud máte existující coreos linuxový virtuální počítač v Azure, připojte se k němu pomocí SSH, pak pokračujte dalším krokem a [začněte konfigurovat virtuální počítač](#configure-the-hosts-file).

Pokud potřebujete vytvořit virtuální počítač CoreOS Linux nebo chcete vytvořit testovací virtuální počítač pro použití s tímto článkem, můžete použít jednu z následujících metod:

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
* *coreos* je název hostitele virtuálního počítače CoreOS, ke kterému se připojujete ke spravované doméně.

Aktualizujte tyto názvy vlastními hodnotami:

```console
127.0.0.1 coreos coreos.aaddscontoso.com
```

Po dokončení uložte a ukončete soubor *hosts* pomocí `:wq` příkazu editoru.

## <a name="configure-the-sssd-service"></a>Konfigurace služby SSSD

Aktualizujte konfiguraci SSSD */etc/sssd/sssd.conf.*

```console
sudo vi /etc/sssd/sssd.conf
```

Zadejte svůj vlastní název spravované domény Azure AD DS pro následující parametry:

* *domény* ve všech velkých velkých písmen
* *[doména/AADDS],* kde aadds je ve všech velkých písmen
* *ldap_uri*
* *ldap_search_base*
* *krb5_server*
* *krb5_realm* ve všech velkých písmenech

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = AADDSCONTOSO.COM

[domain/AADDSCONTOSO.COM]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://aaddscontoso.com
ldap_search_base = dc=aaddscontoso,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = aaddscontoso.com
krb5_realm = AADDSCONTOSO.COM
```

## <a name="join-the-vm-to-the-managed-domain"></a>Připojení virtuálního virtuálního klienta ke spravované doméně

Když je konfigurační soubor SSSD aktualizovaný, teď připojte virtuální počítač ke spravované doméně.

1. Nejprve pomocí `adcli info` příkazu ověřte, že můžete zobrazit informace o spravované doméně Azure AD DS. Následující příklad získá informace o *doméně AADDSCONTOSO.COM*. Zadejte svůj vlastní název spravované domény Azure AD DS ve všech velkých písmen:

    ```console
    sudo adcli info AADDSCONTOSO.COM
    ```

   Pokud `adcli info` příkaz nemůže najít vaši spravovanou doménu Azure AD DS, přečtěte si následující kroky řešení potíží:

    * Ujistěte se, že doména je dosažitelná z virtuálního virtuálního soudu. Pokuste `ping aaddscontoso.com` se zjistit, zda je vrácena kladná odpověď.
    * Zkontrolujte, že virtuální počítač je nasazený do stejné nebo partnerské virtuální sítě, ve které je dostupná spravovaná doména Azure AD DS.
    * Zkontrolujte, zda bylo nastavení serveru DNS pro virtuální síť aktualizováno tak, aby ukazovalo na řadiče domény spravované spravované doménou Azure AD DS.

1. Teď se připojte k virtuálnímu počítači do `adcli join` spravované domény Azure AD DS pomocí příkazu. Zadejte uživatele, který je součástí spravované domény Azure AD DS. V případě potřeby [přidejte uživatelský účet do skupiny ve službě Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Znovu název spravované domény Azure AD DS musí být zadán ve všech velkých písmen. V následujícím příkladu se `contosoadmin@aaddscontoso.com` účet s názvem používá k inicializaci protokolu Kerberos. Zadejte svůj vlastní uživatelský účet, který je součástí spravované domény Azure AD DS.

    ```console
    sudo adcli join -D AADDSCONTOSO.COM -U contosoadmin@AADDSCONTOSO.COM -K /etc/krb5.keytab -H coreos.aaddscontoso.com -N coreos
    ```

    Příkaz `adcli join` nevrátí žádné informace, když se virtuální počítač úspěšně připojil ke spravované doméně Azure AD DS.

1. Chcete-li použít konfiguraci připojení k doméně, spusťte službu SSSD:
  
    ```console
    sudo systemctl start sssd.service
    ```

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Přihlášení k virtuálnímu virtuálnímu účtu pomocí účtu domény

Chcete-li ověřit, že virtuální počítač byl úspěšně připojen ke spravované doméně Azure AD DS, spusťte nové připojení SSH pomocí uživatelského účtu domény. Zkontrolujte, zda byl vytvořen domovský adresář a zda je použito členství ve skupině z domény.

1. Vytvořte nové připojení SSH z konzole. Pomocí `ssh -l` příkazu použijte účet domény, který patří `contosoadmin@aaddscontoso.com` do spravované domény, například adresu virtuálního počítače, například *coreos.aaddscontoso.com*. Pokud používáte Azure Cloud Shell, použijte veřejnou IP adresu virtuálního počítače, nikoli interní název DNS.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com coreos.aaddscontoso.com
    ```

1. Nyní zkontrolujte, zda jsou členství ve skupinách správně vyřešena:

    ```console
    id
    ```

    Měli byste vidět vaše členství ve skupinách ze spravované domény Azure AD DS.

## <a name="next-steps"></a>Další kroky

Pokud máte problémy s připojením virtuálního počítače ke spravované doméně Azure AD DS nebo při přihlášení pomocí účtu domény, [přečtěte si článek Poradce při potížích s připojením domény](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
