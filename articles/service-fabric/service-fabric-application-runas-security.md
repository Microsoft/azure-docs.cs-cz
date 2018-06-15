---
title: Spuštění služby Azure Service Fabric v rámci systému a účtů místní zabezpečení | Microsoft Docs
description: Zjistěte, jak spouštět aplikace Service Fabric v systému a účtů místní zabezpečení.  Vytvořit objekty zabezpečení a použití zásady Spustit jako k vašim službám bezpečně spustit.
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
ms.openlocfilehash: 33ca23834f35e631c6943ec22a88f4fe3dc853e1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212394"
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Spuštění služby jako účet místního uživatele nebo místní systémový účet
Pomocí Azure Service Fabric můžete zabezpečit aplikace, které jsou spuštěny v clusteru v rámci jiné uživatelské účty. Ve výchozím nastavení se aplikace Service Fabric běžet pod účtem, proces Fabric.exe kompatibilní se. Service Fabric taky poskytuje možnost spouštět aplikace pod účtem místního uživatele nebo systému. Typy účtů podporovaný místní systém **LocalUser**, **NetworkService**, **LocalService**, a **LocalSystem**.  Pokud používáte Service Fabric na samostatné clusteru se systémem Windows, můžete spustit službu pod [účtů domény služby Active Directory](service-fabric-run-service-as-ad-user-or-group.md) nebo [skupiny účty spravované služby](service-fabric-run-service-as-gmsa.md).

V manifestu aplikace, můžete definovat uživatelských účtů nezbytných pro spuštění služby nebo zabezpečeným prostředkům v **objekty** části. Můžete také definovat a vytvořit skupiny uživatelů, aby jeden nebo více uživatelů je možné spravovat společně. To je užitečné, pokud existuje více uživatelů pro různé služby vstupní body a potřebují běžné oprávnění, které jsou k dispozici na úrovni skupiny.  Uživatele se pak odkazuje v RunAs zásady, které se použije pro konkrétní službu nebo všechny služby v aplikaci. 

Ve výchozím nastavení je RunAs zásady použijí pro hlavní vstupní bod.  Můžete taky použít zásady RunAs vstupnímu bodu instalační program, pokud potřebujete [spustit určité operace instalace s vysokou úrovní oprávnění v rámci účtu system](service-fabric-run-script-at-service-startup.md), nebo oba hlavní a instalační program vstupní body.  

> [!NOTE] 
> Pokud použijete RunAs zásady pro služby a service manifest deklaruje koncový bod prostředků pomocí protokolu HTTP, je nutné zadat **SecurityAccessPolicy**.  Další informace najdete v tématu [přiřadit zásady zabezpečení přístupu pro koncové body HTTP a HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

## <a name="run-a-service-as-a-local-user"></a>Spuštění služby jako místní uživatel
Můžete vytvořit místní uživatele, který slouží k zajištění služby v rámci aplikace. Když **LocalUser** typ účtu je definováno v sekci objekty manifestu aplikace Service Fabric vytvoří místní uživatelské účty na počítačích, kde je aplikace nasazená. Ve výchozím nastavení, tyto účty nemají stejné názvy jako platformám zadaným v manifest aplikace (například *Customer3* v následujícím příkladu manifestu aplikace). Místo toho se dynamicky generují a mít náhodných hesla.

V **RunAsPolicy** část **ServiceManifestImport**, určete uživatelský účet z **objekty** část spusťte balíček kódu služby.  Následující příklad ukazuje, jak vytvořit místní uživatele a použít RunAs zásad pro hlavní vstupní bod:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="Customer3" />
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="create-a-local-user-group"></a>Vytvořte skupinu místního uživatele
Můžete vytvořit skupiny uživatelů a přidejte jeden nebo více uživatelů do skupiny. To je užitečné, pokud existuje více uživatelů pro různé služby vstupní body a vyžadují, aby byla určité společné oprávnění, které jsou k dispozici na úrovni skupiny. Následující příklad manifestu aplikace ukazuje místní skupina s názvem *LocalAdminGroup* s oprávněními správce. Dva uživatelé *Customer1* a *Customer2*, stanou členy této místní skupiny. V **ServiceManifestImport** části RunAs, zásady se použijí ke spuštění *Stateful1Pkg* balíček kódu jako *Customer2*.  Jiné zásady RunAs se použije ke spuštění *Web1Pkg* balíček kódu jako *Customer1*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer2" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="Stateful1" ServicePackageActivationMode="ExclusiveProcess">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Groups>
      <Group Name="LocalAdminGroup">
        <Membership>
          <SystemGroup Name="Administrators" />
        </Membership>
      </Group>
    </Groups>
    <Users>
      <User Name="Customer1">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <User Name="Customer2">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Použít výchozí zásady na všechny balíčky kódu služby
Můžete použít **DefaultRunAsPolicy** části zadejte výchozí účet uživatele pro všechny kód balíčky, které nemají konkrétní **RunAsPolicy** definované. Pokud většinu kódu balíčky, které jsou určené v service manifest používaný aplikací k potřebovali pro spuštění pod stejného uživatele, můžete aplikaci právě definovat výchozí zásady RunAs se s uživatelským účtem. Následující příklad určuje, že pokud balíček kódu neobsahuje **RunAsPolicy** zadán, balíček kódu by měl běžet pod **MyDefaultAccount** uživatele zadané v části objekty zabezpečení.  Typy podporované účtů jsou LocalUser, NetworkService, LocalSystem a LocalService.  Pokud používáte místního uživatele nebo služby, také zadejte název účtu a heslo.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <Policies>
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
  </Policies>
</ApplicationManifest>
```

## <a name="debug-a-code-package-locally-using-console-redirection"></a>Ladění kódu balíčku místně pomocí přesměrování konzoly
V některých případech je užitečná pro účely ladění zobrazíte výstup konzoly z spuštěné služby. Můžete nastavit zásadu konzoly přesměrování na vstupní bod v manifestu služby, který zapíše výstup do souboru. Soubor výstup zapsán do aplikace složku s názvem **protokolu** na uzlu clusteru, kde je aplikace nasazena a spustit. 

> [!WARNING]
> Nikdy nepoužívejte konzolu Zásady přesměrování v aplikaci, která je nasazena v produkčním prostředí, protože to může mít vliv převzetí služeb při selhání aplikaci. *Pouze* používejte pro místní vývoj a účely ladění.  
> 
> 

Následující služby manifest příklad ukazuje povolení přesměrování konzoly s hodnotou FileRetentionCount:

```xml
<CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
        <ConsoleRedirection FileRetentionCount="10"/>
      </ExeHost>
    </EntryPoint>
</CodePackage>

```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další postup
* [Pochopení aplikačního modelu](service-fabric-application-model.md)
* [Zadejte prostředky v service manifest](service-fabric-service-manifest-resources.md)
* [Nasazení aplikace](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
