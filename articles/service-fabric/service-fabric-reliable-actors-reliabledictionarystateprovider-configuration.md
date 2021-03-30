---
title: Změnit nastavení ReliableDictionaryActorStateProvider
description: Přečtěte si o konfiguraci stavových aktérů Azure Service Fabric typu ReliableDictionaryActorStateProvider.
author: sumukhs
ms.topic: conceptual
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: fbd6f7cd3ade753c659464522408aa715cce48f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "75609736"
---
# <a name="configuring-reliable-actors--reliabledictionaryactorstateprovider"></a>Konfigurace Reliable Actors--ReliableDictionaryActorStateProvider
Můžete upravit výchozí konfiguraci ReliableDictionaryActorStateProvider změnou souboru settings.xml generovaného v kořenové složce balíčku sady Visual Studio v konfigurační složce pro zadaný objekt actor.

Modul runtime služby Azure Service Fabric hledá v souboru settings.xml předdefinované názvy oddílů a při vytváření podkladových komponent modulu runtime tyto hodnoty spotřebovává.

> [!NOTE]
> **Neodstraňujte** ani neměňte názvy oddílů následujících konfigurací v souboru settings.xml, který je generován v řešení sady Visual Studio.
> 
> 

Existují také globální nastavení, která mají vliv na konfiguraci ReliableDictionaryActorStateProvider.

## <a name="global-configuration"></a>Globální konfigurace
Globální konfigurace je určena v manifestu clusteru pro cluster v části KtlLogger. Umožňuje konfiguraci umístění a velikosti sdíleného protokolu a také omezení globální paměti používané protokolovacím nástrojem. Všimněte si, že změny v manifestu clusteru mají vliv na všechny služby, které používají ReliableDictionaryActorStateProvider a spolehlivé stavové služby.

Manifest clusteru je jeden soubor XML, který obsahuje nastavení a konfigurace, které platí pro všechny uzly a služby v clusteru. Tento soubor se obvykle označuje jako ClusterManifest.xml. Manifest clusteru pro cluster můžete zobrazit pomocí příkazu Get-ServiceFabricClusterManifest PowerShellu.

### <a name="configuration-names"></a>Názvy konfigurací
| Name | Jednotka | Výchozí hodnota | Poznámky |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Kilobajtů |8388608 |Minimální počet KB pro přidělení v režimu jádra pro fond paměti vyrovnávací paměti zápisu pro protokolovací nástroj. Tento fond paměti se používá k ukládání informací o stavu do mezipaměti před zápisem na disk. |
| WriteBufferMemoryPoolMaximumInKB |Kilobajtů |Bez omezení |Maximální velikost, do které může růst fondu paměti zápisu pro zápis protokolovacího nástroje. |
| SharedLogId |Identifikátor GUID |"" |Určuje jedinečný identifikátor GUID, který se použije pro identifikaci výchozího sdíleného souboru protokolu používaného všemi spolehlivými službami na všech uzlech v clusteru, které neurčují SharedLogId v konfiguraci specifické pro danou službu. Je-li zadán parametr SharedLogId, musí být také zadán parametr SharedLogPath. |
| SharedLogPath |Plně kvalifikovaný název cesty |"" |Určuje plně kvalifikovanou cestu, kde se sdílený soubor protokolu používaný všemi spolehlivými službami na všech uzlech v clusteru nespecifikuje SharedLogPath v konfiguraci specifické pro danou službu. Pokud je však zadán parametr SharedLogPath, musí být také zadán parametr SharedLogId. |
| SharedLogSizeInMB |Megabajty |8192 |Určuje počet MB místa na disku, které se staticky přidělí pro sdílený protokol. Hodnota musí být 2048 nebo větší. |

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
Protokolovací nástroj má Globální fond paměti přidělený z nestránkované paměti jádra, který je dostupný pro všechny spolehlivé služby na uzlu pro ukládání dat stavu do mezipaměti, než se zapíše do vyhrazeného protokolu přidruženého k spolehlivé replice služby. Velikost fondu je řízena nastavením WriteBufferMemoryPoolMinimumInKB a WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB Určuje počáteční velikost tohoto fondu paměti a nejnižší velikost, se kterou se může fond paměti zmenšit. WriteBufferMemoryPoolMaximumInKB je nejvyšší velikost, na kterou může fond paměti růst. Každá otevřená replika spolehlivé služby může zvětšit velikost fondu paměti podle zjištěného množství systému až do WriteBufferMemoryPoolMaximumInKB. Pokud je paměť z fondu paměti větší, než je k dispozici, požadavky na paměť budou zpožděny, dokud nebude k dispozici paměť. Pokud je fond paměti pro vyrovnávací paměť pro zápis příliš malý pro určitou konfiguraci, může dojít ke zhoršení výkonu.

