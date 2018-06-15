---
title: Změna nastavení KVSActorStateProvider v Azure mikroslužeb | Microsoft Docs
description: Další informace o konfiguraci Azure Service Fabric stavová aktéři typu KVSActorStateProvider.
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: timlt
editor: ''
ms.assetid: dbed72f4-dda5-4287-bd56-da492710cd96
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: f29754c73db74f02214522a4de15904e65df0e98
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208256"
---
# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>Konfigurace Reliable Actors – KVSActorStateProvider
Výchozí konfigurace KVSActorStateProvider můžete upravit změnou souborech settings.xml souboru, který se vygeneruje v kořenu balíčku Microsoft Visual Studio ve složce Konfigurace pro zadaný objekt actor.

Modul runtime Azure Service Fabric předdefinované části z názvů v souboru souborech settings.xml a odebírá hodnoty konfigurace při vytvoření základní komponenty modulu runtime.

> [!NOTE]
> Proveďte **není** odstranit nebo změnit názvy části následující konfigurace v souborech settings.xml soubor, který se generuje ve řešení sady Visual Studio.
> 
> 

## <a name="replicator-security-configuration"></a>Konfigurace zabezpečení replikátoru
Konfigurace zabezpečení Replikátor slouží k zabezpečení komunikačního kanálu, který se používá během replikace. To znamená, že služby nelze vzájemně provoz replikace, zajišťující, že data, která vysokou dostupnost, je také zabezpečené.
Ve výchozím nastavení zabraňuje konfigurační oddíl prázdný zabezpečení zabezpečení replikace.

### <a name="section-name"></a>Název oddílu
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Replikátor konfigurace
Konfigurace Replikátor nakonfigurovat Replikátor, která zodpovídá za vytvoření zprostředkovatele stavu objektu Actor stavu vysoce spolehlivé.
Výchozí konfigurace je generován šablony sady Visual Studio a měla by stačit. Tato část pojednává o další konfigurace, které jsou k dispozici pro optimalizaci replikátor.

### <a name="section-name"></a>Název oddílu
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Názvy konfigurace
| Název | Jednotka | Výchozí hodnota | Poznámky |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Sekundy |0.015 |Časové období, pro které Replikátor na sekundární počká po přijetí operace před odesláním zpět na primární potvrzení. Další potvrzení k odeslání pro operace zpracování v rámci tohoto intervalu se odesílají jako jedna odpověď. |
| ReplicatorEndpoint |neuvedeno |Žádná výchozí hodnota – povinný parametr |IP adresa a port, který Replikátor primární a sekundární bude používat pro komunikaci s další replikátory na replice nastavit. To by měl odkazovat koncový bod TCP prostředků v service manifest. Odkazovat na [manifestu prostředky služby](service-fabric-service-manifest-resources.md) Další informace o definování koncový bod prostředků v service manifest. |
| RetryInterval |Sekundy |5 |Časové období, po které Replikátor znovu odesílá zprávy neobdrží potvrzení operace. |
| Maxreplicationmessagesize. |B |50 MB |Maximální velikost data replikace, která mohou být přenesena do jedné zprávy. |
| MaxPrimaryReplicationQueueSize |Počet operací |1024 |Maximální počet operací ve frontě primární. Operace uvolněno po primární Replikátor obdrží potvrzení ze sekundární replikátory. Tato hodnota musí být větší než 64 a druhou mocninou 2. |
| MaxSecondaryReplicationQueueSize |Počet operací |2 048 |Maximální počet operací v sekundární fronty. Operace uvolněno po provedení vysoce dostupný prostřednictvím trvalost stavu. Tato hodnota musí být větší než 64 a druhou mocninou 2. |

## <a name="store-configuration"></a>Konfigurace úložiště
Konfigurace úložiště se používají ke konfiguraci místního úložiště, který je použitý k zachování stavu, který je právě replikován.
Výchozí konfigurace je generován šablony sady Visual Studio a měla by stačit. Tato část pojednává o další konfigurace, které jsou k dispozici pro optimalizaci místní úložiště.

### <a name="section-name"></a>Název oddílu
&lt;ActorName&gt;ServiceLocalStoreConfig

### <a name="configuration-names"></a>Názvy konfigurace
| Název | Jednotka | Výchozí hodnota | Poznámky |
| --- | --- | --- | --- |
| MaxAsyncCommitDelayInMilliseconds |Milisekundy |200 |Nastaví maximální dávkování interval pro potvrzení trvanlivý místního úložiště. |
| MaxVerPages |Počet stránek |16384 |Maximální počet stránek verze místní uložení databáze. Určuje maximální počet nezpracovaných transakcí. |

## <a name="sample-configuration-file"></a>Vzorový konfigurační soubor
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
   </Section>
   <Section Name="MyActorServiceLocalStoreConfig">
      <Parameter Name="MaxVerPages" Value="8192" />
   </Section>
   <Section Name="MyActorServiceReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```
## <a name="remarks"></a>Poznámky
Parametr BatchAcknowledgementInterval řídí latenci replikace. Hodnota '0' výsledkem nejnižší možnou latenci, za cenu propustnost (jako další potvrzení zprávy musí být odeslána a zpracování jednotlivých obsahující méně potvrzení).
Čím větší hodnotu BatchAcknowledgementInterval, tím vyšší celkový replikace propustnost, za cenu vyšší latence operace. Výsledkem je přímo na latenci potvrzení transakcí.

