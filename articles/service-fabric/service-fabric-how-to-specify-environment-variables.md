---
title: Určení proměnných prostředí pro služby
description: Ukazuje, jak používat proměnné prostředí pro aplikace v Service Fabric
author: mikkelhegn
ms.topic: conceptual
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: f4c4f2a1c140e3d0f181c4fd55482056f9f91b62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614311"
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Jak zadat proměnné prostředí pro služby v Service Fabric

Tento článek ukazuje, jak určit proměnné prostředí pro službu nebo kontejner v Service Fabric.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>Postup pro určení proměnných prostředí pro služby

V tomto příkladu nastavíte proměnnou prostředí pro kontejner. Článek předpokládá, že již máte manifest aplikace a služby.

1. Otevřete soubor ServiceManifest.xml.
2. V `CodePackage` elementu přidejte `EnvironmentVariables` nový `EnvironmentVariable` prvek a prvek pro každou proměnnou prostředí.

    ```xml
    <CodePackage Name="MyCode" Version="CodeVersion1">
            ...
            <EnvironmentVariables>
                  <EnvironmentVariable Name="MyEnvVariable" Value="DefaultValue"/>
                  <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentVariables>
    </CodePackage>
    ```

   Proměnné prostředí mohou být přepsány v manifestu aplikace.

3. Chcete-li přepsat proměnné prostředí v manifestu `EnvironmentOverrides` aplikace, použijte prvek.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="specifying-environment-variables-dynamically-using-docker-compose"></a>Dynamické určení proměnných prostředí pomocí funkce Docker Compose

Service Fabric podporuje možnost [používat Docker Compose pro nasazení](service-fabric-docker-compose.md#supported-compose-directives). Skládání souborů může zdroj proměnné prostředí z prostředí. Toto chování lze dynamicky nahradit požadované hodnoty prostředí:

```yml
environment:
  - "hostname:${hostname}"
```

## <a name="next-steps"></a>Další kroky
Další informace o některých základních konceptech, které jsou popsány v tomto článku, naleznete v [článcích Správa aplikací pro více prostředí](service-fabric-manage-multiple-environment-app-configuration.md).

Informace o dalších možnostech správy aplikací, které jsou dostupné v sadě Visual Studio, naleznete v [tématu Správa aplikací Service Fabric v sadě Visual Studio](service-fabric-manage-application-in-visual-studio.md).
