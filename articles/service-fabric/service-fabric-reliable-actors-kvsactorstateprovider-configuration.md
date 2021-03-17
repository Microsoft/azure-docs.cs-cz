---
title: Změnit nastavení KVSActorStateProvider
description: Přečtěte si o konfiguraci stavových aktérů Azure Service Fabric typu KVSActorStateProvider.
author: sumukhs
ms.topic: conceptual
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: cdb115bd57cf3d5af4388f4efa03c2522feef9ca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75609770"
---
# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>Konfigurace Reliable Actors--KVSActorStateProvider
Výchozí konfiguraci KVSActorStateProvider můžete upravit tak, že změníte soubor settings.xml, který je vygenerovaný v kořenovém adresáři balíčku Microsoft Visual Studio v konfigurační složce pro zadaný objekt actor.

Modul runtime služby Azure Service Fabric hledá v souboru settings.xml předdefinované názvy oddílů a při vytváření podkladových komponent modulu runtime tyto hodnoty spotřebovává.

> [!NOTE]
> **Neodstraňujte** ani neměňte názvy oddílů následujících konfigurací v souboru settings.xml, který je generován v řešení sady Visual Studio.
> 
> 

## <a name="replicator-security-configuration"></a>Konfigurace zabezpečení replikátoru
Konfigurace zabezpečení replikátoru slouží k zabezpečení komunikačního kanálu, který se používá při replikaci. To znamená, že služby nevidí provoz replikace mezi ostatními a zajišťují, že data, která jsou vytvořená jako vysoce dostupná, jsou také zabezpečená.
Ve výchozím nastavení se v prázdném oddílu konfigurace zabezpečení zabrání zabezpečení replikace.

> [!IMPORTANT]
> V uzlech se systémem Linux musí být certifikáty PEM ve formátu. Další informace o vyhledání a konfiguraci certifikátů pro Linux najdete v tématu [Konfigurace certifikátů v systému Linux](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="section-name"></a>Název oddílu
&lt;Jméno objektu actor &gt; ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Konfigurace replikátoru
Konfigurace replikátoru konfigurují Replikátor, který zodpovídá za vysoce spolehlivý stav poskytovatele stavu objektu actor.
Výchozí konfigurace je generována šablonou sady Visual Studio a měla by stačit. Tato část pojednává o dalších konfiguracích, které jsou k dispozici pro optimalizaci replikátoru.

### <a name="section-name"></a>Název oddílu
&lt;Jméno objektu actor &gt; ServiceReplicatorConfig

### <a name="configuration-names"></a>Názvy konfigurací
| Název | Jednotka | Výchozí hodnota | Poznámky |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Sekundy |0,015 |Časové období, po které se Replikátor v sekundárním čekání po přijetí operace před odesláním zpět na primární. Jakékoli další potvrzení, která se mají odeslat pro operace zpracovávané v tomto intervalu, se odešlou jako jedna odpověď. |
| ReplicatorEndpoint |Není k dispozici |Žádný výchozí – parametr není povinný. |IP adresa a port, které bude primární a sekundární Replikátor používat ke komunikaci s ostatními replikačními replikami v sadě replik. To by mělo odkazovat na koncový bod prostředku TCP v manifestu služby. Další informace o definování prostředků koncového bodu v manifestu služby najdete v článku [prostředky manifestu služby](service-fabric-service-manifest-resources.md) . |
| RetryInterval |Sekundy |5 |Časové období, po kterém Replikátor znovu přenáší zprávu, pokud neobdrží potvrzení operace. |
| MaxReplicationMessageSize |Bajty |50 MB |Maximální velikost replikačních dat, která se dají přenést v jedné zprávě |
| MaxPrimaryReplicationQueueSize |Počet operací |1024 |Maximální počet operací v primární frontě. Když primární Replikátor dostane potvrzení ze všech sekundárních replikátorů, operace se uvolní. Tato hodnota musí být větší než 64 a mocnina 2. |
| MaxSecondaryReplicationQueueSize |Počet operací |2 048 |Maximální počet operací v sekundární frontě. Po zajištění vysoké dostupnosti stavu prostřednictvím trvalosti se operace uvolní. Tato hodnota musí být větší než 64 a mocnina 2. |

## <a name="store-configuration"></a>Konfigurace úložiště
Konfigurace úložiště se používají ke konfiguraci místního úložiště, které se používá k zachování replikovaného stavu.
Výchozí konfigurace je generována šablonou sady Visual Studio a měla by stačit. Tato část pojednává o dalších konfiguracích, které jsou k dispozici pro optimalizaci místního úložiště.

### <a name="section-name"></a>Název oddílu
&lt;Jméno objektu actor &gt; ServiceLocalStoreConfig

### <a name="configuration-names"></a>Názvy konfigurací
| Název | Jednotka | Výchozí hodnota | Poznámky |
| --- | --- | --- | --- |
| MaxAsyncCommitDelayInMilliseconds |Milisekund |200 |Nastaví maximální interval dávkování pro potvrzení trvalého místního úložiště. |
| MaxVerPages |Počet stránek |16384 |Maximální počet stránek verze v místní databázi úložiště Určuje maximální počet nezpracovaných transakcí. |

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
Parametr BatchAcknowledgementInterval řídí latenci replikace. Hodnota 0 má za následek nejnižší možnou latenci. náklady na propustnost (v případě, že je nutné odesílat a zpracovávat další zprávy o potvrzení, obsahují méně potvrzení).
Čím větší je hodnota pro BatchAcknowledgementInterval, tím vyšší je celková propustnost replikace za cenu vyšší latence operace. To přímo překládá na latenci potvrzení transakcí.

