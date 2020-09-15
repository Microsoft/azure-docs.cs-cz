---
title: Azure Key Vault jako zdroj Event Grid
description: V této části najdete popis vlastností a schématu poskytnutých pro Azure Key Vault události Azure Event Grid
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: f6e2bdfb2000f3a4c4a8f91eee23348d9cc9c766
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90090393"
---
# <a name="azure-key-vault-as-event-grid-source"></a>Azure Key Vault jako zdroj Event Grid

Tento článek poskytuje vlastnosti a schéma pro události v [Azure Key Vault](../key-vault/index.yml). Úvod do schémat událostí najdete v tématu [Azure Event Grid schéma událostí](event-schema.md).

## <a name="event-grid-event-schema"></a>Schéma událostí služby Event Grid

### <a name="available-event-types"></a>Dostupné typy událostí

Účet Azure Key Vault generuje následující typy událostí:

| Úplný název události | Zobrazovaný název události | Popis |
| ---------- | ----------- |---|
| Microsoft. klíčů trezor. CertificateNewVersionCreated | Nově vytvořená verze certifikátu | Aktivuje se při vytvoření nového certifikátu nebo verze nového certifikátu. |
| Microsoft. klíčů trezor. CertificateNearExpiry | Brzo vyprší platnost certifikátu | Aktivuje se v případě vypršení platnosti aktuální verze certifikátu. (Událost se aktivuje 30 dní před datem vypršení platnosti.) |
| Microsoft. klíčů trezor. CertificateExpired | Platnost certifikátu vypršela | Aktivováno, když vypršela platnost certifikátu. |
| Microsoft. klíčů trezor. KeyNewVersionCreated | Byla vytvořena klíčová nová verze. | Aktivuje se při vytvoření nového klíče nebo verze nového klíče. |
| Microsoft. klíčů trezor. KeyNearExpiry | Klíč s blížícím se koncem platnosti | Aktivuje se, když brzy vyprší platnost aktuální verze klíče. (Událost se aktivuje 30 dní před datem vypršení platnosti.) |
| Platnost služby Microsoft. klíčů trezor. | Platnost klíče vypršela. | Aktivuje se, když vypršela platnost klíče. |
| Microsoft. klíčů trezor. SecretNewVersionCreated | Byla vytvořena tajná nová verze. | Aktivuje se, když se vytvoří nový tajný kód nebo nová tajná verze. |
| Microsoft. klíčů trezor. SecretNearExpiry | Tajný kód v blízkosti vypršení platnosti | Aktivuje se, když brzy vyprší platnost aktuální verze tajného kódu. (Událost se aktivuje 30 dní před datem vypršení platnosti.) |
| Microsoft. klíčů trezor. SecretExpired | Platnost tajného kódu vypršela | Aktivováno, když vypršela platnost tajného klíče. |
| Microsoft. klíčů trezor. VaultAccessPolicyChanged | Zásady přístupu k trezoru se změnily. | Aktivuje se při změně zásady přístupu u Key Vault. Zahrnuje situaci, kdy se model oprávnění Key Vault změnil do/z Azure RBAC.  |

### <a name="event-examples"></a>Příklady událostí

Následující příklad ukazuje schéma pro **Microsoft. SecretNewVersionCreated trezor**:

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "topic":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "eventType":"Microsoft.KeyVault.SecretNewVersionCreated",
      "eventTime":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "dataVersion":"1",
      "metadataVersion":"1"
   }
]
```

### <a name="event-properties"></a>Vlastnosti události

Událost má následující data nejvyšší úrovně:

| Vlastnost | Typ | Popis |
| ---------- | ----------- |---|
| id | řetězec | ID objektu, který aktivoval tuto událost |
| vaultName | řetězec | Název trezoru klíčů u objektu, který aktivoval tuto událost |
| objectType | řetězec | Typ objektu, který aktivoval tuto událost |
| objectName | řetězec | Název objektu, který aktivoval tuto událost |
| verze | řetězec | Verze objektu, který aktivoval tuto událost |
| NBF | číslo | Datum před dnem v sekundách od roku 1970-01-01T00:00:00Z objektu, který aktivoval tuto událost |
| exp | číslo | Datum vypršení platnosti v sekundách od 1970-01-01T00:00:00Z objektu, který aktivoval tuto událost |

## <a name="tutorials-and-how-tos"></a>Kurzy a postupy
|Nadpis  |Popis  |
|---------|---------|
| [Monitorování událostí Key Vault s využitím Azure Event Grid](../key-vault/general/event-grid-overview.md) | Přehled integrace Key Vault s Event Grid |
| [Kurz: vytváření a sledování událostí Key Vault s využitím Event Grid](../key-vault/general/event-grid-tutorial.md) | Přečtěte si, jak nastavit oznámení Event Grid pro Key Vault. |


## <a name="next-steps"></a>Další kroky

* Úvod do Azure Event Grid najdete v tématu [co je Event Grid?](overview.md).
* Další informace o tom, jak vytvořit předplatné Azure Event Grid, najdete v tématu [Event Grid schématu předplatného](subscription-creation-schema.md).
* Další informace o integraci Key Vault s Event Grid najdete v tématu [monitorování Key Vault Azure Event Grid](../key-vault/general/event-grid-overview.md).
* Kurz týkající se Key Vault integrace s Event Grid najdete v tématu [příjem a reakce na oznámení o trezoru klíčů pomocí Azure Event Grid](../key-vault/general/event-grid-tutorial.md).
* Další pokyny pro Key Vault a Azure Automation najdete v těchto tématech:
    - [Co je Azure Key Vault?](../key-vault/general/overview.md)
    - [Monitorování Key Vault s využitím Azure Event Grid](../key-vault/general/event-grid-overview.md)
    - [Příjem a reakce na oznámení o trezoru klíčů pomocí Azure Event Grid](../key-vault/general/event-grid-tutorial.md)
    - [Přehled Azure Automation](../automation/index.yml)
