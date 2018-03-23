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
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: e2f15ca3a46af51ab6228e772298c51ad33fd49c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="make-web-and-api-apps-available-to-your-azure-stack-users"></a>Zpřístupnit web a aplikace API Azure zásobníku uživatelům
Jako správce cloudu Azure zásobníku, můžete vytvořit nabídky, které uživatelům (klienty) vytvořit Azure Functions a rozhraní API a webové aplikace. Tím, že uživatelům poskytuje přístup k těmto aplikacím na vyžádání, založené na cloudu, můžete je uložit čas a prostředky. Pokud chcete nastavit tuto možnost, bude takto:

> [!div class="checklist"]
> * Nasazení zprostředkovatele prostředků služby App Service
> * Vytvoření nabídky
> * Testování nabídky

## <a name="deploy-the-app-service-resource-provider"></a>Nasazení zprostředkovatele prostředků služby App Service

1. [Příprava hostitele Azure zásobníku Development Kit](azure-stack-app-service-before-you-get-started.md). To zahrnuje nasazení poskytovatele prostředků systému SQL Server, který je vyžadován pro vytvoření některé aplikace.
2. [Stáhněte si skripty Instalační program a pomocné rutiny](azure-stack-app-service-deploy.md).
3. [Spusťte skript pomocné rutiny pro vytvoření požadované certifikáty](azure-stack-app-service-deploy.md).
4. [Nainstalujte zprostředkovatele prostředků služby App Service](azure-stack-app-service-deploy.md) (bude trvat několik hodin k instalaci a u všech rolí pracovního procesu se objeví).
5. [Ověření instalace](azure-stack-app-service-deploy.md#validate-the-app-service-on-azure-stack-installation).

## <a name="create-an-offer"></a>Vytvoření nabídky

Například můžete vytvořit nabídku, která umožní uživatelům vytvářet systémy DNN webové správy obsahu. Vyžaduje službu SQL Server, který jste nepovolili instalací poskytovatele prostředků systému SQL Server.

1.  [Nastavení kvóty](azure-stack-setting-quotas.md) a pojmenujte ji *AppServiceQuota*. Vyberte **Microsoft.Web** pro **Namespace** pole.
2.  [Vytvoření plánu](azure-stack-create-plan.md). Pojmenujte ji *TestAppServicePlan*, vyberte **Microsoft.SQL** služby, a **kvót služby App Service** kvóty.

    > [!NOTE]
    > Umožníte uživatelům vytvářet jiné aplikace, může být vyžadován další služby v plánu. Například Azure Functions vyžaduje, aby plánu zahrnout **Microsoft.Storage** služby, zatímco Wordpress vyžaduje **Microsoft.MySQL**.
    > 
    >

3.  [Vytvořit nabídku](azure-stack-create-offer.md), pojmenujte ji **TestAppServiceOffer** a vyberte **TestAppServicePlan** plán.

## <a name="test-the-offer"></a>Testování nabídky

Teď, když máte ukázku nasazenou zprostředkovatele prostředků služby App Service a vytvořit nabídku, můžete přihlásit jako uživatel, přihlášení k odběru na nabídku a vytvoření aplikace. V tomto příkladu vytvoříme DNN platformy systému správy obsahu. Musíte nejdřív vytvořit databázi SQL a poté DNN webové aplikace.

### <a name="subscribe-to-the-offer"></a>Přihlášení k odběru do nabídky
1. Přihlaste se k portálu Azure zásobníku (https://portal.local.azurestack.external) jako klient.
2. Klikněte na tlačítko **získat předplatné** > typ **TestAppServiceSubscription** pod **zobrazovaný název** > **vyberte nabídku**  >  **TestAppServiceOffer** > **vytvořit**.

### <a name="create-a-sql-database"></a>Vytvoření databáze SQL

1. Klikněte na tlačítko **+**  >  **Data + úložiště** > **databáze SQL**.
2. Ponechte výchozí hodnoty pro pole, s výjimkou následujícím způsobem:
    - **Název databáze**: DNNdb
    - **Maximální velikost v MB**: 100
    - **Předplatné**: TestAppServiceOffer
    - **Skupina prostředků**: DNN-RG
3. Klikněte na tlačítko **nastavení přihlášení**, zadejte přihlašovací údaje pro databázi a pak klikněte na tlačítko **OK**. Tyto přihlašovací údaje použijete později v těchto krocích.
4. Klikněte na tlačítko **SKU** > vyberte SKU SQL, kterou jste vytvořili pro hostování serveru SQL > **OK**.
5. Klikněte na možnost **Vytvořit**.

### <a name="create-a-dnn-app"></a>Vytvoření aplikace DNN    

1. Klikněte na tlačítko **+**  >  **zobrazit všechny** > **preview platformy DNN** > **vytvořit**.
2. Typ *DNNapp* pod **název aplikace** a vyberte **TestAppServiceOffer** pod **předplatné**.
3. Klikněte na tlačítko **konfigurovat požadované nastavení** > **vytvořit nový** > typ **plán služby App Service** název.
4. Klikněte na tlačítko **cenová úroveň** > **F1 Free** > **vyberte** > **OK**.
5. Klikněte na tlačítko **databáze** a zadejte informace pro databázi SQL jste vytvořili dříve.
6. Klikněte na možnost **Vytvořit**.

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nasazení zprostředkovatele prostředků služby App Service
> * Vytvoření nabídky
> * Testování nabídky

Přechod na další kurzu se dozvíte, jak:

> [!div class="nextstepaction"]
> [Nasazení aplikací do Azure a Azure zásobníku](user/azure-stack-solution-pipeline.md)
