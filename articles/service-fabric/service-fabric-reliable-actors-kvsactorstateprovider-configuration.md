---
title: Změna nastavení KVSActorStateProvider
description: Další informace o konfiguraci Azure Service Fabric stavové objekty actor typu KVSActorStateProvider.
author: sumukhs
ms.topic: conceptual
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: cdb115bd57cf3d5af4388f4efa03c2522feef9ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609770"
---
# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>Konfigurace spolehlivých herců – KVSActorStateProvider
Výchozí konfiguraci objektu KVSActorStateProvider můžete změnit změnou souboru settings.xml, který je generován v kořenovém adresáři balíčku sady Microsoft Visual Studio ve složce Config pro zadaný objekt actor.

Prostředí Azure Service Fabric hledá předdefinované názvy oddílů v souboru settings.xml a spotřebovává hodnoty konfigurace při vytváření podkladových komponent runtime.

> [!NOTE]
> Neodstraňujte **ani neupravujte** názvy oddílů následujících konfigurací v souboru settings.xml, který je generován v řešení sady Visual Studio.
> 
> 

## <a name="replicator-security-configuration"></a>Konfigurace zabezpečení replikátoru
Konfigurace zabezpečení replikátoru se používají k zabezpečení komunikačního kanálu, který se používá během replikace. To znamená, že služby nemohou zobrazit navzájem replikační provoz, zajištění, že data, která je vysoce dostupné je také zabezpečené.
Ve výchozím nastavení brání prázdný oddíl konfigurace zabezpečení zabezpečení zabezpečení zabezpečení.

> [!IMPORTANT]
> V uzlech Linuxu musí být certifikáty ve formátu PEM. Další informace o vyhledání a konfiguraci certifikátů pro Linux najdete [v tématu Konfigurace certifikátů v Linuxu](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="section-name"></a>Název oddílu
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Konfigurace replikátoru
Konfigurace replikátoru nakonfigurují replikátor, který je zodpovědný za to, že stav zprostředkovatele stavu actor je vysoce spolehlivý.
Výchozí konfigurace je generována šablonou sady Visual Studio a měla by stačit. Tato část hovoří o další konfigurace, které jsou k dispozici pro ladění replikátoru.

### <a name="section-name"></a>Název oddílu
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Názvy konfigurací
| Name (Název) | Jednotka | Výchozí hodnota | Poznámky |
| --- | --- | --- | --- |
| Interval potvrzení dávky |Sekundy |0.015 |Časové období, pro které replikátor na sekundární čeká po přijetí operace před odesláním zpět potvrzení primární. Všechna ostatní potvrzení, která mají být odeslána pro operace zpracované v tomto intervalu, jsou odeslána jako jedna odpověď. |
| ReplikátorEndpoint |Není dostupné. |Žádný výchozí -- povinný parametr |Adresa IP a port, který primární a sekundární replikátor použije ke komunikaci s ostatními replikátory v sadě replik. To by měl odkazovat na koncový bod prostředku TCP v manifestu služby. Odkazovat na [service manifest prostředky](service-fabric-service-manifest-resources.md) se přečtěte více o definování prostředků koncového bodu v manifestu služby. |
| Interval Retry |Sekundy |5 |Časové období, po kterém replikátor znovu odešle zprávu, pokud neobdrží potvrzení pro operaci. |
| MaxReplicationMessageSize |Bajty |50 MB |Maximální velikost dat replikace, která lze přenášet v jedné zprávě. |
| Velikost maxprimárníreplikační fronty |Počet operací |1024 |Maximální počet operací v primární frontě. Operace je uvolněna poté, co primární replikátor obdrží potvrzení od všech sekundárních replikátorů. Tato hodnota musí být větší než 64 a mocninu 2. |
| Velikost fronty maxsekundární replikace |Počet operací |2 048 |Maximální počet operací v sekundární frontě. Operace je uvolněna po provedení jeho stavu vysoce dostupné prostřednictvím trvalosti. Tato hodnota musí být větší než 64 a mocninu 2. |

## <a name="store-configuration"></a>Konfigurace úložiště
Konfigurace úložiště se používají ke konfiguraci místního úložiště, které se používá k zachování stavu, který je replikován.
Výchozí konfigurace je generována šablonou sady Visual Studio a měla by stačit. Tato část popisuje další konfigurace, které jsou k dispozici pro vyladění místního úložiště.

### <a name="section-name"></a>Název oddílu
&lt;ActorName&gt;ServiceLocalStoreConfig

### <a name="configuration-names"></a>Názvy konfigurací
| Name (Název) | Jednotka | Výchozí hodnota | Poznámky |
| --- | --- | --- | --- |
| MaxAsyncCommitDelayVmilisekundách |Milisekund |200 |Nastaví maximální interval dávkování pro trvalé potvrzení místního úložiště. |
| MaxVerStránky |Počet stran |16384 |Maximální počet stránek verze v databázi místního úložiště. Určuje maximální počet neuhrazených transakcí. |

## <a name="sample-configuration-file"></a>Ukázkový konfigurační soubor
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
Parametr BatchAcknowledgementInterval řídí latenci replikace. Hodnota "0" má za následek nejnižší možnou latenci za cenu propustnosti (protože musí být odesláno a zpracováno více potvrzení zpráv, z nichž každá obsahuje méně potvrzení).
Čím větší je hodnota batchacknowledgementinterval, tím vyšší je celková propustnost replikace za cenu vyšší latence provozu. To se přímo promítá do latence potvrzení transakcí.

