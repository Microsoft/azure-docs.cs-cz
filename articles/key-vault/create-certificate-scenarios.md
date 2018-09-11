---
title: Monitorování a správa vytvoření certifikátu
description: Scénáře předvede celou řadu možností pro vytváření, monitorování a interakce s vytvoření certifikátu zpracovat pomocí služby Key Vault.
services: key-vault
documentationcenter: ''
author: bryanla
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 0d0995aa-b60d-4811-be12-ba0a45390197
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: bryanla
ms.openlocfilehash: 0e24bd56123279a24a72b9b52d8cb51e2a3a5eae
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44300626"
---
# <a name="monitor-and-manage-certificate-creation"></a>Monitorování a správa vytvoření certifikátu
Platí pro: Azure  

Následující 

Scénáře / operací popsaných v tomto článku jsou:

- Požádat o certifikát KV s podporovanou vystavitele
- Získání žádostí čekajících na vyřízení – stav žádosti je "inProgress"
- Získání žádostí čekajících na vyřízení – stav žádosti je "dokončených"
- Získejte žádost - čekající žádosti je "zrušeno" nebo "se nezdařilo" čekající na vyřízení
- Získejte žádost - čekající žádosti je "odstranit" nebo "přepsat" čekající na vyřízení
- Vytvořit (nebo Import) při čekající žádost existuje – stav je "inProgress"
- Sloučení při čekající žádost se vytvoří s vystavitele (například DigiCert)
- Požádat o zrušení čekající žádosti o stavu je "inProgress"
- Odstranění čekající žádosti o objektu
- Vytvořit certifikát KV ručně
- Sloučit při čekající žádosti o vytvoření – vytvoření ruční certifikátu

## <a name="request-a-kv-certificate-with-a-supported-issuer"></a>Požádat o certifikát KV s podporovanou vystavitele 

|Metoda|Identifikátor URI žádosti|  
|------------|-----------------|  
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|  

Následující příklady vyžadují objekt s názvem "mydigicert" už být k dispozici v trezoru klíčů pomocí zprostředkovatele vystavitele jako DigiCert. Vystavitel certifikátu se entitě reprezentované v Azure Key Vault (KV) jako prostředek CertificateIssuer. Používá se k zadání informací o zdroji KV certifikátu; název vystavitele, poskytovatele, přihlašovací údaje a další podrobnosti o správě.  

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

## <a name="get-pending-request---request-status-is-inprogress"></a>Získání žádostí čekajících na vyřízení – stav žádosti je "inProgress"

|Metoda|Identifikátor URI žádosti|  
|------------|-----------------|  
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

### <a name="request"></a>Žádost  
 ZÍSKAT `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 NEBO  

 ZÍSKAT `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

> [!NOTE]
>  Pokud *request_id* zadaný v dotazu, funguje jako filtr. Pokud *request_id* v dotazu a čeká na objektu se liší, je vrácen stavový kód http 404.  

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

## <a name="get-pending-request---request-status-is-complete"></a>Získání žádostí čekajících na vyřízení – stav žádosti je "dokončených"

### <a name="request"></a>Žádost  

|Metoda|Identifikátor URI žádosti|  
|------------|-----------------|  
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

 ZÍSKAT `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 NEBO  

 ZÍSKAT `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

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

## <a name="get-pending-request---pending-request-status-is-canceled-or-failed"></a>Získejte žádost - čekající žádosti je "zrušeno" nebo "se nezdařilo" čekající na vyřízení  

### <a name="request"></a>Žádost  

|Metoda|Identifikátor URI žádosti|  
|------------|-----------------|  
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

 ZÍSKAT `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 NEBO  

 ZÍSKAT  `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

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

## <a name="get-pending-request---pending-request-status-is-deleted-or-overwritten"></a>Získejte žádost - čekající žádosti je "odstranit" nebo "přepsat" čekající na vyřízení  
 Čeká na objekt lze odstraní nebo přepíše operace vytvoření nebo import při jeho stav není "inProgress".

|Metoda|Identifikátor URI žádosti|  
|------------|-----------------|  
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

### <a name="request"></a>Žádost  
 ZÍSKAT `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 NEBO  

 ZÍSKAT `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

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

## <a name="create-or-import-when-pending-request-exists---status-is-inprogress"></a>Vytvořit (nebo Import) při čekající žádost existuje – stav je "inProgress"
 Čeká na objekt má čtyři možné stavy; "inprogress", "zrušení", "se nezdařilo" nebo "dokončeno".

 Pokud stav čekající na vyřízení žádosti je "inprogress", vytvořit (a importu) operace selže s kódem stavu http 409 (konflikt).  

 Chcete-li vyřešit konflikt:  

 - Pokud ručně se vytváří certifikát, můžete dokončit KV certifikát provedením sloučení nebo odstranit čekající objektu.  

 - Pokud tento certifikát se vytváří s vystavitele, můžete počkat, dokud certifikát dokončí, selže nebo je zrušena. Alternativně můžete odstranit objekt čekající na vyřízení.

> [!NOTE]
> Odstraňuje se objekt čekající na vyřízení může nebo nemusí zrušit x509 žádost o certifikát s tímto poskytovatelem.  

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

## <a name="merge-when-pending-request-is-created-with-an-issuer"></a>Sloučit při vytvoření čekající žádost se vystavitele
 Sloučení není povolený, pokud čeká na objekt je vytvořen s vystavitele, ale je povolená, když má stav "inProgress". 

 Pokud požadavek na vytvoření x509 certifikátu se nezdaří vytváření či zruší z nějakého důvodu a pokud x x509 certifikát lze načíst prostředky out-of-band, sloučení operaci lze provést k dokončení KV certifikátu.  

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

## <a name="request-a-cancellation-while-the-pending-request-status-is-inprogress"></a>Požádat o zrušení čekající žádosti o stavu je "inProgress"  
 Zrušení je možné jenom požádat.  Žádost může nebo nemusí být zrušena. Pokud žádost není "inProgress", se vrátí stav http 400 (Chybný požadavek).  

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

## <a name="delete-a-pending-request-object"></a>Odstranění čekající žádosti o objektu  

> [!NOTE]
> Odstraňuje se objekt čekající na vyřízení může nebo nemusí zrušit x509 žádost o certifikát s tímto poskytovatelem.  

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

## <a name="create-a-kv-certificate-manually"></a>Vytvořit certifikát KV ručně  
 Můžete vytvořit certifikát vydaný s podmíněným Přístupem nástroje podle vašeho výběru pomocí procesu ruční vytvoření. Nastavte název vystavitele "Neznámá", nebo nezadávejte pole vystavitele.  

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

## <a name="merge-when-a-pending-request-is-created---manual-certificate-creation"></a>Sloučit při čekající žádosti o vytvoření – vytvoření ruční certifikátu  

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

|Název elementu|Požaduje se|Typ|Verze|Popis|  
|------------------|--------------|----------|-------------|-----------------|  
|x5c|Ano|pole|\<Představení verze >|Řetěz certifikátů X509 jako základní 64 řetězců.|  

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
