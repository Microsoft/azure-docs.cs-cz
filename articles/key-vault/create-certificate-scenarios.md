---
title: Sledování a správě vytváření certifikátu
description: Scénáře demonstraci celou řadu možností pro vytváření, monitorování a interakci s vytváření certifikátu zpracovat Key Vault.
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 0d0995aa-b60d-4811-be12-ba0a45390197
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: e1ea77304fa59b67e0e28a4c7e0b13633eeeff6f
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012078"
---
# <a name="monitor-and-manage-certificate-creation"></a>Sledování a správě vytváření certifikátu
Platí pro: Azure  

Následující 

Scénáře / operace uvedených v tomto článku jsou:

- Vyžádání certifikátu KV se podporované vystavitele
- Získat čekající na vyřízení žádosti – stav žádosti je "inProgress"
- Získat čekající na vyřízení žádosti – stav žádosti je "úplná"
- Získat čekající žádost - čekající žádost o stavu je "zrušení" nebo "se nezdařilo"
- Získat čekající žádost - čekající žádost o stavu je "odstraněny" nebo "přepsání"
- Vytvořit (nebo Import) při čekající na vyřízení požadavku existuje – stav je "inProgress"
- Sloučení při čekajícího požadavku je vytvořena s vystavitele (například DigiCert)
- Žádosti o zrušení "inProgress" při stavu čekající na vyřízení žádosti
- Odstranit objekt čekající na vyřízení žádosti
- Vytvoření certifikátu KV ručně
- Při vytváření čekající žádosti o - vytváření ruční certifikátu

## <a name="request-a-kv-certificate-with-a-supported-issuer"></a>Vyžádání certifikátu KV se podporované vystavitele 

|Metoda|Identifikátor URI žádosti|  
|------------|-----------------|  
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|  

Následující příklady vyžadují objekt s názvem "mydigicert" již být k dispozici v trezoru klíčů s poskytovatelem vystavitele jako DigiCert. Další informace o práci s vystavitelů najdete v tématu [vystavitelů certifikátů](/rest/api/keyvault/certificate-issuers.md).  

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

## <a name="get-pending-request---request-status-is-inprogress"></a>Získat čekající na vyřízení žádosti – stav žádosti je "inProgress"

|Metoda|Identifikátor URI žádosti|  
|------------|-----------------|  
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

### <a name="request"></a>Žádost  
 GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 NEBO  

 GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

> [!NOTE]
>  Pokud *request_id* je zadaný v dotazu, funguje jako filtr. Pokud *request_id* v dotazu a čeká na objekt se liší, je vrácen stavový kód 404 protokolu http.  

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

## <a name="get-pending-request---request-status-is-complete"></a>Získat čekající na vyřízení žádosti – stav žádosti je "úplná"

### <a name="request"></a>Žádost  

|Metoda|Identifikátor URI žádosti|  
|------------|-----------------|  
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

 GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 NEBO  

 GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

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

## <a name="get-pending-request---pending-request-status-is-canceled-or-failed"></a>Získat čekající žádost - čekající žádost o stavu je "zrušení" nebo "se nezdařilo"  

### <a name="request"></a>Žádost  

|Metoda|Identifikátor URI žádosti|  
|------------|-----------------|  
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

 GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 NEBO  

 GET  `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

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
   "error":  
    {  
        "code": "<errorcode>",    
        "message": "<message>"  
    }  
}  

```  

> [!NOTE]
> Hodnota *errorcode* může být "Chyba vystavitele certifikátu" nebo "Požadavek zamítnut" podle chyba vystavitele nebo uživatele v uvedeném pořadí.  

## <a name="get-pending-request---pending-request-status-is-deleted-or-overwritten"></a>Získat čekající žádost - čekající žádost o stavu je "odstraněny" nebo "přepsání"  
 Objekt čekající na vyřízení je možné odstranit nebo operaci Vytvořit nebo importovat přepsány, pokud stav není "inProgress".

|Metoda|Identifikátor URI žádosti|  
|------------|-----------------|  
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

### <a name="request"></a>Žádost  
 GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 NEBO  

 GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

### <a name="response"></a>Odpověď  

```  
StatusCode: 404, ReasonPhrase: 'Not Found'  
{  
  "error":  
    {  
         "code": "PendingCertificateNotFound",  
        "message": "…"  
    }  
}  

```  

