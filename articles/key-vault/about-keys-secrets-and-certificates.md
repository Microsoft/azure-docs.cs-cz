---
title: Informace o klíčích, tajných kódů a certifikátů Azure Key Vault
description: Přehled Azure Key Vault REST rozhraní a pro vývojáře podrobnosti pro klíče, tajné kódy a certifikáty.
services: key-vault
documentationcenter: ''
author: BryanLa
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: abd1b743-1d58-413f-afc1-d08ebf93828a
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: bryanla
ms.openlocfilehash: 00a2c0d50a2476995dfb0d16d0cdbc5bd0ec313b
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50138983"
---
# <a name="about-keys-secrets-and-certificates"></a>Informace o klíčích, tajných kódů a certifikátů

Služba Azure Key Vault umožňuje uživatelům ukládat a používat několik typů tajného kódu a klíče dat a aplikací Microsoft Azure:

- Kryptografické klíče: podporuje více typy klíčů a algoritmy a umožňuje použití z moduly hardwarového zabezpečení (HSM) pro vysoká hodnota klíče. 
- Tajné kódy: Poskytuje zabezpečené úložiště tajných kódů, jako jsou hesla a databázové připojovací řetězce.
- Certifikáty: Podporuje certifikáty, které jsou vytvořené s využitím klíče a tajné kódy a přidat funkci automatického obnovení.
- Azure Storage: Může spravovat klíče účtu služby Azure Storage za vás. Interně jsou služby Key Vault můžete vypsat klíče (sync) pomocí účtu služby Azure Storage a znovu vygenerovat (Otočit) klíče pravidelně. 

Další obecné informace o službě Key Vault najdete v tématu [co je Azure Key Vault?](/azure/key-vault/key-vault-whatis)

## <a name="azure-key-vault"></a>Azure Key Vault

Následující části nabízí obecné informace napříč implementace služby Key Vault.

###  <a name="supporting-standards"></a>Podpora standardů

Zápis JSON (JavaScript Object) a specifikace jazyka JavaScript objekt podepisování a šifrování (blog JOSE) jsou důležité informace.  

