---
title: Přidat vlastní sestavy stavu Service Fabric | Microsoft Docs
description: Popisuje, jak odesílat sestavy vlastní stavu entity stavu Azure Service Fabric. Poskytuje doporučení pro navrhování a implementace sestav stavu kvality.
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
ms.openlocfilehash: 3eccb6ba18e6689c3726c8d930279b8a85ab1c92
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="add-custom-service-fabric-health-reports"></a>Přidání vlastních stavových sestav Service Fabric
Představuje Azure Service Fabric [stavu modelu](service-fabric-health-introduction.md) navržený tak, aby příznak není v pořádku, cluster a aplikace podmínek na konkrétní entity. Health model používá **stavu reporters** (součásti systému a watchdogs). Cílem je rychlé a snadné diagnostiky a opravy. Služba zapisovače muset myslíte o stavu předem. Všechny podmínku, která může mít vliv na stav by měl být zaznamenány na, zejména v případě, že může pomoci příznak problémy blízko kořenu. Informace o stavu můžete ušetřit čas a úsilí na ladění a šetření. Užitečnost je obzvláště vymazat, jakmile služba je spuštěná ve velkém měřítku v cloudu (privátní nebo Azure).

Monitorování reporters Service Fabric identifikovat podmínky, které vás zajímají. Sestavy budou tyto podmínky založené na jejich místní zobrazení. [Úložiště stavu](service-fabric-health-introduction.md#health-store) agreguje data o stavu posílá všechny reporters určit, jestli entity jsou globální v pořádku. Model má být bohaté, flexibilní a snadno se používá. Kvalitu sestav stavu určuje přesnost zobrazení stavu clusteru. Falešně pozitivních zjištění, které se nesprávně zobrazí není v pořádku problémy může mít negativní vliv na upgrady nebo jiné služby, které používají data o stavu. Příkladem takové služby jsou opravy služeb a mechanismy pro výstrahy. Proto je některé myšlenku potřebných k poskytování sestavy, které zaznamenat podmínky zájem o optimálně.

Návrh a implementaci vytváření sestav stavu, watchdogs a součástí systému musí:

* Zadejte podmínku, kterou budou zajímat, způsob, jakým je monitorovaný a dopad na funkci clusteru nebo aplikace. Na základě této informace, vyberte vlastnost sestavy stavu a stavu.
* Určení [entity](service-fabric-health-introduction.md#health-entities-and-hierarchy) vztahující se k sestavě.
* Určení, kde je reporting done, z v rámci služby nebo interní nebo externí sledovací zařízení.
* Definujte zdroj použít k identifikaci ohlašování.
* Volba strategie vytváření sestav, pravidelně nebo na přechody. Doporučený způsob je pravidelně jako vyžaduje kód jednodušší a méně náchylná k chybám.
* Zjistěte, jak dlouho má zůstat v sestavě není v pořádku podmínky v health store a jak je nutné vymazat. Na základě těchto informací rozhodnete sestavy Hodnota time to live a chování odebrat na vypršení platnosti.

Jak je uvedeno, vytváření sestav můžete provést z:

* Monitorované replika služby Service Fabric.
* Interní watchdogs nasazené jako služba Service Fabric (například Service Fabric bezstavové služba, která monitoruje podmínky a vydá sestavy). Může být watchdogs nasadit všechny uzly, nebo můžete spřažené s monitorované služby.
* Interní watchdogs, které běžet na uzlech Service Fabric, ale jsou *není* implementovaný jako služby Service Fabric.
* Externí watchdogs, které probe prostředků z *mimo* cluster Service Fabric (například monitorování služby jako Gomez).

> [!NOTE]
> Předinstalované cluster bude zahrnovat sestavy o stavu odeslané součástmi systému. Další informace v [stavu systému pomocí sestav pro řešení potíží s](service-fabric-understand-and-troubleshoot-with-system-health-reports.md). Uživatel sestavy, musí se poslat [entity stavu](service-fabric-health-introduction.md#health-entities-and-hierarchy) , již byly vytvořeny v systému.
> 
> 

Jednou stav vytváření sestav, je zřejmé, návrh, stav sestavy lze zaslat snadno. Můžete použít [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) do sestav stavu, pokud clusteru není [zabezpečené](service-fabric-cluster-security.md) nebo klienta fabric má oprávnění správce. Vytváření sestav můžete provést prostřednictvím rozhraní API pomocí pomocí [FabricClient.HealthManager.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth), pomocí prostředí PowerShell nebo prostřednictvím REST. Konfigurace knoflíky dávky sestavy pro zlepšení výkonu.

> [!NOTE]
> Sestava stavu je synchronní a představuje pouze pracovní ověření na straně klienta. Fakt, že sestava je přijat stavu klienta nebo `Partition` nebo `CodePackageActivationContext` objekty neznamená, že se použije v úložišti. Je odeslán asynchronně a které by mohly mít zpracovat v dávce s dalšími sestavami. Zpracování na serveru stále může selhat: pořadové číslo může být zastaralé, entita, na kterém se musí použít sestava byla odstraněna, atd.
> 
> 

## <a name="health-client"></a>Stav klienta
K úložišti stavu prostřednictvím stavu klienta, které je umístěn uvnitř klienta fabric se odesílají zprávy stavu. Stav klienta můžete nakonfigurovat následující nastavení:

* **HealthReportSendInterval**: zpoždění mezi časem sestavy se přidá do klienta a čas odeslání úložišti stavu. Používá k batch sestavy do jedné zprávy, místo odeslání jednu zprávu pro každé sestavy. Dávkování zvyšuje výkon. Výchozí hodnota: 30 sekund.
* **HealthReportRetrySendInterval**: interval, ve kterém stavu klient znovu odešle Akumulovaná stavu sestavy k úložišti stavu. Výchozí hodnota: 30 sekund.
* **HealthOperationTimeout**: časový limit pro sestavu zprávy odeslané do úložiště stavu. Pokud zprávu časového limitu, stavu klienta se opakuje, dokud úložiště zdravotní potvrdí, že sestava byla zpracována. Výchozí hodnota: dvě minuty.

> [!NOTE]
> Pokud jsou sestavy zpracovat v dávce, klienta fabric musí být udržovány zachování připojení pro alespoň HealthReportSendInterval zajistit, že jejich odesláním. Pokud zpráva dojde ke ztrátě nebo úložiště zdravotní nelze použít způsobovaly přechodné chyby, klienta fabric musí být zachováno již umožnit možnost opakujte.
> 
> 

Ukládání do vyrovnávací paměti, v klientovi trvá jedinečnosti sestavy v úvahu. Například pokud konkrétní chybný ohlašování vykazování 100 sestav za sekundu na stejnou vlastnost stejné entity, sestavy se nahradí na poslední verzi. Maximálně jeden takový sestavy existuje ve frontě klienta. Pokud je nakonfigurovaná dávkování, je počet zpráv odeslaných do úložiště stavu pouze jedním intervalu odeslání. Tato sestava je poslední přidané zprávu, která odráží aktuální stav entity.
Zadejte parametry konfigurace při `FabricClient` se vytvoří pomocí předání [FabricClientSettings](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclientsettings) s požadované hodnoty pro stav související položky.

Následující příklad vytvoří klienta fabric a určuje, že by měly být odeslány sestavy při přidání. V časových limitů a chyb, které můžete zkusit znovu opakování dojít každých 40 sekund.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

Doporučujeme zachovat klienta fabric výchozí nastavení, která nastavit `HealthReportSendInterval` na 30 sekund. Toto nastavení zajistí optimální výkon z důvodu dávkování. Pro kritické sestavy, které musí být nejdříve poslat, použijte `HealthReportSendOptions` s Immediate `true` v [FabricClient.HealthClient.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) rozhraní API. Okamžitou sestavy nepoužívat dávkování intervalu. Pomocí tohoto příznaku dát pozor; Chceme využít výhod stavu klienta dávkování kdykoli je to možné. Okamžité odeslání je také užitečné při zavírání klienta fabric (například proces bylo zjištěno neplatné stavu a je vypnout, aby se zabránilo vedlejší účinky). Zajišťuje best effort odesílání Akumulovaná sestav. Při přidání jednu sestavu s okamžitou příznakem, klient stavu dávek Akumulovaná sestavy od posledního odeslání.

Stejnými parametry lze zadat, pokud připojení ke clusteru je vytvořen pomocí prostředí PowerShell. Následující příklad spustí připojení k místnímu clusteru:

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

Podobně jako na rozhraní API, sestavy lze zaslat pomocí `-Immediate` přepínač tak, aby se poslala okamžitě, bez ohledu `HealthReportSendInterval` hodnotu.

Pro REST se odesílají zprávy k bráně Service Fabric, která má klientem interní prostředků infrastruktury. Ve výchozím nastavení, je tento klient nakonfigurovat na odesílání sestav zpracovat v dávce každých 30 sekund. Batch interval, můžete změnit nastavení konfigurace clusteru `HttpGatewayHealthReportSendInterval` na `HttpGateway`. Jak je uvedeno, je lepší volbou odesílat sestavy s `Immediate` hodnotu true. 

> [!NOTE]
> Aby se zajistilo, že neoprávněným služby nelze sestavy stavu u entity v clusteru, nakonfigurujte server tak, aby přijímal požadavky jenom z zabezpečené klientů. `FabricClient` Použité pro generování sestav, musí být schopni komunikovat s clusterem (například pomocí protokolu Kerberos nebo ověření certifikátu) povoleno zabezpečení. Další informace o [clusteru zabezpečení](service-fabric-cluster-security.md).
> 
> 

## <a name="report-from-within-low-privilege-services"></a>Sestava z v rámci služby nízkou úrovní oprávnění
Pokud služby Service Fabric nemají přístup správce ke clusteru, můžete sestavu stavu na entity z aktuálního kontextu prostřednictvím `Partition` nebo `CodePackageActivationContext`.

* Pro bezstavové služby použijte [IStatelessServicePartition.ReportInstanceHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatelessservicepartition.reportinstancehealth) chcete sestavu podle aktuální instanci služby.
* Pro stavové služby, použijte [IStatefulServicePartition.ReportReplicaHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition.reportreplicahealth) chcete sestavu podle aktuální repliky.
* Použití [IServicePartition.ReportPartitionHealth](https://docs.microsoft.com/dotnet/api/system.fabric.iservicepartition.reportpartitionhealth) chcete sestavu podle aktuální oddíl entity.
* Použití [CodePackageActivationContext.ReportApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportapplicationhealth) zprávu o aktuální aplikaci.
* Použití [CodePackageActivationContext.ReportDeployedApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedapplicationhealth) chcete sestavu podle aktuální aplikace nasazené na aktuálním uzlu.
* Použití [CodePackageActivationContext.ReportDeployedServicePackageHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedservicepackagehealth) hlásit balíček služby pro aplikace nasazené na aktuálním uzlu.

> [!NOTE]
> Interně `Partition` a `CodePackageActivationContext` uložení stavu klienta konfiguruje s výchozím nastavením. Jak je popsáno pro [stavu klienta](service-fabric-report-health.md#health-client), sestavy se dávkují a posílají časovače. Objekty by měly být udržovány příležitost k odeslání zprávy o zachování připojení.
> 
> 

Můžete zadat `HealthReportSendOptions` při odesílání sestavy prostřednictvím `Partition` a `CodePackageActivationContext` stavu rozhraní API. Pokud máte kritická sestavy, které musí být nejdříve poslat, použijte `HealthReportSendOptions` s Immediate `true`. Okamžitou sestavy nepoužívat dávkování interval interní stavu klienta. Jak je uvedeno nahoře, použijte tento příznak dát pozor; Chceme využít výhod stavu klienta dávkování kdykoli je to možné.

## <a name="design-health-reporting"></a>Návrh, vytváření sestav stavu
Prvním krokem při generování sestav vysoce kvalitní je identifikace podmínky, které může mít vliv na stav služby. Všechny podmínku, která může pomoci příznak problémy v clusteru nebo služby, pokud spustí--nebo i lepší, než se stane problém – můžete potenciálně uložit až miliardy dolarů. Mezi výhody patří menší výpadek, méně noci čas strávený příčin a opravu problémů a vyšší spokojenost zákazníků.

Jakmile podmínky jsou určeny, zapisovače sledovací zařízení musí zjistit nejlepší způsob, jak monitorovat je pro rovnováhu mezi režii a použitelnosti. Představte si třeba služba, která nemá komplexní výpočty, které používají některé dočasné soubory ve sdílené složce. Sledovací zařízení může sledovat sdílenou složku na Ujistěte se, že je k dispozici dostatek místa. Ho může čekat na oznámení změn v souboru nebo adresáře. Pokud je dosaženo předem prahovou hodnotu a zobrazovat chyby, pokud je úplná sdílenou složku ho může zprávu upozornění. Oprava systému na upozornění, může spustit čištění starší soubory ve sdílené složce. Při chybě může oprava systému přesunout repliky služby do jiného uzlu. Všimněte si, jak jsou popsané podmínky stavy z hlediska stavu: stav položky podmínku, která lze považovat za v pořádku (ok) nebo není v pořádku (upozornění nebo chyby).

Jakmile monitorování podrobnosti jsou nastaveny, zapisovač sledovací zařízení musí zjistit, jak implementovat sledovací zařízení. Pokud podmínky lze určit v rámci služby, sledovací zařízení můžou být součástí samotné monitorované služby. Například můžete kódu služby, zkontrolujte, jestli sdílenou složku a pak zprávy o každém pokusu o zápis do souboru. Výhodou tohoto přístupu je, že vytváření sestav je jednoduché. Musí být pozor na zabránit sledovací zařízení chyby funkcí služby, které mají vliv.

Generování sestav z v monitorované službě není vždy možnost. Sledovací zařízení v rámci služby nemusí být schopna zjistit podmínky. Něj nemusí být logiky nebo data tak, aby stanovení. Nároky na monitorování podmínek může být vysoká. Podmínky také nemusí být specifické pro služby, ale místo toho ovlivnit interakce mezi službami. Další možností je mít watchdogs v clusteru jako samostatné procesy. Watchdogs monitorování podmínek a sestavy, aniž by to ovlivnilo hlavní služby žádným způsobem. Například může být tyto watchdogs implementovaný jako bezstavové služby ve stejné aplikaci, nasazené na všech uzlech nebo stejným uzlům jako služba.

V některých případech sledovací zařízení spuštěných v clusteru není možnost buď. Pokud monitorované podmínky dostupnosti nebo funkce služby jako uživatele nevidíte, je nejlepší mít watchdogs na stejném místě jako klienti uživatele. Zde se testování operací stejným způsobem, jakým uživatelé volat. Například můžete mít sledovací zařízení, který je umístěn mimo cluster, vydá žádosti o službu a kontroluje latenci a správnost výsledku. (Pro službu kalkulačky, například 2 + 2 nevrátí 4 v přiměřené době?)

Jakmile udrželi podrobnosti sledovací zařízení byste měli rozhodnout na ID zdroje, který jednoznačně identifikuje. Pokud více watchdogs stejného typu žijí v clusteru, musí hlásit různých entit, nebo, pokud ohlásí na stejnou entitu, použijte jiný zdroj ID nebo vlastnost. Tímto způsobem jejich sestavy mohou existovat vedle sebe. Vlastnost sestava stavu by měl zaznamenat monitorovaných podmínku. (V příkladu výše, může být vlastnost **ShareSize**.) Pokud více sestav použít podmínku, vlastnost by měla obsahovat některé dynamické informace sestavy tak existovat vedle sebe. Například pokud více sdílených složek je potřeba monitorovat, název vlastnosti může být **ShareSize sharename**.

> [!NOTE]
> Proveďte *není* použití stavu úložiště pro zachování informací o stavu. Pouze informace týkající se stavu by měl hlášené jako stavu, protože tyto informace má dopad na vyhodnocení stavu entity. Úložiště stavu nebyl navržen jako úložiště pro obecné účely. Použije k agregaci všechna data do stavu logiku vyhodnocení stavu. Odeslání informací o stavu (jako je vytváření sestav stavu s stav OK), které nejsou neovlivní agregovaný stav v pořádku, ale může negativně ovlivnit výkon úložiště stavu.
> 
> 

Další rozhodnutí bod je na které entitě do sestavy. Většinu času, podmínku jasně idetifies entity. Vyberte entitu s rozlišením nejlepší možný. Pokud stav má dopad na všechny repliky v oddílu, zprávu o oddílu, nikoli na službu. Existují případy rohu, kde další myšlenku je potřeba, když. Pokud se podmínka má dopad na entity, jako je například repliku, ale přání je, aby podmínky příznakem déle než doba života repliky, a měly by být uvedeny v oddílu. V opačném při odstranění repliky úložiště zdravotní vyčistí její sestavy. Sledovací zařízení zapisovače musí vzít v úvahu životnosti entity a sestavy. Musí být zrušte, pokud by měla být vyčištěna sestavy z úložiště (například při chybě oznámené entitu již nepoužívá.).

Podívejme se na příklad, který převádí společně body, které I popsané. Vezměte v úvahu, že aplikace Service Fabric se skládá z hlavní stavové služby trvalé a sekundární bezstavové služby nasazeni na všechny uzly (jeden typ sekundární služby pro každý typ úlohy). Hlavní má zpracování fronty, který obsahuje příkazy být vykonán sekundární databáze. Sekundárních provést příchozí požadavky a odesílání signálů back potvrzení. Jednu podmínku, která může sledovat je délka fronty hlavní zpracování. Pokud délka fronty hlavní dosáhne prahové hodnoty, je uvedená upozornění. Upozornění označuje, že sekundární databáze nemůže zpracovat zatížení. Pokud fronty dosáhne maximální délku a příkazy jsou vyřadit, zobrazí se chybová zpráva, protože služby nelze obnovit. Sestavy mohou být na vlastnost **QueueStatus**. Sledovací zařízení je umístěn uvnitř službu a se pravidelně odesílají na hlavní primární repliky. TTL se dvě minuty a odesláním pravidelně každých 30 sekund. Pokud primární přestane fungovat, sestavy se automaticky vyčistí z úložiště. Pokud je replika služby nahoru, ale je zablokována nebo jiné problémy s sestavy vyprší v health store. V takovém případě je entita vyhodnocovány v chybě.

Jiná podmínka, která se dá sledovat je čas spuštění úlohy. Hlavní distribuuje úkoly do sekundární databáze, v závislosti na typu úloh. V závislosti na návrh může dotazovat hlavní sekundární databáze pro stav úlohy. Může také počkejte sekundární databáze k odesílání signálů back potvrzení, pokud se provádí. V druhém případě se musí dát pozor ke zjištění situacích, kdy jsou ztraceny die sekundární repliky nebo zprávy. Jednou z možností je pro hlavní server k odeslání žádosti příkazu ping na stejnou sekundární, který odesílá zpět jeho stav. Pokud byl přijat neexistuje stav, který je hlavní server bude považovat za selhání a provádí změny plánu úlohy. Toto chování se předpokládá, že úkoly se idempotent.

Monitorované podmínky lze přeložit jako varování, pokud úloha neprobíhá v určitém čase (**t1**, například 10 minut). Pokud úloha není dokončena v čase (**t2**, například 20 minut), monitorovaných podmínky lze přeložit jako chyba. Vytváření těchto sestav lze provést několika způsoby:

* Hlavní primární repliky hlásí pravidelně sám na sobě. Může mít jednu vlastnost pro všechny úlohy čekající na vyřízení ve frontě. Pokud alespoň jedna úloha trvá déle, stav sestavy na vlastnost **PendingTasks** je upozornění nebo chyby, podle potřeby. Pokud neexistují žádné čekající úlohy nebo všech úloh spustil provádění, je její stav v pořádku. Úkoly jsou trvalé. Pokud primární přestane fungovat, můžete nadále nově propagovaných primární sestav správně.
* Jiný proces sledovací zařízení (v cloudu nebo externí) kontroluje úlohy (z mimo, na základě jejího výsledku) Chcete-li zobrazit, pokud jejich dokončení. Pokud nerespektují prahové hodnoty, sestavy se odesílají na hlavní služby. Sestavy se také odesílají na každý úkol, který obsahuje identifikátor úlohy, jako je třeba **PendingTask + taskId**. Sestavy by měly být odeslány pouze na stavy není v pořádku. Nastavte hodnotu doby za provozu na několik minut a označit sestav se odebere, když vypršení jejich platnosti zajistit čištění.
* Sekundární, který spouští úlohu nahlásí, když trvá déle, než se očekávalo ji spustit. Oznámí na instanci služby pro vlastnost **PendingTasks**. Sestava zjišťuje instance služby, která má potíže, ale neukládá situaci, kdy kterou instanci. Sestavy jsou pak vyčistit. Ho může zprávu o sekundární službu. Pokud sekundární dokončí úlohu, vymaže sekundární instance sestavy z úložiště. Sestavu neukládá situaci, kdy dojde ke ztrátě zpráv potvrzení a úloha není dokončena z hlediska předlohy.

Ale generování sestav se provádí v případech popsané výše, sestavy při vyhodnocování stavu zaznamenání ve stavu aplikace.

## <a name="report-periodically-vs-on-transition"></a>Sestava pravidelně oproti na přechod
Pomocí model vykazování stavu watchdogs sestavy můžete odesílat pravidelně nebo na přechody. Doporučený způsob pro vytváření sestav sledovací zařízení je pravidelně, protože kód je mnohem jednodušší a méně náchylná k chybám. Watchdogs musí zajistit, být co nejjednodušší, aby se zabránilo chyby, které aktivují nesprávné sestavy. Nesprávný *není v pořádku* sestavy vliv vyhodnocení stavu a scénáře podle stavu, včetně upgrady. Nesprávný *pořádku* sestavy skrýt problémy v clusteru, což není žádoucí.

Pro pravidelné vytváření sestav, se dá implementovat sledovací zařízení s časovačem. Na zpětné volání časovače můžete sledovací zařízení zkontrolovat stav a odesílat sestavy na základě aktuálního stavu. Není nutné naleznete, která sestava byla odeslána dříve nebo zkontrolujte všechny optimalizace z hlediska zasílání zpráv. Dávkování logiku usnadní výkonu má klient stavu. Při stavu klienta je udržováno zachování připojení, se pokusí interně dokud sestavy je potvrzena úložiště zdravotní nebo sledovací zařízení vytváří novější sestavu s stejné entity, vlastností a zdroj.

Vytváření sestav na přechody vyžaduje pečlivě zpracování stavu. Sledovací zařízení monitoruje některé podmínky a sestavy pouze, když změnit podmínky. Vzhůru tohoto přístupu je, že jsou potřeba méně sestavy. Nevýhodou je komplexní logiku sledovací zařízení. Sledovací zařízení musí zachovat podmínky nebo sestavy, tak, aby mohly být zkontrolovány k určení změny stavu. Na převzetí služeb při selhání se musí dát pozor se sestavami, přidat, ale dosud nebyl odeslán do úložiště stavu. Pořadové číslo musí být stále rostoucí. Pokud ne, sestavy jsou odmítnuty jako zastaralé. Ve výjimečných případech, kde je při ztrátě dat může být potřeba synchronizace mezi stav zpravodaje a stav úložiště stavu.

Zprávy o přechody smysl pro služby vytváření sestav na sami, prostřednictvím `Partition` nebo `CodePackageActivationContext`. Při místní objekt (repliky nebo nasazený balíček služby / nasazené aplikace) je odebrána, všechny její sestavy jsou také odebrány. Toto automatické čištění zmírní potřebu synchronizace mezi ohlašování a health store. Pokud sestava je pro nadřazený oddíl nebo nadřazená aplikace, musí dát pozor na převzetí služeb při selhání předejdete zastaralé sestavy v health store. Logika musí být přidaný do udržovat správný stav a zrušte výběr sestavy z úložiště, když už není potřeba.

## <a name="implement-health-reporting"></a>Implementovat, vytváření sestav stavu
Jakmile podrobnosti entitu a sestavy jsou jasné, odesílání sestav stavu lze provést prostřednictvím rozhraní API, Powershellu nebo REST.

### <a name="api"></a>Rozhraní API
Chcete-li sestavy prostřednictvím rozhraní API, vytvoření sestavy stavu specifické pro typ entity, které se chcete v sestavě. Udělte sestavy stavu klienta. Alternativně vytvořte informace o stavu a předejte jej opravit reporting metody na `Partition` nebo `CodePackageActivationContext` chcete sestavu podle aktuální entity.

Následující příklad ukazuje pravidelné generování sestav z sledovací zařízení v rámci clusteru. Sledovací zařízení kontroluje, zda externí prostředek je přístupná z v rámci uzlu. Prostředek je potřeba služba manifestu v aplikaci. Pokud prostředek není k dispozici, jiných služeb v aplikaci můžete i nadále fungovat správně. Proto sestavy se odesílají na entity balíček nasazené služby každých 30 sekund.

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
Odesílat zprávy o stavu s **odeslání ServiceFabric*EntityType*HealthReport**.

Následující příklad ukazuje pravidelné vytváření sestav na hodnoty využití procesoru na uzlu. Sestavy by měly být odeslány každých 30 sekund a mají hodnota time to live dvě minuty. Pokud vypršení jejich platnosti, zpravodaje, která má problémy, takže uzlu je vyhodnocován v chybě. Když procesoru je nad prahovou hodnotou, sestava má stav varování. Pokud procesor zůstává nad prahovou hodnotu pro více než nakonfigurovaném čase, uvede se jako chyba. Zpravodaje, jinak hodnota odešle stav OK.

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

Následující příklad hlásí přechodný upozornění v replice. Získá první ID oddílu a pak ID repliky pro službu, kterou je zajímá. Pak odešle zprávu z **PowershellWatcher** na vlastnost **ResourceDependency**. Sestava je určen pouze dvě minuty, a automaticky odebrán z úložiště.

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
Odesílat zprávy o stavu REST pomocí požadavků POST, které mají v těle popis sestavy stavu a přejděte na požadovanou entitu. Například zjistit, jak odeslat REST [clusteru sestav stavu](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-cluster) nebo [sestav stavu služby](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service). Jsou podporovány všechny entity.

## <a name="next-steps"></a>Další postup
Na základě stavu dat, služba zapisovače a Správce clusteru nebo aplikace si můžete představit způsoby, jak využívat informace. Například se můžete k zachycení závažné problémy před jejich vyvolat výpadků nastavit výstrahy na základě stavu. Správci můžete také nastavit systémy opravy Chcete automaticky opravit problémy.

[Úvod do Service Fabric stavu monitorování](service-fabric-health-introduction.md)

[Zobrazit sestavy stavu Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Postup vytvoření sestavy a zkontrolujte stav služby](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Pomocí sestav o stavu systému pro řešení potíží](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Monitorování a Diagnostika služby místně](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Upgrade aplikace Service Fabric](service-fabric-application-upgrade.md)

