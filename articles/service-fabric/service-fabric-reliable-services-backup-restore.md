---
title: Service Fabric zálohování a obnovení
description: Koncepční dokumentace pro Service Fabric zálohování a obnovení, služba pro konfiguraci zálohování spolehlivých stavových služeb a Reliable Actors.
author: mcoskun
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: mcoskun
ms.openlocfilehash: 712069a34b6bc5d8aa4bcbab3fdbf9fc9cd8958b
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645544"
---
# <a name="backup-and-restore-reliable-services-and-reliable-actors"></a>Zálohování a obnovení Reliable Services a Reliable Actors
Azure Service Fabric je platforma s vysokou dostupností, která replikuje stav napříč několika uzly a udržuje tak vysokou dostupnost.  Proto i v případě, že jeden uzel v clusteru dojde k chybě, služby budou nadále k dispozici. I když je tato integrovaná redundance poskytovaná platformou dostatečná, může být v některých případech žádoucí, aby služba mohla zálohovat data (do externího úložiště).

> [!NOTE]
> Je důležité používat k zálohování a obnovení dat (a otestovat, že funguje podle očekávání), můžete obnovit z ztrátě dat.
> 

> [!NOTE]
> Společnost Microsoft doporučuje používat pro konfiguraci zálohování spolehlivých stavových služeb a Reliable Actors [pravidelné zálohování a obnovování](service-fabric-backuprestoreservice-quickstart-azurecluster.md) dat. 
> 


Například služba může chtít zálohovat data, aby se chránila z následujících scénářů:

- V případě trvalé ztráty celého Service Fabric clusteru.
- Trvalá ztráta většiny replik oddílu služby
- Chyby správy, při kterých se stav omylem odstranil nebo je poškozen. K tomu může dojít například v případě, že správce s dostatečným oprávněním omylem odstraní službu.
- Chyby ve službě, které způsobují poškození dat. K tomu může dojít například v případě, že upgrade kódu služby začne psát vadná data do spolehlivé kolekce. V takovém případě je možné, že kód i data budou muset být vráceny do předchozího stavu.
- Zpracování offline dat. Může být vhodné mít offline zpracování dat pro business intelligence, ke kterým dochází nezávisle na službě, která data generuje.

Funkce zálohování a obnovení umožňuje službám postaveným na rozhraní Reliable Services API vytvářet a obnovovat zálohy. Rozhraní API pro zálohování poskytovaná platformou umožňují zálohování stavu oddílu služby bez blokování operací čtení nebo zápisu. Rozhraní API pro obnovení umožňují obnovit stav oddílu služby ze zvolené zálohy.

## <a name="types-of-backup"></a>Typy zálohování
Existují dvě možnosti zálohování: úplné a přírůstkové.
Úplná záloha je záloha, která obsahuje všechna data potřebná k opětovnému vytvoření stavu repliky: kontrolní body a všechny záznamy protokolu.
Vzhledem k tomu, že obsahuje kontrolní body a protokol, může úplné zálohování obnovit sám sebe.

Problém s úplnými zálohami nastane, pokud jsou kontrolní body velké.
Například replika, která má 16 GB stavu, bude obsahovat kontrolní body, které přidají přibližně 16 GB.
Pokud máme v cíli bodu obnovení pět minut, replika musí být zálohována každých pět minut.
Pokaždé, když ho zálohuje, potřebuje zkopírovat 16 GB kontrolních bodů kromě 50 MB (nastavitelná pomocí `CheckpointThresholdInMB`) s využitím protokolů.

![Příklad úplného zálohování](media/service-fabric-reliable-services-backup-restore/FullBackupExample.PNG)

Řešením tohoto problému je přírůstkové zálohování, kde zálohování obsahuje pouze změněné záznamy protokolů od poslední zálohy.

![Příklad přírůstkového zálohování](media/service-fabric-reliable-services-backup-restore/IncrementalBackupExample.PNG)

Vzhledem k tomu, že přírůstkové zálohy jsou od poslední zálohy pouze změny (nezahrnují kontrolní body), jsou obvykle rychlejší, ale nelze je obnovit sami.
Chcete-li obnovit přírůstkovou zálohu, je nutné zadat celý řetězec zálohy.
Řetězec zálohy představuje řetězec záloh počínaje úplnou zálohou a následovaný několika souvislými přírůstkových zálohami.

## <a name="backup-reliable-services"></a>Reliable Services zálohy
Autor služby má úplnou kontrolu nad tím, kdy se mají ukládat zálohy a kam se budou ukládat zálohy.

