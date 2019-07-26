---
title: Parametrizovat konfigurační soubory v Azure Service Fabric | Microsoft Docs
description: Naučte se, jak parametrizovat konfigurační soubory v Service Fabric.
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/09/2018
ms.author: mikhegn
ms.openlocfilehash: dad497978de7187177998524db3b2f2ee448c717
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464787"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Jak parametrizovat konfigurační soubory v Service Fabric

V tomto článku se dozvíte, jak parametrizovat konfigurační soubor v Service Fabric.  Pokud ještě nejste obeznámeni se základními koncepty správy aplikací pro více prostředí, přečtěte si téma [Správa aplikací pro více prostředí](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="procedure-for-parameterizing-configuration-files"></a>Postup pro konfigurační soubory Parametrizace

V tomto příkladu přepíšete konfigurační hodnotu pomocí parametrů ve vašem nasazení aplikace.

1. V projektu služby otevřete soubor  *mojesluzba>\PackageRoot\Config\Settings.XML.\<*
1. Přidáním následujícího kódu XML nastavte název a hodnotu konfiguračního parametru, například velikost mezipaměti rovnou 25.

   ```xml
    <Section Name="MyConfigSection">
      <Parameter Name="CacheSize" Value="25" />
    </Section>
   ```

1. Uložte soubor a zavřete ho.
1. Otevřete soubor MyApplication > \ApplicationPackageRoot\ApplicationManifest.XML.  *\<*
1. V souboru souboru ApplicationManifest. XML deklarujte parametr a výchozí hodnotu v `Parameters` elementu.  Doporučuje se, aby název parametru obsahoval název služby (například "Mojesluzba").

   ```xml
    <Parameters>
      <Parameter Name="MyService_CacheSize" DefaultValue="80" />
    </Parameters>
   ```
1. V části souboru souboru ApplicationManifest. XML `ConfigOverrides` přidejte element and `ConfigOverride` , který odkazuje na konfigurační balíček, část a parametr. `ServiceManifestImport`

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

## <a name="next-steps"></a>Další postup
Informace o dalších možnostech správy aplikací, které jsou k dispozici v sadě Visual Studio, najdete v tématu [Správa aplikací Service Fabric v sadě Visual Studio](service-fabric-manage-application-in-visual-studio.md).
