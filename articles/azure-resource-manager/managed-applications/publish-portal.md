---
title: Publikování spravovaných aplikací prostřednictvím portálu
description: Ukazuje, jak pomocí portálu Azure vytvořit spravovanou aplikaci Azure, která je určena pro členy vaší organizace.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: tomfitz
ms.openlocfilehash: 05302d92f2304be35a7b88fac6fabfc17b13c63e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651719"
---
# <a name="publish-a-service-catalog-application-through-azure-portal"></a>Publikování aplikace katalogu služeb prostřednictvím portálu Azure

Portál Azure můžete použít k publikování [spravovaných aplikací,](overview.md) které jsou určené pro členy vaší organizace. Oddělení IT může například publikovat spravované aplikace, které zajišťují dodržování standardů organizace. Tyto spravované aplikace jsou k dispozici prostřednictvím katalogu služeb, ne prostřednictvím Azure Marketplace.

## <a name="prerequisites"></a>Požadavky

Při publikování spravované aplikace zadáte identitu pro správu prostředků. Doporučujeme zadat skupinu uživatelů služby Azure Active Directory. Pokud chcete vytvořit skupinu uživatelů Služby Azure Active Directory, přečtěte si informace [o vytvoření skupiny a přidání členů ve službě Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). 

Soubor ZIP obsahující definici spravované aplikace musí být k dispozici prostřednictvím identifikátoru URI. Doporučujeme nahrát soubor ZIP do objektu blob úložiště. 

## <a name="create-managed-application-with-portal"></a>Vytvoření spravované aplikace pomocí portálu

1. V levém horním rohu vyberte **+ Nový**.

   ![Nová služba](./media/publish-portal/new.png)

1. Vyhledejte **katalog služeb**.

1. Ve výsledcích posuňte, dokud nenajdete **definici spravované aplikace katalogu služeb**. Vyberte ji.

   ![Hledání definic spravovaných aplikací](./media/publish-portal/select-managed-apps-definition.png)

1. Výběrem **možnosti Vytvořit** zahájíte proces vytváření definice spravované aplikace.

   ![Vytvoření definice spravované aplikace](./media/publish-portal/create-definition.png)

1. Zadejte hodnoty pro název, zobrazovaný název, popis, umístění, předplatné a skupinu prostředků. U identifikátoru URI souboru balíčku zadejte cestu k souboru ZIP, který jste vytvořili.

   ![Zadat hodnoty](./media/publish-portal/fill-application-values.png)

1. Až se dostanete do části Úroveň ověřování a uzamčení, vyberte **Přidat autorizaci**.

   ![Přidání autorizace](./media/publish-portal/add-authorization.png)

1. Vyberte skupinu Azure Active Directory pro správu prostředků a vyberte **OK**.

   ![Přidat skupinu autorizací](./media/publish-portal/add-auth-group.png)

1. Po zadání všech hodnot vyberte **příkaz Vytvořit**.

   ![Vytvoření spravované aplikace](./media/publish-portal/create-app.png)

## <a name="next-steps"></a>Další kroky

* Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací](overview.md).
* Příklady spravovaných aplikací najdete v [tématu Ukázkové projekty pro spravované aplikace Azure](sample-projects.md).
* Pokud chcete zjistit, jak vytvořit definiční soubor uživatelského rozhraní pro spravovanou aplikaci, přečtěte si téma [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).