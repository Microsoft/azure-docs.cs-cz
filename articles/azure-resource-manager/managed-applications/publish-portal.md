---
title: Publikování spravovaných aplikací prostřednictvím portálu
description: Ukazuje, jak použít Azure Portal k vytvoření spravované aplikace Azure, která je určená pro členy vaší organizace.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: tomfitz
ms.openlocfilehash: 05302d92f2304be35a7b88fac6fabfc17b13c63e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "75651719"
---
# <a name="publish-a-service-catalog-application-through-azure-portal"></a>Publikování aplikace katalogu služeb prostřednictvím Azure Portal

K publikování [spravovaných aplikací](overview.md) , které jsou určené pro členy vaší organizace, můžete použít Azure Portal. Oddělení IT může například publikovat spravované aplikace, které zajišťují dodržování standardů organizace. Tyto spravované aplikace jsou k dispozici prostřednictvím katalogu služeb, ne prostřednictvím Azure Marketplace.

## <a name="prerequisites"></a>Požadavky

Při publikování spravované aplikace zadáte identitu pro správu prostředků. Doporučujeme zadat Azure Active Directory skupinu uživatelů. Chcete-li vytvořit skupinu uživatelů Azure Active Directory, přečtěte si téma [Vytvoření skupiny a přidání členů v Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). 

Soubor. zip, který obsahuje definici spravované aplikace, musí být k dispozici prostřednictvím identifikátoru URI. Doporučujeme nahrát soubor. zip do objektu BLOB úložiště. 

## <a name="create-managed-application-with-portal"></a>Vytvoření spravované aplikace pomocí portálu

1. V levém horním rohu vyberte **+ Nový**.

   ![Nová služba](./media/publish-portal/new.png)

1. Vyhledejte **katalog služeb**.

1. Ve výsledcích posuňte posuvník, dokud nenajdete **definici spravované aplikace katalogu služeb**. Vyberte ji.

   ![Hledat definice spravovaných aplikací](./media/publish-portal/select-managed-apps-definition.png)

1. Výběrem **vytvořit** zahájíte proces vytváření definice spravované aplikace.

   ![Vytvořit definici spravované aplikace](./media/publish-portal/create-definition.png)

1. Zadejte hodnoty pro název, zobrazovaný název, popis, umístění, předplatné a skupinu prostředků. Jako identifikátor URI souboru balíčku zadejte cestu k souboru zip, který jste vytvořili.

   ![Zadat hodnoty](./media/publish-portal/fill-application-values.png)

1. Až se dostanete do části ověřování a úroveň zámku, vyberte **Přidat autorizaci**.

   ![Přidat autorizaci](./media/publish-portal/add-authorization.png)

1. Vyberte skupinu Azure Active Directory pro správu prostředků a vyberte **OK**.

   ![Přidat skupinu autorizací](./media/publish-portal/add-auth-group.png)

1. Pokud jste zadali všechny hodnoty, vyberte **vytvořit**.

   ![Vytvoření spravované aplikace](./media/publish-portal/create-app.png)

## <a name="next-steps"></a>Další kroky

* Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací](overview.md).
* Příklady spravovaných aplikací najdete v tématu [Ukázkové projekty pro spravované aplikace Azure](sample-projects.md).
* Pokud chcete zjistit, jak vytvořit definiční soubor uživatelského rozhraní pro spravovanou aplikaci, přečtěte si téma [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).