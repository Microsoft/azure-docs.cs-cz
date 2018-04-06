---
title: Správa souběžnosti v Microsoft Azure Storage
description: Jak spravovat souběžnosti pro služby objektů Blob, fronty, tabulky a soubor
services: storage
documentationcenter: ''
author: jasontang501
manager: tadb
editor: tysonn
ms.assetid: cc6429c4-23ee-46e3-b22d-50dd68bd4680
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/11/2017
ms.author: jasontang501
ms.openlocfilehash: 937cca66a0af0674b868e6a87681adbea330e91c
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Správa souběžnosti v Microsoft Azure Storage
## <a name="overview"></a>Přehled
Moderní aplikace na základě Internetu obvykle mají více uživatelů, zobrazování a aktualizace dat současně. To vyžaduje vývojáři aplikace k byste pečlivě zvážit, jak zajistit předvídatelný prostředí pro své koncové uživatele, zejména pro scénáře, kde více uživatelů můžete aktualizovat stejná data. Existují tři strategie souběžnosti hlavní data, které vývojáři obvykle zvážit:  

1. Optimistickou metodu souběžného – provádění aplikace, aktualizace bude jako součást jeho aktualizace ověřte, jestli se od aplikace změnil data poslední přečíst data. Například pokud dva uživatelé zobrazení na stránce wikiwebu provádět aktualizace na stejné stránce pak platformou wiki musí zkontrolujte, že druhé aktualizace nepřepíší první aktualizace – a oba uživatele jasné, jestli jejich aktualizace proběhla úspěšně, či nikoli. Tato strategie se nejčastěji používá v webových aplikací.
2. Pesimistické souběžnosti – aplikace vyhledávání se k provedení aktualizace bude trvat zámek objektu brání aktualizace dat, dokud se uvolní zámek jiných uživatelů. Například v případě hlavní nebo podřízený data replikace kde pouze hlavní provede aktualizace je hlavní server bude obvykle obsahovat výhradní zámek po delší dobu času na data a ujistěte se, že nikdo jiný ji aktualizuje.
3. Poslední zapisovače služby wins – přístup, která umožňuje všechny operace aktualizace pokračovat bez ověření, pokud jiná aplikace má aktualizovat data od aplikace nejprve číst data. Tato strategie (nebo nedostatečná strategie formální) se obvykle používá kde dat rozdělena na oddíly tak, že neexistuje žádná pravděpodobnost, že více uživatelů se přístup ke stejným datům. Může také být užitečné, kde jsou zpracovávány krátkodobou datové proudy.  

Tento článek obsahuje přehled jak platformou Azure Storage usnadňuje vývoj díky prvotřídní podporu pro všechny tři strategie tyto souběžnosti.  

## <a name="azure-storage--simplifies-cloud-development"></a>Úložiště Azure – usnadňuje vývoj cloudu
Služba úložiště Azure podporuje všechny tři strategie, i když je charakteristické v jeho schopnost poskytovat plnou podporu pro pesimistické a optimistickou metodu souběžného zpracování, protože byl navržen zapojení silnou konzistenci model, který zaručuje, že pokud Služba úložiště potvrdí vložení dat nebo operace aktualizace všechny další přistupuje na která data se zobrazí nejnovější aktualizace. Úložiště platformy, které používají model konzistence typu případné mít prodleva mezi, pokud se provádí zápis, jeden uživatel a aktualizovaná data můžete si je prohlížet ostatní uživatelé proto komplikují vývoj klientských aplikací, aby se zabránilo nekonzistence z dopad na koncové uživatele.  

Kromě Výběr strategie odpovídající souběžnosti musí být vývojáři taky vědět, jak úložiště platformy izoluje změn – zvlášť změny do stejného objektu napříč transakce. Služba úložiště Azure používá izolaci snímku umožňující operace čtení provést souběžně operace zápisu v rámci jednoho oddílu. Na rozdíl od jiných úrovních izolace izolaci snímku zaručuje, že všechny operace čtení najdete v části konzistentního snímku dat i když se vyskytnou aktualizace – v podstatě vrácením poslední potvrzené hodnoty při aktualizaci zpracování transakce.  

