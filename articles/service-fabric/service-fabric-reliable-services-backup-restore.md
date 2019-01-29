---
title: Service Fabric – zálohování a obnovení | Dokumentace Microsoftu
description: Rámcové dokumentaci pro Service Fabric – zálohování a obnovení
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
ms.date: 10/29/2018
ms.author: mcoskun
ms.openlocfilehash: 986a7be49f8ae0f683b89596204845bb08eeaf2d
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55095766"
---
# <a name="backup-and-restore-reliable-services-and-reliable-actors"></a>Zálohování a obnovení modelu Reliable Services a Reliable Actors
Azure Service Fabric je platforma vysoké dostupnosti, která replikuje stav napříč několika uzly tento vysokou dostupnost.  Proto i když selže jeden uzel v clusteru služby nadále k dispozici. Tuto redundanci integrované poskytovaný platformou může být dostačující pro některé, v některých případech je vhodné pro službu pro zálohování dat (pro externí úložiště).

> [!NOTE]
> Je důležité používat k zálohování a obnovení dat (a otestovat, že funguje podle očekávání), můžete obnovit z ztrátě dat.
> 

> [!NOTE]
> Společnost Microsoft doporučuje používat [pravidelné zálohování a obnovení](service-fabric-backuprestoreservice-quickstart-azurecluster.md) pro konfiguraci zálohování dat Reliable Stateful services a Reliable Actors. 
> 


Služba může být například vhodné zálohovat data z důvodu ochrany z následujících scénářů:

- V případě trvalé ztrátě celý cluster Service Fabric.
- Trvalé ztrátě většinou repliky oddílu služby
- Chyb správy kterým stav omylem získá odstraněn nebo poškozen. Například k tomu může dojít, pokud správce s dostatečná oprávnění omylem odstraní službu.
- Chyby ve službě, které dojít k poškození dat. Například tomu může dojít v případě upgrade kódu služby zahájí zápis vadným dat k Reliable Collection. V takovém případě se kód a data možná muset vrátit do předchozího stavu.
- Zpracování dat v režimu offline. Může být užitečné mít offline zpracování dat pro business intelligence, která se stane odděleně od služby, který generuje data.

Zálohování a obnovení funkce umožňuje služeb postavených na rozhraní API Reliable Services pro vytvoření a obnovení zálohy. Zálohování rozhraní API poskytovaný platformou povolit počet záloh: oddíl služby stavu, bez blokování pro čtení nebo zápisu. Obnovení rozhraní API umožňují oddíl služby stavu pro obnovení ze zálohy zvolená.

## <a name="types-of-backup"></a>Typy zálohování
Existují dvě možnosti zálohování: Úplné a přírůstkové.
Úplné zálohování je záloha, která obsahuje všechna data potřebná k rekonstrukci stavu repliky: kontrolní body a všechny záznamy protokolu.
Protože obsahuje kontrolní body a v protokolu, je možné úplné zálohy obnovit samostatně.

Vznikne problém s úplnými zálohami, když jsou velké kontrolní body.
Repliky, který má 16 GB paměti stav bude mít například kontrolní body, které aplikacím dodávají přibližně až 16 GB.
Pokud budeme mít cíl bodu obnovení pět minut, replika je potřeba zálohovat každých pět minut.
Pokaždé, když se zálohuje, je potřeba zkopírovat 16 GB paměti kontrolní body kromě 50 MB (konfigurovat pomocí `CheckpointThresholdInMB`) za protokoly.

![Příklad úplného zálohování.](media/service-fabric-reliable-services-backup-restore/FullBackupExample.PNG)

Řešení tohoto problému je přírůstkové zálohování, kde zálohování obsahuje pouze záznamy protokolu změněné od posledního zálohování.

![Příklad přírůstkové zálohování.](media/service-fabric-reliable-services-backup-restore/IncrementalBackupExample.PNG)

Protože přírůstkové zálohy jsou pouze změny od posledního zálohování (nezahrnuje kontrolní body), bude rychlejší, mají tendenci, ale není možné obnovit na své vlastní.
Chcete-li obnovit přírůstkové zálohování, je potřeba celý řetěz záloh.
Řetězec zálohování je řetěz záloh počínaje úplnou zálohu a za nímž následuje počet souvislých přírůstkové zálohování.

## <a name="backup-reliable-services"></a>Zálohování modelu Reliable Services
Autor služby má plnou kontrolu, kdy se mají zálohovat, a kam se budou ukládat zálohy.

