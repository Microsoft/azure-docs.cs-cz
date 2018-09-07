---
title: Změnit nastavení ReliableDictionaryActorStateProvider v Azure Service Fabric actors | Dokumentace Microsoftu
description: Přečtěte si o Azure Service Fabric actors stavové typu ReliableDictionaryActorStateProvider konfigurace.
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
ms.openlocfilehash: 13cf9c148e36201bd8ee81d4072d8b8cd8660bd1
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44053144"
---
# <a name="configuring-reliable-actors--reliabledictionaryactorstateprovider"></a>Konfigurace Reliable Actors – ReliableDictionaryActorStateProvider
Výchozí konfigurace pro ReliableDictionaryActorStateProvider můžete upravit změnou souboru settings.xml vygenerované v kořenovém adresáři balíčku sady Visual Studio ve složce Konfigurace pro zadaný objekt actor.

Modul runtime Azure Service Fabric vyhledá názvy předdefinovaných oddílů v souboru settings.xml a využívá hodnoty konfigurace při vytvoření základní komponenty prostředí runtime.

> [!NOTE]
> Proveďte **není** odstranit ani změnit názvy oddílů následující konfigurace v souboru settings.xml, který je vygenerován v řešení sady Visual Studio.
> 
> 

Existují také globální nastavení, která mají vliv na konfiguraci ReliableDictionaryActorStateProvider.

## <a name="global-configuration"></a>Globální konfigurace
Globální konfigurace je určená v manifestu clusteru pro cluster v části KtlLogger. To umožňuje konfiguraci protokolu sdílené umístění a velikost a omezení globální paměti používané protokolovacího nástroje. Všimněte si, že změny v manifestu clusteru mít vliv na všechny služby, které používají ReliableDictionaryActorStateProvider a reliable stateful services.

V manifestu clusteru je jeden soubor XML obsahující nastavení a konfigurace, které platí pro všechny uzly a služby v clusteru. Soubor se obvykle nazývá ClusterManifest.xml. Zobrazí se clusteru pro cluster pomocí příkazu powershellu Get-ServiceFabricClusterManifest manifestu.

### <a name="configuration-names"></a>Názvy konfigurace
| Název | Jednotka | Výchozí hodnota | Poznámky |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Kilobajtů |8388608 |Minimální počet Kilobajtů přidělení v režimu jádra pro fond vyrovnávací paměti zápisu protokolovacího nástroje. Tento fond paměti se používá pro ukládání do mezipaměti informace o stavu před zápisu na disk. |
| WriteBufferMemoryPoolMaximumInKB |Kilobajtů |Bez omezení |Maximální velikost, ke kterému protokolovač zápis vyrovnávací paměti fondu paměti můžou růst. |
| SharedLogId |GUID |"" |Určuje jedinečný identifikátor GUID pro účely identifikace výchozí sdíleného souboru protokolu používá všechny spolehlivé služby na všech uzlech v clusteru, u kterých není SharedLogId v jejich konkrétní konfiguraci služby. Pokud SharedLogId není zadána, musíte zadat také tento SharedLogPath. |
| SharedLogPath |Plně kvalifikovaný název cesty |"" |Určuje plně kvalifikovanou cestu použití sdíleného protokol všech spolehlivých služeb ve všech uzlech v clusteru, u kterých není SharedLogPath v jejich konkrétní konfiguraci služby. Nicméně pokud SharedLogPath je, pak SharedLogId musí také uvést. |
| SharedLogSizeInMB |Megabajtů |8192 |Určuje počet MB místa na disku se staticky přidělit pro sdílené protokolu. Hodnota musí být 2 048 nebo větší. |

