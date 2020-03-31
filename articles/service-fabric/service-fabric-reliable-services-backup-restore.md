---
title: Zálohování a obnovení Service Fabric
description: Koncepční dokumentace pro zálohování a obnovení service fabric, služba pro konfiguraci zálohování spolehlivé stavové služby a spolehlivé objekty actor.
author: mcoskun
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: mcoskun
ms.openlocfilehash: ac6bb14517b67a4b308460583e8c9fb99a2df9f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75922780"
---
# <a name="backup-and-restore-reliable-services-and-reliable-actors"></a>Zálohování a obnovení spolehlivých služeb a spolehlivých aktérů
Azure Service Fabric je platforma s vysokou dostupností, která replikuje stav napříč více uzly k udržení této vysoké dostupnosti.  Proto i v případě, že jeden uzel v clusteru selže, služby jsou i nadále k dispozici. Zatímco tato vestavěná redundance poskytovaná platformou může být pro některé dostatečná, v některých případech je žádoucí, aby služba zálohovala data (do externího úložiště).

> [!NOTE]
> Je důležité zálohovat a obnovovat data (a otestovat, že funguje podle očekávání), abyste se mohli zotavit ze scénářů ztráty dat.
> 

> [!NOTE]
> Společnost Microsoft doporučuje používat [pravidelné zálohování a obnovení](service-fabric-backuprestoreservice-quickstart-azurecluster.md) pro konfiguraci zálohování dat spolehlivé stavové služby a spolehlivé objekty actor. 
> 


Služba může například chtít zálohovat data za účelem ochrany před následujícími scénáři:

- V případě trvalé ztráty celého clusteru Service Fabric.
- Trvalá ztráta většiny replik oddílu služby
- Chyby správy, při kterých se stav omylem odstraní nebo poškodí. K tomu může dojít například v případě, že správce s dostatečným oprávněním chybně odstraní službu.
- Chyby ve službě, které způsobují poškození dat. Například k tomu může dojít, když upgrade kódu služby začne psát chybná data do spolehlivé kolekce. V takovém případě kód a data může být muset vrátit do dřívějšího stavu.
- Offline zpracování dat. Může být vhodné mít offline zpracování dat pro business intelligence, které se děje odděleně od služby, která generuje data.

Funkce Zálohování a obnovení umožňuje službám založeným na rozhraní API spolehlivých služeb vytvářet a obnovovat zálohy. Zálohovací rozhraní API poskytovaná platformou umožňují zálohování stavu oddílu služby bez blokování operací čtení nebo zápisu. Rozhraní API pro obnovení umožňují obnovení stavu oddílu služby z vybrané zálohy.

## <a name="types-of-backup"></a>Typy zálohování
Existují dvě možnosti zálohování: Úplné a Přírůstkové.
Úplná záloha je záloha, která obsahuje všechna data potřebná k opětovnému vytvoření stavu repliky: kontrolní body a všechny záznamy protokolu.
Vzhledem k tomu, že má kontrolní body a protokol, úplnou zálohu lze obnovit samostatně.

Problém s úplnými zálohami nastává, když jsou kontrolní body velké.
Například replika, která má 16 GB stavu bude mít kontrolní body, které se sčítají přibližně 16 GB.
Pokud máme cíl bodu obnovení pět minut, replika musí být zálohována každých pět minut.
Pokaždé, když zálohuje je třeba zkopírovat 16 GB kontrolních bodů `CheckpointThresholdInMB`kromě 50 MB (konfigurovatelné pomocí) v hodnotě protokolů.

![Příklad úplného zálohování.](media/service-fabric-reliable-services-backup-restore/FullBackupExample.PNG)

Řešením tohoto problému je přírůstkové zálohování, kde zálohování obsahuje pouze změněné záznamy protokolu od poslední zálohy.

![Příklad přírůstkového zálohování.](media/service-fabric-reliable-services-backup-restore/IncrementalBackupExample.PNG)

