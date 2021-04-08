---
title: Zálohování a obnovení Service Fabric
description: Koncepční dokumentace pro Service Fabric zálohování a obnovení, služba pro konfiguraci zálohování spolehlivých stavových služeb a Reliable Actors.
author: mcoskun
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: mcoskun
ms.custom: devx-track-csharp
ms.openlocfilehash: 2674d1285544e4bc9b6fcb3d0b2e6f4b607786a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791607"
---
# <a name="backup-and-restore-reliable-services-and-reliable-actors"></a>Zálohování a obnovení Reliable Services a Reliable Actors
Azure Service Fabric je platforma s vysokou dostupností, která replikuje stav napříč několika uzly a udržuje tak vysokou dostupnost.  Proto i v případě, že jeden uzel v clusteru dojde k chybě, služby budou nadále k dispozici. I když je tato integrovaná redundance poskytovaná platformou dostatečná, může být v některých případech žádoucí, aby služba mohla zálohovat data (do externího úložiště).

> [!NOTE]
> Je důležité zálohovat a obnovovat data (a otestovat, jestli funguje podle očekávání), abyste se mohli zotavit ze scénářů ztráty dat.
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
Pokaždé, když ho zálohuje, potřebuje zkopírovat 16 GB kontrolních bodů kromě 50 MB (konfigurovatelných pomocí) pro vydaných `CheckpointThresholdInMB` protokolů.

![Příklad úplného zálohování](media/service-fabric-reliable-services-backup-restore/FullBackupExample.PNG)

Řešením tohoto problému je přírůstkové zálohování, kde zálohování obsahuje pouze změněné záznamy protokolů od poslední zálohy.

![Příklad přírůstkového zálohování](media/service-fabric-reliable-services-backup-restore/IncrementalBackupExample.PNG)

Vzhledem k tomu, že přírůstkové zálohy jsou od poslední zálohy pouze změny (nezahrnují kontrolní body), jsou obvykle rychlejší, ale nelze je obnovit sami.
Chcete-li obnovit přírůstkovou zálohu, je nutné zadat celý řetězec zálohy.
Řetězec zálohy představuje řetězec záloh počínaje úplnou zálohou a následovaný několika souvislými přírůstkových zálohami.

## <a name="backup-reliable-services"></a>Reliable Services zálohy
Autor služby má úplnou kontrolu nad tím, kdy se mají ukládat zálohy a kam se budou ukládat zálohy.

Chcete-li spustit zálohování, musí služba vyvolat zděděnou členskou funkci `BackupAsync` .  
Zálohy se dají provádět jenom z primárních replik a vyžadují udělení stavu zápisu.

Jak je vidět níže, `BackupAsync` přebírá `BackupDescription` objekt, kde jedna může zadat úplnou nebo přírůstkovou zálohu, a také funkci zpětného volání, `Func<< BackupInfo, CancellationToken, Task<bool>>>` která je vyvolána, když byla složka zálohy vytvořena místně a je připravena k jejímu přesunutí do některého externího úložiště.

```csharp

BackupDescription myBackupDescription = new BackupDescription(BackupOption.Incremental,this.BackupCallbackAsync);

await this.BackupAsync(myBackupDescription);

```

Požadavek na převzetí přírůstkové zálohy může selhat s `FabricMissingFullBackupException` . Tato výjimka označuje, že se děje jedna z následujících věcí:

- replika nikdy netrvalou úplnou zálohu, protože se stala primární,
- Některé záznamy protokolu od poslední zálohy byly zkráceny nebo
- replika prošla `MaxAccumulatedBackupLogSizeInMB` limitem.

Uživatelé mohou zvýšit pravděpodobnost, že bude možné provádět přírůstkové zálohování konfigurací `MinLogSizeInMB` nebo `TruncationThresholdFactor` .
Zvýšením počtu těchto hodnot se zvýší využití disku na jednu repliku.
Další informace najdete v tématu [konfigurace Reliable Services](service-fabric-reliable-services-configuration.md) .

`BackupInfo` obsahuje informace týkající se zálohování, včetně umístění složky, do které modul runtime uložil zálohu ( `BackupInfo.Directory` ). Funkce zpětného volání může přesunout `BackupInfo.Directory` do externího úložiště nebo do jiného umístění.  Tato funkce také vrátí logickou hodnotu, která označuje, zda byla schopna úspěšně přesunout složku záloha do jejího cílového umístění.

Následující kód ukazuje, jak `BackupCallbackAsync` lze metodu použít k nahrání zálohy do Azure Storage:

```csharp
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, cancellationToken);

    return true;
}
```

V předchozím příkladu `ExternalBackupStore` je ukázková třída, která se používá k rozhraní služby Azure Blob Storage, a `UploadBackupFolderAsync` je metoda, která komprimuje složku a umístí ji do úložiště objektů BLOB v Azure.

