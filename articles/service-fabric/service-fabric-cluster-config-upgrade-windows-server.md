---
title: Upgradovat konfiguraci samostatného clusteru Azure Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak upgradovat konfiguraci, na kterém běží samostatného clusteru Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 88846845f1f8ffc71fb193e134a18ec38f619141
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2018
ms.locfileid: "51855179"
---
# <a name="upgrade-the-configuration-of-a-standalone-cluster"></a>Upgradovat konfiguraci samostatného clusteru 

Možnosti upgradu pro moderního systému, je klíčem k dlouhodobý úspěch vašeho produktu. Cluster Azure Service Fabric je prostředek, který vlastníte. Tento článek popisuje, jak upgradovat konfigurační nastavení samostatného clusteru Service Fabric.

## <a name="customize-cluster-settings-in-the-clusterconfigjson-file"></a>Nastavení clusteru v souboru ClusterConfig.json
Samostatné clustery se konfigurují *ClusterConfig.json* souboru. Další informace o různých nastaveních najdete v tématu [nastavení konfigurace pro samostatný cluster Windows](service-fabric-cluster-manifest.md).

Přidání, aktualizace nebo odebrání nastavení v `fabricSettings` části [vlastnosti clusteru](./service-fabric-cluster-manifest.md#cluster-properties) tématu *ClusterConfig.json*. 

Například následující kód JSON přidá nové nastavení *MaxDiskQuotaInMB* k *diagnostiky* části `fabricSettings`:

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Poté, co jste změnili nastavení v souboru ClusterConfig.json [testování konfigurace clusteru](#test-the-cluster-configuration) a potom [upgradovat konfiguraci clusteru](#upgrade-the-cluster-configuration) používat nastavení pro váš cluster. 

## <a name="test-the-cluster-configuration"></a>Test konfigurace clusteru
Než zahájíte configuration upgrade, můžete otestovat novou konfiguraci clusteru JSON spuštěním následujícího skriptu prostředí PowerShell v samostatném balíčku:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>
```

Nebo použijte tento skript:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>
```

Některé konfigurace se nedá upgradovat, jako jsou koncové body, název clusteru, IP adresu uzlu, atd. Novou konfiguraci clusteru JSON je testován oproti původní a vyvolá chyby v okně Powershellu, pokud se vyskytl problém.

## <a name="upgrade-the-cluster-configuration"></a>Upgradovat konfiguraci clusteru
Chcete-li upgradovat konfigurace upgradu clusteru, spusťte [Start ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade). Konfigurace upgradu je zpracovaných upgradovací doména podle upgradovací domény.

```powershell
Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

## <a name="upgrade-cluster-certificate-configuration"></a>Upgradovat konfiguraci certifikátů clusteru
Certifikát clusteru se používá k ověřování mezi uzly clusteru. Certifikáty vyměnit by měl provést další opatrně, protože selhání blokuje komunikaci mezi uzly clusteru.

Podporovány jsou čtyři parametry:  

* Jeden certifikát upgrade: způsob upgradu je certifikát (primární) -> certifikát B (primární) -> C certifikát (primární) ->...

* Double upgradu certifikátu: způsob upgradu je certifikát (primární) -> certifikátu (primární) a B (sekundární) -> certifikát B (primární) -> certifikát B (primární) a jazyka C (sekundární) -> C certifikát (primární) ->...

* Typ upgradu certifikátu: Konfigurace certifikátu na základě CommonName configuration <> – na základě kryptografického otisku certifikátu. Například kryptografický otisk certifikátu (primární) a kryptografický otisk B (sekundární) -> certifikát CommonName C.

* Upgrade kryptografický otisk vystavitele certifikátu: způsob upgradu je CN certifikát = A, IssuerThumbprint = IT1 (primární) -> certifikátů CN = A, IssuerThumbprint = IT1 IT2 (primární) -> CN certifikát = A, IssuerThumbprint = IT2 (primární).


## <a name="next-steps"></a>Další postup
* Zjistěte, jak přizpůsobit některé [nastavení clusteru Service Fabric](service-fabric-cluster-fabric-settings.md).
* Zjistěte, jak [škálování clusteru dovnitř a ven](service-fabric-cluster-scale-up-down.md).
* Další informace o [upgrady aplikací](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