Chcete-li spustit zálohování, služba potřebuje k vyvolání funkce zděděný člen `BackupAsync`.  
Zálohování lze provést pouze z primární repliky a vyžadují stav zápisu obdržet.

Jak je znázorněno níže, `BackupAsync` přijímá `BackupDescription` objektu, ve kterém jeden zadejte úplné nebo přírůstkové zálohování, stejně jako funkce zpětného volání, `Func<< BackupInfo, CancellationToken, Task<bool>>>` , které je voláno, když v zálohovací složce místně vytvořit a je připravený k přesunout do některé externí úložiště.

```csharp

BackupDescription myBackupDescription = new BackupDescription(backupOption.Incremental,this.BackupCallbackAsync);

await this.BackupAsync(myBackupDescription);

```

Požadavek na přírůstkovou zálohu může selhat s `FabricMissingFullBackupException`. Tato výjimka označuje, že se děje jednu z následujících akcí:

- replika se nikdy neobsadila úplnou zálohu. vzhledem k tomu se stal primárním,
- Některé záznamy protokolu od poslední zálohy byl zkrácen nebo
- repliky, které jsou předány `MaxAccumulatedBackupLogSizeInMB` limit.

Uživatele můžete zvýšit pravděpodobnost, že byli schopni to udělat přírůstkové zálohování nakonfigurováním `MinLogSizeInMB` nebo `TruncationThresholdFactor`.
Toto zvýšení hodnoty zvýší za využití disku repliky.
Další informace najdete v tématu [konfigurace Reliable Services](service-fabric-reliable-services-configuration.md)

`BackupInfo` poskytuje informace o zálohování, včetně umístění složky, kam modul runtime zálohování (`BackupInfo.Directory`). Funkce zpětného volání můžete přesunout `BackupInfo.Directory` k externím úložišti nebo v jiném umístění.  Tato funkce také vrátí hodnotu, která určuje, zda to bylo možné úspěšně přesunout v zálohovací složce do cílového umístění.

Následující kód ukazuje, jak `BackupCallbackAsync` metody slouží k nahrávání zálohování do Azure Storage:

```csharp
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, cancellationToken);

    return true;
}
```

V předchozím příkladu `ExternalBackupStore` je ukázkovou třídu, která slouží k rozhraní Azure Blob Storage a `UploadBackupFolderAsync` metodu komprimuje složce a umístí jej do úložiště objektů Blob v Azure.

Poznámky:

  - Může existovat pouze jedna operace zálohování vydávaných za pochodu na repliku v daném okamžiku. Více než jeden `BackupAsync` volání vždy vyvolá výjimku `FabricBackupInProgressException` omezit aktivních pořadových zálohování do jednoho.
  - Pokud replika převezme služby při selhání během zálohování, zálohování nemusí byly dokončeny. Proto po dokončení převzetí služeb při selhání, zodpovídá za služby restartujte zálohování vyvoláním `BackupAsync` podle potřeby.

## <a name="restore-reliable-services"></a>Obnovení modelu Reliable Services
Obecně platí případech, kdy možná budete muset provést operaci obnovení spadají do jedné z těchto kategorií:

  - Služba oddílu ke ztrátě dat. Například disku pro dvě ze tří replik pro oddíl (včetně primární replice) získá poškozený nebo dojde k vymazání. Nový primární možná muset obnovit data ze zálohy.
  - Celé služby bude ztracena. Například správce odstraní celou službu a tím služby a data je nutné obnovit.
  - Služba replikovaná data. poškozený aplikace (například z důvodu chyb aplikaci). V tomto případě služba má upgradovat, nebo se vrátit k odebrání příčinu poškození a -poškodit data se mají obnovit.

Zatímco řada přístupů je možné, nabízíme několik příkladů použití `RestoreAsync` obnovení z výše uvedených scénářů.

## <a name="partition-data-loss-in-reliable-services"></a>Oddíl ztrátě dat v modelu Reliable Services
V takovém případě by modul runtime automaticky zjistit ztrátu dat a vyvolání `OnDataLossAsync` rozhraní API.

Autor služby musí provádět následující obnovení:

  - Potlačí metodu virtuální základní třídy `OnDataLossAsync`.
  - Vyhledá poslední zálohy v externích umístění, které obsahuje zálohy služby.
  - Stáhněte si nejnovější zálohu (a dekomprimovat zálohování do zálohovací složky, pokud byla komprimována).
  - `OnDataLossAsync` Poskytuje metody `RestoreContext`. Volání `RestoreAsync` rozhraní API na zadaných `RestoreContext`.
  - Vrací true, pokud obnovení bylo úspěšné.

