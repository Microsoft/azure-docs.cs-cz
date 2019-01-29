---
title: Spuštění služby Azure Service Fabric v rámci účtu gMSA | Dokumentace Microsoftu
description: Informace o spuštění služby jako gMSA na samostatný cluster Service Fabric Windows.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: mfussell
ms.openlocfilehash: 56a7478ab7221a1ccb4394a245540d3181e4ad8e
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55155574"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Spuštění služby jako skupinový účet spravované služby
Na samostatného clusteru Windows Server můžete spustit službu jako skupinu pomocí zásad spustit jako účtu spravované služby (gMSA).  Ve výchozím nastavení aplikace Service Fabric spustit pod účtem, který spouští proces Fabric.exe pod. Spouštění aplikací pomocí jiných účtů, dokonce i ve sdíleném prostředí prostředí, je mezi nimi vlastně bezpečnější od sebe. Všimněte si, že to používá služby Active Directory místně v rámci vaší domény a ne Azure Active Directory (Azure AD). Pomocí gMSA není žádné heslo nebo šifrované heslo, které jsou uloženy v manifestu aplikace.  Můžete také spustit službu jako [uživatele služby Active Directory nebo skupiny](service-fabric-run-service-as-ad-user-or-group.md).

Následující příklad ukazuje, jak vytvořit účet gMSA s názvem *svc Test$*; jak nasadit tento spravovaný účet služby na uzlech clusteru a jak nakonfigurovat hlavní název uživatele.

Předpoklady:
- Doména musí kořenový klíč KDS.
- Musí být v systému Windows Server 2012 nebo vyšší úroveň funkčnosti domény.

1. Jste správcem domény služby Active Directory vytvořit pomocí účtu skupiny spravované `New-ADServiceAccount` rutiny a ujistěte se, že `PrincipalsAllowedToRetrieveManagedPassword` zahrnuje všechny uzly clusteru service fabric. `AccountName`, `DnsHostName`, a `ServicePrincipalName` musí být jedinečný.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. Uzly clusteru na každém Service Fabric (například `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`), nainstalovat a otestovat gMSA.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Nakonfigurovat hlavní název uživatele a nakonfigurovat zásady RunAsPolicy tak, aby odkazovaly uživatele.
    
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
          <ConfigOverrides />
          <Policies>
              <RunAsPolicy CodePackageRef="Code" UserRef="DomaingMSA"/>
          </Policies>
        </ServiceManifestImport>
      <Principals>
        <Users>
          <User Name="DomaingMSA" AccountType="ManagedServiceAccount" AccountName="domain\svc-Test$"/>
        </Users>
      </Principals>
    </ApplicationManifest>
    ```

> [!NOTE] 
> Pokud použijete zásady RunAs na službu a manifest služby deklaruje koncový bod prostředků pomocí protokolu HTTP, je nutné zadat **SecurityAccessPolicy**.  Další informace najdete v tématu [přiřazení zásad zabezpečení přístupu pro koncové body HTTP a HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged--> V dalším kroku přečtěte si následující články:
* [Pochopení aplikačního modelu](service-fabric-application-model.md)
* [Zadání prostředků v manifestu služby](service-fabric-service-manifest-resources.md)
* [Nasazení aplikace](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
