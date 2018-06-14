---
title: Šifrování na straně klienta s Python pro Microsoft Azure Storage | Microsoft Docs
description: Klientská knihovna pro úložiště Azure pro jazyk Python podporuje šifrování na straně klienta pro maximální zabezpečení pro vaše aplikace Azure Storage.
services: storage
documentationcenter: python
author: lakasa
manager: jahogg
editor: tysonn
ms.assetid: f9bf7981-9948-4f83-8931-b15679a09b8a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/11/2017
ms.author: lakasa
ms.openlocfilehash: c925b41d1654bd5c9b40438c4b6b9f402ec4bac2
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2018
ms.locfileid: "29742638"
---
# <a name="client-side-encryption-with-python-for-microsoft-azure-storage"></a>Šifrování na straně klienta s Python pro Microsoft Azure Storage
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Přehled
[Klientská knihovna pro úložiště Azure pro jazyk Python](https://pypi.python.org/pypi/azure-storage) podporuje šifrování dat v rámci klientské aplikace před nahráním do úložiště Azure a dešifrování dat při stahování do klienta.

> [!NOTE]
> Knihovna Python úložiště Azure je ve verzi preview.
> 
> 

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Šifrování a dešifrování pomocí technik obálky
Procesy šifrování a dešifrování podle technika obálku.

### <a name="encryption-via-the-envelope-technique"></a>Šifrování pomocí technik obálky
Šifrování pomocí technik obálky funguje následujícím způsobem:

1. Klientská knihovna pro úložiště Azure generuje obsahu šifrovací klíč (CEK), což je použití jednoho bránu symetrického klíče.
2. Data se šifrují pomocí této CEK.
3. CEK je vnořena (šifrované) pomocí klíčů šifrovacího klíče (KEK). Klíče KEK je identifikovaná identifikátorem klíče a může být pár asymetrických klíčů nebo symetrický klíč, který je spravovaný místně.
   Klientská knihovna pro úložiště, samotné nikdy má přístup k KEK. Knihovny vyvolá algoritmus zabalení klíče, který zajišťuje klíče KEK. Uživatelé mohou používat vlastní zprostředkovatele pro klíče zabalení/rozbalování v případě potřeby.
4. Šifrovaná data se pak odešlou do služby Azure Storage. Zabalená klíč společně se některé další šifrování metadat je uložena jako metadata (na binární rozsáhlý objekt) nebo interpolované s šifrovaná data (zprávy fronty a entity tabulky).

### <a name="decryption-via-the-envelope-technique"></a>Dešifrování pomocí technik obálky
Dešifrování pomocí technik obálky funguje následujícím způsobem:

1. Klientská knihovna předpokládá, že uživatel je Správa klíčů šifrovací klíč (KEK) místně. Uživatel nemusí vědět konkrétní klíč, který slouží k šifrování. Překladač klíče, který se přeloží různé klíče identifikátory ke klíčům, místo toho můžete nastavit a používat.
2. Klientská knihovna stáhne šifrovaná data spolu se žádné šifrování materiál, který je uložený ve službě.
3. Zabalená obsahu šifrovací klíč (CEK) je pak rozbalenou (dešifrovaný) používání klíčů šifrovací klíč (KEK). Zde znovu, knihovny klienta nemá přístup k KEK. Vyvolá jednoduše rozbalení algoritmem vlastního zprostředkovatele.
4. Obsahu šifrovací klíč (CEK) se pak používá k dešifrování dat šifrovaných uživatele.

## <a name="encryption-mechanism"></a>Mechanismus šifrování
Klientská knihovna pro úložiště používá [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) k šifrování dat uživatele. Konkrétně [šifrovací bloku algoritmem CBC](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) režimu pomocí standardu AES. Každý trochu jinak, služba funguje, se budeme zabývat každý z nich zde.

### <a name="blobs"></a>Objekty blob
Klientská knihovna aktuálně podporuje šifrování pouze celý objekty BLOB. Konkrétně je podporováno šifrování, pokud uživatelé používají **vytvořit*** metody. Pro stahování, obě dokončení a rozsah stahování jsou podporované a paralelizace nahrávání a stahování je k dispozici.

Během šifrování se klientské knihovny bude generovat náhodných inicializace vektoru (IV) 16 bajtů, společně s náhodných obsahu šifrovací klíč (CEK) 32 bajtů a provádí obálky šifrování dat objektů blob na základě těchto informací. Zabalená CEK a některé další šifrování metadata jsou pak uloženy jako objekt blob metadat spolu s zašifrovaný objekt blob ve službě.

> [!WARNING]
> Pokud jsou úpravy nebo odesílání vlastní metadata pro objekt blob, je třeba zajistit, že se zachová, i tato metadata. Pokud nahrajete novými metadaty bez těchto metadat, bude ztracena zabalené CEK, IV a další metadata a obsah objektu blob se nikdy bude nenávratně ztracený.
> 
> 

Stahování zašifrovaný objekt blob zahrnuje načítání obsah pomocí celý objekt blob **získat*** usnadňující metody. Zabalená CEK je úkony, spočívající a společně s IV (uložené v tomto případě jako metadata objektu blob) používá k vrácení dešifrovaná data pro uživatele.

Stahování libovolný rozsah (**získat*** předané metody s parametry rozsahu) v zašifrovaný objekt blob zahrnuje úpravy rozsahu poskytované uživatelé mohli malé množství další data, která slouží k dešifrování úspěšně požadovaný rozsah.

Objekty BLOB bloku a objekty BLOB stránky může být pouze šifrovat nebo dešifrovat použití tohoto schématu. Aktuálně nepodporuje se pro šifrování doplňovací objekty BLOB.

### <a name="queues"></a>Fronty
Vzhledem k tomu, že fronta zprávy můžou být libovolném formátu, klientské knihovny definuje vlastní formát, který zahrnuje inicializační vektor (IV) a šifrované obsahu šifrovací klíč (CEK) v textu zprávy.

Během šifrování se klientské knihovny generuje náhodné IV 16 bajtů společně s náhodných CEK 32 bajtů a provádí šifrování obálky text zprávy fronty pomocí těchto informací. Zabalená CEK a některé další šifrování metadat se pak přidají do zprávy ve frontě šifrované. Této upravené zprávy (zobrazené dole) je uložený ve službě.

```
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

Během dešifrování je zabalené klíč extrahovaným ze zprávy ve frontě a úkony, spočívající. IV je také extrahovaným ze zprávy ve frontě a používat společně s rozbalenou klíčem k dešifrování dat zprávu fronty. Všimněte si, že metadata šifrování je malá (v bajtech) 500, takže když ho započítávat limit 64KB pro zprávu fronty, by mělo být dopad spravovat.

### <a name="tables"></a>Tabulky
Klientská knihovna podporuje šifrování vlastností entity pro vložení a nahrazovat operace.

> [!NOTE]
> Sloučení se aktuálně nepodporuje. Vzhledem k tomu, že podmnožinu vlastností může být šifrována dříve pomocí jiného klíče, jednoduše slučování nové vlastnosti a aktualizace metadat dojde ke ztrátě dat. Slučování buď vyžaduje volání další služby ke čtení existující entity ze služby, nebo pomocí nového klíče na vlastnosti, které nejsou vhodné z důvodů výkonu.
> 
> 

Šifrování dat tabulky funguje takto:

1. Uživatelé zadat vlastnosti k zašifrování.
2. Klientská knihovna generuje náhodných inicializace vektoru (IV) 16 bajtů společně s klíčem náhodných šifrování obsahu (CEK) 32 bajtů pro každou entitu a provádí šifrování obálky na jednotlivé vlastnosti k zašifrování odvozením nová IV za vlastnost. Zašifrované vlastnosti se ukládají jako binární data.
3. Zabalená CEK a některé další šifrování metadata jsou pak uloženy jako dva další rezervované vlastnosti. První rezervované vlastnosti (\_ClientEncryptionMetadata1) se ve vlastnosti string, který obsahuje informace o IV, verzi a zabalené klíč. Druhý rezervované vlastnosti (\_ClientEncryptionMetadata2) je binární vlastnost, která obsahuje informace o vlastnosti, které jsou zašifrované. Informace v této druhé vlastnosti (\_ClientEncryptionMetadata2) je sám zašifrovaná.
4. Z důvodu tyto další rezervované vlastnosti vyžadované pro šifrování uživatelé nyní mohou mít pouze 250 vlastní vlastnosti místo 252. Celková velikost entity, musí být menší než 1MB.
   
   Všimněte si, že pouze vlastnosti řetězce mohou být šifrována. Pokud jsou i další typy vlastností k šifrování, musí být převeden na řetězce. Šifrované řetězce jsou uložené ve službě jako binární vlastnosti a převedené zpět do řetězce (nezpracovaná řetězce, není EntityProperties s typem EdmType.STRING) po dešifrování.
   
   Pro tabulky, kromě zásady šifrování musí uživatelé zadat vlastnosti k zašifrování. To lze provést buď ukládání těchto vlastností v objektech TableEntity je typ nastaven na EdmType.STRING a šifrování nastaven na hodnotu true nebo nastavením encryption_resolver_function tableservice objektu. Překladač šifrování je funkce, která přebírá klíč oddílu, klíč řádku a název vlastnosti a vrátí logickou hodnotu, která určuje, jestli by se šifrovat tuto vlastnost. Během šifrování se klientské knihovny použije tyto informace se rozhodnout, jestli by se vlastnost šifrovat při zápisu do sítě. Delegát taky poskytuje možnost logiku kolem jak jsou zašifrované vlastnosti. (Například pokud X, potom šifrování vlastnost A; v opačném případě šifrování vlastnosti A a B.) Všimněte si, že není nutné poskytnout tyto informace při čtení nebo dotazování entity.

### <a name="batch-operations"></a>Dávkové operace
Jedna zásada šifrování se vztahuje na všechny řádky v dávce. Klientská knihovna interně vygenerujte nový náhodný IV a náhodných CEK na řádek v dávce. Uživatelé mohou také k šifrování různé vlastnosti pro všechny operace v dávce definováním toto chování v překladač šifrování.
Pokud dávky je vytvořen jako správce kontextu prostřednictvím metody batch() tableservice, zásady šifrování tableservice bude automaticky použita pro dávku. Dávky explicitně vytvořena při volání konstruktoru, musí být předán jako parametr a vlevo ponechat beze změny po dobu jeho existence dávky zásady šifrování.
Všimněte si, že entit je jím zašifrovaná, jako jsou vloženy do dávky pomocí zásad šifrování batch (entit je jím zašifrovaná není v době potvrzení batch pomocí zásad šifrování tableservice).

### <a name="queries"></a>Dotazy
> [!NOTE]
> Protože entity, které jsou zašifrované, nelze spustit dotazy, které filtrovat v zašifrované vlastnosti.  Pokud se pokusíte, bude výsledky nesprávný, protože služba by pokusu porovnat šifrovaná data s nešifrovaným datům.
> 
>
K provedení operace dotazů, je nutné zadat klíče překladače, který se bude moct vyřešit všechny klíče v sadě výsledků dotazu. Pokud entity obsažené ve výsledku dotazu nelze přeložit na poskytovatele, knihovny klienta vyvolá chybu. Po jakémkoli dotazu, který provádí projekce straně serveru, budou klientské knihovny přidat metadata vlastnosti speciální šifrování (\_ClientEncryptionMetadata1 a \_ClientEncryptionMetadata2) ve výchozím nastavení vybrané sloupce.

> [!IMPORTANT]
> Mějte na paměti tyto důležité bodů při použití šifrování na straně klienta:
> 
> * Při čtení nebo zápisu do zašifrovaný objekt blob, pomocí příkazů nahrávání celý objekt blob a rozsah nebo celé blob stažení příkazy. Vyhněte se zápis do zašifrovaný objekt blob pomocí protokolu operací, jako je Put bloku uvést seznam blokovaných, zápis stránky stránkách nebo zrušte; jinak může dojít k poškození zašifrovaný objekt blob a nastavit jej jako nečitelná.
> * Pro tabulky podobně jako omezení existuje. Pečlivě nelze aktualizovat zašifrované vlastnosti bez aktualizace metadata šifrování.
> * Pokud jste nastavili metadata na zašifrovaný objekt blob, může přepsat metadata šifrování požadované pro dešifrování, protože není sčítání nastavení metadat. To platí také pro snímky; Vyhněte se zadání metadat při vytváření snímku zašifrovaný objekt blob. Pokud musí být nastavená metadata, je nutné volat **get_blob_metadata** metodu nejdřív získat aktuální metadata šifrování a zabránit souběžných zápisy při nastavování metadat.
> * Povolit **require_encryption** příznak na objekt služby pro uživatele, kteří by měla fungovat jenom s šifrovaná data. Další informace najdete níže.
> 
> 

Klientská knihovna pro úložiště očekává, že zadaný KEK a klíče překladač implementovat následující rozhraní. [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) podporu pro správu Python KEK čeká na vyřízení a integrují do této knihovny po dokončení.

## <a name="client-api--interface"></a>Klientské rozhraní API / rozhraní
Po vytvoření objektu služby úložiště (tj. blockblobservice), uživatel může přiřadit hodnoty na pole, které tvoří zásady šifrování: key_encryption_key, key_resolver_function a require_encryption. Uživatelé zadat pouze KEK pouze překladač nebo obojí. key_encryption_key je základní typ klíče, který je identifikován pomocí identifikátoru klíče, který poskytuje logiku pro zabalení/rozbalování. key_resolver_function se používá k překladu během procesu dešifrovací klíč. Vrátí platný KEK zadaný identifikátor klíče. To poskytuje uživatelům možnost zvolit mezi více klíčů, které jsou spravovány v několika umístěních.

Klíče KEK musí implementovat následující metody, které úspěšně šifrování dat:

* wrap_key(cek): zabalí zadanou CEK (bajty) pomocí algoritmu podle volby uživatele. Vrátí zabalené klíč.
* get_key_wrap_algorithm(): vrátí algoritmus slouží k zabalení klíče.
* get_kid(): vrátí řetězec id klíče pro tento KEK.
  Klíče KEK musí implementovat následující metody, které úspěšně dešifrovat data:
* unwrap_key (cek, algoritmus): vrátí rozbalenou formu zadaný CEK pomocí algoritmu zadaný řetězec.
* get_kid(): vrací řetězec id klíče pro tento KEK.

Překladač klíče alespoň musí implementovat metodu, která zadané id klíče, vrátí odpovídající KEK implementace rozhraní výše. Jenom tato metoda je přiřazen key_resolver_function vlastnost v objektu služby.

* Pro šifrování používá se vždy a chybí klíč bude mít za následek chybu.
* K dešifrování:
  
  * Překladač klíče je volána, pokud zadaný k získání klíče. Pokud je zadaný překladač, ale nemá mapování pro identifikátor klíče, je vyvolána k chybě.
  * Pokud není zadaný překladač, ale je určen klíč, klíč se používá, pokud jeho identifikátoru odpovídá identifikátoru požadované klíče. Pokud identifikátor neodpovídá, je vyvolána k chybě.
    
    Ukázky šifrování v azure.storage.samples <fix URL>předvádí podrobnější scénář začátku do konce pro objekty BLOB, fronty a tabulky.
      Ukázka implementace KEK a klíče překladač jsou uvedeny v ukázkové soubory jako KeyWrapper a KeyResolver v uvedeném pořadí.

### <a name="requireencryption-mode"></a>Režim RequireEncryption
Uživatelé mohou volitelně povolit režim operace, kde musí být všechny nahrávání a stahování zašifrována. V tomto režimu se nezdaří pokusy o odeslání dat bez zásady šifrování nebo data, která nejsou šifrována ve službě Stažení na straně klienta. **Require_encryption** příznak objektu Služba řídí toto chování.

### <a name="blob-service-encryption"></a>Šifrování služby objektů BLOB
Nastavte šifrování polí zásad u objektu blockblobservice. Všem ostatním bude zpracovávat klientské knihovny interně.

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

### <a name="queue-service-encryption"></a>Šifrování služby fronty
Nastavte šifrování polí zásad u queueservice objektu. Všem ostatním bude zpracovávat klientské knihovny interně.

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
Kromě vytváření zásad šifrování a jeho nastavení na žádost o možnostech, musíte buď určit **encryption_resolver_function** na **tableservice**, nebo nastavte atribut šifrování EntityProperty.

### <a name="using-the-resolver"></a>Pomocí překladače

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
Jak je uvedeno nahoře, mohou být vlastnost označeny pro šifrování ukládání v objektu EntityProperty a nastavení šifrování pole.

```python
encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)
```

## <a name="encryption-and-performance"></a>Šifrování a výkonu
Všimněte si, že šifrování dat výsledky úložiště v dalších zatížení. Musí být generovány klíč obsahu a IV, musí být šifrovaný samotný obsah a další metadata musí být naformátovaná a nahrát. Tato dodatečná režie se bude lišit v závislosti na objemu dat šifrovaný. Doporučujeme vám, že zákazníci vždy testování aplikací pro výkon při vývoji.

## <a name="next-steps"></a>Další postup
* Stažení [Klientská knihovna pro úložiště Azure pro úložiště PyPi Java balíček](https://pypi.python.org/pypi/azure-storage)
* Stažení [Klientská knihovna pro úložiště Azure pro jazyk Python zdrojový kód z Githubu](https://github.com/Azure/azure-storage-python)
