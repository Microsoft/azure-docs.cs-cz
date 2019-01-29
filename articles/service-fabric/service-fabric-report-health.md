---
title: Přidání vlastních stavových sestav Service Fabric | Dokumentace Microsoftu
description: Popisuje, jak k odesílání vlastních stavových sestav stavu entit Azure Service Fabric. Najdete tam doporučení pro navrhování a implementace sestav o stavu kvality.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: ''
ms.assetid: 0a00a7d2-510e-47d0-8aa8-24c851ea847f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: 775c9b155f080c8996a7680514cb2fb004a4e3fb
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55152244"
---
# <a name="add-custom-service-fabric-health-reports"></a>Přidání vlastních stavových sestav Service Fabric
Azure Service Fabric představuje [modelu stavu](service-fabric-health-introduction.md) navržené tak, aby příznak není v pořádku, cluster a aplikace podmínek u konkrétních entit. Health model používá **stavu reporters** (systémové součásti a watchdogs). Cílem je rychlou a snadnou diagnostiku a opravy. Služba zapisovače musí přemýšlet o stavu předem. Jakoukoli podmínku, která může mít vliv na stav by se měly hlásit, zejména v případě, že může být snazší příznak problémy blízko kořenový adresář. Informace o stavu můžete ušetřit čas a úsilí o ladění a šetření. Užitečnost je zvláště jasné, jakmile služba je spuštěná ve velkém měřítku v cloudu (privátní nebo Azure).

