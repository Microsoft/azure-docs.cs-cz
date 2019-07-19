---
title: 'Azure Active Directory Domain Services: Vytvoření skupinového účtu spravované služby | Microsoft Docs'
description: Zjistěte, jak vytvořit skupinový účet spravované služby (gMSA) pro použití s Azure Active Directory Domain Services spravovanými doménami.
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: iainfou
ms.openlocfilehash: 404160c895a8d0a72921fe202adba82c3d069aaf
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234115"
---
# <a name="create-a-group-managed-service-account-gmsa-on-an-azure-ad-domain-services-managed-domain"></a>Vytvoření skupinového účtu spravované služby (gMSA) ve spravované doméně Azure AD Domain Services
V tomto článku se dozvíte, jak vytvořit účty spravované služby ve spravované doméně Azure AD Domain Services.

## <a name="managed-service-accounts"></a>Účty spravované služby
Samostatný účet spravované služby (sMSA) je účet spravované domény, jehož heslo se automaticky spravuje. Zjednodušuje správu hlavního názvu služby (SPN) a umožňuje delegovanou správu jiným správcům. Tento typ účtu spravované služby (MSA) se zavedl v systémech Windows Server 2008 R2 a Windows 7.

Účet spravované služby (gMSA) pro skupinu nabízí stejné výhody pro mnoho serverů v doméně. Všechny instance služby hostované na serverové farmě musí používat stejný instanční objekt pro fungování vzájemného ověřování protokolů. Když se jako instanční objekt používá gMSA, operační systém Windows spravuje heslo účtu, ale nespoléhá se na správce.

**Další informace:**
- [Přehled skupinových účtů spravované služby](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [Začínáme se skupinovými účty spravované služby](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)


## <a name="using-service-accounts-in-azure-ad-domain-services"></a>Používání účtů služeb ve službě Azure AD Domain Services
Azure AD Domain Services spravované domény jsou zamčené a spravované Microsoftem. Při použití účtů služeb s Azure AD Domain Services existuje několik důležitých doporučení.

### <a name="create-service-accounts-within-custom-organizational-units-ou-on-the-managed-domain"></a>Vytváření účtů služeb v rámci vlastních organizačních jednotek (OU) ve spravované doméně
Účet služby nemůžete vytvořit v předdefinovaných organizačních jednotkách AADDC Users nebo AADDC Computers. [Vytvořte vlastní organizační jednotku](create-ou.md) ve spravované doméně a pak vytvořte účty služeb v rámci této vlastní organizační jednotky.

### <a name="the-key-distribution-services-kds-root-key-is-already-pre-created"></a>Kořenový klíč služby KDS (Key Distribution Services) je již předem vytvořen.
Kořenový klíč služby KDS (Key Distribution Services) je předem vytvořen ve spravované doméně Azure AD Domain Services. Nemusíte vytvářet kořenový klíč KDS a nemáte k tomu oprávnění. Kořenový klíč KDS se ve spravované doméně nedá zobrazit ani jedna z nich.

## <a name="sample---create-a-gmsa-using-powershell"></a>Ukázka – vytvoření gMSA pomocí prostředí PowerShell
Následující příklad ukazuje, jak vytvořit vlastní organizační jednotku pomocí prostředí PowerShell. Pak můžete vytvořit gMSA v rámci této organizační jednotky pomocí ```-Path``` parametru pro určení organizační jednotky.

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
- [Rutina New-ADOrganizationalUnit](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit)
- [Rutina New-ADServiceAccount](https://docs.microsoft.com/powershell/module/addsadministration/New-ADServiceAccount)


## <a name="next-steps"></a>Další kroky
- [Vytvoření vlastní organizační jednotky ve spravované doméně](create-ou.md)
- [Přehled skupinových účtů spravované služby](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [Začínáme se skupinovými účty spravované služby](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)
