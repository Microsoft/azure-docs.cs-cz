---
title: Jak zadat číslo portu služby pomocí parametrů v Azure Service Fabric | Dokumentace Microsoftu
description: Ukazuje, jak pomocí parametrů můžete zadat port pro aplikace v Service Fabric
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
ms.openlocfilehash: d69e02126564388bf045693b9960e6e574307641
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391330"
---
# <a name="how-to-specify-the-port-number-of-a-service-using-parameters-in-service-fabric"></a>Jak zadat číslo portu služby v Service Fabric pomocí parametrů

Tento článek ukazuje, jak zadat číslo portu služby pomocí parametrů v Service Fabric pomocí sady Visual Studio.

## <a name="procedure-for-specifying-the-port-number-of-a-service-using-parameters"></a>Postup pro zadání číslo portu služby pomocí parametrů

V tomto příkladu nastavíte číslo portu pro asp.net core webového rozhraní API pomocí parametru.

1. Otevřít Visual Studio a vytvořte novou aplikaci Service Fabric.
1. Výběr šablony Bezstavová služba ASP.NET Core.
1. Zvolte webového rozhraní API.
1. Otevřete soubor ServiceManifest.xml.
1. Poznamenejte si název koncového bodu určeného pro vaši službu. Výchozí hodnota je `ServiceEndpoint`.
1. Otevřete soubor ApplicationManifest.xml
1. V `ServiceManifestImport` prvku, přidejte novou `RessourceOverrides` element s odkazem na koncový bod v souboru ServiceManifest.xml.

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

1. V `Endpoint` elementu, je nyní přepsat všechny atributy, pomocí parametru. V tomto příkladu zadáte `Port` a nastavte ho na název parametru pomocí hranatých závorek – například `[MyWebAPI_PortNumber]`

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

1. Stále v souboru ApplicationManifest.xml potom zadáte parametr do `Parameters` – element

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
1. Zadejte jiný port pro použití při publikování do vzdáleného clusteru, přidejte parametr číslo portu do tohoto souboru.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" Value="80" />
      </Parameters>
    ```

Při publikování aplikace ze sady Visual Studio pomocí Cloud.xml profil publikování, vaše služba je nakonfigurovaná pro používání portu 80. Při nasazení aplikace bez zadání parametru MyWebAPI_PortNumber, služba používá port 8080.

## <a name="next-steps"></a>Další postup
Další informace o některých klíčových konceptech, které jsou popsané v tomto článku najdete v tématu [Správa aplikací pro více prostředí článků](service-fabric-manage-multiple-environment-app-configuration.md).

Informace o dalších možnostech správy aplikací, které jsou k dispozici v sadě Visual Studio najdete v tématu [Správa aplikací Service Fabric v sadě Visual Studio](service-fabric-manage-application-in-visual-studio.md).
