---
title: Spravovaná identita v pracovním prostoru synapse
description: Článek s vysvětlením spravované identity v pracovním prostoru Azure synapse
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 10/16/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 7790bc2895449e8ab21cbd30d7da0e5529eb0562
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670683"
---
# <a name="azure-synapse-workspace-managed-identity"></a>Spravovaná identita pracovního prostoru Azure Synapse

V tomto článku se dozvíte o spravované identitě v pracovním prostoru Azure synapse.

## <a name="managed-identities"></a>Spravované identity

Spravovaná identita pro prostředky Azure je funkcí Azure Active Directory. Tato funkce poskytuje službám Azure automaticky spravovanou identitu v Azure AD. Možnost spravované identity můžete použít k ověření u libovolné služby, která podporuje ověřování Azure AD.

Spravované identity pro prostředky Azure jsou novým názvem pro službu, která se dřív jmenovala jako Identita spravované služby (MSI). Další informace najdete v tématu [spravované identity](../../active-directory/managed-identities-azure-resources/overview.md) .

## <a name="azure-synapse-workspace-managed-identity"></a>Spravovaná identita pracovního prostoru Azure Synapse

Při vytvoření pracovního prostoru Azure Synapse se pro pracovní prostor vytvoří spravovaná identita přiřazená systémem.

>[!NOTE]
>Tato identita spravovaná pracovním prostorem se bude v ostatních částech tohoto dokumentu označovat jako spravovaná identita.

Azure synapse používá spravovanou identitu k integraci kanálů. Životní cyklus spravované identity je přímo vázaný na pracovní prostor Azure synapse. Pokud pracovní prostor Azure synapse odstraníte, vyčistí se i spravovaná identita.

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
