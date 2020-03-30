---
title: Aktualizovat konfiguraci úložiště služby Recovery Services pomocí rozhraní REST API
description: V tomto článku se dozvíte, jak aktualizovat konfiguraci úložiště pomocí rozhraní REST API.
ms.topic: conceptual
ms.date: 12/06/2019
ms.assetid: 9aafa5a0-1e57-4644-bf79-97124db27aa2
ms.openlocfilehash: 6cecbb18e0cd6f548e1688ef978f10dcee7d9fbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252360"
---
# <a name="update-azure-recovery-services-vault-configurations-using-rest-api"></a>Aktualizace konfigurací trezoru služby Azure Recovery Services pomocí rozhraní REST API

Tento článek popisuje, jak aktualizovat konfigurace související se zálohováním v trezoru služby Azure Recovery Services pomocí rozhraní REST API.

## <a name="soft-delete-state"></a>Stav obnovitelného odstranění

Odstranění záloh chráněné položky je významná operace, která musí být sledována. Chcete-li chránit před náhodným odstraněním, trezor služby Azure Recovery Services má funkci obnovitelného odstranění. Tato funkce umožňuje zákazníkům obnovit odstraněné zálohy, v případě potřeby v časovém období po odstranění.

Existují však scénáře, ve kterých tato schopnost není vyžadována. Trezor služby Azure Recovery Services nelze odstranit, pokud jsou v něm položky zálohování, dokonce i ty obnovitelné odstraněné. To může představovat problém, pokud je třeba úložiště okamžitě odstranit. Například: operace nasazení často čistí vytvořené prostředky ve stejném pracovním postupu. Nasazení může vytvořit trezor, nakonfigurovat zálohy pro položku, provést testovací obnovení a potom pokračovat k odstranění položek záloh a trezoru. Pokud se odstranění trezoru nezdaří, může se nezdaří celé nasazení. Zakázání obnovitelného odstranění je jediný způsob, jak zaručit okamžité odstranění.

Proto musí zákazník pečlivě zvolit, zda chcete zakázat obnovitelné odstranění pro konkrétní trezor v závislosti na scénáři. Další informace naleznete v [článku obnovitelného odstranění](backup-azure-security-feature-cloud.md#soft-delete).

### <a name="fetch-soft-delete-state-using-rest-api"></a>Načtení stavu obnovitelného odstranění pomocí rozhraní REST API

Ve výchozím nastavení bude pro všechny nově vytvořené trezory služby Recovery Services povolen stav obnovitelného odstranění. Chcete-li načíst nebo aktualizovat stav obnovitelného odstranění úložiště, použijte [dokument rozhraní REST API](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs) související s konfigurací úložiště záloh

Chcete-li načíst aktuální stav obnovitelného odstranění pro trezor, použijte následující operaci *GET*

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

Identifikátor URI `{subscriptionId}`GET `{vaultName}` `{vaultresourceGroupName}` má parametry , . V tomto `{vaultName}` příkladu je "testVault" a `{vaultresourceGroupName}` je "testVaultRG". Vzhledem k tomu, že všechny požadované parametry jsou uvedeny v identifikátoru URI, není potřeba samostatnétělo požadavku.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="responses"></a>Odezvy

Úspěšná odpověď na operaci GET je uvedena níže:

|Name (Název)  |Typ  |Popis  |
|---------|---------|---------|
|200 OK     |   [Nástroj BackupResourceVaultConfig](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Příklad odpovědi

Po odeslání požadavku GET je vrácena odpověď 200 (úspěšné).

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Enabled"
  }
}
```

### <a name="update-soft-delete-state-using-rest-api"></a>Aktualizace stavu obnovitelného odstranění pomocí rozhraní REST API

Chcete-li aktualizovat stav obnovitelného odstranění trezoru služeb obnovení pomocí rozhraní REST API, použijte následující operaci *PATCH*

```http
PATCH https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

Identifikátor URI `{subscriptionId}`patch `{vaultName}` `{vaultresourceGroupName}` má parametry , . V tomto `{vaultName}` příkladu je "testVault" a `{vaultresourceGroupName}` je "testVaultRG". Pokud nahradíme identifikátor URI výše uvedenými hodnotami, bude identifikátor URI vypadat takto.

```http
PATCH https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>Vytvoření těla požadavku

Následující běžné definice se používají k vytvoření těla požadavku

Další podrobnosti naleznete [v dokumentaci k rozhraní REST API](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/update#request-body)

|Name (Název)  |Požaduje se  |Typ  |Popis  |
|---------|---------|---------|---------|
|Etag     |         |   Řetězec      |  Volitelná eTag       |
|location     |  true       |Řetězec         |   Umístění prostředku      |
|properties     |         | [Vlastnosti úložiště](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Vlastnosti trezoru       |
|tags     |         | Objekt        |     Značky prostředků    |

#### <a name="example-request-body"></a>Ukázkové tělo požadavku

Následující příklad slouží k aktualizaci stavu obnovitelného odstranění na "zakázáno".

```json
{
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

#### <a name="responses"></a>Odezvy

Úspěšná odpověď na operaci "PATCH" je uvedena níže:

|Name (Název)  |Typ  |Popis  |
|---------|---------|---------|
|200 OK     |   [Nástroj BackupResourceVaultConfig](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Příklad odpovědi

Po odeslání požadavku PATCH je vrácena odpověď 200 (úspěšné).

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

## <a name="next-steps"></a>Další kroky

[Vytvořte zásady zálohování pro zálohování virtuálního počítače Azure v tomto trezoru](backup-azure-arm-userestapi-createorupdatepolicy.md).

Další informace o azure rest api, najdete v následujících dokumentech:

- [Rozhraní REST ROZHRANÍ REST ZPROSTŘEDKOVATELE Služby Azure Recovery Services](/rest/api/recoveryservices/)
- [Začínáme s Azure REST API](/rest/api/azure/)
