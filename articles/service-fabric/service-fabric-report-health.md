---
title: Přidat vlastní sestavy o stavu Service Fabric
description: Popisuje, jak odesílat vlastní sestavy o stavu do entit Azure Service Fabric Health. Poskytuje doporučení pro návrh a implementaci sestav o stavu kvality.
ms.topic: conceptual
ms.date: 2/28/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: c87c063ac9d3b4810657f72c46c17725b8899c77
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626165"
---
# <a name="add-custom-service-fabric-health-reports"></a>Přidat vlastní sestavy o stavu Service Fabric
Azure Service Fabric zavádí [model stavu](service-fabric-health-introduction.md) , který je navržený tak, aby na konkrétní entity mohl označovat stav není v pořádku a podmínky použití aplikace. Model stavu používá **sestavy stavu** (systémové součásti a sledovací zařízení). Cílem je snadno a rychle diagnostikovat a opravit. Zapisovače služeb se musí představit předem o stavu. Všechny podmínky, které mohou ovlivnit stav, by měly být hlášeny, zejména v případě, že mohou přispět k potížím s příznakem blízko ke kořenu. Informace o stavu mohou ušetřit čas a úsilí při ladění a vyšetřování. Užitečnost je obzvláště jasné, když je služba v cloudu (soukromá nebo Azure) v provozu.

