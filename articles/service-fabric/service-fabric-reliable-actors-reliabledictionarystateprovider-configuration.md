---
title: Změna nastavení ReliableDictionaryActorStateProvider v Azure mikroslužeb | Microsoft Docs
description: Další informace o konfiguraci Azure Service Fabric stavová aktéři typu ReliableDictionaryActorStateProvider.
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: timlt
editor: ''
ms.assetid: 79b48ffa-2474-4f1c-a857-3471f9590ded
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: 1c7e31f9da2b2f9fe7a3a7c64cd6927224cb9fa1
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37017891"
---
# <a name="configuring-reliable-actors--reliabledictionaryactorstateprovider"></a>Konfigurace Reliable Actors – ReliableDictionaryActorStateProvider
Výchozí konfigurace ReliableDictionaryActorStateProvider můžete upravit změnou souboru souborech settings.xml vygenerované zadaného objektu actor v kořenu balíčku sady Visual Studio ve složce Konfigurace.

Modul runtime Azure Service Fabric předdefinované části z názvů v souboru souborech settings.xml a odebírá hodnoty konfigurace při vytvoření základní komponenty modulu runtime.

> [!NOTE]
> Proveďte **není** odstranit nebo změnit názvy části následující konfigurace v souborech settings.xml soubor, který se generuje ve řešení sady Visual Studio.
> 
> 

Existují také globální nastavení, které ovlivňují konfiguraci ReliableDictionaryActorStateProvider.

## <a name="global-configuration"></a>Globální konfigurace
Globální konfiguraci je zadáno v manifestu clusteru pro cluster části KtlLogger. Umožňuje konfiguraci protokolu sdílené umístění a velikost a limity globální paměti používané protokolovacího nástroje. Všimněte si, že změny v manifestu clusteru vliv na všechny služby, které používají ReliableDictionaryActorStateProvider a spolehlivé stavové služby.

V manifestu clusteru je jednoho souboru XML, který obsahuje nastavení a konfigurace, které platí pro všechny uzly a služby v clusteru. Soubor se obvykle nazývá ClusterManifest.xml. Můžete zobrazit clusteru manifestu clusteru pomocí příkazu Get-ServiceFabricClusterManifest prostředí powershell.

### <a name="configuration-names"></a>Názvy konfigurace
| Název | Jednotka | Výchozí hodnota | Poznámky |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Kilobajtů |8388608 |Minimální počet KB přidělit v režimu jádra pro fond vyrovnávací paměti zápisu protokolovacího nástroje. Tento fond paměti se používá pro ukládání do mezipaměti informace o stavu před zápis na disk. |
| WriteBufferMemoryPoolMaximumInKB |Kilobajtů |Bez omezení |Maximální velikost, do které protokolovacího nástroje zápis fondu vyrovnávací paměti můžou růst. |
| SharedLogId |GUID |"" |Určuje jedinečný identifikátor GUID pro identifikaci výchozí sdíleného souboru protokolu v případě všech spolehlivé služeb ve všech uzlech v clusteru, které neurčují SharedLogId v jejich konkrétní konfiguraci služby. Pokud je zadán SharedLogId, musíte zadat také tento SharedLogPath. |
| SharedLogPath |Plně kvalifikovaný název cesty |"" |Určuje plně kvalifikovanou cestu, kde soubor sdílený protokolu používané všechny spolehlivé služby ve všech uzlech v clusteru, které neurčují SharedLogPath v jejich konkrétní konfiguraci služby. Ale pokud je zadán SharedLogPath, pak SharedLogId musí také uvést. |
| SharedLogSizeInMB |Megabajtů |8192 |Určuje počet MB místa na disku staticky přidělení pro sdílené protokolu. Hodnota musí být 2 048 nebo větší. |

### <a name="sample-cluster-manifest-section"></a>Ukázka oddílu manifestu clusteru
```xml
   <Section Name="KtlLogger">
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
     <Parameter Name="SharedLogSizeInMB" Value="16383"/>
   </Section>
```

