---
title: Konfigurovat spolehlivé Azure mikroslužeb | Microsoft Docs
description: Další informace o konfiguraci stavové spolehlivé služby v Azure Service Fabric.
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
ms.openlocfilehash: c5aaf9869326f2de86d3bff33f36e8f967f3e6fa
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34209997"
---
# <a name="configure-stateful-reliable-services"></a>Konfigurovat stavová spolehlivé služby
Existují dvě sady nastavení konfigurace pro spolehlivé služby. Jedna sada je globální pro všechny spolehlivé služby v clusteru, zatímco druhá sada je specifická pro konkrétní službu spolehlivé.

## <a name="global-configuration"></a>Globální konfigurace
Konfigurace globálních spolehlivé služby je zadáno v manifestu clusteru pro cluster části KtlLogger. Umožňuje konfiguraci protokolu sdílené umístění a velikost a limity globální paměti používané protokolovacího nástroje. V manifestu clusteru je jednoho souboru XML, který obsahuje nastavení a konfigurace, které platí pro všechny uzly a služby v clusteru. Soubor se obvykle nazývá ClusterManifest.xml. Můžete zobrazit clusteru manifestu clusteru pomocí příkazu Get-ServiceFabricClusterManifest prostředí powershell.

### <a name="configuration-names"></a>Názvy konfigurace
| Název | Jednotka | Výchozí hodnota | Poznámky |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Kilobajtů |8388608 |Minimální počet KB přidělit v režimu jádra pro fond vyrovnávací paměti zápisu protokolovacího nástroje. Tento fond paměti se používá pro ukládání do mezipaměti informace o stavu před zápis na disk. |
| WriteBufferMemoryPoolMaximumInKB |Kilobajtů |Bez omezení |Maximální velikost, do které protokolovacího nástroje zápis fondu vyrovnávací paměti můžou růst. |
| SharedLogId |GUID |"" |Určuje jedinečný identifikátor GUID pro identifikaci výchozí sdíleného souboru protokolu v případě všech spolehlivé služeb ve všech uzlech v clusteru, které neurčují SharedLogId v jejich konkrétní konfiguraci služby. Pokud je zadán SharedLogId, musíte zadat také tento SharedLogPath. |
| SharedLogPath |Plně kvalifikovaný název cesty |"" |Určuje plně kvalifikovanou cestu, kde soubor sdílený protokolu používané všechny spolehlivé služby ve všech uzlech v clusteru, které neurčují SharedLogPath v jejich konkrétní konfiguraci služby. Ale pokud je zadán SharedLogPath, pak SharedLogId musí také uvést. |
| SharedLogSizeInMB |Megabajtů |8192 |Určuje počet MB místa na disku staticky přidělení pro sdílené protokolu. Hodnota musí být 2 048 nebo větší. |

V Azure ARM nebo v šabloně JSON místní následující příklad ukazuje, jak změnit sdílené transakčního protokolu, která je vytvořena zálohovat všechny spolehlivé kolekce pro stavové služby.

    "fabricSettings": [{
        "name": "KtlLogger",
        "parameters": [{
            "name": "SharedLogSizeInMB",
            "value": "4096"
        }]
    }]

### <a name="sample-local-developer-cluster-manifest-section"></a>Ukázka místní vývojáře oddílu manifestu clusteru
Pokud chcete změnit to na vašem místním vývojovém prostředí, musíte upravit soubor místní clustermanifest.xml.

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
Protokolovač má globálního fondu paměti přidělené z paměti nestránkovaného jádra, která je k dispozici pro všechny spolehlivé služby v uzlu pro ukládání do mezipaměti data o stavu, než se zapíšou do protokolu vyhrazené související s replikou spolehlivé služby. Velikost fondu je řízena nastavením WriteBufferMemoryPoolMinimumInKB a WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB Určuje počáteční velikost tohoto fondu paměti a velikost nejnižší, ke kterému může zmenšit se využívá fondem paměti. WriteBufferMemoryPoolMaximumInKB je nejvyšší velikost, která může dosáhnout se využívá fondem paměti. Každou repliku spolehlivá služba, který se otevírá může zvýšit velikost fondu paměti systému určit velikost až WriteBufferMemoryPoolMaximumInKB. Pokud existuje více požadavků na paměť z fondu paměti, než je k dispozici, požadavky na paměť se odloží až do paměti je k dispozici. Proto pokud fondu zápis vyrovnávací paměť je příliš malá pro konkrétní konfigurací pak výkonu může dojít ke snížení.

Nastavení SharedLogId a SharedLogPath se vždy používají společně zadat identifikátor GUID a umístění sdílené protokolu výchozí pro všechny uzly v clusteru. Protokol sdílené výchozí se používá pro všechny spolehlivé služby, které neurčují nastavení v souborech settings.xml pro konkrétní službu. Pro nejlepší výkon musí být sdílené soubory protokolu umístěny na disky, které se používají výhradně pro soubor sdílený protokolu ke snížení kolizí.

