---
title: Správa souběžnosti
titleSuffix: Azure Storage
description: Naučte se spravovat souběžnost v Azure Storage pro objekty blob, front, tabulek a souborové služby. Seznamte se s využitím tří hlavních strategií souběžnosti dat.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 2732781d32e92c8ec03116988e33ec4fbe0b2330
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89021557"
---
# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Správa souběžnosti v Microsoft Azure Storage

Moderní internetové aplikace obvykle mají více uživatelů, kteří prohlížejí a aktualizují data současně. To vyžaduje, aby vývojáři aplikací pečlivě prostudovali, jak zajistit předvídatelné prostředí koncovým uživatelům, zejména pro scénáře, kdy může aktualizovat stejná data více uživatelů. Existují tři hlavní strategie souběžnosti dat, které vývojáři obvykle uvažují:  

1. Optimistická souběžnost – aplikace, která provádí aktualizaci, bude v rámci své aktualizace ověřovat, jestli se data od posledního načtení těchto dat od aplikace změnila. Například pokud dva uživatelé, kteří si prohlížejí stránku wikiwebu, provedou aktualizaci na stejnou stránku, musí platforma wiki zajistit, aby druhá aktualizace přepsala první aktualizaci – a aby oba uživatelé pochopili, jestli byla aktualizace úspěšná. Tato strategie se nejčastěji používá ve webových aplikacích.
2. Pesimistická souběžnost – aplikace, která se má provést aktualizace, pozastaví zámek u objektu, který ostatním uživatelům brání v aktualizaci dat, dokud se zámek neuvolní. Například ve scénáři hlavní/podřízená replikace dat, kde pouze hlavní server provede aktualizace, bude hlavní server obvykle uchovávat výhradní zámek po delší dobu na data, aby se zajistilo, že ho nikdo jiný nebude moct aktualizovat.
3. Poslední zapisovač WINS – přístup, který umožňuje, aby všechny operace aktualizace pokračovaly bez ověření, jestli jiná aplikace aktualizovala data, protože aplikace nejdřív data načetla. Tato strategie (nebo nedostatečná formální strategie) se obvykle používá tam, kde jsou data rozdělená takovým způsobem, že neexistují žádná pravděpodobnost, že k datům budou mít přístup více uživatelů. Může být také užitečné, pokud jsou zpracovávány krátkodobé datové streamy.  

Tento článek poskytuje přehled o tom, jak platforma Azure Storage zjednodušuje vývoj tím, že poskytuje prvotřídní podporu pro všechny tři tyto strategie souběžnosti.  

## <a name="azure-storage-simplifies-cloud-development"></a>Azure Storage zjednodušuje vývoj v cloudu

Služba Azure Storage podporuje všechny tři strategie, i když je jejich schopnost poskytovat úplnou podporu pro optimistickou a pesimistickou souběžnost, protože byla navržena tak, aby procházela se silným modelem konzistence, který zaručuje, že když služba úložiště potvrdí operaci vložení nebo aktualizace dat, zobrazí se nejnovější aktualizace. Platformy úložiště, které používají model s možnou konzistencí, mají prodlevu mezi tím, kdy je zápis prováděn jedním uživatelem, a když se aktualizovaná data mohou zobrazit jiní uživatelé, což ztěžuje vývoj klientských aplikací, aby nedocházelo k nekonzistencím, aby ovlivnily koncové uživatele.  

Kromě toho, že byste měli vybrat vhodné vývojové strategie, by si měli být vědomi, jak platforma úložiště izoluje změny – zejména změny u stejného objektu napříč transakcemi. Služba Azure Storage používá izolaci snímků, aby mohla operace čtení probíhat souběžně s operacemi zápisu v rámci jednoho oddílu. Na rozdíl od jiných úrovní izolace zaručuje izolaci snímků, že všechny čtení uvidí konzistentní snímek dat, i když probíhá aktualizace, a to v podstatě vrácením naposledy potvrzených hodnot během zpracování transakce aktualizace.  

## <a name="managing-concurrency-in-blob-storage"></a>Správa souběžnosti v úložišti objektů BLOB