Chcete-li spustit zálohování, musí služba vyvolat zděděnou členskou funkci `BackupAsync`.  
Zálohy se dají provádět jenom z primárních replik a vyžadují udělení stavu zápisu.

Jak je vidět níže, `BackupAsync` převezme v objektu `BackupDescription`, kde jedna může zadat úplnou nebo přírůstkovou zálohu, a také funkci zpětného volání, která se vyvolá `Func<< BackupInfo, CancellationToken, Task<bool>>>`, když se složka zálohování vytvoří místně a je připravena k jejímu přesunutí do některého externího úložiště.

```csharp

BackupDescription myBackupDescription = new BackupDescription(backupOption.Incremental,this.BackupCallbackAsync);

await this.BackupAsync(myBackupDescription);

```

Požadavek na převzetí přírůstkové zálohy může při `FabricMissingFullBackupException`selhat. Tato výjimka označuje, že se děje jedna z následujících věcí:

- replika nikdy netrvalou úplnou zálohu, protože se stala primární,
- Některé záznamy protokolu od poslední zálohy byly zkráceny nebo
- replika prošla omezením `MaxAccumulatedBackupLogSizeInMB`.

Uživatelé mohou zvýšit pravděpodobnost, že bude možné provádět přírůstkové zálohování konfigurací `MinLogSizeInMB` nebo `TruncationThresholdFactor`.
Zvýšením počtu těchto hodnot se zvýší využití disku na jednu repliku.
Další informace najdete v tématu [konfigurace Reliable Services](service-fabric-reliable-services-configuration.md) .

`BackupInfo` poskytuje informace týkající se zálohování, včetně umístění složky, do které modul runtime zálohu uložil (`BackupInfo.Directory`). Funkce zpětného volání může přesunout `BackupInfo.Directory` do externího úložiště nebo do jiného umístění.  Tato funkce také vrátí logickou hodnotu, která označuje, zda byla schopna úspěšně přesunout složku záloha do jejího cílového umístění.

Následující kód ukazuje, jak lze metodu `BackupCallbackAsync` použít k nahrání zálohy do Azure Storage:

```csharp
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, cancellationToken);

    return true;
}
```

V předchozím příkladu je `ExternalBackupStore` ukázkovou třídou, která se používá k rozhraní služby Azure Blob Storage, a `UploadBackupFolderAsync` je metoda, která komprimuje složku a umístí ji do úložiště objektů BLOB v Azure.

Poznámky:

  - V každém okamžiku může být v každém z těchto replik pouze jedna operace zálohování za sekundu. Více než jedna `BackupAsync` volání v jednom okamžiku vyvolá `FabricBackupInProgressException` k omezení záložních záloh na jednu.
  - Pokud replika převezme při selhání během zálohování, zálohování se nemusí dokončit. Po dokončení převzetí služeb při selhání je tedy zodpovědnost služby na restartování zálohování vyvoláním `BackupAsync` podle potřeby.

## <a name="restore-reliable-services"></a>Obnovit Reliable Services
Obecně platí, že případy, kdy může být nutné provést operaci obnovení, spadají do jedné z těchto kategorií:

  - Služba oddílu ke ztrátě dat. Například disk pro dvě z následujících replik pro oddíl (včetně primární repliky) je poškozený nebo smazáný. Nová primární aplikace může potřebovat obnovit data ze zálohy.
  - Dojde ke ztrátě celé služby. Správce například odebere celou službu, což znamená, že služba a data se musí obnovit.
  - Služba replikuje poškozená aplikační data (například kvůli chybě aplikace). V takovém případě musí být služba upgradována nebo vrácena, aby bylo možné odstranit příčinu poškození, a data, která nejsou poškozena, musí být obnovena.

I když je možné využít spoustu přístupů, nabízíme několik příkladů použití `RestoreAsync` k obnovení z výše uvedených scénářů.

## <a name="partition-data-loss-in-reliable-services"></a>Rozdělení dat do oddílů v Reliable Services
V tomto případě modul runtime automaticky detekuje ztrátu dat a vyvolá rozhraní `OnDataLossAsync` API.

Autor služby musí provést následující kroky, aby obnovil:

  - Přepište metodu virtuální základní třídy `OnDataLossAsync`.
  - Vyhledejte nejnovější zálohu v externím umístění, které obsahuje zálohy služby.
  - Stáhněte si nejnovější zálohu (a dekomprimujte zálohu do zálohovací složky, pokud byla komprimovaná).
  - Metoda `OnDataLossAsync` poskytuje `RestoreContext`. Volání rozhraní API `RestoreAsync` v poskytnuté `RestoreContext`.
  - Vrátí hodnotu true, pokud bylo obnovení úspěšné.

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

