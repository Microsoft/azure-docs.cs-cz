---
title: Tom, jak zadat číslo portu služby pomocí parametrů v Azure Service Fabric | Microsoft Docs
description: Ukazuje, jak používat parametry zadejte port pro aplikace v Service Fabric
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
ms.openlocfilehash: 06cfb375c6c18082a0d0316cfcb742a7779fc8a8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="how-to-specify-the-port-number-of-a-service-using-parameters-in-service-fabric"></a>Tom, jak zadat číslo portu služby pomocí parametrů v Service Fabric

Tento článek ukazuje, jak zadat číslo portu služby pomocí parametrů v Service Fabric pomocí sady Visual Studio.

## <a name="procedure-for-specifying-the-port-number-of-a-service-using-parameters"></a>Postup pro zadání číslo portu služby pomocí parametrů

V tomto příkladu nastavíte číslo portu pro asp.net core webového rozhraní API pomocí parametru.

1. Otevřete Visual Studio a vytvořte novou aplikaci Service Fabric.
1. Zvolte šablonu bezstavové ASP.NET Core.
1. Vyberte webové rozhraní API.
1. Otevřete soubor ServiceManifest.xml.
1. Poznamenejte si název zadaného služby koncového bodu. Výchozí hodnota je `ServiceEndpoint`.
1. Otevřete soubor ApplicationManifest.xml
1. V `ServiceManifestImport` elementu, přidejte nový `RessourceOverrides` element s odkazem na koncový bod v souboru ServiceManifest.xml.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. V `Endpoint` elementu, můžete nyní přepsat všechny atributy, pomocí parametru. V tomto příkladu zadáte `Port` a nastavte ji na použití hranaté závorky – například název parametru `[MyWebAPI_PortNumber]`

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint" Port="[MyWebAPI_PortNumber]"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. Stále v souboru ApplicationManifest.xml, pak zadejte parametr v `Parameters` – element

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" />
      </Parameters>
    ```

1. A definovat `DefaultValue`

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" DefaultValue="8080" />
      </Parameters>
    ```

1. Otevřete složku ApplicationParameters a `Cloud.xml` souboru
1. Pokud chcete zadat jiný port, který se má použít při publikování na vzdálený cluster, přidejte parametr číslo portu do tohoto souboru.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" DefaultValue="80" />
      </Parameters>
    ```

Při publikování aplikace ze sady Visual Studio pomocí Cloud.xml profil publikování, vaše služba je nakonfigurována pro používání portu 80. Pokud nasadíte aplikaci bez zadání parametru MyWebAPI_PortNumber, služba service používá port 8080.

## <a name="next-steps"></a>Další postup
Další informace o některých základní koncepty, které jsou popsané v tomto článku najdete v tématu [spravovat aplikace pro více článcích prostředí](service-fabric-manage-multiple-environment-app-configuration.md).

Informace o dalším funkcím správy aplikace, které jsou k dispozici v sadě Visual Studio najdete v tématu [spravovat aplikace Service Fabric v sadě Visual Studio](service-fabric-manage-application-in-visual-studio.md).