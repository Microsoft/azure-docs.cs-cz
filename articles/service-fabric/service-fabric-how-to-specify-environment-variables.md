---
title: Postup zadání proměnných prostředí pro služby v Azure Service Fabric | Dokumentace Microsoftu
description: Ukazuje, jak použít proměnné prostředí pro aplikace v Service Fabric
documentationcenter: .net
author: mikkelhegn
manager: markfuss
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: f75de635f08ae06db349387a436c636c149ec9f2
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/17/2018
ms.locfileid: "42057491"
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Jak zadat proměnné prostředí pro služby v Service Fabric

Tento článek ukazuje, jak zadat proměnné prostředí pro službu nebo kontejner v Service Fabric.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>Postup pro zadání proměnných prostředí pro služby

V tomto příkladu nastavení proměnné prostředí pro kontejner. Tento článek předpokládá, že již máte manifest aplikace a služby.

1. Otevřete soubor ServiceManifest.xml.
1. V `CodePackage` prvku, přidejte novou `EnvironmentVariables` elementu a `EnvironmentVariable` – element pro každou proměnnou prostředí.

    ```xml
      <CodePackage Name="MyCode" Version="CodeVersion1">
      ...
        <EnvironmentVariables>
          <EnvironmentVariable Name="MyEnvVariable" Value="DefaultValue"/>
          <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentVariables>
      </CodePackage>
    ```

    Proměnné prostředí se dá přepsat v manifestu aplikace.

1. Chcete-li přepsat objekt environment variables v manifestu aplikace, použijte `EnvironmentOverrides` elementu.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="next-steps"></a>Další postup
Další informace o některých klíčových konceptech, které jsou popsané v tomto článku najdete v tématu [Správa aplikací pro více prostředí článků](service-fabric-manage-multiple-environment-app-configuration.md).

Informace o dalších možnostech správy aplikací, které jsou k dispozici v sadě Visual Studio najdete v tématu [Správa aplikací Service Fabric v sadě Visual Studio](service-fabric-manage-application-in-visual-studio.md).