---
title: Služba Fabric zálohování a obnovení | Microsoft Docs
description: Rámcová dokumentace pro Service Fabric zálohování a obnovení
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: subramar,zhol
ms.assetid: 91ea6ca4-cc2a-4155-9823-dcbd0b996349
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/6/2017
ms.author: mcoskun
ms.openlocfilehash: 46f9c6129ccf99fb72a285fa4089b7b3f01f7d7b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34643028"
---
# <a name="back-up-and-restore-reliable-services-and-reliable-actors"></a>Zálohování a obnovení Reliable Services a Reliable Actors
Azure Service Fabric je vysoká dostupnost platforma, která replikuje stav napříč několika uzly udržovat tento vysokou dostupnost.  Proto i v případě, že jeden uzel v clusteru selže, služby nadále k dispozici. Při této redundance v vytvořená poskytované platformou pravděpodobně dostatečná pro některé, v některých případech je žádoucí, aby službu, kterou chcete zálohovat data (na externí úložiště).

> [!NOTE]
> Je důležité používat k zálohování a obnovení dat (a otestovat, že funguje podle očekávání), můžete obnovit z ztrátě dat.
> 
> 

Služba může například chcete zálohovat data, aby bylo možné chránit z následujících scénářů:

- V případě trvalé ztrátě celého clusteru Service Fabric.
- Trvalé ztrátě většiny repliky oddílu služby
- Správce chyby, které stav omylem získá odstranit nebo poškozený. Například to může dojít, pokud správce s dostatečná oprávnění omylem odstraní službu.
- Chyby v rámci služby, které dojít k poškození dat. Například tomu může dojít při aktualizace služby kód spustí zápis vadný dat na kolekci spolehlivé. V takovém případě budou muset kód a data mohou být vrácen do předchozího stavu.
- Zpracování dat je offline. Může být vhodné používat offline zpracování dat pro business intelligence, který se stane odděleně od služby, který generuje data.

Funkce zálohování a obnovení umožňuje službám založený na rozhraní API spolehlivé Services k vytvoření a obnovení zálohy. Zálohování rozhraní API poskytované platformou povolit zálohy oddílu služby stavu, bez blokování čtení nebo operace zápisu. Obnovení rozhraní API umožňují oddílu služby stav, který má být obnovena ze zálohy, vybrané.

## <a name="types-of-backup"></a>Typy zálohování
Existují dvě možnosti zálohování: úplné a přírůstkové.
Úplné zálohování je zálohy, která obsahuje všechna data požadovaná k opětovnému vytvoření stavu repliky: kontrolních bodů a všechny záznamy protokolu.
Protože obsahuje kontrolní body a protokol, úplné zálohování lze obnovit samostatně.

Problém s úplnými zálohami mohou nastat, pokud jsou velké kontrolní body.
Repliky, která má 16 GB paměti stavu bude mít například kontrolní body, které přidávají přibližně až 16 GB.
Pokud budeme mít plánovaného bodu obnovení pět minut, replika musí zálohovat každých pět minut.
Pokaždé, když se zálohuje, je nutné zkopírovat 16 GB paměti kontrolní body kromě 50 MB (konfigurovat pomocí `CheckpointThresholdInMB`) za protokolů.

![Příklad úplného zálohování.](media/service-fabric-reliable-services-backup-restore/FullBackupExample.PNG)

Řešení tohoto problému je přírůstkové zálohování, kde zálohování jenom obsahuje záznamy protokolu změněné od posledního zálohování.

![Příklad přírůstkové zálohování.](media/service-fabric-reliable-services-backup-restore/IncrementalBackupExample.PNG)

Vzhledem k tomu, že přírůstkové zálohy jsou pouze změny od poslední zálohy (nezahrnuje kontrolní body), budou většinou poměrně rychlejší, ale je nebylo možné obnovit samostatně.
Obnovit přírůstkové zálohy, celý řetěz záloh je potřeba.
Řetěz záloh je řetěz záloh počínaje úplnou zálohu a následuje číslo souvislý přírůstkových záloh.

## <a name="backup-reliable-services"></a>Zálohování spolehlivé služby
Autor služby má plnou kontrolu, kdy vytvořit zálohování a uložení zálohy.

Pokud chcete spustit zálohování, služba potřebuje k vyvolání funkce zděděného členu `BackupAsync`.  
Zálohování může být vytvořen pouze ze primární repliky a vyžadují stav zápisu udělit oprávnění.

