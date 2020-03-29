---
title: Parametrize konfiguračních souborů ve službě Azure Fabric
description: Naučte se parametrizovat konfigurační soubory v Service Fabric, což je užitečná technika při správě více prostředí.
author: mikkelhegn
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: mikhegn
ms.openlocfilehash: 4e96a732cffd70b0a5c24e7ebafe214297a72720
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644626"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Jak parametrizovat konfigurační soubory v service fabric

Tento článek ukazuje, jak parametrizovat konfigurační soubor v Service Fabric.  Pokud ještě nejste obeznámeni se základními koncepty správy aplikací pro více prostředí, přečtěte [si článek Správa aplikací pro více prostředí](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="procedure-for-parameterizing-configuration-files"></a>Postup pro parametrizaci konfiguračních souborů

V tomto příkladu přepsat hodnotu konfigurace pomocí parametrů v nasazení aplikace.

1. Otevřete soubor * \<MyService>\PackageRoot\Config\Settings.xml* v projektu služby.
1. Přidáním následujícího xml nastavte název a hodnotu konfiguračního parametru, například velikost mezipaměti rovnou 25:

   ```xml
    <Section Name="MyConfigSection">
      <Parameter Name="CacheSize" Value="25" />
    </Section>
   ```

1. Uložte soubor a zavřete ho.
1. Otevřete soubor * \<MyApplication>\ApplicationPackageRoot\ApplicationManifest.xml.*
1. V souboru ApplicationManifest.xml deklarujte parametr `Parameters` a výchozí hodnotu v elementu.  Doporučuje se, aby název parametru obsahoval název služby (například "MyService").

   ```xml
    <Parameters>
      <Parameter Name="MyService_CacheSize" DefaultValue="80" />
    </Parameters>
   ```
1. V `ServiceManifestImport` části souboru ApplicationManifest.xml `ConfigOverrides` přidejte `ConfigOverride` a element odkazující na konfigurační balíček, oddíl a parametr.

   ```xml
    <ConfigOverrides>
      <ConfigOverride Name="Config">
          <Settings>
            <Section Name="MyConfigSection">
                <Parameter Name="CacheSize" Value="[MyService_CacheSize]" />
            </Section>
          </Settings>
      </ConfigOverride>
    </ConfigOverrides>
   ```

> [!NOTE]
> V případě, že přidáte ConfigOverride, Service Fabric vždy zvolí parametry aplikace nebo výchozí hodnotu zadanou v manifestu aplikace.
>
>

## <a name="next-steps"></a>Další kroky
Informace o dalších možnostech správy aplikací, které jsou dostupné v sadě Visual Studio, naleznete v [tématu Správa aplikací Service Fabric v sadě Visual Studio](service-fabric-manage-application-in-visual-studio.md).
