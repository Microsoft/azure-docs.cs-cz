---
title: Nasazení aplikace katalogu služeb pomocí portálu Azure Portal
description: Zobrazuje spotřebitele spravovaných aplikací, jak nasadit aplikaci katalogu služeb prostřednictvím portálu Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: 9a69296ddfc93fd7e8a6650df91876829631f5d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473060"
---
# <a name="quickstart-deploy-service-catalog-app-through-azure-portal"></a>Úvodní příručka: Nasazení aplikace katalogu služeb prostřednictvím portálu Azure

V [předchozím rychlém startu](publish-managed-app-definition-quickstart.md)jste publikovali definici spravované aplikace. V tomto rychlém startu vytvoříte aplikaci katalogu služeb z této definice.

## <a name="create-service-catalog-app"></a>Vytvořit aplikaci katalogu služeb

Na webu Azure Portal použijte následující kroky:

1. Vyberte **Vytvořit prostředek**.

   ![Vytvoření prostředku](./media/deploy-service-catalog-quickstart/create-new.png)

1. Vyhledejte **spravovanou aplikaci katalogu služeb** a vyberte ji z dostupných možností.

   ![Hledat aplikaci katalogu služeb](./media/deploy-service-catalog-quickstart/select-service-catalog.png)

1. Zobrazí se popis služby Spravované aplikace. Vyberte **Vytvořit**.

   ![Výběr možnosti vytvoření](./media/deploy-service-catalog-quickstart/create-service-catalog.png)

1. Portál zobrazuje definice spravovaných aplikací, ke kterým máte přístup. Z dostupných definic vyberte ten, který chcete nasadit. V tomto rychlém startu použijte definici **účtu spravovaného úložiště,** kterou jste vytvořili v předchozím rychlém startu. Vyberte **Vytvořit**.

   ![Vyberte definici, která se má nasadit.](./media/deploy-service-catalog-quickstart/select-definition.png)

1. Zadejte hodnoty pro kartu **Základy.** Vytvořte novou skupinu prostředků s názvem **applicationGroup**. Vyberte umístění aplikace. Po dokončení vyberte **OK**.

   ![Zadejte hodnoty pro základní](./media/deploy-service-catalog-quickstart/provide-basics.png)

1. Zadejte předponu pro název účtu úložiště. Vyberte typ účtu úložiště, který chcete vytvořit. Po dokončení vyberte **OK**.

   ![Zadejte hodnoty pro úložiště](./media/deploy-service-catalog-quickstart/provide-storage.png)

1. Zkontrolujte souhrnné informace. Po úspěšném ověření vyberte **ok** a stesk.

   ![Zobrazit souhrn](./media/deploy-service-catalog-quickstart/view-summary.png)

## <a name="view-results"></a>Zobrazení výsledků

Po nasazení aplikace katalogu služeb máte dvě nové skupiny prostředků. Jedna skupina prostředků obsahuje aplikaci katalogu služeb. Druhá skupina prostředků obsahuje prostředky pro aplikaci katalogu služeb.

1. Zobrazení mno že skupiny prostředků s názvem **applicationGroup** zobrazíte aplikaci katalogu služeb.

   ![Zobrazení aplikace](./media/deploy-service-catalog-quickstart/view-managed-application.png)

1. Chcete-li zobrazit prostředky aplikace katalogu služeb, prohlédněte skupinu prostředků s názvem **applicationGroup{hash-characters}.**

   ![Zobrazení prostředků](./media/deploy-service-catalog-quickstart/view-resources.png)

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak vytvořit definiční soubory pro spravovanou aplikaci, naleznete v [tématu Vytvoření a publikování definice spravované aplikace](publish-service-catalog-app.md).
* Informace o rozhraní příkazového příkazového nastavení Azure najdete [v tématu Nasazení aplikace katalogu služeb pomocí rozhraní příkazového příkazu Kontu Azure](./scripts/managed-application-cli-sample-create-application.md).
* V PowerShellu [najdete v tématu Nasazení aplikace katalogu služeb s PowerShellem](./scripts/managed-application-poweshell-sample-create-application.md).
