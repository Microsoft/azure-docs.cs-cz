---
title: Změnit nastavení KVSActorStateProvider v Azure Service Fabric actors | Dokumentace Microsoftu
description: Přečtěte si o Azure Service Fabric actors stavové typu KVSActorStateProvider konfigurace.
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
ms.openlocfilehash: 81c09d61a5213319fa01ef5cc7070ffe385bbab1
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44049506"
---
# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>Konfigurace Reliable Actors – KVSActorStateProvider
Výchozí konfigurace KVSActorStateProvider můžete upravit změnou souboru settings.xml, který je vygenerován v kořenovém adresáři balíčku Microsoft Visual Studio ve složce Konfigurace pro zadaný objekt actor.

Modul runtime Azure Service Fabric vyhledá názvy předdefinovaných oddílů v souboru settings.xml a využívá hodnoty konfigurace při vytvoření základní komponenty prostředí runtime.

> [!NOTE]
> Proveďte **není** odstranit ani změnit názvy oddílů následující konfigurace v souboru settings.xml, který je vygenerován v řešení sady Visual Studio.
> 
> 

## <a name="replicator-security-configuration"></a>Konfigurace zabezpečení replikátoru
Konfigurace zabezpečení replikátoru slouží k zabezpečení komunikačního kanálu, který se používá během replikace. To znamená, že služby neuvidí druhé strany provoz replikace, zajišťující, že je také data, která je k dispozici vysoce zabezpečené.
Ve výchozím nastavení zabraňuje oddíl konfigurace prázdný zabezpečení zabezpečení replikace.

> [!IMPORTANT]
> Certifikáty na uzly s Linuxem, musí být ve formátu PEM. Další informace o vyhledání a konfigurace certifikátů pro Linux najdete v tématu [konfigurace certifikátů v Linuxu](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="section-name"></a>Název oddílu
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Konfigurace replikace
Konfigurace replikace nakonfigurovat replikátoru je zodpovědný za vytváření vysoce spolehlivých stavu zprostředkovatele stavu objektu Actor.
Výchozí konfigurace vygeneruje šablony sady Visual Studio a měl postačit. Tato část se hovoří o dalším konfiguracím, které jsou k dispozici pro vyladění replikátoru.

### <a name="section-name"></a>Název oddílu
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Názvy konfigurace
| Název | Jednotka | Výchozí hodnota | Poznámky |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Sekundy |0.015 |Časové období, pro které Replikátor na sekundární čeká po přijetí operace před odesláním zpět na primární potvrzení. Další potvrzení k odeslání pro operace zpracování v rámci tohoto intervalu se odesílají jako jednu odpověď. |
| ReplicatorEndpoint |neuvedeno |Žádná výchozí hodnota--povinný parametr |Nastavení IP adresy a portu, které Replikátor primárního a sekundárního se používají ke komunikaci s další replikátorů v replice. To by měla odkazovat na koncový bod TCP prostředků v manifestu služby. Odkazovat na [prostředky manifestu služby](service-fabric-service-manifest-resources.md) Další informace o definování koncový bod prostředků v manifestu služby. |
| retryInterval |Sekundy |5 |Časové období, po jejímž uplynutí Replikátor znovu odesílá zprávu neobdrží potvrzení operace. |
| MaxReplicationMessageSize |B |50 MB |Maximální velikost dat replikace, která mohou být přenesena do jedné zprávy. |
| MaxPrimaryReplicationQueueSize |Počet operací |1024 |Maximální počet operací ve frontě primární. Operace je uvolněna až primární Replikátor obdrží od všech sekundárních replikátorů potvrzení. Tato hodnota musí být větší než 64 a mocninou čísla 2. |
| MaxSecondaryReplicationQueueSize |Počet operací |2 048 |Maximální počet operací ve frontě sekundární. Operace je uvolněna až po zajištění vysoké dostupnosti prostřednictvím trvalého stavu. Tato hodnota musí být větší než 64 a mocninou čísla 2. |

## <a name="store-configuration"></a>Konfigurace Store
Konfigurace Store slouží ke konfiguraci místního úložiště, který se používá k uchování stav, který je právě replikován.
Výchozí konfigurace vygeneruje šablony sady Visual Studio a měl postačit. Tato část pojednává o dalším konfiguracím, které jsou k dispozici pro ladění místního úložiště.

### <a name="section-name"></a>Název oddílu
&lt;ActorName&gt;ServiceLocalStoreConfig

### <a name="configuration-names"></a>Názvy konfigurace
| Název | Jednotka | Výchozí hodnota | Poznámky |
| --- | --- | --- | --- |
| MaxAsyncCommitDelayInMilliseconds |Milisekundy |200 |Nastaví maximální interval potvrzení trvalý místního úložiště do dávek. |
| MaxVerPages |Počet stránek |16384 |Maximální počet stránek verze místní uložení databáze. Určuje maximální počet existují nevyřízené transakce. |

## <a name="sample-configuration-file"></a>Ukázkový konfigurační soubor
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
Latence replikace BatchAcknowledgementInterval parametr ovládací prvky. Hodnota '0' za následek nejnižší možnou latenci, a to za cenu propustnost (jako další zprávy potvrzení musí být odeslána a zpracována, každá obsahuje méně potvrzení).
Čím větší hodnotu BatchAcknowledgementInterval, tím vyšší celkovou replikaci propustnost, za cenu vyšší latence operace. Výsledkem je přímo na latenci potvrzení transakcí.

