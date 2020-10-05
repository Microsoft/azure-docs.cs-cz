---
title: Spravovaná identita v pracovním prostoru synapse
description: Článek s vysvětlením spravované identity v pracovním prostoru Azure synapse
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: e3c2b2ce5bd9af60d6d1fd4ac066681ee8473521
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91249446"
---
# <a name="azure-synapse-workspace-managed-identity-preview"></a>Spravovaná identita v pracovním prostoru Azure synapse (Preview)

V tomto článku se dozvíte o spravované identitě v pracovním prostoru Azure synapse.

## <a name="managed-identities"></a>Spravované identity

Spravovaná identita pro prostředky Azure je funkcí Azure Active Directory. Tato funkce poskytuje službám Azure automaticky spravovanou identitu v Azure AD. Možnost spravované identity můžete použít k ověření u libovolné služby, která podporuje ověřování Azure AD.

Spravované identity pro prostředky Azure jsou novým názvem pro službu, která se dřív jmenovala jako Identita spravované služby (MSI). Další informace najdete v tématu [spravované identity](../../active-directory/managed-identities-azure-resources/overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .

## <a name="azure-synapse-workspace-managed-identity"></a>Spravovaná identita v pracovním prostoru Azure synapse

Při vytváření pracovního prostoru se vytvoří spravovaná identita přiřazená systémem pro váš pracovní prostor Azure synapse.

>[!NOTE]
>Tato identita spravovaná pracovním prostorem se bude v ostatních částech tohoto dokumentu označovat jako spravovaná identita.

Azure synapse používá spravovanou identitu k orchestraci kanálů. Životní cyklus spravované identity je přímo vázaný na pracovní prostor Azure synapse. Pokud pracovní prostor Azure synapse odstraníte, vyčistí se i spravovaná identita.

Identita spravovaná pracovním prostorem potřebuje oprávnění k provádění operací v kanálech. K vyhledání spravované identity při udělování oprávnění můžete použít ID objektu nebo název vašeho pracovního prostoru Azure synapse.

## <a name="retrieve-managed-identity-in-azure-portal"></a>Načtení spravované identity v Azure Portal

Spravovanou identitu můžete načíst v Azure Portal. V Azure Portal otevřete pracovní prostor Azure synapse a v levém navigačním panelu vyberte **Přehled** . Na hlavní obrazovce se zobrazí ID objektu spravované identity.

![ID spravovaného objektu identity](./media/synapse-workspace-managed-identity/workspace-managed-identity-1.png)

Informace o spravované identitě se zobrazí také při vytvoření propojené služby, která podporuje ověřování spravované identity z Azure synapse studia.

Spusťte **Azure synapse Studio** a v levém navigačním panelu vyberte kartu **Spravovat** . Pak vyberte **propojené služby** a zvolte možnost **+ Nová** a vytvořte novou propojenou službu.

![Vytvoření propojené služby 1](./media/synapse-workspace-managed-identity/workspace-managed-identity-2.png)

V okně **Nová propojená služba** zadejte *Azure Data Lake Storage Gen2*. V níže uvedeném seznamu vyberte typ prostředku **Azure Data Lake Storage Gen2** a zvolte **pokračovat**.

![Vytvoření propojené služby 2](./media/synapse-workspace-managed-identity/workspace-managed-identity-3.png)

V dalším okně vyberte možnost **spravovaná identita** pro **metodu ověřování**. Zobrazí se **název** spravované identity a **ID objektu**.

![Vytvoření propojené služby 3](./media/synapse-workspace-managed-identity/workspace-managed-identity-4.png)

## <a name="next-steps"></a>Další kroky

Další informace o [udělování oprávnění ke spravované identitě Azure synapse Workspace](./how-to-grant-workspace-managed-identity-permissions.md)
