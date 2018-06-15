---
title: Publikování Azure spravované aplikace prostřednictvím portálu | Microsoft Docs
description: Ukazuje, jak používat Azure portálu Azure vytvořit spravované aplikace, která je určena pro členy vaší organizace.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 11/02/2017
ms.author: tomfitz
ms.openlocfilehash: 69d31a7199347574e8866b275ec17ba3997d80c2
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
ms.locfileid: "34305119"
---
# <a name="publish-a-service-catalog-application-through-azure-portal"></a>Publikování aplikace katalogu služby prostřednictvím portálu Azure

Na portálu Azure můžete použít k publikování [spravované aplikace](overview.md) , jsou určené pro členy vaší organizace. IT oddělení například může publikovat spravovaných aplikací, které bylo možné zajistit kompatibilitu s organizační standardy. Tyto spravované aplikace jsou k dispozici prostřednictvím katalogu služeb, není v Azure marketplace.

## <a name="prerequisites"></a>Požadavky

Při publikování spravované aplikace, zadejte identitu, chcete-li spravovat prostředky. Doporučujeme, abyste že zadejte skupinu uživatele Azure Active Directory. K vytvoření skupiny uživatelů Azure Active Directory, najdete v části [vytvořte skupinu a přidejte členy v Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md). 

Soubor .zip, který obsahuje definici spravované aplikace musí být k dispozici prostřednictvím identifikátoru URI. Doporučujeme odeslat soubor ZIP do objektu blob storage. 

## <a name="create-managed-application-with-portal"></a>Vytvoření spravované aplikaci pomocí portálu

1. V levém horním rohu vyberte **+ nový**.

   ![Nové služby](./media/publish-portal/new.png)

1. Vyhledejte **katalogu služeb**.

1. Ve výsledcích vyhledejte **definice aplikace spravované katalogu služby**. Vyberte ho.

   ![Vyhledejte definice spravovaných aplikací](./media/publish-portal/select-managed-apps-definition.png)

1. Vyberte **vytvořit** ke spuštění procesu vytvoření definice spravovaných aplikací.

   ![Vytvořit definici spravované aplikace](./media/publish-portal/create-definition.png)

1. Zadejte hodnoty pro název, zobrazovaný název, popis, umístění, předplatné a skupina prostředků. Pro soubor balíčku identifikátor URI zadejte cestu k souboru zip, kterou jste vytvořili.

   ![Zadejte hodnoty](./media/publish-portal/fill-application-values.png)

1. Když dojde k ověřování a úroveň zámku části, vyberte **přidat autorizační**.

   ![Přidání ověřování](./media/publish-portal/add-authorization.png)

1. Vyberte skupiny služby Azure Active Directory ke správě prostředků a vyberte **OK**.

   ![Přidat skupinu autorizace](./media/publish-portal/add-auth-group.png)

1. Pokud jste zadali všechny hodnoty, vyberte **vytvořit**.

   ![Vytvoření spravované aplikace](./media/publish-portal/create-app.png)

## <a name="next-steps"></a>Další postup

* Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací](overview.md).
* Příklady spravovaných aplikací najdete v tématu [ukázkové projekty Azure spravované aplikace](sample-projects.md).
* Pokud chcete zjistit, jak vytvořit definiční soubor uživatelského rozhraní pro spravovanou aplikaci, přečtěte si téma [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).