---
title: Spuštění služby Azure Service Fabric v rámci účtu gMSA
description: Naučte se spouštět službu jako gMSA na samostatném clusteru Service Fabric se systémem Windows.
author: dkkapur
ms.topic: conceptual
ms.date: 03/29/2018
ms.author: dekapur
ms.openlocfilehash: 99d8089bd12d05e46f91e55c933d58d50baa92f5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464270"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Spuštění služby jako skupinový účet spravované služby
V samostatném clusteru se systémem Windows Server můžete službu spustit jako skupinový účet spravované služby (gMSA) pomocí zásad RunAs.  Ve výchozím nastavení Service Fabric aplikace běží pod účtem, pod kterým běží proces Fabric. exe. Spouštění aplikací v rámci různých účtů, i ve sdíleném hostovaném prostředí, je mezi sebou bezpečnější. Všimněte si, že se používá místní služba Active Directory v rámci vaší domény a ne Azure Active Directory (Azure AD). Pomocí gMSA není v manifestu aplikace uloženo žádné heslo ani šifrované heslo.  Službu můžete spustit také jako [uživatel nebo skupina služby Active Directory](service-fabric-run-service-as-ad-user-or-group.md).

Následující příklad ukazuje, jak vytvořit účet gMSA s názvem *svc-test $* ; Jak nasadit tento účet spravované služby na uzly clusteru; a jak nakonfigurovat objekt zabezpečení uživatele.

Požadavky:
- Doména potřebuje kořenový klíč KDS.
- V doméně musí být alespoň jeden řadič domény se systémem Windows Server 2012 (nebo R2).

1. Správce domény služby Active Directory vytvoří skupinový účet spravované služby pomocí `New-ADServiceAccount` rutiny a zajistěte, aby `PrincipalsAllowedToRetrieveManagedPassword` zahrnoval všechny uzly clusteru Service Fabric. `AccountName`, `DnsHostName`a `ServicePrincipalName` musí být jedinečné.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. Na každém Service Fabric uzlech clusteru (například `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`) nainstalujte a otestujte gMSA.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Nakonfigurujte objekt zabezpečení uživatele a nakonfigurujte RunAsPolicy tak, aby odkazoval na uživatele.
    
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
> Použijete-li zásadu RunAs na službu a manifest služby deklaruje prostředky koncového bodu s protokolem HTTP, je nutné zadat **SecurityAccessPolicy**.  Další informace najdete v tématu [přiřazení zásad zabezpečení přístupu k koncovým bodům http a HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
V dalším kroku si přečtěte následující články:
* [Pochopení aplikačního modelu](service-fabric-application-model.md)
* [Určení prostředků v manifestu služby](service-fabric-service-manifest-resources.md)
* [Nasazení aplikace](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
