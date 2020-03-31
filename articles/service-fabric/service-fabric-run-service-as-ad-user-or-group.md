---
title: Spuštění služby Azure Service Fabric jako uživatele nebo skupiny služby AD
description: Zjistěte, jak spustit službu jako uživatel nebo skupina služby Active Directory v samostatném clusteru service fabric systému Windows.
author: dkkapur
ms.topic: conceptual
ms.date: 03/29/2018
ms.author: dekapur
ms.openlocfilehash: d440aadb66562e32331c9725a9367c12440a315d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464245"
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>Spuštění služby jako uživatele nebo skupiny služby Active Directory
V samostatném clusteru systému Windows Server můžete spustit službu jako uživatel nebo skupinu služby Active Directory pomocí zásad runas.  Ve výchozím nastavení service fabric aplikace spustit pod účtem, který Fabric.exe proces běží pod. Spouštění aplikací pod různými účty, a to i ve sdíleném hostovaném prostředí, je činí bezpečnějšími mezi sebou navzájem. Všimněte si, že to používá službu Active Directory místně v rámci vaší domény a ne Azure Active Directory (Azure AD).  Službu můžete také spustit jako [skupinový účet spravované služby (gMSA).](service-fabric-run-service-as-gmsa.md)

Pomocí uživatele domény nebo skupiny pak můžete přistupovat k dalším prostředkům v doméně (například ke sdíleným položkám souborů), kterým byla udělena oprávnění.

Následující příklad ukazuje uživatele služby Active Directory s názvem *TestUser* s heslem domény zašifrovaným pomocí certifikátu *mycert*. Příkaz `Invoke-ServiceFabricEncryptText` PowerShell můžete použít k vytvoření tajného šifrovacího textu. Podrobnosti [najdete v tématu Správa tajných kódů v aplikacích Service Fabric.](service-fabric-application-secret-management.md)

Musíte nasadit soukromý klíč certifikátu k dešifrování hesla do místního počítače pomocí metody out-of-band (v Azure je to přes Azure Resource Manager). Potom, když Service Fabric nasadí balíček služby do počítače, je schopen dešifrovat tajný klíč a (spolu s uživatelským jménem) ověřit pomocí služby Active Directory spustit pod těmito pověřeními.

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
> Pokud použijete zásadu RunAs pro službu a manifest služby deklaruje prostředky koncového bodu pomocí protokolu HTTP, musíte také zadat **SecurityAccessPolicy**.  Další informace naleznete [v tématu Přiřazení zásad přístupu k zabezpečení pro koncové body PROTOKOLU HTTP a HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Jako další krok si přečtěte následující články:
* [Principy aplikačního modelu](service-fabric-application-model.md)
* [Určení prostředků v manifestu služby](service-fabric-service-manifest-resources.md)
* [Nasazení aplikace](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
