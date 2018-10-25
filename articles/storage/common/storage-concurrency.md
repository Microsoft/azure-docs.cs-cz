---
title: Správa souběžnosti v Microsoft Azure Storage
description: Správa souběžnosti pro služby objektů Blob, fronty, tabulky a souboru
services: storage
author: jasontang501
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 05/11/2017
ms.author: jasontang501
ms.component: common
ms.openlocfilehash: 91eb9c12a8913c0a96ee7c3133dc5f982c42cad7
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50025292"
---
# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Správa souběžnosti v Microsoft Azure Storage
## <a name="overview"></a>Přehled
Moderní aplikace na základě Internet obvykle mít více uživatelů, zobrazování a aktualizace dat současně. To vyžaduje, aby pečlivě zvážit, jak poskytnout předvídatelný prostředí pro koncové uživatele, zejména pro scénáře, kde můžete víc uživatelů aktualizovat stejná data vývojáři aplikace. Existují tři hlavní data souběžnosti strategie, které vývojáři obvykle vezměte v úvahu:  

1. Optimistická souběžnost – provádění aplikace, které aktualizace jako součást její aktualizace ověří, pokud se data změnila od aplikace posledního čtení tato data. Například pokud dvě uživatelům, kteří zobrazují wikistránky provést aktualizaci na stejnou stránku pak platformu wiki musíte zajistit, že druhý aktualizace nepřepisuje první aktualizace – a, že oba uživatelům pochopit, jestli jejich aktualizace byla úspěšná, či nikoli. Tato strategie je nejčastěji používají ve webových aplikacích.
2. Pesimistická souběžnost – aplikace hledání se provede aktualizaci se zámek objektu bránit ostatním uživatelům v aktualizaci dat, dokud se zámek je uvolněn. Například ve scénáři model hlavní/podřízený se data replikace, kde pouze hlavní provede aktualizace hlavní bude obvykle obsahovat výhradní zámek pro delší dobu na data, která mají Ujistěte se, že nikdo jiný ji aktualizovat.
3. Poslední zápis – přístup, který umožňuje všechny operace aktualizace pokračovat bez ověření, pokud všechny ostatní aplikace se aktualizovala data od aplikace nejprve číst data. Tato strategie (nebo chybějící formální strategie) se obvykle používá kde data jsou rozdělená tak, že neexistuje žádná pravděpodobnost, že více uživatelů se přístup ke stejným datům. To může být také užitečné kde krátkodobou datových proudů zpracovává.  

Tento článek obsahuje přehled, jak platformy úložiště Azure usnadňuje vývoj tím, že poskytuje prvotřídní podporu pro všechny tři z těchto strategií souběžnosti.  

## <a name="azure-storage--simplifies-cloud-development"></a>Azure Storage – zjednodušuje vývoj pro cloudové prostředí
Službu Azure storage podporuje všechny tři strategie, i když je zvláštní v jeho schopnost poskytují úplnou podporu pro optimistické a Pesimistická souběžnost, protože byl navržen a využívat model pro zajištění konzistence, což zaručuje, že Služba Storage potvrzení dat vložte nebo všechny operace aktualizace dál přistupuje, aby mohl která data se zobrazí nejnovější aktualizace. Úložiště platformy, používající model konečné konzistence mají s menší prodlevou mezi při provádění zápis uživatelů a ostatním uživatelům, tedy komplikovali vývoji klientských aplikací, aby se zabránilo nekonzistencím z můžou vidět aktualizovaná data vliv na koncové uživatele.  

Kromě výběru strategie odpovídající souběžnosti vývojáři měli také něco vědět o jak platforma úložiště izoluje změny – zejména změny na stejný objekt napříč transakce. Službu Azure storage používá izolaci snímku k umožnění operací čtení, která se provede současně operace zápisu v rámci jednoho oddílu. Na rozdíl od jiných úrovních izolace zaručuje izolaci snímku, které všechny operace čtení se zobrazí snímek konzistentní vzhledem k aplikacím dat dokonce i za běhu dochází k aktualizace – v podstatě tak, že vrací naposledy potvrzeny hodnoty při aktualizaci zpracování transakce.  

