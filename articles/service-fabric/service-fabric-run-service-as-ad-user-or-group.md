---
title: Spuštění služby Azure Service Fabric jako skupina nebo uživatel AD | Dokumentace Microsoftu
description: Zjistěte, jak spustit službu jako uživatele služby Active Directory nebo skupiny na samostatný cluster Service Fabric Windows.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: dekapur
ms.openlocfilehash: 3e0bb62609f13430bd2beab2332a31983874eb8e
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664739"
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>Spuštění služby jako uživatele služby Active Directory nebo skupiny
Na samostatného clusteru Windows Server můžete spustit službu jako uživatele služby Active Directory nebo skupiny, pomocí RunAs zásad.  Ve výchozím nastavení aplikace Service Fabric spustit pod účtem, který spouští proces Fabric.exe pod. Spouštění aplikací pomocí jiných účtů, dokonce i ve sdíleném prostředí prostředí, je mezi nimi vlastně bezpečnější od sebe. Všimněte si, že to používá služby Active Directory místně v rámci vaší domény a ne Azure Active Directory (Azure AD).  Můžete také spustit službu jako [skupinový účet spravované služby (gMSA)](service-fabric-run-service-as-gmsa.md).

S použitím účtem uživatele domény nebo skupiny, pak dostanete další prostředky v doméně (například sdílené složky), které máte oprávnění.

Následující příklad ukazuje uživatele služby Active Directory volá *TestUser* s jejich domény heslo, které jsou šifrované pomocí certifikátu nazývá *MyCert*. Můžete použít `Invoke-ServiceFabricEncryptText` příkaz prostředí PowerShell pro vytvoření tajných kódů šifrovaného textu. Zobrazit [správu tajných kódů aplikace Service Fabric](service-fabric-application-secret-management.md) podrobnosti.

Je nutné nasadit privátní klíč certifikátu k dešifrování hesla do místního počítače pomocí metody out-of-band (v Azure, je to prostřednictvím Azure Resource Manageru). Potom když Service Fabric nasadí balíček služby k počítači, je možné dešifrovat tajný kód a (spolu s uživatelské jméno) proveďte ověření pomocí služby Active Directory pro spuštění v rámci své přihlašovací údaje.

```xml
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
```

> [!NOTE] 
> Pokud použijete zásady RunAs na službu a manifest služby deklaruje koncový bod prostředků pomocí protokolu HTTP, musíte zadat také **SecurityAccessPolicy**.  Další informace najdete v tématu [přiřazení zásad zabezpečení přístupu pro koncové body HTTP a HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
V dalším kroku přečtěte si následující články:
* [Pochopení aplikačního modelu](service-fabric-application-model.md)
* [Zadání prostředků v manifestu služby](service-fabric-service-manifest-resources.md)
* [Nasazení aplikace](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