Vzhledem k tomu, že přírůstkové zálohy jsou pouze změny od poslední zálohy (nezahrnuje kontrolní body), mají tendenci být rychlejší, ale nemohou být obnoveny samy o sobě.
Chcete-li obnovit přírůstkové zálohování, je vyžadován celý řetězec zálohování.
Řetězec zálohování je řetězec záloh počínaje úplnou zálohou a následovaný řadou souvislých přírůstkových záloh.

## <a name="backup-reliable-services"></a>Spolehlivé služby zálohování
Autor služby má plnou kontrolu nad tím, kdy provést zálohování a kde budou zálohy uloženy.

Chcete-li spustit zálohu, služba musí vyvolat `BackupAsync`zděděnou člennou funkci .  
Zálohy lze provést pouze z primárních replik a vyžadují udělení stavu zápisu.

Jak je `BackupAsync` znázorněno `BackupDescription` níže, bere v objektu, kde je možné zadat úplné `Func<< BackupInfo, CancellationToken, Task<bool>>>` nebo přírůstkové zálohování, stejně jako funkce zpětného volání, která je vyvolána, když byla vytvořena záložní složka místně a je připravena k přesunutí do některého externího úložiště.

```csharp

BackupDescription myBackupDescription = new BackupDescription(BackupOption.Incremental,this.BackupCallbackAsync);

await this.BackupAsync(myBackupDescription);

```

Požadavek na přijetí přírůstkové `FabricMissingFullBackupException`zálohy může selhat s programem . Tato výjimka označuje, že se děje jedna z následujících věcí:

- replika nikdy nepřijala úplnou zálohu od té doby, co se stala primární,
- některé záznamy protokolu od poslední zálohy byly zkráceny nebo
- replika `MaxAccumulatedBackupLogSizeInMB` překročila limit.

Uživatelé mohou zvýšit pravděpodobnost, že budou moci `MinLogSizeInMB` provést `TruncationThresholdFactor`přírůstkové zálohování konfigurací nebo .
Zvýšení těchto hodnot zvyšuje využití disku repliky.
Další informace naleznete v tématu [Reliable Services Configuration](service-fabric-reliable-services-configuration.md)

`BackupInfo`obsahuje informace týkající se zálohy, včetně umístění složky,`BackupInfo.Directory`do které runtime zálohu uložil ( ). Funkce zpětného `BackupInfo.Directory` volání můžete přesunout do externího úložiště nebo jiného umístění.  Tato funkce také vrátí bool, který označuje, zda se jí podařilo úspěšně přesunout záložní složku do cílového umístění.

Následující kód ukazuje, `BackupCallbackAsync` jak lze metodu použít k nahrání zálohy do služby Azure Storage:

```csharp
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, cancellationToken);

    return true;
}
```

V předchozím příkladu `ExternalBackupStore` je ukázková třída, která se používá k `UploadBackupFolderAsync` rozhraní s úložištěm objektů Blob Azure a je metoda, která komprimuje složku a umístí ji do úložiště objektů blob Azure.

Poznámky:

  - V daném okamžiku může být v jedné replice pouze jedna operace zálohování za letu. Více než `BackupAsync` jeden hovor najednou `FabricBackupInProgressException` bude házet omezit zálohy za letu na jeden.
  - Pokud replika převezme služby při selhání během zálohování, je možné, že záloha nebyla dokončena. Proto po dokončení převzetí služeb při selhání, je odpovědností služby restartovat `BackupAsync` zálohu vyvoláním podle potřeby.

## <a name="restore-reliable-services"></a>Obnovení spolehlivých služeb
Obecně platí, že případy, kdy budete muset provést operaci obnovení spadají do jedné z těchto kategorií:

  - Oddíl služby ztratil data. Například disk pro dvě ze tří replik pro oddíl (včetně primární repliky) dojde k poškození nebo vymazání. Nové primární může být nutné obnovit data ze zálohy.
  - Celá služba je ztracena. Správce například odebere celou službu a proto je třeba obnovit službu a data.
  - Služba replikovala poškozená data aplikací (například z důvodu chyby aplikace). V takovém případě musí být služba upgradována nebo vrácena, aby byla vrácena příčina poškození, a nepoškozená data musí být obnovena.

