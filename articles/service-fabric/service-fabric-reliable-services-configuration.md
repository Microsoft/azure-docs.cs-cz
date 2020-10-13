---
title: Konfigurace služby Azure Service Fabric Reliable Services
description: Přečtěte si o konfiguraci stavového Reliable Services v aplikaci Azure Service Fabric globálně a pro jednu službu.
author: sumukhs
ms.topic: conceptual
ms.date: 10/02/2017
ms.author: sumukhs
ms.custom: devx-track-csharp
ms.openlocfilehash: cda0a9f988afae58a60bff051885a5eec8afe434
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89021965"
---
# <a name="configure-stateful-reliable-services"></a>Konfigurovat stavové služby Reliable Services
Pro spolehlivé služby jsou k dispozici dvě sady nastavení konfigurace. Jedna sada je globální pro všechny spolehlivé služby v clusteru, zatímco druhá sada je specifická pro konkrétní spolehlivé služby.

## <a name="global-configuration"></a>Globální konfigurace
Globální konfigurace spolehlivé služby je určena v manifestu clusteru pro cluster v části KtlLogger. Umožňuje konfiguraci umístění a velikosti sdíleného protokolu a také omezení globální paměti používané protokolovacím nástrojem. Manifest clusteru je jeden soubor XML, který obsahuje nastavení a konfigurace, které platí pro všechny uzly a služby v clusteru. Tento soubor se obvykle označuje jako ClusterManifest.xml. Manifest clusteru pro cluster můžete zobrazit pomocí příkazu Get-ServiceFabricClusterManifest PowerShellu.

### <a name="configuration-names"></a>Názvy konfigurací
| Name | Jednotka | Výchozí hodnota | Poznámky |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Kilobajtů |8388608 |Minimální počet KB pro přidělení v režimu jádra pro fond paměti vyrovnávací paměti zápisu pro protokolovací nástroj. Tento fond paměti se používá k ukládání informací o stavu do mezipaměti před zápisem na disk. |
| WriteBufferMemoryPoolMaximumInKB |Kilobajtů |Bez omezení |Maximální velikost, do které může růst fondu paměti zápisu pro zápis protokolovacího nástroje. |
| SharedLogId |Identifikátor GUID |"" |Určuje jedinečný identifikátor GUID, který se použije pro identifikaci výchozího sdíleného souboru protokolu používaného všemi spolehlivými službami na všech uzlech v clusteru, které neurčují SharedLogId v konfiguraci specifické pro danou službu. Je-li zadán parametr SharedLogId, musí být také zadán parametr SharedLogPath. |
| SharedLogPath |Plně kvalifikovaný název cesty |"" |Určuje plně kvalifikovanou cestu, kde se sdílený soubor protokolu používaný všemi spolehlivými službami na všech uzlech v clusteru nespecifikuje SharedLogPath v konfiguraci specifické pro danou službu. Pokud je však zadán parametr SharedLogPath, musí být také zadán parametr SharedLogId. |
| SharedLogSizeInMB |Megabajty |8192 |Určuje počet MB místa na disku, které se staticky přidělí pro sdílený protokol. Hodnota musí být 2048 nebo větší. |

V následujícím příkladu v Azure ARM nebo v místní šabloně JSON se dozvíte, jak změnit protokol sdílené transakce, který se vytvoří pro všechny spolehlivé kolekce pro stavové služby.

```json
"fabricSettings": [{
    "name": "KtlLogger",
    "parameters": [{
        "name": "SharedLogSizeInMB",
        "value": "4096"
    }]
}]
```

### <a name="sample-local-developer-cluster-manifest-section"></a>Ukázkový oddíl manifestu místního vývojářského clusteru
Pokud ho chcete změnit ve svém místním vývojovém prostředí, je potřeba upravit místní soubor clustermanifest.xml.

```xml
   <Section Name="KtlLogger">
     <Parameter Name="SharedLogSizeInMB" Value="4096"/>
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
   </Section>
```