Monitor Service Fabric reporters identifikovat podmínky, které vás zajímají. Tyto sestavy tyto podmínky na základě jejich místní zobrazení. [Health store](service-fabric-health-introduction.md#health-store) agreguje data o stavu odeslaných všechny reporters k určení, zda entity jsou globálně v pořádku. Model je určen jako bohaté, flexibilní a snadno se používá. Určuje kvalitu sestav o stavu přesností zobrazení stavu clusteru. Falešně pozitivních shod nesprávně zobrazit není v pořádku, problémy mohou mít negativní vliv na upgrady nebo jiné služby, které používají data o stavu. Příkladem takové služby jsou opravy služeb a mechanismy upozorňování. Proto je uvažujete potřebných k poskytování sestavy, které zaznamenávají podmínky zájem optimálně.

Pro návrh a implementace sestav stavu watchdogs a musí být součástí systému:

* Definujte podmínky, které je zajímají, způsob, jakým se monitoruje a sníží se dopad na funkčnost clusteru nebo aplikace. Na základě těchto informací, rozhodují o vlastnost sestavy stavu a stavu.
* Určení [entity](service-fabric-health-introduction.md#health-entities-and-hierarchy) , sestava se vztahuje na.
* Určení, kde reporting je Hotovo, z v rámci služby nebo z interních nebo externích sledovacích.
* Definujte zdroj slouží k identifikaci osoby podávající hlášení.
* Volba strategie generování sestav, pravidelně nebo na přechodů. Doporučený postup je pravidelně, protože vyžaduje jednodušší kódu a je náchylný k chybám.
* Určete, jak dlouho sestavy není v pořádku podmínek by mělo zůstat v health store a jak ho by měla zůstat nezaškrtnutá. Na základě těchto informací rozhodněte sestavy Hodnota time to live a chování odebrat na vypršení platnosti.

Jak už bylo zmíněno, vytváření sestav se dá udělat z:

* Monitorované repliku služby Service Fabric.
* Interní watchdogs nasadit jako službu Service Fabric (například Service Fabric bezstavovou službu, která monitoruje podmínky a vydá sestavy). Může být watchdogs nasadit všechny uzly, nebo můžete spřažené s monitorované služby.
* Interní watchdogs spuštění na uzlech Service Fabric, které jsou *není* implementovaná jako služeb Service Fabric.
* Externí watchdogs, které pro zjišťování prostředků z *mimo* clusteru Service Fabric (například monitorování službě, jako je Gomez).

> [!NOTE]
> Hned po spuštění clusteru se vyplní sestav o stavu odeslané součásti systému. Další informace najdete v [stavu systému pomocí sestav pro řešení potíží s](service-fabric-understand-and-troubleshoot-with-system-health-reports.md). Sestavy uživatelů se musí odeslat [entity stavu](service-fabric-health-introduction.md#health-entities-and-hierarchy) , které již byly vytvořeny v systému.
> 
> 

Jednou stav vytváření sestav, je jasné, stavu sestavy lze zaslat snadno. Můžete použít [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) sestavy stav clusteru není-li [zabezpečené](service-fabric-cluster-security.md) nebo pokud fabric klient má oprávnění správce. Generování sestav můžete otevřít přes rozhraní API podle pomocí [FabricClient.HealthManager.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth), prostřednictvím prostředí PowerShell, nebo prostřednictvím REST. Konfigurace knoflíky batch sestav za účelem vylepšení výkonu.

> [!NOTE]
> Sestava stavu je synchronní a představuje fungovat ověřování pouze na straně klienta. Fakt, že sestava je přijat ve stavu klienta nebo `Partition` nebo `CodePackageActivationContext` objekty neznamená, že se použije v úložišti. Je odeslán asynchronně a pravděpodobně dávce s dalšími sestavami. Zpracování na serveru stále může selhat: pořadové číslo může být zastaralá, entity, na kterém je nutné použít sestava byla odstraněna, atd.
> 
> 

## <a name="health-client"></a>Stav klienta
K úložišti stavů prostřednictvím stavu klienta, který se nachází uvnitř klienta fabric se odesílají sestavy stavu. Stav klienta lze nastavit s následujícím nastavením:

* **HealthReportSendInterval**: Zpoždění mezi časem, sestava se přidá do klienta a čas odeslání k úložišti stavů. Používá k batch sestavy do jedné zprávy, namísto odesílání zpráv pro každou sestavu. Dávkování zvyšuje výkon. Výchozí: 30 sekund.
* **HealthReportRetrySendInterval**: Interval, kdy stav klienta znovu odešle nahromaděné stavu sestavy k úložišti stavů. Výchozí: 30 sekund.
* **HealthOperationTimeout**: Časový limit sestavy zprávy odeslané k úložišti stavů. Pokud zpráva vyprší časový limit, stavu klient pokus obnovuje ho do úložiště stavů potvrdí, že sestava se zpracovalo. Výchozí hodnota: dvě minuty.

> [!NOTE]
> Pokud jsou sestavy v dávce, klienta fabric musí být zachováno pro alespoň HealthReportSendInterval zajistit, že jejich odesláním. Pokud dojde ke ztrátě zprávy nebo stav úložiště nelze použít z důvodu přechodných chyb, klienta fabric musí být zachováno už nabízí příležitost dobře se zkuste to znovu.
> 
> 

Ukládání do vyrovnávací paměti, na straně klienta používá jedinečnost sestavy v úvahu. Například pokud je konkrétní chybný reportérka 100 sestav za sekundu na stejnou vlastnost u stejné entity, sestavy jsou nahrazeny poslední verzi. Nejvýše jeden takový sestavy existuje ve frontě klienta. Pokud dávkování je nakonfigurovaný, počet sestav zasílaných do úložiště stavů je jen jeden interval odeslat. Tato sestava je poslední přidané sestava, která odráží aktuální stav entity.
Zadejte parametry konfigurace při `FabricClient` vytvoření předáním [FabricClientSettings](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclientsettings) s požadované hodnoty pro položky související se stavem.

Následující příklad vytvoří klienta fabric a určuje, že když se přidají má být odeslán sestavy. Na časové limity a chyby, které umožňují opakovaný pokus opakované pokusy dojít každých 40 sekund.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

Doporučujeme klienta fabric výchozí nastavení, které nastavit `HealthReportSendInterval` na 30 sekund. Toto nastavení zajistí optimální výkon, protože dávkování. Pro kritické sestavy, které musí být odeslána co nejdříve, použijte `HealthReportSendOptions` s okamžité `true` v [FabricClient.HealthClient.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) rozhraní API. Okamžité sestavy obejít interval dávkování. Pomocí tohoto příznaku opatrně; Chceme, abyste mohli využívat stavu klienta do dávek, kdykoli je to možné. Okamžité odesílání je užitečné také, když se zavírá klienta fabric (například proces zjistil neplatný stav a potřebuje vypnout zabránit vedlejší účinky). Zajišťuje odesílání best effort nahromaděné sestav. Když přidáte jednu sestavu s okamžitou příznakem, stav klienta dávek všechny souhrnné sestavy od posledního odeslání.

Stejné parametry lze zadat, když se vytvoří připojení ke clusteru pomocí prostředí PowerShell. Následující příklad spustí připojení k místnímu clusteru:

```powershell
PS C:\> Connect-ServiceFabricCluster -HealthOperationTimeoutInSec 120 -HealthReportSendIntervalInSec 0 -HealthReportRetrySendIntervalInSec 40
True

ConnectionEndpoint   :
FabricClientSettings : {
                       ClientFriendlyName                   : PowerShell-1944858a-4c6d-465f-89c7-9021c12ac0bb
                       PartitionLocationCacheLimit          : 100000
                       PartitionLocationCacheBucketCount    : 1024
                       ServiceChangePollInterval            : 00:02:00
                       ConnectionInitializationTimeout      : 00:00:02
                       KeepAliveInterval                    : 00:00:20
                       HealthOperationTimeout               : 00:02:00
                       HealthReportSendInterval             : 00:00:00
                       HealthReportRetrySendInterval        : 00:00:40
                       NotificationGatewayConnectionTimeout : 00:00:00
                       NotificationCacheUpdateTimeout       : 00:00:00
                       }
GatewayInformation   : {
                       NodeAddress                          : localhost:19000
                       NodeId                               : 1880ec88a3187766a6da323399721f53
                       NodeInstanceId                       : 130729063464981219
                       NodeName                             : Node.1
                       }
```

Podobně k rozhraní API, sestavy lze zaslat pomocí `-Immediate` přepínač odeslané okamžitě, bez ohledu `HealthReportSendInterval` hodnotu.

Pro REST odesílají sestavy do brány Service Fabric, který má klientem interní infrastruktury. Ve výchozím nastavení, je tento klient nakonfigurovaný zasílání zpráv v dávce každých 30 sekund. Batch interval můžete změnit nastavení konfigurace clusteru `HttpGatewayHealthReportSendInterval` na `HttpGateway`. Jak už bylo zmíněno, je lepší volbou zasílání zpráv s `Immediate` hodnotu true. 

> [!NOTE]
> Aby bylo zajištěno, že neoprávněným služby nemůžete nahlásit stav pro entity v clusteru, konfigurace serveru tak, aby přijímal žádosti pouze zabezpečené klientů. `FabricClient` Pro vykazování, musí být schopný komunikovat s clusteru (například pomocí protokolu Kerberos nebo ověření certifikátem) povoleno zabezpečení. Další informace o [clusteru zabezpečení](service-fabric-cluster-security.md).
> 
> 

## <a name="report-from-within-low-privilege-services"></a>Sestava z v rámci služeb s nízkým oprávněním
Pokud služby Service Fabric nemají přístup správce ke clusteru, můžete vykázat stav entit na základě aktuálního kontextu prostřednictvím `Partition` nebo `CodePackageActivationContext`.

* Pro bezstavové služby použijte [IStatelessServicePartition.ReportInstanceHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatelessservicepartition.reportinstancehealth) k sestavě na aktuální instanci služby.
* Pro stavové služby, použijte [IStatefulServicePartition.ReportReplicaHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition.reportreplicahealth) zprávu o aktuální replika.
* Použití [IServicePartition.ReportPartitionHealth](https://docs.microsoft.com/dotnet/api/system.fabric.iservicepartition.reportpartitionhealth) zprávu o entitě aktuálního oddílu.
* Použití [CodePackageActivationContext.ReportApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportapplicationhealth) zprávu o aktuální aplikaci.
* Použití [CodePackageActivationContext.ReportDeployedApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedapplicationhealth) zprávu o aktuální aplikaci nasazenou na aktuálním uzlu.
* Použití [CodePackageActivationContext.ReportDeployedServicePackageHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedservicepackagehealth) chcete vygenerovat sestavu pro balíček služby pro aplikaci nasazenou na aktuálním uzlu.

> [!NOTE]
> Interně `Partition` a `CodePackageActivationContext` uložení stavu klienta konfiguruje s výchozím nastavením. Jak je vysvětleno pro [stavu klienta](service-fabric-report-health.md#health-client), jsou sestavy vložit do dávky a odesílají na časovač. Objekty by měly být zachováno mít příležitost dobře se poslat zprávu.
> 
> 

Můžete zadat `HealthReportSendOptions` při odesílání zprávy prostřednictvím `Partition` a `CodePackageActivationContext` stavu rozhraní API. Pokud máte kritické sestavy, které musí být odeslána co nejdříve použít `HealthReportSendOptions` s okamžité `true`. Okamžité sestavy obejít dávkování interval vnitřní stav klienta. Jak už bylo uvedeno dříve, pomocí tohoto příznaku opatrně; Chceme, abyste mohli využívat stavu klienta do dávek, kdykoli je to možné.

## <a name="design-health-reporting"></a>Návrh sestav stavu
Prvním krokem při generování sestav vysoce kvalitní je identifikace podmínky, které může mít vliv na stav služby. Jakoukoli podmínku, který vám pomůže příznak problémy ve službě nebo v clusteru při jeho spuštění--nebo ještě lepší, než dojde k problému – můžete potenciálně ušetřit miliard dolarů. Mezi výhody patří méně výpadků, stráví méně noční hodin potřebného ke zkoumání a opravit problémy a vyšší spokojenost zákazníků.

Jakmile jsou označeny podmínky, uživatelé vytvářející obsah sledovacího zařízení potřebovat zjistit nejlepší způsob, jak monitorovat pro vyvážení zatížení a užitečnost. Představte si třeba služba, která provádí složité výpočty, které používají některé dočasné soubory ve sdílené složce. Sledovacího zařízení může monitorovat sdílenou složku k zajištění, že je k dispozici dostatek místa. Může naslouchat oznámení změn souboru nebo adresáři. Pokud je dosaženo předem prahové hodnoty a hlášena chyba, pokud sdílená složka je plná ho může hlásit upozornění. Oprava systému na upozornění, by mohl spustit čištění starších souborů ve sdílené složce. V případě chyby může oprava systému Přesunout repliku služby do jiného uzlu. Všimněte si, jak jsou popsané podmínky stavy z hlediska stavu: stav podmínku, která lze považovat za v pořádku (ok) nebo není v pořádku (upozornění nebo chyba).

Po nastavení monitorování podrobnosti jsou zapisovač sledovacího zařízení je potřeba zjistit, jak implementovat sledovacího zařízení. Pokud podmínky lze určit v rámci služby, sledovacího zařízení můžou být součástí monitorovaných samotné služby. Například kód služby můžete zkontrolovat využití sdílené složky a vytvářet zprávy při každém pokusu o zápis do souboru. Výhodou tohoto přístupu je, že generování sestav je jednoduché. Zabránit chyby sledovacího zařízení vliv na funkčnost služby musíte věnovat pozornost.

Vytváření sestav v rámci monitorované služby není vždy možné. Sledovacího zařízení v rámci služby nemusí být schopna zjistit podmínky. Nemusí mít logiku nebo data rozhodnout. Režie monitorování podmínek může být vysoká. Podmínky také nemusí být specifické pro služby, ale místo toho ovlivnit interakci mezi službami. Další možností je, aby watchdogs v clusteru jako samostatné procesy. Watchdogs monitorování podmínek a sestavy, aniž by to ovlivnilo hlavní služby žádným způsobem. Například tyto watchdogs může implementovat jako bezstavové služby v jedné aplikaci nasadit na všech uzlech nebo na stejné uzly jako službu.

V některých případech sledovacích spuštěné v clusteru není možné zvolit buď. Pokud monitorované podmínka je dostupnost nebo služby, jak ji uživatelé uvidí, je vhodné mít watchdogs na stejném místě jako klienti uživatele. Existuje jejich testování operací stejným způsobem, jakým uživatelé volat. Například můžete mít sledovacího zařízení, která se nachází mimo cluster, problémy s požadavky na službu a zkontroluje, latence a správnost výsledku. (Pro službu kalkulačky, například 2 + 2 nevrátí 4 v přiměřené době?)

Jakmile máte byla dokončena. Podrobnosti o sledovacího zařízení, byste měli rozhodnout na ID zdroje, který ji jednoznačně identifikuje. Pokud více watchdogs stejného typu žijí v clusteru, musí vykazovat různé entity, nebo, pokud se ohlásí na stejnou entitu, použijte ID různé zdroje nebo vlastnost. Tímto způsobem své sestavy mohou existovat vedle sebe. Vlastnost sestava stavu by měl vystihnout monitorovaných podmínku. (V příkladu výše, může být vlastnost **ShareSize**.) Pokud více sestav použít stejná podmínka, by měl obsahovat vlastnost dynamických informací, které umožňuje sestavy existovat vedle sebe. Například pokud několik sdílených složek je potřeba monitorovat, název vlastnosti může být **ShareSize sharename**.

> [!NOTE]
> Proveďte *není* sloužit k udržení informace o stavu úložiště stavů. Jenom informace související se stavem by měl označení stavu, protože tyto informace ovlivňuje vyhodnocování stavu entity. Úložiště stavu nebyl navržen jako úložiště pro obecné účely. Logika vyhodnocení stavu používá k agregaci všechna data na stav v pořádku. Odesílání informací, které nesouvisí se stav (jako je vytváření sestav stavu s stav OK) nebude mít vliv na agregovaný stav v pořádku, ale může negativně ovlivnit výkon úložiště stavů.
> 
> 

Dalším kritériem je na které entity do sestavy. Ve většině případů, podmínky jednoznačně identifikují entitu. Zvolte entitu s nejlepší možné členitosti. Pokud podmínka má vliv na všechny repliky v oddílu, sestavy v oddílu, ne na službu. Krajní případy, kde další přiměje je potřeba, i když nejsou k dispozici. Pokud podmínka má vliv na entitu, jako je například repliku, ale přání je označen jako podmínku pro více než doba života repliky, pak by měly být uvedeny v oddílu. V opačném případě při odstranění repliky úložiště stavu vyčistí všechny její sestavy. Uživatelé vytvářející obsah sledovacího zařízení musíte myslet na dobu života entity a sestavy. Musí být jasné, když by měla být vyčištěna sestavy z úložiště (třeba při chybě oznámené na entitě už neplatí).

Podívejme se na příklad, který se umístí společně body, které můžu popsané. Vezměte v úvahu, že aplikace Service Fabric se skládá z hlavní stavové trvalé služby a sekundární bezstavových služeb nasazených na všech uzlech (jeden typ sekundární služby pro každý typ úlohy). Hlavní server má fronta zpracování, která obsahuje příkazy, které mají být provedeny podle sekundárních replik. Sekundárních replik provádění příchozích požadavků a odesílání signálů zpět potvrzení. Jednu podmínku, která by mohla být monitorovaná je délka fronty hlavní. Pokud délka fronty hlavní dosáhne prahové hodnoty, upozornění je hlášeno. Toto upozornění označuje, že sekundární databáze nemůže zpracovat zatížení. Pokud se nedosáhne maximální délku fronty a příkazy se zahodí, dojde k chybě, protože služby nelze obnovit. Sestavy mohou být na vlastnost **klidu**. Sledovacího zařízení kdekoli uvnitř služby, a to se pravidelně odesílají na hlavní primární repliku. Time to live je dvě minuty a odesláním pravidelně každých 30 sekund. Pokud primární ocitne mimo provoz, sestava se automaticky vyčistí z úložiště. Pokud je replika služby, ale je zablokovaná nebo pokud máte jiné potíže, sestavy vyprší platnost v health store. Entita v tomto případě je vyhodnocen při chybě.

Další typ podmínek, které je možné monitorovat je čas spuštění úlohy. Hlavní distribuuje úkoly do sekundární databáze založená na typu úloh. V závislosti na návrhu může dotazovat hlavní sekundární databáze pro stav úlohy. Může také počkat na sekundární databáze k odesílání signálů zpět potvrzení, pokud to je všechno. V druhém případě musíte věnovat pozornost k detekci situacích, kdy jsou ztraceny kostka sekundární databáze nebo zprávy. Jednou z možností je pro hlavní server k odeslání žádosti příkazu ping na stejný sekundární, který odešle zpět jeho stav. Pokud neobdrží žádná stav hlavního serveru bude považovat za chybu a přeplánuje úkolu. Toto chování se předpokládá, že úkoly jsou idempotentní.

Monitorované podmínku lze přeložit jako varování, pokud úloha není očekávána v určitém čase (**t1**, například 10 minut). Pokud úloha není dokončena v čase (**t2**, např. 20 minut), monitorovaných podmínku lze přeložit jako chyba. Tato oznámení můžete udělat několika různými způsoby:

* Hlavní primární repliky sestavy pravidelně sám na sobě. Můžete mít jednu vlastnost pro všechny čekající úkoly ve frontě. Pokud aspoň jedna úloha trvá déle, hlášení stavu na vlastnost **PendingTasks** upozornění nebo chyby, podle potřeby. Pokud neexistují žádné čekající úlohy nebo všech úloh spustil provádění pořadí, je hlášení stavu v pořádku. Úlohy jsou trvalé. Pokud primární ocitne mimo provoz, můžete dále připojovaly primární Generovat sestavy správně.
* Jiné sledovací proces (v cloudu nebo externí) kontroluje úlohy (z mimo, na základě jejího výsledku) Chcete-li zobrazit, pokud se nedokončí. Pokud nerespektují prahové hodnoty, zpráva se odešle na hlavní službou. Sestavy se také odesílají na každý úkol, který obsahuje identifikátor úlohy, jako je třeba **PendingTask + taskId**. Sestavy má být odeslána pouze stavy není v pořádku. Nastavení doby živého obsahu i několik minut a označit sestav, které chcete odebrat, pokud platnost souhlasu vyprší zajistit vyčištění.
* Sekundární, který spouští úlohu sestavy při trvá déle než obvykle k jeho spuštění. Sestavy o instanci služby na vlastnost **PendingTasks**. Sestava zjišťuje instance služby, který má problémy, ale nezachycuje situace, ve kterém instance přestane být funkční. Sestavy se potom vyčistí. Může zprávu o sekundární službu. Pokud se sekundární dokončí úlohu, sekundární instance vymaže sestavy z úložiště. Sestavu neukládá situaci, kdy dojde ke ztrátě zprávy potvrzení a úloha není dokončená z pohledu hlavní server.

Ale vytváření sestav se provádí v případech je popsáno výše, sestavy zachyceny v stav aplikací při vyhodnocování stavu.

## <a name="report-periodically-vs-on-transition"></a>Sestava pravidelně vs. na přechod
Když použijete model vytváření sestav stavu, watchdogs odeslat sestavy pravidelně nebo přechody. Doporučený postup pro vytváření sestav sledovacího zařízení je pravidelně, protože kód je mnohem jednodušší a méně náchylná k chybám. Watchdogs musí přitom se snaží být co nejjednodušší, předejdete tak chybám, které aktivují nesprávné sestavy. Nesprávný *není v pořádku* ovlivnit sestavy vyhodnocení stavu a scénářů podle stavu, včetně upgradů. Nesprávný *v pořádku* sestavy skrýt problémy v clusteru, což není žádoucí.

Pro pravidelné vytváření sestav, je možné implementovat sledovacího zařízení pomocí časovače. Na zpětné volání časovače můžete sledovacího zařízení zkontrolujte stav a odeslat zprávu na základě aktuálního stavu. Není nutné zobrazit, která sestava byl dříve odeslán nebo provést žádné optimalizace z hlediska zasílání zpráv. Stav klienta má dávkování logiku k pomoci s výkonem. Při stavu klienta je zachováno, znovu zkusí interně dokud sestavy je potvrzen úložiště stavu nebo sledovacího zařízení generuje novější sestavu s stejné entity, vlastností a zdroje.

Vytváření sestav o přechody vyžaduje pečlivé zpracování stavu. Sledovacích sleduje některé podmínky a sestavy, jenom když se změní podmínky. Vzhůru tohoto přístupu je, že je potřeba méně sestavy. Nevýhodou je komplexní logiku sledovacího zařízení. Sledovacího zařízení, musíte mít podmínky nebo sestavy, tak, aby mohly být zkontrolovány k určení stavu změny. Na převzetí služeb při selhání musí se sestavami přidán, ale dosud nebyl odeslán k úložišti stavů věnovat pozornost. Pořadové číslo musí být stále se zvětšujícím. V opačném případě sestavy jsou byl odmítnut jako zastaralé. Ve výjimečných případech, kde dojde ke ztrátě dat může být potřeba synchronizace mezi osoby podávající hlášení stavu a stavu health store.

Vytváření sestav o přechody dává smysl pro služby vytváření sestav o samotné, prostřednictvím `Partition` nebo `CodePackageActivationContext`. Při místní objekt (repliky nebo nasazený balíček služby / nasazené aplikace) je odebrána, všechny její sestavy se taky odeberou. Toto automatické čištění zmírňuje potřeba synchronizace mezi reportérka a úložištěm stavů. Pokud je sestava pro nadřazený oddíl nebo nadřazenou aplikací, musí věnovat pozornost na převzetí služeb při selhání, aby zastaralé sestavy v health store. Logiky musí přidat do udržovat správný stav a zrušte zaškrtnutí sestavy z úložiště, když už nejsou potřeba.

## <a name="implement-health-reporting"></a>Implementace sestav stavu
Jakmile podrobnosti entity a sestavy jsou jasně, funkci zasílání zpráv o stavu můžete udělat prostřednictvím rozhraní API, Powershellu nebo REST.

### <a name="api"></a>Rozhraní API
Oznámit přes rozhraní API, je potřeba vytvořit sestavy stavu specifické pro typ entity, které se chcete v sestavě. Zadejte sestavu stavu klienta. Alternativně vytvořte informací o stavu a předejte jej chcete-li vytváření sestav metod na `Partition` nebo `CodePackageActivationContext` zprávu o aktuální entity.

Následující příklad ukazuje pravidelné vytváření sestav ze sledovacího zařízení v rámci clusteru. Sledovacího zařízení zkontroluje, jestli externího prostředku lze přistupovat z v rámci uzlu. Prostředek je potřeba pro manifest služby v rámci aplikace. Pokud je prostředek nedostupný, ostatní služby v rámci aplikace může i nadále fungovat správně. Proto se zpráva se odešle na entitě balíček nasazené služby každých 30 sekund.

```csharp
private static Uri ApplicationName = new Uri("fabric:/WordCount");
private static string ServiceManifestName = "WordCount.Service";
private static string NodeName = FabricRuntime.GetNodeContext().NodeName;
private static Timer ReportTimer = new Timer(new TimerCallback(SendReport), null, 30 * 1000, 30 * 1000);
private static FabricClient Client = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });

public static void SendReport(object obj)
{
    // Test whether the resource can be accessed from the node
    HealthState healthState = this.TestConnectivityToExternalResource();

    // Send report on deployed service package, as the connectivity is needed by the specific service manifest
    // and can be different on different nodes
    var deployedServicePackageHealthReport = new DeployedServicePackageHealthReport(
        ApplicationName,
        ServiceManifestName,
        NodeName,
        new HealthInformation("ExternalSourceWatcher", "Connectivity", healthState));

    // TODO: handle exception. Code omitted for snippet brevity.
    // Possible exceptions: FabricException with error codes
    // FabricHealthStaleReport (non-retryable, the report is already queued on the health client),
    // FabricHealthMaxReportsReached (retryable; user should retry with exponential delay until the report is accepted).
    Client.HealthManager.ReportHealth(deployedServicePackageHealthReport);
}
```

### <a name="powershell"></a>PowerShell
Odeslání sestav stavu s **odeslat ServiceFabric*EntityType*HealthReport**.

Následující příklad ukazuje pravidelné vytváření sestav na hodnoty využití procesoru na uzlu. Sestavy by měly být odeslány každých 30 sekund a mají čas TTL dvě minuty. Pokud platnost souhlasu vyprší, má osoby podávající hlášení problémů, tak uzel je vyhodnocen při chybě. Když procesoru je nad prahovou hodnotou, sestava má stav varování. Pokud procesor zůstává nad prahovou hodnotou pro větší než nastavená doba, uvede se jako chyba. V opačném případě osoby podávající hlášení odešle stav OK.

```powershell
PS C:\> Send-ServiceFabricNodeHealthReport -NodeName Node.1 -HealthState Warning -SourceId PowershellWatcher -HealthProperty CPU -Description "CPU is above 80% threshold" -TimeToLiveSec 120

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='CPU', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 5
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : CPU
                        HealthState           : Warning
                        SequenceNumber        : 130741236814913394
                        SentAt                : 4/21/2015 9:01:21 PM
                        ReceivedAt            : 4/21/2015 9:01:21 PM
                        TTL                   : 00:02:00
                        Description           : CPU is above 80% threshold
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:01:21 PM
```

Následující příklad sestavy přechodných upozornění v replice. Získá první ID oddílu a pak ID repliky pro službu, kterou ho zajímají. Pak odešle zprávu z **PowershellWatcher** na vlastnost **ResourceDependency**. Sestava je relevantní pouze dvě minuty a odebere se současně z úložiště automaticky.

```powershell
PS C:\> $partitionId = (Get-ServiceFabricPartition -ServiceName fabric:/WordCount/WordCount.Service).PartitionId

PS C:\> $replicaId = (Get-ServiceFabricReplica -PartitionId $partitionId | where {$_.ReplicaRole -eq "Primary"}).ReplicaId

PS C:\> Send-ServiceFabricReplicaHealthReport -PartitionId $partitionId -ReplicaId $replicaId -HealthState Warning -SourceId PowershellWatcher -HealthProperty ResourceDependency -Description "The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes." -TimeToLiveSec 120 -RemoveWhenExpired

PS C:\> Get-ServiceFabricReplicaHealth  -PartitionId $partitionId -ReplicaOrInstanceId $replicaId


PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415594605869
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='ResourceDependency', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740768777734943
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : ResourceDependency
                        HealthState           : Warning
                        SequenceNumber        : 130741243777723555
                        SentAt                : 4/21/2015 9:12:57 PM
                        ReceivedAt            : 4/21/2015 9:12:57 PM
                        TTL                   : 00:02:00
                        Description           : The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:12:32 PM
```

### <a name="rest"></a>REST
Odeslání sestav o stavu pomocí rozhraní REST s požadavky POST, přejděte na požadovanou entitu a mají v těle popis sestavy stavu. Například zjistit, jak odeslat REST [clusteru sestav o stavu](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-cluster) nebo [služby sestav o stavu](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service). Podporují se všechny entity.

## <a name="next-steps"></a>Další postup
Na základě dat o stavu služby autoři a Správci clusteru/aplikace si můžete představit různé způsoby získávání informací. Například, můžete zachytit závažných problémů před jejich způsobit výpadky nastavit výstrahy na základě stavu. Správci můžou také nastavit systémy opravu chcete automaticky opravit problémy s.

[Úvod do služby health Service Fabric monitorování](service-fabric-health-introduction.md)

[Zobrazení sestav health Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Způsob hlášení a kontrola stavu služby](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Použití sestav stavu systému pro řešení potíží](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Monitorování a Diagnostika služeb místně](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Upgrade aplikace Service Fabric](service-fabric-application-upgrade.md)

