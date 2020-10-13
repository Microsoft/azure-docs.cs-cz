---
title: Skupinové účty spravované služby pro Azure AD Domain Services | Microsoft Docs
description: Zjistěte, jak vytvořit skupinový účet spravované služby (gMSA) pro použití s Azure Active Directory Domain Services spravovanými doménami.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 723b8cf7a71e5af672c8665040645d55645fe9f7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87281850"
---
# <a name="create-a-group-managed-service-account-gmsa-in-azure-active-directory-domain-services"></a>Vytvoření skupinového účtu spravované služby (gMSA) v Azure Active Directory Domain Services

Aplikace a služby často potřebují identitu k ověřování pomocí jiných prostředků. Webová služba může například vyžadovat ověření pomocí databázové služby. Pokud má aplikace nebo služba více instancí, jako je například webová serverová farma, ruční vytvoření a Konfigurace identit pro tyto prostředky získá časově náročnou dobu.

Místo toho je možné vytvořit skupinový účet spravované služby (gMSA) ve spravované doméně služby Azure Active Directory Domain Services (Azure služba AD DS). OPERAČNÍ systém Windows automaticky spravuje přihlašovací údaje pro gMSA, což zjednodušuje správu velkých skupin prostředků.

V tomto článku se dozvíte, jak vytvořit gMSA ve spravované doméně pomocí Azure PowerShell.

## <a name="before-you-begin"></a>Než začnete

K dokončení tohoto článku potřebujete následující prostředky a oprávnění:

* Musíte mít aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
* Ve vašem tenantovi Azure AD je povolená a nakonfigurovaná spravovaná doména Azure Active Directory Domain Services.
    * V případě potřeby dokončete kurz a [vytvořte a nakonfigurujte Azure Active Directory Domain Services spravovanou doménu][create-azure-ad-ds-instance].
* Virtuální počítač pro správu Windows serveru, který je připojený k spravované doméně Azure služba AD DS.
    * V případě potřeby dokončete kurz a [vytvořte virtuální počítač pro správu][tutorial-create-management-vm].

## <a name="managed-service-accounts-overview"></a>Přehled účtů spravované služby

Samostatný účet spravované služby (sMSA) je doménový účet, jehož heslo se automaticky spravuje. Tento přístup zjednodušuje správu hlavního názvu služby (SPN) a umožňuje delegovanou správu jiným správcům. Přihlašovací údaje pro účet nemusíte vytvářet a střídat ručně.

Skupinový účet spravované služby (gMSA) poskytuje stejné zjednodušení správy, ale pro více serverů v doméně. GMSA umožňuje, aby všechny instance služby hostované na serverové farmě používaly stejný instanční objekt pro fungování protokolů pro vzájemné ověřování. Když se jako instanční objekt používá gMSA, operační systém Windows znovu spravuje heslo účtu, ale nespoléhá se na správce.

Další informace najdete v tématu [Přehled skupinových účtů spravované služby (gMSA)][gmsa-overview].

## <a name="using-service-accounts-in-azure-ad-ds"></a>Používání účtů služeb v Azure služba AD DS

Jelikož jsou spravované domény zamčené a spravované společností Microsoft, existují při používání účtů služeb několik důležitých informací:

* V části vlastní organizační jednotky (OU) ve spravované doméně vytvořte účty služeb.
    * Účet služby nemůžete vytvořit v předdefinovaných *AADDC uživatelích* nebo organizačních jednotkách *AADDC počítačů* .
    * Místo toho [vytvořte vlastní organizační jednotku][create-custom-ou] ve spravované doméně a potom vytvořte účty služby v této vlastní organizační jednotce.
* Kořenový klíč služby KDS (Key Distribution Services) je předem vytvořen.
    * Kořenový klíč KDS se používá ke generování a načítání hesel pro účty gMSA. V Azure služba AD DS se pro vás vytvoří kořen KDS.
    * Nemáte oprávnění k vytvoření jiné nebo zobrazení výchozího kořenového klíče KDS.

## <a name="create-a-gmsa"></a>Vytvoření gMSA

Nejdřív vytvořte vlastní organizační jednotku pomocí rutiny [New-ADOrganizationalUnit][New-AdOrganizationalUnit] . Další informace o vytváření a správě vlastních organizačních jednotek najdete v tématu [Custom ou v Azure služba AD DS][create-custom-ou].

> [!TIP]
> K vytvoření gMSA [použijte virtuální počítač pro správu][tutorial-create-management-vm]a proveďte tyto kroky. Tento virtuální počítač pro správu by už měl mít požadované rutiny služby AD PowerShell a připojení ke spravované doméně.

Následující příklad vytvoří vlastní organizační jednotku s názvem *myNewOU* ve spravované doméně s názvem *aaddscontoso.com*. Použijte vlastní organizační jednotku a název spravované domény:

```powershell
New-ADOrganizationalUnit -Name "myNewOU" -Path "DC=aaddscontoso,DC=COM"
```

Nyní vytvořte gMSA pomocí rutiny [New-ADServiceAccount][New-ADServiceAccount] . Jsou definovány následující příklady parametrů:

* **-Name** je nastavená na *WebFarmSvc* .
* Parametr **-path** určuje vlastní organizační jednotku pro gMSA vytvořené v předchozím kroku.
* Pro *WebFarmSvc.aaddscontoso.com* jsou nastavené položky DNS a hlavní názvy služeb.
* Objekty zabezpečení v *AADDSCONTOSO-Server $* smějí načíst heslo a používat identitu.

Zadejte vlastní názvy a názvy domén.

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

Aplikace a služby se teď dají nakonfigurovat tak, aby v případě potřeby používaly gMSA.

## <a name="next-steps"></a>Další kroky

Další informace o účty gMSA najdete v tématu [Začínáme se skupinovými účty spravované služby][gmsa-start].

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
