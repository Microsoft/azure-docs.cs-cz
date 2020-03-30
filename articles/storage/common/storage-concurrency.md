---
title: Správa souběžnosti
titleSuffix: Azure Storage
description: Zjistěte, jak spravovat souběžnost pro služby blob, fronta, tabulka a souborové služby.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 12/20/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 9879f98e72e22fc0745a9e91f29216cbe74ab8fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255298"
---
# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Správa souběžnosti v Microsoft Azure Storage

Moderní internetové aplikace mají obvykle více uživatelů prohlížení a aktualizaci dat současně. To vyžaduje, aby vývojáři aplikací pečlivě promysleli, jak koncovým uživatelům poskytnout předvídatelné prostředí, zejména pro scénáře, kde může více uživatelů aktualizovat stejná data. Existují tři hlavní strategie souběžnosti dat, které vývojáři obvykle považují:  

1. Optimistická souběžnost – aplikace provádějící aktualizaci bude součástí aktualizace ověřit, zda se data změnila od posledního čtení dat aplikace. Pokud například dva uživatelé, kteří si prohlíží wikistránku, aktualizují stejnou stránku, musí wiki platforma zajistit, aby druhá aktualizace nepřepsala první aktualizaci - a aby oba uživatelé pochopili, zda byla jejich aktualizace úspěšná nebo ne. Tato strategie se nejčastěji používá ve webových aplikacích.
2. Pesimistické souběžnosti – aplikace, která chce provést aktualizaci, zamořuje objekt, který ostatním uživatelům zabrání v aktualizaci dat, dokud nebude zámek uvolněn. Například ve scénáři replikace hlavních a podřízených dat, kde pouze hlavní server provede aktualizace, bude hlavní server obvykle držet výhradní zámek po delší dobu na datech, aby se zajistilo, že je nikdo jiný nebude moci aktualizovat.
3. Poslední zapisovatel wins – přístup, který umožňuje všechny operace aktualizace pokračovat bez ověření, zda jiná aplikace aktualizovala data od aplikace poprvé číst data. Tato strategie (nebo nedostatek formální strategie) se obvykle používá tam, kde jsou data rozdělena tak, že není pravděpodobné, že více uživatelů bude mít přístup ke stejným datům. To může být také užitečné, pokud jsou zpracovávány datové proudy s krátkou životností.  

Tento článek poskytuje přehled o tom, jak platforma Azure Storage zjednodušuje vývoj tím, že poskytuje prvotřídní podporu pro všechny tři tyto strategie souběžnosti.  

## <a name="azure-storage-simplifies-cloud-development"></a>Azure Storage zjednodušuje vývoj cloudu

Služba úložiště Azure podporuje všechny tři strategie, i když je charakteristická svou schopností poskytovat plnou podporu pro optimistickou a pesimistickou souběžnost, protože byla navržena tak, aby zahrnovala silný model konzistence, který zaručuje, že když Služba úložiště potvrdí vložení dat nebo aktualizaci operace všechny další přístupy k těmto datům se zobrazí nejnovější aktualizace. Platformy úložiště, které používají model případné konzistence, mají prodlevy mezi tím, kdy zápis provádí jeden uživatel, a tím, kdy jsou aktualizovaná data viděna jinými uživateli, což komplikuje vývoj klientských aplikací, aby se zabránilo nekonzistencím postihující koncové uživatele.  

Kromě výběru vhodné strategie souběžnosti vývojáři by také měli být vědomi toho, jak platforma úložiště izoluje změny – zejména změny stejného objektu napříč transakcemi. Služba úložiště Azure používá izolaci snímek, aby umožnila souběžné operace čtení s operacemi zápisu v rámci jednoho oddílu. Na rozdíl od jiných úrovní izolace, snímek izolace zaručuje, že všechny čtení zobrazit konzistentní snímek dat i v době, kdy dochází k aktualizacím – v podstatě vrácením poslední potvrzené hodnoty při zpracování transakce aktualizace.  

## <a name="managing-concurrency-in-blob-storage"></a>Správa souběžnosti ve úložišti objektů Blob

Můžete se rozhodnout použít optimistické nebo pesimistické souběžnosti modely ke správě přístupu k objektům BLOB a kontejnerů ve službě objektu blob. Pokud explicitně nezadáte strategii, je výchozí hodnota wins posledního zápisu.  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Optimistická souběžnost pro objekty BLOB a kontejnery

