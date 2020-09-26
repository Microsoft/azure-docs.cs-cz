---
title: Vytvoření trezorů Recovery Services pomocí REST API
description: V tomto článku se dozvíte, jak spravovat operace zálohování a obnovení zálohování virtuálních počítačů Azure pomocí REST API.
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: a37808548ec58977b7d6af16c75b94b7b5efe446
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91271592"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>Vytvoření trezoru služby Azure Recovery Services pomocí REST API

Postup vytvoření trezoru služby Azure Recovery Services pomocí REST API je popsaný v dokumentaci k [Vytvoření trezoru REST API](/rest/api/recoveryservices/vaults/createorupdate) . Pojďme tento dokument použít jako referenci k vytvoření trezoru s názvem "testVault" v "Západní USA".

Pokud chcete vytvořit nebo aktualizovat trezor služby Azure Recovery Services, použijte následující operaci *vložení* .

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>Vytvoření žádosti

Chcete-li vytvořit požadavek *Put* , `{subscription-id}` je vyžadován parametr. Pokud máte více předplatných, přečtěte si téma [práce s více předplatnými](/cli/azure/manage-azure-subscriptions-azure-cli). Nadefinujete `{resourceGroupName}` a `{vaultName}` pro své prostředky spolu s `api-version` parametrem. Tento článek používá `api-version=2016-06-01` .

Jsou vyžadovány následující hlavičky:

| Hlavička požadavku   | Popis |
|------------------|-----------------|
| *Content-Type:*  | Povinná hodnota. Nastavte na `application/json`. |
| *Authorization:* | Povinná hodnota. Nastavte na platný [přístupový token](/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer`. |

Další informace o tom, jak vytvořit žádost, najdete v tématu [komponenty REST API žádosti o odezvu](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Vytvoření textu žádosti

Následující běžné definice se používají k sestavení textu žádosti:

|Name  |Požaduje se  |Typ  |Popis  |
|---------|---------|---------|---------|
|značk     |         |   Řetězec      |  Volitelné eTag       |
|location     |  true       |Řetězec         |   Umístění prostředku      |
|properties     |         | [VaultProperties](/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Vlastnosti trezoru       |
|skladové     |         |  [Skladové](/rest/api/recoveryservices/vaults/createorupdate#sku)       |    Určuje jedinečný systémový identifikátor každého prostředku Azure.     |
|tags     |         | Objekt        |     Značky prostředků    |

Všimněte si, že název trezoru a název skupiny prostředků jsou k dispozici v identifikátoru URI PUT. Text žádosti definuje umístění.

## <a name="example-request-body"></a>Příklad textu žádosti

Následující vzorový text se používá k vytvoření trezoru v "Západní USA". Zadejte umístění. SKU je vždycky "Standard".

```json
{
  "properties": {},
  "sku": {
    "name": "Standard"
  },
  "location": "West US"
}
```

## <a name="responses"></a>Odpovědi

Existují dvě úspěšné odpovědi, které by mohla operace vytvořit nebo aktualizovat Recovery Services trezoru:

|Název  |Typ  |Popis  |
|---------|---------|---------|
|200 OK     |   [Trezor](/rest/api/recoveryservices/vaults/createorupdate#vault)      | OK        |
|201 vytvořeno     | [Trezor](/rest/api/recoveryservices/vaults/createorupdate#vault)        |   Vytvořeno      |

Další informace o odpovědích REST API najdete v tématu [zpracování zprávy s odpovědí](/rest/api/azure/#process-the-response-message).

### <a name="example-response"></a>Příklad odpovědi

Zhuštěná *201 vytvořená* odpověď z předchozího ukázkového textu žádosti ukazuje, že *ID* bylo přiřazeno a *provisioningState* bylo *úspěšné*:

```json
{
  "location": "westus",
  "name": "testVault",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "id": "/subscriptions/77777777-b0c6-47a2-b37c-d8e65a629c18/resourceGroups/Default-RecoveryServices-ResourceGroup/providers/Microsoft.RecoveryServices/vaults/testVault",
  "type": "Microsoft.RecoveryServices/vaults",
  "sku": {
    "name": "Standard"
  }
}
```

## <a name="next-steps"></a>Další kroky

[Vytvořte zásady zálohování pro zálohování virtuálního počítače Azure v tomto trezoru](backup-azure-arm-userestapi-createorupdatepolicy.md).

Další informace o rozhraních REST API Azure najdete v následujících dokumentech:

- [Poskytovatel Azure Recovery Services REST API](/rest/api/recoveryservices/)
- [Začínáme s Azure REST API](/rest/api/azure/)
