---
title: Aktualizace konfigurace trezoru Recovery Services pomocí REST API
description: V tomto článku se dozvíte, jak aktualizovat konfiguraci trezoru pomocí REST API.
ms.topic: conceptual
ms.date: 12/06/2019
ms.assetid: 9aafa5a0-1e57-4644-bf79-97124db27aa2
ms.openlocfilehash: 19a335d17ee0aa5ff9f989556656f5cf20d2b1a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91567821"
---
# <a name="update-azure-recovery-services-vault-configurations-using-rest-api"></a>Aktualizace konfigurace služby Azure Recovery Services trezoru pomocí REST API

Tento článek popisuje, jak aktualizovat konfigurace související s zálohováním ve službě Azure Recovery Services trezoru pomocí REST API.

## <a name="soft-delete-state"></a>Stav obnovitelného odstranění

Odstranění záloh chráněné položky je významnou operací, kterou je třeba monitorovat. Pro zajištění ochrany před náhodným odstraněním má Azure Recovery Services trezor možnost obnovitelného odstranění. Tato funkce umožňuje obnovit odstraněné zálohy, pokud je to nutné, do časového období po odstranění.

Ale v některých případech se tato možnost nevyžaduje. Trezor služby Azure Recovery Services nejde odstranit, pokud v něm existují zálohované položky, a to i obnovitelné odstranění. Může to představovat problém, pokud je potřeba trezor okamžitě odstranit. Například: operace nasazení často vyčistí vytvořené prostředky ve stejném pracovním postupu. Nasazení může vytvořit trezor, nakonfigurovat zálohování pro položku, provést test obnovení a pak pokračovat v odstraňování zálohovaných položek a trezoru. Pokud odstranění trezoru selže, může dojít k selhání celého nasazení. Vypnutí obnovitelného odstranění je jediným způsobem, jak zaručit okamžité odstranění.

Proto je třeba pečlivě určit, jestli se má v závislosti na scénáři zakázat obnovitelné odstranění pro určitý trezor. Další informace najdete v článku o [obnovitelném odstranění](backup-azure-security-feature-cloud.md).

### <a name="fetch-soft-delete-state-using-rest-api"></a>Načíst stav obnovitelného odstranění pomocí REST API

Ve výchozím nastavení bude stav obnovitelného odstranění povolen pro všechny nově vytvořené Recovery Services trezory. Pokud chcete načíst nebo aktualizovat stav obnovitelného odstranění trezoru, použijte [dokument REST API](/rest/api/backup/backupresourcevaultconfigs) související s konfigurací úložiště záloh.

Pokud chcete načíst aktuální stav obnovitelného odstranění trezoru, použijte následující operaci *Get* .

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-06-15
```

Identifikátor URI Get má `{subscriptionId}` `{vaultName}` parametr,, `{vaultresourceGroupName}` parametry. V tomto příkladu `{vaultName}` je "testVault" a `{vaultresourceGroupName}` je "testVaultRG". Všechny požadované parametry jsou uvedeny v identifikátoru URI, takže není nutné, aby bylo samostatné tělo požadavku.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-06-15
```

#### <a name="responses"></a>Odpovědi

Úspěšná odpověď pro operaci GET je uvedená níže:

|Název  |Typ  |Description  |
|---------|---------|---------|
|200 OK     |   [BackupResourceVaultConfig](/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Příklad odpovědi

Po odeslání žádosti o získání se vrátí odpověď 200 (úspěšné).

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

### <a name="update-soft-delete-state-using-rest-api"></a>Aktualizovat stav obnovitelného odstranění pomocí REST API

Pokud chcete aktualizovat stav obnovitelného odstranění trezoru Recovery Services pomocí REST API, použijte následující operaci *vložení* .

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-06-15
```

Identifikátor URI pro vložení má `{subscriptionId}` , `{vaultName}` , `{vaultresourceGroupName}` parametry. V tomto příkladu `{vaultName}` je "testVault" a `{vaultresourceGroupName}` je "testVaultRG". Pokud identifikátor URI nahradíte výše uvedenými hodnotami, identifikátor URI bude vypadat takto.

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-06-15
```

#### <a name="create-the-request-body"></a>Vytvoření textu žádosti

K vytvoření textu žádosti se použijí tyto společné definice.

Další podrobnosti najdete [v dokumentaci k REST API](/rest/api/backup/backupresourcevaultconfigs/update#request-body) .

|Name  |Požaduje se  |Typ  |Description  |
|---------|---------|---------|---------|
|značk     |         |   Řetězec      |  Volitelné eTag       |
|location     |  true       |Řetězec         |   Umístění prostředku      |
|properties     |         | [VaultProperties](/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Vlastnosti trezoru       |
|tags     |         | Objekt        |     Značky prostředků    |

#### <a name="example-request-body"></a>Příklad textu žádosti

Následující příklad slouží k aktualizaci stavu obnovitelného odstranění na Disabled.

```json
{
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

#### <a name="responses-for-the-patch-operation"></a>Odpovědi na operaci opravy

Úspěšná odpověď pro operaci PATCH je uvedená níže:

|Název  |Typ  |Description  |
|---------|---------|---------|
|200 OK     |   [BackupResourceVaultConfig](/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response-for-the-patch-operation"></a>Příklad odpovědi na operaci opravy

Po odeslání žádosti o opravu se vrátí odpověď 200 (úspěšné).

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

Další informace o rozhraních REST API Azure najdete v následujících dokumentech:

- [Poskytovatel Azure Recovery Services REST API](/rest/api/recoveryservices/)
- [Začínáme s Azure REST API](/rest/api/azure/)