Jak je uvedeno níže, `BackupAsync` přebírá `BackupDescription` objektu, kde jeden určit úplné nebo přírůstkové zálohování, jakož i funkce zpětného volání, `Func<< BackupInfo, CancellationToken, Task<bool>>>` která je volána, když v zálohovací složce byl vytvořen místně a je připravený k přesunu na některé externí úložiště.

```csharp

BackupDescription myBackupDescription = new BackupDescription(backupOption.Incremental,this.BackupCallbackAsync);

await this.BackupAsync(myBackupDescription);

```

Žádost o trvat přírůstkové zálohování může selhat s `FabricMissingFullBackupException`. Tato výjimka označuje děje jednu z následujících akcí:

- repliky nikdy trvá úplné zálohování, protože již není primární,
- Některé záznamy protokolu od poslední zálohy byl zkrácen nebo
- repliky předán `MaxAccumulatedBackupLogSizeInMB` limit.

Uživatelé mohou zvýšit pravděpodobnost moci provést přírůstkové zálohování tak, že konfigurace `MinLogSizeInMB` nebo `TruncationThresholdFactor`.
Všimněte si, že tyto zvýšení hodnoty zvyšuje za využití disku repliky.
Další informace najdete v tématu [spolehlivé konfigurace služeb](service-fabric-reliable-services-configuration.md)

`BackupInfo` poskytuje informace o zálohování, včetně umístění složky pro uložení zálohy modulu runtime (`BackupInfo.Directory`). Funkce zpětného volání můžete přesunout `BackupInfo.Directory` externím obchodu nebo do jiného umístění.  Tato funkce také vrací logickou hodnotu, která určuje, zda bylo možné úspěšně přesunout do cílového umístění v zálohovací složce.

Následující kód ukazuje, jak `BackupCallbackAsync` metoda slouží k nahrání zálohování do úložiště Azure:

```csharp
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, cancellationToken);

    return true;
}
```

V předchozím příkladu `ExternalBackupStore` je ukázka třída, která se používá k rozhraní s Azure Blob storage a `UploadBackupFolderAsync` je metoda, která komprimaci složce a umístí jej do úložiště objektů Blob v Azure.

Poznámky:

  - Může existovat jenom jedna operace zálohování během letu za repliky v daném okamžiku. Více než jeden `BackupAsync` volání současně vyvolá výjimku `FabricBackupInProgressException` omezit aktivních pořadových zálohování na jedno.
  - Pokud repliku převezme v průběhu zálohování, zálohování nemusí byly dokončeny. Po dokončení převzetí z toho důvodu je odpovědnost služby restartujte zálohování vyvoláním `BackupAsync` podle potřeby.

## <a name="restore-reliable-services"></a>Obnovení spolehlivé služby
Obecně platí případech, kdy možná budete muset provést operaci obnovení spadat do jednoho z těchto kategorií:

  - Služba oddílu ke ztrátě dat. Například získá disku pro dvě ze tří repliky pro oddíl (včetně primární repliky) poškozený nebo vymazat. Nový primární možná muset obnovit data ze zálohy.
  - S celou službou bude ztracena. Například správcem odebere s celou službou a proto služby a data je nutné obnovit.
  - Služba replikovaných dat poškozené aplikace (například z důvodu chyb aplikaci). V takovém případě služby je třeba upgradovat nebo vrátit zpět na odebrat příčinu poškození, a -poškození dat musí být obnovena.

Zatímco řada přístupů je možné, nabízíme některé příklady použití `RestoreAsync` obnovení z výše uvedených scénářů.

## <a name="partition-data-loss-in-reliable-services"></a>Oddíl ztrátě dat v spolehlivé služby
V takovém případě by modulu runtime automaticky rozpoznat ztrátě dat a vyvolání `OnDataLossAsync` rozhraní API.

Autor služby musí, proveďte následující kroky k obnovení:

  - Potlačí metodu virtuální základní třídy `OnDataLossAsync`.
  - Najít poslední zálohy v externích umístění, které obsahuje služby zálohování.
  - Stáhněte si nejnovější zálohu (a dekomprimovat zálohování do zálohovací složky, pokud byla komprimována).
  - `OnDataLossAsync` Poskytuje metody `RestoreContext`. Volání `RestoreAsync` rozhraní API na zadaných `RestoreContext`.
  - Vrátí hodnotu PRAVDA, pokud obnovení bylo úspěšné.

Následuje příklad implementace `OnDataLossAsync` metoda:

```csharp
protected override async Task<bool> OnDataLossAsync(RestoreContext restoreCtx, CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    var restoreDescription = new RestoreDescription(backupFolder);

    await restoreCtx.RestoreAsync(restoreDescription);

    return true;
}
```