Můžete se rozhodnout pro správu přístupu k objektům blob a kontejnerům v Blob service použít buď optimistické, nebo pesimistické modely souběžnosti. Pokud explicitně nezadáte strategii poslední zápisy, výchozí hodnota je WINS.  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Optimistická souběžnost pro objekty BLOB a kontejnery

Služba Storage přiřadí identifikátor každému uloženému objektu. Tento identifikátor se aktualizuje pokaždé, když se u objektu provede operace aktualizace. Identifikátor se vrátí klientovi jako součást odpovědi HTTP GET pomocí hlavičky ETag (značka entity), která je definovaná v protokolu HTTP. Uživatel, který provádí aktualizaci takového objektu, může odeslat v původním ETag spolu s podmíněnou hlavičkou, aby se zajistilo, že k aktualizaci dojde pouze v případě, že byla splněna určitá podmínka – v tomto případě je podmínka záhlaví "If-Match", které vyžaduje, aby služba úložiště zajistila, že hodnota ETag zadaná v žádosti o aktualizaci je stejná jako ta, která je uložená ve službě  

Osnova tohoto procesu je následující:  

1. Načtení objektu BLOB ze služby úložiště, odpověď obsahuje hodnotu hlavičky ETag protokolu HTTP, která identifikuje aktuální verzi objektu ve službě úložiště.
2. Při aktualizaci objektu BLOB zahrňte hodnotu ETag, kterou jste dostali v kroku 1 v podmíněné hlavičce **If-Match** žádosti, kterou odešlete do služby.
3. Služba porovnává hodnotu ETag v žádosti s aktuální hodnotou ETag objektu BLOB.
4. Pokud je aktuální hodnota ETag objektu BLOB odlišná od verze ETag v podmíněné hlavičce **If-Match** v žádosti, služba vrátí klientovi chybu 412. To indikuje klientovi, že objekt BLOB aktualizoval jiný proces, protože ho klient získal.
5. Pokud má aktuální hodnota ETag objektu BLOB stejnou verzi jako ETag v podmíněné hlavičce **If-Match** v žádosti, služba provede požadovanou operaci a aktualizuje aktuální hodnotu ETag objektu blob, aby ukázala, že vytvořil novou verzi.  

Následující fragment kódu jazyka C# (pomocí knihovny úložiště klienta 4.2.0) ukazuje jednoduchý příklad, jak vytvořit **AccessCondition If-Match** na základě hodnoty ETag, která je k dispozici z vlastností objektu blob, který byl dříve načten nebo vložen. Pak použije objekt **AccessCondition** při aktualizaci objektu BLOB: objekt **AccessCondition** přidá do žádosti hlavičku **If-Match** . Pokud se objekt BLOB aktualizoval jiným procesem, Blob service vrátí stavovou zprávu HTTP 412 (Předběžná podmínka selhala). Úplnou ukázku si můžete stáhnout tady: [Správa souběžnosti pomocí Azure Storage](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).  

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