`RestoreDescription` předaných do volání `RestoreContext.RestoreAsync` obsahuje člen s názvem `BackupFolderPath`.
Při obnovování jediné úplné zálohy by tato `BackupFolderPath` měla být nastavená na místní cestu ke složce, která obsahuje úplnou zálohu.
Při obnovování úplné zálohy a počtu přírůstkových záloh by `BackupFolderPath` měla být nastavena na místní cestu ke složce, která obsahuje pouze úplné zálohování, ale také všechny přírůstkové zálohy.
`RestoreAsync` volání může vyvolat `FabricMissingFullBackupException`, pokud poskytnutý `BackupFolderPath` neobsahuje úplnou zálohu.
Může také vyvolat `ArgumentException`, pokud `BackupFolderPath` má porušený řetězec přírůstkových záloh.
Například pokud obsahuje úplnou zálohu, první přírůstkové a třetí přírůstkové zálohování, ale ne druhou přírůstkovou zálohu.

> [!NOTE]
> RestorePolicy je ve výchozím nastavení standardně nastavená na hodnotu Safe.  To znamená, že rozhraní `RestoreAsync` API selže s chybou ArgumentException, pokud zjistí, že složka pro zálohování obsahuje stav, který je starší než nebo roven stavu obsaženému v této replice.  `RestorePolicy.Force` lze použít k přeskočení této kontroly bezpečnosti. Tento parametr je určený jako součást `RestoreDescription`.
> 

