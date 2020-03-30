---
title: Změnit nastavení ReliableDictionaryActorStateProvider
description: Další informace o konfiguraci Azure Service Fabric stavové objekty actor typu ReliableDictionaryActorStateProvider.
author: sumukhs
ms.topic: conceptual
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: fbd6f7cd3ade753c659464522408aa715cce48f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609736"
---
# <a name="configuring-reliable-actors--reliabledictionaryactorstateprovider"></a>Konfigurace spolehlivých herců–ReliableDictionaryActorStateProvider
Výchozí konfiguraci služby ReliableDictionaryActorStateProvider můžete změnit změnou souboru settings.xml generovaného v kořenovém adresáři balíčku sady Visual Studio ve složce Config pro zadaný objekt actor.

Prostředí Azure Service Fabric hledá předdefinované názvy oddílů v souboru settings.xml a spotřebovává hodnoty konfigurace při vytváření podkladových komponent runtime.

> [!NOTE]
> Neodstraňujte **ani neupravujte** názvy oddílů následujících konfigurací v souboru settings.xml, který je generován v řešení sady Visual Studio.
> 
> 

Existují také globální nastavení, které ovlivňují konfiguraci ReliableDictionaryActorStateProvider.

## <a name="global-configuration"></a>Globální konfigurace
Globální konfigurace je určena v manifestu clusteru pro cluster v části KtlLogger. Umožňuje konfiguraci sdílené umístění protokolu a velikost plus globální limity paměti používané protokolovacíprotokol. Všimněte si, že změny v manifestu clusteru ovlivnit všechny služby, které používají ReliableDictionaryActorStateProvider a spolehlivé stavové služby.

Manifest clusteru je jeden soubor XML, který obsahuje nastavení a konfigurace, které platí pro všechny uzly a služby v clusteru. Soubor se obvykle nazývá ClusterManifest.xml. Manifest clusteru pro váš cluster se zobrazí pomocí příkazu Get-ServiceFabricClusterManifest powershellu.

### <a name="configuration-names"></a>Názvy konfigurací
| Name (Název) | Jednotka | Výchozí hodnota | Poznámky |
| --- | --- | --- | --- |
| ZápisBufferMemoryPoolminimumInKB |Kilobajtech |8388608 |Minimální počet KB přidělit v režimu jádra pro zásobníku paměti paměti zápisu protokolů. Tento fond paměti se používá pro ukládání informací o stavu ukládání do mezipaměti před zápisem na disk. |
| WriteBufferMemoryPoolMaximumInKB |Kilobajtech |Bez omezení |Maximální velikost, na kterou může růst fond vyrovnávací paměti pro zápis do protokolování. |
| SharedLogId |GUID |"" |Určuje jedinečný identifikátor GUID, který se má použít k identifikaci výchozího sdíleného souboru protokolu používaného všemi spolehlivými službami ve všech uzlech v clusteru, které neurčují identifikátor SharedLogId v konfiguraci specifické pro službu. Pokud sharedLogId je zadán, pak SharedLogPath musí být také zadán. |
| SharedLogPath |Plně kvalifikovaný název cesty |"" |Určuje plně kvalifikovanou cestu, kde sdílený soubor protokolu používaný všemi spolehlivými službami ve všech uzlech v clusteru, které neurčují sdílenou položku The SharedLogPath v konfiguraci specifické pro službu. Pokud je však zadána aplikace SharedLogPath, musí být zadána také sharedlogid. |
| SharedLogSizeInMB |Megabajtů |8192 |Určuje počet MB místa na disku, které má být staticky přiděleno pro sdílený protokol. Hodnota musí být 2048 nebo větší. |

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
Protokolování má globální fond paměti přidělené z paměti bez stránkovaného jádra, který je k dispozici pro všechny spolehlivé služby na uzlu pro ukládání dat stavu do mezipaměti před zápisem do vyhrazeného protokolu přidruženého k replikě spolehlivé služby. Velikost fondu je řízena nastaveními WriteBufferMemoryPoolMaximumInKB a WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB určuje počáteční velikost tohoto fondu paměti a nejnižší velikost, do které může zmenšit fondu paměti. WriteBufferMemoryPoolMaximumInKB je nejvyšší velikost, do které může růst fondu paměti. Každá replika spolehlivé služby, která je otevřena může zvýšit velikost fondu paměti o systémem určené množství až WriteBufferMemoryPoolMaximumInKB. Pokud je větší poptávka po paměti z fondu paměti, než je k dispozici, požadavky na paměť bude zpožděna, dokud není k dispozici paměť. Proto pokud fond paměti vyrovnávací paměti zápisu je příliš malý pro konkrétní konfiguraci pak může dojít k unesení výkonu.

