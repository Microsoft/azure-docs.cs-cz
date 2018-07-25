---
title: Informace o klíčích, tajných kódů a certifikátů
description: Přehled rozhraní REST a KV podrobnosti pro vývojáře
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
ms.openlocfilehash: 77675b3c0b2ed9fcdb923c92638384d215bddc40
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972396"
---
# <a name="about-keys-secrets-and-certificates"></a>Informace o klíčích, tajných kódů a certifikátů
Služba Azure Key Vault umožňuje ukládat a používat kryptografických klíčů v rámci prostředí Microsoft Azure. Key Vault podporuje více typy klíčů a algoritmy a umožňuje použití z moduly hardwarového zabezpečení (HSM) pro vysoká hodnota klíče. Kromě toho služby Key Vault umožňuje bezpečné ukládání tajných klíčů. Tajné klíče jsou objekty octet omezenou velikost se žádné specifické sémantiku. Key Vault podporuje také certifikáty, které jsou vytvořené s využitím klíče a tajné kódy a přidat funkci automatického obnovení.

Další obecné informace o službě Azure Key Vault najdete v tématu [co je Azure Key Vault?](/azure/key-vault/key-vault-whatis)

**Obecné podrobnosti o službě Key Vault**

-   [Podpora standardů](about-keys-secrets-and-certificates.md#BKMK_Standards)
-   [Datové typy](about-keys-secrets-and-certificates.md#BKMK_DataTypes)  
-   [Objekty, identifikátory a správy verzí](about-keys-secrets-and-certificates.md#BKMK_ObjId)  

**Informace o klíči**

-   [Klíče a typy klíčů](about-keys-secrets-and-certificates.md#BKMK_KeyTypes)  
-   [Algoritmy RSA](about-keys-secrets-and-certificates.md#BKMK_RSAAlgorithms)  
-   [Algoritmy RSA HSM](about-keys-secrets-and-certificates.md#BKMK_RSA-HSMAlgorithms)  
-   [Ochrana kryptografických](about-keys-secrets-and-certificates.md#BKMK_Cryptographic)
-   [Klíčové operace](about-keys-secrets-and-certificates.md#BKMK_KeyOperations)  
-   [Klíčové atributy](about-keys-secrets-and-certificates.md#BKMK_KeyAttributes)  
-   [Klíče značek](about-keys-secrets-and-certificates.md#BKMK_Keytags)  

**O tajných kódů** 

-   [Práce s tajnými kódy](about-keys-secrets-and-certificates.md#BKMK_WorkingWithSecrets)  
-   [Atributy tajného kódu](about-keys-secrets-and-certificates.md#BKMK_SecretAttrs)  
-   [Značky tajného kódu](about-keys-secrets-and-certificates.md#BKMK_SecretTags)  
-   [Řízení přístupu k tajným klíčům](about-keys-secrets-and-certificates.md#BKMK_SecretAccessControl)  

**O certifikátech**

-   [Složení certifikátu](#BKMK_CompositionOfCertificate)  
-   [Certifikát atributy a značky](#BKMK_CertificateAttributesAndTags)  
-   [Zásady certifikátů](#BKMK_CertificatePolicy)  
-   [Vystavitel certifikátu](#BKMK_CertificateIssuer)  
-   [Kontakty certifikátu](#BKMK_CertificateContacts)  
-   [Řízení přístupu na certifikát](#BKMK_CertificateAccessControl)  

--

## <a name="key-vault-general-details"></a>Obecné podrobnosti o službě Key Vault

Následující části nabízí obecné informace napříč implementaci služby Azure Key Vault.

###  <a name="BKMK_Standards"></a> Podpora standardů

Zápis JSON (JavaScript Object) a specifikace jazyka JavaScript objekt podepisování a šifrování (blog JOSE) jsou důležité informace.  

-   [Webového klíče JSON (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [Šifrování webové JSON (pomocí JWE.)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON Web algoritmy (DŽWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON Web podpis (JWS)](http://tools.ietf.org/html/draft-ietf-jose-json-web-signature)  

### <a name="BKMK_DataTypes"></a> datové typy

Odkazovat [JOSE specifikace](#BKMK_Standards) pro příslušné datové typy klíčů, šifrování a podepisování.  

-   **algoritmus** – podporované algoritmus klíče operace, například RSA1_5  
-   **Hodnota šifrovaného textu** -šifer oktety text, kódované pomocí Base64URL  
-   **Hodnota ověřování algoritmem Digest** -výstupu algoritmu hash kódovaný pomocí Base64URL  
-   **Typ klíče** – jeden z podporovaných typů klíčů, třeba RSA.  
-   **Hodnota prostého textu** -oktety ve formátu prostého textu, kódované pomocí Base64URL  
-   **hodnota podpisu** – výstupní algoritmu podpisu kódovaný pomocí Base64URL  
-   **base64URL** – Base64URL [RFC4648] kódování binární hodnota  
-   **Logická** – true nebo false  
-   **Identita** – identit z Azure Active Directory (AAD).  
-   **IntDate** – Desítková hodnota JSON představující počet sekund od 1970-01-01T0:0:0Z UTC až do zadaného data a času UTC. Najdete v článku [RFC3339] pro podrobné informace o datu a času v obecných a standardem UTC zejména.  

###  <a name="BKMK_ObjId"></a> Objekty, identifikátory a správy verzí

Objekty uložené ve službě Azure Key Vault zachovat verze pokaždé, když je vytvořena nová instance objektu, a každá verze obsahuje jedinečný identifikátor a adresu URL. Při prvním vytvoření objektu, je uvedena verze jedinečný identifikátor a je označen jako aktuální verze objektu. Vytvoření nové instance se stejným názvem objektu poskytuje nový objekt verze jedinečný identifikátor a způsobí, že se aktuální verzi.  

Objekty ve službě Azure Key Vault se dají řešit pomocí aktuální identifikátor nebo identifikátor konkrétní verze. Například s ohledem klíč s názvem "MasterKey", provádění operací s aktuální identifikátor způsobí, že systém použít nejnovější dostupnou verzi. Provádění operací s identifikátorem specifické pro verzi způsobí, že systém na tuto konkrétní verzi objektu.  

Objekty se jednoznačně identifikují v rámci služby Azure Key Vault pomocí adresy URL tak, aby žádné dva objekty v systému, bez ohledu na to, zeměpisné polohy, mají stejnou adresu URL. Úplnou adresu URL na objekt, se nazývá identifikátor objektu a skládá se z část předponu, která identifikuje Key Vault, typ objektu, název objektu a Object Version zadaná uživatelem. Název objektu je velká a malá písmena a neměnné. Identifikátory, které neobsahují verze objektu se označují jako základ identifikátory.  

Další informace najdete v tématu [ověřování, požadavky a odpovědi](authentication-requests-and-responses.md)

Identifikátor objektu má následující obecné formát:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Kde:  

|||  
|-|-|  
|`keyvault-name`|Název pro trezor klíčů ve službě Microsoft Azure Key Vault.<br /><br /> Key Vault názvy jsou vybrané uživatelem a globálně jedinečný.<br /><br /> Název služby Key Vault musí být řetězec 3 až 24 znaků v délka obsahující pouze (0-9, a – z, A-Z a -).|  
|`object-type`|Typ objektu, "klíče" nebo "tajné".|  
|`object-name`|`object-name` Je k dispozici uživatelské jméno a musí být jedinečné v rámci služby Key Vault. Název musí být řetězec 1 – 127 znaků obsahující pouze 0-9, a – z, A-Z a -.|  
|`object-version`|`object-version` Je generována, identifikátor řetězce 32 znaků, který je volitelně používat k adresování jedinečnou verzi objektu.|  

## <a name="key-vault-keys"></a>Klíče služby Key Vault

###  <a name="BKMK_KeyTypes"></a> Klíče a typy klíčů

Kryptografické klíče ve službě Azure Key Vault jsou reprezentovány ve formě objektů webového klíče JSON [JWK]. Základní specifikace JWK/DŽWA jsou také rozšířeny, aby povolte klíče typy, které jsou jedinečné pro implementaci služby Azure Key Vault, třeba import klíče do Azure Key Vault pomocí modulu hardwarového zabezpečení balení konkrétního dodavatele (Thales) povolit zabezpečený přenos sady klíčů, například že může se použít jenom v modulů HSM služby Azure Key Vault.  

Počáteční verze služby Azure Key Vault podporuje pouze; klíče RSA budoucí verze může podporovat další typy klíčů, jako jsou symetrické a eliptické křivky.  

-   **RSA**: 2048bitový klíč RSA. Toto je "text soft" klíč, který se zpracuje v softwaru ve službě Key Vault, ale je uloženy v zašifrované podobě v klidovém stavu pomocí systému klíč, který je v modulu hardwarového zabezpečení. Klienti mohou importovat existující klíč RSA nebo požádat o Azure Key Vault generovat jeden.  
-   **RSA HSM**: klíč RSA, která jsou zpracovávána v modulu hardwarového zabezpečení. V jednom z Azure Key Vault HSM zabezpečení světů jsou chráněné klíče RSA modulu hardwarového zabezpečení (je architektury Security World podle zeměpisné oblasti aby se zachovala izolace). Klienti mohou importovat klíč RSA se obnovitelného formuláře nebo tak, že vyexportujete z kompatibilní zařízení HSM, nebo požádat o Azure Key Vault generovat jeden. Tento typ klíče přidá atribut T JWK získat pro přenesení klíče HSM.  

     Další informace o zeměpisných hranic najdete v tématu [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/privacy/)  

###  <a name="BKMK_RSAAlgorithms"></a> Algoritmy RSA  
 Podporuje následující identifikátory algoritmus klíče RSA ve službě Azure Key Vault.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY A UNWRAPKEY, ŠIFROVÁNÍ/DEŠIFROVÁNÍ

-   **RSA1_5** -šifrování klíče RSAES V1_5 PKCS1 [RFC3447]  
-   **RSA OAEP** – RSAES optimální asymetrického šifrování odsazení (OAEP) [RFC3447], pomocí zadané podle RFC. v části podle A.2.1 3447 výchozí parametry. Tyto výchozí parametry jsou pomocí funkce hash SHA-1 a funkce generování maska MGF1 pomocí algoritmu SHA-1.  

#### <a name="signverify"></a>/ OVĚŘENÍ

-   **RS256** – RSASSA-PKCS-v1_5 pomocí algoritmu SHA-256. Hodnotu digest aplikace zadán musí být vypočítán pomocí algoritmu SHA-256 a musí mít délku 32 bajtů.  
-   **RS384** – RSASSA-PKCS-v1_5 pomocí algoritmu SHA-384. Hodnota ověřování algoritmem digest aplikace zadán musí být vypočítán pomocí algoritmu SHA-384 a musí být aspoň 48 bajtů v délce.  
-   **RS512** – RSASSA-PKCS-v1_5 pomocí algoritmu SHA-512. Hodnotu digest aplikace zadán musí být vypočítán pomocí algoritmu SHA-512 a musí mít délku 64 bajtů.  
-   **RSNULL** – viz [RFC2437] specializované případu použití k povolení určitých scénářích TLS.  

###  <a name="BKMK_RSA-HSMAlgorithms"></a> Algoritmy RSA HSM  
Podporuje následující identifikátory algoritmus klíče RSA HSM ve službě Azure Key Vault.  

### <a name="BKMK_Cryptographic"></a> Ochrana kryptografických

Kryptografických modulů, které používá služby Azure Key Vault, zda HSM nebo software, jsou ověřené podle standardu FIPS. Nemusíte dělat nic zvláštního ke spuštění v režimu FIPS. Pokud jste **vytvořit** nebo **importovat** klíče jako chráněné pomocí HSM, zaručeno v ke zpracování uvnitř modulů HSM ověřené na FIPS 140-2 úrovně 2 nebo vyšší. Pokud jste **vytvořit** nebo **importovat** klíče jako chráněnými softwarem a jejich zpracování uvnitř kryptografických modulů ověřené podle standardu FIPS 140-2 úrovně 1 nebo novější. Další informace najdete v tématu [klíče a typy klíčů](about-keys-secrets-and-certificates.md#BKMK_KeyTypes).

#### <a name="wrapunwrap-encryptdecrypt"></a>ZABALIT NEBO ROZBALIT, ŠIFROVÁNÍ/DEŠIFROVÁNÍ

-   **RSA1_5** -šifrování klíče RSAES V1_5 PKCS1 [RFC3447].  
-   **RSA OAEP** – RSAES optimální asymetrického šifrování odsazení (OAEP) [RFC3447], pomocí zadané podle RFC. v části podle A.2.1 3447 výchozí parametry. Tyto výchozí parametry jsou pomocí funkce hash SHA-1 a funkce generování maska MGF1 pomocí algoritmu SHA-1.  

 #### <a name="signverify"></a>/ OVĚŘENÍ  

-   **RS256** – RSASSA-PKCS-v1_5 pomocí algoritmu SHA-256. Hodnotu digest aplikace zadán musí být vypočítán pomocí algoritmu SHA-256 a musí mít délku 32 bajtů.  
-   **RS384** – RSASSA-PKCS-v1_5 pomocí algoritmu SHA-384. Hodnota ověřování algoritmem digest aplikace zadán musí být vypočítán pomocí algoritmu SHA-384 a musí být aspoň 48 bajtů v délce.  
-   **RS512** – RSASSA-PKCS-v1_5 pomocí algoritmu SHA-512. Hodnotu digest aplikace zadán musí být vypočítán pomocí algoritmu SHA-512 a musí mít délku 64 bajtů.  
-   RSNULL: Viz [RFC2437] specializované případu použití k povolení určitých scénářích TLS.  

###  <a name="BKMK_KeyOperations"></a> Klíčové operace

Azure Key Vault podporuje následující operace na klíčové objekty:  

-   **Vytvoření**: umožňuje klientovi vytvoření klíče ve službě Azure Key Vault. Hodnota klíče je generována přes Azure Key Vault a ukládají a neuvolní do klienta. Asymetrické (a v budoucnu eliptické křivky a Symmetric) klíče mohou být vytvořeny ve službě Azure Key Vault.  
-   **Importovat**: umožňuje klientovi importovat existující klíč do služby Azure Key Vault. Asymetrické (a v budoucnu eliptické křivky a Symmetric) klíče mohou být naimportovány do služby Azure Key Vault pomocí celou řadou různých balení metod v rámci JWK konstrukce.  
-   **Aktualizace**: umožňuje klientovi s dostatečnými oprávněními k úpravě metadat (klíčové atributy) přidružené k dříve uložené ve službě Azure Key Vault klíč.  
-   **Odstranit**: umožňuje klientovi s dostatečnými oprávněními k odstranění klíče z Azure Key Vault.  
-   **Seznam**: umožňuje klientovi seznam všech klíčů v dané služby Azure Key Vault.  
-   **Seznam verzí**: umožňuje klientovi seznam všech verzí daného klíče v dané služby Azure Key Vault.  
-   **Získat**: umožňuje klientovi načíst veřejnou částí daného klíče do služby Azure Key Vault.  
-   **Zálohování**: Exportuje klíče ve formě chráněné.  
-   **Obnovit**: importovat dříve zálohovaná klíč.  

Další informace najdete v tématu [klíče operace](/rest/api/keyvault/key-operations).  

Po vytvoření klíče ve službě Azure Key Vault může následující kryptografické operace provádějí pomocí klíče:  

-   **Přihlášení a ověřte, zda**: výhradně, tato operace je "hodnota hash podpisu" nebo "ověřit", protože Azure Key Vault nepodporuje hashování obsahu jako součást vytváření podpis. Aplikace by měla hash data, která mají být podepsané místně a poté požádat o Azure Key Vault pro přihlášení-the-hash. Ověření hodnoty hash podepsaný držitelem je podporovaný jako pohodlí operace pro aplikace, které nemusí mít přístup k [veřejná] materiál klíče; Společnost Microsoft, doporučujeme pro zajištění nejlepšího výkonu aplikace, ověřte, že operace se provádějí místně.  
-   **Šifrování klíče / obtékání**: klíč uložený ve službě Azure Key Vault slouží k ochraně klíče jiného, obvykle symetrického obsahu šifrovacího klíče (CEK). Po asymetrického klíče ve službě Azure Key Vault se používá šifrování pomocí klíče, třeba jsou ekvivalentní k šifrování a DEŠIFROVÁNÍ RSA OAEP a operace, WRAPKEY a UNWRAPKEY. Po symetrický klíč ve službě Azure Key Vault se používá klíč obtékání; například AES KW. WRAPKEY operace je podporována v zájmu usnadnění práce pro aplikace, které nemusí mít přístup k [veřejná] materiál klíče; doporučuje se, že pro zajištění nejlepšího výkonu aplikace, WRAPKEY operace se provádějí místně.  
-   **Šifrování a dešifrování**: klíč uložený ve službě Azure Key Vault slouží k šifrování nebo dešifrování jeden blok dat, jejíž velikost se zakládá na typ klíče a vybrané šifrovací algoritmus. Pro usnadnění práce pro aplikace, které nemusí mít přístup k [veřejná] materiál klíče; je zadaná operace šifrování Dále je doporučeno, pro zajištění nejlepšího výkonu aplikace, šifrovat operace provádět místně.  

Během operace je ekvivalentní k šifrování a DEŠIFROVÁNÍ se může zdát nadbytečný WRAPKEY a UNWRAPKEY pomocí asymetrických klíčů, použijte distinct operací se považuje za důležité poskytnout sémantické a povolení oddělení těchto operací a konzistence Když jiné typy klíčů jsou podporovány službou.  

Služba Azure Key Vault nepodporuje operace EXPORTU: po zřízení klíčem v systému není možné extrahovat nebo změnit jeho klíče.  Ale uživatele služby Azure Key Vault, kdo může vyžadovat svůj klíč pro ostatní případy použití nebo po odstranění ve službě Azure Key Vault, může použít k exportu/importu klíče ve formě chráněné operace zálohování a obnovení. Klíče vytvořené operací zálohování nejsou použitelné mimo Azure Key Vault. Alternativně se operace IMPORTU lze proti více instancí služby Azure Key Vault.  

Uživatelé mohou omezit všechny kryptografické operace, které podporuje Azure Key Vault na základě za klíč pomocí vlastnosti key_ops JWK objektu.  

Další informace o objektech JWK, naleznete v tématu [JSON Web klíč (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key).  

###  <a name="BKMK_KeyAttributes"></a> Klíčové atributy

Kromě materiál klíče je možné zadat následující atributy. V požadavku JSON, atributy – klíčové slovo a složené závorky ' {""} ", jsou požadovány, i když nejsou žádné atributy určené.  

- *povolené*: logická hodnota, nepovinné, výchozí hodnota je **true**. Určuje, zda je klávesa aktivní a použitelný pro kryptografické operace. *Povolené* atribut se používá ve spojení s *nbf* a *exp*. Při operaci dochází mezi *nbf* a *exp*, se budou jenom povoleny Pokud *povolené* je nastavena na **true**. Operace mimo *nbf* / *exp* okno automaticky jsou zakázané, s výjimkou určitých typů operace v rámci [konkrétní podmínky](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops).
- *NBF*: IntDate, volitelné, výchozí hodnota je teď. *Nbf* (ne před) atribut určuje dobu, před kterým musí klíč není používán pro kryptografické operace, s výjimkou určitých typů operace v rámci [konkrétní podmínky](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops). Zpracování *nbf* atribut vyžaduje, že aktuální datum a čas musí nastat po nebo rovno nikoli-před podle data a času *nbf* atribut. Azure Key Vault může poskytnout pro některé malé volnost, obvykle ne více než několik minut, aby se zohlednily hodiny zkosení. Hodnotou musí být číslo obsahující hodnotu IntDate.  
- *Exp*: IntDate, volitelné, výchozí hodnota je "navždy". *Exp* atribut (čas vypršení platnosti) označuje čas vypršení platnosti nebo později, který klíč nesmí použít pro kryptografické operace, s výjimkou určitých typů operace v rámci [konkrétní podmínky](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops). Zpracování *exp* atribut vyžaduje, že aktuální datum a čas musí být před datum a čas vypršení platnosti uvedené v *exp* atribut. Azure Key Vault může poskytnout pro některé malé volnost, obvykle ne více než několik minut, aby se zohlednily hodiny zkosení. Hodnotou musí být číslo obsahující hodnotu IntDate.  

Existují další atributy jen pro čtení, které jsou zahrnuty v odpovědi, která zahrnuje klíčové atributy:  

- *vytvoření*: IntDate volitelné. *Vytvořili* atribut označuje, kdy byla vytvořena tato verze klíče. Je tato hodnota null pro klíče vytvořené před přidáním tohoto atributu. Hodnotou musí být číslo obsahující hodnotu IntDate.  
- *Aktualizovat*: IntDate volitelné. *Aktualizovat* atribut označuje, kdy došlo k aktualizaci této verze klíče. Je tato hodnota null pro klíče, které byly aktualizovány naposledy před přidáním tohoto atributu. Hodnotou musí být číslo obsahující hodnotu IntDate.  

Další informace o IntDate a jiné datové typy najdete v tématu [datové typy](about-keys-secrets-and-certificates.md#BKMK_DataTypes)  

#### <a name="BKMK_key-date-time-ctrld-ops"></a> Operace řízené datum a čas

Vypršela platnost a není dosud platný klíčů, tyto mimo *nbf* / *exp* okna, bude fungovat pro **dešifrovat**, **rozbalení** a **ověřte** operací (nevrátí 403, zakázáno). Důvody pro použití není dosud platný stavu je umožnit klíč, který má být testována před použití v produkčním prostředí. Důvody pro použití vypršela platnost je umožnit operace obnovení pro data, která byla vytvořena, když byl platný klíč. Kromě toho můžete zakázat přístup ke klíči pomocí služby Key Vault zásad nebo aktualizací *povolené* klíčový atribut k **false**.

Další informace o datových typech naleznete v tématu, [datové typy](about-keys-secrets-and-certificates.md#BKMK_DataTypes).

Další informace o dalších atributů je to možné, najdete v článku [JSON Web klíč (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key).

### <a name="BKMK_Keytags"></a> Klíče značek

Můžete zadat další metadata specifická pro aplikaci ve formě značek. Azure Key Vault podporuje až 15 značek, z nichž každá může mít 256 znaků názvu a hodnoty 256 znaků.  

>[!Note]
>Značky jsou přečíst volající v případě, že mají *seznamu* nebo *získat* oprávnění pro tento typ objektu; klíče, tajné kódy a certifikáty.

###  <a name="BKMK_KeyAccessControl"></a> Řízení přístupu ke klíčům

Řízení přístupu pro klíče spravované službou Key Vault je zadaná na úrovni, která funguje jako kontejner klíčů služby Key Vault. Existuje zásad řízení přístupu pro klíče, který se liší od zásad řízení přístupu pro tajné kódy ve stejné službě Key Vault. Uživatelé mohou vytvořit jeden nebo více trezorů pro uložení klíče a jsou požadovány pro zajištění odpovídající segmentace scénář a správu klíčů. Řízení přístupu pro klíče je nezávislé na řízení přístupu pro tajné kódy.  

Tato oprávnění lze udělit, na za uživatele / service základem v položky řízení přístupu klíče v trezoru. Tato oprávnění úzce zrcadlí přípustné na klíče objektu operace:  

-   *vytvoření*: vytvářet nové klíče
-   *získat*: Přečtěte si části veřejný klíč a jeho atributy
-   *seznam*: seznam klíčů nebo verze klíč uložený ve službě key vault
-   *Aktualizovat*: aktualizovat atributy pro klíč
-   *Odstranit*: odstranění klíče objektu
-   *znaménko*: použití hodnoty klíč k podepsání Digest
-   *Ověřte*: používá se k ověření přehledu
-   *wrapKey*: používá se k ochraně symetrický klíč
-   *unwrapKey*: použít klíč k odemknutí zabalené symetrické klíče
-   *šifrování*: používá se pro ochranu libovolného pořadí bajtů
-   *dešifrování*: použít klíč k odemknutí pořadí bajtů
-   *Importovat*: Import klíče do trezoru klíčů
-   *Zálohování*: zálohování klíče v trezoru klíčů
-   *Obnovit*: Obnovte zálohovaný klíč do trezoru klíčů
-   *všechny*: všechna oprávnění

## <a name="key-vault-secrets"></a>Tajné klíče služby Key Vault 

###  <a name="BKMK_WorkingWithSecrets"></a> Práce s tajnými kódy

Tajné kódy ve službě Azure Key Vault se octet pořadí s maximální velikostí 25 tisíc bajtů. Služba Azure Key Vault neposkytuje žádné sémantiku tajných kódů; pouze přijímá data, zašifruje a uloží jej vrátí identifikátor tajného kódu "id", který slouží k načtení tajný kód později.  

Pro vysoce citlivá data klienti měli zvážit další vrstvy ochrany pro data, která je uložená ve službě Azure Key Vault; například před šifrováním dat pomocí klíče samostatné ochrany.  

Služba Azure Key Vault podporuje také pole contentType tajnými kódy. Klienti mohou zadat typ obsahu, "contentType", které pomáhají při interpretaci tajných dat při načítání tajného kódu. Maximální délka tohoto pole je 255 znaků. Neexistují žádné předem definované hodnoty. Navrhované využívá jako Nápověda pro interpretaci tajná data. Implementace může například jako tajné kódy ukládat hesla a certifikáty pak toto pole použít k označení, která. Neexistují žádné předdefinované hodnoty.  

###  <a name="BKMK_SecretAttrs"></a> Atributy tajného kódu

Kromě tajných dat lze zadat následující atributy:  

- *Exp*: IntDate, volitelné, výchozí hodnota je **navždy**. *Exp* atribut (čas vypršení platnosti) označuje čas vypršení platnosti nebo po které nesmí tajná data načíst, s výjimkou [konkrétní situace](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops). Zpracování *exp* atribut vyžaduje, že aktuální datum a čas musí být před datum a čas vypršení platnosti uvedené v *exp* atribut. Azure Key Vault může poskytnout pro některé malé volnost, obvykle ne více než několik minut, aby se zohlednily hodiny zkosení. Hodnotou musí být číslo obsahující hodnotu IntDate.  
- *NBF*: IntDate, volitelné, výchozí hodnota je **nyní**. *Nbf* (ne před) atribut určuje dobu, před kterým musí nebyla načtena tajných dat, s výjimkou [konkrétní situace](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops). Zpracování *nbf* atribut vyžaduje, že aktuální datum a čas musí nastat po nebo rovno nikoli-před podle data a času *nbf* atribut. Azure Key Vault může poskytnout pro některé malé volnost, obvykle ne více než několik minut, aby se zohlednily hodiny zkosení. Hodnotou musí být číslo obsahující hodnotu IntDate.  
- *povolené*: logická hodnota, nepovinné, výchozí hodnota je **true**. Tento atribut určuje, zda mohou být načteny tajná data. Atribut enabled se používá ve spojení s a *exp* dojde operaci mezi a exp, ji bude povoleno pouze pokud je povoleno nastavena na **true**. Operace mimo *nbf* a *exp* okno se automaticky zakázaná, s výjimkou v [konkrétní situace](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops).  

Existují další atributy jen pro čtení, které jsou zahrnuty v odpovědi, která zahrnuje tajný atributy:  

- *vytvoření*: IntDate volitelné. Vytvořený atribut označuje, kdy byla vytvořena tato verze tajného klíče. Je tato hodnota null pro tajné klíče vytvořené před přidáním tohoto atributu. Hodnotou musí být číslo obsahující hodnotu IntDate.  
- *Aktualizovat*: IntDate volitelné. Aktualizovaný atribut označuje, kdy došlo k aktualizaci této verzi tajného klíče. Je tato hodnota null pro tajné kódy, které byly aktualizovány naposledy před přidáním tohoto atributu. Hodnotou musí být číslo obsahující hodnotu IntDate.

#### <a name="BKMK_secret-date-time-ctrld-ops"></a> Operace řízené datum a čas

Tajného **získat** operace bude fungovat pro tajné klíče není dosud platný a jejichž platnost vypršela, mimo *nbf* / *exp* okna. Volání tajného **získat** operaci pro není dosud platný tajný kód, lze použít pro účely testování. Načítání (**získat**ing) vypršela platnost tajného klíče, lze použít pro operace obnovení.

Další informace o datových typech naleznete v tématu, [datové typy](about-keys-secrets-and-certificates.md#BKMK_DataTypes).  

###  <a name="BKMK_SecretAccessControl"></a> Řízení přístupu k tajným klíčům

Řízení přístupu pro tajné klíče spravované ve službě Azure Key Vault je k dispozici na úrovni, která funguje jako kontejner těchto tajných kódů služby Key Vault. Existuje zásad řízení přístupu pro tajné klíče, který se liší od zásad řízení přístupu pro klíče ve stejné službě Key Vault. Uživatelé mohou vytvořit jeden nebo více trezorů pro uložení tajných kódů a jsou potřeba k údržbě scénář odpovídající segmentaci a správu tajných kódů. Řízení přístupu pro tajné klíče jsou nezávislé na řízení přístupu pro klíče.  

Tato oprávnění můžete použít, na základě na objekt v položky řízení přístupu tajné klíče v trezoru a úzce zrcadlení přípustné tajného kódu objektu operace:  

-   *Nastavte*: vytvoření nové tajných kódů  
-   *získat*: čtení tajného klíče  
-   *seznam*: seznam tajných kódů nebo verzí tajného klíče do služby Key Vault  
-   *Odstranit*: odstranění tajného klíče  
-   *všechny*: všechna oprávnění  

Další informace o práci s tajnými kódy, naleznete v tématu [tajných kódů operací](/rest/api/keyvault/secret-operations).  

###  <a name="BKMK_SecretTags"></a> Značky tajného kódu  
Můžete zadat další metadata specifická pro aplikaci ve formě značek. Azure Key Vault podporuje až 15 značek, z nichž každá může mít 256 znaků názvu a hodnoty 256 znaků.  

>[!Note]
>Značky jsou přečíst volající v případě, že mají *seznamu* nebo *získat* oprávnění pro tento typ objektu; klíče, tajné kódy a certifikáty.

## <a name="key-vault-certificates"></a>Certifikátů trezor klíčů

Podpora certifikáty služby Key Vault nabízí pro správu vaší x509 certifikáty a následujícího chování:  

-   Umožňuje vlastníkovi certifikátů k vytvoření certifikátu prostřednictvím procesu vytváření služby Key Vault nebo importu stávajícího certifikátu. To zahrnuje i podepsaný svým držitelem a vygenerovat certifikáty certifikační autority.
-   Umožňuje vlastníkovi certifikát služby Key Vault implementací zabezpečeného úložiště a správy X509 certifikáty bez interakce s materiál privátního klíče.  
-   Umožňuje vlastníkovi certifikát můžete vytvořit zásadu, která přesměruje služby Key Vault pro správu životního cyklu certifikátu.  
-   Umožňuje vlastníkům certifikát lze zadat kontaktní informace pro oznámení o události životního cyklu vypršení platnosti a obnovení certifikátu.  
-   Podporuje automatické obnovení s vybranou vystavitelů - Key Vault partnera X509 certifikát poskytovatele / certifikační autority.

>[!Note]
>Partnerství jiných poskytovatelů nebo autority jsou také povoleny, ale nebude podporovat funkci automatického obnovení.

###  <a name="BKMK_CompositionOfCertificate"></a> Složení certifikátu

Když se vytvoří certifikátem Key Vaultu, se stejným názvem, vytvoří také adresovatelný klíč a tajný klíč služby. Klíč služby Key Vault umožňuje operace klíče a tajného kódu Key Vault umožňuje načíst hodnotu certifikát jako tajný kód. Certifikát služby Key Vault také obsahuje veřejné x509 certifikátu metadat.  

Identifikátor a verzi certifikátů je podobný tomu klíčů a tajných kódů. Konkrétní verzi adresovatelný klíč a tajný kód s verzí certifikát služby Key Vault je k dispozici v odpovědi certifikátu služby Key Vault.
 
![Certifikáty jsou komplexních objektů](media/azure-key-vault.png)

###  <a name="BKMK_CertificateExportableOrNonExportableKey"></a> Exportovatelné nebo jiných exportovatelné klíče

Když se vytvoří certifikátem Key Vaultu, se dá načíst z adresovatelný tajného kódu s privátním klíčem ve formátu PFX nebo PEM Pokud zásady použité k vytvoření certifikátu, že klíč je možné exportovat. Pokud zásady použité k vytvoření certifikátu služby Key Vault klíč bude neexportovatelného, pak privátní klíč není součástí hodnotu, pokud načíst jako tajný kód.  

Adresovatelný klíč se stane relevantnější neexportovatelného KV certifikáty. Operace adresovatelný KV klíč mapování z *keyusage* Zásady certifikátů KV použilo k vytvoření certifikátu KV pole.  

Podporuje dva typy klíče – *RSA* nebo *RSA HSM* pomocí certifikátů. Exportovatelné je povolen pouze v RSA, není podporována RSA modulu hardwarového zabezpečení.  

###  <a name="BKMK_CertificateAttributesAndTags"></a> Certifikát atributy a značky

Kromě metadaty certifikátu, adresovatelný klíč a adresovatelný tajný klíč, certifikátem Key Vaultu taky obsahuje atributy a značky.  

#### <a name="attributes"></a>Atributy

Atributy adresovatelný klíč a tajný klíč vytvořený při vytváření certifikátu KV jsou zrcadleny atributy certifikátu.  

Certifikát služby Key Vault má následující atributy:  

-   *povolené*: logická hodnota, nepovinné, výchozí hodnota je **true**. Tento atribut lze k označení, pokud je možné načíst data certifikátu jako provozuschopného jako klíče nebo tajného kódu. To se používá ve spojení s *nbf* a *exp* dojde operaci mezi *nbf* a *exp*, ji budou pouze povoleny Pokud je povoleno je nastavena na hodnotu true. Operace mimo *nbf* a *exp* okna jsou automaticky zakázány.  

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
> Značky jsou přečíst volající v případě, že mají *seznamu* nebo *získat* oprávnění pro tento typ objektu; klíče, tajné kódy a certifikáty.

###  <a name="BKMK_CertificatePolicy"></a> Zásady certifikátů

Zásady certifikátu obsahuje informace o tom, jak vytvářet a spravovat životní cyklus KV certifikátu. Při importu certifikátu s privátním klíčem do služby key vault, najdete x509 se vytvoří výchozí zásady certifikátu.  

Když KV certifikát je vytvořený z nuly, musí být zadán do služby Key Vault informovat o tom, jak vytvořit tento KV certifikát nebo další verzi certifikát KV zásadu. Po vytvoření zásady, není nutné s po sobě jdoucích vytvoření operací vytvořit další verze KV certifikátů.  

Existuje pouze jedna instance zásady pro všechny verze KV certifikátu.  

Na vysoké úrovni obsahuje následující zásady certifikátu:  

-   Vlastnosti certifikátu X509: obsahuje název subjektu, alternativní názvy subjektu atd. Umožňuje vytvořit x x509 žádost o certifikát.  
-   Vlastnosti klíče: obsahuje typ klíče, délka, exportovatelné klíče a znovu použít pole klíče. Tato pole dáte pokyn, aby služby key vault o tom, jak vygenerovat klíč.  
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

###  <a name="BKMK_CertificateIssuer"></a> Vystavitel certifikátu

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

###  <a name="BKMK_CertificateContacts"></a> Kontakty certifikátu

Kontakty certifikátu obsahovat kontaktní informace k zasílání oznámení aktivovaného událostmi, doba platnosti certifikátu. Informace o kontakty sdílí všechny certifikáty ve službě key vault. Všechny zadané kontaktů pro událost pro jakýkoliv certifikát ve službě key vault je odesláno upozornění.  

Pokud zásady certifikátu je nastavena na automatické obnovení, se odešle oznámení o následujících událostech.  

-   Před obnovením certifikátu
-   Po obnovení certifikátu, s informacemi o tom, pokud byl úspěšně obnovený certifikát, nebo pokud došlo k chybě, které vyžadují ruční prodloužení platnosti certifikátu.  

 Pokud zásady certifikátu nastaven ručně obnovené (e-mailu jenom) a pak oznámení se odešle, když je čas na obnovení certifikátu.  

###  <a name="BKMK_CertificateAccessControl"></a> Řízení přístupu na certifikát

 Řízení přístupu pro certifikáty je spravovaný službou Key Vault a je k dispozici na úrovni služby Key Vault, která funguje jako kontejner těchto certifikátů. Existuje zásad řízení přístupu pro certifikáty, které se liší od zásad řízení přístupu pro klíče a tajné kódy ve stejné službě Key Vault. Uživatelé mohou vytvořit jeden nebo více trezorů pro uložení certifikáty a jsou požadovány pro zajištění odpovídající segmentace scénář a správu certifikátů.  

 Na základě na objekt v položky řízení přístupu tajné klíče v trezoru klíčů a úzce zrcadlení přípustné tajného kódu objektu operace lze použít následující oprávnění:  

-   *získat*: umožňuje získat aktuální certifikát verzi nebo verzi certifikát 
-   *seznam*: umožňuje seznam aktuálních certifikátů nebo verze certifikátu  
-   *Odstranit*: umožňuje odstranit certifikát, své zásady a všechny jeho verze  
-   *vytvoření*: umožňuje vytvoření certifikátu služby Key Vault.  
-   *Importovat*: umožňuje import certifikátu materiál do certifikátů trezor klíč.  
-   *Aktualizovat*: Povolí aktualizace certifikátu.  
-   *manageconnects*: umožňuje správu kontakty certifikátu služby Key Vault  
-   *getissuers*: umožňuje získat vystavitelů certifikátů.  
-   *listissuers*: umožňuje seznam vystavitelů certifikátů.  
-   *setissuers*: umožňuje vytvořit nebo aktualizovat vystavitelů certifikátů služby Key Vault  
-   *deleteissuers*: umožňuje odstranit vystavitelů certifikátů služby Key Vault  
-   *všechny*: uděluje všechna oprávnění  

## <a name="additional-information-for-certificates"></a>Další informace o certifikáty

- [Certifikáty a zásady](/rest/api/keyvault/certificates-and-policies)
- [Vystavitelů certifikátů](/rest/api/keyvault/certificate-issuers)
- [Kontakty certifikátu](/rest/api/keyvault/certificate-contacts)

## <a name="see-also"></a>Viz také

- [Ověřování, požadavky a odpovědi](authentication-requests-and-responses.md)
- [Verze služby Key Vault](key-vault-versions.md)
- [Příručka pro vývojáře trezor klíčů](/azure/key-vault/key-vault-developers-guide)
