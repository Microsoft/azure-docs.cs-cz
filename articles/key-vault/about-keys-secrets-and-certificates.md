---
title: Informace o klíčích, tajných klíčů a certifikátů
description: Přehled rozhraní REST a KV vývojáře podrobnosti
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: abd1b743-1d58-413f-afc1-d08ebf93828a
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: 2c7dd89d9c2e5d50f2533101499a6e50e52047b3
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="about-keys-secrets-and-certificates"></a>Informace o klíčích, tajných klíčů a certifikátů
Azure Key Vault umožňuje uživatelům ukládat a používat kryptografické klíče v rámci prostředí Microsoft Azure. Key Vault podporuje více typy klíčů a algoritmy a umožňuje použití z modulů hardwarového zabezpečení (HSM) pro vysoká hodnota klíče. Kromě toho Key Vault umožňuje uživatelům bezpečně uložit tajných klíčů. Tajné klíče jsou omezené velikost octet objekty s žádné konkrétní sémantiku. Key Vault podporuje také certifikáty, které jsou vytvořeny na základě klíčů a tajných klíčů a přidejte funkci automatického obnovení.

Další obecné informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](/azure/key-vault/key-vault-whatis)

**Obecné podrobnosti klíče trezoru**

-   [Podpora standardů](about-keys-secrets-and-certificates.md#BKMK_Standards)
-   [Datové typy](about-keys-secrets-and-certificates.md#BKMK_DataTypes)  
-   [Objekty, identifikátory a správa verzí](about-keys-secrets-and-certificates.md#BKMK_ObjId)  

**O klíče**

-   [Klíče a typy klíčů](about-keys-secrets-and-certificates.md#BKMK_KeyTypes)  
-   [Algoritmy RSA](about-keys-secrets-and-certificates.md#BKMK_RSAAlgorithms)  
-   [Algoritmy RSA modulu hardwarového zabezpečení](about-keys-secrets-and-certificates.md#BKMK_RSA-HSMAlgorithms)  
-   [Kryptografické ochrany](about-keys-secrets-and-certificates.md#BKMK_Cryptographic)
-   [Operace s klíčem](about-keys-secrets-and-certificates.md#BKMK_KeyOperations)  
-   [Atributy klíče](about-keys-secrets-and-certificates.md#BKMK_KeyAttributes)  
-   [Klíč značky](about-keys-secrets-and-certificates.md#BKMK_Keytags)  

**O tajných klíčů** 

-   [Práci s tajnými klíči](about-keys-secrets-and-certificates.md#BKMK_WorkingWithSecrets)  
-   [Tajný atributy](about-keys-secrets-and-certificates.md#BKMK_SecretAttrs)  
-   [Tajný značky](about-keys-secrets-and-certificates.md#BKMK_SecretTags)  
-   [Řízení přístupu tajné](about-keys-secrets-and-certificates.md#BKMK_SecretAccessControl)  

**O certifikátech**

-   [Složení certifikátu](#BKMK_CompositionOfCertificate)  
-   [Certifikát atributy a značky](#BKMK_CertificateAttributesAndTags)  
-   [Zásady certifikátu](#BKMK_CertificatePolicy)  
-   [Vystavitel certifikátu](#BKMK_CertificateIssuer)  
-   [Certifikát kontaktů](#BKMK_CertificateContacts)  
-   [Řízení přístupu certifikátu](#BKMK_CertificateAccessControl)  

--

## <a name="key-vault-general-details"></a>Obecné podrobnosti klíče trezoru

Následující části nabízí obecné informace o příslušných napříč implementace služby Azure Key Vault.

###  <a name="BKMK_Standards"></a> Podpora standardů

JavaScript Object Notation (JSON) a specifikace JavaScript objekt podepisování a šifrování (JOSE) jsou důležité základní informace.  

-   [Klíč webového JSON (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [Šifrování webové JSON (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [Algoritmy webové JSON (DŽWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [Podpis webové JSON (JWS)](http://tools.ietf.org/html/draft-ietf-jose-json-web-signature)  

### <a name="BKMK_DataTypes"></a> Datové typy

Odkazovat [JOSE specifikace](#BKMK_Standards) pro příslušné datové typy pro klíče, šifrování a podepisování.  

-   **algoritmus** -podporované algoritmus klíče operace, například RSA1_5  
-   **Hodnota ciphertext** -šifer oktety text, kódované pomocí Base64URL  
-   **ověřování algoritmem Digest hodnota** – výstup algoritmus hash, kódované pomocí Base64URL  
-   **Typ klíče** – jeden z podporovaných typů klíčů, třeba RSA.  
-   **Hodnota ve formátu prostého textu** -oktety ve formátu prostého textu, kódované pomocí Base64URL  
-   **Hodnota podpis** – výstupní algoritmu podpisu a kódované pomocí Base64URL  
-   **base64URL** -Base64URL [RFC4648] kódovaný binární hodnoty  
-   **Logická hodnota** – true nebo false  
-   **Identity** – identit z Azure Active Directory (AAD).  
-   **IntDate** – JSON desítkovou hodnotu představující počet sekund od pod hodnotou 1970-01-01T0:0:0Z UTC dokud zadané datum a čas UTC. Podrobnosti týkající se data naleznete v tématu [RFC3339] / times obecně a UTC v konkrétní.  

###  <a name="BKMK_ObjId"></a> Objekty, identifikátory a správa verzí

Objekty uložené v Azure Key Vault zachovat verze vždy, když je vytvořena nová instance objektu, a každá verze má jedinečný identifikátor a adresy URL. Při prvním vytvoření objektu je uveden verze jedinečný identifikátor a je označen jako aktuální verze objektu. Vytvoření nové instance se stejným názvem objektu poskytuje nový objekt verze jedinečný identifikátor a způsobí, že aby se aktuální verze.  

Objekty v Azure Key Vault se dají řešit pomocí aktuální identifikátor nebo identifikátor specifické pro verzi. Například přiřazen klíč s názvem "MasterKey", provádění operací s aktuální identifikátor způsobí, že systém použít na nejnovější dostupnou verzi. Provádění operací s identifikátorem specifické pro verzi způsobí, že systém pro použití této konkrétní verze objektu.  

Objekty jsou jednoznačně identifikuje v rámci Azure Key Vault pomocí adresy URL, tak, aby žádné dva objekty v systému, bez ohledu na to geografického umístění, mají stejnou adresu URL. Úplnou adresu URL k objektu, se nazývá identifikátor objektu a skládá se z část předpona, kterou identifikuje Key Vault, typ objektu, uživatel zadaný název objektu a verze objektu. Název objektu je velká a malá písmena a neměnné. Identifikátory, které neobsahují verze objektu se označují jako základní identifikátory.  

Další informace najdete v tématu [ověřování, požadavky a odpovědi](authentication-requests-and-responses.md)

Identifikátor objektu má následující obecné formát:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Kde:  

|||  
|-|-|  
|`keyvault-name`|Název trezoru klíčů ve službě Microsoft Azure Key Vault.<br /><br /> Key Vault názvy jsou vybrané uživatelem a jsou globálně jedinečný.<br /><br /> Název trezoru klíč musí být řetězec 3 až 24 znaků v délka obsahující pouze (0-9, a – z, A-Z a -).|  
|`object-type`|Typ objektu, klíčů."nebo"tajemství".|  
|`object-name`|`object-name` Je zadané uživatelské jméno a musí být jedinečný v rámci Key Vault. Název musí být řetězec 1-127 znaků v obsahující pouze 0 – 9,-z, A-Z a -.|  
|`object-version`|`object-version` Je vygenerované systémem, identifikátor řetězce 32 znaků, který je volitelně používat k adresování jedinečný verze objektu.|  

## <a name="key-vault-keys"></a>Klíč trezoru klíčů

###  <a name="BKMK_KeyTypes"></a> Klíče a typy klíčů

Kryptografické klíče v Azure Key Vault, jsou reprezentovány jako objekty JSON webové klíč [JWK]. Základní specifikace JWK/DŽWA jsou také rozšířené povolit typy klíčů jedinečné pro implementaci Azure Key Vault, například import klíče do Azure Key Vault pomocí modulu hardwarového zabezpečení balení konkrétní dodavatele (Thales) Chcete-li povolit zabezpečené Transport modulů klíčů tak, že je může použít pouze v modulech HSM Azure Key Vault.  

Počáteční verze Azure Key Vault podporuje klíče RSA pouze; budoucích verzích může podporovat další typy klíčů jako jsou symetrické a eliptické křivky.  

-   **RSA**: klíč RSA 2048 bitů. Toto je "soft" klíč, který zpracovává v softwaru Key Vault, ale je uložený v zašifrované podobě pomocí klíče systému, který je v modulu hardwarového zabezpečení. Klienti mohou importovat existující klíč RSA nebo požádat o Azure Key Vault generovat jednu.  
-   **RSA HSM**: RSA klíč, který je zpracování v modulu hardwarového zabezpečení. Klíče RSA modulu hardwarového zabezpečení jsou chráněné v jedné z zabezpečení světů Azure Key Vault modulu hardwarového zabezpečení (je architektury Security World za geography udržovat izolace). Klienti mohou importovat klíč RSA, buď v podobě logicky ani export z kompatibilní zařízení modulu hardwarového zabezpečení, nebo požádat o Azure Key Vault generovat jednu. Tento typ klíče přidá atribut T JWK získat k provedení materiál klíče HSM.  

     Další informace o zeměpisné hranice, najdete v části [Microsoft Azure Trust Center](https://azure.microsoft.com/en-us/support/trust-center/privacy/)  

###  <a name="BKMK_RSAAlgorithms"></a> Algoritmy RSA  
 Jsou podporovány následující identifikátory algoritmus klíče RSA v Azure Key Vault.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY ŠIFROVÁNÍ/DEŠIFROVÁNÍ

-   **RSA1_5** -šifrování pomocí klíče RSAES V1_5 PKCS1 [RFC3447]  
-   **RSA OAEP** – RSAES pomocí výchozí parametry určeného. 3447 RFC v části podle A.2.1 optimální asymetrické šifrování odsazení (OAEP) [RFC3447]. Tyto výchozí parametry jsou pomocí funkce hash SHA-1 a funkce generování maska MGF1 SHA-1.  

#### <a name="signverify"></a>/ OVĚŘENÍ

-   **RS256** – RSASSA-PKCS-v1_5 pomocí algoritmu SHA-256. Hodnota ověřování algoritmem digest zadané aplikace musí být vypočítány pomocí algoritmu SHA-256 a musí být délce 32 bajtů.  
-   **RS384** – RSASSA-PKCS-v1_5 pomocí algoritmu SHA-384. Hodnota ověřování algoritmem digest zadané aplikace musí být vypočítány pomocí algoritmu SHA-384 a musí být délku 48 bajtů.  
-   **RS512** – RSASSA-PKCS-v1_5 pomocí algoritmu SHA-512. Hodnota ověřování algoritmem digest zadané aplikace musí být vypočítány pomocí algoritmu SHA-512 a musí být délku 64 bajtů.  
-   **RSNULL** -najdete v tématu [RFC2437] specializované případ použití k povolení určitých scénářů TLS.  

###  <a name="BKMK_RSA-HSMAlgorithms"></a> Algoritmy RSA modulu hardwarového zabezpečení  
Jsou podporovány následující identifikátory algoritmus klíče RSA modulu hardwarového zabezpečení v Azure Key Vault.  

### <a name="BKMK_Cryptographic"></a> Kryptografické ochrany

Kryptografické moduly, které používá Azure Key Vault, zda modul hardwarového zabezpečení nebo software, jsou ověřené podle standardu FIPS. Nemusíte provádět žádné zvláštní ke spuštění v režimu FIPS. Pokud jste **vytvořit** nebo **importovat** klíče jako chráněných pomocí HSM, zaručeně v mají být zpracovány uvnitř modulů HSM ověřena k FIPS 140-2 Level 2 nebo vyšší. Pokud jste **vytvořit** nebo **importovat** klíče jako chráněný pak se zpracovávají uvnitř kryptografické moduly softwarem ověřena k FIPS 140-2 úrovně 1 nebo vyšší. Další informace najdete v tématu [klíče a typy klíčů](about-keys-secrets-and-certificates.md#BKMK_KeyTypes).

#### <a name="wrapunwrap-encryptdecrypt"></a>ROZBALENÍ/WRAP, ŠIFROVÁNÍ/DEŠIFROVÁNÍ

-   **RSA1_5** -RSAES V1_5 PKCS1 [RFC3447] šifrovací klíče.  
-   **RSA OAEP** – RSAES pomocí výchozí parametry určeného. 3447 RFC v části podle A.2.1 optimální asymetrické šifrování odsazení (OAEP) [RFC3447]. Tyto výchozí parametry jsou pomocí funkce hash SHA-1 a funkce generování maska MGF1 SHA-1.  

 #### <a name="signverify"></a>/ OVĚŘENÍ  

-   **RS256** – RSASSA-PKCS-v1_5 pomocí algoritmu SHA-256. Hodnota ověřování algoritmem digest zadané aplikace musí být vypočítány pomocí algoritmu SHA-256 a musí být délce 32 bajtů.  
-   **RS384** – RSASSA-PKCS-v1_5 pomocí algoritmu SHA-384. Hodnota ověřování algoritmem digest zadané aplikace musí být vypočítány pomocí algoritmu SHA-384 a musí být délku 48 bajtů.  
-   **RS512** – RSASSA-PKCS-v1_5 pomocí algoritmu SHA-512. Hodnota ověřování algoritmem digest zadané aplikace musí být vypočítány pomocí algoritmu SHA-512 a musí být délku 64 bajtů.  
-   RSNULL: Viz [RFC2437] specializované případ použití k povolení určitých scénářů TLS.  

###  <a name="BKMK_KeyOperations"></a> Operace s klíčem

Azure Key Vault podporuje následující operace na objekty klíče:  

-   **Vytvoření**: umožňuje klienta k vytvoření klíče v Azure Key Vault. Hodnota klíče je generované Azure Key Vault a uložená a se neuvolní klientovi. Asymetrické (a v budoucnu eliptické křivky a Symmetric) klíče může být vytvořeny v Azure Key Vault.  
-   **Import**: umožňuje klientovi importovat existující klíč do Azure Key Vault. Asymetrické (a v budoucnu eliptické křivky a Symmetric) klíče může být naimportovány do Azure Key Vault s počtem obalů různých metod v rámci JWK konstrukce.  
-   **Aktualizace**: umožňuje klientům s dostatečná oprávnění k úpravě metadaty (atributy klíče) přidružené klíče dříve uloženého v rámci Azure Key Vault.  
-   **Odstranit**: umožňuje klientům s dostatečnými oprávněními k odstranění klíče z Azure Key Vault.  
-   **Seznam**: umožňuje klientovi seznam všech klíčů v dané Azure Key Vault.  
-   **Seznam verzí**: umožňuje klientovi seznam všech verzí k danému klíči v dané Azure Key Vault.  
-   **Získat**: umožňuje klientům pro načtení veřejné části k danému klíči v Azure Key Vault.  
-   **Zálohování**: Exportuje klíč v chráněném formuláře.  
-   **Obnovení**: importovat dříve zálohovaná klíč.  

Další informace najdete v tématu [klíče operací](/rest/api/keyvault/key-operations.md)  

Po vytvoření klíče v Azure Key Vault, následující kryptografické operace může provést pomocí klíče:  

-   **Přihlášení a ověřte, zda**: výhradně, tato operace je "hash přihlášení" nebo "ověřit", protože Azure Key Vault nepodporují algoritmu hash obsahu v rámci vytváření podpis. Aplikace by měla hash data, která mají být podepsané místně a pak si mohou vyžádat označení Azure Key Vault hodnotu hash. Ověření hodnoty hash podepsaný držitelem je podporovaný jako pohodlí operace pro aplikace, které nemá přístup k [veřejné] materiál klíče; Myslíme, doporučujeme pro nejlepší výkon aplikací, ověřte, že operace se provádějí místně.  
-   **Šifrovací klíč / zabalení**: klíče uložené v Azure Key Vault může použít k ochraně jiný klíč, obvykle symetrického obsahu šifrovacího klíče (CEK). Po asymetrického klíče v Azure Key Vault se používá šifrování pomocí klíče, třeba RSA OAEP a činnosti WRAPKEY/UNWRAPKEY odpovídají šifrování/DEŠIFROVÁNÍ. Po symetrického klíče v Azure Key Vault se používá klíč zabalení; například AES KW. Operace WRAPKEY je podporována pro potřeby pro aplikace, které nemá přístup k [veřejné] materiál klíče; doporučuje se, že pro nejlepší výkon aplikace WRAPKEY operací místně.  
-   **Šifrování a dešifrování**: klíče uložené v Azure Key Vault může použít k zašifrování a dešifrování dat, velikost, která je určena typ klíče a vybrané šifrovací algoritmus jeden blok. Operace šifrování se poskytuje pro usnadnění práce pro aplikace, které nemá přístup k [veřejné] materiál klíče; Dále je doporučeno, pro nejlepší výkon aplikace, šifrují operace provádět místně.  

Při WRAPKEY/UNWRAPKEY pomocí asymetrických klíčů se může zdát nadbytečné jako operaci je ekvivalentní k šifrování/DEŠIFROVÁNÍ, použití různých operací, je důležité zajistit sémantického a autorizace oddělení těchto operací a konzistence Když jiné typy klíčů jsou podporovány službou.  

Azure Key Vault nepodporuje operace EXPORTU: po zřízení klíč v systému není možné extrahovat nebo změnit jeho materiál klíče.  Uživatelé Azure Key Vault, kdo může vyžadovat jejich klíč pro ostatní však případy použití nebo po byl odstraněn v Azure Key Vault, může použít operace zálohování a obnovení pro export a import klíče v chráněném formuláře. Vytvořená operací zálohování klíče nejsou použitelné mimo Azure Key Vault. Operace IMPORTU může případně použít u více instancí Azure Key Vault.  

Uživatelé mohou omezit všechny kryptografické operace, které podporuje Azure Key Vault pro každý klíč pomocí vlastnost key_ops JWK objektu.  

Další informace o JWK objektů najdete v tématu [JSON webové klíč (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key).  

###  <a name="BKMK_KeyAttributes"></a> Atributy klíče

Kromě materiál klíče je možné zadat následující atributy. V požadavku na JSON, atributy – klíčové slovo a složené závorky ' {' '}', jsou požadovány, i když nejsou zadané žádné atributy.  

- *Povolit*: logická hodnota, volitelné, výchozí hodnota je **true**. Určuje, zda klíč povolené a funkční pro kryptografické operace. *Povoleno* atribut se používá ve spojení s *nbf* a *exp*. Když probíhá operace mezi *nbf* a *exp*, se bude pouze povoleno v případě *povoleno* je nastaven na **true**. Operace mimo *nbf* / *exp* okno automaticky jsou zakázané, s výjimkou určité typy operací v části [konkrétní podmínky](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops).
- *NBF*: IntDate, volitelné, výchozí je nyní. *Nbf* (ne před) atribut určuje dobu, před kterou je nutno používat klíč pro kryptografické operace, s výjimkou určité typy operací v části [konkrétní podmínky](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops). Zpracování *nbf* atribut vyžaduje, aby aktuální datum a čas musí následovat po nebo není rovno-před datum a čas uvedený v *nbf* atribut. Azure Key Vault poskytovaných pro některé malé volně mohou obvykle ne víc než několik minut, aby se zohlednily hodiny zkosení. Její hodnota musí být číslo obsahující hodnotu IntDate.  
- *Exp*: IntDate, volitelné, výchozí hodnota je "navždy". *Exp* (čas vypršení platnosti) atribut určuje čas vypršení platnosti na nebo po kterém se klíč nesmí používat pro kryptografickou operací, s výjimkou určité typy operací v části [konkrétní podmínky](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops). Zpracování *exp* atribut vyžaduje, že aktuální datum a čas musí být před datum a čas vypršení platnosti uvedené v *exp* atribut. Azure Key Vault poskytovaných pro některé malé volně mohou obvykle ne víc než několik minut, aby se zohlednily hodiny zkosení. Její hodnota musí být číslo obsahující hodnotu IntDate.  

Existují další atributy jen pro čtení, které jsou součástí všechny odpovědi, která zahrnuje klíčových atributů:  

- *vytvořit*: IntDate, volitelné. *Vytvořit* atribut uvádí, kdy byla vytvořena tato verze klíče. Tato hodnota je null pro klíče vytvořené před přidání tohoto atributu. Její hodnota musí být číslo obsahující hodnotu IntDate.  
- *Aktualizovat*: IntDate, volitelné. *Aktualizovat* atribut označuje aktualizace tato verze klíče. Tato hodnota je null pro klíče, které byly poslední aktualizace před přidání tohoto atributu. Její hodnota musí být číslo obsahující hodnotu IntDate.  

Další informace o IntDate a dalších typů dat najdete v tématu [datové typy](about-keys-secrets-and-certificates.md#BKMK_DataTypes)  

#### <a name="BKMK_key-date-time-ctrld-ops"></a> Operace řízené data a času

Není dosud platný a vypršela platnost klíče, tyto mimo *nbf* / *exp* okno, bude fungovat pro **dešifrovat**, **rozbalení** a **ověřte** operací (nevrátí 403, zakázáno). Důvody pomocí není dosud platný stav je umožnit klíč má být testována před použití v provozním prostředí. Při použití vypršela platnost je umožnit operace obnovení pro data, která byla vytvořena, když klíč byl neplatný. Navíc můžete zakázat přístup ke klíči pomocí Key Vault zásad nebo aktualizací *povoleno* klíčový atribut na **false**.

Další informace o datové typy najdete [datové typy](about-keys-secrets-and-certificates.md#BKMK_DataTypes).

Další informace o další možné atributy, najdete v článku [JSON webové klíč (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key).

### <a name="BKMK_Keytags"></a> Klíč značky

Můžete zadat další metadata specifická pro aplikace v podobě značek. Azure Key Vault podporuje až 15 značek, z nichž každá může mít název 256 znaků a hodnotu 256 znaků.  

>[!Note]
>Značky jsou přečíst volající v případě, že mají *seznamu* nebo *získat* oprávnění pro tento typ objektu; klíče, tajné klíče ani certifikáty.

###  <a name="BKMK_KeyAccessControl"></a> Řízení přístupu klíče

Řízení přístupu pro klíče, které spravuje Key Vault je k dispozici na úrovni Key Vault, který slouží jako kontejner klíčů. Není zásad řízení přístupu pro klíče, se liší od zásad řízení přístupu pro tajných klíčů v trezoru stejný klíč. Uživatelé může vytvořit jeden nebo více trezorů pro uložení klíče a je potřeba udržovat odpovídající segmentace scénář a správu klíčů. Řízení přístupu pro klíče je nezávislé na řízení přístupu pro tajných klíčů.  

Tato oprávnění lze udělit, na na jednotlivé uživatele / service hlavní základ, v položce řízení přístupu klíčů v trezoru. Tato oprávnění úzce zrcadlení operace povolená na klíče objektu:  

-   *vytvoření*: vytvoření nového klíče
-   *získat*: Přečtěte si část veřejný klíč a jeho atributy
-   *seznam*: seznam klíčů nebo verzích klíče uloženého v trezoru klíčů
-   *Aktualizovat*: aktualizovat atributy pro klíč
-   *Odstranit*: odstranění klíče objektu
-   *přihlašovací*: použití hodnoty Digest klíč k podepsání
-   *Ověřte*: používá se k ověření hodnoty Digest
-   *wrapKey*: používá se k ochraně symetrického klíče
-   *unwrapKey*: zrušení ochrany pomocí klíče zabalené symetrické klíče
-   *šifrování*: používá se k ochraně libovolnou sekvenci bajtů
-   *dešifrování*: sekvenci bajtů, zrušení ochrany pomocí klíče
-   *Import*: Import klíče do trezoru klíčů
-   *Zálohování*: zálohování klíče v trezoru klíčů
-   *obnovení*: Obnovte zálohovaný klíč k trezoru klíčů
-   *všechny*: všechna oprávnění

## <a name="key-vault-secrets"></a>Tajné klíče Key Vault 

###  <a name="BKMK_WorkingWithSecrets"></a> Práci s tajnými klíči

Tajné klíče v Azure Key Vault jsou octet pořadí s maximální velikostí 25 kB každé. Služba Azure Key Vault neposkytuje žádné sémantiku pro tajné klíče; jenom přijímá data, zašifruje a uloží ji vrácení identifikátor tajný "id", který se dá použít k načtení tajný klíč později.  

Klienti pro vysoce citlivých dat, zvažte další vrstvy ochrany pro data, která je uložená v Azure Key Vault; například předem šifrování dat pomocí samostatných ochrany klíče.  

Azure Key Vault také podporuje pole contentType pro tajných klíčů. Klienti mohou zadat typ obsahu, "contentType", které vám pomohou v interpretace tajných dat při načítání tajného klíče. Maximální délka tohoto pole je 255 znaků. Nejsou k dispozici žádné předem definovaných hodnot. Navrhované využití je jako nápovědu pro překlad tajný data. Implementace může například ukládat hesla a certifikáty jako tajné klíče potom slouží k označení, které v tomto poli. Nejsou k dispozici žádné předem definovaných hodnot.  

###  <a name="BKMK_SecretAttrs"></a> Tajný atributy

Kromě tajných dat může být zadány následující atributy:  

- *Exp*: IntDate, volitelné, výchozí hodnota je **navždy**. *Exp* (čas vypršení platnosti) atribut určuje čas vypršení platnosti na nebo po které nesmí tajných dat načíst, kromě [konkrétní situace](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops). Zpracování *exp* atribut vyžaduje, že aktuální datum a čas musí být před datum a čas vypršení platnosti uvedené v *exp* atribut. Azure Key Vault poskytovaných pro některé malé volně mohou obvykle ne víc než několik minut, aby se zohlednily hodiny zkosení. Její hodnota musí být číslo obsahující hodnotu IntDate.  
- *NBF*: IntDate, volitelné, výchozí hodnota je **nyní**. *Nbf* (ne před) atribut určuje dobu, před kterou je nutné načtena tajných dat, kromě [konkrétní situace](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops). Zpracování *nbf* atribut vyžaduje, aby aktuální datum a čas musí následovat po nebo není rovno-před datum a čas uvedený v *nbf* atribut. Azure Key Vault poskytovaných pro některé malé volně mohou obvykle ne víc než několik minut, aby se zohlednily hodiny zkosení. Její hodnota musí být číslo obsahující hodnotu IntDate.  
- *Povolit*: logická hodnota, volitelné, výchozí hodnota je **true**. Tento atribut určuje, zda lze načíst tajné údaje. Povoleno atribut se používá ve spojení s a *exp* když probíhá operace mezi a exp, se bude povoleno pouze pokud je povoleno nastavena na **true**. Operace mimo *nbf* a *exp* okna jsou automaticky nepovoleném, s výjimkou v [konkrétní situace](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops).  

Existují další atributy jen pro čtení, které jsou součástí všechny odpovědi, která zahrnuje tajný atributy:  

- *vytvořit*: IntDate, volitelné. Vytvořený atribut uvádí, vytvoření této verzi tajný klíč. Tato hodnota je null pro tajné klíče vytvořené před přidání tohoto atributu. Její hodnota musí být číslo obsahující hodnotu IntDate.  
- *Aktualizovat*: IntDate, volitelné. Aktualizovaný atribut uvádí, aktualizace této verzi tajný klíč. Tato hodnota je null pro tajné klíče, které byly poslední aktualizace před přidání tohoto atributu. Její hodnota musí být číslo obsahující hodnotu IntDate.

#### <a name="BKMK_secret-date-time-ctrld-ops"></a> Operace řízené data a času

Tajný klíč **získat** operace bude fungovat pro není dosud platný a jeho platnost tajné údaje, mimo *nbf* / *exp* okno. Volání metody tajný klíč **získat** operace, není dosud platný tajný klíč, můžete použít pro účely testování. Načítání (**získat**ing) vypršela platnost tajný klíč, lze použít pro operace obnovení.

Další informace o datové typy najdete [datové typy](about-keys-secrets-and-certificates.md#BKMK_DataTypes).  

###  <a name="BKMK_SecretAccessControl"></a> Řízení přístupu tajné

Řízení přístupu pro spravované v Azure Key Vault tajné klíče je k dispozici na úrovni Key Vault, který slouží jako kontejner těchto tajných klíčů. Není zásad řízení přístupu pro tajné klíče, se liší od zásad řízení přístupu pro klíče v úložišti stejný klíč. Uživatelé může vytvořit jeden nebo více trezorů pro uložení tajných klíčů a je potřeba udržovat odpovídající segmentace scénář a správu tajných klíčů. Řízení přístupu pro tajné klíče jsou nezávislé na řízení přístupu pro klíče.  

Tato oprávnění lze použít, na základě na objekt v položce řízení přístupu tajných klíčů v trezoru a úzce zrcadlení povolené na objekt tajný operace:  

-   *nastavit*: vytvoření nové tajné klíče  
-   *získat*: čtení tajný klíč  
-   *seznam*: seznam tajných klíčů nebo verzí tajného klíče uložené v Key Vault  
-   *Odstranit*: odstranění tajného klíče  
-   *všechny*: všechna oprávnění  

Další informace o práci s tajnými klíči najdete v tématu [operace nad tajnými klíči](/rest/api/keyvault/secret-operations.md).  

###  <a name="BKMK_SecretTags"></a> Tajný značky  
Můžete zadat další metadata specifická pro aplikace v podobě značek. Azure Key Vault podporuje až 15 značek, z nichž každá může mít název 256 znaků a hodnotu 256 znaků.  

>[!Note]
>Značky jsou přečíst volající v případě, že mají *seznamu* nebo *získat* oprávnění pro tento typ objektu; klíče, tajné klíče ani certifikáty.

## <a name="key-vault-certificates"></a>Trezor klíčů certifikátů

Podpora certifikáty Key Vault poskytuje správu vašeho x509 certifikáty a následující chování:  

-   Umožňuje vlastníkovi certifikátů k vytvoření certifikátu provede procesem vytvoření trezoru klíč nebo prostřednictvím importu stávajícího certifikátu. To zahrnuje i podepsaného svým držitelem a vygeneruje certifikáty certifikační autority.
-   Umožňuje vlastníka certifikátu Key Vault pro implementaci zabezpečeného úložiště a správu X509 certifikáty bez interakce s materiál privátního klíče.  
-   Umožňuje vlastníkovi certifikátů k vytvoření zásady, která přesměruje Key Vault pro správu životního cyklu certifikátu.  
-   Umožňuje vlastníkům certifikátu zadejte kontaktní údaje pro oznámení o události životního cyklu vypršení platnosti a obnovení certifikátu.  
-   Podporuje automatické obnovení se vybrané vystavitelů - Key Vault partnera X509 certifikátu zprostředkovatelé / certifikační autority.

>[!Note]
>Bez spolupráci zprostředkovatelé/autorit jsou také povoleny, ale nebude podporovat funkci automatického obnovení.

###  <a name="BKMK_CompositionOfCertificate"></a> Složení certifikátu

Když je vytvořen certifikát Key Vault, se stejným názvem vytvoří také adresovatelné klíč a tajný klíč. Klíč Key Vault umožňuje operace s klíčem a tajný klíč Key Vault umožňuje načtení hodnotu certifikátu jako tajný klíč. Certifikát Key Vault také obsahuje veřejné x509 certifikátu metadat.  

Identifikátor a verzi certifikáty je podobná klíče a tajné klíče. Na konkrétní verzi nástroje adresovatelné klíč a tajný klíč vytvořených pomocí certifikátu verze Key Vault je k dispozici v odpovědi certifikátu Key Vault.
 
![Cetificates jsou komplexní objekty](media/azure-key-vault.png)

###  <a name="BKMK_CertificateExportableOrNonExportableKey"></a> Klíč jako exportovatelný nebo bez exportovatelný

Když je vytvořen certifikát Key Vault, se dá načíst z adresovatelné tajný klíč s privátním klíčem ve formátu PFX nebo PEM Pokud zásady používané k vytvoření certifikátu ukázalo, že je klíč jako exportovatelný. Pokud zásady používané k vytvoření certifikátu Key Vault uvedené klíč, který chcete být neexportovatelného, pak privátní klíč není součástí hodnotu, pokud načíst jako tajný klíč.  

Adresovatelné klíč stane relevantnější s neexportovatelného KV certifikáty. Operace adresovatelné KV klíče, jsou namapované z *keyusage* pole zásad KV certifikát použitý k vytvoření certifikátu KV.  

Podporuje dva typy klíče – *RSA* nebo *RSA HSM* s certifikáty. Exportovatelný je povolen pouze v RSA, nepodporuje šifrování RSA HSM.  

###  <a name="BKMK_CertificateAttributesAndTags"></a> Certifikát atributy a značky

Kromě certifikátů metadata, adresovatelné klíč a adresovatelné tajný klíč, obsahuje certifikát Key Vault také atributy a značky.  

#### <a name="attributes"></a>Atributy

Certifikát atributy jsou zrcadleny atributy adresovatelné klíč a vytvoří, když se vytvoří certifikát KV tajný klíč.  

Key Vault certifikát má následující atributy:  

-   *Povolit*: logická hodnota, volitelné, výchozí hodnota je **true**. Tento atribut lze k označení, pokud lze načíst data certifikátu jako tajný rukou nebo jako klíč. To se používá ve spojení s *nbf* a*exp*když probíhá operace mezi *nbf* a exp, se bude povoleno pouze pokud je povoleno nastavena na hodnotu true. Operace mimo *nbf* a*exp*okno automaticky nejsou povoleny.  

Existují další atributy jen pro čtení, které jsou zahrnuty v odpovědi:

-   *vytvořit*: IntDate: označuje vytvoření této verze certifikátu.  
-   *Aktualizovat*: IntDate: Určuje aktualizace této verze certifikátu.  
-   *Exp*: IntDate: obsahuje hodnotu x509 datum vypršení platnosti certifikátu.  
-   *NBF*: IntDate: obsahuje hodnotu data x509 certifikátu.  

> [!Note] 
> Pokud vyprší platnost certifikátu Key Vault, je adresovatelný klíč a tajný klíč dojít k vyřazení.  

#### <a name="tags"></a>Značky

 Klient zadaného slovníku párů klíčových hodnot, podobně jako značky v klíče a tajné klíče.  

 > [!Note]
> Značky jsou přečíst volající v případě, že mají *seznamu* nebo *získat* oprávnění pro tento typ objektu; klíče, tajné klíče ani certifikáty.

###  <a name="BKMK_CertificatePolicy"></a> Zásady certifikátu

Zásady certifikátu obsahuje informace o tom, jak vytvořit a spravovat životní cyklus certifikátu KV. Při importu certifikátu s privátním klíčem do trezoru klíčů, je výchozí zásady vytvořené pro čtení x509 certifikátu.  

Při vytváření KV certifikát od začátku zásady musí být zadán pro Key Vault informovat o tom, jak vytvořit tuto verzi certifikátu KV nebo na další verzi KV certifikátu. Po vytvoření zásady, není nutné s následných vytvoření operací vytvořit další verze KV certifikátů.  

Existuje pouze jedna instance zásady pro všechny verze KV certifikátu.  

Na vysoké úrovni zásady certifikátu obsahuje následující:  

-   Vlastnosti certifikátu X509: obsahuje název subjektu, alternativní názvy subjektu atd. Umožňuje vytvořit x509 žádost o certifikát.  
-   Klíčové vlastnosti: typ klíče, obsahuje délka exportovatelný klíče a opakovaně používat klíčová pole. Tato pole, požádejte trezoru klíčů, o tom, jak vygenerovat klíč.  
-   Tajný vlastnosti: obsahuje tajný vlastnosti, jako je například typ obsahu adresovatelné tajného klíče ke generování tajná hodnota pro načtení certifikátu jako tajný klíč.  
-   Doba platnosti akce: obsahuje akce doba platnosti certifikátu KV. Každá akce životnost obsahuje:  

     - Aktivační události: Zadaný prostřednictvím dní před vypršení platnosti nebo doba života procento rozpětí  

     - Akce: určení typu akce – *emailContacts* nebo *automatickému*  

-   Vystavitel: Parametry o vystavitele certifikátu používat k vystavování x509 certifikátů.  
-   Atributy zásad: obsahuje atributy přidružených k zásadě  

#### <a name="x509-to-key-vault-usage-mapping"></a>X509 pro mapování využití Key Vault

Následující tabulka představuje mapování x509 zásady použití klíče pro efektivní operace s klíčem klíče vytvořené jako součást vytváření certifikátu Key Vault.

|**X509 flags použití klíče**|**Klíče ops Key Vault**|**Výchozí chování**|
|----------|--------|--------|
|DataEncipherment|šifrování, dešifrování| Není k dispozici |
|DecipherOnly|Dešifrování| Není k dispozici  |
|Bity DigitalSignature|podepsání, ověření| Výchozí Key Vault, bez použití specifikace v okamžiku vytvoření certifikátu | 
|EncipherOnly|encrypt| Není k dispozici |
|KeyCertSign|podepsání, ověření|Není k dispozici|
|KeyEncipherment|wrapKey unwrapKey| Výchozí Key Vault, bez použití specifikace v okamžiku vytvoření certifikátu | 
|Nepopiratelnosti odpovědnosti|podepsání, ověření| Není k dispozici |
|crlsign|podepsání, ověření| Není k dispozici |

###  <a name="BKMK_CertificateIssuer"></a> Vystavitel certifikátu

Objekt Key Vault certifikát obsahuje konfiguraci používaný ke komunikaci s poskytovatelem vystavitele vybraný certifikát pořadí x509 certifikáty.  

-   Key Vault partnerům následující poskytovatelé vystavitele certifikátu pro certifikáty SSL

|**Název zprostředkovatele**|**Umístění**|
|----------|--------|
|DigiCert|Podporované ve všech umístěních trezor klíčů služby ve veřejném cloudu a Azure Government.|
|GlobalSign|Podporované ve všech umístěních trezor klíčů služby ve veřejném cloudu a Azure Government.|

Aby bylo možné vytvořit vystavitele certifikátu v Key Vault, následujících požadovaných kroků 1 a 2 úspěšně provést.  

1. Připojit k zprostředkovatelů certifikátů certifikační autority  

    -   Správce organizace musí integrovaného společnosti (např. Contoso) s alespoň jeden poskytovatel certifikační Autority.  

2. Správce vytvoří žadatel přihlašovací údaje pro klíč trezoru k registraci (a obnovení) certifikáty SSL  

    -   Poskytuje konfigurace, který se má použít pro vytvoření objektu vystavitele zprostředkovatele v trezoru klíčů  

Další informace o vytváření objektů vystavitele z portálu certifikáty, najdete v článku [blog certifikáty trezoru klíčů](http://aka.ms/kvcertsblog)  

Key Vault umožňuje vytvoření více vystavitele objektů pomocí různých vystavitele zprostředkovatele konfigurace. Jakmile je vytvořen objekt vystavitele, jeho název může být odkazováno v jedné nebo více zásad certifikátu. Odkazování na objekt vystavitele dá pokyn Key Vault při žádosti x509 použít konfigurace jako zadaný v objektu vystavitele certifikátu od certifikační Autority zprostředkovatele během vytváření certifikátu a obnovení.  

Vystavitel objekty jsou vytvořené v trezoru a lze použít pouze s KV certifikáty v úložišti stejné.  

###  <a name="BKMK_CertificateContacts"></a> Certifikát kontaktů

Kontakty certifikát obsahovat kontaktní informace k odesílání oznámení aktivovaného událostmi doba platnosti certifikátu. Informace o kontakty sdílejí všechny certifikáty v trezoru klíčů. Oznámení se posílá všechny kontakty zadaná pro událost pro některý z certifikátů v trezoru klíčů.  

Pokud zásady certifikátů je nastavena na automatické obnovení, je odesláno upozornění na následující události.  

-   Před obnovením certifikátu
-   Po obnovení certifikátu, pokud certifikát byl úspěšně obnoven s informacemi o tom, nebo pokud došlo k chybě, které vyžadují ruční obnovení certifikátu.  

 Pokud zásady certifikátů je nastaven jako ručně obnovené (e-mailu jenom) a potom oznámení se odešle Pokud nastane čas na obnovení certifikátu.  

###  <a name="BKMK_CertificateAccessControl"></a> Řízení přístupu certifikátu

 Řízení přístupu pro certifikáty spravuje Key Vault a je k dispozici na úrovni Key Vault, který slouží jako kontejner těchto certifikátů. Není zásad řízení přístupu pro certifikáty, se liší od zásad řízení přístupu pro klíče a tajné klíče v úložišti stejný klíč. Uživatelé může vytvořit jeden nebo více trezorů pro certifikáty a je potřeba udržovat odpovídající segmentace scénář a správu certifikátů.  

 Na základě na objekt v položce řízení přístupu tajných klíčů v trezoru klíčů a úzce zrcadlení povolené na objekt tajný operace lze použít následující oprávnění:  

-   *získat*: umožňuje get aktuální certifikát verzi nebo verzi certifikát 
-   *seznam*: umožňuje seznam aktuálních certifikátů nebo verze certifikátu  
-   *Odstranit*: umožňuje odstranit certifikát, zásad a všechny její verze  
-   *vytvoření*: umožňuje vytvoření certifikátu Key Vault.  
-   *Import*: umožňuje import certifikátu materiálu do klíč trezoru certifikát.  
-   *Aktualizovat*: umožňuje aktualizace certifikátu.  
-   *manageconnects*: umožňuje správu Key Vault certifikát kontaktů  
-   *getissuers*: umožňuje get certifikátů vystavitelů  
-   *listissuers*: umožňuje seznam vystavitelů certifikátů na  
-   *setissuers*: umožňuje vytvořit nebo aktualizovat vystavitelů certifikátů, Key Vault  
-   *deleteissuers*: umožňuje odstranit vystavitelů certifikátů, Key Vault  
-   *všechny*: uděluje všechna oprávnění  

## <a name="additional-information-for-certificates"></a>Další informace o certifikáty

- [Certifikáty a zásady](/rest/api/keyvault/certificates-and-policies.md)
- [Vystavitelů certifikátů](/rest/api/keyvault/certificate-issuers.md)
- [Certifikát kontaktů](/rest/api/keyvault/certificate-contacts.md)

## <a name="see-also"></a>Viz také

- [Ověřování, požadavky a odpovědi](authentication-requests-and-responses.md)
- [Verze Key Vault](key-vault-versions.md)
- [Příručka pro vývojáře trezoru klíčů](/azure/key-vault/key-vault-developers-guide)
