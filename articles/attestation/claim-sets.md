---
title: Sady deklarací identit Azure
description: Sady deklarací identity Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: e17002534d35f477467f0c35833560a0267dd596
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "92909773"
---
# <a name="claim-sets"></a>Sady deklarací identity

Deklarace identity vygenerované v procesu ověřování enclaves pomocí Microsoft Azure ověření identity mohou být rozděleny do následujících kategorií:

- **Příchozí deklarace identity** : deklarace identity vygenerované Microsoft Azure ověření identity po analýze legitimace ověření identity.

- **Odchozí deklarace identity** : deklarace identity vytvořené jako výstup pomocí ověření identity Azure. Obsahuje všechny deklarace identity, které by měly skončit v tokenu ověření identity.

- **Deklarace vlastností** : deklarace identity vytvořené jako výstup pomocí ověření identity Azure. Obsahuje všechny deklarace identity, které reprezentují vlastnosti tokenu ověření identity, jako je například kódování sestavy, doba platnosti sestavy atd.

Pod deklaracemi, které jsou definované pomocí RFC JWT a které používá Azure Attestation v objektu Response:

- **deklarace identity "ISS" (Issuer)** : deklarace "ISS" (Issuer) identifikuje objekt zabezpečení, který VYSTAVIL token JWT. Zpracování této deklarace identity je všeobecně specifické pro aplikaci. Hodnota "ISS" je řetězec rozlišující velká a malá písmena obsahující hodnotu StringOrURI.
- **deklarace identity "IAT" (vydáno na)** : deklarace identity "IAT" (vydané na adrese) identifikuje čas vydání tokenu JWT. Tato deklarace identity se dá použít k určení stáří tokenu JWT. Jeho hodnota musí být číslo obsahující hodnotu NumericDate.
- **deklarace "EXP" (čas vypršení platnosti)** : deklarace "EXP" (čas vypršení platnosti) identifikuje čas vypršení platnosti nebo po kterém nesmí být požadavek JWT přijat ke zpracování. Zpracování deklarace identity exp vyžaduje, aby aktuální datum a čas MUSELy být před datem a časem vypršení platnosti uvedené v deklaraci "EXP".

  Poznámka: do času vydání (IAT) se přidá 5 minut Leeway, aby se zohlednila časová zkosená doba.
- **deklarace "NBF" (ne dřív)** : deklarace "NBF" (ne dřív) označuje dobu, před kterou nebude požadavek JWT přijat ke zpracování. Zpracování deklarace "NBF" vyžaduje, aby aktuální datum a čas musel být pozdější nebo rovno datu a času, které je uvedené v deklaraci "NBF".
  Poznámka: do času vydání (IAT) se přidá 5 minut Leeway, aby se zohlednila časová zkosená doba.

## <a name="claims-issued-by-azure-attestation-in-sgx-enclaves"></a>Deklarace identity vydané Azure Attestation v SGX enclaves

### <a name="incoming-claims"></a>Příchozí deklarace identity 

- **$is-laditelné** : logická hodnota, která označuje, jestli má enklávy povolené ladění nebo ne.
- **$SGX-mrsigner** : šestnáctková hodnota zakódovaná pro pole mrsigner v uvozovkách
- **$SGX-mrenclave** : šestnáctková hodnota zakódovaná pro pole mrenclave v uvozovkách
- **$product – ID**
- **$SVN** : číslo verze zabezpečení kódované v uvozovkách 
- **$Tee** : typ enklávy 

### <a name="outgoing-claims"></a>Odchozí deklarace identity

- **je-laditelné** : logická hodnota, která označuje, jestli má enklávy povolené ladění nebo ne.
- **SGX-mrsigner** : šestnáctková hodnota v poli "mrsigner" v uvozovkách
- **SGX-mrenclave** : šestnáctková hodnota v poli "mrenclave" v uvozovkách
- **ID produktu**
- **SVN** : číslo verze zabezpečení kódované v uvozovkách 
- **Tee** : typ enklávy 
- **Maa-EHD** : Base64Url kódovaná verze enklávy uchovávaných dat, která jsou uvedena v žádosti o ověření identity 
- **AAS-EHD** : Base64Url kódovaná verze "enklávy uchovávaných dat" zadaných v žádosti o ověření identity 

## <a name="claims-issued-by-azure-attestation-in-vbs-enclaves"></a>Deklarace identity vydané Azure Attestation v VBS enclaves

### <a name="incoming-claims-can-also-be-used-as-outgoing-claims"></a>Příchozí deklarace identity (můžou se taky používat jako odchozí deklarace identity)

