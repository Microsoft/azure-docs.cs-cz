---
title: Cloud Services Role config tahák XPath pro | Dokumentace Microsoftu
description: Různá nastavení XPath vám pomůže v konfiguraci role cloudové služby nabízejí nastavení jako proměnnou prostředí.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: c51e4493-0643-4d05-bc44-06c76bcbf7d1
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: jeconnoc
ms.openlocfilehash: 2db63be6c6997840f7409a3ca79f1845f30e4ceb
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008055"
---
# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>Zpřístupnit nastavení konfigurace role jako proměnnou prostředí, jejichž výraz XPath
V pracovního procesu cloud service nebo definiční soubor služby webové role mohou vystavit hodnoty konfigurace modulu runtime jako proměnné prostředí. Následující výraz XPath hodnoty jsou podporovány (které odpovídají hodnotám rozhraní API).

Tyto hodnoty XPath jsou také k dispozici prostřednictvím [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) knihovny. 

## <a name="app-running-in-emulator"></a>Aplikace spuštěné v emulátoru
Označuje, že je aplikace spuštěna v emulátoru.

| Typ | Příklad: |
| --- | --- |
| Výraz XPath |výraz XPath = "/RoleEnvironment/Deployment/@emulated" |
| Kód |var x = RoleEnvironment.IsEmulated; |

## <a name="deployment-id"></a>ID nasazení
Načte identifikátor ID nasazení pro instanci.

| Typ | Příklad: |
| --- | --- |
| Výraz XPath |výraz XPath = "/RoleEnvironment/Deployment/@id" |
| Kód |var deploymentId = RoleEnvironment.DeploymentId; |

## <a name="role-id"></a>ID role
Načte aktuální ID role pro instanci.

| Typ | Příklad: |
| --- | --- |
| Výraz XPath |výraz XPath = "/RoleEnvironment/CurrentInstance/@id" |
| Kód |var id = RoleEnvironment.CurrentRoleInstance.Id; |

## <a name="update-domain"></a>Aktualizace domény
Načte aktualizační doména instance.

| Typ | Příklad: |
| --- | --- |
| Výraz XPath |výraz XPath = "/RoleEnvironment/CurrentInstance/@updateDomain" |
| Kód |var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |

## <a name="fault-domain"></a>Chybná doména
Načte doménu selhání instance.

| Typ | Příklad: |
| --- | --- |
| Výraz XPath |výraz XPath = "/RoleEnvironment/CurrentInstance/@faultDomain" |
| Kód |var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |

## <a name="role-name"></a>Název role
Načte název instance role.

| Typ | Příklad: |
| --- | --- |
| Výraz XPath |výraz XPath = "/RoleEnvironment/CurrentInstance/@roleName" |
| Kód |var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |

## <a name="config-setting"></a>Nastavení konfigurace
Načte hodnotu zadaného nastavení.

| Typ | Příklad: |
| --- | --- |
| Výraz XPath |výraz XPath = "/ RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting [@name= 'Setting1']/@value" |
| Kód |var nastavení = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |

## <a name="local-storage-path"></a>Cesta k místnímu úložišti
Načte cestu místního úložiště pro instanci.

| Typ | Příklad: |
| --- | --- |
| Výraz XPath |výraz XPath = "/ RoleEnvironment/CurrentInstance/LocalResources/LocalResource [@name= 'LocalStore1']/@path" |
| Kód |var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1"). RootPath; |

## <a name="local-storage-size"></a>Velikost místního úložiště
Získá velikost místního úložiště pro instanci.

| Typ | Příklad: |
| --- | --- |
| Výraz XPath |výraz XPath = "/ RoleEnvironment/CurrentInstance/LocalResources/LocalResource [@name= 'LocalStore1']/@sizeInMB" |
| Kód |var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1"). MaximumSizeInMegabytes; |

## <a name="endpoint-protocol"></a>Protokol koncového bodu
Načte protokol koncového bodu pro instanci.

| Typ | Příklad: |
| --- | --- |
| Výraz XPath |výraz XPath = "/ RoleEnvironment/CurrentInstance/koncové body nebo koncový bod [@name= 'koncovém bodě 1']/@protocol" |
| Kód |var ochranu = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. Protokol; |

## <a name="endpoint-ip"></a>Koncový bod IP
Získá zadaný koncový bod IP adresu.

| Typ | Příklad: |
| --- | --- |
| Výraz XPath |výraz XPath = "/ RoleEnvironment/CurrentInstance/koncové body nebo koncový bod [@name= 'koncovém bodě 1']/@address" |
| Kód |var adresa = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Address |

## <a name="endpoint-port"></a>Port koncového bodu
Načte port koncového bodu pro instanci.

| Typ | Příklad: |
| --- | --- |
| Výraz XPath |výraz XPath = "/ RoleEnvironment/CurrentInstance/koncové body nebo koncový bod [@name= 'koncovém bodě 1']/@port" |
| Kód |var port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Port; |

## <a name="example"></a>Příklad:
Tady je příklad, který vytváří úlohy po spuštění s proměnnou prostředí s názvem role pracovního procesu `TestIsEmulated` nastaveno [ @emulated hodnota xpath](#app-running-in-emulator). 

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

## <a name="next-steps"></a>Další postup
Další informace o [souboru ServiceConfiguration.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) souboru.

Vytvoření [ServicePackage.cspkg](cloud-services-model-and-package.md#servicepackagecspkg) balíčku.

Povolit [vzdálené plochy](cloud-services-role-enable-remote-desktop-new-portal.md) pro roli.

