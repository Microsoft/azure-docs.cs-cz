---
title: Konfigurace reliable mikroslužeb Azure | Dokumentace Microsoftu
description: Další informace o konfiguraci stavovém modelu Reliable Services v Azure Service Fabric.
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: timlt
editor: vturecek
ms.assetid: 9f72373d-31dd-41e3-8504-6e0320a11f0e
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/02/2017
ms.author: sumukhs
ms.openlocfilehash: f2af7c65d42cbbec28fd511be18c72a6cd3c3d0c
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2018
ms.locfileid: "39249015"
---
# <a name="configure-stateful-reliable-services"></a>Konfigurace stavového modelu reliable services
Existují dvě sady nastavení konfigurace pro služby reliable services. Jedna sada je globální pro všechny služby reliable services v clusteru, zatímco je další sada specifická pro konkrétní službu reliable.

## <a name="global-configuration"></a>Globální konfigurace
V manifestu clusteru pro cluster v části KtlLogger je zadána konfigurace globálních spolehlivé služby. To umožňuje konfiguraci protokolu sdílené umístění a velikost a omezení globální paměti používané protokolovacího nástroje. V manifestu clusteru je jeden soubor XML obsahující nastavení a konfigurace, které platí pro všechny uzly a služby v clusteru. Soubor se obvykle nazývá ClusterManifest.xml. Zobrazí se clusteru pro cluster pomocí příkazu powershellu Get-ServiceFabricClusterManifest manifestu.

### <a name="configuration-names"></a>Názvy konfigurace
| Název | Jednotka | Výchozí hodnota | Poznámky |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Kilobajtů |8388608 |Minimální počet Kilobajtů přidělení v režimu jádra pro fond vyrovnávací paměti zápisu protokolovacího nástroje. Tento fond paměti se používá pro ukládání do mezipaměti informace o stavu před zápisu na disk. |
| WriteBufferMemoryPoolMaximumInKB |Kilobajtů |Bez omezení |Maximální velikost, ke kterému protokolovač zápis vyrovnávací paměti fondu paměti můžou růst. |
| SharedLogId |GUID |"" |Určuje jedinečný identifikátor GUID pro účely identifikace výchozí sdíleného souboru protokolu používá všechny spolehlivé služby na všech uzlech v clusteru, u kterých není SharedLogId v jejich konkrétní konfiguraci služby. Pokud SharedLogId není zadána, musíte zadat také tento SharedLogPath. |
| SharedLogPath |Plně kvalifikovaný název cesty |"" |Určuje plně kvalifikovanou cestu použití sdíleného protokol všech spolehlivých služeb ve všech uzlech v clusteru, u kterých není SharedLogPath v jejich konkrétní konfiguraci služby. Nicméně pokud SharedLogPath je, pak SharedLogId musí také uvést. |
| SharedLogSizeInMB |Megabajtů |8192 |Určuje počet MB místa na disku se staticky přidělit pro sdílené protokolu. Hodnota musí být 2 048 nebo větší. |

V Azure ARM nebo v šabloně JSON v místním následující příklad ukazuje, jak změnit sdílené transakčního protokolu, která se vytvoří zálohovat všechny spolehlivé kolekce pro stavové služby.

    "fabricSettings": [{
        "name": "KtlLogger",
        "parameters": [{
            "name": "SharedLogSizeInMB",
            "value": "4096"
        }]
    }]