Zatímco mnoho přístupů je možné, nabízíme některé příklady na použití `RestoreAsync` k obnovení z výše uvedených scénářů.

## <a name="partition-data-loss-in-reliable-services"></a>Ztráta dat oddílu ve spolehlivých službách
V takovém případě by runtime automaticky zjistit ztrátu dat a vyvolat `OnDataLossAsync` rozhraní API.

Autor služby musí provést následující obnovení:

  - Přepsat metodu `OnDataLossAsync`třídy virtuální základní .
  - Najděte nejnovější zálohu v externím umístění, které obsahuje zálohy služby.
  - Stáhněte si nejnovější zálohu (a rozbalte zálohu do záložní složky, pokud byla komprimována).
  - Metoda `OnDataLossAsync` poskytuje `RestoreContext`. Volání `RestoreAsync` rozhraní API na `RestoreContext`zadaný .
  - Vrátit true v případě, že obnovení byl úspěšný.

Následuje příklad implementace `OnDataLossAsync` metody:

```csharp
protected override async Task<bool> OnDataLossAsync(RestoreContext restoreCtx, CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    var restoreDescription = new RestoreDescription(backupFolder);

    await restoreCtx.RestoreAsync(restoreDescription);

    return true;
}
```

`RestoreDescription`předándo `RestoreContext.RestoreAsync` volání obsahuje člen `BackupFolderPath`s názvem .
Při obnovení jedné úplné zálohy by měla `BackupFolderPath` být nastavena na místní cestu složky, která obsahuje úplnou zálohu.
Při obnovení úplné zálohy a počet přírůstkové zálohy, `BackupFolderPath` by měla být nastavena na místní cestu složky, která obsahuje nejen úplné zálohy, ale také všechny přírůstkové zálohy.
`RestoreAsync`volání může `FabricMissingFullBackupException` vyvolat, pokud zapředpokladu, `BackupFolderPath` že neobsahuje úplnou zálohu.
Může také `ArgumentException` vyvolat, pokud `BackupFolderPath` má přerušený řetězec přírůstkových záloh.
Například pokud obsahuje úplnou zálohu, první přírůstkové a třetí přírůstkové zálohování, ale ne druhý přírůstkové zálohování.

> [!NOTE]
> Zásady obnovení jsou ve výchozím nastavení nastaveny na hodnotu Bezpečné.  To znamená, `RestoreAsync` že rozhraní API se nezdaří s ArgumentException, pokud zjistí, že záložní složka obsahuje stav, který je starší než nebo rovno stavu obsaženému v této replice.  `RestorePolicy.Force`lze použít k přeskočení této bezpečnostní kontroly. To to je `RestoreDescription`určeno jako součást .
> 

