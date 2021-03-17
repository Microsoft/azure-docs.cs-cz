---
title: Sady deklarací identit Azure
description: Sady deklarací identity Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 23bcfcb92a7fa642e111a67bf92c1306a606bb2a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704799"
---
# <a name="claim-sets"></a>Sady deklarací identity

Deklarace identity vygenerované v procesu ověřování enclaves pomocí Microsoft Azure ověření identity mohou být rozděleny do následujících kategorií:

- **Příchozí deklarace identity**: deklarace identity vygenerované Microsoft azurem ověření identity po analýze legitimace ověřování a můžou je používat autoři zásad k definování autorizačních pravidel ve vlastních zásadách.

- **Odchozí deklarace identity**: deklarace identity vygenerované službou Azure Attestation a obsahují všechny deklarace identity, které končí v tokenu ověření identity.

- **Deklarace vlastností**: deklarace identity vytvořené jako výstup pomocí ověření identity Azure. Obsahuje všechny deklarace identity, které reprezentují vlastnosti tokenu ověření identity, jako je například kódování sestavy, doba platnosti sestavy atd.

### <a name="common-incoming-claims-across-all-attestation-types"></a>Běžné příchozí deklarace identity napříč všemi typy ověření identity

Níže jsou deklarace identity generované službou Azure Attestation a můžou je používat autoři zásad k definování autorizačních pravidel ve vlastních zásadách pro všechny typy ověření identity.

