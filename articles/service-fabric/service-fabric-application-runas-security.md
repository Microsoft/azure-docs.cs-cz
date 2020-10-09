---
title: Spuštění služby pod systémovými a místními účty zabezpečení
description: Naučte se, jak spustit aplikaci Service Fabric v části účty System a Local Security.  Vytvořte objekty zabezpečení a použijte zásady Run-As pro bezpečné spouštění služeb.
ms.topic: conceptual
ms.date: 03/29/2018
ms.openlocfilehash: 53212f8636602705899834b6db1d3f0d80b5fe4f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75610113"
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Spusťte službu jako místní uživatelský účet nebo účet místního systému.
Pomocí Azure Service Fabric můžete zabezpečit aplikace spuštěné v clusteru v rámci různých uživatelských účtů. Ve výchozím nastavení Service Fabric aplikace běží pod účtem, pod kterým běží proces Fabric.exe. Service Fabric taky nabízí možnost spouštět aplikace pod účtem místního uživatele nebo systému. Podporovány jsou typy místních systémových účtů **LocalUser**, **NetworkService**, **LocalService**a **LocalSystem**.  Pokud používáte Service Fabric v samostatném clusteru se systémem Windows, můžete spustit službu pod [účtem domény služby Active Directory](service-fabric-run-service-as-ad-user-or-group.md) nebo [skupinovými účty spravované služby](service-fabric-run-service-as-gmsa.md).

V manifestu aplikace definujete uživatelské účty potřebné ke spouštění služeb nebo zabezpečených prostředků v oddílu **objekty zabezpečení** . Můžete také definovat a vytvářet skupiny uživatelů, aby bylo možné spravovat jednoho nebo více uživatelů současně. To je užitečné v případě, že existuje více uživatelů pro různé vstupní body služby a potřebují společná oprávnění, která jsou k dispozici na úrovni skupiny.  Na tyto uživatele se pak odkazuje v zásadách RunAs, které se aplikují na konkrétní službu nebo všechny služby v aplikaci. 

Ve výchozím nastavení se zásada RunAs aplikuje na hlavní vstupní bod.  Můžete také použít zásadu RunAs na vstupní bod instalace, pokud potřebujete [Spustit určité operace s nastavením vysokého oprávnění pod účtem systému](service-fabric-run-script-at-service-startup.md)nebo hlavní a vstupní body nastavení.  

> [!NOTE] 
> Použijete-li zásadu RunAs na službu a manifest služby deklaruje prostředky koncového bodu s protokolem HTTP, je nutné zadat **SecurityAccessPolicy**.  Další informace najdete v tématu [přiřazení zásad zabezpečení přístupu k koncovým bodům http a HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

## <a name="run-a-service-as-a-local-user"></a>Spustit službu jako místní uživatel
Můžete vytvořit místního uživatele, který lze použít k zabezpečení služby v rámci aplikace. Pokud je v oddílu objekty zabezpečení v manifestu aplikace zadán typ účtu **LocalUser** , Service Fabric vytvoří místní uživatelské účty na počítačích, kde je aplikace nasazena. Ve výchozím nastavení tyto účty nemají stejné názvy jako ty, které jsou zadány v manifestu aplikace (například *Customer3* v následujícím příkladu manifestu aplikace). Místo toho se generují dynamicky a mají náhodná hesla.

V části **RunAsPolicy** pro **ServiceManifestImport**zadejte uživatelský účet z oddílu **objekty zabezpečení** a spusťte balíček kódu služby.  Následující příklad ukazuje, jak vytvořit místního uživatele a použít zásadu RunAs na hlavní vstupní bod:

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

## <a name="create-a-local-user-group"></a>Vytvořit místní skupinu uživatelů
Skupiny uživatelů můžete vytvořit a přidat do ní jednoho nebo více uživatelů. To je užitečné v případě, že existuje více uživatelů pro různé vstupní body služby a potřebují určitá společná oprávnění, která jsou k dispozici na úrovni skupiny. Následující příklad manifestu aplikace ukazuje místní skupinu s názvem *LocalAdminGroup* , která má oprávnění správce. Dva uživatelé, *Customer1* a *Customer2*, jsou členy této místní skupiny. V části **ServiceManifestImport** se použije zásada RunAs pro spuštění balíčku kódu *Stateful1Pkg* jako *Customer2*.  Pro spuštění balíčku kódu *Web1Pkg* jako *Customer1*se použije další zásada runas.

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

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Použít výchozí zásadu na všechny balíčky kódu služby
Část **DefaultRunAsPolicy** slouží k určení výchozího uživatelského účtu pro všechny balíčky kódu, které nemají definovaný konkrétní **RunAsPolicy** . Pokud většina balíčků kódu, které jsou zadány v manifestu služby používaném aplikací, musí běžet stejným uživatelem, aplikace může pouze definovat výchozí zásadu RunAs s tímto uživatelským účtem. Následující příklad určuje, že pokud balíček kódu nemá zadaný **RunAsPolicy** , balíček kódu by měl běžet pod uživatelem **MyDefaultAccount** zadaným v oddílu objekty zabezpečení.  Podporované typy účtů jsou LocalUser, NetworkService, LocalSystem a LocalService.  Pokud používáte místního uživatele nebo službu, zadejte taky název účtu a heslo.

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

## <a name="debug-a-code-package-locally-using-console-redirection"></a>Místní ladění balíčku kódu pomocí přesměrování konzoly
V některých případech je užitečné pro účely ladění, aby se zobrazil výstup konzoly z běžící služby. Zásady přesměrování konzoly můžete nastavit na vstupním bodu v manifestu služby, který zapisuje výstup do souboru. Výstup souboru se zapisuje do složky aplikace s názvem **log (protokol** ) na uzlu clusteru, kde je aplikace nasazená a spuštěná. 

> [!WARNING]
> Nikdy nepoužívejte zásady přesměrování konzoly v aplikaci, která je nasazená v produkčním prostředí, protože to může mít vliv na převzetí služeb při selhání aplikace. Tato operace se používá *pouze* pro účely místního vývoje a ladění.  
> 
> 

Následující příklad manifestu služby ukazuje povolování přesměrování konzoly s hodnotou FileRetentionCount:

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
## <a name="next-steps"></a>Další kroky
* [Pochopení aplikačního modelu](service-fabric-application-model.md)
* [Určení prostředků v manifestu služby](service-fabric-service-manifest-resources.md)
* [Nasazení aplikace](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