Poznámky:

  - V každém okamžiku může být v každém z těchto replik pouze jedna operace zálohování za sekundu. Více než jedno `BackupAsync` volání v jednom okamžiku vygeneruje `FabricBackupInProgressException` , aby se omezily záložní zálohy na jeden.
  - Pokud replika převezme při selhání během zálohování, zálohování se nemusí dokončit. Po dokončení převzetí služeb při selhání je tedy zodpovědnost služby pro restartování zálohování vyvoláním `BackupAsync` podle potřeby.

## <a name="restore-reliable-services"></a>Obnovit Reliable Services
Obecně platí, že případy, kdy může být nutné provést operaci obnovení, spadají do jedné z těchto kategorií:

  - Oddíl služby ztratil data. Například disk pro dvě z následujících replik pro oddíl (včetně primární repliky) je poškozený nebo smazáný. Nová primární aplikace může potřebovat obnovit data ze zálohy.
  - Dojde ke ztrátě celé služby. Správce například odebere celou službu, což znamená, že služba a data se musí obnovit.
  - Služba replikuje poškozená aplikační data (například kvůli chybě aplikace). V takovém případě musí být služba upgradována nebo vrácena, aby bylo možné odstranit příčinu poškození, a data, která nejsou poškozena, musí být obnovena.

I když je možné mnoho přístupů, nabízíme několik příkladů použití `RestoreAsync` pro obnovení z výše uvedených scénářů.

## <a name="partition-data-loss-in-reliable-services"></a>Rozdělení dat do oddílů v Reliable Services
V tomto případě modul runtime automaticky detekuje ztrátu dat a vyvolá `OnDataLossAsync` rozhraní API.

Autor služby musí provést následující kroky, aby obnovil:

  - Přepsat metodu virtuální základní třídy `OnDataLossAsync` .
  - Vyhledejte nejnovější zálohu v externím umístění, které obsahuje zálohy služby.
  - Stáhněte si nejnovější zálohu (a dekomprimujte zálohu do zálohovací složky, pokud byla komprimovaná).
  - `OnDataLossAsync`Metoda poskytuje `RestoreContext` . Zavolejte `RestoreAsync` rozhraní API na poskytnutou adresu `RestoreContext` .
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

`RestoreDescription` předání do `RestoreContext.RestoreAsync` volání obsahuje člen s názvem `BackupFolderPath` .
Při obnovování jediné úplné zálohy `BackupFolderPath` by měla být tato nastavení nastavena na místní cestu ke složce, která obsahuje úplnou zálohu.
Při obnovování úplné zálohy a počtu přírůstkových záloh `BackupFolderPath` byste měli nastavit místní cestu ke složce, která obsahuje pouze úplné zálohování, ale také všechny přírůstkové zálohy.
`RestoreAsync` volání může být vyvoláno, `FabricMissingFullBackupException` Pokud `BackupFolderPath` zadané neobsahuje úplnou zálohu.
Může také vyvolat výjimku, `ArgumentException` Pokud `BackupFolderPath` má porušený řetězec přírůstkových záloh.
Například pokud obsahuje úplnou zálohu, první přírůstkové a třetí přírůstkové zálohování, ale ne druhou přírůstkovou zálohu.

> [!NOTE]
> RestorePolicy je ve výchozím nastavení standardně nastavená na hodnotu Safe.  To znamená, že `RestoreAsync` rozhraní API selže s chybou ArgumentException, pokud zjistí, že složka pro zálohování obsahuje stav, který je starší než nebo roven stavu obsaženému v této replice.  `RestorePolicy.Force` dá se použít k přeskočení této kontroly bezpečnosti. Tento parametr je určen jako součást `RestoreDescription` .
> 

