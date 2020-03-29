---
title: Odebrání typu uzlu v azure service fabric | Dokumenty společnosti Microsoft
description: Zjistěte, jak odebrat typ uzlu z clusteru Service Fabric spuštěného v Azure.
author: inputoutputcode
manager: sridmad
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: chrpap
ms.openlocfilehash: 330b455a61c45ccdb59e5aef8162fd1b04859a00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969409"
---
# <a name="how-to-remove-a-service-fabric-node-type"></a>Odebrání typu uzlu Service Fabric
Tento článek popisuje, jak škálovat cluster Azure Service Fabric odebráním existujícího typu uzlu z clusteru. Cluster Service Fabric je síťová sada virtuálních nebo fyzických počítačů, do kterých jsou vaše mikroslužby nasazeny a spravovány. Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel. Škálovací sady virtuálních počítačů jsou výpočetní prostředek Azure, který používáte k nasazení a správě kolekce virtuálních počítačů jako sady. Každý typ uzlu, který je definován v clusteru Azure, je [nastaven jako samostatná škálovací sada](service-fabric-cluster-nodetypes.md). Každý typ uzlu lze pak spravovat samostatně. Po vytvoření clusteru Service Fabric můžete škálovat cluster vodorovně odebráním typu uzlu (škálovací sada virtuálních strojů) a všech uzlů.  Cluster můžete kdykoli škálovat, i když jsou v clusteru spuštěny úlohy.  Při škálování clusteru se automaticky škálují také vaše aplikace.

> [!WARNING]
> Pomocí tohoto přístupu k odebrání typu uzlu z produkčního clusteru se nedoporučuje používat často. Jedná se o nebezpečný příkaz, protože odstraní prostředek škálovací sady virtuálních strojů za typem uzlu. 

## <a name="durability-characteristics"></a>Charakteristiky trvanlivosti
Bezpečnost je upřednostněna před rychlostí při použití Remove-AzServiceFabricNodeType. Typ uzlu musí být úroveň [odolnosti](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)stříbra nebo zlata , protože:
- Bronz vám nedává žádné záruky o ukládání státních informací.
- Odolnost stříbra a zlata savkuje všechny změny v sadě měřítek.
- Gold také poskytuje kontrolu nad aktualizacemi Azure pod škálovací sadou.

Service Fabric "orchestruje" základní změny a aktualizace tak, aby data nebyla ztracena. Však při odebrání typu uzlu s bronzovou trvanlivost, může dojít ke ztrátě informace o stavu. Pokud odebíráte typ primárního uzlu a vaše aplikace je bezstavová, bronz je přijatelný. Při spuštění stavové úlohy v produkčním prostředí, minimální konfigurace by měla být silver. Podobně pro produkční scénáře primární typ uzlu by měl být vždy stříbrná nebo zlatá.

### <a name="more-about-bronze-durability"></a>Více o odolnosti bronze

Při odebrání typu uzlu, který je bronz, všechny uzly v typu uzlu přejít dolů okamžitě. Service Fabric nevytváří přesahy žádné bronzové uzly škálování nastavit aktualizace, tak všechny virtuální chody jít dolů okamžitě. Pokud jste měli něco stavového na těchto uzlech, data se ztratí. Nyní, i kdybyste byli bezstavové, všechny uzly v Service Fabric účastnit ringu, takže celé sousedství může být ztracena, což může destabilizovat clusteru sám.

## <a name="remove-a-node-type"></a>Odebrání typu uzlu

1. Prosím, postarejte se o tyto předpoklady před zahájením procesu.

    - Cluster je v pořádku.
    - Po odebrání typu uzlu bude stále dostatečná kapacita, např. počet uzlů pro umístění požadovaného počtu replik.

2. Přesuňte všechny služby, které mají omezení umístění použít typ uzlu z typu uzlu.

    - Upravte manifest aplikace / služby tak, aby již neodkazoval na typ uzlu.
    - Nasadit změnu.

    Pak ověřte, že:
    - Všechny výše upravené služby již nejsou spuštěny na uzlu, který patří k typu uzlu.
    - Všechny služby jsou zdravé.

3. Zrušení označení typu uzlu jako neprimárního (Přeskočit pro neprimární typy uzlů)

    - Vyhledejte šablonu Azure Resource Manageru používanou pro nasazení.
    - Část související s typem uzlu najdete v části Service Fabric.
    - Změna jePrimary vlastnost false. ** Neodstraňujte oddíl související s typem uzlu v tomto úkolu.
    - Nasaďte upravenou šablonu Správce prostředků Azure. ** V závislosti na konfiguraci clusteru může tento krok chvíli trvat.
    
    Pak ověřte, že:
    - Oddíl Service Fabric na portálu označuje, že cluster je připraven.
    - Cluster je v pořádku.
    - Žádný z uzlů patřících k typu uzlu nejsou označeny jako seed uzel.

