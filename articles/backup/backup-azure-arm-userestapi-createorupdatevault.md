---
title: Vytvoření trezorů služby Recovery Services pomocí rozhraní REST API
description: V tomto článku se dozvíte, jak spravovat operace zálohování a obnovení zálohování virtuálních počítačích Azure pomocí rozhraní REST API.
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: 1901c35d2b4d8bcd02cc064fcfc844e19969e3b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74173420"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>Vytvoření trezoru služby Azure Recovery Services pomocí rozhraní REST API

Kroky k vytvoření trezoru služby Azure Recovery Services pomocí rozhraní REST API jsou popsané v dokumentaci [k rozhraní REST API.](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate) Použijte tento dokument jako odkaz k vytvoření trezoru s názvem "testVault" v "Západní USA".

Chcete-li vytvořit nebo aktualizovat trezor služby Azure Recovery Services, použijte následující operaci *PUT.*

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>Vytvoření požadavku

Chcete-li vytvořit požadavek *PUT,* je vyžadován `{subscription-id}` parametr. Pokud máte více předplatných, přečtěte si informace [o práci s více předplatnými](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). Definujete `{resourceGroupName}` a `{vaultName}` a pro vaše `api-version` prostředky, spolu s parametrem. Tento článek `api-version=2016-06-01`používá .

Jsou vyžadovány následující hlavičky:

| Hlavička požadavku   | Popis |
|------------------|-----------------|
| *Content-Type:*  | Povinná hodnota. Nastavte na `application/json`. |
| *Authorization:* | Povinná hodnota. Nastavte na platný  [přístupový token](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients)`Bearer`. |

Další informace o vytvoření požadavku naleznete v [tématu Součásti požadavku/odpovědi rozhraní REST API](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Vytvoření těla požadavku

K vytvoření těla požadavku se používají následující běžné definice:

|Name (Název)  |Požaduje se  |Typ  |Popis  |
|---------|---------|---------|---------|
|Etag     |         |   Řetězec      |  Volitelná eTag       |
|location     |  true       |Řetězec         |   Umístění prostředku      |
|properties     |         | [Vlastnosti úložiště](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Vlastnosti trezoru       |
|Sku     |         |  [Sku](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#sku)       |    Identifikuje jedinečný identifikátor systému pro každý prostředek Azure.     |
|tags     |         | Objekt        |     Značky prostředků    |

Všimněte si, že název úložiště a název skupiny prostředků jsou uvedeny v identifikátoru URI. Tělo požadavku definuje umístění.

## <a name="example-request-body"></a>Ukázkové tělo požadavku

Následující příklad těla se používá k vytvoření trezoru v "Západní USA". Zadejte umístění. Skladová položka je vždy "Standardní".

```json
{
  "properties": {},
  "sku": {
    "name": "Standard"
  },
  "location": "West US"
}
```

## <a name="responses"></a>Odezvy

Operace má dvě úspěšné odpovědi k vytvoření nebo aktualizaci trezoru služby Recovery Services:

|Name (Název)  |Typ  |Popis  |
|---------|---------|---------|
|200 OK     |   [Trezoru](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)      | OK        |
|201 Vytvořeno     | [Trezoru](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)        |   Vytvořeno      |

Další informace o odpovědích rozhraní REST API naleznete [v tématu Zpracování zprávy o odpovědi](/rest/api/azure/#process-the-response-message).

### <a name="example-response"></a>Příklad odpovědi

Zkrácená *201 Vytvořená* odpověď z předchozího těla požadavku příkladu ukazuje, že bylo přiřazeno *id* a *zřizováníState* je *succeeded*:

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

Další informace o azure rest api, najdete v následujících dokumentech:

- [Rozhraní REST ROZHRANÍ REST ZPROSTŘEDKOVATELE Služby Azure Recovery Services](/rest/api/recoveryservices/)
- [Začínáme s Azure REST API](/rest/api/azure/)