- **aikValidated** : logická hodnota obsahující informace, pokud byl ověřený certifikát sady AIK (Attestation Identity Key).
- **aikPubHash** : řetězec obsahující base64 (SHA256 (veřejný klíč sady AIK ve formátu der)).
- **tpmVersion** : celočíselná hodnota obsahující hlavní verzi čipu TPM (Trusted Platform Module).
- **secureBootEnabled** : logická hodnota určující, zda je povoleno zabezpečené spuštění.
- **iommuEnabled** : logická hodnota určující, zda je povolena jednotka pro správu paměti vstupu a výstupu (IOMMU).
- **bootDebuggingDisabled** : logická hodnota označující, zda je spouštěcí ladění zakázáno.
- **notSafeMode** : logická hodnota určující, zda systém Windows není spuštěn v nouzovém režimu.
- **notWinPE** : logická hodnota označující, zda systém Windows není spuštěn v režimu WinPE.
- **vbsEnabled** : logická hodnota označující, zda je povolený vbs.
- **vbsReportPresent** : logická hodnota označující, zda je k dispozici sestava vbs enklávy.
- **enclaveAuthorId** : hodnota řetězce obsahující Base64Url kódovanou hodnotu ID autora enklávy – identifikátor autora primárního modulu pro enklávy.
- **enclaveImageId** : hodnota řetězce obsahující Base64Urlou hodnotu ID image enklávy – identifikátor image primárního modulu pro enklávy.
- **enclaveOwnerId** : hodnota řetězce obsahující Base64Url kódovanou hodnotu ID vlastníka enklávy – identifikátor vlastníka pro enklávy.
- **enclaveFamilyId** : hodnota řetězce obsahující Base64Url kódovanou hodnotu ID rodiny enklávy. Identifikátor rodiny primárního modulu pro enklávy.
- **enclaveSvn** : celočíselná hodnota obsahující číslo verze zabezpečení primární modul pro enklávy.
- **enclavePlatformSvn** : celočíselná hodnota obsahující číslo verze zabezpečení platformy, která hostuje enklávy.
- **enclaveFlags** : deklarace EnclaveFlags je celočíselná hodnota obsahující příznaky, které popisují zásady modulu runtime pro enklávy.
  
### <a name="outgoing-claims"></a>Odchozí deklarace identity

- **policy_hash** : hodnota řetězce obsahující SHA256 hash textu zásad vypočítaného pomocí BASE64URL (SHA256 (BASE64URL (UTF8 (text zásady))).
- **policy_signer** : obsahuje JWK s veřejným klíčem nebo řetěz certifikátů, který se nachází v podepsané hlavičce zásad.
- **ver (verze)** : řetězcová hodnota obsahující verzi sestavy. Aktuálně 1,0.
- **deklarace identity CNF (potvrzení)** : k identifikaci klíče pro kontrolu příznaku se používá deklarace identity CNF. Potvrzovací deklarace definovaná v dokumentu RFC 7800 obsahuje veřejnou část enklávy klíče s ověřovacím kódem, který je reprezentován jako objekt webového klíče JSON (JWK) (RFC 7517).
- **rp_data (data předávající strany)** : data předávající strany, pokud jsou zadána v žádosti, používaná předávající stranou jako hodnota nonce k zajištění aktuálnosti sestavy.
- **deklarace identity JTI (ID JWT)** : deklarace identity JTI (ID JWT) poskytuje jedinečný identifikátor tokenu JWT. Hodnota identifikátoru je přiřazena způsobem, který zajišťuje nezanedbatelnou pravděpodobnost, že stejná hodnota bude omylem přiřazena k jinému datovému objektu.

### <a name="property-claims"></a>Deklarace vlastností

- **report_validity_in_minutes** : celočíselná deklarace, která označuje, jak dlouho je token platný.
  - **Výchozí hodnota (Time)** : jeden den v minutách.
  - **Maximální hodnota (Time)** : jeden rok v minutách.
- **omit_x5c** : logická deklarace identity, která označuje, jestli by ověřování Azure mělo vynechat certifikát, který se používá k zajištění ověření pravosti služby. Při hodnotě true se x5t přidá do tokenu ověření identity. Pokud je hodnota false (výchozí), X5C se přidá do tokenu ověření.

## <a name="next-steps"></a>Další kroky
- [Jak vytvářet a podepisovat zásady ověření identity](author-sign-policy.md)
- [Nastavení ověření Azure pomocí PowerShellu](quickstart-powershell.md)
