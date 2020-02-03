---
title: Typy uzlů a škálovací sady virtuálních počítačů
description: Přečtěte si, jak se typy uzlů Azure Service Fabric vztahují k sadám škálování virtuálních počítačů a jak se vzdáleně připojit k instanci sady škálování nebo uzlu clusteru.
ms.topic: conceptual
ms.date: 03/23/2018
ms.author: pepogors
ms.custom: sfrev
ms.openlocfilehash: e751b3dd9108d364c900bbd059dc89c1eb3770c4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722335"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Typy uzlů Service Fabric Azure a Virtual Machine Scale Sets

Služby [Virtual Machine Scale Sets](/azure/virtual-machine-scale-sets) jsou výpočetním prostředkem Azure. Sady škálování můžete použít k nasazení a správě kolekce virtuálních počítačů jako sady. Každý typ uzlu, který definujete v clusteru Azure Service Fabric, nastaví samostatné škálování. Modul runtime Service Fabric se instaluje na každý virtuální počítač ve škále nastaveném rozšířením virtuálního počítače *Microsoft. Azure. ServiceFabric* . Můžete nezávisle škálovat jednotlivé typy uzlů nahoru nebo dolů, měnit skladovou jednotku operačního systému spuštěnou na každém uzlu clusteru, mít různé sady portů otevřené a používat jiné metriky kapacity.

Následující obrázek ukazuje cluster, který má dva typy uzlů s názvem *front* -end a *back-end*. Každý typ uzlu má pět uzlů.

![Cluster, který má dva typy uzlů][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Mapování instancí sady škálování virtuálního počítače na uzly

Jak je znázorněno na předchozím obrázku, škálované instance sad se spustí v instanci 0 a pak se zvýší o 1. Číslování se projeví v názvech uzlů. Například Node BackEnd_0 je instance 0 sady škálování back-endu. Tato konkrétní sada škálování má pět instancí s názvem BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 a BackEnd_4.

Při horizontálním navýšení kapacity se vytvoří nová instance. Nový název instance sady škálování je obvykle název sady škálování a další číslo instance. V našem příkladu je BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Škálování mapy – nastavení nástrojů pro vyrovnávání zatížení na typy uzlů a sady škálování

Pokud jste cluster nasadili v Azure Portal nebo jste použili ukázkovou šablonu Azure Resource Manager, zobrazí se všechny prostředky v rámci skupiny prostředků. Nástroje pro vyrovnávání zatížení můžete zobrazit pro jednotlivé sady škálování nebo typy uzlů. Název nástroje pro vyrovnávání zatížení používá následující formát: **&lt;název typu pro typ uzlu&gt;** . Příklad je sfcluster4doc-0, jak je znázorněno na následujícím obrázku:

![Zdroje a prostředky][Resources]

## <a name="service-fabric-virtual-machine-extension"></a>Service Fabric rozšíření virtuálního počítače

Service Fabric rozšíření virtuálního počítače se používá ke spuštění Service Fabric do Azure Virtual Machines a konfiguraci zabezpečení uzlů.

Následuje fragment Service Fabric rozšíření virtuálního počítače:

```json
"extensions": [
  {
    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
    "properties": {
      "type": "ServiceFabricLinuxNode",
      "autoUpgradeMinorVersion": true,
      "protectedSettings": {
        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
       },
       "publisher": "Microsoft.Azure.ServiceFabric",
       "settings": {
         "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
         "nodeTypeRef": "[variables('vmNodeType0Name')]",
         "durabilityLevel": "Silver",
         "enableParallelJobs": true,
         "nicPrefixOverride": "[variables('subnet0Prefix')]",
         "dataPath": "D:\\\\SvcFab",
         "certificate": {
           "commonNames": [
             "[parameters('certificateCommonName')]"
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
         }
       },
       "typeHandlerVersion": "1.1"
     }
   },
```

Níže jsou uvedeny popisy vlastností:

| **Název** | **Povolené hodnoty** | **Doprovodné materiály nebo krátký popis** |
| --- | --- | --- | --- |
| jméno | řetězec | jedinečný název pro rozšíření |
| type | "ServiceFabricLinuxNode" nebo "ServiceFabricWindowsNode" | Identifikuje Service Fabric operačního systému. |
| autoUpgradeMinorVersion | true nebo false | Povolit automatický upgrade dílčích verzí SF modulu runtime |
| publisher | Microsoft.Azure.ServiceFabric | Název vydavatele rozšíření Service Fabric |
| clusterEndpont | řetězec | Identifikátor URI: PORT pro koncový bod správy |
| nodeTypeRef | řetězec | název nodeType |
| durabilityLevel | bronzová, stříbrná, zlatá, Platinum | doba, po kterou je možné pozastavit neproměnlivou infrastrukturu Azure |
| enableParallelJobs | true nebo false | Povolte výpočetní ParallelJobs, jako je například odebrat virtuální počítač a restartovat virtuální počítač ve stejné sadě škálování paralelně. |
| nicPrefixOverride | řetězec | Předpona podsítě jako 10.0.0.0/24 |
| commonNames | řetězec [] | Běžné názvy instalovaných certifikátů clusteru |
| x509StoreName | řetězec | Název úložiště, ve kterém je umístěný nainstalovaný certifikát clusteru |
| typeHandlerVersion | 1.1 | Verze rozšíření pro upgrade na 1,1 se doporučuje používat klasickou verzi 1,0. |
| dataPath | řetězec | Cesta k jednotce, která slouží k uložení stavu pro Service Fabric systémových služeb a dat aplikací.

## <a name="next-steps"></a>Další kroky

* Podívejte se na [Přehled funkce nasazení kamkoli a porovnání s clustery spravovanými Azure](service-fabric-deploy-anywhere.md).
* Přečtěte si o [zabezpečení clusteru](service-fabric-cluster-security.md).
* [Vzdálené připojení](service-fabric-cluster-remote-connect-to-azure-cluster-node.md) ke konkrétní instanci sady škálování
* [Aktualizace hodnot rozsahu portů RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) na virtuálních počítačích clusteru po nasazení
* [Změna uživatelského jména a hesla správce](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) pro virtuální počítače clusteru

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
