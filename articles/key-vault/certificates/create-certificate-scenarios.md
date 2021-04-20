---
title: Monitorování a správa vytvoření certifikátu
description: Scénáře demonstrují škálu možností pro vytváření, monitorování a interakci s procesem vytváření certifikátů pomocí Key Vault.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 8e4acb5195497dd31f466829b1cde301ba9696b3
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751089"
---
# <a name="monitor-and-manage-certificate-creation"></a>Monitorování a správa vytvoření certifikátu
Platí pro: Azure

Scénáře/operace popsaných v tomto článku jsou:

- Požádat o certifikát KV s podporovaným vystavitelem
- Žádost o přijetí žádosti – stav žádosti je "probíhá"
- Žádost o získání žádosti čeká na dokončení.
- Čeká se na vyřízení žádosti – stav žádosti čeká na zrušení nebo chyba.
- Žádost o přijetí žádosti – stav žádosti čekají na odstranění nebo přepsání
- Vytvořit (nebo importovat) v případě, že žádost čeká na vyřízení – stav je "probíhá"
- Sloučit po vytvoření nevyřízené žádosti pomocí vystavitele (například DigiCert)
- Požádat o zrušení, zatímco stav žádosti čeká na "probíhá"
- Odstranění objektu žádosti čeká na vyřízení
- Ruční vytvoření certifikátu KV
- Sloučení při vytvoření nevyřízené žádosti – ruční vytvoření certifikátu

## <a name="request-a-kv-certificate-with-a-supported-issuer"></a>Požádat o certifikát KV s podporovaným vystavitelem 

|Metoda|Identifikátor URI žádosti|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

Následující příklady vyžadují, aby objekt s názvem "mydigicert" byl již k dispozici ve vašem trezoru klíčů s poskytovatelem vystavitele jako DigiCert. Vystavitel certifikátu je entita reprezentovaná v Azure Key Vault (KV) jako prostředek CertificateIssuer. Slouží k poskytnutí informací o zdroji certifikátu KV; název vystavitele, poskytovatel, přihlašovací údaje a další podrobnosti o správě.

### <a name="request"></a>Žádost

```json
{
  "policy": {
    "x509_props": {
      "subject": "CN=MyCertSubject1"
    },
    "issuer": {
      "name": "mydigicert",
      "cty": "OV-SSL",
    }
  }
}
```

### <a name="response"></a>Odpověď

```
StatusCode: 202, ReasonPhrase: 'Accepted'
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "mydigicert"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "InProgress",
  "status_details": "Pending certificate created. Certificate request is in progress. This may take some time based on the issuer provider. Please check again later",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}

```

## <a name="get-pending-request---request-status-is-inprogress"></a>Žádost o přijetí žádosti – stav žádosti je "probíhá"

|Metoda|Identifikátor URI žádosti|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Žádost
Čtěte `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

NEBO

Čtěte `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

> [!NOTE]
> Pokud je v dotazu zadána *request_id* , funguje jako filtr. Pokud *request_id* v dotazu a v objektu, který čeká na vyřízení, se vrátí stavový kód HTTP 404.

### <a name="response"></a>Odpověď

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "inProgress",
  "status_details": "…",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}

```

## <a name="get-pending-request---request-status-is-complete"></a>Žádost o získání žádosti čeká na dokončení.

### <a name="request"></a>Žádost

|Metoda|Identifikátor URI žádosti|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

Čtěte `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

NEBO

Čtěte `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Odpověď

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "completed",
  "request_id": "a76827a18b63421c917da80f28e9913d",
  "target": “https://mykeyvault.vault.azure.net/certificates/mycert1?api-version={api-version}"
}

```

## <a name="get-pending-request---pending-request-status-is-canceled-or-failed"></a>Čeká se na vyřízení žádosti – stav žádosti čeká na zrušení nebo chyba.

### <a name="request"></a>Žádost

|Metoda|Identifikátor URI žádosti|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

