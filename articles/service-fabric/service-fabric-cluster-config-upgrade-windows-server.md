---
title: Upgrade konfigurace samostatného clusteru
description: Naučte se upgradovat konfiguraci, která spouští samostatný cluster Service Fabric.
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 3cb1d40f5b32415588d3fd5a108967cfb4c0e534
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91842610"
---
# <a name="upgrade-the-configuration-of-a-standalone-cluster"></a>Upgrade konfigurace samostatného clusteru 

Pro všechny moderní systémy je možnost upgradu klíčem na dlouhodobou úspěšnost vašeho produktu. Cluster Azure Service Fabric je prostředek, který vlastníte. Tento článek popisuje, jak upgradovat nastavení konfigurace samostatného clusteru Service Fabric.

## <a name="customize-cluster-settings-in-the-clusterconfigjson-file"></a>Přizpůsobení nastavení clusteru v ClusterConfig.jssouboru
Samostatné clustery jsou nakonfigurovány prostřednictvím *ClusterConfig.jsv* souboru. Další informace o různých nastaveních najdete v tématu [nastavení konfigurace samostatného clusteru Windows](service-fabric-cluster-manifest.md).

Můžete přidat, aktualizovat nebo odebrat nastavení v části v části `fabricSettings` [Vlastnosti clusteru](./service-fabric-cluster-manifest.md#cluster-properties) v *ClusterConfig.js*. 

Například následující kód JSON přidá nové nastavení *MaxDiskQuotaInMB* do části *Diagnostika* v části `fabricSettings` :

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

Po úpravě nastavení v ClusterConfig.jssouboru proveďte [Test konfigurace clusteru](#test-the-cluster-configuration) a pak [upgradujte konfiguraci clusteru](#upgrade-the-cluster-configuration) , aby se nastavení projevilo u vašeho clusteru. 

## <a name="test-the-cluster-configuration"></a>Test konfigurace clusteru
Než začnete s upgradem konfigurace, můžete otestovat nový formát JSON konfigurace clusteru spuštěním následujícího skriptu PowerShellu v samostatném balíčku:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>
```

Nebo použijte tento skript:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>
```

Některé konfigurace není možné upgradovat, jako jsou koncové body, název clusteru, IP adresa uzlu atd. Nový kód JSON konfigurace clusteru se testuje proti starému a vyvolá chyby v okně PowerShellu, pokud dojde k nějakému problému.

## <a name="upgrade-the-cluster-configuration"></a>Upgrade konfigurace clusteru
Pokud chcete upgradovat upgrade konfigurace clusteru, spusťte rutinu [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade). Upgrade konfigurace je zpracována upgradovací doména podle upgradované domény.

```powershell
Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

## <a name="upgrade-cluster-certificate-configuration"></a>Upgradovat konfiguraci certifikátu clusteru
Certifikát clusteru se používá pro ověřování mezi uzly clusteru. Změna certifikátu by se měla provádět s mimořádnou opatrností, protože selhání blokuje komunikaci mezi uzly clusteru.

Podporovány jsou čtyři možnosti:  

* Upgrade jednoho certifikátu: cesta pro upgrade je certifikát A (primární) – > certifikát B (primární) – > certifikát C (primární) – >....

* Dvojitý upgrade certifikátu: cesta pro upgrade je certifikát A (primární) – > certifikát a (primární) a B (sekundární) – > certifikát B (primární) – > certifikát B (primární) a C (sekundární) – > certifikát C (primární) – >....

* Upgrade typu certifikátu: Konfigurace certifikátu založená na kryptografických Otiskech <-> konfiguraci certifikátu založeného na společném certifikátu. Například kryptografický otisk certifikátu (primární) a kryptografický otisk B (sekundární) – > Common C certifikátu.

* Upgrade kryptografického otisku vystavitele certifikátu: cesta pro upgrade je certifikát CN = A, IssuerThumbprint = IT1 (primární)-> certifikát CN = A, IssuerThumbprint = IT1, IT2 (primární)-> certifikát CN = A, IssuerThumbprint = IT2 (primární).


## <a name="next-steps"></a>Další kroky
* Přečtěte si, jak přizpůsobit některá [Service Fabric nastavení clusteru](service-fabric-cluster-fabric-settings.md).
* Přečtěte si, jak [škálovat cluster na úrovni a](service-fabric-cluster-scale-in-out.md).
* Seznamte se s [upgrady aplikací](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
