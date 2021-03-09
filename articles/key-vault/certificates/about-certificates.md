---
title: Informace o Azure Key Vaultch certifikátů – Azure Key Vault
description: Přehled rozhraní a certifikátů REST Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: b410dc89b286ef830f0d5b6a9c33fe77d380f5d1
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102507207"
---
# <a name="about-azure-key-vault-certificates"></a>Informace o certifikátech ve službě Azure Key Vault

Podpora certifikátů Key Vault poskytuje správu certifikátů x509 a následující chování:  

-   Umožňuje vlastníkovi certifikátu vytvořit certifikát prostřednictvím procesu vytváření Key Vault nebo pomocí importu existujícího certifikátu. Zahrnuje certifikáty podepsané svým držitelem i certifikáty vygenerované certifikační autoritou.
-   Umožňuje vlastníkovi Key Vault certifikátu implementovat zabezpečené úložiště a správu certifikátů x509 bez interakce s materiálem privátního klíče.  
-   Umožňuje vlastníkovi certifikátu vytvořit zásadu, která směruje Key Vault pro správu životního cyklu certifikátu.  
-   Umožňuje vlastníkům certifikátů poskytovat kontaktní informace pro oznámení o událostech životního cyklu vypršení platnosti a obnovení certifikátu.  
-   Podporuje automatické obnovení s vybranými vystaviteli – Key Vault poskytovatelé certifikátů a certifikačních autorit partnerů x509.

>[!Note]
>Neposkytovatelé nebo autority, které nejsou partnerskými servery, jsou také povoleny, ale nebudou podporovat funkci automatického obnovení.

## <a name="composition-of-a-certificate"></a>Složení certifikátu

Když se vytvoří certifikát Key Vault, vytvoří se také adresovatelný klíč a tajný kód se stejným názvem. Klíč Key Vault umožňuje operace s klíči a Key Vault tajný klíč umožňuje načtení hodnoty certifikátu jako tajného klíče. Certifikát Key Vault obsahuje také veřejná metadata certifikátu x509.  

Identifikátor a verze certifikátů jsou podobné klíčům a tajným klíčům. V odpovědi na certifikát Key Vault je dostupná konkrétní verze adresovatelného klíče a tajného klíče vytvořeného pomocí Key Vault verze certifikátu.
 
![Certifikáty jsou komplexní objekty.](../media/azure-key-vault.png)

## <a name="exportable-or-non-exportable-key"></a>Exportovatelné nebo neexportované klíče

Když se vytvoří certifikát Key Vault, dá se načíst z adresního tajemství s privátním klíčem ve formátu PFX nebo PEM. Zásady použité k vytvoření certifikátu musí označovat, že klíč lze exportovat. Pokud zásada indikuje, že není exportovaná, privátní klíč není součástí hodnoty, když se načte jako tajný kód.  

Adresovatelný klíč se bude důležitější s neexportovatelnými certifikáty KV. Operace s adresovatelnými klávesami se mapují z pole *použití* zásad certifikátu KV, které slouží k vytvoření certifikátu kV.  

Typ páru klíčů, který se má pro certifikáty podporovat

 - Podporované typy typů: RSA, RSA-HSM, ES, ES-HSM, Oct ( [zde](/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype)uvedené) exportovatelné je povolené jenom pro RSA, ES. Klíče HSM by nebyly exportovatelné.

|Typ klíče|Informace|Zabezpečení|
|--|--|--|
|**RSA**| Klíč RSA chráněný softwarem|FIPS 140-2 úrovně 1|
|**RSA – HSM**| Klíč RSA chráněný HSM (jenom SKU úrovně Premium)|Modul HARDWAROVÉho zabezpečení FIPS 140-2 úrovně 2|
|**EC**| Klíč eliptické křivky "chráněný softwarem"|FIPS 140-2 úrovně 1|
|**EC – HSM**| Klávesa s eliptickou křivkou chráněná HSM (jenom SKU úrovně Premium)|Modul HARDWAROVÉho zabezpečení FIPS 140-2 úrovně 2|
|||

## <a name="certificate-attributes-and-tags"></a>Atributy a značky certifikátů

Kromě metadat certifikátu obsahuje Key Vault certifikát také atributy a značky, které mají adresovatelný klíč a adresovatelný tajný klíč.  

