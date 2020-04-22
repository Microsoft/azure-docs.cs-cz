---
title: Klíče azure trezoru klíčů – Azure Key Vault
description: Přehled rozhraní Azure Key Vault REST a podrobnosti o vývojáři klíčů.
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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686063"
---
# <a name="about-azure-key-vault-keys"></a>O klíčích trezoru klíčů Azure

Azure Key Vault podporuje více typů klíčů a algoritmů a umožňuje použití modulů hardwarového zabezpečení (HSM) pro klíče s vysokou hodnotou.

Kryptografické klíče v trezoru klíčů jsou reprezentovány jako objekty json webového klíče [JWK]. Specifikace JavaScript Object Notation (JSON) a JavaScript Object Signing and Encryption (JOSE) jsou:

-   [Webový klíč JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [Webové šifrování JSON (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [Webové algoritmy JSON (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [Webový podpis JSON (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

Základní specifikace JWK/JWA jsou také rozšířeny tak, aby umožňovalo typy klíčů jedinečné pro implementaci trezoru klíčů. Například import klíčů pomocí balení specifického pro dodavatele hsm umožňuje bezpečnou přepravu klíčů, které lze použít pouze v hsmech trezoru klíčů. 

Azure Key Vault podporuje měkké i pevné klíče:

- **"Měkké" klíče**: Klíč zpracovaný v softwaru trezorem klíčů, ale je šifrován v klidovém stavu pomocí systémového klíče, který je v objektu hesm. Klienti mohou importovat existující klíč RSA nebo EC (Elliptic Curve) nebo požadovat, aby trezor klíčů byl generován.
- **"Pevné" klíče**: Klíč zpracovaný v modulu hardwarového zabezpečení (Hardware Security Module). Tyto klíče jsou chráněny v jednom ze světů zabezpečení hsm úložiště klíčů (existuje jeden svět zabezpečení na zeměpisné oblasti pro udržení izolace). Klienti mohou importovat klíč RSA nebo ES v měkké formě nebo exportem z kompatibilního zařízení hardwarového zabezpečení. Klienti mohou také požádat trezor klíčů o vygenerování klíče. Tento typ klíče přidá atribut key_hsm do získání JWK pro přenos materiálu klíče HSM.

Další informace o geografických hranicích najdete v [tématu Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/privacy/)  

## <a name="cryptographic-protection"></a>Kryptografická ochrana

Trezor klíčů podporuje pouze klávesy RSA a Elliptic Curve. 

-   **EC**: "Soft" eliptická křivka klíč.
-   **EC-HSM**: "Hard" eliptická křivka klíč.
-   **RSA**: "Soft" RSA klíč.
-   **RSA-HSM**: "Hard" RSA klíč.

Key Vault podporuje RSA klíče o velikostech 2048, 3072 a 4096. Trezor klíčů podporuje typy klíčů elliptic Curve P-256, P-384, P-521 a P-256K (SECP256K1).

Kryptografické moduly, které trezor klíčů používá, ať už moduly hesm nebo software, jsou ověřeny fips (Federal Information Processing Standards). Nemusíte dělat nic zvláštního pro spuštění v režimu FIPS. Klíče **vytvořené** nebo **importované** jako chráněné pomocí hsm jsou zpracovány uvnitř objektu hsm, ověřené na FIPS 140-2 úroveň 2. Klíče **vytvořené** nebo **importované** jako softwarově chráněné jsou zpracovány uvnitř kryptografických modulů ověřených na FIPS 140-2 Level 1.

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

##  <a name="key-operations"></a>Klíčové operace

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

## <a name="key-attributes"></a>Klíčové atributy

Vedle nastavení týkajících se klíčů samotných je možné ještě zadat následující atributy. V požadavku JSON jsou vyžadovány klíčové slovo atributy a složená závorky {' '}, i když nejsou zadány žádné atributy.  

- *povoleno*: logická, volitelná, výchozí hodnota **je true**. Určuje, zda je klíč povolen a použitelný pro kryptografické operace. Povolený *enabled* atribut se používá ve spojení s *nbf* a *exp*. Dojde-li k operaci mezi *nbf* a *exp*, bude povolena pouze v případě, že je *povolena* na **hodnotu true**. Operace mimo okno*exp* *nbf* / jsou automaticky zakázány, s výjimkou určitých typů operací [za určitých podmínek](#date-time-controlled-operations).
- *nbf*: IntDate, volitelné, výchozí je nyní. Atribut *nbf* (ne dříve) určuje dobu, před kterou nesmí být klíč použit pro kryptografické operace, s výjimkou určitých typů operací [za určitých podmínek](#date-time-controlled-operations). Zpracování atributu *nbf* vyžaduje, aby aktuální datum a čas musí být po nebo rovno nepřed datu a času uvedenému v atributu *nbf.* Trezor klíčů může poskytnout nějakou malou volnost, obvykle ne více než několik minut, aby se zohlednilo zkosení hodin. Jeho hodnota musí být číslo obsahující hodnotu IntDate.  
- *exp*: IntDate, volitelné, výchozí hodnota je "navždy". Atribut *exp* (expirační doba) určuje dobu vypršení platnosti nebo po které nesmí být klíč použit pro kryptografickou operaci, s výjimkou určitých typů operací [za určitých podmínek](#date-time-controlled-operations). Zpracování *exp* atribut vyžaduje, aby aktuální datum a čas musí být před datem a časem vypršení platnosti uvedených v *exp* atributu. Trezor klíčů může poskytnout některé malé volnost, obvykle ne více než několik minut, na účet hodiny zkosení. Jeho hodnota musí být číslo obsahující hodnotu IntDate.  

Existují další atributy jen pro čtení, které jsou zahrnuty v odpovědi, která obsahuje klíčové atributy:  

- *vytvořeno*: IntDate, volitelné. Vytvořený *created* atribut označuje, kdy byla tato verze klíče vytvořena. Hodnota je null pro klíče vytvořené před přidáním tohoto atributu. Jeho hodnota musí být číslo obsahující hodnotu IntDate.  
- *aktualizováno*: IntDate, volitelné. *Aktualizovaný* atribut označuje, kdy byla tato verze klíče aktualizována. Hodnota je null pro klíče, které byly naposledy aktualizovány před přidáním tohoto atributu. Jeho hodnota musí být číslo obsahující hodnotu IntDate.  

Další informace o intdate a dalších datových typech naleznete v tématu [O klíčích, tajných klíčích a certifikátech: [Datové typy](../general/about-keys-secrets-certificates.md#data-types).

### <a name="date-time-controlled-operations"></a>Řízené operace podle data a času

Dosud neplatné klíče s prošlou platností mimo okno *nbf* / *exp* budou fungovat pro **dešifrování**, **rozbalení**a **ověření** operací (nevrátí 403, Zakázáno). Důvodem pro použití dosud neplatného stavu je umožnit testování klíče před použitím výroby. Důvodem pro použití stavu vypršela je povolit operace obnovení na data, která byla vytvořena, když byl platný klíč. Přístup ke klíči můžete také zakázat pomocí zásad trezoru klíčů nebo aktualizací *atributu povoleného* klíče na **hodnotu false**.

Další informace o datových typech naleznete v [tématu Datové typy](../general/about-keys-secrets-certificates.md#data-types).

Další informace o dalších možných atributech naleznete v [json webovém klíči (JWK).](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)

## <a name="key-tags"></a>Klíčové značky

Můžete zadat další metadata specifická pro aplikaci ve formě značek. Trezor klíčů podporuje až 15 značek, z nichž každá může mít název 256 znaků a hodnotu 256 znaků.  

>[!Note]
>Značky jsou čitelné volajícím, pokud mají *seznam* nebo *získat* oprávnění k tomuto typu objektu (klíče, tajné klíče nebo certifikáty).

##  <a name="key-access-control"></a>Řízení přístupu ke klíčům

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

## <a name="next-steps"></a>Další kroky

- [Informace o službě Key Vault](../general/overview.md)
- [Klíče, tajné klíče a certifikáty](../general/about-keys-secrets-certificates.md)
- [Informace o tajných kódech](../secrets/about-secrets.md)
- [Informace o certifikátech](../certificates/about-certificates.md)
- [Ověřování, požadavky a odpovědi](../general/authentication-requests-and-responses.md)
- [Průvodce vývojáře pro Key Vault](../general/developers-guide.md)
