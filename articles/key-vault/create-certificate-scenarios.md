---
title: Monitorování a správa vytvoření certifikátu
description: Scénáře demonstrující řadu možností pro vytváření, monitorování a interakci s procesem vytváření certifikátů s trezorem klíčů.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 2c6d33acddb106a3c032c8e21d3111cb428b9a45
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631644"
---
# <a name="monitor-and-manage-certificate-creation"></a>Monitorování a správa vytvoření certifikátu
Platí pro: Azure

Scénáře / operace uvedené v tomto článku jsou:

- Žádost o certifikát KV s podporovaným vystavitelem
- Získat čekající požadavek - stav požadavku je "inProgress"
- Získat nevyřízený požadavek - stav požadavku je "dokončen"
- Získat nevyřízený požadavek – stav čekající na vyřízení požadavku je "zrušen" nebo "neúspěšný"
- Získat čekající požadavek – stav čekající na vyřízení požadavku je "odstraněn" nebo "přepsán"
- Vytvořit (nebo importovat) v případě, že existuje nevyřízený požadavek - stav je "inProgress"
- Sloučení při vytvoření čekajícího požadavku s vydavatelem (například DigiCert)
- Požádat o zrušení, zatímco stav čekající požadavek je "inProgress"
- Odstranění objektu čekajícího požadavku
- Ruční vytvoření certifikátu KV
- Sloučení při vytvoření čekajícího požadavku – ruční vytvoření certifikátu

## <a name="request-a-kv-certificate-with-a-supported-issuer"></a>Žádost o certifikát KV s podporovaným vystavitelem 

|Metoda|Identifikátor URI žádosti|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

Následující příklady vyžadují objekt s názvem "mydigicert" již být k dispozici v trezoru klíčů s poskytovatelem vystavitele jako DigiCert. Vystavitel certifikátu je entita reprezentovaná v trezoru klíčů Azure (KV) jako prostředek certifikátu issuer. Používá se k poskytnutí informací o zdroji certifikátu KV; název vystavitele, zprostředkovatele, pověření a další podrobnosti o správě.

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

## <a name="get-pending-request---request-status-is-inprogress"></a>Získat čekající požadavek - stav požadavku je "inProgress"

|Metoda|Identifikátor URI žádosti|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Žádost
Dostat`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

NEBO

Dostat`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

> [!NOTE]
> Pokud je v dotazu zadán *request_id,* bude se chová jako filtr. Pokud *request_id* v dotazu a čekající objekt se liší, je vrácen stavový kód http 404.

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

## <a name="get-pending-request---request-status-is-complete"></a>Získat nevyřízený požadavek - stav požadavku je "dokončen"

### <a name="request"></a>Žádost

|Metoda|Identifikátor URI žádosti|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

Dostat`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

NEBO

Dostat`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

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

## <a name="get-pending-request---pending-request-status-is-canceled-or-failed"></a>Získat nevyřízený požadavek – stav čekající na vyřízení požadavku je "zrušen" nebo "neúspěšný"

### <a name="request"></a>Žádost

|Metoda|Identifikátor URI žádosti|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

Dostat`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

NEBO

Dostat`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

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
> Hodnota *chybového kódu* může být "Chyba vystavittele certifikátu" nebo "Žádost odmítnuta" na základě chyby vystavittele nebo uživatele.

## <a name="get-pending-request---pending-request-status-is-deleted-or-overwritten"></a>Získat čekající požadavek – stav čekající na vyřízení požadavku je "odstraněn" nebo "přepsán"
Čekající objekt může být odstraněn nebo přepsán operací vytvoření nebo importu, pokud jeho stav není "inProgress".

|Metoda|Identifikátor URI žádosti|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Žádost
Dostat`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

NEBO

Dostat`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

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

## <a name="create-or-import-when-pending-request-exists---status-is-inprogress"></a>Vytvořit (nebo importovat) v případě, že existuje nevyřízený požadavek - stav je "inProgress"
Čekající objekt má čtyři možné stavy; "neprobíhá", "zrušeno", "nepodařilo se" nebo "dokončeno".

Pokud je stav čekající žádosti "probíhá", vytvoření (a import) operace se nezdaří s http stavový kód 409 (konflikt).

Jak konflikt vyřešit:

- Pokud je certifikát vytvářen ručně, můžete buď dokončit certifikát KV sloučením nebo odstranit objekt čekající na vyřízení.

- Pokud je certifikát vytvářen s vystavitelem, můžete počkat, dokud certifikát nedokončí, selže nebo bude zrušen. Případně můžete odstranit čekající objekt.

> [!NOTE]
> Odstranění čekajícího objektu může nebo nemusí zrušit žádost o certifikát x509 u poskytovatele.

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

## <a name="merge-when-pending-request-is-created-with-an-issuer"></a>Sloučení při vytvoření čekajícího požadavku s vystavitelem
Sloučení není povoleno, pokud je čekající objekt vytvořen s vystavitelem, ale je povolen, pokud je jeho stav "inProgress". 

Pokud požadavek na vytvoření certifikátu x509 z nějakého důvodu selže nebo zruší a certifikát x509 lze načíst nepásmovými prostředky, lze operaci sloučení provést k dokončení certifikátu KV.

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

## <a name="request-a-cancellation-while-the-pending-request-status-is-inprogress"></a>Požádat o zrušení, zatímco stav čekající požadavek je "inProgress"
O zrušení lze požádat pouze. Požadavek může, ale nemusí být zrušen. Pokud požadavek není "inProgress", je vrácen stav http 400 (Bad Request).

|Metoda|Identifikátor URI žádosti|
|------------|-----------------|
|Oprava|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Žádost
Oprava`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

NEBO

Oprava`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

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

## <a name="delete-a-pending-request-object"></a>Odstranění objektu čekajícího požadavku

> [!NOTE]
> Odstranění čekajícího objektu může nebo nemusí zrušit žádost o certifikát x509 u poskytovatele.

|Metoda|Identifikátor URI žádosti|
|------------|-----------------|
|DELETE|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Žádost
Odstranit`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

NEBO

Odstranit`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

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
Pomocí ručního vytváření můžete vytvořit certifikát vydaný certifikační autoritou podle vašeho výběru. Nastavte název vystavittele na "Neznámý" nebo nezadávejte pole vystavittele.

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

## <a name="merge-when-a-pending-request-is-created---manual-certificate-creation"></a>Sloučení při vytvoření čekajícího požadavku – ruční vytvoření certifikátu

|Metoda|Identifikátor URI žádosti|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|

### <a name="request"></a>Žádost

```json
{
  "x5c": [ "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8=" ]
}

```

|Název elementu|Požaduje se|Typ|Version|Popis|
|------------------|--------------|----------|-------------|-----------------|
|x5c|Ano|pole|\<zavedení verze>|Řetěz certifikátů X509 jako základní pole 64 řetězců.|

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

## <a name="see-also"></a>Viz také
- [Informace o klíčích, tajných kódech a certifikátech](about-keys-secrets-and-certificates.md)
