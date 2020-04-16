---
title: Spravovaná identita v pracovním prostoru Azure Synapse
description: Článek, který vysvětluje spravovanou identitu v pracovním prostoru Azure Synapse
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: ee0e6249acf3fbbab369d42ae691a5a826df1ee8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81425116"
---
# <a name="azure-synapse-workspace-managed-identity-preview"></a>Spravovaná identita pracovního prostoru Azure Synapse (preview)

V tomto článku se dozvíte o spravované identity v pracovním prostoru Azure Synapse.

## <a name="managed-identities"></a>Spravované identity

Spravovaná identita pro prostředky Azure je funkce Služby Azure Active Directory. Tato funkce poskytuje službám Azure automaticky spravovanou identitu v Azure AD. Možnost spravované identity můžete použít k ověření na libovolné služby, které podporují ověřování Azure AD.

Spravované identity pro prostředky Azure jsou nový název služby dříve známé jako identita spravované služby (MSI). Další informace najdete v [tématu Spravované identity.](../../active-directory/managed-identities-azure-resources/overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="azure-synapse-workspace-managed-identity"></a>Spravovaná identita pracovního prostoru Azure Synapse

Systémem přiřazená spravovaná identita se vytvoří pro váš pracovní prostor Azure Synapse při vytváření pracovního prostoru.

>[!NOTE]
>Tato identita spravované pracovního prostoru bude označována jako spravovaná identita prostřednictvím zbytku tohoto dokumentu.

Azure Synapse používá spravovanou identitu k orchestraci kanálů. Životní cyklus spravované identity je přímo vázaný na pracovní prostor Azure Synapse. Pokud odstraníte pracovní prostor Azure Synapse, pak se také vyčistit spravovaná identita.

Identita spravovaná v pracovním prostoru potřebuje oprávnění k provádění operací v kanálech. ID objektu nebo název pracovního prostoru Azure Synapse můžete použít k nalezení spravované identity při udělování oprávnění.

## <a name="retrieve-managed-identity-in-azure-portal"></a>Načtení spravované identity na webu Azure Portal

Spravovanou identitu můžete načíst na webu Azure Portal. Otevřete pracovní prostor Azure Synapse na webu Azure Portal a z levé navigace vyberte **Přehled.** ID objektu spravované identity se zobrazí na hlavní obrazovce.

![ID spravovaného objektu identity](./media/synapse-workspace-managed-identity/workspace-managed-identity-1.png)

Informace o spravované identitě se také zobrazí, když vytvoříte propojenou službu, která podporuje ověřování spravované identity z Azure Synapse Studio.

Spusťte **Azure Synapse Studio** a v levé navigaci vyberte kartu **Spravovat.** Pak vyberte **Propojené služby** a zvolte možnost **+ Nový** pro vytvoření nové propojené služby.

![Vytvoření propojené služby 1](./media/synapse-workspace-managed-identity/workspace-managed-identity-2.png)

V okně **Nová propojená služba** zadejte *Azure Data Lake Storage Gen2*. Vyberte typ prostředku Azure **Data Lake Storage Gen2** ze seznamu níže a zvolte **Pokračovat**.

![Vytvoření propojené služby 2](./media/synapse-workspace-managed-identity/workspace-managed-identity-3.png)

V dalším okně zvolte **Metodu Spravované identity** pro **ověřování**. Zobrazí se **název** spravované identity a **ID objektu**.

![Vytvoření propojené služby 3](./media/synapse-workspace-managed-identity/workspace-managed-identity-4.png)

## <a name="next-steps"></a>Další kroky

[Udělení oprávnění spravované identitě pracovního prostoru Azure Synapse](./how-to-grant-workspace-managed-identity-permissions.md)
