---
title: Protokol zabezpečení založený na virtualizaci (VBS) pro ověření Azure
description: Protokol ověření identity VBS
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 38012c5b4bb9338c1200d9583256193ee8402c98
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99507875"
---
# <a name="trusted-platform-module-tpm-and-virtualization-based-securityvbs-enclave-attestation-protocol"></a>Trusted Platform Module (TPM) a enklávy protokol ověřování na základě virtualizace (Virtualization Security) 

Microsoft Azure ověřování pro poskytování silné záruky zabezpečení spoléhá na ověření řetězu důvěryhodnosti z kořene důvěryhodnosti (TPM) na spuštění hypervisoru a zabezpečeného jádra. Aby bylo možné tuto službu Azure Attestation Identity ověřit, musí se před vytvořením důvěry v zabezpečeném enklávyu ověřit stav spouštění počítače. Binární soubory jádra operačního systému, hypervisoru a zabezpečení musí být podepsané správnými oficiálními úřady Microsoftu a zabezpečeným způsobem. Jakmile máme vztah důvěryhodnosti mezi čipem TPM (Trusted Platform Module) a stavem hypervisoru, můžeme důvěřovat enklávy IDKs (Virtualization Security), která je součástí měřeného protokolu spuštění. díky tomu můžeme ověřit, že dvojice klíčů byla vygenerována enklávy a mentolová sestavu ověření identity, která váže vztah důvěryhodnosti v tomto klíči a obsahuje další deklarace identity, jako jsou například vlastnosti úrovně zabezpečení a ověření spouštění. 

VBS enclaves vyžaduje, aby čip TPM poskytoval měření pro ověření základu zabezpečení. VBS enclaves jsou ověřeny koncovým bodem čipu TPM s přičtením k objektu Request v protokolu. 

## <a name="protocol-messages"></a>Zprávy protokolu

### <a name="init-message"></a>Inicializační zpráva

#### <a name="direction"></a>Směr

Ověření identity klienta > Azure

#### <a name="payload"></a>Délka

```
{ 
  "type": "aikcert" 
} 
```

"Type" (řetězec ASCII): představuje typ vyžadovaného ověření identity. V současné době je podporována pouze možnost "aikcert".

### <a name="challenge-message"></a>Zpráva výzvy

#### <a name="direction"></a>Směr

Ověřování Azure – klient >

#### <a name="payload"></a>Délka

```
{ 
  "challenge": "<BASE64URL(CHALLENGE)>", 
  "service_context": "<BASE64URL(SERVICECONTEXT)>" 
} 
```

**Challenge** (BASE64URL (oktety)): náhodná hodnota vydaná službou.

**service_context** (BASE64URL (oktety)): neprůhledný kontext vytvořený službou.


### <a name="request-message"></a>Zpráva požadavku

#### <a name="direction"></a>Směr

Ověření identity klienta > Azure 

#### <a name="payload"></a>Délka

```
{
  "request": "<JWS>"
}
```

**Request** (JWS): požadavek se skládá ze struktury webového podpisu JSON (JWS). Níže jsou uvedeny hlavičky Protected JWS a datová část JWS. Stejně jako v jakékoli struktuře JWS se výsledná hodnota skládá z:

BASE64URL (UTF8 (JWS Protected Header)) | | '.' ||

BASE64URL (datová část JWS) | | '.' ||

BASE64URL (podpis JWS)

##### <a name="jws-protected-header"></a>JWS chráněná hlavička

```
{
  "alg": "PS256",
  "typ": "attReq"
  // no "kid" parameter as the key specified by attest_key MUST sign this JWS to prove possession.
}
```

##### <a name="jws-payload"></a>Datová část JWS

Datová část JWS může být typu Basic nebo VBS. Základní se používá v případě, že legitimace ověřování nezahrnuje data VBS. 

Ukázka pouze TPM: 

``` 
{ 
  "att_type": "basic", 
  "att_data": { 
    "rp_id": "<URL>", 
    "rp_data": "<BASE64URL(RPCUSTOMDATA)>", 
    "challenge": "<BASE64URL(CHALLENGE)>", 

    "tpm_att_data": { 
      "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>", 
      "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>", 
      "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>", 
      "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>", 
      "aik_cert": "<BASE64URL(AIKCERTIFICATE)>", 

      // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517). 

      "aik_pub": { 
        "kty": "RSA", 
        "n": "<Base64urlUInt(MODULUS)>", 
        "e": "<Base64urlUInt(EXPONENT)>" 
      }, 
      "current_claim": "<BASE64URL(CURRENTCLAIM)>", 
      "boot_claim": "<BASE64URL(BOOTCLAIM)>" 
    }, 

    // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517). 

    "attest_key": { 
      "kty": "RSA", 
      "n": "<Base64urlUInt(MODULUS)>", 
      "e": "<Base64urlUInt(EXPONENT)>" 
    }, 
    "custom_claims": [ 
      { 
        "name": "<name>", 
        "value": "<value>", 
        "value_type": "<value_type>" 
      }, 
      { 
        "name": "<name>", 
        "value": "<value>", 
        "value_type": "<value_type>" 
      } 
    ], 
    "service_context": "<BASE64URL(SERVICECONTEXT)>" 
  } 
} 
```

Ukázka čipu TPM + VBS enklávy: 