### <a name="sample-cluster-manifest-section"></a>Ukázkový oddíl manifestu clusteru
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
Protokolovač se globální fond paměti přidělené z paměti nestránkovaná jádra, který je k dispozici pro všechny spolehlivé služby v uzlu pro ukládání do mezipaměti dat o stavu před zápisem do protokolu vyhrazené přidružené repliky spolehlivé služby. Velikost fondu je řízena nastavením WriteBufferMemoryPoolMinimumInKB a WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB Určuje počáteční velikost tohoto fondu paměti a představuje nejmenší velikost, ke kterému může zmenšit fondu paměti. WriteBufferMemoryPoolMaximumInKB je nejvyšší velikost, ke kterému může dosáhnout fondu paměti. Každou repliku spolehlivé služby, která je otevřena může systému určit množství až WriteBufferMemoryPoolMaximumInKB zvětšit velikost fondu paměti. Pokud existuje více poptávka po paměti z fondu paměti, než je k dispozici, požadavky na paměť bude zpozdit. dokud nebude k dispozici paměť. Proto pokud fond zápisu vyrovnávací paměti je příliš malá pro konkrétní konfiguraci pak výkonu může dojít ke snížení.

Nastavení SharedLogId a SharedLogPath se vždy společně používají k definování GUID a umístění pro výchozí sdílené protokolu pro všechny uzly v clusteru. Výchozí sdílené protokol se používá pro všechny služby reliable services, u kterých není nastavení v settings.xml pro konkrétní službu. Pro zajištění nejlepšího výkonu by měl umístit sdílené soubory protokolu na discích, které se používají výhradně pro soubor protokolu sdílené za účelem omezení kolizí.

SharedLogSizeInMB určuje množství místa na disku pro předběžné přidělení pro protokol výchozí sdílené ve všech uzlech.  SharedLogId a SharedLogPath není nutné zadat v pořadí pro SharedLogSizeInMB zadání.

## <a name="replicator-security-configuration"></a>Konfigurace zabezpečení replikátoru
Konfigurace zabezpečení replikátoru slouží k zabezpečení komunikačního kanálu, který se používá během replikace. To znamená, že služby neuvidí druhé strany replikace provoz, zajištění, že data, která je vytvořena s vysokou dostupností je také zabezpečené.
Ve výchozím nastavení zabraňuje oddíl konfigurace prázdný zabezpečení zabezpečení replikace.