### <a name="attributes"></a>Atributy

Atributy certifikátu se zrcadlí do atributů adresovatelného klíče a tajného klíče vytvořeného při vytvoření certifikátu KV.  

Key Vault certifikát má následující atributy:  

-   *povoleno*: logická hodnota, volitelná, výchozí hodnota je **true**. Dá se zadat, aby označovala, jestli se data certifikátu dají načíst jako klíčová, nebo se dá ovládat jako klíč. Používá se také ve spojení s *NBF* a *exp* při výskytu operace mezi *NBF* a *exp* a bude povoleno pouze v případě, že je vlastnost Enabled nastavena na hodnotu true. Operace mimo okno *NBF* a *exp* jsou automaticky zakázané.  

V odpovědi jsou k dispozici další atributy jen pro čtení, které jsou k dispozici:

-   *Vytvořeno*: IntDate: udává, kdy byla vytvořena tato verze certifikátu.  
-   *Aktualizováno*: IntDate: udává, kdy se tato verze certifikátu aktualizovala.  
-   *exp*: IntDate: obsahuje hodnotu data vypršení platnosti certifikátu x509.  
-   *NBF*: IntDate: obsahuje hodnotu data certifikátu x509.  

> [!Note] 
> Pokud vyprší platnost certifikátu ve službě Key Vault, jeho adresovatelný klíč a tajný klíč přestanou fungovat.  

### <a name="tags"></a>Značky

 Klient zadal slovník párů klíčových hodnot, podobně jako značky v klíčích a tajných klíčích.  

 > [!Note]
> Značky mohou číst volající, pokud mají *seznam* , nebo *získat* oprávnění k tomuto typu objektu (klíče, tajné klíče nebo certifikáty).

## <a name="certificate-policy"></a>Zásady certifikátu

Zásady certifikátu obsahují informace o tom, jak vytvořit a spravovat životní cyklus certifikátu Key Vault. Když se certifikát s privátním klíčem importuje do trezoru klíčů, vytvoří se výchozí zásada načtením certifikátu x509.  

Když se od začátku vytvoří certifikát Key Vault, musí se zadat zásada. Zásada určuje, jak se má vytvořit tato Key Vault verze certifikátu nebo další verze certifikátu Key Vault. Jakmile je zásada navázána, nepožaduje se u následných operací vytvoření budoucích verzí. Pro všechny verze Key Vaultho certifikátu existuje jenom jedna instance zásady.  

Zásady certifikátu na nejvyšší úrovni obsahují následující informace (jejich definice najdete [tady](/powershell/module/az.keyvault/set-azkeyvaultcertificatepolicy)):  

-   Vlastnosti certifikátu x509: obsahuje název subjektu, alternativní názvy subjektu a další vlastnosti používané k vytvoření žádosti o certifikát x509.  
-   Klíčové vlastnosti: obsahuje typ klíče, délku klíče, exportovatelné a ReuseKeyOnRenewal pole. Tato pole instruují Trezor klíčů, jak vygenerovat klíč. 
     - Podporované typy typů: RSA, RSA-HSM, ES, ES-HSM, Oct ( [tady](/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype)se uvádí) 
-   Vlastnosti tajného klíče: obsahuje tajné vlastnosti, jako je typ obsahu adresovatelných tajných klíčů, aby se vygenerovala tajná hodnota pro načtení certifikátu jako tajného klíče.  
-   Akce životního cyklu: obsahuje akce životního cyklu pro certifikát KV. Každá akce životnosti obsahuje:  

     - Aktivační událost: zadáno přes dny před vypršením platnosti nebo procentem rozsahu platnosti  

     - Akce: určení typu akce – *emailContacts* nebo *autorenew*  

-   Vystavitel: parametry týkající se vystavitele certifikátu, který se má použít k vydávání certifikátů x509.  
-   Atributy zásad: obsahuje atributy přidružené k zásadě.  

### <a name="x509-to-key-vault-usage-mapping"></a>Mapování x509 na Key Vault mapování použití

Následující tabulka představuje mapování zásad použití klíče x509 na efektivní klíčové operace klíče vytvořené jako součást vytváření certifikátu Key Vault.