SharedLogSizeInMB určuje množství místa na disku pro předběžné přidělení pro protokol výchozí sdílené na všech uzlech.  SharedLogId a SharedLogPath není potřeba zadat v pořadí pro SharedLogSizeInMB zadat.

## <a name="service-specific-configuration"></a>Konkrétní konfigurace služby
Stavová spolehlivé služby výchozí konfigurace můžete upravit pomocí konfiguračního balíčku (Config) nebo implementace služby (kód).

* **Konfigurace** -konfigurace prostřednictvím konfigurační balíček se provádí tak, že změníte souborech Settings.xml souboru, který je vygenerován v kořenu balíčku Microsoft Visual Studio ve složce Konfigurace pro každou službu v aplikaci.
* **Kód** -konfigurace prostřednictvím kódu se provádí tak, že vytvoříte ReliableStateManager objekt ReliableStateManagerConfiguration pomocí sady požadované možnosti.

Ve výchozím modulu runtime Azure Service Fabric předdefinované části z názvů v souboru souborech Settings.xml a odebírá hodnoty konfigurace při vytvoření základní komponenty modulu runtime.

> [!NOTE]
> Proveďte **není** odstranit části názvy následující konfigurace v souborech Settings.xml soubor, který se generuje ve řešení sady Visual Studio, pokud chcete konfigurovat služby prostřednictvím kódu.
> Přejmenování názvy konfigurace balíčku nebo oddíl, bude vyžadovat změny kódu při konfiguraci ReliableStateManager.
> 
> 

### <a name="replicator-security-configuration"></a>Konfigurace zabezpečení replikátoru
Konfigurace zabezpečení Replikátor slouží k zabezpečení komunikačního kanálu, který se používá během replikace. To znamená, služby nebudou moci zobrazit vzájemně provoz replikace, zajišťující, že data, která vysokou dostupnost, je také zabezpečené. Ve výchozím nastavení zabraňuje konfigurační oddíl prázdný zabezpečení zabezpečení replikace.

### <a name="default-section-name"></a>Výchozí název oddílu
ReplicatorSecurityConfig

> [!NOTE]
> Chcete-li změnit tento název oddílu, přepište parametr replicatorSecuritySectionName konstruktoru ReliableStateManagerConfiguration při vytváření ReliableStateManager pro tuto službu.
> 
> 

### <a name="replicator-configuration"></a>Replikátor konfigurace
Konfigurace Replikátor nakonfigurovat Replikátor, která je odpovědná za vytváření vysoce spolehlivé stavové spolehlivé služby stavu replikace a zachování stavu místně.
Výchozí konfigurace je generován šablony sady Visual Studio a měla by stačit. Tato část pojednává o další konfigurace, které jsou k dispozici pro optimalizaci replikátor.

### <a name="default-section-name"></a>Výchozí název oddílu
ReplicatorConfig

> [!NOTE]
> Chcete-li změnit tento název oddílu, přepište parametr replicatorSettingsSectionName konstruktoru ReliableStateManagerConfiguration při vytváření ReliableStateManager pro tuto službu.
> 
> 

