---
title: Šifrování na straně klienta pomocí Javy pro úložiště Microsoft Azure | Dokumenty společnosti Microsoft
description: Klientská knihovna úložiště Azure pro Jazyk Java podporuje šifrování na straně klienta a integraci s Azure Key Vault pro maximální zabezpečení pro vaše aplikace Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.devlang: java
ms.topic: article
ms.date: 05/11/2017
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 32691e0ddcee3f5410b12f07a2fb80806345bc26
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460507"
---
# <a name="client-side-encryption-and-azure-key-vault-with-java-for-microsoft-azure-storage"></a>Šifrování na straně klienta a trezor klíčů Azure s Javou pro úložiště Microsoft Azure
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Přehled
[Klientská knihovna úložiště Azure pro Jazyk Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) podporuje šifrování dat v klientských aplikacích před odesláním do Služby Azure Storage a dešifrování dat při stahování do klienta. Knihovna také podporuje integraci s [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů účtu úložiště.

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
Klientská knihovna aktuálně podporuje šifrování pouze celých objektů BLOB. Konkrétně šifrování je podporováno, když uživatelé používají metody **upload*** nebo **metodu openOutputStream.** Pro stahování jsou podporovány úplné i rozsah stahování.  

Během šifrování klientská knihovna vygeneruje náhodný inicializační vektor (IV) 16 bajtů spolu s náhodným šifrovacím klíčem obsahu (CEK) o 32 bajtů a provede šifrování obálek dat objektů blob pomocí těchto informací. Zabalená CEK a některá další šifrovací metadata jsou pak uložena jako metadata objektu blob spolu se šifrovaným objektem blob ve službě.

> [!WARNING]
> Pokud upravujete nebo nahráváte vlastní metadata pro objekt blob, musíte zajistit, aby tato metadata byla zachována. Pokud nahrajete nová metadata bez těchto metadat, zalomené CEK, IV a další metadata se ztratí a obsah objektu blob už nikdy nebude možné znovu načíst.
> 
> 

Stahování šifrovaného objektu blob zahrnuje načítání obsahu celého objektu blob pomocí metod **download**/**openInputStream** convenience. Zabalená sada CEK je rozbalena a používá se společně s IV (v tomto případě uložená jako metadata objektů blob) k vrácení dešifrovaných dat uživatelům.

Stahování libovolný rozsah (**downloadRange** metody) v šifrované objektu blob zahrnuje úpravu rozsahu poskytované uživateli s cílem získat malé množství dalších dat, které lze použít k úspěšnému dešifrování požadovaného rozsahu.  

Všechny typy objektů blob (objekty BLOB bloku, objekty BLOB stránky a objekty BLOB) lze zašifrovat nebo dešifrovat pomocí tohoto schématu.

### <a name="queues"></a>Fronty
Vzhledem k tomu, že zprávy fronty mohou být libovolného formátu, definuje klientská knihovna vlastní formát, který obsahuje inicializační vektor (IV) a šifrovaný šifrovací klíč obsahu (CEK) v textu zprávy.  

Během šifrování klientská knihovna generuje náhodné IV 16 bajtů spolu s náhodným CEK 32 bajtů a provádí šifrování obálky textu zprávy fronty pomocí těchto informací. Zabalená cek a některá další šifrovací metadata jsou pak přidána do šifrované zprávy fronty. Tato upravená zpráva (viz níže) je uložena ve službě.

```
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

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
   
   U tabulek musí uživatelé kromě zásad šifrování zadat vlastnosti, které mají být šifrovány. To lze provést zadáním atributu [Encrypt] (pro entity POCO, které jsou odvozeny z TableEntity) nebo překládáním šifrování v možnostech požadavku. Překládání šifrování je delegát, který přebírá klíč oddílu, klíč řádku a název vlastnosti a vrátí logickou hodnotu, která označuje, zda má být tato vlastnost zašifrována. Během šifrování bude klientská knihovna používat tyto informace k rozhodnutí, zda má být vlastnost při zápisu do drátu šifrována. Delegát také poskytuje možnost logiky kolem jak jsou zašifrovány vlastnosti. (Například pokud X, pak šifrovat vlastnost A; jinak šifrovat vlastnosti A a B.) Všimněte si, že není nutné poskytnout tyto informace při čtení nebo dotazování entit.

### <a name="batch-operations"></a>Dávkové operace
V dávkových operacích bude stejný KEK použit ve všech řádcích v této dávkové operaci, protože klientská knihovna umožňuje pouze jeden objekt možností (a tedy jednu zásadu/KEK) na dávkovou operaci. Klientská knihovna však interně vygeneruje nové náhodné IV a náhodné CEK na řádek v dávce. Uživatelé mohou také zvolit šifrování různých vlastností pro každou operaci v dávce definováním tohoto chování v překládání šifrování.

### <a name="queries"></a>Dotazy
> [!NOTE]
> Vzhledem k tomu, že entity jsou šifrovány, nelze spustit dotazy, které filtrují šifrovanou vlastnost.  Pokud se pokusíte, výsledky budou nesprávné, protože služba by se pokouší porovnat šifrovaná data s nešifrovaná data.
> 
> 
> Chcete-li provést operace dotazu, je nutné zadat překládání klíčů, který je schopen přeložit všechny klíče v sadě výsledků. Pokud entitu obsaženou ve výsledku dotazu nelze přeložit zprostředkovateli, klientská knihovna vyvolá chybu. Pro každý dotaz, který provádí projekce na straně serveru, klientská knihovna přidá ve výchozím nastavení do vybraných sloupců speciální vlastnosti metadat šifrování (_ClientEncryptionMetadata1 a _ClientEncryptionMetadata2).

## <a name="azure-key-vault"></a>Azure Key Vault
Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Pomocí služby Azure Key Vault mohou uživatelé šifrovat klíče a tajné klíče (například ověřovací klíče, klíče účtu úložiště, klíče pro šifrování dat, . PFX soubory a hesla) pomocí klíčů, které jsou chráněny moduly hardwarového zabezpečení (HSM). Další informace najdete v článku [Co je Azure Key Vault](../../key-vault/general/overview.md).

Klientská knihovna úložiště používá základní knihovnu úložiště klíčů k poskytování společného rámce pro správu klíčů v rámci celého Azure. Uživatelé také získat další výhody pomocí knihovny rozšíření trezoru klíčů. Knihovna rozšíření poskytuje užitečné funkce kolem jednoduchých a bezproblémových poskytovatelů místních a cloudových klíčů Symmetric/RSA, stejně jako s agregací a ukládáním do mezipaměti.

### <a name="interface-and-dependencies"></a>Rozhraní a závislosti
Existují tři balíčky trezoru klíčů:  

* azure-keyvault-core obsahuje IKey a IKeyResolver. Jedná se o malý balíček bez závislostí. Klientská knihovna úložiště pro Jazyk Java ji definuje jako závislost.
* azure-keyvault obsahuje klienta REST trezoru klíčů.  
* azure-keyvault-extensions obsahuje rozšiřující kód, který obsahuje implementace kryptografických algoritmů a RSAKey a SymmetricKey. Závisí na oborech názvů Core a KeyVault a poskytuje funkce pro definování agregačního překládání (pokud uživatelé chtějí používat více zprostředkovatelů klíčů) a překladače klíče mezipaměti. Přestože knihovna klienta úložiště není přímo závislá na tomto balíčku, pokud uživatelé chtějí použít Azure Key Vault k ukládání svých klíčů nebo použít rozšíření trezoru klíčů využívat místní a cloudové zprostředkovatele kryptografických služeb, budou potřebovat tento balíček.  
  
  Trezor klíčů je určen pro hlavní klíče s vysokou hodnotou a omezení omezení na trezor klíčů jsou navržena s ohledem na toto. Při provádění šifrování na straně klienta pomocí trezoru klíčů je upřednostňovaným modelem použití symetrických hlavních klíčů uložených jako tajné klíče v trezoru klíčů a místně uložené v mezipaměti. Uživatelé musí provést následující akce:  

1. Vytvořte tajný klíč offline a nahrajte ho do trezoru klíčů.  
2. Použijte základní identifikátor tajného klíče jako parametr k vyřešení aktuální verze tajného klíče pro šifrování a ukládání těchto informací místně do mezipaměti. Pro ukládání do mezipaměti použijte pro ukládání do mezipaměti program CachingKeyResolver. od uživatelů se neočekává, že budou implementovat vlastní logiku ukládání do mezipaměti.  
3. Při vytváření zásad šifrování použijte překladač mezipaměti jako vstup.
   Další informace týkající se využití trezoru klíčů naleznete ve vzorcích šifrovacího kódu.

## <a name="best-practices"></a>Osvědčené postupy
Podpora šifrování je k dispozici pouze v knihovně klienta úložiště pro Jazyk Java.

> [!IMPORTANT]
> Při použití šifrování na straně klienta si uvědomte tyto důležité body:
> 
> * Při čtení nebo zápisu do šifrovaného objektu blob použijte příkazy pro nahrávání celého objektu blob a příkazy pro stahování objektů blob. Vyhněte se zápisu do šifrovaného objektu blob pomocí operací protokolu, jako je blok Put, seznam blokovaných položek, zápis stránek, vymazat stránky nebo připojit blok; v opačném případě můžete poškozený objekt blob poškodit a učinit jej nečitelným.
> * Pro tabulky existuje podobné omezení. Dávejte pozor, abyste neaktualizovali šifrované vlastnosti bez aktualizace metadat šifrování.  
> * Pokud nastavíte metadata na šifrovaném objektu blob, můžete přepsat metadata související s šifrováním požadovaná pro dešifrování, protože nastavení metadat není aditivní. To platí také pro snímky; vyhněte se zadávání metadat při vytváření snímku šifrovaného objektu blob. Pokud musí být nastavena metadata, nezapomeňte nejprve zavolat metodu **downloadAttributes,** abyste získali aktuální metadata šifrování a vyhnuli se souběžným zápisům při nastavování metadat.  
> * Povolte příznak **requireEncryption** ve výchozích možnostech požadavku pro uživatele, kteří by měli pracovat pouze se šifrovanými daty. Viz níže pro více informací.  
> 
> 

## <a name="client-api--interface"></a>Klientské rozhraní API / rozhraní
Při vytváření objektu EncryptionPolicy mohou uživatelé poskytnout pouze klíč (implementaci iKey), pouze překládání (implementace IKeyResolver) nebo obojí. IKey je základní typ klíče, který je identifikován pomocí identifikátorklíče a který poskytuje logiku pro obtékání/rozbalování. IKeyResolver se používá k vyřešení klíče během procesu dešifrování. Definuje ResolveKey metoda, která vrací IKey daný identifikátor klíče. To poskytuje uživatelům možnost vybrat si mezi více klíčů, které jsou spravovány ve více umístěních.

* Pro šifrování klíč se používá vždy a absence klíče bude mít za následek chybu.  
* Pro dešifrování:  
  
  * Překladač klíčů je vyvolán, pokud je zadán získat klíč. Pokud je zadán překladač, ale nemá mapování pro identifikátor klíče, je vyvolána chyba.  
  * Pokud překladač není zadán, ale je zadán klíč, klíč se používá, pokud jeho identifikátor odpovídá požadovanému identifikátoru klíče. Pokud identifikátor neodpovídá, je vyvolána chyba.  
    
    [Ukázky šifrování](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) ukazují podrobnější scénář od konce na konec pro objekty BLOB, fronty a tabulky spolu s integrací trezoru klíčů.

### <a name="requireencryption-mode"></a>Režim RequireEncryption
Uživatelé mohou volitelně povolit provozní režim, ve kterém musí být zašifrovány všechny obrázky a soubory ke stažení. V tomto režimu se pokusy o nahrání dat bez zásad šifrování nebo stahování dat, která nejsou ve službě šifrována, v klientovi nezdaří. Příznak **requireEncryption** objektu možnosti požadavku řídí toto chování. Pokud vaše aplikace zašifruje všechny objekty uložené ve službě Azure Storage, můžete nastavit vlastnost **requireEncryption** na výchozí možnosti požadavku pro objekt klienta služby.   

Například použijte **CloudBlobClient.getDefaultRequestOptions().setRequireEncryption(true)** vyžadovat šifrování pro všechny operace objektu blob prováděné prostřednictvím tohoto objektu klienta.

### <a name="blob-service-encryption"></a>Šifrování služby objektů blob
Vytvořte objekt **BlobEncryptionPolicy** a nastavte jej v možnostech požadavku (na rozhraní API nebo na úrovni klienta pomocí **DefaultRequestOptions).** Vše ostatní bude zpracováno klientskou knihovnou interně.

```java
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

// Set the encryption policy on the request options.
BlobRequestOptions options = new BlobRequestOptions();
options.setEncryptionPolicy(policy);

// Upload the encrypted contents to the blob.
blob.upload(stream, size, null, options, null);

// Download and decrypt the encrypted contents from the blob.
ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
blob.download(outputStream, null, options, null);
```

### <a name="queue-service-encryption"></a>Šifrování služby fronty
Vytvořte objekt **QueueEncryptionPolicy** a nastavte jej v možnostech požadavku (na rozhraní API nebo na úrovni klienta pomocí **DefaultRequestOptions).** Vše ostatní bude zpracováno klientskou knihovnou interně.

```java
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

// Add message
QueueRequestOptions options = new QueueRequestOptions();
options.setEncryptionPolicy(policy);

queue.addMessage(message, 0, 0, options, null);

// Retrieve message
CloudQueueMessage retrMessage = queue.retrieveMessage(30, options, null);
```

### <a name="table-service-encryption"></a>Šifrování služby Table Service
Kromě vytvoření zásadšifrování a nastavení na možnosti požadavku je nutné zadat **překladač šifrování** v **tableRequestOptions**nebo nastavit atribut [Encrypt] na getter a setter entity.

### <a name="using-the-resolver"></a>Použití překládání

```java
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

TableRequestOptions options = new TableRequestOptions()
options.setEncryptionPolicy(policy);
options.setEncryptionResolver(new EncryptionResolver() {
    public boolean encryptionResolver(String pk, String rk, String key) {
        if (key == "foo")
        {
            return true;
        }
        return false;
    }
});

// Insert Entity
currentTable.execute(TableOperation.insert(ent), options, null);

// Retrieve Entity
// No need to specify an encryption resolver for retrieve
TableRequestOptions retrieveOptions = new TableRequestOptions()
retrieveOptions.setEncryptionPolicy(policy);

TableOperation operation = TableOperation.retrieve(ent.PartitionKey, ent.RowKey, DynamicTableEntity.class);
TableResult result = currentTable.execute(operation, retrieveOptions, null);
```

### <a name="using-attributes"></a>Použití atributů
Jak bylo uvedeno výše, pokud entita implementuje TableEntity, pak vlastnosti getter a setter mohou být dekorovány atributem [Encrypt] namísto určení **EncryptionResolver**.

```java
private string encryptedProperty1;

@Encrypt
public String getEncryptedProperty1 () {
    return this.encryptedProperty1;
}

@Encrypt
public void setEncryptedProperty1(final String encryptedProperty1) {
    this.encryptedProperty1 = encryptedProperty1;
}
```

## <a name="encryption-and-performance"></a>Šifrování a výkon

Všimněte si, že šifrování dat úložiště má za následek další režii výkonu. Klíč obsahu a IV musí být generovány, samotný obsah musí být šifrován a další metadata musí být formátována a odeslána. Tato režie se bude lišit v závislosti na množství šifrovaných dat. Doporučujeme, aby zákazníci vždy testovat své aplikace na výkon během vývoje.

## <a name="next-steps"></a>Další kroky

* Stažení [balíčku klienta úložiště Azure pro Java Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)  
* Stažení [klientské knihovny úložiště Azure pro zdrojový kód Java z GitHubu](https://github.com/Azure/azure-storage-java)
* Stáhněte si balíčky Azure Key Vault Maven Library for Java Maven:
  * [Základní](https://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault-core) balíček
  * [Balíček klienta](https://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault)
* Navštivte [dokumentaci k úložišti klíčů Azure](../../key-vault/general/overview.md)
