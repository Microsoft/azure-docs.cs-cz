---
title: Upgrade konfigurace samostatného clusteru
description: Zjistěte, jak upgradovat konfiguraci, která spouští samostatný cluster Service Fabric.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 8e7e01dac29cb9ba91c83270dac4e46c73b2089e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610114"
---
# <a name="upgrade-the-configuration-of-a-standalone-cluster"></a>Upgrade konfigurace samostatného clusteru 

Pro každý moderní systém je schopnost upgradu klíčem k dlouhodobému úspěchu vašeho produktu. Cluster Azure Service Fabric je prostředek, který vlastníte. Tento článek popisuje, jak inovovat nastavení konfigurace samostatného clusteru Service Fabric.

## <a name="customize-cluster-settings-in-the-clusterconfigjson-file"></a>Přizpůsobení nastavení clusteru v souboru ClusterConfig.json
Samostatné clustery jsou konfigurovány prostřednictvím souboru *ClusterConfig.json.* Další informace o různých nastaveních naleznete v [tématu Nastavení konfigurace samostatného clusteru Windows](service-fabric-cluster-manifest.md).

Nastavení můžete přidat, aktualizovat nebo `fabricSettings` odebrat v části v části [Vlastnosti clusteru](./service-fabric-cluster-manifest.md#cluster-properties) v *souboru ClusterConfig.json*. 

Například následující JSON přidá nové nastavení *MaxDiskQuotaInMB* do `fabricSettings`části *Diagnostika* v části :

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

Po úpravě nastavení v souboru ClusterConfig.json [otestujte konfiguraci clusteru](#test-the-cluster-configuration) a [potom inovujte konfiguraci clusteru](#upgrade-the-cluster-configuration) tak, aby se nastavení použilo pro váš cluster. 

## <a name="test-the-cluster-configuration"></a>Testování konfigurace clusteru
Před zahájením upgradu konfigurace můžete otestovat novou konfiguraci clusteru JSON spuštěním následujícího skriptu prostředí PowerShell v samostatném balíčku:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>
```

Nebo použijte tento skript:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>
```

Některé konfigurace nelze upgradovat, například koncové body, název clusteru, IP uzel atd. Nová konfigurace clusteru JSON je testován proti staré a vyvolá chyby v okně Prostředí PowerShell, pokud dojde k problému.

## <a name="upgrade-the-cluster-configuration"></a>Upgrade konfigurace clusteru
Chcete-li inovovat upgrade konfigurace clusteru, spusťte [spuštění nástroje Start-ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade). Upgrade konfigurace je zpracován a upgrade domény podle domény upgradu.

```powershell
Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

## <a name="upgrade-cluster-certificate-configuration"></a>Upgrade konfigurace certifikátu clusteru
Certifikát clusteru se používá pro ověřování mezi uzly clusteru. Přechod certifikátu by měl být prováděn s mimořádnou opatrností, protože selhání blokuje komunikaci mezi uzly clusteru.

Jsou podporovány čtyři možnosti:  

* Upgrade jednoho certifikátu: Cesta k upgradu je certifikát A (primární) -> certifikát B (primární) -> certifikát C (primární) ->....

* Dvojitý upgrade certifikátu: Cesta upgradu je certifikát A (primární) -> certifikát A (primární) a B (sekundární) -> certifikát B (primární) -> certifikát B (primární) a C (sekundární) -> certifikát C (primární) ->....

* Upgrade typu certifikátu: Konfigurace certifikátu založená na kryptografickém tisku <-> konfigurace certifikátu commonname. Například kryptografický otisk certifikátu A (primární) a kryptografický otisk B (sekundární) -> certifikát u společného názvu C.

* Upgrade kryptografického náhledu vystavittele certifikátu: Cesta upgradu je Certifikát CN=A,IssuerThumbprint=IT1 (Primární) -> certifikát CN=A,IssuerThumbprint=IT1,IT2 (Primární) -> Certifikát CN=A,IssuerThumbprint=IT2 (primární).


## <a name="next-steps"></a>Další kroky
* Přečtěte si, jak přizpůsobit některá [nastavení clusteru Service Fabric](service-fabric-cluster-fabric-settings.md).
* Přečtěte si, jak [škálovat cluster y dovnitř a ven](service-fabric-cluster-scale-up-down.md).
* Informace o [upgradech aplikací](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
