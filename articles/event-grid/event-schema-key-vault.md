---
title: Schéma událostí Azure Event Grid pro Azure Key Vault
description: Popisuje vlastnosti a schémata k dispozici pro události Azure Key Vault s Azure Event Grid
services: event-grid
author: msmbaldwin
ms.service: event-grid
ms.topic: reference
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 17404388b2b6c3fee1c6ab666f7233a66817f642
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082871"
---
# <a name="azure-event-grid-event-schema-for-azure-key-vault-preview"></a>Schéma událostí Azure Event Grid pro Azure Key Vault (preview)

Tento článek obsahuje vlastnosti a schéma pro události v [azure key vault](../key-vault/index.yml), aktuálně ve verzi preview. Úvod do schémat událostí najdete v [tématu schéma událostí služby Azure Event Grid](event-schema.md).

## <a name="available-event-types"></a>Dostupné typy událostí

Účet Azure Key Vault generuje následující typy událostí:

| Celé jméno události | Zobrazovaný název události | Popis |
| ---------- | ----------- |---|
| Microsoft.KeyVault.CertificateNewVersionVytvořeno | Byla vytvořena nová verze certifikátu | Aktivuje se při vytvoření nového certifikátu nebo nové verze certifikátu. |
| Nearexpiry rozhraní Microsoft.KeyVault.Certificate | Certifikát blížící se vypršení platnosti | Aktivuje se, když vyprší platnost aktuální verze certifikátu. (Výchozí hodnota je 30 dní před datem vypršení platnosti.) |
| Platnost programu Microsoft.KeyVault.Certificate vypršela | Platnost certifikátu vypršela | Aktivuje se po vypršení platnosti certifikátu. |
| Microsoft.KeyVault.KeyNewVersionVytvořeno | Vytvořena nová verze klíče | Aktivuje se při vytvoření nového klíče nebo nové verze klíče. |
| Microsoft.KeyVault.KeyNearExpiry | Klíč v blízkosti vypršení platnosti | Aktivuje se, když vyprší platnost aktuální verze klíče. (Výchozí hodnota je 30 dní před datem vypršení platnosti.) |
| Platnost souboru Microsoft.KeyVault.KeyVypršela | Platnost klíče vypršela. | Aktivuje se při vypršení platnosti klíče. |
| Microsoft.KeyVault.SecretNewVersionVytvořeno | Byla vytvořena tajná nová verze | Aktivuje se při vytvoření nového tajného nebo nového tajného klíče. |
| Microsoft.KeyVault.SecretNearExpiry | Tajné téměř vypršení platnosti | Aktivuje se, když vyprší platnost aktuální verze tajného klíče. (Výchozí hodnota je 30 dní před datem vypršení platnosti.) |
| Platnost souboru Microsoft.KeyVault.SecretVypršela | Platnost tajného klíče vypršela | Aktivuje se při vypršení platnosti tajného klíče. |

## <a name="event-examples"></a>Příklady událostí

Následující příklad ukazuje schéma pro **Microsoft.KeyVault.SecretNewVersionCreated**:

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
| id | řetězec | ID objektu, který tuto událost spustil |
| název trezoru | řetězec | Název trezoru klíčů objektu, který tuto událost spustil |
| Objecttype | řetězec | Typ objektu, který tuto událost spustil |
| Objectname | řetězec | Název objektu, který tuto událost spustil |
| version | řetězec | Verze objektu, který tuto událost spustil |
| Nbf | číslo | Datum nepředspotřeby v sekundách od 1970-01-01T00:00:00Z objektu, který tuto událost spustil |
| exp | číslo | Datum vypršení platnosti v sekundách od 1970-01-01T00:00:00Z objektu, který spustil tuto událost |


## <a name="next-steps"></a>Další kroky

* Úvod do Služby Azure Event Grid najdete v tématu [Co je event grid?](overview.md).
* Další informace o tom, jak vytvořit předplatné Služby Event Grid, najdete v [tématu schéma předplatného služby Event Grid](subscription-creation-schema.md).
* Další informace o integraci trezoru klíčů s mřížkou událostí najdete v [tématu Sledování trezoru klíčů pomocí Azure Event Grid (preview).](../key-vault/event-grid-overview.md)
* Kurz o integraci trezoru klíčů s událostí gridu najdete v tématu [Příjem a reagovat na oznámení trezoru klíčů s Azure Event Grid (preview)](../key-vault/event-grid-tutorial.md).
* Další pokyny pro trezor klíčů a azure automation najdete v těchto tématech:
    - [Co je Azure Key Vault?](../key-vault/key-vault-overview.md)
    - [Monitorování trezoru klíčů pomocí Azure Event Grid (preview)](../key-vault/event-grid-overview.md)
    - [Příjem a reakce na oznámení trezoru klíčů pomocí Azure Event Grid (preview)](../key-vault/event-grid-tutorial.md)
    - [Azure Automation – přehled](../automation/index.yml)