Čtěte `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

NEBO

Čtěte `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Odpověď

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "failed",
  "status_details": "",
  "request_id": "a76827a18b63421c917da80f28e9913d",
  "error": {
    "code": "<errorcode>",
    "message": "<message>"
  }
}

```

> [!NOTE]
> Hodnota *ErrorCode* může být "Chyba vystavitele certifikátu" nebo "žádost zamítnutá" na základě chyby vystavitele nebo uživatele.

## <a name="get-pending-request---pending-request-status-is-deleted-or-overwritten"></a>Žádost o přijetí žádosti – stav žádosti čekají na odstranění nebo přepsání
Objekt, který čeká na vyřízení, může být odstraněn nebo přepsán operací vytvořit/importovat, pokud jeho stav není "probíhá".

|Metoda|Identifikátor URI žádosti|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Žádost
Čtěte `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

NEBO

Čtěte `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Odpověď

```
StatusCode: 404, ReasonPhrase: 'Not Found'
{
  "error": {
    "code": "PendingCertificateNotFound",
    "message": "…"
  }
}

```

## <a name="create-or-import-when-pending-request-exists---status-is-inprogress"></a>Vytvořit (nebo importovat) v případě, že žádost čeká na vyřízení – stav je "probíhá"
Objekt, který čeká na vyřízení, má čtyři možné stavy; "InProgress", "Canceled", "Failed" nebo "Completed".

Pokud je stav probíhající žádosti "probíhá", operace vytvoření (a importu) selžou se stavovým kódem HTTP 409 (konflikt).

Postup při opravě konfliktu:

- Pokud se certifikát vytváří ručně, můžete dokončit jeho provedení sloučením nebo odstraněním u objektu, který čeká na vyřízení.

- Pokud se certifikát vytváří pomocí vystavitele, můžete počkat, až se certifikát dokončí, dojde k chybě nebo je zrušený. Alternativně můžete odstranit objekt, který čeká na vyřízení.

> [!NOTE]
> Odstranění objektu, který čeká na vyřízení, může nebo nemusí zrušit žádost o certifikát x509 u poskytovatele.

|Metoda|Identifikátor URI žádosti|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

### <a name="request"></a>Žádost

```json
{
  "policy": {
    "x509_props": {
      "subject": "CN=MyCertSubject1"
    },
    "issuer": {
      "name": "mydigicert"
    }
  }
}
```

### <a name="response"></a>Odpověď

```
StatusCode: 409, ReasonPhrase: 'Conflict'
{
  "error": {
    "code": "Forbidden",
    "message": "A new key vault certificate can not be created or imported while a pending key vault certificate's status is inProgress."
  }
}

```

## <a name="merge-when-pending-request-is-created-with-an-issuer"></a>Sloučit po vytvoření nevyřízené žádosti s vystavitelem
Sloučení není povoleno, pokud je objekt s vystavitelem vytvořen, ale je povolen, když je jeho stav "probíhá". 

Pokud se žádost o vytvoření certifikátu x509 nezdařila nebo se z nějakého důvodu zruší, a pokud se certifikát x509 dá načíst pomocí vzdálené správy, může se provést operace sloučení, která dokončí certifikát KV.

|Metoda|Identifikátor URI žádosti|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|

### <a name="request"></a>Žádost

```json
{
  "x5c": [ "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8=" ]
}

```

### <a name="response"></a>Odpověď

```json
StatusCode: 403, ReasonPhrase: 'Forbidden'
{
  "error": {
    "code": "Forbidden",
    "message": "Merge is forbidden on pending object created with issuer : <issuer-name> while it is in progess."
  }
}

```

## <a name="request-a-cancellation-while-the-pending-request-status-is-inprogress"></a>Požádat o zrušení, zatímco stav žádosti čeká na "probíhá"
Zrušení může být požadováno pouze. Požadavek se může nebo nemusí zrušit. Pokud žádost není "neprůběh", vrátí se stav HTTP 400 (chybný požadavek).

|Metoda|Identifikátor URI žádosti|
|------------|-----------------|
|POUŽITA|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Žádost
POUŽITA `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