## <a name="deleted-or-lost-service"></a>Odstraněná nebo ztracená služba
Pokud je služba odebrána, musíte nejprve znovu vytvořit službu před obnovením dat.  Je důležité vytvořit službu se stejnou konfigurací, například schéma dělení, aby data mohla být bez problémů obnovena.  Jakmile je služba nahoru, rozhraní API`OnDataLossAsync` pro obnovení dat (výše) musí být vyvolána na každém oddílu této služby. Jedním ze způsobů, jak toho dosáhnout, je pomocí [FabricClient.TestManagementClient.StartPartitionDataLossAsync](https://msdn.microsoft.com/library/mt693569.aspx) na každém oddílu.  

Od tohoto okamžiku implementace je stejná jako výše uvedený scénář. Každý oddíl musí obnovit nejnovější relevantní zálohu z externího úložiště. Jedním z upozornění je, že ID oddílu může nyní změnily, protože runtime vytváří ID oddílu dynamicky. Služba tedy potřebuje uložit příslušné informace o oddílu a název služby k identifikaci správné nejnovější zálohy obnovit z pro každý oddíl.

> [!NOTE]
> Nedoporučuje se používat `FabricClient.ServiceManager.InvokeDataLossAsync` v každém oddílu k obnovení celé služby, protože to může poškodit stav clusteru.
> 

## <a name="replication-of-corrupt-application-data"></a>Replikace poškozených dat aplikace
Pokud nově nasazený upgrade aplikace má chybu, která může způsobit poškození dat. Upgrade aplikace může například spustit aktualizaci každého záznamu telefonního čísla ve spolehlivém slovníku s neplatným směrovým číslem oblasti.  V takovém případě budou replikována neplatná telefonní čísla, protože service fabric si není vědom a povahu dat, která jsou uložena.

První věc, kterou musíte udělat poté, co zjistíte takovou nehoráznou chybu, která způsobuje poškození dat, je zmrazit službu na úrovni aplikace a pokud je to možné, upgradovat na verzi kódu aplikace, která nemá chybu.  Však i po opevnění kódu služby data může být stále poškozena, a proto může být nutné obnovit data.  V takových případech nemusí být k obnovení nejnovější zálohy dostatečná, protože nejnovější zálohy mohou být také poškozeny.  Proto musíte najít poslední zálohu, která byla provedena před poškozením dat.

Pokud si nejste jisti, které zálohy jsou poškozené, můžete nasadit nový cluster Service Fabric a obnovit zálohy ohrožených oddílů stejně jako výše uvedený scénář "Odstraněno nebo ztracená služba".  Pro každý oddíl začněte obnovovat zálohy od nejnovějších po nejmenší. Jakmile najdete zálohu, která nemá poškození, přesuňte nebo odstraňte všechny zálohy tohoto oddílu, které byly novější (než tato záloha). Tento postup opakujte pro každý oddíl. Nyní, `OnDataLossAsync` když je volána na oddíl v produkčním clusteru, poslední záloha nalezená v externím úložišti bude ta, kterou vybere výše uvedený proces.

Nyní kroky v části "Odstraněná nebo ztracená služba" lze obnovit stav služby do stavu před buggy kód poškozen stav.

Poznámky:

  - Při obnovení je pravděpodobné, že obnovení zálohy je starší než stav oddílu před ztrátou dat. Z tohoto důvodu byste měli obnovit pouze jako poslední možnost obnovit co nejvíce dat, jak je to možné.
  - Řetězec, který představuje cestu záložní složky a cesty souborů uvnitř záložní složky může být větší než 255 znaků, v závislosti na fabricdataroot cestu a název aplikace název. To může způsobit některé metody `Directory.Move`.NET, `PathTooLongException` jako je , vyvolat výjimku. Jedním z řešení je přímo volat kernel32 API, jako `CopyFile`je .

## <a name="back-up-and-restore-reliable-actors"></a>Zálohování a obnovení spolehlivých herců


Architektura reliable actors framework je postavena na spolehlivých službách. ActorService, který hostuje actor(s) je stavové spolehlivé služby. Proto všechny funkce zálohování a obnovení k dispozici ve spolehlivé služby je také k dispozici spolehlivé objekty actor (s výjimkou chování, které jsou specifické pro zprostředkovatele stavu). Vzhledem k tomu, že zálohy budou převzaty na základě oddílu, stavy pro všechny subjekty v tomto oddílu budou zálohovány (a obnovení je podobné a bude probíhat na základě oddílu). Chcete-li provést zálohování nebo obnovení, vlastník služby by měl vytvořit vlastní třídu služby actor, která je odvozena od třídy ActorService a potom provést zálohování a obnovení podobné spolehlivé služby, jak je popsáno výše v předchozích částech.

```csharp
class MyCustomActorService : ActorService
{
    public MyCustomActorService(StatefulServiceContext context, ActorTypeInformation actorTypeInfo)
          : base(context, actorTypeInfo)
    {
    }
    
    //
    // Method overrides and other code.
    //
}
```

Při vytváření vlastní třídy služby objektu actor je třeba zaregistrovat také při registraci objektu actor.

```csharp
ActorRuntime.RegisterActorAsync<MyActor>(
    (context, typeInfo) => new MyCustomActorService(context, typeInfo)).GetAwaiter().GetResult();
```

Výchozí zprostředkovatel stavu pro `KvsActorStateProvider`spolehlivé objekty actor je . Přírůstkové zálohování není ve `KvsActorStateProvider`výchozím nastavení povoleno pro program . Přírůstkové zálohování můžete `KvsActorStateProvider` povolit vytvořením s příslušným nastavením v jeho konstruktoru a jeho předáním konstruktoru ActorService, jak je znázorněno v následujícím fragmentu kódu:

```csharp
class MyCustomActorService : ActorService
{
    public MyCustomActorService(StatefulServiceContext context, ActorTypeInformation actorTypeInfo)
          : base(context, actorTypeInfo, null, null, new KvsActorStateProvider(true)) // Enable incremental backup
    {
    }
    
    //
    // Method overrides and other code.
    //
}
```

Po povolení přírůstkového zálohování může provedení přírůstkové zálohy selhat s fabricmissingfullbackupexception z jednoho z následujících důvodů a před provedením přírůstkového zálohování (y) budete muset provést úplnou zálohu:

  - Replika nikdy nepřijala úplnou zálohu od doby, kdy se stala primární.
  - Některé záznamy protokolu byly od poslední zálohy zkráceny.

Je-li povoleno `KvsActorStateProvider` přírůstkové zálohování, nepoužívá cyklické vyrovnávací paměti ke správě záznamů protokolu a pravidelně jej zkrátí. Pokud uživatel neprovádí žádnou zálohu po dobu 45 minut, systém automaticky zkrátí záznamy protokolu. Tento interval lze nakonfigurovat `logTruncationIntervalInMinutes` `KvsActorStateProvider` zadáním v konstruktoru (podobně jako při povolení přírůstkové zálohy). Záznamy protokolu může také získat zkrácena, pokud primární replika potřebuje k vytvoření jiné repliky odesláním všech svých dat.

Při provádění obnovení z řetězce zálohování, podobně jako spolehlivé služby, BackupFolderPath by měl obsahovat podadresáře s jedním podadresářem obsahujícím úplné zálohování a další podadresáře obsahující přírůstkové zálohování. Rozhraní API pro obnovení vyvolá FabricException s příslušnou chybovou zprávou, pokud se ověření řetězce zálohování nezdaří. 

> [!NOTE]
> `KvsActorStateProvider`V současné době ignoruje možnost RestorePolicy.Safe. Podpora této funkce je plánována v nadcházející verzi.
> 

## <a name="testing-back-up-and-restore"></a>Testování zálohování a obnovení
Je důležité zajistit, aby byla kritická data zálohována a mohla být obnovena. To lze provést vyvoláním `Start-ServiceFabricPartitionDataLoss` rutiny v prostředí PowerShell, která může vyvolat ztrátu dat v určitém oddílu a otestovat, zda funkce zálohování a obnovení dat pro vaši službu funguje podle očekávání.  Je také možné programově vyvolat ztrátu dat a obnovení z této události také.

> [!NOTE]
> Ukázkovou implementaci funkcí zálohování a obnovení najdete v aplikaci Web Reference App na GitHubu. Další podrobnosti `Inventory.Service` naleznete ve službě.
> 
> 

## <a name="under-the-hood-more-details-on-backup-and-restore"></a>Pod kapotou: další podrobnosti o zálohování a obnovení
Zde jsou některé další podrobnosti o zálohování a obnovení.

### <a name="backup"></a>Zálohování
Správce spolehlivého stavu poskytuje možnost vytvářet konzistentní zálohy bez blokování všech operací čtení nebo zápisu. K tomu využívá kontrolní bod a mechanismus trvalosti protokolu.  Správce spolehlivého stavu trvá fuzzy (lehký) kontrolní body v určitých bodech ke zmírnění tlaku z transakční protokolu a zlepšit dobu obnovení.  Při `BackupAsync` volání, správce spolehlivého stavu instruuje všechny spolehlivé objekty zkopírovat své nejnovější soubory kontrolního bodu do místní záložní složky.  Správce spolehlivého stavu pak zkopíruje všechny záznamy protokolu, počínaje "ukazatel start" na nejnovější záznam protokolu do záložní složky.  Vzhledem k tomu, že všechny záznamy protokolu až do posledního záznamu protokolu jsou zahrnuty v záloze a správce`CommitAsync` spolehlivého stavu zachová protokolování s předstihem zápisu, správce spolehlivého stavu zaručuje, že všechny transakce, které jsou potvrzeny (úspěšně vráceny) jsou zahrnuty do zálohy.

Všechny transakce, které `BackupAsync` potvrdí po byla volána může nebo nemusí být v záloze.  Jakmile je místní záložní složka naplněna platformou (to znamená, že místní zálohování je dokončeno za běhu), je vyvoláno zpětné volání zálohy služby.  Toto zpětné volání je zodpovědné za přesunutí záložní složky do externího umístění, jako je azure storage.

### <a name="restore"></a>Obnovení
Správce spolehlivého stavu poskytuje možnost obnovení ze `RestoreAsync` zálohy pomocí rozhraní API.  
Metoda `RestoreAsync` na `RestoreContext` lze volat pouze `OnDataLossAsync` uvnitř metody.
Bool vrácena `OnDataLossAsync` označuje, zda služba obnovila svůj stav z externího zdroje.
Pokud `OnDataLossAsync` vrátí hodnotu true, Service Fabric znovu vytvoří všechny ostatní repliky z tohoto primárního. Service Fabric zajišťuje, že repliky, které budou přijímat `OnDataLossAsync` volání první přechod na primární roli, ale není udělen stav čtení nebo zápisu stavu.
To znamená, že pro implementátory `RunAsync` StatefulService nebude volána, dokud `OnDataLossAsync` úspěšně nedokončí.
Potom `OnDataLossAsync` bude vyvolána na nové primární.
Dokud služba úspěšně dokončí toto rozhraní API (vrácením true nebo false) a dokončí příslušnou rekonfiguraci, rozhraní API bude nadále volat jeden po druhém.

`RestoreAsync`nejprve klesne všechny existující stav v primární repliky, která byla volána. Potom správce spolehlivého stavu vytvoří všechny spolehlivé objekty, které existují v záložní složce. Dále reliable objekty jsou instruovány k obnovení z jejich kontrolníbody v záložní složce. Nakonec Správce spolehlivého stavu obnoví svůj vlastní stav ze záznamů protokolu v záložní složce a provede obnovení. Jako součást procesu obnovení operace počínaje "počáteční bod", které mají potvrzené záznamy protokolu v záložní složce jsou přehrány spolehlivé objekty. Tento krok zajišťuje, že obnovený stav je konzistentní.

## <a name="next-steps"></a>Další kroky
  - [Reliable Collections](service-fabric-work-with-reliable-collections.md)
  - [Rychlý start spolehlivých služeb](service-fabric-reliable-services-quick-start.md)
  - [Spolehlivé služby oznámení](service-fabric-reliable-services-notifications.md)
  - [Konfigurace spolehlivých služeb](service-fabric-reliable-services-configuration.md)
  - [Odkaz pro vývojáře pro spolehlivé kolekce](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  - [Pravidelné zálohování a obnovení v Azure Service Fabric](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

