---
title: Při nasazování spravované aplikace použít Key Vault
description: Ukazuje, jak používat přístupové tajné kódy v Azure Key Vault při nasazování spravovaných aplikací.
author: tfitzmac
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: d82e5aed6318e112a0daabf581aec61c8ed5fcbc
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75650640"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Přístup Key Vault tajného klíče při nasazení Azure Managed Applications

Pokud během nasazení potřebujete předat zabezpečenou hodnotu (třeba heslo) jako parametr, můžete načíst hodnotu z [Azure Key Vault](../../key-vault/key-vault-overview.md). Pro přístup k Key Vault při nasazování spravovaných aplikací musíte udělit přístup k instančnímu objektu **poskytovatele prostředků zařízení** . Služba spravovaných aplikací používá tuto identitu ke spuštění operací. Chcete-li úspěšně načíst hodnotu z Key Vault během nasazení, instanční objekt musí být schopný získat přístup k Key Vault.

Tento článek popisuje, jak nakonfigurovat Key Vault pro práci se spravovanými aplikacemi.

## <a name="enable-template-deployment"></a>Povolit nasazení šablony

1. Na portálu vyberte svůj Key Vault.

1. Vyberte **Zásady přístupu**.   

   ![Vybrat zásady přístupu](./media/key-vault-access/select-access-policies.png)

1. Vyberte **Kliknutím zobrazíte pokročilé zásady přístupu**.

   ![Zobrazit pokročilé zásady přístupu](./media/key-vault-access/advanced.png)

1. **Pro nasazení šablony vyberte Povolit přístup k Azure Resource Manager**. Potom vyberte **Uložit**.

   ![Povolit nasazení šablony](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Přidat službu jako přispěvatele

1. Vyberte **Řízení přístupu (IAM)** .

   ![Vybrat řízení přístupu](./media/key-vault-access/access-control.png)

1. Vyberte **přidat přiřazení role**.

   ![Výběr možnosti Přidat](./media/key-vault-access/add-access-control.png)

1. Jako role vyberte **Přispěvatel** . Vyhledejte **poskytovatele prostředků zařízení** a vyberte ho z dostupných možností.

   ![Hledat poskytovatele](./media/key-vault-access/search-provider.png)

1. Vyberte **Uložit**.

## <a name="reference-key-vault-secret"></a>Reference Key Vault tajný klíč

Chcete-li předat tajný kód z Key Vault do šablony ve spravované aplikaci, je nutné použít [propojenou šablonu](../templates/linked-templates.md) a odkazovat na Key Vault v parametrech pro propojenou šablonu. Zadejte ID prostředku Key Vault a název tajného klíče.

```json
"resources": [{
  "apiVersion": "2015-01-01",
  "name": "linkedTemplate",
  "type": "Microsoft.Resources/deployments",
  "properties": {
    "mode": "incremental",
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json",
      "contentVersion": "1.0.0.0"
    },
    "parameters": {
      "adminPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"
          },
          "secretName": "<secret-name>"
        }
      },
      "adminLogin": { "value": "[parameters('adminLogin')]" },
      "sqlServerName": {"value": "[parameters('sqlServerName')]"}
    }
  }
}],
```

## <a name="next-steps"></a>Další kroky

Nakonfigurovali jste Key Vault, aby byly dostupné během nasazování spravované aplikace.

* Informace o předání hodnoty z Key Vault jako parametru šablony naleznete v tématu [použití Azure Key Vault k předání hodnoty zabezpečeného parametru během nasazování](../templates/key-vault-parameter.md).
* Příklady spravovaných aplikací najdete v tématu [Ukázkové projekty pro spravované aplikace Azure](sample-projects.md).
* Pokud chcete zjistit, jak vytvořit definiční soubor uživatelského rozhraní pro spravovanou aplikaci, přečtěte si téma [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).