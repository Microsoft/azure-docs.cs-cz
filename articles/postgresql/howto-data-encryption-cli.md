---
title: Šifrování dat – Azure CLI – pro Azure Database for PostgreSQL – jeden server
description: Naučte se, jak nastavit a spravovat šifrování dat pro váš Azure Database for PostgreSQL samostatný server pomocí Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 03/30/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 757782e8842fbcaca9c8d95ec8086dd5791a817b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93240609"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-cli"></a>Šifrování dat pro Azure Database for PostgreSQL jeden server pomocí Azure CLI

Naučte se používat Azure CLI k nastavení a správě šifrování dat pro váš Azure Database for PostgreSQL jeden server.

## <a name="prerequisites-for-azure-cli"></a>Předpoklady pro Azure CLI

* Musíte mít předplatné Azure a mít oprávnění správce k tomuto předplatnému.
* Vytvořte Trezor klíčů a klíč, který se použije pro klíč spravovaný zákazníkem. V trezoru klíčů taky povolte možnost Vymazat ochranu a obnovitelné odstranění.

   ```azurecli-interactive
   az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true --enable-purge-protection true
   ```

* Ve vytvořeném Azure Key Vault vytvořte klíč, který se použije pro šifrování dat Azure Database for PostgreSQL jednoho serveru.

   ```azurecli-interactive
   az keyvault key create --name <key_name> -p software --vault-name <vault_name>
   ```