``` 
{ 
  "att_type": "vbs", 
  "att_data": { 
    "report_signed": { 
      "rp_id": "<URL>", 
      "rp_data": "<BASE64URL(RPCUSTOMDATA)>", 
      "challenge": "<BASE64URL(CHALLENGE)>", 
      "tpm_att_data": { 
        "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>", 
        "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>", 
        "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>", 
        "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>", 
        "aik_cert": "<BASE64URL(AIKCERTIFICATE)>", 

        // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517). 

        "aik_pub": { 
          "kty": "RSA", 
          "n": "<Base64urlUInt(MODULUS)>", 
          "e": "<Base64urlUInt(EXPONENT)>" 
        }, 
        "current_claim": "<BASE64URL(CURRENTCLAIM)>", 
        "boot_claim": "<BASE64URL(BOOTCLAIM)>" 
      }, 

      // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517). 

      "attest_key": { 
        "kty": "RSA", 
        "n": "<Base64urlUInt(MODULUS)>", 
        "e": "<Base64urlUInt(EXPONENT)>" 
      }, 
      "custom_claims": [ 
        { 
          "name": "<name>", 
          "value": "<value>", 
          "value_type": "<value_type>" 
        }, 
        { 
          "name": "<name>", 
          "value": "<value>", 
          "value_type": "<value_type>" 
        } 
      ], 
      "service_context": "<BASE64URL(SERVICECONTEXT)>" 
    }, 
    "vsm_report": "<BASE64URL(REPORT)>" 
  } 
} 
``` 

**rp_id** (StringOrURI): identifikátor předávající strany. Používá se službou při výpočtu deklarace identity ID počítače.

**rp_data** (BASE64URL (oktety)): neprůhledná data předaná předávající stranou. Tuto hodnotu obvykle používá předávající strana jako hodnota nonce k zajištění aktuálnosti sestavy.

**Challenge** (BASE64URL (oktety)): náhodná hodnota vydaná službou

**tpm_att_data**: data ověřování související s čipem TPM

- **srtm_boot_log (BASE64URL (oktety))**: protokoly spouštění SRTM, které jsou načteny funkcí Tbsi_Get_TCG_Log_Ex s typem log = TBS_TCGLOG_SRTM_BOOT

- **srtm_resume_log (BASE64URL (oktety))**: SRTM obnoví protokol, jak je načten funkcí Tbsi_Get_TCG_Log_Ex s typem log = TBS_TCGLOG_SRTM_RESUME

- **drtm_boot_log (BASE64URL (oktety))**: protokoly spouštění drtm, které jsou načteny funkcí Tbsi_Get_TCG_Log_Ex s typem log = TBS_TCGLOG_DRTM_BOOT

- **drtm_resume_log (BASE64URL (oktety))**: drtm obnoví protokol, jak je načten funkcí Tbsi_Get_TCG_Log_Ex s typem log = TBS_TCGLOG_DRTM_RESUME

- **aik_cert (BASE64URL (oktety))**: certifikát X. 509 pro AIK, jak vrátila funkce NCryptGetProperty s vlastností = NCRYPT_CERTIFICATE_PROPERTY

- **aik_pub**: veřejná část sady AIK reprezentovaná jako objekt JWK (JSON web Key) (RFC 7517)

- **current_claim (BASE64URL (oktety))**: deklarace identity ověření pro aktuální stav PCR vracené funkcí NCryptCreateClaim s dwClaimType = NCRYPT_CLAIM_PLATFORM a parametr NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK nastavenou na zahrnutí všech Registry. Při výpočtu této deklarace identity by se měla použít taky výzva poslaná službou.

- **boot_claim (BASE64URL (oktety))**: deklarace identity ověření pro stav PCR při spuštění, jakou vrátila funkce NCryptCreateClaim s dwClaimType = NCRYPT_CLAIM_PLATFORM a parametr NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK nastavenou tak, aby zahrnovala všechny Registry

**vsm_report**   (BASE64URL (OKTETy)): sestava VBS enklávy Attestation, jak vrátila funkce EnclaveGetAttestationReport. Parametr EnclaveData musí být hodnota hash SHA-512 hodnoty report_signed (včetně levé a pravé složené závorky). Vstup funkce hash je UTF8 (report_signed).

**attest_key**: veřejná část enklávy klíče reprezentovaná jako objekt JSON web Key (JWK) (RFC 7517)

**custom_claims**: pole vlastních deklarací enklávy odeslaných službě, které může zásada vyhodnotit. Deklarace identity

- **název (řetězec)**: název deklarace identity. Tento název se připojí k adrese URL určené službou ověření identity (aby se zamezilo konfliktům) a zřetězeným řetězcem bude typ deklarace identity, která se dá použít v zásadě.

- **Value (String)**: hodnota deklarace identity

- **value_type (String)**: datový typ hodnoty deklarace identity

**service_context** (BASE64URL (oktety)): neprůhledný kontext vytvořený službou.

### <a name="report-message"></a>Zpráva sestavy

#### <a name="direction"></a>Směr

Ověřování Azure – klient >

#### <a name="payload"></a>Délka

```
{
  "report": "<JWT>"
}
```

**Report** (Jwt): sestava ověření identity ve formátu JSON web token (Jwt) (RFC 7519).

## <a name="next-steps"></a>Další kroky

- [Pracovní postup Azure Attestation](workflow.md)