### <a name="remarks"></a>Poznámky
Protokolovač má globálního fondu paměti přidělené z paměti nestránkovaného jádra, která je k dispozici pro všechny spolehlivé služby v uzlu pro ukládání do mezipaměti data o stavu, než se zapíšou do protokolu vyhrazené související s replikou spolehlivé služby. Velikost fondu je řízena nastavením WriteBufferMemoryPoolMinimumInKB a WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB Určuje počáteční velikost tohoto fondu paměti a velikost nejnižší, ke kterému může zmenšit se využívá fondem paměti. WriteBufferMemoryPoolMaximumInKB je nejvyšší velikost, která může dosáhnout se využívá fondem paměti. Každou repliku spolehlivá služba, který se otevírá může zvýšit velikost fondu paměti systému určit velikost až WriteBufferMemoryPoolMaximumInKB. Pokud existuje více požadavků na paměť z fondu paměti, než je k dispozici, požadavky na paměť se odloží až do paměti je k dispozici. Proto pokud fondu zápis vyrovnávací paměť je příliš malá pro konkrétní konfigurací pak výkonu může dojít ke snížení.

Nastavení SharedLogId a SharedLogPath se vždy používají společně zadat identifikátor GUID a umístění sdílené protokolu výchozí pro všechny uzly v clusteru. Protokol sdílené výchozí se používá pro všechny spolehlivé služby, které neurčují nastavení v souborech settings.xml pro konkrétní službu. Pro nejlepší výkon musí být sdílené soubory protokolu umístěny na disky, které se používají výhradně pro soubor sdílený protokolu ke snížení kolizí.

SharedLogSizeInMB určuje množství místa na disku pro předběžné přidělení pro protokol výchozí sdílené na všech uzlech.  SharedLogId a SharedLogPath není potřeba zadat v pořadí pro SharedLogSizeInMB zadat.

## <a name="replicator-security-configuration"></a>Konfigurace zabezpečení replikátoru
Konfigurace zabezpečení Replikátor slouží k zabezpečení komunikačního kanálu, který se používá během replikace. To znamená, že služby nelze vzájemně provoz replikace, zajištění, že data, která je vysoké dostupnosti je také zabezpečené.
Ve výchozím nastavení zabraňuje konfigurační oddíl prázdný zabezpečení zabezpečení replikace.

