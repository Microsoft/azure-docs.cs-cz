---
title: Šifrování na straně klienta pomocí Javy pro službu Microsoft Azure Storage | Dokumentace Microsoftu
description: Klientská knihovna pro úložiště Azure pro Javu podporuje šifrování na straně klienta a integraci s Azure Key Vault z důvodu maximálního zabezpečení pro vaše aplikace Azure Storage.
services: storage
author: lakasa
ms.service: storage
ms.devlang: java
ms.topic: article
ms.date: 05/11/2017
ms.author: lakasa
ms.subservice: common
ms.openlocfilehash: 9a96f80c609f446dcc1fea2a87925dec3dadfedd
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55471891"
---
# <a name="client-side-encryption-and-azure-key-vault-with-java-for-microsoft-azure-storage"></a>Šifrování na straně klienta a Azure Key Vault v Javě pro Microsoft Azure Storage
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Přehled
[Klientská knihovna pro úložiště Azure pro Javu](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) podporuje šifrování dat v rámci klientské aplikace před nahráním do služby Azure Storage a dešifrování dat při stahování do klienta. Knihovna také podporuje integraci s [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů účtu úložiště.

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Šifrování a dešifrování prostřednictvím techniku obálky
Procesy šifrování a dešifrování podle techniku obálky.  

### <a name="encryption-via-the-envelope-technique"></a>Šifrování prostřednictvím techniku obálky
Šifrování prostřednictvím obálky technika pracuje následujícím způsobem:  

1. Klientská knihovna pro úložiště Azure vygeneruje obsahu šifrovací klíč (CEK), což je použití jednoho jednorázových symetrický klíč.  
2. Uživatel data se šifrují pomocí této CEK.   
3. CEK je vnořena (zašifrovaný) pomocí šifrovací klíč klíče (KEK). Klíče KEK je identifikovaná identifikátorem klíče a mohou být asymetrický pár klíčů nebo symetrický klíč a může být spravovaný místně nebo uloženy v Azure Key Vault.  
   Klientská knihovna pro úložiště, samotný nikdy má přístup k KEK. Knihovny vyvolá algoritmus klíče zabalení, která je k dispozici ve službě Key Vault. Uživatelé mohou používat vlastní zprostředkovatelé pro klíče zabalení a rozbalení v případě potřeby.  
4. Šifrovaná data se pak nahrají do služby Azure Storage. Zabalená klíče spolu s některé další šifrování metadat je uložená jako metadata (na objekt blob) nebo interpolovaných s šifrovaná data (zprávy fronty a tabulky entity).

### <a name="decryption-via-the-envelope-technique"></a>Dešifrování pomocí obálky technika
Dešifrování pomocí obálky technika pracuje následujícím způsobem:  

1. Knihovna klienta předpokládá, že uživatel spravuje šifrovací klíč klíče (KEK), místně nebo v Azure Key Vault. Uživatel není potřeba zjistit, konkrétní klíč, který se použil pro šifrování. Místo toho překladače klíče, které se překládá identifikátory klíče na klíče můžete nastavit a použít.  
2. Klientská knihovna šifrovaná data spolu se žádné šifrování materiál, který je uložený ve službě soubory ke stažení.  
3. Zabalená obsahu šifrovací klíč (CEK) je rozbalená (dešifrovaný) pomocí klíčů šifrování klíčů (KEK). Zde znovu, knihovna klienta nemá přístup k KEK. Jednoduše vyvolá vlastní nebo zprostředkovatele služby Key Vault rozbalení algoritmus.  
4. Klíč obsahu šifrovací (CEK) se pak použije k dešifrování šifrovaného uživatelská data.

## <a name="encryption-mechanism"></a>Mechanismus šifrování
Klientská knihovna pro úložiště používá [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) pro šifrování dat uživatele. Konkrétně [Cipher Block řetězení (CBC)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) režimu s AES. Každý trochu jinak, service funguje tak, že bude každý z nich tady probereme.

### <a name="blobs"></a>Objekty blob
Klientská knihovna aktuálně podporuje šifrování pouze celé objekty BLOB. Konkrétně šifrování je podporované, když uživatelé používají **nahrát*** metody nebo **openOutputStream** metody. Soubory ke stažení, i kompletní a soubory ke stažení rozsahu jsou podporované.  

Při šifrování klientské knihovny bude generovat náhodné inicializační vektor (IV) 16 bajtů, společně s náhodné obsahu šifrovací klíč (CEK) 32 bajtů a provádět obálky šifrování dat objektů blob pomocí těchto informací. Zabalená CEK a některé další šifrování metadat jsou pak uloženy jako metadata spolu s zašifrovaný objekt blob ve službě objektů blob.

> [!WARNING]
> Pokud jsou úpravy nebo odesílání vlastních metadat pro tento objekt blob, je potřeba zajistit, že tato metadata je zachováno. Pokud nahrajete nová metadata bez těchto metadat, zabalené CEK, IV a další metadata budou ztraceny a obsah objektu blob se nikdy bude nenávratně ztracený.
> 
> 

Stažení objektu blob šifrované zahrnuje načítání obsahu z celý objekt blob pomocí **stahování * / openInputStream** vhodné metody. Zabalené CEK je neobalený a používá se společně s IV (uložené v tomto případě jako metadata objektu blob) k vrácení dešifrovaná data pro uživatele.

Stažení libovolného rozsahu (**downloadRange*** metody) v objektu blob šifrované zahrnuje úpravy rozsahu poskytuje uživatelům zajistí malé množství další data, která slouží k dešifrování úspěšně požadované rozsah.  

Všechny typy objektů blob (objekty BLOB bloku, objekty BLOB stránky a doplňovací objekty BLOB) je možné šifrované/dešifrovat použití tohoto schématu.

### <a name="queues"></a>Fronty
Protože fronty zprávy mohou mít libovolný formát, definuje klientské knihovně pro vlastní formát, který obsahuje inicializační vektor (IV) a šifrovaného obsahu šifrovací klíč (CEK) v textu zprávy.  

Během šifrování se Klientská knihovna generuje náhodné IV 16 bajtů spolu s náhodné CEK 32 bajtů a provádí obálky šifrování pomocí těchto informací text zprávy ve frontě. Zabalená CEK a některé další šifrování metadat se poté přidají ke šifrované fronty zpráv. Tuto upravenou zprávu (viz dole) je uložená ve službě.

```
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

Při dešifrování zabalené klíč je extrahují z fronty zpráv a neobalený. Vektor IV je také extrahují z fronty zpráv a použít spolu s rozbalení klíče k dešifrování dat fronty zpráv. Všimněte si, že metadata šifrování malá (pod 500 bajtů), zatímco počítají limit 64KB zprávu fronty, by tak měly být spravovatelné dopad.

### <a name="tables"></a>Tabulky
Klientská knihovna podporuje šifrování vlastností entity pro vložení a nahrazovat operace.

> [!NOTE]
> Sloučení se momentálně nepodporuje. Protože podmnožinu vlastností mohou byla zašifrována pomocí dříve za jiný klíč, jednoduše slučování nové vlastnosti a metadata aktualizace způsobí ztrátu dat. Slučují se buď vyžaduje, aby volání další služby do existující entity načíst ze služby nebo pomocí nového klíče pro jednu vlastnost, které nejsou vhodné z důvodů výkonu.
> 
> 

Šifrování dat tabulky funguje takto:  

1. Uživatelé zadat vlastnosti pro šifrování.  
2. Klientská knihovna vygeneruje náhodných inicializační vektor (IV) 16 bajtů spolu s náhodný obsahu šifrovací klíč (CEK) 32 bajtů pro každou entitu a provádí šifrování obálky na jednotlivé vlastnosti šifrování odvozením nové IV jednu vlastnost. Zašifrované vlastnosti se ukládají jako binární data.  
3. Zabalená CEK a některé další šifrování metadat jsou pak uloženy jako dvě další rezervované vlastnosti. Vlastnost první vyhrazené (_ClientEncryptionMetadata1) je řetězcovou vlastnost, která uchovává informace o IV, verzi a zabaleného klíče. Za druhé rezervované vlastnosti (_ClientEncryptionMetadata2) je binární vlastnost, která uchovává informace o vlastnosti, která jsou šifrovaná. Informace v této druhé vlastnosti (_ClientEncryptionMetadata2) je zašifrovaná.  
4. Z důvodu tyto další rezervované vlastnosti vyžadované pro šifrování uživatelé nyní mohou mít pouze 250 vlastní vlastnosti namísto 252. Celková velikost entity musí být menší než 1MB.  
   
   Všimněte si, že lze pouze vlastnosti řetězce zašifrovaná. Pokud se ostatní typy vlastností jsou zašifrované, musí být převedeny na řetězce. Šifrované řetězce jsou uložené ve službě jako binární vlastnosti a jsou převedeny na řetězce zpět po dešifrování.
   
   Pro tabulky, vedle zásady šifrování musí uživatelé zadat vlastnosti, které mají být šifrována. To lze provést zadáním buď atribut [šifrovat] \(pro entity objektů POCO, které jsou odvozeny od TableEntity) nebo šifrování překladač v žádosti o možnostech. Překladač šifrování je delegát, který má klíč oddílu, klíč řádku a název vlastnosti a vrátí logickou hodnotu, která určuje, jestli by měl být šifrovaná tuto vlastnost. Při šifrování klientské knihovny použije tyto informace se rozhodnout, zda vlastnost by se měla šifrovat během zápisu lince. Delegát také poskytuje možnost logiky po tom, jak jsou zašifrované vlastnosti. (Například, pokud X, pak šifrování vlastnost A; v opačném případě šifrování vlastnosti A a B.) Všimněte si, že není potřeba zadat tyto informace při čtení nebo dotazování entit.

### <a name="batch-operations"></a>Dávkové operace
V dávkových operací stejné KEK se použije napříč všemi řádky dávkové operace vzhledem k tomu, že klientská knihovna umožňuje pouze jeden objekt možnosti (a tudíž jedna zásada/KEK) na operaci služby batch. Ale klientské knihovny interně vygeneruje nový náhodný IV a náhodné CEK každý řádek v dávce. Uživatelé také mohou šifrovat definováním toto chování překladač šifrování různé vlastnosti pro všechny operace v dávce.

### <a name="queries"></a>Dotazy
> [!NOTE]
> Vzhledem k tomu, entit je jím zašifrovaná, nelze spustit dotazy, které filtrují v zašifrované vlastnosti.  Pokud se pokusíte, budou výsledky nesprávné, služba by se pokouší o porovnání šifrovaná data s nešifrovaným datům.
> 
>
K provedení operace dotazů, je nutné zadat překladače klíče, který je schopen převést všechny klíče v sadě výsledků. Pokud se entity obsažené ve výsledku dotazu. nelze přeložit na zprostředkovateli, klientské knihovny vyvolá chybu. Pro každého dotazu, který provádí projekce na straně serveru přidá klientské knihovně pro speciální šifrování metadat vlastnosti (_ClientEncryptionMetadata1 a _ClientEncryptionMetadata2) ve výchozím nastavení vybrané sloupce.

## <a name="azure-key-vault"></a>Azure Key Vault
Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Uživatele pomocí služby Azure Key Vault můžete šifrovat klíče a tajné klíče (např. ověřovací klíče, klíče účtu úložiště, šifrovací klíče dat, Soubory PFX a hesla) pomocí klíčů chráněných moduly hardwarového zabezpečení (HSM). Další informace najdete v tématu [co je Azure Key Vault?](../../key-vault/key-vault-whatis.md).

Klientská knihovna pro úložiště používá základní knihovny služby Key Vault negace společná architektura v Azure pro správu klíčů. Uživatelé získají také další výhodou používání rozšíření knihovny služby Key Vault. Knihovna rozšíření nabízí užitečné funkce kolem snadný Symmetric/RSA místní a klíče poskytovatelů cloudu, a také s shromažďování a ukládání do mezipaměti.

### <a name="interface-and-dependencies"></a>Rozhraní a závislosti
Existují tři balíčky služby Key Vault:  

* core v trezoru klíčů Azure obsahuje Instrumentační klíč a IKeyResolver. Je malý balíček nemá žádné závislosti. Klientská knihovna pro úložiště pro Javu ji definuje jako závislost.
* Azure Key vaultu obsahuje klienta Key Vault REST.  
* rozšíření Azure keyvault obsahuje rozšíření kód, který zahrnuje implementace kryptografické algoritmy a RSAKey a SymmetricKey. Závisí na obory názvů jádra a trezor klíčů a poskytuje funkce, které definují agregační překladače, (Pokud uživatelé chtějí použití více poskytovatelů klíče) a ukládání do mezipaměti klíče překladač. I když klientskou knihovnu pro úložiště není závislý přímo na tento balíček, pokud uživatelé chtějí používat Azure Key Vault pro ukládání své klíče nebo použití služby Key Vault rozšíření využívají místní a cloudové zprostředkovatelé kryptografických služeb, musí tento balíček.  
  
  Key Vault je navržený pro vysokou hodnotu hlavního klíče a limity omezování služby Key Vault jsou určeny se to v úvahu. Při provádění šifrování na straně klienta se službou Key Vault, je preferovaným modelem použití symetrické klíče předlohy uložených jako tajné kódy ve službě Key Vault a uložili do mezipaměti místně. Uživatelé musí provést následující:  

1. Vytvoření tajného klíče do offline režimu a nahrajte ho do služby Key Vault.  
2. Základní identifikátor tajného klíče použijte jako parametr vyřešit aktuální verzi tajného klíče pro šifrování a ukládat do mezipaměti tyto informace v místně. Použít CachingKeyResolver pro ukládání do mezipaměti; Uživatelé se nepředpokládá implementaci vlastní logiky ukládání do mezipaměti.  
3. Použití ukládání do mezipaměti překladač jako vstup při vytváření zásady šifrování.
   Další informace týkající se používání služby Key Vault najdete v ukázkách kódu šifrování. <fix URL>  

## <a name="best-practices"></a>Osvědčené postupy
Podpora šifrování je dostupná jenom na klientskou knihovnu pro úložiště pro Javu.

> [!IMPORTANT]
> Mějte na paměti těchto důležitých bodů při použití šifrování na straně klienta:
> 
> * Při čtení nebo zápisu do objektu blob šifrované, použijte příkazy nahrávání celého objektu blob a příkazy ke stažení objektů blob v rozsahu/celé. Vyhněte se zápis do blob šifrovaným pomocí protokolu operace, třeba vložit blok, vložte seznam blokovaných položek, psát stránky, vymazat stránky nebo připojit bloku; jinak může dojít k poškození zašifrovaný objekt blob a nastavte ji nejde přečíst.
> * Pro tabulky existuje podobná omezení. Buďte opatrní při aktualizaci šifrované vlastnosti bez aktualizace metadat šifrování.  
> * Pokud nastavíte metadata pro objekt blob šifrované, mohou přepsat šifrování metadat požadovaná pro dešifrování, protože nastavení metadat není sčítání. To platí také pro snímky Neurčujte metadat vytvřit snímek objektu blob šifrované. Pokud metadata musí být nastavena, je nutné volat **downloadAttributes** nejprve má metoda načíst aktuální metadata šifrování a vyhnout souběžných zápisů při metadat je nastavena.  
> * Povolit **requireEncryption** příznak v žádosti o výchozí možnosti pro uživatele, kteří by měla fungovat jenom s šifrovaná data. Další informace najdete níže.  
> 
> 

## <a name="client-api--interface"></a>Klientské rozhraní API / rozhraní
Při vytváření objektu EncryptionPolicy, mohou uživatelé zadat pouze klíč (implementace Instrumentační klíč), pouze překladače (implementace IKeyResolver), nebo obojí. Instrumentační klíč je základní typ klíče, který je identifikován pomocí identifikátoru klíče, který poskytuje logiku pro zabalení a rozbalení. IKeyResolver se používá k překladu během procesu dešifrovací klíč. Definuje ResolveKey metodu, která vrátí Instrumentační klíč zadaný identifikátor klíče. To poskytuje uživatelům možnost výběru mezi více klíčů, které jsou spravovány v několika umístěních.

* Pro šifrování klíč je používán vždy a absence klíč způsobí chybu.  
* K dešifrování:  
  
  * Překladač klíče je vyvolána, pokud zadaná k získání klíče. Pokud překladač je zadána, ale nemá žádné mapování pro identifikátor klíče, je vržena chyba.  
  * Pokud překladač není zadán, ale zadaný klíč, klíč se používá, pokud jeho identifikátor odpovídá požadovaný identifikátor klíče. Pokud se neshoduje identifikátor, je vržena chyba.  
    
    [Šifrování ukázky](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) <fix URL>ukazují podrobnější scénář začátku do konce pro objekty BLOB, fronty a tabulky, spolu s integrací služby Key Vault.

### <a name="requireencryption-mode"></a>Režim RequireEncryption
Uživatelé mohou volitelně povolit režim operace, kde musí být všechny nahrávání a stahování šifrovaná. V tomto režimu se nezdaří pokusy o nahrání dat bez zásady šifrování nebo stahování dat, které nejsou šifrovány ve službě na straně klienta. **RequireEncryption** příznak možnosti objektu požadavku určuje toto chování. Pokud vaše aplikace bude šifrování všech objektů uložených ve službě Azure Storage, pak můžete nastavit **requireEncryption** vlastnost na výchozí možnosti žádosti klienta objektu služby.   

Například použít **CloudBlobClient.getDefaultRequestOptions().setRequireEncryption(true)** k vynucení šifrování pro všechny operace provádí prostřednictvím objektu klienta s objekty blob.

### <a name="blob-service-encryption"></a>Šifrování objektů BLOB service
Vytvoření **BlobEncryptionPolicy** objektu a nastavte v možnostech požadavku (rozhraní API nebo na úrovni klienta s použitím **DefaultRequestOptions**). Všechno ostatní bude zpracován adresou klientské knihovny interně.

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

### <a name="queue-service-encryption"></a>Šifrování služby Queue
Vytvoření **QueueEncryptionPolicy** objektu a nastavte v možnostech požadavku (rozhraní API nebo na úrovni klienta s použitím **DefaultRequestOptions**). Všechno ostatní bude zpracován adresou klientské knihovny interně.

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

### <a name="table-service-encryption"></a>Šifrování služby Table
Kromě vytváření zásad šifrování a nastavíte ho na možnosti žádosti, je třeba buď zadat **EncryptionResolver** v **TableRequestOptions**, nebo nastavte atribut [šifrovat] na entity Metoda getter a setter.

### <a name="using-the-resolver"></a>Použitím překladač

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

### <a name="using-attributes"></a>Pomocí atributů
Jak je uvedeno výše, pokud entita implementuje TableEntity, pak vlastnosti getter a setter může být doplněny pomocí atributu [šifrovat] místo určení **EncryptionResolver**.

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

## <a name="encryption-and-performance"></a>Šifrování a výkonu
Všimněte si, že šifruje vaše výsledky úložiště dat v dalším výkonnostním režiím. Vygenerovat symetrický klíč a vektor IV, samotný obsah musí být zašifrován a další metadata musí být ve formátu a nahrát. Tato dodatečná režie se liší v závislosti na množství dat, které jsou šifrované. Doporučujeme vždy testovat výkon při vývoji svých aplikací zákazníkům.

## <a name="next-steps"></a>Další postup
* Stáhněte si [Klientská knihovna pro úložiště Azure pro jazyk Java Maven balíček](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)  
* Stáhněte si [Klientská knihovna Azure Storage pro zdrojový kód v Javě z Githubu](https://github.com/Azure/azure-storage-java)   
* Stáhněte klíč trezoru Maven knihovnu služby Azure pro jazyk Java Maven balíčky:
  * [Základní](http://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault-core) balíčku
  * [Klient](http://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault) balíčku
* Přejděte [dokumentace ke službě Azure Key Vault](../../key-vault/key-vault-whatis.md)