* Aby bylo možné použít existující Trezor klíčů, musí mít následující vlastnosti, které se mají použít jako klíč spravovaný zákazníkem:
  * [Obnovitelné odstranění](../key-vault/general/soft-delete-overview.md)

      ```azurecli-interactive
      az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
      ```

  * [Vyprázdnit chráněné](../key-vault/general/soft-delete-overview.md#purge-protection)

      ```azurecli-interactive
      az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
      ```

* Aby bylo možné klíč použít jako klíč spravovaný zákazníkem, musí mít následující atributy:
  * Nesmí mít žádné datum ukončení platnosti.
  * Nesmí být zakázaný.
  * Provádění operací **Get**, **Wrap** a **Unwrap**

## <a name="set-the-right-permissions-for-key-operations"></a>Nastavení správných oprávnění pro klíčové operace

1. Existují dva způsoby, jak získat spravovanou identitu pro váš Azure Database for PostgreSQL jeden server.

    ### <a name="create-an-new-azure-database-for-postgresql-server-with-a-managed-identity"></a>Vytvořte nový server Azure Database for PostgreSQL se spravovanou identitou.

    ```azurecli-interactive
    az postgres server create --name <server_name> -g <resource_group> --location <location> --storage-size <size>  -u <user> -p <pwd> --backup-retention <7> --sku-name <sku name> --geo-redundant-backup <Enabled/Disabled> --assign-identity
    ```

    ### <a name="update-an-existing-the-azure-database-for-postgresql-server-to-get-a-managed-identity"></a>Aktualizujte existující server Azure Database for PostgreSQL, aby získal spravovanou identitu.

    ```azurecli-interactive
    az postgres server update --resource-group <resource_group> --name <server_name> --assign-identity
    ```

2. Nastavte **klíčová oprávnění** (**získání**, **zabalení**, **rozbalení**) pro **objekt zabezpečení**, což je název serveru PostgreSQL Single server.

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>Nastavení šifrování dat pro Azure Database for PostgreSQL jeden server

1. Povolte šifrování dat pro Azure Database for PostgreSQL jeden server pomocí klíče vytvořeného v Azure Key Vault.

    ```azurecli-interactive
    az postgres server key create --name <server_name> -g <resource_group> --kid <key_url>
    ```

    Adresa URL klíče:  `https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Použití šifrování dat pro obnovení nebo servery repliky

Po zašifrování Azure Database for PostgreSQL jednoho serveru pomocí spravovaného klíče zákazníka uloženého v Key Vault jsou všechny nově vytvořené kopie serveru taky zašifrované. Tuto novou kopii můžete vytvořit buď prostřednictvím operace místního nebo geografického obnovení, nebo prostřednictvím operace repliky (místní/napříč oblastí). Takže pro zašifrovaný Server s jedním serverem PostgreSQL můžete použít následující postup k vytvoření šifrovaného obnoveného serveru.

### <a name="creating-a-restoredreplica-server"></a>Vytvoření obnoveného serveru nebo serveru repliky

* [Vytvoření serveru pro obnovení](howto-restore-server-cli.md)
* [Vytvoření serveru repliky pro čtení](howto-read-replicas-cli.md)

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>Po obnovení serveru se znovu ověří šifrování dat obnoveného serveru.

*   Přiřazení identity serveru repliky
```azurecli-interactive
az postgres server update --name  <server name>  -g <resoure_group> --assign-identity
```

*   Získat existující klíč, který se má použít pro obnovený server nebo server repliky

```azurecli-interactive
az postgres server key list --name  '<server_name>'  -g '<resource_group_name>'
```

*   Nastavte zásady pro novou identitu pro obnovený server nebo server repliky.

```azurecli-interactive
az keyvault set-policy --name <keyvault> -g <resoure_group> --key-permissions get unwrapKey wrapKey --object-id <principl id of the server returned by the step 1>
```

* Znovu ověřit obnovený server nebo server repliky pomocí šifrovacího klíče

```azurecli-interactive
az postgres server key create –name  <server name> -g <resource_group> --kid <key url>
```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-postgresql-single-server"></a>Další funkce pro klíč, který se používá pro Azure Database for PostgreSQL jeden server

### <a name="get-the-key-used"></a>Získat použitý klíč

```azurecli-interactive
az postgres server key show --name <server name>  -g <resource_group> --kid <key url>
```

Adresa URL klíče: `https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

### <a name="list-the-key-used"></a>Vypíše použitý klíč.

```azurecli-interactive
az postgres server key list --name  <server name>  -g <resource_group>
```

### <a name="drop-the-key-being-used"></a>Vyřadit použitý klíč

```azurecli-interactive
az postgres server key delete -g <resource_group> --kid <key url> 
```

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Použití šablony Azure Resource Manager k povolení šifrování dat

Kromě Azure Portal můžete taky povolit šifrování dat na Azure Database for PostgreSQL jednom serveru pomocí šablon Azure Resource Manager pro nový i existující server.

### <a name="for-a-new-server"></a>Pro nový server

Použijte jednu z předem vytvořených šablon Azure Resource Manager k zřízení serveru s povoleným šifrováním dat: [příklad s šifrováním dat](https://github.com/Azure/azure-postgresql/tree/master/arm-templates/ExampleWithDataEncryption)

Tato šablona Azure Resource Manager vytvoří Azure Database for PostgreSQL jeden server a k povolení šifrování dat na serveru používá **trezor** **klíčů a klíč** předaný jako parametry.

### <a name="for-an-existing-server"></a>Pro existující server
Kromě toho můžete pomocí Azure Resource Manager šablon povolit šifrování dat na stávajících Azure Database for PostgreSQLch jednotlivých serverech.

* Předejte ID prostředku Azure Key Vaultho klíče, který jste zkopírovali dříve pod `Uri` vlastností v objektu Properties (vlastnosti).

* Jako verzi rozhraní API použijte *2020-01-01-Preview* .

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "serverName": {
      "type": "string"
    },
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Key vault name where the key to use is stored"
      }
    },
    "keyVaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "Key vault resource group name where it is stored"
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "Key name in the key vault to use as encryption protector"
      }
    },
    "keyVersion": {
      "type": "string",
      "metadata": {
        "description": "Version of the key in the key vault to use as encryption protector"
      }
    }
  },
  "variables": {
    "serverKeyName": "[concat(parameters('keyVaultName'), '_', parameters('keyName'), '_', parameters('keyVersion'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforPostgreSQL/servers",
      "apiVersion": "2017-12-01",
      "kind": "",
      "location": "[parameters('location')]",
      "identity": {
        "type": "SystemAssigned"
      },
      "name": "[parameters('serverName')]",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-05-01",
      "name": "addAccessPolicy",
      "resourceGroup": "[parameters('keyVaultResourceGroupName')]",
      "dependsOn": [
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.KeyVault/vaults/accessPolicies",
              "name": "[concat(parameters('keyVaultName'), '/add')]",
              "apiVersion": "2018-02-14-preview",
              "properties": {
                "accessPolicies": [
                  {
                    "tenantId": "[subscription().tenantId]",
                    "objectId": "[reference(resourceId('Microsoft.DBforPostgreSQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
                    "permissions": {
                      "keys": [
                        "get",
                        "wrapKey",
                        "unwrapKey"
                      ]
                    }
                  }
                ]
              }
            }
          ]
        }
      }
    },
    {
      "name": "[concat(parameters('serverName'), '/', variables('serverKeyName'))]",
      "type": "Microsoft.DBforPostgreSQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "serverKeyType": "AzureKeyVault",
        "uri": "[concat(reference(resourceId(parameters('keyVaultResourceGroupName'), 'Microsoft.KeyVault/vaults/', parameters('keyVaultName')), '2018-02-14-preview', 'Full').properties.vaultUri, 'keys/', parameters('keyName'), '/', parameters('keyVersion'))]"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Další kroky

 Další informace o šifrování dat najdete v tématu [Azure Database for PostgreSQL šifrování dat s jedním serverem pomocí klíče spravovaného zákazníkem](concepts-data-encryption-postgresql.md).
