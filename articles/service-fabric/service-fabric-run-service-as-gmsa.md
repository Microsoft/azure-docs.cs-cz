---
title: Spuštění služby Azure Service Fabric pod účtem gMSA
description: Zjistěte, jak spustit službu jako účet služby spravované skupinou (gMSA) v samostatném clusteru Service Fabric windows.
author: dkkapur
ms.topic: how-to
ms.date: 03/29/2018
ms.author: dekapur
ms.custom: sfrev
ms.openlocfilehash: 19343d370547cb5457f6bed70a8465187ff27102
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988392"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Spuštění služby jako skupinový účet spravované služby

V samostatném clusteru se systémem Windows Server můžete spustit službu jako *účet spravované služby skupiny* (gMSA) pomocí zásad *runas.*  Ve výchozím nastavení service fabric aplikace `Fabric.exe` spustit pod účtem, který je spuštěn pod procesem. Spouštění aplikací pod různými účty, a to i ve sdíleném hostovaném prostředí, je činí bezpečnějšími mezi sebou navzájem. Pomocí gMSA, není žádné heslo nebo šifrované heslo uložené v manifestu aplikace.  Službu můžete spustit také jako [uživatele nebo skupinu služby Active Directory](service-fabric-run-service-as-ad-user-or-group.md).

Následující příklad ukazuje, jak vytvořit účet gMSA s názvem *svc-Test$*, jak nasadit tento účet spravované služby do uzlů clusteru a jak nakonfigurovat uživatelský objekt.

> [!NOTE]
> Použití gMSA se samostatným clusterem Service Fabric vyžaduje službu Active Directory místně v rámci vaší domény (spíše než Azure Active Directory (Azure AD)).

Požadavky:

- Doména potřebuje kořenový klíč KDS.
- V doméně musí být alespoň jeden řadič domény systému Windows Server 2012 (nebo R2).

1. Nechte správce domény služby Active Directory vytvořit `New-ADServiceAccount` účet služby spravované `PrincipalsAllowedToRetrieveManagedPassword` skupinou pomocí rutiny a ujistěte se, že obsahuje všechny uzly clusteru Service Fabric. `AccountName`, `DnsHostName`a `ServicePrincipalName` musí být jedinečný.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. Na každém uzle clusteru Service `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`Fabric (například ) nainstalujte a otestujte gMSA.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Nakonfigurujte objekt `RunAsPolicy` User a nakonfigurujte odkaz na [uživatele](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-fabric-settings#runas).
    
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
> Pokud použijete zásadu RunAs pro službu a manifest služby deklaruje prostředky koncového bodu pomocí protokolu HTTP, je nutné zadat **SecurityAccessPolicy**.  Další informace naleznete [v tématu Přiřazení zásad přístupu k zabezpečení pro koncové body PROTOKOLU HTTP a HTTPS](service-fabric-assign-policy-to-endpoint.md).
>

Následující články vás provedou dalšími kroky:

- [Principy aplikačního modelu](service-fabric-application-model.md)
- [Určení prostředků v manifestu služby](service-fabric-service-manifest-resources.md)
- [Nasazení aplikace](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