Tady je příklad implementace `OnDataLossAsync` metody:

```csharp
protected override async Task<bool> OnDataLossAsync(RestoreContext restoreCtx, CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    var restoreDescription = new RestoreDescription(backupFolder);

    await restoreCtx.RestoreAsync(restoreDescription);

    return true;
}
```

`RestoreDescription` předaný do `RestoreContext.RestoreAsync` volání obsahuje člen s názvem `BackupFolderPath`.
Při obnovení jedné úplné zálohy, to `BackupFolderPath` musí být nastavena na místní cestu ke složce, která obsahuje úplnou zálohu.
Při obnovení úplné zálohy a počet přírůstkové zálohy, `BackupFolderPath` musí být nastavena na místní cestu ke složce, která obsahuje pouze úplné zálohování, ale také všechny přírůstkové zálohování.
`RestoreAsync` může vyvolat volání `FabricMissingFullBackupException` Pokud `BackupFolderPath` k dispozici neobsahuje úplnou zálohu.
Můžete také vyvolat `ArgumentException` Pokud `BackupFolderPath` má porušený řetězec přírůstkových záloh.
Například, pokud obsahuje úplné zálohování první přírůstkové a třetí přírůstkové zálohování, ale ne druhý přírůstkové zálohování.

> [!NOTE]
> Ve výchozím nastavení do bezpečných RestorePolicy.  To znamená, že `RestoreAsync` rozhraní API se nezdaří s ArgumentException, pokud zjistí, že v zálohovací složce obsahuje stavu, ve kterém je starší než nebo rovna hodnotě obsažené v této replice stavu.  `RestorePolicy.Force` je možné aby přeskočil tuto kontrolu zabezpečení. Tento parametr je zadán jako součást `RestoreDescription`.
> 

