---
title: Zpřístupnit web a aplikace API Azure zásobníku uživatelům | Microsoft Docs
description: Kurz k instalaci zprostředkovatele prostředků služby App Service a vytvoření nabízí, zásobník Azure uživatelům zadat možnost vytvářet webové aplikace nebo aplikace API.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/05/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 0171dba639e480a04cdd1c7f23d546d01121fb42
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247394"
---
# <a name="tutorial-make-web-and-api-apps-available-to-your-azure-stack-users"></a>Kurz: zpřístupníte web a aplikace API Azure zásobníku uživatelům

Jako správce cloudu Azure zásobníku, můžete vytvořit nabídky, které uživatelům (klienty) vytvořit Azure Functions a rozhraní API a webové aplikace. Tím, že vaši uživatelé přístup pro tyto aplikace na vyžádání, založené na cloudu, můžete je uložit čas a prostředky.

Pokud chcete nastavit tuto možnost, bude takto:

> [!div class="checklist"]
> * Nasazení zprostředkovatele prostředků služby App Service
> * Vytvoření nabídky
> * Testování nabídky

## <a name="deploy-the-app-service-resource-provider"></a>Nasazení zprostředkovatele prostředků služby App Service

1. [Příprava hostitele Azure zásobníku Development Kit](azure-stack-app-service-before-you-get-started.md). To zahrnuje nasazení poskytovatele prostředků systému SQL Server, který je vyžadován pro vytvoření některé aplikace.
2. [Stáhněte si skripty Instalační program a pomocné rutiny](azure-stack-app-service-deploy.md).
3. [Spusťte skript pomocné rutiny pro vytvoření požadované certifikáty](azure-stack-app-service-deploy.md).
4. [Nainstalujte zprostředkovatele prostředků služby App Service](azure-stack-app-service-deploy.md) (bude trvat několik hodin k instalaci a u všech rolí pracovního procesu se objeví.)
5. [Ověření instalace](azure-stack-app-service-deploy.md#validate-the-app-service-on-azure-stack-installation).

## <a name="create-an-offer"></a>Vytvoření nabídky

Například můžete vytvořit nabídku, která umožní uživatelům vytvářet systémy DNN webové správy obsahu. Vyžaduje službu SQL Server, který jste nepovolili instalací poskytovatele prostředků systému SQL Server.

1.  [Nastavení kvóty](azure-stack-setting-quotas.md) a pojmenujte ji *AppServiceQuota*. Vyberte **Microsoft.Web** pro **Namespace** pole.
2.  [Vytvoření plánu](azure-stack-create-plan.md). Pojmenujte ji *TestAppServicePlan*, vyberte **Microsoft.SQL** služby a **kvót služby App Service** kvóty.

    > [!NOTE]
    > Umožníte uživatelům vytvářet jiné aplikace, může být vyžadován další služby v plánu. Například vyžaduje Azure Functions **Microsoft.Storage** služby v plánu, při Wordpress vyžaduje **Microsoft.MySQL**.

3.  [Vytvořit nabídku](azure-stack-create-offer.md), pojmenujte ji **TestAppServiceOffer** a vyberte **TestAppServicePlan** plán.

## <a name="test-the-offer"></a>Testování nabídky

Teď, když máte ukázku nasazenou zprostředkovatele prostředků služby App Service a vytvořit nabídku, můžete přihlásit jako uživatel, přihlášení k odběru na nabídku a vytvoření aplikace.

V tomto příkladu vytvoříme DNN platformy systému správy obsahu. Nejprve je třeba vytvořit databázi SQL a poté DNN webové aplikace.

### <a name="subscribe-to-the-offer"></a>Přihlášení k odběru do nabídky

1. Přihlaste se k portálu Azure zásobníku (https://portal.local.azurestack.external) jako klient.
2. Vyberte **získat předplatné** >, zadejte **TestAppServiceSubscription** pod **zobrazovaný název** > **vyberte nabídku**  >  **TestAppServiceOffer** > **vytvořit**.

### <a name="create-a-sql-database"></a>Vytvoření databáze SQL

1. Vyberte **+**  >  **Data + úložiště** > **databáze SQL**.
2. Ponechte výchozí hodnoty, s výjimkou následující pole:

    - **Název databáze**: DNNdb
    - **Maximální velikost v MB**: 100
    - **Předplatné**: TestAppServiceOffer
    - **Skupina prostředků**: DNN-RG

3. Vyberte **nastavení přihlášení**, zadejte přihlašovací údaje pro databázi a potom vyberte **OK**. Tyto přihlašovací údaje použijete později v tomto kurzu.
4. V části **SKU** > vyberte SKU SQL, kterou jste vytvořili pro hostování serveru SQL > a pak vyberte **OK**.
5. Vyberte **Vytvořit**.

### <a name="create-a-dnn-app"></a>Vytvoření aplikace DNN

1. Vyberte **+**  >  **zobrazit všechny** > **preview platformy DNN** > **vytvořit** .
2. Zadejte *DNNapp* pod **název aplikace** a vyberte **TestAppServiceOffer** pod **předplatné**.
3. Vyberte **konfigurovat požadované nastavení** > **vytvořit nový** > zadat **plán služby App Service** název.
4. Vyberte **cenová úroveň** > **F1 Free** > **vyberte** > **OK**.
5. Vyberte **databáze** a zadejte přihlašovací údaje pro databázi SQL, který jste vytvořili dříve.
6. Vyberte **Vytvořit**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nasazení zprostředkovatele prostředků služby App Service
> * Vytvoření nabídky
> * Testování nabídky

Přechod na další kurzu se dozvíte, jak:

> [!div class="nextstepaction"]
> [Nasazení aplikací do Azure a Azure zásobníku](user/azure-stack-solution-pipeline.md)
