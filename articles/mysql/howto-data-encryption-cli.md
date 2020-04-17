---
title: Šifrování dat – Azure CLI – Azure Database for MySQL
description: Zjistěte, jak nastavit a spravovat šifrování dat pro azure databázi pro MySQL pomocí azure cli.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 37f6f0dc9c1221207273110252bff445d2e1245b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459096"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-cli"></a>Šifrování dat pro Azure Database for MySQL pomocí azure cli

Zjistěte, jak pomocí azure cli nastavit a spravovat šifrování dat pro azure database for MySQL.

## <a name="prerequisites-for-azure-cli"></a>Požadavky pro azure cli

* Musíte mít předplatné Azure a být správcem tohoto předplatného.
* Vytvořte trezor klíčů a klíč, který chcete použít pro klíč spravovaný zákazníkem. Povolte také ochranu proti vymazání a měkké odstranění v trezoru klíčů.

    ```azurecli-interactive
    az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true --enable-purge-protection true
    ```

* Ve vytvořeném trezoru klíčů Azure vytvořte klíč, který se použije pro šifrování dat databáze Azure pro MySQL.

    ```azurecli-interactive
    az keyvault key create --name <key_name> -p software --vault-name <vault_name>
    ```

* Chcete-li použít existující trezor klíčů, musí mít následující vlastnosti, které chcete použít jako klíč spravovaný zákazníkem:
  * [Obnovitelné odstranění](../key-vault/general/overview-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Vyčištění chráněno](../key-vault/general/overview-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* Klíč musí mít následující atributy, které chcete použít jako klíč spravovaný zákazníkem:
  * Bez data vypršení platnosti
  * Není zakázáno
  * Provést **operace get**, **wrap**, **unwrap**

## <a name="set-the-right-permissions-for-key-operations"></a>Nastavení správných oprávnění pro klíčové operace

1. Existují dva způsoby, jak získat spravovanou identitu pro databázi Azure pro MySQL.

    ### <a name="create-an-new-azure-database-for-mysql-server-with-a-managed-identity"></a>Vytvořte novou Azure Database for MySQL server se spravovanou identitou.

    ```azurecli-interactive
    az mysql server create --name -g <resource_group> --location <locations> --storage-size <size>  -u <user>-p <pwd> --backup-retention <7> --sku-name <sku name> --geo-redundant-backup <Enabled/Disabled>  --assign-identity
    ```

    ### <a name="update-an-existing-the-azure-database-for-mysql-server-to-get-a-managed-identity"></a>Aktualizujte existující databázi Azure pro server MySQL, abyste získali spravovanou identitu.

    ```azurecli-interactive
    az mysql server update --name  <server name>  -g <resource_group> --assign-identity
    ```

2. Nastavte **oprávnění klíče** (**Get**, **Wrap**, **Unwrap**) pro **hlavní**, což je název serveru MySQL.

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Nastavení šifrování dat pro Azure Database for MySQL

1. Povolte šifrování dat pro Azure Database for MySQL pomocí klíče vytvořeného v trezoru klíčů Azure.

    ```azurecli-interactive
    az mysql server key create –name  <server name>  -g <resource_group> --kid <key url>
    ```

    Klíčová adresa url:https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Použití šifrování dat pro servery pro obnovení nebo repliky

Po Azure Database for MySQL je šifrována s klientem spravovaný klíč uložený v trezoru klíčů, všechny nově vytvořené kopie serveru je také šifrována. Tuto novou kopii můžete vytvořit buď prostřednictvím místní operace nebo operace geografického obnovení, nebo prostřednictvím operace repliky (místní/mezi oblastmi). Takže pro šifrovaný server MySQL můžete použít následující kroky k vytvoření šifrovaného obnoveného serveru.

### <a name="creating-a-restoredreplica-server"></a>Vytvoření obnoveného/replikového serveru

  *  [Vytvoření serveru pro obnovení](howto-restore-server-cli.md) 
  *  [Vytvoření serveru replik y pro čtení](howto-read-replicas-cli.md) 

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>Po obnovení serveru znovu ověřte šifrování dat obnovený server

    ```azurecli-interactive
    az mysql server key create –name  <server name> -g <resource_group> --kid <key url>
    ```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-mysql"></a>Další funkce pro klíč používaný pro databázi Azure pro MySQL

### <a name="get-the-key-used"></a>Získání klíče

    ```azurecli-interactive
    az mysql server key show --name  <server name>  -g <resource_group> --kid <key url>
    ```

    Key url:  https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>

### <a name="list-the-key-used"></a>Seznam použitého klíče

    ```azurecli-interactive
    az mysql server key list --name  <server name>  -g <resource_group>
    ```

### <a name="drop-the-key-being-used"></a>Zapustit používaný klíč

    ```azurecli-interactive
    az mysql server key delete -g <resource_group> --kid <key url> 
    ```

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Povolení šifrování dat pomocí šablony Azure Resource Manageru

Kromě portálu Azure můžete taky povolit šifrování dat na serveru Azure Database for MySQL pomocí šablon Azure Resource Manager pro nové i stávající servery.

### <a name="for-a-new-server"></a>Pro nový server

Použití jedné z předem vytvořených šablon Azure Resource Manageru k zřízení serveru s povoleným šifrováním dat: [Příklad s šifrováním dat](https://github.com/Azure/azure-mysql/tree/master/arm-templates/ExampleWithDataEncryption)

Tato šablona Azure Resource Manager vytvoří Azure Database for MySQL server a používá **KeyVault** a **Key** předaný jako parametry pro povolení šifrování dat na serveru.

### <a name="for-an-existing-server"></a>Pro existující server
Kromě toho můžete použít šablony Azure Resource Manager uvolit šifrování dat na stávajících azure databáze pro servery MySQL.

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
      "type": "Microsoft.DBforMySQL/servers",
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
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
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
                    "objectId": "[reference(resourceId('Microsoft.DBforMySQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
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
      "type": "Microsoft.DBforMySQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
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

 Další informace o šifrování dat najdete v [tématu Azure Database for MySQL šifrování dat pomocí klíče spravovaného zákazníkem](concepts-data-encryption-mysql.md).