### <a name="sample-local-developer-cluster-manifest-section"></a>Ukázka místního vývojového clusteru oddílu manifestu
Pokud chcete změnit to ve svém místním vývojovém prostředí, budete muset upravit clustermanifest.xml místní soubor.

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
Protokolovač se globální fond paměti přidělené z paměti nestránkovaná jádra, který je k dispozici pro všechny spolehlivé služby v uzlu pro ukládání do mezipaměti dat o stavu před zápisem do protokolu vyhrazené přidružené repliky spolehlivé služby. Velikost fondu je řízena nastavením WriteBufferMemoryPoolMinimumInKB a WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB Určuje počáteční velikost tohoto fondu paměti a představuje nejmenší velikost, ke kterému může zmenšit fondu paměti. WriteBufferMemoryPoolMaximumInKB je nejvyšší velikost, ke kterému může dosáhnout fondu paměti. Každou repliku spolehlivé služby, která je otevřena může systému určit množství až WriteBufferMemoryPoolMaximumInKB zvětšit velikost fondu paměti. Pokud existuje více poptávka po paměti z fondu paměti, než je k dispozici, požadavky na paměť bude zpozdit. dokud nebude k dispozici paměť. Proto pokud fond zápisu vyrovnávací paměti je příliš malá pro konkrétní konfiguraci pak výkonu může dojít ke snížení.

Nastavení SharedLogId a SharedLogPath se vždy společně používají k definování GUID a umístění pro výchozí sdílené protokolu pro všechny uzly v clusteru. Výchozí sdílené protokol se používá pro všechny služby reliable services, u kterých není nastavení v settings.xml pro konkrétní službu. Pro zajištění nejlepšího výkonu by měl umístit sdílené soubory protokolu na discích, které se používají výhradně pro soubor protokolu sdílené za účelem omezení kolizí.

SharedLogSizeInMB určuje množství místa na disku pro předběžné přidělení pro protokol výchozí sdílené ve všech uzlech.  SharedLogId a SharedLogPath není nutné zadat v pořadí pro SharedLogSizeInMB zadání.

## <a name="service-specific-configuration"></a>Konkrétní konfigurace služby
Výchozí konfigurace stavovém modelu Reliable Services' můžete upravit pomocí konfiguračního balíčku (konfigurace) nebo implementace služby (kód).

* **Konfigurace** – konfigurace prostřednictvím balíčku konfigurace se provádí tak, že změníte Settings.xml souboru, který je vygenerován v kořenovém adresáři balíčku Microsoft Visual Studio ve složce Konfigurace pro každou službu v aplikaci.
* **Kód** -prostřednictvím kódu je nakonfigurovat tak, že vytvoříte ReliableStateManager pomocí objektu ReliableStateManagerConfiguration sadou odpovídající možnosti.

Ve výchozím nastavení modul runtime Azure Service Fabric vyhledá názvy předdefinovaných oddílů v souboru Settings.xml a využívá hodnoty konfigurace při vytvoření základní komponenty prostředí runtime.

> [!NOTE]
> Proveďte **není** odstranit názvy oddílů následující konfigurace v souboru Settings.xml, který je generován v řešení sady Visual Studio, pokud máte v plánu konfigurace služby prostřednictvím kódu.
> Přejmenování názvy konfigurace balíčku nebo části budou vyžadovat změny kódu, při konfiguraci ReliableStateManager.
> 
> 

### <a name="replicator-security-configuration"></a>Konfigurace zabezpečení replikátoru
Konfigurace zabezpečení replikátoru slouží k zabezpečení komunikačního kanálu, který se používá během replikace. To znamená, že služby nebudou vidět uživatele toho druhého provoz replikace, zajišťující, že je také data, která je k dispozici vysoce zabezpečené. Ve výchozím nastavení zabraňuje oddíl konfigurace prázdný zabezpečení zabezpečení replikace.

> [!IMPORTANT]
> Certifikáty na uzly s Linuxem, musí být ve formátu PEM. Další informace o vyhledání a konfigurace certifikátů pro Linux najdete v tématu [konfigurace certifikátů v Linuxu](./service-fabric-configure-certificates-linux.md). 
> 
> 

### <a name="default-section-name"></a>Výchozí název oddílu
ReplicatorSecurityConfig

> [!NOTE]
> Chcete-li změnit tento název oddílu, přepište parametr replicatorSecuritySectionName konstruktoru ReliableStateManagerConfiguration při vytváření ReliableStateManager pro tuto službu.
> 
> 

