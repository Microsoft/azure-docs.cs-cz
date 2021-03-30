---
title: Odebrání typu uzlu v Azure Service Fabric | Microsoft Docs
description: Naučte se, jak odebrat typ uzlu z Service Fabric clusteru se systémem v Azure.
author: inputoutputcode
manager: sridmad
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: chrpap
ms.openlocfilehash: ede999bee9ce1a4a9dd10652a2c52a840d5b24be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88163573"
---
# <a name="how-to-remove-a-service-fabric-node-type"></a>Postup odebrání typu Service Fabric uzlu
Tento článek popisuje, jak škálovat cluster Azure Service Fabric odebráním existujícího typu uzlu z clusteru. Cluster Service Fabric je sada virtuálních nebo fyzických počítačů připojených k síti, do kterých se vaše mikroslužby nasazují a spravují. Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel. Sady škálování virtuálních počítačů jsou výpočetním prostředkem Azure, který můžete použít k nasazení a správě kolekce virtuálních počítačů jako sady. Každý typ uzlu, který je definovaný v clusteru Azure, je [nastavený jako samostatná sada škálování](service-fabric-cluster-nodetypes.md). Každý typ uzlu se pak dá spravovat samostatně. Po vytvoření clusteru Service Fabric můžete škálovat cluster vodorovně odebráním typu uzlu (sada škálování virtuálního počítače) a všech jeho uzlů.  Cluster můžete škálovat kdykoli, a to i v případě, že úlohy běží v clusteru.  I když se cluster škáluje, vaše aplikace se automaticky škálují.

> [!WARNING]
> Použití tohoto přístupu k odebrání typu uzlu z produkčního clusteru se nedoporučuje používat na častém základě. Jedná se o nebezpečný příkaz, protože odstraňuje prostředek sady škálování virtuálního počítače za typem uzlu. 

## <a name="durability-characteristics"></a>Charakteristiky odolnosti
Bezpečnost je při použití Remove-AzServiceFabricNodeType nastavena na vyšší prioritu. Typ uzlu musí být stříbrná nebo zlatá [úroveň odolnosti](./service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster), protože:
- Bronz vám neposkytuje žádné záruky týkající se ukládání informací o stavu.
- Stříbrná a zlatá odolnost proti změnám v sadě škálování.
- Gold taky poskytuje kontrolu nad aktualizacemi Azure pod sadou škálování.

Service Fabric "orchestruje" příslušné změny a aktualizace, aby nedošlo ke ztrátě dat. Pokud však odeberete typ uzlu s bronzovou odolností, může dojít ke ztrátě informací o stavu. Pokud odebíráte typ primárního uzlu a vaše aplikace je Bezstavová, je k DISA bronzová. Při spouštění stavových úloh v produkčním prostředí by měla být minimální konfigurace stříbrná. Podobně v produkčních scénářích by měl být primární typ uzlu vždy stříbrná nebo zlatá.

### <a name="more-about-bronze-durability"></a>Další informace o bronzové odolnosti

Při odebírání typu uzlu, který je bronz, se okamžitě najdou všechny uzly v typu uzlu. Service Fabric nepřesahují žádné aktualizace nastavení škály bronzových uzlů. všechny virtuální počítače se pak hned dostanou. Pokud na těchto uzlech máte nějaké stav, ztratí se data. Nyní i v případě, že jste byli bezstavní, všechny uzly v Service Fabric se účastní prstence, takže celé čtvrti může být ztracena, což může destabilizovat samotný cluster.

## <a name="remove-a-node-type"></a>Odebrání typu uzlu

1. Před zahájením procesu se prosím zajistěte, aby se tyto požadavky postaral.

    - Cluster je v pořádku.
    - Po odebrání typu uzlu bude mít ještě dostatek kapacity, např. počet uzlů k umístění požadovaného počtu replik.

2. Přesuňte všechny služby, které mají omezení umístění, aby používaly typ uzlu mimo typ uzlu.

    - Upravte manifest aplikace nebo služby tak, aby již neodkazoval na typ uzlu.
    - Nasaďte změnu.

    Pak ověřte, že:
    - Všechny výše změněné služby již nejsou spuštěny na uzlu, který patří do typu uzlu.
    - Všechny služby jsou v pořádku.

3. Zrušit označení typu uzlu jako neprimární (přeskočit pro neprimární typy uzlů)

    - Vyhledejte šablonu Azure Resource Manager použitou pro nasazení.
    - V části Service Fabric najdete část týkající se typu uzlu.
    - Změňte vlastnost Primary na hodnotu false. * * Neodstraňujte oddíl související s typem uzlu v této úloze.
    - Nasaďte upravenou šablonu Azure Resource Manager. * * V závislosti na konfiguraci clusteru může tento krok chvíli trvat.
    
    Pak ověřte, že:
    - Service Fabric oddíl na portálu na portálu označuje, že cluster je připravený.
    - Cluster je v pořádku.
    - Žádný z uzlů náležejících k typu uzlu není označen jako počáteční uzel.

