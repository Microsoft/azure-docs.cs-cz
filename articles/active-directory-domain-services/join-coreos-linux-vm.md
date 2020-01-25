---
title: Připojte virtuální počítač CoreOS k Azure AD Domain Services | Microsoft Docs
description: Naučte se nakonfigurovat virtuální počítač s CoreOS a připojit ho k Azure AD Domain Services spravované doméně.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: iainfou
ms.openlocfilehash: ddf6c9238cabedfbdeeb8056864072edc543c342
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712615"
---
# <a name="join-a-coreos-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Připojení virtuálního počítače s CoreOS k spravované doméně Azure AD Domain Services

Pokud chcete umožnit uživatelům přihlašovat se k virtuálním počítačům v Azure pomocí jediné sady přihlašovacích údajů, můžete připojit virtuální počítače k spravované doméně Azure Active Directory Domain Services (služba AD DS). Když připojíte virtuální počítač k spravované doméně Azure služba AD DS, můžete k přihlášení a správě serverů použít uživatelské účty a přihlašovací údaje z domény. Členství ve skupinách ze spravované domény Azure služba AD DS se taky používá, aby vám umožnila řídit přístup k souborům nebo službám na virtuálním počítači.

V tomto článku se dozvíte, jak připojit virtuální počítač CoreOS k spravované doméně Azure služba AD DS.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
* Ve vašem tenantovi Azure AD je povolená a nakonfigurovaná spravovaná doména Azure Active Directory Domain Services.
    * V případě potřeby se v prvním kurzu [vytvoří a nakonfiguruje instance Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Uživatelský účet, který je členem skupiny *správců řadičů domény Azure AD* ve vašem TENANTOVI Azure AD.

## <a name="create-and-connect-to-a-coreos-linux-vm"></a>Vytvoření virtuálního počítače se systémem CoreOS Linux a připojení k němu

Pokud máte v Azure existující virtuální počítač s CoreOS Linux, připojte se k němu pomocí SSH a potom pokračujte dalším krokem a [Spusťte konfiguraci virtuálního počítače](#configure-the-hosts-file).

Pokud potřebujete vytvořit virtuální počítač s CoreOS Linux nebo chcete vytvořit testovací virtuální počítač pro použití s tímto článkem, můžete použít jednu z následujících metod:

* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Když vytváříte virtuální počítač, věnujte pozornost nastavení virtuální sítě a ujistěte se, že virtuální počítač může komunikovat se spravovanou doménou Azure služba AD DS:

* Nasaďte virtuální počítač do stejné nebo partnerské virtuální sítě, ve které jste povolili Azure AD Domain Services.
* Virtuální počítač nasaďte do jiné podsítě, než je vaše instance Azure AD Domain Services.

Až se virtuální počítač nasadí, připojte se k virtuálnímu počítači pomocí SSH podle pokynů.

## <a name="configure-the-hosts-file"></a>Konfigurace souboru hostitelů

Abyste se ujistili, že je název hostitele virtuálního počítače správně nakonfigurovaný pro spravovanou doménu, upravte soubor */etc/hosts* a nastavte název hostitele:

```console
sudo vi /etc/hosts
```

V souboru *hosts* aktualizujte adresu *localhost* . V následujícím příkladu:

* *aadds.contoso.com* je název domény DNS vaší spravované domény Azure služba AD DS.
* *CoreOS* je název hostitele vašeho virtuálního počítače s CoreOS, ke kterému se připojujete do spravované domény.

Aktualizujte tyto názvy vlastními hodnotami:

```console
127.0.0.1 coreos coreos.aadds.contoso.com
```

Po dokončení uložte a zavřete soubor *hosts* pomocí příkazu `:wq` editoru.

## <a name="configure-the-sssd-service"></a>Konfigurace služby SSSD

Aktualizujte konfiguraci */etc/SSSD/SSSD.conf* SSSD.

```console
sudo vi /etc/sssd/sssd.conf
```

Zadejte vlastní název spravované domény Azure služba AD DS pro následující parametry:

* *domény* ve všech velkých písmenech
* *[Domain/AADDS]* kde je AADDS ve všech velkých písmenech
* *ldap_uri*
* *ldap_search_base*
* *krb5_server*
* *KRB5_REALM* velkými písmeny

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = AADDS.CONTOSO.COM

[domain/AADDS.CONTOSO.COM]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://aadds.contoso.com
ldap_search_base = dc=aadds.contoso,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = aadds.contoso.com
krb5_realm = AADDS.CONTOSO.COM
```

## <a name="join-the-vm-to-the-managed-domain"></a>Připojte virtuální počítač k spravované doméně.

Po aktualizaci konfiguračního souboru SSSD se teď připojte k virtuálnímu počítači ke spravované doméně.

1. Nejprve pomocí příkazu `adcli info` ověřte, zda se vám zobrazí informace o spravované doméně Azure služba AD DS. Následující příklad načte informace pro doménu *AADDS. CONTOSO.COM*. Zadejte vlastní název spravované domény Azure služba AD DS, a to velkými PÍSMENy:

    ```console
    sudo adcli info AADDS.CONTOSO.COM
    ```

   Pokud příkaz `adcli info` nemůže najít spravovanou doménu služba AD DS Azure, přečtěte si následující postup řešení potíží:

    * Ujistěte se, že je doména dosažitelná z virtuálního počítače. Zkuste `ping aadds.contoso.com`, abyste viděli, jestli se vrátí kladná odpověď.
    * Ověřte, že je virtuální počítač nasazený do stejné nebo partnerské virtuální sítě, ve které je dostupná doména spravovaná v Azure služba AD DS.
    * Ověřte, že se nastavení serveru DNS pro virtuální síť aktualizovala tak, aby odkazovala na řadiče domény spravované domény Azure služba AD DS.

1. Nyní připojte virtuální počítač k spravované doméně Azure služba AD DS pomocí příkazu `adcli join`. Zadejte uživatele, který patří do skupiny *Správci řadičů domény AAD* . V případě potřeby [přidejte uživatelský účet do skupiny v Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Znovu, název spravované domény Azure služba AD DS musí být zadaný velkými PÍSMENy. V následujícím příkladu se k inicializaci protokolu Kerberos používá účet s názvem `contosoadmin@aadds.contoso.com`. Zadejte vlastní uživatelský účet, který je členem skupiny *AAD DC Administrators* .

    ```console
    sudo adcli join -D AADDS.CONTOSO.COM -U contosoadmin@AADDS.CONTOSO.COM -K /etc/krb5.keytab -H coreos.aadds.contoso.com -N coreos
    ```

    Příkaz `adcli join` nevrátí žádné informace, pokud se virtuální počítač úspěšně připojil ke spravované doméně Azure služba AD DS.

1. Pokud chcete použít konfiguraci připojení k doméně, spusťte službu SSSD:
  
    ```console
    sudo systemctl start sssd.service
    ```

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Přihlaste se k virtuálnímu počítači pomocí účtu domény.

Pokud chcete ověřit, jestli se virtuální počítač úspěšně připojil ke spravované doméně Azure služba AD DS, spusťte nové připojení SSH pomocí účtu uživatele domény. Potvrďte, že byl vytvořen domovský adresář a že je použito členství ve skupině z domény.

1. Vytvořte nové připojení SSH z konzoly. Pomocí příkazu `ssh -l`, jako je třeba `contosoadmin@aadds.contoso.com`, zadejte účet domény, který patří do spravované domény, a pak zadejte adresu vašeho virtuálního počítače, třeba *CoreOS.aadds.contoso.com*. Pokud používáte Azure Cloud Shell, použijte veřejnou IP adresu virtuálního počítače místo interního názvu DNS.

    ```console
    ssh -l contosoadmin@AADDS.CONTOSO.com coreos.aadds.contoso.com
    ```

1. Nyní ověřte, zda jsou členství ve skupině správně řešena:

    ```console
    id
    ```

    Měli byste vidět členství ve skupinách ze spravované domény Azure služba AD DS.

## <a name="next-steps"></a>Další kroky

Pokud máte problémy s připojením k virtuálnímu počítači ke spravované doméně Azure služba AD DS nebo když se přihlašujete pomocí účtu domény, přečtěte si téma [řešení potíží s připojením k doméně](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
