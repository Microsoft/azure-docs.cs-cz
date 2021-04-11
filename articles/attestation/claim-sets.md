---
title: Sady deklarací identit Azure
description: Sady deklarací identity Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 0d6d5a08ea85ebb666acc0336f1e1d7ec5e097da
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044664"
---
# <a name="claim-sets"></a>Sady deklarací identity

Deklarace identity vygenerované v procesu ověřování enclaves pomocí Microsoft Azure ověření identity mohou být rozděleny do následujících kategorií:

- **Příchozí deklarace identity**: deklarace identity vygenerované Microsoft azurem ověření identity po analýze legitimace ověřování a můžou je používat autoři zásad k definování autorizačních pravidel ve vlastních zásadách.

- **Odchozí deklarace identity**: deklarace identity vygenerované službou Azure Attestation a zahrnuté do tokenu ověření identity

- **Deklarace vlastností**: deklarace identity vytvořené jako výstup pomocí ověření identity Azure. Obsahuje všechny deklarace identity, které reprezentují vlastnosti tokenu ověření identity, jako je například kódování sestavy, doba platnosti sestavy atd.

## <a name="incoming-claims"></a>Příchozí deklarace identity 

### <a name="sgx-attestation"></a>Ověření identity SGX

Deklarace identity, které budou používat autoři zásad k definování autorizačních pravidel v zásadách ověřování SGX:

- **x-MS-SGX-je-laditelné**: logická hodnota, která označuje, jestli má enklávy povolený ladění nebo ne.
- **x-MS-SGX-Product-ID**: hodnota ID produktu SGX enklávy 
- **x-MS-SGX-mrsigner**: hodnota zakódovaná v poli "mrsigner" v uvozovkách
- **x-MS-SGX-mrenclave**: hodnota zakódovaná v poli "mrenclave" v uvozovkách
- **x-MS-SGX-SVN**: číslo verze zabezpečení kódované v uvozovkách 

Níže jsou deklarace, které se považují za zastaralé, ale jsou plně podporované a budou i nadále zahrnuty do budoucna. Doporučuje se používat nepoužívané názvy deklarací identity.

Zastaralá deklarace identity | Doporučená deklarace identity
--- | --- 
$is-laditelné | x-MS-SGX-je laditelné
$product – ID | x-MS-SGX-Product-ID
$sgx – mrsigner | x-MS-SGX-mrsigner
$sgx – mrenclave | x-MS-SGX-mrenclave
$svn | x-MS-SGX-SVN

### <a name="tpm-attestation"></a>Osvědčení TPM

Deklarace identity, které budou používat autoři zásad k definování autorizačních pravidel v zásadách ověření identity TPM:

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

### <a name="vbs-attestation"></a>VBS Attestation

Kromě deklarací identity zásad ověření identity TPM můžou autoři zásad používat níže uvedené deklarace identity, které definují autorizační pravidla v zásadách ověřování VBS.

- **enclaveAuthorId**: hodnota řetězce obsahující Base64Url kódovanou hodnotu ID autora enklávy – identifikátor autora primárního modulu pro enklávy
- **enclaveImageId**: hodnota řetězce obsahující Base64Urlou hodnotu ID image enklávy – identifikátor image primárního modulu pro enklávy
- **enclaveOwnerId**: hodnota řetězce obsahující Base64Url kódovanou hodnotu ID vlastníka enklávy – identifikátor vlastníka pro enklávy
- **enclaveFamilyId**: hodnota řetězce obsahující Base64Url KÓDOVANOU hodnotu ID rodiny enklávy. Identifikátor rodiny primárního modulu pro enklávy
- **enclaveSvn**: celočíselná hodnota obsahující číslo verze zabezpečení primárního modulu pro enklávy
- **enclavePlatformSvn**: celočíselná hodnota obsahující číslo verze zabezpečení platformy, která hostuje enklávy
- **enclaveFlags**: deklarace EnclaveFlags je celočíselná hodnota obsahující příznaky, které popisují zásady modulu runtime pro enklávy

## <a name="outgoing-claims"></a>Odchozí deklarace identity 

### <a name="common-for-all-attestation-types"></a>Společné pro všechny typy ověření identity

Azure Attestation obsahuje níže uvedené deklarace identity v tokenu ověření pro všechny typy ověření identity. 

