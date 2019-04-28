---
title: Spouštěna systém a zabezpečení místních účtů služby Azure Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak spustit aplikaci Service Fabric v části systém a zabezpečení místních účtů.  Vytvoření objektů zabezpečení a použití zásad spustit jako pro bezpečné spuštění služby.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: aljo
ms.openlocfilehash: 28cd1162d7cae2b3a16062bdf18a2971e1f05aad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621169"
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Spuštění služby jako místní uživatelský účet nebo účet místního systému
Pomocí Azure Service Fabric můžete zabezpečit aplikace, které jsou spuštěny v clusteru pod různými uživatelskými účty. Ve výchozím nastavení aplikace Service Fabric spustit pod účtem, který spouští proces Fabric.exe pod. Service Fabric nabízí také možnost spouštět aplikace v rámci místní uživatel nebo systémový účet. Typy účtů podporovaný místní systém **LocalUser**, **NetworkService**, **LocalService**, a **LocalSystem**.  Pokud používáte na Windows samostatného clusteru Service Fabric, můžete spustit služby v rámci [doménových účtů služby Active Directory](service-fabric-run-service-as-ad-user-or-group.md) nebo [skupiny spravované účty služeb](service-fabric-run-service-as-gmsa.md).

V manifestu aplikace můžete definovat uživatelských účtů nezbytných pro spuštění služby nebo zabezpečeným prostředkům v **objekty zabezpečení** oddílu. Můžete také definovat a vytvořit skupiny uživatelů, tak, aby jeden nebo více uživatelů je možné spravovat pohromadě. To je užitečné, pokud existuje více uživatelů pro jiné služby vstupní body a potřebují oprávnění zabezpečení, které jsou k dispozici na úrovni skupiny.  Uživatelé se pak odkazuje v zásadách spustit jako, které platí pro konkrétní službu nebo všech služeb v aplikaci. 

Ve výchozím nastavení je RunAs zásady použijí pro hlavní vstupní bod.  Můžete také použít zásady RunAs do vstupního bodu nastavení, pokud je potřeba [spouštět určité operace instalace s vysokou úrovní oprávnění pod účtem systému](service-fabric-run-script-at-service-startup.md), nebo oba hlavní a nastavit vstupní body.  

> [!NOTE] 
> Pokud použijete zásady RunAs na službu a manifest služby deklaruje koncový bod prostředků pomocí protokolu HTTP, je nutné zadat **SecurityAccessPolicy**.  Další informace najdete v tématu [přiřazení zásad zabezpečení přístupu pro koncové body HTTP a HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

## <a name="run-a-service-as-a-local-user"></a>Spuštění služby jako místní uživatel
Můžete vytvořit místní uživatel, který slouží k zabezpečení služby v rámci aplikace. Když **LocalUser** typ účtu je definováno v sekci objekty zabezpečení v manifestu aplikace Service Fabric vytvoří místní uživatelské účty na počítačích, ve kterém je aplikace nasazená. Ve výchozím nastavení, nemají tyto účty stejné názvy jako ty určená v manifestu aplikace (například *Customer3* v následujícím příkladu manifestu aplikace). Místo toho jsou dynamicky generované a mít náhodná hesla.

V **RunAsPolicy** v části **ServiceManifestImport**, zadejte uživatelský účet z **objekty zabezpečení** části spustit balíček kódu služby.  Následující příklad ukazuje, jak vytvořit místní uživatele a použít zásady RunAs na hlavní vstupní bod:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
Můžete vytvořit skupiny uživatelů a přidejte jeden nebo více uživatelů do skupiny. To je užitečné, pokud existuje více uživatelů pro jiné služby vstupní body a potřebují mít určitá oprávnění zabezpečení, které jsou k dispozici na úrovni skupiny. V následujícím příkladu manifestu aplikace se zobrazí místní skupina s názvem *LocalAdminGroup* , který má oprávnění správce. Dva uživatele *Customer1* a *Customer2*, jsou členy místní skupiny. V **ServiceManifestImport** části RunAs zásad se použije ke spuštění *Stateful1Pkg* balíček kódu jako *Customer2*.  Jiné zásady Spustit jako se použije ke spuštění *Web1Pkg* balíček kódu jako *Customer1*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
Můžete použít **DefaultRunAsPolicy** pro zadání výchozí uživatelský účet pro veškerý kód balíčky, které nemají konkrétní **RunAsPolicy** definované. Pokud většinu balíčky kódu, které jsou určené v manifestu služby používaný aplikací k nutné ke spuštění v rámci stejného uživatele, aplikace stačí definovat výchozí zásady RunAs k tomuto účtu. Následující příklad určuje, že pokud balíček kódu nemá **RunAsPolicy** zadán, balíček kódu má být spuštěna pod **MyDefaultAccount** definováno v sekci objekty zabezpečení uživatele.  Typy podporovaných účtů jsou LocalUser NetworkService, LocalSystem a LocalService.  Pokud používáte místní uživatele nebo službu, zadejte také název účtu a heslo.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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

## <a name="debug-a-code-package-locally-using-console-redirection"></a>Ladit místně pomocí přesměrování konzoly balíček kódu
V některých případech je užitečné pro účely ladění, pokud chcete zobrazit výstup konzoly ze spuštěné služby. Můžete nastavit zásadu přesměrování konzoly pro vstupní bod v manifestu služby, který zapíše výstup do souboru. Výstupní soubor je zapsána do aplikace složku s názvem **protokolu** na uzlu clusteru, kde nasazení a spuštění aplikace. 

> [!WARNING]
> Nikdy nepoužívejte zásady přesměrování konzoly v aplikaci, která je nasazená v produkčním prostředí, protože to může mít vliv převzetí služeb při selhání aplikace. *Pouze* používá se pro místní vývoj a ladění.  
> 
> 

Povolit přesměrování konzoly s hodnotou FileRetentionCount ukazuje příklad manifestu následující služby:

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
* [Zadání prostředků v manifestu služby](service-fabric-service-manifest-resources.md)
* [Nasazení aplikace](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