### <a name="remarks"></a>Poznámky
Protokolovací nástroj má Globální fond paměti přidělený z nestránkované paměti jádra, který je dostupný pro všechny spolehlivé služby na uzlu pro ukládání dat stavu do mezipaměti, než se zapíše do vyhrazeného protokolu přidruženého k spolehlivé replice služby. Velikost fondu je řízena nastavením WriteBufferMemoryPoolMinimumInKB a WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB Určuje počáteční velikost tohoto fondu paměti a nejnižší velikost, se kterou se může fond paměti zmenšit. WriteBufferMemoryPoolMaximumInKB je nejvyšší velikost, na kterou může fond paměti růst. Každá otevřená replika spolehlivé služby může zvětšit velikost fondu paměti podle zjištěného množství systému až do WriteBufferMemoryPoolMaximumInKB. Pokud je paměť z fondu paměti větší, než je k dispozici, požadavky na paměť budou zpožděny, dokud nebude k dispozici paměť. Pokud je fond paměti pro vyrovnávací paměť pro zápis příliš malý pro určitou konfiguraci, může dojít ke zhoršení výkonu.

Nastavení SharedLogId a SharedLogPath se vždycky používají společně k definování identifikátoru GUID a umístění pro výchozí sdílený protokol pro všechny uzly v clusteru. Výchozí sdílený protokol se používá pro všechny spolehlivé služby, které neurčují nastavení v settings.xml pro konkrétní službu. Pro dosažení co nejlepších výsledků by se měly soubory sdíleného protokolu umístit na disky, které se používají výhradně pro sdílený soubor protokolu pro snížení kolizí.

SharedLogSizeInMB určuje velikost místa na disku, které se má předem přidělit pro výchozí sdílený protokol na všech uzlech.  SharedLogId a SharedLogPath není nutné zadávat, aby bylo možné zadat SharedLogSizeInMB.

## <a name="service-specific-configuration"></a>Konfigurace specifická pro službu
Výchozí konfigurace stavového Reliable Services můžete upravit pomocí konfiguračního balíčku (config) nebo implementace služby (Code).

* **Konfigurace – konfigurace** prostřednictvím konfiguračního balíčku se provádí tak, že změníte Settings.xml soubor, který se vygeneruje v kořenovém adresáři balíčku Microsoft Visual Studio v konfigurační složce pro každou službu v aplikaci.
* Konfigurace **kódu** prostřednictvím kódu je dosaženo vytvořením ReliableStateManager pomocí objektu ReliableStateManagerConfiguration s odpovídající sadou možností.

Ve výchozím nastavení vyhledává modul runtime Azure Service Fabric předdefinované názvy oddílů v souboru Settings.xml a při vytváření podkladových komponent modulu runtime tyto hodnoty spotřebovává.

> [!NOTE]
> **Neodstraňujte** názvy oddílů následujících konfigurací v souboru Settings.xml, který je generován v řešení sady Visual Studio, pokud neplánujete konfiguraci služby prostřednictvím kódu.
> Přejmenování konfiguračního balíčku nebo názvu oddílu bude při konfiguraci ReliableStateManager vyžadovat změnu kódu.
> 
> 

### <a name="replicator-security-configuration"></a>Konfigurace zabezpečení replikátoru
Konfigurace zabezpečení replikátoru slouží k zabezpečení komunikačního kanálu, který se používá při replikaci. To znamená, že služby nebudou moci zobrazovat provoz replikace mezi ostatními, což zajistí, že data, která jsou nastavena jako vysoce dostupná, jsou také zabezpečená. Ve výchozím nastavení se v prázdném oddílu konfigurace zabezpečení zabrání zabezpečení replikace.

> [!IMPORTANT]
> V uzlech se systémem Linux musí být certifikáty PEM ve formátu. Další informace o vyhledání a konfiguraci certifikátů pro Linux najdete v tématu [Konfigurace certifikátů v systému Linux](./service-fabric-configure-certificates-linux.md). 
> 
> 

### <a name="default-section-name"></a>Výchozí název oddílu
ReplicatorSecurityConfig

> [!NOTE]
> Chcete-li změnit tento název oddílu, přepište parametr replicatorSecuritySectionName konstruktoru ReliableStateManagerConfiguration při vytváření ReliableStateManager pro tuto službu.
> 
> 

### <a name="replicator-configuration"></a>Konfigurace replikátoru
Konfigurace replikátoru konfigurují Replikátor, který zodpovídá za vysoce spolehlivou konfiguraci stavové spolehlivé služby tím, že replikuje a trvale uchovává stav.
Výchozí konfigurace je generována šablonou sady Visual Studio a měla by stačit. Tato část pojednává o dalších konfiguracích, které jsou k dispozici pro optimalizaci replikátoru.

### <a name="default-section-name"></a>Výchozí název oddílu
ReplicatorConfig