## <a name="deleted-or-lost-service"></a>Odstraněné nebo ke ztrátě služeb
Pokud služba je odebrán, budete muset nejprve znovu vytvořit službu předtím, než je možné obnovit data.  Je důležité vytvořit službu se stejnou konfigurací, například dělení schéma, takže data můžete obnovit bez problémů.  Jakmile je služba, rozhraní API za účelem obnovení dat (`OnDataLossAsync` výše) musí být volána na každý oddíl této služby. Jeden ze způsobů, jak dosáhnout jde pomocí [FabricClient.TestManagementClient.StartPartitionDataLossAsync](https://msdn.microsoft.com/library/mt693569.aspx) v každém oddílu.  

Od této chvíle implementace je stejný jako výše popsaném scénáři. Každý oddíl musí obnovit poslední relevantní zálohu z externího úložiště. Jeden výstrahou je, ID oddílu může nyní změnily, protože modul runtime vytvoří dynamicky ID oddílů. Díky tomu se služba potřebuje k uložení příslušného oddílu informací a službou název pro identifikaci správné poslední zálohu k obnovení z pro každý oddíl.

> [!NOTE]
> Nedoporučuje se používat `FabricClient.ServiceManager.InvokeDataLossAsync` v jednotlivých oddílech obnovit celou službou, protože stav vašeho clusteru, který může poškodit.
> 

## <a name="replication-of-corrupt-application-data"></a>Replikace dat poškozený aplikací
Pokud se upgrade nově nasazené aplikace obsahuje chybu, která může způsobit poškození dat. Upgrade aplikace například může začít aktualizovat každý záznam telefonní číslo do spolehlivého slovníku se neplatný kód oblasti.  Neplatnými telefonními čísly v takovém případě budou replikovat, protože nemá žádné informace o povaze dat, která ukládají Service Fabric.

První věc, kterou chcete provést po zjištění takové mimořádně závažných chyb, která způsobí poškození dat, je zablokování služby na úrovni aplikace a pokud je to možné upgradovat na verzi aplikace kód, který nemá žádné chyby.  Ale i po opravě kódu služby data stále může být poškozený, a proto může data muset obnovit.  V takovém případě nemusí být dostatečné k obnovení poslední zálohy, protože nejnovější zálohy také může být poškozený.  Proto budete muset najít poslední zálohy, která byla vytvořená před získali poškozená data.

Pokud si nejste jisti, které zálohování je poškozený, můžete nasadit nový cluster Service Fabric a obnovení záloh ovlivněné oddíly stejně jako výše "Odstraněno nebo ke ztrátě služeb" scénář.  Pro každý oddíl start obnovení zálohy z poslední nejméně. Jakmile najdete zálohy, která nemá poškození přesunutí nebo odstranění všechny zálohy tohoto oddílu, které byly novější (než zálohování). Tento postup opakujte pro každý oddíl. Teď, když `OnDataLossAsync` je volána na oddíl v produkčním prostředí clusteru, poslední zálohy v externím úložišti bude po procesu výše.

Nyní, kroky v "odstraněno nebo ke ztrátě služeb" oddílu je možné obnovit stav služby do stavu před chybový kód poškozený stav.

Poznámky:

  - Při obnovení, může se stát, že je zálohování, který se má obnovit starší než stav oddílu předtím, než data byla ztracena. Z tohoto důvodu musíte obnovit pouze jako poslední možnost obnovit data, která možná.
  - Řetězec, který představuje cestu ke složce pro zálohování a cest souborů ve složce backup, které může být větší než 255 znaků, v závislosti na FabricDataRoot cestu a název typu aplikace. To může způsobit některých metod rozhraní .NET, jako je třeba `Directory.Move`, chcete-li vyvolat `PathTooLongException` výjimky. Jeden alternativním řešením je přímo volat rozhraní API kernel32, jako je `CopyFile`.

## <a name="back-up-and-restore-reliable-actors"></a>Zálohování a obnovení Reliable Actors


Reliable Actors rozhraní Framework je nástavbou Reliable Services. ActorService, který je hostitelem actor(s) je spolehlivé stavové služby. Proto všechny zálohování a obnovení funkce dostupné v modelu Reliable Services dostupná je i Reliable Actors (s výjimkou chování, které jsou specifické pro zprostředkovatele stavu). Protože zálohy budete přesměrováni na bázi oddílů, stavy pro všechny objekty actor oddíl se dají zálohovat (a obnovení se podobá a bude probíhat na bázi oddílů). K provedení zálohování a obnovení, vlastník služby by měl vytvořit třídu vlastního objektu actor service, která je odvozena z třídy ActorService a proveďte zálohování a obnovení podobný modelu Reliable Services, jak je popsáno výše v předchozích částech.

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

Když vytvoříte třídu vlastního objektu actor service, musíte zaregistrovat, který také při registraci objektu actor.

```csharp
ActorRuntime.RegisterActorAsync<MyActor>(
   (context, typeInfo) => new MyCustomActorService(context, typeInfo)).GetAwaiter().GetResult();
```

Výchozího zprostředkovatele stavu Reliable actors je `KvsActorStateProvider`. Přírůstková záloha není povolena ve výchozím nastavení pro `KvsActorStateProvider`. Přírůstkové zálohování můžete povolit vytvořením `KvsActorStateProvider` s odpovídající nastavení 've svém konstruktoru a předejte jí ActorService konstruktoru, jak je znázorněno v následujícím fragmentu kódu:

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

Po povolení přírůstkové zálohy, přírůstkové zálohování může selhat s FabricMissingFullBackupException pro jednu z následujících důvodů a budete muset vytvořit úplnou zálohu před provedením přírůstkové zálohy:

  - Replika má nikdy neobsadila úplné zálohy. protože se převedly na primární.
  - Některé záznamy protokolu došlo ke zkrácení od poslední zálohy.

Pokud je povoleno přírůstkové zálohování, `KvsActorStateProvider` nepoužívá ke správě své záznamy protokolu cyklické vyrovnávací paměti a pravidelně ho zkrátí. Pokud žádná záloha uživatelem po dobu 45 minut, systém automaticky zkrátí záznamy protokolu. Tento interval můžete nakonfigurovat tak, že zadáte `logTruncationIntervalInMinutes` v `KvsActorStateProvider` konstruktoru (podobně jako při povolování přírůstkové zálohování). Záznamy protokolu může získat i zkrácen, pokud primární repliky se vyžaduje k sestavení jiná replika odesláním všechna jeho data.

Při obnovení ze zálohy řetěz, podobně jako spolehlivé služby BackupFolderPath by měl obsahovat podadresářů pomocí jednoho podadresáře obsahující úplné zálohy a další podadresáře obsahující přírůstkové zálohy. Rozhraní API pro obnovení vyvolá výjimku FabricException s příslušnou chybovou zprávu, pokud selže ověření řetězu zálohování. 

> [!NOTE]
> `KvsActorStateProvider` aktuálně ignoruje možnost RestorePolicy.Safe. Podpora pro tuto funkci je plánované v nadcházející verzi.
> 

## <a name="testing-back-up-and-restore"></a>Testování zálohování a obnovení
Je důležité zajistit, že během zálohování důležitých dat a dají se obnovit z. To můžete udělat tak, že vyvolá `Start-ServiceFabricPartitionDataLoss` rutiny v prostředí PowerShell, který může způsobit ztrátu dat na konkrétní oddíl k ověření, zda data zálohování a obnovení pro vaše služba funguje podle očekávání.  Také je možné programově vyvolání ztráty dat a obnovení z této události i.

> [!NOTE]
> Můžete najít ukázková implementace zálohování a obnovení ve webové aplikaci odkaz na Githubu. Podívejte se `Inventory.Service` service pro další podrobnosti.
> 
> 

## <a name="under-the-hood-more-details-on-backup-and-restore"></a>Pod pokličkou: Další informace o zálohování a obnovení
Tady jsou některé další podrobnosti k zálohování a obnovení.

### <a name="backup"></a>Backup
Reliable State Manager umožňuje vytvářet zálohy konzistentní vzhledem k aplikacím bez blokování libovolné čtení nebo operace zápisu. Uděláte to tak, využívá mechanismus trvalého kontrolního bodu a protokolu.  Reliable State Manager trvá přibližných shod (jednoduchý) kontrolní body v určitých bodech, snížit zatížení z transakční protokol a zkrátit dobu obnovení.  Když `BackupAsync` je volána, Reliable State Manager nastaví všechny objekty Reliable kopírovat jejich nejnovější soubory kontrolního bodu do místní složky pro zálohy.  Potom Reliable State Manager zkopíruje všechny záznamy protokolu od ukazatel"start" až o nejnovější záznam protokolu do zálohovací složky.  Protože všechny záznamy protokolu až o nejnovější záznam protokolu jsou zahrnuté do zálohování a Reliable State Manager zachová dávky zápisu protokolování, Reliable State Manager zaručuje, že všechny transakce, která jsou potvrzeny (`CommitAsync` úspěšně vrátila ) jsou součástí zálohy.

Všechny transakce, která provádí po `BackupAsync` byla volána může nebo nemusí být v záloze.  Jakmile se připravil záložní složky místní platformou (to znamená, že místní bylo zálohování dokončeno modulem runtime), služby backup zpětné volání je volána.  Toto zpětné volání je zodpovědná za přesun v zálohovací složce do externího umístění, jako je Azure Storage.

### <a name="restore"></a>Obnovení
Poskytuje možnost obnovení ze zálohy s využitím Reliable State Manager `RestoreAsync` rozhraní API.  
`RestoreAsync` Metoda `RestoreContext` lze volat pouze `OnDataLossAsync` metody.
Bool vrácený `OnDataLossAsync` označuje, zda služba obnoví svůj stav z externího zdroje.
Pokud `OnDataLossAsync` vrací hodnotu true, Service Fabric se opětovné sestavení všech ostatních repliky z tomuto primárnímu. Service Fabric zajišťuje, že repliky, které budou přijímat `OnDataLossAsync` volání prvním přechodu do primární role, ale nejsou udělena stav zápisu nebo čtení stavu.
To znamená, že pro implementátory StatefulService `RunAsync` nebude volat, dokud nebude `OnDataLossAsync` úspěšně dokončen.
Potom `OnDataLossAsync` bude vyvolána na nový primární.
Dokud služba toto rozhraní API se dokončí úspěšně (tak, že vrací hodnotu true nebo false) a dokončí příslušné změny konfigurace, volá rozhraní API se zachovat se postupně po jednom.

`RestoreAsync` nejprve zahodí všechny stávající stav v primární replice, která byla volána pro. Reliable State Manager pak vytvoří spolehlivé objekty, které existují ve složce pro zálohování. V dalším kroku spolehlivé objekty jsou vydal pokyn pro nástroje pro obnovení z jejich kontrolní body ve složce pro zálohování. Reliable State Manager nakonec obnoví svoji stav ze záznamů protokolu ve složce pro zálohování a provede obnovení. Jako součást procesu obnovení jsou operace od "výchozí bod", které mají potvrzeny záznamy protokolu ve složce backup přehrály spolehlivé objektů. Tento krok zajistí, že obnovený stav je konzistentní.

## <a name="next-steps"></a>Další postup
  - [Reliable Collections](service-fabric-work-with-reliable-collections.md)
  - [Rychlý start Reliable Services](service-fabric-reliable-services-quick-start.md)
  - [Oznámení Reliable Services](service-fabric-reliable-services-notifications.md)
  - [Konfigurace Reliable Services](service-fabric-reliable-services-configuration.md)
  - [Referenční informace pro vývojáře pro Reliable Collections](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  - [Pravidelné zálohování a obnovení v Azure Service Fabric](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

