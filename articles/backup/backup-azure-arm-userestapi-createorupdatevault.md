---
title: 'Azure Backup: Trezory služby Recovery Services pomocí rozhraní REST API'
description: Správa zálohování a obnovení z Azure zálohování virtuálního počítače pomocí rozhraní REST API
services: backup
author: pvrk
manager: shivamg
keywords: ROZHRANÍ REST API; Zálohování virtuálních počítačů Azure; Obnovení virtuálního počítače Azure;
ms.service: backup
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: pullabhk
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: 4f18b10ee3f4148badc8e53a9660c9f5c998aef7
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55734330"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>Vytvoření trezoru služeb zotavení Azure pomocí rozhraní REST API

Postup pro vytvoření trezoru služeb zotavení Azure pomocí rozhraní REST API jsou popsány v [vytvořit trezor rozhraní REST API](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate) dokumentaci. Dejte nám tento dokument použijte jako referenci pro vytvoření trezoru nazývá "testVault" v "Západní USA".

Chcete-li vytvořit nebo aktualizovat trezor služby Azure Recovery Services, použijte následující *UMÍSTIT* operace.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>Vytvořit žádost

Chcete-li vytvořit *UMÍSTIT* požadavek, `{subscription-id}` parametr je povinný. Pokud máte více předplatných, přečtěte si téma [práce s několika předplatnými](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). Můžete definovat `{resourceGroupName}` a `{vaultName}` pro vaše prostředky, spolu s `api-version` parametru. Tento článek používá `api-version=2016-06-01`.

Vyžadují se následující hlavičky:

| Hlavička požadavku   | Popis |
|------------------|-----------------|
| *Typ obsahu:*  | Povinná hodnota. Nastavte na `application/json`. |
| *Autorizace:* | Povinná hodnota. Nastaven na platné `Bearer` [přístupový token](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

Další informace o tom, jak vytvořit žádost, naleznete v tématu [součástí žádost/odpověď rozhraní REST API](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Vytvořit datovou část požadavku

Následující běžné definice slouží k sestavení hlavní část žádosti:

|Název  |Požaduje se  |Typ  |Popis  |
|---------|---------|---------|---------|
|značka eTag     |         |   String      |  Volitelná značka eTag       |
|location     |  true (pravda)       |String         |   Umístění prostředku      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Vlastnosti trezoru       |
|SKU     |         |  [Sku](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#sku)       |    Určuje jedinečný identifikátor systému každého prostředku Azure     |
|tags     |         | Objekt        |     Značky prostředků    |

Všimněte si, že název trezoru a název skupiny prostředků jsou k dispozici v identifikátoru URI PUT. Tělo požadavku určuje umístění.

## <a name="example-request-body"></a>Text požadavku na příkladu

Následující příklad těla slouží k vytvoření trezoru v "Západní USA". Zadejte umístění. SKU je vždy "Standard".

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

Existují dva úspěšné odpovědi pro operaci vytvoření nebo aktualizace trezoru služby Recovery Services:

|Název  |Typ  |Popis  |
|---------|---------|---------|
|200 OK     |   [Trezor](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)      | OK        |
|201 – vytvořeno     | [Trezor](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)        |   Vytvořeno      |

Další informace o rozhraní REST API odpovědi najdete v tématu [zpracování zprávy s odpovědí](/rest/api/azure/#process-the-response-message).

### <a name="example-response"></a>Příklad odpovědi

Zhuštěné *201 – vytvořeno* ukazuje těla odpovědi z předchozí příklad žádosti *id* byla přiřazena a *provisioningState* je *proběhlo úspěšně.* :

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

## <a name="next-steps"></a>Další postup

[Vytvořte zásadu zálohování k zálohování virtuálního počítače Azure v tomto trezoru](backup-azure-arm-userestapi-createorupdatepolicy.md).

Další informace o rozhraní REST API služby Azure najdete v následujících dokumentech:

- [Rozhraní REST API poskytovatele Azure Recovery Services](/rest/api/recoveryservices/)
- [Začínáme s Azure REST API](/rest/api/azure/)
