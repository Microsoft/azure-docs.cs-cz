---
title: Spuštění služby Azure Service Fabric pod účtem gMSA. | Microsoft Docs
description: Informace o spuštění služby jako gMSA na samostatný cluster služby Windows Fabric.
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
ms.date: 03/29/2018
ms.author: mfussell
ms.openlocfilehash: e22c656218abcd0564faec6fae6d6979f09b386a
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Spuštění služby jako skupinový účet spravované služby
Na samostatné clusteru Windows Server můžete spustit službu jako skupina pomocí zásad RunAs účet spravované služby (gMSA).  Ve výchozím nastavení se aplikace Service Fabric běžet pod účtem, proces Fabric.exe kompatibilní se. Spouštění aplikací pod různými účty, i v sdílené hostovaném prostředí, je díky bezpečnější od sebe navzájem. Všimněte si, že se používá služby Active Directory místně v rámci domény a není Azure Active Directory (Azure AD). Pomocí gMSA neexistuje žádné heslo nebo zašifrované heslo, které jsou uložené v manifestu aplikace.  Můžete taky spustit služba jako [uživatele služby Active Directory nebo skupiny](service-fabric-run-service-as-ad-user-or-group.md).

Následující příklad ukazuje, jak vytvořit účet gMSA s názvem *svc Test$*; pro nasazení tohoto účtu spravované služby pro uzly clusteru; a jak nakonfigurovat hlavní název uživatele.

Předpoklady:
- Doména musí kořenový klíč služby KDS.
- Musí být v systému Windows Server 2012 nebo novější úroveň funkčnosti domény.

1. Požádejte správce domény služby Active Directory, vytvoření účtu služby skupina spravované pomocí `New-ADServiceAccount` příkaz a ujistěte se, že `PrincipalsAllowedToRetrieveManagedPassword` zahrnuje všechny uzly clusteru service fabric. `AccountName`, `DnsHostName`, a `ServicePrincipalName` musí být jedinečný.

    ```poweshell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. Uzly clusteru na každém Service Fabric (například `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`), instalace a testování gMSA.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Nakonfigurovat hlavní název uživatele a nakonfigurovat RunAsPolicy tak, aby odkazovaly uživatele.
    
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
> Pokud použijete RunAs zásady pro služby a service manifest deklaruje koncový bod prostředků pomocí protokolu HTTP, je nutné zadat **SecurityAccessPolicy**.  Další informace najdete v tématu [přiřadit zásady zabezpečení přístupu pro koncové body HTTP a HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Jako další krok přečtěte si následující články:
* [Pochopení aplikačního modelu](service-fabric-application-model.md)
* [Zadejte prostředky v service manifest](service-fabric-service-manifest-resources.md)
* [Nasazení aplikace](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
