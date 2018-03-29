---
title: Spuštění služby Azure Service Fabric jako AD uživatele nebo skupiny | Microsoft Docs
description: Informace o spuštění služby jako uživatele služby Active Directory nebo skupiny na samostatný cluster služby Windows Fabric.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: 1cf23a8f564553e65ac2c0fd34d44d81fe2327ea
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>Spuštění služby jako uživatele služby Active Directory nebo skupiny
Pomocí Azure Service Fabric můžete zabezpečit aplikace, které jsou spuštěny v clusteru v rámci jiné uživatelské účty. Díky spuštěné aplikace, i v prostředí sdílené hostované bezpečnější od sebe navzájem. Ve výchozím nastavení se aplikace Service Fabric běžet pod účtem, proces Fabric.exe kompatibilní se. Pro samostatné cluster Windows Server, můžete spustit službu jako [skupinový účet spravované služby (gMSA)](service-fabric-run-service-as-gmsa.md) nebo uživatele služby Active Directory nebo skupiny pomocí zásad RunAs. Všimněte si, že se používá služby Active Directory místně v rámci domény a není Azure Active Directory (Azure AD).

Pomocí účtem uživatele domény nebo skupiny můžete pak přístup k jiným prostředkům v doméně (například sdílené složky), kterým bylo uděleno oprávnění.

Následující příklad ukazuje uživatele služby Active Directory názvem *TestUser* s jejich domény heslo šifrované pomocí certifikátu nazývá *MyCert*. Můžete použít `Invoke-ServiceFabricEncryptText` příkaz prostředí PowerShell k vytvoření tajný šifrovaný text. V tématu [Správa tajných klíčů v Service Fabric aplikace](service-fabric-application-secret-management.md) podrobnosti.

Je nutné nasadit privátní klíč certifikátu dešifrovat heslo k místnímu počítači pomocí metody out-of-band (v Azure, je to prostřednictvím Správce Azure Resource Manager). Potom při Service Fabric nasadí balíček služby k počítači, je možné dešifrovat tajný klíč a (spolu s uživatelské jméno) ověření pomocí služby Active Directory pro spuštění pod tyto přihlašovací údaje.

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
> Pokud použijete RunAs zásady pro služby a service manifest deklaruje koncový bod prostředků pomocí protokolu HTTP, je nutné také zadat **SecurityAccessPolicy**.  Další informace najdete v tématu [přiřadit zásady zabezpečení přístupu pro koncové body HTTP a HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Jako další krok přečtěte si následující články:
* [Pochopení aplikačního modelu](service-fabric-application-model.md)
* [Zadejte prostředky v service manifest](service-fabric-service-manifest-resources.md)
* [Nasazení aplikace](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
