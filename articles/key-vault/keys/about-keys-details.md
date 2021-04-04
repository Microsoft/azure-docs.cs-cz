---
title: Typy klíčů, algoritmy a operace – Azure Key Vault
description: Podporované typy klíčů, algoritmy a operace (podrobnosti).
services: key-vault
author: amitbapat
manager: msmbaldwin
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 10/22/2020
ms.author: ambapat
ms.openlocfilehash: 675c4f04ece322000ae0ebb44d6291c455db9397
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98133272"
---
# <a name="key-types-algorithms-and-operations"></a>Klíčové typy, algoritmy a operace

Key Vault podporuje dva typy prostředků: trezory a spravované HSM. Oba typy prostředků podporují různé šifrovací klíče. Chcete-li zobrazit souhrn podporovaných typů klíčů, typy ochrany podle jednotlivých typů prostředků, přečtěte si [o klíčích](about-keys.md).

Následující tabulka obsahuje souhrn typů klíčů a podporovaných algoritmů.

|Typy/velikosti a křivky klíčů| Šifrování a dešifrování<br>(Zabalení a rozbalení) | Podepsat/ověřit | 
| --- | --- | --- |
|ES-P256, ES-P256K, ES-KLÍČEM P384, ES-521|NA|ES256<br>ES256K<br>ES384<br>ES512|
|RSA 2K, 3K, 4K| RSA1_5<br>RSA – VÝPLNĚ OAEP<br>RSA – VÝPLNĚ OAEP-256|PS256<br>PS384<br>PS512<br>RS256<br>RS384<br>RS512<br>RSNULL| 
|AES 128-bit, 256-bit <br/>(Jenom spravované HSM)| AES – KW<br>AES – GCM<br>AES – CBC| NA| 
|||

##  <a name="ec-algorithms"></a>Algoritmy ES
 Následující identifikátory algoritmu jsou podporovány pomocí klíčů ES-HSM.

### <a name="curve-types"></a>Typy křivek

