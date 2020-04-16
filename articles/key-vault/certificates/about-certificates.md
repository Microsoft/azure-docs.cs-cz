---
title: O certifikátech trezoru klíčů Azure – trezor klíčů Azure
description: Přehled rozhraní azure trezoru klíčů REST a certifikáty.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: e9fbe624563ad03880ff1a75efdc2df41b151846
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424276"
---
# <a name="about-azure-key-vault-certificates"></a>O certifikátech Azure Key Vault

Azure Key Vault umožňuje aplikacím a uživatelům Microsoft Azure ukládat a používat certifikáty, které jsou postavené na klíčích a tajných klíčích a přidávají funkci automatického obnovení.

Obecnější informace o trezoru klíčů najdete v tématu [Co je Azure Key Vault?](/azure/key-vault/key-vault-overview)

## <a name="azure-key-vault"></a>Azure Key Vault

Následující části nabízejí obecné informace platné v rámci implementace služby Trezor klíčů.

### <a name="supporting-standards"></a>Podpůrné standardy

Důležitými základními informacemi jsou specifikace JavaScript object notace (JSON) a JavaScript Object Signing and Encryption (JOSE).  

-   [Webový klíč JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)  
-   [Webové šifrování JSON (JWE)](https://tools.ietf.org/html/draft-ietf-jose-json-web-encryption-40)  
-   [Webové algoritmy JSON (JWA)](https://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms-40)  
-   [Webový podpis JSON (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature-41)  

### <a name="objects-identifiers-and-versioning"></a>Objekty, identifikátory a správa verzí

Objekty uložené v trezoru klíčů jsou verzí při každém vytvoření nové instance objektu. Každé verzi je přiřazen jedinečný identifikátor a adresa URL. Při prvním vytvoření objektu je mu přidělen jedinečný identifikátor verze a je označen jako aktuální verze objektu. Vytvoření nové instance se stejným názvem objektu dává novému objektu jedinečný identifikátor verze, což způsobuje, že se stane aktuální verzí.  

Objekty v trezoru klíčů lze adresovat pomocí aktuálního identifikátoru nebo identifikátoru specifického pro verzi. Například vzhledem ke klíči s názvem `MasterKey`, provádění operací s aktuálním identifikátorem způsobí, že systém použije nejnovější dostupnou verzi. Provádění operací s identifikátorem specifickým pro verzi způsobí, že systém použije tuto konkrétní verzi objektu.  

Objekty jsou jednoznačně identifikovány v trezoru klíčů pomocí adresy URL. Žádné dva objekty v systému mají stejnou adresu URL, bez ohledu na geografické umístění. Úplná adresa URL objektu se nazývá Identifikátor objektu. Adresa URL se skládá z předpony, která identifikuje trezor klíčů, typ objektu, uživatelem poskytnutý název objektu a verzi objektu. Název objektu je malá a velká písmena a neměnné. Identifikátory, které neobsahují verzi objektu, se označují jako základní identifikátory.  

Další informace naleznete v [tématu Ověřování, požadavky a odpovědi](../general/authentication-requests-and-responses.md)

Identifikátor objektu má následující obecný formát:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Kde:  

|||  
|-|-|  
|`keyvault-name`|Název trezoru klíčů ve službě Microsoft Azure Key Vault.<br /><br /> Názvy trezorů klíčů jsou vybrány uživatelem a jsou globálně jedinečné.<br /><br /> Název trezoru klíčů musí být řetězec 3-24 znaků, který obsahuje pouze 0-9, a-z, A-Z a -.|  
|`object-type`|Typ objektu, "klíče" nebo "tajné klíče".|  
|`object-name`|A `object-name` je uživatel zadaný název pro a musí být jedinečný v trezoru klíčů. Název musí být řetězec znaků 1-127, který obsahuje pouze 0-9, a-z, A-Z a -.|  
|`object-version`|A `object-version` je systémem generovaný, 32 znakový řetězec identifikátor, který se volitelně používá k adresování jedinečnou verzi objektu.|  


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
 
![Certifikáty jsou složité objekty](../media/azure-key-vault.png)

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
|DataEncipherment|šifrovat, dešifrovat| – |
|DecipovatPouze|Dešifrování| –  |
|Digitalsignature|podepsat, ověřit| Výchozí nastavení úložiště klíčů bez specifikace použití v době vytvoření certifikátu | 
|Pouze šifrovat|encrypt| – |
|Znak KeyCert|podepsat, ověřit|–|
|Klíčové slídení|wrapKey, rozbalitklíč| Výchozí nastavení úložiště klíčů bez specifikace použití v době vytvoření certifikátu | 
|Neodvolatelnost|podepsat, ověřit| – |
|crlsign|podepsat, ověřit| – |

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

## <a name="see-also"></a>Viz také

- [Ověřování, požadavky a odpovědi](../general/authentication-requests-and-responses.md)
- [Průvodce vývojáře pro Key Vault](../general/developers-guide.md)
