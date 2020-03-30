---
title: Přidání typu uzlu do clusteru Azure Service Fabric
description: Zjistěte, jak škálovat cluster Service Fabric přidáním škálovací sady virtuálních strojů.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: 1e7bae89561d43d717eb4d15e95183761b077443
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463973"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>Škálování clusteru Service Fabric přidáním škálovací sady virtuálních strojů
Tento článek popisuje, jak škálovat cluster Azure Service Fabric přidáním nového typu uzlu do existujícího clusteru. Cluster Service Fabric je síťová sada virtuálních nebo fyzických počítačů, do kterých jsou vaše mikroslužby nasazeny a spravovány. Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel. Škálovací sady virtuálních počítačů jsou výpočetní prostředek Azure, který používáte k nasazení a správě kolekce virtuálních počítačů jako sady. Každý typ uzlu, který je definován v clusteru Azure, je [nastaven jako samostatná škálovací sada](service-fabric-cluster-nodetypes.md). Každý typ uzlu lze pak spravovat samostatně. Po vytvoření clusteru Service Fabric můžete škálovat cluster vodorovně přidáním nového typu uzlu (škálovací sada virtuálního počítače) do existujícího clusteru.  Cluster můžete kdykoli škálovat, i když jsou v clusteru spuštěny úlohy.  Při škálování clusteru se automaticky škálují také vaše aplikace.

## <a name="add-an-additional-scale-set-to-an-existing-cluster"></a>Přidání další škálovací sady do existujícího clusteru
Přidání nového typu uzlu (který je zálohován škálovací sadou virtuálního počítače) do existujícího clusteru je podobné [upgradu primárního typu uzlu](service-fabric-scale-up-node-type.md), s tím rozdílem, že nebudete používat stejný NodeTypeRef; Samozřejmě nebude zakázání žádné aktivně používané škálovací sady virtuálních strojů a neztratíte dostupnost clusteru, pokud neaktualizujete typ primárního uzlu. 

Vlastnost NodeTypeRef je deklarována v rámci vlastností rozšíření service fabric škálovky virtuálního počítače:
```json
<snip>
"publisher": "Microsoft.Azure.ServiceFabric",
     "settings": {
     "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
     "nodeTypeRef": "[parameters('vmNodeType2Name')]",
     "dataPath": "D:\\\\SvcFab",
     "durabilityLevel": "Silver",
<snip>
```

Navíc budete muset přidat tento nový typ uzlu do prostředku clusteru Service Fabric:

```json
<snip>
"nodeTypes": [
      {
      "name": "[parameters('vmNodeType2Name')]",
      "applicationPorts": {
                "endPort": "[parameters('nt2applicationEndPort')]",
                "startPort": "[parameters('nt2applicationStartPort')]"
      },
      "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
      "durabilityLevel": "Silver",
       "ephemeralPorts": {
                "endPort": "[parameters('nt2ephemeralEndPort')]",
                "startPort": "[parameters('nt2ephemeralStartPort')]"
      },
      "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
      "isPrimary": false,
      "vmInstanceCount": "[parameters('nt2InstanceCount')]"
},
<snip>
```

## <a name="next-steps"></a>Další kroky
* Naučte se [vertikálně navýšit kapacitu typu primárního uzlu](service-fabric-scale-up-node-type.md)
* Informace o [škálovatelnosti aplikací](service-fabric-concepts-scalability.md).
* [Škálování clusteru Azure dovnitř nebo ven](service-fabric-tutorial-scale-cluster.md).
* [Škálování clusteru Azure programově](service-fabric-cluster-programmatic-scaling.md) pomocí plynulé Azure compute SDK.
* [Škálování samostatného clusteru dovnitř nebo ven](service-fabric-cluster-windows-server-add-remove-nodes.md).

