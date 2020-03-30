---
title: Přidání vlastních sestav stavu service fabric
description: Popisuje, jak odesílat vlastní sestavy stavu do entit stavu Azure Service Fabric. Poskytuje doporučení pro navrhování a implementaci zpráv o stavu kvality.
author: oanapl
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: d00f740085b15bdb5fe698a069d97f168507f31f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451590"
---
# <a name="add-custom-service-fabric-health-reports"></a>Přidání vlastních sestav stavu service fabric
Azure Service Fabric zavádí [model stavu](service-fabric-health-introduction.md) navržený příznakem není v pořádku clusteru a podmínky použití na konkrétní entity. Model stavu používá **zdravotní reportéři** (součásti systému a watchdogs). Cílem je snadná a rychlá diagnostika a oprava. Service spisovatelé musí přemýšlet předem o zdraví. Všechny podmínky, které mohou mít vliv na zdraví by měly být hlášeny na, zejména v případě, že může pomoci příznak problémy v blízkosti kořene. Informace o stavu může ušetřit čas a úsilí na ladění a vyšetřování. Užitečnost je obzvláště jasná, jakmile je služba spuštěna ve velkém měřítku v cloudu (privátní nebo Azure).

Reportéři Service Fabric monitorují zjištěné podmínky zájmu. Podávají o těchto podmínkách zprávy na základě svého místního pohledu. [Úložiště stavu](service-fabric-health-introduction.md#health-store) agreguje data stavu odeslaná všemi reportéry k určení, zda entity jsou globálně v pořádku. Model je určen k bohatému, flexibilnímu a snadno použitelnému. Kvalita zpráv o stavu určuje přesnost zobrazení stavu clusteru. Falešně pozitivní výsledky, které nesprávně zobrazit problémy není v pořádku může negativně ovlivnit upgrady nebo jiné služby, které používají data stavu. Příkladem takových služeb jsou opravárenské služby a upozorňující mechanismy. Proto je zapotřebí některé myšlenky, aby zprávy, které zachycují podmínky zájmu v nejlepším možným způsobem.

Chcete-li navrhnout a implementovat zprávy o stavu, musí sledovací zařízení a systémové součásti:

* Definujte podmínku, která je zajímá, způsob, jakým je sledována, a dopad na funkčnost clusteru nebo aplikace. Na základě těchto informací rozhodněte o vlastnostech a stavu sestavy stavu.
* Určete [entitu,](service-fabric-health-introduction.md#health-entities-and-hierarchy) na kterou se sestava vztahuje.
* Určete, kde se provádí vytváření sestav, z v rámci služby nebo z interní nebo externí sledovací ho dohledu.
* Definujte zdroj použitý k identifikaci reportéra.
* Zvolte strategii vytváření sestav, a to buď pravidelně, nebo při přechodech. Doporučený způsob je pravidelně, protože vyžaduje jednodušší kód a je méně náchylný k chybám.
* Určete, jak dlouho by měla sestava pro nefunkční podmínky zůstat v úložišti stavu a jak by měla být vymazána. Pomocí těchto informací rozhodněte, jak se sestava bude žít a bude se řídit chováním při vypršení platnosti.

Jak již bylo zmíněno, podávání zpráv lze provést z:

* Replika služby Service Fabric monitorované služby.
* Interní watchdogs nasazené jako služba Service Fabric (například služba Service Fabric bezstavové, která monitoruje podmínky a problémy sestavy). Watchdogs lze nasadit všechny uzly nebo může být spřažené do monitorované služby.
* Interní watchdogs, které běží na uzly Service Fabric, ale *nejsou* implementovány jako služby Service Fabric.
* Externí watchdogs, které sondují prostředek *mimo* cluster Service Fabric (například monitorovací služby jako Gomez).

> [!NOTE]
> Po vybalení je cluster naplněn zprávami o stavu odeslanými součástmi systému. Další informace naleznete v [informacích O používání zpráv o stavu systému pro řešení potíží](service-fabric-understand-and-troubleshoot-with-system-health-reports.md). Uživatelské sestavy musí být odeslány na [entity stavu,](service-fabric-health-introduction.md#health-entities-and-hierarchy) které již byly vytvořeny systémem.
> 
> 

Jakmile je návrh zprávy o stavu jasný, lze snadno odeslat zprávy o stavu. [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) můžete použít k hlášení stavu, pokud cluster není [zabezpečený](service-fabric-cluster-security.md) nebo pokud má klient prostředků infrastruktury oprávnění správce. Vytváření sestav lze provést prostřednictvím rozhraní API pomocí [FabricClient.HealthManager.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth), prostřednictvím prostředí PowerShell nebo prostřednictvím REST. Konfigurační knoflíky dávkové sestavy pro lepší výkon.

> [!NOTE]
> Stav sestavy je synchronní a představuje pouze ověřovací práci na straně klienta. Skutečnost, že sestava je přijatklienta `Partition` stavu `CodePackageActivationContext` nebo nebo objekty neznamená, že je použita v úložišti. Je odeslána asynchronně a případně dávkově s jinými sestavami. Zpracování na serveru může stále selhat: pořadové číslo může být zastaralé, entita, na které musí být sestava použita, byla odstraněna atd.
> 
> 

## <a name="health-client"></a>Klient stavu
Sestavy stavu jsou odesílány správce stavu prostřednictvím klienta stavu, který žije uvnitř klienta prostředků infrastruktury. Správce stavu ukládá sestavy v úložišti stavu. Klienta stavu lze nakonfigurovat s následujícím nastavením:

* **HealthReportSendInterval**: Zpoždění mezi časem, kdy je sestava přidána klientovi, a časem, kdy je odeslána správci stavu. Používá se k dávkové sestavy do jedné zprávy, nikoli odesílání jedné zprávy pro každou sestavu. Dávkování zlepšuje výkon. Výchozí: 30 sekund.
* **HealthReportRetrySendInterval**: Interval, ve kterém klient stavu znovu odešle akumulované sestavy stavu správci stavu. Výchozí: 30 sekund, minimálně: 1 sekunda.
* **HealthOperationTimeout**: Časový čas pro zprávu sestavy odeslané správci stavu. Pokud zpráva out out, klient stavu opakuje, dokud správce stavu potvrdí, že sestava byla zpracována. Výchozí: dvě minuty.

> [!NOTE]
> Při dávkové sestavy, musí být klient prostředků infrastruktury udržovány naživu alespoň HealthReportSendInterval zajistit, že jsou odeslány. Pokud dojde ke ztrátě zprávy nebo správce stavu je nemůže použít z důvodu přechodných chyb, musí být klient prostředků infrastruktury déle udržován při životě, aby měl možnost to zkusit znovu.
> 
> 

Ukládání do vyrovnávací paměti na klienta bere v úvahu jedinečnost sestav. Například pokud konkrétní chybný reportér hlásí 100 sestav za sekundu na stejnou vlastnost stejné entity, sestavy jsou nahrazeny poslední verze. Ve frontě klienta existuje maximálně jedna taková sestava. Pokud je nakonfigurováno dávkování, počet sestav odeslaných správci stavu je pouze jeden interval pro odesílání. Tato sestava je poslední přidanou sestavou, která odráží nejaktuálnější stav entity.
Zadejte parametry `FabricClient` konfigurace při vytvoření předáním [FabricClientSettings](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclientsettings) s požadovanými hodnotami pro položky související se stavem.

Následující příklad vytvoří klienta infrastruktury a určuje, že sestavy by měly být odeslány při jejich přidání. Na časové oty a chyby, které lze opakovat, opakování dochází každých 40 sekund.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

Doporučujeme zachovat výchozí nastavení klienta `HealthReportSendInterval` prostředků infrastruktury, které je nastaveno na 30 sekund. Toto nastavení zajišťuje optimální výkon díky dávkování. Pro kritické sestavy, které musí být `HealthReportSendOptions` odeslány `true` co nejdříve, použijte s Immediate v [FabricClient.HealthClient.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) API. Okamžité sestavy obcházejí interval dávkování. Používejte tuto vlajku opatrně; chceme využít dávkování klienta stavu, kdykoli je to možné. Okamžité odeslání je také užitečné, když se klient infrastruktury zavírá (například proces určil neplatný stav a je třeba jej vypnout, aby se zabránilo vedlejším účinkům). Zajišťuje nejlepší úsilí odeslání nahromaděných zpráv. Když je jedna sestava přidána s příznakem Okamžité, klient stavu dávky všechny akumulované sestavy od posledního odeslání.

Stejné parametry lze zadat při vytvoření připojení ke clusteru prostřednictvím prostředí PowerShell. Následující příklad spustí připojení k místnímu clusteru:

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

Podobně jako rozhraní API, sestavy `-Immediate` lze odesílat pomocí přepínače, které mají být odeslány okamžitě, bez ohledu na hodnotu. `HealthReportSendInterval`

Pro REST sestavy jsou odesílány do brány Service Fabric, který má interní infrastruktury klienta. Ve výchozím nastavení je tento klient nakonfigurován tak, aby odesílá sestavy dávkově každých 30 sekund. Dávkový interval můžete změnit pomocí `HttpGatewayHealthReportSendInterval` nastavení `HttpGateway`konfigurace clusteru na . Jak již bylo zmíněno, lepší možností `Immediate` je posílat zprávy s true. 

> [!NOTE]
> Chcete-li zajistit, aby neautorizované služby nemohly vykazovat stav proti entitám v clusteru, nakonfigurujte server tak, aby přijímal požadavky pouze od zabezpečených klientů. Použití `FabricClient` pro vytváření sestav musí mít povoleno zabezpečení, aby bylo možné komunikovat s clusterem (například s protokolem Kerberos nebo ověřováním certifikátů). Přečtěte si další informace o [zabezpečení clusteru](service-fabric-cluster-security.md).
> 
> 

## <a name="report-from-within-low-privilege-services"></a>Sestavy ze služeb s nízkými oprávněními
Pokud služby Service Fabric nemají přístup správce ke clusteru, můžete vykazovat `Partition` stav `CodePackageActivationContext`entit z aktuálního kontextu až nebo .

* Pro bezstavové služby použijte [IStatelessServicePartition.ReportInstanceHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatelessservicepartition.reportinstancehealth) k hlášení aktuální instance služby.
* Pro stavové služby použijte [iStatefulServicePartition.ReportReplicaHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition.reportreplicahealth) k sestavování aktuální repliky.
* Pomocí [stavu IServicePartition.ReportPartitionHealth](https://docs.microsoft.com/dotnet/api/system.fabric.iservicepartition.reportpartitionhealth) můžete vykazovat aktuální entitu oddílu.
* Pomocí [codePackageActivationContext.ReportApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportapplicationhealth) můžete vykazovat aktuální aplikaci.
* Pomocí [codePackageActivationContext.ReportDeployedApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedapplicationhealth) můžete vykazovat aktuální aplikaci nasazenou v aktuálním uzlu.
* Pomocí [codePackageActivationContext.ReportDeployedServicePackageHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedservicepackagehealth) můžete vykazovat na balíček služby pro aplikaci nasazenou v aktuálním uzlu.

> [!NOTE]
> Interně `Partition` a `CodePackageActivationContext` hold klienta stavu nakonfigurován s výchozím nastavením. Jak je vysvětleno pro [klienta stavu](service-fabric-report-health.md#health-client), sestavy jsou dávkově a odesílány na časovač. Objekty by měly zůstat naživu, aby měly šanci odeslat zprávu.
> 
> 

Můžete určit `HealthReportSendOptions` při odesílání `Partition` `CodePackageActivationContext` sestav prostřednictvím a stav api. Pokud máte kritické sestavy, které musí být `HealthReportSendOptions` odeslány `true`co nejdříve, použijte s okamžitou . Okamžité sestavy obcházejí interval dávkování interního klienta stavu. Jak již bylo zmíněno dříve, používat tuto vlajku opatrně; chceme využít dávkování klienta stavu, kdykoli je to možné.

## <a name="design-health-reporting"></a>Návrh sestavy stavu
Prvním krokem při generování vysoce kvalitních sestav je identifikace podmínek, které mohou mít vliv na stav služby. Jakýkoli stav, který může pomoci označit problémy ve službě nebo clusteru při spuštění - nebo ještě lépe, než dojde k problému - může potenciálně ušetřit miliardy dolarů. Mezi výhody patří méně prostojů, méně nočních hodin strávených vyšetřováním a opravou problémů a vyšší spokojenost zákazníků.

Jakmile jsou identifikovány podmínky, watchdog spisovatelé musí přijít na to, nejlepší způsob, jak sledovat je pro rovnováhu mezi režií a užitečnosti. Zvažte například službu, která provádí složité výpočty, které používají některé dočasné soubory ve sdílené složce. Hlídací pes může sledovat sdílenou složku, aby bylo zajištěno, že je k dispozici dostatek místa. Mohlo by naslouchat oznámení o souboru nebo změny adresáře. Může nahlásit upozornění, pokud je dosaženo počáteční prahové hodnoty, a ohlásit chybu, pokud je sdílená složky plná. Na upozornění může systém opravy spustit čištění starších souborů ve sdílené složce. Při chybě může systém oprav přesunout repliku služby do jiného uzlu. Všimněte si, jak jsou stavy popsány z hlediska stavu: stav stavu, který lze považovat za v pořádku (ok) nebo není v pořádku (upozornění nebo chyba).

Jakmile jsou nastaveny podrobnosti monitorování, zapisovač watchdog musí zjistit, jak implementovat watchdog. Pokud podmínky lze určit v rámci služby, může být hlídací pes součástí samotné sledované služby. Kód služby může například zkontrolovat využití sdílené složky a potom sestavy pokaždé, když se pokusí zapsat soubor. Výhodou tohoto přístupu je, že vytváření sestav je jednoduché. Je třeba dbát na to, aby chyby sledovacího zařízení neměly vliv na funkce služby.

Reporting z v rámci monitorované služby není vždy možnost. Sledovací zařízení v rámci služby nemusí být schopen zjistit podmínky. Nemusí mít logiku nebo data pro určení. Režie sledování podmínek může být vysoká. Podmínky také nemusí být specifické pro službu, ale místo toho ovlivňují interakce mezi službami. Další možností je mít watchdogs v clusteru jako samostatné procesy. Hlídací psi sledují podmínky a zprávy, aniž by jakkoli ovlivnili hlavní služby. Například tyto watchdogs může být implementována jako bezstavové služby ve stejné aplikaci, nasazené na všech uzlech nebo na stejných uzlech jako služba.

V některých proto není možnost sledovacího zařízení spuštěného v clusteru. Pokud je monitorovaná podmínka dostupnost nebo funkce služby, jak ji vidí uživatelé, je nejlepší mít watchdogs na stejném místě jako klienti uživatelů. Tam mohou testovat operace stejným způsobem, jakým je uživatelé nazývají. Můžete mít například sledovací ho dohledu, který žije mimo cluster, vydává požadavky na službu a kontroluje latenci a správnost výsledku. (Pro službu kalkulačky, například, vrátí 2 +2 4 v přiměřeném čase?)

Po dokončení podrobností o sledovacím zařízení byste se měli rozhodnout pro ID zdroje, které jej jednoznačně identifikuje. Pokud více watchdogs stejného typu žijí v clusteru, musí sestavy na různé entity, nebo pokud sestavy na stejné entity, použijte jiné ID zdroje nebo vlastnost. Tímto způsobem mohou jejich zprávy koexistovat. Vlastnost sestavy stavu by měla zachytit sledovaný stav. (Pro výše uvedený příklad může být vlastnost **ShareSize**.) Pokud více sestav platí pro stejnou podmínku, vlastnost by měla obsahovat některé dynamické informace, které umožňuje sestavy koexistovat. Pokud například potřebujete sledovat více sdílených složek, může být název vlastnosti **ShareSize-sharename**.

> [!NOTE]
> *Nepoužívejte* úložiště stavu k uchovávat informace o stavu. Jako zdravotní informace by měly být vykazovány pouze informace týkající se zdraví, protože tyto informace mají vliv na hodnocení stavu subjektu. Úložiště stavu nebyl navržen jako úložiště pro obecné účely. Používá logiku hodnocení stavu agregovat všechna data do stavu. Odesílání informací nesouvisejících se stavem (například hlášení stavu se stavem OK) nemá vliv na agregovaný stav, ale může negativně ovlivnit výkon úložiště stavu.
> 
> 

Dalším bodem rozhodnutí je, o které entitě se má podat zprávu. Ve většině času podmínka jasně identifikuje entitu. Vyberte entitu s nejlepší možnou rozlišovací schopnost. Pokud podmínka má vliv na všechny repliky v oddílu, sestavy na oddíl, nikoli na službu. Existují rohové případy, kde je potřeba více myšlení. Pokud podmínka má vliv na entitu, například repliku, ale touha je mít podmínku označenou pro více než dobu životnosti repliky, pak by měla být uvedena na oddílu. V opačném případě při odstranění repliky úložiště stavu vyčistí všechny své sestavy. Autoři watchdogu musí přemýšlet o životnosti entity a sestavy. Musí být jasné, kdy by měla být sestava vyčištěna z úložiště (například když se již nepoužije chyba hlášená na entitu).

Podívejme se na příklad, který dává dohromady body, které jsem popsal. Zvažte aplikaci Service Fabric složenou z hlavní stavové trvalé služby a sekundárních bezstavových služeb nasazených ve všech uzlech (jeden typ sekundární služby pro každý typ úkolu). Hlavní server má frontu zpracování, která obsahuje příkazy, které mají být provedeny sekundárními servery. Sekundární provádějí příchozí požadavky a odesílají zpět signály potvrzení. Jednou z podmínek, které by mohly být sledovány, je délka fronty hlavního zpracování. Pokud délka hlavní fronty dosáhne prahové hodnoty, je hlášeno upozornění. Upozornění označuje, že sekundární nemohou zpracovat zatížení. Pokud fronta dosáhne maximální délky a příkazy jsou vynechány, je hlášena chyba, protože služba nemůže obnovit. Sestavy mohou být na **vlastnosti QueueStatus**. Watchdog žije uvnitř služby a je pravidelně odesílán na hlavní primární repliku. Čas života je dvě minuty a je odesílán pravidelně každých 30 sekund. Pokud primární přejde dolů, sestava je automaticky vyčištěna z úložiště. Pokud je replika služby nahoře, ale je zablokována nebo má jiné problémy, vyprší platnost sestavy v úložišti stavu. V tomto případě je entita vyhodnocena jako chyba.

Další podmínkou, kterou lze sledovat, je doba provádění úloh. Předloha distribuuje úkoly do sekundárních zařízení na základě typu úkolu. V závislosti na návrhu může hlavní server dotazovat sekundární údaje pro stav úkolu. To by mohlo také čekat na sekundární poslat zpět potvrzení signály, když jsou hotové. Ve druhém případě je třeba dbát na zjištění situací, kdy sekundární zemře nebo zprávy jsou ztraceny. Jednou z možností je pro předlohu odeslat požadavek ping na stejné sekundární, který odešle zpět svůj stav. Pokud není přijat žádný stav, hlavní server jej považuje za neúspěšný a přeplánuje úkol. Toto chování předpokládá, že úkoly jsou idempotentní.

Sledovaný stav lze přeložit jako upozornění, pokud úkol není proveden v určitém čase (**t1**, například 10 minut). Pokud úkol není dokončen včas (**t2**, například 20 minut), lze monitorovnou podmínku přeložit jako Chybu. Toto vykazování lze provést několika způsoby:

* Hlavní primární replika se pravidelně hlásí sama o sobě. Můžete mít jednu vlastnost pro všechny čekající úkoly ve frontě. Pokud alespoň jeden úkol trvá déle, stav sestavy na vlastnost **PendingTasks** je upozornění nebo chyba, podle potřeby. Pokud neexistují žádné čekající úkoly nebo všechny úkoly spuštěné provádění, stav sestavy je v pořádku. Úkoly jsou trvalé. Pokud primární přejde dolů, nově povýšené primární může i nadále hlásit správně.
* Jiný proces sledovacího zařízení (v cloudu nebo externí) zkontroluje úkoly (zvenčí, na základě požadovaného výsledku úkolu) a zjistí, zda jsou dokončeny. Pokud nedodržují prahové hodnoty, je odeslána zpráva o hlavní službě. Sestava je také odeslána na každý úkol, který obsahuje identifikátor úkolu, jako **pendingTask +taskId**. Sestavy by měly být odesílány pouze v stavech není v pořádku. Nastavte čas na několik minut a označte sestavy, které mají být odebrány, když vyprší jejich platnost, aby bylo zajištěno vyčištění.
* Sekundární, který je provádění úlohy sestavy, pokud trvá déle, než bylo očekáváno spustit. Hlásí instanci služby ve vlastnosti **PendingTasks**. Sestava určuje instanci služby, která má problémy, ale nezachycuje situaci, kdy instance zemře. Zprávy jsou tedy vyčištěny. Mohla by podat zprávu o sekundární službě. Pokud sekundární dokončí úlohu, sekundární instance vymaže sestavu z úložiště. Sestava nezachycuje situaci, kdy dojde ke ztrátě potvrzení a úkol není dokončen z hlediska hlavního serveru.

Však hlášení se provádí v případech popsaných výše, sestavy jsou zachyceny ve stavu aplikace při vyhodnocení stavu.

## <a name="report-periodically-vs-on-transition"></a>Pravidelně nahlašovat vs. o přechodu
Pomocí modelu vykazování stavu watchdogs můžete odesílat sestavy pravidelně nebo na přechody. Doporučený způsob vytváření sestav watchdog je pravidelně, protože kód je mnohem jednodušší a méně náchylné k chybám. Watchdogs musí usilovat o to, aby byly co nejjednodušší, aby se zabránilo chybám, které spouštějí nesprávné sestavy. Nesprávné *sestavy není v pořádku* vliv hodnocení stavu a scénáře založené na stavu, včetně upgradů. Nesprávné *sestavy v pořádku* skrýt problémy v clusteru, což není žádoucí.

Pro pravidelné vykazování watchdog lze implementovat s časovačem. Při zpětném volání časovače může hlídací pes zkontrolovat stav a odeslat sestavu na základě aktuálního stavu. Není třeba vidět, která sestava byla odeslána dříve, ani provádět optimalizace z hlediska zasílání zpráv. Klient stavu má dávkování logiku pomoci s výkonem. Zatímco klient stavu je udržována naživu, opakuje interně, dokud je sestava potvrzena úložiště stavu nebo watchdog generuje novější sestavu se stejnou entitu, vlastnost a zdroj.

Vykazování přechodů vyžaduje pečlivé zpracování stavu. Hlídací pes sleduje některé podmínky a hlásí pouze v případě, že se podmínky změní. Výhodou tohoto přístupu je, že je zapotřebí méně zpráv. Nevýhodou je, že logika hlídacího psa je složitá. Watchdog musí udržovat podmínky nebo sestavy, aby mohly být zkontrolovány k určení změn stavu. Při převzetí služeb při selhání je třeba dbát na to, aby byly přidány sestavy, ale ještě nebyly odeslány do úložiště stavu. Pořadové číslo musí být stále rostoucí. Pokud ne, zprávy jsou odmítnuty jako zastaralé. Ve výjimečných případech, kdy dojde ke ztrátě dat, může být nutná synchronizace mezi stavem reportéra a stavem úložiště stavu.

Podávání zpráv o přechodech má smysl `Partition` pro `CodePackageActivationContext`služby, které podávají zprávy o sobě, prostřednictvím nebo . Při odebrání místního objektu (repliky nebo nasazeného balíčku služby / nasazené aplikace) jsou odebrány také všechny jeho sestavy. Tento automatický vyčištění uvolňuje potřebu synchronizace mezi reportérem a úložištěm zdraví. Pokud je sestava pro nadřazený oddíl nebo nadřazenou aplikaci, je třeba dbát na převzetí služeb při selhání, aby se zabránilo zastaralé sestavy v úložišti stavu. Logika musí být přidána k udržení správného stavu a vymazat sestavu z úložiště, když už není potřeba.

## <a name="implement-health-reporting"></a>Implementace vykazování stavu
Jakmile jsou podrobnosti o entitě a sestavě jasné, odesílání zpráv o stavu lze provést prostřednictvím rozhraní API, Prostředí PowerShell nebo REST.

### <a name="api"></a>rozhraní API
Chcete-li sestavy prostřednictvím rozhraní API, musíte vytvořit sestavu stavu specifickou pro typ entity, které chcete sestavy. Předaj te sestavu klientovi stavu. Případně vytvořte informace o stavu a předajte je správné metody vykazování na `Partition` nebo `CodePackageActivationContext` sestavy o aktuální ch entit.

Následující příklad ukazuje periodické vykazování z hlídacího psa v rámci clusteru. Watchdog zkontroluje, zda externí prostředek lze přistupovat z v rámci uzlu. Prostředek je potřeba manifestu služby v rámci aplikace. Pokud prostředek není k dispozici, ostatní služby v rámci aplikace stále fungovat správně. Proto je sestava odeslána na entitu balíčku nasazených služeb každých 30 sekund.

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
Odeslat zprávy o stavu pomocí **send-ServiceFabric*EntityType*HealthReport**.

Následující příklad ukazuje periodické vykazování hodnot procesoru v uzlu. Zprávy by měly být zasílány každých 30 sekund a mají čas žít dvě minuty. Pokud vyprší jejich platnost, má reportér problémy, takže uzel je vyhodnocen a chyba. Pokud je procesor nad prahovou hodnotou, sestava má stav upozornění. Když procesor zůstane nad prahovou hodnotou pro více než nakonfigurovaný čas, je hlášena jako chyba. V opačném případě reportér odešle stav OK.

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

Následující příklad hlásí přechodné upozornění na repliku. Nejprve získá ID oddílu a potom ID repliky pro službu, která má zájem. Potom odešle zprávu z **PowershellWatcher** na vlastnost **ResourceDependency**. Sestava je zajímavá pouze dvě minuty a je automaticky odebrána z úložiště.

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
Odeslat sestavy stavu pomocí REST s požadavky POST, které přejdou na požadovanou entitu a mají v těle popis sestavy stavu. Přečtěte si například, jak odesílat [zprávy o stavu clusteru](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-cluster) REST nebo [sestavy stavu služby](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service). Podporovány jsou všechny entity.

## <a name="next-steps"></a>Další kroky
Na základě dat o stavu mohou autoři služeb a správci clusteru/aplikací vymyslet způsoby, jak tyto informace využívat. Mohou například nastavit výstrahy na základě stavu, aby zachytily závažné problémy dříve, než vyvolají výpadky. Správci mohou také nastavit systémy oprav k automatickému řešení problémů.

[Úvod do monitorování stavu service fabric](service-fabric-health-introduction.md)

[Zobrazit sestavy stavu service fabric](service-fabric-view-entities-aggregated-health.md)

[Jak nahlásit a zkontrolovat stav služby](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Použití sestav stavu systému pro řešení potíží](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Místní monitorování a diagnostika služeb](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Upgrade aplikace Service Fabric](service-fabric-application-upgrade.md)

