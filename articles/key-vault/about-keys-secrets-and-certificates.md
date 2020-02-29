---
title: Informace o Azure Key Vault klíčů, tajných klíčích a certifikátech – Azure Key Vault
description: Přehled rozhraní Azure Key Vault REST a podrobností pro vývojáře pro klíče, tajné klíče a certifikáty.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 9bbbcc38116c5681e3b5c867690c296f60507ad1
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196106"
---
# <a name="about-keys-secrets-and-certificates"></a>O klíčích, tajných klíčích a certifikátech

Azure Key Vault umožňuje aplikacím a uživatelům Microsoft Azure ukládat a používat několik typů tajných dat a klíčů:

- Kryptografické klíče: podporuje více typů klíčů a algoritmů a umožňuje použití modulů hardwarového zabezpečení (HSM) pro klíče s vysokou hodnotou. 
- Tajné kódy: poskytuje zabezpečené úložiště tajných kódů, jako jsou hesla a databázové připojovací řetězce.
- Certifikáty: podporuje certifikáty, které jsou postavené na klíčích a tajných klíčích a přidávají funkci automatického obnovení.
- Azure Storage: může spravovat klíče účtu Azure Storage za vás. Interně Key Vault možné vypsat (synchronizovat) klíče s účtem Azure Storage a pravidelně je znovu vygenerovat (otočit) klíče. 

Obecnější informace o Key Vault najdete v tématu [co je Azure Key Vault?](/azure/key-vault/key-vault-overview)

## <a name="azure-key-vault"></a>Azure Key Vault

Následující části obsahují obecné informace, které se vztahují k implementaci služby Key Vault.

### <a name="supporting-standards"></a>Podpůrné standardy

Specifikace JavaScript Object Notation (JSON) a JOSE (JavaScript Object Signing and Encryption) jsou důležité informace na pozadí.  

