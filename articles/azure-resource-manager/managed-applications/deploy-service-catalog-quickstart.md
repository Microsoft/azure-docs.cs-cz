---
title: Použití Azure Portal k nasazení aplikace Service Catalog
description: Zobrazuje uživatele spravovaných aplikací, jak nasadit aplikaci katalogu služeb prostřednictvím Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: ce58fc69496f54c078b0a0a55a8a3c7cad82a051
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "81391711"
---
# <a name="quickstart-deploy-service-catalog-app-through-azure-portal"></a>Rychlý Start: nasazení aplikace katalogu služeb prostřednictvím Azure Portal

V [předchozím rychlém](publish-service-catalog-app.md)startu jste publikovali definici spravované aplikace. V tomto rychlém startu vytvoříte aplikaci katalogu služeb z této definice.

## <a name="create-service-catalog-app"></a>Vytvořit aplikaci katalogu služeb

V Azure Portal použijte následující postup:

1. Vyberte **Vytvořit prostředek**.

   ![Vytvoření prostředku](./media/deploy-service-catalog-quickstart/create-new.png)

1. Vyhledejte **spravovanou aplikaci katalogu služeb** a vyberte ji z dostupných možností.

   ![Vyhledat aplikaci katalogu služeb](./media/deploy-service-catalog-quickstart/select-service-catalog.png)

1. Zobrazí se popis spravované aplikační služby. Vyberte **Vytvořit**.

   ![Výběr možnosti vytvoření](./media/deploy-service-catalog-quickstart/create-service-catalog.png)

1. Na portálu se zobrazí definice spravovaných aplikací, ke kterým máte přístup. Z dostupných definic vyberte ten, který chcete nasadit. V tomto rychlém startu použijte definici **spravovaného účtu úložiště** , kterou jste vytvořili v předchozím rychlém startu. Vyberte **Vytvořit**.

   ![Vyberte definici pro nasazení](./media/deploy-service-catalog-quickstart/select-definition.png)

1. Zadejte hodnoty pro kartu **základy** . Vyberte předplatné Azure, do kterého chcete nasadit aplikaci katalogu služeb. Vytvořte novou skupinu prostředků s názvem skupina **aplikací**. Vyberte umístění pro vaši aplikaci. Po dokončení vyberte **OK**.

   ![Zadejte hodnoty pro základní](./media/deploy-service-catalog-quickstart/provide-basics.png)

1. Zadejte předponu názvu účtu úložiště. Vyberte typ účtu úložiště, který se má vytvořit. Po dokončení vyberte **OK**.

   ![Zadejte hodnoty pro úložiště.](./media/deploy-service-catalog-quickstart/provide-storage.png)

1. Zkontrolujte souhrnné informace. Po úspěšném ověření spusťte nasazení výběrem **OK** .

   ![Zobrazit souhrn](./media/deploy-service-catalog-quickstart/view-summary.png)

## <a name="view-results"></a>Zobrazení výsledků

Po nasazení aplikace katalogu služeb máte dvě nové skupiny prostředků. Jedna skupina prostředků obsahuje aplikaci katalogu služeb. Tato skupina prostředků obsahuje prostředky pro aplikaci katalogu služeb.

1. Zobrazte skupinu prostředků s názvem App a podívejte **se na aplikaci** katalogu služeb.

   ![Zobrazení aplikace](./media/deploy-service-catalog-quickstart/view-managed-application.png)

1. Pokud chcete zobrazit prostředky pro aplikaci katalogu služeb, zobrazte skupinu prostředků s názvem App Group **{hash-Characters}** .

   ![Zobrazení prostředků](./media/deploy-service-catalog-quickstart/view-resources.png)

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak vytvořit definiční soubory pro spravovanou aplikaci, najdete v tématu [Vytvoření a publikování definice spravované aplikace](publish-service-catalog-app.md).
* Informace o rozhraní příkazového řádku Azure najdete v tématu [nasazení aplikace katalogu služeb pomocí Azure CLI](./scripts/managed-application-cli-sample-create-application.md).
* Informace o PowerShellu najdete v tématu [nasazení aplikace katalogu služeb pomocí PowerShellu](./scripts/managed-application-poweshell-sample-create-application.md).
