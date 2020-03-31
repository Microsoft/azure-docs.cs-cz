---
title: Klíče, tajné klíče a certifikáty azure trezoru klíčů – Azure Key Vault
description: Přehled rozhraní Azure Key Vault REST a podrobnosti o vývojáři klíčů, tajných kódů a certifikátů.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: dd8be482009e067bf9016cc8e351fc42a2db39c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271730"
---
# <a name="about-keys-secrets-and-certificates"></a>Informace o klíčích, tajných kódech a certifikátech

Azure Key Vault umožňuje aplikacím a uživatelům Microsoft Azure ukládat a používat několik typů tajných dat a dat klíče:

- Kryptografické klíče: Podporuje více typů klíčů a algoritmů a umožňuje použití modulů hardwarového zabezpečení (HSM) pro klíče s vysokou hodnotou. 
- Tajné klíče: Poskytuje zabezpečené ukládání tajných kódů, jako jsou hesla a připojovací řetězce databáze.
- Certifikáty: Podporuje certifikáty, které jsou postaveny na klíče a tajných kódů a přidat funkci automatického obnovení.
- Azure Storage: Můžete spravovat klíče účtu Azure Storage za vás. Trezor klíčů může interně vypsat (synchronizovat) klíče pomocí účtu úložiště Azure a pravidelně je regenerovat (otáčet). 

Obecnější informace o trezoru klíčů najdete v tématu [Co je Azure Key Vault?](/azure/key-vault/key-vault-overview)

## <a name="azure-key-vault"></a>Azure Key Vault

Následující části nabízejí obecné informace platné v rámci implementace služby Trezor klíčů.

### <a name="supporting-standards"></a>Podpůrné standardy

Důležitými základními informacemi jsou specifikace JavaScript object notace (JSON) a JavaScript Object Signing and Encryption (JOSE).  