-   [Webového klíče JSON (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [Šifrování webové JSON (pomocí JWE.)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON Web algoritmy (DŽWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON Web podpis (JWS)](http://tools.ietf.org/html/draft-ietf-jose-json-web-signature)  

### <a name="data-types"></a>Typy dat

Odkazovat na blog JOSE specifikace pro příslušné datové typy klíčů, šifrování a podepisování.  

-   **algoritmus** – podporované algoritmus klíče operace, například RSA1_5  
-   **Hodnota šifrovaného textu** -šifer oktety text, kódované pomocí Base64URL  
-   **Hodnota ověřování algoritmem Digest** -výstupu algoritmu hash kódovaný pomocí Base64URL  
-   **Typ klíče** – jeden z podporovaných typů klíčů, třeba RSA (Rivest-Shamir-Adleman).  
-   **Hodnota prostého textu** -oktety ve formátu prostého textu, kódované pomocí Base64URL  
-   **hodnota podpisu** – výstupní algoritmu podpisu kódovaný pomocí Base64URL  
-   **base64URL** – Base64URL [RFC4648] kódování binární hodnota  
-   **Logická** – true nebo false  
-   **Identita** – identit z Azure Active Directory (AAD).  
-   **IntDate** – Desítková hodnota JSON představující počet sekund od 1970-01-01T0:0:0Z UTC až do zadaného data a času UTC. Viz zejména RFC3339 podrobnosti týkající se date/times obecně a standardem UTC.  

###  <a name="objects-identifiers-and-versioning"></a>Objekty, identifikátory a správy verzí

Objekty uložené ve službě Key Vault se systémovou správou verzí pokaždé, když je vytvořena nová instance objektu. Každá verze je přiřazen jedinečný identifikátor a adresu URL. Při prvním vytvoření objektu má daný identifikátor jedinečný verze a označena jako aktuální verze objektu. Vytvoření nové instance se stejným názvem objektu poskytuje nový objekt verze jedinečný identifikátor, by ji k aktuální verzi.  

Objekty ve službě Key Vault se dají řešit pomocí aktuální identifikátor nebo identifikátor konkrétní verze. Mějme například klíč s názvem `MasterKey`, provádění operací s aktuální identifikátor způsobí, že systém použít nejnovější dostupnou verzi. Provádění operací s identifikátorem specifické pro verzi způsobí, že systém na tuto konkrétní verzi objektu.  

Objekty se jednoznačně identifikují v rámci služby Key Vault pomocí adresy URL. Žádné dva objekty v systému mají stejnou adresu URL, bez ohledu na to geografického umístění. Úplnou adresu URL na objekt, se nazývá identifikátor objektu. Adresa URL se skládá z předpony, který identifikuje Key Vault, typ objektu, uživatel zadal název objektu a Object Version. Název objektu je velká a malá písmena a neměnné. Identifikátory, které neobsahují verze objektu se označují jako základ identifikátory.  

Další informace najdete v tématu [ověřování, požadavky a odpovědi](authentication-requests-and-responses.md)

Identifikátor objektu má následující obecné formát:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Kde:  

|||  
|-|-|  
|`keyvault-name`|Název pro trezor klíčů ve službě Microsoft Azure Key Vault.<br /><br /> Key Vault názvy jsou vybrané uživatelem a globálně jedinečný.<br /><br /> Název služby Key Vault musí být řetězec 3 až 24 znaků obsahující pouze 0-9, a – z, A-Z a -.|  
|`object-type`|Typ objektu, "klíče" nebo "tajné".|  
|`object-name`|`object-name` Je k dispozici uživatelské jméno a musí být jedinečné v rámci služby Key Vault. Název musí být řetězec 1 – 127 znaků obsahující pouze 0-9, a – z, A-Z a -.|  
|`object-version`|`object-version` Se generována, 32 identifikátor řetězce znaků, která je volitelně používat k adresování jedinečnou verzi objektu.|  

## <a name="key-vault-keys"></a>Klíče služby Key Vault

###  <a name="keys-and-key-types"></a>Klíče a typy klíčů

Kryptografické klíče ve službě Key Vault jsou reprezentovány ve formě objektů webového klíče JSON [JWK]. Základní specifikace JWK/DŽWA jsou také rozšířeny Pokud chcete povolit typy klíčů jedinečné pro implementaci služby Key Vault. Import klíče pomocí modulu hardwarového zabezpečení specifické pro výrobce balení, například umožňuje zabezpečený přenos sady klíčů, které se dá použít jenom v modulů HSM služby Key Vault.  

- **"Text soft" klíče**: klíč zpracovány v softwaru ve službě Key Vault, ale se šifrují při nečinnosti pomocí systému klíč, který je v modulu hardwarového zabezpečení. Klienti mohou importovat existující klíč RSA nebo ES (Elliptic Curve) nebo požadavku, že Key Vault vygenerovat.
- **"Pevné" klíče**: klíč zpracovány v modulu hardwarového zabezpečení (modulu hardwarového zabezpečení). Tyto klíče jsou chráněné v jedné z Světy zabezpečení Key Vault HSM (je jeden Security World podle zeměpisné oblasti, aby se zachovala izolace). Klienti mohou importovat klíč RSA nebo ES, měkké formuláře nebo tak, že vyexportujete z kompatibilní zařízení HSM. Klienti mohou také požádat o službě Key Vault ke generování klíče. Tento typ klíče přidá atribut T JWK získat pro přenesení klíče HSM.

     Další informace o zeměpisných hranic najdete v tématu [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/privacy/)  

Key Vault podporuje jenom klíče RSA a eliptické křivky. 

-   **ES**: klíč "text Soft" eliptické křivky.
-   **ES HSM**: klíč "Pevného" eliptické křivky.
-   **RSA**: klíč RSA "text Soft".
-   **RSA HSM**: klíč RSA "Pevné".

Key Vault podporuje klíče RSA 2048, 3072 do 4096 velikostí. Key Vault podporuje eliptické křivky klíč typy p-256, p-384, p-521 a P-256_K (SECP256K1).

### <a name="cryptographic-protection"></a>Ochrana kryptografických

Kryptografických modulů, které používá služby Key Vault, zda HSM nebo software, jsou ověřené podle standardu FIPS (Federal Information Processing Standards). Nemusíte dělat nic zvláštního ke spuštění v režimu FIPS. Klíče **vytvořili** nebo **importovat** jako chráněné pomocí HSM jsou zpracovány uvnitř modulu hardwarového zabezpečení, ověření podle standardu FIPS 140-2 úrovně 2 nebo vyšší. Klíče **vytvořili** nebo **importovat** jako chráněný softwarem, jsou zpracovávány v kryptografických modulů, které jsou ověřené podle standardu FIPS 140-2 úrovně 1 nebo novější. Další informace najdete v tématu [klíče a typy klíčů](#keys-and-key-types).

###  <a name="ec-algorithms"></a>Algoritmy ES
 Podporují se následující identifikátory algoritmus s klíči ES a ES HSM ve službě Key Vault. 

#### <a name="curve-types"></a>Typy křivky

-   **P-256** – The NIST křivky p-256, definované v [DSS FIPS PUB 186 4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-256_K** – The s křivky SECP256K1 definované na [s 2: doporučené parametry domény eliptické křivky](http://www.secg.org/sec2-v2.pdf).
-   **P-384** – The NIST křivky p-384 definované na [DSS FIPS PUB 186 4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-521** – The NIST křivky p-521 definované na [DSS FIPS PUB 186 4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).

#### <a name="signverify"></a>/ OVĚŘENÍ

-   **ES256** – hodnoty Digest ECDSA algoritmu SHA-256 a klíče vytvořené pomocí křivky p-256. Tento algoritmus je popsán v [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES256K** – hodnoty Digest ECDSA algoritmu SHA-256 a klíče vytvořené pomocí křivky P-256_K. Čeká na tento algoritmus normalizace.
-   **ES384** – hodnoty Digest ECDSA pro SHA-384 a klíče vytvořené pomocí křivky p-384. Tento algoritmus je popsán v [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES512** – hodnoty Digest pro algoritmus SHA-512 ECDSA a klíče vytvořené pomocí křivky p-521. Tento algoritmus je popsán v [RFC7518](https://tools.ietf.org/html/rfc7518).

###  <a name="rsa-algorithms"></a>Algoritmy RSA  
 Podporují se následující identifikátory algoritmus s klíči RSA a RSA HSM ve službě Key Vault.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY A UNWRAPKEY, ŠIFROVÁNÍ/DEŠIFROVÁNÍ

-   **RSA1_5** -šifrování klíče RSAES V1_5 PKCS1 [RFC3447]  
-   **RSA OAEP** – RSAES optimální asymetrického šifrování odsazení (OAEP) [RFC3447], pomocí zadané podle RFC. v části podle A.2.1 3447 výchozí parametry. Tyto výchozí parametry jsou pomocí funkce hash SHA-1 a funkce generování maska MGF1 pomocí algoritmu SHA-1.  

#### <a name="signverify"></a>/ OVĚŘENÍ

-   **RS256** – RSASSA-PKCS-v1_5 pomocí algoritmu SHA-256. Hodnotu digest aplikace zadán musí být vypočítán pomocí algoritmu SHA-256 a musí mít délku 32 bajtů.  
-   **RS384** – RSASSA-PKCS-v1_5 pomocí algoritmu SHA-384. Hodnota ověřování algoritmem digest aplikace zadán musí být vypočítán pomocí algoritmu SHA-384 a musí být aspoň 48 bajtů v délce.  
-   **RS512** – RSASSA-PKCS-v1_5 pomocí algoritmu SHA-512. Hodnotu digest aplikace zadán musí být vypočítán pomocí algoritmu SHA-512 a musí mít délku 64 bajtů.  
-   **RSNULL** – viz [RFC2437] specializované případu použití k povolení určitých scénářích TLS.  

###  <a name="key-operations"></a>Klíčové operace

Key Vault podporuje následující operace na klíčové objekty:  

-   **Vytvoření**: umožňuje klientovi vytvoření klíče v Key Vault. Hodnotu klíče je generována pomocí služby Key Vault a uloženy a není vydané do klienta. Asymetrické klíče mohou být vytvořeny ve službě Key Vault.  
-   **Importovat**: umožňuje klientovi importovat existující klíč služby Key Vault. Asymetrické klíče mohou importovat do služby Key Vault pomocí několika různých balení metod v rámci JWK konstrukce. 
-   **Aktualizace**: umožňuje klientovi s dostatečnými oprávněními k úpravě metadat (klíčové atributy) přidružené k dříve uložené ve službě Key Vault klíč.  
-   **Odstranit**: umožňuje klientovi s dostatečnými oprávněními k odstranění klíče ze služby Key Vault.  
-   **Seznam**: umožňuje klientovi seznam všech klíčů v dané službě Key Vault.  
-   **Seznam verzí**: umožňuje klientovi seznam všech verzí daného klíče v dané službě Key Vault.  
-   **Získat**: umožňuje klientovi načíst veřejnou částí daného klíče ve službě Key Vault.  
-   **Zálohování**: Exportuje klíče ve formě chráněné.  
-   **Obnovit**: importovat dříve zálohovaná klíč.  

Další informace najdete v tématu [klíče operací v Key Vault REST API odkaz](/rest/api/keyvault).  

Po vytvoření klíče ve službě Key Vault může následující kryptografické operace provádějí pomocí klíče:  

-   **Přihlášení a ověřte, zda**: výhradně, tato operace je "hodnota hash podpisu" nebo "ověřit", Key Vault nepodporuje hashování obsahu jako součást vytváření podpis. Aplikace by měla hash data, která mají být podepsané místně, a pak žádost o přihlášení služby Key Vault-the-hash. Ověření hodnoty hash podepsaný držitelem je podporovaný jako pohodlí operace pro aplikace, které nemusí mít přístup k [veřejná] materiál klíče. Pro zajištění nejlepšího výkonu aplikace ověřte, že operace se provádějí místně.  
-   **Šifrování klíče / obtékání**: klíč uložený ve službě Key Vault slouží k ochraně klíče jiného, obvykle symetrického obsahu šifrovacího klíče (CEK). Po asymetrického klíče v Key Vault se používá šifrování pomocí klíče. Například operace WRAPKEY a UNWRAPKEY a RSA OAEP jsou ekvivalentní k šifrování a DEŠIFROVÁNÍ. Po symetrický klíč ve službě Key Vault klíč wrapping se používá. Například AES KW. WRAPKEY operace je podporována v zájmu usnadnění práce pro aplikace, které nemusí mít přístup k [veřejná] materiál klíče. Pro zajištění nejlepšího výkonu aplikace WRAPKEY operace je třeba provést místně.  
-   **Šifrování a dešifrování**: klíč uložený ve službě Key Vault slouží k šifrování a dešifrování dat jeden blok. Velikost bloku je určen podle typu klíče a vybrané šifrovací algoritmus. Operace šifrování se poskytuje pro usnadnění práce pro aplikace, které nemusí mít přístup k [veřejná] materiál klíče. Pro zajištění nejlepšího výkonu aplikace šifrování operace by měl provádět místně.  

Při WRAPKEY a UNWRAPKEY pomocí asymetrických klíčů se může zdát nadbytečný (jak operace je ekvivalentní k šifrování a DEŠIFROVÁNÍ), je důležité používat různé operace. Rozdíl poskytuje sémantické a povolení oddělení těchto operací a konzistence, když jiné typy klíčů jsou podporovány službou.  

Key Vault nepodporuje operace EXPORTU. Po zřízení klíčem v systému není možné extrahovat nebo změnit jeho klíče. Uživatelé služby Key Vault však může vyžadovat svůj klíč pro případy, například jako po byla odstraněna další použití. V takovém případě může používají operace zálohování a obnovení pro export a import klíče ve formě chráněné. Klíče vytvořené operací zálohování nejsou použitelné mimo služby Key Vault. Alternativně se operace IMPORTU lze proti více instancí služby Key Vault.  

Uživatelé mohou omezit všechny kryptografické operace, které služby Key Vault podporuje na základě za klíč pomocí vlastnosti key_ops JWK objektu.  

Další informace o objektech JWK, naleznete v tématu [JSON Web klíč (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key).  

###  <a name="key-attributes"></a>Klíčové atributy

Kromě materiál klíče je možné zadat následující atributy. V požadavku JSON, atributy – klíčové slovo a složené závorky ' {""} ", jsou požadovány, i když nejsou žádné atributy určené.  

- *povolené*: logická hodnota, nepovinné, výchozí hodnota je **true**. Určuje, zda je klávesa aktivní a použitelný pro kryptografické operace. *Povolené* atribut se používá ve spojení s *nbf* a *exp*. Při operaci dochází mezi *nbf* a *exp*, se budou jenom povoleny Pokud *povolené* je nastavena na **true**. Operace mimo *nbf* / *exp* okno automaticky jsou zakázané, s výjimkou určitých typů operace v rámci [konkrétní podmínky](#date-time-controlled-operations).
- *NBF*: IntDate, volitelné, výchozí hodnota je teď. *Nbf* (ne před) atribut určuje dobu, před kterým musí klíč není používán pro kryptografické operace, s výjimkou určitých typů operace v rámci [konkrétní podmínky](#date-time-controlled-operations). Zpracování *nbf* atribut vyžaduje, že aktuální datum a čas musí nastat po nebo rovno nikoli-před podle data a času *nbf* atribut. Key Vault může poskytnout pro některé malé volnost, obvykle více než několik minut, aby se zohlednily hodin zkreslit. Hodnotou musí být číslo obsahující hodnotu IntDate.  
- *Exp*: IntDate, volitelné, výchozí hodnota je "navždy". *Exp* atribut (čas vypršení platnosti) označuje čas vypršení platnosti nebo později, který klíč nesmí použít pro kryptografické operace, s výjimkou určitých typů operace v rámci [konkrétní podmínky](#date-time-controlled-operations). Zpracování *exp* atribut vyžaduje, že aktuální datum a čas musí být před datum a čas vypršení platnosti uvedené v *exp* atribut. Key Vault může poskytnout pro některé malé volnost, obvykle více než několik minut, aby se zohlednily hodin zkreslit. Hodnotou musí být číslo obsahující hodnotu IntDate.  

Existují další atributy jen pro čtení, které jsou zahrnuty v odpovědi, která zahrnuje klíčové atributy:  

- *vytvoření*: IntDate volitelné. *Vytvořili* atribut označuje, kdy byla vytvořena tato verze klíče. Je hodnota null pro klíče vytvořené před přidáním tohoto atributu. Hodnotou musí být číslo obsahující hodnotu IntDate.  
- *Aktualizovat*: IntDate volitelné. *Aktualizovat* atribut označuje, kdy došlo k aktualizaci této verze klíče. Je hodnota null pro klíče, které byly aktualizovány naposledy před přidáním tohoto atributu. Hodnotou musí být číslo obsahující hodnotu IntDate.  

Další informace o IntDate a jiné datové typy najdete v tématu [datové typy](#data-types)  

#### <a name="date-time-controlled-operations"></a>Operace řízené datum a čas

Není dosud platný a jeho platnost klíče, mimo *nbf* / *exp* okna, bude fungovat pro **dešifrovat**, **rozbalení**a **ověřte** operací (nevrátí 403, zakázáno). Důvody pro použití není dosud platný stavu je umožnit klíč, který má být testována před použití v produkčním prostředí. Důvody pro použití vypršela platnost je umožnit operace obnovení pro data, která byla vytvořena, když byl platný klíč. Kromě toho můžete zakázat přístup ke klíči pomocí služby Key Vault zásad nebo aktualizací *povolené* klíčový atribut k **false**.

Další informace o typech dat najdete v části [datové typy](#data-types).

Další informace o dalších atributů je to možné, najdete v článku [JSON Web klíč (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key).

### <a name="key-tags"></a>Klíče značek

Můžete zadat další metadata specifická pro aplikaci ve formě značek. Key Vault podporuje až 15 značek, z nichž každá může mít 256 znaků názvu a hodnoty 256 znaků.  

>[!Note]
>Značky jsou přečíst volající v případě, že mají *seznamu* nebo *získat* oprávnění pro tento typ objektu (klíče, tajné kódy nebo certifikáty).

###  <a name="key-access-control"></a>Řízení přístupu ke klíčům

Řízení přístupu pro klíče spravované službou Key Vault je zadaná na úrovni, která funguje jako kontejner klíčů služby Key Vault. Zásada řízení přístupu pro klíče, se liší od zásad řízení přístupu pro tajné kódy ve stejné službě Key Vault. Uživatelé mohou vytvořit jeden nebo více trezorů pro uložení klíče a jsou nutné k údržbě scénář odpovídající segmentaci a správu klíčů. Řízení přístupu pro klíče je nezávislé na řízení přístupu pro tajné kódy.  

Tato oprávnění lze udělit, na za uživatele / service základem v položky řízení přístupu klíče v trezoru. Tato oprávnění úzce zrcadlí přípustné na klíče objektu operace:  

- Oprávnění pro operace správy klíčů
  - *získat*: Přečtěte si části veřejný klíč a jeho atributy
  - *seznam*: seznam klíčů nebo verze klíč uložený ve službě key vault
  - *Aktualizovat*: aktualizovat atributy pro klíč
  - *vytvoření*: vytvářet nové klíče
  - *Importovat*: Import klíče do trezoru klíčů
  - *Odstranit*: odstranění klíče objektu
  - *Obnovit*: obnovení odstraněné klíče
  - *Zálohování*: zálohu klíče v trezoru klíčů
  - *Obnovit*: Obnovte zálohovaný klíč do trezoru klíčů

- Oprávnění pro kryptografické operace
  - *dešifrování*: použít klíč k odemknutí pořadí bajtů
  - *šifrování*: používá se pro ochranu libovolného pořadí bajtů
  - *unwrapKey*: použít klíč k odemknutí zabalené symetrické klíče
  - *wrapKey*: používá se k ochraně symetrický klíč
  - *Ověřte*: používá se k ověření přehledu  
  - *znaménko*: použití hodnoty klíč k podepsání Digest
    
- Oprávnění u privilegovaných operací
  - *Vyprázdnit*: Vymazat (trvale odstranit) odstraněné klíče

Další informace o práci s klíči najdete v tématu [klíče operací v Key Vault REST API odkaz](/rest/api/keyvault). Informace o vytvářejí se oprávnění najdete v tématu [trezory - vytvořit nebo aktualizovat](/rest/api/keyvault/vaults/createorupdate) a [trezorů – aktualizace zásad přístupu](/rest/api/keyvault/vaults/updateaccesspolicy). 

## <a name="key-vault-secrets"></a>Tajné klíče služby Key Vault 

### <a name="working-with-secrets"></a>Práce s tajnými kódy

Rozhraní API Key Vault z pohledu vývojáře přijímají a vrací hodnoty tajných kódů jako řetězce. Interně služby Key Vault uchovává a spravuje tajné kódy jako sekvence oktety (8 bitů v bajtech), s maximální velikostí 25 tisíc bajtů. Služba Key Vault neposkytuje sémantiku tajnými kódy. Je pouze přijímá data, šifruje, uloží ho a vrátí identifikátor tajného kódu ("id"). Tento identifikátor slouží k načtení tajný kód později.  

Pro vysoce citlivá data klienti měli zvážit další vrstvy ochrany pro data. Šifrování dat pomocí klíče samostatné ochrany před úložiště ve službě Key Vault je jedním z příkladů.  

Key Vault podporuje také pole contentType tajnými kódy. Klienti mohou zadat typ obsahu pomáhat při interpretaci tajných dat při načítání tajného kódu. Maximální délka tohoto pole je 255 znaků. Neexistují žádné předem definované hodnoty. Navrhované využívá jako Nápověda pro interpretaci tajná data. Například implementace může ukládat hesla a certifikáty jako tajné kódy a pak toto pole použít k rozlišení. Neexistují žádné předdefinované hodnoty.  

### <a name="secret-attributes"></a>Atributy tajného kódu

Kromě tajných dat lze zadat následující atributy:  

- *Exp*: IntDate, volitelné, výchozí hodnota je **navždy**. *Exp* atribut (čas vypršení platnosti) označuje čas vypršení platnosti na nebo za které tajná data by měla nelze načíst, s výjimkou [konkrétní situace](#date-time-controlled-operations). Toto pole je **informační** účely jen informuje uživatele ze služby key vault, nelze použít konkrétní tajný klíč. Hodnotou musí být číslo obsahující hodnotu IntDate.   
- *NBF*: IntDate, volitelné, výchozí hodnota je **nyní**. *Nbf* (ne před) atribut určuje dobu, před kterým by MĚL nebyla načtena tajných dat, s výjimkou [konkrétní situace](#date-time-controlled-operations). Toto pole je **informační** pouze účely. Hodnotou musí být číslo obsahující hodnotu IntDate. 
- *povolené*: logická hodnota, nepovinné, výchozí hodnota je **true**. Tento atribut určuje, zda lze načíst tajné údaje. Atribut enabled se používá ve spojení s *nbf* a *exp* dojde operaci mezi *nbf* a *exp*, bude pouze povolené, pokud je povoleno je nastavená na **true**. Operace mimo *nbf* a *exp* okno se automaticky zakázaná, s výjimkou v [konkrétní situace](#date-time-controlled-operations).  

Existují další atributy jen pro čtení, které jsou zahrnuty v odpovědi, která zahrnuje tajný atributy:  

- *vytvoření*: IntDate volitelné. Vytvořený atribut označuje, kdy byla vytvořena tato verze tajného klíče. Je tato hodnota null pro tajné klíče vytvořené před přidáním tohoto atributu. Hodnotou musí být číslo obsahující hodnotu IntDate.  
- *Aktualizovat*: IntDate volitelné. Aktualizovaný atribut označuje, kdy došlo k aktualizaci této verzi tajného klíče. Je tato hodnota null pro tajné kódy, které byly aktualizovány naposledy před přidáním tohoto atributu. Hodnotou musí být číslo obsahující hodnotu IntDate.

#### <a name="date-time-controlled-operations"></a>Operace řízené datum a čas

Tajného **získat** operace bude fungovat pro tajné klíče není dosud platný a jejichž platnost vypršela, mimo *nbf* / *exp* okna. Volání tajného **získat** operaci pro není dosud platný tajný kód, lze použít pro účely testování. Načítání (**získat**nocení) vypršela platnost tajného klíče, lze použít pro operace obnovení.

Další informace o typech dat najdete v části [datové typy](#data-types).  

### <a name="secret-access-control"></a>Řízení přístupu k tajným klíčům

Řízení přístupu pro tajné klíče spravované ve službě Key Vault, je k dispozici na úrovni služby Key Vault, která obsahuje tyto tajné kódy. Zásady řízení přístupu pro tajné klíče, se liší od zásad řízení přístupu pro klíče ve stejné službě Key Vault. Uživatelé mohou vytvořit jeden nebo více trezorů pro uložení tajných kódů a jsou nutné k údržbě scénář odpovídající segmentaci a správu tajných kódů.   

Tato oprávnění můžete použít, na základě na objekt v položky řízení přístupu tajné klíče v trezoru a úzce zrcadlení přípustné tajného kódu objektu operace:  

- Oprávnění pro operace správy tajných kódů
  - *získat*: čtení tajného klíče  
  - *seznam*: seznam tajných kódů nebo verzí tajného klíče do služby Key Vault  
  - *Nastavte*: vytvoření tajného klíče  
  - *Odstranit*: odstranění tajného kódu  
  - *Obnovit*: obnovení odstraněných tajného kódu
  - *Zálohování*: zálohování tajný klíč v trezoru klíčů
  - *Obnovit*: obnovení zálohovaného až tajného klíče do trezoru klíčů

- Oprávnění u privilegovaných operací
  - *Vyprázdnit*: Vymazat (trvale odstranit) odstraněné tajného kódu

Další informace o práci s tajnými kódy, naleznete v tématu [tajných kódů operací v Key Vault REST API odkaz](/rest/api/keyvault). Informace o vytvářejí se oprávnění najdete v tématu [trezory - vytvořit nebo aktualizovat](/rest/api/keyvault/vaults/createorupdate) a [trezorů – aktualizace zásad přístupu](/rest/api/keyvault/vaults/updateaccesspolicy). 

### <a name="secret-tags"></a>Značky tajného kódu  
Můžete zadat další metadata specifická pro aplikaci ve formě značek. Key Vault podporuje až 15 značek, z nichž každá může mít 256 znaků názvu a hodnoty 256 znaků.  

>[!Note]
>Značky jsou přečíst volající v případě, že mají *seznamu* nebo *získat* oprávnění pro tento typ objektu (klíče, tajné kódy nebo certifikáty).

## <a name="key-vault-certificates"></a>Certifikátů trezor klíčů

Podpora certifikáty služby Key Vault nabízí pro správu vaší x509 certifikáty a následujícího chování:  

-   Umožňuje vlastníkovi certifikátů k vytvoření certifikátu prostřednictvím procesu vytváření služby Key Vault nebo importu stávajícího certifikátu. Zahrnuje podepsané svým držitelem i a vygenerovat certifikáty certifikační autority.
-   Umožňuje vlastníkovi certifikát služby Key Vault implementací zabezpečeného úložiště a správy X509 certifikáty bez interakce s materiál privátního klíče.  
-   Umožňuje vlastníkovi certifikát můžete vytvořit zásadu, která přesměruje služby Key Vault pro správu životního cyklu certifikátu.  
-   Umožňuje vlastníkům certifikát lze zadat kontaktní informace pro oznámení o události životního cyklu vypršení platnosti a obnovení certifikátu.  
-   Podporuje automatické obnovení s vybranou vystavitelů - Key Vault partnera X509 certifikát poskytovatele / certifikační autority.

>[!Note]
>Partnerství jiných poskytovatelů nebo autority jsou také povoleny, ale nebude podporovat funkci automatického obnovení.

### <a name="composition-of-a-certificate"></a>Složení certifikátu

Když se vytvoří certifikátem Key Vaultu, se stejným názvem, vytvoří také adresovatelný klíč a tajný klíč služby. Klíč služby Key Vault umožňuje operace klíče a tajného kódu Key Vault umožňuje načíst hodnotu certifikát jako tajný kód. Certifikát služby Key Vault také obsahuje veřejné x509 certifikátu metadat.  

Identifikátor a verzi certifikátů je podobný tomu klíčů a tajných kódů. Konkrétní verzi adresovatelný klíč a tajný kód s verzí certifikát služby Key Vault je k dispozici v odpovědi certifikátu služby Key Vault.
 
![Certifikáty jsou komplexních objektů](media/azure-key-vault.png)

### <a name="exportable-or-non-exportable-key"></a>Exportovatelné nebo jiných exportovatelné klíče

Když se vytvoří certifikátem Key Vaultu, se dá načíst z adresovatelný tajného kódu s privátním klíčem ve formátu PFX nebo PEM. Zásady použité k vytvoření certifikátu musí uvádět, že klíč je možné exportovat. Pokud zásady označuje neexportovatelného, privátní klíč není součástí hodnotu, pokud načíst jako tajný kód.  

Adresovatelný klíč se stane relevantnější neexportovatelného KV certifikáty. Operace adresovatelný KV klíč mapování z *keyusage* Zásady certifikátů KV použilo k vytvoření certifikátu KV pole.  

Podporuje dva typy klíče – *RSA* nebo *RSA HSM* pomocí certifikátů. Exportovatelné je povolen pouze v RSA, není podporována RSA modulu hardwarového zabezpečení.  

### <a name="certificate-attributes-and-tags"></a>Certifikát atributy a značky

Kromě metadaty certifikátu, adresovatelný klíč a adresovatelný tajný klíč certifikátem Key Vaultu taky obsahuje atributy a značky.  

#### <a name="attributes"></a>Atributy

Atributy adresovatelný klíč a tajný klíč vytvořený při vytváření certifikátu KV jsou zrcadleny atributy certifikátu.  

Certifikát služby Key Vault má následující atributy:  

-   *povolené*: logická hodnota, nepovinné, výchozí hodnota je **true**. Je možné zadat pro označení, pokud je možné načíst data certifikátu jako provozuschopného jako klíče nebo tajného kódu. Také se používá ve spojení s *nbf* a *exp* dojde operaci mezi *nbf* a *exp*a budou povoleny pouze pokud je povoleno je nastavena na hodnotu true. Operace mimo *nbf* a *exp* okna jsou automaticky zakázány.  

Existují další atributy jen pro čtení, které jsou zahrnuty v odpovědi:

-   *vytvoření*: IntDate: Určuje, kdy byla vytvořena tato verze certifikátu.  
-   *Aktualizovat*: IntDate: Určuje, kdy došlo k aktualizaci této verze certifikátu.  
-   *Exp*: IntDate: obsahuje hodnotu Datum vypršení platnosti x509 certifikátu.  
-   *NBF*: IntDate: obsahuje hodnotu Datum x509 certifikátu.  

> [!Note] 
> Pokud vyprší platnost certifikátu služby Key Vault, je adresovatelný klíč a tajný kód nefunkčnost.  

#### <a name="tags"></a>Značky

 Klient zadaného slovníku páry klíč-hodnota, podobně jako značky v klíči a tajnými kódy.  

 > [!Note]
> Značky jsou přečíst volající v případě, že mají *seznamu* nebo *získat* oprávnění pro tento typ objektu (klíče, tajné kódy nebo certifikáty).

### <a name="certificate-policy"></a>Zásady certifikátů

Zásady certifikátu obsahuje informace o tom, jak vytvářet a spravovat životní cyklus certifikátem Key Vaultu. Při importu certifikátu s privátním klíčem do služby key vault, najdete x509 se vytvoří výchozí zásady certifikátu.  

Když certifikátem Key Vaultu je vytvořený z nuly, zásady musí být zadán. Tato zásada určuje, jak vytvořit verzi této služby Key Vault certifikátu nebo na další verzi certifikátu služby Key Vault. Po vytvoření zásady, není to nutné s po sobě jdoucích vytvoření operací pro budoucí verze. Existuje pouze jedna instance zásady pro všechny verze sady certifikátem Key Vaultu.  

Zásady certifikátu na vysoké úrovni obsahuje následující informace:  

-   Vlastnosti certifikátu X509: obsahuje název subjektu, alternativní názvy subjektu a další vlastnosti použít k vytvoření x x509 žádost o certifikát.  
-   Vlastnosti klíče: obsahuje typ klíče, délka, exportovat, klíče a znovu použít pole klíče. Tato pole dáte pokyn, aby služby key vault o tom, jak vygenerovat klíč.  
-   Vlastnosti tajného kódu: obsahuje vlastnosti tajného kódu, jako je například typ obsahu adresovatelný tajný kód k vygenerování tajná hodnota pro načtení certifikát jako tajný kód.  
-   Doba života akce: obsahuje akce životnosti KV certifikátu. Obsahuje všechny akce životnost:  

     - Aktivační události: Zadaný přes dní před vypršení platnosti nebo doba života procento rozpětí  

     - Akce: určení typu akce – *emailContacts* nebo *aktualizují*  

-   Vystavitel: Parametry o používat certifikáty x509 vystavitele certifikátu.  
-   Zásad atributy: obsahuje atributy přidružených k zásadě  

#### <a name="x509-to-key-vault-usage-mapping"></a>X509 mapování využití služby Key Vault

Následující tabulka představuje mapování x509 zásady použití klíče pro efektivní klíčové operace klíče vytvořené jako součást vytváření certifikátů služby Key Vault.

|**X509 příznaky použití klíče**|**Ops klíčů služby Key Vault**|**Výchozí chování**|
|----------|--------|--------|
|DataEncipherment|šifrování, dešifrování| neuvedeno |
|DecipherOnly|dešifrování| neuvedeno  |
|Bity DigitalSignature|podepsání, ověření| Výchozí služby Key Vault bez specifikace využití v okamžiku vytvoření certifikátu | 
|EncipherOnly|encrypt| neuvedeno |
|KeyCertSign|podepsání, ověření|neuvedeno|
|KeyEncipherment|wrapKey, unwrapKey| Výchozí služby Key Vault bez specifikace využití v okamžiku vytvoření certifikátu | 
|Nepopiratelnosti odpovědnosti|podepsání, ověření| neuvedeno |
|crlsign|podepsání, ověření| neuvedeno |

### <a name="certificate-issuer"></a>Vystavitel certifikátu

Objekt certifikátu služby Key Vault uchovává konfigurace používá ke komunikaci se zprostředkovateli vybraný certifikát vystavitele pořadí x509 certifikáty.  

-   Partneři služby Key Vault s následujícím poskytovatelé vystavitele certifikátu pro certifikáty SSL

|**Název poskytovatele**|**Umístění**|
|----------|--------|
|DigiCert|Podporováno ve všech umístěních služby key vault ve veřejném cloudu a Azure Government|
|GlobalSign|Podporováno ve všech umístěních služby key vault ve veřejném cloudu a Azure Government|

Vystavitel certifikátu bylo možné vytvořit ve službě Key Vault, musí úspěšně provedli následující požadované kroky 1 a 2.  

1. Připojení ke zprostředkovatelům certifikátu autority (CA)  

    -   Správce organizace musíte připojit své společnosti (např.) Contoso) s aspoň jednoho poskytovatele certifikační Autority.  

2. Správce vytvoří žadatel přihlašovacích údajů pro službu Key Vault k registraci (a obnovit) certifikáty SSL  

    -   Poskytuje konfigurace se použije k vytvoření objektu Vystavitel zprostředkovatele ve službě key vault  

Další informace o vytváření objektů vystavitele z portálu certifikáty, najdete v článku [Key Vault Certificates blogu](http://aka.ms/kvcertsblog)  

Key Vault umožňuje vytvoření více objektů vystavitele s konfigurací poskytovatele jinou vystavitele. Jakmile je vytvořen objekt vystavitelů, jeho název může být odkazováno v jedné nebo více certifikátů. Odkazování na objekt vystavitelů dává pokyn Key Vault a při žádosti x509 použít konfigurace uvedená v objektu vystavitele certifikátu od poskytovatele certifikační Autority během vytváření certifikátu a obnovení.  

Vystavitel objekty jsou vytvořeny v trezoru a jde použít jenom s certifikáty KV ve stejném trezoru.  

### <a name="certificate-contacts"></a>Kontakty certifikátu

Kontakty certifikátu obsahovat kontaktní informace k zasílání oznámení aktivovaného událostmi, doba platnosti certifikátu. Informace o kontakty sdílí všechny certifikáty ve službě key vault. Všechny zadané kontaktů pro událost pro jakýkoliv certifikát ve službě key vault je odesláno upozornění.  

Pokud zásady certifikátu je nastavena na automatické obnovení, se odešle oznámení o následujících událostech.  

-   Před obnovením certifikátu
-   Po obnovení certifikátu, s informacemi o tom, pokud byl úspěšně obnovený certifikát, nebo pokud došlo k chybě, které vyžadují ruční prodloužení platnosti certifikátu.  

 Když se zásady certifikátu, který je nastaven na ručně obnovit (pouze pro e-mailu), jsou oznámení odesílána až nastane čas na obnovení certifikátu.  

### <a name="certificate-access-control"></a>Řízení přístupu na certifikát

 Řízení přístupu pro certifikáty je spravovaný službou Key Vault a poskytuje služby Key Vault, která obsahuje tyto certifikáty. Zásada řízení přístupu pro certifikáty se liší od zásad řízení přístupu pro klíče a tajné kódy ve stejné službě Key Vault. Uživatelé mohou vytvořit jeden nebo více trezorů pro uložení certifikáty pro zajištění odpovídající segmentace scénář a správu certifikátů.  

 Na základě na objekt v položky řízení přístupu tajné klíče v trezoru klíčů a úzce zrcadlení přípustné tajného kódu objektu operace lze použít následující oprávnění:  

- Oprávnění pro operace správy certifikátů
  - *získat*: získání aktuální verze certifikátu nebo jakoukoli verzi nástroje certifikát 
  - *seznam*: seznam aktuálních certifikátů nebo verze certifikátu  
  - *Aktualizovat*: aktualizace certifikátu
  - *vytvoření*: vytvořit certifikát služby Key Vault
  - *Importovat*: Import certifikátu materiál do certifikátem Key Vaultu
  - *Odstranit*: Odstraňte certifikát, své zásady a všechny jeho verze  
  - *Obnovit*: obnovení odstraněných certifikátu
  - *Zálohování*: zálohování certifikátu ve službě key vault
  - *Obnovit*: obnovení certifikátu zálohovaná do trezoru klíčů
  - *managecontacts*: Správa služby Key Vault kontakty certifikátu  
  - *manageissuers*: Správa služby Key Vault autority/vystavitelů certifikátů
  - *getissuers*: získání certifikátu autority/vystavitele
  - *listissuers*: seznam certifikátu autority/vystavitele  
  - *setissuers*: vytvoření nebo aktualizace trezoru klíčů certifikátu autority/vystavitelů  
  - *deleteissuers*: odstranit certifikát služby Key Vault autority/vystavitelů  
 
- Oprávnění u privilegovaných operací
  - *Vyprázdnit*: Vymazat (trvale odstranit) certifikát odstranil

Další informace najdete v tématu [operace v referenci rozhraní REST API služby Key Vault s certifikáty](/rest/api/keyvault). Informace o vytvářejí se oprávnění najdete v tématu [trezory - vytvořit nebo aktualizovat](/rest/api/keyvault/vaults/createorupdate) a [trezorů – aktualizace zásad přístupu](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="azure-storage-account-key-management"></a>Azure správu klíčů účtu úložiště

Key Vault můžete spravovat klíče účtu úložiště Azure:

- Key Vault interně, můžete zobrazit seznam klíčů (sync) pomocí účtu služby Azure storage. 
- Key Vault obnoví (obmění) klíče pravidelně.
- Hodnoty klíče se nikdy vrátí odpověď na volajícího.
- Key Vault slouží ke správě klíčů účtů úložiště a klasických účtů úložiště.

Další informace najdete v tématu [klíčů k účtu úložiště Azure Key Vault](key-vault-ovw-storage-keys.md)

### <a name="storage-account-access-control"></a>Řízení přístupu k účtu úložiště

Tato oprávnění lze použít při autorizaci uživatele nebo instančního objektu aplikace k provádění operací na spravovaný účet úložiště:  

- Oprávnění pro spravovaný účet úložiště a definice SaS operace
  - *získat*: získá informace o účtu úložiště 
  - *seznam*: seznam účtů úložiště spravované službou Key Vault
  - *Aktualizovat*: aktualizace účtu úložiště
  - *Odstranit*: odstranění účtu úložiště  
  - *Obnovit*: obnovení odstraněného účtu
  - *Zálohování*: účet úložiště pro zálohování
  - *Obnovit*: obnovení účtu úložiště zálohovaných do služby Key Vault
  - *Nastavte*: vytvořit nebo aktualizovat účet úložiště
  - *regeneratekey*: znovu vygenerovat zadanou hodnotou klíče účtu úložiště
  - *getsas*: získání informací o definice SAS pro účet úložiště
  - *listsas*: seznam úložiště definice SAS pro účet úložiště
  - *deletesas*: odstranění definice SAS z účtu úložiště
  - *setsas*: vytvořit nebo aktualizovat nové SAS definice/atributy pro účet úložiště

- Oprávnění u privilegovaných operací
  - *Vyprázdnit*: Vymazat (trvale odstranit) spravovaný účet úložiště

Další informace najdete v tématu [operace účtů úložiště v referenci rozhraní REST API služby Key Vault](/rest/api/keyvault). Informace o vytvářejí se oprávnění najdete v tématu [trezory - vytvořit nebo aktualizovat](/rest/api/keyvault/vaults/createorupdate) a [trezorů – aktualizace zásad přístupu](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="see-also"></a>Viz také

- [Ověřování, požadavky a odpovědi](authentication-requests-and-responses.md)
- [Verze služby Key Vault](key-vault-versions.md)
- [Příručka pro vývojáře trezor klíčů](/azure/key-vault/key-vault-developers-guide)