Nastavení SharedLogId a SharedLogPath se vždy používají společně k definování identifikátoru GUID a umístění výchozího sdíleného protokolu pro všechny uzly v clusteru. Výchozí sdílený protokol se používá pro všechny spolehlivé služby, které neurčují nastavení v souboru settings.xml pro konkrétní službu. Pro dosažení nejlepšího výkonu by měly být sdílené soubory protokolu umístěny na disky, které se používají výhradně pro sdílený soubor protokolu ke snížení kolizí.

SharedLogSizeInMB určuje velikost místa na disku, které má být předem přiděleno pro výchozí sdílený protokol ve všech uzlech.  SharedLogId a SharedLogPath není nutné zadat, aby bylo určeno SharedLogSizeInMB.

## <a name="replicator-security-configuration"></a>Konfigurace zabezpečení replikátoru
Konfigurace zabezpečení replikátoru se používají k zabezpečení komunikačního kanálu, který se používá během replikace. To znamená, že služby nemohou zobrazit navzájem replikační provoz, zajištění dat, která je k dispozici vysoce dostupné je také zabezpečené.
Ve výchozím nastavení brání prázdný oddíl konfigurace zabezpečení zabezpečení zabezpečení zabezpečení.

> [!IMPORTANT]
> V uzlech Linuxu musí být certifikáty ve formátu PEM. Další informace o vyhledání a konfiguraci certifikátů pro Linux najdete [v tématu Konfigurace certifikátů v Linuxu](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="section-name"></a>Název oddílu
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Konfigurace replikátoru
Replikátor konfigurace se používají ke konfiguraci replikátoru, který je zodpovědný za nastavení stavu actor státního zprostředkovatele vysoce spolehlivé replikaci a trvalé stavu místně.
Výchozí konfigurace je generována šablonou sady Visual Studio a měla by stačit. Tato část hovoří o další konfigurace, které jsou k dispozici pro ladění replikátoru.

### <a name="section-name"></a>Název oddílu
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Názvy konfigurací
| Name (Název) | Jednotka | Výchozí hodnota | Poznámky |
| --- | --- | --- | --- |
| Interval potvrzení dávky |Sekundy |0.015 |Časové období, pro které replikátor na sekundární čeká po přijetí operace před odesláním zpět potvrzení primární. Všechna ostatní potvrzení, která mají být odeslána pro operace zpracované v tomto intervalu, jsou odeslána jako jedna odpověď. |
| ReplikátorEndpoint |Není dostupné. |Žádný výchozí -- povinný parametr |Adresa IP a port, který primární a sekundární replikátor použije ke komunikaci s ostatními replikátory v sadě replik. To by měl odkazovat na koncový bod prostředku TCP v manifestu služby. Odkazovat na [service manifest prostředky](service-fabric-service-manifest-resources.md) se přečtěte více o definování prostředků koncového bodu v manifestu služby. |
| MaxReplicationMessageSize |Bajty |50 MB |Maximální velikost dat replikace, která lze přenášet v jedné zprávě. |
| Velikost maxprimárníreplikační fronty |Počet operací |8192 |Maximální počet operací v primární frontě. Operace je uvolněna poté, co primární replikátor obdrží potvrzení od všech sekundárních replikátorů. Tato hodnota musí být větší než 64 a mocninu 2. |
| Velikost fronty maxsekundární replikace |Počet operací |16384 |Maximální počet operací v sekundární frontě. Operace je uvolněna po provedení jeho stavu vysoce dostupné prostřednictvím trvalosti. Tato hodnota musí být větší než 64 a mocninu 2. |
| CheckpointThresholdInMB |MB |200 |Velikost místa v souboru protokolu, po kterém je stav kontrolním bodem. |
| MaxRecordSizeInKB |KB |1024 |Největší velikost záznamu, který replikátor může zapsat do protokolu. Tato hodnota musí být násobkem 4 a větší než 16. |
| OptimizeLogForLowerDiskUsage |Logická hodnota |true |Pokud je hodnota true, je protokol nakonfigurován tak, aby byl vyhrazený soubor protokolu repliky vytvořen pomocí řídkého souboru NTFS. Tím se sníží skutečné využití místa na disku pro soubor. Pokud false, soubor je vytvořen s pevnou přidělení, které poskytují nejlepší výkon zápisu. |
| SharedLogId |Identifikátor guid |"" |Určuje jedinečný identifikátor GUID, který se má použít k identifikaci sdíleného souboru protokolu použitého v této replice. Služby by obvykle neměly používat toto nastavení. Pokud je však zadáno SharedLogId, musí být zadána také aplikace SharedLogPath. |
| SharedLogPath |Plně kvalifikovaný název cesty |"" |Určuje plně kvalifikovanou cestu, kde bude vytvořen sdílený soubor protokolu pro tuto repliku. Služby by obvykle neměly používat toto nastavení. Pokud je však zadána aplikace SharedLogPath, musí být zadána také sharedlogid. |

## <a name="sample-configuration-file"></a>Ukázkový konfigurační soubor
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
Parametr BatchAcknowledgementInterval řídí latenci replikace. Hodnota "0" má za následek nejnižší možnou latenci za cenu propustnosti (protože musí být odesláno a zpracováno více potvrzení zpráv, z nichž každá obsahuje méně potvrzení).
Čím větší je hodnota batchacknowledgementinterval, tím vyšší je celková propustnost replikace za cenu vyšší latence provozu. To se přímo promítá do latence potvrzení transakcí.

Parametr CheckpointThresholdInMB určuje velikost místa na disku, které může replikátor použít k ukládání informací o stavu do vyhrazeného souboru protokolu repliky. Zvýšení této hodnoty na vyšší hodnotu než výchozí může mít za následek rychlejší rekonfigurace časy při přidání nové repliky do sady. To je způsobeno částečným přenosem stavu, ke kterému dochází z důvodu dostupnosti další historie operací v protokolu. To může potenciálně zvýšit dobu obnovení repliky po selhání.

Pokud nastavíte OptimizeForLowerDiskUsage na true, místo v souboru protokolu bude nadměrně zřízeno, takže aktivní repliky mohou ukládat více informací o stavu v souborech protokolu, zatímco neaktivní repliky budou používat méně místa na disku. To umožňuje hostovat více replik na uzlu. Pokud nastavíte OptimizeForLowerDiskUsage na false, informace o stavu se zapisuje do souborů protokolu rychleji.

Nastavení MaxRecordSizeInKB definuje maximální velikost záznamu, který může replikátor zapsat do souboru protokolu. Ve většině případů je optimální výchozí velikost záznamu 1024 KB. Pokud však služba způsobuje, že větší datové položky jsou součástí informací o stavu, může být nutné tuto hodnotu zvýšit. Je malý přínos v tom, že MaxRecordSizeInKB menší než 1024, protože menší záznamy používají pouze místo potřebné pro menší záznam. Očekáváme, že tato hodnota bude muset být změněna pouze ve výjimečných případech.

Nastavení SharedLogId a SharedLogPath se vždy používají společně k tomu, aby služba používala samostatný sdílený protokol z výchozího sdíleného protokolu pro uzel. Pro nejlepší efektivitu by mělo zadat stejný sdílený protokol co nejvíce služeb. Sdílené soubory protokolu by měly být umístěny na disky, které se používají výhradně pro sdílený soubor protokolu, ke snížení nároky pohybu hlavy. Očekáváme, že tyto hodnoty budou muset být změněny pouze ve vzácných případech.

