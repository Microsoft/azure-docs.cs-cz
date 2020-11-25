---
title: Šifrování na straně klienta pomocí Pythonu
titleSuffix: Azure Storage
description: Klientská knihovna Azure Storage pro Python podporuje šifrování na straně klienta pro zajištění maximálního zabezpečení pro vaše Azure Storage aplikace.
services: storage
author: tamram
ms.service: storage
ms.devlang: python
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 511166e156591562b2120b58cc420f3fccd1d8c4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008922"
---
# <a name="client-side-encryption-with-python"></a>Šifrování na straně klienta pomocí Pythonu

[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Přehled
[Klientská knihovna Azure Storage pro Python](https://pypi.python.org/pypi/azure-storage) podporuje šifrování dat v rámci klientských aplikací před odesláním do Azure Storage a dešifrování dat při stahování do klienta.

> [!NOTE]
> Knihovna Pythonu Azure Storage je ve verzi Preview.
> 
> 

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Šifrování a dešifrování prostřednictvím techniky obálek
Procesy šifrování a dešifrování se řídí způsobem obálky.

### <a name="encryption-via-the-envelope-technique"></a>Šifrování prostřednictvím techniky obálek
Šifrování prostřednictvím techniky obálek funguje následujícím způsobem:

1. Klientská knihovna pro úložiště Azure vygeneruje šifrovací klíč obsahu (CEK), což je symetrický klíč založený na jednorázovém použití.
2. Uživatelská data se šifrují pomocí tohoto CEK.
3. CEK se pak zabalí (zašifruje) pomocí klíčového šifrovacího klíče (KEK). KEK je identifikován identifikátorem klíče a může se jednat o asymetrickou dvojici klíčů nebo symetrický klíč, který je spravován místně.
   Klientská knihovna pro úložiště nemá nikdy přístup k KEK. Knihovna vyvolá algoritmus pro zabalení klíče, který je k dispozici v KEK. V případě potřeby mohou uživatelé používat vlastní poskytovatele pro zalamování a rozbalení klíče.
4. Šifrovaná data se pak nahrají do služby Azure Storage. Zabalené klíče spolu s dalšími metadaty šifrování se ukládají jako metadata (v objektu BLOB) nebo interpolovaná pomocí šifrovaných dat (zprávy fronty a entity tabulky).

### <a name="decryption-via-the-envelope-technique"></a>Dešifrování prostřednictvím techniky obálek
Dešifrování prostřednictvím techniky obálek funguje následujícím způsobem:

1. Klientská knihovna předpokládá, že uživatel spravuje klíč šifrovacího klíče (KEK) místně. Uživatel nemusí znát konkrétní klíč, který se použil pro šifrování. Místo toho je možné nastavit a použít překladač klíčů, který překládá různé identifikátory klíčů na klíče.
2. Klientská knihovna stáhne zašifrovaná data spolu s jakýmkoli šifrovacím materiálem uloženým ve službě.
3. Nezabalený šifrovací klíč obsahu (CEK) se pak nebalí (dešifruje) pomocí klíčového šifrovacího klíče (KEK). V takovém případě knihovna klienta nemá přístup k KEK. Jednoduše vyvolá algoritmus rozbalení vlastního poskytovatele.
4. Šifrovací klíč obsahu (CEK) se pak použije k dešifrování šifrovaných uživatelských dat.

## <a name="encryption-mechanism"></a>Šifrovací mechanismus
Klientská knihovna pro úložiště používá [algoritmus AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) , aby se šifroval data uživatelů. Konkrétně režim [řetězení bloků šifry (CBC)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) s AES. Každá služba funguje trochu jinak, takže se na ně podíváme každý z nich.

### <a name="blobs"></a>Objekty blob
Klientská knihovna aktuálně podporuje pouze šifrování celých objektů BLOB. Šifrování je konkrétně podporováno, pokud uživatelé používají metody **Create** _. Pro soubory ke stažení jsou podporované jak stahování dokončeno, tak i rozsah, ale paralelní využívání nahrávání i stahování je dostupné.

Při šifrování vygeneruje Klientská knihovna náhodný vektor inicializace (IV) o 16 bajtech, společně s náhodným šifrovacím klíčem obsahu (CEK) 32 bajtů a provede šifrování obálky dat objektů BLOB pomocí těchto informací. Zabalené CEK a některá další šifrovací metadata se pak ukládají jako metadata objektů BLOB společně s šifrovaným objektem BLOB ve službě.

> [!WARNING]
> Pokud upravujete nebo ukládáte vlastní metadata pro objekt blob, musíte zajistit, aby byla tato metadata zachovaná. Pokud nahrajete nová metadata bez těchto metadat, zabalené CEK, IV a další metadata budou ztraceny a obsah objektu BLOB nebude nikdy možné znovu načíst.
> 
> 

Stažení zašifrovaného objektu BLOB zahrnuje načtení obsahu celého objektu BLOB pomocí metod _*Get* *_ pohodlí. Zabalená CEK se nebalí a používá společně s IV (uloženými jako metadata objektů BLOB v tomto případě) k vrácení dešifrovaných dat uživatelům.

Stažení libovolného rozsahu (metody _*Get* *_ s předanými parametry rozsahu) v zašifrovaném objektu BLOB zahrnuje úpravu rozsahu poskytnutého uživateli, aby bylo možné získat malé množství dalších dat, která lze použít k úspěšnému dešifrování požadovaného rozsahu.

Objekty blob bloku a objekty blob stránky je možné šifrovat nebo dešifrovat pomocí tohoto schématu. V tuto chvíli není k dispozici žádná podpora pro šifrování doplňovacích objektů BLOB.

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
3. Zabalené CEK a některá další šifrovací metadata se pak uloží jako dvě další rezervované vlastnosti. První vyhrazená vlastnost ( \_ ClientEncryptionMetadata1) je řetězcová vlastnost, která obsahuje informace o IV, verzi a zabaleném klíči. Druhá rezervovaná vlastnost ( \_ ClientEncryptionMetadata2) je binární vlastnost, která obsahuje informace o vlastnostech, které jsou zašifrovány. Informace v této druhé vlastnosti ( \_ ClientEncryptionMetadata2) jsou zašifrované.
4. Vzhledem k těmto dalším rezervovaným vlastnostem vyžadovaným pro šifrování mohou uživatelé nyní mít pouze 250 vlastních vlastností místo 252. Celková velikost entity musí být menší než 1 MB.

   Všimněte si, že lze šifrovat pouze vlastnosti řetězce. Pokud mají být zašifrovány jiné typy vlastností, je nutné je převést na řetězce. Šifrované řetězce jsou uloženy ve službě jako binární vlastnosti a jsou převedeny zpět na řetězce (nezpracované řetězce, nikoli EntityProperties s typem EdmType. STRING) po dešifrování.

   U tabulek (kromě zásad šifrování) musí uživatelé zadat vlastnosti, které mají být zašifrované. To lze provést buď uložením těchto vlastností v objektech TableEntity s typem nastaveným na EdmType. STRING a Encrypted na hodnotu true, nebo nastavením encryption_resolver_function na objektu tableservice. Překladač šifrování je funkce, která přebírá klíč oddílu, klíč řádku a název vlastnosti a vrací logickou hodnotu, která označuje, zda má být tato vlastnost zašifrována. Při šifrování bude knihovna klienta tyto informace používat k rozhodnutí, jestli má být při zápisu do tohoto drátu zašifrovaná vlastnost. Delegát také poskytuje možnost logiky kolem způsobu šifrování vlastností. (Například pokud X, pak Šifrujte vlastnost A; jinak Zašifrujte vlastnosti a a B.) Všimněte si, že při čtení nebo dotazování entit není nutné tyto informace zadávat.

### <a name="batch-operations"></a>Dávkové operace
Jedna zásada šifrování se vztahuje na všechny řádky v dávce. Knihovna klienta interně vygeneruje nový náhodný a náhodný CEK na řádek v dávce. Uživatelé také mohou zvolit šifrování různých vlastností každé operace v dávce definováním tohoto chování v překladači šifrování.
Pokud je dávka vytvořená jako správce kontextu prostřednictvím metody tableservice Batch (), zásada šifrování tableservice se automaticky použije pro dávku. Pokud je dávka vytvořena explicitně voláním konstruktoru, musí být zásady šifrování předány jako parametr a v případě životnosti dávky ponechány beze změny.
Všimněte si, že entity se šifrují, protože jsou vložené do dávky pomocí zásad šifrování této dávky (entity nejsou v době potvrzení dávky pomocí zásad šifrování tableservice šifrované.)

### <a name="queries"></a>Dotazy
> [!NOTE]
> Vzhledem k tomu, že jsou entity zašifrované, nemůžete spouštět dotazy, které filtrují na zašifrovanou vlastnost.  Pokud se pokusíte, výsledky budou nesprávné, protože se služba snaží porovnat zašifrovaná data s nezašifrovanými daty.
> 
> 
> Chcete-li provést operace s dotazem, je nutné zadat překladač klíčů, který dokáže vyřešit všechny klíče v sadě výsledků dotazu. Pokud entitu obsaženou ve výsledku dotazu nelze přeložit na zprostředkovatele, bude vyvolána chyba klientské knihovny. Pro všechny dotazy, které provádějí projekce na straně serveru, knihovna klienta ve výchozím nastavení přidá do vybraných sloupců speciální vlastnosti šifrovacích metadat ( \_ ClientEncryptionMetadata1 a \_ ClientEncryptionMetadata2).
> 
> [!IMPORTANT]
> Pamatujte na tyto důležité body při použití šifrování na straně klienta:
> 
> _ Při čtení nebo zápisu do šifrovaného objektu BLOB použijte úplné příkazy pro nahrání objektů BLOB a rozsah nebo celé objekty pro stažení objektů BLOB. Vyhněte se zápisu do šifrovaného objektu BLOB pomocí operací protokolu, jako je blok vložení, seznam blokovaných objektů, zápis stránek nebo vymazat stránky. v opačném případě může dojít k poškození šifrovaného objektu BLOB a zpřístupnění ho nečitelným.
> * V případě tabulek existuje podobné omezení. Nezapomeňte neaktualizovat šifrované vlastnosti bez aktualizace metadat šifrování.
> * Pokud nastavíte metadata pro zašifrovaný objekt blob, můžete přepsat metadata týkající se šifrování, která jsou nutná k dešifrování, protože nastavení metadat není aditivní. To platí také pro snímky; Vyhněte se zadávání metadat při vytváření snímku šifrovaného objektu BLOB. Pokud musí být nastavena metadata, nezapomeňte nejprve zavolat metodu **get_blob_metadata** a získat aktuální šifrovací metadata a vyhnout se souběžným zápisům při nastavování metadat.
> * Pro uživatele, kteří by měli pracovat pouze s šifrovanými daty, povolte příznak **require_encryption** u objektu služby. Další informace najdete níže.

Klientská knihovna pro úložiště očekává, že poskytnuté KEK a překladač klíčů implementují následující rozhraní. Podpora [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) správy Python KEK čeká na vyřízení a po dokončení bude integrovaná do této knihovny.

## <a name="client-api--interface"></a>Rozhraní API klienta/rozhraní
Po vytvoření objektu služby úložiště (tj. blockblobservice) může uživatel přiřadit hodnoty k polím, která tvoří zásady šifrování: key_encryption_key, key_resolver_function a require_encryption. Uživatelé můžou poskytovat jenom KEK, jenom překladač nebo obojí. key_encryption_key je základní typ klíče, který je identifikován pomocí identifikátoru klíče a poskytuje logiku pro zabalení nebo rozbalení. key_resolver_function slouží k překladu klíče během dešifrovacího procesu. Vrátí platný KEK s daným identifikátorem klíče. To umožňuje uživatelům volit mezi několika klíči, které jsou spravovány ve více umístěních.

KEK musí implementovat následující metody pro úspěšné šifrování dat:

* wrap_key (cek): zabalí zadané CEK (bajty) pomocí algoritmu výběru uživatele. Vrátí zabaleného klíče.
* get_key_wrap_algorithm (): vrátí algoritmus použitý k zabalení klíčů.
* get_kid (): vrátí ID řetězcového klíče pro tento KEK.
  KEK musí implementovat následující metody pro úspěšné dešifrování dat:
* unwrap_key (cek, Algorithm): vrátí nezabalenou formu zadaného CEK pomocí algoritmu zadaného řetězce.
* get_kid (): vrátí ID řetězcového klíče pro tento KEK.

Překladač klíčů musí přinejmenším implementovat metodu, která za ID klíče vrátí odpovídající KEK implementující rozhraní výše. Vlastnost key_resolver_function objektu služby se přiřadí pouze této metodě.

* Pro šifrování se klíč použije vždycky a absence klíče bude mít za následek chybu.
* Pro dešifrování:

  * Překladač klíčů je vyvolán, pokud je zadán pro získání klíče. Pokud je překladač zadán, ale nemá mapování pro identifikátor klíče, je vyvolána chyba.
  * Pokud není překladač zadán, ale je zadán klíč, použije se klíč, pokud jeho identifikátor odpovídá požadovanému identifikátoru klíče. Pokud identifikátor neodpovídá, je vyvolána chyba.

    Ukázky šifrování v Azure. Storage. Samples ukazují podrobnější scénář pro objekty blob, fronty a tabulky, které jsou podrobněji kompletní.
      Ukázkové implementace KEK a překladače klíčů jsou k dispozici v ukázkových souborech jako základní obálku a v uvedeném pořadí.

### <a name="requireencryption-mode"></a>RequireEncryption režim
Uživatelé mohou volitelně povolit režim operace, kde všechna nahraná a stažená soubory musí být zašifrovaná. V tomto režimu se pokusy o nahrání dat bez zásad šifrování nebo stažení dat, která nejsou ve službě zašifrovaná, selžou na klientovi. Toto chování řídí příznak **require_encryption** na objektu služby.

### <a name="blob-service-encryption"></a>Blob service šifrování
Nastavte pole zásad šifrování v objektu blockblobservice. Všechny ostatní budou zpracovávány v interní knihovně klienta.

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

### <a name="queue-service-encryption"></a>Služba front šifrování
Nastavte pole zásad šifrování v objektu QueueService. Všechny ostatní budou zpracovávány v interní knihovně klienta.

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

### <a name="table-service-encryption"></a>Table service šifrování
Kromě vytvoření zásad šifrování a jejich nastavení v možnostech žádosti musíte buď zadat **encryption_resolver_function** na **tableservice**, nebo nastavit atribut Encrypt na EntityProperty.

### <a name="using-the-resolver"></a>Použití překladače

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
Jak bylo uvedeno výše, vlastnost může být označena pro šifrování tím, že ji uloží do objektu EntityProperty a nastaví pole šifrovat.

```python
encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)
```

## <a name="encryption-and-performance"></a>Šifrování a výkon
Všimněte si, že šifrování dat úložiště má za následek zvýšené nároky na výkon. Klíč obsahu a IV se musí vygenerovat, samotný obsah musí být zašifrovaný a musí se naformátovat a nahrát další metadata. Tato režie se bude lišit v závislosti na množství šifrovaných dat. Zákazníkům doporučujeme, aby při vývoji vždy otestovali své aplikace na výkon.

## <a name="next-steps"></a>Další kroky
* Azure Storage stažení [balíčku klientské knihovny pro PyPI Java](https://pypi.python.org/pypi/azure-storage)
* Stažení [klientské knihovny Azure Storage pro zdrojový kód Pythonu z GitHubu](https://github.com/Azure/azure-storage-python)
