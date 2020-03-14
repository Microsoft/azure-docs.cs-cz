---
title: Šifrování dat – Azure Portal Azure Database for MySQL
description: Naučte se, jak nastavit a spravovat šifrování dat pro Azure Database for MySQL pomocí Azure Portal.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 78a290b1e2984719645fb4d4ff253ab021a0826e
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299035"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>Šifrování dat pro Azure Database for MySQL pomocí Azure Portal

Naučte se používat Azure Portal k nastavení a správě šifrování dat pro Azure Database for MySQL.

## <a name="prerequisites-for-azure-cli"></a>Předpoklady pro Azure CLI

* Musíte mít předplatné Azure a mít oprávnění správce k tomuto předplatnému.
* V Azure Key Vault vytvořte Trezor klíčů a klíč, který se použije pro klíč spravovaný zákazníkem.
* Trezor klíčů musí mít následující vlastnosti, které se mají použít jako klíč spravovaný zákazníkem:
  * [Obnovitelné odstranění](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Vyprázdnit chráněné](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* Klíč musí obsahovat následující atributy, které se použijí jako klíč spravovaný zákazníkem:
  * Žádné datum vypršení platnosti
  * Nezakázáno
  * Může provádět operace Get, Wrap Key, rozbalení klíčových operací.

## <a name="set-the-right-permissions-for-key-operations"></a>Nastavení správných oprávnění pro klíčové operace

1. V Key Vault vyberte **zásady přístupu** > **Přidat zásady přístupu**.

   ![Snímek obrazovky Key Vault se zvýrazněnými zásadami přístupu a přidáním zásad přístupu](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Vyberte **klíčová oprávnění**a vyberte **získat**, **zalamovat**, **rozbalení**a **objekt zabezpečení**, což je název serveru MySQL. Pokud se váš hlavní server nenašel v seznamu existujících objektů zabezpečení, je potřeba ho zaregistrovat. Budete vyzváni k registraci objektu zabezpečení serveru, když se pokusíte nastavit šifrování dat poprvé, a dojde k chybě.

   ![Přehled zásad přístupu](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. Vyberte **Save** (Uložit).

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Nastavení šifrování dat pro Azure Database for MySQL

1. V Azure Database for MySQL pro nastavení klíče spravovaného zákazníkem vyberte možnost **šifrování dat** .

   ![Snímek obrazovky Azure Database for MySQL se zvýrazněným šifrováním dat](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Můžete buď vybrat Trezor klíčů a pár klíčů, nebo zadat identifikátor klíče.

   ![Snímek obrazovky Azure Database for MySQL s zvýrazněnými možnostmi šifrování dat](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. Vyberte **Save** (Uložit).

4. Aby bylo zajištěno, že všechny soubory (včetně dočasných souborů) jsou plně zašifrované, restartujte server.

## <a name="restore-or-create-a-replica-of-the-server"></a>Obnovit nebo vytvořit repliku serveru

Když je Azure Database for MySQL zašifrovaný pomocí spravovaného klíče zákazníka uloženého v Key Vault, všechny nově vytvořené kopie serveru se taky šifrují. Tuto novou kopii můžete vytvořit buď prostřednictvím operace místního nebo geografického obnovení, nebo prostřednictvím operace repliky (místní/napříč oblastí). Pro zašifrovaný Server MySQL pak můžete použít následující postup k vytvoření šifrovaného obnoveného serveru.

1. Na serveru vyberte **přehled** > **obnovit**.

   ![Snímek obrazovky Azure Database for MySQL s zvýrazněným přehledem a obnovením](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   V případě serveru s povolenou replikací vyberte v části **Nastavení** možnost **replikace**.

   ![Snímek obrazovky Azure Database for MySQL s zvýrazněnou replikací](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. Po dokončení operace obnovení bude nový server vytvořen zašifrovaný pomocí klíče primárního serveru. Funkce a možnosti na serveru jsou ale zakázané a server není dostupný. Tím zabráníte manipulaci s daty, protože identitě nového serveru ještě nebyla udělena oprávnění pro přístup k trezoru klíčů.

   ![Snímek obrazovky Azure Database for MySQL s zvýrazněným stavem nepřístupu](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Aby byl server přístupný, znovu ověřte klíč na obnoveném serveru. Vyberte možnost **šifrování dat** > znovu **Ověřit klíč**.

   > [!NOTE]
   > První pokus o nové ověření se nezdaří, protože instanční objekt nového serveru musí mít přístup k trezoru klíčů. Chcete-li vygenerovat instanční objekt, vyberte znovu **Ověřit klíč**, čímž se zobrazí chyba, ale vygeneruje se instanční objekt. Potom si přečtěte tento [postup](#set-the-right-permissions-for-key-operations) výše v tomto článku.

   ![Snímek obrazovky Azure Database for MySQL se zvýrazněným krokem replatným](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Bude nutné poskytnout Trezor klíčů k novému serveru.

4. Po registraci instančního objektu znovu ověřte klíč a server obnoví své běžné funkce.

   ![Snímek obrazovky Azure Database for MySQL se zobrazením obnovených funkcí](media/concepts-data-access-and-security-data-encryption/restore-successful.png)


## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Použití šablony Azure Resource Manager k povolení šifrování dat

Kromě Azure Portal můžete taky povolit šifrování dat na Azure Database for MySQL serveru pomocí šablon Azure Resource Manager pro nové i existující servery.

### <a name="for-a-new-server"></a>Pro nový server

Použijte jednu z předem vytvořených šablon Azure Resource Manager k zřízení serveru s povoleným šifrováním dat: [příklad s šifrováním dat](https://github.com/Azure/azure-mysql/tree/master/arm-templates/ExampleWithDataEncryption)

Tato šablona Azure Resource Manager vytvoří server Azure Database for MySQL a použije **trezor** **klíčů a klíč** předaný jako parametry pro povolení šifrování dat na serveru.

### <a name="for-an-existing-server"></a>Pro existující server
Kromě toho můžete pomocí Azure Resource Manager šablon povolit šifrování dat na stávajících serverech Azure Database for MySQL.

* Předejte identifikátor URI Azure Key Vaultho klíče, který jste zkopírovali dříve pod vlastností `keyVaultKeyUri` v objektu Properties (vlastnosti).

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

 Další informace o šifrování dat najdete v tématu [Azure Database for MySQL šifrování dat pomocí klíče spravovaného zákazníkem](concepts-data-encryption-mysql.md).
