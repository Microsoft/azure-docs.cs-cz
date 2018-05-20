---
title: 'Postupy: Parametrizace konfigurační soubory v Azure Service Fabric | Microsoft Docs'
description: Ukazuje, jak Parametrizace konfigurační soubory v Service Fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: e5bb2f270cc5a6f288e1e995f4bfa74f4e3551b7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Postupy: Parametrizace konfigurační soubory v Service Fabric

Tento článek ukazuje, jak Parametrizace konfigurační soubor v Service Fabric.

## <a name="procedure-for-parameterizing-configuration-files"></a>Postup pro Parametrizace konfigurační soubory

V tomto příkladu přepíšete hodnotu konfigurace pomocí parametrů v nasazení vaší aplikace.

1. Otevřete soubor Config\Settings.xml.
1. Nastavení parametru konfigurace, přidáním následující kód XML:

    ```xml
      <Section Name="MyConfigSection">
        <Parameter Name="CacheSize" Value="25" />
      </Section>
    ```

1. Uložte soubor a zavřete ho.
1. Otevřete soubor `ApplicationManifest.xml`.
1. Přidat `ConfigOverride` elementu, odkazující na konfigurační balíček, v části a parametr.

      ```xml
        <ConfigOverrides>
          <ConfigOverride Name="Config">
              <Settings>
                <Section Name="MyConfigSection">
                    <Parameter Name="CacheSize" Value="[Stateless1_CacheSize]" />
                </Section>
              </Settings>
          </ConfigOverride>
        </ConfigOverrides>
      ```

1. Stále v souboru ApplicationManifest.xml, pak zadejte parametr v `Parameters` – element

    ```xml
      <Parameters>
        <Parameter Name="Stateless1_CacheSize" />
      </Parameters>
    ```

1. A definovat `DefaultValue`

    ```xml
      <Parameters>
        <Parameter Name="Stateless1_CacheSize" DefaultValue="80" />
      </Parameters>
    ```

> [!NOTE]
> V případě, kdy přidáte ConfigOverride Service Fabric vždy vybere parametry aplikace nebo z výchozí hodnoty zadané v manifestu aplikace.
>
>

## <a name="next-steps"></a>Další postup
Další informace o některých základní koncepty, které jsou popsané v tomto článku najdete v tématu [spravovat aplikace pro více článcích prostředí](service-fabric-manage-multiple-environment-app-configuration.md).

Informace o dalším funkcím správy aplikace, které jsou k dispozici v sadě Visual Studio najdete v tématu [spravovat aplikace Service Fabric v sadě Visual Studio](service-fabric-manage-application-in-visual-studio.md).