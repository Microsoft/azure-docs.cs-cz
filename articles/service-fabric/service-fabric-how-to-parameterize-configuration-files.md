---
title: Parametrizovat konfiguračních souborů v Azure Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak parametrizovat konfigurační soubory v Service Fabric.
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
ms.openlocfilehash: 9057cdc22e277e4e12e9f439f3fbe0c5a5cda2a2
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900509"
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Jak parametrizovat konfigurační soubory v Service Fabric

Tento článek ukazuje, jak parametrizovat konfiguračním souboru v Service Fabric.  Pokud si nejste již znáte základní koncepty správy aplikací pro víc prostředí, přečtěte si [Správa aplikací pro víc prostředí](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="procedure-for-parameterizing-configuration-files"></a>Postup pro parametrizaci konfiguračních souborů

V tomto příkladu je přepsat hodnotu konfigurace pomocí parametrů v nasazení vaší aplikace.

1. Otevřít  *<MyService>\PackageRoot\Config\Settings.xml* souboru v projektu služby.
1. Nastavte název konfiguračního parametru a hodnota, například velikost mezipaměti roven 25, přidáním následující kód XML:

  ```xml
    <Section Name="MyConfigSection">
      <Parameter Name="CacheSize" Value="25" />
    </Section>
  ```

1. Uložte soubor a zavřete ho.
1. Otevřít  *<MyApplication>\ApplicationPackageRoot\ApplicationManifest.xml* souboru.
1. V souboru ApplicationManifest.xml deklarovat parametr a výchozí hodnotu v `Parameters` elementu.  Doporučuje se, že název parametru obsahuje název služby (například "Moje_služba").

  ```xml
    <Parameters>
      <Parameter Name="MyService_CacheSize" DefaultValue="80" />
    </Parameters>
  ```
1. V `ServiceManifestImport` oddílu v souboru ApplicationManifest.xml přidat `ConfigOverride` element odkazující na konfigurační balíček, v části a parametr.

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
> V případě, kde můžete přidávat ConfigOverride vždy vybere Service Fabric parametry aplikace nebo výchozí hodnota zadaná v manifestu aplikace.
>
>

## <a name="next-steps"></a>Další postup
Informace o dalších možnostech správy aplikací, které jsou k dispozici v sadě Visual Studio najdete v tématu [Správa aplikací Service Fabric v sadě Visual Studio](service-fabric-manage-application-in-visual-studio.md).