NEBO

POUŽITA `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

```json
{
  "cancellation_requested": true
}

```

### <a name="response"></a>Odpověď

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": true,
  "status": "inProgress",
  "status_details": "…",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}
```

## <a name="delete-a-pending-request-object"></a>Odstranění objektu žádosti čeká na vyřízení

> [!NOTE]
> Odstranění objektu, který čeká na vyřízení, může nebo nemusí zrušit žádost o certifikát x509 u poskytovatele.

|Metoda|Identifikátor URI žádosti|
|------------|-----------------|
|DELETE|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Žádost
DSTRANIT `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

NEBO

DSTRANIT `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Odpověď

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "inProgress",
  "request_id": "a76827a18b63421c917da80f28e9913d",
}
```

## <a name="create-a-kv-certificate-manually"></a>Ruční vytvoření certifikátu KV
Certifikát vydaný s vámi zvolenou certifikační autoritou můžete vytvořit prostřednictvím procesu ručního vytvoření. Nastavte název vystavitele na "Neznámý" nebo nezadávejte pole Issuer.

|Metoda|Identifikátor URI žádosti|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

### <a name="request"></a>Žádost

```json
{
  "policy": {
    "x509_props": {
      "subject": "CN=MyCertSubject1"
    },
    "issuer": {
      "name": "Unknown"
    }
  }
}

```

### <a name="response"></a>Odpověď

```
StatusCode: 202, ReasonPhrase: 'Accepted'
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "Unknown"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "status": "inProgress",
  "status_details": "Pending certificate created. Please Perform Merge to complete the request.",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}

```

## <a name="merge-when-a-pending-request-is-created---manual-certificate-creation"></a>Sloučení při vytvoření nevyřízené žádosti – ruční vytvoření certifikátu

|Metoda|Identifikátor URI žádosti|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|

### <a name="request"></a>Žádost

```json
{
  "x5c": [ "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8=" ]
}

```

|Název elementu|Požaduje se|Typ|Verze|Popis|
|------------------|--------------|----------|-------------|-----------------|
|x5c|Yes|array|\<introducing version>|Řetěz certifikátů x509 jako základní pole řetězců 64.|

### <a name="response"></a>Odpověď

```
StatusCode: 201, ReasonPhrase: 'Created'
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1?api-version={api-version}"
{
    "id": "https mykeyvault.vault.azure.net/certificates/mycert1/f366e1a9dd774288ad84a45a5f620352",
    "kid": "https:// mykeyvault.vault.azure.net/keys/mycert1/f366e1a9dd774288ad84a45a5f620352",
    "sid": " mykeyvault.vault.azure.net/secrets/mycert1/f366e1a9dd774288ad84a45a5f620352",
    "cer": "……de34534……",
    "x5t": "n14q2wbvyXr71Pcb58NivuiwJKk",
    "attributes": {
        "enabled": true,
        "exp": 1530394215,
        "nbf": 1435699215,
        "created": 1435699919,
        "updated": 1435699919
    },
    "pending": {
        "id": "https:// mykeyvault.vault.azure.net/certificates/mycert1/pending"
    },
    "policy": {
        "id": "https:// mykeyvault.vault.azure.net/certificates/mycert1/policy",
        "key_props": {
            "exportable": false,
            "kty": "RSA",
            "key_size": 2048,
            "reuse_key": false
        },
        "secret_props": {
            "contentType": "application/x-pkcs12"
        },
        "x509_props": {
            "subject": "CN=Mycert1",
            "ekus": ["1.3.6.1.5.5.7.3.1", "1.3.6.1.5.5.7.3.2"],
            "validity_months": 12
        },
        "lifetime_actions": [{
            "trigger": {
                "lifetime_percentage": 80
            },
            "action": {
                "action_type": "EmailContacts"
            }
        }],
        "issuer": {
            "name": "Unknown"
        },
        "attributes": {
            "enabled": true,
            "created": 1435699811,
            "updated": 1435699811
        }
    }
}

```
