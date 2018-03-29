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
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: 62917a1d342158ec2114a9204ee1ca9e447284fa
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Spuštění služby jako účet místního uživatele nebo místní systémový účet
Pomocí Azure Service Fabric můžete zabezpečit aplikace, které jsou spuštěny v clusteru v rámci jiné uživatelské účty. Ve výchozím nastavení se aplikace Service Fabric běžet pod účtem, proces Fabric.exe kompatibilní se. Service Fabric taky poskytuje možnost spouštět aplikace pod účtem místního uživatelského účtu nebo účtu local system, která se provádí zadáním RunAs zásadu v rámci manifest aplikace. Typy účtů podporovaný místní systém **LocalUser**, **NetworkService**, **LocalService**, a **LocalSystem**.

Můžete také definovat a vytvořit skupiny uživatelů, aby pro každou skupinu pro správu společně lze přidat jeden nebo více uživatelů. To je užitečné, pokud existuje více uživatelů pro různé služby vstupní body a vyžadují, aby byla určité společné oprávnění, které jsou k dispozici na úrovni skupiny.

> [!NOTE] 
> Pokud použijete RunAs zásady pro služby a service manifest deklaruje koncový bod prostředků pomocí protokolu HTTP, je nutné zadat **SecurityAccessPolicy**.  Další informace najdete v tématu [přiřadit zásady zabezpečení přístupu pro koncové body HTTP a HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

## <a name="create-local-user-groups"></a>Vytvořit místní skupiny uživatelů
Můžete definovat a vytvořit skupiny uživatelů, které umožňují jeden nebo více uživatelů, který se má přidat do skupiny. To je užitečné, pokud existuje více uživatelů pro různé služby vstupní body a vyžadují, aby byla určité společné oprávnění, které jsou k dispozici na úrovni skupiny. Následující příklad ukazuje místní skupinu s názvem **LocalAdminGroup** s oprávněními správce. Dva uživatelé, Customer1 a Customer2, stanou členy této skupiny místní v tomto příkladu manifestu aplikace:

```xml
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
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
```

## <a name="create-local-users"></a>Vytvořit místní uživatele
Můžete vytvořit místní uživatele, který slouží k zajištění služby v rámci aplikace. Když **LocalUser** typ účtu je definováno v sekci objekty manifestu aplikace Service Fabric vytvoří místní uživatelské účty na počítačích, kde je aplikace nasazená. Ve výchozím nastavení tyto účty nemají stejné názvy jako platformám zadaným v manifest aplikace (například Customer3 v následujícím příkladu manifestu aplikace). Místo toho se dynamicky generují a mít náhodných hesla.

```xml
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
```

Pokud aplikace vyžaduje, aby uživatelský účet a heslo být stejná ve všech počítačích (například k povolení ověřování NTLM), musíte nastavit v manifestu clusteru **NTLMAuthenticationEnabled** na hodnotu true. V manifestu clusteru musíte zadat také **NTLMAuthenticationPasswordSecret** který se používá ke generování stejné heslo ve všech počítačích.

```xml
<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
```

## <a name="assign-policies-to-the-service-code-packages"></a>Přiřadit zásady do balíčků kódu služby
**RunAsPolicy** část **ServiceManifestImport** Určuje účet z části objekty zabezpečení, který se má použít ke spuštění balíček kódu. Také přidruží kód balíčky service manifest s uživatelskými účty v části objekty zabezpečení. Je to zadané pro instalaci nebo hlavní vstupních bodů, nebo můžete zadat `All` chcete použít pro obojí. Následující příklad ukazuje použití různých zásad:

```xml
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
```

Pokud **entrypointtype typu** není zadán, výchozí hodnota je nastavena na `EntryPointType=”Main”`. Určení **SetupEntryPoint** je obzvláště užitečné, když chcete spustit určité operace instalace s vysokou úrovní oprávnění v rámci účtu system. Další informace najdete v tématu [spustit skript spuštění služby jako účet místního uživatele nebo systému](service-fabric-run-script-at-service-startup.md). Aktuální služby kód může být spuštěn pod účtem nižší oprávnění.

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Použít výchozí zásady na všechny balíčky kódu služby
Můžete použít **DefaultRunAsPolicy** části zadejte výchozí účet uživatele pro všechny kód balíčky, které nemají konkrétní **RunAsPolicy** definované. Pokud většinu kódu balíčky, které jsou určené v service manifest používaný aplikací k potřebovali pro spuštění pod stejného uživatele, můžete aplikaci právě definovat výchozí zásady RunAs se s uživatelským účtem. Následující příklad určuje, že pokud balíček kódu neobsahuje **RunAsPolicy** zadán, balíček kódu by měl běžet pod **MyDefaultAccount** zadané v části objekty zabezpečení.

```xml
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další postup
* [Pochopení aplikačního modelu](service-fabric-application-model.md)
* [Zadejte prostředky v service manifest](service-fabric-service-manifest-resources.md)
* [Nasazení aplikace](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