## <a name="managing-concurrency-in-blob-storage"></a>Správa souběžnosti v úložišti objektů Blob
Můžete se rozhodnout použít buď modely souběžného zpracování optimistického nebo pesimistického ke správě přístupu k objektům BLOB a kontejnerů ve službě blob service. Pokud explicitně neurčíte strategie poslední wins zápisy je výchozí nastavení.  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Pro objekty BLOB a kontejnerů optimistického řízení souběžnosti
Služba úložiště přiřadí identifikátor každé objekt uložený. Tento identifikátor se aktualizuje pokaždé, když se operace aktualizace se provádí na objekt. Identifikátor je vrácen do klienta jako součást pomocí hlavičky značky ETag (značka entity), která je definována v rámci protokolu HTTP odpovědi HTTP GET. Provedení aktualizace na takový objekt uživatele a zkontrolujte, že aktualizace se vztahuje pouze na Pokud byly splněny určité podmínky – v tomto případě je podmínka hlavičku "If-Match", který vyžaduje službu Storage t odeslání původní ETag spolu s podmíněnou hlavičku o Ujistěte se, že hodnota značka ETag zadaná v žádosti o aktualizaci je shodná se ukládají ve službě úložiště.  

Přehled tohoto procesu je následujícím způsobem:  

1. Načtení objektu blob ze služby storage, odpověď obsahuje hodnotu hlavičky značky ETag protokolu HTTP, který identifikuje aktuální verzi objektu ve službě úložiště.
2. Při aktualizaci objektu blob patří hodnota ETag jste získali v kroku 1 **If-Match** podmíněnou hlavičku požadavku odeslat do služby.
3. Služba porovnává hodnota ETag v požadavku s aktuální hodnotou ETag objektu blob.
4. Pokud je aktuální hodnotou ETag objektu blob jinou verzi než Etagu v **If-Match** podmíněnou hlavičku v žádosti o službu vrátí chybu 412 klientovi. To znamená do klienta, že jiným procesem má aktualizovat objekt blob, protože jejím načtení klienta.
5. Pokud aktuální hodnotou ETag objektu blob se stejnou verzí jako značka ETag v **If-Match** podmíněnou hlavičku v žádosti o službu, provede požadovanou operaci a aktualizuje aktuální hodnotou ETag objektu blob zobrazit, že bylo vytvořeno Nová verze.  

Následující (s využitím klientské knihovny pro úložiště 4.2.0) fragment kódu jazyka C# ukazuje jednoduchý příklad toho, jak vytvořit **If-Match AccessCondition** na základě hodnoty ETag, která je přístupná z vlastností objektu blob, který byl dříve buď načíst ani vloženy. Poté použije **AccessCondition** objektu při aktualizaci objektu blob: **AccessCondition** objekt přidá **If-Match** záhlaví žádosti. Pokud se objekt blob byl aktualizován jiným procesem, službu blob service vrátí zprávu stav HTTP 412 (Předběžná podmínka je neplatná). Úplnou ukázku si můžete stáhnout: [Správa souběžnosti pomocí služby Azure Storage](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).  