4. Zakázat data pro typ uzlu.

    Připojte se ke clusteru pomocí Prostředí PowerShell a spusťte následující krok.
    
    ```powershell
    $nodeType = "" # specify the name of node type
    $nodes = Get-ServiceFabricNode
    
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
      }
    }
    ```

    - Pro bronzovou odolnost počkejte, až se všechny uzly dostanou do zakázaného stavu
    - Pro stříbrnou a zlatou odolnost, některé uzly půjdou do zdravotně postižených a zbytek bude v zakázaném stavu. Zkontrolujte kartu podrobností uzlů ve stavu zakázání, pokud jsou všechny přilepená na zajištění kvora pro oddíly služby infrastruktury, pak je bezpečné pokračovat.

5. Zastavit data pro typ uzlu.

    Připojte se ke clusteru pomocí Prostředí PowerShell a spusťte následující krok.
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
      }
    }
    ```
    
    Počkejte, až všechny uzly pro typ uzlu jsou označeny dolů.
    
6. Odeberte data pro typ uzlu.

    Připojte se ke clusteru pomocí Prostředí PowerShell a spusťte následující krok.
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
      }
    }
    ```

    Počkejte, až budou odebrány všechny uzly z clusteru. Uzly by neměly být zobrazeny v SFX.

7. Odebrat typ uzlu z části Service Fabric.

    - Vyhledejte šablonu Azure Resource Manageru používanou pro nasazení.
    - Část související s typem uzlu najdete v části Service Fabric.
    - Odeberte oddíl odpovídající typu uzlu.
    - Pouze pro clustery Silver a vyšší odolnost i pro clustery s vyšší odolností aktualizujte prostředek clusteru v šabloně a nakonfigurujte zásady stavu tak, aby ignorovaly prostředky infrastruktury:/Stav systémové aplikace přidáním `applicationDeltaHealthPolicies` prostředků pod prostředek `properties` clusteru, jak je uvedeno níže. Níže uvedené zásady by měly ignorovat existující chyby, ale neumožňují nové chyby stavu. 
 
 
     ```json
    "upgradeDescription":  
    { 
      "forceRestart": false, 
      "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
      "healthCheckWaitDuration": "00:05:00", 
      "healthCheckStableDuration": "00:05:00", 
      "healthCheckRetryTimeout": "00:45:00", 
      "upgradeTimeout": "12:00:00", 
      "upgradeDomainTimeout": "02:00:00", 
      "healthPolicy": { 
        "maxPercentUnhealthyNodes": 100, 
        "maxPercentUnhealthyApplications": 100 
      }, 
      "deltaHealthPolicy":  
      { 
        "maxPercentDeltaUnhealthyNodes": 0, 
        "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
        "maxPercentDeltaUnhealthyApplications": 0, 
        "applicationDeltaHealthPolicies":  
        { 
            "fabric:/System":  
            { 
                "defaultServiceTypeDeltaHealthPolicy":  
                { 
                        "maxPercentDeltaUnhealthyServices": 0 
                } 
            } 
        } 
      } 
    },
    ```

    - Nasaďte upravenou šablonu Správce prostředků Azure. ** Tento krok bude chvíli trvat, obvykle až dvě hodiny. Tento upgrade změní nastavení infrastructureservice, proto je nutné restartovat uzel. V tomto `forceRestart` případě je ignorována. 
    Parametr `upgradeReplicaSetCheckTimeout` určuje maximální dobu, po kterou service fabric čeká na oddíl v bezpečném stavu, pokud již není v bezpečném stavu. Jakmile bezpečnostní kontroly projít pro všechny oddíly na uzlu, Service Fabric pokračuje s upgradena tomto uzlu.
    Hodnota parametru `upgradeTimeout` může být snížena na 6 hodin, ale pro maximální bezpečnost by mělo být použito 12 hodin.

    Pak ověřte, že:
    - Service Fabric Resource in portal shows ready.

8. Odeberte všechny odkazy na prostředky vztahující se k typu uzlu.

    - Vyhledejte šablonu Azure Resource Manageru používanou pro nasazení.
    - Odeberte ze šablony škálovací sadu virtuálních strojů a další prostředky související s typem uzlu.
    - Nasadit změny.

    Potom:
    - Počkejte na dokončení nasazení.

## <a name="next-steps"></a>Další kroky
- Další informace o [vlastnostech odolnosti clusteru](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).
- Další informace o [typech uzlů a škálovacích sadách virtuálních počítačů](service-fabric-cluster-nodetypes.md).
- Další informace o [škálování clusteru Service Fabric](service-fabric-cluster-scaling.md).
