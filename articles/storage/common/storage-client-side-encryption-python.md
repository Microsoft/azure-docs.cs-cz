---
title: Šifrování na straně klienta s využitím Pythonu pro službu Microsoft Azure Storage | Dokumentace Microsoftu
description: Klientská knihovna Azure Storage pro Python podporuje šifrování na straně klienta z důvodu maximálního zabezpečení pro vaše aplikace Azure Storage.
services: storage
author: lakasa
ms.service: storage
ms.devlang: python
ms.topic: article
ms.date: 05/11/2017
ms.author: lakasa
ms.subservice: common
ms.openlocfilehash: dfff159d7e0204a752935458a2b4845499c0d652
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55453395"
---
# <a name="client-side-encryption-with-python-for-microsoft-azure-storage"></a>Šifrování na straně klienta s využitím Pythonu pro službu Microsoft Azure Storage
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Přehled
[Klientská knihovna Azure Storage pro Python](https://pypi.python.org/pypi/azure-storage) podporuje šifrování dat v rámci klientské aplikace před nahráním do služby Azure Storage a dešifrování dat při stahování do klienta.

> [!NOTE]
> Knihovna Python pro úložiště Azure je ve verzi preview.
> 
> 

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Šifrování a dešifrování prostřednictvím techniku obálky
Procesy šifrování a dešifrování podle techniku obálky.

### <a name="encryption-via-the-envelope-technique"></a>Šifrování prostřednictvím techniku obálky
Šifrování prostřednictvím obálky technika pracuje následujícím způsobem:

1. Klientská knihovna pro úložiště Azure vygeneruje obsahu šifrovací klíč (CEK), což je použití jednoho jednorázových symetrický klíč.
2. Uživatel data se šifrují pomocí této CEK.
3. CEK je vnořena (zašifrovaný) pomocí šifrovací klíč klíče (KEK). Klíče KEK je identifikovaná identifikátorem klíče a může být asymetrický pár klíčů nebo symetrický klíč, který je spravovaný místně.
   Klientská knihovna pro úložiště, samotný nikdy má přístup k KEK. Knihovny vyvolá klíče zabalení algoritmus, který je poskytován klíče KEK. Uživatelé mohou používat vlastní zprostředkovatelé pro klíče zabalení a rozbalení v případě potřeby.
4. Šifrovaná data se pak nahrají do služby Azure Storage. Zabalená klíče spolu s některé další šifrování metadat je uložená jako metadata (na objekt blob) nebo interpolovaných s šifrovaná data (zprávy fronty a tabulky entity).

### <a name="decryption-via-the-envelope-technique"></a>Dešifrování pomocí obálky technika
Dešifrování pomocí obálky technika pracuje následujícím způsobem:

1. Knihovna klienta předpokládá, že uživatel spravuje šifrovací klíč klíče (KEK) místně. Uživatel není potřeba zjistit, konkrétní klíč, který se použil pro šifrování. Místo toho překladače klíče, které se překládá identifikátory klíče ke klíčům, můžete nastavit a použít.
2. Klientská knihovna šifrovaná data spolu se žádné šifrování materiál, který je uložený ve službě soubory ke stažení.
3. Zabalená obsahu šifrovací klíč (CEK) je rozbalená (dešifrovaný) pomocí klíčů šifrování klíčů (KEK). Zde znovu, knihovna klienta nemá přístup k KEK. Jednoduše volá rozbalení algoritmus vlastního zprostředkovatele.
4. Klíč obsahu šifrovací (CEK) se pak použije k dešifrování šifrovaného uživatelská data.

## <a name="encryption-mechanism"></a>Mechanismus šifrování
Klientská knihovna pro úložiště používá [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) pro šifrování dat uživatele. Konkrétně [Cipher Block řetězení (CBC)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) režimu s AES. Každý trochu jinak, service funguje tak, že bude každý z nich tady probereme.

### <a name="blobs"></a>Objekty blob
Klientská knihovna aktuálně podporuje šifrování pouze celé objekty BLOB. Konkrétně šifrování je podporované, když uživatelé používají **vytvořit*** metody. Pro soubory ke stažení, i kompletní a soubory ke stažení rozsahu jsou podporované a paralelizaci nahrávání a stahování je k dispozici.

Při šifrování klientské knihovny bude generovat náhodné inicializační vektor (IV) 16 bajtů, společně s náhodné obsahu šifrovací klíč (CEK) 32 bajtů a provádět obálky šifrování dat objektů blob pomocí těchto informací. Zabalená CEK a některé další šifrování metadat jsou pak uloženy jako metadata spolu s zašifrovaný objekt blob ve službě objektů blob.

> [!WARNING]
> Pokud jsou úpravy nebo odesílání vlastních metadat pro tento objekt blob, je potřeba zajistit, že tato metadata je zachováno. Pokud nahrajete nová metadata bez těchto metadat, zabalené CEK, IV a další metadata budou ztraceny a obsah objektu blob se nikdy bude nenávratně ztracený.
> 
> 

Stažení objektu blob šifrované zahrnuje načítání obsahu z celý objekt blob pomocí **získat*** vhodné metody. Zabalené CEK je neobalený a používá se společně s IV (uložené v tomto případě jako metadata objektu blob) k vrácení dešifrovaná data pro uživatele.

Stažení libovolného rozsahu (**získat*** metody s parametry rozsahu předaný) v objektu blob šifrované zahrnuje úpravy rozsahu poskytuje uživatelům zajistí malé množství další data, která slouží k úspěšně dešifrování požadovaný rozsah.

Objekty BLOB bloku a objekty BLOB stránky pouze může být šifrované/dešifrovat použití tohoto schématu. Současné době není dostupná podpora pro šifrování doplňovacích objektů BLOB.

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
3. Zabalená CEK a některé další šifrování metadat jsou pak uloženy jako dvě další rezervované vlastnosti. První rezervované vlastnosti (\_ClientEncryptionMetadata1) je řetězec, který obsahuje informace o IV, verzi a zabalené klíč. Druhá rezervované vlastnosti (\_ClientEncryptionMetadata2) je binární vlastnost, která uchovává informace o vlastnosti, která jsou šifrovaná. Informace v této druhé vlastnosti (\_ClientEncryptionMetadata2) sama o sobě šifrovaná.
4. Z důvodu tyto další rezervované vlastnosti vyžadované pro šifrování uživatelé nyní mohou mít pouze 250 vlastní vlastnosti namísto 252. Celková velikost entity musí být menší než 1MB.
   
   Všimněte si, že lze pouze vlastnosti řetězce zašifrovaná. Pokud se ostatní typy vlastností jsou zašifrované, musí být převedeny na řetězce. Šifrované řetězce jsou uložené ve službě jako binární vlastnosti a jsou převedeny na řetězce (nezpracovaná řetězce, nikoli EntityProperties s typem EdmType.STRING) zpět po dešifrování.
   
   Pro tabulky, vedle zásady šifrování musí uživatelé zadat vlastnosti, které mají být šifrována. To lze provést buď uložení těchto vlastností v objektech TableEntity je typ nastaven na EdmType.STRING a šifrování nastavenou na hodnotu true nebo nastavením encryption_resolver_function tableservice objektu. Překladač šifrování je funkce, která přebírá klíč oddílu, klíč řádku a název vlastnosti a vrací logickou hodnotu, která určuje, jestli by měl být šifrovaná tuto vlastnost. Při šifrování klientské knihovny použije tyto informace se rozhodnout, zda vlastnost by se měla šifrovat během zápisu lince. Delegát také poskytuje možnost logiky po tom, jak jsou zašifrované vlastnosti. (Například, pokud X, pak šifrování vlastnost A; v opačném případě šifrování vlastnosti A a B.) Všimněte si, že není potřeba zadat tyto informace při čtení nebo dotazování entit.

### <a name="batch-operations"></a>Dávkové operace
Jedna zásada šifrování platí pro všechny řádky v dávce. Klientská knihovna interně vygenerujte nový náhodný IV a náhodné CEK každý řádek v dávce. Uživatelé také mohou šifrovat definováním toto chování překladač šifrování různé vlastnosti pro všechny operace v dávce.
Pokud dávku se vytvoří jako místní správce prostřednictvím metody batch() tableservice, tableservice pro šifrování zásady použijí automaticky služby batch. Pokud dávky je explicitně nevytvořili zavoláním konstruktoru, musíte být zásady šifrování předaného jako parametr a vlevo ponechat beze změny po dobu životnosti služby batch.
Všimněte si, že entity jsou zašifrované, jako jsou vloženy do služby batch pomocí zásad šifrování služby batch (entity nejsou šifrovaná při potvrzování služby batch pomocí zásad šifrování tableservice).

### <a name="queries"></a>Dotazy
> [!NOTE]
> Vzhledem k tomu, entit je jím zašifrovaná, nelze spustit dotazy, které filtrují v zašifrované vlastnosti.  Pokud se pokusíte, budou výsledky nesprávné, služba by se pokouší o porovnání šifrovaná data s nešifrovaným datům.
> 
>
K provedení operace dotazů, je nutné zadat překladače klíče, který je schopen převést všechny klíče v sadě výsledků. Pokud se entity obsažené ve výsledku dotazu. nelze přeložit na zprostředkovateli, klientské knihovny vyvolá chybu. Pro každého dotazu, který provádí projekce na straně serveru, klientské knihovny přidá vlastnosti metadat speciální šifrování (\_ClientEncryptionMetadata1 a \_ClientEncryptionMetadata2) ve výchozím nastavení vybrané sloupce.

> [!IMPORTANT]
> Mějte na paměti těchto důležitých bodů při použití šifrování na straně klienta:
> 
> * Při čtení nebo zápisu do objektu blob šifrované, použijte příkazy nahrávání celého objektu blob a příkazy ke stažení objektů blob v rozsahu/celé. Vyhněte se zápis do blob šifrovaným pomocí operace protokolu například vložit blok, vložte seznam blokovaných položek, zápis stránky nebo vymazat stránky; jinak může dojít k poškození zašifrovaný objekt blob a nastavte ji nejde přečíst.
> * Pro tabulky existuje podobná omezení. Buďte opatrní při aktualizaci šifrované vlastnosti bez aktualizace metadat šifrování.
> * Pokud nastavíte metadata pro objekt blob šifrované, mohou přepsat šifrování metadat požadovaná pro dešifrování, protože nastavení metadat není sčítání. To platí také pro snímky Neurčujte metadat vytvřit snímek objektu blob šifrované. Pokud metadata musí být nastavena, je nutné volat **get_blob_metadata** nejprve má metoda načíst aktuální metadata šifrování a vyhnout souběžných zápisů při metadat je nastavena.
> * Povolit **require_encryption** příznak na objekt služby pro uživatele, kteří by měla fungovat jenom s šifrovaná data. Další informace najdete níže.
> 
> 

Klientská knihovna pro úložiště očekává, že zadaná KEK a klíče překladač implementovat následující rozhraní. [Služba Azure Key Vault](https://azure.microsoft.com/services/key-vault/) podporu pro Python KEK správy čeká na vyřízení a bude se integrovat do této knihovny po dokončení.

## <a name="client-api--interface"></a>Klientské rozhraní API / rozhraní
Po vytvoření objektu služby storage (tj. blockblobservice), uživatel může přiřadit hodnoty polí, které tvoří zásady šifrování: key_encryption_key key_resolver_function a require_encryption. Uživatelé mohou zadat pouze KEK pouze překladače, nebo obojí. key_encryption_key je základní typ klíče, který je identifikován pomocí identifikátoru klíče, který poskytuje logiku pro zabalení a rozbalení. key_resolver_function se používá k překladu během procesu dešifrovací klíč. Vrátí platný KEK zadaný identifikátor klíče. To poskytuje uživatelům možnost výběru mezi více klíčů, které jsou spravovány v několika umístěních.

Klíče KEK musí implementovat následující metody, které úspěšně šifrování dat:

* wrap_key(cek): Zabalí zadanou CEK (v bajtech), pomocí algoritmu podle volby uživatele. Vrátí zabaleného klíče.
* get_key_wrap_algorithm(): Vrátí algoritmus, který slouží k zabalení klíče.
* get_kid(): Vrátí řetězec id klíče pro tento KEK.
  Klíče KEK musí implementovat následující metody, které úspěšně dešifrovat data:
* unwrap_key (cek, algoritmus): Vrátí nezabalené formu zadané CEK pomocí algoritmu zadaný řetězec.
* get_kid(): Vrátí id klíče řetězec pro tento KEK.

Překladač klíčů alespoň musí implementovat metodu, která zadané id klíče, vrátí odpovídající klíče KEK implementující rozhraní výše. Jenom tato metoda slouží k přiřazení vlastnosti key_resolver_function na objekt služby.

* Pro šifrování klíč je používán vždy a absence klíč způsobí chybu.
* K dešifrování:
  
  * Překladač klíče je vyvolána, pokud zadaná k získání klíče. Pokud překladač je zadána, ale nemá žádné mapování pro identifikátor klíče, je vržena chyba.
  * Pokud překladač není zadán, ale zadaný klíč, klíč se používá, pokud jeho identifikátor odpovídá požadovaný identifikátor klíče. Pokud se neshoduje identifikátor, je vržena chyba.
    
    Ukázky šifrování azure.storage.samples <fix URL>předvádí podrobnější scénář začátku do konce pro objekty BLOB, fronty a tabulky.
      Ukázková implementace KEK a překladač klíče jsou k dispozici v ukázkové soubory jako KeyWrapper a KeyResolver v uvedeném pořadí.

### <a name="requireencryption-mode"></a>Režim RequireEncryption
Uživatelé mohou volitelně povolit režim operace, kde musí být všechny nahrávání a stahování šifrovaná. V tomto režimu se nezdaří pokusy o nahrání dat bez zásady šifrování nebo stahování dat, které nejsou šifrovány ve službě na straně klienta. **Require_encryption** příznak na objekt služby, určuje toto chování.

### <a name="blob-service-encryption"></a>Šifrování objektů BLOB service
U objektu blockblobservice nastavte šifrování zásad pole. Všechno ostatní bude zpracován adresou klientské knihovny interně.

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1') # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_block_blob_service.key_encryption_key = kek
my_block_blob_service.key_resolver_funcion = key_resolver.resolve_key

# Upload the encrypted contents to the blob.
my_block_blob_service.create_blob_from_stream(container_name, blob_name, stream)

# Download and decrypt the encrypted contents from the blob.
blob = my_block_blob_service.get_blob_to_bytes(container_name, blob_name)
```

### <a name="queue-service-encryption"></a>Šifrování služby Queue
U objektu queueservice nastavte šifrování zásad pole. Všechno ostatní bude zpracován adresou klientské knihovny interně.

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1') # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_queue_service.key_encryption_key = kek
my_queue_service.key_resolver_funcion = key_resolver.resolve_key

# Add message
my_queue_service.put_message(queue_name, content)

# Retrieve message
retrieved_message_list = my_queue_service.get_messages(queue_name)
```

### <a name="table-service-encryption"></a>Šifrování služby Table
Kromě vytváření zásad šifrování a nastavíte ho na možnosti žádosti, je třeba buď zadat **encryption_resolver_function** na **tableservice**, nebo nastavte atribut šifrování EntityProperty.

### <a name="using-the-resolver"></a>Použitím překladač

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1') # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Define the encryption resolver_function.
def my_encryption_resolver(pk, rk, property_name):
    if property_name == 'foo':
        return True
    return False

# Set the KEK and key resolver on the service object.
my_table_service.key_encryption_key = kek
my_table_service.key_resolver_funcion = key_resolver.resolve_key
my_table_service.encryption_resolver_function = my_encryption_resolver

# Insert Entity
my_table_service.insert_entity(table_name, entity)

# Retrieve Entity
# Note: No need to specify an encryption resolver for retrieve, but it is harmless to leave the property set.
my_table_service.get_entity(table_name, entity['PartitionKey'], entity['RowKey'])
```

### <a name="using-attributes"></a>Pomocí atributů
Jak je uvedeno výše, mohou být označeny vlastností pro šifrování tak, že ho uložíte v objektu EntityProperty a nastavení šifrování pole.

```python
encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)
```

## <a name="encryption-and-performance"></a>Šifrování a výkonu
Všimněte si, že šifruje vaše výsledky úložiště dat v dalším výkonnostním režiím. Vygenerovat symetrický klíč a vektor IV, samotný obsah musí být zašifrován a další metadata musí být ve formátu a nahrát. Tato dodatečná režie se liší v závislosti na množství dat, které jsou šifrované. Doporučujeme vždy testovat výkon při vývoji svých aplikací zákazníkům.

## <a name="next-steps"></a>Další postup
* Stáhněte si [Klientská knihovna pro úložiště Azure pro Javu PyPi balíček](https://pypi.python.org/pypi/azure-storage)
* Stáhněte si [Klientská knihovna Azure Storage pro Python zdrojový kód z Githubu](https://github.com/Azure/azure-storage-python)
