---
title: 'Rychlý Start: Vytvoření šablony Azure Resource Manager pracovního prostoru Azure synapse'
description: Naučte se vytvořit pracovní prostor synapse pomocí šablony Azure Resource Manager (šablona ARM).
services: azure-resource-manager
author: julieMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 08/07/2020
ms.openlocfilehash: 2b05220081dde4db6f07f21fb9978d9e466235ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92740366"
---
# <a name="quickstart-create-an-azure-synapse-workspace-using-an-arm-template"></a>Rychlý Start: vytvoření pracovního prostoru Azure synapse pomocí šablony ARM

Tato šablona Azure Resource Manager (šablona ARM) vytvoří pracovní prostor Azure synapse s podkladovým Data Lake Storage. Pracovní prostor Azure synapse je zabezpečená hranice spolupráce pro analytické procesy v Azure synapse Analytics.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure 1](../media/template-deployments/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="review-the-template"></a>Kontrola šablony

Můžete zkontrolovat šablonu výběrem odkazu **vizualizace** . Pak vyberte **Upravit šablonu**.

[![Vizualizace](../media/template-deployments/template-visualize-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

Šablona definuje dva prostředky:

- Účet úložiště
- Pracovní prostor

## <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte následující obrázek pro přihlášení do Azure a otevřete šablonu. Tato šablona vytvoří pracovní prostor synapse.

   [![Nasazení do Azure 2](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

1. Zadejte nebo aktualizujte následující hodnoty:

   - **Předplatné**: vyberte předplatné Azure.
   - **Skupina prostředků**: vyberte **vytvořit novou** a zadejte jedinečný název pro skupinu prostředků a vyberte **OK**. Nová skupina prostředků vám usnadní vyčištění prostředků.
   - **Oblast**: Vyberte oblast.  Například **USA – střed**.
   - **Název**: zadejte název pracovního prostoru.
   - **Přihlášení správce SQL**: zadejte uživatelské jméno správce pro SQL Server.
   - **Heslo správce SQL**: zadejte heslo správce pro SQL Server.
   - **Hodnoty značek**: přijměte výchozí hodnotu.
   - **Zkontrolujte a vytvořte**: vybrat.
   - **Vytvořit**: vyberte.

## <a name="next-steps"></a>Další kroky

Další informace o službě Azure synapse Analytics a Azure Resource Manager najdete dál v článcích níže.

- Přečtěte si [Přehled služby Azure synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)
- Další informace o [Azure Resource Manageru](../azure-resource-manager/management/overview.md)
- [Vytvoření a nasazení první šablony ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