- **x-MS-ver**: verze schématu JWT (očekávaná jako "1,0")
- **x-MS-ověření identity-typ**: řetězcová hodnota představující typ ověření identity 
- **x-MS-Policy-hash**: hash zásad hodnocení Azure Attestation vypočítaných jako BASE64URL (SHA256 (UTF8 (BASE64URL (UTF8 (text zásady))))
- **x-MS-Policy-Signer**: objekt JSON s členem "JWK", který představuje klíč, který zákazník použil k podepsání svých zásad. To platí, když zákazník nahraje podepsané zásady.

Ve [specifikaci IETF JWT](https://tools.ietf.org/html/rfc7519) se používají níže uvedené názvy deklarací identity.

- **deklarace identity JTI (ID JWT)** – jedinečný identifikátor pro token JWT
- **deklarace identity "ISS" (Issuer)** – objekt zabezpečení, který VYSTAVIL token JWT. 
- **deklarace identity "IAT" (vydáno na)** – čas vydání tokenu JWT 
- **"EXP" (čas vypršení platnosti) deklarace identity** – čas vypršení platnosti, po kterém nesmí být požadavek JWT přijat ke zpracování
- **deklarace "NBF" (ne před) deklarací** – ne před časem, po který nesmí být požadavek JWT přijat ke zpracování 

Níže uvedené názvy deklarací identity se používají ze [specifikace IETF Eat konceptu](https://tools.ietf.org/html/draft-ietf-rats-eat-03#page-9) .

- Hodnota **nonce Claim (nonce)** – netransformovaná Přímá kopie volitelné hodnoty nonce, kterou poskytuje klient 

Níže jsou deklarace, které se považují za zastaralé, ale jsou plně podporované a budou i nadále zahrnuty do budoucna. Doporučuje se používat nepoužívané názvy deklarací identity.

Zastaralá deklarace identity | Doporučená deklarace identity
--- | --- 
ver | x-MS-ver
Tee | x-MS-ověření identity – typ
policy_hash | x-MS-Policy-hash
maa-policyHash | x-MS-Policy-hash
policy_signer  | x-MS-Policy-Signer

### <a name="sgx-attestation"></a>Ověření identity SGX 

Služba pro ověření identity SGX vygenerovala tyto deklarace a zahrne je do tokenu ověření identity.

- **x-MS-SGX-je-laditelné**: logická hodnota, která označuje, jestli má enklávy povolený ladění nebo ne.
- **x-MS-SGX-Product-ID**: hodnota ID produktu SGX enklávy 
- **x-MS-SGX-mrsigner**: hodnota zakódovaná v poli "mrsigner" v uvozovkách
- **x-MS-SGX-mrenclave**: hodnota zakódovaná v poli "mrenclave" v uvozovkách
- **x-MS-SGX-SVN**: číslo verze zabezpečení kódované v uvozovkách 
- **x-MS-SGX-EHD**: enklávy uchovává data FORMÁTOVANÁ jako BASE64URL (enklávy uchovávaná data).
- **x-MS-SGX-** prokládání: objekt JSON popisující materiály použité k provedení ověření identity. Hodnota pro deklaraci identity x-MS-SGX-promítá je vnořený objekt JSON s následujícími páry klíč/hodnota:
    - **qeidcertshash**: SHA256 hodnota Quoting ENKLÁVY (QE) vystavujících certifikátů identity
    - **qeidcrlhash**: SHA256 hodnota QE seznamu certifikátů pro certifikáty vystavování identit identity
    - **qeidhash**: SHA256 hodnota pro QE identity
    - **quotehash**: SHA256 hodnota vyhodnocené uvozovky
    - **tcbinfocertshash**: SHA256 hodnota informací o vystavování certifikátů TCB
    - **tcbinfocrlhash**: SHA256 hodnota informací o seznamu CRL vydaných certifikátů TCB
    - **tcbinfohash**: SHA256 hodnota informačního podkladu TCB

Níže jsou deklarace, které se považují za zastaralé, ale jsou plně podporované a budou i nadále zahrnuty do budoucna. Doporučuje se používat nepoužívané názvy deklarací identity.

Zastaralá deklarace identity | Doporučená deklarace identity
--- | --- 
$is-laditelné | x-MS-SGX-je laditelné
$product – ID | x-MS-SGX-Product-ID
$sgx – mrsigner | x-MS-SGX-mrsigner
$sgx – mrenclave | x-MS-SGX-mrenclave
$svn | x-MS-SGX-SVN
$maa – EHD | x-MS-SGX-EHD
$aas – EHD | x-MS-SGX-EHD
$maa – attestationcollateral | x-MS-SGX-prosourozenci

### <a name="tpm-and-vbs-attestation"></a>Ověření identity TPM a VBS

- **CNF (potvrzení)**: deklarace identity "CNF" se používá k identifikaci klíče pro ověření příznaku. Potvrzovací deklarace definovaná v dokumentu RFC 7800 obsahuje veřejnou část enklávy klíče, který je reprezentován jako objekt webového klíče JSON (JWK) (RFC 7517).
- **rp_data (data předávající strany)**: data předávající strany, pokud jsou zadána v žádosti, používaná předávající stranou jako hodnota nonce k zajištění aktuálnosti sestavy. rp_data se přidá jenom v případě, že existuje rp_data

## <a name="property-claims"></a>Deklarace vlastností

### <a name="tpm-and-vbs-attestation"></a>Ověření identity TPM a VBS

- **report_validity_in_minutes**: celočíselná deklarace, která označuje, jak dlouho je token platný.
  - **Výchozí hodnota (Time)**: jeden den v minutách.
  - **Maximální hodnota (Time)**: jeden rok v minutách.
- **omit_x5c**: logická deklarace identity, která označuje, jestli by ověřování Azure mělo vynechat certifikát, který se používá k zajištění ověření pravosti služby. Při hodnotě true se x5t přidá do tokenu ověření identity. Pokud je hodnota false (výchozí), X5C se přidá do tokenu ověření.

## <a name="next-steps"></a>Další kroky
- [Jak vytvářet a podepisovat zásady ověření identity](author-sign-policy.md)
- [Nastavení ověření Azure pomocí PowerShellu](quickstart-powershell.md)