Služba Storage přiřadí identifikátor každému uloženému objektu. Tento identifikátor je aktualizován při každém provedení operace aktualizace na objektu. Identifikátor je vrácen klientovi jako součást odpovědi HTTP GET pomocí hlavičky ETag (značka entity), která je definována v protokolu HTTP. Uživatel provádějící aktualizaci takového objektu může odeslat v původním eTag spolu s podmíněnou hlavičkou, aby zajistil, že k aktualizaci dojde pouze v případě, že byla splněna určitá podmínka – v tomto případě je podmínka hlavičkou "If-Match", která vyžaduje službu Storage Service chcete-li zajistit, že hodnota eTag zadaná v požadavku na aktualizaci je stejná jako hodnota uložená ve službě Storage Service.  

Nástin tohoto procesu je následující:  

1. Načíst objekt blob ze služby úložiště, odpověď obsahuje hodnotu hlavičky HTTP ETag, která identifikuje aktuální verzi objektu ve službě úložiště.
2. Při aktualizaci objektu blob zahrňte hodnotu ETag, kterou jste obdrželi v kroku 1, do podmíněné hlavičky **If-Match** požadavku, který odešlete službě.
3. Služba porovná hodnotu ETag v požadavku s aktuální hodnotou ETag objektu blob.
4. Pokud aktuální hodnota ETag objektu blob je jiná verze než ETag v **if-Match** podmíněné záhlaví v požadavku, služba vrátí 412 chyba klientovi. To znamená klientovi, že jiný proces aktualizoval objekt blob od doby, kdy jej klient načetl.
5. Pokud aktuální hodnota ETag objektu blob je stejná verze jako ETag v **if-Match** podmíněné záhlaví v požadavku, služba provede požadovanou operaci a aktualizuje aktuální hodnotu ETag objektu blob ukázat, že vytvořil novou verzi.  

Následující fragment jazyka C# (pomocí knihovny klientského úložiště 4.2.0) ukazuje jednoduchý příklad, jak vytvořit **if-Match AccessCondition** na základě hodnoty ETag, která je přístupná z vlastností objektu blob, který byl dříve načten nebo vložen. Potom používá **AccessCondition** objekt při aktualizaci objektu blob: **AccessCondition** objekt přidá **If-Match** záhlaví požadavku. Pokud jiný proces aktualizoval objekt blob, vrátí služba Blob stavovou zprávu HTTP 412 (Podmínka se nezdařila). Celou ukázku si můžete stáhnout zde: [Správa souběžnosti pomocí služby Azure Storage](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).  

```csharp
// Retrieve the ETag from the newly created blob
// Etag is already populated as UploadText should cause a PUT Blob call
// to storage Blob service which returns the ETag in response.
string originalETag = blockBlob.Properties.ETag;

// This code simulates an update by a third party.
string helloText = "Blob updated by a third party.";

// No ETag provided so original blob is overwritten (thus generating a new ETag)
blockBlob.UploadText(helloText);
Console.WriteLine("Blob updated. Updated ETag = {0}",
blockBlob.Properties.ETag);

// Now try to update the blob using the original ETag provided when the blob was created
try
{
    Console.WriteLine("Trying to update blob using original ETag to generate if-match access condition");
    blockBlob.UploadText(helloText,accessCondition:
    AccessCondition.GenerateIfMatchCondition(originalETag));
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
    {
        Console.WriteLine("Precondition failure as expected. Blob's original ETag no longer matches");
        // TODO: client can decide on how it wants to handle the 3rd party updated content.
    }
    else
        throw;
}  
```

