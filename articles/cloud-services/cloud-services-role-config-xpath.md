---
title: Cloud Services tabulka tahák XPath pro konfiguraci rolí | Microsoft Docs
description: Různá nastavení XPath, která můžete použít v konfiguraci role cloudové služby k vystavování nastavení jako proměnné prostředí.
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: 380b0be4e4e4b19d16cb611b0b472294339f2199
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75386081"
---
# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>Vystavit nastavení konfigurace role jako proměnnou prostředí pomocí XPath
V souboru s definicí pracovníka cloudové služby nebo v definičním souboru služby webové role můžete vystavit hodnoty konfigurace modulu runtime jako proměnné prostředí. Podporovány jsou následující hodnoty XPath (které odpovídají hodnotám rozhraní API).

Tyto hodnoty XPath jsou také k dispozici prostřednictvím knihovny [Microsoft. windowsazure. ServiceRuntime](/previous-versions/azure/reference/ee773173(v=azure.100)) . 

## <a name="app-running-in-emulator"></a>Aplikace spuštěná v emulátoru
Indikuje, že aplikace běží v emulátoru.

| Typ | Příklad |
| --- | --- |
| XPath |XPath = " /RoleEnvironment/Deployment/@emulated " |
| Kód |var x = RoleEnvironment. Emulation; |

## <a name="deployment-id"></a>ID nasazení
Načte ID nasazení instance.

| Typ | Příklad |
| --- | --- |
| XPath |XPath = " /RoleEnvironment/Deployment/@id " |
| Kód |var deploymentId = RoleEnvironment. DeploymentId; |

## <a name="role-id"></a>ID role
Načte aktuální ID role pro instanci.

| Typ | Příklad |
| --- | --- |
| XPath |XPath = " /RoleEnvironment/CurrentInstance/@id " |
| Kód |var ID = RoleEnvironment.CurrentRoleInstance.Id; |

## <a name="update-domain"></a>Aktualizace domény
Načte aktualizační doménu instance.

| Typ | Příklad |
| --- | --- |
| XPath |XPath = " /RoleEnvironment/CurrentInstance/@updateDomain " |
| Kód |var ud = RoleEnvironment. CurrentRoleInstance. UpdateDomain; |

## <a name="fault-domain"></a>Doména selhání
Načte doménu selhání instance.

| Typ | Příklad |
| --- | --- |
| XPath |XPath = " /RoleEnvironment/CurrentInstance/@faultDomain " |
| Kód |var FD = RoleEnvironment. CurrentRoleInstance. FaultDomain; |

## <a name="role-name"></a>Název role
Načte název role instancí.

| Typ | Příklad |
| --- | --- |
| XPath |XPath = " /RoleEnvironment/CurrentInstance/@roleName " |
| Kód |var RNAME = RoleEnvironment.CurrentRoleInstance.Role.Name; |

## <a name="config-setting"></a>Nastavení konfigurace
Načte hodnotu zadaného nastavení konfigurace.

| Typ | Příklad |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting [ @name = ' Setting1 '] /@value " |
| Kód |var – nastavení = RoleEnvironment. GetConfigurationSettingValue ("Setting1"); |

## <a name="local-storage-path"></a>Cesta k místnímu úložišti
Načte cestu místního úložiště pro instanci.

| Typ | Příklad |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/CurrentInstance/LocalResources/LocalResource [ @name = ' LocalStore1 '] /@path " |
| Kód |var localResourcePath = RoleEnvironment. GetLocalResource ("LocalStore1"). RootPath |

## <a name="local-storage-size"></a>Velikost místního úložiště
Načte velikost místního úložiště pro instanci.

| Typ | Příklad |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/CurrentInstance/LocalResources/LocalResource [ @name = ' LocalStore1 '] /@sizeInMB " |
| Kód |var localResourceSizeInMB = RoleEnvironment. GetLocalResource ("LocalStore1"). MaximumSizeInMegabytes; |

## <a name="endpoint-protocol"></a>Protokol koncového bodu
Načte protokol koncového bodu pro instanci.

| Typ | Příklad |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/CurrentInstance/Endpoints/Endpoint [ @name = ' Endpoint1 '] /@protocol " |
| Kód |var prot = RoleEnvironment. CurrentRoleInstance. InstanceEndpoints ["Endpoint1"]. Protokol |

## <a name="endpoint-ip"></a>IP adresa koncového bodu
Získá IP adresu zadaného koncového bodu.

| Typ | Příklad |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/CurrentInstance/Endpoints/Endpoint [ @name = ' Endpoint1 '] /@address " |
| Kód |var Address = RoleEnvironment. CurrentRoleInstance. InstanceEndpoints ["Endpoint1"]. IPEndpoint. Address |

## <a name="endpoint-port"></a>Port koncového bodu
Načte pro instanci port koncového bodu.

| Typ | Příklad |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/CurrentInstance/Endpoints/Endpoint [ @name = ' Endpoint1 '] /@port " |
| Kód |var port = RoleEnvironment. CurrentRoleInstance. InstanceEndpoints ["Endpoint1"]. IPEndpoint. port; |

## <a name="example"></a>Příklad
Tady je příklad role pracovního procesu, která vytvoří úlohu po spuštění s proměnnou prostředí s názvem `TestIsEmulated` nastavenou na [ @emulated hodnotu XPath](#app-running-in-emulator). 

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
Přečtěte si další informace o souboru [ServiceConfiguration. cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) .

Vytvořte balíček [ServicePack. cspkg](cloud-services-model-and-package.md#servicepackagecspkg) .

Povolit [vzdálenou plochu](cloud-services-role-enable-remote-desktop-new-portal.md) pro roli.




