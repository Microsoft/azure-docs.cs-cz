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
ms.date: 07/09/2018
ms.author: tomfitz
ms.openlocfilehash: 232bea437b38335bdaa189e504d4e5fd9b080a05
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38724054"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Tajný kód trezoru klíčů přístup při nasazení spravované aplikace Azure

Pokud potřebujete předat jako parametr zabezpečenou hodnotu (jako jsou hesla) během nasazení, můžete načíst hodnotu z [Azure Key Vault](../key-vault/key-vault-whatis.md). Pro přístup k trezoru klíčů při nasazování spravovaných aplikací, je nutné udělit přístup k **poskytovatele prostředků zařízení** instančního objektu. Tento článek popisuje postup konfigurace služby Key Vault pro práci s spravované aplikace.

## <a name="enable-template-deployment"></a>Povolit nasazení šablony

1. Na portálu vyberte služby Key Vault.

1. Vyberte **Zásady přístupu**.   

   ![Vyberte zásady přístupu](./media/key-vault-access/select-access-policies.png)

1. Vyberte **kliknutím zobrazíte pokročilé zásady přístupu**.

   ![Zobrazit pokročilé zásady přístupu.](./media/key-vault-access/advanced.png)

1. Vyberte **povolit přístup k Azure Resource Manageru pro nasazení šablony**. Potom vyberte **Uložit**.

   ![Povolit nasazení šablony](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Přidání služby jako přispěvatele

1. Vyberte **řízení přístupu (IAM)**.

   ![Vyberte řízení přístupu](./media/key-vault-access/access-control.png)

1. Vyberte **Přidat**.

   ![Výběr možnosti Přidat](./media/key-vault-access/add-access-control.png)

1. Vyberte **Přispěvatel** pro danou roli. Vyhledejte **poskytovatele prostředků zařízení** a vyberte ho z dostupných možností.

   ![Vyhledejte poskytovatele](./media/key-vault-access/search-provider.png)

1. Vyberte **Uložit**.

## <a name="next-steps"></a>Další postup

Nakonfigurujete služby Key Vault má být přístupné během nasazení spravované aplikace.

* Informace o předáním hodnoty ze služby Key Vault jako parametr šablony najdete v tématu [použití Azure Key Vault k předání zabezpečený parametr. hodnoty během nasazení](../azure-resource-manager/resource-manager-keyvault-parameter.md).
* Spravované aplikace příklady najdete v tématu [ukázkových projektů Azure spravované aplikace](sample-projects.md).
* Pokud chcete zjistit, jak vytvořit definiční soubor uživatelského rozhraní pro spravovanou aplikaci, přečtěte si téma [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).