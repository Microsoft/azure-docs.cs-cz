---
title: Spuštění služby Azure Service Fabric jako uživatele nebo skupiny AD
description: Naučte se, jak spustit službu jako uživatel nebo skupinu služby Active Directory v Service Fabric samostatném clusteru se systémem Windows.
ms.topic: conceptual
ms.date: 03/29/2018
ms.openlocfilehash: d4a7afc2ddb0f39014a7cf0fd006d7fe23673a95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91840723"
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>Spuštění služby jako uživatel nebo skupina služby Active Directory
V samostatném clusteru se systémem Windows Server můžete službu spustit jako uživatele nebo skupinu služby Active Directory pomocí zásad RunAs.  Ve výchozím nastavení Service Fabric aplikace běží pod účtem, pod kterým běží proces Fabric.exe. Spouštění aplikací v rámci různých účtů, i ve sdíleném hostovaném prostředí, je mezi sebou bezpečnější. Všimněte si, že se používá místní služba Active Directory v rámci vaší domény a ne Azure Active Directory (Azure AD).  Službu můžete také spustit jako [skupinový účet spravované služby (gMSA)](service-fabric-run-service-as-gmsa.md).

Pomocí uživatele nebo skupiny domény pak můžete přistupovat k dalším prostředkům v doméně (například ke sdíleným složkám souborů), kterým bylo uděleno oprávnění.

Následující příklad ukazuje uživatele služby Active Directory s názvem *testuser* s heslem v doméně zašifrovaným pomocí certifikátu s názvem *mycert*. `Invoke-ServiceFabricEncryptText`K vytvoření tajného šifrovacího textu můžete použít příkaz prostředí PowerShell. Podrobnosti najdete [v tématu Správa tajných klíčů v aplikacích Service Fabric](service-fabric-application-secret-management.md) .

Privátní klíč certifikátu je nutné nasadit k dešifrování hesla k místnímu počítači pomocí vzdálené metody (v Azure je to přes Azure Resource Manager). Poté, když Service Fabric nasadí balíček služby do počítače, je možné dešifrovat tajný klíč a (společně s uživatelským jménem) ověřit pomocí služby Active Directory, aby se pod těmito přihlašovacími údaji spouštěly.

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
> Pokud použijete zásadu RunAs na službu a manifest služby deklaruje prostředky koncového bodu s protokolem HTTP, je nutné zadat také **SecurityAccessPolicy**.  Další informace najdete v tématu [přiřazení zásad zabezpečení přístupu k koncovým bodům http a HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
V dalším kroku si přečtěte následující články:
* [Pochopení aplikačního modelu](service-fabric-application-model.md)
* [Určení prostředků v manifestu služby](service-fabric-service-manifest-resources.md)
* [Nasazení aplikace](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
