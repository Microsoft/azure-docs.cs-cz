---
title: Zadat proměnné prostředí pro služby
description: Ukazuje, jak používat proměnné prostředí pro aplikace v Service Fabric
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: 610e44ac98b8b8a2ce5a91fdbcfda145ae36a94b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576736"
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Určení proměnných prostředí pro služby v Service Fabric

V tomto článku se dozvíte, jak zadat proměnné prostředí pro službu nebo kontejner v Service Fabric.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>Postup určení proměnných prostředí pro služby

V tomto příkladu nastavíte proměnnou prostředí pro kontejner. Článek předpokládá, že již máte manifest aplikace a služby.

1. Otevřete soubor ServiceManifest.xml.
2. V `CodePackage` elementu přidejte nový `EnvironmentVariables` element a `EnvironmentVariable` element pro každou proměnnou prostředí.

    ```xml
    <CodePackage Name="MyCode" Version="CodeVersion1">
            ...
            <EnvironmentVariables>
                  <EnvironmentVariable Name="MyEnvVariable" Value="DefaultValue"/>
                  <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentVariables>
    </CodePackage>
    ```

   Proměnné prostředí lze přepsat v manifestu aplikace.

3. Chcete-li přepsat proměnné prostředí v manifestu aplikace, použijte `EnvironmentOverrides` element.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="specifying-environment-variables-dynamically-using-docker-compose"></a>Dynamické zadání proměnných prostředí pomocí Docker Compose

Service Fabric podporuje možnost [použití Docker Compose pro nasazení](service-fabric-docker-compose.md#supported-compose-directives). Soubory pro vytváření souborů můžou být z prostředí proměnné pro zdrojové prostředí. Toto chování se dá použít k dynamickému nahrazení požadovaných hodnot prostředí:

```yml
environment:
  - "hostname:${hostname}"
```

## <a name="next-steps"></a>Další kroky
Další informace o některých základních konceptech, které jsou popsány v tomto článku, najdete v [článcích o správě aplikací pro více prostředí](service-fabric-manage-multiple-environment-app-configuration.md).

Informace o dalších možnostech správy aplikací, které jsou k dispozici v sadě Visual Studio, najdete v tématu [Správa aplikací Service Fabric v sadě Visual Studio](service-fabric-manage-application-in-visual-studio.md).