|**Příznaky použití klíče x509**|**Operace klíče Key Vault Key**|**Výchozí chování**|
|----------|--------|--------|
|DataEncipherment|šifrování, dešifrování| – |
|DecipherOnly|mohli| –  |
|DigitalSignature|podepsat, ověřit| Key Vault výchozí bez specifikace použití při vytváření certifikátu | 
|EncipherOnly|encrypt| – |
|KeyCertSign|podepsat, ověřit|–|
|KeyEncipherment|wrapKey, unwrapKey| Key Vault výchozí bez specifikace použití při vytváření certifikátu | 
|Nepopiratelnosti odpovědnosti|podepsat, ověřit| – |
|bit crlsign|podepsat, ověřit| – |

## <a name="certificate-issuer"></a>Vystavitel certifikátu

Objekt certifikátu Key Vault obsahuje konfiguraci, která se používá ke komunikaci s vybraným poskytovatelem vystavitele certifikátu pro řazení certifikátů x509.  

-   Key Vault partneři s následujícími poskytovateli vystavitelů certifikátů pro certifikáty TLS/SSL

|**Název poskytovatele**|**Umístění**|
|----------|--------|
|DigiCert|Podporuje se ve všech umístěních služby trezoru klíčů ve veřejném cloudu a Azure Government|
|GlobalSign|Podporuje se ve všech umístěních služby trezoru klíčů ve veřejném cloudu a Azure Government|

Předtím, než je možné vytvořit vystavitele certifikátu v Key Vault, je nutné provést následující kroky 1 a 2 úspěšně.  

1. Připojení zprostředkovatelů certifikační autority (CA)  

    -   Správce organizace musí být na své společnosti na své společnosti (např. Contoso) s alespoň jedním poskytovatelem CA.  

2. Správce vytvoří přihlašovací údaje žadatele pro Key Vault k registraci (a obnovení) certifikátů TLS/SSL.  

    -   Poskytuje konfiguraci, která se má použít k vytvoření objektu vystavitele zprostředkovatele v trezoru klíčů.  

Další informace o vytváření objektů vystavitele z portálu Certificates najdete na [blogu Key Vault Certificates](/archive/blogs/kv/manage-certificates-via-azure-key-vault) .  

Key Vault umožňuje vytvoření více objektů vystavitele s jinou konfigurací zprostředkovatele vystavitele. Po vytvoření objektu vystavitele se na jeho název dá odkazovat v jedné nebo několika zásadách certifikátu. Odkazování na objekt vystavitele instruuje Key Vault pro použití konfigurace, jak je uvedeno v objektu vystavitele při vyžádání certifikátu x509 od poskytovatele CA během vytváření a obnovování certifikátu.  

Objekty vystavitele se vytvoří v trezoru a dají se použít jenom u certifikátů KV ve stejném trezoru.  

## <a name="certificate-contacts"></a>Kontakty certifikátu

Kontakty certifikátu obsahují kontaktní informace pro odesílání oznámení aktivovaných událostmi životnosti certifikátu. Informace o kontaktech jsou sdíleny pomocí všech certifikátů v trezoru klíčů. Oznámení se pošle všem zadaným kontaktům pro událost pro libovolný certifikát v trezoru klíčů. Informace o tom, jak nastavit kontakt s certifikátem, najdete [tady](overview-renew-certificate.md#steps-to-set-certificate-notifications) .  

## <a name="certificate-access-control"></a>Access Control certifikátu

 Řízení přístupu k certifikátům zajišťuje služba Key Vault, ve které se certifikáty nacházejí. Zásady řízení přístupu pro certifikáty se liší od zásad řízení přístupu pro klíče a tajné klíče ve stejné Key Vault. Uživatelé mohou vytvořit jeden nebo více trezorů pro ukládání certifikátů, aby bylo možné zachovat vhodné segmentaci a správu certifikátů.  Další informace o řízení přístupu k certifikátu najdete [tady](certificate-access-control.md) .

## <a name="next-steps"></a>Další kroky

- [Informace o službě Key Vault](../general/overview.md)
- [Informace o klíčích, tajných kódech a certifikátech](../general/about-keys-secrets-certificates.md)
- [Informace o klíčích](../keys/about-keys.md)
- [Informace o tajných kódech](../secrets/about-secrets.md)
- [Ověřování, žádosti a odpovědi](../general/authentication-requests-and-responses.md)
- [Průvodce vývojáře pro službu Key Vault](../general/developers-guide.md)