## <a name="create-or-import-when-pending-request-exists---status-is-inprogress"></a>Vytvořit (nebo Import) při čekající na vyřízení požadavku existuje – stav je "inProgress"
 Objekt čekající má čtyři možné stavy; "inprogress", "zrušit", "se nezdařilo" nebo "dokončeno".

 Pokud je stav čekající žádost "inprogress", vytvořit (a importovat) operace selže s kódem stavu http 409 (konflikt).  

 Chcete-li vyřešit konflikt:  

 - Pokud ručně se vytváří certifikát, můžete dokončit KV certifikát pomocí tohoto postupu sloučení nebo odstranit čekající objektu.  

 - Pokud certifikát se vytváří pomocí vystavitele, můžete počkat, dokud certifikát dokončí, selže nebo je zrušena. Alternativně můžete odstranit objekt čekající na vyřízení.

> [!NOTE]
> Odstranit objekt čekající na vyřízení může nebo nemusí zrušit x509 žádost o certifikát s poskytovatelem.  

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
  "error":  
    {  
        "code": "Forbidden",  
        "message": "A new key vault certificate can not be created or imported while a pending key vault certificate's status is inProgress."  
    }  
}  

```  

## <a name="merge-when-pending-request-is-created-with-an-issuer"></a>Sloučení při čekající na vyřízení žádosti s vystavitele
 Merge není povolená, pokud objekt čekající na vyřízení je vytvořen s vystavitele, ale je povolená, pokud je jeho stav "inProgress". 

 Pokud požadavek na vytvoření x509 certifikát selže nebo zruší z nějakého důvodu, a pokud x509 certifikát se dá načíst out-of-band způsobem, lze provést operaci sloučení k dokončení KV certifikát.  

|Metoda|Identifikátor URI žádosti|  
|------------|-----------------|  
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|  

### <a name="request"></a>Žádost  

```json
{  
  "x5c": [  "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8="  
  ]  
}  

```  

### <a name="response"></a>Odpověď  

```json
StatusCode: 403, ReasonPhrase: 'Forbidden'  
{  
  "error":  
    {  
       "code": "Forbidden",  
       "message": "Merge is forbidden on pending object created with issuer : <issuer-name> while it is in progess."  
    }  
}  

```  

## <a name="request-a-cancellation-while-the-pending-request-status-is-inprogress"></a>Žádosti o zrušení "inProgress" při stavu čekající na vyřízení žádosti  
 Můžete požadovat jenom zrušení.  Žádost o může nebo nemusí být zrušena. Pokud požadavek není "inProgress", je vrácen stav http 400 (Chybný požadavek).  

|Metoda|Identifikátor URI žádosti|  
|------------|-----------------|  
|PATCH|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

### <a name="request"></a>Žádost  
 OPRAVA `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 NEBO  

 OPRAVA `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

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

## <a name="delete-a-pending-request-object"></a>Odstranit objekt čekající na vyřízení žádosti  

> [!NOTE]
> Odstranit objekt čekající na vyřízení může nebo nemusí zrušit x509 žádost o certifikát s poskytovatelem.  

|Metoda|Identifikátor URI žádosti|  
|------------|-----------------|  
|DELETE|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

### <a name="request"></a>Žádost  
 ODSTRANIT `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 NEBO  

 ODSTRANIT `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

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

## <a name="create-a-kv-certificate-manually"></a>Vytvoření certifikátu KV ručně  
 Můžete vytvořit certifikát vydaný s certifikační Autoritou zvoleného procesem ruční vytvoření. Nastavte název vystavitele na "Neznámý", nebo nezadávejte žádné pole Vystavitel.  

|Metoda|Identifikátor URI žádosti|  
|------------|-----------------|  
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|  

### <a name="request"></a>Žádost  

```json
{  
  "policy": {  
    "x509_props": {  
      "subject": "CN=MyCertSubject1"  
    }  
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

## <a name="merge-when-a-pending-request-is-created---manual-certificate-creation"></a>Při vytváření čekající žádosti o - vytváření ruční certifikátu  

|Metoda|Identifikátor URI žádosti|  
|------------|-----------------|  
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|  

### <a name="request"></a>Žádost  

```json
{  
  "x5c": [  "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8="  
  ]  
}  

```  

|Název elementu|Požadované|Typ|Verze|Popis|  
|------------------|--------------|----------|-------------|-----------------|  
|x5c|Ano|pole|\<Představení verze >|Řetěz certifikátů X509 jako základní pole 64 řetězců.|  

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
                       "validity_months":12  
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
