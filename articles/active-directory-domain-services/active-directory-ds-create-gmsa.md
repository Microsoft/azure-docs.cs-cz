---
title: 'Azure Active Directory Domain Services: Vytvoření účtu služby skupiny spravované | Dokumentace Microsoftu'
description: Správa spravované domény Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: ergreenl
ms.openlocfilehash: e477bd310f2d5ea2e216ba2e7630a375ea81c48c
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856736"
---
# <a name="create-a-group-managed-service-account-gmsa-on-an-azure-ad-domain-services-managed-domain"></a>Vytvoření účtu skupiny spravované služby (gMSA) ve spravované doméně Azure AD Domain Services
V tomto článku se dozvíte, jak vytvořit účty spravované služby ve spravované doméně Azure AD Domain Services.

## <a name="managed-service-accounts"></a>Účty spravované služby
Spravovat samostatný účet služby (sMSA) je spravovaný účet domény čí heslo se automaticky spravuje. Zjednodušuje správu služeb (SPN) pro hlavní název, a umožňuje delegované správy na jiné správce. Tento typ účtu spravované služby (MSA) byla zavedena v systému Windows Server 2008 R2 a Windows 7.

Účet skupiny spravované služby (gMSA) poskytuje stejné výhody pro mnoho serverů v doméně. Všechny instance služby hostované na serverové farmě muset použít stejné instančním objektu pro protokoly pro vzájemné ověřování pracovat. Pokud gMSA slouží jako instanční objekt, operační systém Windows spravuje heslo účtu, aniž byste museli spoléhat na správce.

**Další informace:**
- [Přehled účtů spravované služby skupiny](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [Začínáme s skupinových účtů spravované služby](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)


## <a name="using-service-accounts-in-azure-ad-domain-services"></a>Použití účtů služby ve službě Azure AD Domain services
Spravované domény služby Azure AD Domain Services je uzamčen a spravován společností Microsoft. Existuje několik klíčových faktorů při použití účtů služby Azure AD Domain Services.

### <a name="create-service-accounts-within-custom-organizational-units-ou-on-the-managed-domain"></a>Vytvořit účty služeb v rámci vlastní organizační jednotky (OU) ve spravované doméně
Nelze vytvořit účet služby v předdefinované "Uživatelé AADDC" nebo "Kontejnery počítače AADDC" organizačních jednotek. [Vytvoření vlastní organizační jednotky](active-directory-ds-admin-guide-create-ou.md) ve vaší spravované doméně a pak vytvořte účty služeb v rámci dané vlastní organizační jednotce.

### <a name="the-key-distribution-services-kds-root-key-is-already-pre-created"></a>Kořenový klíč distribuce KDS klíčových služeb () je už předem vytvořené
Kořenový klíč distribuce KDS klíčových služeb () se předem vytvořené ve spravované doméně Azure AD Domain Services. Není nutné k vytvoření KDS kořenového klíče a nemáte oprávnění udělat to. Kořenový klíč KDS nelze zobrazit buď ve spravované doméně.

## <a name="sample---create-a-gmsa-using-powershell"></a>Ukázka: vytvoření gMSA rutinou Powershellu
Následující příklad ukazuje, jak vytvořit vlastní organizační jednotky pomocí prostředí PowerShell. Potom můžete vytvořit gMSA v rámci dané organizační jednotce pomocí ```-Path``` parametr k určení organizační jednotku.

```powershell
# Create a new custom OU on the managed domain
New-ADOrganizationalUnit -Name "MyNewOU" -Path "DC=CONTOSO100,DC=COM"

# Create a service account 'WebFarmSvc' within the custom OU.
New-ADServiceAccount -Name WebFarmSvc  `
-DNSHostName ` WebFarmSvc.contoso100.com  `
-Path "OU=MYNEWOU,DC=CONTOSO100,DC=com"  `
-KerberosEncryptionType AES128, AES256  ` -ManagedPasswordIntervalInDays 30  `
-ServicePrincipalNames http/WebFarmSvc.contoso100.com/contoso100.com, `
http/WebFarmSvc.contoso100.com/contoso100,  `
http/WebFarmSvc/contoso100.com, http/WebFarmSvc/contoso100  `
-PrincipalsAllowedToRetrieveManagedPassword CONTOSO-SERVER$
```

**Dokumentace k rutinám prostředí PowerShell:**
- [Nové ADOrganizationalUnit rutiny](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit)
- [New-ADServiceAccount cmdlet](https://docs.microsoft.com/powershell/module/addsadministration/New-ADServiceAccount)


## <a name="next-steps"></a>Další postup
- [Vytvoření vlastní organizační jednotky ve spravované doméně](active-directory-ds-admin-guide-create-ou.md)
- [Přehled účtů spravované služby skupiny](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [Začínáme s skupinových účtů spravované služby](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)