- **x-MS-ver**: verze schématu JWT (očekávaná jako "1,0")
- **x-MS-ověření identity-typ**: řetězcová hodnota představující typ ověření identity 
- **x-MS-Policy-hash**: hash zásad hodnocení Azure Attestation vypočítaných jako BASE64URL (SHA256 (UTF8 (BASE64URL (UTF8 (text zásady))))
- **x-MS-Policy-Signer**: objekt JSON s členem "JWK", který představuje klíč, který zákazník použil k podepsání svých zásad, když zákazník nahraje podepsané zásady

Níže jsou deklarace, které se považují za zastaralé, ale jsou plně podporované. Doporučuje se používat nepoužívané názvy deklarací identity.

Zastaralá deklarace identity | Doporučená deklarace identity 
--- | --- 
ver | x-MS-ver
Tee | x-MS-ověření identity – typ
maa-policyHash | x-MS-Policy-hash
policy_hash | x-MS-Policy-hash
policy_signer | x-MS-Policy-Signer

### <a name="common-outgoing-claims-across-all-attestation-types"></a>Běžné odchozí deklarace identity napříč všemi typy ověření identity

Níže jsou deklarace identity zahrnuté v tokenu ověření identity pro všechny typy ověření identity službou.

Zdroj: definovaný serverem [IETF JWT](https://tools.ietf.org/html/rfc7519)

- **Deklarace identity "JTI" (JWT ID)**
- **Deklarace identity "ISS" (Issuer)**
- **Deklarace identity "IAT" (vydáno na)**
- **Deklarace identity "EXP" (čas vypršení platnosti)**
- **Deklarace "NBF" (ne před)**

Zdroj: podle definice [IETF Eat](https://tools.ietf.org/html/draft-ietf-rats-eat-03#page-9)

- **Hodnota Nonce deklarace (nonce)**

Ve výchozím nastavení jsou v tokenu ověření zahrnuty deklarace identity na základě příchozích deklarací identity:

- **x-MS-ver**: verze schématu JWT (očekávaná jako "1,0")
- **x-MS-ověření identity-typ**: řetězcová hodnota představující typ ověření identity 
- **x-MS-Policy-hash**: hodnota řetězce obsahující SHA256 hash textu zásad vypočítaného BASE64URL (SHA256 (UTF8 (BASE64URL (UTF8 (text zásady))))
- **x-MS-Policy-Signer**: obsahuje JWK s veřejným klíčem nebo řetěz certifikátů, který se nachází v podepsané hlavičce zásad. x-MS-Policy-Signer se přidá jenom v případě, že je zásada podepsaná.

## <a name="claims-specific-to-sgx-enclaves"></a>Deklarace identity specifické pro SGX enclaves

### <a name="incoming-claims-specific-to-sgx-attestation"></a>Příchozí deklarace identity specifické pro ověření identity SGX

Níže jsou deklarace identity vygenerované službou Azure Attestation a můžou je používat autoři zásad k definování autorizačních pravidel ve vlastních zásadách pro SGX ověření identity.

- **x-MS-SGX-je-laditelné**: logická hodnota, která označuje, jestli má enklávy povolený ladění nebo ne.
- **x-MS-SGX-Product-ID**
- **x-MS-SGX-mrsigner**: hodnota zakódovaná v poli "mrsigner" v uvozovkách
- **x-MS-SGX-mrenclave**: hodnota zakódovaná v poli "mrenclave" v uvozovkách
- **x-MS-SGX-SVN**: číslo verze zabezpečení kódované v uvozovkách 

### <a name="outgoing-claims-specific-to-sgx-attestation"></a>Odchozí deklarace identity specifické pro ověření identity SGX

Služba pro ověření identity SGX vygenerovala tyto deklarace a zahrne je do tokenu ověření identity.

- **x-MS-SGX-je-laditelné**: logická hodnota, která označuje, jestli má enklávy povolený ladění nebo ne.
- **x-MS-SGX-Product-ID**
- **x-MS-SGX-mrsigner**: hodnota zakódovaná v poli "mrsigner" v uvozovkách
- **x-MS-SGX-mrenclave**: hodnota zakódovaná v poli "mrenclave" v uvozovkách
- **x-MS-SGX-SVN**: číslo verze zabezpečení kódované v uvozovkách 
- **x-MS-SGX-EHD**: enklávy uchovává data FORMÁTOVANÁ jako BASE64URL (enklávy uchovávaná data).
- **x-MS-SGX-** prokládání: objekt JSON popisující materiály použité k provedení ověření identity. Hodnota pro deklaraci identity x-MS-SGX-promítá je vnořený objekt JSON s následujícími páry klíč/hodnota:
    - **qeidcertshash**: SHA256 hodnota QEch certifikátů pro vydávání identity
    - **qeidcrlhash**: SHA256 hodnota QE seznamu certifikátů pro certifikáty vystavování identit identity
    - **qeidhash**: SHA256 hodnota pro QE identity
    - **quotehash**: SHA256 hodnota vyhodnocené uvozovky
    - **tcbinfocertshash**: SHA256 hodnota informací o vystavování certifikátů TCB
    - **tcbinfocrlhash**: SHA256 hodnota informací o seznamu CRL vydaných certifikátů TCB
    - **tcbinfohash**: objekt JSON popisující materiál, který se používá k provedení ověření identity

Níže jsou deklarace, které se považují za zastaralé, ale jsou plně podporované a budou i nadále zahrnuty do budoucna. Doporučuje se používat nepoužívané názvy deklarací identity.

Zastaralá deklarace identity | Doporučená deklarace identity
--- | --- 
$is-laditelné | x-MS-SGX-je laditelné
$sgx – mrsigner | x-MS-SGX-mrsigner
$sgx – mrenclave | x-MS-SGX-mrenclave
$product – ID | x-MS-SGX-Product-ID
$svn | x-MS-SGX-SVN
$tee | x-MS-ověření identity – typ
maa-ehd | x-MS-SGX-EHD
AAS – EHD | x-MS-SGX-EHD
maa-attestationcollateral | x-MS-SGX-prosourozenci

## <a name="claims-specific-to-trusted-platform-module-tpm-vbs-attestation"></a>Deklarace identity specifické pro ověření identity TPM (Trusted Platform Module)/VBS

### <a name="incoming-claims-for-tpm-attestation"></a>Příchozí deklarace identity pro ověření TPM

Deklarace identity vydané Azure Attestation pro ověření identity TPM Dostupnost deklarací identity závisí na důkazech poskytnutých pro ověření identity.

- **aikValidated**: logická hodnota obsahující informace, pokud byl ověřený certifikát sady AIK (Attestation Identity Key).
- **aikPubHash**: řetězec obsahující base64 (SHA256 (veřejný klíč sady AIK ve formátu der))
- **tpmVersion**: celočíselná hodnota obsahující hlavní verzi čipu TPM (Trusted Platform Module)
- **secureBootEnabled**: logická hodnota označující, zda je povoleno zabezpečené spouštění
- **iommuEnabled**: logická hodnota označující, jestli je povolená jednotka pro správu paměti vstupu a výstupu (IOMMU)
- **bootDebuggingDisabled**: logická hodnota označující, jestli je zakázané spouštěcí ladění
- **notSafeMode**: logická hodnota určující, zda systém Windows není spuštěn v nouzovém režimu
- **notWinPE**: logická hodnota označující, že systém Windows není spuštěn v režimu WinPE
- **vbsEnabled**: logická hodnota označující, jestli je POVOLENý vbs
- **vbsReportPresent**: logická hodnota označující, zda je k dispozici sestava vbs enklávy

### <a name="incoming-claims-for-vbs-attestation"></a>Příchozí deklarace identity pro VBS

Deklarace identity vydané službou Azure Attestation pro VBS pro ověřování VBS jsou kromě deklarací dostupných k dispozici pro ověření identity čipem TPM. Dostupnost deklarací identity závisí na důkazech poskytnutých pro ověření identity.

- **enclaveAuthorId**: hodnota řetězce obsahující Base64Url kódovanou hodnotu ID autora enklávy – identifikátor autora primárního modulu pro enklávy
- **enclaveImageId**: hodnota řetězce obsahující Base64Urlou hodnotu ID image enklávy – identifikátor image primárního modulu pro enklávy
- **enclaveOwnerId**: hodnota řetězce obsahující Base64Url kódovanou hodnotu ID vlastníka enklávy – identifikátor vlastníka pro enklávy
- **enclaveFamilyId**: hodnota řetězce obsahující Base64Url KÓDOVANOU hodnotu ID rodiny enklávy. Identifikátor rodiny primárního modulu pro enklávy
- **enclaveSvn**: celočíselná hodnota obsahující číslo verze zabezpečení primárního modulu pro enklávy
- **enclavePlatformSvn**: celočíselná hodnota obsahující číslo verze zabezpečení platformy, která hostuje enklávy
- **enclaveFlags**: deklarace EnclaveFlags je celočíselná hodnota obsahující příznaky, které popisují zásady modulu runtime pro enklávy

### <a name="outgoing-claims-specific-to-tpm-and-vbs-attestation"></a>Odchozí deklarace identity specifické pro ověření identity TPM a VBS

- **CNF (potvrzení)**: deklarace identity "CNF" se používá k identifikaci klíče pro ověření příznaku. Potvrzovací deklarace definovaná v dokumentu RFC 7800 obsahuje veřejnou část enklávy klíče, který je reprezentován jako objekt webového klíče JSON (JWK) (RFC 7517).
- **rp_data (data předávající strany)**: data předávající strany, pokud jsou zadána v žádosti, používaná předávající stranou jako hodnota nonce k zajištění aktuálnosti sestavy. rp_data se přidá jenom v případě, že existuje rp_data

### <a name="property-claims"></a>Deklarace vlastností

- **report_validity_in_minutes**: celočíselná deklarace, která označuje, jak dlouho je token platný.
  - **Výchozí hodnota (Time)**: jeden den v minutách.
  - **Maximální hodnota (Time)**: jeden rok v minutách.
- **omit_x5c**: logická deklarace identity, která označuje, jestli by ověřování Azure mělo vynechat certifikát, který se používá k zajištění ověření pravosti služby. Při hodnotě true se x5t přidá do tokenu ověření identity. Pokud je hodnota false (výchozí), X5C se přidá do tokenu ověření.

## <a name="next-steps"></a>Další kroky
- [Jak vytvářet a podepisovat zásady ověření identity](author-sign-policy.md)
- [Nastavení ověření Azure pomocí PowerShellu](quickstart-powershell.md)
