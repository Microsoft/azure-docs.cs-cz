---
title: Šifrování dat – Azure CLI – pro Azure Database for PostgreSQL – jeden server
description: Zjistěte, jak nastavit a spravovat šifrování dat pro váš server Azure Database for PostgreSQL Single pomocí azure cli.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: c778130a95be1aa0afe81241130b7daf165360c4
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522131"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-cli"></a>Šifrování dat pro Azure Database for PostgreSQL Single server pomocí azure cli

Zjistěte, jak pomocí azure cli nastavit a spravovat šifrování dat pro váš azure database pro postgresql jeden server.

## <a name="prerequisites-for-azure-cli"></a>Požadavky pro azure cli

* Musíte mít předplatné Azure a být správcem tohoto předplatného.
* Vytvořte trezor klíčů a klíč, který chcete použít pro klíč spravovaný zákazníkem. Povolte také ochranu proti vymazání a měkké odstranění v trezoru klíčů.

    ```azurecli-interactive
    az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true --enable-purge-protection true
    ```

* Ve vytvořeném trezoru klíčů Azure vytvořte klíč, který se použije pro šifrování dat databáze Azure pro server PostgreSQL Single.

    ```azurecli-interactive
    az keyvault key create --name <key_name> -p software --vault-name <vault_name>
    ```

* Chcete-li použít existující trezor klíčů, musí mít následující vlastnosti, které chcete použít jako klíč spravovaný zákazníkem:
  * [Obnovitelné odstranění](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Vyčištění chráněno](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* Klíč musí mít následující atributy, které chcete použít jako klíč spravovaný zákazníkem:
  * Bez data vypršení platnosti
  * Není zakázáno
  * Provádět operace **get**, **wrap** a **unwrap**

## <a name="set-the-right-permissions-for-key-operations"></a>Nastavení správných oprávnění pro klíčové operace

1. Existují dva způsoby, jak získat spravovanou identitu pro váš server Azure Database for PostgreSQL Single.

    ### <a name="create-an-new-azure-database-for-mysql-server-with-a-managed-identity"></a>Vytvořte novou Azure Database for MySQL server se spravovanou identitou.

    ```azurecli-interactive
    az postgres server create --name -g <resource_group> --location <locations> --storage-size <size>  -u <user>-p <pwd> --backup-retention <7> --sku-name <sku name> --geo-redundant-backup <Enabled/Disabled>  --assign-identity
    ```

    ### <a name="update-an-existing-the-azure-database-for-mysql-server-to-get-a-managed-identity"></a>Aktualizujte existující databázi Azure pro server MySQL, abyste získali spravovanou identitu.

    ```azurecli-interactive
    az postgres server update –name <server name>  -g <resoure_group> --assign-identity
    ```

2. Nastavte **oprávnění klíče** (**Get**, **Wrap**, **Unwrap**) pro **hlavní**, což je název serveru PostgreSQL Single.

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>Nastavení šifrování dat pro azure databázi pro server PostgreSQL Single

1. Povolte šifrování dat pro server Azure Database for PostgreSQL Single pomocí klíče vytvořeného v trezoru klíčů Azure.

    ```azurecli-interactive
    az postgres server key create –name  <server name>  -g <resource_group> --kid <key url>
    ```

    Klíčová adresa url:https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Použití šifrování dat pro servery pro obnovení nebo repliky

Po Azure Database for PostgreSQL Single server je šifrována s klientem spravovaný klíč uložený v trezoru klíčů, všechny nově vytvořené kopie serveru je také šifrována. Tuto novou kopii můžete vytvořit buď prostřednictvím místní operace nebo operace geografického obnovení, nebo prostřednictvím operace repliky (místní/mezi oblastmi). Takže pro šifrovaný server PostgreSQL Single můžete použít následující kroky k vytvoření šifrovaného obnoveného serveru.

### <a name="creating-a-restoredreplica-server"></a>Vytvoření obnoveného/replikového serveru

  *  [Vytvoření serveru pro obnovení](howto-restore-server-cli.md) 
  *  [Vytvoření serveru replik y pro čtení](howto-read-replicas-cli.md) 

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>Po obnovení serveru znovu ověřte šifrování dat obnovený server

    ```azurecli-interactive
    az postgres server key create –name  <server name> -g <resource_group> --kid <key url>
    ```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-postgresql-single-server"></a>Další funkce pro klíč používaný pro azure databáze pro postgreSQL jediný server

### <a name="get-the-key-used"></a>Získání klíče

    ```azurecli-interactive
    az mysql server key show --name  <server name>  -g <resource_group> --kid <key url>
    ```

    Key url:  https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>

### <a name="list-the-key-used"></a>Seznam použitého klíče

    ```azurecli-interactive
    az postgres server key list --name  <server name>  -g <resource_group>
    ```

### <a name="drop-the-key-being-used"></a>Zapustit používaný klíč

    ```azurecli-interactive
    az postgres server key delete -g <resource_group> --kid <key url> 
    ```
## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Povolení šifrování dat pomocí šablony Azure Resource Manageru

Kromě portálu Azure můžete taky povolit šifrování dat na jednotném serveru Azure Database for PostgreSQL pomocí šablon Azure Resource Manager pro nový i stávající server.

### <a name="for-a-new-server"></a>Pro nový server

Použití jedné z předem vytvořených šablon Azure Resource Manageru k zřízení serveru s povoleným šifrováním dat: [Příklad s šifrováním dat](https://github.com/Azure/azure-postgresql/tree/master/arm-templates/ExampleWithDataEncryption)

Tato šablona Azure Resource Manager vytvoří databázi Azure pro server PostgreSQL Single a použije **KeyVault** a **Key** předané jako parametry k povolení šifrování dat na serveru.

### <a name="for-an-existing-server"></a>Pro existující server
Kromě toho můžete použít šablony Azure Resource Manager uvolit šifrování dat na stávajících azure database pro postgreSQL jediné servery.

* Předaj ID prostředku klíče Azure Key Vault, `Uri` který jste zkopírovali dříve pod vlastností v objektu vlastností.

* Jako verzi rozhraní API použijte *náhled 2020-01-01.*

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

 Další informace o šifrování dat najdete v [tématu Azure Database for PostgreSQL Jednoserverové šifrování dat pomocí klíče spravovaného zákazníkem](concepts-data-encryption-postgresql.md).