```csharp
// Retrieve the ETag from the newly created blob
// Etag is already populated as UploadText should cause a PUT Blob call
// to storage blob service which returns the ETag in response.
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

Služby úložiště, jako také zahrnuje podporu pro další podmíněné záhlaví **If-Modified-Since**, **If-bez jakýchkoli úprav-Since** a **If-None-Match** stejně jako jejich kombinace. Další informace najdete v tématu [zadání podmíněné hlavičky operace služby objektů Blob](http://msdn.microsoft.com/library/azure/dd179371.aspx) na webové stránce MSDN.  

Následující tabulka shrnuje operace kontejnerů, které podmíněné hlavičky accept, jako **If-Match** v požadavku a, která vrátí hodnotu značky ETag do odpovědi.  

| Operace | Vrátí hodnotu ETag kontejneru | Přijímá podmíněné záhlaví |
|:--- |:--- |:--- |
| Vytvoření kontejneru |Ano |Ne |
| Získat vlastnosti kontejneru |Ano |Ne |
| Získání metadat kontejneru |Ano |Ne |
| Nastavte Metadata kontejneru |Ano |Ano |
| Získání seznamu ACL kontejneru |Ano |Ne |
| Nastavení ACL kontejneru |Ano |Ano (*) |
| Odstranění kontejneru |Ne |Ano |
| Zapůjčení kontejneru |Ano |Ano |
| Výpis objektů BLOB |Ne |Ne |

(*) Oprávnění určené SetContainerACL jsou uložené v mezipaměti a aktualizace tato oprávnění trvat 30 sekund na dokončení propagace, během nichž aktualizace nemusí být konzistentní vzhledem k aplikacím.  

Následující tabulka shrnuje operace objektů blob, které podmíněné hlavičky accept, jako **If-Match** v požadavku a, která vrátí hodnotu značky ETag do odpovědi.

| Operace | Vrátí hodnotu značky ETag | Přijímá podmíněné záhlaví |
|:--- |:--- |:--- |
| Vložení objektu Blob |Ano |Ano |
| Získání objektu Blob |Ano |Ano |
| Získání vlastností objektu Blob |Ano |Ano |
| Nastavit vlastnosti objektu Blob |Ano |Ano |
| Získat Metadata objektu Blob |Ano |Ano |
| Nastavte Metadata objektu Blob |Ano |Ano |
| Zapůjčení objektu Blob (*) |Ano |Ano |
| Vytvoření snímku objektu Blob |Ano |Ano |
| Zkopírování objektu Blob |Ano |Ano (pro zdrojové a cílové objektů blob) |
| Přerušit objekt Blob kopírování |Ne |Ne |
| Odstranit objekt Blob |Ne |Ano |
| Vložit blok |Ne |Ne |
| Vložit blokovaných webů. |Ano |Ano |
| Získat seznam blokovaných položek |Ano |Ne |
| Vložit stránky |Ano |Ano |
| Získání rozsahů stránek |Ano |Ano |

(*) Zapůjčení objektu Blob nezmění značku ETag na objekt blob.  

### <a name="pessimistic-concurrency-for-blobs"></a>Pesimistická souběžnost pro objekty BLOB
Zamknout objekt blob pro výhradní použití, můžete získat [zapůjčení](http://msdn.microsoft.com/library/azure/ee691972.aspx) na něj. Když získáte zapůjčení, určíte, jak dlouho je nutné zapůjčení: to může být pro mezi 15 až 60 sekund nebo nekonečno, který částky výhradní zámek. Obnovení můžete provést omezené zapůjčení ji rozšířit, a až skončíte s ním můžete uvolnit všechny zapůjčení. Službu blob service automaticky uvolní omezené zapůjčení po vypršení jejich platnosti.  

Zapůjčení povolení synchronizace různé strategie a proto není podporován, včetně výhradní zápis / sdílené čtení, výhradní zápisu / výhradně číst a sdílet zápisu / čtení vylučují. Pokud existuje zapůjčení na službu storage vynucuje exkluzivní zápisu (put, nastavte a operace odstraňování) ale zajistit exkluzivitu, kterou pro operace čtení vyžaduje pro vývojáře tím zajistit, že všechny klientské aplikace používají ID zapůjčení a tento klient pouze jeden po druhém ID platné zapůjčení. Operace čtení, které neobsahují výsledek ID zapůjčení sdílené čtení.  

Následující jazyka C# fragment kódu ukazuje příklad získání exkluzivní zapůjčení na 30 sekund na objekt blob, aktualizuje obsah objektu blob a pak uvolníte zapůjčení. Pokud už existuje platné zapůjčení u objektu blob při pokusu získat nové zapůjčení, službu blob service vrátí výsledek stav "Konflikt HTTP (409)". Následující fragment kódu používá **AccessCondition** objektu k zapouzdření informací o zapůjčení, pokud odešle požadavek na aktualizaci objektu blob služby storage.  Úplnou ukázku si můžete stáhnout: [Správa souběžnosti pomocí služby Azure Storage](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

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

Pokud se pokusíte operaci zápisu zapůjčení objektu BLOB bez při předávání ID zapůjčení, požadavek selže s chybou 412. Všimněte si, že pokud vyprší platnost zapůjčení před voláním **UploadText** metoda ale stále předat ID zapůjčení, požadavek selže i s **412** chyby. Další informace o správě dobu vypršení platnosti zapůjčení a ID zapůjčení, najdete v článku [zapůjčení objektu Blob](http://msdn.microsoft.com/library/azure/ee691972.aspx) dokumentace k REST.  

Následující operace objektů blob můžete použít ke správě Pesimistická souběžnost zapůjčení:  

* Vložení objektu Blob
* Získání objektu Blob
* Získání vlastností objektu Blob
* Nastavit vlastnosti objektu Blob
* Získat Metadata objektu Blob
* Nastavte Metadata objektu Blob
* Odstranit objekt Blob
* Vložit blok
* Vložit blokovaných webů.
* Získat seznam blokovaných položek
* Vložit stránky
* Získání rozsahů stránek
* Vytvoření snímku objektu Blob - ID zapůjčení volitelné, pokud existuje zapůjčení
* Objekt Blob kopírování – ID zapůjčení povinné, pokud existuje zapůjčení na cílový objekt blob
* Objekt Blob kopírování přerušení - ID zapůjčení vyžaduje existenci nekonečné zapůjčení na cílový objekt blob
* Zapůjčení objektu Blob  

### <a name="pessimistic-concurrency-for-containers"></a>Pesimistická souběžnost pro kontejnery
Zapůjčení v kontejnerech povolit stejné strategie synchronizace podporovat jako objekty BLOB (výhradní zápisu / sdílené čtení, výhradní zápisu / výhradně číst a sdílet zápisu / čtení výhradně) ale na rozdíl od objekty BLOB služby storage pouze exkluzivitu, kterou na vynucuje operace odstranění. Pokud chcete odstranit kontejner s aktivní zapůjčení, klient musí obsahovat ID aktivní zapůjčení s žádostí o odstranění. Všechny ostatní operace kontejneru proběhnout úspěšně, v kontejneru zapůjčení včetně ID zapůjčení v takovém případě jsou sdílené operace. Pokud aktualizace (put nebo sadu) nebo operace čtení exkluzivitu, kterou je potřeba pak vývojáři se ujistěte, že všichni klienti používat ID zapůjčení a který pouze jeden klientský současně má platné zapůjčení ID.  

Následující operace kontejnerů můžete použít ke správě Pesimistická souběžnost zapůjčení:  

* Odstranění kontejneru
* Získat vlastnosti kontejneru
* Získání metadat kontejneru
* Nastavte Metadata kontejneru
* Získání seznamu ACL kontejneru
* Nastavení ACL kontejneru
* Zapůjčení kontejneru  

Další informace naleznete v tématu:  

* [Určení podmíněné záhlaví pro operace služby objektů Blob](http://msdn.microsoft.com/library/azure/dd179371.aspx)
* [Zapůjčení kontejneru](http://msdn.microsoft.com/library/azure/jj159103.aspx)
* [Zapůjčení objektu Blob ](http://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-the-table-service"></a>Správa souběžnosti v službě Table Service
Služba table service používá optimistické souběžnosti kontrol jako výchozí chování při práci s entitami, na rozdíl od blob service, kde je nutné explicitně vybrat k provedení kontroly optimistické souběžnosti. Rozdíl mezi službami tabulek a objektů blob je, že službou blob service můžete spravovat souběžnosti kontejnerů a objektů BLOB můžete pouze spravovat souběžnosti chování entit.  

Chcete-li použít optimistické řízení souběžnosti a zaškrtněte, pokud jiný proces upravit entity, protože jste získali z služba table storage, můžete použít hodnota ETag, který jste dostali, když se vrátí entity služby table service. Přehled tohoto procesu je následujícím způsobem:  

1. Načtení entity ze služby table storage, odpověď obsahuje hodnotu ETag, která identifikuje aktuální identifikátor přidružený k entitě služby úložiště.
2. Při aktualizaci entity zahrnují jste získali v kroku 1 povinná hodnota ETag **If-Match** záhlaví požadavku odeslat do služby.
3. Služba porovnává hodnota ETag v požadavku s aktuální hodnotou ETag entity.
4. Pokud aktuální hodnotou ETag entity neodpovídá Etagu v povinných **If-Match** záhlaví v žádosti o službu vrátí chybu 412 klientovi. To znamená do klienta jiný proces aktualizoval entitu od klienta jejím načtení.
5. Pokud aktuální značka ETag hodnota entity je stejný jako značka ETag v povinných **If-Match** hlavičky v požadavku nebo **If-Match** záhlaví obsahuje zástupný znak (*), služba provádí požadovanou operaci a aktualizuje aktuální hodnotou ETag, která má být zobrazit, že byl aktualizován.  

Upozorňujeme, že na rozdíl od služby blob service, služby table service vyžaduje klient patří **If-Match** záhlaví v žádosti o aktualizaci. Je však možné vynutit Nepodmíněný aktualizovat (poslední zapisovače služby wins strategie) a obejít řízení souběžnosti, pokud se nastaví klienta **If-Match** záhlaví zástupný znak (*) v žádosti.  

Následující fragment C# ukazuje entita zákazník, který byl dříve vytvořena nebo načíst s e-mailová adresa aktualizovat. Počáteční vložit nebo načíst operace úložiště hodnota ETag v objektu zákazníků, a protože příklad používá stejnou instanci objektu při provádění operaci nahrazení, ho odešle zpět do služby table service umožňuje službě automaticky hodnota ETag Kontrola souběžnosti porušení. Pokud jiný proces se aktualizovala entity ve službě table storage, služba vrátí zprávu stav HTTP 412 (Předběžná podmínka je neplatná).  Úplnou ukázku si můžete stáhnout: [Správa souběžnosti pomocí služby Azure Storage](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

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

Explicitně zakázat kontrolu souběžnosti, byste měli nastavit **ETag** vlastnost **zaměstnance** objektu "*" před provedením operace nahrazení.  

```csharp
customer.ETag = "*";  
```

Následující tabulka shrnuje použití značky ETag hodnoty operace entitu tabulky:

| Operace | Vrátí hodnotu značky ETag | Vyžaduje hlavičky žádosti If-Match |
|:--- |:--- |:--- |
| Dotazování entit |Ano |Ne |
| Vložte Entity |Ano |Ne |
| Aktualizace Entity |Ano |Ano |
| Sloučit Entity |Ano |Ano |
| Odstranit entitu |Ne |Ano |
| Vložení nebo nahrazení Entity |Ano |Ne |
| Vložit nebo sloučit Entity |Ano |Ne |

Všimněte si, že **vložení nebo nahrazení Entity** a **vložení nebo sloučit Entity** provést operace *není* provést jakékoli souběžnosti, protože se neodesílají na hodnotu značky ETag do tabulky Služba.  

Vývojářům, kteří používají tabulky obecně by se neměla spoléhat na optimistického řízení souběžnosti, při vytváření škálovatelných aplikací. V případě potřeby pesimistické zamykání můžete jedním z přístupů vývojáři si při přístupu k tabulek je přiřadit určeného objektu blob pro každou tabulku a pokuste se provést zapůjčení pro objekt blob před fungujícími v tabulce. Tento přístup vyžaduje, aby aplikace k zajištění všechny cesty k datům přístup získat zapůjčení před fungujícími v tabulce. Také nezapomeňte přitom, že doba zapůjčení minimální je 15 sekund, které vyžaduje důkladné zvážení pro zajištění škálovatelnosti.  

Další informace naleznete v tématu:  

* [Operace s entitami](http://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>Správa souběžnosti ve službě Queue
Jeden scénář, ve které souběžnosti je problém v jejich zařazování do fronty služby je, kde jsou více klientů načítání zpráv z fronty. Při načtení zprávy z fronty odpověď obsahuje zprávy a potvrzení hodnotu, která je vyžadována k odstranění zprávy. Zpráva není automaticky odstraněn z fronty, ale poté, co se má načíst, není viditelné pro ostatní klienty pro časový interval zadaný parametrem visibilitytimeout. K odstranění zprávy po jejich zpracování a před časový limit určený parametrem TimeNextVisible element odpovědi, které se počítá na základě hodnoty parametru visibilitytimeout očekává klienta, který načte zprávu. Hodnota visibilitytimeout se přidá do doby načtení zprávy k určení hodnoty TimeNextVisible.  

Služba fronty nemá podporu pro optimistického nebo pesimistického souběžnosti a pro tuto klientů z důvodu zpracování zprávy načtené z fronty by měl zajistit zpracování zpráv způsobem idempotentní. Poslední strategie wins zapisovače se používá pro operace aktualizace, jako je například SetQueueServiceProperties, SetQueueMetaData, UpdateMessage a SetQueueACL.  

Další informace naleznete v tématu:  

* [Rozhraní REST API služby front](http://msdn.microsoft.com/library/azure/dd179363.aspx)
* [Získání zprávy](http://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-the-file-service"></a>Správa souběžnosti v souboru služby
Služba souborů lze přistupovat pomocí dva koncové body jiný protokol – SMB a REST. Podpora optimistické uzamykání, nebo pesimistické zamykání nemá žádné služby REST a všechny aktualizace brzo bude následovat poslední strategie wins zapisovače. Klienti SMB, které aktuálně připojit sdílené složky můžete využít soubor zamykání mechanismy systému pro správu přístupu ke sdíleným souborům – včetně možnosti provádět pesimistické zamykání. Po otevření souboru v klientovi SMB určuje sdílet i přístup k souborům režimu. Nastavení možnosti přístupu k souborům "Write" nebo "R/w" spolu se sdílenou složku režim "None", bude výsledkem soubor je uzamčen klientovi SMB dlouho, dokud se nezavře soubor. Při pokusu o operaci REST na soubor, ve kterém klientovi SMB je soubor uzamčen službu REST se vrátit stavový kód 409 (konflikt) s kódem chyby SharingViolation.  

Když klientovi SMB se otevře soubor pro odstranění, označí soubor jako čekající na odstranění do jiných klientů protokolu SMB jsou uzavřené otevřenými popisovači v tomto souboru. Když soubor je označená jako probíhající odstranění, všechny operace REST na tento soubor vrátí stavový kód 409 (konflikt) s kódem chyby SMBDeletePending. Protože je možné, klient SMB můžete odebrat příznak čeká na odstranění před zavřením souboru není vrátil stavový kód 404 (Nenalezeno). Jinými slovy stavový kód 404 (Nenalezeno) se očekávají jenom, když se ho odebral. Všimněte si, že soubor je v SMB odstranit stav Čekání na vyřízení, nebude zahrnutý ve výsledcích seznam souborů. Nezapomeňte, že operace REST odstranit soubor a adresář odstraňte REST usilujeme o to atomicky se výsledek není ve stavu čekající na odstranění.  

Další informace naleznete v tématu:  

* [Správa souborů uzamkne](http://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="summary-and-next-steps"></a>Shrnutí a další kroky
Služby Microsoft Azure Storage byly navržené pro potřeby nejsložitější online aplikace bez vynucení ohrožení bezpečnosti nebo znovu zamysleli nad důležité předpoklady, například souběžnosti a konzistenci dat, které se vývojáře udělit.  

Pro kompletní ukázkovou aplikaci odkazovat v tomto blogu:  

* [Správa souběžnosti pomocí služby Azure Storage – ukázková aplikace](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Další informace o Azure Storage najdete:  

* [Microsoft Azure Storage domovské stránky](https://azure.microsoft.com/services/storage/)
* [Seznámení se službou Azure Storage](storage-introduction.md)
* Začínáme se Storage [Blob](../blobs/storage-dotnet-how-to-use-blobs.md), [tabulky](../../cosmos-db/table-storage-how-to-use-dotnet.md), [fronty](../storage-dotnet-how-to-use-queues.md), a [soubory](../storage-dotnet-how-to-use-files.md)
* Architektura úložiště – [služby Azure Storage: vysoce dostupné cloudové služby úložiště se silnou konzistenci](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