### <a name="replicator-configuration"></a>Konfigurace replikace
Konfigurace replikace nakonfigurovat replikace, která zodpovídá za zajištění vysoce spolehlivé stavové služby Reliable stavu na základě replikaci a při zachování stavu místně.
Výchozí konfigurace vygeneruje šablony sady Visual Studio a měl postačit. Tato část se hovoří o dalším konfiguracím, které jsou k dispozici pro vyladění replikátoru.

### <a name="default-section-name"></a>Výchozí název oddílu
ReplicatorConfig

> [!NOTE]
> Chcete-li změnit tento název oddílu, přepište parametr replicatorSettingsSectionName konstruktoru ReliableStateManagerConfiguration při vytváření ReliableStateManager pro tuto službu.
> 
> 

### <a name="configuration-names"></a>Názvy konfigurace
| Název | Jednotka | Výchozí hodnota | Poznámky |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Sekundy |0.015 |Časové období, pro které Replikátor na sekundární čeká po přijetí operace před odesláním zpět na primární potvrzení. Další potvrzení k odeslání pro operace zpracování v rámci tohoto intervalu se odesílají jako jednu odpověď. |
| ReplicatorEndpoint |neuvedeno |Žádná výchozí hodnota--povinný parametr |Nastavení IP adresy a portu, které Replikátor primárního a sekundárního se používají ke komunikaci s další replikátorů v replice. To by měla odkazovat na koncový bod TCP prostředků v manifestu služby. Odkazovat na [prostředky manifestu služby](service-fabric-service-manifest-resources.md) Další informace o definování koncový bod prostředků v manifestu služby. |
| MaxPrimaryReplicationQueueSize |Počet operací |8192 |Maximální počet operací ve frontě primární. Operace je uvolněna až primární Replikátor obdrží od všech sekundárních replikátorů potvrzení. Tato hodnota musí být větší než 64 a mocninou čísla 2. |
| MaxSecondaryReplicationQueueSize |Počet operací |16384 |Maximální počet operací ve frontě sekundární. Operace je uvolněna až po zajištění vysoké dostupnosti prostřednictvím trvalého stavu. Tato hodnota musí být větší než 64 a mocninou čísla 2. |
| CheckpointThresholdInMB |MB |50 |Množství místa souboru protokolu, po jejímž uplynutí je stav byl vytvořen kontrolní bod. |
| MaxRecordSizeInKB |kB |1024 |Největší velikost záznamu, který Replikátor může zapisovat do protokolu. Tato hodnota musí být násobkem 4 a je větší než 16. |
| MinLogSizeInMB |MB |0 (určit systém) |Minimální velikost protokolu transakcí. V protokolu, nebudou moct zkrátit na velikost pod toto nastavení. Hodnota 0 znamená, že Replikátor určí minimální velikost protokolu. Zvýšení této hodnoty zvyšuje pravděpodobnost způsobem částečné kopie a přírůstkové zálohování od šance na příslušných protokolových záznamy, které vedlo ke zkrácení klesnou. |
| TruncationThresholdFactor |faktor |2 |Určuje, jakou velikost protokolu se budou aktivovat zkrácení. Prahová hodnota pro zkrácení je určeno MinLogSizeInMB vynásobené TruncationThresholdFactor. TruncationThresholdFactor musí být větší než 1. MinLogSizeInMB * TruncationThresholdFactor musí být menší než MaxStreamSizeInMB. |
| ThrottlingThresholdFactor |faktor |4 |Určuje, jakou velikost protokolu replika začne omezené. Omezení prahovou hodnotu (v MB) se určuje podle maximální ((MinLogSizeInMB * ThrottlingThresholdFactor),(CheckpointThresholdInMB * ThrottlingThresholdFactor)). Omezení prahovou hodnotu (v MB) musí být větší než prahová hodnota zkrácení (v MB). Zkrácení prahovou hodnotu (v MB) musí být menší než MaxStreamSizeInMB. |
| MaxAccumulatedBackupLogSizeInMB |MB |800 |Maximální počet shromážděných velikost (v MB) protokoly zálohování v řetězci dané zálohy protokolu. Přírůstkové zálohování žádostí selže, pokud Přírůstková záloha vygeneruje protokol zálohování, která může způsobit nahromaděné protokoly zálohování od odpovídající úplné zálohování větší než tato velikost. V takovém případě je potřeba uživatele proveďte úplnou zálohu. |
| SharedLogId |GUID |"" |Určuje jedinečný identifikátor GUID pro účely identifikace sdílené protokol využít tuto repliku. Služby by neměl obvykle, pomocí tohoto nastavení. Nicméně pokud SharedLogId je, pak SharedLogPath musí také uvést. |
| SharedLogPath |Plně kvalifikovaný název cesty |"" |Určuje plně kvalifikovanou cestu, kde se vytvoří soubor sdílený protokolu pro tuto repliku. Služby by neměl obvykle, pomocí tohoto nastavení. Nicméně pokud SharedLogPath je, pak SharedLogId musí také uvést. |
| SlowApiMonitoringDuration |Sekundy |300 |Nastaví interval monitorování pro spravované volání rozhraní API. Příklad: uživatel k dispozici funkce zálohování zpětného volání. Po uplynutí intervalu, sestava stavu upozornění se odešlou do Správce stavu. |
| LogTruncationIntervalSeconds |Sekundy |0 |Při přihlášení, které se iniciovalo zkrácení na každou repliku konfigurovatelným intervalem. Používá se k Ujistěte se, že protokol zkrácen také na základě času namísto pouze velikost protokolu. Toto nastavení taky vynutí, mazání odstraněných položek spolehlivého slovníku. Proto jej lze použít k zajištění odstraněné položky mažou včas. |

