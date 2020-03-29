---
title: Šifrování na straně klienta pomocí Pythonu
titleSuffix: Azure Storage
description: Klientská knihovna úložiště Azure pro Python podporuje šifrování na straně klienta pro maximální zabezpečení aplikací Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.devlang: python
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 16e66cd762b86b27dc6703542ca7261b2300a33b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74895371"
---
# <a name="client-side-encryption-with-python"></a>Šifrování na straně klienta pomocí Pythonu

[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Přehled
[Klientská knihovna úložiště Azure pro Python](https://pypi.python.org/pypi/azure-storage) podporuje šifrování dat v klientských aplikacích před odesláním do Úložiště Azure a dešifrování dat při stahování do klienta.

> [!NOTE]
> Knihovna Azure Storage Python je ve verzi preview.
> 
> 

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Šifrování a dešifrování pomocí obálkové techniky
Procesy šifrování a dešifrování se řídí technikou obálky.

### <a name="encryption-via-the-envelope-technique"></a>Šifrování pomocí obálkové techniky
Šifrování pomocí obálky technika funguje následujícím způsobem:

1. Klientská knihovna úložiště Azure generuje šifrovací klíč obsahu (CEK), což je jednorázový symetrický klíč.
2. Uživatelská data jsou šifrována pomocí této CEK.
3. CEK je pak zabalen (šifrován) pomocí šifrovacího klíče (KEK). KEK je identifikován identifikátor klíče a může být asymetrický pár klíčů nebo symetrický klíč, který je spravován místně.
   Samotná knihovna klienta úložiště nikdy nemá přístup k KEK. Knihovna vyvolá algoritmus obtékání klíčů, který je k dispozici KEK. Uživatelé si mohou v případě potřeby vybrat vlastní zprostředkovatele pro obtékání/rozbalení klíčů.
4. Šifrovaná data se pak nahrají do služby Azure Storage. Zabalený klíč spolu s některými dalšími šifrovacími metadaty je uložen jako metadata (na objektu blob) nebo interpolován šifrovanými daty (zprávy fronty a entity tabulky).

### <a name="decryption-via-the-envelope-technique"></a>Dešifrování pomocí obálkové techniky
Dešifrování pomocí obálky technika funguje následujícím způsobem:

1. Klientská knihovna předpokládá, že uživatel správu klíče šifrovací klíč (KEK) místně. Uživatel nemusí znát konkrétní klíč, který byl použit pro šifrování. Místo toho lze nastavit a použít překladač klíčů, který řeší různé identifikátory klíčů.
2. Klientská knihovna stáhne šifrovaná data spolu s libovolným šifrovacím materiálem uloženým ve službě.
3. Zalomený šifrovací klíč obsahu (CEK) se pak rozbalí (dešifruje) pomocí šifrovacího klíče (KEK). Zde opět klientská knihovna nemá přístup k KEK. Jednoduše vyvolá algoritmus rozbalení vlastního zprostředkovatele.
4. Šifrovací klíč obsahu (CEK) se pak používá k dešifrování šifrovaných uživatelských dat.

## <a name="encryption-mechanism"></a>Šifrovací mechanismus
Klientská knihovna úložiště používá [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) k šifrování uživatelských dat. Konkrétně režim [Cipher Block Chaining (CBC)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) s AES. Každá služba funguje poněkud jinak, takže budeme diskutovat o každém z nich zde.

### <a name="blobs"></a>Objekty blob
Klientská knihovna aktuálně podporuje šifrování pouze celých objektů BLOB. Konkrétně šifrování je podporováno, když uživatelé používají metody **create***. Pro stahování jsou podporovány úplné i rozsah stahování a paralelizace nahrávání i stahování je k dispozici.

Během šifrování klientská knihovna vygeneruje náhodný inicializační vektor (IV) 16 bajtů spolu s náhodným šifrovacím klíčem obsahu (CEK) o 32 bajtů a provede šifrování obálek dat objektů blob pomocí těchto informací. Zabalená CEK a některá další šifrovací metadata jsou pak uložena jako metadata objektu blob spolu se šifrovaným objektem blob ve službě.

> [!WARNING]
> Pokud upravujete nebo nahráváte vlastní metadata pro objekt blob, musíte zajistit, aby tato metadata byla zachována. Pokud nahrajete nová metadata bez těchto metadat, zalomené CEK, IV a další metadata se ztratí a obsah objektu blob už nikdy nebude možné znovu načíst.
> 
> 

Stahování šifrovaného objektu blob zahrnuje načítání obsahu celého objektu blob pomocí metod **get*** convenience. Zabalená sada CEK je rozbalena a používá se společně s IV (v tomto případě uložená jako metadata objektů blob) k vrácení dešifrovaných dat uživatelům.

Stahování libovolného rozsahu (**get*** metody s parametry rozsahu předaných v) v šifrovaném objektu blob zahrnuje úpravu rozsahu poskytovaného uživateli, aby bylo možné získat malé množství dalších dat, které lze použít k úspěšnému dešifrování požadovaného rozsahu.

Blokové objekty BLOB a objekty BLOB stránky lze zašifrovat nebo dešifrovat pomocí tohoto schématu. V současné době neexistuje žádná podpora pro šifrování objektů BLOB připojit.

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
3. Zabalená CEK a některá další šifrovací metadata jsou pak uložena jako dvě další rezervované vlastnosti. První vyhrazená\_vlastnost ( ClientEncryptionMetadata1) je vlastnost řetězce, která obsahuje informace o IV, verzi a zabaleném klíči. Druhá vyhrazená\_vlastnost ( ClientEncryptionMetadata2) je binární vlastnost, která obsahuje informace o vlastnostech, které jsou šifrovány. Informace v této druhé\_vlastnosti ( ClientEncryptionMetadata2) je sám zašifrován.
4. Vzhledem k těmto další vyhrazené vlastnosti potřebné pro šifrování, uživatelé mohou nyní mít pouze 250 vlastní vlastnosti namísto 252. Celková velikost entity musí být menší než 1 MB.

   Všimněte si, že lze zašifrovat pouze vlastnosti řetězce. Pokud mají být šifrovány jiné typy vlastností, musí být převedeny na řetězce. Šifrované řetězce jsou uloženy ve službě jako binární vlastnosti a jsou převedeny zpět na řetězce (nezpracované řetězce, nikoli EntityProperties s typem EdmType.STRING) po dešifrování.

   U tabulek musí uživatelé kromě zásad šifrování zadat vlastnosti, které mají být šifrovány. To lze provést buď ukládání těchto vlastností v TableEntity objekty s typem nastavenna EdmType.STRING a šifrování nastavena na hodnotu true nebo nastavení encryption_resolver_function na tableservice objektu. Překládání šifrování je funkce, která přebírá klíč oddílu, klíč řádku a název vlastnosti a vrátí logickou hodnotu, která označuje, zda má být tato vlastnost zašifrována. Během šifrování bude klientská knihovna používat tyto informace k rozhodnutí, zda má být vlastnost při zápisu do drátu šifrována. Delegát také poskytuje možnost logiky kolem jak jsou zašifrovány vlastnosti. (Například pokud X, pak šifrovat vlastnost A; jinak šifrovat vlastnosti A a B.) Všimněte si, že není nutné poskytnout tyto informace při čtení nebo dotazování entit.

### <a name="batch-operations"></a>Dávkové operace
Jedna zásada šifrování platí pro všechny řádky v dávce. Klientská knihovna interně vygeneruje nové náhodné IV a náhodné CEK na řádek v dávce. Uživatelé mohou také zvolit šifrování různých vlastností pro každou operaci v dávce definováním tohoto chování v překládání šifrování.
Pokud dávka je vytvořen jako správce kontextu prostřednictvím tableservice batch() metoda, tableservice zásady šifrování bude automaticky použita pro dávku. Pokud dávka je vytvořena explicitně voláním konstruktoru, zásady šifrování musí být předány jako parametr a ponechat beze změny po dobu životnosti dávky.
Všimněte si, že entity jsou šifrovány, jak jsou vloženy do dávky pomocí zásadšifrování dávky (entity nejsou šifrovány v době potvrzení dávky pomocí zásadšifrování tableservice).

### <a name="queries"></a>Dotazy
> [!NOTE]
> Vzhledem k tomu, že entity jsou šifrovány, nelze spustit dotazy, které filtrují šifrovanou vlastnost.  Pokud se pokusíte, výsledky budou nesprávné, protože služba by se pokouší porovnat šifrovaná data s nešifrovaná data.
> 
> 
> Chcete-li provést operace dotazu, je nutné zadat překládání klíčů, který je schopen přeložit všechny klíče v sadě výsledků. Pokud entitu obsaženou ve výsledku dotazu nelze přeložit zprostředkovateli, klientská knihovna vyvolá chybu. Pro všechny dotazy, které provádí projekce na straně serveru, klientská\_knihovna přidá \_ve výchozím nastavení do vybraných sloupců speciální vlastnosti metadat šifrování (ClientEncryptionMetadata1 a ClientEncryptionMetadata2).
> 
> [!IMPORTANT]
> Při použití šifrování na straně klienta si uvědomte tyto důležité body:
> 
> * Při čtení nebo zápisu do šifrovaného objektu blob použijte příkazy pro nahrávání celého objektu blob a příkazy pro stahování objektů blob. Vyhněte se zápisu do šifrovaného objektu blob pomocí operací protokolu, jako je blok Put, seznam blokovaných položek, zápis stránek nebo vymazat stránky; v opačném případě můžete poškozený objekt blob poškodit a učinit jej nečitelným.
> * Pro tabulky existuje podobné omezení. Dávejte pozor, abyste neaktualizovali šifrované vlastnosti bez aktualizace metadat šifrování.
> * Pokud nastavíte metadata na šifrovaném objektu blob, můžete přepsat metadata související s šifrováním požadovaná pro dešifrování, protože nastavení metadat není aditivní. To platí také pro snímky; vyhněte se zadávání metadat při vytváření snímku šifrovaného objektu blob. Pokud metadata musí být nastavena, nezapomeňte nejprve volat **get_blob_metadata** metodu získat aktuální metadata šifrování a vyhnout se souběžné zápisy při nastavování metadat.
> * Povolte **příznak require_encryption** na objektu služby pro uživatele, kteří by měli pracovat pouze se šifrovanými daty. Viz níže pro více informací.

Knihovna klienta úložiště očekává, že poskytované KEK a klíč překládání implementovat následující rozhraní. [Podpora Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pro správu Pythonu KEK čeká na vyřízení a po dokončení bude integrována do této knihovny.

## <a name="client-api--interface"></a>Klientské rozhraní API / rozhraní
Po vytvoření objektu služby úložiště (tj. blockblobservice) může uživatel přiřadit hodnoty polím, která tvoří zásady šifrování: key_encryption_key, key_resolver_function a require_encryption. Uživatelé mohou poskytnout pouze KEK, pouze překládání nebo obojí. key_encryption_key je základní typ klíče, který je identifikován pomocí identifikátoru klíče a který poskytuje logiku pro zalamování/rozbalování. key_resolver_function se používá k vyřešení klíče během procesu dešifrování. Vrátí platný KEK s přiděleným identifikátorem klíče. To poskytuje uživatelům možnost vybrat si mezi více klíčů, které jsou spravovány ve více umístěních.

KEK musí implementovat následující metody úspěšně šifrovat data:

* wrap_key(cek): Zabalí zadaný CEK (bajty) pomocí algoritmu podle volby uživatele. Vrátí zabalený klíč.
* get_key_wrap_algorithm(): Vrátí algoritmus použitý k zabalení klíčů.
* get_kid(): Vrátí id klíče řetězce pro tento KEK.
  KEK musí implementovat následující metody k úspěšnému dešifrování dat:
* unwrap_key(cek, algoritmus): Vrátí nezabalenou formu zadaného CEK pomocí algoritmu zadaného řetězcem.
* get_kid(): Vrátí id klíče řetězce pro tento KEK.

Překladač klíčů musí alespoň implementovat metodu, která dané id klíče vrátí odpovídající KEK implementaci rozhraní výše. Pouze tato metoda má být přiřazena vlastnosti key_resolver_function na objektu služby.

* Pro šifrování klíč se používá vždy a absence klíče bude mít za následek chybu.
* Pro dešifrování:

  * Překladač klíčů je vyvolán, pokud je zadán získat klíč. Pokud je zadán překladač, ale nemá mapování pro identifikátor klíče, je vyvolána chyba.
  * Pokud překladač není zadán, ale je zadán klíč, klíč se používá, pokud jeho identifikátor odpovídá požadovanému identifikátoru klíče. Pokud identifikátor neodpovídá, je vyvolána chyba.

    Vzorky šifrování v azure.storage.samples ukazují podrobnější scénář od konce konce pro objekty BLOB, fronty a tabulky.
      Ukázkové implementace KEK a překladač klíčů jsou k dispozici v ukázkových souborech jako KeyWrapper a KeyResolver v uvedeném pořadí.

### <a name="requireencryption-mode"></a>Režim RequireEncryption
Uživatelé mohou volitelně povolit provozní režim, ve kterém musí být zašifrovány všechny obrázky a soubory ke stažení. V tomto režimu se pokusy o nahrání dat bez zásad šifrování nebo stahování dat, která nejsou ve službě šifrována, v klientovi nezdaří. Příznak **require_encryption** na objektu služby řídí toto chování.

### <a name="blob-service-encryption"></a>Šifrování služby objektů blob
Nastavte pole zásad šifrování na objektu blockblobservice. Vše ostatní bude zpracováno klientskou knihovnou interně.

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_block_blob_service.key_encryption_key = kek
my_block_blob_service.key_resolver_funcion = key_resolver.resolve_key

# Upload the encrypted contents to the blob.
my_block_blob_service.create_blob_from_stream(
    container_name, blob_name, stream)

# Download and decrypt the encrypted contents from the blob.
blob = my_block_blob_service.get_blob_to_bytes(container_name, blob_name)
```

### <a name="queue-service-encryption"></a>Šifrování služby fronty
Nastavte pole zásad šifrování na objektu queueservice. Vše ostatní bude zpracováno klientskou knihovnou interně.

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

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

### <a name="table-service-encryption"></a>Šifrování služby Table Service
Kromě vytvoření zásadšifrování a nastavení na možnosti požadavku, musíte buď zadat **encryption_resolver_function** na **tableservice**, nebo nastavit atribut encrypt na EntityProperty.

### <a name="using-the-resolver"></a>Použití překládání

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

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
my_table_service.get_entity(
    table_name, entity['PartitionKey'], entity['RowKey'])
```

### <a name="using-attributes"></a>Použití atributů
Jak bylo uvedeno výše, vlastnost může být označena pro šifrování uložením do entityProperty objektu a nastavením šifrovacího pole.

```python
encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)
```

## <a name="encryption-and-performance"></a>Šifrování a výkon
Všimněte si, že šifrování dat úložiště má za následek další režii výkonu. Klíč obsahu a IV musí být generovány, samotný obsah musí být šifrován a další metadata musí být formátována a odeslána. Tato režie se bude lišit v závislosti na množství šifrovaných dat. Doporučujeme, aby zákazníci vždy testovat své aplikace na výkon během vývoje.

## <a name="next-steps"></a>Další kroky
* Stažení [balíčku klienta úložiště Azure pro Java PyPi](https://pypi.python.org/pypi/azure-storage)
* Stažení [klientské knihovny úložiště Azure pro zdrojový kód Pythonu z GitHubu](https://github.com/Azure/azure-storage-python)
