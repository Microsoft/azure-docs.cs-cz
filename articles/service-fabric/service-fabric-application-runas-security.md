---
title: Spuštění služby pod systémovými a místními účty zabezpečení
description: Zjistěte, jak spustit aplikaci Service Fabric pod systémovými a místními účty zabezpečení.  Vytvořte objekty zabezpečení a použijte zásadu Run-As pro bezpečné spouštění služeb.
ms.topic: conceptual
ms.date: 03/29/2018
ms.openlocfilehash: 53212f8636602705899834b6db1d3f0d80b5fe4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610113"
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Spuštění služby jako místního uživatelského účtu nebo místního systémového účtu
Pomocí Azure Service Fabric, můžete zabezpečit aplikace, které jsou spuštěny v clusteru pod různými uživatelskými účty. Ve výchozím nastavení service fabric aplikace spustit pod účtem, který Fabric.exe proces běží pod. Service Fabric také poskytuje možnost spouštět aplikace pod místním uživatelským nebo systémovým účtem. Podporované typy místních systémových účtů jsou **LocalUser**, **NetworkService**, **LocalService**a **LocalSystem**.  Pokud používáte service fabric v samostatném clusteru se systémem Windows, můžete spustit službu v části [Účty domény služby Active Directory](service-fabric-run-service-as-ad-user-or-group.md) nebo účty [spravovaných služeb služby služby služby Služby](service-fabric-run-service-as-gmsa.md)Active Directory .

V manifestu aplikace definujete uživatelské účty potřebné ke spuštění služeb nebo zabezpečení prostředků v části **Objekty zabezpečení.** Můžete také definovat a vytvářet skupiny uživatelů tak, aby jeden nebo více uživatelů bylo možné spravovat společně. To je užitečné, pokud existuje více uživatelů pro různé vstupní body služby a potřebují společná oprávnění, která jsou k dispozici na úrovni skupiny.  Uživatelé jsou pak odkazováni v zásadách RunAs, která se používá pro konkrétní službu nebo všechny služby v aplikaci. 

Ve výchozím nastavení je zásada RunAs použita pro hlavní vstupní bod.  Můžete také použít zásadu RunAs pro vstupní bod nastavení, pokud potřebujete [spustit určité operace nastavení s vysokými oprávněními pod systémovým účtem](service-fabric-run-script-at-service-startup.md)nebo hlavní vstupní body i vstupní body nastavení.  

> [!NOTE] 
> Pokud použijete zásadu RunAs pro službu a manifest služby deklaruje prostředky koncového bodu pomocí protokolu HTTP, je nutné zadat **SecurityAccessPolicy**.  Další informace naleznete [v tématu Přiřazení zásad přístupu k zabezpečení pro koncové body PROTOKOLU HTTP a HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

## <a name="run-a-service-as-a-local-user"></a>Spuštění služby jako místního uživatele
Můžete vytvořit místního uživatele, který lze použít k zabezpečení služby v rámci aplikace. Pokud je typ účtu **LocalUser** zadán v části objekty zabezpečení manifestu aplikace, Service Fabric vytvoří místní uživatelské účty v počítačích, kde je aplikace nasazena. Ve výchozím nastavení tyto účty nemají stejné názvy jako ty, které jsou zadány v manifestu aplikace (například *Customer3* v následujícím příkladu manifestu aplikace). Místo toho jsou dynamicky generovány a mají náhodná hesla.

V části **RunAsPolicy** pro **ServiceManifestImport**zadejte uživatelský účet z oddílu Principals pro spuštění balíčku kódu **služby.**  Následující příklad ukazuje, jak vytvořit místního uživatele a použít zásadu RunAs na hlavní vstupní bod:

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

## <a name="create-a-local-user-group"></a>Vytvoření místní skupiny uživatelů
Můžete vytvořit skupiny uživatelů a přidat do skupiny jednoho nebo více uživatelů. To je užitečné, pokud existuje více uživatelů pro různé vstupní body služby a potřebují mít určitá společná oprávnění, která jsou k dispozici na úrovni skupiny. Následující příklad manifestu aplikace ukazuje místní skupinu s názvem *LocalAdminGroup,* která má oprávnění správce. Dva uživatelé, *Customer1* a *Customer2*, jsou členy této místní skupiny. V části **ServiceManifestImport** se použije zásada RunAs ke spuštění balíčku kódu *Stateful1Pkg* jako *Customer2*.  Pro spuštění balíčku kódu *Web1Pkg* jako *Customer1*je použita jiná zásada RunAs .

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

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Použití výchozí zásady pro všechny balíčky kódu služby
Oddíl **DefaultRunAsPolicy** slouží k určení výchozího uživatelského účtu pro všechny balíčky kódu, které nemají definovánkonkrétní **runaspolicy.** Pokud většina balíčků kódu, které jsou zadány v manifestu služby používané aplikace je třeba spustit pod stejným uživatelem, aplikace můžete pouze definovat výchozí Zásady RunAs s tímto uživatelským účtem. Následující příklad určuje, že pokud balíček kódu nemá zadaný **runaspolicy,** měl by být balíček kódu spuštěn pod uživatelem **MyDefaultAccount** zadaným v části Objekty zabezpečení.  Podporované typy účtů jsou LocalUser, NetworkService, LocalSystem a LocalService.  Pokud používáte místního uživatele nebo službu, zadejte také název účtu a heslo.

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

## <a name="debug-a-code-package-locally-using-console-redirection"></a>Ladění balíčku kódu místně pomocí přesměrování konzoly
V některých případě je užitečné pro účely ladění zobrazit výstup konzoly ze spuštěné služby. Můžete nastavit zásady přesměrování konzoly v vstupním bodě v manifestu služby, který zapíše výstup do souboru. Výstup souboru se zapíše do složky aplikace s názvem **protokol** v uzlu clusteru, kde je aplikace nasazena a spuštěna. 

> [!WARNING]
> Nikdy nepoužívejte zásady přesměrování konzoly v aplikaci, která je nasazena v produkčním prostředí, protože to může ovlivnit převzetí služeb při selhání aplikace. Používejte *pouze* pro účely místního vývoje a ladění.  
> 
> 

Následující příklad manifestu služby ukazuje povolení přesměrování konzoly s hodnotou FileRetentionCount:

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
* [Principy aplikačního modelu](service-fabric-application-model.md)
* [Určení prostředků v manifestu služby](service-fabric-service-manifest-resources.md)
* [Nasazení aplikace](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