### <a name="sample-configuration-via-code"></a>Ukázkové konfiguraci prostřednictvím kódu
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
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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
Latence replikace BatchAcknowledgementInterval ovládacích prvků. Hodnota '0' za následek nejnižší možnou latenci, a to za cenu propustnost (jako další zprávy potvrzení musí být odeslána a zpracována, každá obsahuje méně potvrzení).
Čím větší hodnotu BatchAcknowledgementInterval, tím vyšší celkovou replikaci propustnost, za cenu vyšší latence operace. Výsledkem je přímo na latenci potvrzení transakcí.

Hodnota pro CheckpointThresholdInMB určuje množství místa na disku, které Replikátor můžete použít k ukládání informací o stavu v souboru protokolu vyhrazené repliky. To zvyšuje na vyšší hodnotu než výchozí by mohlo způsobit změna konfigurace rychlejší při přidání novou repliku do sady. To je způsobeno přenos částečné stavu, které u něho kvůli dostupnosti další historie operace v protokolu. To potenciálně může prodloužit dobu obnovení replik po zhroucení.

Nastavení MaxRecordSizeInKB definuje maximální velikost záznamu, který můžou Replikátor zapisovat do souboru protokolu. Ve většině případů je optimální výchozí velikost záznamu 1 024 KB. Ale pokud služba způsobuje větších datových položek jako součást informací o stavu, pak tato hodnota možná bude nutné navýšit. Výhoda je malá při vytvoření MaxRecordSizeInKB menší než 1024, menší záznamy k používání pouze prostor potřebný pro menší záznamu. Očekáváme, že tato hodnota by bylo potřeba změnit jen vzácně.

Nastavení SharedLogId a SharedLogPath se vždy používají společně službu použití samostatných sdílených protokolu z výchozí sdílené protokolu pro uzel. Pro nejlepší účinnosti by měl tolik služby nejvíce zadejte stejné sdílené protokolu. Sdílené soubory protokolu musí být umístěny na disky, které se používají výhradně pro soubor protokolu sdílené za účelem omezení kolizí hlavní pohyb. Očekáváme, že tato hodnota by bylo potřeba změnit jen vzácně.

## <a name="next-steps"></a>Další postup
* [Ladění aplikace Service Fabric v sadě Visual Studio](service-fabric-debugging-your-application.md)
* [Referenční informace pro vývojáře pro službu Reliable Services](https://msdn.microsoft.com/library/azure/dn706529.aspx)

