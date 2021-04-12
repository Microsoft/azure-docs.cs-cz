---
title: Azure Key Vault jako zdroj Event Grid
description: V této části najdete popis vlastností a schématu poskytnutých pro Azure Key Vault události Azure Event Grid
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: ea8821b15000b74a10f28730ccf82b538e7819e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100363402"
---
# <a name="azure-key-vault-as-event-grid-source"></a>Azure Key Vault jako zdroj Event Grid

Tento článek poskytuje vlastnosti a schéma pro události v [Azure Key Vault](../key-vault/index.yml). Úvod do schémat událostí najdete v tématu [Azure Event Grid schéma událostí](event-schema.md).


## <a name="available-event-types"></a>Dostupné typy událostí

Účet Azure Key Vault generuje následující typy událostí:

| Úplný název události | Zobrazovaný název události | Description |
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
| Microsoft. klíčů trezor. VaultAccessPolicyChanged | Zásady přístupu k trezoru se změnily. | Aktivuje se při změně zásady přístupu u Key Vault. Zahrnuje scénář při změně Key Vault modelu oprávnění na řízení přístupu na základě role v Azure.   |

## <a name="event-examples"></a>Příklady událostí

# <a name="event-grid-event-schema"></a>[Schéma událostí služby Event Grid](#tab/event-grid-event-schema)

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

# <a name="cloud-event-schema"></a>[Schéma cloudových událostí](#tab/cloud-event-schema)

Následující příklad ukazuje schéma pro **Microsoft. SecretNewVersionCreated trezor**:

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "source":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "type":"Microsoft.KeyVault.SecretNewVersionCreated",
      "time":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "specversion":"1.0"
   }
]
```

---

### <a name="event-properties"></a>Vlastnosti události

# <a name="event-grid-event-schema"></a>[Schéma událostí služby Event Grid](#tab/event-grid-event-schema)
Událost má následující data nejvyšší úrovně:

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| `topic` | řetězec | Úplná cesta prostředku ke zdroji událostí. Do tohoto pole nejde zapisovat. Tuto hodnotu poskytuje Event Grid. |
| `subject` | řetězec | Cesta k předmětu události, kterou definuje vydavatel. |
| `eventType` | řetězec | Jeden z registrovaných typů události pro tento zdroj události. |
| `eventTime` | řetězec | Čas, kdy se událost generuje na základě času UTC poskytovatele. |
| `id` | řetězec | Jedinečný identifikátor události |
| `data` | object | Data události konfigurace aplikace |
| `dataVersion` | řetězec | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| `metadataVersion` | řetězec | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |


# <a name="cloud-event-schema"></a>[Schéma cloudových událostí](#tab/cloud-event-schema)

Událost má následující data nejvyšší úrovně:

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| `source` | řetězec | Úplná cesta prostředku ke zdroji událostí. Do tohoto pole nejde zapisovat. Tuto hodnotu poskytuje Event Grid. |
| `subject` | řetězec | Cesta k předmětu události, kterou definuje vydavatel. |
| `type` | řetězec | Jeden z registrovaných typů události pro tento zdroj události. |
| `time` | řetězec | Čas, kdy se událost generuje na základě času UTC poskytovatele. |
| `id` | řetězec | Jedinečný identifikátor události |
| `data` | object | Data události konfigurace aplikace |
| `specversion` | řetězec | Verze specifikace schématu CloudEvents |

---
 

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Description |
| ---------- | ----------- |---|
| `id` | řetězec | ID objektu, který aktivoval tuto událost |
| `vaultName` | řetězec | Název trezoru klíčů u objektu, který aktivoval tuto událost |
| `objectType` | řetězec | Typ objektu, který aktivoval tuto událost |
| `objectName` | řetězec | Název objektu, který aktivoval tuto událost |
| `version` | řetězec | Verze objektu, který aktivoval tuto událost |
| `nbf` | číslo | Datum před dnem v sekundách od roku 1970-01-01T00:00:00Z objektu, který aktivoval tuto událost |
| `exp` | číslo | Datum vypršení platnosti v sekundách od 1970-01-01T00:00:00Z objektu, který aktivoval tuto událost |

## <a name="tutorials-and-how-tos"></a>Kurzy a postupy
|Nadpis  |Popis  |
|---------|---------|
| [Monitorování událostí Key Vault s využitím Azure Event Grid](../key-vault/general/event-grid-overview.md) | Přehled integrace Key Vault s Event Grid |
| [Kurz: vytváření a sledování událostí Key Vault s využitím Event Grid](../key-vault/general/event-grid-logicapps.md) | Přečtěte si, jak nastavit oznámení Event Grid pro Key Vault. |


## <a name="next-steps"></a>Další kroky

* Úvod do Azure Event Grid najdete v tématu [co je Event Grid?](overview.md).
* Další informace o tom, jak vytvořit předplatné Azure Event Grid, najdete v tématu [Event Grid schématu předplatného](subscription-creation-schema.md).
* Další informace o Key Vault najdete v tématu [co je Azure Key Vault?](../key-vault/general/overview.md)

