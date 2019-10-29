---
title: Schéma události Azure Event Grid Azure Key Vault
description: V této části najdete popis vlastností a schématu poskytnutých pro Azure Key Vault události Azure Event Grid
services: event-grid
author: msmbaldwin
ms.service: event-grid
ms.topic: reference
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: c92352dd28b870c5f58dec0b82a8000f14a8e62d
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73033521"
---
# <a name="azure-event-grid-event-schema-for-azure-key-vault-preview"></a>Azure Event Grid schéma událostí pro Azure Key Vault (Preview)

Tento článek poskytuje vlastnosti a schéma pro události [Azure Key Vault](../key-vault/index.yml) , které jsou aktuálně ve verzi Preview. Úvod do schémat událostí najdete v tématu [Azure Event Grid schéma událostí](event-schema.md).

## <a name="available-event-types"></a>Dostupné typy událostí

Účet Azure Key Vault emituje následující typy událostí:

| Úplný název události | Zobrazovaný název události | description |
| ---------- | ----------- |---|
| Microsoft. klíčů trezor. CertificateNewVersionCreated | Nově vytvořená verze certifikátu | Aktivuje se při vytvoření nového certifikátu nebo verze nového certifikátu. |
| Microsoft. klíčů trezor. CertificateNearExpiry | Brzo vyprší platnost certifikátu | Aktivuje se v případě vypršení platnosti aktuální verze certifikátu (výchozí hodnota je 30 dní před datem vypršení platnosti). |
| Microsoft. klíčů trezor. CertificateExpired | Platnost certifikátu vypršela | Aktivuje se, když vypršela platnost certifikátu. |
| Microsoft. klíčů trezor. KeyNewVersionCreated | Byla vytvořena klíčová nová verze. | Aktivuje se při vytvoření nového klíče nebo nové verze klíče. |
| Microsoft. klíčů trezor. KeyNearExpiry | Klíč s blížícím se koncem platnosti | Aktivuje se v případě vypršení platnosti aktuální verze klíče (výchozí hodnota je 30 dní před datem vypršení platnosti). |
| Platnost služby Microsoft. klíčů trezor. | Platnost klíče vypršela. | Aktivuje se po vypršení platnosti klíče. |
| Microsoft. klíčů trezor. SecretNewVersionCreated | Byla vytvořena tajná nová verze. | Aktivuje se, když se vytvoří nový tajný kód nebo nová tajná verze. |
| Microsoft. klíčů trezor. SecretNearExpiry | Tajný kód v blízkosti vypršení platnosti | Aktivuje se, když brzy vyprší platnost aktuální verze tajného kódu (výchozí hodnota je 30 dní před datem vypršení platnosti). |
| Microsoft. klíčů trezor. SecretExpired | Platnost tajného kódu vypršela | Aktivuje se po vypršení platnosti tajného klíče. |

## <a name="event-examples"></a>Příklady událostí

Následující příklad ukazuje schéma pro **Microsoft. SecretNewVersionCreated trezor**.

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

## <a name="event-properties"></a>Vlastnosti události

Událost má následující data nejvyšší úrovně:

| Vlastnost | Typ | Popis |
| ---------- | ----------- |---|
| id | string | ID objektu, který aktivoval tuto událost. |
| vaultName | string | Název trezoru klíčů objektu, který aktivoval tuto událost. |
| objectType | string | Typ objektu, který aktivoval tuto událost |
| ObjectName | string | Název objektu, který aktivoval tuto událost |
| version | string | Verze objektu, který aktivoval tuto událost |
| NBF | číslo | Není dřívější než datum v sekundách od 1970-01-01T00:00:00Z objektu, který aktivoval tuto událost. |
| Oček | číslo | Datum vypršení platnosti v sekundách od 1970-01-01T00:00:00Z objektu, který aktivoval tuto událost |


## <a name="next-steps"></a>Další kroky

* Úvod do Azure Event Grid najdete v tématu [co je Event Grid?](overview.md)
* Další informace o vytváření předplatného Azure Event Grid najdete v tématu [schéma předplatného Event Grid](subscription-creation-schema.md).
* Další informace o integraci Key Vault/Event Grid najdete v tématu [monitorování Key Vault s Azure Event Grid (Preview)](../key-vault/event-grid-overview.md) .
* Kurz týkající se integrace Key Vault/Event Grid najdete v tématu [How to: Route Key Vault Events to the Automation Runbook (Preview)](../key-vault/event-grid-tutorial.md).

- [Přehled Azure Key Vault](../key-vault/key-vault-overview.md)
- [Přehled Azure Event Grid](overview.md)
- [Monitorování Key Vault s využitím Azure Event Grid (Preview)](../key-vault/event-grid-overview.md)
- [Postupy: směrování událostí Key Vault do sady Automation Runbook (Preview)](../key-vault/event-grid-tutorial.md).
- [Přehled Azure Automation](../automation/index.yml)
