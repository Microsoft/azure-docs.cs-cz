---
title: Publikování Azure spravované aplikace prostřednictvím portálu | Dokumentace Microsoftu
description: Ukazuje, jak používat Azure portal k vytvoření Azure spravované aplikace, která je určená pro členy vaší organizace.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 11/02/2017
ms.author: tomfitz
ms.openlocfilehash: e52acd8587203c4729ac2bcd6e4bbc09620ead86
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35899170"
---
# <a name="publish-a-service-catalog-application-through-azure-portal"></a>Publikování aplikace katalogu služeb prostřednictvím webu Azure portal

Na webu Azure portal můžete použít k publikování [spravované aplikace](overview.md) , která jsou určená pro členy vaší organizace. Oddělení IT může například publikovat spravované aplikace, které zajišťují dodržování standardů organizace. Tyto spravované aplikace jsou k dispozici prostřednictvím katalogu služeb, ne prostřednictvím Azure Marketplace.

## <a name="prerequisites"></a>Požadavky

Při publikování spravované aplikace zadejte identitu ke správě prostředků. Doporučujeme, abyste že zadejte novou skupinu uživatelů Azure Active Directory. Chcete-li vytvořit novou skupinu uživatelů Azure Active Directory, přečtěte si téma [vytvoření skupiny a přidání členů v Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). 

Soubor .zip, který obsahuje definici spravované aplikace musí být k dispozici prostřednictvím identifikátoru URI. Doporučujeme vám, nahrajte soubor ZIP do objektu blob storage. 

## <a name="create-managed-application-with-portal"></a>Vytvoření spravované aplikace pomocí portálu

1. V levém horním rohu vyberte **+ nová**.

   ![Nová služba](./media/publish-portal/new.png)

1. Vyhledejte **modulu service catalog**.

1. Ve výsledcích vyhledejte **definici spravované aplikace aplikace služby katalogu**. Vyberte ji.

   ![Vyhledat definice spravované aplikace](./media/publish-portal/select-managed-apps-definition.png)

1. Vyberte **vytvořit** zahájíte proces vytvoření definice spravované aplikace.

   ![Vytvoření definice spravované aplikace](./media/publish-portal/create-definition.png)

1. Zadejte hodnoty pro název, zobrazovaný název, popis, umístění, předplatné a skupinu prostředků. Pro identifikátor URI souboru balíčku zadejte cestu k souboru zip, který jste vytvořili.

   ![Zadejte hodnoty](./media/publish-portal/fill-application-values.png)

1. Při přechodu na úroveň ověřování a zámku části vyberte **přidat autorizační**.

   ![Přidat autorizaci](./media/publish-portal/add-authorization.png)

1. Vyberte skupinu Azure Active Directory ke správě prostředků a vyberte **OK**.

   ![Přidat skupinu autorizace](./media/publish-portal/add-auth-group.png)

1. Až zadáte všechny hodnoty, vyberte **vytvořit**.

   ![Vytvoření spravované aplikace](./media/publish-portal/create-app.png)

## <a name="next-steps"></a>Další postup

* Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací](overview.md).
* Spravované aplikace příklady najdete v tématu [ukázkových projektů Azure spravované aplikace](sample-projects.md).
* Pokud chcete zjistit, jak vytvořit definiční soubor uživatelského rozhraní pro spravovanou aplikaci, přečtěte si téma [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).