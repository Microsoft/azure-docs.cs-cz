---
title: Typy uzlů a škálovací sady virtuálních počítačů
description: Zjistěte, jak typy uzlů Azure Service Fabric souvisejí se škálovacími sadami virtuálních počítačů a jak se vzdáleně připojit k instanci škálovací sady nebo uzlu clusteru.
ms.topic: conceptual
ms.date: 03/23/2018
ms.author: pepogors
ms.custom: sfrev
ms.openlocfilehash: 37d4c27d3033545c523cefc2f317073af531f095
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78199712"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Typy uzlů Azure Service Fabric a škálovací sady virtuálních počítačů

[Škálovací sady virtuálních strojů](/azure/virtual-machine-scale-sets) jsou výpočetní prostředek Azure. Škálovací sady můžete použít k nasazení a správě kolekce virtuálních počítačů jako sady. Každý typ uzlu, který definujete v clusteru Azure Service Fabric nastaví přesně jednu škálovací sadu: více typů uzlů nelze zálohovat stejnou škálovací sadou a jeden typ uzlu by neměl (ve většině případů) být zálohována více škálovacích sad. Výjimkou je ve výjimečných situacích [vertikální škálování](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations) typu uzlu, pokud dočasně máte `nodeTypeRef` dvě škálovací sady se stejnou hodnotou, zatímco repliky jsou migrovány z původní na inovodškálovací sadu.

Runtime Service Fabric se nainstaluje na každém virtuálním počítači v měřítku nastaveném rozšířením *Microsoft.Azure.ServiceFabric* Virtual Machine. Můžete nezávisle škálovat každý typ uzlu nahoru nebo dolů, změnit skladovou položku operačního systému spuštěnou na každém uzlu clusteru, mít otevřené různé sady portů a používat různé metriky kapacity.

Následující obrázek znázorňuje cluster, který má dva typy uzlů s názvem *FrontEnd* a *Back-End*. Každý typ uzlu má pět uzlů.

![Cluster se dvěma typy uzlů][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Mapování instancí škálovací sady virtuálních strojů na uzly

Jak je znázorněno na předchozím obrázku, instance škálovací sady začínají na instanci 0 a pak se zvýší o 1. Číslování se projeví v názvech uzlů. Například uzel BackEnd_0 je instance 0 škálovací sady Back-End. Tato konkrétní škálovací sada má pět instancí s názvem BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 a BackEnd_4.

Při škálování škálovací sady se vytvoří nová instance. Název instance nové škálovací sady je obvykle název škálovací sady plus číslo další instance. V našem příkladu je to BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Mapovat škálovací sady nástrojů pro vyrovnání zatížení na typy uzlů a škálovací sady

Pokud jste svůj cluster nasadili na webu Azure Portal nebo jste použili ukázkovou šablonu Azure Resource Manager, jsou uvedeny všechny prostředky v rámci skupiny prostředků. Můžete vidět navorače zatížení pro každou škálovací sadu nebo typ uzlu. Název pro vyrovnávání zatížení používá následující formát: **LB-&lt;název typu&gt;uzlu**. Příkladem je LB-sfcluster4doc-0, jak je znázorněno na následujícím obrázku:

![Prostředky][Resources]

## <a name="service-fabric-virtual-machine-extension"></a>Rozšíření virtuálního počítače Service Fabric

Rozšíření virtuálního počítače Service Fabric se používá k zavádění service fabric na virtuální počítače Azure a nakonfigurovat zabezpečení uzlu.

Následuje úryvek rozšíření virtuálního počítače Service Fabric:

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

Následují popisy vlastností:

| **Název** | **Povolené hodnoty** | **Pokyny nebo krátký popis** |
| --- | --- | --- | --- |
| jméno | řetězec | Jedinečný název rozšíření |
| type | "ServiceFabricLinuxNode" nebo "ServiceFabricWindowsNode" | Identifikuje, že tkanina Služby Operačního se zavádí do |
| autoupgradeMinorVersion | true nebo false | Povolit automatický upgrade dílčích verzí sf runtime |
| vydavatel | Microsoft.Azure.ServiceFabric | Název vydavatele rozšíření Service Fabric |
| clusterEndpont | řetězec | IDENTIFIKÁTOR URI:PORT do koncového bodu správy |
| nodeTypeRef | řetězec | Název typu nodeType |
| durabilityLevel | bronz, stříbro, zlato, platina | Čas povolen pro pozastavení neměnné infrastruktury Azure |
| enableParallelJobs | true nebo false | Povolit výpočetní paralelní úlohy, jako je odebrání virtuálního počítače a restartování virtuálního počítače ve stejné škálovací sadě paralelně |
| nicPrefixOverride | řetězec | Předpona podsítě jako "10.0.0.0/24" |
| commonNames | řetězec[] | Běžné názvy nainstalovaných certifikátů clusteru |
| x509Název store | řetězec | Název obchodu, kde je umístěn nainstalovaný certifikát clusteru |
| typeHandlerVersion | 1.1 | Verze rozšíření. 1.0 klasická verze rozšíření se doporučuje upgradovat na 1.1 |
| datová cesta | řetězec | Cesta k jednotce, která slouží k uložení stavu pro systémové služby Service Fabric a data aplikací.

## <a name="next-steps"></a>Další kroky

* Podívejte se na [přehled funkce "Nasadit kdekoli" a porovnání s clustery spravovanými azure](service-fabric-deploy-anywhere.md).
* Informace o [zabezpečení clusteru](service-fabric-cluster-security.md).
* [Vzdálené připojení](service-fabric-cluster-remote-connect-to-azure-cluster-node.md) ke konkrétní instanci škálovací sady
* [Aktualizace hodnot rozsahu portů RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) na virtuálních počítačích clusteru po nasazení
* [Změna uživatelského jména a hesla správce](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) pro virtuální počítače clusteru

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