4. Zakažte všechny uzly v typu uzlu.

    Připojte se ke clusteru pomocí PowerShellu a pak spusťte následující krok.
    
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

    - Pro bronzovou odolnost počkejte, než se všechny uzly dostanou do stavu zakázáno.
    - V případě odolnosti stříbrného a zlata budou některé uzly v neaktivním stavu a zbytek bude zakázán. Zkontrolujte kartu Podrobnosti uzlů v části zakázání stavu, pokud jsou všechny zablokované na zajištění kvora pro oddíly služby infrastruktury, a pak je bezpečné pokračovat.

5. Zastavte data pro typ uzlu.

    Připojte se ke clusteru pomocí PowerShellu a pak spusťte následující krok.
    
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
    
    Počkejte, dokud nebudou všechny uzly typu uzlu označeny.

6. Zruší přidělení uzlů v původní sadě škálování virtuálního počítače.
    
    Přihlaste se k předplatnému Azure, kde byla sada škálování nasazená, a odeberte sadu škálování virtuálního počítače. 

    ```powershell
    $scaleSetName="myscaleset"
    $scaleSetResourceType="Microsoft.Compute/virtualMachineScaleSets"
    
    Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
    ```

    
7. Odeberte data pro typ uzlu.

    Připojte se ke clusteru pomocí PowerShellu a pak spusťte následující krok.
    
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

    Počkejte, až se všechny uzly z clusteru odeberou. Uzly by se neměly zobrazovat v SFX.

8. Odebrat typ uzlu z Service Fabric oddíl

    - Vyhledejte šablonu Azure Resource Manager použitou pro nasazení.
    - V části Service Fabric najdete část týkající se typu uzlu.
    - Odeberte oddíl odpovídající typu uzlu.
    - V případě vysoké a vyšší odolnosti clusterů aktualizujte clusterový prostředek v šabloně a nakonfigurujte zásady stavu tak, aby ignorovaly stav aplikací prostředků infrastruktury:/systému přidáním `applicationDeltaHealthPolicies` v prostředku clusteru `properties` , jak je uvedeno níže. Níže uvedené zásady by měly ignorovat existující chyby, ale nedovolují nové chyby v oblasti stavu. 
 
 
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

    - Nasaďte upravenou šablonu Azure Resource Manager. * * Tento krok může trvat delší dobu, obvykle až dvě hodiny. Tento upgrade změní nastavení na InfrastructureService, a proto je nutné restartovat uzel. V tomto případě `forceRestart` se ignoruje. 
    Parametr `upgradeReplicaSetCheckTimeout` Určuje maximální dobu, po kterou Service Fabric čeká, až bude oddíl v bezpečném stavu, pokud ještě není v bezpečném stavu. Jakmile kontroly bezpečnosti projde pro všechny oddíly v uzlu, Service Fabric pokračuje s upgradem v tomto uzlu.
    Hodnota parametru `upgradeTimeout` může být snížena na 6 hodin, ale v případě maximálního zabezpečení 12 hodin by se měla použít.

    Pak ověřte, že:
    - Service Fabric prostředek na portálu se zobrazuje jako připravený.

9. Odeberte všechny odkazy na prostředky související s typem uzlu ze šablony ARM.

    - Vyhledejte šablonu Azure Resource Manager použitou pro nasazení.
    - Odeberte sadu škálování virtuálního počítače a další prostředky související s typem uzlu ze šablony.
    - Nasaďte změny.

    Potom:
    - Počkejte na dokončení nasazení.
    
10. Odeberte prostředky týkající se typu uzlu, které se již nepoužívají. Příklad Load Balancer a veřejná IP adresa. 

    - Chcete-li odebrat tyto prostředky, můžete použít stejný příkaz prostředí PowerShell, který se používá v kroku 6 určení konkrétního typu prostředku a verze rozhraní API. 

> [!Note]
> Tento krok je nepovinný, pokud jsou stejné Load Balancer a IP se znovu použijí mezi typy uzlů.

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [vlastnostech odolnosti](./service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)clusteru.
- Další informace o [typech uzlů a Virtual Machine Scale Sets](service-fabric-cluster-nodetypes.md).
- Přečtěte si další informace o [Service Fabric škálování clusteru](service-fabric-cluster-scaling.md).
