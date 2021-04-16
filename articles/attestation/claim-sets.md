---
title: Sady deklarací identit Azure
description: Sady deklarací identity Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: e82e9fc93bf8c816fcbfd5869156745dea630313
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517551"
---
# <a name="claim-sets"></a>Sady deklarací identity

Deklarace identity vygenerované v procesu ověřování enclaves pomocí Microsoft Azure ověření identity mohou být rozděleny do následujících kategorií:

- **Příchozí deklarace identity**: deklarace identity vygenerované Microsoft azurem ověření identity po analýze legitimace ověřování a můžou je používat autoři zásad k definování autorizačních pravidel ve vlastních zásadách.

- **Odchozí deklarace identity**: deklarace identity vygenerované službou Azure Attestation a zahrnuté do tokenu ověření identity

- **Deklarace vlastností**: deklarace identity vytvořené jako výstup pomocí ověření identity Azure. Obsahuje všechny deklarace identity, které reprezentují vlastnosti tokenu ověření identity, jako je například kódování sestavy, doba platnosti sestavy atd.

## <a name="incoming-claims"></a>Příchozí deklarace identity 

### <a name="sgx-attestation"></a>Ověření identity SGX

Deklarace identity, které budou používat autoři zásad k definování autorizačních pravidel v zásadách ověřování SGX:

- **x-MS-SGX-je-laditelné**: logická hodnota, která indikuje, jestli je povolené ladění enklávy, nebo ne.
  
  SGX enclaves lze načíst s vypnutým nebo povoleným laděním. Pokud je příznak nastaven na hodnotu true v enklávy, povolí funkce ladění pro kód enklávy. To zahrnuje možnost přístupu k paměti enklávy. Proto se doporučuje nastavit příznak na hodnotu true pouze pro účely vývoje. Pokud je tato možnost povolená v produkčním prostředí, záruky zabezpečení SGX se nezachovají.
  
  Uživatelé Azure Attestation můžou pomocí zásad ověření identity ověřit, jestli je ladění pro SGX enklávy zakázané. Po přidání pravidla zásad se ověření identity nezdaří, pokud uživatel se zlými úmysly zapne podporu ladění, aby získal přístup k enklávy obsahu.

- **x-MS-SGX-Product-ID**: celočíselná hodnota, která označuje ID produktu SGX enklávy.

  Enklávy autor přiřadí každému enklávyu ID produktu. ID produktu umožňuje, aby autor enklávy segment enclaves podepsaný pomocí stejné MRSIGNER. Když v zásadách ověření identity přidáte ověřovací pravidlo, můžou si zákazníci ověřit, jestli používají zamýšlenou enclaves. Ověření identity se nezdaří, pokud ID produktu enklávy neodpovídá hodnotě publikované autorem enklávy.

- **x-MS-SGX-mrsigner**: řetězcová hodnota, která IDENTIFIKUJE autora SGX enklávy.

  MRSIGNER je hodnota hash veřejného klíče autora enklávy, který se používá k podepsání binárního souboru enklávy. Když zákazníci ověřují MRSIGNER prostřednictvím zásad ověřování identity, můžou ověřit, jestli jsou v enklávy spuštěné důvěryhodné binární soubory. Pokud deklarace identity zásady neodpovídá MRSIGNER autora enklávy, znamená to, že binární soubor enklávy není podepsán důvěryhodným zdrojem a ověření identity se nezdařilo.
  
  Když autor enklávy preferuje MRSIGNER z bezpečnostních důvodů, je nutné aktualizovat zásady ověření Azure tak, aby podporovaly nové a staré hodnoty MRSIGNER před aktualizací binárních souborů. V opačném případě se kontroly autorizace nezdaří v důsledku selhání ověřování identity.
  
  Zásady ověření identity je potřeba aktualizovat pomocí níže uvedeného formátu. 
 
  #### <a name="before-key-rotation"></a>Před otočením klíče
 
   ```
    version= 1.0;
    authorizationrules 
    {
    [ type=="x-ms-sgx-is-debuggable", value==false]&&
    [ type=="x-ms-sgx-mrsigner", value=="mrsigner1"] => permit(); 
    };
  ```

   #### <a name="during-key-rotation"></a>Během střídání klíčů

    ```
      version= 1.0;
      authorizationrules 
      {
      [ type=="x-ms-sgx-is-debuggable", value==false]&&
      [ type=="x-ms-sgx-mrsigner", value=="mrsigner1"] => permit(); 
      [ type=="x-ms-sgx-is-debuggable", value==false ]&& 
      [ type=="x-ms-sgx-mrsigner", value=="mrsigner2"] => permit(); 
      };
    ```

   #### <a name="after-key-rotation"></a>Po otočení klíče

    ```
      version= 1.0;
      authorizationrules 
      { 
      [ type=="x-ms-sgx-is-debuggable", value==false]&& 
      [ type=="x-ms-sgx-mrsigner", value=="mrsigner2"] => permit(); 
      };
    ```

- **x-MS-SGX-mrenclave**: řetězcová hodnota, která identifikuje kód a data načtená v enklávy paměti. 

  MRENCLAVE je jedno z enklávy měření, které lze použít k ověření binárních souborů enklávy. Je to hodnota hash kódu běžícího uvnitř enklávy. Měření se mění při každé změně binárního kódu enklávy. Když zákazníci ověřují MRENCLAVE prostřednictvím zásad ověřování identity, můžou ověřit, jestli jsou v enklávy spuštěné zamýšlené binární soubory. Nicméně vzhledem k tomu, že MRENCLAVE se očekává, že se často mění jakákoli triviální Úprava stávajícího kódu, doporučuje se ověřit binární soubory enklávy pomocí ověřování MRSIGNER v zásadách ověření identity.

- **x-MS-SGX-SVN**: celočíselná hodnota, která označuje číslo verze zabezpečení SGX enklávy

  Enklávy autor přiřadí každé verzi SGX enklávy číslo verze zabezpečení (SVN). Když se v kódu enklávy zjistí problém se zabezpečením, enklávy autor zvýší hodnotu chyby zabezpečení post SVN. Aby se zabránilo interakci s nezabezpečeným enklávy kódem, můžou zákazníci přidat ověřovací pravidlo do zásad ověřování identity. Pokud SVN kódu enklávy neodpovídá verzi Doporučené autorem enklávy, ověření identity se nezdaří.

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