### <a name="configuration-names"></a>Názvy konfigurace
| Název | Jednotka | Výchozí hodnota | Poznámky |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Sekundy |0.015 |Časové období, pro které Replikátor na sekundární počká po přijetí operace před odesláním zpět na primární potvrzení. Další potvrzení k odeslání pro operace zpracování v rámci tohoto intervalu se odesílají jako jedna odpověď. |
| ReplicatorEndpoint |neuvedeno |Žádná výchozí hodnota – povinný parametr |IP adresa a port, který Replikátor primární a sekundární bude používat pro komunikaci s další replikátory na replice nastavit. To by měl odkazovat koncový bod TCP prostředků v service manifest. Odkazovat na [manifestu prostředky služby](service-fabric-service-manifest-resources.md) Další informace o definování koncový bod prostředků v service manifest. |
| MaxPrimaryReplicationQueueSize |Počet operací |8192 |Maximální počet operací ve frontě primární. Operace uvolněno po primární Replikátor obdrží potvrzení ze sekundární replikátory. Tato hodnota musí být větší než 64 a druhou mocninou 2. |
| MaxSecondaryReplicationQueueSize |Počet operací |16384 |Maximální počet operací v sekundární fronty. Operace uvolněno po provedení vysoce dostupný prostřednictvím trvalost stavu. Tato hodnota musí být větší než 64 a druhou mocninou 2. |
| CheckpointThresholdInMB |MB |50 |Množství místa souboru protokolu, po jejímž uplynutí je kontrolován stav. |
| MaxRecordSizeInKB |kB |1024 |Maximální velikost záznam, který Replikátor může zapisovat do protokolu. Tato hodnota musí být násobkem 4 a je větší než 16. |
| MinLogSizeInMB |MB |0 (systém určit) |Minimální velikost transakčního protokolu. Protokol nebudou povolena, aby došlo ke zkrácení velikost pod toto nastavení. Hodnota 0 znamená, že Replikátor určí minimální velikost protokolu. Zvýšení hodnoty zvyšuje pravděpodobnost plnění od pravděpodobnost příslušných protokolových záznamy, které ke zkrácení je snížena částečné kopie a přírůstkové zálohování. |
| TruncationThresholdFactor |Koeficient |2 |Určuje, jakou velikost protokolu zkrácení aktivuje. Prahová hodnota zkrácení je určen podle MinLogSizeInMB násobí hodnotou TruncationThresholdFactor. TruncationThresholdFactor musí být větší než 1. MinLogSizeInMB * TruncationThresholdFactor musí být menší než MaxStreamSizeInMB. |
| ThrottlingThresholdFactor |Koeficient |4 |Určuje, jakou velikost protokolu replika začne omezené. Omezení prahovou hodnotu (v MB) je dáno maximální ((MinLogSizeInMB * ThrottlingThresholdFactor),(CheckpointThresholdInMB * ThrottlingThresholdFactor)). Omezení prahovou hodnotu (v MB) musí být větší než prahová hodnota zkrácení (v MB). Zkrácení prahovou hodnotu (v MB) musí být menší než MaxStreamSizeInMB. |
| MaxAccumulatedBackupLogSizeInMB |MB |800 |Maximální počet shromážděných velikost (v MB) zálohování protokolů v řetězci dané zálohy protokolu. Požadavky přírůstkové zálohování se nezdaří, pokud Přírůstková záloha by vygeneroval zálohy protokolu, které by způsobily Akumulovaná protokoly zálohování od relevantní úplné zálohování musí být větší než tato velikost. V takových případech uživatele je potřeba provést úplnou zálohu. |
| SharedLogId |GUID |"" |Určuje jedinečný identifikátor GUID pro identifikaci soubor sdílený protokolu použít s této repliky. Služby obvykle neměli používat toto nastavení. Ale pokud je zadán SharedLogId, pak SharedLogPath musí také uvést. |
| SharedLogPath |Plně kvalifikovaný název cesty |"" |Určuje plně kvalifikovanou cestu, kde bude vytvořen soubor sdílený protokolu pro tuto repliku. Služby obvykle neměli používat toto nastavení. Ale pokud je zadán SharedLogPath, pak SharedLogId musí také uvést. |
| SlowApiMonitoringDuration |Sekundy |300 |Nastaví interval monitorování pro spravované volání rozhraní API. Příklad: zadaná uživatelem funkce zálohování zpětného volání. Po uplynutí intervalu, sestavy stavu upozornění, budou odeslány do Správce stavu. |

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


### <a name="sample-configuration-file"></a>Vzorový konfigurační soubor
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
BatchAcknowledgementInterval řídí latenci replikace. Hodnota '0' výsledkem nejnižší možnou latenci, za cenu propustnost (jako další potvrzení zprávy musí být odeslána a zpracování jednotlivých obsahující méně potvrzení).
Čím větší hodnotu BatchAcknowledgementInterval, tím vyšší celkový replikace propustnost, za cenu vyšší latence operace. Výsledkem je přímo na latenci potvrzení transakcí.

Hodnota CheckpointThresholdInMB určuje množství místa na disku, které Replikátor můžete použít k ukládání stavových informací v souboru protokolu vyhrazené repliky. To zvyšuje na vyšší hodnotu než výchozí může mít za následek Rekonfigurace rychlejší, když je přidat novou repliku do sady. Toto je z důvodu přenosu částečné stavu, která se provádí z důvodu dostupnost další historie operací v protokolu. Po chybě potenciálně může prodloužit dobu obnovení repliky.

Nastavení MaxRecordSizeInKB definuje maximální velikost záznam, který lze zapisovat, které Replikátor do souboru protokolu. Ve většině případů je optimální výchozí velikost 1024 KB záznamu. Ale pokud služba způsobuje větší datové položky se jednat o část informace o stavu, pak tato hodnota možná muset zvýšit. Výhoda je malá při vytvoření MaxRecordSizeInKB menší než 1024, jako menší záznamů použijte pouze prostor potřebný pro menší záznamu. Očekáváme, že tato hodnota by bylo potřeba změnit v pouze výjimečných případech.

Nastavení SharedLogId a SharedLogPath jsou vždy společně použít k nastavení služby použití samostatných sdílených protokolu z protokolu sdílené výchozí pro uzel. Pro nejlepšího výkonu by měl tolik služby nejdříve zadejte stejné sdílené protokolu. Sdílené soubory protokolu musí být umístěny na disky, které se používají výhradně pro soubor sdílený protokolu ke snížení head přesun kolizí. Očekáváme, že tato hodnota by bylo potřeba změnit v pouze výjimečných případech.

## <a name="next-steps"></a>Další postup
* [Ladění aplikace Service Fabric v sadě Visual Studio](service-fabric-debugging-your-application.md)
* [Referenční informace pro vývojáře pro spolehlivé služby](https://msdn.microsoft.com/library/azure/dn706529.aspx)

