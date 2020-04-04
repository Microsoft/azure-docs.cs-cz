---
title: Účty spravovaných služeb skupiny pro služby Azure AD Domain Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak vytvořit účet spravované služby skupiny (gMSA) pro použití se spravovanými doménami služby Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 5955f52cda73630f371a46f83ac0fb9a252b80e3
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655491"
---
# <a name="create-a-group-managed-service-account-gmsa-in-azure-ad-domain-services"></a>Vytvoření účtu služby spravované skupiny (gMSA) ve službě Azure AD Domain Services

Aplikace a služby často potřebují identitu k ověření sami s jinými prostředky. Webová služba může být například nutné ověřit pomocí databázové služby. Pokud aplikace nebo služba má více instancí, jako je například farma webového serveru, ruční vytváření a konfigurace identit pro tyto prostředky získá časově náročné.

Místo toho lze vytvořit účet spravované služby skupiny (gMSA) ve spravované doméně Služby Azure Active Directory Domain Services (Azure AD DS). Operační systém Windows automaticky spravuje pověření pro gMSA, což zjednodušuje správu velkých skupin prostředků.

Tento článek ukazuje, jak vytvořit gMSA ve spravované doméně Azure AD DS pomocí Azure PowerShellu.

## <a name="before-you-begin"></a>Než začnete

Chcete-li tento článek dokončit, potřebujete následující zdroje a oprávnění:

* Aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte si účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, synchronizovaný s místním adresářem nebo s adresářem pouze pro cloud.
    * V případě potřeby [vytvořte klienta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure ke svému účtu][associate-azure-ad-tenant].
* Spravovaná doména Služby Azure Active Directory Domain Services povolená a nakonfigurovaná ve vašem tenantovi Azure AD.
    * V případě potřeby proveďte kurz [a vytvořte a nakonfigurujte instanci služby Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Virtuální počítač pro správu Windows Serveru, který je spojený se spravovanou doménou Azure AD DS.
    * V případě potřeby dokončete kurz [a vytvořte virtuální virtuální virtuální montovny pro správu][tutorial-create-management-vm].

## <a name="managed-service-accounts-overview"></a>Přehled účtů spravovaných služeb

Samostatný účet spravované služby (sMSA) je účet domény, jehož heslo je automaticky spravováno. Tento přístup zjednodušuje správu hlavního názvu služby (SPN) a umožňuje delegovanou správu ostatním správcům. Nemusíte ručně vytvářet a otáčet přihlašovací údaje pro účet.

Účet spravované služby skupiny (gMSA) poskytuje stejné zjednodušení správy, ale pro více serverů v doméně. GMSA umožňuje všem instancím služby hostované na farmě serveru používat stejný instanční objekt pro vzájemné ověřovací protokoly. Pokud je jako instanční objekt použit gMSA, operační systém Windows opět spravuje heslo účtu namísto spoléhání se na správce.

Další informace naleznete v tématu [přehled účtů spravovaných služeb skupiny (gMSA).][gmsa-overview]

## <a name="using-service-accounts-in-azure-ad-ds"></a>Použití účtů služeb ve službě Azure AD DS

Vzhledem k tomu, že spravované domény Azure AD DS jsou uzamčeny a spravovány společností Microsoft, existují některé důležité informace při používání účtů služeb:

* Vytvořte účty služeb ve vlastních organizačních jednotkách (OU) ve spravované doméně.
    * Nelze vytvořit účet služby v předdefinované *uživatelé AADDC* nebo *AADDC počítače* ru.
    * Místo toho [vytvořte vlastní ou ve][create-custom-ou] spravované doméně Azure AD DS a pak vytvořte účty služeb v této vlastní hlavní výužce.
* Kořenový klíč služby Distribuce klíčů (KDS) je předem vytvořen.
    * Kořenový klíč KDS se používá ke generování a načtení hesel pro gMSA. Ve službě Azure AD DS se vytvoří kořen KDS pro vás.
    * Nemáte oprávnění k vytvoření jiného nebo zobrazení výchozího kořenového klíče KDS.

## <a name="create-a-gmsa"></a>Vytvoření gMSA

Nejprve vytvořte vlastní organizační jednotku pomocí rutiny [New-ADOrganizationalUnit.][New-AdOrganizationalUnit] Další informace o vytváření a správě vlastních vou naleznete v [tématu Vlastní vou ve službě Azure AD DS][create-custom-ou].

> [!TIP]
> Chcete-li provést tyto kroky k vytvoření gMSA, [použijte virtuální počítač pro správu][tutorial-create-management-vm]. Tento virtuální virtuální měnový virtuální ms pro správu by už měl mít požadované rutiny prostředí AD PowerShell a připojení ke spravované doméně.

Následující příklad vytvoří vlastní *ouovou* ve spravované doméně Azure AD DS s názvem *aaddscontoso.com*. Použijte vlastní ou službu a název spravované domény:

```powershell
New-ADOrganizationalUnit -Name "myNewOU" -Path "DC=aaddscontoso,DC=COM"
```

Nyní vytvořte gMSA pomocí rutiny [New-ADServiceAccount.][New-ADServiceAccount] Jsou definovány následující ukázkové parametry:

* **-Name** je nastavena na *WebFarmSvc*
* **Parametr -Path** určuje vlastní ou událost pro gMSA vytvořenou v předchozím kroku.
* Položky DNS a hlavní názvy služeb jsou nastaveny pro *WebFarmSvc.aaddscontoso.com*
* Objekty v *AADDSCONTOSO-SERVER$* mohou načíst heslo použít identitu.

Zadejte svá vlastní jména a názvy domén.

```powershell
New-ADServiceAccount -Name WebFarmSvc `
    -DNSHostName WebFarmSvc.aaddscontoso.com `
    -Path "OU=MYNEWOU,DC=aaddscontoso,DC=com" `
    -KerberosEncryptionType AES128, AES256 `
    -ManagedPasswordIntervalInDays 30 `
    -ServicePrincipalNames http/WebFarmSvc.aaddscontoso.com/aaddscontoso.com, `
        http/WebFarmSvc.aaddscontoso.com/aaddscontoso, `
        http/WebFarmSvc/aaddscontoso.com, `
        http/WebFarmSvc/aaddscontoso `
    -PrincipalsAllowedToRetrieveManagedPassword AADDSCONTOSO-SERVER$
```

Aplikace a služby lze nyní nakonfigurovat tak, aby používaly gMSA podle potřeby.

## <a name="next-steps"></a>Další kroky

Další informace o gMSA najdete [v tématu Začínáme s účty spravovaných služeb skupiny][gmsa-start].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[create-custom-ou]: create-ou.md

<!-- EXTERNAL LINKS -->
[New-ADOrganizationalUnit]: /powershell/module/addsadministration/New-AdOrganizationalUnit
[New-ADServiceAccount]: /powershell/module/addsadministration/New-AdServiceAccount
[gmsa-overview]: /windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview
[gmsa-start]: /windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts
