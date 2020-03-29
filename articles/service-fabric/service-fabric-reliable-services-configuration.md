---
title: Konfigurace spolehlivých služeb Azure Service Fabric
description: Přečtěte si o konfiguraci stavových spolehlivých služeb v aplikaci Azure Service Fabric globálně a pro jednu službu.
author: sumukhs
ms.topic: conceptual
ms.date: 10/02/2017
ms.author: sumukhs
ms.openlocfilehash: 9743213394b59af701b25b8be9dd48cf4310b499
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645510"
---
# <a name="configure-stateful-reliable-services"></a>Konfigurace stavových spolehlivých služeb
Existují dvě sady nastavení konfigurace pro spolehlivé služby. Jedna sada je globální pro všechny spolehlivé služby v clusteru, zatímco druhá sada je specifická pro konkrétní spolehlivou službu.

## <a name="global-configuration"></a>Globální konfigurace
Konfigurace globální spolehlivé služby je určena v manifestu clusteru pro cluster v části KtlLogger. Umožňuje konfiguraci sdílené umístění protokolu a velikost plus globální limity paměti používané protokolovacíprotokol. Manifest clusteru je jeden soubor XML, který obsahuje nastavení a konfigurace, které platí pro všechny uzly a služby v clusteru. Soubor se obvykle nazývá ClusterManifest.xml. Manifest clusteru pro váš cluster se zobrazí pomocí příkazu Get-ServiceFabricClusterManifest powershellu.

### <a name="configuration-names"></a>Názvy konfigurací
| Name (Název) | Jednotka | Výchozí hodnota | Poznámky |
| --- | --- | --- | --- |
| ZápisBufferMemoryPoolminimumInKB |Kilobajtech |8388608 |Minimální počet KB přidělit v režimu jádra pro zásobníku paměti paměti zápisu protokolů. Tento fond paměti se používá pro ukládání informací o stavu ukládání do mezipaměti před zápisem na disk. |
| WriteBufferMemoryPoolMaximumInKB |Kilobajtech |Bez omezení |Maximální velikost, na kterou může růst fond vyrovnávací paměti pro zápis do protokolování. |
| SharedLogId |GUID |"" |Určuje jedinečný identifikátor GUID, který se má použít k identifikaci výchozího sdíleného souboru protokolu používaného všemi spolehlivými službami ve všech uzlech v clusteru, které neurčují identifikátor SharedLogId v konfiguraci specifické pro službu. Pokud sharedLogId je zadán, pak SharedLogPath musí být také zadán. |
| SharedLogPath |Plně kvalifikovaný název cesty |"" |Určuje plně kvalifikovanou cestu, kde sdílený soubor protokolu používaný všemi spolehlivými službami ve všech uzlech v clusteru, které neurčují sdílenou položku The SharedLogPath v konfiguraci specifické pro službu. Pokud je však zadána aplikace SharedLogPath, musí být zadána také sharedlogid. |
| SharedLogSizeInMB |Megabajtů |8192 |Určuje počet MB místa na disku, které má být staticky přiděleno pro sdílený protokol. Hodnota musí být 2048 nebo větší. |

V Azure ARM nebo místní šabloně JSON následující příklad ukazuje, jak změnit protokol sdílených transakcí, který získá vytvořené pro zálohování všechny spolehlivé kolekce pro stavové služby.

    "fabricSettings": [{
        "name": "KtlLogger",
        "parameters": [{
            "name": "SharedLogSizeInMB",
            "value": "4096"
        }]
    }]

### <a name="sample-local-developer-cluster-manifest-section"></a>Ukázka oddílu manifestu místního vývojářského clusteru
Chcete-li tuto změnu změnit v místním vývojovém prostředí, je třeba upravit místní soubor clustermanifest.xml.

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
Protokolování má globální fond paměti přidělené z paměti bez stránkovaného jádra, který je k dispozici pro všechny spolehlivé služby na uzlu pro ukládání dat stavu do mezipaměti před zápisem do vyhrazeného protokolu přidruženého k replikě spolehlivé služby. Velikost fondu je řízena nastaveními WriteBufferMemoryPoolMaximumInKB a WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB určuje počáteční velikost tohoto fondu paměti a nejnižší velikost, do které může zmenšit fondu paměti. WriteBufferMemoryPoolMaximumInKB je nejvyšší velikost, do které může růst fondu paměti. Každá replika spolehlivé služby, která je otevřena může zvýšit velikost fondu paměti o systémem určené množství až WriteBufferMemoryPoolMaximumInKB. Pokud je větší poptávka po paměti z fondu paměti, než je k dispozici, požadavky na paměť bude zpožděna, dokud není k dispozici paměť. Proto pokud fond paměti vyrovnávací paměti zápisu je příliš malý pro konkrétní konfiguraci pak může dojít k unesení výkonu.

Nastavení SharedLogId a SharedLogPath se vždy používají společně k definování identifikátoru GUID a umístění výchozího sdíleného protokolu pro všechny uzly v clusteru. Výchozí sdílený protokol se používá pro všechny spolehlivé služby, které neurčují nastavení v souboru settings.xml pro konkrétní službu. Pro dosažení nejlepšího výkonu by měly být sdílené soubory protokolu umístěny na disky, které se používají výhradně pro sdílený soubor protokolu ke snížení kolizí.

SharedLogSizeInMB určuje velikost místa na disku, které má být předem přiděleno pro výchozí sdílený protokol ve všech uzlech.  SharedLogId a SharedLogPath není nutné zadat, aby bylo určeno SharedLogSizeInMB.

## <a name="service-specific-configuration"></a>Konfigurace specifická pro službu
Výchozí konfigurace stavových služeb spolehlivé služby můžete upravit pomocí konfiguračního balíčku (Config) nebo implementace služby (kód).

* **Config** - Konfigurace prostřednictvím balíčku config se provádí změnou souboru Settings.xml, který je generován v kořenovém adresáři balíčku sady Microsoft Visual Studio pod složkou Config pro každou službu v aplikaci.
* **Kód** - Konfigurace pomocí kódu se provádí vytvořením reliablestatemanager pomocí ReliableStateManagerConfiguration objektu s příslušnými sada možností.

Ve výchozím nastavení prostředí Azure Service Fabric hledá předdefinované názvy oddílů v souboru Settings.xml a spotřebovává hodnoty konfigurace při vytváření podkladových komponent runtime.

> [!NOTE]
> **Neodstraňujte** názvy oddílů následujících konfigurací v souboru Settings.xml, který je generován v řešení sady Visual Studio, pokud neplánujete konfigurovat službu pomocí kódu.
> Přejmenování balíčku konfigurace nebo názvů oddílů bude vyžadovat změnu kódu při konfiguraci služby ReliableStateManager.
> 
> 

### <a name="replicator-security-configuration"></a>Konfigurace zabezpečení replikátoru
Konfigurace zabezpečení replikátoru se používají k zabezpečení komunikačního kanálu, který se používá během replikace. To znamená, že služby nebudou moci zobrazit navzájem replikační provoz, zajištění, že data, která je vysoce dostupné je také zabezpečené. Ve výchozím nastavení brání prázdný oddíl konfigurace zabezpečení zabezpečení zabezpečení zabezpečení.

> [!IMPORTANT]
> V uzlech Linuxu musí být certifikáty ve formátu PEM. Další informace o vyhledání a konfiguraci certifikátů pro Linux najdete [v tématu Konfigurace certifikátů v Linuxu](./service-fabric-configure-certificates-linux.md). 
> 
> 

### <a name="default-section-name"></a>Výchozí název oddílu
ReplicatorSecurityConfig

> [!NOTE]
> Chcete-li změnit název tohoto oddílu, přepište parametr replicatorSecuritySectionName na konstruktor reliablestatemanagerconfiguration při vytváření služby ReliableStateManager pro tuto službu.
> 
> 

### <a name="replicator-configuration"></a>Konfigurace replikátoru
Konfigurace replikátoru nakonfigurují replikátor, který je zodpovědný za to, že stavového stavu spolehlivé služby je vysoce spolehlivý replikací a uchováním stavu místně.
Výchozí konfigurace je generována šablonou sady Visual Studio a měla by stačit. Tato část hovoří o další konfigurace, které jsou k dispozici pro ladění replikátoru.

### <a name="default-section-name"></a>Výchozí název oddílu
ReplikátorConfig

> [!NOTE]
> Chcete-li změnit název tohoto oddílu, přepište parametr replicatorSettingsSectionName na konstruktor reliablestatemanagerconfiguration při vytváření služby ReliableStateManager pro tuto službu.
> 
> 

### <a name="configuration-names"></a>Názvy konfigurací
| Name (Název) | Jednotka | Výchozí hodnota | Poznámky |
| --- | --- | --- | --- |
| Interval potvrzení dávky |Sekundy |0.015 |Časové období, pro které replikátor na sekundární čeká po přijetí operace před odesláním zpět potvrzení primární. Všechna ostatní potvrzení, která mají být odeslána pro operace zpracované v tomto intervalu, jsou odeslána jako jedna odpověď. |
| ReplikátorEndpoint |Není dostupné. |Žádný výchozí -- povinný parametr |Adresa IP a port, který primární a sekundární replikátor použije ke komunikaci s ostatními replikátory v sadě replik. To by měl odkazovat na koncový bod prostředku TCP v manifestu služby. Odkazovat na [service manifest prostředky](service-fabric-service-manifest-resources.md) se přečtěte více o definování prostředků koncového bodu v manifestu služby. |
| Velikost maxprimárníreplikační fronty |Počet operací |8192 |Maximální počet operací v primární frontě. Operace je uvolněna poté, co primární replikátor obdrží potvrzení od všech sekundárních replikátorů. Tato hodnota musí být větší než 64 a mocninu 2. |
| Velikost fronty maxsekundární replikace |Počet operací |16384 |Maximální počet operací v sekundární frontě. Operace je uvolněna po provedení jeho stavu vysoce dostupné prostřednictvím trvalosti. Tato hodnota musí být větší než 64 a mocninu 2. |
| CheckpointThresholdInMB |MB |50 |Velikost místa v souboru protokolu, po kterém je stav kontrolním bodem. |
| MaxRecordSizeInKB |KB |1024 |Největší velikost záznamu, který replikátor může zapsat do protokolu. Tato hodnota musí být násobkem 4 a větší než 16. |
| MinLogSizeInMB |MB |0 (systém určen) |Minimální velikost transakční protokol. Protokol nebude moci zkrátit na velikost pod toto nastavení. 0 označuje, že replikátor určí minimální velikost protokolu. Zvýšení této hodnoty zvyšuje možnost provedení částečné kopie a přírůstkové zálohování, protože je snížena pravděpodobnost, že příslušné záznamy protokolu jsou zkráceny. |
| Faktor reruncationThresholdFactor |Faktor |2 |Určuje, jakou velikost protokolu bude spuštěna zkrácení. Zkrácení prahová hodnota je určena MinLogSizeInMB vynásobené TruncationThresholdFactor. ZkráceníThresholdFactor musí být větší než 1. MinLogSizeInMB * ZkráceníThresholdFactor musí být menší než MaxStreamSizeInMB. |
| Faktor omezení threshold |Faktor |4 |Určuje, jakou velikost protokolu, replika začne omezena. Prahová hodnota omezení (v MB) je určena max((MinLogSizeInMB * OmezeníThresholdFactor),(CheckpointThresholdInMB * OmezeníThresholdFactor)). Prahová hodnota omezení (v MB) musí být větší než prahová hodnota zkrácení (v MB). Zkrácení prahová hodnota (v MB) musí být menší než MaxStreamSizeInMB. |
| MaxAkumavovanýprogramALogSizeInMB |MB |800 |Maximální akumulovaná velikost (v MB) protokolů zálohování v daném řetězci protokolu zálohování. Přírůstkové požadavky na zálohování se nezdaří, pokud přírůstkové zálohování by generovat protokol zálohování, které by způsobily akumulované protokoly zálohování, protože příslušné úplné zálohování být větší než tato velikost. V takových případech je uživatel povinen provést úplnou zálohu. |
| SharedLogId |GUID |"" |Určuje jedinečný identifikátor GUID, který se má použít k identifikaci sdíleného souboru protokolu použitého v této replice. Služby by obvykle neměly používat toto nastavení. Pokud je však zadáno SharedLogId, musí být zadána také aplikace SharedLogPath. |
| SharedLogPath |Plně kvalifikovaný název cesty |"" |Určuje plně kvalifikovanou cestu, kde bude vytvořen sdílený soubor protokolu pro tuto repliku. Služby by obvykle neměly používat toto nastavení. Pokud je však zadána aplikace SharedLogPath, musí být zadána také sharedlogid. |
| SlowApiMonitoringDuration |Sekundy |300 |Nastaví interval monitorování pro spravovaná volání rozhraní API. Příklad: uživatel poskytl funkci zpětného volání zálohování. Po uplynutí intervalu bude správci stavu odeslána zpráva o stavu upozornění. |
| LogTruncationIntervalSeconds |Sekundy |0 |Konfigurovatelný interval, ve kterém bude zahájeno zkrácení protokolu na každé replice. Používá se k zajištění protokolu je také zkrácen na základě času namísto pouze velikost protokolu. Toto nastavení také vynutí vymazání odstraněných položek ve spolehlivém slovníku. Proto jej lze použít k zajištění odstranění položek jsou vymazány včas. |
| EnableStableReads |Logická hodnota |False |Povolení stabilní čtení omezuje sekundární repliky na vrácení hodnoty, které byly kvorum-acked. |

### <a name="sample-configuration-via-code"></a>Ukázková konfigurace pomocí kódu
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
BatchAcknowledgementInterval řídí latenci replikace. Hodnota "0" má za následek nejnižší možnou latenci za cenu propustnosti (protože musí být odesláno a zpracováno více potvrzení zpráv, z nichž každá obsahuje méně potvrzení).
Čím větší je hodnota batchacknowledgementinterval, tím vyšší je celková propustnost replikace za cenu vyšší latence provozu. To se přímo promítá do latence potvrzení transakcí.

Hodnota pro CheckpointThresholdInMB řídí velikost místa na disku, které replikátor můžete použít k ukládání informací o stavu v souboru vyhrazené protokolu repliky. Zvýšení této hodnoty na vyšší hodnotu než výchozí může mít za následek rychlejší rekonfigurace časy při přidání nové repliky do sady. To je způsobeno částečným přenosem stavu, ke kterému dochází z důvodu dostupnosti další historie operací v protokolu. To může potenciálně zvýšit dobu obnovení repliky po selhání.

Nastavení MaxRecordSizeInKB definuje maximální velikost záznamu, který může replikátor zapsat do souboru protokolu. Ve většině případů je optimální výchozí velikost záznamu 1024 KB. Pokud však služba způsobuje, že větší datové položky jsou součástí informací o stavu, může být nutné tuto hodnotu zvýšit. Je malý přínos v tom, že MaxRecordSizeInKB menší než 1024, protože menší záznamy používají pouze místo potřebné pro menší záznam. Očekáváme, že tato hodnota bude muset být změněna pouze ve vzácných případech.

Nastavení SharedLogId a SharedLogPath se vždy používají společně k tomu, aby služba používala samostatný sdílený protokol z výchozího sdíleného protokolu pro uzel. Pro nejlepší efektivitu by mělo zadat stejný sdílený protokol co nejvíce služeb. Sdílené soubory protokolu by měly být umístěny na disky, které se používají výhradně pro sdílený soubor protokolu ke snížení tvrzení o pohybu hlavy. Očekáváme, že tato hodnota bude muset být změněna pouze ve vzácných případech.

## <a name="next-steps"></a>Další kroky
* [Ladění aplikace Service Fabric v sadě Visual Studio](service-fabric-debugging-your-application.md)
* [Odkaz pro vývojáře pro spolehlivé služby](https://msdn.microsoft.com/library/azure/dn706529.aspx)

