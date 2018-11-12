---
title: Zpřístupnění webové aplikace a aplikace API pro vaše uživatele Azure stacku | Dokumentace Microsoftu
description: Kurz k instalaci poskytovatele prostředků App Service a vytvoření nabízí, které poskytují uživatelům Azure stacku možnost vytvářet webové aplikace a aplikace API.
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
ms.date: 11/05/2018
ms.author: jeffgilb
ms.reviewer: anwestg
ms.custom: mvc
ms.openlocfilehash: f8a54436a61889e6d39c7e491e45c25e3b38236f
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2018
ms.locfileid: "51036782"
---
# <a name="tutorial-make-web-and-api-apps-available-to-your-azure-stack-users"></a>Kurz: zpřístupnění webové aplikace a aplikace API pro vaše uživatele Azure stacku

Jako správce cloudu Azure Stack, můžete vytvořit nabídek, které uživatelé mohli vytvořit Azure Functions a rozhraní API a webové aplikace (klienty). Tím, že vaši uživatelé přístup k těmto aplikacím na vyžádání, založené na cloudu, můžete je šetřit čas i prostředky.

Chcete-li toto nastavení bude:

> [!div class="checklist"]
> * Nasazení poskytovatele prostředků App Service
> * Vytvoření nabídky
> * Test nabídky

## <a name="deploy-the-app-service-resource-provider"></a>Nasazení poskytovatele prostředků App Service

1. [Příprava hostitele Azure Stack Development Kit](azure-stack-app-service-before-you-get-started.md). To zahrnuje nasazení poskytovatele prostředků SQL serveru, která je potřebná pro vytváření některých aplikací.
2. [Stáhněte si instalační program a pomocné skripty](azure-stack-app-service-deploy.md).
3. [Spusťte skript pomocné rutiny pro vytvoření požadované certifikáty](azure-stack-app-service-deploy.md).
4. [Instalace poskytovatele prostředků App Service](azure-stack-app-service-deploy.md) (bude trvat několik hodin k instalaci a pro všechny role pracovního procesu se zobrazí.)
5. [Ověření instalace](azure-stack-app-service-deploy.md#validate-the-app-service-on-azure-stack-installation).

## <a name="create-an-offer"></a>Vytvoření nabídky

Například můžete vytvořit nabídku, která umožní uživatelům vytvářet systémy správy obsahu webové DNN. Vyžaduje službu systému SQL Server, který jste nepovolili instalací poskytovatele prostředků SQL serveru.

1.  [Nastavení kvóty](azure-stack-setting-quotas.md) a pojmenujte ho *AppServiceQuota*. Vyberte **Microsoft.Web** pro **Namespace** pole.
2.  [Vytvoření plánu](azure-stack-create-plan.md). Pojmenujte ji *TestAppServicePlan*, vyberte **Microsoft.SQL** služby a **kvóty služby App Service** kvóty.

    > [!NOTE]
    > Umožníte uživatelům vytvářet další aplikace, může se vyžadovat další služby v plánu. Například Azure Functions vyžaduje **Microsoft.Storage** služby v plánu, zatímco Wordpress vyžaduje **Microsoft.MySQL**.

3.  [Vytvoření nabídky](azure-stack-create-offer.md), pojmenujte ho **TestAppServiceOffer** a vyberte **TestAppServicePlan** plánu.

## <a name="test-the-offer"></a>Test nabídky

Teď, když jste nasadili poskytovatele prostředků App Service a vytvořené v rámci nabídky, se můžete přihlásit jako uživatel, předplacení nabídky a vytvoření nové aplikace.

V tomto příkladu vytvoříme platformou DNN systém správy obsahu. Nejprve vytvořte databázi SQL a pak DNN webové aplikace.

### <a name="subscribe-to-the-offer"></a>Předplacení nabídky

1. Přihlaste se k portálu Azure Stack (https://portal.local.azurestack.external) jako tenant.
2. Vyberte **pořiďte si předplatné** >, zadejte **TestAppServiceSubscription** pod **zobrazovaný název** > **vybrat některou nabídku**  >  **TestAppServiceOffer** > **vytvořit**.

### <a name="create-a-sql-database"></a>Vytvoření databáze SQL

1. Vyberte **+**  >  **Data + úložiště** > **SQL Database**.
2. Ponechte výchozí hodnoty, s výjimkou následujících polí:

    - **Název databáze**: DNNdb
    - **Maximální velikost v MB**: 100
    - **Předplatné**: TestAppServiceOffer
    - **Skupina prostředků**: DNN-RG

3. Vyberte **nastavení přihlášení**, zadejte přihlašovací údaje pro databázi a pak vyberte **OK**. Tyto přihlašovací údaje použijete později v tomto kurzu.
4. V části **SKU** > vyberte jednotku SKU SQL, kterou jste vytvořili pro SQL Server, který je hostitelem > a pak vyberte **OK**.
5. Vyberte **Vytvořit**.

### <a name="create-a-dnn-app"></a>Vytvoření aplikace DNN

1. Vyberte **+**  >  **zobrazit všechny** > **platformou DNN preview** > **vytvořit** .
2. Zadejte *DNNapp* pod **název aplikace** a vyberte **TestAppServiceOffer** pod **předplatné**.
3. Vyberte **konfigurovat požadované nastavení** > **vytvořit nový** > zadat **plán služby App Service** název.
4. Vyberte **cenová úroveň** > **F1 Free** > **vyberte** > **OK**.
5. Vyberte **databáze** a zadejte přihlašovací údaje pro databázi SQL, který jste vytvořili dříve.
6. Vyberte **Vytvořit**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nasazení poskytovatele prostředků App Service
> * Vytvoření nabídky
> * Test nabídky

Pokračujte k dalším kurzu se dozvíte, jak:

> [!div class="nextstepaction"]
> [Nasazení aplikací do Azure a Azure Stack](user/azure-stack-solution-pipeline.md)
