---
title: Spuštění služby Azure Service Fabric v rámci účtu gMSA
description: Naučte se, jak spustit službu jako účet spravované služby (gMSA) na samostatném clusteru s Service Fabric Windows.
ms.topic: how-to
ms.date: 03/29/2018
ms.openlocfilehash: 9750042764306c5df7a391429cc6926704db05ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91838904"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Spuštění služby jako skupinový účet spravované služby

V samostatném clusteru se systémem Windows Server můžete službu spustit jako *skupinový účet spravované služby* (gMSA) pomocí zásad *runas* .  Ve výchozím nastavení Service Fabric aplikace spouštěny pod účtem, `Fabric.exe` pod kterým proces běží. Spouštění aplikací v rámci různých účtů, i ve sdíleném hostovaném prostředí, je mezi sebou bezpečnější. Pomocí gMSA není v manifestu aplikace uloženo žádné heslo ani šifrované heslo.  Službu můžete spustit také jako [uživatel nebo skupina služby Active Directory](service-fabric-run-service-as-ad-user-or-group.md).

Následující příklad ukazuje, jak vytvořit účet gMSA s názvem *svc-test $*, jak nasadit tento účet spravované služby na uzly clusteru a jak nakonfigurovat objekt zabezpečení uživatele.

> [!NOTE]
> Použití gMSA se samostatným clusterem Service Fabric vyžaduje místní službu Active Directory v rámci vaší domény (místo Azure Active Directory (Azure AD)).

Požadavky:

- Doména potřebuje kořenový klíč KDS.
- V doméně musí být alespoň jeden řadič domény se systémem Windows Server 2012 (nebo R2).

1. Správce domény služby Active Directory vytvoří pomocí rutiny účet spravované služby skupiny `New-ADServiceAccount` a zajistěte, aby `PrincipalsAllowedToRetrieveManagedPassword` zahrnoval všechny uzly Service Fabric clusteru. `AccountName`, `DnsHostName` , a `ServicePrincipalName` musí být jedinečné.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. Na všech uzlech Service Fabric clusteru (například `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$` ) nainstalujte a otestujte gMSA.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Nakonfigurujte objekt zabezpečení uživatele a nakonfigurujte `RunAsPolicy` ho tak, aby odkazoval na [uživatele](./service-fabric-cluster-fabric-settings.md#runas).
    
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

Následující články vás provedou dalšími kroky:

- [Pochopení aplikačního modelu](service-fabric-application-model.md)
- [Určení prostředků v manifestu služby](service-fabric-service-manifest-resources.md)
- [Nasazení aplikace](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