> [!IMPORTANT]
> Certifikáty na uzly Linux musí být ve formátu PEM. Další informace o vyhledání a konfigurace certifikátů pro Linux najdete v tématu [konfigurace certifikátů pro systémy Linux](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="section-name"></a>Název oddílu
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Replikátor konfigurace
Replikátor konfigurace se používají ke konfiguraci Replikátor, která je odpovědná za vytváření vysoce spolehlivé zprostředkovatele stavu objektu Actor stavu replikace a zachování stavu místně.
Výchozí konfigurace je generován šablony sady Visual Studio a měla by stačit. Tato část pojednává o další konfigurace, které jsou k dispozici pro optimalizaci replikátor.

### <a name="section-name"></a>Název oddílu
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Názvy konfigurace
| Název | Jednotka | Výchozí hodnota | Poznámky |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Sekundy |0.015 |Časové období, pro které Replikátor na sekundární počká po přijetí operace před odesláním zpět na primární potvrzení. Další potvrzení k odeslání pro operace zpracování v rámci tohoto intervalu se odesílají jako jedna odpověď. |
| ReplicatorEndpoint |neuvedeno |Žádná výchozí hodnota – povinný parametr |IP adresa a port, který Replikátor primární a sekundární bude používat pro komunikaci s další replikátory na replice nastavit. To by měl odkazovat koncový bod TCP prostředků v service manifest. Odkazovat na [manifestu prostředky služby](service-fabric-service-manifest-resources.md) Další informace o definování koncový bod prostředků v service manifest. |
| Maxreplicationmessagesize. |B |50 MB |Maximální velikost data replikace, která mohou být přenesena do jedné zprávy. |
| MaxPrimaryReplicationQueueSize |Počet operací |8192 |Maximální počet operací ve frontě primární. Operace uvolněno po primární Replikátor obdrží potvrzení ze sekundární replikátory. Tato hodnota musí být větší než 64 a druhou mocninou 2. |
| MaxSecondaryReplicationQueueSize |Počet operací |16384 |Maximální počet operací v sekundární fronty. Operace uvolněno po provedení vysoce dostupný prostřednictvím trvalost stavu. Tato hodnota musí být větší než 64 a druhou mocninou 2. |
| CheckpointThresholdInMB |MB |200 |Množství místa souboru protokolu, po jejímž uplynutí je kontrolován stav. |
| MaxRecordSizeInKB |kB |1024 |Maximální velikost záznam, který Replikátor může zapisovat do protokolu. Tato hodnota musí být násobkem 4 a je větší než 16. |
| OptimizeLogForLowerDiskUsage |Logická hodnota |true (pravda) |V případě hodnoty true je u protokolu nakonfigurována tak, aby repliky vyhrazené soubor protokolu se vytvoří pomocí systému souborů NTFS zhuštěného souboru. To snižuje využití místa skutečné disku pro soubor. Pokud má hodnotu NEPRAVDA, se vytvoří soubor s pevnou přidělení, které poskytují nejlepší zápisu výkonu. |
| SharedLogId |Identifikátor GUID |"" |Určuje jedinečný identifikátor guid pro identifikaci soubor sdílený protokolu použít s této repliky. Služby obvykle neměli používat toto nastavení. Ale pokud je zadán SharedLogId, pak SharedLogPath musí také uvést. |
| SharedLogPath |Plně kvalifikovaný název cesty |"" |Určuje plně kvalifikovanou cestu, kde bude vytvořen soubor sdílený protokolu pro tuto repliku. Služby obvykle neměli používat toto nastavení. Ale pokud je zadán SharedLogPath, pak SharedLogId musí také uvést. |

## <a name="sample-configuration-file"></a>Vzorový konfigurační soubor
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="180" />
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

Parametr CheckpointThresholdInMB řídí množství místa na disku, které Replikátor můžete použít k ukládání stavových informací v souboru protokolu vyhrazené repliky. To zvyšuje na vyšší hodnotu než výchozí může mít za následek Rekonfigurace rychlejší, když je přidat novou repliku do sady. Toto je z důvodu přenosu částečné stavu, která se provádí z důvodu dostupnost další historie operací v protokolu. Po chybě potenciálně může prodloužit dobu obnovení repliky.

Pokud OptimizeForLowerDiskUsage nastavíte na hodnotu true, bude místo souboru protokolu neoprávněně zřízené tak, aby aktivní repliky můžete ukládat další informace o stavu v souborech protokolu i neaktivních replik se bude používat méně místa na disku. Díky tomu je možné k hostování víc replik na uzlu. Pokud jste nastavili OptimizeForLowerDiskUsage na hodnotu false, informace o stavu je rychleji zapisovat do souborů protokolu.

Nastavení MaxRecordSizeInKB definuje maximální velikost záznam, který lze zapisovat, které Replikátor do souboru protokolu. Ve většině případů je optimální výchozí velikost 1024 KB záznamu. Ale pokud služba způsobuje větší datové položky se jednat o část informace o stavu, pak tato hodnota možná muset zvýšit. Výhoda je malá při vytvoření MaxRecordSizeInKB menší než 1024, jako menší záznamů použijte pouze prostor potřebný pro menší záznamu. Očekáváme, že tato hodnota by bylo potřeba změnit pouze ve výjimečných případech.

Nastavení SharedLogId a SharedLogPath jsou vždy společně použít k nastavení služby použití samostatných sdílených protokolu z protokolu sdílené výchozí pro uzel. Pro nejlepšího výkonu by měl tolik služby nejdříve zadejte stejné sdílené protokolu. Sdílené soubory protokolu musí být umístěny na disky, které se používají výhradně pro soubor sdílený protokolu ke snížení head přesun kolizí. Očekáváme, že tyto hodnoty by bylo potřeba změnit pouze ve výjimečných případech.

