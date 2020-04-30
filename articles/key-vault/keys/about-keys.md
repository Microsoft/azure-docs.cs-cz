---
title: Informace o Azure Key Vault Keys – Azure Key Vault
description: Přehled rozhraní REST Azure Key Vault a podrobností pro vývojáře pro klíče.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 3d89275e1418035fed8aad3ffddd8def2c1d59ce
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81686063"
---
# <a name="about-azure-key-vault-keys"></a>O Azure Key Vaultch klíčích

Azure Key Vault podporuje více typů klíčů a algoritmů a umožňuje použití modulů hardwarového zabezpečení (HSM) pro klíče s vysokými hodnotami.

Kryptografické klíče v Key Vault jsou reprezentovány jako objekty webového klíče JSON [JWK]. Specifikace JavaScript Object Notation (JSON) a JOSE (JavaScript Object Signing and Encryption) jsou:

-   [Webový klíč JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [Webové šifrování JSON (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [Webové algoritmy JSON (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [Webový podpis JSON (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

Základní specifikace JWK/JWA se také rozšiřují tak, aby umožňovaly použití typů klíčů jedinečných pro Key Vault implementaci. Například import klíčů pomocí balení specifického pro dodavatele HSM umožňuje zabezpečenou přepravu klíčů, které se dají používat jenom v Key Vault HSM. 

Azure Key Vault podporuje jak měkké, tak pevné klíče:

- **"Tiché" klíče**: klíč zpracovaný v softwaru Key Vault, ale je zašifrovaný v klidovém formátu pomocí systémového klíče, který je v modulu hardwarového zabezpečení. Klienti mohou importovat stávající klíč RSA nebo EC (eliptická křivka) nebo požádat, aby ho Key Vault vygeneroval.
- Klíčové **klíče**: klíč zpracovaný v modulu HSM (Hardware Security Module). Tyto klíče jsou chráněné v jednom z Key Vault Zabezpečení HSM světů (pro zachování izolace existuje jeden celosvětový svět na geografickou oblast). Klienti mohou importovat klíč RSA nebo ES, v případě slabé formy nebo exportováním z kompatibilního zařízení HSM. Klienti si taky můžou vyžádat Key Vault, aby vygenerovali klíč. Tento typ klíče přidá atribut key_hsm do JWK pro získání materiálu klíče HSM.

Další informace o geografických hranicích najdete v tématu [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/privacy/) .  

## <a name="cryptographic-protection"></a>Kryptografická ochrana

Key Vault podporuje pouze klíče RSA a eliptické křivky. 

-   **ES**: "měkký" klíč eliptické křivky.
-   **ES-HSM**: "tvrdý" eliptický klíč křivky.
-   **RSA**: "měkký" klíč RSA.
-   **RSA-HSM**: "pevný" klíč RSA.

Key Vault podporuje klíče RSA velikosti 2048, 3072 a 4096. Key Vault podporuje typy klíčů eliptické křivky P-256, P-384, P-521 a P-256 (SECP256K1).

Kryptografické moduly, které Key Vault používá, je-li modul HARDWAROVÉho zabezpečení nebo software, ověřovány pomocí standardu FIPS (Federal Information Processing Standards). Ke spuštění v režimu FIPS nemusíte nic dělat zvlášť. Klíče **vytvořené** nebo **importované** jako chráněné HSM se zpracovávají v modulu hardwarového zabezpečení, který je ověřený na úrovni FIPS 140-2 Level 2. Klíče **vytvořené** nebo **importované** jako chráněný software se zpracovávají v rámci kryptografických modulů ověřených na FIPS 140-2 úrovně 1.

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

-   **PS256** -RSASSA-PSS pomocí algoritmu sha-256 a MGF1 s algoritmem sha-256, jak je popsáno v [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS384** -RSASSA-PSS pomocí algoritmu sha-384 a MGF1 s algoritmem sha-384, jak je popsáno v [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS512** -RSASSA-PSS pomocí algoritmu sha-512 a MGF1 s algoritmem sha-512, jak je popsáno v [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **RS256** -RSASSA-PKCS-V1_5 pomocí SHA-256. Hodnota algoritmu Digest poskytnutá aplikací musí být počítaná pomocí SHA-256 a musí být 32 bajtů.  
-   **RS384** -RSASSA-PKCS-V1_5 pomocí SHA-384. Hodnota algoritmu Digest poskytnutá aplikací musí být počítaná pomocí SHA-384 a musí být 48 bajtů.  
-   **RS512** -RSASSA-PKCS-V1_5 pomocí SHA-512. Hodnota algoritmu Digest poskytnutá aplikací musí být počítaná pomocí SHA-512 a musí být 64 bajtů.  
-   **RSNULL** – viz [RFC2437], specializovaný případ použití pro povolení určitých scénářů TLS.  

##  <a name="key-operations"></a>Klíčové operace

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

## <a name="key-attributes"></a>Klíčové atributy

Vedle nastavení týkajících se klíčů samotných je možné ještě zadat následující atributy. V požadavku JSON jsou klíčové slovo atributů a složené závorky ({' '} ') vyžadovány i v případě, že nejsou zadány žádné atributy.  

- *povoleno*: logická hodnota, volitelná, výchozí hodnota je **true**. Určuje, jestli je klíč povolený a použitelný pro kryptografické operace. Atribut *Enabled* se používá ve spojení s *NBF* a *exp*. Pokud dojde k operaci mezi *NBF* a *exp*, bude povoleno pouze v případě, že je vlastnost *Enabled* nastavena na **hodnotu true**. Operace mimo okno s*expem* *NBF* / se automaticky nepovolují, s výjimkou určitých typů operací za určitých [podmínek](#date-time-controlled-operations).
- *NBF*: IntDate, volitelné, výchozí nastavení je teď. Atribut *NBF* (nikoli před) určuje dobu, po jejímž uplynutí nesmí být klíč použit pro kryptografické operace, s výjimkou určitých typů operací za určitých [podmínek](#date-time-controlled-operations). Zpracování atributu *NBF* vyžaduje, aby aktuální datum a čas musel být pozdější nebo rovno datu a času, které je uvedené v atributu *NBF* . Key Vault může poskytovat některé malé Leeway, obvykle ne více než několik minut, aby se zohlednila časová zkosená část. Jeho hodnota musí být číslo obsahující hodnotu IntDate.  
- *exp*: IntDate, volitelné, výchozí je "navždy". Atribut *exp* (čas vypršení platnosti) identifikuje čas vypršení platnosti nebo po jehož uplynutí se klíč nesmí používat pro kryptografickou operaci s výjimkou určitých typů operací za určitých [podmínek](#date-time-controlled-operations). Zpracování atributu *exp* vyžaduje, aby aktuální datum a čas musely být před datem a časem vypršení platnosti uvedené v atributu *exp* . Key Vault může poskytovat malým leewayům, obvykle ne více než několik minut, pro účet pro časové zkosení. Jeho hodnota musí být číslo obsahující hodnotu IntDate.  

Existují další atributy jen pro čtení, které jsou zahrnuty v jakékoli odpovědi, která obsahuje klíčové atributy:  

- *Vytvořeno*: IntDate, volitelné. *Vytvořený* atribut označuje, kdy byla tato verze klíče vytvořena. Pro klíče vytvořené před přidáním tohoto atributu je hodnota null. Jeho hodnota musí být číslo obsahující hodnotu IntDate.  
- *Aktualizováno*: IntDate, volitelné. *Aktualizovaný* atribut určuje, kdy byla tato verze klíče aktualizována. Hodnota je null u klíčů, které byly naposledy aktualizovány před přidáním tohoto atributu. Jeho hodnota musí být číslo obsahující hodnotu IntDate.  

Další informace o IntDate a dalších datových typech najdete v tématu [informace o klíčích, tajných klíčích a certifikátech: [datové typy](../general/about-keys-secrets-certificates.md#data-types).

### <a name="date-time-controlled-operations"></a>Operace kontrolovaného data a času

Neplatných klíčů a klíčů s vypršenou platností *nbf* / mimo okno*exp exp* budou fungovat pro **dešifrování**, **rozbalení**a **ověření** operací (nevrátí 403, zakázáno). K tomu, aby bylo možné použít neplatný stav, je povolení testování klíče před použitím v produkčním prostředí. Odůvodnění použití stavu s vypršenou platností je povolení operací obnovení u dat, která byla vytvořena při platnosti klíče. Můžete také zakázat přístup k klíči pomocí zásad Key Vault nebo aktualizací atributu *Enabled* Key na **hodnotu false (NEPRAVDA**).

Další informace o typech dat najdete v tématu [datové typy](../general/about-keys-secrets-certificates.md#data-types).

Další informace o dalších možných atributech najdete v tématu [webový klíč JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).

## <a name="key-tags"></a>Klíčové značky

Můžete zadat další metadata specifická pro aplikaci ve formě značek. Key Vault podporuje až 15 značek, z nichž každá může mít 256 název znaku a 256 znaková hodnota.  

>[!Note]
>Značky mohou číst volající, pokud mají *seznam* , nebo *získat* oprávnění k tomuto typu objektu (klíče, tajné klíče nebo certifikáty).

##  <a name="key-access-control"></a>Řízení přístupu ke klíčům

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

## <a name="next-steps"></a>Další kroky

- [Informace o službě Key Vault](../general/overview.md)
- [Informace o klíčích, tajných kódech a certifikátech](../general/about-keys-secrets-certificates.md)
- [Informace o tajných kódech](../secrets/about-secrets.md)
- [Informace o certifikátech](../certificates/about-certificates.md)
- [Ověřování, žádosti a odpovědi](../general/authentication-requests-and-responses.md)
- [Průvodce vývojáře pro Key Vault](../general/developers-guide.md)