Azure Storage také zahrnuje podporu dalších podmíněných hlaviček, jako je například **If-Modified-od**, **if-unmodified-od** a **If-None-Match** , a také jejich kombinace. Další informace najdete v tématu [určení podmíněných hlaviček pro operace služby BLOB Service](https://msdn.microsoft.com/library/azure/dd179371.aspx).  

Následující tabulka shrnuje operace kontejneru, které přijímají podmíněná záhlaví, například **If-Match** v žádosti a vrací hodnotu ETag v odpovědi.  

| Operace | Vrátí hodnotu ETag kontejneru. | Akceptuje podmíněná záhlaví |
|:--- |:--- |:--- |
| Vytvoření kontejneru |Yes |No |
| Získat vlastnosti kontejneru |Yes |No |
| Získat metadata kontejneru |Yes |No |
| Nastavení metadat kontejneru |Yes |Yes |
| Získat seznam ACL kontejneru |Yes |No |
| Nastavení seznamu ACL kontejneru |Yes |Ano (*) |
| Odstranění kontejneru |No |Yes |
| Kontejner zapůjčení |Yes |Yes |
| Výpis objektů BLOB |No |No |

(*) Oprávnění definovaná pomocí SetContainerACL jsou uložená v mezipaměti a aktualizace těchto oprávnění trvá 30 sekund, než se rozšíří, protože aktualizace nejsou zaručené jako konzistentní.  

Následující tabulka shrnuje operace objektů blob, které přijímají podmíněná záhlaví, jako je například **If-Match** v požadavku a vrací hodnotu ETag v odpovědi.

| Operace | Vrátí hodnotu ETag. | Akceptuje podmíněná záhlaví |
|:--- |:--- |:--- |
| Vložení objektu blob |Yes |Yes |
| Získat objekt BLOB |Yes |Yes |
| Získání vlastností objektu blob |Yes |Yes |
| Nastavení vlastností objektu BLOB |Yes |Yes |
| Získat metadata objektu BLOB |Yes |Yes |
| Nastavení metadat objektu BLOB |Yes |Yes |
| Objekt BLOB zapůjčení (*) |Yes |Yes |
| Pořízení snímku objektu blob |Yes |Yes |
| Zkopírování objektu blob |Yes |Ano (pro zdrojový a cílový objekt BLOB) |
| Přerušit kopii objektu BLOB |No |No |
| Odstranění objektu blob |No |Yes |
| Blok vložení |No |No |
| Seznam blokovaných umístění |Yes |Yes |
| Získat seznam blokovaných webů |Yes |No |
| Vložit stránku |Yes |Yes |
| Získat rozsahy stránek |Yes |Yes |

(*) Objekt BLOB zapůjčení nemění ETag u objektu BLOB.  

### <a name="pessimistic-concurrency-for-blobs"></a>Pesimistická souběžnost pro objekty blob

K uzamknutí objektu BLOB pro výhradní použití můžete získat [zapůjčení](https://msdn.microsoft.com/library/azure/ee691972.aspx) . Po získání zapůjčení určíte, jak dlouho budete potřebovat zapůjčení: může to být v rozmezí od 15 do 60 sekund nebo nekonečno, což platí pro výhradní zámek. Omezené zapůjčení můžete prodloužit tak, že ho budete moct prodloužit a po dokončení práce můžete uvolnit jakékoli zapůjčení. Při vypršení platnosti Blob service automaticky uvolňuje konečná zapůjčení.  

Zapůjčení umožňují podporovat různé strategie synchronizace, včetně exkluzivního zápisu/sdíleného čtení, exkluzivního zápisu/exkluzivního čtení a sdíleného zápisu/výhradní čtení. V případě, že existuje zapůjčení, vynutí služba úložiště exkluzivní zápisy (operace PUT, set a Delete). zajištění výhradních operací pro operace čtení ale vyžaduje, aby vývojář zajistil, že všechny klientské aplikace používají ID zapůjčení a že v jednom okamžiku má k dispozici pouze jeden klient s platným ID zapůjčení. Operace čtení, které neobsahují ID zapůjčení, mají za následek sdílené čtení.  

Následující fragment kódu jazyka C# ukazuje příklad získání exkluzivního zapůjčení po dobu 30 sekund v objektu blob, aktualizaci obsahu objektu BLOB a uvolnění zapůjčení. Pokud je v objektu BLOB při pokusu o získání nového zapůjčení již platné zapůjčení, Blob service vrátí výsledek stavu "HTTP (409)" konfliktu ". Následující fragment kódu používá objekt **AccessCondition** k zapouzdření informací o zapůjčení, když odešle požadavek na aktualizaci objektu BLOB ve službě úložiště.  Úplnou ukázku si můžete stáhnout tady: [Správa souběžnosti pomocí Azure Storage](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

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

Pokud se pokusíte o operaci zápisu u pronajatého objektu BLOB bez předání ID zapůjčení, požadavek se nezdaří a zobrazí se chyba 412. Všimněte si, že pokud zapůjčení vyprší před voláním metody **UploadText** , ale přesto PŘEdáte ID zapůjčení, požadavek se také nezdařil s chybou **412** . Další informace o správě časů vypršení platnosti zapůjčení a ID zapůjčení najdete v dokumentaci k [zapůjčení objektu BLOB](https://msdn.microsoft.com/library/azure/ee691972.aspx) REST.  

Následující operace objektů BLOB můžou použít zapůjčení ke správě pesimistické souběžnosti:  

* Vložení objektu blob
* Získat objekt BLOB
* Získání vlastností objektu blob
* Nastavení vlastností objektu BLOB
* Získat metadata objektu BLOB
* Nastavení metadat objektu BLOB
* Odstranění objektu blob
* Blok vložení
* Seznam blokovaných umístění
* Získat seznam blokovaných webů
* Vložit stránku
* Získat rozsahy stránek
* Objekt BLOB snímku – ID zapůjčení je volitelné, pokud existuje zapůjčení.
* Kopírovat objekt BLOB – ID zapůjčení je povinné, pokud v cílovém objektu BLOB existuje zapůjčení.
* Přerušit kopii objektu BLOB – vyžaduje se ID zapůjčení, pokud v cílovém objektu BLOB existuje nekonečné zapůjčení.
* Operace Lease Blob  

### <a name="pessimistic-concurrency-for-containers"></a>Pesimistická souběžnost pro kontejnery

Zapůjčení u kontejnerů umožňuje, aby byly stejné strategie synchronizace jako u objektů BLOB (exkluzivní zápis/sdílení čtení, exkluzivní zápis/výhradní čtení a sdílení zápisu/exkluzivní čtení) na rozdíl od objektů blob, ale služba úložiště vynutila výhradně výhradní operace odstranění. Aby mohl klient odstranit kontejner s aktivním zapůjčením, musí do žádosti o odstranění zahrnout aktivní ID zapůjčení. Všechny ostatní operace kontejneru jsou úspěšné na pronajatém kontejneru bez zahrnutí ID zapůjčení, v takovém případě se jedná o sdílené operace. Pokud je vyžadováno právo na aktualizaci (Put nebo Set) nebo operace čtení, vývojáři by měli zajistit, aby všichni klienti používali ID zapůjčení a aby měl pouze jeden klient v jednom okamžiku platné ID zapůjčení.  

Následující operace kontejneru můžou použít zapůjčení ke správě pesimistické souběžnosti:  

* Odstranění kontejneru
* Získat vlastnosti kontejneru
* Získat metadata kontejneru
* Nastavení metadat kontejneru
* Získat seznam ACL kontejneru
* Nastavení seznamu ACL kontejneru
* Kontejner zapůjčení  

Další informace naleznete v tématech:  

* [Určení hlaviček podmínek pro operace Blob service](https://msdn.microsoft.com/library/azure/dd179371.aspx)
* [Kontejner zapůjčení](https://msdn.microsoft.com/library/azure/jj159103.aspx)
* [Operace Lease Blob](https://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-table-storage"></a>Správa souběžnosti v úložišti tabulek

Table service používá kontroly souběžného souběžnosti jako výchozí chování při práci s entitami, na rozdíl od Blob service, kde musíte explicitně zvolit, aby se prováděly kontroly optimistického řízení souběžnosti. Dalším rozdílem mezi tabulkami a službami BLOB Services je, že je možné spravovat pouze chování souběžnosti entit, zatímco s Blob service můžete spravovat souběžnost kontejnerů a objektů BLOB.  

Pokud chcete použít optimistickou souběžnost a ověřit, jestli jiný proces změnil entitu, protože jste ji načetli ze služby Table Storage, můžete použít hodnotu ETag, která se zobrazí, když služba Table vrátí entitu. Osnova tohoto procesu je následující:  

1. Načte entitu ze služby Table Storage. odpověď obsahuje hodnotu ETag, která identifikuje aktuální identifikátor přidružený k dané entitě ve službě úložiště.
2. Když aktualizujete entitu, zahrňte hodnotu ETag, kterou jste dostali v kroku 1 v hlavičce povinného **If-Match** žádosti, kterou odešlete do služby.
3. Služba porovnává hodnotu ETag v žádosti s aktuální hodnotou ETag entity.
4. Pokud je aktuální hodnota ETag entity odlišná od značky ETag v povinné hlavičce **If-Match** v žádosti, služba vrátí klientovi chybu 412. To indikuje klientovi, že entita aktualizovala jiný proces, protože ji klient načetl.
5. Pokud je aktuální hodnota ETag entity stejná jako značka ETag v povinné hlavičce **If-Match** v požadavku nebo hlavička **If-Match** obsahuje zástupný znak (*), služba provede požadovanou operaci a aktualizuje aktuální hodnotu ETag entity, aby ukázala, že byla aktualizována.  

Všimněte si, že na rozdíl od Blob service služba Table Service vyžaduje, aby klient zahrnul hlavičku **If-Match** do žádostí o aktualizaci. Je ale možné vynutit nepodmíněnou aktualizaci (poslední strategii služby WINS pro zápis) a obejít kontrolu souběžnosti, pokud klient v žádosti nastaví hlavičku **If-Match** na zástupný znak (*).  

Následující fragment kódu jazyka C# ukazuje entitu zákazníka, která byla dříve vytvořena nebo načtena s aktualizovanou e-mailovou adresou. Operace prvotního vložení nebo načtení uloží hodnotu ETag do objektu Customer, protože ukázka používá stejnou instanci objektu při provádění operace Replace, automaticky pošle hodnotu ETag zpátky do služby Table Service a tím umožňuje službě kontrolovat narušení souběžnosti. Pokud se entita v úložišti tabulek aktualizovala jiným procesem, služba vrátí stavovou zprávu HTTP 412 (Předběžná podmínka selhala).  Úplnou ukázku si můžete stáhnout tady: [Správa souběžnosti pomocí Azure Storage](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

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

Chcete-li explicitně zakázat kontrolu souběžnosti, měli byste nastavit vlastnost **ETag** objektu **Employee** na "*" před provedením operace Replace.  

```csharp
customer.ETag = "*";  
```

Následující tabulka shrnuje, jak operace entity tabulky používají hodnoty ETag:

| Operace | Vrátí hodnotu ETag. | Vyžaduje hlavičku If-Match žádosti. |
|:--- |:--- |:--- |
| Entity dotazu |Yes |No |
| Vložit entitu |Yes |No |
| Aktualizovat entitu |Yes |Yes |
| Sloučit entitu |Yes |Yes |
| Odstranit entitu |No |Yes |
| Vložit nebo nahradit entitu |Yes |No |
| Vložit nebo sloučit entitu |Yes |No |

Všimněte si, že operace **vložení nebo nahrazení entit** a **vložení nebo sloučení entit** *neprovede žádné* kontroly souběžnosti, protože neodesílají hodnotu ETag do služby Table Service.  

V obecných vývojářích, kteří používají tabulky, by měly při vývoji škálovatelných aplikací spoléhat na optimistickou souběžnost. Pokud je potřeba pesimistické zamykání, můžou při přístupu k tabulkám podniknout vývojáři, kteří přiřadí určený objekt BLOB pro každou tabulku a před tím, než bude v tabulce pracovat, se pokusí o objekt BLOB převzít zapůjčení. Tento přístup vyžaduje aplikaci, aby se zajistilo, že všechny cesty k datům získají zapůjčení před tím, než se v tabulce začne pracovat. Je také potřeba si uvědomit, že minimální doba zapůjčení je 15 sekund, což vyžaduje pečlivou pozornost škálovatelnosti.  

Další informace naleznete v tématech:  

* [Operace s entitami](https://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>Správa souběžnosti ve službě front

Jedním z scénářů, ve kterých je souběžnost ve frontě ve službě Queueing, je, že více klientů načítá zprávy z fronty. Pokud je z fronty načtena zpráva, odpověď obsahuje zprávu a hodnotu pro příjem pop, která je vyžadována k odstranění zprávy. Zpráva není automaticky odstraněna z fronty, ale po jejím načtení není viditelná pro ostatní klienty v časovém intervalu určeném parametrem visibilitytimeout. Očekává se, že klient, který načte zprávu, odstraní zprávu po jejím zpracování a před časem specifikovaným prvkem TimeNextVisible odpovědi, který se vypočítá na základě hodnoty parametru visibilitytimeout. Hodnota visibilitytimeout je přidána do času, ve kterém je načtena zpráva k určení hodnoty TimeNextVisible.  

Služba front nemá podporu pro optimistickou ani pesimistickou souběžnost a z tohoto důvodu klienti zpracovávající zprávy načtené z fronty mají jistotu, že se zprávy zpracovávají idempotentní způsobem. Pro operace aktualizace, jako je SetQueueServiceProperties, SetQueueMetaData, SetQueueACL a UpdateMessage, se používá poslední strategie zapisovače WINS.  

Další informace naleznete v tématech:  

* [Rozhraní REST API služby Queue Service](https://msdn.microsoft.com/library/azure/dd179363.aspx)
* [Získat zprávy](https://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-azure-files"></a>Správa souběžnosti v souborech Azure

K Souborové službě se dá dostat pomocí dvou různých koncových bodů protokolu – SMB a REST. Služba REST nepodporuje buď optimistické zamykání, nebo pesimistické zamykání a všechny aktualizace budou následovat po strategii služby WINS pro poslední zapisovače. Klienti SMB, kteří připojují sdílené složky, můžou využít mechanismy zamykání systému souborů ke správě přístupu ke sdíleným souborům – včetně možnosti provádět pesimistické zamykání. Když klient SMB otevře soubor, určí režim přístupu k souboru i sdílení. Nastavení možnosti přístupu k souboru pro zápis nebo čtení a zápis spolu s režimem sdílení souborů "žádné" způsobí, že se soubor zamkne klientem SMB, dokud nebude soubor uzavřený. Pokud se při pokusu o operaci REST u souboru, kde je u klienta SMB soubor zamčený, služba REST vrátí stavový kód 409 (konflikt) s kódem chyby SharingViolation.  

Když klient SMB otevře soubor pro odstranění, označí soubor jako nedokončený, dokud nebudou všechny ostatní otevřené popisovače protokolu SMB v tomto souboru zavřeny. Když je soubor označený jako čeká na odstranění, všechny operace REST na tomto souboru vrátí stavový kód 409 (konflikt) s kódem chyby SMBDeletePending. Stavový kód 404 (Nenalezeno) se nevrátí, protože klient SMB může před zavřením souboru odstranit příznak nedokončeného odstranění. Jinými slovy, stavový kód 404 (Nenalezeno) se očekává jenom v případě, že byl soubor odebraný. Všimněte si, že když je soubor ve stavu čekání na odstranění protokolu SMB, nebude zahrnutý do výsledků souborů seznamu. Všimněte si také, že operace odstranění souboru REST a odstranění adresáře REST jsou potvrzeny atomicky a nevedou k nedokončenému stavu odstranění.  

Další informace naleznete v tématech:  

* [Správa zámků souborů](https://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="next-steps"></a>Další kroky

Úplnou ukázkovou aplikaci, na kterou se odkazuje na tomto blogu:  

* [Správa souběžnosti pomocí Azure Storage ukázkové aplikace](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Další informace o Azure Storage najdete v těchto tématech:  

* [Microsoft Azure Storage domovskou stránku](https://azure.microsoft.com/services/storage/)
* [Seznámení se službou Azure Storage](storage-introduction.md)
* Začínáme úložiště pro objekty [BLOB](../blobs/storage-dotnet-how-to-use-blobs.md), [tabulky](../../cosmos-db/table-storage-how-to-use-dotnet.md),  [fronty](../storage-dotnet-how-to-use-queues.md)a [soubory](../storage-dotnet-how-to-use-files.md)
* Architektura úložiště – [Azure Storage: vysoce dostupná služba cloudového úložiště s silnou konzistencí](https://docs.microsoft.com/archive/blogs/windowsazurestorage/sosp-paper-windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency)