`RestoreDescription` předaná do `RestoreContext.RestoreAsync` volání obsahuje člena s názvem `BackupFolderPath`.
Při obnovení jedné úplné zálohování, to `BackupFolderPath` musí být nastavena na místní cestu složky, která obsahuje vaše úplné zálohování.
Při obnovování úplnou zálohu a počet přírůstkové zálohování, `BackupFolderPath` musí být nastavena na místní cestu složky, která obsahuje není pouze úplné zálohování, ale také všechny přírůstkové zálohy.
`RestoreAsync` můžete vyvolat volání `FabricMissingFullBackupException` Pokud `BackupFolderPath` zadané neobsahuje úplnou zálohu.
Můžete také vyvolat `ArgumentException` Pokud `BackupFolderPath` má porušený řetězec přírůstkových záloh.
Například, pokud obsahuje úplné zálohování první přírůstkové a třetí přírůstkové zálohování, ale ne druhý přírůstkové zálohování.

> [!NOTE]
> Ve výchozím nastavení do bezpečných RestorePolicy.  To znamená, že `RestoreAsync` rozhraní API se nezdaří s ArgumentException – pokud zjistí, že v zálohovací složce obsahuje stavu, který je starší než nebo rovno stavu obsažené v této replice.  `RestorePolicy.Force` můžete použít tak, aby přeskočil tuto kontrolu zabezpečení. To je zadaný jako součást `RestoreDescription`.
> 