> [!IMPORTANT]
> Certifikáty na uzly s Linuxem, musí být ve formátu PEM. Další informace o vyhledání a konfigurace certifikátů pro Linux najdete v tématu [konfigurace certifikátů v Linuxu](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="section-name"></a>Název oddílu
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Konfigurace replikace
Konfigurace replikace se používají ke konfiguraci replikace, který je zodpovědný za vytváření vysoce spolehlivých stavu zprostředkovatele stavu objektu Actor replikaci a zachování stavu místně.
Výchozí konfigurace vygeneruje šablony sady Visual Studio a měl postačit. Tato část se hovoří o dalším konfiguracím, které jsou k dispozici pro vyladění replikátoru.

### <a name="section-name"></a>Název oddílu
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Názvy konfigurace
| Název | Jednotka | Výchozí hodnota | Poznámky |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Sekundy |0.015 |Časové období, pro které Replikátor na sekundární čeká po přijetí operace před odesláním zpět na primární potvrzení. Další potvrzení k odeslání pro operace zpracování v rámci tohoto intervalu se odesílají jako jednu odpověď. |
| ReplicatorEndpoint |neuvedeno |Žádná výchozí hodnota--povinný parametr |Nastavení IP adresy a portu, které Replikátor primárního a sekundárního se používají ke komunikaci s další replikátorů v replice. To by měla odkazovat na koncový bod TCP prostředků v manifestu služby. Odkazovat na [prostředky manifestu služby](service-fabric-service-manifest-resources.md) Další informace o definování koncový bod prostředků v manifestu služby. |
| MaxReplicationMessageSize |B |50 MB |Maximální velikost dat replikace, která mohou být přenesena do jedné zprávy. |
| MaxPrimaryReplicationQueueSize |Počet operací |8192 |Maximální počet operací ve frontě primární. Operace je uvolněna až primární Replikátor obdrží od všech sekundárních replikátorů potvrzení. Tato hodnota musí být větší než 64 a mocninou čísla 2. |
| MaxSecondaryReplicationQueueSize |Počet operací |16384 |Maximální počet operací ve frontě sekundární. Operace je uvolněna až po zajištění vysoké dostupnosti prostřednictvím trvalého stavu. Tato hodnota musí být větší než 64 a mocninou čísla 2. |
| CheckpointThresholdInMB |MB |200 |Množství místa souboru protokolu, po jejímž uplynutí je stav byl vytvořen kontrolní bod. |
| MaxRecordSizeInKB |kB |1024 |Největší velikost záznamu, který Replikátor může zapisovat do protokolu. Tato hodnota musí být násobkem 4 a je větší než 16. |
| OptimizeLogForLowerDiskUsage |Logická hodnota |true (pravda) |V případě hodnoty true je u protokolu nakonfigurována tak, aby repliky vyhrazené soubor protokolu se vytvoří pomocí chování řídkého souboru systému souborů NTFS. To snižuje využití místa na disku skutečného souboru. V případě hodnoty false se vytvoří soubor s pevnou přidělení, které poskytují nejlepší zápisu výkonu. |
| SharedLogId |identifikátor GUID |"" |Určuje jedinečný identifikátor guid pro účely identifikace sdílené protokol využít tuto repliku. Služby by neměl obvykle, pomocí tohoto nastavení. Nicméně pokud SharedLogId je, pak SharedLogPath musí také uvést. |
| SharedLogPath |Plně kvalifikovaný název cesty |"" |Určuje plně kvalifikovanou cestu, kde se vytvoří soubor sdílený protokolu pro tuto repliku. Služby by neměl obvykle, pomocí tohoto nastavení. Nicméně pokud SharedLogPath je, pak SharedLogId musí také uvést. |

## <a name="sample-configuration-file"></a>Ukázkový konfigurační soubor
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
Latence replikace BatchAcknowledgementInterval parametr ovládací prvky. Hodnota '0' za následek nejnižší možnou latenci, a to za cenu propustnost (jako další zprávy potvrzení musí být odeslána a zpracována, každá obsahuje méně potvrzení).
Čím větší hodnotu BatchAcknowledgementInterval, tím vyšší celkovou replikaci propustnost, za cenu vyšší latence operace. Výsledkem je přímo na latenci potvrzení transakcí.

Parametr CheckpointThresholdInMB řídí množství místa na disku, které Replikátor můžete použít k ukládání informací o stavu v souboru protokolu vyhrazené repliky. To zvyšuje na vyšší hodnotu než výchozí by mohlo způsobit změna konfigurace rychlejší při přidání novou repliku do sady. To je způsobeno přenos částečné stavu, které u něho kvůli dostupnosti další historie operace v protokolu. To potenciálně může prodloužit dobu obnovení replik po zhroucení.

Pokud nastavíte OptimizeForLowerDiskUsage na hodnotu true, bude místo v souboru protokolu nadměrně zřízených tak, aby aktivní repliky můžete ukládat další informace o stavu v souborech protokolu, zatímco neaktivní repliky bude používat méně místa na disku. Díky tomu je možné k hostování víc replik na uzlu. Pokud nastavíte OptimizeForLowerDiskUsage na hodnotu false, informace o stavu je rychleji zapisovat do souborů protokolu.

Nastavení MaxRecordSizeInKB definuje maximální velikost záznamu, který můžou Replikátor zapisovat do souboru protokolu. Ve většině případů je optimální výchozí velikost záznamu 1 024 KB. Ale pokud služba způsobuje větších datových položek jako součást informací o stavu, pak tato hodnota možná bude nutné navýšit. Výhoda je malá při vytvoření MaxRecordSizeInKB menší než 1024, menší záznamy k používání pouze prostor potřebný pro menší záznamu. Očekáváme, že tato hodnota by bylo potřeba změnit pouze ve výjimečných případech.

Nastavení SharedLogId a SharedLogPath se vždy používají společně službu použití samostatných sdílených protokolu z výchozí sdílené protokolu pro uzel. Pro nejlepší účinnosti by měl tolik služby nejvíce zadejte stejné sdílené protokolu. Sdílené soubory protokolu musí být umístěny na disky, které se používají výhradně pro sdílené protokol za účelem omezení kolizí hlavní pohyb. Očekáváme, že tyto hodnoty by bylo potřeba změnit pouze ve výjimečných případech.