## <a name="deleted-or-lost-service"></a>Odstraněná nebo ztracená služba
Je-li služba odebrána, je nutné nejprve znovu vytvořit službu, aby bylo možné data obnovit.  Je důležité vytvořit službu se stejnou konfigurací, například pomocí schématu dělení, aby bylo možné data snadno obnovit.  Jakmile je služba spuštěná, musí se v každém oddílu této služby vyvolávat rozhraní API pro obnovení dat (`OnDataLossAsync` výše). Jedním ze způsobů, jak toho dosáhnout, je použít [FabricClient. TestManagementClient. StartPartitionDataLossAsync](https://msdn.microsoft.com/library/mt693569.aspx) na každém oddílu.  

Od tohoto okamžiku je implementace stejná jako u výše uvedeného scénáře. Každý oddíl musí obnovit poslední relevantní zálohu z externího úložiště. Jedna výstraha znamená, že ID oddílu se teď může změnit, protože modul runtime vytvoří ID oddílů dynamicky. Proto služba potřebuje Uložit příslušné informace o oddílu a název služby k identifikaci správné poslední zálohy pro obnovení pro každý oddíl.

> [!NOTE]
> Pro obnovení celé služby se nedoporučuje používat `FabricClient.ServiceManager.InvokeDataLossAsync` v každém oddílu, protože to může poškodit stav clusteru.
> 

## <a name="replication-of-corrupt-application-data"></a>Replikace poškozených dat aplikací
Pokud nově nasazená aplikace obsahuje chybu, může to způsobit poškození dat. Upgrade aplikace může například začít aktualizovat každý záznam telefonního čísla ve spolehlivém slovníku s neplatným kódem oblasti.  V takovém případě se neplatná telefonní čísla replikují, protože Service Fabric nemá informace o povaze uložených dat.

První věc, kterou je třeba provést po zjištění takové chyby egregious, která způsobí poškození dat, je zmrazení služby na úrovni aplikace a pokud je to možné, upgraduje na verzi kódu aplikace, která chybu neobsahuje.  I po tom, co je kód služby opraven, mohou být data stále poškozena, a proto může být nutné obnovit data.  V takových případech nemusí být stačit obnovit poslední zálohu, protože může dojít také k poškození nejnovějších záloh.  Proto je nutné najít poslední zálohu, která byla provedena před poškozením dat.

Pokud si nejste jistí, které zálohy jsou poškozené, můžete nasadit nový cluster Service Fabric a obnovit zálohy ovlivněných oddílů stejně jako u výše uvedeného scénáře "odstraněno nebo ztracená služba".  Pro každý oddíl Začněte obnovovat zálohy od nejnovějších k nejméně. Jakmile najdete zálohu, která nemá poškození, přesuňte nebo odstraňte všechny zálohy tohoto oddílu, které byly novější (než tato záloha). Tento postup opakujte pro každý oddíl. Když se teď `OnDataLossAsync` volá na oddíl v produkčním clusteru, poslední záloha nalezená v externím úložišti bude ta, kterou vybral výše uvedený proces.

Nyní je možné pomocí oddílu "Odstraněná nebo ztracená služba" obnovit stav služby do stavu před tím, než kód ladění ztratí stav.

Poznámky:

  - Při obnovení je pravděpodobné, že obnovená záloha je starší než stav oddílu před ztrátou dat. Z tohoto důvodu byste měli provést obnovení jenom jako poslední možnost obnovení co nejvíc dat.
  - Řetězec, který představuje cestu zálohovací složky a cesty souborů uvnitř zálohovací složky, může být větší než 255 znaků v závislosti na délce FabricDataRoot cesty a názvu typu aplikace. To může způsobit, že některé metody rozhraní .NET, například `Directory.Move`, vyvolávají výjimku `PathTooLongException`. Jedním z alternativních řešení je přímé volání rozhraní Kernel32 API, například `CopyFile`.

## <a name="back-up-and-restore-reliable-actors"></a>Zálohování a obnovení Reliable Actors


Rozhraní Reliable Actors Framework je postaveno nad Reliable Services. ActorService, který je hostitelem objektů Actor, je spolehlivě stavová služba. Všechny funkce zálohování a obnovení, které jsou k dispozici v Reliable Services, jsou také k dispozici pro Reliable Actors (s výjimkou chování, které jsou specifické pro poskytovatele stavu). Vzhledem k tomu, že se zálohy budou provádět na jednotlivých oddílech, budou všechny stavy všech objektů actor v tomto oddílu zálohovány (a obnovení je podobné a provede se v jednotlivých oddílech). K provedení zálohování a obnovení by měl vlastník služby vytvořit vlastní třídu služby objektu actor, která je odvozena od třídy ActorService, a pak provést zálohování a obnovení podobně jako Reliable Services, jak je popsáno výše v předchozích částech.

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

Když vytvoříte vlastní třídu služby objektu actor, je nutné ji zaregistrovat i při registraci objektu actor.

```csharp
ActorRuntime.RegisterActorAsync<MyActor>(
    (context, typeInfo) => new MyCustomActorService(context, typeInfo)).GetAwaiter().GetResult();
```

Výchozím zprostředkovatelem stavu pro Reliable Actors je `KvsActorStateProvider`. Přírůstkové zálohování není ve výchozím nastavení pro `KvsActorStateProvider`povolené. Přírůstkové zálohování můžete povolit vytvořením `KvsActorStateProvider` s příslušným nastavením ve svém konstruktoru a následným předáním do konstruktoru ActorService, jak je znázorněno v následujícím fragmentu kódu:

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

Po povolení přírůstkového zálohování může přírůstkové zálohování selhat s FabricMissingFullBackupException z některého z následujících důvodů a před provedením přírůstkových záloh budete muset provést úplnou zálohu:

  - Replika nikdy netrvalou úplnou zálohu, protože se stala primární.
  - Některé záznamy protokolu byly od posledního zálohování zkráceny.

Když je zapnuté přírůstkové zálohování, `KvsActorStateProvider` nepoužívá k řízení záznamů protokolů cyklickou vyrovnávací paměť a pravidelně ji zkrátí. Pokud uživatel neprovádí zálohu po dobu 45 minut, systém automaticky zkrátí záznamy protokolu. Tento interval lze nakonfigurovat zadáním `logTruncationIntervalInMinutes` v konstruktoru `KvsActorStateProvider` (podobně jako při povolení přírůstkového zálohování). V případě, že primární replika potřebuje sestavit další repliku odesláním všech svých dat, mohou být záznamy protokolu také zkráceny.

Při obnovení ze záložního řetězce, podobně jako Reliable Services, by měl BackupFolderPath obsahovat podadresáře s jedním podadresářem obsahujícím úplné zálohování a další podadresáře, které obsahují přírůstkové zálohování. Pokud selže ověření řetězu zálohování, vrátí rozhraní API pro obnovení výjimku FabricException s příslušnou chybovou zprávou. 

> [!NOTE]
> `KvsActorStateProvider` aktuálně ignoruje možnost RestorePolicy. safe. Podpora pro tuto funkci se plánuje v nadcházející verzi.
> 

## <a name="testing-back-up-and-restore"></a>Testování zálohování a obnovení
Je důležité zajistit, aby byla důležitá data zálohována a mohla by být obnovena z. To se dá udělat vyvoláním rutiny `Start-ServiceFabricPartitionDataLoss` v prostředí PowerShell, která může způsobit ztrátu dat v určitém oddílu a otestovat, jestli funkce zálohování a obnovení dat pro vaši službu fungují podle očekávání.  Také je možné programově vyvolat ztráty a obnovení dat z této události.

> [!NOTE]
> Ukázkovou implementaci funkcí zálohování a obnovení můžete najít v aplikaci Web reference na GitHubu. Další podrobnosti najdete ve službě `Inventory.Service`.
> 
> 

## <a name="under-the-hood-more-details-on-backup-and-restore"></a>V rámci digestoře: Další podrobnosti o zálohování a obnovení
Tady jsou další podrobnosti o zálohování a obnovení.

### <a name="backup"></a>Backup
Správce spolehlivého stavu poskytuje možnost vytvářet konzistentní zálohy bez blokování operací čtení a zápisu. Pokud to chcete udělat, využívá mechanismus kontrolního bodu a protokolu trvalosti.  Správce spolehlivého stavu provádí v určitých bodech nepřibližné (zjednodušené) kontrolní body, které zbavují tlak z transakčního protokolu a zlepšují dobu obnovení.  Když se zavolá `BackupAsync`, správce spolehlivého stavu vydá pokyn všem spolehlivým objektům ke zkopírování jejich nejnovějších souborů kontrolních bodů do místní záložní složky.  Správce Reliable State pak zkopíruje všechny záznamy protokolu počínaje od "ukazatele" Start "k nejnovějšímu záznamu protokolu do složky pro zálohování.  Vzhledem k tomu, že všechny záznamy protokolu až do nejnovějšího záznamu protokolu jsou součástí zálohování a správce spolehlivého stavu zachovává protokolování před zápisem, zaručuje správce spolehlivého stavu, že všechny potvrzené transakce (`CommitAsync` se úspěšně vrátily) do zálohy.

Všechny transakce, které po `BackupAsync` zavolaly, mohou nebo nemusí být v záloze.  Po naplnění místní složky zálohování platformou (která znamená, že je místní záloha dokončená modulem runtime) se vyvolá zpětné volání zálohy služby.  Toto zpětné volání zodpovídá za přesunutí zálohovací složky do externího umístění, jako je například Azure Storage.

### <a name="restore"></a>Obnovení
Správce Reliable State nabízí možnost obnovení ze zálohy pomocí rozhraní `RestoreAsync` API.  
Metodu `RestoreAsync` v `RestoreContext` lze volat pouze uvnitř metody `OnDataLossAsync`.
Hodnota bool vrácená funkcí `OnDataLossAsync` určuje, zda služba obnovila svůj stav z externího zdroje.
Pokud `OnDataLossAsync` vrátí hodnotu true, Service Fabric znovu sestaví všechny ostatní repliky z tohoto primárního. Service Fabric zajistí, aby repliky, které obdrží `OnDataLossAsync`, přešly nejprve na primární roli, ale neudělily stav čtení nebo stav zápisu.
To znamená, že pro StatefulService Implements nebude volána `RunAsync`, dokud `OnDataLossAsync` dokončena úspěšně.
Pak se `OnDataLossAsync` vyvolá na novém primárním počítači.
Dokud služba toto rozhraní API úspěšně dokončí (vrátí hodnotu true nebo false) a dokončí příslušnou novou konfiguraci, rozhraní API se v tuto chvíli bude dál volat.

`RestoreAsync` nejprve přenechá všechny existující stavy v primární replice, na které byla volána. Správce Reliable State pak vytvoří všechny spolehlivé objekty, které existují ve složce Backup. V dalším kroku jsou spolehlivé objekty pokyny k obnovení ze svých kontrolních bodů ve složce Backup. Nakonec správce spolehlivého stavu obnoví svůj vlastní stav ze záznamů protokolu ve složce zálohy a provede obnovení. V rámci procesu obnovení jsou operace začínající od "počátečního bodu", které mají potvrzené záznamy protokolu v zálohovací složce, přehrávány do spolehlivých objektů. Tento krok zajistí, že obnovený stav je konzistentní.

## <a name="next-steps"></a>Další kroky
  - [Reliable Collections](service-fabric-work-with-reliable-collections.md)
  - [Rychlý Start Reliable Services](service-fabric-reliable-services-quick-start.md)
  - [Oznámení Reliable Services](service-fabric-reliable-services-notifications.md)
  - [Konfigurace Reliable Services](service-fabric-reliable-services-configuration.md)
  - [Referenční informace pro vývojáře pro spolehlivé kolekce](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  - [Pravidelné zálohování a obnovení v Azure Service Fabric](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