Nastavení SharedLogId a SharedLogPath se vždycky používají společně k definování identifikátoru GUID a umístění pro výchozí sdílený protokol pro všechny uzly v clusteru. Výchozí sdílený protokol se používá pro všechny spolehlivé služby, které neurčují nastavení v settings.xml pro konkrétní službu. Pro dosažení co nejlepších výsledků by se měly soubory sdíleného protokolu umístit na disky, které se používají výhradně pro sdílený soubor protokolu pro snížení kolizí.

SharedLogSizeInMB určuje velikost místa na disku, které se má předem přidělit pro výchozí sdílený protokol na všech uzlech.  SharedLogId a SharedLogPath není nutné zadávat, aby bylo možné zadat SharedLogSizeInMB.

## <a name="replicator-security-configuration"></a>Konfigurace zabezpečení replikátoru
Konfigurace zabezpečení replikátoru slouží k zabezpečení komunikačního kanálu, který se používá při replikaci. To znamená, že služby nevidí provoz replikace mezi ostatními, takže data, která jsou vytvořená jako vysoce dostupná, jsou také zabezpečená.
Ve výchozím nastavení se v prázdném oddílu konfigurace zabezpečení zabrání zabezpečení replikace.

> [!IMPORTANT]
> V uzlech se systémem Linux musí být certifikáty PEM ve formátu. Další informace o vyhledání a konfiguraci certifikátů pro Linux najdete v tématu [Konfigurace certifikátů v systému Linux](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="section-name"></a>Název oddílu
&lt;Jméno objektu actor &gt; ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Konfigurace replikátoru
Konfigurace replikátoru slouží ke konfiguraci replikátoru, který zodpovídá za to, aby byl stav poskytovatele stavu objektu actor vysoce spolehlivý díky replikaci a trvalému zachování stavu.
Výchozí konfigurace je generována šablonou sady Visual Studio a měla by stačit. Tato část pojednává o dalších konfiguracích, které jsou k dispozici pro optimalizaci replikátoru.

### <a name="section-name"></a>Název oddílu
&lt;Jméno objektu actor &gt; ServiceReplicatorConfig

### <a name="configuration-names"></a>Názvy konfigurací
| Name | Jednotka | Výchozí hodnota | Poznámky |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Sekundy |0,015 |Časové období, po které se Replikátor v sekundárním čekání po přijetí operace před odesláním zpět na primární. Jakékoli další potvrzení, která se mají odeslat pro operace zpracovávané v tomto intervalu, se odešlou jako jedna odpověď. |
| ReplicatorEndpoint |– |Žádný výchozí – parametr není povinný. |IP adresa a port, které bude primární a sekundární Replikátor používat ke komunikaci s ostatními replikačními replikami v sadě replik. To by mělo odkazovat na koncový bod prostředku TCP v manifestu služby. Další informace o definování prostředků koncového bodu v manifestu služby najdete v článku [prostředky manifestu služby](service-fabric-service-manifest-resources.md) . |
| MaxReplicationMessageSize |Bajty |50 MB |Maximální velikost replikačních dat, která se dají přenést v jedné zprávě |
| MaxPrimaryReplicationQueueSize |Počet operací |8192 |Maximální počet operací v primární frontě. Když primární Replikátor dostane potvrzení ze všech sekundárních replikátorů, operace se uvolní. Tato hodnota musí být větší než 64 a mocnina 2. |
| MaxSecondaryReplicationQueueSize |Počet operací |16384 |Maximální počet operací v sekundární frontě. Po zajištění vysoké dostupnosti stavu prostřednictvím trvalosti se operace uvolní. Tato hodnota musí být větší než 64 a mocnina 2. |
| CheckpointThresholdInMB |MB |200 |Velikost místa pro soubor protokolu, po kterém je stav nastaven na kontrolní bod. |
| MaxRecordSizeInKB |KB |1024 |Největší velikost záznamu, kterou může Replikátor zapsat do protokolu. Tato hodnota musí být násobkem 4 a větší než 16. |
| OptimizeLogForLowerDiskUsage |Logická hodnota |true |V případě hodnoty true je protokol nakonfigurován tak, aby byl soubor protokolu vyhrazené repliky vytvořen pomocí zhuštěného souboru systému souborů NTFS. Tím se sníží skutečné využití místa na disku pro daný soubor. Pokud je hodnota false, vytvoří se soubor s pevnými alokacemi, které poskytují nejlepší výkon zápisu. |
| SharedLogId |guid |"" |Určuje jedinečný identifikátor GUID, který se použije pro identifikaci sdíleného souboru protokolu používaného v této replice. Služby by obvykle neměly používat toto nastavení. Pokud je však zadán parametr SharedLogId, musí být také zadán parametr SharedLogPath. |
| SharedLogPath |Plně kvalifikovaný název cesty |"" |Určuje plně kvalifikovanou cestu, kam se vytvoří sdílený soubor protokolu pro tuto repliku. Služby by obvykle neměly používat toto nastavení. Pokud je však zadán parametr SharedLogPath, musí být také zadán parametr SharedLogId. |

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
Parametr BatchAcknowledgementInterval řídí latenci replikace. Hodnota 0 má za následek nejnižší možnou latenci. náklady na propustnost (v případě, že je nutné odesílat a zpracovávat další zprávy o potvrzení, obsahují méně potvrzení).
Čím větší je hodnota pro BatchAcknowledgementInterval, tím vyšší je celková propustnost replikace za cenu vyšší latence operace. To přímo překládá na latenci potvrzení transakcí.

Parametr CheckpointThresholdInMB řídí velikost místa na disku, které může Replikátor použít k ukládání informací o stavu do vyhrazeného souboru protokolu repliky. Zvýšením hodnoty na vyšší hodnotu, než je výchozí, může způsobit rychlejší opakovanou konfiguraci při přidání nové repliky do sady. Důvodem je přenos s částečným stavem, který probíhá v důsledku dostupnosti další historie operací v protokolu. To může potenciálně prodloužit dobu obnovení repliky po havárii.

Pokud nastavíte OptimizeForLowerDiskUsage na hodnotu true, místo pro soubory protokolu bude zajištěno, aby aktivní repliky ukládaly do svých souborů protokolů více informací o stavu, zatímco neaktivní repliky budou používat méně místa na disku. Díky tomu je možné hostovat více replik na uzlu. Pokud nastavíte OptimizeForLowerDiskUsage na false, informace o stavu se zapisují do souborů protokolu rychleji.

Nastavení MaxRecordSizeInKB definuje maximální velikost záznamu, který může být zapsán replikátorem do souboru protokolu. Ve většině případů je výchozí velikost záznamu 1024-KB optimální. Pokud však služba způsobuje, že jsou větší datové položky součástí informací o stavu, může být nutné zvýšit tuto hodnotu. Je málo výhod při vytváření MaxRecordSizeInKB menších než 1024, protože menší záznamy používají pouze prostor, který je potřeba pro menší záznam. Očekáváme, že by se tato hodnota musela změnit jenom ve výjimečných případech.

Nastavení SharedLogId a SharedLogPath se vždycky používají společně k vytvoření služby, která používá samostatný sdílený protokol z výchozího sdíleného protokolu pro uzel. Pro dosažení co nejlepších výsledků je potřeba, aby bylo možné zadat stejný sdílený protokol, kolik služeb je možné. Sdílené soubory protokolu by měly být umístěné na discích, které se používají výhradně pro sdílený soubor protokolu, aby se snížila platnost přesunutí hlav. Očekáváme, že tyto hodnoty by se měly změnit jenom ve výjimečných případech.

