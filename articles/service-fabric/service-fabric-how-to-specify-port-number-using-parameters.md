---
title: Určení čísla portu služby pomocí parametrů
description: Ukazuje, jak pomocí parametrů zadat port pro aplikaci v Service Fabric
author: mikkelhegn
ms.topic: conceptual
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: a53626b8fd362397ba89df30b099fa3c9ff7b0a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75609855"
---
# <a name="how-to-specify-the-port-number-of-a-service-using-parameters-in-service-fabric"></a>Jak zadat číslo portu služby pomocí parametrů v Service Fabric

V tomto článku se dozvíte, jak zadat číslo portu služby pomocí parametrů v Service Fabric pomocí sady Visual Studio.

## <a name="procedure-for-specifying-the-port-number-of-a-service-using-parameters"></a>Postup určení čísla portu služby pomocí parametrů

V tomto příkladu nastavíte číslo portu webového rozhraní API asp.net Core pomocí parametru.

1. Otevřete Visual Studio a vytvořte novou Service Fabric aplikaci.
1. Vyberte šablonu nestavové ASP.NET Core.
1. Vyberte webové rozhraní API.
1. Otevřete soubor ServiceManifest.xml.
1. Poznamenejte si název koncového bodu určeného pro vaši službu. Výchozí je `ServiceEndpoint`.
1. Otevřít soubor ApplicationManifest.xml
1. V `ServiceManifestImport` elementu přidejte nový `RessourceOverrides` element s odkazem na koncový bod v souboru ServiceManifest.xml.

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

1. V `Endpoint` elementu teď můžete přepsat libovolný atribut pomocí parametru. V tomto příkladu zadáte `Port` a nastavíte ho na název parametru pomocí hranatých závorek – například `[MyWebAPI_PortNumber]`

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

1. Pořád v souboru ApplicationManifest.xml pak určíte parametr v `Parameters` elementu.

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

1. Otevřete složku ApplicationParameters a `Cloud.xml` soubor
1. Pokud chcete zadat jiný port, který se má použít při publikování do vzdáleného clusteru, přidejte do tohoto souboru parametr s číslem portu.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" Value="80" />
      </Parameters>
    ```

Při publikování aplikace ze sady Visual Studio pomocí Cloud.xml publikační profil, je vaše služba nakonfigurovaná tak, aby používala port 80. Pokud nasadíte aplikaci bez zadání parametru MyWebAPI_PortNumber, služba používá port 8080.

## <a name="next-steps"></a>Další kroky
Další informace o některých základních konceptech, které jsou popsány v tomto článku, najdete v [článcích o správě aplikací pro více prostředí](service-fabric-manage-multiple-environment-app-configuration.md).

Informace o dalších možnostech správy aplikací, které jsou k dispozici v sadě Visual Studio, najdete v tématu [Správa aplikací Service Fabric v sadě Visual Studio](service-fabric-manage-application-in-visual-studio.md).