## <a name="managing-concurrency-in-blob-storage"></a>Správa souběžnosti v úložišti objektů Blob
Můžete se rozhodnout používat buď modely pesimistické nebo optimistickou metodu souběžného zpracování můžete spravovat přístup k objektům BLOB a kontejnerů ve službě blob. Pokud nezadáte explicitně strategie poslední zápisy wins je výchozí.  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Optimistickou metodu souběžného pro objekty BLOB a kontejnery
Služby úložiště přiřadí identifikátor každé objekt uložené. Tento identifikátor se aktualizuje pokaždé, když se operace aktualizace se provádí na objekt. Identifikátor je vrácen do klienta v rámci metody GET protokolu HTTP odpovědi HTTP pomocí hlavičku ETag (značky entity), který je definován v rámci protokolu HTTP. Uživatel provedení aktualizace na takový objekt může odesílat v původní značka ETag společně s podmíněným záhlaví zajistit, aby aktualizace dojde pouze pokud byla splněna určitá podmínka – v takovém případě je podmínka vyhodnocena jako hlavičku "If-Match", který vyžaduje službu úložiště t o Ujistěte se, že hodnota značka ETag zadaná v žádosti o aktualizaci je stejný jako uložené ve službě úložiště.  

Obrys tento proces je následující:  

1. Načtení objektu blob ze služby storage, odpověď obsahuje hodnotu hlavičky protokolu HTTP ETag identifikující aktuální verze objektu ve službě úložiště.
2. Při aktualizaci objektu blob obsahoval hodnotu značka ETag se vám zobrazila v kroku 1 **If-Match** podmíněného hlavičky požadavku odeslat do služby.
3. Služba porovnává hodnotu ETag v žádosti se aktuální hodnota ETag objektu blob.
4. Pokud je aktuální hodnota ETag objektu blob jinou verzi než ETag v **If-Match** podmíněného záhlaví v žádosti o službu vrátí chybu 412 klientovi. To znamená klientovi, že má jiným procesem od klienta jejím načtení aktualizace objektu blob.
5. Pokud je aktuální hodnota ETag objektu blob stejnou verzi jako značka ETag v **If-Match** podmíněného záhlaví v žádosti o službu provede požadovanou operaci a aktualizuje aktuální hodnota ETag objektu blob ukazují, že bylo vytvořeno novou verzi.  

Následující (s použitím knihovny klienta úložiště 4.2.0) fragment kódu jazyka C# ukazuje jednoduchý příklad toho, jak vytvořit **AccessCondition If-Match** založená na hodnotě značka ETag, která je k němu přistupovat z vlastnosti objektu blob, která byla dříve buď Vložit ani načíst. Poté použije **AccessCondition** objektu při aktualizaci objektu blob: **AccessCondition** přidá objekt **If-Match** hlavičky k žádosti. Pokud objekt blob byl aktualizován jiným procesem, vrátí se služby objektů blob zpráva stav HTTP 412 (nezdařil se předběžnou podmínku). Si můžete stáhnout úplnou ukázku: [Správa souběžnosti použití služby Azure Storage](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).  

```csharp
// Retrieve the ETag from the newly created blob
// Etag is already populated as UploadText should cause a PUT Blob call
// to storage blob service which returns the etag in response.
string orignalETag = blockBlob.Properties.ETag;

// This code simulates an update by a third party.
string helloText = "Blob updated by a third party.";

// No etag, provided so orignal blob is overwritten (thus generating a new etag)
blockBlob.UploadText(helloText);
Console.WriteLine("Blob updated. Updated ETag = {0}",
blockBlob.Properties.ETag);

// Now try to update the blob using the orignal ETag provided when the blob was created
try
{
    Console.WriteLine("Trying to update blob using orignal etag to generate if-match access condition");
    blockBlob.UploadText(helloText,accessCondition:
    AccessCondition.GenerateIfMatchCondition(orignalETag));
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
    {
        Console.WriteLine("Precondition failure as expected. Blob's orignal etag no longer matches");
        // TODO: client can decide on how it wants to handle the 3rd party updated content.
    }
    else
        throw;
}  
```