Azure Storage také zahrnuje podporu pro další podmíněné hlavičky, jako je **If-Modified-Since**, **If-Unmodified-Since** a **If-None-None-Match,** stejně jako jejich kombinace. Další informace naleznete [v tématu Určení podmíněných záhlaví pro operace služby objektů Blob](https://msdn.microsoft.com/library/azure/dd179371.aspx).  

Následující tabulka shrnuje operace kontejneru, které přijímají podmíněné hlavičky, jako je **například If-Match** v požadavku a které vracejí hodnotu ETag v odpovědi.  

| Operace | Vrátí hodnotu etag kontejneru. | Přijímá podmíněná záhlaví. |
|:--- |:--- |:--- |
| Vytvoření kontejneru |Ano |Ne |
| Získat vlastnosti kontejneru |Ano |Ne |
| Získat metadata kontejneru |Ano |Ne |
| Nastavit metadata kontejneru |Ano |Ano |
| Získat kontejner ACL |Ano |Ne |
| Nastavit kontejner ACL |Ano |Ano (*) |
| Odstranění kontejneru |Ne |Ano |
| Zapůjčení kontejneru |Ano |Ano |
| Seznam objektů Blob |Ne |Ne |

(*) Oprávnění definovaná sadou SetContainerACL jsou ukládána do mezipaměti a aktualizace těchto oprávnění trvá 30 sekund, než se rozšíří během doby, kdy není zaručeno, že aktualizace budou konzistentní.  

Následující tabulka shrnuje operace objektů blob, které přijímají podmíněné hlavičky, jako je **například If-Match** v požadavku a které vracejí hodnotu ETag v odpovědi.

| Operace | Vrátí hodnotu eTag. | Přijímá podmíněná záhlaví. |
|:--- |:--- |:--- |
| Vložení objektu blob |Ano |Ano |
| Získat blob |Ano |Ano |
| Získání vlastností objektu blob |Ano |Ano |
| Nastavení vlastností objektu blob |Ano |Ano |
| Získání metadat objektů blob |Ano |Ano |
| Nastavení metadat objektu blob |Ano |Ano |
| Blob pro zapůjčení (*) |Ano |Ano |
| Pořízení snímku objektu blob |Ano |Ano |
| Zkopírování objektu blob |Ano |Ano (pro zdrojový a cílový objekt blob) |
| Přerušit kopírování objektů blob |Ne |Ne |
| Odstranění objektu blob |Ne |Ano |
| Umístit blok |Ne |Ne |
| Seznam blokovaných položek |Ano |Ano |
| Získat seznam blokování |Ano |Ne |
| Umístit stránku |Ano |Ano |
| Získat rozsahy stránek |Ano |Ano |

(*) Zapůjčení objektu blob nezmění etag na objekt blob.  

### <a name="pessimistic-concurrency-for-blobs"></a>Pesimistické souběžnosti pro objekty BLOB

Chcete-li zamknout objekt blob pro výhradní použití, můžete získat [zapůjčení](https://msdn.microsoft.com/library/azure/ee691972.aspx) na něj. Když získáte zapůjčení, zadáte, jak dlouho budete potřebovat zapůjčení: to může být mezi 15 až 60 sekund nebo nekonečné, což představuje výhradní zámek. Můžete obnovit omezenou zapůjčení prodloužit a můžete uvolnit všechny zapůjčení po dokončení s ním. Služba blob automaticky uvolní omezené zapůjčení, když vyprší jejich platnost.  

Pronájmy umožňují různé strategie synchronizace, které mají být podporovány, včetně výhradního zápisu / sdílené čtení, exkluzivní zápis / exkluzivní čtení a sdílené zápis / exkluzivní čtení. Pokud existuje zapůjčení, služba úložiště vynucuje výhradní zápisy (operace put, set a delete), ale zajištění exkluzivity pro operace čtení vyžaduje, aby vývojář zajistil, že všechny klientské aplikace používají ID zapůjčení a že pouze jeden klient má současně platné ID zapůjčení. Operace čtení, které neobsahují ID zapůjčení, mají za následek sdílené čtení.  

Následující fragment jazyka C# ukazuje příklad získání výhradní zapůjčení po dobu 30 sekund na objekt blob, aktualizace obsahu objektu blob a uvolnění zapůjčení. Pokud již existuje platný zapůjčení na objekt blob při pokusu o získání nové ho zapůjčení, vrátí služba blob výsledek stavu "Konflikt HTTP (409). Následující fragment fragmentu používá **AccessCondition** objekt zapouzdřit informace o zapůjčení při požadavku na aktualizaci objektu blob ve službě úložiště.  Celou ukázku si můžete stáhnout zde: [Správa souběžnosti pomocí služby Azure Storage](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
// Acquire lease for 15 seconds
string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

// Update blob using lease. This operation will succeed
const string helloText = "Blob updated";
var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
blockBlob.UploadText(helloText, accessCondition: accessCondition);
Console.WriteLine("Blob updated using an exclusive lease");

//Simulate third party update to blob without lease
try
{
    // Below operation will fail as no valid lease provided
    Console.WriteLine("Trying to update blob without valid lease");
    blockBlob.UploadText("Update without lease, will fail");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        Console.WriteLine("Precondition failure as expected. Blob's lease does not match");
    else
        throw;
}  
```

Pokud se pokusíte o operaci zápisu na zapůjčené objekt blob bez předání ID zapůjčení, požadavek se nezdaří s chybou 412. Všimněte si, že pokud zapůjčení vyprší před voláním **UploadText** metoda, ale stále předat ID zapůjčení, požadavek také selže s chybou **412.** Další informace o správě doby vypršení platnosti zapůjčení a ID zapůjčení naleznete v dokumentaci [k zapůjčení objektu BLOB](https://msdn.microsoft.com/library/azure/ee691972.aspx) REST.  

Následující operace objektů blob můžete použít zapůjčení ke správě pesimistické souběžnosti:  

* Vložení objektu blob
* Získat blob
* Získání vlastností objektu blob
* Nastavení vlastností objektu blob
* Získání metadat objektů blob
* Nastavení metadat objektu blob
* Odstranění objektu blob
* Umístit blok
* Seznam blokovaných položek
* Získat seznam blokování
* Umístit stránku
* Získat rozsahy stránek
* Snímek objektblo - ID zapůjčení volitelné, pokud existuje zapůjčení
* Kopírovat objekt Blob - id zapůjčení povinné, pokud existuje zapůjčení v cílovém objektu blob
* Přerušit kopírování objektů Blob - id zapůjčení povinné, pokud v cílovém objektu blob existuje nekonečná zapůjčení
* Operace Lease Blob  

### <a name="pessimistic-concurrency-for-containers"></a>Pesimistické souběžnosti pro kontejnery

Zapůjčení kontejnerů umožňuje podporu stejných strategií synchronizace jako u objektů BLOB (výhradní zápis / sdílené čtení, výhradní zápis / výhradní zápis a sdílené čtení / výhradní čtení), ale na rozdíl od objektů BLOB služba úložiště vynucuje pouze exkluzivitu při operacích odstranění. Chcete-li odstranit kontejner s aktivní zapůjčení, klient musí obsahovat aktivní ID zapůjčení s požadavkem na odstranění. Všechny ostatní operace kontejneru úspěšné na pronajatém kontejneru bez zahrnutí ID zapůjčení v takovém případě jsou sdílené operace. Pokud je vyžadována exkluzivita aktualizace (put nebo set) nebo operace čtení, vývojáři by měli zajistit, aby všichni klienti používali ID zapůjčení a že pouze jeden klient má platné ID zapůjčení.  

Následující operace kontejneru můžete použít zapůjčení ke správě pesimistické souběžnosti:  

* Odstranění kontejneru
* Získat vlastnosti kontejneru
* Získat metadata kontejneru
* Nastavit metadata kontejneru
* Získat kontejner ACL
* Nastavit kontejner ACL
* Zapůjčení kontejneru  

Další informace naleznete v tématu:  

* [Určení hlaviček podmínek pro operace Blob service](https://msdn.microsoft.com/library/azure/dd179371.aspx)
* [Zapůjčení kontejneru](https://msdn.microsoft.com/library/azure/jj159103.aspx)
* [Operace Lease Blob](https://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-table-storage"></a>Správa souběžnosti v úložišti tabulky

Služba Table používá optimistické kontroly souběžnosti jako výchozí chování při práci s entitami, na rozdíl od služby objektů Blob, kde je nutné explicitně zvolit provádění optimistických kontrol souběžnosti. Další rozdíl mezi tabulka a služby objektů blob je, že můžete spravovat pouze chování souběžnosti entit vzhledem k tomu, že se službou objektů blob můžete spravovat souběžnost kontejnerů a objektů BLOB.  

Chcete-li použít optimistickou souběžnost a zkontrolovat, zda jiný proces změnil entitu od doby, kdy jste ji načetli ze služby table storage, můžete použít hodnotu ETag, kterou obdržíte, když služba tabulky vrátí entitu. Nástin tohoto procesu je následující:  

1. Načíst entitu ze služby úložiště tabulky, odpověď obsahuje hodnotu ETag, která identifikuje aktuální identifikátor přidružený k této entitě ve službě úložiště.
2. Při aktualizaci entity zahrňte hodnotu ETag, kterou jste obdrželi v kroku 1, do povinné hlavičky **If-Match** požadavku, který odešlete službě.
3. Služba porovná hodnotu ETag v požadavku s aktuální hodnotou ETag entity.
4. Pokud aktuální hodnota ETag entity se liší od ETag v povinné **if-Match** záhlaví v požadavku, služba vrátí 412 chyba klientovi. To znamená klientovi, že jiný proces aktualizoval entitu od doby, kdy ji klient načetl.
5. Pokud je aktuální hodnota ETag entity stejná jako eTag v povinné hlavičce **If-Match** v požadavku nebo v hlavičce **If-Match** obsahuje zástupný znak (*), služba provede požadovanou operaci a aktualizuje aktuální hodnotu ETag entity, aby ukázala, že byla aktualizována.  

Všimněte si, že na rozdíl od služby blob služba tabulka vyžaduje, aby klient zahrnout **if-match** záhlaví v požadavcích na aktualizaci. Je však možné vynutit bezpodmínečnou aktualizaci (poslední zapisovatel wins strategie) a obejít kontroly souběžnosti, pokud klient nastaví **if-Match** záhlaví zástupný znak (*) v požadavku.  

Následující fragment c# zobrazuje entitu zákazníka, která byla dříve vytvořena nebo načtena s aktuálnou e-mailovou adresou. Počáteční operace vložení nebo načtení ukládá hodnotu ETag v objektu zákazníka a protože ukázka používá stejnou instanci objektu při provádění operace nahrazení, automaticky odešle hodnotu ETag zpět do služby table service, což umožňuje službě služby zkontrolujte porušení souběžnosti. Pokud jiný proces aktualizoval entitu v úložišti tabulek, vrátí služba stavovou zprávu HTTP 412 (Podmínka se nezdařila).  Celou ukázku si můžete stáhnout zde: [Správa souběžnosti pomocí služby Azure Storage](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
try
{
    customer.Email = "updatedEmail@contoso.org";
    TableOperation replaceCustomer = TableOperation.Replace(customer);
    customerTable.Execute(replaceCustomer);
    Console.WriteLine("Replace operation succeeded.");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == 412)
        Console.WriteLine("Optimistic concurrency violation – entity has changed since it was retrieved.");
    else
        throw;
}  
```

Chcete-li explicitně zakázat kontrolu souběžnosti, měli byste nastavit vlastnost **ETag** objektu **zaměstnance** na "*" před provedením operace nahrazení.  

```csharp
customer.ETag = "*";  
```

Následující tabulka shrnuje, jak operace entity tabulky používají hodnoty ETag:

| Operace | Vrátí hodnotu eTag. | Vyžaduje hlavičku požadavku If-Match |
|:--- |:--- |:--- |
| Entity dotazu |Ano |Ne |
| Vložit entitu |Ano |Ne |
| Aktualizovat entitu |Ano |Ano |
| Sloučit entitu |Ano |Ano |
| Odstranit entitu |Ne |Ano |
| Vložit nebo nahradit entitu |Ano |Ne |
| Vložit nebo sloučit entitu |Ano |Ne |

Všimněte si, že **vložit nebo nahradit entity** a vložit nebo **sloučit entity** operace *neprovádějí* žádné kontroly souběžnosti, protože neodesílají hodnotu ETag do služby tabulky.  

Obecně vývojáři pomocí tabulek by měl spoléhat na optimistické souběžnosti při vývoji škálovatelných aplikací. Pokud pesimistické zamykání je potřeba, jeden přístup vývojáři mohou mít při přístupu tabulky je přiřadit určený objekt blob pro každou tabulku a pokusit se přijmout zapůjčení na objekt blob před provozem na tabulce. Tento přístup vyžaduje, aby aplikace zajistila, že všechny cesty přístupu k datům získají zapůjčení před zahájením provozu v tabulce. Měli byste také poznamenat, že minimální doba zapůjčení je 15 sekund, což vyžaduje pečlivé zvážení škálovatelnosti.  

Další informace naleznete v tématu:  

* [Operace s entitami](https://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>Správa souběžnosti ve službě Fronty

Jeden scénář, ve kterém souběžnost je problém ve službě fronty je, kde více klientů načítá zprávy z fronty. Při načtení zprávy z fronty, odpověď obsahuje zprávu a pop příjem hodnota, která je nutné odstranit zprávu. Zpráva není automaticky odstraněna z fronty, ale po načtení není viditelná pro ostatní klienty v časovém intervalu určeném parametrem visibilityytimeout. Klient, který načte zprávu se očekává, že odstranit zprávu po zpracování a před čas určený TimeNextVisible prvek odpovědi, která je vypočtena na základě hodnoty visibilitytimeout parametr. Hodnota visibilitytimeout je přidán do okamžiku, kdy je zpráva načtena k určení hodnoty TimeNextVisible.  

Služba fronty nemá podporu pro optimistickou nebo pesimistickou souběžnost a z tohoto důvodu by klienti zpracovávající zprávy načtené z fronty měli zajistit, aby zprávy byly zpracovány idempotentním způsobem. Strategie wins posledního zapisovače se používá pro operace aktualizace, jako jsou SetQueueServiceProperties, SetQueueMetaData, SetQueueACL a UpdateMessage.  

Další informace naleznete v tématu:  

* [Rozhraní REST API služby Queue Service](https://msdn.microsoft.com/library/azure/dd179363.aspx)
* [Získat zprávy](https://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-azure-files"></a>Správa souběžnosti v souborech Azure

Souborová služba je přístupná pomocí dvou různých koncových bodů protokolu – SMB a REST. Služba REST nemá podporu pro optimistické zamykání nebo pesimistické zamykání a všechny aktualizace budou následovat poslední scénář wins strategie. Klienti SMB, kteří připojují sdílené složky, mohou využít mechanismy uzamčení systému souborů ke správě přístupu ke sdíleným souborům – včetně možnosti provádět pesimistické zamykání. Když klient SMB otevře soubor, určuje režim přístupu k souboru i režimsdílení. Nastavení možnosti přístupu k souborům "Zápis" nebo "Čtení/Zápis" spolu s režimem sdílení souborů "Žádný" bude mít za následek, že soubor bude uzamčen klientem SMB, dokud nebude soubor uzavřen. Pokud rest operace je pokus o soubor, kde má soubor uzamčen soubor uzamčené služby REST vrátí stavový kód 409 (Conflict) s kódem chyby SharingViolation.  

Když klient SMB otevře soubor pro odstranění, označí soubor jako čekající na odstranění, dokud nebudou uzavřeny všechny ostatní otevřené popisovače klienta SMB. Zatímco soubor je označen jako čekající odstranění, všechny operace REST v tomto souboru vrátí stavový kód 409 (Conflict) s kódem chyby SMBDeletePending. Stavový kód 404 (nebyl nalezen) není vrácen, protože je možné, aby klient SMB před zavřením souboru odstranil příznak čekající na odstranění. Jinými slovy, stavový kód 404 (Nebyl nalezen) se očekává pouze v případě, že byl soubor odebrán. Všimněte si, že zatímco soubor je ve stavu SMB čekající odstranění, nebudou zahrnuty ve výsledcích seznam souborů. Všimněte si také, že operace REST Delete File a REST Delete Directory jsou potvrzeny atomicky a nevedou k čekajícímu stavu odstranění.  

Další informace naleznete v tématu:  

* [Správa uzamčení souborů](https://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="next-steps"></a>Další kroky

Pro celou ukázkovou aplikaci uvedenou v tomto blogu:  

* [Správa souběžnosti pomocí Azure Storage – ukázková aplikace](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Další informace o Azure Storage najdete v tématu:  

* [Domovská stránka úložiště Microsoft Azure](https://azure.microsoft.com/services/storage/)
* [Seznámení se službou Azure Storage](storage-introduction.md)
* Začínáme úložiště pro [objekt Blob](../blobs/storage-dotnet-how-to-use-blobs.md), [Tabulka](../../cosmos-db/table-storage-how-to-use-dotnet.md), [Fronty](../storage-dotnet-how-to-use-queues.md)a [Soubory](../storage-dotnet-how-to-use-files.md)
* Architektura úložiště – [Azure Storage: Vysoce dostupná služba cloudového úložiště se silnou konzistencí](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

