---
title: Cloudové služby Role konfigurace XPath tahák | Microsoft Docs
description: Různá nastavení XPath můžete v konfiguračním cloudové služby role vystavit nastavení jako proměnné prostředí.
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
ms.assetid: c51e4493-0643-4d05-bc44-06c76bcbf7d1
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: adegeo
ms.openlocfilehash: e71adbca34390bda3a7d4067742ffb3a28201449
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2017
ms.locfileid: "24860364"
---
# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>Vystavit nastavení konfigurace role v proměnné prostředí, jejichž výraz XPath
V pracovní cloudové služby nebo v souboru definice služby role Webový můžou zpřístupnit hodnoty konfigurace modulu runtime jako proměnné prostředí. Následující výraz XPath hodnoty jsou podporovány (které odpovídají hodnotám rozhraní API).

Tyto hodnoty XPath jsou také k dispozici prostřednictvím [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) knihovny. 

## <a name="app-running-in-emulator"></a>Aplikaci spuštěnou v emulátoru
Označuje, že aplikace běží v emulátoru.

| Typ | Příklad |
| --- | --- |
| Výraz XPath |výraz XPath = "/RoleEnvironment/Deployment/@emulated" |
| Kód |var x = RoleEnvironment.IsEmulated; |

## <a name="deployment-id"></a>ID nasazení
Načte identifikátor ID nasazení pro instanci.

| Typ | Příklad |
| --- | --- |
| Výraz XPath |výraz XPath = "/RoleEnvironment/Deployment/@id" |
| Kód |var deploymentId = RoleEnvironment.DeploymentId; |

## <a name="role-id"></a>Role ID
Načte aktuální ID role pro instanci.

| Typ | Příklad |
| --- | --- |
| Výraz XPath |výraz XPath = "/RoleEnvironment/CurrentInstance/@id" |
| Kód |var id = RoleEnvironment.CurrentRoleInstance.Id; |

## <a name="update-domain"></a>Aktualizace domény
Načte aktualizace domény instance.

| Typ | Příklad |
| --- | --- |
| Výraz XPath |výraz XPath = "/RoleEnvironment/CurrentInstance/@updateDomain" |
| Kód |var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |

## <a name="fault-domain"></a>Doména selhání
Načte domény selhání instance.

| Typ | Příklad |
| --- | --- |
| Výraz XPath |výraz XPath = "/RoleEnvironment/CurrentInstance/@faultDomain" |
| Kód |var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |

## <a name="role-name"></a>Název role
Načte název role instance.

| Typ | Příklad |
| --- | --- |
| Výraz XPath |výraz XPath = "/RoleEnvironment/CurrentInstance/@roleName" |
| Kód |var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |

## <a name="config-setting"></a>Nastavení konfigurace
Načte hodnotu zadaného konfiguračního nastavení.

| Typ | Příklad |
| --- | --- |
| Výraz XPath |výraz XPath = "/ RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting [@name= 'Setting1']/@value" |
| Kód |var nastavení = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |

## <a name="local-storage-path"></a>Cesta k místnímu úložišti
Načte cestu místní úložiště pro instanci.

| Typ | Příklad |
| --- | --- |
| Výraz XPath |výraz XPath = "/ RoleEnvironment/CurrentInstance/LocalResources/LocalResource [@name= 'LocalStore1']/@path" |
| Kód |var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1"). RootPath; |

## <a name="local-storage-size"></a>Velikost místní úložiště
Získá velikost místního úložiště pro instanci.

| Typ | Příklad |
| --- | --- |
| Výraz XPath |výraz XPath = "/ RoleEnvironment/CurrentInstance/LocalResources/LocalResource [@name= 'LocalStore1']/@sizeInMB" |
| Kód |var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1"). MaximumSizeInMegabytes; |

## <a name="endpoint-protocol"></a>Koncový bod protokolu
Načte protokol koncový bod pro instanci.

| Typ | Příklad |
| --- | --- |
| Výraz XPath |výraz XPath = "/ RoleEnvironment nebo CurrentInstance nebo koncových bodů nebo koncového bodu [@name= 'koncovém bodě 1']/@protocol" |
| Kód |var ochranu = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. Protokol; |

## <a name="endpoint-ip"></a>Koncový bod IP
Získá zadaný koncový bod IP adresu.

| Typ | Příklad |
| --- | --- |
| Výraz XPath |výraz XPath = "/ RoleEnvironment nebo CurrentInstance nebo koncových bodů nebo koncového bodu [@name= 'koncovém bodě 1']/@address" |
| Kód |var adresu = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Address |

## <a name="endpoint-port"></a>koncový port
Načte koncový port pro instanci.

| Typ | Příklad |
| --- | --- |
| Výraz XPath |výraz XPath = "/ RoleEnvironment nebo CurrentInstance nebo koncových bodů nebo koncového bodu [@name= 'koncovém bodě 1']/@port" |
| Kód |var port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Port; |

## <a name="example"></a>Příklad
Tady je příklad role pracovního procesu, který vytváří úloha spuštění s proměnnou prostředí s názvem `TestIsEmulated` nastavte na [ @emulated hodnotu xpath](#app-running-in-emulator). 

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
Další informace o [souboru ServiceConfiguration.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) souboru.

Vytvoření [ServicePackage.cspkg](cloud-services-model-and-package.md#servicepackagecspkg) balíčku.

Povolit [vzdálené plochy](cloud-services-role-enable-remote-desktop-new-portal.md) pro roli.

