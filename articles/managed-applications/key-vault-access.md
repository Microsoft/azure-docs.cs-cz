---
title: Použití Azure Key Vault se spravovanými aplikacemi | Dokumentace Microsoftu
description: Ukazuje způsob použití tajné přístupové kódy ve službě Azure Key Vault při nasazení spravované aplikace
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 07/11/2018
ms.author: tomfitz
ms.openlocfilehash: dcfbb7f3b1d110d4c1fdf22863d795c85152ec35
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52725008"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Tajný kód trezoru klíčů přístup při nasazení spravované aplikace Azure

Pokud potřebujete předat jako parametr zabezpečenou hodnotu (jako jsou hesla) během nasazení, můžete načíst hodnotu z [Azure Key Vault](../key-vault/key-vault-whatis.md). Pro přístup k trezoru klíčů při nasazování spravovaných aplikací, je nutné udělit přístup k **poskytovatele prostředků zařízení** instančního objektu. Tento článek popisuje postup konfigurace služby Key Vault pro práci s spravované aplikace.

## <a name="enable-template-deployment"></a>Povolit nasazení šablony

1. Na portálu vyberte služby Key Vault.

1. Vyberte **Zásady přístupu**.   

   ![Vyberte zásady přístupu](./media/key-vault-access/select-access-policies.png)

1. Vyberte **Kliknutím zobrazíte pokročilé zásady přístupu**.

   ![Zobrazit pokročilé zásady přístupu.](./media/key-vault-access/advanced.png)

1. Vyberte **povolit přístup k Azure Resource Manageru pro nasazení šablony**. Potom vyberte **Uložit**.

   ![Povolit nasazení šablony](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Přidání služby jako přispěvatele

1. Vyberte **řízení přístupu (IAM)**.

   ![Vyberte řízení přístupu](./media/key-vault-access/access-control.png)

1. Vyberte **přidat přiřazení role**.

   ![Výběr možnosti Přidat](./media/key-vault-access/add-access-control.png)

1. Vyberte **Přispěvatel** pro danou roli. Vyhledejte **poskytovatele prostředků zařízení** a vyberte ho z dostupných možností.

   ![Vyhledejte poskytovatele](./media/key-vault-access/search-provider.png)

1. Vyberte **Uložit**.

## <a name="reference-key-vault-secret"></a>Odkaz na tajný kód trezoru klíčů

K předání tajného klíče ze služby Key Vault do šablony v aplikaci spravované, je nutné použít [propojené šablony](../azure-resource-manager/resource-group-linked-templates.md) a odkazovat na parametry šablony propojené službě Key Vault. Zadejte ID prostředku služby Key Vault a název tajného klíče.

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

## <a name="next-steps"></a>Další postup

Nakonfigurujete služby Key Vault má být přístupné během nasazení spravované aplikace.

* Informace o předáním hodnoty ze služby Key Vault jako parametr šablony najdete v tématu [použití Azure Key Vault k předání zabezpečený parametr. hodnoty během nasazení](../azure-resource-manager/resource-manager-keyvault-parameter.md).
* Spravované aplikace příklady najdete v tématu [ukázkových projektů Azure spravované aplikace](sample-projects.md).
* Pokud chcete zjistit, jak vytvořit definiční soubor uživatelského rozhraní pro spravovanou aplikaci, přečtěte si téma [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).