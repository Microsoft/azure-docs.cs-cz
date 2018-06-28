---
title: 'Azure Active Directory Domain Services: Vytvoření účtu služby spravované skupiny | Microsoft Docs'
description: Správa spravované domény Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: maheshu
ms.openlocfilehash: 4b4ce876760d024170020ae3faf618c7e9e76773
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035778"
---
# <a name="create-a-group-managed-service-account-gmsa-on-an-azure-ad-domain-services-managed-domain"></a>Vytvoření účtu skupiny spravované služby (gMSA) na spravované doméně služby Azure AD Domain Services
Tento článek ukazuje, jak vytvořit účty spravované služby na spravované doméně služby Azure AD Domain Services.

## <a name="managed-service-accounts"></a>Účty spravované služby
Spravovat samostatný účet služby (sMSA) je spravovaný účet domény jejichž heslo je automaticky spravované. Zjednodušuje správu (SPN) pro hlavní název služby, a umožňuje delegované správy na jiné správce. Tento typ účtu spravované služby (MSA) byla zavedená v systému Windows Server 2008 R2 a Windows 7.

Účet skupiny spravované služby (gMSA) poskytuje stejné výhody pro velký počet serverů v doméně. Všechny instance služby hostované na serverové farmě potřebovat objekt služby pro protokoly pro vzájemné ověřování pracovat. Když gMSA slouží jako instančního objektu, operační systém Windows spravuje heslo účtu, aniž byste museli spoléhat na správce.

**Další informace:**
- [Přehled účtů spravované služby skupiny](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [Začínáme s skupinové účty spravované služby](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)


## <a name="using-service-accounts-in-azure-ad-domain-services"></a>Použití účtů služby ve službě Azure AD Domain services
Spravované domény služby Azure AD Domain Services jsou uzamčené a spravován společností Microsoft. Existuje několik klíčových faktorů při použití účtů služby s Azure AD Domain Services.

### <a name="create-service-accounts-within-custom-organizational-units-ou-on-the-managed-domain"></a>Vytvoření účtů služby v rámci vlastní organizační jednotky (OU) ve spravované doméně
Nelze vytvořit účet služby v předdefinované 'AADDC uživatele' nebo 'AADDC počítačů, organizačních jednotek. [Vytvořit vlastní](active-directory-ds-admin-guide-create-ou.md) na vaší spravované domény a pak vytvořte účty služby v rámci dané vlastní organizační jednotce.

### <a name="the-key-distribution-services-kds-root-key-is-already-pre-created"></a>Kořenový klíč distribuční KDS klíčových služeb () je již předem vytvořené
Kořenový klíč distribuční KDS klíčových služeb () je předem vytvořené na spravované doméně služby Azure AD Domain Services. Nepotřebujete k vytvoření kořene KDS klíče a nemáte oprávnění udělat tak. Kořenového klíče KDS nelze zobrazit buď na spravované domény.

## <a name="sample---create-a-gmsa-using-powershell"></a>Ukázka – vytvoření gMSA pomocí prostředí PowerShell
Následující příklad ukazuje, jak vytvořit vlastní pomocí prostředí PowerShell. Potom můžete vytvořit gMSA v rámci dané organizační jednotce pomocí ```-Path``` parametr k určení organizační jednotku.

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

**Dokumentace rutiny prostředí PowerShell:**
- [Nové ADOrganizationalUnit rutiny](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit)
- [Rutiny nové-ADServiceAccount](https://docs.microsoft.com/powershell/module/addsadministration/New-ADServiceAccount)


## <a name="next-steps"></a>Další postup
- [Vytvořit vlastní na spravované doméně](active-directory-ds-admin-guide-create-ou.md)
- [Přehled účtů spravované služby skupiny](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [Začínáme s skupinové účty spravované služby](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)
