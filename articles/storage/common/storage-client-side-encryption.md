---
title: Šifrování na straně klienta pomocí rozhraní .NET pro úložiště Microsoft Azure | Dokumenty společnosti Microsoft
description: Klientská knihovna úložiště Azure pro rozhraní .NET podporuje šifrování na straně klienta a integraci s Azure Key Vault pro maximální zabezpečení pro vaše aplikace Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/20/2017
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 6cf19292c3675382789ca25af7f9b7f69e9066fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255415"
---
# <a name="client-side-encryption-and-azure-key-vault-for-microsoft-azure-storage"></a>Šifrování na straně klienta a trezor klíčů Azure pro úložiště Microsoft Azure
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Přehled
[Klientská knihovna úložiště Azure pro rozhraní .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet) podporuje šifrování dat v klientských aplikacích před odesláním do Služby Azure Storage a dešifrování dat při stahování do klienta. Knihovna také podporuje integraci s [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů účtu úložiště.

Podrobný kurz, který vás provede procesem šifrování objektů BLOB pomocí šifrování na straně klienta a trezoru klíčů Azure, najdete [v tématu Šifrování a dešifrování objektů BLOB v Úložišti Microsoft Azure pomocí trezoru klíčů Azure](../blobs/storage-encrypt-decrypt-blobs-key-vault.md).

Šifrování na straně klienta pomocí Javy najdete v [tématu Šifrování na straně klienta pomocí javy pro úložiště Microsoft Azure](storage-client-side-encryption-java.md).

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Šifrování a dešifrování pomocí obálkové techniky
Procesy šifrování a dešifrování se řídí technikou obálky.

### <a name="encryption-via-the-envelope-technique"></a>Šifrování pomocí obálkové techniky
Šifrování pomocí obálky technika funguje následujícím způsobem:

1. Klientská knihovna úložiště Azure generuje šifrovací klíč obsahu (CEK), což je jednorázový symetrický klíč.
2. Uživatelská data jsou šifrována pomocí této CEK.
3. CEK je pak zabalen (šifrován) pomocí šifrovacího klíče (KEK). KEK je identifikován identifikátor klíče a může být asymetrický pár klíčů nebo symetrický klíč a lze spravovat místně nebo uloženy v trezorech klíčů Azure.
   
    Samotná knihovna klienta úložiště nikdy nemá přístup k KEK. Knihovna vyvolá algoritmus obtékání klíčů, který je k dispozici trezoru klíčů. Uživatelé si mohou v případě potřeby vybrat vlastní zprostředkovatele pro obtékání/rozbalení klíčů.

4. Šifrovaná data se pak nahrají do služby Azure Storage. Zabalený klíč spolu s některými dalšími šifrovacími metadaty je uložen jako metadata (na objektu blob) nebo interpolován šifrovanými daty (zprávy fronty a entity tabulky).

### <a name="decryption-via-the-envelope-technique"></a>Dešifrování pomocí obálkové techniky
Dešifrování pomocí obálky technika funguje následujícím způsobem:

1. Klientská knihovna předpokládá, že uživatel spravuje klíč šifrovací klíč (KEK) místně nebo v trezorech klíčů Azure. Uživatel nemusí znát konkrétní klíč, který byl použit pro šifrování. Místo toho lze nastavit a použít překladač klíčů, který řeší různé identifikátory klíčů.
2. Klientská knihovna stáhne šifrovaná data spolu s libovolným šifrovacím materiálem uloženým ve službě.
3. Zalomený šifrovací klíč obsahu (CEK) se pak rozbalí (dešifruje) pomocí šifrovacího klíče (KEK). Zde opět klientská knihovna nemá přístup k KEK. Jednoduše vyvolá algoritmus rozbalení vlastního nebo klíčového úložiště.
4. Šifrovací klíč obsahu (CEK) se pak používá k dešifrování šifrovaných uživatelských dat.

## <a name="encryption-mechanism"></a>Šifrovací mechanismus
Klientská knihovna úložiště používá [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) k šifrování uživatelských dat. Konkrétně režim [Cipher Block Chaining (CBC)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) s AES. Každá služba funguje poněkud jinak, takže budeme diskutovat o každém z nich zde.

### <a name="blobs"></a>Objekty blob
Klientská knihovna aktuálně podporuje šifrování pouze celých objektů BLOB. Konkrétně šifrování je podporováno, když uživatelé používají metody **UploadFrom** nebo **OpenWrite.** Pro stahování jsou podporovány úplné i rozsah stahování.

Během šifrování klientská knihovna vygeneruje náhodný inicializační vektor (IV) 16 bajtů spolu s náhodným šifrovacím klíčem obsahu (CEK) o 32 bajtů a provede šifrování obálek dat objektů blob pomocí těchto informací. Zabalená CEK a některá další šifrovací metadata jsou pak uložena jako metadata objektu blob spolu se šifrovaným objektem blob ve službě.

> [!WARNING]
> Pokud upravujete nebo nahráváte vlastní metadata pro objekt blob, musíte zajistit, aby tato metadata byla zachována. Pokud nahrajete nová metadata bez těchto metadat, zalomené CEK, IV a další metadata se ztratí a obsah objektu blob už nikdy nebude možné znovu načíst.
> 
> 

Stahování šifrovaného objektu blob zahrnuje načítání obsahu celého objektu blob pomocí metod pohodlí **DownloadTo**/**BlobReadStream.** Zabalená sada CEK je rozbalena a používá se společně s IV (v tomto případě uložená jako metadata objektů blob) k vrácení dešifrovaných dat uživatelům.

Stahování libovolný rozsah (**DownloadRange** metody) v šifrované objektu blob zahrnuje úpravu rozsahu poskytované uživateli s cílem získat malé množství dalších dat, které lze použít k úspěšnému dešifrování požadovaného rozsahu.

Všechny typy objektů blob (objekty BLOB bloku, objekty BLOB stránky a objekty BLOB) lze zašifrovat nebo dešifrovat pomocí tohoto schématu.

### <a name="queues"></a>Fronty
Vzhledem k tomu, že zprávy fronty mohou být libovolného formátu, definuje klientská knihovna vlastní formát, který obsahuje inicializační vektor (IV) a šifrovaný šifrovací klíč obsahu (CEK) v textu zprávy.

Během šifrování klientská knihovna generuje náhodné IV 16 bajtů spolu s náhodným CEK 32 bajtů a provádí šifrování obálky textu zprávy fronty pomocí těchto informací. Zabalená cek a některá další šifrovací metadata jsou pak přidána do šifrované zprávy fronty. Tato upravená zpráva (viz níže) je uložena ve službě.

    <MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>

Během dešifrování je zabalený klíč extrahován ze zprávy fronty a rozbalen. IV je také extrahován ze zprávy fronty a používá se spolu s nebaleným klíčem k dešifrování dat zprávy fronty. Všimněte si, že metadata šifrování je malý (pod 500 bajtů), takže zatímco se započítává do limitu 64 kB pro zprávu fronty, dopad by měl být zvládnutelný.

### <a name="tables"></a>Tabulky
Klientská knihovna podporuje šifrování vlastností entity pro operace vložení a nahrazení.

> [!NOTE]
> Sloučení není aktuálně podporováno. Vzhledem k tomu, že podmnožina vlastností mohla být dříve zašifrována pomocí jiného klíče, jednoduše slučování nových vlastností a aktualizace metadat bude mít za následek ztrátu dat. Sloučení buď vyžaduje provedení volání další služby ke čtení již existující entity ze služby nebo pomocí nového klíče na vlastnost, z nichž oba nejsou vhodné z důvodů výkonu.
> 
> 

Šifrování dat tabulky funguje takto:  

1. Uživatelé určují vlastnosti, které mají být šifrovány.
2. Klientská knihovna generuje náhodný inicializační vektor (IV) 16 bajtů spolu s náhodným šifrovacím klíčem obsahu (CEK) 32 bajtů pro každou entitu a provádí šifrování obálky na jednotlivých vlastnostech, které mají být šifrovány odvozením nového IV na vlastnost. Šifrovaná vlastnost je uložena jako binární data.
3. Zabalená CEK a některá další šifrovací metadata jsou pak uložena jako dvě další rezervované vlastnosti. První vyhrazená vlastnost (_ClientEncryptionMetadata1) je vlastnost řetězce, která obsahuje informace o IV, verzi a zabaleném klíči. Druhá vyhrazená vlastnost (_ClientEncryptionMetadata2) je binární vlastnost, která obsahuje informace o vlastnostech, které jsou šifrovány. Informace v této druhé vlastnosti (_ClientEncryptionMetadata2) jsou samy o sobě zašifrovány.
4. Vzhledem k těmto další vyhrazené vlastnosti potřebné pro šifrování, uživatelé mohou nyní mít pouze 250 vlastní vlastnosti namísto 252. Celková velikost entity musí být menší než 1 MB.

Všimněte si, že lze zašifrovat pouze vlastnosti řetězce. Pokud mají být šifrovány jiné typy vlastností, musí být převedeny na řetězce. Šifrované řetězce jsou uloženy ve službě jako binární vlastnosti a jsou převedeny zpět na řetězce po dešifrování.

U tabulek musí uživatelé kromě zásad šifrování zadat vlastnosti, které mají být šifrovány. To lze provést zadáním atributu [EncryptProperty] (pro entity POCO, které jsou odvozeny z TableEntity) nebo překládáním šifrování v možnostech požadavku. Překládání šifrování je delegát, který přebírá klíč oddílu, klíč řádku a název vlastnosti a vrátí logickou hodnotu, která označuje, zda má být tato vlastnost zašifrována. Během šifrování bude klientská knihovna používat tyto informace k rozhodnutí, zda má být vlastnost při zápisu do drátu šifrována. Delegát také poskytuje možnost logiky kolem jak jsou zašifrovány vlastnosti. (Například pokud X, pak šifrovat vlastnost A; jinak šifrovat vlastnosti A a B.) Všimněte si, že není nutné poskytnout tyto informace při čtení nebo dotazování entit.

### <a name="batch-operations"></a>Dávkové operace
V dávkových operacích bude stejný KEK použit ve všech řádcích v této dávkové operaci, protože klientská knihovna umožňuje pouze jeden objekt možností (a tedy jednu zásadu/KEK) na dávkovou operaci. Klientská knihovna však interně vygeneruje nové náhodné IV a náhodné CEK na řádek v dávce. Uživatelé mohou také zvolit šifrování různých vlastností pro každou operaci v dávce definováním tohoto chování v překládání šifrování.

### <a name="queries"></a>Dotazy
> [!NOTE]
> Vzhledem k tomu, že entity jsou šifrovány, nelze spustit dotazy, které filtrují šifrovanou vlastnost.  Pokud se pokusíte, výsledky budou nesprávné, protože služba by se pokouší porovnat šifrovaná data s nešifrovaná data.
> 
> 
> Chcete-li provést operace dotazu, je nutné zadat překládání klíčů, který je schopen přeložit všechny klíče v sadě výsledků. Pokud entitu obsaženou ve výsledku dotazu nelze přeložit zprostředkovateli, klientská knihovna vyvolá chybu. Pro všechny dotazy, které provádějí projekce na straně serveru, klientská knihovna přidá ve výchozím nastavení do vybraných sloupců speciální vlastnosti metadat šifrování (_ClientEncryptionMetadata1 a _ClientEncryptionMetadata2).

## <a name="azure-key-vault"></a>Azure Key Vault
Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Pomocí služby Azure Key Vault mohou uživatelé šifrovat klíče a tajné klíče (například ověřovací klíče, klíče účtu úložiště, klíče pro šifrování dat, . PFX soubory a hesla) pomocí klíčů, které jsou chráněny moduly hardwarového zabezpečení (HSM). Další informace najdete v článku [Co je Azure Key Vault](../../key-vault/key-vault-overview.md).

Klientská knihovna úložiště používá základní knihovnu úložiště klíčů k poskytování společného rámce pro správu klíčů v rámci celého Azure. Uživatelé také získat další výhody pomocí knihovny rozšíření trezoru klíčů. Knihovna rozšíření poskytuje užitečné funkce kolem jednoduchých a bezproblémových poskytovatelů místních a cloudových klíčů Symmetric/RSA, stejně jako s agregací a ukládáním do mezipaměti.

### <a name="interface-and-dependencies"></a>Rozhraní a závislosti
Existují tři balíčky trezoru klíčů:

* Microsoft.Azure.KeyVault.Core obsahuje IKey a IKeyResolver. Jedná se o malý balíček bez závislostí. Klientská knihovna úložiště pro rozhraní .NET ji definuje jako závislost.
* Microsoft.Azure.KeyVault obsahuje klienta REST trezoru klíčů.
* Microsoft.Azure.KeyVault.Extensions obsahuje rozšiřující kód, který obsahuje implementace kryptografických algoritmů a RSAKey a SymmetricKey. Závisí na oborech názvů Core a KeyVault a poskytuje funkce pro definování agregačního překládání (pokud uživatelé chtějí používat více zprostředkovatelů klíčů) a překladače klíče mezipaměti. Přestože knihovna klienta úložiště není přímo závislá na tomto balíčku, pokud uživatelé chtějí použít Azure Key Vault k ukládání svých klíčů nebo použít rozšíření trezoru klíčů využívat místní a cloudové zprostředkovatele kryptografických služeb, budou potřebovat tento balíček.

Trezor klíčů je určen pro hlavní klíče s vysokou hodnotou a omezení omezení na trezor klíčů jsou navržena s ohledem na toto. Při provádění šifrování na straně klienta pomocí trezoru klíčů je upřednostňovaným modelem použití symetrických hlavních klíčů uložených jako tajné klíče v trezoru klíčů a místně uložené v mezipaměti. Uživatelé musí provést následující akce:

1. Vytvořte tajný klíč offline a nahrajte ho do trezoru klíčů.
2. Použijte základní identifikátor tajného klíče jako parametr k vyřešení aktuální verze tajného klíče pro šifrování a ukládání těchto informací místně do mezipaměti. Pro ukládání do mezipaměti použijte pro ukládání do mezipaměti program CachingKeyResolver. od uživatelů se neočekává, že budou implementovat vlastní logiku ukládání do mezipaměti.
3. Při vytváření zásad šifrování použijte překladač mezipaměti jako vstup.

Další informace týkající se využití trezoru klíčů naleznete ve [vzorcích šifrovacího kódu](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples).

## <a name="best-practices"></a>Osvědčené postupy
Podpora šifrování je k dispozici pouze v knihovně klienta úložiště pro rozhraní .NET. Windows Phone a Windows Runtime momentálně nepodporují šifrování.

> [!IMPORTANT]
> Při použití šifrování na straně klienta si uvědomte tyto důležité body:
> 
> * Při čtení nebo zápisu do šifrovaného objektu blob použijte příkazy pro nahrávání celého objektu blob a příkazy pro stahování objektů blob. Vyhněte se zápisu do šifrovaného objektu blob pomocí operací protokolu, jako je blok Put, seznam blokovaných položek, zápis stránek, vymazat stránky nebo připojit blok; v opačném případě můžete poškozený objekt blob poškodit a učinit jej nečitelným.
> * Pro tabulky existuje podobné omezení. Dávejte pozor, abyste neaktualizovali šifrované vlastnosti bez aktualizace metadat šifrování.
> * Pokud nastavíte metadata na šifrovaném objektu blob, můžete přepsat metadata související s šifrováním požadovaná pro dešifrování, protože nastavení metadat není aditivní. To platí také pro snímky; vyhněte se zadávání metadat při vytváření snímku šifrovaného objektu blob. Pokud musí být nastavena metadata, nezapomeňte nejprve zavolat metodu **FetchAttributes,** abyste získali aktuální metadata šifrování a vyhnuli se souběžným zápisům při nastavování metadat.
> * Povolte vlastnost **RequireEncryption** ve výchozích možnostech požadavku pro uživatele, kteří by měli pracovat pouze se šifrovanými daty. Viz níže pro více informací.
> 
> 

## <a name="client-api--interface"></a>Klientské rozhraní API / rozhraní
Při vytváření objektu EncryptionPolicy mohou uživatelé poskytnout pouze klíč (implementaci iKey), pouze překládání (implementace IKeyResolver) nebo obojí. IKey je základní typ klíče, který je identifikován pomocí identifikátorklíče a který poskytuje logiku pro obtékání/rozbalování. IKeyResolver se používá k vyřešení klíče během procesu dešifrování. Definuje ResolveKey metoda, která vrací IKey daný identifikátor klíče. To poskytuje uživatelům možnost vybrat si mezi více klíčů, které jsou spravovány ve více umístěních.

* Pro šifrování klíč se používá vždy a absence klíče bude mít za následek chybu.
* Pro dešifrování:
  * Překladač klíčů je vyvolán, pokud je zadán získat klíč. Pokud je zadán překladač, ale nemá mapování pro identifikátor klíče, je vyvolána chyba.
  * Pokud překladač není zadán, ale je zadán klíč, klíč se používá, pokud jeho identifikátor odpovídá požadovanému identifikátoru klíče. Pokud identifikátor neodpovídá, je vyvolána chyba.

Příklady kódu v tomto článku ukazují nastavení zásad šifrování a práci se šifrovanými daty, ale nedemonstrují práci s Azure Key Vault. [Ukázky šifrování](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) na GitHubu ukazují podrobnější scénář od konce pro objekty BLOB, fronty a tabulky spolu s integrací trezoru klíčů.

### <a name="requireencryption-mode"></a>Režim RequireEncryption
Uživatelé mohou volitelně povolit provozní režim, ve kterém musí být zašifrovány všechny obrázky a soubory ke stažení. V tomto režimu se pokusy o nahrání dat bez zásad šifrování nebo stahování dat, která nejsou ve službě šifrována, v klientovi nezdaří. Vlastnost **RequireEncryption** objektu možnosti požadavku řídí toto chování. Pokud vaše aplikace zašifruje všechny objekty uložené ve službě Azure Storage, můžete nastavit **vlastnost RequireEncryption** na výchozí možnosti požadavku pro objekt klienta služby. Například nastavte **CloudBlobClient.DefaultRequestOptions.RequireEncryption** na **true** tak, aby vyžadovala šifrování pro všechny operace objektu blob prováděné prostřednictvím tohoto objektu klienta.


### <a name="blob-service-encryption"></a>Šifrování služby objektů blob
Vytvořte objekt **BlobEncryptionPolicy** a nastavte jej v možnostech požadavku (na rozhraní API nebo na úrovni klienta pomocí **DefaultRequestOptions).** Vše ostatní bude zpracováno klientskou knihovnou interně.

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

 // Set the encryption policy on the request options.
 BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

 // Upload the encrypted contents to the blob.
 blob.UploadFromStream(stream, size, null, options, null);

 // Download and decrypt the encrypted contents from the blob.
 MemoryStream outputStream = new MemoryStream();
 blob.DownloadToStream(outputStream, null, options, null);
```

### <a name="queue-service-encryption"></a>Šifrování služby fronty
Vytvořte objekt **QueueEncryptionPolicy** a nastavte jej v možnostech požadavku (na rozhraní API nebo na úrovni klienta pomocí **DefaultRequestOptions).** Vše ostatní bude zpracováno klientskou knihovnou interně.

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

 // Add message
 QueueRequestOptions options = new QueueRequestOptions() { EncryptionPolicy = policy };
 queue.AddMessage(message, null, null, options, null);

 // Retrieve message
 CloudQueueMessage retrMessage = queue.GetMessage(null, options, null);
```

### <a name="table-service-encryption"></a>Šifrování služby Table Service
Kromě vytvoření zásadšifrování a nastavení na možnosti požadavku je nutné zadat v **aplikaci TableRequestOptions**buď **překladač šifrování** , nebo nastavit atribut [EncryptProperty] u entity.

#### <a name="using-the-resolver"></a>Použití překládání

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

 TableRequestOptions options = new TableRequestOptions()
 {
    EncryptionResolver = (pk, rk, propName) =>
     {
        if (propName == "foo")
         {
            return true;
         }
         return false;
     },
     EncryptionPolicy = policy
 };

 // Insert Entity
 currentTable.Execute(TableOperation.Insert(ent), options, null);

 // Retrieve Entity
 // No need to specify an encryption resolver for retrieve
 TableRequestOptions retrieveOptions = new TableRequestOptions()
 {
    EncryptionPolicy = policy
 };

 TableOperation operation = TableOperation.Retrieve(ent.PartitionKey, ent.RowKey);
 TableResult result = currentTable.Execute(operation, retrieveOptions, null);
```

#### <a name="using-attributes"></a>Použití atributů
Jak bylo uvedeno výše, pokud entita implementuje TableEntity, pak vlastnosti mohou být dekorovány atributem [EncryptProperty] namísto určení **EncryptResolver**.

```csharp
[EncryptProperty]
 public string EncryptedProperty1 { get; set; }
```

## <a name="encryption-and-performance"></a>Šifrování a výkon
Všimněte si, že šifrování dat úložiště má za následek další režii výkonu. Klíč obsahu a IV musí být generovány, samotný obsah musí být šifrován a další metadata musí být formátována a odeslána. Tato režie se bude lišit v závislosti na množství šifrovaných dat. Doporučujeme, aby zákazníci vždy testovat své aplikace na výkon během vývoje.

## <a name="next-steps"></a>Další kroky
* [Kurz: Šifrování a dešifrování objektů BLOB v úložišti Microsoft Azure pomocí azure key vaultu](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)
* Stažení [balíčku klienta úložiště Azure pro soubor .NET NuGet](https://www.nuget.org/packages/WindowsAzure.Storage)
* Stažení balíčků Azure Key Vault NuGet [Core](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/), [Client](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)a [Extensions](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/)  
* Navštivte [dokumentaci k úložišti klíčů Azure](../../key-vault/key-vault-overview.md)