Služba úložiště, jako také zahrnuje podporu pro další podmíněného záhlaví **If-upravit-Since**, **If-Unmodified-Since** a **If-None-Match** a také jejich kombinace. Další informace najdete v tématu [zadání podmíněného hlavičky pro operace služby objektů Blob](http://msdn.microsoft.com/library/azure/dd179371.aspx) na webu MSDN.  

Následující tabulka shrnuje kontejneru operace, které podmíněného hlavičky accept, jako **If-Match** v požadavku a že vrátit hodnotu ETag v odpovědi.  

| Operace | Vrátí hodnotu ETag kontejneru | Přijímá podmíněného hlavičky |
|:--- |:--- |:--- |
| Vytvoření kontejneru |Ano |Ne |
| Získat vlastnosti kontejneru |Ano |Ne |
| Získat Metadata kontejneru |Ano |Ne |
| Nastavit Metadata kontejneru |Ano |Ano |
| Získání kontejneru seznamu ACL |Ano |Ne |
| Nastavit kontejneru seznamu ACL |Ano |Ano (*) |
| Odstranit kontejner |Ne |Ano |
| Zapůjčení kontejneru |Ano |Ano |
| Seznam objektů BLOB |Ne |Ne |

(*) Oprávnění definované SetContainerACL jsou uložené v mezipaměti a aktualizace těchto oprávnění trvat 30 sekund potřebný k šíření během nichž aktualizace nemusí být konzistentní.  

Následující tabulka shrnuje operace objektů blob, které podmíněného hlavičky accept, jako **If-Match** v požadavku a že vrátit hodnotu ETag v odpovědi.

| Operace | Vrátí hodnotu ETag | Přijímá podmíněného hlavičky |
|:--- |:--- |:--- |
| Uveďte objektů Blob |Ano |Ano |
| Získání objektu Blob |Ano |Ano |
| Získat vlastnosti objektu Blob |Ano |Ano |
| Nastavit vlastnosti objektů Blob |Ano |Ano |
| Získat Metadata objektu Blob |Ano |Ano |
| Nastavit Metadata objektu Blob |Ano |Ano |
| Objekt Blob zapůjčení (*) |Ano |Ano |
| Objekt Blob snímku |Ano |Ano |
| Zkopírování objektu Blob |Ano |Ano (pro zdrojový a cílový objekt blob) |
| Abort – objekt Blob kopie |Ne |Ne |
| Odstranit objekt Blob |Ne |Ano |
| Uveďte bloku |Ne |Ne |
| Uveďte seznam blokovaných položek |Ano |Ano |
| Získat seznam blokovaných položek |Ano |Ne |
| Uveďte stránky |Ano |Ano |
| Get rozsahů stránek |Ano |Ano |

(*) Objekt Blob zapůjčení nezmění značku ETag na objekt blob.  

### <a name="pessimistic-concurrency-for-blobs"></a>Pesimistické souběžnosti pro objekty BLOB
Zamknout objekt blob pro výhradní použití, můžete získat [zapůjčení](http://msdn.microsoft.com/library/azure/ee691972.aspx) na něm. Když získáte zapůjčení, určíte, jak dlouho je nutné zapůjčení: to může být pro mezi 15 až 60 sekund nebo nekonečné, která dosáhne výhradní zámek. Můžete obnovit také omezené zapůjčení ji rozšířit, a můžete uvolnit všechny zapůjčení, když jste hotovi s ním. Služba objektů blob automaticky uvolní konečné zapůjčení po vypršení jejich platnosti.  

Zapůjčení povolit různých synchronizace strategie podporovaná, včetně výhradní zápis / sdílené zápisu pro čtení, výhradní / exkluzivní čtení a zápisu sdílené / číst vylučují. Tam, kde existuje zapůjčení služby úložiště vynucuje výhradní zápisy (put, nastavení a operace odstranění) ale zajistit výhradní právo pro operace čtení vyžaduje vývojáři zajistěte, aby všechny klientské aplikace používají ID zapůjčení, a že pouze jeden klient současně má ID platný zapůjčení. Operace čtení, které neobsahují výsledku ID zapůjčení sdílené čtení.  

Následující fragment C# ukazuje příklad získávání výhradní zapůjčení pro 30 sekund na objekt blob, aktualizace obsahu objektu blob a potom uvolnění zapůjčení. Pokud je již platná zapůjčení na objekt blob při pokusu o získání nového zapůjčení, služby objektů blob vrátí výsledek stav "Konflikt HTTP (409)". Následující fragment kódu používá **AccessCondition** objekt, který chcete informace o zapůjčení pro zapouzdření při zadávání požadavku k aktualizaci objektu blob ve službě úložiště.  Si můžete stáhnout úplnou ukázku: [Správa souběžnosti použití služby Azure Storage](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

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

Pokud se pokusíte operaci zápisu na objekt blob zapůjčených bez předávání ID zapůjčení, požadavek selže s 412 chyby. Všimněte si, že pokud zapůjčení vyprší před voláním **UploadText** metoda ale stále předat ID zapůjčení, požadavek se také nezdaří s **412** chyby. Další informace o správě časy vypršení platnosti zapůjčení a zapůjčení ID najdete v tématu [zapůjčení Blob](http://msdn.microsoft.com/library/azure/ee691972.aspx) dokumentace k REST.  

Následující operace objektů blob můžete použít ke správě pesimistické souběžnosti zapůjčení:  

* Uveďte objektů Blob
* Získání objektu Blob
* Získat vlastnosti objektu Blob
* Nastavit vlastnosti objektů Blob
* Získat Metadata objektu Blob
* Nastavit Metadata objektu Blob
* Odstranit objekt Blob
* Uveďte bloku
* Uveďte seznam blokovaných položek
* Získat seznam blokovaných položek
* Uveďte stránky
* Get rozsahů stránek
* Objekt Blob snímku - ID zapůjčení nepovinná, pokud existuje zapůjčení
* Kopírování objektu Blob - ID zapůjčení vyžaduje, pokud existuje zapůjčení na cílový objekt blob
* Abort – objekt Blob kopírování – zapůjčení ID vyžaduje, pokud existuje nekonečné zapůjčení na cílový objekt blob
* Objekt Blob zapůjčení  

### <a name="pessimistic-concurrency-for-containers"></a>Pesimistické souběžnosti pro kontejnery
Zapůjčení kontejnerům povolit stejné strategie synchronizace podporovaný jako na objekty BLOB (exkluzivní zápisu / sdílené zápisu pro čtení, výhradní / exkluzivní čtení a zápisu sdílené / číst exkluzivní) ale na rozdíl od objektů BLOB služby úložiště pouze vynucuje výhradní právo na operace odstranění. Pokud chcete odstranit kontejner s aktivní zapůjčení, klient musí obsahovat ID aktivní zapůjčení s požadavku na odstranění. Všechny ostatní operace kontejneru proběhnout úspěšně při zapůjčených kontejner bez zahrnutí ID zapůjčení v takovém případě jsou sdílené operace. Pokud se vyžaduje výhradní právo na aktualizace (put nebo sadu) nebo operací čtení pak vývojáři měli zajistěte, aby že všichni klienti používat ID zapůjčení a že jen jeden klientský současně má platný zapůjčení ID.  

Následující operace kontejneru můžete použít ke správě pesimistické souběžnosti zapůjčení:  

* Odstranit kontejner
* Získat vlastnosti kontejneru
* Získat Metadata kontejneru
* Nastavit Metadata kontejneru
* Získání kontejneru seznamu ACL
* Nastavit kontejneru seznamu ACL
* Zapůjčení kontejneru  

Další informace naleznete v tématu:  

* [Určení podmíněného hlavičky pro operace služby objektů Blob](http://msdn.microsoft.com/library/azure/dd179371.aspx)
* [Zapůjčení kontejneru](http://msdn.microsoft.com/library/azure/jj159103.aspx)
* [Objekt Blob zapůjčení ](http://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-the-table-service"></a>Správa souběžnost v služby Table
Služba table používá optimistickou metodu, souběžnosti kontroly jako výchozí chování při práci s entitami, na rozdíl od služby objektů blob, kde je nutné provést kontroly optimistickou metodu souběžného explicitně vybrat. Rozdíl mezi službami tabulky a objektů blob je, že službou objektů blob můžete spravovat souběžnosti kontejnery a objekty BLOB můžete pouze spravovat souběžnosti chování entit.  

Chcete-li použít optimistickou metodu souběžného a zkontrolovat, pokud jiný proces upravit entitu vzhledem k tomu, že jste získali z tabulky úložiště služby, můžete hodnota ETag, kterou dostanete při služby table vrátí entity. Obrys tento proces je následující:  

1. Načtení entity ze služby table storage, odpověď obsahuje hodnotu značka ETag, která identifikuje aktuální identifikátor přidružený ke dané entity v úložišti služby.
2. Při aktualizaci entity obsahoval hodnotu ETag získaný v kroku 1 v povinné **If-Match** hlavičky požadavku odeslat do služby.
3. Služba porovnává hodnotu ETag v žádosti se aktuální hodnota ETag entity.
4. Pokud je aktuální hodnota ETag entity se liší od ETag v povinné **If-Match** záhlaví v žádosti o službu vrátí chybu 412 klientovi. To znamená klientovi, že má jiným procesem od klienta jejím načtení aktualizace entity.
5. Pokud je aktuální hodnota ETag entity, která je stejná jako značka ETag v povinné **If-Match** hlavičky v požadavku nebo **If-Match** hlavička obsahuje zástupný znak (*), provede služba požadovanou operaci a aktualizuje aktuální hodnota ETag entity, která má zobrazit, že byla aktualizována.  

Všimněte si, že na rozdíl od služby objektů blob služby table vyžaduje klienta zahrnout **If-Match** záhlaví v žádosti o aktualizaci. Je však možné vynutit nepodmíněné aktualizovat (poslední zapisovače wins strategie) a obejít kontrolách souběžnosti, pokud se nastaví klienta **If-Match** hlavičky k zástupný znak (*) v žádosti.  

Následující fragment C# ukazuje entitu zákazníka, která byla dříve buď vytvořit nebo načíst s e-mailové adresy aktualizovat. Počáteční vložit nebo načtení operace úložiště ETag hodnoty v objektu zákazníka, a protože ukázku používá stejnou instanci objektu, když se provede operaci nahrazení, automaticky odesílá hodnota ETag zpět do služby table, povolení služby Kontrola souběžnosti porušení. Pokud jiný proces byl aktualizován entity ve službě table storage, službu vrátí zprávu stav HTTP 412 (nezdařil se předběžnou podmínku).  Si můžete stáhnout úplnou ukázku: [Správa souběžnosti použití služby Azure Storage](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

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

Kontrola souběžnosti explicitně zakázat, byste měli nastavit **značka ETag** vlastnost **zaměstnanec** do objektu "*" před spuštěním operaci nahrazení.  

```csharp
customer.ETag = "*";  
```

Následující tabulka shrnuje, jak používat činnosti tabulka entity ETag hodnoty:

| Operace | Vrátí hodnotu ETag | Vyžaduje hlavička If-Match požadavku |
|:--- |:--- |:--- |
| Dotaz entity |Ano |Ne |
| Vložení Entity |Ano |Ne |
| Aktualizace Entity |Ano |Ano |
| Sloučení Entity |Ano |Ano |
| Odstranění Entity |Ne |Ano |
| Vložení nebo nahrazení Entity |Ano |Ne |
| Vložení nebo sloučení Entity |Ano |Ne |

Všimněte si, že **vložení nebo nahrazení Entity** a **vložení nebo sloučení Entity** provést operace *není* provést jakékoli kontrolách souběžnosti, protože se neodesílají hodnotu značky ETag do tabulky Služba.  

Obecně vývojáře, kteří používají tabulky se spoléhají na optimistickou metodu souběžného při vývoji škálovatelné aplikace. V případě potřeby pesimistické zamykání může trvat jeden ze způsobů vývojáři při přístupu k tabulky je přiřadit určený objekt blob pro každou tabulku a zkuste provést zapůjčení u objektu blob před operační v tabulce. Tento přístup vyžadují, aby se zajistit všechny cesty k datům přístup získat zapůjčení před operační v tabulce. Také Upozorňujeme, že doba minimální zapůjčení je 15 sekund, což vyžaduje pečlivě zvážit pro škálovatelnost.  

Další informace naleznete v tématu:  

* [Operace na entity](http://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>Správa souběžnost v služby front
Jeden scénář, ve které souběžnosti je problém v rámci služby Řízení front služby je, kde jsou více klientů načítání zpráv z fronty. Pokud zpráva se načítají z fronty, odpověď obsahuje zprávu a hodnotu pop přijetí, která je potřeba odstranit zprávy. Zpráva není automaticky odstraněn z fronty, ale po byl načteny, se nezobrazuje pro ostatní klienty pro časový interval určený parametrem visibilitytimeout. Očekává se, že klienta, který načte zprávu zprávu odstranit po jeho zpracování a před časem určeného TimeNextVisible element odpovědi, které se počítá na základě hodnoty parametru visibilitytimeout. Čas načtení zprávy k určení hodnoty TimeNextVisible je přidána hodnota visibilitytimeout.  

Služba fronty nemá podpora souběžnosti optimistické nebo pesimistické a pro tento důvod klienti zpracování zpráv z fronty načíst by měl zajistěte, aby způsobem idempotent zpracování zpráv. Strategie poslední wins zapisovače se používá pro operace aktualizace, jako je například SetQueueServiceProperties, SetQueueMetaData, SetQueueACL a UpdateMessage.  

Další informace naleznete v tématu:  

* [Rozhraní API REST služby fronty](http://msdn.microsoft.com/library/azure/dd179363.aspx)
* [Získávání zpráv](http://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-the-file-service"></a>Správa souběžnost v služba souborů
Služba souborů je přístupný pomocí dva koncové body jiný protokol – protokolu SMB a REST. Službu REST nemá podporu pro optimistické zamykání nebo pesimistické zamykání a všechny aktualizace bude postupovat podle strategie poslední zapisovače služby wins. Klienti SMB, které připojení sdílené složky můžete využít soubor systému uzamčení mechanismy můžete spravovat přístup ke sdíleným souborům – včetně schopnost provádět pesimistické zamykání. Když klienti SMB otevře soubor, určuje, jak se přístup k souborům a sdílení režimu. Nastavení možnosti přístup k souborům "Zápisu" nebo "Pro čtení a zápis" spolu s režimem sdílené složky "Žádný" bude v souboru je uzamčený klienti SMB, dokud soubor zavřený. Pokud dojde k pokusu o souboru, kde klienti SMB má soubor uzamčen operaci REST ve službě REST vrátí stavový kód 409 (konflikt) s kódem chyby SharingViolation.  

Když klienti SMB otevře soubor pro odstranění, označí soubor jako čekající na vyřízení odstranit, dokud všechny ostatní klienty protokolu SMB jsou uzavřeny otevřenými popisovači v souboru. Když soubor je označen jako čekající na odstranění, všechny operace REST u tohoto souboru vrátí stavový kód 409 (konflikt) s kódem chyby SMBDeletePending. Stavový kód 404 (není nalezena) nevrátí, protože je to možné, odeberte příznak čeká na odstranění před zavírání souboru klientovi SMB. Jinými slovy stavový kód 404 (není nalezena) se očekávají jenom, když soubor byl odebrán. Všimněte si, že soubor je v SMB čeká na odstranění stavu, nebude zahrnutý ve výsledcích seznamu souborů. Všimněte si také, že operace REST odstranit soubor a odstraňte adresář REST se potvrdí atomicky a nezpůsobovalo ve stavu čeká na odstraňování.  

Další informace naleznete v tématu:  

* [Správa souborů zamkne](http://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="summary-and-next-steps"></a>Souhrn a další kroky
Služby Microsoft Azure Storage byly navržené tak, aby vyhovovaly nejsložitější online aplikace bez vynucení vývojářům ohrozit nebo přehodnotit předpoklady klíče návrh například souběžnosti a konzistenci dat, který se má provést pro oprávnění.  

Pro dokončení ukázkovou aplikaci, kterou se odkazuje v tomto blogu:  

* [Správa souběžnosti použití služby Azure Storage – ukázková aplikace](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Další informace o Azure Storage najdete v tématu:  

* [Microsoft Azure Storage domovské stránky](https://azure.microsoft.com/services/storage/)
* [Úvod do Azure Storage](storage-introduction.md)
* Začínáme se Storage [Blob](../blobs/storage-dotnet-how-to-use-blobs.md), [tabulky](../../cosmos-db/table-storage-how-to-use-dotnet.md), [fronty](../storage-dotnet-how-to-use-queues.md), a [soubory](../storage-dotnet-how-to-use-files.md)
* Architektura úložiště – [Azure Storage: vysoce dostupný Cloudová služba úložiště se silnou konzistenci](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