-   [Webový klíč JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)  
-   [Webové šifrování JSON (JWE)](https://tools.ietf.org/html/draft-ietf-jose-json-web-encryption-40)  
-   [Webové algoritmy JSON (JWA)](https://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms-40)  
-   [Webový podpis JSON (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature-41)  

### <a name="data-types"></a>Typy dat

Klíče, šifrování a podepisování naleznete ve specifikacích JOSE pro příslušné datové typy.  

-   **algoritmus** - podporovaný algoritmus pro klíčovou operaci, například RSA1_5  
-   **ciphertext-value** - šifrovací textové oktety, kódované pomocí Base64URL  
-   **digest-value** - výstup algoritmu hash, kódovaný pomocí Base64URL  
-   **typ klíče** - jeden z podporovaných typů klíčů, například RSA (Rivest-Shamir-Adleman).  
-   **hodnota prostého textu** - oktety ve formátu prostého textu, kódované pomocí base64URL  
-   **podpis-hodnota** - výstup podpisového algoritmu, kódovaný pomocí Base64URL  
-   **base64URL** - základní 64URL [RFC4648] kódovaná binární hodnota  
-   **logická** - buď pravdivá nebo nepravdivá  
-   **Identita** – identita ze služby Azure Active Directory (AAD).  
-   **IntDate** - desetinná hodnota JSON představující počet sekund od 1970-01-01T0:0:0Z UTC až do zadaného data a času UTC. Podrobnosti o datu/časech obecně a zejména u UTC viz RFC3339.  

### <a name="objects-identifiers-and-versioning"></a>Objekty, identifikátory a správa verzí

Objekty uložené v trezoru klíčů jsou verzí při každém vytvoření nové instance objektu. Každé verzi je přiřazen jedinečný identifikátor a adresa URL. Při prvním vytvoření objektu je mu přidělen jedinečný identifikátor verze a je označen jako aktuální verze objektu. Vytvoření nové instance se stejným názvem objektu dává novému objektu jedinečný identifikátor verze, což způsobuje, že se stane aktuální verzí.  

Objekty v trezoru klíčů lze adresovat pomocí aktuálního identifikátoru nebo identifikátoru specifického pro verzi. Například vzhledem ke klíči s názvem `MasterKey`, provádění operací s aktuálním identifikátorem způsobí, že systém použije nejnovější dostupnou verzi. Provádění operací s identifikátorem specifickým pro verzi způsobí, že systém použije tuto konkrétní verzi objektu.  

Objekty jsou jednoznačně identifikovány v trezoru klíčů pomocí adresy URL. Žádné dva objekty v systému mají stejnou adresu URL, bez ohledu na geografické umístění. Úplná adresa URL objektu se nazývá Identifikátor objektu. Adresa URL se skládá z předpony, která identifikuje trezor klíčů, typ objektu, uživatelem poskytnutý název objektu a verzi objektu. Název objektu je malá a velká písmena a neměnné. Identifikátory, které neobsahují verzi objektu, se označují jako základní identifikátory.  

Další informace naleznete v [tématu Ověřování, požadavky a odpovědi](authentication-requests-and-responses.md)

Identifikátor objektu má následující obecný formát:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Kde:  

|||  
|-|-|  
|`keyvault-name`|Název trezoru klíčů ve službě Microsoft Azure Key Vault.<br /><br /> Názvy trezorů klíčů jsou vybrány uživatelem a jsou globálně jedinečné.<br /><br /> Název trezoru klíčů musí být řetězec 3-24 znaků, který obsahuje pouze 0-9, a-z, A-Z a -.|  
|`object-type`|Typ objektu, "klíče" nebo "tajné klíče".|  
|`object-name`|A `object-name` je uživatel zadaný název pro a musí být jedinečný v trezoru klíčů. Název musí být řetězec znaků 1-127, který obsahuje pouze 0-9, a-z, A-Z a -.|  
|`object-version`|A `object-version` je systémem generovaný, 32 znakový řetězec identifikátor, který se volitelně používá k adresování jedinečnou verzi objektu.|  

## <a name="key-vault-keys"></a>Klíče trezoru klíčů

### <a name="keys-and-key-types"></a>Klíče a typy klíčů

Kryptografické klíče v trezoru klíčů jsou reprezentovány jako objekty json webového klíče [JWK]. Základní specifikace JWK/JWA jsou také rozšířeny tak, aby umožňovalo typy klíčů jedinečné pro implementaci trezoru klíčů. Například import klíčů pomocí balení specifického pro dodavatele hsm umožňuje bezpečnou přepravu klíčů, které lze použít pouze v hsmech trezoru klíčů.  

- **"Měkké" klíče**: Klíč zpracovaný v softwaru trezorem klíčů, ale je šifrován v klidovém stavu pomocí systémového klíče, který je v objektu hesm. Klienti mohou importovat existující klíč RSA nebo EC (Elliptic Curve) nebo požadovat, aby trezor klíčů byl generován.
- **"Pevné" klíče**: Klíč zpracovaný v modulu hardwarového zabezpečení (Hardware Security Module). Tyto klíče jsou chráněny v jednom ze světů zabezpečení hsm úložiště klíčů (existuje jeden svět zabezpečení na zeměpisné oblasti pro udržení izolace). Klienti mohou importovat klíč RSA nebo ES v měkké formě nebo exportem z kompatibilního zařízení hardwarového zabezpečení. Klienti mohou také požádat trezor klíčů o vygenerování klíče. Tento typ klíče přidá atribut key_hsm do získání JWK pro přenos materiálu klíče HSM.

     Další informace o geografických hranicích najdete v [tématu Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/privacy/)  

Trezor klíčů podporuje pouze klávesy RSA a Elliptic Curve. 

-   **EC**: "Soft" eliptická křivka klíč.
-   **EC-HSM**: "Hard" eliptická křivka klíč.
-   **RSA**: "Soft" RSA klíč.
-   **RSA-HSM**: "Hard" RSA klíč.

Key Vault podporuje RSA klíče o velikostech 2048, 3072 a 4096. Trezor klíčů podporuje typy klíčů elliptic Curve P-256, P-384, P-521 a P-256K (SECP256K1).

### <a name="cryptographic-protection"></a>Kryptografická ochrana

Kryptografické moduly, které trezor klíčů používá, ať už moduly hesm nebo software, jsou ověřeny fips (Federal Information Processing Standards). Nemusíte dělat nic zvláštního pro spuštění v režimu FIPS. Klíče **vytvořené** nebo **importované** jako chráněné pomocí hsm jsou zpracovány uvnitř objektu hsm, ověřené na FIPS 140-2 úroveň 2. Klíče **vytvořené** nebo **importované** jako softwarově chráněné jsou zpracovány uvnitř kryptografických modulů ověřených na FIPS 140-2 Level 1. Další informace naleznete v [tématu Klíče a typy klíčů](#keys-and-key-types).

###  <a name="ec-algorithms"></a>Algoritmy ES
 Následující identifikátory algoritmu jsou podporovány pomocí kláves EC a EC-HSM v trezoru klíčů. 

#### <a name="curve-types"></a>Typy křivek

-   **P-256** - Křivka NIST P-256, definovaná na [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-256K** - Křivka SEC SECP256K1, definovaná na [SEC 2: Doporučené parametry domény eliptické křivky](https://www.secg.org/sec2-v2.pdf).
-   **P-384** - Křivka NIST P-384, definovaná na [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-521** - Křivka NIST P-521, definovaná na [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).

#### <a name="signverify"></a>PODEPSAT/OVĚŘIT

-   **ES256** - ECDSA pro digesty SHA-256 a klíče vytvořené křivkou P-256. Tento algoritmus je popsán na [rfc7518](https://tools.ietf.org/html/rfc7518).
-   **ES256K** - ECDSA pro digesty SHA-256 a klíče vytvořené pomocí křivky P-256K. Tento algoritmus čeká na standardizaci.
-   **ES384** - ECDSA pro digesty SHA-384 a klíče vytvořené křivkou P-384. Tento algoritmus je popsán na [rfc7518](https://tools.ietf.org/html/rfc7518).
-   **ES512** - ECDSA pro digesty SHA-512 a klíče vytvořené pomocí křivky P-521. Tento algoritmus je popsán na [rfc7518](https://tools.ietf.org/html/rfc7518).


###  <a name="rsa-algorithms"></a>RSA algoritmy  
 Následující identifikátory algoritmu jsou podporovány pomocí klíčů RSA a RSA-HSM v trezoru klíčů.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, ŠIFROVAT/DEŠIFROVAT

-   **RSA1_5** - šifrování klíče RSAES-PKCS1-V1_5 [RFC3447]  
-   **RSA-OAEP** - RSAES používající optimální asymetrické šifrovací odsazení (OAEP) [RFC3447], s výchozími parametry specifikovanými rfc 3447 v oddíle A.2.1. Tyto výchozí parametry používají funkci hash SHA-1 a funkci generování masky MGF1 s SHA-1.  

#### <a name="signverify"></a>PODEPSAT/OVĚŘIT

-   **PS256** - RSASSA-PSS pomocí SHA-256 a MGF1 s SHA-256, jak je popsáno v [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS384** - RSASSA-PSS pomocí SHA-384 a MGF1 s SHA-384, jak je popsáno v [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS512** - RSASSA-PSS pomocí SHA-512 a MGF1 s SHA-512, jak je popsáno v [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **RS256** - RSASSA-PKCS-v1_5 pomocí SHA-256. Zadaná hodnota digest u aplikace musí být vypočítána pomocí SHA-256 a musí mít délku 32 bajtů.  
-   **RS384** - RSASSA-PKCS-v1_5 pomocí SHA-384. Zadaná hodnota digest u aplikace musí být vypočítána pomocí SHA-384 a musí mít délku 48 bajtů.  
-   **RS512** - RSASSA-PKCS-v1_5 pomocí SHA-512. Zadaná hodnota digest u aplikace musí být vypočítána pomocí SHA-512 a musí mít délku 64 bajtů.  
-   **RSNULL** - Viz [RFC2437], specializovaný případ použití povolit určité scénáře TLS.  

###  <a name="key-operations"></a>Klíčové operace

Trezor klíčů podporuje následující operace s klíčovými objekty:  

-   **Vytvořit**: Umožňuje klientovi vytvořit klíč v trezoru klíčů. Hodnota klíče je generována trezoru klíčů a uložena a není uvolněna klientovi. V trezoru klíčů mohou být vytvořeny asymetrické klíče.  
-   **Import**: Umožňuje klientovi importovat existující klíč do trezoru klíčů. Asymetrické klíče mohou být importovány do trezoru klíčů pomocí několika různých metod balení v rámci konstrukce JWK. 
-   **Aktualizace**: Umožňuje klientovi s dostatečnými oprávněními upravovat metadata (klíčové atributy) přidružené ke klíči, který byl dříve uložen v trezoru klíčů.  
-   **Odstranit**: Umožňuje klientovi s dostatečnými oprávněními odstranit klíč z trezoru klíčů.  
-   **Seznam**: Umožňuje klientovi vypsat všechny klíče v daném trezoru klíčů.  
-   **Seznam verzí**: Umožňuje klientovi vypsat všechny verze daného klíče v daném trezoru klíčů.  
-   **Get**: Umožňuje klientovi načíst veřejné části daného klíče v trezoru klíčů.  
-   **Zálohování**: Exportuje klíč v chráněné matné podobě.  
-   **Obnovení**: Importuje dříve zálohovaný klíč.  

Další informace naleznete [v tématu Klíčové operace v odkazu rozhraní REST API úložiště klíčů](/rest/api/keyvault).  

Po vytvoření klíče v trezoru klíčů lze pomocí klíče provádět následující kryptografické operace:  

-   **Podepsat a ověřit**: Přísně je tato operace "podepsat hash" nebo "ověřit hash", protože trezor klíčů nepodporuje hašování obsahu jako součást vytváření podpisu. Aplikace by měly hašovat data, která mají být podepsána místně, a poté požadovat, aby trezor klíčů znak hash. Ověření podepsaných hash je podporováno jako pohodlná operace pro aplikace, které nemusí mít přístup k materiálu klíče [public]. Chcete-li dosáhnout nejlepšího výkonu aplikace, ověřte, zda jsou operace prováděny místně.  
-   **Šifrování / obtékání**klíčů : Klíč uložený v trezoru klíčů lze použít k ochraně jiného klíče, obvykle symetrického šifrovacího klíče obsahu (CEK). Pokud je klíč v trezoru klíčů asymetrický, použije se šifrování klíčů. Například operace RSA-OAEP a WRAPKEY/UNWRAPKEY jsou ekvivalentní encrypt/decrypt. Pokud je klíč v trezoru klíčů symetrický, použije se obtékání klíčů. Například AES-KW. Operace WRAPKEY je podporována jako pohodlí pro aplikace, které nemusí mít přístup k materiálu klíče [public]. Pro nejlepší výkon aplikace wrapkey operace by měly být prováděny místně.  
-   **Šifrovat a dešifrovat**: Klíč uložený v trezoru klíčů může být použit k šifrování nebo dešifrování jednoho bloku dat. Velikost bloku je určena typem klíče a vybraným šifrovacím algoritmem. Operace Šifrování je k dispozici pro pohodlí pro aplikace, které nemusí mít přístup k [veřejné] klíč materiálu. Pro nejlepší výkon aplikace šifrovat operace by měly být prováděny místně.  

Zatímco WRAPKEY/UNWRAPKEY pomocí asymetrických klíčů se může zdát nadbytečné (protože operace je ekvivalentní ENCRYPT/DECRYPT), použití různých operací je důležité. Rozlišení poskytuje sémantické a autorizace oddělení těchto operací a konzistence, pokud jsou podporovány jiné typy klíčů služby.  

Trezor klíčů nepodporuje operace EXPORT. Jakmile je klíč zřízen v systému, nelze jej extrahovat nebo upravit jeho materiál. Uživatelé trezoru klíčů však může vyžadovat jejich klíč pro jiné případy použití, například po jeho odstranění. V takovém případě mohou použít operace ZÁLOHOVÁNÍ a OBNOVENÍ k exportu nebo importu klíče v chráněném formuláři. Klíče vytvořené operací zálohování nejsou použitelné mimo trezor klíčů. Alternativně import operace může být použita proti více instancí trezoru klíčů.  

Uživatelé mohou omezit všechny kryptografické operace, které podporuje trezor klíčů na základě klíče pomocí key_ops vlastnost iWK objektu.  

Další informace o objektech JWK naleznete v tématu [JSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).  

###  <a name="key-attributes"></a>Klíčové atributy

Vedle nastavení týkajících se klíčů samotných je možné ještě zadat následující atributy. V požadavku JSON jsou vyžadovány klíčové slovo atributy a složená závorky {' '}, i když nejsou zadány žádné atributy.  

- *povoleno*: logická, volitelná, výchozí hodnota **je true**. Určuje, zda je klíč povolen a použitelný pro kryptografické operace. Povolený *enabled* atribut se používá ve spojení s *nbf* a *exp*. Dojde-li k operaci mezi *nbf* a *exp*, bude povolena pouze v případě, že je *povolena* na **hodnotu true**. Operace mimo okno*exp* *nbf* / jsou automaticky zakázány, s výjimkou určitých typů operací [za určitých podmínek](#date-time-controlled-operations).
- *nbf*: IntDate, volitelné, výchozí je nyní. Atribut *nbf* (ne dříve) určuje dobu, před kterou nesmí být klíč použit pro kryptografické operace, s výjimkou určitých typů operací [za určitých podmínek](#date-time-controlled-operations). Zpracování atributu *nbf* vyžaduje, aby aktuální datum a čas musí být po nebo rovno nepřed datu a času uvedenému v atributu *nbf.* Trezor klíčů může poskytnout nějakou malou volnost, obvykle ne více než několik minut, aby se zohlednilo zkosení hodin. Jeho hodnota musí být číslo obsahující hodnotu IntDate.  
- *exp*: IntDate, volitelné, výchozí hodnota je "navždy". Atribut *exp* (expirační doba) určuje dobu vypršení platnosti nebo po které nesmí být klíč použit pro kryptografickou operaci, s výjimkou určitých typů operací [za určitých podmínek](#date-time-controlled-operations). Zpracování *exp* atribut vyžaduje, aby aktuální datum a čas musí být před datem a časem vypršení platnosti uvedených v *exp* atributu. Trezor klíčů může poskytnout některé malé volnost, obvykle ne více než několik minut, na účet hodiny zkosení. Jeho hodnota musí být číslo obsahující hodnotu IntDate.  

Existují další atributy jen pro čtení, které jsou zahrnuty v odpovědi, která obsahuje klíčové atributy:  

- *vytvořeno*: IntDate, volitelné. Vytvořený *created* atribut označuje, kdy byla tato verze klíče vytvořena. Hodnota je null pro klíče vytvořené před přidáním tohoto atributu. Jeho hodnota musí být číslo obsahující hodnotu IntDate.  
- *aktualizováno*: IntDate, volitelné. *Aktualizovaný* atribut označuje, kdy byla tato verze klíče aktualizována. Hodnota je null pro klíče, které byly naposledy aktualizovány před přidáním tohoto atributu. Jeho hodnota musí být číslo obsahující hodnotu IntDate.  

Další informace o IntDate a dalších datových typech najdete [v tématu Datové typy](#data-types)  

#### <a name="date-time-controlled-operations"></a>Řízené operace podle data a času

Dosud neplatné klíče s prošlou platností mimo okno *nbf* / *exp* budou fungovat pro **dešifrování**, **rozbalení**a **ověření** operací (nevrátí 403, Zakázáno). Důvodem pro použití dosud neplatného stavu je umožnit testování klíče před použitím výroby. Důvodem pro použití stavu vypršela je povolit operace obnovení na data, která byla vytvořena, když byl platný klíč. Přístup ke klíči můžete také zakázat pomocí zásad trezoru klíčů nebo aktualizací *atributu povoleného* klíče na **hodnotu false**.

Další informace o datových typech naleznete v [tématu Datové typy](#data-types).

Další informace o dalších možných atributech naleznete v [json webovém klíči (JWK).](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)

### <a name="key-tags"></a>Klíčové značky

Můžete zadat další metadata specifická pro aplikaci ve formě značek. Trezor klíčů podporuje až 15 značek, z nichž každá může mít název 256 znaků a hodnotu 256 znaků.  

>[!Note]
>Značky jsou čitelné volajícím, pokud mají *seznam* nebo *získat* oprávnění k tomuto typu objektu (klíče, tajné klíče nebo certifikáty).

###  <a name="key-access-control"></a>Řízení přístupu ke klíčům

Řízení přístupu ke klíčům spravovaným trezorem klíčů je k dispozici na úrovni trezoru klíčů, který funguje jako kontejner klíčů. Zásady řízení přístupu pro klíče se liší od zásadřízení přístupu pro tajné klíče ve stejném trezoru klíčů. Uživatelé mohou vytvořit jeden nebo více trezorů pro uložení klíčů a jsou povinni udržovat scénář odpovídající segmentace a správu klíčů. Řízení přístupu pro klíče je nezávislé na řízení přístupu pro tajné klíče.  

Následující oprávnění mohou být udělena na základě uživatele / instančního objektu v položce řízení přístupu klíčů v trezoru. Tato oprávnění úzce zrcadlí operace povolené pro klíčový objekt.  Udělení přístupu k instančnímu objektu v trezoru klíčů je jednorázová operace a zůstane stejná pro všechna předplatná Azure. Můžete ji použít k nasazení tolik a mnoho certifikátů, kolik chcete. 

- Oprávnění pro klíčové operace správy
  - *get*: Přečtěte si veřejnou část klíče, plus jeho atributy
  - *Seznam*: Seznam klíčů nebo verzí klíče uložených v trezoru klíčů
  - *aktualizace*: Aktualizace atributů klíče
  - *vytvořit*: Vytvořit nové klávesy
  - *import*: Import klíče do trezoru klíčů
  - *delete*: Odstranění klíčového objektu
  - *recover*: Obnovení odstraněného klíče
  - *Zálohování*: Zálohování klíče v trezoru klíčů
  - *Obnovení*: Obnovení zálohovaného klíče do trezoru klíčů

- Oprávnění pro kryptografické operace
  - *dešifrování*: Pomocí klíče odkreslujte posloupnost bajtů
  - *šifrovat*: Pomocí klíče ochráníte libovolnou sekvenci bajtů.
  - *unwrapKey*: Pomocí klíče odkreslovat zabalené symetrické klíče
  - *wrapKey*: Pomocí klíče k ochraně symetrického klíče
  - *ověření*: Pomocí klíče ověřte výtahy  
  - *znaménko*: Pomocí klíče podepisujete digesty
    
- Oprávnění pro privilegované operace
  - *vymazání*: Vymazání (trvalé odstranění) odstraněného klíče

Další informace o práci s klíči naleznete [v tématu Klíčové operace v odkazu rozhraní REST ROZHRANÍ KEY Vault REST .](/rest/api/keyvault) Informace o vytváření oprávnění naleznete v [tématu Trezory – Vytvoření nebo aktualizace](/rest/api/keyvault/vaults/createorupdate) a Úložiště – Aktualizovat [zásady přístupu](/rest/api/keyvault/vaults/updateaccesspolicy). 

## <a name="key-vault-secrets"></a>Tajné klíče trezoru klíčů 

### <a name="working-with-secrets"></a>Práce s tajemstvími

Z pohledu vývojáře, key vault API přijmout a vrátit tajné hodnoty jako řetězce. Trezor klíčů interně ukládá a spravuje tajné klíče jako sekvence oktetů (8bitových bajtů) s maximální velikostí 25 kB. Služba Trezor klíčů neposkytuje sémantiku pro tajné klíče. Pouze přijímá data, šifruje je, ukládá a vrací tajný identifikátor ("id"). Identifikátor lze použít k načtení tajného klíče později.  

U vysoce citlivých dat by klienti měli zvážit další vrstvy ochrany dat. Příkladem může být šifrování dat pomocí samostatného ochranného klíče před uložením ve službě Key Vault.  

Trezor klíčů také podporuje pole contentType pro tajné klíče. Klienti mohou určit typ obsahu tajného klíče, který vám pomůže při interpretaci tajných dat při jejich načtení. Maximální délka tohoto pole je 255 znaků. Neexistují žádné předem definované hodnoty. Navrhované použití je jako nápověda pro interpretaci tajných dat. Například implementace může ukládat hesla a certifikáty jako tajné klíče, pak použijte toto pole k rozlišení. Neexistují žádné předdefinované hodnoty.  

### <a name="secret-attributes"></a>Tajné atributy

Kromě tajných dat mohou být specifikovány následující atributy:  

- *exp*: IntDate, volitelné, výchozí hodnota je **navždy**. *Exp* (expirační čas) atribut identifikuje čas vypršení platnosti na nebo po kterém by neměla být načtena tajná data, s výjimkou [konkrétních situací](#date-time-controlled-operations). Toto pole slouží pouze pro **informační** účely, protože informuje uživatele služby trezoru klíčů, že konkrétní tajný klíč nesmí být použit. Jeho hodnota musí být číslo obsahující hodnotu IntDate.   
- *nbf*: IntDate, volitelné, výchozí je **nyní**. Atribut *nbf* (ne dříve) určuje dobu, než by tajná data neměla být načtena, s výjimkou [konkrétních situací](#date-time-controlled-operations). Toto pole je pouze pro **informační** účely. Jeho hodnota musí být číslo obsahující hodnotu IntDate. 
- *povoleno*: logická, volitelná, výchozí hodnota **je true**. Tento atribut určuje, zda lze načíst tajná data. Povolený atribut se používá ve spojení s *nbf* a *exp,* když dojde k operaci mezi *nbf* a *exp*, bude povolen pouze v případě, že je povoleno na **hodnotu true**. Operace mimo okno *nbf* a *exp* jsou automaticky zakázány, s výjimkou [konkrétních situací](#date-time-controlled-operations).  

Existují další atributy jen pro čtení, které jsou zahrnuty v odpovědi, která obsahuje tajné atributy:  

- *vytvořeno*: IntDate, volitelné. Vytvořený atribut označuje, kdy byla tato verze tajného klíče vytvořena. Tato hodnota je null pro tajné klíče vytvořené před přidáním tohoto atributu. Jeho hodnota musí být číslo obsahující hodnotu IntDate.  
- *aktualizováno*: IntDate, volitelné. Aktualizovaný atribut označuje, kdy byla tato verze tajného klíče aktualizována. Tato hodnota je null pro tajné klíče, které byly naposledy aktualizovány před přidáním tohoto atributu. Jeho hodnota musí být číslo obsahující hodnotu IntDate.

#### <a name="date-time-controlled-operations"></a>Řízené operace podle data a času

Tajná operace **get** bude fungovat pro dosud neplatné a prošlé tajné kódy, mimo okno *nbf* / *exp.* Volání tajné **ho získat** operaci, pro dosud neplatné tajné, lze použít pro testovací účely. Načítání (**získání**ting) vypršela tajný klíč, lze použít pro operace obnovení.

Další informace o datových typech naleznete v [tématu Datové typy](#data-types).  

### <a name="secret-access-control"></a>Řízení přístupu k tajným klíčům

Řízení přístupu pro tajné klíče spravované v trezoru klíčů, je k dispozici na úrovni trezoru klíčů, který obsahuje tyto tajné klíče. Zásady řízení přístupu pro tajné klíče se liší od zásady řízení přístupu pro klíče ve stejném trezoru klíčů. Uživatelé mohou vytvořit jeden nebo více trezorů pro uložení tajných kódů a jsou povinni udržovat scénář odpovídající segmentace a správa tajných kódů.   

Následující oprávnění lze použít na základě hlavního povinného v položka řízení přístupu k tajným kódům v úložišti a úzce zrcadlit operace povolené na tajný objekt:  

- Oprávnění pro operace správy tajných správě
  - *get*: Přečtěte si tajemství  
  - *Seznam*: Seznam tajných kódů nebo verzí tajného klíče uloženého v trezoru klíčů  
  - *set*: Vytvoření tajného klíče  
  - *odstranit*: Odstranění tajného klíče  
  - *recover*: Obnovení odstraněného tajného klíče
  - *Zálohování*: Zálohování tajného klíče v trezoru klíčů
  - *Obnovení*: Obnovení zálohovaného tajného klíče do trezoru klíčů

- Oprávnění pro privilegované operace
  - *vymazání*: Vymazání (trvalé odstranění) odstraněného tajného klíče

Další informace o práci s tajnými kódy naleznete [v tématu Tajné operace v odkazu rozhraní REST ROZHRANÍ KEY Vault .](/rest/api/keyvault) Informace o vytváření oprávnění naleznete v [tématu Trezory – Vytvoření nebo aktualizace](/rest/api/keyvault/vaults/createorupdate) a Úložiště – Aktualizovat [zásady přístupu](/rest/api/keyvault/vaults/updateaccesspolicy). 

### <a name="secret-tags"></a>Tajné značky  
Můžete zadat další metadata specifická pro aplikaci ve formě značek. Trezor klíčů podporuje až 15 značek, z nichž každá může mít název 256 znaků a hodnotu 256 znaků.  

>[!Note]
>Značky jsou čitelné volajícím, pokud mají *seznam* nebo *získat* oprávnění k tomuto typu objektu (klíče, tajné klíče nebo certifikáty).

## <a name="key-vault-certificates"></a>Certifikáty trezoru klíčů

Podpora certifikátů trezoru klíčů poskytuje správu certifikátů x509 a následující chování:  

-   Umožňuje vlastníkovi certifikátu vytvořit certifikát prostřednictvím procesu vytváření trezoru klíčů nebo importem existujícího certifikátu. Zahrnuje certifikáty podepsané svým držitelem i certifikáty generovanými certifikačníautoritou.
-   Umožňuje vlastníkovi certifikátu Trezoru klíčů implementovat zabezpečené úložiště a správu certifikátů X509 bez interakce s materiálem soukromého klíče.  
-   Umožňuje vlastníkovi certifikátu vytvořit zásadu, která přikáže trezoru klíčů spravovat životní cyklus certifikátu.  
-   Umožňuje vlastníkům certifikátů poskytovat kontaktní informace pro oznámení o událostech životního cyklu vypršení platnosti a obnovení certifikátu.  
-   Podporuje automatické obnovení s vybranými vystaviteli - Key Vault partner X509 certifikát poskytovatelů / certifikačních úřadů.

>[!Note]
>Nepartneři poskytovatelé/úřady jsou také povoleny, ale nebudou podporovat funkci automatického obnovení.

### <a name="composition-of-a-certificate"></a>Složení osvědčení

Při vytvoření certifikátu trezoru klíčů jsou vytvořeny také adresovatelné klíče a tajný klíč se stejným názvem. Klíč Trezor klíčů umožňuje operace klíčů a tajný klíč trezoru klíčů umožňuje načtení hodnoty certifikátu jako tajného klíče. Certifikát trezoru klíčů také obsahuje veřejná metadata certifikátu x509.  

Identifikátor a verze certifikátů je podobná jako u klíčů a tajných kódů. Konkrétní verze adresovatelného klíče a tajného klíče vytvořeného pomocí verze certifikátu Trezor u klíčů je k dispozici v odpovědi na certifikát trezoru klíčů.
 
![Certifikáty jsou složité objekty](media/azure-key-vault.png)

### <a name="exportable-or-non-exportable-key"></a>Exportovatelný nebo neexportovatelný klíč

Při vytvoření certifikátu trezoru klíčů jej lze načíst z adresovatelného tajného klíče pomocí soukromého klíče ve formátu PFX nebo PEM. Zásada použitá k vytvoření certifikátu musí označovat, že klíč lze exportovat. Pokud zásada označuje neexportovatelné, pak soukromý klíč není součástí hodnoty při načtení jako tajný klíč.  

Adresovatelný klíč se stává relevantnější s neexportovatelné KV certifikáty. Operace adresovatelného klíče KV jsou mapovány z pole *využití klíčů* v zásadách certifikátu KV, které slouží k vytvoření certifikátu KV.  

Podporovány jsou dva typy klíčů – *RSA* nebo *RSA HSM* s certifikáty. Exportovatelné je povoleno pouze s RSA, není podporovánrsa HSM.  

### <a name="certificate-attributes-and-tags"></a>Atributy a značky certifikátu

Kromě metadat certifikátu, adresovatelného klíče a adresovatelného tajného klíče obsahuje certifikát Trezoru klíčů také atributy a značky.  

#### <a name="attributes"></a>Atributy

Atributy certifikátu jsou zrcadleny na atributy adresovatelného klíče a tajného klíče vytvořeného při vytvoření certifikátu KV.  

Certifikát trezoru klíčů má následující atributy:  

-   *povoleno*: logická, volitelná, výchozí hodnota **je true**. Lze zadat k označení, zda data certifikátu lze načíst jako tajný nebo ovladatelný jako klíč. Používá se také ve spojení s *nbf* a *exp,* když dojde k operaci mezi *nbf* a *exp*, a bude povoleno pouze v případě, že je povoleno na hodnotu true. Operace mimo okno *nbf* a *exp* jsou automaticky zakázány.  

Existují další atributy jen pro čtení, které jsou zahrnuty v odpovědi:

-   *created*: IntDate: označuje, kdy byla tato verze certifikátu vytvořena.  
-   *Aktualizováno*: IntDate: označuje, kdy byla tato verze certifikátu aktualizována.  
-   *exp*: IntDate: obsahuje hodnotu data vypršení platnosti certifikátu x509.  
-   *nbf*: IntDate: obsahuje hodnotu data certifikátu x509.  

> [!Note] 
> Pokud vyprší platnost certifikátu trezoru klíčů, jeho adresovatelný klíč a tajný klíč se stanou nefunkčními.  

#### <a name="tags"></a>Značky

 Klient zadaný slovník párů hodnot klíčů, podobně jako značky v klíčích a tajných kódech.  

 > [!Note]
> Značky jsou čitelné volajícím, pokud mají *seznam* nebo *získat* oprávnění k tomuto typu objektu (klíče, tajné klíče nebo certifikáty).

### <a name="certificate-policy"></a>Zásady certifikátu

Zásady certifikátu obsahují informace o tom, jak vytvořit a spravovat životní cyklus certifikátu trezoru klíčů. Při importu certifikátu se soukromým klíčem do trezoru klíčů se načte teče certifikát x509.  

Při vytvoření certifikátu trezoru klíčů je třeba poskytnout zásady. Zásada určuje, jak vytvořit tuto verzi certifikátu trezoru klíčů nebo další verzi certifikátu trezoru klíčů. Jakmile je vytvořena zásada, není vyžadována s následnými operacemi vytváření pro budoucí verze. Existuje pouze jedna instance zásady pro všechny verze certifikátu trezoru klíčů.  

Na vysoké úrovni obsahuje zásada certifikátu následující informace:  

-   Vlastnosti certifikátu X509: Obsahuje název subjektu, alternativní názvy předmětů a další vlastnosti použité k vytvoření žádosti o certifikát x509.  
-   Vlastnosti klíče: obsahuje typ klíče, délku klíče, exportovatelné a opakovaně použít klíčová pole. Tato pole instruují trezor klíčů o tom, jak generovat klíč.  
-   Tajné vlastnosti: obsahuje tajné vlastnosti, jako je například typ obsahu adresovatelného tajného klíče pro generování tajné hodnoty, pro načítání certifikátu jako tajného klíče.  
-   Doživotní akce: obsahuje celoživotní akce pro certifikát KV. Každá celoživotní akce obsahuje:  

     - Aktivační událost: zadaná dny před vypršením platnosti nebo procento životnosti  

     - Akce: určení typu akce – *e-mailKontakty* nebo *autoRenew*  

-   Vystavitel: Parametry o vystaviteli certifikátu, který má být vydán k vydání certifikátů x509.  
-   Atributy zásad: obsahuje atributy přidružené k zásadě.  

#### <a name="x509-to-key-vault-usage-mapping"></a>Mapování využití x509 do trezoru klíčů

Následující tabulka představuje mapování zásad použití klíče x509 na efektivní klíčové operace klíče vytvořeného jako součást vytvoření certifikátu trezoru klíčů.

|**Příznaky použití klíče X509**|**Klíčové operace klíčů**|**Výchozí chování**|
|----------|--------|--------|
|DataEncipherment|šifrovat, dešifrovat| Není dostupné. |
|DecipovatPouze|Dešifrování| Není dostupné.  |
|Digitalsignature|podepsat, ověřit| Výchozí nastavení úložiště klíčů bez specifikace použití v době vytvoření certifikátu | 
|Pouze šifrovat|encrypt| Není dostupné. |
|Znak KeyCert|podepsat, ověřit|Není dostupné.|
|Klíčové slídení|wrapKey, rozbalitklíč| Výchozí nastavení úložiště klíčů bez specifikace použití v době vytvoření certifikátu | 
|Neodvolatelnost|podepsat, ověřit| Není dostupné. |
|crlsign|podepsat, ověřit| Není dostupné. |

### <a name="certificate-issuer"></a>Vystavitel certifikátu

Objekt certifikátu Trezor klíčů obsahuje konfiguraci používanou ke komunikaci s vybraným poskytovatelem certifikátu k objednání certifikátů x509.  

-   Trezor klíčů spolupracuje s následujícími poskytovateli certifikátů pro certifikáty TLS/SSL

|**Název zprostředkovatele**|**Umístění**|
|----------|--------|
|DigiCert|Podporováno ve všech umístěních služeb trezoru klíčů ve veřejném cloudu a Azure Government|
|Globální znak|Podporováno ve všech umístěních služeb trezoru klíčů ve veřejném cloudu a Azure Government|

Před vytvořením vystavittele certifikátu v trezoru klíčů musí být úspěšně provedeny následující nezbytné kroky 1 a 2.  

1. Poskytovatelé na palubě certifikační autority (CA)  

    -   Správce organizace musí napalubě své společnosti (např. contoso) s alespoň jedním poskytovatelem certifikační autority.  

2. Správce vytvoří pověření uživatele pro trezor klíčů k zápisu (a obnovení) certifikátů TLS/SSL.  

    -   Poskytuje konfiguraci, která má být použita k vytvoření objektu vystavitele zprostředkovatele v trezoru klíčů.  

Další informace o vytváření objektů vystavitela z portálu Certifikáty naleznete v [blogu Certifikáty trezoru klíčů.](https://aka.ms/kvcertsblog)  

Trezor klíčů umožňuje vytvoření více objektů vystavitele s konfigurací jiného poskytovatele vystavitele. Po vytvoření objektu vystavithopole lze odkazovat na jeho název v jedné nebo více zásadách certifikátu. Odkazování na objekt vystavitele dává pokyn trezoru klíčů, aby při požadavku na certifikát x509 od poskytovatele certifikátu během vytváření a obnovování certifikátu používal konfiguraci určenou v objektu vystavitele.  

Objekty vystavitena jsou vytvořeny v úschovně a lze je použít pouze s certifikáty KV ve stejném trezoru.  

### <a name="certificate-contacts"></a>Kontakty certifikátu

Kontakty certifikátu obsahují kontaktní informace pro odesílání oznámení spouštěných událostmi životnosti certifikátu. Informace o kontaktech jsou sdíleny všemi certifikáty v trezoru klíčů. Všem určeným kontaktům je odesláno oznámení o události pro libovolný certifikát v trezoru klíčů.  

Pokud je zásada certifikátu nastavena na automatické obnovení, je odesláno oznámení o následujících událostech.  

- Před obnovením certifikátu
- Po obnovení certifikátu s uvedením, zda byl certifikát úspěšně obnoven, nebo zda došlo k chybě, vyžadující ruční obnovení certifikátu.  

  Pokud je zásada certifikátu nastavená na ruční obnovení (pouze e-mail), je odesláno oznámení, když je čas na obnovení certifikátu.  

### <a name="certificate-access-control"></a>Řízení přístupu k certifikátu

 Řízení přístupu k certifikátům je spravováno trezorem klíčů a je poskytováno trezorem klíčů, který tyto certifikáty obsahuje. Zásady řízení přístupu pro certifikáty se liší od zásad řízení přístupu pro klíče a tajné klíče ve stejném trezoru klíčů. Uživatelé mohou vytvořit jeden nebo více trezorů pro uložení certifikátů, udržovat scénář odpovídající segmentace a správu certifikátů.  

 Následující oprávnění lze použít na základě hlavního povinného v položka řízení přístupu k tajným klíčům v trezoru klíčů a úzce zrcadlí operace povolené na tajný objekt:  

- Oprávnění pro operace správy certifikátů
  - *get*: Získání aktuální verze certifikátu nebo libovolné verze certifikátu 
  - *seznam*: Seznam aktuálních certifikátů nebo verzí certifikátu  
  - *aktualizace*: Aktualizace certifikátu
  - *vytvořit*: Vytvoření certifikátu trezoru klíčů
  - *import*: Import materiálu certifikátu do certifikátu Trezoru klíčů
  - *odstranění*: Odstranění certifikátu, jeho zásad a všech jeho verzí  
  - *recover*: Obnovení odstraněného certifikátu
  - *Zálohování*: Zálohování certifikátu v trezoru klíčů
  - *Obnovení*: Obnovení zálohovaného certifikátu do trezoru klíčů
  - *správa kontaktů*: Správa kontaktů certifikátů trezoru klíčů  
  - *manageissuers*: Správa autority certifikátů trezoru klíčů/ vystaviteni
  - *getissuers*: Získejte certifikát orgány / emitenty
  - *seznam emitentů*: Seznam orgánů/emitentů certifikátu  
  - *setissuers*: Vytvoření nebo aktualizace autority/vystavitelé certifikátu trezoru klíčů  
  - *deleteissuers*: Odstranění autorit/vydavatelů certifikátu trezoru klíčů  
 
- Oprávnění pro privilegované operace
  - *vymazání*: Vymazání (trvalé odstranění) odstraněného certifikátu

Další informace naleznete [v operacích certifikátu v odkazu rozhraní REST API úložiště klíčů](/rest/api/keyvault). Informace o vytváření oprávnění naleznete v [tématu Trezory – Vytvoření nebo aktualizace](/rest/api/keyvault/vaults/createorupdate) a Úložiště – Aktualizovat [zásady přístupu](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="azure-storage-account-key-management"></a>Správa klíčů účtu Azure Storage

Trezor klíčů může spravovat klíče účtů úložiště Azure:

- Trezor klíčů může interně uvádět (synchronizovat) klíče pomocí účtu úložiště Azure. 
- Trezor klíčů pravidelně generuje (otáčí) klíče.
- Hodnoty klíče jsou nikdy vráceny v reakci na volajícího.
- Trezor klíčů spravuje klíče účtů úložiště i klasických účtů úložiště.

Další informace najdete v tématu [Klíče účtů úložiště úložiště azure trezoru klíčů](key-vault-ovw-storage-keys.md)

### <a name="storage-account-access-control"></a>Řízení přístupu k účtu úložiště

Následující oprávnění lze použít při autorizaci uživatele nebo objektu zabezpečení aplikace k provádění operací s účtem spravovaného úložiště:  

- Oprávnění pro účet spravovaného úložiště a operace s definicí SaS
  - *získat*: Získá informace o účtu úložiště 
  - *Seznam*: Seznam účtů úložiště spravovaných trezorem klíčů
  - *aktualizace*: Aktualizace účtu úložiště
  - *odstranění*: Odstranění účtu úložiště  
  - *obnovení*: Obnovení odstraněného účtu úložiště
  - *zálohování*: Zálohování účtu úložiště
  - *obnovení*: Obnovení účtu zálohovaného úložiště do trezoru klíčů
  - *set*: Vytvoření nebo aktualizace účtu úložiště
  - *regeneratekey*: Regenerate a specified key value for a storage account Regeneratekey : Regenerate a specified key value for a storage account Regeneratekey : Regenerate a specified key value for a storage account Regenerate
  - *getsas*: Získat informace o definici SAS pro účet úložiště
  - *listsas*: Seznam definic SAS úložiště pro účet úložiště
  - *deletesas*: Odstranění definice SAS z účtu úložiště
  - *setsas*: Vytvoření nebo aktualizace nové definice/atributů SAS pro účet úložiště

- Oprávnění pro privilegované operace
  - *vymazání*: Vymazání (trvalé odstranění) účtu spravovaného úložiště

Další informace naleznete [v operacích účtu úložiště v odkazu rozhraní REST ROZHRANÍ KEY Vault REST .](/rest/api/keyvault) Informace o vytváření oprávnění naleznete v [tématu Trezory – Vytvoření nebo aktualizace](/rest/api/keyvault/vaults/createorupdate) a Úložiště – Aktualizovat [zásady přístupu](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="see-also"></a>Viz také

- [Ověřování, požadavky a odpovědi](authentication-requests-and-responses.md)
- [Průvodce vývojáře pro Key Vault](/azure/key-vault/key-vault-developers-guide)
