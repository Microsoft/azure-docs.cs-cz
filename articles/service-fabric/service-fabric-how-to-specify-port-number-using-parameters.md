---
title: Zadání čísla portu služby pomocí parametrů
description: Ukazuje, jak pomocí parametrů určit port pro aplikaci v Service Fabric.
author: mikkelhegn
ms.topic: conceptual
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: a53626b8fd362397ba89df30b099fa3c9ff7b0a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609855"
---
# <a name="how-to-specify-the-port-number-of-a-service-using-parameters-in-service-fabric"></a>Jak zadat číslo portu služby pomocí parametrů v service fabric

Tento článek ukazuje, jak zadat číslo portu služby pomocí parametrů v Service Fabric pomocí sady Visual Studio.

## <a name="procedure-for-specifying-the-port-number-of-a-service-using-parameters"></a>Postup pro určení čísla portu služby pomocí parametrů

V tomto příkladu nastavíte číslo portu pro základní webové rozhraní API asp.net pomocí parametru.

1. Otevřete Visual Studio a vytvořte novou aplikaci Service Fabric.
1. Zvolte šablonu ASP.NET Core bezstavové.
1. Zvolte webové rozhraní API.
1. Otevřete soubor ServiceManifest.xml.
1. Poznamenejte si název koncového bodu určeného pro vaši službu. Výchozí je `ServiceEndpoint`.
1. Otevření souboru ApplicationManifest.xml
1. Do `ServiceManifestImport` prvku přidejte `RessourceOverrides` nový prvek s odkazem na koncový bod v souboru ServiceManifest.xml.

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

1. V `Endpoint` elementu můžete nyní přepsat libovolný atribut pomocí parametru. V tomto příkladu `Port` jej zadáte a nastavíte na název parametru pomocí hranatých závorek – například`[MyWebAPI_PortNumber]`

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

1. V souboru ApplicationManifest.xml pak zadáte parametr `Parameters` v elementu

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" />
      </Parameters>
    ```

1. A definovat`DefaultValue`

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" DefaultValue="8080" />
      </Parameters>
    ```

1. Otevření složky ApplicationParameters `Cloud.xml` a souboru
1. Chcete-li určit jiný port, který se má použít při publikování do vzdáleného clusteru, přidejte do tohoto souboru parametr s číslem portu.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" Value="80" />
      </Parameters>
    ```

Při publikování aplikace z Visual Studia pomocí profilu publikování Cloud.xml je vaše služba nakonfigurována tak, aby používala port 80. Pokud nasadíte aplikaci bez zadání parametru MyWebAPI_PortNumber, služba použije port 8080.

## <a name="next-steps"></a>Další kroky
Další informace o některých základních konceptech, které jsou popsány v tomto článku, naleznete v [článcích Správa aplikací pro více prostředí](service-fabric-manage-multiple-environment-app-configuration.md).

Informace o dalších možnostech správy aplikací, které jsou dostupné v sadě Visual Studio, naleznete v [tématu Správa aplikací Service Fabric v sadě Visual Studio](service-fabric-manage-application-in-visual-studio.md).
