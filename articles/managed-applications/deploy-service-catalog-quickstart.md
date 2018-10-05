---
title: Pomocí webu Azure portal k nasazení služby app Service catalog | Dokumentace Microsoftu
description: Příjemci spravované aplikace ukazuje, jak nasadit aplikaci katalogu služeb na webu Azure portal.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: 4d2e8b442f70ee791fe65a32402e5272eda3f209
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809252"
---
# <a name="deploy-service-catalog-app-through-azure-portal"></a>Nasazení aplikace katalogu služeb prostřednictvím webu Azure portal

V [předchozím rychlém startu](publish-managed-app-definition-quickstart.md), jste publikovali definici spravované aplikace. V tomto rychlém startu vytvoříte aplikaci katalogu služeb z této definice.

## <a name="create-service-catalog-app"></a>Vytvoření aplikace katalogu služeb

Na webu Azure Portal postupujte následovně:

1. Vyberte **vytvořit prostředek**.

   ![Vytvoření prostředku](./media/deploy-service-catalog-quickstart/create-new.png)

1. Vyhledejte **spravované aplikace katalogu služeb** a vyberte ho z dostupných možností.

   ![Hledat aplikace katalogu služeb](./media/deploy-service-catalog-quickstart/select-service-catalog.png)

1. Zobrazí popis služby spravované aplikace. Vyberte **Vytvořit**.

   ![Výběr možnosti vytvoření](./media/deploy-service-catalog-quickstart/create-service-catalog.png)

1. Na portálu se zobrazí definice spravované aplikace, ke kterým máte přístup. Z dostupných definic vyberte ten, který chcete nasadit. V tomto rychlém startu pomocí **spravovaný účet úložiště** definice, kterou jste vytvořili v předchozím rychlém startu. Vyberte **Vytvořit**.

   ![Vyberte definici nasazení](./media/deploy-service-catalog-quickstart/select-definition.png)

1. Zadejte hodnoty **Základy** kartu. Vyberte předplatné Azure, které chcete nasadit aplikaci katalogu služeb. Vytvořit novou skupinu prostředků s názvem **skupině applicationGroup**. Vyberte umístění pro vaši aplikaci. Až budete hotovi, vyberte **OK**.

   ![Zadejte hodnoty pro basic](./media/deploy-service-catalog-quickstart/provide-basics.png)

1. Zadejte předponu pro název účtu úložiště. Vyberte typ účtu úložiště vytvořit. Až budete hotovi, vyberte **OK**.

   ![Zadání hodnot pro úložiště](./media/deploy-service-catalog-quickstart/provide-storage.png)

1. Zkontrolujte souhrnné informace. Po úspěšném ověření vyberte **OK** zahájíte nasazení.

   ![Zobrazit souhrn](./media/deploy-service-catalog-quickstart/view-summary.png)

## <a name="view-results"></a>Zobrazení výsledků

Po nasazení aplikace katalogu služeb, máte dvě nové skupiny prostředků. Jedna skupina prostředků obsahuje aplikace katalogu služeb. Jiné skupiny prostředků obsahuje prostředky pro aplikaci katalogu služeb.

1. Zobrazte si skupinu prostředků s názvem **skupině applicationGroup** a zobrazte aplikaci katalogu služeb.

   ![Zobrazení aplikace](./media/deploy-service-catalog-quickstart/view-managed-application.png)

1. Zobrazte si skupinu prostředků s názvem **skupině applicationGroup {znaky hash}** a zobrazit prostředky pro aplikaci katalogu služeb.

   ![Zobrazit prostředky](./media/deploy-service-catalog-quickstart/view-resources.png)

## <a name="next-steps"></a>Další postup

* Zjistěte, jak vytvořit definiční soubory pro spravované aplikace, najdete v článku [vytvoření a publikování definici spravované aplikace](publish-service-catalog-app.md).
* Rozhraní příkazového řádku Azure, najdete v části [nasazení služby katalogu aplikací pomocí Azure CLI](./scripts/managed-application-cli-sample-create-application.md).
* Prostředí PowerShell, najdete v části [nasazení služby katalogu aplikací s využitím Powershellu](./scripts/managed-application-poweshell-sample-create-application.md).