## <a name="deleted-or-lost-service"></a>Odstraněné nebo ke ztrátě služby
Pokud je odebrán služby, musíte nejdřív znovu vytvořit službu předtím, než lze obnovit data.  Je důležité vytvořit službu se stejnou konfigurací, například vytváření oddílů schéma, tak, aby bezproblémově lze obnovit data.  Jakmile je služba, rozhraní API k obnovení dat (`OnDataLossAsync` výše) musí být spuštěna v každém oddílu této služby. Jeden ze způsobů dosažení jde pomocí [FabricClient.TestManagementClient.StartPartitionDataLossAsync](https://msdn.microsoft.com/library/mt693569.aspx) každý oddíl.  

Z tohoto bodu implementace je stejný jako výše uvedené scénáře. Každý oddíl musí obnovit poslední relevantní zálohu z externího úložiště. Jeden přímý přístup paměti je, že ID oddílu může mít nyní změněn, vzhledem k tomu, že modul runtime dynamicky vytvoří ID oddílů. Proto služba potřebuje k uložení název oddílu příslušné informace a služby k identifikaci správné nejnovější zálohu k obnovení z pro každý oddíl.

> [!NOTE]
> Nedoporučuje se používat `FabricClient.ServiceManager.InvokeDataLossAsync` na každý oddíl pro obnovení s celou službou, vzhledem k tomu, které by mohlo poškodit váš stav clusteru.
> 

## <a name="replication-of-corrupt-application-data"></a>Replikace dat poškozený aplikací
Pokud upgradu nově nasazené aplikace obsahuje chyby, které může způsobit poškození dat. Upgrade aplikace může například spuštění aktualizace každých telefonní číslo záznam v spolehlivé slovník s neplatný kód oblasti.  Neplatnými telefonními čísly v takovém případě bude replikován, protože nemá žádné informace o povaze data, která ukládají Service Fabric.

První věc udělat po zjištění takové mimořádně závažných chyb, která způsobuje poškození dat je zmrazení služby na úrovni aplikace a pokud je to možné, upgradujte na verzi aplikační kód, který nemá chyb.  Ale i po kódu služby vyřešen, data mohou být poškozena a proto může třeba obnovit data.  V takových případech nemusí být dostatečná k obnovení poslední zálohu, protože nejnovější zálohy také může být poškozený.  Proto je nutné nalézt poslední zálohy, která byla vytvořená před tu poškozená data.

Pokud si nejste jisti, který zálohy jsou poškozené, můžete nasadit do nového clusteru Service Fabric a obnovit zálohy ovlivněných oddílů stejně jako výše "Odstraněno nebo ke ztrátě služby" scénář.  Pro každý oddíl spuštění obnovení zálohování z nejnovější na nejmenší. Po nalezení zálohy, která nemá poškození přesunutí nebo odstranění tohoto oddílu všechny zálohy, které byly novější (než zálohování). Tento postup opakujte pro každý oddíl. Teď, když `OnDataLossAsync` je volána v oddílu v clusteru výroby, bude poslední zálohy v externím úložišti nalezen jeden zachyceny proces výše.

Nyní, kroky v "odstraněno nebo ke ztrátě služby" části lze použít k obnovení stavu služby stavu, než kód buggy poškozený stav.

Poznámky:

  - Při obnovování, je pravděpodobné, že obnovení ze zálohy je starší než stav oddílu, než data bylo ztraceno. Z toho důvodu obnovíte pouze jako poslední možnost obnovit jako množství dat nejdříve.
  - Řetězec, který představuje cestu ke složce pro zálohování a cest souborů ve složce pro zálohování může být větší než 255 znaků, v závislosti na cesta k adresáři FabricDataRoot a délka názvu typu aplikace. To může způsobit některé metody rozhraní .NET, jako je třeba `Directory.Move`, má být vyvolána `PathTooLongException` výjimky. Jeden řešení je přímo volat rozhraní API kernel32, jako je `CopyFile`.

## <a name="backup-and-restore-reliable-actors"></a>Zálohování a obnovení Reliable Actors


Spolehlivé Framework aktéři je postavená na spolehlivé služby. ActorService, který hostuje actor(s) je stavová služba spolehlivé. Všechny zálohování a obnovení funkce dostupné v spolehlivé služby je proto také k dispozici pro Reliable Actors (s výjimkou chování, které jsou specifické pro zprostředkovatele stavu). Vzhledem k tomu, že zálohování budete přesměrováni na bázi oddílů, se stavy pro všechny účastníky v, že se bude zálohovat oddílu (a obnovení je podobný a proběhne na bázi oddílů). K provedení zálohování a obnovení, by měl vytvořit vlastní objektu actor služby třídu odvozenou od třídy ActorService a potom proveďte vlastník služby zálohování nebo obnovení podobná spolehlivé služby, jak je popsáno výše v předchozích částech.

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

Když vytváříte třídu služby objektu actor vlastní, budete muset zaregistrovat, a při registraci objektu actor.

```csharp
ActorRuntime.RegisterActorAsync<MyActor>(
   (context, typeInfo) => new MyCustomActorService(context, typeInfo)).GetAwaiter().GetResult();
```

Výchozí zprostředkovatel stavu Reliable actors je `KvsActorStateProvider`. Přírůstkové zálohování není povoleno ve výchozím nastavení pro `KvsActorStateProvider`. Přírůstkové zálohování můžete povolit vytvořením `KvsActorStateProvider` s příslušná nastavení v jeho konstruktoru a předejte jí ActorService konstruktoru, jak je znázorněno v následující fragment kódu:

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

Po přírůstkové zálohování, trvá přírůstkové zálohování může selhat s FabricMissingFullBackupException pro jednu z následujících důvodů a budete muset provést úplné zálohy před provedením přírůstkové zálohy:

  - Replika nikdy trvá úplné zálohování, vzhledem k tomu, že jsou primární.
  - Některé záznamy protokolu bylo zkráceno od poslední zálohy.

Pokud je povoleno přírůstkové zálohování, `KvsActorStateProvider` nepoužívá ke správě svoje záznamy protokolu cyklické vyrovnávací paměti a pravidelně se zkrátí. Pokud nedojde k žádné zálohování uživatelem po dobu 45 minut, systém automaticky zkrátí záznamy protokolu. Tento interval můžete nakonfigurovat tak, že zadáte `logTrunctationIntervalInMinutes` v `KvsActorStateProvider` konstruktoru (podobně jako při povolování přírůstkové zálohování). Záznamy protokolu může získat také zkrácen, pokud primární repliky potřebuje k vytvoření jiné repliky odesláním všechna jeho data.

Při provádění obnovení ze zálohy řetěz, spolehlivé služby, podobně jako BackupFolderPath by měl obsahovat podadresářů pomocí jednoho podadresáři obsahující úplnou zálohu a ostatní podadresáře obsahující přírůstkové zálohy. Rozhraní API obnovení vyvolá výjimku FabricException se příslušná chybová zpráva, pokud selže ověření řetěz záloh. 

> [!NOTE]
> `KvsActorStateProvider` možnost RestorePolicy.Safe aktuálně ignoruje. Podpora pro tuto funkci je plánované v nadcházející verzi.
> 

## <a name="testing-backup-and-restore"></a>Testování zálohování a obnovení
Je důležité zajistit, že se záloha důležitých dat a lze obnovit z. To lze provést vyvoláním `Start-ServiceFabricPartitionDataLoss` rutiny v prostředí PowerShell, které může způsobit ztrátu dat v konkrétní oddílu k ověření, zda data zálohování a obnovení funkce pro vaše služba funguje podle očekávání.  Také je možné programově vyvolání ztrátě dat a obnovit ze také tuto událost.

> [!NOTE]
> Můžete najít na ukázkové implementace zálohování a obnovení funkce ve webové aplikaci odkaz na Githubu. Podívejte se prosím na `Inventory.Service` služby další podrobnosti.
> 
> 

## <a name="under-the-hood-more-details-on-backup-and-restore"></a>Pod pokličkou: Další informace o zálohování a obnovení
Zde je některé další informace o zálohování a obnovení.

### <a name="backup"></a>Backup
Správce spolehlivé stavu poskytuje možnost vytvořit konzistentní zálohování bez blokování všech pro čtení nebo zápisu operace. Uděláte to tak ho využívá mechanismus trvalosti kontrolního bodu a protokolu.  Správce spolehlivé stavu trvá přibližné (lightweight) kontrolní body v určitých bodech snížit zatížení z protokol transakcí a vylepšovat dobu obnovení.  Když `BackupAsync` je volána, spolehlivé správce stavu dá pokyn všechny objekty spolehlivé kopírovat jejich nejnovější soubory kontrolního bodu do místní složky zálohování.  Spolehlivé správce stavu pak zkopíruje všechny záznamy protokolu od ukazatele"počáteční" nejnovější záznam protokolu do zálohovací složky.  Vzhledem k tomu, že všechny záznamy protokolu až nejnovější záznam protokolu jsou zahrnuté do zálohy a spolehlivé správce stavu zachovává předběžné protokolování, spolehlivé správce stavu zaručuje, že jsou všechny transakce, potvrzeny (`CommitAsync` úspěšně vrátila) jsou zahrnuté do zálohy.

Jakékoli transakce, která provádí po `BackupAsync` byla volána může nebo nemusí být v záloze.  Jakmile naplněné v zálohovací složce místní platformou (tedy místní bylo zálohování dokončeno modulem runtime), je volána službou zálohování zpětného volání.  Tato zpětné volání je zodpovědná za přesunutí v zálohovací složce do externího umístění například Azure Storage.

### <a name="restore"></a>Obnovení
Správce spolehlivé stavu poskytuje možnost obnovení ze zálohy pomocí `RestoreAsync` rozhraní API.  
`RestoreAsync` Metodu `RestoreContext` lze volat pouze uvnitř `OnDataLossAsync` metoda.
Bool vrácený `OnDataLossAsync` označuje, zda se služba obnovit stav z externího zdroje.
Pokud `OnDataLossAsync` vrátí hodnotu true, Service Fabric se znovu vytvořit všechny ostatní repliky z tomuto primárnímu. Service Fabric zajišťuje, že repliky, které obdrží `OnDataLossAsync` volání první přechod do primární role, ale nejsou uděleno číst stav nebo zapisovat stav.
To vyplývá, že pro implementátory StatefulService `RunAsync` dokud nebude volána `OnDataLossAsync` úspěšně dokončí.
Potom `OnDataLossAsync` bude volána pro nový primární.
Dokud služby dokončí toto rozhraní API úspěšně (vrací hodnotu true nebo false) a dokončí příslušné změny konfigurace, rozhraní API bude udržovat volána po jednom.

`RestoreAsync` nejprve zahodí všechny existující stav v primární replice, která byla volána na. Spolehlivé správce stavu vytvoří spolehlivé objekty, které existují ve složce zálohy. Dále jsou spolehlivé objekty pokyn k obnovení z jejich kontrolní body ve složce pro zálohování. Nakonec spolehlivé správce stavu obnoví vlastní stavu ze záznamů protokolu ve složce pro zálohování a provede obnovení. Jako součást procesu obnovení operace od "výchozí bod", které mají potvrzení záznamů protokolu ve složce zálohování se přehraje na virtuálním pevném spolehlivé objekty. Tento krok zajistí, že obnovené stav je konzistentní.

## <a name="next-steps"></a>Další postup
  - [Reliable Collections](service-fabric-work-with-reliable-collections.md)
  - [Spolehlivé služby rychlý start](service-fabric-reliable-services-quick-start.md)
  - [Spolehlivé služby oznámení](service-fabric-reliable-services-notifications.md)
  - [Spolehlivé služby konfigurace](service-fabric-reliable-services-configuration.md)
  - [Referenční informace pro vývojáře pro spolehlivé kolekce](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  - [Pravidelné zálohování a obnovení v Azure Service Fabric](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