## <a name="deleted-or-lost-service"></a>Odstraněná nebo ztracená služba
Je-li služba odebrána, je nutné nejprve znovu vytvořit službu, aby bylo možné data obnovit.  Je důležité vytvořit službu se stejnou konfigurací, například pomocí schématu dělení, aby bylo možné data snadno obnovit.  Jakmile je služba spuštěná, `OnDataLossAsync` musí se v každém oddílu této služby vyvolávat rozhraní API pro obnovení dat (výše). Jedním ze způsobů, jak toho dosáhnout, je použít [FabricClient. TestManagementClient. StartPartitionDataLossAsync](/dotnet/api/system.fabric.fabricclient.testmanagementclient#System_Fabric_FabricClient_TestManagementClient_StartPartitionDataLossAsync_System_Guid_System_Fabric_PartitionSelector_System_Fabric_DataLossMode_) na každém oddílu.  

Od tohoto okamžiku je implementace stejná jako u výše uvedeného scénáře. Každý oddíl musí obnovit poslední relevantní zálohu z externího úložiště. Jedna výstraha znamená, že ID oddílu se teď může změnit, protože modul runtime vytvoří ID oddílů dynamicky. Proto služba potřebuje Uložit příslušné informace o oddílu a název služby k identifikaci správné poslední zálohy pro obnovení pro každý oddíl.

> [!NOTE]
> Pro obnovení celé služby se nedoporučuje používat `FabricClient.ServiceManager.InvokeDataLossAsync` na všech oddílech, protože to může poškodit stav clusteru.
> 

## <a name="replication-of-corrupt-application-data"></a>Replikace poškozených dat aplikací
Pokud nově nasazená aplikace obsahuje chybu, může to způsobit poškození dat. Upgrade aplikace může například začít aktualizovat každý záznam telefonního čísla ve spolehlivém slovníku s neplatným kódem oblasti.  V takovém případě se neplatná telefonní čísla replikují, protože Service Fabric nemá informace o povaze uložených dat.

První věc, kterou je třeba provést po zjištění takové chyby egregious, která způsobí poškození dat, je zmrazení služby na úrovni aplikace a pokud je to možné, upgraduje na verzi kódu aplikace, která chybu neobsahuje.  I po tom, co je kód služby opraven, mohou být data stále poškozena, a proto může být nutné obnovit data.  V takových případech nemusí být stačit obnovit poslední zálohu, protože může dojít také k poškození nejnovějších záloh.  Proto je nutné najít poslední zálohu, která byla provedena před poškozením dat.

Pokud si nejste jistí, které zálohy jsou poškozené, můžete nasadit nový cluster Service Fabric a obnovit zálohy ovlivněných oddílů stejně jako u výše uvedeného scénáře "odstraněno nebo ztracená služba".  Pro každý oddíl Začněte obnovovat zálohy od nejnovějších k nejméně. Jakmile najdete zálohu, která nemá poškození, přesuňte nebo odstraňte všechny zálohy tohoto oddílu, které byly novější (než tato záloha). Tento postup opakujte pro každý oddíl. Když se teď v `OnDataLossAsync` oddílu v produkčním clusteru volá poslední záloha, která se nachází v externím úložišti, bude ta, kterou vybral výše uvedený proces.

Nyní je možné pomocí oddílu "Odstraněná nebo ztracená služba" obnovit stav služby do stavu před tím, než kód ladění ztratí stav.

Poznámky:

  - Při obnovení je pravděpodobné, že obnovená záloha je starší než stav oddílu před ztrátou dat. Z tohoto důvodu byste měli provést obnovení jenom jako poslední možnost obnovení co nejvíc dat.
  - Řetězec, který představuje cestu zálohovací složky a cesty souborů uvnitř zálohovací složky, může být větší než 255 znaků v závislosti na délce FabricDataRoot cesty a názvu typu aplikace. To může způsobit, že některé metody .NET, například `Directory.Move` , vyvolávají `PathTooLongException` výjimku. Jedním z alternativních řešení je přímé volání rozhraní API Kernel32, třeba `CopyFile` .

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

Výchozím zprostředkovatelem stavu pro Reliable Actors je `KvsActorStateProvider` . Přírůstkové zálohování není pro. standardně povolené `KvsActorStateProvider` . Můžete povolit přírůstkové zálohování vytvořením `KvsActorStateProvider` s příslušným nastavením ve svém konstruktoru a pak ho předat konstruktoru ActorService, jak je znázorněno v následujícím fragmentu kódu:

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

Když je zapnuté přírůstkové zálohování, `KvsActorStateProvider` nepoužívá k řízení záznamů protokolů cyklickou vyrovnávací paměť a pravidelně ji zkrátí. Pokud uživatel neprovádí zálohu po dobu 45 minut, systém automaticky zkrátí záznamy protokolu. Tento interval lze nakonfigurovat zadáním `logTruncationIntervalInMinutes` v `KvsActorStateProvider` konstruktoru (podobně jako při povolení přírůstkového zálohování). V případě, že primární replika potřebuje sestavit další repliku odesláním všech svých dat, mohou být záznamy protokolu také zkráceny.

Při obnovení ze záložního řetězce, podobně jako Reliable Services, by měl BackupFolderPath obsahovat podadresáře s jedním podadresářem obsahujícím úplné zálohování a další podadresáře, které obsahují přírůstkové zálohování. Pokud selže ověření řetězu zálohování, vrátí rozhraní API pro obnovení výjimku FabricException s příslušnou chybovou zprávou. 

> [!NOTE]
> `KvsActorStateProvider` v současné době ignoruje možnost RestorePolicy. safe. Podpora pro tuto funkci se plánuje v nadcházející verzi.
> 

## <a name="testing-back-up-and-restore"></a>Testování zálohování a obnovení
Je důležité zajistit, aby byla důležitá data zálohována a mohla by být obnovena z. To se dá udělat vyvoláním `Start-ServiceFabricPartitionDataLoss` rutiny v prostředí PowerShell, která může způsobit ztrátu dat v určitém oddílu a otestovat, jestli funkce zálohování a obnovení dat pro vaši službu fungují podle očekávání.  Také je možné programově vyvolat ztráty a obnovení dat z této události.

> [!NOTE]
> Ukázkovou implementaci funkcí zálohování a obnovení můžete najít v aplikaci Web reference na GitHubu. Podívejte se prosím na službu, kde najdete `Inventory.Service` Další podrobnosti.
> 
> 

## <a name="under-the-hood-more-details-on-backup-and-restore"></a>V rámci digestoře: Další podrobnosti o zálohování a obnovení
Tady jsou další podrobnosti o zálohování a obnovení.

### <a name="backup"></a>Backup
Správce spolehlivého stavu poskytuje možnost vytvářet konzistentní zálohy bez blokování operací čtení a zápisu. Pokud to chcete udělat, využívá mechanismus kontrolního bodu a protokolu trvalosti.  Správce spolehlivého stavu provádí v určitých bodech nepřibližné (zjednodušené) kontrolní body, které zbavují tlak z transakčního protokolu a zlepšují dobu obnovení.  Když `BackupAsync` je volána, správce spolehlivého stavu vydá pokyn všem spolehlivým objektům ke zkopírování jejich nejnovějších souborů kontrolních bodů do místní záložní složky.  Správce Reliable State pak zkopíruje všechny záznamy protokolu počínaje od "ukazatele" Start "k nejnovějšímu záznamu protokolu do složky pro zálohování.  Vzhledem k tomu, že všechny záznamy protokolu až do nejnovějšího záznamu protokolu jsou součástí zálohování a správce spolehlivého stavu zachovává protokolování proti zápisu, zaručuje správce spolehlivého stavu, že všechny potvrzené transakce ( `CommitAsync` úspěšně vráceny) jsou součástí zálohy.

Všechny transakce, které po `BackupAsync` volání metody zavolaly, mohou nebo nemusí být v záloze.  Po naplnění místní složky zálohování platformou (která znamená, že je místní záloha dokončená modulem runtime) se vyvolá zpětné volání zálohy služby.  Toto zpětné volání zodpovídá za přesunutí zálohovací složky do externího umístění, jako je například Azure Storage.

### <a name="restore"></a>Obnovení
Správce Reliable State nabízí možnost obnovení ze zálohy pomocí `RestoreAsync` rozhraní API.  
`RestoreAsync`Metoda on `RestoreContext` může být volána pouze uvnitř `OnDataLossAsync` metody.
Logická hodnota vrácená funkcí `OnDataLossAsync` označuje, zda služba obnovila svůj stav z externího zdroje.
Pokud `OnDataLossAsync` vrátí hodnotu true, Service Fabric bude znovu sestavovat všechny ostatní repliky z tohoto primárního. Service Fabric zajistí, aby repliky, které budou přijímat `OnDataLossAsync` volání, přešly na primární roli, ale neudělily stav čtení nebo stav zápisu.
To znamená, že pro StatefulService Implements nebude `RunAsync` volána, dokud nebude `OnDataLossAsync` dokončena úspěšně.
Pak `OnDataLossAsync` bude vyvolána na novém primárním.
Dokud služba toto rozhraní API úspěšně dokončí (vrátí hodnotu true nebo false) a dokončí příslušnou novou konfiguraci, rozhraní API se v tuto chvíli bude dál volat.

`RestoreAsync` nejprve zahodí všechny existující stavy v primární replice, na které byla volána. Správce Reliable State pak vytvoří všechny spolehlivé objekty, které existují ve složce Backup. V dalším kroku jsou spolehlivé objekty pokyny k obnovení ze svých kontrolních bodů ve složce Backup. Nakonec správce spolehlivého stavu obnoví svůj vlastní stav ze záznamů protokolu ve složce zálohy a provede obnovení. V rámci procesu obnovení jsou operace začínající od "počátečního bodu", které mají potvrzené záznamy protokolu v zálohovací složce, přehrávány do spolehlivých objektů. Tento krok zajistí, že obnovený stav je konzistentní.

## <a name="next-steps"></a>Další kroky
  - [Reliable Collections](service-fabric-work-with-reliable-collections.md)
  - [Rychlý Start Reliable Services](service-fabric-reliable-services-quick-start.md)
  - [Oznámení Reliable Services](service-fabric-reliable-services-notifications.md)
  - [Konfigurace Reliable Services](service-fabric-reliable-services-configuration.md)
  - [Referenční informace pro vývojáře pro spolehlivé kolekce](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
  - [Pravidelné zálohování a obnovení v Azure Service Fabric](service-fabric-backuprestoreservice-quickstart-azurecluster.md)