-   **P-256** – křivka NIST p-256, definovaná na [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-256** – křivka s SECP256K1, definovaná v [sekundách 2: Doporučené parametry domény eliptické křivky](https://www.secg.org/sec2-v2.pdf).
-   **P-384** – křivka NIST p-384, definovaná na [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-521** – křivka NIST p-521, definovaná na [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).

### <a name="signverify"></a>PODEPSAT/OVĚŘIT

-   **ES256** -ECDSA pro výtahy SHA-256 a klíče vytvořené pomocí křivky P-256. Tento algoritmus je popsaný na adrese [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES256K** -ECDSA pro SHA-256 Digest a klíče vytvořené pomocí křivky P-256. Tento algoritmus čeká na standardizaci.
-   **ES384** -ECDSA pro výtahy SHA-384 a klíče vytvořené pomocí křivky P-384. Tento algoritmus je popsaný na adrese [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES512** -ECDSA pro výtahy SHA-512 a klíče vytvořené pomocí křivky P-521. Tento algoritmus je popsaný na adrese [RFC7518](https://tools.ietf.org/html/rfc7518).

##  <a name="rsa-algorithms"></a>Algoritmy RSA  
 Následující identifikátory algoritmu jsou podporovány pomocí klíčů RSA a RSA-HSM.  

### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, ŠIFROVAT/DEŠIFROVAT

-   **RSA1_5** -RSAES-výplně pkcs1-V1_5 [RFC3447] šifrování klíče  
-   **RSA-výplně OAEP** -RSAES s použitím optimálního odsazení asymetrického šifrování (výplně OAEP) [RFC3447] s výchozími parametry URČENÝMI specifikací RFC 3447 v oddílu A. 2.1. Tyto výchozí parametry používají funkci hash SHA-1 a funkci generování masky MGF1 s SHA-1.  
-  **RSA-výplně OAEP-256** – RSAES s použitím optimálního odsazení asymetrického šifrování s funkcí hash sha-256 a funkcí generování masky MGF1 with SHA-256

### <a name="signverify"></a>PODEPSAT/OVĚŘIT

-   **PS256** -RSASSA-PSS pomocí algoritmu sha-256 a MGF1 s algoritmem sha-256, jak je popsáno v [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS384** -RSASSA-PSS pomocí algoritmu sha-384 a MGF1 s algoritmem sha-384, jak je popsáno v [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS512** -RSASSA-PSS pomocí algoritmu sha-512 a MGF1 s algoritmem sha-512, jak je popsáno v [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **RS256** -RSASSA-PKCS-V1_5 pomocí SHA-256. Hodnota algoritmu Digest poskytnutá aplikací musí být počítaná pomocí SHA-256 a musí být 32 bajtů.  
-   **RS384** -RSASSA-PKCS-V1_5 pomocí SHA-384. Hodnota algoritmu Digest poskytnutá aplikací musí být počítaná pomocí SHA-384 a musí být 48 bajtů.  
-   **RS512** -RSASSA-PKCS-V1_5 pomocí SHA-512. Hodnota algoritmu Digest poskytnutá aplikací musí být počítaná pomocí SHA-512 a musí být 64 bajtů.  
-   **RSNULL** – viz [RFC2437](https://tools.ietf.org/html/rfc2437), specializovaný případ použití pro povolení určitých scénářů protokolu TLS.  

##  <a name="symmetric-key-algorithms-managed-hsm-only"></a>Algoritmy symetrických klíčů (jenom spravované HSM)
- **AES-kW** -zabalení klíče AES ([RFC3394](https://tools.ietf.org/html/rfc3394)).
- Šifrování **AES-GCM** -AES v režimu čítače Galois ([NIST SP 800-38d](https://csrc.nist.gov/publications/sp800))
- Šifrování **AES-CBC** -AES v režimu řetězení bloků šifry ([NIST SP 800-38a](https://csrc.nist.gov/publications/sp800))

> [!NOTE] 
> Aktuální implementace AES-GCM a odpovídající rozhraní API jsou experimentální. Implementace a rozhraní API se mohou v budoucích iteracích podstatně měnit. 

##  <a name="key-operations"></a>Klíčové operace

Spravovaný modul HSM podporuje pro klíčové objekty následující operace:  

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

-   **Sign a Verify**: striktně Tato operace je "Sign hash" nebo "verify hash", protože Key Vault v rámci vytváření podpisu nepodporuje Hashování obsahu. Aplikace by měly vyhodnotit data, která se mají místně podepsat, a pak požadovat, Key Vault podepsat hodnotu hash. Ověřování podepsaných hodnot hash se podporuje jako pohodlná operace pro aplikace, které nemusí mít přístup k klíčovému materiálu [public]. Pro dosažení optimálního výkonu aplikací je třeba ověřit, zda operace proběhne místně.  
-   **Šifrování a zabalení klíče**: klíč uložený v Key Vault může sloužit k ochraně jiného klíče, obvykle se jedná o šifrovací klíč symetrického obsahu (cek). Když je klíč v Key Vault asymetrická, použije se šifrování klíče. Například operace RSA-výplně OAEP a operace WRAPKEY/UNWRAPKEY jsou ekvivalentem šifrování/dešifrování. Když je klíč v Key Vault symetrický, používá se zalamování kláves. Například AES-KW. Operace WRAPKEY je podporována jako pohodlí pro aplikace, které nemusejí mít přístup k klíčovému materiálu [public]. Pro dosažení optimálního výkonu aplikací by se operace WRAPKEY měly provádět místně.  
-   **Šifrování a dešifrování**: klíč uložený v Key Vault může sloužit k šifrování nebo dešifrování jednoho bloku dat. Velikost bloku je určena typem klíče a vybraným šifrovacím algoritmem. Operace šifrování je poskytována pro usnadnění pro aplikace, které nemusí mít přístup k klíčovému materiálu [public]. Pro dosažení optimálního výkonu aplikací by se operace šifrování měly provádět místně.  

I když se WRAPKEY/UNWRAPKEY s použitím asymetrických klíčů může zdát nadbytečných (protože operace je ekvivalentní zašifrování nebo dešifrování), je důležité použití jedinečných operací. Rozlišení poskytuje sémantiku a oddělení autorizace těchto operací a konzistenci v případě, že služba podporuje jiné typy klíčů.  

Key Vault nepodporuje operace exportu. Jakmile se v systému zřídí klíč, nedá se extrahovat ani upravovat materiál klíče. Uživatelé Key Vault však mohou vyžadovat jejich klíč pro jiné případy použití, například po odstranění. V takovém případě mohou pomocí operací zálohování a obnovení exportovat nebo importovat klíč v chráněném formuláři. Klíče vytvořené operací zálohování nejsou použitelné mimo Key Vault. Alternativně lze operaci importu použít pro více instancí Key Vault.  

Uživatelé mohou omezit jakékoli kryptografické operace, které Key Vault podporuje na základě klíče pomocí vlastnosti key_ops objektu JWK.  

Další informace o objektech JWK naleznete v tématu [JSON web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).  

## <a name="key-attributes"></a>Klíčové atributy

Vedle nastavení týkajících se klíčů samotných je možné ještě zadat následující atributy. V požadavku JSON jsou klíčové slovo atributů a složené závorky ({' '} ') vyžadovány i v případě, že nejsou zadány žádné atributy.  

- *povoleno*: logická hodnota, volitelná, výchozí hodnota je **true**. Určuje, jestli je klíč povolený a použitelný pro kryptografické operace. Atribut *Enabled* se používá ve spojení s *NBF* a *exp*. Pokud dojde k operaci mezi *NBF* a *exp*, bude povoleno pouze v případě, že je vlastnost *Enabled* nastavena na **hodnotu true**. Operace mimo okno s  /  *expem* NBF se automaticky nepovolují, s výjimkou určitých typů operací za určitých [podmínek](#date-time-controlled-operations).
- *NBF*: IntDate, volitelné, výchozí nastavení je teď. Atribut *NBF* (nikoli před) určuje dobu, po jejímž uplynutí nesmí být klíč použit pro kryptografické operace, s výjimkou určitých typů operací za určitých [podmínek](#date-time-controlled-operations). Zpracování atributu *NBF* vyžaduje, aby aktuální datum a čas musel být pozdější nebo rovno datu a času, které je uvedené v atributu *NBF* . Key Vault může poskytovat některé malé Leeway, obvykle ne více než několik minut, aby se zohlednila časová zkosená část. Jeho hodnota musí být číslo obsahující hodnotu IntDate.  
- *exp*: IntDate, volitelné, výchozí je "navždy". Atribut *exp* (čas vypršení platnosti) identifikuje čas vypršení platnosti nebo po jehož uplynutí se klíč nesmí používat pro kryptografickou operaci s výjimkou určitých typů operací za určitých [podmínek](#date-time-controlled-operations). Zpracování atributu *exp* vyžaduje, aby aktuální datum a čas musely být před datem a časem vypršení platnosti uvedené v atributu *exp* . Key Vault může poskytovat malým leewayům, obvykle ne více než několik minut, pro účet pro časové zkosení. Jeho hodnota musí být číslo obsahující hodnotu IntDate.  

Existují další atributy jen pro čtení, které jsou zahrnuty v jakékoli odpovědi, která obsahuje klíčové atributy:  

- *Vytvořeno*: IntDate, volitelné. *Vytvořený* atribut označuje, kdy byla tato verze klíče vytvořena. Pro klíče vytvořené před přidáním tohoto atributu je hodnota null. Jeho hodnota musí být číslo obsahující hodnotu IntDate.  
- *Aktualizováno*: IntDate, volitelné. *Aktualizovaný* atribut určuje, kdy byla tato verze klíče aktualizována. Hodnota je null u klíčů, které byly naposledy aktualizovány před přidáním tohoto atributu. Jeho hodnota musí být číslo obsahující hodnotu IntDate.  

Další informace o IntDate a dalších datových typech najdete v tématu [informace o klíčích, tajných klíčích a certifikátech: [datové typy](../general/about-keys-secrets-certificates.md#data-types).

### <a name="date-time-controlled-operations"></a>Operace kontrolovaného data a času

Neplatných klíčů a klíčů s vypršenou platností   /  mimo okno *exp exp* budou fungovat pro **dešifrování**, **rozbalení** a **ověření** operací (nevrátí 403, zakázáno). K tomu, aby bylo možné použít neplatný stav, je povolení testování klíče před použitím v produkčním prostředí. Odůvodnění použití stavu s vypršenou platností je povolení operací obnovení u dat, která byla vytvořena při platnosti klíče. Můžete také zakázat přístup k klíči pomocí zásad Key Vault nebo aktualizací atributu *Enabled* Key na **hodnotu false (NEPRAVDA**).

Další informace o typech dat najdete v tématu [datové typy](../general/about-keys-secrets-certificates.md#data-types).

Další informace o dalších možných atributech najdete v tématu [webový klíč JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).

## <a name="key-tags"></a>Klíčové značky

Můžete zadat další metadata specifická pro aplikaci ve formě značek. Key Vault podporuje až 15 značek, z nichž každá může mít 256 název znaku a 256 znaková hodnota.  

> [!NOTE] 
> Značky mohou číst volající, pokud mají *seznam* , nebo *získat* oprávnění k tomuto klíči.

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
- [Informace o službě Managed HSM](../managed-hsm/overview.md)
- [Informace o tajných kódech](../secrets/about-secrets.md)
- [Informace o certifikátech](../certificates/about-certificates.md)
- [Přehled Key Vault REST API](../general/about-keys-secrets-certificates.md)
- [Ověřování, žádosti a odpovědi](../general/authentication-requests-and-responses.md)
- [Průvodce vývojáře pro službu Key Vault](../general/developers-guide.md)