Service Fabric sestavy monitorují identifikované podmínky zájmu. Vykazují tyto podmínky na základě jejich místního zobrazení. [Health Store](service-fabric-health-introduction.md#health-store) agreguje data o stavu odesílaná všemi sestavami, aby zjistila, jestli jsou entity globálně v pořádku. Model má být bohatě, flexibilní a snadno použitelný. Kvalita sestav stavu určuje přesnost zobrazení stavu clusteru. Falešné kladné hodnoty, které nesprávně ukazují problémy ve špatném stavu, můžou mít negativní dopad na upgrady nebo jiné služby, které používají data o stavu. Příklady takových služeb jsou opravné služby a mechanismy upozorňování. Proto je nutné, aby bylo možné poskytovat sestavy, které zachytí podmínky zájmu co nejlepším možným způsobem.

Pro návrh a implementaci vytváření sestav o stavu, sledovacích zařízení a systémových komponent musí:

* Definujte podmínku, které vás zajímá, způsob jejího monitorování a dopad na fungování clusteru nebo aplikace. Na základě těchto informací se rozhodněte o vlastnosti sestavy o stavu a stavu.
* Určete [entitu](service-fabric-health-introduction.md#health-entities-and-hierarchy) , na kterou se sestava vztahuje.
* Určete, kde se vytváření sestav provádí, ze služby nebo z interního nebo externího sledovacího zařízení.
* Definujte zdroj, který se používá k identifikaci zpravodaje.
* Vyberte strategii vytváření sestav, ať už pravidelně, nebo na přechodech. Doporučený způsob je pravidelně, protože vyžaduje jednodušší kód a méně náchylná k chybám.
* Určete, jak dlouho má sestava pro podmínky, které nejsou v pořádku, zůstat v Health Store a jak se má vymazat. Pomocí těchto informací se rozhodněte, jak dlouho má sestava fungovat a kdy se má chování zrušit při vypršení platnosti.

Jak bylo zmíněno, vytváření sestav je možné provést z těchto kroků:

* Replika služby monitorované Service Fabric.
* Interní sledovací zařízení nasazená jako služba Service Fabric (například Service Fabric Bezstavová služba, která monitoruje stavy a hlášení problémů). Sledovací zařízení lze nasadit do všech uzlů nebo je lze spřažené do monitorované služby.
* Interní sledovací zařízení, která běží na Service Fabricch uzlech, ale *nejsou implementovaná* jako Service Fabric služby.
* Externí sledovací zařízení, která procházejí prostředkem *mimo* Service Fabric cluster (například monitorovací služba jako Gomez).

> [!NOTE]
> V nedostatku se cluster naplní zprávami o stavu, které odesílají součásti systému. Další informace najdete v tématu [použití sestav stavu systému k řešení potíží](service-fabric-understand-and-troubleshoot-with-system-health-reports.md). Sestavy uživatelů musí být odesílány na [entity stavu](service-fabric-health-introduction.md#health-entities-and-hierarchy) , které již byly vytvořeny systémem.
> 
> 

Jakmile je návrh vytváření sestav stavu jasný, je možné snadno odesílat sestavy o stavu. [FabricClient](/dotnet/api/system.fabric.fabricclient) můžete použít k hlášení stavu, Pokud cluster není [zabezpečený](service-fabric-cluster-security.md) nebo pokud má klient prostředků infrastruktury oprávnění správce. Vytváření sestav můžete provést prostřednictvím rozhraní API pomocí [FabricClient. HealthManager. ReportHealth](/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth), prostřednictvím PowerShellu nebo pomocí REST. Ovladače konfigurace dávkují sestavy pro lepší výkon.

> [!NOTE]
> Stav sestavy je synchronní a představuje pouze práci ověřování na straně klienta. Skutečnost, že je sestava přijata klientem stavu nebo `Partition` `CodePackageActivationContext` objekty nebo, neznamená, že se používá v úložišti. Odesílá se asynchronně a pravděpodobně dávkuje s jinými sestavami. Zpracování na serveru může stále selhat: pořadové číslo může být zastaralé, entita, na které je nutné sestavu použít, byla odstraněna atd.
> 
> 

## <a name="health-client"></a>Klient stavu
Sestavy o stavu se odesílají do Správce stavu prostřednictvím klienta Health, který je umístěn v rámci klienta prostředků infrastruktury. Správce stavu ukládá sestavy do Health Store. U klienta Health se dá nakonfigurovat toto nastavení:

* **HealthReportSendInterval**: zpoždění mezi časem přidání sestavy do klienta a čas odeslání do Správce stavu. Slouží k dávkování sestav do jedné zprávy namísto odesílání jedné zprávy pro každou sestavu. Dávkování zvyšuje výkon. Výchozí hodnota: 30 sekund.
* **HealthReportRetrySendInterval**: interval, ve kterém klient stavu znovu odesílá shromážděné zprávy o stavu do Správce stavu. Výchozí: 30 sekund, minimum: 1 sekunda.
* **HealthOperationTimeout**: časový limit pro zprávu sestavy odeslanou správci stavu. Pokud dojde k vypršení časového limitu zprávy, klient stavu je znovu pokusí, dokud správce stavu nepotvrdí, že sestava byla zpracována. Výchozí: dvě minuty.

> [!NOTE]
> Při dávkovém zpracování sestav je potřeba, aby byl klient prostředků infrastruktury udržován na aktivním místě alespoň pro HealthReportSendInterval, aby bylo zajištěno jejich odeslání. Pokud dojde ke ztrátě zprávy nebo ji správce stavu nemůže použít kvůli přechodným chybám, musí být klient prostředků infrastruktury stále aktivní a dát mu možnost opakovat akci.
> 
> 

Ukládání do vyrovnávací paměti klienta vezme v úvahu jedinečnosti sestav. Pokud například určitý špatný zpravodaj hlásí 100 sestav za sekundu na stejné vlastnosti stejné entity, nahradí se sestavy poslední verzí. Ve frontě klienta existuje nejvýše jedna taková sestava. Pokud nakonfigurujete dávkování, počet zpráv odeslaných do Správce stavu je pouze jeden za interval odeslání. Tato sestava je poslední přidanou sestavou, která odráží aktuální stav entity.
Zadejte parametry konfigurace při `FabricClient` vytváření předáváním [FabricClientSettings](/dotnet/api/system.fabric.fabricclientsettings) s požadovanými hodnotami pro položky související se stavem.

Následující příklad vytvoří klienta prostředků infrastruktury a určí, že se mají sestavy odesílat při jejich přidání. V případě časových limitů a chyb, které je možné opakovat, se opakování provádí každých 40 sekund.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

Doporučujeme ponechat výchozí nastavení klienta prostředků infrastruktury, které je nastavené `HealthReportSendInterval` na 30 sekund. Toto nastavení zajišťuje optimální výkon z důvodu dávkování. V případě kritických sestav, které je třeba odeslat co nejdříve, použijte příkaz `HealthReportSendOptions` s okamžitým `true` rozhraním API [FabricClient. HealthClient. ReportHealth](/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) . Okamžité sestavy vycházejí z intervalu dávkování. Pomocí tohoto příznaku se opatrně; Pokud je to možné, chceme využít dávkování klientů healthing. Okamžité odeslání je užitečné také v případě, že je klient Fabric ukončován (například proces zjistil neplatný stav a musí vypnout, aby se zabránilo vedlejším účinkům). Zajišťuje nejlepší úsilí při posílání kumulovaných sestav. Když se přidá jedna sestava s okamžitým příznakem, klient stavu se po posledním odeslání zařadí všechny shromážděné sestavy.

Při vytváření připojení ke clusteru prostřednictvím PowerShellu se dají zadat stejné parametry. Následující příklad spustí připojení k místnímu clusteru:

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

Podobně jako rozhraní API je možné zprávy odesílat pomocí `-Immediate` přepínače k okamžitému odeslání bez ohledu na `HealthReportSendInterval` hodnotu.

V případě REST se zprávy odesílají do Service Fabric brány, která má interního klienta prostředků infrastruktury. Ve výchozím nastavení je tento klient nakonfigurován tak, aby odesílal sestavy do dávky každých 30 sekund. Interval dávky můžete změnit pomocí nastavení konfigurace clusteru `HttpGatewayHealthReportSendInterval` v `HttpGateway` . Jak bylo zmíněno, lepší možnost je odeslat sestavy pomocí `Immediate` hodnoty true. 

> [!NOTE]
> Aby bylo zajištěno, že neautorizované služby nemohou hlásit stav na základě entit v clusteru, nakonfigurujte server tak, aby přijímal požadavky pouze od zabezpečených klientů. `FabricClient`Použití pro vytváření sestav musí mít povolené zabezpečení, aby bylo možné komunikovat s clusterem (například pomocí protokolu Kerberos nebo ověřování certifikátů). Přečtěte si další informace o [zabezpečení clusteru](service-fabric-cluster-security.md).
> 
> 

## <a name="report-from-within-low-privilege-services"></a>Sestava v rámci služeb s nízkou úrovní oprávnění
Pokud služba Service Fabric Services nemá přístup správce ke clusteru, můžete hlásit stav entit z aktuálního kontextu prostřednictvím `Partition` nebo `CodePackageActivationContext` .

* U bezstavových služeb použijte [IStatelessServicePartition. ReportInstanceHealth](/dotnet/api/system.fabric.istatelessservicepartition.reportinstancehealth) k hlášení aktuální instance služby.
* Pro stavové služby použijte [IStatefulServicePartition. ReportReplicaHealth](/dotnet/api/system.fabric.istatefulservicepartition.reportreplicahealth) k hlášení o aktuální replice.
* Pomocí [IServicePartition. ReportPartitionHealth](/dotnet/api/system.fabric.iservicepartition.reportpartitionhealth) nahlaste aktuální entitu oddílu.
* K hlášení aktuální aplikace použijte [CodePackageActivationContext. ReportApplicationHealth](/dotnet/api/system.fabric.codepackageactivationcontext.reportapplicationhealth) .
* Pomocí [CodePackageActivationContext. ReportDeployedApplicationHealth](/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedapplicationhealth) můžete nahlásit aktuální aplikaci nasazenou v aktuálním uzlu.
* Pomocí [CodePackageActivationContext. ReportDeployedServicePackageHealth](/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedservicepackagehealth) nahlaste balíček služby pro aplikaci nasazenou na aktuálním uzlu.

> [!NOTE]
> Interně jsou `Partition` a `CodePackageActivationContext` držitelem stavu s nakonfigurovaným výchozím nastavením. Jak je vysvětleno pro [klienta Health](service-fabric-report-health.md#health-client), sestavy jsou dávkově a odesílány v časovači. Objekty by měly zůstat aktivní, aby měly možnost Odeslat zprávu.
> 
> 

Můžete určit, `HealthReportSendOptions` kdy se mají odesílat sestavy přes `Partition` rozhraní API pro stav a `CodePackageActivationContext` . Pokud máte důležité sestavy, které je třeba poslat co nejdříve, používejte `HealthReportSendOptions` s okamžitou možností `true` . Okamžité sestavy obcházejí interval dávkování interního klienta stavu. Jak bylo uvedeno dříve, používejte tento příznak s péčí; Pokud je to možné, chceme využít dávkování klientů healthing.

## <a name="design-health-reporting"></a>Vytváření sestav o stavu
Prvním krokem při generování vysoce kvalitních sestav je určení podmínek, které mohou mít vliv na stav služby. Jakákoli podmínka, která může pomáhat při potížích s příznakem ve službě nebo v clusteru, když je spuštěná nebo ještě lepší, než se problém stane – může potenciálně ušetřit miliardy dolarů. Výhody zahrnují méně času, méně nočních hodin strávených zkoumáním a opravou problémů a vyšší spokojenost zákazníků.

Jakmile jsou podmínky identifikovány, zapisovače sledovacích zařízení musí zjistit nejlepší způsob, jak je monitorovat pro rovnováhu mezi režií a užitečnosti. Představte si třeba službu, která provádí složité výpočty, které používají některé dočasné soubory ve sdílené složce. Sledovací zařízení může monitorovat sdílenou složku, aby bylo zajištěno, že je k dispozici dostatek místa. Může naslouchat oznámení o změně souboru nebo adresáře. Může ohlásit upozornění, pokud je dosaženo prahové hodnoty pro začátek, a ohlásit chybu, pokud je sdílená složka plná. Systém opravy může na upozornění zahájit čištění starších souborů ve sdílené složce. Při chybě mohl systém opravy přesunout repliku služby na jiný uzel. Všimněte si, jak jsou stavy stavů popsány v tématu stav: stav podmínky, kterou lze považovat za v pořádku (ok) nebo není v pořádku (upozornění nebo chyba).

Po nastavení podrobností monitorování musí zapisovač sledovacích zařízení zjistit, jak se má sledovací modul implementovat. Pokud je možné určit podmínky v rámci služby, sledovací zařízení může být součástí samotné monitorované služby. Kód služby může například kontrolovat využití sdílení a pak pokaždé, když se pokusí zapisovat soubor. Výhodou tohoto přístupu je, že vytváření sestav je jednoduché. Je nutné dbát na to, aby se zabránilo chybám sledovacího zařízení z vlivu na funkčnost služby.

Vytváření sestav z monitorované služby není vždy možnost. Sledovací zařízení v rámci služby nemusí být schopné detekovat podmínky. Nemusí mít logiku nebo data, aby bylo možné provést stanovení. Režie monitorování podmínek může být vysoká. Podmínky také nemusí být specifické pro službu, ale místo toho ovlivňují interakce mezi službami. Další možností je mít v clusteru jako samostatné procesy sledovací zařízení. Sledovací zařízení sledují podmínky a sestavu, aniž by to mělo vliv na hlavní služby. Například tato sledovací zařízení by mohla být implementována jako bezstavové služby ve stejné aplikaci, nasazená na všech uzlech nebo na stejných uzlech, jako je služba.

V některých případech se sledovací zařízení běžící v clusteru nejedná o možnost ani jednu z možností. Pokud je monitorovaná podmínka dostupnost nebo funkce služby, jak ji uživatelé uvidí, je vhodné mít sledovací zařízení na stejném místě jako klienti uživatelů. Tam můžou tyto operace testovat stejným způsobem, jakým je uživatelé volají. Například můžete mít sledovací zařízení, které žije mimo cluster, vystavuje požadavky na službu a kontroluje latenci a správnost výsledku. (U služby kalkulačky například v rozumné době provede 2 + 2 vrácení 4?)

Jakmile budou podrobnosti sledovacího zařízení dokončeny, měli byste se rozhodnout pro ID zdroje, které ho jednoznačně identifikuje. Pokud v clusteru žije více sledovacích zařízení stejného typu, musí zprávy o různých entitách, nebo pokud jsou v sestavě stejné entity, použít jiné ID zdroje nebo vlastnost. Tímto způsobem můžou jejich sestavy existovat společně. Vlastnost sestavy o stavu by měla zachytit monitorovanou podmínku. (Pro výše uvedený příklad může být vlastnost **ShareSize**.) Pokud je u stejné podmínky použito více sestav, vlastnost by měla obsahovat některé dynamické informace, které umožní, aby sestavy existovaly současně. Pokud například potřebujete monitorovat více sdílených složek, název vlastnosti může být **ShareSize-název_sdílené_položky**.

> [!NOTE]
> Nepoužívejte *Health Store* k uchování informací o stavu. Pouze informace týkající se stavu by měly být hlášeny jako stav, protože tyto informace mají vliv na hodnocení stavu entity. Health Store nebyla navržena jako úložiště pro obecné účely. Pomocí logiky vyhodnocení stavu agreguje všechna data do stavu. Odesílání informací, které nesouvisí se stavem (například stav vytváření sestav s stavem OK) nemá vliv na agregovaný stav, ale může negativně ovlivnit výkon Health Store.
> 
> 

Dalším rozhodovacím bodem je entita, na které se má sestava nahlásit. Ve většině případů podmínka jednoznačně identifikuje entitu. Vyberte entitu s nejlepší možnou členitosti. Pokud podmínka ovlivní všechny repliky v oddílu, sestavte na oddílu, nikoli ve službě. Existují rohové případy, kde je potřeba více představit, ale. Pokud podmínka ovlivňuje entitu, jako je třeba replika, ale je potřeba, aby byla podmínka označená za více než po dobu životnosti repliky, pak by se měla nahlásit v oddílu. V opačném případě Health Store při odstranění repliky vyčistí všechny své sestavy. Zapisovače sledovacích zařízení musí uvažovat o životnosti entity a sestavy. Musí být jasné, pokud by měla být sestava vyčištěna z úložiště (například když se v entitě již nepoužívá chyba).

Pojďme se podívat na příklad, který přináší výše popsané body. Vezměte v úvahu Service Fabric aplikaci složenou z hlavní stavové trvalé služby a sekundární stavové služby nasazené na všech uzlech (jeden typ sekundární služby pro každý typ úlohy). Hlavní server má frontu zpracování obsahující příkazy, které mají být spouštěny pomocí sekundárních. Sekundární spustí příchozí požadavky a pošle zpět signály pro potvrzení. Jednou podmínkou, kterou lze monitorovat, je délka fronty hlavního zpracování. Pokud délka hlavní fronty dosáhne prahové hodnoty, je hlášeno upozornění. Upozornění indikuje, že sekundární nedokáže zpracovat zatížení. Pokud fronta dosáhne maximální délky a příkazy se zahozeny, nahlásí se chyba, protože služba nemůže provést obnovení. Sestavy mohou být ve vlastnosti **QueueStatus**. Sledovací zařízení se nachází uvnitř služby a pravidelně se odesílají na hlavní primární repliku. Doba do provozu je dvě minuty a je pravidelně odesílána každých 30 sekund. Pokud primární databáze dojde k výpadku, vyčistí se sestava automaticky ze Storu. Pokud je replika služby zapnutá, ale je zablokovaná nebo má jiné problémy, vyprší platnost sestavy Health Store. V takovém případě je entita vyhodnocena chybou.

Další podmínkou, kterou lze monitorovat, je čas spuštění úlohy. Hlavní distribuuje úlohy do sekundárních na základě typu úkolu. V závislosti na návrhu může hlavní tabulka dotazovat se na sekundární stav úlohy. Může také počkat na to, že sekundárnímu odeslání signalizuje potvrzení. V druhém případě je potřeba dbát na detekci situací, kdy dojde ke ztrátě sekundárních kostek nebo zpráv. Jednou z možností je, aby hlavní server odesílal požadavek příkazu pro odeslání na stejnou sekundární hodnotu, která odesílá zpět svůj stav. Pokud se neobdrží žádný stav, hlavní server to považuje za selhání a znovu naplánuje úlohu. Toto chování předpokládá, že se úkoly idempotentní.

Monitorovaná podmínka se dá přeložit jako upozornění, pokud se úloha v určitou dobu neprovádí **(například** 10 minut). Pokud úloha není dokončená v čase (**T2**, například 20 minut), monitorovaná podmínka se dá přeložit jako chyba. Vytváření sestav je možné provést několika způsoby:

* Hlavní primární replika sám o sobě sestavuje pravidelně. U všech čekajících úloh ve frontě můžete mít jednu vlastnost. Pokud nejméně jeden úkol trvá déle, je stav sestavy ve vlastnosti **PendingTasks** upozornění nebo chyba. Pokud neexistují žádné úlohy, které čekají na zpracování nebo jsou spuštěné všechny úlohy, stav sestavy je OK. Úkoly jsou trvalé. Pokud primární primární postup, může nově povýšená primární aplikace nadále správně nahlásit.
* Jiný proces sledovacího procesu (v cloudu nebo externí) kontroluje úlohy (mimo jiné na základě požadovaného výsledku úkolu), aby bylo možné zjistit, zda jsou dokončeny. Pokud nerespektují prahové hodnoty, pošle se sestava do hlavní služby. Do každé úlohy, která zahrnuje identifikátor úkolu, jako je například **PendingTask + taskid**, se pošle zpráva. Sestavy by se měly odesílat jenom v případě, že stav není v pořádku. Nastavte čas na živé až několik minut a označte sestavy, které se mají odebrat, když vyprší jejich platnost, aby se zajistilo vyčištění.
* Sekundární, který spouští sestavy úlohy, když trvá spuštění déle, než se čekalo. Oznamuje na instanci služby na vlastnosti **PendingTasks**. Tato sestava znamená instanci služby, která má problémy, ale nezachycuje situaci, kdy instance zemře. Sestavy se vyčistí. Může být hlášená na sekundární službu. Pokud sekundární instance úkol dokončí, sekundární instance vymaže sestavu ze Storu. Sestava nezachycuje situaci, kdy došlo ke ztrátě zprávy o potvrzení a úloha není dokončena z hlavního bodu zobrazení.

Vytváření sestav se ale provádí v případech popsaných výše, sestavy se zaznamenávají do stavu aplikace, když se stav vyhodnocuje.

## <a name="report-periodically-vs-on-transition"></a>Pravidelné sestavování vs. při přechodu
Pomocí modelu vytváření sestav stavu můžou sledovací zařízení pravidelně odesílat sestavy nebo na přechody. Doporučený způsob vytváření sestav sledovacího zařízení je pravidelně, protože kód je mnohem jednodušší a méně náchylný k chybám. Sledovací zařízení se musí co nejrychleji snažit a vyhnout se chybám, které aktivují nesprávné sestavy. Nesprávné sestavy, které nejsou v *pořádku* , mají za důsledek vyhodnocení stavu a scénáře na základě stavu, včetně upgradů. Nesprávné zprávy v *pořádku* skryjí problémy v clusteru, což není žádoucí.

Pro pravidelné generování sestav je možné sledovací zařízení implementovat pomocí časovače. Při zpětném volání časovače může sledovací zařízení kontrolovat stav a odeslat sestavu na základě aktuálního stavu. Není nutné, abyste viděli, která sestava byla dříve odeslána, nebo aby se zajistila optimalizace zasílání zpráv. Klient Health má logiku dávkování, která vám může pomáhat s výkonem. I když je klient stavu spuštěný jako aktivní, opakuje se interně, dokud Health Store sestavu nepotvrdí, nebo sledovacímu zařízení vygeneruje novější sestavu se stejnou entitou, vlastností a zdrojem.

Vytváření sestav pro přechody vyžaduje pečlivé zpracování stavu. Sledovací zařízení monitoruje některé podmínky a sestavy pouze v případě, že se mění podmínky. Na straně tohoto přístupu je potřeba zajistit méně sestav. Nevýhodou je, že logika sledovacího zařízení je složitá. Sledovací zařízení musí udržovat podmínky nebo sestavy, aby bylo možné je zkontrolovat, aby bylo možné určit změny stavu. Při převzetí služeb při selhání je potřeba dbát na to, aby byly přidané sestavy, ale ještě nebyly odeslány do Health Store. Pořadové číslo se musí stále zvyšovat. V takovém případě se zprávy odmítnou jako zastaralé. Ve výjimečných případech, kdy dojde ke ztrátě dat, může být potřeba synchronizace mezi stavem zpravodaje a stavem Health Store.

Vytváření sestav o přechodech dává smysl pro služby, které se samy hlásí, prostřednictvím `Partition` nebo `CodePackageActivationContext` . Když se odebere místní objekt (Replika nebo nasazená sada nebo nasazená aplikace), odeberou se taky všechny jeho sestavy. Díky tomuto automatickému vyčištění je potřeba synchronizovat mezi zpravodajem a Health Store. Pokud je sestava pro nadřazený oddíl nebo nadřazenou aplikaci, je nutné dbát na převzetí služeb při selhání, aby se předešlo zastaralým sestavám v Health Store. Aby bylo možné zachovat správný stav, je nutné přidat logiku a vymazat sestavu ze Storu, pokud již není potřebná.

## <a name="implement-health-reporting"></a>Implementace vytváření sestav o stavu
Jakmile jsou informace o entitě a sestavě jasné, můžete odesílat sestavy o stavu prostřednictvím rozhraní API, PowerShellu nebo REST.

### <a name="api"></a>Rozhraní API
K vytváření sestav přes rozhraní API potřebujete vytvořit sestavu o stavu, která je specifická pro typ entity, na kterou chtějí nahlásit. Poskytněte zprávu klientovi stavu. Případně můžete vytvořit informace o stavu a předat je do správné metody vytváření sestav `Partition` u `CodePackageActivationContext` aktuálních entit.

Následující příklad ukazuje pravidelné generování sestav z sledovacího zařízení v rámci clusteru. Sledovací zařízení kontroluje, jestli je k externímu prostředku možné přistupovat v rámci uzlu. Prostředek vyžaduje manifest služby v rámci aplikace. Pokud prostředek není k dispozici, ostatní služby v aplikaci mohou stále fungovat správně. Proto je sestava odeslána na nasazenou entitu balíčku služby každých 30 sekund.

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
Odesílání sestav o stavu pomocí **Send-ServiceFabric *EntityType* HealthReport**.

Následující příklad ukazuje pravidelné generování sestav o hodnotách procesoru v uzlu. Sestavy by se měly posílat každých 30 sekund a jejich doba je živá po dobu dvou minut. Pokud vyprší, má zpravodaj problémy, takže se uzel vyhodnocuje při chybě. Pokud je procesor nad prahovou hodnotou, má sestava stav varování. Pokud procesor zůstává nad prahovou hodnotou pro více než nakonfigurovaný čas, nahlásí se jako chyba. V opačném případě zpravodaj odešle stav OK.

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

Následující příklad oznamuje přechodné upozornění na replice. Nejprve získá ID oddílu a pak ID repliky služby, které vás zajímá. Pak pošle sestavu z **PowershellWatcher** ve vlastnosti **ResourceDependency**. Sestava je zajímavá jenom za dvě minuty a z úložiště se automaticky odebere.

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
Odesílat sestavy o stavu pomocí REST s požadavky POST, které přecházejí na požadovanou entitu a mají v těle popis sestavy o stavu. Podívejte se například na téma odeslání [sestav stavu clusteru](/rest/api/servicefabric/report-the-health-of-a-cluster) REST nebo [sestav o stavu služby](/rest/api/servicefabric/report-the-health-of-a-service). Všechny entity jsou podporovány.

## <a name="next-steps"></a>Další kroky
Na základě dat o stavu, zapisovače služeb a správců clusterů a aplikací si můžete představit, jak tyto informace využívat. Můžou například nastavit výstrahy na základě stavu a zachytit vážné problémy před tím, než vyplní výpadky. Správci můžou také nastavit systémy oprav, aby automaticky opravily problémy.

[Úvod do monitorování stavu Service Fabric](service-fabric-health-introduction.md)

[Zobrazit Service Fabric sestavy o stavu](service-fabric-view-entities-aggregated-health.md)

[Postup hlášení a kontroly stavu služby](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Použití sestav stavu systému pro řešení potíží](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Místní monitorování a diagnostika služeb](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Upgrade aplikace Service Fabric](service-fabric-application-upgrade.md)