-   [Webový klíč JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)  
-   [Webové šifrování JSON (JWE)](https://tools.ietf.org/html/draft-ietf-jose-json-web-encryption-40)  
-   [Webové algoritmy JSON (JWA)](https://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms-40)  
-   [Webový podpis JSON (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature-41)  

### <a name="data-types"></a>Typy dat

V tématu Specifikace JOSE najdete relevantní datové typy pro klíče, šifrování a podepisování.  

-   **Algorithm** – podporovaný algoritmus pro klíčové operace, například RSA1_5  
-   **šifrovaný text-hodnota** – oktety textu šifry, kódované pomocí Base64URL  
-   **Digest-Value** – výstup algoritmu hash, kódovaný pomocí Base64URL  
-   **typ klíče** – jeden z podporovaných typů klíčů, například RSA (Rivest-Shamir-Adleman).  
-   oktety ve formátu **prostého** textu a prostého textu kódované pomocí Base64URL  
-   **Signatura-hodnota** – výstup algoritmu podpisu kódovanýho pomocí Base64URL  
-   **base64URL** – binární hodnota kódovaná v BASE64URL [RFC4648]  
-   **Boolean** – buď true, nebo false  
-   **Identita** – identita z Azure Active Directory (AAD).  
-   **IntDate** – Desítková hodnota JSON představující počet sekund od roku 1970-01-01T0:0: 0Z UTC až do zadaného data a času UTC. Podrobnosti týkající se data a času, obecně a UTC, najdete v tématu RFC3339.  

### <a name="objects-identifiers-and-versioning"></a>Objekty, identifikátory a správa verzí

Objekty uložené v Key Vault jsou tvořeny verzí pokaždé, když je vytvořena nová instance objektu. Každé verzi je přiřazen jedinečný identifikátor a adresa URL. Při prvním vytvoření objektu se mu přidaný jedinečný identifikátor verze a označen jako aktuální verze objektu. Vytvoření nové instance se stejným názvem objektu udělí novému objektu jedinečný identifikátor verze, což způsobí, že se stane aktuální verzí.  

Objekty v Key Vault lze řešit pomocí aktuálního identifikátoru nebo identifikátoru specifického pro verzi. Například při zadání klíče s názvem `MasterKey`provádění operací s aktuálním identifikátorem způsobí, že systém použije nejnovější dostupnou verzi. Provádění operací s identifikátorem specifickým pro verzi způsobí, že systém použije určitou konkrétní verzi objektu.  

Objekty se jednoznačně identifikují v rámci Key Vault pomocí adresy URL. Žádné dva objekty v systému nemají stejnou adresu URL, bez ohledu na geografickou polohu. Úplná adresa URL objektu se nazývá identifikátor objektu. Adresa URL se skládá z předpony, která identifikuje Key Vault, typ objektu, uživatelem poskytnutý název objektu a verzi objektu. Název objektu rozlišuje velká a malá písmena. Identifikátory, které neobsahují verzi objektu, jsou označovány jako základní identifikátory.  

Další informace najdete v tématu [ověřování, žádosti a odpovědi](authentication-requests-and-responses.md) .

Identifikátor objektu má následující obecný formát:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Kde:  

|||  
|-|-|  
|`keyvault-name`|Název trezoru klíčů ve službě Microsoft Azure Key Vault.<br /><br /> Key Vault jména vybere uživatel a jsou globálně jedinečné.<br /><br /> Key Vault název musí být řetězec znaků 3-24, který obsahuje pouze 0-9, a-z, a-Z a-.|  
|`object-type`|Typ objektu, buď klíče, nebo tajné klíče.|  
|`object-name`|`object-name` je název zadaný uživatelem pro a musí být jedinečný v rámci Key Vault. Název musí být řetězec znaků 1-127, který obsahuje pouze 0-9, a-z, a-Z a-.|  
|`object-version`|`object-version` je identifikátor řetězce znaků generovaný systémem 32, který se volitelně používá k adresování jedinečné verze objektu.|  

## <a name="key-vault-keys"></a>Key Vault klíče

### <a name="keys-and-key-types"></a>Typy klíčů a klíčů

Kryptografické klíče v Key Vault jsou reprezentovány jako objekty webového klíče JSON [JWK]. Základní specifikace JWK/JWA se také rozšiřují tak, aby umožňovaly použití typů klíčů jedinečných pro Key Vault implementaci. Například import klíčů pomocí balení specifického pro dodavatele HSM umožňuje zabezpečenou přepravu klíčů, které se dají používat jenom v Key Vault HSM.  

- **"Tiché" klíče**: klíč zpracovaný v softwaru Key Vault, ale je zašifrovaný v klidovém formátu pomocí systémového klíče, který je v modulu hardwarového zabezpečení. Klienti mohou importovat stávající klíč RSA nebo EC (eliptická křivka) nebo požádat, aby ho Key Vault vygeneroval.
- Klíčové **klíče**: klíč zpracovaný v modulu HSM (Hardware Security Module). Tyto klíče jsou chráněné v jednom z Key Vault Zabezpečení HSM světů (pro zachování izolace existuje jeden celosvětový svět na geografickou oblast). Klienti mohou importovat klíč RSA nebo ES, v případě slabé formy nebo exportováním z kompatibilního zařízení HSM. Klienti si taky můžou vyžádat Key Vault, aby vygenerovali klíč. Tento typ klíče přidá atribut key_hsm do JWK pro získání materiálu klíče HSM.

     Další informace o geografických hranicích najdete v tématu [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/privacy/) .  

Key Vault podporuje pouze klíče RSA a eliptické křivky. 

-   **ES**: "měkký" klíč eliptické křivky.
-   **ES-HSM**: "tvrdý" eliptický klíč křivky.
-   **RSA**: "měkký" klíč RSA.
-   **RSA-HSM**: "pevný" klíč RSA.

Key Vault podporuje klíče RSA velikosti 2048, 3072 a 4096. Key Vault podporuje typy klíčů eliptické křivky P-256, P-384, P-521 a P-256 (SECP256K1).

### <a name="cryptographic-protection"></a>Kryptografická ochrana

Kryptografické moduly, které Key Vault používá, je-li modul HARDWAROVÉho zabezpečení nebo software, ověřovány pomocí standardu FIPS (Federal Information Processing Standards). Ke spuštění v režimu FIPS nemusíte nic dělat zvlášť. Klíče **vytvořené** nebo **importované** jako chráněné HSM se zpracovávají v modulu hardwarového zabezpečení, který je ověřený na úrovni FIPS 140-2 Level 2. Klíče **vytvořené** nebo **importované** jako chráněný software se zpracovávají v rámci kryptografických modulů ověřených na FIPS 140-2 úrovně 1. Další informace naleznete v tématu [klíče a typy klíčů](#keys-and-key-types).

###  <a name="ec-algorithms"></a>Algoritmy ES
 Následující identifikátory algoritmu jsou podporovány s klíči ES a modulem ES-HSM v Key Vault. 

#### <a name="curve-types"></a>Typy křivek

-   **P-256** – křivka NIST p-256, definovaná na [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-256** – křivka s SECP256K1, definovaná v [sekundách 2: Doporučené parametry domény eliptické křivky](https://www.secg.org/sec2-v2.pdf).
-   **P-384** – křivka NIST p-384, definovaná na [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-521** – křivka NIST p-521, definovaná na [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).

#### <a name="signverify"></a>PODEPSAT/OVĚŘIT

-   **ES256** -ECDSA pro výtahy SHA-256 a klíče vytvořené pomocí křivky P-256. Tento algoritmus je popsaný na adrese [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES256K** -ECDSA pro SHA-256 Digest a klíče vytvořené pomocí křivky P-256. Tento algoritmus čeká na standardizaci.
-   **ES384** -ECDSA pro výtahy SHA-384 a klíče vytvořené pomocí křivky P-384. Tento algoritmus je popsaný na adrese [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES512** -ECDSA pro výtahy SHA-512 a klíče vytvořené pomocí křivky P-521. Tento algoritmus je popsaný na adrese [RFC7518](https://tools.ietf.org/html/rfc7518).

###  <a name="rsa-algorithms"></a>Algoritmy RSA  
 Následující identifikátory algoritmu jsou podporovány u klíčů RSA a RSA-HSM v Key Vault.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, ŠIFROVAT/DEŠIFROVAT

-   **RSA1_5** -RSAES-výplně pkcs1-V1_5 [RFC3447] šifrování klíče  
-   **RSA-výplně OAEP** -RSAES s použitím optimálního odsazení asymetrického šifrování (výplně OAEP) [RFC3447] s výchozími parametry URČENÝMI specifikací RFC 3447 v oddílu A. 2.1. Tyto výchozí parametry používají funkci hash SHA-1 a funkci generování masky MGF1 s SHA-1.  

#### <a name="signverify"></a>PODEPSAT/OVĚŘIT

-   **RS256** -RSASSA-PKCS-V1_5 pomocí SHA-256. Hodnota algoritmu Digest poskytnutá aplikací musí být počítaná pomocí SHA-256 a musí být 32 bajtů.  
-   **RS384** -RSASSA-PKCS-V1_5 pomocí SHA-384. Hodnota algoritmu Digest poskytnutá aplikací musí být počítaná pomocí SHA-384 a musí být 48 bajtů.  
-   **RS512** -RSASSA-PKCS-V1_5 pomocí SHA-512. Hodnota algoritmu Digest poskytnutá aplikací musí být počítaná pomocí SHA-512 a musí být 64 bajtů.  
-   **RSNULL** – viz [RFC2437], specializovaný případ použití pro povolení určitých scénářů TLS.  

###  <a name="key-operations"></a>Klíčové operace

Key Vault podporuje pro klíčové objekty následující operace:  

-   **Vytvořit**: umožňuje klientovi vytvořit klíč v Key Vault. Hodnota klíče je vygenerována Key Vault a uložená a není klientovi uvolněna. V Key Vault lze vytvořit asymetrické klíče.  
-   **Import**: umožňuje klientovi importovat existující klíč do Key Vault. Asymetrické klíče mohou být importovány do Key Vault pomocí řady různých metod balení v rámci konstrukce JWK. 
-   **Aktualizace**: umožňuje klientovi s dostatečnými oprávněními upravovat metadata (atributy klíčů) přidružená k klíči, který byl dřív uložený v Key Vault.  
-   **Odstranit**: umožňuje klientovi s dostatečnými oprávněními k odstranění klíče z Key Vault.  
-   **Seznam**: umožňuje klientovi zobrazit seznam všech klíčů v daném Key Vault.  
-   **Verze seznamu**: umožňuje klientovi zobrazit seznam všech verzí daného klíče v daném Key Vault.  
-   **Get**: umožňuje klientovi načíst veřejné části daného klíče v Key Vault.  
-   **Backup**: exportuje klíč do chráněného formuláře.  
-   **Obnovení**: importuje dříve zálohovaný klíč.  

Další informace naleznete v tématu [klíčové operace v Key Vault REST API](/rest/api/keyvault).  

Po vytvoření klíče v Key Vault můžou pomocí klíče provádět následující kryptografické operace:  

-   **Sign a Verify**: striktně Tato operace je "Sign hash" nebo "verify hash", protože Key Vault v rámci vytváření podpisu nepodporuje Hashování obsahu. Aplikace by měly vyhodnotit data, která se mají místně podepsat, a pak požadovat, Key Vault podepsat hodnotu hash. Ověřování podepsaných hodnot hash se podporuje jako pohodlná operace pro aplikace, které nemusí mít přístup k klíčovému materiálu [public]. Pro dosažení optimálního výkonu aplikací ověřte, zda jsou operace prováděny místně.  
-   **Šifrování a zabalení klíče**: klíč uložený v Key Vault může sloužit k ochraně jiného klíče, obvykle se jedná o šifrovací klíč symetrického obsahu (cek). Když je klíč v Key Vault asymetrická, použije se šifrování klíče. Například operace RSA-výplně OAEP a operace WRAPKEY/UNWRAPKEY jsou ekvivalentem šifrování/dešifrování. Když je klíč v Key Vault symetrický, používá se zalamování kláves. Například AES-KW. Operace WRAPKEY je podporována jako pohodlí pro aplikace, které nemusejí mít přístup k klíčovému materiálu [public]. Pro dosažení optimálního výkonu aplikací by se operace WRAPKEY měly provádět místně.  
-   **Šifrování a dešifrování**: klíč uložený v Key Vault může sloužit k šifrování nebo dešifrování jednoho bloku dat. Velikost bloku je určena typem klíče a vybraným šifrovacím algoritmem. Operace šifrování je poskytována pro usnadnění pro aplikace, které nemusí mít přístup k klíčovému materiálu [public]. Pro dosažení optimálního výkonu aplikací by se operace šifrování měly provádět místně.  

I když se WRAPKEY/UNWRAPKEY s použitím asymetrických klíčů může zdát nadbytečných (protože operace je ekvivalentní zašifrování nebo dešifrování), je důležité použití jedinečných operací. Rozlišení poskytuje sémantiku a oddělení autorizace těchto operací a konzistenci v případě, že služba podporuje jiné typy klíčů.  

Key Vault nepodporuje operace exportu. Jakmile se v systému zřídí klíč, nedá se extrahovat ani upravovat materiál klíče. Uživatelé Key Vault však mohou vyžadovat jejich klíč pro jiné případy použití, například po odstranění. V takovém případě mohou pomocí operací zálohování a obnovení exportovat nebo importovat klíč v chráněném formuláři. Klíče vytvořené operací zálohování nejsou použitelné mimo Key Vault. Alternativně lze operaci importu použít pro více instancí Key Vault.  

Uživatelé mohou omezit jakékoli kryptografické operace, které Key Vault podporuje na základě klíče pomocí vlastnosti key_ops objektu JWK.  

Další informace o objektech JWK naleznete v tématu [JSON web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).  

###  <a name="key-attributes"></a>Klíčové atributy

Kromě klíčového materiálu lze zadat následující atributy. V požadavku JSON jsou klíčové slovo atributů a složené závorky ({' '} ') vyžadovány i v případě, že nejsou zadány žádné atributy.  

- *povoleno*: logická hodnota, volitelná, výchozí hodnota je **true**. Určuje, jestli je klíč povolený a použitelný pro kryptografické operace. Atribut *Enabled* se používá ve spojení s *NBF* a *exp*. Pokud dojde k operaci mezi *NBF* a *exp*, bude povoleno pouze v případě, že je vlastnost *Enabled* nastavena na **hodnotu true**. Operace mimo okno *nbf* / *exp* se automaticky nepovolují, s výjimkou určitých typů operací za určitých [podmínek](#date-time-controlled-operations).
- *NBF*: IntDate, volitelné, výchozí nastavení je teď. Atribut *NBF* (nikoli před) určuje dobu, po jejímž uplynutí nesmí být klíč použit pro kryptografické operace, s výjimkou určitých typů operací za určitých [podmínek](#date-time-controlled-operations). Zpracování atributu *NBF* vyžaduje, aby aktuální datum a čas musel být pozdější nebo rovno datu a času, které je uvedené v atributu *NBF* . Key Vault může poskytovat některé malé Leeway, obvykle ne více než několik minut, aby se zohlednila časová zkosená část. Jeho hodnota musí být číslo obsahující hodnotu IntDate.  
- *exp*: IntDate, volitelné, výchozí je "navždy". Atribut *exp* (čas vypršení platnosti) identifikuje čas vypršení platnosti nebo po jehož uplynutí se klíč nesmí používat pro kryptografickou operaci s výjimkou určitých typů operací za určitých [podmínek](#date-time-controlled-operations). Zpracování atributu *exp* vyžaduje, aby aktuální datum a čas musely být před datem a časem vypršení platnosti uvedené v atributu *exp* . Key Vault může poskytovat malým leewayům, obvykle ne více než několik minut, pro účet pro časové zkosení. Jeho hodnota musí být číslo obsahující hodnotu IntDate.  

Existují další atributy jen pro čtení, které jsou zahrnuty v jakékoli odpovědi, která obsahuje klíčové atributy:  

- *Vytvořeno*: IntDate, volitelné. *Vytvořený* atribut označuje, kdy byla tato verze klíče vytvořena. Pro klíče vytvořené před přidáním tohoto atributu je hodnota null. Jeho hodnota musí být číslo obsahující hodnotu IntDate.  
- *Aktualizováno*: IntDate, volitelné. *Aktualizovaný* atribut určuje, kdy byla tato verze klíče aktualizována. Hodnota je null u klíčů, které byly naposledy aktualizovány před přidáním tohoto atributu. Jeho hodnota musí být číslo obsahující hodnotu IntDate.  

Další informace o IntDate a dalších datových typech najdete v tématu [datové typy](#data-types) .  

#### <a name="date-time-controlled-operations"></a>Operace kontrolovaného data a času

Neplatných klíčů a klíčů, jejichž platnost vypršela, a to mimo *okno / * *exp* , bude fungovat pro **dešifrování**, **rozbalení**a **ověření** operací (nevrátí 403, zakázáno). K tomu, aby bylo možné použít neplatný stav, je povolení testování klíče před použitím v produkčním prostředí. Odůvodnění použití stavu s vypršenou platností je povolení operací obnovení u dat, která byla vytvořena při platnosti klíče. Můžete také zakázat přístup k klíči pomocí zásad Key Vault nebo aktualizací atributu *Enabled* Key na **hodnotu false (NEPRAVDA**).

Další informace o typech dat najdete v tématu [datové typy](#data-types).

Další informace o dalších možných atributech najdete v tématu [webový klíč JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).

### <a name="key-tags"></a>Klíčové značky

Můžete zadat další metadata specifická pro aplikaci ve formě značek. Key Vault podporuje až 15 značek, z nichž každá může mít 256 název znaku a 256 znaková hodnota.  

>[!Note]
>Značky mohou číst volající, pokud mají *seznam* , nebo *získat* oprávnění k tomuto typu objektu (klíče, tajné klíče nebo certifikáty).

###  <a name="key-access-control"></a>Řízení přístupu ke klíčům

Řízení přístupu pro klíče spravované pomocí Key Vault je k dispozici na úrovni Key Vault, která funguje jako kontejner klíčů. Zásady řízení přístupu pro klíče se liší od zásad řízení přístupu pro tajné klíče ve stejné Key Vault. Uživatelé mohou vytvořit jeden nebo více trezorů pro blokování klíčů a jsou požadovány pro zachování vhodného segmentace a správy klíčů ve scénáři. Řízení přístupu pro klíče je nezávisle na řízení přístupu tajných kódů.  

Následující oprávnění se dají udělit na základě jednotlivých uživatelů nebo instančních objektů v položce řízení přístupu klíčů v trezoru. Tato oprávnění pečlivě zrcadlí operace povolené u objektu klíče.  Udělení přístupu k instančnímu objektu v trezoru klíčů je operace jednorázová a ta zůstane stejná pro všechna předplatná Azure. Můžete ji použít k nasazení libovolných certifikátů, kolik chcete. 

- Oprávnění pro operace správy klíčů
  - *Get*: Přečtěte si veřejnou část klíče a jeho atributy
  - *seznam*: vypsání klíčů nebo verzí klíče uloženého v trezoru klíčů
  - *aktualizace*: Aktualizace atributů pro klíč
  - *vytvořit*: vytvořit nové klíče
  - *Import*: Import klíče do trezoru klíčů
  - *Odstranit*: odstranit objekt klíče
  - *obnovení*: obnovit odstraněný klíč
  - *zálohování*: zálohování klíče v trezoru klíčů
  - *obnovení*: obnovení zálohovaného klíče do trezoru klíčů

- Oprávnění pro kryptografické operace
  - *dešifrovat*: pomocí klíče můžete zrušit ochranu posloupnosti bajtů.
  - *Šifrovat*: použijte klíč k ochraně libovolné posloupnosti bajtů.
  - *unwrapKey*: pomocí klíče zrušte ochranu zabaleného symetrického klíče.
  - *wrapKey*: použijte klíč k ochraně symetrického klíče.
  - *ověřit*: pomocí klíče ověřte hodnoty Digest.  
  - *Sign*: pomocí klíče podepište hodnoty Digest.
    
- Oprávnění pro privilegované operace
  - *vyprázdnit*: vyprázdnit (trvale odstranit) odstraněný klíč

Další informace o práci s klíči naleznete v tématu [klíčové operace v odkazu Key Vault REST API](/rest/api/keyvault). Informace o tom, jak vytvářet oprávnění, najdete v tématu [trezory – vytvoření nebo aktualizace](/rest/api/keyvault/vaults/createorupdate) a [trezory – zásady přístupu pro aktualizaci](/rest/api/keyvault/vaults/updateaccesspolicy). 

## <a name="key-vault-secrets"></a>Key Vault tajných klíčů 

### <a name="working-with-secrets"></a>Práce s tajnými kódy

Z pohledu vývojáře Key Vault rozhraní API akceptuje a vrací tajné hodnoty jako řetězce. Interně Key Vault ukládá a spravuje tajné klíče jako sekvence oktetů (8bitové bajty) a maximální velikost 25k bajtů. Služba Key Vault neposkytuje sémantiku tajných kódů. Přijímá pouze data, šifruje je, ukládá je a vrací tajný identifikátor (ID). Identifikátor lze použít k pozdějšímu načtení tajného klíče.  

Pro vysoce citlivá data by klienti měli zvážit další vrstvy ochrany dat. Šifrování dat pomocí samostatného klíče ochrany před úložištěm v Key Vault je jedním příkladem.  

Key Vault také podporuje pole contentType pro tajné klíče. Klienti můžou určit typ obsahu tajného kódu, který pomůže při interpretaci tajných dat při jejich načítání. Maximální délka tohoto pole je 255 znaků. Neexistují žádné předem definované hodnoty. Navrhované použití je jako pomocný parametr pro interpretaci tajných dat. Implementace může například ukládat hesla i certifikáty jako tajné klíče a pak je odlišit pomocí tohoto pole. Nejsou k dispozici žádné předdefinované hodnoty.  

### <a name="secret-attributes"></a>Tajné atributy

Kromě tajných dat lze zadat následující atributy:  

- *exp*: IntDate, volitelné, výchozí je **trvale**. Atribut *exp* (čas vypršení platnosti) identifikuje dobu vypršení platnosti nebo po jejímž uplynutí by se neměla načíst tajná data, s výjimkou [zvláštních situací](#date-time-controlled-operations). Toto pole slouží pouze k **informativním** účelům, protože informuje uživatele služby trezoru klíčů, že není možné použít konkrétní tajný klíč. Jeho hodnota musí být číslo obsahující hodnotu IntDate.   
- *NBF*: IntDate, volitelné, výchozí nastavení je **teď**. Atribut *NBF* (nikoli dřív) určuje čas, před kterým by se neměla načíst tajná data, s výjimkou [zvláštních situací](#date-time-controlled-operations). Toto pole slouží pouze k **informativním** účelům. Jeho hodnota musí být číslo obsahující hodnotu IntDate. 
- *povoleno*: logická hodnota, volitelná, výchozí hodnota je **true**. Tento atribut určuje, zda mohou být načtena tajná data. Atribut enabled se používá ve spojení s *NBF* a *exp* , pokud dojde k operaci mezi *NBF* a *exp*, ale bude povolená jenom v případě, že je povolená možnost nastavená na **hodnotu true**. Operace mimo okno *NBF* a *exp* se automaticky nepovolují, s výjimkou [zvláštních situací](#date-time-controlled-operations).  

Existují další atributy jen pro čtení, které jsou zahrnuty v jakékoli odpovědi, která obsahuje tajné atributy:  

- *Vytvořeno*: IntDate, volitelné. Vytvořený atribut označuje, kdy byla tato verze tajného kódu vytvořena. Tato hodnota je null pro tajné klíče vytvořené před přidáním tohoto atributu. Jeho hodnota musí být číslo obsahující hodnotu IntDate.  
- *Aktualizováno*: IntDate, volitelné. Aktualizovaný atribut určuje, kdy byla tato verze tajného kódu aktualizována. Tato hodnota je null pro tajné kódy, které byly naposledy aktualizovány před přidáním tohoto atributu. Jeho hodnota musí být číslo obsahující hodnotu IntDate.

#### <a name="date-time-controlled-operations"></a>Operace kontrolovaného data a času

Operace **Get** tajného klíče bude fungovat pro tajné klíče (dosud neplatných) a s vypršenou platností, a to mimo okno *NBF* / *exp* . Volání operace **Get** tajného klíče, pro neplatný tajný klíč, lze použít pro testovací účely. Načtení (**získání**nocení) tajného kódu, jehož platnost vypršela, lze použít pro operace obnovení.

Další informace o typech dat najdete v tématu [datové typy](#data-types).  

### <a name="secret-access-control"></a>Řízení přístupu k tajným klíčům

Access Control pro tajné klíče spravované v Key Vault je k dispozici na úrovni Key Vault, které tyto tajné kódy obsahují. Zásada řízení přístupu tajných klíčů je odlišná od zásad řízení přístupu pro klíče ve stejné Key Vault. Uživatelé mohou vytvořit jeden nebo více trezorů pro blokování tajných kódů a jsou vyžadovány pro udržení vhodného segmentace a správy tajných kódů.   

Následující oprávnění se dají použít, pro jednotlivé hlavní objekty, v položce řízení přístupu tajných klíčů v trezoru a při tom pozorně zrcadlit operace povolené u tajného objektu:  

- Oprávnění pro operace správy tajných klíčů
  - *získat*: čtení tajného klíče  
  - *seznam*: výpis tajných kódů nebo verzí tajného kódu uloženého v Key Vault  
  - *nastavit*: vytvořit tajný kód  
  - *Odstranit*: odstranění tajného klíče  
  - *obnovení*: obnovení odstraněných tajných klíčů
  - *zálohování*: zálohování tajného klíče v trezoru klíčů
  - *obnovení*: obnovení zálohovaného tajného klíče do trezoru klíčů

- Oprávnění pro privilegované operace
  - *vyprázdnit*: vyprázdnit (trvale odstranit) odstraněný tajný kód

Další informace o práci s tajnými kódy naleznete [v tématu tajné operace v odkazu na Key Vault REST API](/rest/api/keyvault). Informace o tom, jak vytvářet oprávnění, najdete v tématu [trezory – vytvoření nebo aktualizace](/rest/api/keyvault/vaults/createorupdate) a [trezory – zásady přístupu pro aktualizaci](/rest/api/keyvault/vaults/updateaccesspolicy). 

### <a name="secret-tags"></a>Tajné značky  
Můžete zadat další metadata specifická pro aplikaci ve formě značek. Key Vault podporuje až 15 značek, z nichž každá může mít 256 název znaku a 256 znaková hodnota.  

>[!Note]
>Značky mohou číst volající, pokud mají *seznam* , nebo *získat* oprávnění k tomuto typu objektu (klíče, tajné klíče nebo certifikáty).

## <a name="key-vault-certificates"></a>Key Vault certifikátů

Podpora certifikátů Key Vault poskytuje správu certifikátů x509 a následující chování:  

-   Umožňuje vlastníkovi certifikátu vytvořit certifikát prostřednictvím procesu vytváření Key Vault nebo pomocí importu existujícího certifikátu. Zahrnuje certifikáty podepsané svým držitelem i certifikáty vygenerované certifikační autoritou.
-   Umožňuje vlastníkovi Key Vault certifikátu implementovat zabezpečené úložiště a správu certifikátů x509 bez interakce s materiálem privátního klíče.  
-   Umožňuje vlastníkovi certifikátu vytvořit zásadu, která směruje Key Vault pro správu životního cyklu certifikátu.  
-   Umožňuje vlastníkům certifikátů poskytovat kontaktní informace pro oznámení o událostech životního cyklu vypršení platnosti a obnovení certifikátu.  
-   Podporuje automatické obnovení s vybranými vystaviteli – Key Vault poskytovatelé certifikátů a certifikačních autorit partnerů x509.

>[!Note]
>Neposkytovatelé nebo autority, které nejsou partnerskými servery, jsou také povoleny, ale nebudou podporovat funkci automatického obnovení.

### <a name="composition-of-a-certificate"></a>Složení certifikátu

Když se vytvoří certifikát Key Vault, vytvoří se také adresovatelný klíč a tajný kód se stejným názvem. Klíč Key Vault umožňuje operace s klíči a Key Vault tajný klíč umožňuje načtení hodnoty certifikátu jako tajného klíče. Certifikát Key Vault obsahuje také veřejná metadata certifikátu x509.  

Identifikátor a verze certifikátů jsou podobné klíčům a tajným klíčům. V odpovědi na certifikát Key Vault je dostupná konkrétní verze adresovatelného klíče a tajného klíče vytvořeného pomocí Key Vault verze certifikátu.
 
![Certifikáty jsou komplexní objekty.](media/azure-key-vault.png)

### <a name="exportable-or-non-exportable-key"></a>Exportovatelné nebo neexportované klíče

Když se vytvoří certifikát Key Vault, dá se načíst z adresního tajemství s privátním klíčem ve formátu PFX nebo PEM. Zásady použité k vytvoření certifikátu musí označovat, že klíč lze exportovat. Pokud zásada indikuje, že není exportovaná, privátní klíč není součástí hodnoty, když se načte jako tajný kód.  

Adresovatelný klíč se bude důležitější s neexportovatelnými certifikáty KV. Operace s adresovatelnými klávesami se mapují z pole *použití* zásad certifikátu KV, které slouží k vytvoření certifikátu kV.  

Podporovány jsou dva typy klíčů – modul *hardwarového* zabezpečení *RSA* nebo RSA s certifikáty. Exportovatelné je povolené jenom s šifrováním RSA, které nepodporuje modul HARDWAROVÉho zabezpečení RSA.  

### <a name="certificate-attributes-and-tags"></a>Atributy a značky certifikátů

Kromě metadat certifikátu obsahuje Key Vault certifikát také atributy a značky, které mají adresovatelný klíč a adresovatelný tajný klíč.  

#### <a name="attributes"></a>Atributy

Atributy certifikátu se zrcadlí do atributů adresovatelného klíče a tajného klíče vytvořeného při vytvoření certifikátu KV.  

Key Vault certifikát má následující atributy:  

-   *povoleno*: logická hodnota, volitelná, výchozí hodnota je **true**. Dá se zadat, aby označovala, jestli se data certifikátu dají načíst jako klíčová, nebo se dá ovládat jako klíč. Používá se také ve spojení s *NBF* a *exp* při výskytu operace mezi *NBF* a *exp*a bude povoleno pouze v případě, že je vlastnost Enabled nastavena na hodnotu true. Operace mimo okno *NBF* a *exp* jsou automaticky zakázané.  

V odpovědi jsou k dispozici další atributy jen pro čtení, které jsou k dispozici:

-   *Vytvořeno*: IntDate: udává, kdy byla vytvořena tato verze certifikátu.  
-   *Aktualizováno*: IntDate: udává, kdy se tato verze certifikátu aktualizovala.  
-   *exp*: IntDate: obsahuje hodnotu data vypršení platnosti certifikátu x509.  
-   *NBF*: IntDate: obsahuje hodnotu data certifikátu x509.  

> [!Note] 
> Pokud Key Vault certifikát vyprší, bude se adresovat klíč a tajný kód nebude fungovat.  

#### <a name="tags"></a>Značky

 Klient zadal slovník párů klíčových hodnot, podobně jako značky v klíčích a tajných klíčích.  

 > [!Note]
> Značky mohou číst volající, pokud mají *seznam* , nebo *získat* oprávnění k tomuto typu objektu (klíče, tajné klíče nebo certifikáty).

### <a name="certificate-policy"></a>Zásady certifikátu

Zásady certifikátu obsahují informace o tom, jak vytvořit a spravovat životní cyklus certifikátu Key Vault. Když se certifikát s privátním klíčem importuje do trezoru klíčů, vytvoří se výchozí zásada načtením certifikátu x509.  

Když se od začátku vytvoří certifikát Key Vault, musí se zadat zásada. Zásada určuje, jak se má vytvořit tato Key Vault verze certifikátu nebo další verze certifikátu Key Vault. Jakmile je zásada navázána, nepožaduje se u následných operací vytvoření budoucích verzí. Pro všechny verze Key Vaultho certifikátu existuje jenom jedna instance zásady.  

Zásady certifikátu na nejvyšší úrovni obsahují následující informace:  

-   Vlastnosti certifikátu x509: obsahuje název subjektu, alternativní názvy subjektu a další vlastnosti používané k vytvoření žádosti o certifikát x509.  
-   Klíčové vlastnosti: obsahuje typ klíče, délku klíče, exportovatelné a znovu použít klíčová pole. Tato pole instruují Trezor klíčů, jak vygenerovat klíč.  
-   Vlastnosti tajného klíče: obsahuje tajné vlastnosti, jako je typ obsahu adresovatelných tajných klíčů, aby se vygenerovala tajná hodnota pro načtení certifikátu jako tajného klíče.  
-   Akce životního cyklu: obsahuje akce životního cyklu pro certifikát KV. Každá akce životnosti obsahuje:  

     - Aktivační událost: zadáno přes dny před vypršením platnosti nebo procentem rozsahu platnosti  

     - Akce: určení typu akce – *emailContacts* nebo *autorenew*  

-   Vystavitel: parametry týkající se vystavitele certifikátu, který se má použít k vydávání certifikátů x509.  
-   Atributy zásad: obsahuje atributy přidružené k zásadě.  

#### <a name="x509-to-key-vault-usage-mapping"></a>Mapování x509 na Key Vault mapování použití

Následující tabulka představuje mapování zásad použití klíče x509 na efektivní klíčové operace klíče vytvořené jako součást vytváření certifikátu Key Vault.

|**Příznaky použití klíče x509**|**Operace klíče Key Vault Key**|**Výchozí chování**|
|----------|--------|--------|
|DataEncipherment|šifrování, dešifrování| neuvedeno |
|DecipherOnly|mohli| neuvedeno  |
|DigitalSignature|podepsat, ověřit| Key Vault výchozí bez specifikace použití při vytváření certifikátu | 
|EncipherOnly|encrypt| neuvedeno |
|KeyCertSign|podepsat, ověřit|neuvedeno|
|KeyEncipherment|wrapKey, unwrapKey| Key Vault výchozí bez specifikace použití při vytváření certifikátu | 
|Nepopiratelnosti odpovědnosti|podepsat, ověřit| neuvedeno |
|bit crlsign|podepsat, ověřit| neuvedeno |

### <a name="certificate-issuer"></a>Vystavitel certifikátu

Objekt certifikátu Key Vault obsahuje konfiguraci, která se používá ke komunikaci s vybraným poskytovatelem vystavitele certifikátu pro řazení certifikátů x509.  

-   Key Vault partneři s následujícími poskytovateli vystavitelů certifikátů pro certifikáty TLS/SSL

|**Název zprostředkovatele**|**Umístění**|
|----------|--------|
|DigiCert|Podporuje se ve všech umístěních služby trezoru klíčů ve veřejném cloudu a Azure Government|
|GlobalSign|Podporuje se ve všech umístěních služby trezoru klíčů ve veřejném cloudu a Azure Government|

Předtím, než je možné vytvořit vystavitele certifikátu v Key Vault, je nutné provést následující kroky 1 a 2 úspěšně.  

1. Připojení zprostředkovatelů certifikační autority (CA)  

    -   Správce organizace musí být na své společnosti na své společnosti (např. Contoso) s alespoň jedním poskytovatelem CA.  

2. Správce vytvoří přihlašovací údaje žadatele pro Key Vault k registraci (a obnovení) certifikátů TLS/SSL.  

    -   Poskytuje konfiguraci, která se má použít k vytvoření objektu vystavitele zprostředkovatele v trezoru klíčů.  

Další informace o vytváření objektů vystavitele z portálu Certificates najdete na [blogu Key Vault Certificates](https://aka.ms/kvcertsblog) .  

Key Vault umožňuje vytvoření více objektů vystavitele s jinou konfigurací zprostředkovatele vystavitele. Po vytvoření objektu vystavitele se na jeho název dá odkazovat v jedné nebo několika zásadách certifikátu. Odkazování na objekt vystavitele instruuje Key Vault pro použití konfigurace, jak je uvedeno v objektu vystavitele při vyžádání certifikátu x509 od poskytovatele CA během vytváření a obnovování certifikátu.  

Objekty vystavitele se vytvoří v trezoru a dají se použít jenom u certifikátů KV ve stejném trezoru.  

### <a name="certificate-contacts"></a>Kontakty certifikátu

Kontakty certifikátu obsahují kontaktní informace pro odesílání oznámení aktivovaných událostmi životnosti certifikátu. Informace o kontaktech jsou sdíleny pomocí všech certifikátů v trezoru klíčů. Oznámení se pošle všem zadaným kontaktům pro událost pro libovolný certifikát v trezoru klíčů.  

Pokud je zásada certifikátu nastavená na automatické obnovení, pošle se na následující události oznámení.  

- Před obnovením certifikátu
- Po obnovení certifikátu uveďte, jestli se certifikát úspěšně obnovil, nebo jestli došlo k chybě, která vyžaduje ruční obnovení certifikátu.  

  Když se zásada certifikátu, která je nastavená na ruční obnovení (jenom e-mailem), pošle, pošle se oznámení, když se certifikát obnoví.  

### <a name="certificate-access-control"></a>Access Control certifikátu

 Řízení přístupu pro certifikáty je spravované pomocí Key Vault a poskytuje Key Vault, které tyto certifikáty obsahují. Zásady řízení přístupu pro certifikáty se liší od zásad řízení přístupu pro klíče a tajné klíče ve stejné Key Vault. Uživatelé mohou vytvořit jeden nebo více trezorů pro ukládání certifikátů, aby bylo možné zachovat vhodné segmentaci a správu certifikátů.  

 Následující oprávnění se dají použít, pro jednotlivé hlavní objekty, v položce řízení přístupu tajných klíčů v trezoru klíčů a pozorně zrcadlí operace povolené u tajného objektu:  

- Oprávnění pro operace správy certifikátů
  - *získání*: získání aktuální verze certifikátu nebo libovolné verze certifikátu 
  - *seznam*: vypíše aktuální certifikáty nebo verze certifikátu.  
  - *aktualizace*: aktualizace certifikátu
  - *vytvořit*: vytvoření certifikátu Key Vault
  - *Import*: Import materiálu certifikátů do certifikátu Key Vault
  - *Odstranit*: odstranit certifikát, jeho zásadu a všechny jeho verze  
  - *obnovení*: obnovit odstraněný certifikát
  - *zálohování*: zálohování certifikátu v trezoru klíčů
  - *obnovení*: obnovení zálohovaného certifikátu do trezoru klíčů
  - *managecontacts*: Správa kontaktů certifikátů Key Vault  
  - *manageissuers*: Správa certifikačních autorit/vystavitelů Key Vault
  - *getissuer*: získání autority nebo vystavitelů certifikátu
  - *listissuers*: výpis autorit/vystavitelů certifikátů  
  - *setissuers*: vytvoření nebo aktualizace autorit/vystavitelů certifikátu Key Vault  
  - *deleteissuers*: odstranění autorit/vystavitelů certifikátu Key Vault  
 
- Oprávnění pro privilegované operace
  - *vyprázdnit*: vyprázdnit (trvale odstranit) odstraněný certifikát

Další informace najdete v referenčních informacích o [operacích certifikátu v REST API Key Vault](/rest/api/keyvault). Informace o tom, jak vytvářet oprávnění, najdete v tématu [trezory – vytvoření nebo aktualizace](/rest/api/keyvault/vaults/createorupdate) a [trezory – zásady přístupu pro aktualizaci](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="azure-storage-account-key-management"></a>Správa klíčů účtu Azure Storage

Key Vault můžou spravovat klíče účtu úložiště Azure:

- Interně Key Vault možné vypsat (synchronizovat) klíče s účtem služby Azure Storage. 
- Key Vault znovu vygeneruje (otáčí) klíče pravidelně.
- Hodnoty klíče se nikdy nevrátí v reakci na volajícího.
- Key Vault spravuje klíče účtů úložiště i klasických účtů úložiště.

Další informace najdete v tématu [Azure Key Vault klíčů účtu úložiště](key-vault-ovw-storage-keys.md) .

### <a name="storage-account-access-control"></a>Řízení přístupu k účtu úložiště

Následující oprávnění se dají použít při autorizaci objektu zabezpečení uživatele nebo aplikace k provádění operací na spravovaném účtu úložiště:  

- Oprávnění pro spravovaný účet úložiště a operace definice SaS
  - *Get*: načte informace o účtu úložiště. 
  - *seznam*: výpis účtů úložiště spravovaných Key Vault
  - *aktualizace*: aktualizace účtu úložiště
  - *Odstranit*: odstranění účtu úložiště  
  - *obnovení*: obnovení odstraněného účtu úložiště
  - *zálohování*: zálohování účtu úložiště
  - *obnovení*: obnovení účtu zálohovaného úložiště do Key Vault
  - *Nastavení*: vytvoření nebo aktualizace účtu úložiště
  - *RegenerateKey*: znovu vygenerujte zadanou hodnotu klíče pro účet úložiště.
  - *getsas*: Získejte informace o definici SAS pro účet úložiště.
  - *listsas*: Seznamte se s definicemi SAS úložiště pro účet úložiště.
  - *deletesas*: odstranění definice SAS z účtu úložiště
  - *setsas*: vytvoření nebo aktualizace nové definice nebo atributů SAS pro účet úložiště

- Oprávnění pro privilegované operace
  - *vyprázdnit*: vyprázdnit (trvale odstranit) spravovaný účet úložiště

Další informace najdete v referenčních informacích o [operacích účtu úložiště v Key Vault REST API](/rest/api/keyvault). Informace o tom, jak vytvářet oprávnění, najdete v tématu [trezory – vytvoření nebo aktualizace](/rest/api/keyvault/vaults/createorupdate) a [trezory – zásady přístupu pro aktualizaci](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="see-also"></a>Viz také

- [Ověřování, žádosti a odpovědi](authentication-requests-and-responses.md)
- [Průvodce vývojáře pro službu Key Vault](/azure/key-vault/key-vault-developers-guide)
