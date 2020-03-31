---
title: Konfigurační seznam xpath rolí cloudových služeb | Dokumenty společnosti Microsoft
description: Různá nastavení XPath, která můžete použít v konfiguraci role cloudové služby, zobrazí nastavení jako proměnnou prostředí.
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: 380b0be4e4e4b19d16cb611b0b472294339f2199
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386081"
---
# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>Vystavit nastavení konfigurace role jako proměnné prostředí pomocí XPath
V souboru definice pracovníka cloudové služby nebo webové role služby můžete vystavit hodnoty konfigurace za běhu jako proměnné prostředí. Jsou podporovány následující hodnoty XPath (které odpovídají hodnotám rozhraní API).

Tyto hodnoty XPath jsou také k dispozici prostřednictvím knihovny [Microsoft.WindowsAzure.ServiceRuntime.](/previous-versions/azure/reference/ee773173(v=azure.100)) 

## <a name="app-running-in-emulator"></a>Aplikace spuštěná v emulátoru
Označuje, že aplikace běží v emulátoru.

| Typ | Příklad |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@emulated" |
| kód |var x = RoleEnvironment.IsEmulated; |

## <a name="deployment-id"></a>ID nasazení
Načte ID nasazení pro instanci.

| Typ | Příklad |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@id" |
| kód |var deploymentId = RoleEnvironment.DeploymentId; |

## <a name="role-id"></a>Role ID
Načte aktuální ID role pro instanci.

| Typ | Příklad |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@id" |
| kód |var id = RoleEnvironment.CurrentRoleInstance.Id; |

## <a name="update-domain"></a>Aktualizace domény
Načte aktualizační doménu instance.

| Typ | Příklad |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@updateDomain" |
| kód |var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |

## <a name="fault-domain"></a>Doména selhání
Načte doménu selhání instance.

| Typ | Příklad |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@faultDomain" |
| kód |var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |

## <a name="role-name"></a>Název role
Načte název role instancí.

| Typ | Příklad |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@roleName" |
| kód |var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |

## <a name="config-setting"></a>Nastavení konfigurace
Načte hodnotu zadaného nastavení konfigurace.

| Typ | Příklad |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting[@name='Setting1']/@value" |
| kód |var setting = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |

## <a name="local-storage-path"></a>Cesta k místnímu úložišti
Načte cestu místního úložiště pro instanci.

| Typ | Příklad |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@path" |
| kód |var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1"). RootPath; |

## <a name="local-storage-size"></a>Velikost místního úložiště
Načte velikost místního úložiště pro instanci.

| Typ | Příklad |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@sizeInMB" |
| kód |var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1"). MaximumSizeInMegabytes; |

## <a name="endpoint-protocol"></a>Protokol koncového bodu
Načte protokol koncového bodu pro instanci.

| Typ | Příklad |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoint/Endpoint[@name='Endpoint1']/@protocol" |
| kód |var prot = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. protokol; |

## <a name="endpoint-ip"></a>Ip koncový bod
Získá zadaný koncový bod IP adresu.

| Typ | Příklad |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoint/Endpoint[@name='Endpoint1']/@address" |
| kód |var adresa = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Adresa |

## <a name="endpoint-port"></a>Port koncového bodu
Načte port koncového bodu pro instanci.

| Typ | Příklad |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoint/Endpoint[@name='Endpoint1']/@port" |
| kód |var port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Port; |

## <a name="example"></a>Příklad
Zde je příklad role pracovního procesu, která vytvoří `TestIsEmulated` úlohu při spuštění s proměnnou prostředí pojmenovanou na [ @emulated hodnotu xpath](#app-running-in-emulator). 

```xml
<WorkerRole name="Role1">
    <ConfigurationSettings>
      <Setting name="Setting1" />
    </ConfigurationSettings>
    <LocalResources>
      <LocalStorage name="LocalStore1" sizeInMB="1024"/>
    </LocalResources>
    <Endpoints>
      <InternalEndpoint name="Endpoint1" protocol="tcp" />
    </Endpoints>
    <Startup>
      <Task commandLine="example.cmd inputParm">
        <Environment>
          <Variable name="TestConstant" value="Constant"/>
          <Variable name="TestEmptyValue" value=""/>
          <Variable name="TestIsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
          </Variable>
          ...
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="TestConstant" value="Constant"/>
        <Variable name="TestEmptyValue" value=""/>
        <Variable name="TestIsEmulated">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
        </Variable>
        ...
      </Environment>
    </Runtime>
    ...
</WorkerRole>
```

## <a name="next-steps"></a>Další kroky
Další informace o souboru [ServiceConfiguration.cscfg.](cloud-services-model-and-package.md#serviceconfigurationcscfg)

Vytvořte balíček [ServicePackage.cspkg.](cloud-services-model-and-package.md#servicepackagecspkg)

Povolte [vzdálenou plochu](cloud-services-role-enable-remote-desktop-new-portal.md) pro roli.




