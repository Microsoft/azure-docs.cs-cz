---
title: Připojte virtuální počítač CoreOS k Azure AD Domain Services | Microsoft Docs
description: Naučte se nakonfigurovat virtuální počítač s CoreOS a připojit ho k Azure AD Domain Services spravované doméně.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.author: justinha
ms.openlocfilehash: 1e98f32bd6fe7d5373d5ab6621ffdce5e79abc08
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619585"
---
# <a name="join-a-coreos-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Připojení virtuálního počítače s CoreOS k spravované doméně Azure Active Directory Domain Services

Pokud chcete umožnit uživatelům přihlašovat se k virtuálním počítačům v Azure pomocí jediné sady přihlašovacích údajů, můžete připojit virtuální počítače k spravované doméně Azure Active Directory Domain Services (Azure služba AD DS). Když připojíte virtuální počítač k spravované doméně Azure služba AD DS, můžete k přihlášení a správě serverů použít uživatelské účty a přihlašovací údaje z domény. Členství ve skupinách ze spravované domény se používá také k řízení přístupu k souborům nebo službám na virtuálním počítači.

V tomto článku se dozvíte, jak připojit virtuální počítač s CoreOS ke spravované doméně.

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Musíte mít aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
* Ve vašem tenantovi Azure AD je povolená a nakonfigurovaná spravovaná doména Azure Active Directory Domain Services.
    * V případě potřeby se v prvním kurzu [vytvoří a nakonfiguruje Azure Active Directory Domain Services spravovaná doména][create-azure-ad-ds-instance].
* Uživatelský účet, který je součástí spravované domény.

## <a name="create-and-connect-to-a-coreos-linux-vm"></a>Vytvoření virtuálního počítače se systémem CoreOS Linux a připojení k němu

Pokud máte v Azure existující virtuální počítač s CoreOS Linux, připojte se k němu pomocí SSH a potom pokračujte dalším krokem a [Spusťte konfiguraci virtuálního počítače](#configure-the-hosts-file).

Pokud potřebujete vytvořit virtuální počítač s CoreOS Linux nebo chcete vytvořit testovací virtuální počítač pro použití s tímto článkem, můžete použít jednu z následujících metod:

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
* *CoreOS* je název hostitele vašeho virtuálního počítače s CoreOS, ke kterému se připojujete do spravované domény.

Aktualizujte tyto názvy vlastními hodnotami:

```console
127.0.0.1 coreos coreos.aaddscontoso.com
```

Po dokončení uložte a zavřete soubor *hosts* pomocí `:wq` příkazu editoru.

## <a name="configure-the-sssd-service"></a>Konfigurace služby SSSD

Aktualizujte konfiguraci */etc/SSSD/SSSD.conf* SSSD.

```console
sudo vi /etc/sssd/sssd.conf
```

Zadejte vlastní název spravované domény pro následující parametry:

* *domény* ve všech velkých písmenech
* *[Domain/AADDSCONTOSO]* kde je AADDSCONTOSO ve všech velkých písmenech
* *ldap_uri*
* *ldap_search_base*
* *krb5_server*
* *KRB5_REALM* velkými písmeny

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = AADDSCONTOSO.COM

[domain/AADDSCONTOSO]
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

## <a name="join-the-vm-to-the-managed-domain"></a>Připojte virtuální počítač k spravované doméně.

Po aktualizaci konfiguračního souboru SSSD se teď připojte k virtuálnímu počítači ke spravované doméně.

1. Nejprve pomocí příkazu ověřte, že se `adcli info` zobrazí informace o spravované doméně. Následující příklad načte informace pro doménu *AADDSCONTOSO.com*. Zadejte název vlastní spravované domény, který bude obsahovat všechna velká písmena:

    ```console
    sudo adcli info AADDSCONTOSO.COM
    ```

   Pokud `adcli info` příkaz nemůže najít spravovanou doménu, přečtěte si následující postup řešení potíží:

    * Ujistěte se, že je doména dosažitelná z virtuálního počítače. Zkuste `ping aaddscontoso.com` zjistit, jestli je vrácená kladná odpověď.
    * Ověřte, že je virtuální počítač nasazený do stejné nebo partnerské virtuální sítě, ve které je dostupná spravovaná doména.
    * Ověřte, že nastavení serveru DNS pro virtuální síť se aktualizovala tak, aby odkazovala na řadiče domény spravované domény.

1. Nyní připojte virtuální počítač ke spravované doméně pomocí `adcli join` příkazu. Zadejte uživatele, který je součástí spravované domény. V případě potřeby [přidejte uživatelský účet do skupiny v Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Název spravované domény se znovu musí zadat jenom velkými PÍSMENy. V následujícím příkladu se `contosoadmin@aaddscontoso.com` k inicializaci protokolu Kerberos používá účet s názvem. Zadejte vlastní uživatelský účet, který je součástí spravované domény.

    ```console
    sudo adcli join -D AADDSCONTOSO.COM -U contosoadmin@AADDSCONTOSO.COM -K /etc/krb5.keytab -H coreos.aaddscontoso.com -N coreos
    ```

    `adcli join`Příkaz nevrátí žádné informace, pokud se virtuální počítač úspěšně připojil ke spravované doméně.

1. Pokud chcete použít konfiguraci připojení k doméně, spusťte službu SSSD:
  
    ```console
    sudo systemctl start sssd.service
    ```

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Přihlaste se k virtuálnímu počítači pomocí účtu domény.

Chcete-li ověřit, zda byl virtuální počítač úspěšně připojen ke spravované doméně, spusťte nové připojení SSH pomocí účtu uživatele domény. Potvrďte, že byl vytvořen domovský adresář a že je použito členství ve skupině z domény.

1. Vytvořte nové připojení SSH z konzoly. Pomocí příkazu použijte doménový účet, který patří do spravované domény `ssh -l` , `contosoadmin@aaddscontoso.com` a pak zadejte adresu vašeho virtuálního počítače, třeba *CoreOS.aaddscontoso.com*. Pokud používáte Azure Cloud Shell, použijte veřejnou IP adresu virtuálního počítače místo interního názvu DNS.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com coreos.aaddscontoso.com
    ```

1. Nyní ověřte, zda jsou členství ve skupině správně řešena:

    ```console
    id
    ```

    Měli byste vidět členství ve skupinách ze spravované domény.

## <a name="next-steps"></a>Další kroky

Pokud máte problémy s připojením k virtuálnímu počítači ke spravované doméně nebo přihlášením pomocí účtu domény, přečtěte si téma [řešení potíží s připojením k doméně](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
