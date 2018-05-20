---
title: Postupy zadat proměnné prostředí pro služby v Azure Service Fabric | Microsoft Docs
description: Ukazuje, jak používat proměnné prostředí pro aplikace v Service Fabric
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
ms.openlocfilehash: 4325b3acd3cbc73ee5976021bebe96c267b2a6dd
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Určení proměnných prostředí pro služby v Service Fabric

Tento článek ukazuje, jak zadat proměnné prostředí pro službu nebo kontejneru v Service Fabric.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>Postup při zadávání proměnných prostředí pro služby

V tomto příkladu nastavit proměnnou prostředí pro kontejner. Článek předpokládá, že již máte manifest aplikace a služby.

1. Otevřete soubor ServiceManifest.xml.
1. V `CodePackage` elementu, přidejte nový `EnvironmentVariables` elementu a `EnvironmentVariable` element pro každou proměnnou prostředí.

    ```xml
      <CodePackage Name="MyCode" Version="CodeVersion1">
      ...
        <EnvironmentVariables>
          <EnvironmentVariable Name="MyEnvVariable" Value="DeafultValue"/>
          <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentVariables>
      </CodePackage>
    ```

    Proměnné prostředí může být přepsána nastaveními v manifestu aplikace.

1. Chcete-li přepsat proměnné prostředí v manifestu aplikace, použijte `EnvironmentOverrides` elementu.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="next-steps"></a>Další postup
Další informace o některých základní koncepty, které jsou popsané v tomto článku najdete v tématu [spravovat aplikace pro více článcích prostředí](service-fabric-manage-multiple-environment-app-configuration.md).

Informace o dalším funkcím správy aplikace, které jsou k dispozici v sadě Visual Studio najdete v tématu [spravovat aplikace Service Fabric v sadě Visual Studio](service-fabric-manage-application-in-visual-studio.md).