> [!NOTE]
> Chcete-li změnit tento název oddílu, přepište parametr replicatorSettingsSectionName konstruktoru ReliableStateManagerConfiguration při vytváření ReliableStateManager pro tuto službu.
> 
> 

### <a name="configuration-names"></a>Názvy konfigurací
| Name | Jednotka | Výchozí hodnota | Poznámky |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Sekundy |0,015 |Časové období, po které se Replikátor v sekundárním čekání po přijetí operace před odesláním zpět na primární. Jakékoli další potvrzení, která se mají odeslat pro operace zpracovávané v tomto intervalu, se odešlou jako jedna odpověď. |
| ReplicatorEndpoint |Není k dispozici |Žádný výchozí – parametr není povinný. |IP adresa a port, které bude primární a sekundární Replikátor používat ke komunikaci s ostatními replikačními replikami v sadě replik. To by mělo odkazovat na koncový bod prostředku TCP v manifestu služby. Další informace o definování prostředků koncového bodu v manifestu služby najdete v článku [prostředky manifestu služby](service-fabric-service-manifest-resources.md) . |
| MaxPrimaryReplicationQueueSize |Počet operací |8192 |Maximální počet operací v primární frontě. Když primární Replikátor dostane potvrzení ze všech sekundárních replikátorů, operace se uvolní. Tato hodnota musí být větší než 64 a mocnina 2. |
| MaxSecondaryReplicationQueueSize |Počet operací |16384 |Maximální počet operací v sekundární frontě. Po zajištění vysoké dostupnosti stavu prostřednictvím trvalosti se operace uvolní. Tato hodnota musí být větší než 64 a mocnina 2. |
| CheckpointThresholdInMB |MB |50 |Velikost místa pro soubor protokolu, po kterém je stav nastaven na kontrolní bod. |
| MaxRecordSizeInKB |KB |1024 |Největší velikost záznamu, kterou může Replikátor zapsat do protokolu. Tato hodnota musí být násobkem 4 a větší než 16. |
| MinLogSizeInMB |MB |0 (zjištěno systémem) |Minimální velikost transakčního protokolu Protokol nebude moci zkrátit na velikost pod tímto nastavením. 0 znamená, že Replikátor určí minimální velikost protokolu. Zvýšením této hodnoty se zvýší možnost provádět částečné kopírování a přírůstkové zálohování, protože by se snížila pravděpodobnost, že se zkrátí relevantní záznamy protokolů. |
| TruncationThresholdFactor |Faktor |2 |Určuje, jaká velikost protokolu se bude aktivovat. Prahová hodnota zkrácení je určena MinLogSizeInMB vynásobeným TruncationThresholdFactor. TruncationThresholdFactor musí být větší než 1. MinLogSizeInMB * TruncationThresholdFactor musí být menší než MaxStreamSizeInMB. |
| ThrottlingThresholdFactor |Faktor |4 |Určuje, jak velikost protokolu se spustí, aby se replika omezila. Prahová hodnota omezení (v MB) je určena hodnotou Max (((MinLogSizeInMB * ThrottlingThresholdFactor), (CheckpointThresholdInMB * ThrottlingThresholdFactor)). Prahová hodnota omezení (v MB) musí být větší než prahová hodnota zkrácení (v MB). Prahová hodnota zkrácení (v MB) musí být menší než MaxStreamSizeInMB. |
| MaxAccumulatedBackupLogSizeInMB |MB |800 |Maximální kumulovaná velikost (v MB) protokolů zálohování v daném řetězci záložního protokolu. Pokud přírůstkové zálohování vygeneruje protokol zálohování, který by mohl způsobit, že se nashromážděné protokoly zálohování od příslušné úplné zálohy budou větší než tato velikost, budou žádosti o přírůstkové zálohování neúspěšné. V takových případech musí uživatel provést úplnou zálohu. |
| SharedLogId |Identifikátor GUID |"" |Určuje jedinečný identifikátor GUID, který se použije pro identifikaci sdíleného souboru protokolu používaného v této replice. Služby by obvykle neměly používat toto nastavení. Pokud je však zadán parametr SharedLogId, musí být také zadán parametr SharedLogPath. |
| SharedLogPath |Plně kvalifikovaný název cesty |"" |Určuje plně kvalifikovanou cestu, kam se vytvoří sdílený soubor protokolu pro tuto repliku. Služby by obvykle neměly používat toto nastavení. Pokud je však zadán parametr SharedLogPath, musí být také zadán parametr SharedLogId. |
| SlowApiMonitoringDuration |Sekundy |300 |Nastaví interval monitorování pro spravovaná volání rozhraní API. Příklad: uživatelem poskytnutá funkce zpětného volání zálohy. Po uplynutí intervalu se do Správce stavu pošle zpráva o stavu upozornění. |
| LogTruncationIntervalSeconds |Sekundy |0 |Konfigurovatelný interval, při kterém se bude na každé replice inicializovat zkracování protokolu. Slouží k tomu, aby bylo zajištěno, že protokol je také zkrácen na základě času namísto pouze velikosti protokolu. Toto nastavení také vynutí vyprázdnění odstraněných položek ve spolehlivém slovníku. Proto se dá použít k zajištění včasného mazání odstraněných položek. |
| EnableStableReads |Logická hodnota |Nepravda |Povolení stabilních čtení omezuje sekundární repliky, aby vracely hodnoty, které byly kvorum-potvrzeno. |

### <a name="sample-configuration-via-code"></a>Ukázková konfigurace prostřednictvím kódu
```csharp
class Program
{
    /// <summary>
    /// This is the entry point of the service host process.
    /// </summary>
    static void Main()
    {
        ServiceRuntime.RegisterServiceAsync("HelloWorldStatefulType",
            context => new HelloWorldStateful(context, 
                new ReliableStateManager(context, 
        new ReliableStateManagerConfiguration(
                        new ReliableStateManagerReplicatorSettings()
            {
                RetryInterval = TimeSpan.FromSeconds(3)
                        }
            )))).GetAwaiter().GetResult();
    }
}    
```
```csharp
class MyStatefulService : StatefulService
{
    public MyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica stateManager)
        : base(context, stateManager)
    { }
    ...
}
```


### <a name="sample-configuration-file"></a>Ukázkový konfigurační soubor
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="ReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="ReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="512" />
   </Section>
   <Section Name="ReplicatorSecurityConfig">
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


### <a name="remarks"></a>Poznámky
BatchAcknowledgementInterval řídí latenci replikace. Hodnota 0 má za následek nejnižší možnou latenci. náklady na propustnost (v případě, že je nutné odesílat a zpracovávat další zprávy o potvrzení, obsahují méně potvrzení).
Čím větší je hodnota pro BatchAcknowledgementInterval, tím vyšší je celková propustnost replikace za cenu vyšší latence operace. To přímo překládá na latenci potvrzení transakcí.

Hodnota pro CheckpointThresholdInMB řídí velikost místa na disku, které může Replikátor použít k ukládání informací o stavu do vyhrazeného souboru protokolu repliky. Zvýšením hodnoty na vyšší hodnotu, než je výchozí, může způsobit rychlejší opakovanou konfiguraci při přidání nové repliky do sady. Důvodem je přenos s částečným stavem, který probíhá v důsledku dostupnosti další historie operací v protokolu. To může potenciálně prodloužit dobu obnovení repliky po havárii.

Nastavení MaxRecordSizeInKB definuje maximální velikost záznamu, který může být zapsán replikátorem do souboru protokolu. Ve většině případů je výchozí velikost záznamu 1024-KB optimální. Pokud však služba způsobuje, že jsou větší datové položky součástí informací o stavu, může být nutné zvýšit tuto hodnotu. Je málo výhod při vytváření MaxRecordSizeInKB menších než 1024, protože menší záznamy používají pouze prostor, který je potřeba pro menší záznam. Očekáváme, že by se tato hodnota musela změnit ve výjimečných případech.

Nastavení SharedLogId a SharedLogPath se vždycky používají společně k vytvoření služby, která používá samostatný sdílený protokol z výchozího sdíleného protokolu pro uzel. Pro dosažení co nejlepších výsledků je potřeba, aby bylo možné zadat stejný sdílený protokol, kolik služeb je možné. Sdílené soubory protokolu by měly být umístěné na discích, které se používají výhradně pro sdílený soubor protokolu k omezení obsahu přesunu. Očekáváme, že by se tato hodnota musela změnit ve výjimečných případech.

## <a name="next-steps"></a>Další kroky
* [Ladění aplikace Service Fabric v aplikaci Visual Studio](service-fabric-debugging-your-application.md)
* [Referenční příručka pro vývojáře pro Reliable Services](/previous-versions/azure/dn706529(v=azure.100))
