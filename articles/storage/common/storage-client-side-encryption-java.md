---
title: Client-Side šifrování pomocí Java pro Microsoft Azure Storage | Microsoft Docs
description: Klientská knihovna Azure Storage pro jazyk Java podporuje šifrování a integraci na straně klienta s Azure Key Vault pro zajištění maximálního zabezpečení pro vaše Azure Storage aplikace.
services: storage
author: tamram
ms.service: storage
ms.devlang: java
ms.topic: article
ms.date: 02/18/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-java
ms.openlocfilehash: 78baaa3f794bed870b40fb3975f6b80ff37e90f0
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043724"
---
# <a name="client-side-encryption-and-azure-key-vault-with-java-for-microsoft-azure-storage"></a>Client-Side šifrování a Azure Key Vault pomocí Java pro Microsoft Azure Storage
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Přehled
[Klientská knihovna Azure Storage pro jazyk Java](https://search.maven.org/artifact/com.azure/azure-storage-blob-cryptography) podporuje šifrování dat v rámci klientských aplikací před odesláním do Azure Storage a dešifrování dat při stahování do klienta. Knihovna také podporuje integraci s [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu klíčů účtu úložiště.

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Šifrování a dešifrování prostřednictvím techniky obálek
Procesy šifrování a dešifrování se řídí způsobem obálky.  

### <a name="encryption-via-the-envelope-technique"></a>Šifrování prostřednictvím techniky obálek
Šifrování prostřednictvím techniky obálek funguje následujícím způsobem:  

1. Klientská knihovna pro úložiště Azure vygeneruje šifrovací klíč obsahu (CEK), což je symetrický klíč založený na jednorázovém použití.  
2. Uživatelská data se šifrují pomocí tohoto CEK.   
3. CEK se pak zabalí (zašifruje) pomocí klíčového šifrovacího klíče (KEK). KEK je identifikován identifikátorem klíče a může se jednat o asymetrický klíč nebo symetrický klíč a dá se spravovat místně nebo uložit v trezorech klíčů Azure.  
   Klientská knihovna pro úložiště nemá nikdy přístup k KEK. Knihovna vyvolá algoritmus pro zabalení klíče, který je k dispozici v Key Vault. V případě potřeby mohou uživatelé používat vlastní poskytovatele pro zalamování a rozbalení klíče.  
4. Šifrovaná data se pak nahrají do služby Azure Storage. Zabalené klíče spolu s dalšími metadaty šifrování se ukládají jako metadata (v objektu BLOB) nebo interpolovaná pomocí šifrovaných dat (zprávy fronty a entity tabulky).

### <a name="decryption-via-the-envelope-technique"></a>Dešifrování prostřednictvím techniky obálek
Dešifrování prostřednictvím techniky obálek funguje následujícím způsobem:  

1. Klientská knihovna předpokládá, že uživatel spravuje klíč šifrovacího klíče (KEK) buď místně, nebo v trezorech klíčů Azure. Uživatel nemusí znát konkrétní klíč, který se použil pro šifrování. Místo toho je možné nastavit a použít překladač klíčů, který překládá různé identifikátory klíčů na klíče.  
2. Klientská knihovna stáhne zašifrovaná data spolu s jakýmkoli šifrovacím materiálem uloženým ve službě.  
3. Nezabalený šifrovací klíč obsahu (CEK) se pak nebalí (dešifruje) pomocí klíčového šifrovacího klíče (KEK). V takovém případě knihovna klienta nemá přístup k KEK. Jednoduše vyvolá algoritmus rozbalení vlastního nebo Key Vaultho poskytovatele.  
4. Šifrovací klíč obsahu (CEK) se pak použije k dešifrování šifrovaných uživatelských dat.

## <a name="encryption-mechanism"></a>Šifrovací mechanismus
Klientská knihovna pro úložiště používá [algoritmus AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) , aby se šifroval data uživatelů. Konkrétně režim [řetězení bloků šifry (CBC)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) s AES. Každá služba funguje trochu jinak, takže se na ně podíváme každý z nich.

### <a name="blobs"></a>Objekty blob
Klientská knihovna aktuálně podporuje pouze šifrování celých objektů BLOB. Šifrování je konkrétně podporováno, pokud uživatelé používají metody **Upload** _ nebo _ *openOutputStream**. V případě souborů ke stažení jsou podporovány obě položky pro stahování dokončených i rozsahů.  

Při šifrování vygeneruje Klientská knihovna náhodný vektor inicializace (IV) o 16 bajtech, společně s náhodným šifrovacím klíčem obsahu (CEK) 32 bajtů a provede šifrování obálky dat objektů BLOB pomocí těchto informací. Zabalené CEK a některá další šifrovací metadata se pak ukládají jako metadata objektů BLOB společně s šifrovaným objektem BLOB ve službě.

> [!WARNING]
> Pokud upravujete nebo ukládáte vlastní metadata pro objekt blob, musíte zajistit, aby byla tato metadata zachovaná. Pokud nahrajete nová metadata bez těchto metadat, zabalené CEK, IV a další metadata budou ztraceny a obsah objektu BLOB nebude nikdy možné znovu načíst.
> 
> 

Stažení šifrovaného objektu BLOB zahrnuje načtení obsahu celého objektu BLOB pomocí metod **stažení** / **openInputStream** pohodlí. Zabalená CEK se nebalí a používá společně s IV (uloženými jako metadata objektů BLOB v tomto případě) k vrácení dešifrovaných dat uživatelům.

Stahování libovolného rozsahu (**downloadRange** metod) v zašifrovaném objektu BLOB zahrnuje úpravu rozsahu poskytnutého uživateli, aby bylo možné získat malé množství dalších dat, která lze použít k úspěšnému dešifrování požadovaného rozsahu.  

Všechny typy objektů BLOB (objekty blob bloku, objekty blob stránky a doplňovací objekty BLOB) se dají šifrovat nebo dešifrovat pomocí tohoto schématu.

### <a name="queues"></a>Fronty
Vzhledem k tomu, že zprávy fronty mohou být libovolného formátu, knihovna klienta definuje vlastní formát, který obsahuje inicializační vektor (IV) a šifrovaný šifrovací klíč (CEK) šifrovaného obsahu () v textu zprávy.  

Při šifrování generuje Klientská knihovna náhodnou hodnotu IV z 16 bajtů spolu s náhodným CEK 32 bajtů a pomocí těchto informací provádí šifrování obálky textu zprávy fronty. Zabalené CEK a některá další šifrovací metadata se pak přidají do zprávy zašifrované fronty. Tato upravená zpráva (uvedená níže) je uložena ve službě.

```
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

Během dešifrování je zabalený klíč extrahován ze zprávy fronty a rozbalením. Rozhraní IV je také extrahováno ze zprávy fronty a použito společně s nezabaleným klíčem k dešifrování dat zprávy ve frontě. Všimněte si, že metadata šifrování jsou malá (pod 500 bajtů), takže pokud se počítá s limitem 64KB pro zprávu fronty, měl by být dopad spravovatelný.

### <a name="tables"></a>Tabulky
Klientská knihovna podporuje šifrování vlastností entit pro operace INSERT a nahrazování.

> [!NOTE]
> Sloučení se momentálně nepodporuje. Vzhledem k tomu, že podmnožina vlastností mohla být dříve zašifrovaná pomocí jiného klíče, stačí sloučit nové vlastnosti a aktualizace metadat bude mít za následek ztrátu dat. Sloučení vyžaduje, aby dodatečná volání služby přečetla stávající entita ze služby nebo používala nový klíč pro jednu vlastnost, která nejsou vhodná z důvodů výkonu.
> 
> 

Šifrování dat v tabulce funguje takto:  

1. Uživatelé určují vlastnosti, které mají být zašifrovány.  
2. Klientská knihovna generuje náhodný vektor inicializace (IV) o 16 bajtech spolu s náhodným šifrovacím klíčem obsahu (CEK) o 32 bajtech pro každou entitu a provede šifrování obálky u jednotlivých vlastností, které se zašifrují, a to odvozením nové IV na vlastnost. Šifrovaná vlastnost se ukládá jako binární data.  
3. Zabalené CEK a některá další šifrovací metadata se pak uloží jako dvě další rezervované vlastnosti. První vyhrazená vlastnost (_ClientEncryptionMetadata1) je řetězcová vlastnost, která obsahuje informace o IV, verzi a zabaleném klíči. Druhá vyhrazená vlastnost (_ClientEncryptionMetadata2) je binární vlastnost, která obsahuje informace o vlastnostech, které jsou zašifrovány. Informace v této druhé vlastnosti (_ClientEncryptionMetadata2) jsou zašifrované.  
4. Vzhledem k těmto dalším rezervovaným vlastnostem vyžadovaným pro šifrování mohou uživatelé nyní mít pouze 250 vlastních vlastností místo 252. Celková velikost entity musí být menší než 1 MB.  
   
   Všimněte si, že lze šifrovat pouze vlastnosti řetězce. Pokud mají být zašifrovány jiné typy vlastností, je nutné je převést na řetězce. Šifrované řetězce jsou uloženy ve službě jako binární vlastnosti a jsou převedeny zpět na řetězce po dešifrování.
   
   U tabulek (kromě zásad šifrování) musí uživatelé zadat vlastnosti, které mají být zašifrované. To lze provést buď zadáním atributu [Encrypt] (u entit POCO odvozených od TableEntity) nebo překladače šifrování v možnostech žádosti. Překladač šifrování je delegát, který převezme klíč oddílu, klíč řádku a název vlastnosti a vrátí logickou hodnotu, která označuje, jestli má být tato vlastnost zašifrovaná. Při šifrování bude knihovna klienta tyto informace používat k rozhodnutí, jestli má být při zápisu do tohoto drátu zašifrovaná vlastnost. Delegát také poskytuje možnost logiky kolem způsobu šifrování vlastností. (Například pokud X, pak Šifrujte vlastnost A; jinak Zašifrujte vlastnosti a a B.) Všimněte si, že při čtení nebo dotazování entit není nutné tyto informace zadávat.

### <a name="batch-operations"></a>Dávkové operace
V dávkových operacích se stejné KEK budou používat ve všech řádcích této dávkové operace, protože Klientská knihovna pro každou dávkovou operaci povoluje pouze jeden objekt Options (a tedy jednu zásadu/KEK). Knihovna klienta ale interně vygeneruje v dávce nový náhodný a náhodný CEK na řádek. Uživatelé také mohou zvolit šifrování různých vlastností každé operace v dávce definováním tohoto chování v překladači šifrování.

### <a name="queries"></a>Dotazy
> [!NOTE]
> Vzhledem k tomu, že jsou entity zašifrované, nemůžete spouštět dotazy, které filtrují na zašifrovanou vlastnost.  Pokud se pokusíte, výsledky budou nesprávné, protože se služba snaží porovnat zašifrovaná data s nezašifrovanými daty.
> 
> 
> Chcete-li provést operace s dotazem, je nutné zadat překladač klíčů, který dokáže vyřešit všechny klíče v sadě výsledků dotazu. Pokud entitu obsaženou ve výsledku dotazu nelze přeložit na zprostředkovatele, bude vyvolána chyba klientské knihovny. Pro všechny dotazy, které provádějí projekce na straně serveru, knihovna klienta přidá ve výchozím nastavení pro vybrané sloupce speciální vlastnosti šifrovacích metadat (_ClientEncryptionMetadata1 a _ClientEncryptionMetadata2).

## <a name="azure-key-vault"></a>Azure Key Vault
Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Pomocí Azure Key Vault můžou uživatelé šifrovat klíče a tajné klíče (například ověřovací klíče, klíče účtu úložiště, šifrovací klíče dat,. Soubory PFX a hesla) pomocí klíčů chráněných moduly hardwarového zabezpečení (HSM). Další informace najdete v článku [Co je Azure Key Vault](../../key-vault/general/overview.md).

Klientská knihovna pro úložiště používá základní knihovnu Key Vault, aby poskytovala společné rozhraní napříč Azure pro správu klíčů. Uživatelé také získají další výhody použití knihovny rozšíření Key Vault. Knihovna rozšíření poskytuje užitečné funkce kolem jednoduchých a bezproblémového místního a cloudového poskytovatele RSA a také s možností agregace a ukládání do mezipaměti.

### <a name="interface-and-dependencies"></a>Rozhraní a závislosti
Existují tři Key Vault balíčky:  

* Azure – modul úložiště klíčů – jádro obsahuje rozhraní IKey a IKeyResolver. Jedná se o malý balíček bez závislostí. Klientská knihovna pro úložiště pro jazyk Java ji definuje jako závislost.
* Azure – Trezor klíčů obsahuje klienta služby Key Vault REST.  
* Azure – úložiště klíčů – rozšíření obsahují kód rozšíření, který zahrnuje implementace kryptografických algoritmů a RSAKey a SymmetricKey. Závisí na oborech názvů základní a trezoru klíčů a poskytuje funkce pro definování agregovaného překladače (když uživatelé chtějí používat víc zprostředkovatelů klíčů) a překladač klíčů pro ukládání do mezipaměti. I když klientská knihovna pro úložiště nezávisí přímo na tomto balíčku, pokud uživatelé chtějí použít Azure Key Vault k ukládání klíčů nebo k používání rozšíření Key Vault ke využívání místních a cloudových zprostředkovatelů kryptografických služeb, bude tento balíček potřebovat.  
  
  Key Vault je navržená pro hlavní klíče s vysokou hodnotou a omezení omezování na Key Vault jsou navržená s ohledem na to. Při provádění šifrování na straně klienta s Key Vault je upřednostňovaným modelem použití symetrických hlavních klíčů uložených jako tajné klíče v Key Vault a v mezipaměti místně. Uživatelé musí provést následující akce:  

1. Vytvořte tajný kód offline a nahrajte ho do Key Vault.  
2. Použijte základní identifikátor tajného klíče jako parametr k vyřešení aktuální verze tajného klíče pro šifrování a místní ukládání těchto informací do mezipaměti. Použít CachingKeyResolver pro ukládání do mezipaměti; pro uživatele není očekávána implementace vlastní logiky ukládání do mezipaměti.  
3. Při vytváření zásad šifrování používejte překladač mezipaměti jako vstup.
   Další informace o využití Key Vault najdete v ukázkách šifrovacího kódu.

## <a name="best-practices"></a>Osvědčené postupy
Podpora šifrování je k dispozici pouze v klientské knihovně pro úložiště pro jazyk Java.

> [!IMPORTANT]
> Pamatujte na tyto důležité body při použití šifrování na straně klienta:
> 
> * Při čtení nebo zápisu do šifrovaného objektu BLOB použijte úplné příkazy pro nahrání objektů BLOB a rozsah nebo celé objekty pro stažení objektů BLOB. Vyhněte se zápisu do zašifrovaného objektu BLOB pomocí operací protokolu, jako je blok vložení, seznam blokovaných objektů, zápis stránek, vymazat stránky nebo připojit blok; v opačném případě může dojít k poškození šifrovaného objektu BLOB a zpřístupnění ho nečitelným.
> * V případě tabulek existuje podobné omezení. Nezapomeňte neaktualizovat šifrované vlastnosti bez aktualizace metadat šifrování.  
> * Pokud nastavíte metadata pro zašifrovaný objekt blob, můžete přepsat metadata týkající se šifrování, která jsou nutná k dešifrování, protože nastavení metadat není aditivní. To platí také pro snímky; Vyhněte se zadávání metadat při vytváření snímku šifrovaného objektu BLOB. Pokud musí být nastavena metadata, nezapomeňte nejprve zavolat metodu **downloadAttributes** a získat aktuální šifrovací metadata a vyhnout se souběžným zápisům při nastavování metadat.  
> * Povolte pro uživatele, kteří mají pracovat pouze s šifrovanými daty, příznak **requireEncryption** ve výchozích možnostech žádosti. Další informace najdete níže.  
> 
> 

## <a name="client-api--interface"></a>Rozhraní API klienta/rozhraní
Při vytváření objektu EncryptionPolicy můžou uživatelé zadat jenom klíč (implementující IKey), jenom překladač (implementující IKeyResolver) nebo obojí. IKey je základní typ klíče, který je identifikován pomocí identifikátoru klíče a poskytuje logiku pro balení a rozbalení. IKeyResolver se používá k překladu klíče během dešifrovacího procesu. Definuje metodu ResolveKey, která vrací IKey pro daný identifikátor klíče. To umožňuje uživatelům volit mezi několika klíči, které jsou spravovány ve více umístěních.

* Pro šifrování se klíč použije vždycky a absence klíče bude mít za následek chybu.  
* Pro dešifrování:  
  
  * Překladač klíčů je vyvolán, pokud je zadán pro získání klíče. Pokud je překladač zadán, ale nemá mapování pro identifikátor klíče, je vyvolána chyba.  
  * Pokud není překladač zadán, ale je zadán klíč, použije se klíč, pokud jeho identifikátor odpovídá požadovanému identifikátoru klíče. Pokud identifikátor neodpovídá, je vyvolána chyba.  
    
    [Ukázky šifrování](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) demonstrují podrobnější scénář pro objekty blob, fronty a tabulky společně s Key Vault integrací.

### <a name="requireencryption-mode"></a>RequireEncryption režim
Uživatelé mohou volitelně povolit režim operace, kde všechna nahraná a stažená soubory musí být zašifrovaná. V tomto režimu se pokusy o nahrání dat bez zásad šifrování nebo stažení dat, která nejsou ve službě zašifrovaná, selžou na klientovi. Toto chování řídí příznak **requireEncryption** objektu možnosti požadavku. Pokud aplikace zašifruje všechny objekty uložené v Azure Storage, můžete nastavit vlastnost **requireEncryption** na výchozí možnosti požadavku pro objekt klienta služby.   

Použijte například **CloudBlobClient. getDefaultRequestOptions (). setRequireEncryption (true)** , aby se vyžadovalo šifrování pro všechny operace objektů BLOB provedené prostřednictvím tohoto objektu klienta.

### <a name="blob-service-encryption"></a>Blob service šifrování
Vytvořte objekt **BlobEncryptionPolicy** a nastavte ho v možnostech žádosti (na rozhraní API nebo na úrovni klienta pomocí **DefaultRequestOptions**). Všechny ostatní budou zpracovávány v interní knihovně klienta.

# <a name="java-v12"></a>[Java V12](#tab/java)

V současné době pracujeme na vytváření fragmentů kódu, které reflektují verze 12. x Azure Storage klientských knihoven. Další informace najdete v tématu [oznamujeme Azure Storage klientské knihovny V12](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="java-v8"></a>[Java V8](#tab/java8)

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
---

### <a name="queue-service-encryption"></a>Služba front šifrování
Vytvořte objekt **QueueEncryptionPolicy** a nastavte ho v možnostech žádosti (na rozhraní API nebo na úrovni klienta pomocí **DefaultRequestOptions**). Všechny ostatní budou zpracovávány v interní knihovně klienta.

# <a name="java-v12"></a>[Java V12](#tab/java)

V současné době pracujeme na vytváření fragmentů kódu, které reflektují verze 12. x Azure Storage klientských knihoven. Další informace najdete v tématu [oznamujeme Azure Storage klientské knihovny V12](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="java-v8"></a>[Java V8](#tab/java8)

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
---

### <a name="table-service-encryption"></a>Table service šifrování
Kromě vytváření zásad šifrování a jejich nastavení v možnostech žádosti musíte buď zadat **EncryptionResolver** v **TableRequestOptions**, nebo nastavit atribut [Encrypt] pro metodu getter a setter entity.

### <a name="using-the-resolver"></a>Použití překladače

# <a name="java-v12"></a>[Java V12](#tab/java)

V současné době pracujeme na vytváření fragmentů kódu, které reflektují verze 12. x Azure Storage klientských knihoven. Další informace najdete v tématu [oznamujeme Azure Storage klientské knihovny V12](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="java-v8"></a>[Java V8](#tab/java8)

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
---

### <a name="using-attributes"></a>Použití atributů
Jak je uvedeno výše, pokud entita implementuje TableEntity, pak vlastnosti getter a setter lze dekorovat pomocí atributu [Encrypt] namísto zadání **EncryptionResolver**.

# <a name="java-v12"></a>[Java V12](#tab/java)

V současné době pracujeme na vytváření fragmentů kódu, které reflektují verze 12. x Azure Storage klientských knihoven. Další informace najdete v tématu [oznamujeme Azure Storage klientské knihovny V12](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="java-v8"></a>[Java V8](#tab/java8)

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
---

## <a name="encryption-and-performance"></a>Šifrování a výkon

Všimněte si, že šifrování dat úložiště má za následek zvýšené nároky na výkon. Klíč obsahu a IV se musí vygenerovat, samotný obsah musí být zašifrovaný a další metadata musí být naformátovaná a nahraná. Tato režie se bude lišit v závislosti na množství šifrovaných dat. Zákazníkům doporučujeme, aby při vývoji vždy otestovali své aplikace na výkon.

## <a name="next-steps"></a>Další kroky

* Azure Storage stažení [balíčku klientské knihovny pro Maven Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)  
* Stažení [klientské knihovny Azure Storage pro zdrojový kód Java z GitHubu](https://github.com/Azure/azure-storage-java)
* Stažení knihovny Azure Key Vault Maven pro balíčky Java Maven:
  * [Základní](https://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault-core) balíček
  * Balíček [klienta](https://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault)
* Navštivte [dokumentaci k Azure Key Vault](../../key-vault/general/overview.md)
